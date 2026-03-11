# Code-to-Article Mapping

Used by `/update-help-center` to connect code changes to potentially affected articles.

This is the default mapping for the Selvo codebase. Fork this skill and update the table for your own project.

---

## Code-to-Article Mapping

| Code Path Pattern | Article Topic Area | What to Check |
|-------------------|-------------------|---------------|
| `src/app/**/settings/` | Settings and configuration | New settings, changed defaults, removed options |
| `src/app/**/articles/` | Article management | Editor changes, workflow changes |
| `src/app/**/customize/` | Customization and theming | Theme options, branding, layout |
| `src/components/hc/` | Public help center display | Visual changes, layout changes |
| `src/lib/mcp/` | MCP and API integration | New tools, changed parameters, auth requirements |
| `src/lib/db/schema/` | Data model | New fields or constraints that are user-facing |
| `src/config/` | Plans, features, navigation | Plan limits, feature flags, nav changes |
| `README.md` | Getting started | Product description, setup steps |
| `CHANGELOG.md` | What's new | Release notes, new features |
| `package.json` | Setup and dependencies | User-facing dependency or script changes |
| `.env.example` | Configuration | Environment variables, setup instructions |
| `src/app/api/` | API reference | New endpoints, changed parameters, removed routes |
| `src/lib/actions/` | Feature workflows | Changed behavior users interact with |
| `src/app/**/billing/` | Billing and plans | Pricing changes, plan limit changes |

---

## Default Collection Routing

When creating new articles during `/update-help-center`, route to the best-matching collection.

Always call `list_collections` first and use the actual collection names that exist. Fall back to this table only if no match is found.

| Article Topic Pattern | Default Collection |
|----------------------|-------------------|
| Getting started, setup, onboarding, quickstart, install | Getting Started |
| Settings, configuration, preferences, options | Managing Your Account |
| API, MCP, integration, webhook, developer, SDK | Developers |
| Billing, pricing, plans, subscription, upgrade | Billing and Plans |
| Custom domain, DNS, hosting, SEO, subpath | Your Domain and SEO |
| Editor, formatting, content, writing, publishing | Writing Articles |
| Team, members, roles, permissions, access | Team Management |
| Troubleshooting, error, fix, issue, problem | Troubleshooting |
| Changelog, release, what's new, update | What's New |

---

## User-Facing vs. Internal Classification Guide

When classifying changed files, use this as a secondary reference.

**Always user-facing:**
- Anything in `src/app/**/page.tsx` (new routes = new features)
- Anything in `src/components/hc/` (renders in the public help center)
- `.env.example` changes (setup instructions change)
- `src/config/plans.ts` or equivalent (plan limits change)
- Error messages in server actions (users see these)

**Usually internal (skip unless behavior changes):**
- `src/lib/db/queries/` and `src/lib/db/mutations/` — unless the behavior exposed to users changes
- `src/lib/utils/` — utility functions
- Test files (`*.test.ts`, `*.spec.ts`, `__tests__/`)
- `src/types/` — TypeScript type definitions
- Build config: `next.config.ts`, `tailwind.config.ts`, `tsconfig.json`
- `src/lib/db/schema/` — unless a new column adds a user-visible field

**Gray areas (use judgment):**
- `src/lib/actions/` — server actions often expose user-facing behavior
- `src/lib/mcp/tools/` — changes here affect the MCP API users integrate with
- `src/components/app/` — dashboard UI changes (users see these, but usually minor)
