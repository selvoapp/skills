---
name: generate-help-center
description: >
  Generate help center articles from your codebase on Selvo.
  Use when asked to "create help docs", "generate help center",
  "write support articles", or "set up knowledge base".
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
  - mcp__selvo__search_articles
  - mcp__selvo__list_collections
  - mcp__selvo__get_collection
  - mcp__selvo__create_collection
  - Read
  - Glob
  - Grep
  - Bash
---

Generate help center articles for a product by analyzing its codebase
and publishing them to Selvo via MCP.

$ARGUMENTS — Optional: specific topics to focus on, or "all" for full generation.

### Prerequisites check

1. Verify Selvo MCP connection:
   - Call `get_help_center` to confirm connection works.
   - If it fails, tell the user to set up their MCP connection first:
     ```
     claude mcp add --transport http selvo https://app.selvo.co/mcp \
       --header "Authorization: Bearer YOUR_API_KEY"
     ```
   - Get an API key from Settings > API Keys in the Selvo dashboard.

2. Check existing content:
   - Call `list_collections` and `list_articles` to see what already exists.
   - If articles already exist, ask the user:
     "Your help center already has [N] articles in [M] collections.
      Do you want to add to the existing content or start fresh?"

### Content philosophy

Every knowledge base I have ever been hired to rewrite was originally written by someone who started from the feature list instead of the customer's problem. Do not be that person.

The help centers that drive people mad are the ones that obviously map to your internal structure. Nobody outside your company thinks in terms of "Content Management Module" or "User Administration Console." They think: "How do I change the logo?" Write for that person. Use their words, not yours.

Accuracy beats coverage, every time. An incorrect help article is worse than a missing one — your customers will follow bad instructions, get stuck, blame themselves, and then blame you. And now AI tools are slurping up documentation and using it to answer customers directly. They will happily pump out your old, wrong information with complete confidence. So get it right, or leave it out and flag what you could not verify.

Do not wait for perfect. Some knowledge base is better than no knowledge base, and it only gets harder the longer you wait. Write the article. Ship it as a draft. Let the human review it. But get it out of your head and onto the page.

Answer the need, not the question. Sometimes the customer asks the wrong question — they ask "How do I export to CSV?" when what they actually need is "How do I share this report with my boss?" Think about what they are trying to get done, not just what they typed into search.

One more thing: every article this skill generates is also a product demo. This is a help center platform. The articles ARE the showcase. Use Steps blocks, Callouts, Accordions, Tables — not because you can, but because a customer looking at these articles should think "I want my help center to look like this." If the help center for a help center product looks mediocre, that is a problem no amount of marketing can fix.

Always draft, never auto-publish. The human reviews before anything goes live.

### Step 1: Analyze the codebase

Read these files to build a mental model of the product:
- `README.md` — product description, features, setup instructions
- `package.json` — product name, dependencies, scripts
- Glob `src/app/**/page.tsx` or equivalent routing files (features, pages)
- Glob `src/config/**` or equivalent config files (settings, options)
- Glob `src/app/api/**` or `src/lib/actions/**` (API surface)
- Any `CHANGELOG.md` or `docs/` directory

Build a mental model of:
- What the product does (from README)
- What features it has (from routes and pages)
- What settings it exposes (from config files)
- What APIs it offers (from API routes or server actions)

### Step 1b: Think like the customer

Before you plan a single article, do the Kill That Question exercise. Look at the features you found in the codebase and ask yourself: "If I were on the support team for this product, which questions would I be sick of answering?" Those are your first articles. Not the features you think are clever. Not the ones the founder is proud of. The ones that generate tickets.

For each feature, imagine the customer at 11pm on a Tuesday, three tabs open, slightly frustrated. They are not browsing your documentation for fun. They have a problem and they want it gone. What did they type into the search box? That is your article title. What were they trying to accomplish when they got stuck? That is your article content.

Now group those articles. But here is where most knowledge bases go wrong — they group by how the company is organized, not by how the customer thinks. Your engineering team has a "Domain Configuration" service. Your customer has a question about "setting up my custom domain." Your product team calls it "Content Management." Your customer calls it "Writing and Editing." The collection names should make sense to someone who has never seen your org chart and never will.

Name everything from the outside in. If a collection name would make sense on a support ticket subject line, you are on the right track. If it sounds like a Jira project name, start over.

### Step 2: Plan the content structure

Based on the codebase analysis, plan collections and articles.

Default structure (adapt to the actual product):
- Getting Started (first-session setup, account creation, first task)
- [Primary task area] (the main thing users do in the product, named as a verb: "Writing and Editing," "Building Forms," "Managing Projects")
- [Secondary task area] (the second major activity)
- [Setup/Configuration area] (settings, customization, branding -- named for the customer need: "Design and Branding" not "Configuration")
- [Account management] (team, billing, security -- always last, least frequently accessed)
- Troubleshooting (only if there are enough known issues; otherwise fold into individual collections)

**When to use subcollections:**

Subcollections are visual groupings on the same collection page -- section headings that cluster related articles together. They don't add navigation depth or extra clicks. Think of them as paragraph breaks in a long essay: they help the reader scan.

- Use subcollections when a collection has 8 or more articles that fall into distinct clusters (at least 3 articles per group). Below that threshold, a flat list is easier to scan.
- One level of nesting only. Selvo supports parent collections with child subcollections, nothing deeper. If you find yourself wanting sub-subcollections, the collection is too broad -- split it.
- The same customer-language naming rule applies to subcollections. "Writing basics" not "Content creation module."

**Collection architecture -- the mistakes everyone makes:**

- "Troubleshooting" is an article TYPE, not a collection. Nobody browses a troubleshooting section -- they search for their specific problem. Login issues belong in Account. Payment failures belong in Billing. Integration errors belong in the integration's collection. Troubleshooting articles live where the topic lives.
- The primary task collection is the workhorse. Call it "Using [Product Name]" or whatever verb describes the main activity. Roughly 60% of help center content ends up here. It feels too big and undefined at first, but that is where customers spend their time.
- If a collection would only have 2-3 articles, fold it into a related collection. A collection with two articles looks abandoned, not organized.
- Getting Started is a PATH, not a MENU. It should be sequential -- do this, then this, then this. Four articles maximum. The goal is one thing: the customer's first meaningful output. Not a feature tour. Not a concept explainer. They should end up with something visible they can show someone.

For each collection, write a 1-sentence description of what the reader will find there. This appears on the help center homepage.

For each planned article, note:
- Title (in the customer's language)
- Target collection
- Key codebase files that inform the content
- Article type: getting-started, how-to, informational, reference, troubleshooting, faq, or integration-guide
- Order within the collection (frequency-based or workflow sequence)

Present the plan to the user before proceeding:

"I found [N] features in your codebase. Here is my proposed structure:

[Collection 1] — [1-sentence description]
  - Article 1: [title]
  - Article 2: [title]

[Collection 2] — [1-sentence description]
  ...

Shall I proceed, or would you like to adjust?"

Wait for confirmation before Step 3.

### Step 3: Create collections

For each planned collection:
1. Call `list_collections` to check if a matching collection already exists.
2. If it does not exist, call `create_collection` with name, description, and icon.
3. If the plan includes subcollections, create the parent collection first, then create each subcollection with `parent_collection_id` set to the parent's ID.
4. Record the `collection_id` for article assignment. Articles go into the subcollection they belong to, not the parent.

### Step 4: Generate and create articles

Read the references for templates and style:
- `Read(references/style-guide.md)` — writing conventions, including content block guidance
- `Read(references/article-templates.md)` — seven article structure templates

When writing articles, actively use Selvo's content blocks:
- Steps blocks for sequential procedures (3+ steps)
- Callout blocks for warnings, tips, and important notes
- Accordion blocks for supplementary content, provider-specific instructions, or FAQs
- Tables for structured comparisons
- Code blocks with language identifiers for technical content

Every article is a product showcase. The blocks you use demonstrate what the customer's own help center can look like.

For each planned article:
1. Read the relevant codebase files identified in Step 2.
2. Generate the article content following the matching template.
3. Follow the style guide exactly.
4. **Search before create:** Call `search_articles(query: "[article topic]")` to check for duplicates.
5. If a matching article exists, ask the user whether to update it or skip.
6. Call `create_article` with:
   - `title`
   - `collection_id` (from Step 3)
   - `content` (markdown, following the template)
   - `excerpt` (1-2 sentence summary, max 160 characters)
   - `status: "draft"` — ALWAYS draft, never auto-publish

### Step 5: Report results

Present a summary when done:

"Created [N] draft articles in [M] collections:

[Collection 1] (3 articles)
  - [Title 1] — draft
  - [Title 2] — draft
  - [Title 3] — draft

[Collection 2] (2 articles)
  ...

All articles are saved as drafts. Review them in your Selvo dashboard
at https://app.selvo.co and publish when ready."

### Step 5b: Offer to save codebase mapping

After reporting results, offer to save the codebase analysis as a mapping file for the update skill:

"I can save the codebase-to-topic mapping I used to a config file (`.selvo/docs.yaml`).
This helps the `/update-help-center` skill match future code changes to articles more precisely.

Want me to create it?"

If the user says yes:
1. Generate `.selvo/docs.yaml` based on the mappings discovered in Step 1.
2. Include `version: 1` header.
3. Map each collection's source files to a topic entry under `mappings`.
4. Add common internal paths to `ignore` (test files, build config, lock files).
5. Add any paths that required judgment calls to `always_user_facing` if they were classified as user-facing.
6. Create the `.selvo/` directory if it does not exist.
7. Write the file and confirm:

"Saved `.selvo/docs.yaml` with [N] mappings. The `/update-help-center` skill will use this
for more precise change detection. You can edit it anytime."

If the user says no, skip silently. Never auto-create this file.
