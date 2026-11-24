---
title: "Publish post"
date: 2026-11-22
lastmod: 2026-11-22
description: "AI-written: a skill that publishes a finished blog post to a Hugo site."
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

> **Written by AI.** This page was generated from Orlando's own setup by an automated run, and checked for private details before it was published. <a href="/ai-setup/#how-this-section-is-made">How this section is made</a>. Last updated 2026-11-22.

It finds the post, asks for the slug, date, and tags, builds the post folder, checks the site builds, then commits, pushes, and files the working folder away.

This skill is specific to Orlando's blog. It is here because it is part of his setup, and its steps may still be worth borrowing.

**Works with:** <a href="/ai-setup/skills/obsidian-to-hugo/">Obsidian to Hugo</a>.

## <code>SKILL&#46;md</code>

The steps Claude follows when asked to publish a finished blog post to the Hugo site, from finding the post to filing away its working folder.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">---
name: publish-post
description: Publish a finished blog post from the blog projects folder to the Hugo site and push to GitHub
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, AskUserQuestion
---

# Publish Blog Post

Take a finished blog post from `~/ai/projects/blog/` and publish it to the Hugo site at `&lt;blog-folder&gt;/`.

## 1. Identify the post

Find the post to publish. The user will either specify which project folder to use or you should look for posts in these locations:

- `~/ai/projects/blog/posts-to-review/`: posts converted from Obsidian via the `obsidian-to-hugo` skill. Each post lives in a subfolder named by slug (e.g., `posts-to-review/my-post-title/`), containing the markdown file and any images.
- `~/ai/projects/blog/`: other project folders (e.g., `&lt;project-folder&gt;/`). Each project folder may contain a subfolder for the post (e.g., `post-5/`).

Inside the post folder, expect:
- A markdown file with the post body (the longest `.md` file, usually named with a date prefix)
- A title-and-description file (e.g., `post-5-title-and-description.md`) with the title and description
- Zero or more image files (`.jpg`, `.jpeg`, `.png`)
- Possibly other working files (outlines, notes) that are NOT part of the published post

Read the title-and-description file to get the title and description. Read the post body file to get the content.

## 2. Determine the post slug

Ask the user what slug to use for the post URL. Suggest one based on the post content and existing posts in `&lt;blog-folder&gt;/content/posts/`. The slug becomes the folder name (for bundle posts) or filename (for single-file posts).

## 3. Determine the publish date

Ask the user what date to use, or suggest today&#x27;s date.

## 4. Determine the tags

Look at the post content and existing posts to suggest relevant tags. Ask the user to confirm.

## 5. Create the post in Hugo

If the post has images, create a page bundle:
- Create `&lt;blog-folder&gt;/content/posts/[slug]/`
- Copy all image files from the source folder into the bundle directory
- Create `index.md` with front matter and post content

If the post has no images, create a single file:
- Create `&lt;blog-folder&gt;/content/posts/[slug].md` with front matter and post content

### Front matter format

```yaml
---
title: &quot;[title from title-and-description file]&quot;
date: [YYYY-MM-DD]
draft: false
description: &quot;[description from title-and-description file]&quot;
tags: [&quot;tag1&quot;, &quot;tag2&quot;]
cover:
  image: &quot;&quot;
  alt: &quot;&quot;
  hidden: true
---
```

### YouTube embeds

Convert any markdown image links pointing to YouTube URLs into Hugo youtube shortcodes. Match patterns like:

- `![alt](https://youtu.be/VIDEO_ID)` or with query params `![alt](https://youtu.be/VIDEO_ID?rel=0)`
- `![alt](https://www.youtube.com/watch?v=VIDEO_ID)` or with extra params

Replace with: `&#123;&#123;&lt; youtube VIDEO_ID &gt;&#125;&#125;`

### Post content

Copy the post body as-is from the source markdown file, applying the YouTube embed conversion above. Do not make other content, formatting, or editorial changes.

## 6. Verify and build

List the files in the new post directory (or confirm the single file) and show the user the front matter for confirmation before committing.

Then build the site locally. A link to a post that does not exist only shows up in a build, and without this it would first show up on GitHub after the push. The flags keep the build&#x27;s output, cache, generated images and lock file out of the blog folder:

```bash
B=~/ai/tmp/blog-build-[slug]
git -C &lt;blog-folder&gt; status --short
HUGO_RESOURCEDIR=$B/resources hugo build --source &lt;blog-folder&gt; --destination $B/public --cacheDir $B/cache --noBuildLock --quiet
```

If the build reports an error, stop: show the error and don&#x27;t commit. Otherwise check that `$B/public/posts/[slug]/index.html` exists, then remove `$B` (`rm -rf` is fine inside `~/ai/tmp/`). Run `git -C &lt;blog-folder&gt; status --short` again: the only changes should be the new post&#x27;s files.

## 7. Commit and push

Stage the new post files, commit with the message format `Add post: [title]`, and push to the remote.

## 8. Finish

- Put the post&#x27;s address in place of `BLOG_URL` in the Substack file (`post-N-substack.md`) in the post&#x27;s working folder. The address is the `baseURL` in `&lt;blog-folder&gt;/hugo.toml` followed by `posts/[slug]/`.
- Move the post&#x27;s working folder (its `posts-to-review/[slug]/` folder or its dated project folder) to `~/ai/projects/blog/zzCompleted/`, keeping its name.
- Tell Orlando what is still his to do: paste the Substack version and post the LinkedIn blurb.
</code></pre>

Taken out: 8 paths and 1 private project name.
