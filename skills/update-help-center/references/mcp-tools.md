# Selvo MCP Tools Reference

All 14 tools available via the Selvo MCP server at `https://app.selvo.co/mcp`.
Connect with:
```
claude mcp add --transport http selvo https://app.selvo.co/mcp \
  --header "Authorization: Bearer YOUR_API_KEY"
```

---

## Read Tools

### `get_help_center`

Returns the help center this API key is scoped to. No input required.

**Parameters:** none

**Response:**
```json
{
  "id": "hc_01J9...",
  "name": "Acme Help Center",
  "subdomain": "acme",
  "default_language": "en",
  "url": "https://acme.hc.selvo.co"
}
```

**Use for:** Verifying the MCP connection works. Always call this first.

---

### `list_articles`

List articles in your help center. Returns metadata only — use `get_article` for full content.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `status` | `"draft" \| "published" \| "scheduled" \| "archived"` | No | Filter by article status |
| `collection_id` | string | No | Filter by collection ID |

**Response:**
```json
{
  "articles": [
    {
      "id": "art_01J9...",
      "number": 12,
      "status": "published",
      "slug": "install-widget",
      "title": "Installing the widget",
      "excerpt": "Add the Acme widget to your site in two steps.",
      "collection": { "id": "col_01J9...", "name": "Getting Started" },
      "author": { "id": "usr_01J9...", "name": "Jane Smith" },
      "createdAt": "2026-03-01T10:00:00Z",
      "updatedAt": "2026-03-10T14:30:00Z"
    }
  ],
  "total": 1
}
```

**Use for:** Getting an overview of all articles. Pair with `get_article` to read content.

---

### `get_article`

Get the full content of an article as markdown, plus metadata.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `article_id` | string | One of these | The article ID (e.g. `art_01J9...`) |
| `number` | integer | One of these | The article number (e.g. `12`) |

**Response:** Markdown content followed by a metadata block:
```
## Installing the widget

Add the snippet below to your `<head>` tag...

---

id: art_01J9...
number: 12
title: Installing the widget
status: published
slug: install-widget
has_draft: false
collection: Getting Started
collection_id: col_01J9...
author: Jane Smith
created_at: 2026-03-01T10:00:00Z
updated_at: 2026-03-10T14:30:00Z
```

**Use for:** Reading article content before deciding whether it needs updating.

---

### `search_articles`

Search for articles by title. Use before creating to avoid duplicates.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | Yes | Search query (minimum 1 character) |
| `limit` | integer | No | Max results (default 10, max 50) |

**Response:**
```json
{
  "results": [
    {
      "id": "art_01J9...",
      "number": 12,
      "status": "published",
      "slug": "install-widget",
      "title": "Installing the widget",
      "excerpt": "Add the Acme widget to your site in two steps.",
      "collection_id": "col_01J9...",
      "updatedAt": "2026-03-10T14:30:00Z"
    }
  ],
  "total": 1,
  "query": "widget"
}
```

**Use for:** Checking for existing articles before creating new ones. Always call this before `create_article`.

---

### `list_collections`

List all collections as a tree structure with subcollections and article counts.

**Parameters:** none

**Response:**
```json
{
  "collections": [
    {
      "id": "col_01J9...",
      "name": "Getting Started",
      "description": "Set up and first steps",
      "slug": "getting-started",
      "icon": "lucide:rocket",
      "article_count": 4,
      "subcollections": []
    }
  ],
  "total": 1
}
```

**Use for:** Finding collection IDs before creating articles or collections.

---

### `get_collection`

Get a collection's details and its full article list.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `collection_id` | string | One of these | The collection ID (e.g. `col_01J9...`) |
| `number` | integer | One of these | The collection number from public URLs |

**Response:**
```json
{
  "id": "col_01J9...",
  "name": "Getting Started",
  "description": "Set up and first steps",
  "slug": "getting-started",
  "icon": "lucide:rocket",
  "parent_collection_id": null,
  "created_at": "2026-03-01T10:00:00Z",
  "updated_at": "2026-03-10T14:30:00Z",
  "articles": [
    {
      "id": "art_01J9...",
      "number": 12,
      "status": "published",
      "slug": "install-widget",
      "title": "Installing the widget",
      "excerpt": "...",
      "updatedAt": "2026-03-10T14:30:00Z"
    }
  ],
  "article_count": 1
}
```

---

## Write Tools

### `create_article`

Create a new article. Always creates in draft status unless `status: "published"` is passed.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `title` | string | Yes | The article title |
| `collection_id` | string | Yes | Collection to place this article in |
| `content` | string | No | Article body in markdown format |
| `status` | `"draft" \| "published"` | No | Initial status. Defaults to `"draft"` |
| `excerpt` | string | No | Short summary for listing pages and SEO (max 160 chars) |
| `slug` | string | No | URL slug. Auto-generated from title if omitted |

**Supported markdown features:** headings, bold, italic, code blocks, callouts (`> [!NOTE]`), accordions (`<details>`), steps (`<Steps><Step title="...">body</Step></Steps>`), tables, links.

**Response:**
```json
{
  "id": "art_01J9...",
  "number": 15,
  "slug": "install-widget",
  "status": "draft",
  "url": "https://acme.hc.selvo.co/articles/15-install-widget",
  "message": "Article created successfully."
}
```

**Rule:** Always create with `status: "draft"`. Never auto-publish.

---

### `update_article`

Update an article's content or metadata. Content changes go to a draft buffer. Use `publish: true` to promote to live.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `article_id` | string | Yes | The article ID |
| `title` | string | No | New article title (goes to draft) |
| `content` | string | No | New body in markdown (goes to draft, fully replaces) |
| `collection_id` | string | No | Move to a different collection |
| `status` | `"draft" \| "published" \| "archived"` | No | Change article status |
| `slug` | string | No | New URL slug |
| `excerpt` | string | No | Short summary |
| `seo_title` | string | No | SEO title tag override |
| `seo_description` | string | No | SEO meta description override |
| `publish` | boolean | No | When true, immediately publish the draft. Default: false |

**Response:**
```json
{
  "id": "art_01J9...",
  "number": 15,
  "published": false,
  "message": "Article draft updated successfully."
}
```

**Use for:** Full content replacement. For surgical section edits, prefer `update_article_content`.

---

### `update_article_content`

Surgically update a specific section of an article without replacing the entire content. Changes go to the draft buffer.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `article_id` | string | Yes | The article ID |
| `operation` | `"replace_section" \| "insert_after" \| "append"` | Yes | The update operation |
| `heading` | string | Conditional | Exact heading text to target (required for `replace_section` and `insert_after`) |
| `content` | string | Yes | New markdown content |
| `publish` | boolean | No | When true, publish the result immediately. Default: false |

**Operations:**
- `replace_section` — replaces all content under the target heading until the next heading of the same or higher level
- `insert_after` — inserts content after the target heading (before its first paragraph)
- `append` — adds content to the end of the article

**Response:**
```json
{
  "id": "art_01J9...",
  "number": 15,
  "operation": "replace_section",
  "heading": "Configuration",
  "preview": "Set the following environment variables...",
  "published": false,
  "message": "Article content draft updated successfully."
}
```

**Use for:** Updating a single section when the rest of the article is still accurate.

---

### `publish_article`

Publish an article, making it publicly visible. Promotes any pending draft changes.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `article_id` | string | Yes | The article ID to publish |

**Response:**
```json
{
  "id": "art_01J9...",
  "number": 15,
  "status": "published",
  "message": "Article published successfully."
}
```

---

### `unpublish_article`

Revert a published article to draft status. The article is no longer publicly visible.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `article_id` | string | Yes | The article ID to unpublish |

**Response:**
```json
{
  "id": "art_01J9...",
  "number": 15,
  "status": "draft",
  "message": "Article unpublished successfully."
}
```

---

### `delete_article`

Soft-delete an article. Cannot be undone via the API.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `article_id` | string | Yes | The article ID to delete |

**Response:**
```json
{
  "id": "art_01J9...",
  "number": 15,
  "message": "Article deleted successfully."
}
```

**Warning:** Destructive. Always confirm with the user before calling this.

---

### `create_collection`

Create a new collection. Collections can be nested up to one level deep.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Collection name |
| `description` | string | No | Description shown on the collection page |
| `icon` | string | No | Icon in `"lucide:icon-name"` format (e.g., `"lucide:rocket"`) or an emoji |
| `parent_collection_id` | string | No | ID of parent collection (creates subcollection) |

**Response:**
```json
{
  "id": "col_01J9...",
  "number": 3,
  "slug": "getting-started",
  "message": "Collection created successfully."
}
```

---

### `update_collection`

Update a collection's name, description, or icon. Omit fields to leave them unchanged.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `collection_id` | string | Yes | The collection ID |
| `name` | string | No | New name |
| `description` | string | No | New description |
| `icon` | string | No | Icon in `"lucide:icon-name"` format (e.g., `"lucide:rocket"`) or an emoji |

**Response:**
```json
{
  "id": "col_01J9...",
  "message": "Collection updated successfully."
}
```

---

### `delete_collection`

Soft-delete a collection. Cannot be undone via the API.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `collection_id` | string | Yes | The collection ID to delete |

**Response:**
```json
{
  "id": "col_01J9...",
  "message": "Collection deleted successfully."
}
```

**Warning:** Destructive. Always confirm with the user before calling this.

---

## Common Patterns

### Search before create
```
search_articles(query: "getting started") → check for matches
  → no match: create_article(title: "Getting started", status: "draft")
  → match found: ask user whether to update or skip
```

### Surgical section update
```
get_article(article_id: "art_01J9...")  → read current content
update_article_content(
  article_id: "art_01J9...",
  operation: "replace_section",
  heading: "Configuration",
  content: "New config instructions...",
  publish: false
)
```

### Full content replacement
```
update_article(
  article_id: "art_01J9...",
  content: "# Title\n\nNew full content...",
  publish: false
)
```
