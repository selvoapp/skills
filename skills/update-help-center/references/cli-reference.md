# Selvo CLI Reference

Install: `npm install -g @selvo/cli`
Authenticate: `selvo login` (prompts for API key) or set `SELVO_API_KEY` env var.
Verify setup: `selvo doctor`

All commands support `--json` for machine-readable output and `--quiet` (`-q`) to suppress spinners and skip confirmation prompts.

---

## Help Center

### `selvo help-center get`

Returns the help center this API key is scoped to.

```bash
selvo help-center get --json
```

**Use for:** Verifying the CLI connection works.

---

## Articles

### `selvo articles list`

List articles. Returns metadata only — use `selvo articles get` for full content.

```bash
selvo articles list --json
selvo articles list --status published --json
selvo articles list --status draft --collection col_xxx --json
```

| Flag | Description |
|------|-------------|
| `--status` | Filter: `draft`, `published`, `scheduled`, `archived` |
| `--collection` | Filter by collection ID |

---

### `selvo articles get`

Get an article by ID or number.

```bash
selvo articles get 12 --raw       # Clean markdown content only
selvo articles get art_xxx --json  # Full metadata as JSON
```

| Flag | Description |
|------|-------------|
| `--raw` | Markdown content only, no metadata |
| `--json` | Full article data as JSON |

**Use for:** Reading article content before deciding whether it needs updating. Use `--raw` when you need the markdown body, `--json` when you need metadata (dates, status, collection).

---

### `selvo articles search`

Search articles by title. Use before creating to avoid duplicates.

```bash
selvo articles search "custom domain" --json
```

**Use for:** Checking for existing articles before creating new ones. Always search before `selvo articles create`.

---

### `selvo articles create`

Create a new article. Always use `--status draft` unless explicitly told to publish.

```bash
selvo articles create \
  --title "Installing the widget" \
  --collection col_xxx \
  --file ./article.md \
  --excerpt "Add the widget to your site in two steps" \
  --status draft \
  --json
```

| Flag | Required | Description |
|------|----------|-------------|
| `--title` | Yes | Article title |
| `--collection` | Yes | Collection ID |
| `--file` | No* | Path to markdown file for content |
| `--content` | No* | Inline markdown content |
| `--status` | No | `draft` (default) or `published` |
| `--excerpt` | No | Short summary, max 160 chars |
| `--slug` | No | URL slug (auto-generated from title if omitted) |
| `--seo-title` | No | SEO title override |
| `--seo-description` | No | SEO meta description override |
| `--no-writeback` | No | Skip writing uploaded image URLs back to the source file |

*Use `--file` for anything longer than a sentence. It avoids shell escaping issues with markdown content.

**Auto-upload:** When using `--file`, local image paths in markdown (`![alt](./path.png)`) are automatically uploaded and replaced with public URLs. The source file is updated on disk so subsequent runs won't re-upload. Use `--no-writeback` to skip modifying the source file.

**Supported markdown:** headings, bold, italic, code blocks, callouts (`> [!NOTE]`), accordions (`<details>`), steps (`<Steps><Step title="...">body</Step></Steps>`), tables, links, images.

**Rule:** Always create with `--status draft`. Never auto-publish.

---

### `selvo articles update`

Update an article's content or metadata. Content changes go to a draft buffer.

```bash
selvo articles update art_xxx --file ./updated.md --json
selvo articles update art_xxx --title "New title" --excerpt "New summary" --json
selvo articles update art_xxx --file ./updated.md --publish --json
```

| Flag | Description |
|------|-------------|
| `--title` | New title (goes to draft) |
| `--file` | Path to markdown file (fully replaces content, goes to draft) |
| `--content` | Inline markdown (fully replaces content) |
| `--slug` | New URL slug |
| `--excerpt` | New excerpt |
| `--seo-title` | SEO title override |
| `--seo-description` | SEO meta description override |
| `--publish` | Publish immediately after updating |
| `--no-writeback` | Skip writing uploaded image URLs back to the source file |

**Auto-upload:** Same as `articles create` — local image paths in `--file` markdown are auto-uploaded. Use `--no-writeback` to skip modifying the source file.

**Use for:** Full content replacement. For surgical section edits, use `selvo articles content update`.

---

### `selvo articles content update`

Surgically update a specific section without replacing the entire article. Changes go to the draft buffer.

```bash
# Replace a section
selvo articles content update art_xxx \
  --operation replace_section \
  --heading "Configuration" \
  --file ./new-config-section.md

# Append to the end
selvo articles content update art_xxx \
  --operation append \
  --file ./new-section.md

# Insert after a heading
selvo articles content update art_xxx \
  --operation insert_after \
  --heading "Step 1" \
  --file ./step1-details.md
```

| Flag | Required | Description |
|------|----------|-------------|
| `--operation` | Yes | `replace_section`, `insert_after`, or `append` |
| `--heading` | Conditional | Exact heading text (required for `replace_section` and `insert_after`) |
| `--heading-level` | No | Heading level 1-6 (helps disambiguate duplicate headings) |
| `--file` | No* | Path to markdown file with new content |
| `--content` | No* | Inline markdown content |
| `--publish` | No | Publish after updating |

**Use for:** Updating a single section when the rest of the article is still accurate.

---

### `selvo articles publish`

Publish an article. Promotes any pending draft changes.

```bash
selvo articles publish art_xxx
```

---

### `selvo articles unpublish`

Revert a published article to draft status.

```bash
selvo articles unpublish art_xxx
```

---

### `selvo articles delete`

Soft-delete an article. Cannot be undone.

```bash
selvo articles delete art_xxx        # Prompts for confirmation
selvo articles delete art_xxx -q     # Skips confirmation
```

**Warning:** Destructive. Always confirm with the user before running.

---

### `selvo articles move`

Move an article to a different collection.

```bash
selvo articles move art_xxx --collection col_yyy
```

---

### `selvo articles reorder`

Set the display order for articles within a collection.

```bash
selvo articles reorder --ids art_xxx,art_yyy,art_zzz
```

---

## Collections

### `selvo collections list`

List all collections as a tree with subcollections and article counts.

```bash
selvo collections list --json
```

**Use for:** Finding collection IDs before creating articles.

---

### `selvo collections get`

Get a collection's details and its article list.

```bash
selvo collections get col_xxx --json
```

---

### `selvo collections create`

Create a new collection. Nest up to one level deep with `--parent`.

```bash
selvo collections create \
  --name "Getting Started" \
  --description "Set up and first steps" \
  --icon "lucide:rocket" \
  --json

# Create a subcollection
selvo collections create \
  --name "Authentication" \
  --parent col_xxx \
  --json
```

| Flag | Required | Description |
|------|----------|-------------|
| `--name` | Yes | Collection name |
| `--description` | No | Description shown on the collection page |
| `--icon` | No | Icon: `"lucide:icon-name"` format or emoji |
| `--parent` | No | Parent collection ID (creates subcollection) |

---

### `selvo collections update`

Update a collection's name, description, or icon.

```bash
selvo collections update col_xxx --name "New name" --icon "lucide:code" --json
```

---

### `selvo collections delete`

Soft-delete a collection. Cannot be undone.

```bash
selvo collections delete col_xxx      # Prompts for confirmation
selvo collections delete col_xxx -q   # Skips confirmation
```

**Warning:** Destructive. Always confirm with the user before running.

---

### `selvo collections reorder`

Set the display order for collections.

```bash
selvo collections reorder --ids col_xxx,col_yyy,col_zzz
```

---

## Media

### `selvo media upload`

Upload an image and get a public URL. Supported types: JPEG, PNG, GIF, WebP, SVG, ICO. Max 10 MB for article images.

```bash
selvo media upload ./screenshot.png --json
selvo media upload ./logo.svg --category hc-logo --json
```

| Flag | Description |
|------|-------------|
| `--category` | `article-image` (default), `hc-logo`, `hc-favicon`, `hc-hero-bg`, `collection-icon` |

Returns:
```json
{ "url": "https://...", "filename": "screenshot.png", "content_type": "image/png", "size": 245760 }
```

**Use for:** Uploading images before referencing them in article markdown, or uploading help center branding assets.

**Tip:** When using `--file` with `articles create` or `articles update`, local image paths are auto-uploaded — you don't need to upload manually in that workflow.

---

## Common Patterns

### Search before create
```bash
selvo articles search "getting started" --json
# → no match: create the article
# → match found: ask user whether to update or skip
```

### Write to file, then create (recommended for long content)
```bash
mkdir -p .selvo/tmp
# Write article markdown to .selvo/tmp/article-slug.md
selvo articles create \
  --title "Getting started" \
  --collection col_xxx \
  --file .selvo/tmp/article-slug.md \
  --status draft \
  --json
```

### Surgical section update
```bash
selvo articles get art_xxx --raw     # Read current content
# Write replacement section to .selvo/tmp/patch.md
selvo articles content update art_xxx \
  --operation replace_section \
  --heading "Configuration" \
  --file .selvo/tmp/patch.md
```

### Full content replacement
```bash
# Write new content to .selvo/tmp/rewrite.md
selvo articles update art_xxx --file .selvo/tmp/rewrite.md
```

### Articles with local images (auto-upload)
```bash
# article.md contains: ![Setup](./images/setup.png)
selvo articles create \
  --title "Setup Guide" \
  --collection col_xxx \
  --file ./article.md \
  --status draft \
  --json
# → Uploads ./images/setup.png, replaces path with URL in article.md, sends to API
```

### Upload first, then reference (manual workflow)
```bash
url=$(selvo media upload ./diagram.png --json | jq -r '.url')
# Write markdown referencing $url, then create article
```

### Batch create from a directory
```bash
for file in ./docs/*.md; do
  title=$(head -1 "$file" | sed 's/^# //')
  selvo articles create --title "$title" --collection col_xxx --file "$file" --status draft -q
done
```
