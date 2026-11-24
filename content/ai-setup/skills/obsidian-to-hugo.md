---
title: "Obsidian to Hugo: a Claude Code skill that converts Obsidian posts for Hugo"
date: 2026-11-22
lastmod: 2026-11-23
summary: "AI-written: Converts an Obsidian blog post to Hugo-ready Markdown, fixes links and pictures, and places it in a review folder."
heading: "Obsidian to Hugo"
linkTitle: "Obsidian to Hugo"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/skills/obsidian-to-hugo.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">Converts an Obsidian blog post to Hugo-ready Markdown, fixes links and pictures, and places it in a review folder.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-23">2026-11-23</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

<section class="facts facts-skill">

<div class="facts-head"><span class="tag tag-skill">Skill</span><span class="facts-cmd"><code>/obsidian-to-hugo</code></span><span class="facts-files">3 files</span></div>

- **What it is:** Obsidian to Hugo is a Claude Code skill that converts a blog post written in Obsidian into Hugo-compatible Markdown and places it in a folder for review.
- **How it starts:** You type <code>&#47;obsidian&#45;to&#45;hugo</code>. Claude can also pick it up when it fits.
- **Where the file goes:** <code>&#126;&#47;&#46;claude&#47;skills&#47;obsidian&#45;to&#45;hugo&#47;SKILL&#46;md</code>, its other files beside it
- **Needs:** uv, Obsidian, and Hugo
- **To make it yours:** Put your own values where you see <code>&#60;blog&#45;folder&#62;</code>, <code>&#60;vault&#45;folder&#62;</code>, and <code>&#60;yyyymmdd&#62;</code>.
  Taken out for privacy: 4 paths, 1 blog folder name, and 6 dated project folder names.

</section>

This skill is specific to Orlando's blog. It is here because it is part of his setup, and its steps may still be worth borrowing.

<div class="works">

**Works with:**
- **Calls:** <a href="/ai-setup/agents/writer/">Writer</a>.
- **Called by:** <a href="/ai-setup/skills/publish-post/">Publish post</a> and <a href="/ai-setup/agents/writer/">Writer</a>.
- **Also mentions:** <a href="/ai-setup/apps/#corillo">Corillo</a> and <a href="/ai-setup/skills/relay/">Relay</a>.

</div>

<div class="files">

## Files on this page {#files-on-this-page}

| File | What it is | Size |
| --- | --- | --- |
| <a href="#skill-md"><code>SKILL&#46;md</code></a> | The instructions for the skill that converts an Obsidian blog post to Hugo Markdown. | 52 lines |
| <a href="#convert-py"><code>convert&#46;py</code></a> | The script that turns an Obsidian post into Hugo Markdown. | 323 lines |
| <a href="#test-convert-py"><code>test&#95;convert&#46;py</code></a> | The tests for the conversion script, run on made-up posts in a temporary folder. | 93 lines |

</div>

<section class="file">

## The skill file (<code>SKILL&#46;md</code>) {#skill-md}

<a href="#skill-md">Link to this file</a>

The instructions for the skill that converts an Obsidian blog post to Hugo Markdown. It is used when a post is ready to move from the notes vault to review.

<figure class="code">
<div class="code-bar"><span class="code-name">SKILL.md</span><span class="code-meta">Markdown · 52 lines</span><button type="button" class="ai-setup-copy copy" hidden>Copy</button></div>

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">---
name: obsidian-to-hugo
description: Convert an Obsidian blog post to Hugo-compatible markdown and place it in the blog projects folder for review
allowed-tools: Read, Bash, Glob, Grep, AskUserQuestion
---

# Obsidian to Hugo Converter

Convert a blog post written in Obsidian to Hugo-compatible markdown and place it in `~/ai/projects/blog/` for editing and review.

## Paths

- **Obsidian source**: `<span class="ph">&lt;vault-folder&gt;</span>/writing/blog/`
- **Destination**: `~/ai/projects/blog/posts-to-review/`

If the Obsidian path does not exist, ask the user for the correct vault location.

## Steps

### 1. Identify the source file

If the user specified a filename (as an argument or in conversation), use that. Otherwise, list the markdown files in the Obsidian source directory and ask which one to convert.

Match flexibly -- the user might give a partial name, a slug, or a title. Find the closest match in the source directory.

### 2. Run the conversion

Run the conversion script:

```bash
uv run ~/.claude/skills/obsidian-to-hugo/scripts/convert.py --file &quot;<span class="ph">&lt;filename&gt;</span>&quot; [--verbose]
```

Run it without asking. If it says the converted file already exists, ask before running again with `--force`: the copy may hold review edits. The script does not modify the original Obsidian file.

A `[[link]]` becomes a link only when the post exists in `<span class="ph">&lt;blog-folder&gt;</span>/content/posts/`; any other link (a note, a heading) is kept as text without a link, with a warning. Pictures, both `![[...]]` embeds and standard links such as Corillo&#x27;s, are copied next to the converted post.

Tests: `uv run --offline ~/.claude/skills/obsidian-to-hugo/scripts/test_convert.py`.

### 3. Report the result

After the script completes successfully, tell the user where the converted file landed. One line is enough. Example:

&gt; Converted to `~/ai/projects/blog/posts-to-review/my-post-title/my-post-title.md`

If the script reported any warnings (missing images, links kept as text), relay those.

### 4. Errors

If the script fails, show the error output and suggest a fix. Common issues:
- Source file not found -- check the filename and list available files
- Destination directory missing -- this shouldn&#x27;t happen, but create it if needed
</code></pre>

</figure>

</section>

## Scripts

These scripts are one person's working code, shared from Orlando's setup with private details replaced by placeholders. They are not a supported tool: read each one before you run it, and expect to change it to fit your own setup.

<section class="file">

### <code>convert&#46;py</code> {#convert-py}

<a href="#convert-py">Link to this file</a>

The script that turns an Obsidian post into Hugo Markdown. It rewrites picture embeds and links to other posts, copies the pictures, and saves the result in the review folder without touching the original.

**Needs to run:** uv.

<figure class="code">
<div class="code-bar"><span class="code-name">convert.py</span><span class="code-meta">Python · 323 lines</span><button type="button" class="ai-setup-copy copy" hidden>Copy</button></div>
<details class="ai-setup-fold fold">
<summary>Show the file (323 lines)</summary>

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere"># /// script
# requires-python = &quot;&gt;=3.12&quot;
# dependencies = []
# ///
&quot;&quot;&quot;
Obsidian to Hugo Converter

Converts Obsidian-native markdown to Hugo-compatible markdown and places
the result in ~/ai/projects/blog/posts-to-review/ for review with the writer agent.

Does NOT modify the original Obsidian file.
&quot;&quot;&quot;

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
    / &quot;<span class="ph">&lt;vault-folder&gt;</span>/writing/blog&quot;
)
DEFAULT_DEST = Path.home() / &quot;ai/projects/blog/posts-to-review&quot;
DEFAULT_BLOG = Path.home() / &quot;<span class="ph">&lt;blog-folder&gt;</span>/content/posts&quot;

# Obsidian&#x27;s size suffix on an embed: |400 or |400x300
SIZE_ONLY = re.compile(r&quot;^\d+(x\d+)?$&quot;)


def slugify(title: str) -&gt; str:
    &quot;&quot;&quot;Convert a post title to a Hugo-compatible slug.

    &#x27;My Great Post&#x27; -&gt; &#x27;my-great-post&#x27;
    &#x27;<span class="ph">&lt;yyyymmdd&gt;</span>-my-post&#x27; -&gt; &#x27;my-post&#x27;
    &quot;&quot;&quot;
    slug = title.lower().strip()
    # Strip leading date prefix (YYYYMMDD- or YYYY-MM-DD-)
    slug = re.sub(r&quot;^\d&#123;4&#125;-?\d&#123;2&#125;-?\d&#123;2&#125;-&quot;, &quot;&quot;, slug)
    slug = re.sub(r&quot;[^\w\s-]&quot;, &quot;&quot;, slug)  # Remove special chars
    slug = re.sub(r&quot;[\s_]+&quot;, &quot;-&quot;, slug)  # Spaces/underscores to hyphens
    slug = re.sub(r&quot;-+&quot;, &quot;-&quot;, slug)  # Collapse multiple hyphens
    slug = slug.strip(&quot;-&quot;)
    return slug


def find_code_regions(text: str) -&gt; list[tuple[int, int]]:
    &quot;&quot;&quot;Find all code block and inline code regions to skip during transforms.&quot;&quot;&quot;
    regions = []
    # Fenced code blocks
    for match in re.finditer(r&quot;```[\s\S]*?```&quot;, text):
        regions.append((match.start(), match.end()))
    # Inline code spans
    for match in re.finditer(r&quot;`[^`]+`&quot;, text):
        regions.append((match.start(), match.end()))
    return regions


def in_protected_region(pos: int, regions: list[tuple[int, int]]) -&gt; bool:
    &quot;&quot;&quot;Check if a position falls inside a protected region.&quot;&quot;&quot;
    return any(start &lt;= pos &lt; end for start, end in regions)


def find_frontmatter_end(text: str) -&gt; int:
    &quot;&quot;&quot;Find the end position of YAML frontmatter (if present).

    Returns 0 if no frontmatter found.
    &quot;&quot;&quot;
    if not text.startswith(&quot;---&quot;):
        return 0
    match = re.match(r&quot;^---\s*\n.*?\n---\s*\n&quot;, text, re.DOTALL)
    return match.end() if match else 0


def transform_image_embeds(
    text: str, code_regions: list[tuple[int, int]], fm_end: int
) -&gt; tuple[str, list[str]]:
    &quot;&quot;&quot;Convert Obsidian image embeds and local picture links to standard markdown.

    ![[image.png]]          -&gt; ![](image.png)
    ![[image.png|alt text]] -&gt; ![alt text](image.png)
    ![[image.png|400]]      -&gt; ![](image.png)          (size dropped)
    ![[Pasted image.png]]   -&gt; ![](Pasted%20image.png)
    ![Chart](post%20images/chart.png) -&gt; ![Chart](chart.png)   (Corillo&#x27;s form)

    Every picture ends up next to the post, so links use the bare, encoded name.
    Returns (transformed_text, list_of_referenced_images), where each image is
    its path relative to the source file, decoded.
    &quot;&quot;&quot;
    images = []
    embed = re.compile(r&quot;!\[\[([^\]|]+?)(?:\|([^\]]*))?\]\]&quot;)
    # Standard picture links to local files (not web addresses)
    standard = re.compile(r&quot;!\[([^\]]*)\]\((?![a-zA-Z][a-zA-Z0-9+.-]*:)([^)\s]+)\)&quot;)

    def skip(match):
        return in_protected_region(match.start(), code_regions) or match.start() &lt; fm_end

    def link(alt: str, path: str) -&gt; str:
        images.append(path)
        return f&quot;![&#123;alt&#125;](&#123;quote(Path(path).name)&#125;)&quot;

    def replace_embed(match):
        if skip(match):
            return match.group(0)
        alt = match.group(2).strip() if match.group(2) else &quot;&quot;
        if SIZE_ONLY.match(alt):
            alt = &quot;&quot;
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


def post_exists(blog_dir: Path, slug: str) -&gt; bool:
    &quot;&quot;&quot;True if the blog has a post at this slug (bundle folder or single file).&quot;&quot;&quot;
    return bool(slug) and (
        (blog_dir / slug).is_dir() or (blog_dir / f&quot;&#123;slug&#125;.md&quot;).is_file()
    )


def transform_wikilinks(
    text: str, code_regions: list[tuple[int, int]], fm_end: int, blog_dir: Path
) -&gt; tuple[str, list[str]]:
    &quot;&quot;&quot;Convert Obsidian wikilinks to Hugo ref links, only for posts that exist.

    [[Post Title]]              -&gt; [Post Title](&#123;&#123;&lt; ref &quot;post-title&quot; &gt;&#125;&#125;)
    [[Post Title|Display Text]] -&gt; [Display Text](&#123;&#123;&lt; ref &quot;post-title&quot; &gt;&#125;&#125;)
    [[Post Title#Heading]]      -&gt; same as [[Post Title]] (heading dropped)

    A link to anything that is not a published post (a note, a heading in this
    post) would stop the Hugo build, so it becomes its display text with no
    link. Returns (transformed_text, warnings).
    &quot;&quot;&quot;
    pattern = re.compile(r&quot;(?&lt;!!)\[\[([^\]|]+?)(?:\|([^\]]*))?\]\]&quot;)
    warnings = []

    def replace(match):
        if in_protected_region(match.start(), code_regions) or match.start() &lt; fm_end:
            return match.group(0)
        target = match.group(1).strip()
        page, _, heading = target.partition(&quot;#&quot;)
        page = page.strip()
        display = match.group(2).strip() if match.group(2) else (
            page or heading.lstrip(&quot;^&quot;).strip()
        )
        slug = slugify(page)
        if post_exists(blog_dir, slug):
            return f&#x27;[&#123;display&#125;](&#123;&#123;&#123;&#123;&lt; ref &quot;&#123;slug&#125;&quot; &gt;&#125;&#125;&#125;&#125;)&#x27;
        warnings.append(
            f&quot;link &#123;match.group(0)&#125; is not a post on the blog; kept the text without a link&quot;
        )
        return display

    return pattern.sub(replace, text), warnings


def convert_post(
    content: str, blog_dir: Path = DEFAULT_BLOG
) -&gt; tuple[str, list[str], list[str]]:
    &quot;&quot;&quot;Apply all transforms to a post&#x27;s content.

    Returns (converted_content, list_of_referenced_images, link_warnings).
    &quot;&quot;&quot;
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
) -&gt; Path:
    &quot;&quot;&quot;Process a single markdown file. Returns the output file path.&quot;&quot;&quot;
    slug = slugify(source_file.stem)
    project_dir = dest_dir / slug
    out_file = project_dir / f&quot;&#123;slug&#125;.md&quot;
    if out_file.exists() and not force:
        print(
            f&quot;Error: &#123;out_file&#125; already exists and may hold review edits. &quot;
            &quot;Run again with --force to replace it.&quot;,
            file=sys.stderr,
        )
        sys.exit(1)

    content = source_file.read_text(encoding=&quot;utf-8&quot;)
    converted, images, warnings = convert_post(content, blog_dir)

    project_dir.mkdir(parents=True, exist_ok=True)
    out_file.write_text(converted, encoding=&quot;utf-8&quot;)

    if not blog_dir.is_dir():
        print(f&quot;  Warning: blog posts folder not found: &#123;blog_dir&#125;&quot;, file=sys.stderr)
    for warning in warnings:
        print(f&quot;  Warning: &#123;warning&#125;&quot;, file=sys.stderr)

    if verbose:
        print(f&quot;  Source:  &#123;source_file&#125;&quot;)
        print(f&quot;  Output:  &#123;out_file&#125;&quot;)
        if images:
            print(f&quot;  Images:  &#123;&#x27;, &#x27;.join(images)&#125;&quot;)

    # Copy referenced images
    for img_name in images:
        img_source = source_file.parent / img_name
        if img_source.is_file():
            img_dest = project_dir / Path(img_name).name
            shutil.copy2(img_source, img_dest)
            if verbose:
                print(f&quot;  Copied image: &#123;img_name&#125;&quot;)
        else:
            print(f&quot;  Warning: image not found: &#123;img_source&#125;&quot;, file=sys.stderr)

    return out_file


def main():
    parser = argparse.ArgumentParser(
        description=&quot;Convert Obsidian markdown to Hugo-compatible format&quot;
    )
    parser.add_argument(
        &quot;--source&quot;,
        type=Path,
        default=DEFAULT_SOURCE,
        help=f&quot;Obsidian blog directory (default: &#123;DEFAULT_SOURCE&#125;)&quot;,
    )
    parser.add_argument(
        &quot;--dest&quot;,
        type=Path,
        default=DEFAULT_DEST,
        help=f&quot;Destination directory (default: &#123;DEFAULT_DEST&#125;)&quot;,
    )
    parser.add_argument(
        &quot;--blog&quot;,
        type=Path,
        default=DEFAULT_BLOG,
        help=f&quot;Blog posts folder, to check that linked posts exist (default: &#123;DEFAULT_BLOG&#125;)&quot;,
    )
    parser.add_argument(
        &quot;--force&quot;,
        action=&quot;store_true&quot;,
        help=&quot;Replace an existing converted copy (it may hold review edits)&quot;,
    )
    parser.add_argument(
        &quot;--file&quot;,
        type=str,
        required=True,
        help=&quot;Filename to convert (relative to source dir)&quot;,
    )
    parser.add_argument(
        &quot;--verbose&quot;,
        action=&quot;store_true&quot;,
        help=&quot;Print detailed conversion info&quot;,
    )

    args = parser.parse_args()

    # Validate source directory
    if not args.source.exists():
        print(f&quot;Error: Source directory not found: &#123;args.source&#125;&quot;, file=sys.stderr)
        print(&quot;Use --source to specify your Obsidian vault path.&quot;, file=sys.stderr)
        sys.exit(1)

    # Validate destination directory
    if not args.dest.exists():
        print(f&quot;Error: Destination directory not found: &#123;args.dest&#125;&quot;, file=sys.stderr)
        sys.exit(1)

    # Find the source file -- check root, then search subdirectories
    source_file = args.source / args.file
    if not source_file.is_file() and not args.file.endswith(&quot;.md&quot;):
        source_file = args.source / f&quot;&#123;args.file&#125;.md&quot;
    if not source_file.is_file():
        # Search subdirectories for a matching .md file
        name = args.file if args.file.endswith(&quot;.md&quot;) else f&quot;&#123;args.file&#125;.md&quot;
        matches = sorted(args.source.glob(f&quot;*/&#123;name&#125;&quot;))
        if matches:
            source_file = matches[0]
    if not source_file.is_file():
        print(f&quot;Error: File not found: &#123;source_file&#125;&quot;, file=sys.stderr)
        # List available files as a hint (root + one level deep)
        md_files = sorted(args.source.glob(&quot;*.md&quot;)) + sorted(
            args.source.glob(&quot;*/*.md&quot;)
        )
        if md_files:
            print(&quot;\nAvailable files:&quot;, file=sys.stderr)
            for f in md_files:
                rel = f.relative_to(args.source)
                print(f&quot;  &#123;rel&#125;&quot;, file=sys.stderr)
        sys.exit(1)

    out_file = process_file(
        source_file, args.source, args.dest, args.blog, args.verbose, args.force
    )
    print(f&quot;Converted to &#123;out_file&#125;&quot;)


if __name__ == &quot;__main__&quot;:
    main()
</code></pre>

</details>
</figure>

</section>

<section class="file">

### <code>test&#95;convert&#46;py</code> {#test-convert-py}

<a href="#test-convert-py">Link to this file</a>

The tests for the conversion script, run on made-up posts in a temporary folder. They check links, pictures, the guard against overwriting review edits, and file name matching.

**Needs to run:** uv.

<figure class="code">
<div class="code-bar"><span class="code-name">test_convert.py</span><span class="code-meta">Python · 93 lines</span><button type="button" class="ai-setup-copy copy" hidden>Copy</button></div>
<details class="ai-setup-fold fold">
<summary>Show the file (93 lines)</summary>

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere"># /// script
# requires-python = &quot;&gt;=3.12&quot;
# ///
&quot;&quot;&quot;Tests for convert.py, on made-up posts in a temp folder.

Run: uv run --offline test_convert.py [path/to/convert.py]
&quot;&quot;&quot;

import subprocess
import sys
import tempfile
import unittest
from pathlib import Path

SCRIPT = Path(sys.argv.pop(1) if len(sys.argv) &gt; 1 else Path(__file__).with_name(&quot;convert.py&quot;))


class ConvertTest(unittest.TestCase):
    def setUp(self):
        self.tmp = tempfile.TemporaryDirectory()
        root = Path(self.tmp.name)
        self.src, self.dest, self.blog = root / &quot;src&quot;, root / &quot;dest&quot;, root / &quot;posts&quot;
        for d in (self.src, self.dest, self.blog):
            d.mkdir()
        (self.blog / &quot;soil-basics&quot;).mkdir()
        (self.blog / &quot;another-post.md&quot;).write_text(&quot;x&quot;)

    def tearDown(self):
        self.tmp.cleanup()

    def run_convert(self, name, *extra):
        return subprocess.run(
            [sys.executable, str(SCRIPT), &quot;--source&quot;, str(self.src), &quot;--dest&quot;, str(self.dest),
             &quot;--blog&quot;, str(self.blog), &quot;--file&quot;, name, *extra],
            capture_output=True, text=True,
        )

    def post(self, body, folder=&quot;test-post&quot;):
        d = self.src / folder
        d.mkdir(exist_ok=True)
        (d / f&quot;&#123;folder&#125;.md&quot;).write_text(body)
        return d

    def out(self):
        return (self.dest / &quot;test-post&quot; / &quot;test-post.md&quot;).read_text()

    def test_links_only_to_existing_posts(self):  # L3
        self.post(&quot;[[soil-basics|earlier post]], [[market-segmentation|segments]], &quot;
                  &quot;[[Another Post#Part two]], [[the-dirt-book#^loc-200]], [[#Wrap up]].\n&quot;)
        r = self.run_convert(&quot;test-post.md&quot;)
        self.assertEqual(r.returncode, 0, r.stderr)
        self.assertEqual(self.out(), &#x27;[earlier post](&#123;&#123;&lt; ref &quot;soil-basics&quot; &gt;&#125;&#125;), segments, &#x27;
                         &#x27;[Another Post](&#123;&#123;&lt; ref &quot;another-post&quot; &gt;&#125;&#125;), the-dirt-book, Wrap up.\n&#x27;)
        self.assertEqual(r.stderr.count(&quot;not a post on the blog&quot;), 3)

    def test_pictures(self):  # L4
        d = self.post(&quot;![[Pasted image 2026.png]] ![[chart.png|400]] ![[plan.png|the plan]]\n&quot;
                      &quot;![Chart](test-post%20images/corillo-chart.png) ![Gone](missing.png) &quot;
                      &quot;![Video](https://youtu.be/abc)\n&quot;)
        for name in (&quot;Pasted image 2026.png&quot;, &quot;chart.png&quot;, &quot;plan.png&quot;):
            (d / name).write_bytes(b&quot;img&quot;)
        (d / &quot;test-post images&quot;).mkdir()
        (d / &quot;test-post images&quot; / &quot;corillo-chart.png&quot;).write_bytes(b&quot;img&quot;)
        r = self.run_convert(&quot;test-post.md&quot;)
        self.assertEqual(r.returncode, 0, r.stderr)
        self.assertEqual(self.out(), &quot;![](Pasted%20image%202026.png) ![](chart.png) ![the plan](plan.png)\n&quot;
                         &quot;![Chart](corillo-chart.png) ![Gone](missing.png) ![Video](https://youtu.be/abc)\n&quot;)
        for name in (&quot;Pasted image 2026.png&quot;, &quot;chart.png&quot;, &quot;plan.png&quot;, &quot;corillo-chart.png&quot;):
            self.assertTrue((self.dest / &quot;test-post&quot; / name).is_file(), name)
        self.assertIn(&quot;image not found&quot;, r.stderr)
        self.assertIn(&quot;missing.png&quot;, r.stderr)

    def test_second_run_keeps_review_edits(self):  # L5
        self.post(&quot;Hello.\n&quot;)
        self.assertEqual(self.run_convert(&quot;test-post.md&quot;).returncode, 0)
        out = self.dest / &quot;test-post&quot; / &quot;test-post.md&quot;
        out.write_text(&quot;Hello, edited.\n&quot;)
        r = self.run_convert(&quot;test-post.md&quot;)
        self.assertNotEqual(r.returncode, 0)
        self.assertIn(&quot;--force&quot;, r.stderr)
        self.assertEqual(out.read_text(), &quot;Hello, edited.\n&quot;)
        self.assertEqual(self.run_convert(&quot;test-post.md&quot;, &quot;--force&quot;).returncode, 0)
        self.assertEqual(out.read_text(), &quot;Hello.\n&quot;)

    def test_name_without_md_and_same_named_folder(self):  # L19
        self.post(&quot;Hello.\n&quot;)
        r = self.run_convert(&quot;test-post&quot;)
        self.assertEqual(r.returncode, 0, r.stderr)
        self.assertEqual(self.out(), &quot;Hello.\n&quot;)


if __name__ == &quot;__main__&quot;:
    unittest.main()
</code></pre>

</details>
</figure>

</section>

<nav class="page-foot">

Previous: <a href="/ai-setup/skills/connect/">Connect</a>.
Next: <a href="/ai-setup/skills/publish-post/">Publish post</a>.

<a href="/ai-setup/skills/">Back to Skills</a>

<a href="https://www.linkedin.com/in/oneillo/" target="_blank" rel="noopener">Borrowed something? Tell me how it went</a>

Text: <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" rel="license noopener">CC BY 4.0</a>. Scripts: <a href="https://opensource.org/license/mit" target="_blank" rel="license noopener">MIT</a>.

</nav>
