---
name: skill-jotbird
description: Publish Markdown as shareable web pages on JotBird. Use this skill when the user wants to publish content to the web, share a document via URL, create a shareable link from markdown, update or delete a published page, or turn AI output into a hosted web page. Also use when the user mentions JotBird, publishing notes, sharing meeting summaries, or creating quick web pages from text.
metadata:
  author: jotbirdhq
  version: "1.0"
---

# JotBird — Publish Markdown to the Web

Publish any Markdown as a beautifully formatted, shareable web page. One command, instant URL.

## When to use this skill

- The user asks to publish, share, or host content as a web page
- The user wants to turn text, notes, or AI output into a shareable link
- The user mentions JotBird by name
- The user wants to update or remove a previously published page
- The user wants to list their published documents

## Prerequisites

The JotBird CLI requires authentication. If the user hasn't logged in before, run:

```bash
npx jotbird login
```

This opens a browser for the user to sign in and generate an API key. The key is saved locally for future use.

If the user already has an API key (`jb_...`), they can set it as `JOTBIRD_API_KEY` in their environment instead.

## Commands

### Publish a document

Write the Markdown content to a temporary file, then publish it:

```bash
npx jotbird publish document.md
```

To publish with a custom URL slug:

```bash
npx jotbird publish document.md --slug my-page-name
```

You can also pipe content directly via stdin:

```bash
echo "# Hello World" | npx jotbird publish -
```

**Output:**
```
✨ Published → https://share.jotbird.com/my-page-name
```

Always show the user the URL from the output. This is the shareable link to their page.

### Update a document

To update an existing page, publish the same file again. The CLI remembers the slug mapping and updates in place:

```bash
npx jotbird publish document.md
```

```
✓ Updated → https://share.jotbird.com/my-page-name
```

To update a specific slug explicitly:

```bash
npx jotbird publish document.md --slug existing-page
```

The URL stays the same. The content is replaced.

### List published documents

```bash
npx jotbird list
```

Shows all the user's published documents with their URLs, slugs, and expiration dates.

### Delete a document

```bash
npx jotbird remove <slug>
```

Permanently removes the published page. The URL stops working.

## Workflow

1. **Compose the Markdown.** Write the content the user wants to publish. Use proper Markdown formatting — headings, lists, code blocks, links, etc. JotBird renders it with full Markdown support including tables, math (KaTeX), footnotes, callouts, and Mermaid diagrams.

2. **Write to a file.** Save the Markdown to a `.md` file. Local images referenced in the document are automatically uploaded during publish.

3. **Publish.** Run `npx jotbird publish <file>`. If the user wants a custom slug, add `--slug <name>`.

4. **Share the URL.** Always show the user the URL printed by the CLI. This is the shareable link to their page.

5. **Update if needed.** Run the same publish command again with the same file. The CLI reuses the slug automatically.

## Slug rules

- Lowercase letters, numbers, and hyphens only
- Must start and end with a letter or number
- 3-60 characters
- If not provided, JotBird generates a random three-word slug

## Content lifetime

- Free accounts: URLs expire after 90 days
- Pro accounts ($29/year): URLs never expire
- Expired pages are automatically deleted

## Tips

- The published page is also available as raw Markdown by appending `.md` to the URL
- JotBird supports Obsidian-style callouts: `> [!note]`, `> [!warning]`, etc.
- Math works with `$inline$` and `$$block$$` syntax
- Mermaid diagrams render from ` ```mermaid ` code blocks
- Local images in the Markdown file are automatically uploaded during publish
