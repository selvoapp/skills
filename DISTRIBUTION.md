# Distribution Copy

All listing copy for submitting Selvo to external directories and skill registries.

---

## PulseMCP

### Short Description (under 200 chars)

```
Help center for SaaS teams. CLI and MCP server for articles, collections, and search. Create, update, and publish help center content from any AI agent.
```

### Long Description

```
Selvo is a standalone help center for SaaS teams. The skills use the
Selvo CLI to manage your entire help center programmatically:

- List, create, update, delete, publish, and unpublish articles
- Manage collections (categories) with subcollections
- Full-text search across your knowledge base
- Surgical section-level content updates
- Move and reorder articles and collections

Works with Claude Code, Codex, Cursor, Windsurf, and any
agent that supports slash commands or skills. Requires a Selvo
account with an API key (Starter plan or above).

CLI: npm install -g @selvo/cli
Auth: selvo login (API key from Settings > API Keys)
```

### Tags

`documentation`, `knowledge-base`, `help-center`, `content-management`

---

## awesome-claude-skills PR Entry

Category: Business & Marketing (or Development & Code Tools)

```markdown
- [Selvo Help Center](https://github.com/selvoapp/skills) - Two skills for help center management: `/generate-help-center` creates articles from your codebase, `/update-help-center` detects and fixes stale docs. Publishes to Selvo via CLI. *By [@selvoapp](https://github.com/selvoapp)*
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
gh repo edit selvoapp/skills --add-topic agent-skills,claude-code,cli,help-center,documentation,knowledge-base
```

---

## skills.sh Seeding

After repo is public:
```bash
npx skills add selvoapp/skills --all
```

This seeds the skills.sh index (87K+ skills directory). Each subsequent install by any user increments the counter.
