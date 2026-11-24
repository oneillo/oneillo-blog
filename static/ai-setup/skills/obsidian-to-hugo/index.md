# Obsidian to Hugo

- Kind: Claude Code skill
- Address: https://oneillo.com/ai-setup/skills/obsidian-to-hugo/
- Last updated: 2026-11-23

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/skills/obsidian-to-hugo/.

Converts an Obsidian blog post to Hugo-ready Markdown, fixes links and pictures, and places it in a review folder.

- **What it is:** Obsidian to Hugo is a Claude Code skill that converts a blog post written in Obsidian into Hugo-compatible Markdown and places it in a folder for review.
- **How it starts:** You type `/obsidian-to-hugo`. Claude can also pick it up when it fits.
- **Where the file goes:** `~/.claude/skills/obsidian-to-hugo/SKILL.md`, its other files beside it
- **Needs:** uv, Obsidian, and Hugo
- **To make it yours:** Put your own values where you see `<blog-folder>`, `<vault-folder>`, and `<yyyymmdd>`.
  Taken out for privacy: 4 paths, 1 blog folder name, and 6 dated project folder names.

This skill is specific to Orlando's blog. It is here because it is part of his setup, and its steps may still be worth borrowing.

**Works with:** [Corillo](https://oneillo.com/ai-setup/apps/#corillo), [Relay](https://oneillo.com/ai-setup/skills/relay/), and [Writer](https://oneillo.com/ai-setup/agents/writer/).

## The skill file (`SKILL.md`)

The instructions for the skill that converts an Obsidian blog post to Hugo Markdown. It is used when a post is ready to move from the notes vault to review.

````
---
name: obsidian-to-hugo
description: Convert an Obsidian blog post to Hugo-compatible markdown and place it in the blog projects folder for review
allowed-tools: Read, Bash, Glob, Grep, AskUserQuestion
---

# Obsidian to Hugo Converter

Convert a blog post written in Obsidian to Hugo-compatible markdown and place it in `~/ai/projects/blog/` for editing and review.

## Paths

- **Obsidian source**: `<vault-folder>/writing/blog/`
- **Destination**: `~/ai/projects/blog/posts-to-review/`

If the Obsidian path does not exist, ask the user for the correct vault location.

## Steps

### 1. Identify the source file

If the user specified a filename (as an argument or in conversation), use that. Otherwise, list the markdown files in the Obsidian source directory and ask which one to convert.

Match flexibly -- the user might give a partial name, a slug, or a title. Find the closest match in the source directory.

### 2. Run the conversion

Run the conversion script:

```bash
uv run ~/.claude/skills/obsidian-to-hugo/scripts/convert.py --file "<filename>" [--verbose]
```

Run it without asking. If it says the converted file already exists, ask before running again with `--force`: the copy may hold review edits. The script does not modify the original Obsidian file.

A `[[link]]` becomes a link only when the post exists in `<blog-folder>/content/posts/`; any other link (a note, a heading) is kept as text without a link, with a warning. Pictures, both `![[...]]` embeds and standard links such as Corillo's, are copied next to the converted post.

Tests: `uv run --offline ~/.claude/skills/obsidian-to-hugo/scripts/test_convert.py`.

### 3. Report the result

After the script completes successfully, tell the user where the converted file landed. One line is enough. Example:

> Converted to `~/ai/projects/blog/posts-to-review/my-post-title/my-post-title.md`

If the script reported any warnings (missing images, links kept as text), relay those.

### 4. Errors

If the script fails, show the error output and suggest a fix. Common issues:
- Source file not found -- check the filename and list available files
- Destination directory missing -- this shouldn't happen, but create it if needed
````

## Scripts

These scripts are one person's working code, shared from Orlando's setup with private details replaced by placeholders. They are not a supported tool: read each one before you run it, and expect to change it to fit your own setup.

### `convert.py`

The script that turns an Obsidian post into Hugo Markdown. It rewrites picture embeds and links to other posts, copies the pictures, and saves the result in the review folder without touching the original.

**Needs to run:** uv.

````
# /// script
# requires-python = ">=3.12"
# dependencies = []
# ///
"""
Obsidian to Hugo Converter

Converts Obsidian-native markdown to Hugo-compatible markdown and places
the result in ~/ai/projects/blog/posts-to-review/ for review with the writer agent.

Does NOT modify the original Obsidian file.
"""

import argparse
import re
import shutil
import sys
from datetime import date
from pathlib import Path
from urllib.parse import quote, unquote

# Default paths (macOS Obsidian vault)
DEFAULT_SOURCE = (
    Path.home()
    / "<vault-folder>/writing/blog"
)
DEFAULT_DEST = Path.home() / "ai/projects/blog/posts-to-review"
DEFAULT_BLOG = Path.home() / "<blog-folder>/content/posts"

# Obsidian's size suffix on an embed: |400 or |400x300
SIZE_ONLY = re.compile(r"^\d+(x\d+)?$")


def slugify(title: str) -> str:
    """Convert a post title to a Hugo-compatible slug.

    'My Great Post' -> 'my-great-post'
    '<yyyymmdd>-my-post' -> 'my-post'
    """
    slug = title.lower().strip()
    # Strip leading date prefix (YYYYMMDD- or YYYY-MM-DD-)
    slug = re.sub(r"^\d{4}-?\d{2}-?\d{2}-", "", slug)
    slug = re.sub(r"[^\w\s-]", "", slug)  # Remove special chars
    slug = re.sub(r"[\s_]+", "-", slug)  # Spaces/underscores to hyphens
    slug = re.sub(r"-+", "-", slug)  # Collapse multiple hyphens
    slug = slug.strip("-")
    return slug


def find_code_regions(text: str) -> list[tuple[int, int]]:
    """Find all code block and inline code regions to skip during transforms."""
    regions = []
    # Fenced code blocks
    for match in re.finditer(r"```[\s\S]*?```", text):
        regions.append((match.start(), match.end()))
    # Inline code spans
    for match in re.finditer(r"`[^`]+`", text):
        regions.append((match.start(), match.end()))
    return regions


def in_protected_region(pos: int, regions: list[tuple[int, int]]) -> bool:
    """Check if a position falls inside a protected region."""
    return any(start <= pos < end for start, end in regions)


def find_frontmatter_end(text: str) -> int:
    """Find the end position of YAML frontmatter (if present).

    Returns 0 if no frontmatter found.
    """
    if not text.startswith("---"):
        return 0
    match = re.match(r"^---\s*\n.*?\n---\s*\n", text, re.DOTALL)
    return match.end() if match else 0


def transform_image_embeds(
    text: str, code_regions: list[tuple[int, int]], fm_end: int
) -> tuple[str, list[str]]:
    """Convert Obsidian image embeds and local picture links to standard markdown.

    ![[image.png]]          -> ![](image.png)
    ![[image.png|alt text]] -> ![alt text](image.png)
    ![[image.png|400]]      -> ![](image.png)          (size dropped)
    ![[Pasted image.png]]   -> ![](Pasted%20image.png)
    ![Chart](post%20images/chart.png) -> ![Chart](chart.png)   (Corillo's form)

    Every picture ends up next to the post, so links use the bare, encoded name.
    Returns (transformed_text, list_of_referenced_images), where each image is
    its path relative to the source file, decoded.
    """
    images = []
    embed = re.compile(r"!\[\[([^\]|]+?)(?:\|([^\]]*))?\]\]")
    # Standard picture links to local files (not web addresses)
    standard = re.compile(r"!\[([^\]]*)\]\((?![a-zA-Z][a-zA-Z0-9+.-]*:)([^)\s]+)\)")

    def skip(match):
        return in_protected_region(match.start(), code_regions) or match.start() < fm_end

    def link(alt: str, path: str) -> str:
        images.append(path)
        return f"![{alt}]({quote(Path(path).name)})"

    def replace_embed(match):
        if skip(match):
            return match.group(0)
        alt = match.group(2).strip() if match.group(2) else ""
        if SIZE_ONLY.match(alt):
            alt = ""
        return link(alt, match.group(1).strip())

    def replace_standard(match):
        if skip(match):
            return match.group(0)
        return link(match.group(1), unquote(match.group(2)))

    # Standard links first: embed output is itself a standard link.
    text = standard.sub(replace_standard, text)
    code_regions = find_code_regions(text)
    fm_end = find_frontmatter_end(text)
    return embed.sub(replace_embed, text), images


def post_exists(blog_dir: Path, slug: str) -> bool:
    """True if the blog has a post at this slug (bundle folder or single file)."""
    return bool(slug) and (
        (blog_dir / slug).is_dir() or (blog_dir / f"{slug}.md").is_file()
    )


def transform_wikilinks(
    text: str, code_regions: list[tuple[int, int]], fm_end: int, blog_dir: Path
) -> tuple[str, list[str]]:
    """Convert Obsidian wikilinks to Hugo ref links, only for posts that exist.

    [[Post Title]]              -> [Post Title]({{< ref "post-title" >}})
    [[Post Title|Display Text]] -> [Display Text]({{< ref "post-title" >}})
    [[Post Title#Heading]]      -> same as [[Post Title]] (heading dropped)

    A link to anything that is not a published post (a note, a heading in this
    post) would stop the Hugo build, so it becomes its display text with no
    link. Returns (transformed_text, warnings).
    """
    pattern = re.compile(r"(?<!!)\[\[([^\]|]+?)(?:\|([^\]]*))?\]\]")
    warnings = []

    def replace(match):
        if in_protected_region(match.start(), code_regions) or match.start() < fm_end:
            return match.group(0)
        target = match.group(1).strip()
        page, _, heading = target.partition("#")
        page = page.strip()
        display = match.group(2).strip() if match.group(2) else (
            page or heading.lstrip("^").strip()
        )
        slug = slugify(page)
        if post_exists(blog_dir, slug):
            return f'[{display}]({{{{< ref "{slug}" >}}}})'
        warnings.append(
            f"link {match.group(0)} is not a post on the blog; kept the text without a link"
        )
        return display

    return pattern.sub(replace, text), warnings


def convert_post(
    content: str, blog_dir: Path = DEFAULT_BLOG
) -> tuple[str, list[str], list[str]]:
    """Apply all transforms to a post's content.

    Returns (converted_content, list_of_referenced_images, link_warnings).
    """
    fm_end = find_frontmatter_end(content)
    code_regions = find_code_regions(content)

    # Image embeds first (changes positions, so recalculate after)
    content, images = transform_image_embeds(content, code_regions, fm_end)

    # Recalculate protected regions after image transform
    code_regions = find_code_regions(content)
    fm_end = find_frontmatter_end(content)

    content, warnings = transform_wikilinks(content, code_regions, fm_end, blog_dir)

    return content, images, warnings


def process_file(
    source_file: Path,
    source_dir: Path,
    dest_dir: Path,
    blog_dir: Path = DEFAULT_BLOG,
    verbose: bool = False,
    force: bool = False,
) -> Path:
    """Process a single markdown file. Returns the output file path."""
    slug = slugify(source_file.stem)
    project_dir = dest_dir / slug
    out_file = project_dir / f"{slug}.md"
    if out_file.exists() and not force:
        print(
            f"Error: {out_file} already exists and may hold review edits. "
            "Run again with --force to replace it.",
            file=sys.stderr,
        )
        sys.exit(1)

    content = source_file.read_text(encoding="utf-8")
    converted, images, warnings = convert_post(content, blog_dir)

    project_dir.mkdir(parents=True, exist_ok=True)
    out_file.write_text(converted, encoding="utf-8")

    if not blog_dir.is_dir():
        print(f"  Warning: blog posts folder not found: {blog_dir}", file=sys.stderr)
    for warning in warnings:
        print(f"  Warning: {warning}", file=sys.stderr)

    if verbose:
        print(f"  Source:  {source_file}")
        print(f"  Output:  {out_file}")
        if images:
            print(f"  Images:  {', '.join(images)}")

    # Copy referenced images
    for img_name in images:
        img_source = source_file.parent / img_name
        if img_source.is_file():
            img_dest = project_dir / Path(img_name).name
            shutil.copy2(img_source, img_dest)
            if verbose:
                print(f"  Copied image: {img_name}")
        else:
            print(f"  Warning: image not found: {img_source}", file=sys.stderr)

    return out_file


def main():
    parser = argparse.ArgumentParser(
        description="Convert Obsidian markdown to Hugo-compatible format"
    )
    parser.add_argument(
        "--source",
        type=Path,
        default=DEFAULT_SOURCE,
        help=f"Obsidian blog directory (default: {DEFAULT_SOURCE})",
    )
    parser.add_argument(
        "--dest",
        type=Path,
        default=DEFAULT_DEST,
        help=f"Destination directory (default: {DEFAULT_DEST})",
    )
    parser.add_argument(
        "--blog",
        type=Path,
        default=DEFAULT_BLOG,
        help=f"Blog posts folder, to check that linked posts exist (default: {DEFAULT_BLOG})",
    )
    parser.add_argument(
        "--force",
        action="store_true",
        help="Replace an existing converted copy (it may hold review edits)",
    )
    parser.add_argument(
        "--file",
        type=str,
        required=True,
        help="Filename to convert (relative to source dir)",
    )
    parser.add_argument(
        "--verbose",
        action="store_true",
        help="Print detailed conversion info",
    )

    args = parser.parse_args()

    # Validate source directory
    if not args.source.exists():
        print(f"Error: Source directory not found: {args.source}", file=sys.stderr)
        print("Use --source to specify your Obsidian vault path.", file=sys.stderr)
        sys.exit(1)

    # Validate destination directory
    if not args.dest.exists():
        print(f"Error: Destination directory not found: {args.dest}", file=sys.stderr)
        sys.exit(1)

    # Find the source file -- check root, then search subdirectories
    source_file = args.source / args.file
    if not source_file.is_file() and not args.file.endswith(".md"):
        source_file = args.source / f"{args.file}.md"
    if not source_file.is_file():
        # Search subdirectories for a matching .md file
        name = args.file if args.file.endswith(".md") else f"{args.file}.md"
        matches = sorted(args.source.glob(f"*/{name}"))
        if matches:
            source_file = matches[0]
    if not source_file.is_file():
        print(f"Error: File not found: {source_file}", file=sys.stderr)
        # List available files as a hint (root + one level deep)
        md_files = sorted(args.source.glob("*.md")) + sorted(
            args.source.glob("*/*.md")
        )
        if md_files:
            print("\nAvailable files:", file=sys.stderr)
            for f in md_files:
                rel = f.relative_to(args.source)
                print(f"  {rel}", file=sys.stderr)
        sys.exit(1)

    out_file = process_file(
        source_file, args.source, args.dest, args.blog, args.verbose, args.force
    )
    print(f"Converted to {out_file}")


if __name__ == "__main__":
    main()
````

### `test_convert.py`

The tests for the conversion script, run on made-up posts in a temporary folder. They check links, pictures, the guard against overwriting review edits, and file name matching.

**Needs to run:** uv.

```
# /// script
# requires-python = ">=3.12"
# ///
"""Tests for convert.py, on made-up posts in a temp folder.

Run: uv run --offline test_convert.py [path/to/convert.py]
"""

import subprocess
import sys
import tempfile
import unittest
from pathlib import Path

SCRIPT = Path(sys.argv.pop(1) if len(sys.argv) > 1 else Path(__file__).with_name("convert.py"))


class ConvertTest(unittest.TestCase):
    def setUp(self):
        self.tmp = tempfile.TemporaryDirectory()
        root = Path(self.tmp.name)
        self.src, self.dest, self.blog = root / "src", root / "dest", root / "posts"
        for d in (self.src, self.dest, self.blog):
            d.mkdir()
        (self.blog / "soil-basics").mkdir()
        (self.blog / "another-post.md").write_text("x")

    def tearDown(self):
        self.tmp.cleanup()

    def run_convert(self, name, *extra):
        return subprocess.run(
            [sys.executable, str(SCRIPT), "--source", str(self.src), "--dest", str(self.dest),
             "--blog", str(self.blog), "--file", name, *extra],
            capture_output=True, text=True,
        )

    def post(self, body, folder="test-post"):
        d = self.src / folder
        d.mkdir(exist_ok=True)
        (d / f"{folder}.md").write_text(body)
        return d

    def out(self):
        return (self.dest / "test-post" / "test-post.md").read_text()

    def test_links_only_to_existing_posts(self):  # L3
        self.post("[[soil-basics|earlier post]], [[market-segmentation|segments]], "
                  "[[Another Post#Part two]], [[the-dirt-book#^loc-200]], [[#Wrap up]].\n")
        r = self.run_convert("test-post.md")
        self.assertEqual(r.returncode, 0, r.stderr)
        self.assertEqual(self.out(), '[earlier post]({{< ref "soil-basics" >}}), segments, '
                         '[Another Post]({{< ref "another-post" >}}), the-dirt-book, Wrap up.\n')
        self.assertEqual(r.stderr.count("not a post on the blog"), 3)

    def test_pictures(self):  # L4
        d = self.post("![[Pasted image 2026.png]] ![[chart.png|400]] ![[plan.png|the plan]]\n"
                      "![Chart](test-post%20images/corillo-chart.png) ![Gone](missing.png) "
                      "![Video](https://youtu.be/abc)\n")
        for name in ("Pasted image 2026.png", "chart.png", "plan.png"):
            (d / name).write_bytes(b"img")
        (d / "test-post images").mkdir()
        (d / "test-post images" / "corillo-chart.png").write_bytes(b"img")
        r = self.run_convert("test-post.md")
        self.assertEqual(r.returncode, 0, r.stderr)
        self.assertEqual(self.out(), "![](Pasted%20image%202026.png) ![](chart.png) ![the plan](plan.png)\n"
                         "![Chart](corillo-chart.png) ![Gone](missing.png) ![Video](https://youtu.be/abc)\n")
        for name in ("Pasted image 2026.png", "chart.png", "plan.png", "corillo-chart.png"):
            self.assertTrue((self.dest / "test-post" / name).is_file(), name)
        self.assertIn("image not found", r.stderr)
        self.assertIn("missing.png", r.stderr)

    def test_second_run_keeps_review_edits(self):  # L5
        self.post("Hello.\n")
        self.assertEqual(self.run_convert("test-post.md").returncode, 0)
        out = self.dest / "test-post" / "test-post.md"
        out.write_text("Hello, edited.\n")
        r = self.run_convert("test-post.md")
        self.assertNotEqual(r.returncode, 0)
        self.assertIn("--force", r.stderr)
        self.assertEqual(out.read_text(), "Hello, edited.\n")
        self.assertEqual(self.run_convert("test-post.md", "--force").returncode, 0)
        self.assertEqual(out.read_text(), "Hello.\n")

    def test_name_without_md_and_same_named_folder(self):  # L19
        self.post("Hello.\n")
        r = self.run_convert("test-post")
        self.assertEqual(r.returncode, 0, r.stderr)
        self.assertEqual(self.out(), "Hello.\n")


if __name__ == "__main__":
    unittest.main()
```
