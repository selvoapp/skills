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
  version: 2.0.0
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
  - Write
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

2. Check for an existing generation plan:
   - If `.selvo/generation-plan.json` exists, read it.
   - If `status` is `"in_progress"`, offer to resume:
     "You have a generation in progress — [N] of [M] articles created. Resume from where you left off?"
   - If the user says yes, skip to Step 4 and continue from the first uncreated article.
   - If the user says no, delete the plan file and start fresh.

3. Check existing content:
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

Discovery happens in two passes. Pass 1 is cheap — file names and manifests only, no reading source code. Pass 2 reads a small number of strategic files identified by Pass 1. The goal is to build a mental model of what users can DO in this product, not to read every file.

#### Pass 1: Identify the project and its structure

1. **Read the manifest file** to identify the project type:
   - `package.json` → Node.js. Check `dependencies` for framework (Next.js, Express, Fastify, NestJS, Remix, Nuxt, SvelteKit, etc.)
   - `requirements.txt` / `pyproject.toml` / `setup.py` → Python. Check for Django, Flask, FastAPI, Starlette.
   - `Gemfile` → Ruby. Check for Rails, Sinatra.
   - `go.mod` → Go. Check for Gin, Echo, Fiber, Chi, or stdlib `net/http`.
   - `Cargo.toml` → Rust. Check for Actix, Axum, Rocket.
   - `composer.json` → PHP. Check for Laravel, Symfony.
   - `mix.exs` → Elixir. Check for Phoenix.
   - `pom.xml` / `build.gradle` → Java/Kotlin. Check for Spring Boot.
   - `pubspec.yaml` → Dart/Flutter.
   - No manifest → check for `index.html` (static site), `Dockerfile`, or `Makefile`.

2. **Read `README.md`** (if it exists) for the product description, features, and setup instructions.

3. **Run a file tree scan** to understand the directory structure. Do not read source files yet. You are looking for:
   - Where source code lives (`src/`, `app/`, `lib/`, `internal/`, `pkg/`)
   - Where config lives (`config/`, `.env.example`, settings files)
   - Where docs live (`docs/`, `CHANGELOG.md`)
   - Obvious feature groupings from directory names
   - Component and service files that hint at features (a file called `RestTimer.tsx` or `billing-service.ts` tells you a lot)

   Use Bash: `find . -type f -not -path '*/node_modules/*' -not -path '*/.git/*' -not -path '*/vendor/*' -not -path '*/__pycache__/*' -not -path '*/dist/*' -not -path '*/build/*' -not -path '*/.next/*' | head -200`

4. **Record what you found:**
   - Project type: web app / CLI / mobile app / desktop app / library / API / static site
   - Framework and language
   - Source root and directory layout
   - Routing style: file-based / config-based / decorator-based / unknown

#### Pass 2: Find features by reading entry points

You now know the framework. Use your knowledge of that framework to read the RIGHT files — not all files. You are looking for the product's user-facing surface: what can users see, click, configure, or call?

**For file-based routing** (Next.js App Router, SvelteKit, Nuxt, Remix, Astro):
- Glob the routing convention for the framework (e.g., `app/**/page.tsx` for Next.js App Router, `src/routes/**/+page.svelte` for SvelteKit). Read these files. Each file is a route. Each route is a potential feature to document.

**For config-based routing** (React Router, Vue Router, TanStack Router, Angular):
- Find the router configuration file — usually imported in the app's entry point (`src/App.tsx`, `src/router.tsx`, `src/router/index.ts`, `src/app-routing.module.ts`). Read it. It lists all routes and what components they render.

**For decorator-based routing** (Express, Flask, FastAPI, Django, Rails, Laravel, Go, Rust, Phoenix):
- Read the routing entry point for the framework:
  - Express/Fastify: `app.js`, `server.js`, or `index.js` — follow imports to route files
  - Flask/FastAPI: grep for `@app.route` or `@router.get` — these are the routes
  - Django: find and read `urls.py` files — they list all URL patterns
  - Rails: read `config/routes.rb` — it lists all routes
  - Laravel: read `routes/web.php` and `routes/api.php`
  - Go: grep for `HandleFunc`, `router.GET`, or equivalent
  - Phoenix: read `router.ex`
- You may need to follow 1-2 levels of imports to find all routes. That is fine.

**For CLIs:**
- Find the command definitions. Look for `yargs`, `commander`, `cobra`, `click`, `clap`, `argparse`, or the framework's command registration. Each command is a feature to document.

**For libraries/SDKs:**
- Find the public API. Look at the main export file (`index.ts`, `lib.rs`, `__init__.py`, the `main` or `exports` field in the manifest). Each exported function, class, or module is a potential feature to document.

**For mobile apps:**
- Find the navigation/screen definitions. React Native: look for `Navigator`, `Stack.Screen`. Flutter: look for `MaterialApp`, route definitions. Swift: look for `UIViewController` subclasses or SwiftUI views.

**For monorepos:**
- Check for `apps/`, `packages/`, or workspace config. Ask the user which app to document, or document each app separately.

**For anything else:**
- Read the entry point file (whatever the manifest points to) and follow imports one level. The structure will reveal itself.
- If you cannot determine the framework or find the entry points, ask the user: "I can see this is a [language] project but I'm not sure how it's structured. Can you point me to the main routing or entry point file?"

After reading the routing/entry files, also note:
- What imports each route pulls in that sound like user-facing features (hooks, services, controllers — not utilities like `cn`, `formatDate`, `dbClient`)
- Config files that define user-visible settings or feature flags
- API route handlers (if the product has an API users call directly)
- Any `CHANGELOG.md` for recent feature additions

#### What you should know after Step 1

Build a mental model of:
- What the product does (from README)
- What features it has (from routes, commands, screens, or public API)
- What settings it exposes (from config)
- What APIs it offers (from API routes, if applicable)
- What the user's primary workflow is (the main thing they do)
- For each feature: which source files contain the implementation logic (you will read these in Step 4, not now)

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
- Key codebase files that inform the content (the files you will read in Step 4)
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

After the user approves, save the plan to `.selvo/generation-plan.json`:

```json
{
  "version": 1,
  "collections": [
    {
      "name": "Getting Started",
      "description": "Set up your account and complete your first task",
      "articles": [
        {
          "title": "Creating Your Account",
          "source_files": ["app/register/page.tsx", "lib/auth.ts"],
          "type": "getting-started",
          "collection_id": null
        }
      ]
    }
  ],
  "created": [],
  "status": "in_progress"
}
```

Create the `.selvo/` directory if it does not exist. This file is your bookmark — it survives context limits and allows resuming if the session ends mid-run.

### Step 3: Create collections

For each planned collection:
1. Call `list_collections` to check if a matching collection already exists.
2. If it does not exist, call `create_collection` with name, description, and icon.
3. If the plan includes subcollections, create the parent collection first, then create each subcollection with `parent_collection_id` set to the parent's ID.
4. Record the `collection_id` for article assignment. Articles go into the subcollection they belong to, not the parent.
5. Update the plan file with the assigned `collection_id` values.

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
1. **Deep-dive: read the source files listed in the plan for this article.** This is where you read the actual implementation — the services, hooks, components, and controllers that contain HOW the feature works. Follow imports one level deep if needed:
   - **Follow** imports whose names describe something a user would recognize as a feature (`useRestTimer`, `PaymentProcessor`, `DeloadSettings`, `PRCalculator`).
   - **Skip** imports that describe developer plumbing (`cn`, `formatDate`, `dbClient`, `middleware`, `logger`, `withAuth`).
   - Do not read files from previous articles unless they are listed for the current one.
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
7. **Update the plan file:** add the article ID to the `created` array.

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

Update the plan file: set `status` to `"complete"`. Then delete `.selvo/generation-plan.json` — it has served its purpose.

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
