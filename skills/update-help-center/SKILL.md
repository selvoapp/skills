---
name: update-help-center
description: >
  Detect and update stale help center articles on Selvo after code changes.
  Use when asked to "update docs", "sync documentation",
  "check for stale articles", or "refresh help center".
  Requires Selvo MCP server connection.
disable-model-invocation: true
metadata:
  author: Selvo
  version: 1.1.0
  mcp-server: selvo
allowed-tools:
  - mcp__selvo__get_help_center
  - mcp__selvo__list_articles
  - mcp__selvo__get_article
  - mcp__selvo__create_article
  - mcp__selvo__update_article
  - mcp__selvo__update_article_content
  - mcp__selvo__publish_article
  - mcp__selvo__unpublish_article
  - mcp__selvo__delete_article
  - mcp__selvo__search_articles
  - mcp__selvo__list_collections
  - mcp__selvo__get_collection
  - mcp__selvo__create_collection
  - Read
  - Glob
  - Grep
  - Bash
---

A knowledge base should be a living set of documents, not a dusty old history book. This skill keeps it alive.

Every day your docs sit unchanged while the product moves forward, you are building what I call the Museum of the Previously True(ish) — a collection of articles that were accurate once, that nobody has checked since, and that AI tools are now confidently serving to your customers as current truth. Stale documentation is not a minor housekeeping problem. It is actively making things worse. This skill detects what drifted and helps you fix it before your customers (or their AI assistants) find out the hard way.

$ARGUMENTS — Optional: `--since "1 week ago"`, `--full-scan`, `--dry-run`

Read the style guide first -- you will need it during classification, not just when writing:
- `Read(references/style-guide.md)`

Read the optional config file:
- If `.selvo/docs.yaml` exists in the project, read it.
- Use explicit `mappings` entries for classification and topic matching (checked before dynamic analysis).
- Use `ignore` entries to skip additional paths.
- Use `always_user_facing` entries to override internal classification.

Read the dismissal memory:
- If `.selvo/dismissed.yaml` exists in the project, read it.
- Skip any suggestions that match a previously dismissed change description — UNLESS the code around that area has changed significantly since the dismissal date (check git log for the relevant paths). If it has, re-surface the suggestion once with a note: "Previously dismissed, but the code has changed since then."

Read the mapping reference:
- `Read(references/mapping.md)`

### Phase 1: DETECT — what changed

Determine the detection mode:
- If `--since` flag provided: `git log --since="{date}" --name-only`
- If `--full-scan` flag provided: skip git, compare full codebase state
- Default: `git log --oneline -20` then `git diff HEAD~5 --name-only`

For each changed file, classify as USER-FACING or INTERNAL:

If `.selvo/docs.yaml` was loaded:
- Check `always_user_facing` patterns first — any match is USER-FACING.
- Check `ignore` patterns — any match is skipped entirely.
- Check `mappings[].paths` — any match uses the mapping's `topic` for article matching.
- For files not covered by the config, fall back to the rules below.

**USER-FACING** (require doc review):
- UI component changes visible to end users
- API parameter or response changes
- Configuration option added, removed, or renamed
- Pricing or plan feature changes
- Error message changes
- New feature or page added
- Authentication flow changes

**INTERNAL** (skip silently):
- Refactoring without behavioral change
- Performance optimizations
- Test file changes
- Build and tooling changes
- Internal code comments
- Type-only changes (unless they affect a public API)
- Dependency updates (unless user-visible)

Use `references/mapping.md` to connect changed files to article topic areas.

Build a change summary listing only user-facing changes with their inferred impact areas.

### Phase 2: MATCH — compare against existing articles

1. Fetch all published articles: `list_articles(status: "published")`
2. For each article, fetch full content: `get_article(article_id: "...")`
3. Apply the Three-Question Test to each article against the detected changes:
   - Does this article reference any of the changed code, config, or UI elements?
   - Does this article describe behavior that was modified?
   - Does this article contain code examples that use changed APIs or config?

4. Classify each article into one of six categories:

   **FRESH** — All three questions answered NO. Article matches current codebase.
   Action: Skip silently.

   **STALE** — One or more questions answered YES, but the core information is still valid.
   Article references features or config that changed, but is mostly correct.
   Action: Update specific sections. After updating, compare the excerpt to the new content -- if the excerpt no longer accurately summarizes the article, suggest an updated excerpt. Also check whether the article title uses terminology that has been renamed in the codebase; if so, flag for title update.

   **OUTDATED** — Article describes a fundamentally different workflow than the current state.
   The article would actively mislead a reader.
   Action: Flag for rewrite. Provide a full rewrite draft. Do NOT auto-update.

   **ORPHANED** — Article describes a feature that no longer exists in the codebase.
   Action: Flag for archival. Do NOT auto-archive or delete.

   **MISSING** — A user-facing feature exists with no corresponding article.
   Action: Suggest new article creation. Follow templates in `references/article-templates.md`.

   **DRIFT** — Article content is factually accurate but does not match current style guide or formatting standards. Examples: uses "Note:" as plain text instead of a callout block, uses "the user" instead of "you," uses H2 headings for steps instead of Steps blocks.
   Action: Flag for style refresh. Low priority. Only surface in `--full-scan` mode.

5. Apply confidence levels:

   **HIGH CONFIDENCE** (auto-suggest update):
   - Article quotes exact code, config, or UI text that changed
   - Article references a feature that was deleted
   - Article shows a workflow that was redesigned

   **MEDIUM CONFIDENCE** (flag for review):
   - Article covers a topic related to the change
   - May be affected but unclear without user testing

   **LOW CONFIDENCE** (skip):
   - Article is in the same domain but no direct connection
   - Change is minor and article is general enough to remain accurate

6. For each STALE or OUTDATED article, check for incoming links:
   - Search for the article's URL slug across all article content.
   - If found, flag the linking articles for review: "Article [X] links to [Y] which was updated. Verify the link text and context are still accurate."

### Phase 3: DECIDE and ACT

Apply output limits per run:
- Maximum 5 article updates
- Maximum 3 new articles suggested
- Maximum 2 archival suggestions

If more changes detected, prioritize in this order:
1. Articles with incorrect information (safety first)
2. Articles about deleted features (orphaned)
3. Articles with stale code examples
4. New articles for undocumented features
5. Minor wording updates (lowest priority)

For each finding, take action based on category:

**STALE:**
- Use `update_article_content` with `operation: "replace_section"`
- Target the specific heading that needs updating
- Preserve all other content unchanged (surgical update)
- If multiple sections need updating, use `update_article` as fallback
- Set `publish: false` — changes stay in draft buffer

**OUTDATED:**
- Present to user: "This article needs a significant rewrite: [reason]"
- Provide a full rewrite draft in your response
- Do NOT call `update_article` without explicit user confirmation

**ORPHANED:**
- Present to user: "This article describes [deleted feature]. Suggest archiving."
- Do NOT call `delete_article` or `unpublish_article` without confirmation

**MISSING:**
- Search before create: `search_articles(query: "[topic]")`
- If existing article found, suggest updating it instead
- If no match, call `create_article` with `status: "draft"`
- Route to the best-matching collection (see `references/mapping.md`)
- Follow article templates in `references/article-templates.md`

**DRIFT** (only in `--full-scan` mode):
- List affected articles with specific style issues
- Do NOT auto-update. Present as a low-priority style refresh backlog.

**FRESH:**
- Skip silently. Do not mention these in the report.

### Dry run mode

If `--dry-run` flag is provided:
- Run Phase 1 and Phase 2 only
- Present the full report
- Do NOT make any changes to the help center

### Dismissal memory

If the user declines a suggested update:
- Note what was suggested and why it was dismissed
- Do not re-suggest the same change in this session
- If `.selvo/dismissed.yaml` exists, append the dismissal. If not, create it (and the `.selvo/` directory if needed):

```yaml
version: 1
dismissed:
  - change: "Update webhook configuration section in 'Setting up webhooks'"
    reason: "Intentionally different from code — shows simplified example"
    date: 2026-03-12
    article: "Setting up webhooks"
  - change: "Document internal rate limiting middleware"
    reason: "Internal implementation detail, not user-facing"
    date: 2026-03-11
    article: "API Rate Limits"
```

Change-based reconsideration: when checking dismissed items, run `git log --since="{dismissed date}" --name-only` for the relevant code paths. If significant changes have occurred since dismissal, re-surface the suggestion ONCE with: "This was previously dismissed on {date} because: '{reason}'. However, the related code has changed since then. Worth another look?"

### Report format

Present results as a structured report before acting:

"Scanned [N] changed files, reviewed [M] articles.

Updates needed:
  [STALE] "Setting up webhooks" (last updated: 45 days ago) — Configuration section references old env var name
  [STALE] "Getting started" (last updated: 12 days ago) — Setup steps reference a deprecated CLI flag

New articles suggested:
  [MISSING] "Using the new dashboard" — New /dashboard route detected, no article covers it

Archival candidates:
  [ORPHANED] "Legacy API reference" — /api/v1 routes were removed in recent commits

No changes needed:
  [N] articles are still current.

Shall I proceed with the updates?"

Wait for confirmation before making any changes.
