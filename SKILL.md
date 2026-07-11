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

### Publish at a namespaced URL (Pro)

Pro users with a username set in Account Settings can publish at permanent, human-readable URLs like `share.jotbird.com/@username/my-page`. Use `--namespace` instead of `--slug`:

```bash
npx jotbird publish --namespace my-page document.md
```

**Output:**
```
✨ Published → https://share.jotbird.com/@username/my-page
```

The `.jotbird` mapping records the full `@username/slug` path, so subsequent publishes without any flags update the same namespaced URL automatically:

```bash
npx jotbird publish document.md
```

```
✓ Updated → https://share.jotbird.com/@username/my-page
```

To delete a namespaced document:

```bash
npx jotbird remove --namespace my-page
```

### Delete a document

```bash
npx jotbird remove <slug>
```

Permanently removes the published page. The URL stops working.

### View or change page settings

Read a published page's theme, branding, and visibility — and change them without republishing. Accepts a tracked filename, a slug, or an `@username/slug` path.

```bash
# Show current settings
npx jotbird settings <file|slug>

# Change them
npx jotbird settings <file|slug> --theme minimal --visibility public
```

| Flag | Values |
|------|--------|
| `--theme <name>` | `default`, `minimal`, `essay`, `terminal` (non-default themes are Pro) |
| `--hide-branding` / `--show-branding` | Hide or show the JotBird footer branding (hiding is Pro) |
| `--visibility <state>` | `unlisted` (default, noindex), `public` (search-indexable), `password` (Pro) |
| `--password <pw>` | Page password, only with `--visibility password` |

Theme and branding changes apply to the live page right away. **Visibility changes can take up to about a minute** to reach the live page as the edge cache refreshes — the command has still succeeded, so don't re-run it or report a failure if the page looks unchanged; poll for up to a minute. (Turning password protection *on* is immediate; it's removing or relaxing it that waits on the cache.) Free accounts can always clear Pro settings (`--theme default`, `--show-branding`) and switch between `unlisted` and `public`; enabling a Pro setting on a free account fails and names the offending setting.

**Setting a password non-interactively.** Omitting `--password` opens an interactive prompt, which an agent cannot answer. Pipe the password in instead — `--password -` reads one line from stdin (it refuses to run on a terminal, where the password would be echoed in cleartext):

```bash
echo "$PAGE_PASSWORD" | npx jotbird settings my-page --visibility password --password -
```

Never put the password directly in the command line (`--password hunter2`): it lands in shell history and `ps` output. The `JOTBIRD_PAGE_PASSWORD` environment variable also works.

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

## Size limits

- **Markdown source:** up to 256 KB (measured in UTF-8 bytes, so non-ASCII text counts for more)
- **Rendered HTML:** up to 512 KB — the real ceiling, since formatting (CSS classes, math, syntax highlighting) expands the source

An over-limit document is rejected with a "too large" error. If that happens, split the content across multiple pages or trim it before retrying.

## Content lifetime

- Free accounts: URLs expire after 90 days. On expiry the link stops working (returns "not found") and the slug is freed for reuse; the document itself is kept in the account, so it can be republished.
- Pro accounts ($29/year): URLs never expire.
- Anonymous pages (published without an account): automatically deleted 30 days after publishing — both the page and its record are removed.

## Tips

- The published page is also available as raw Markdown by appending `.md` to the URL
- JotBird supports Obsidian-style callouts: `> [!note]`, `> [!warning]`, etc.
- Math works with `$inline$` and `$$block$$` syntax
- Mermaid diagrams render from ` ```mermaid ` code blocks
- Local images in the Markdown file are automatically uploaded during publish
