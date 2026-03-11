# Distribution Copy

All listing copy for submitting Selvo to external directories and skill registries.

---

## PulseMCP

### Short Description (under 200 chars)

```
Help center for SaaS teams. 14-tool MCP server for articles, collections, and search. Create, update, and publish help center content from any MCP client.
```

### Long Description

```
Selvo is a standalone help center for SaaS teams. The MCP server
exposes 14 tools for managing your entire help center programmatically:

- List, create, update, delete, publish, and unpublish articles
- Manage collections (categories)
- Full-text search across your knowledge base
- Read help center configuration

Works with Claude Code, Claude Desktop, Cursor, Windsurf, and any
MCP-compatible client. Requires a Selvo account with an API key
(Starter plan or above).

Endpoint: https://app.selvo.co/mcp
Auth: Bearer token (API key from Settings > API Keys)
Transport: Streamable HTTP
```

### Tags

`documentation`, `knowledge-base`, `help-center`, `content-management`

---

## awesome-claude-skills PR Entry

Category: Business & Marketing (or Development & Code Tools)

```markdown
- [Selvo Help Center](https://github.com/selvoapp/skills) - Two skills for help center management: `/generate-help-center` creates articles from your codebase, `/update-help-center` detects and fixes stale docs. Publishes to Selvo via MCP. *By [@selvoapp](https://github.com/selvoapp)*
```

---

## awesome-agent-skills PR Entry

```markdown
<details>
<summary><h3 style="display:inline">Skills by Selvo</h3></summary>

- **[selvoapp/generate-help-center](https://github.com/selvoapp/skills/tree/main/skills/generate-help-center)** - Generate help center articles from your codebase, organized into collections
- **[selvoapp/update-help-center](https://github.com/selvoapp/skills/tree/main/skills/update-help-center)** - Detect and update stale documentation after code changes

</details>
```

---

## GitHub Topics

```bash
gh repo edit selvoapp/skills --add-topic agent-skills,claude-code,mcp,help-center,documentation,knowledge-base
```

---

## skills.sh Seeding

After repo is public:
```bash
npx skills add selvoapp/skills --all
```

This seeds the skills.sh index (87K+ skills directory). Each subsequent install by any user increments the counter.
