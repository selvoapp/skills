# Code-to-Article Mapping Reference

Used by `/update-help-center` to decide which code changes affect documentation and how to match them to existing articles.

---

## How Matching Works

The update skill follows a four-step process for every code change:

1. **Detect the framework** — understand what kind of project this is so file paths make sense
2. **Classify each changed file** — is this change user-facing or internal?
3. **Match user-facing changes to existing articles** — find articles that describe what changed
4. **Route new articles to the right collection** — when new docs are needed, pick the best collection

If `.selvo/docs.yaml` exists in the project, it is checked first at each step and can override the defaults below.

---

## Step 1: Detect the Framework

Read the project's dependency and config files to understand the codebase structure before classifying any changes.

| File | Framework / Language |
|------|---------------------|
| `package.json` | Node.js — check `dependencies` for Next.js, Express, Fastify, NestJS, etc. |
| `requirements.txt` / `pyproject.toml` | Python — check for Django, Flask, FastAPI, etc. |
| `Gemfile` | Ruby on Rails or Sinatra |
| `go.mod` | Go |
| `Cargo.toml` | Rust |
| `pom.xml` / `build.gradle` | Java / Kotlin (Spring, etc.) |
| `composer.json` | PHP (Laravel, Symfony, etc.) |
| `mix.exs` | Elixir (Phoenix) |

Use this framework knowledge to interpret file paths. For example:
- In a **Next.js** project, `app/**/page.tsx` files are user-facing routes
- In a **Django** project, `views.py` and `urls.py` define user-facing routes
- In a **Rails** project, `app/controllers/` and `config/routes.rb` define routes
- In a **Laravel** project, `routes/web.php` and `app/Http/Controllers/` define routes

When in doubt, prefer reading the actual files rather than guessing from paths alone.

---

## Step 2: Classify Changes

For each changed file, place it into one of three categories.

### ALWAYS USER-FACING — always require doc review

- Route / page files (Next.js `page.tsx`, Rails `views/`, Django `templates/`, Express `routes/`)
- Config files that define user-visible settings or plan limits
- `README.md`, `CHANGELOG.md`, `CHANGELOG.rst`, `HISTORY.md`
- `.env.example` or any documented environment variable reference
- Error message strings that users see (flash messages, API error responses, validation messages)
- Pricing or plan definitions (any file that defines tiers, limits, or feature flags by plan)
- API endpoint handlers that change parameters, response shapes, or authentication requirements
- Authentication flow changes (login, signup, OAuth callbacks, token handling)
- CLI commands or scripts that users run directly

### ALWAYS INTERNAL — skip silently

- Test files (`*.test.*`, `*.spec.*`, `__tests__/`, `tests/`, `test/`, `spec/`)
- Build config (`webpack.config.*`, `vite.config.*`, `tsconfig.json`, `tailwind.config.*`, `babel.config.*`, `rollup.config.*`)
- Type definitions (unless they define a public API contract or SDK types users consume)
- CI/CD config (`.github/`, `.circleci/`, `.gitlab-ci.yml`, `Jenkinsfile`, `.travis.yml`)
- Lock files (`package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`, `Gemfile.lock`, `poetry.lock`, `Cargo.lock`)
- Internal code comments and documentation comments (JSDoc, docstrings, inline comments)
- Dependency version updates (unless the new version changes user-visible behavior)
- Linter / formatter config (`.eslintrc`, `.prettierrc`, `rubocop.yml`, `pyproject.toml` lint sections)
- Database migrations that only add internal columns or indexes (no user-visible fields)

### USE JUDGMENT — read the diff to decide

- **API handlers / server actions** — user-facing if they change behavior a user would notice (new parameter, changed response, different error, new rate limit)
- **Database schema** — user-facing if they add a field users see or a constraint users would hit
- **UI components** — user-facing if they change what users see or interact with; skip if they're purely internal styling or refactors
- **Utility / helper files** — usually internal unless they affect output format or user-visible behavior
- **Middleware** — user-facing if it changes auth flows, rate limits, redirects, or error responses
- **Email templates** — user-facing (users receive these); skip only if it's a developer-only notification
- **Webhook handlers** — user-facing if the webhook payload or behavior changes in ways integrators would notice

---

## Step 3: Match Changes to Articles

Do NOT use a static lookup table. Instead, match dynamically against the actual articles in the help center:

1. Call `list_articles(status: "published")` to get all published articles
2. Read each article's title, excerpt, and content
3. For each user-facing code change, apply the Three-Question Test (defined in SKILL.md):
   - Does this change affect how users set something up?
   - Does this change affect how a documented feature works?
   - Does this change add or remove a capability users would search for in the help center?
4. Match by topic semantics — ask: "does this code change affect what this article describes?" — not by file path pattern

This approach works for any codebase because it matches on meaning, not on file system conventions.

---

## Step 4: Route New Articles to Collections

When a code change requires a new article that does not match any existing article:

1. Call `list_collections` to get all existing collections in this help center
2. Match the article's topic to the most relevant collection by name and description — read what the collection contains, not just its name
3. If no collection matches well, suggest creating a new one and ask the user what to name it
4. **Never hardcode collection names** — always use the actual collections returned by `list_collections`

The following table is a fallback only, for when no collections exist yet or none match:

| Article Topic Pattern | Suggested Collection Name |
|----------------------|--------------------------|
| Getting started, setup, onboarding, quickstart, install | Getting Started |
| Primary product activity (the main thing users do) | Name as a verb phrase — e.g., "Writing and Editing", "Managing Projects", "Sending Campaigns" |
| Settings, configuration, preferences, account | Settings or Account |
| API, integration, webhook, developer, SDK | Developers or Integrations |
| Billing, pricing, plans, subscription, upgrade | Billing |
| Troubleshooting, errors, fixes | Fold into the relevant topic collection — e.g., a billing error goes in Billing, not a separate Troubleshooting collection |
| What's new, changelog, release notes | What's New |

---

## Custom Mappings (Optional)

For precise control, a project can define a `.selvo/docs.yaml` file. The `/update-help-center` skill checks this file first, before running dynamic analysis.

```yaml
# .selvo/docs.yaml — OPTIONAL
# Created by /generate-help-center or by hand.
# The update skill checks this file first, then falls back to dynamic analysis.
version: 1

mappings:
  - paths: ["src/billing/**", "config/plans.*"]
    topic: "billing and pricing"
  - paths: ["src/api/**", "routes/api/**"]
    topic: "API reference"
  - paths: ["src/integrations/**", "src/webhooks/**"]
    topic: "integrations and webhooks"

ignore:
  - "**/*.test.*"
  - "scripts/**"
  - "docs/internal/**"

always_user_facing:
  - "src/api/public/**"
  - "config/feature-flags.*"
```

**Field reference:**

- `mappings[].paths` — glob patterns matching code files in this project. When a changed file matches, the mapping's topic is used for article matching instead of dynamic analysis.
- `mappings[].topic` — the documentation topic area. Matched semantically against article titles and content. Write it as a plain description, not a collection name.
- `ignore` — glob patterns for files to always skip, in addition to the built-in ALWAYS INTERNAL rules above.
- `always_user_facing` — glob patterns for files that should always be classified as user-facing, overriding the ALWAYS INTERNAL and USE JUDGMENT categories.

This file is opt-in. Most projects work well without it. It is most useful when the codebase has unconventional structure or the dynamic analysis produces too many false positives.
