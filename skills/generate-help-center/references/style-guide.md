# Style Guide

All we are really trying to do is be helpful to our customers, but sometimes we make it so complicated for ourselves. These conventions exist to stop us from doing that.

The goal of every article is simple: a person has a problem, they find your article, they solve their problem, they leave. Nobody is reading help docs for the prose style. They want the answer, formatted so they can scan it, written so they can understand it, and accurate so it actually works. Everything in this guide serves that goal. If a rule here ever gets in the way of being genuinely helpful, the rule is wrong.

---

## Voice

- Second person: "you" and "your." Not "the user," "one," or "customers."
- Active voice: "Click **Save**" not "The Save button should be clicked."
- Present tense: "Your article appears" not "Your article will appear."
- Use contractions: "don't," "isn't," "you'll," "it's." They sound natural.
- Warm but not casual. Professional but not cold. The competent colleague, not the excited friend.

## Language

- Use the product's actual terminology from the codebase. Do not invent names for things.
- No marketing language: never "supercharge," "leverage," "unlock," "seamlessly," "powerful," "robust," "cutting-edge."
- No filler words: never "simply," "just," "easily," "quickly." If it were simple, the reader would not be reading the article.
- No emojis in article body.
- No exclamation marks. Zero. Help articles are not celebrations.
- No "In this article, you will learn..." preamble. Start with the answer or the context.
- Avoid hedging unless genuinely uncertain: "Click **Save**" not "You might want to click Save."
- Vary sentence structure. Do not start every sentence with "You can..." or "To do this..."

## Structure

- **Title case** for article titles: "Connecting a Custom Domain"
- **Sentence case** for headings within articles: "Setting up the environment"
- **H2** for major sections. **H3** for subsections within an H2. Stop there. If you need H4, the article is too complex -- split it or flatten the structure.
- Never skip heading levels (H2 directly to H4).
- One topic per article. If it takes more than 800 words, split it.
- Short paragraphs. Two to four sentences maximum. One idea per paragraph.

## Article openings

- Start with the answer or a one-sentence context statement.
- No background, history, or "In this article we'll cover..." preamble.
- The reader should know within two sentences whether they are in the right place.

Good: "Custom domains let your help center live at your own address -- like `help.yourcompany.com` -- instead of the default subdomain."

Bad: "Domain management is an important aspect of running a professional help center. In this article, we will walk through the process of connecting your own custom domain to your help center for a more branded experience."

## Excerpts

- 1-2 sentences, maximum 160 characters. No trailing period.
- Summarize what the article covers and who it is for.
- Write for someone scanning a list of articles, deciding which one to click.

Good: "Point your own domain to your help center with a single CNAME record"
Good: "Troubleshoot common issues when connecting a custom domain"
Bad: "This article explains how to connect a custom domain" (restates the title)
Bad: "Learn all about custom domain configuration" (vague, uses "learn")

## UI references

- Specific paths: "Go to **Settings > API Keys**" not "navigate to the settings area."
- Bold UI element names: **Save**, **New Article**, **Settings > Team**.
- Match exact labels from the product -- check the codebase if uncertain.
- Use "click" for buttons and links. Use "select" for dropdowns and options. Use "enter" for text fields.

## Code

- Always include a language identifier: ```bash, ```javascript, etc.
- Use real values from the codebase, not generic placeholders.
- When showing environment variables, use the exact key names from the codebase.
- Commands should be copy-pasteable without modification where possible.

## Content blocks

Use Selvo's content blocks to make articles scannable and structured. Each block type has a purpose -- use the right one for the situation.

**Callouts** (`> [!NOTE]`, `> [!TIP]`, `> [!WARNING]`, `> [!IMPORTANT]`, `> [!CAUTION]`):
- Use for information the reader should not miss -- warnings, prerequisites, useful tips.
- Most articles need zero to two callouts. If every paragraph has a callout, none stand out.
- Match severity to content: WARNING for real consequences, TIP for helpful shortcuts, NOTE for context.
- Do not use callouts for regular information that belongs in a paragraph.

**Steps** (`<Steps><Step title="...">...</Step></Steps>`):
- Use for sequential procedures with 3 or more steps.
- Each step title should be an action: "Create your account," "Add a CNAME record."
- Do not use for 2-step procedures (overkill) or for reference lists (use bullets).

**Accordions** (`<details><summary>...</summary>...</details>`):
- Use for supplementary content: FAQs, edge cases, provider-specific instructions, advanced options.
- Do not use for content every reader needs to see -- accordions hide by default.
- Good for "How to do this in [Provider X]" sections where the reader only needs one provider.

**Tables**:
- Use for structured comparisons: plan features, keyboard shortcuts, option references.
- Include column headers. Keep cells concise.

**Code blocks**:
- Use for any command, snippet, or configuration the reader might copy.
- Always specify the language for syntax highlighting.

**Numbered lists vs. bullet lists:**
- Numbered lists for steps that must happen in order.
- Bullet lists for items where order does not matter.

## Links

- Link between related articles where relevant.
- Prefer linking to your own articles. Link to external sources only when the reader needs information you cannot provide (third-party documentation, DNS provider guides, etc.).
- Use descriptive link text: "See [Connecting a custom domain](...)" not "Click [here](...)."

## Accuracy

- Include real configuration values and defaults from the codebase.
- If you cannot verify a claim from the codebase, flag it: "Verify the current value in [file or UI location]."
- Never fabricate API response shapes or configuration option names.
- If a feature is plan-gated, state which plan: "Available on Starter and above."
- Incorrect help articles are worse than no help articles. When in doubt, leave it out.

## Contact paths

Every article needs at least one way out. A support email, a contact form link, a "Still not working?" prompt -- something. The reader who cannot self-serve needs a clear next step, and a dead end is worse than no article at all.

This is not about adding a boilerplate footer to every page. It is about thinking: "What if this article does not solve their problem?" The troubleshooting template has a "Still not working?" section. The FAQ template ends with "Don't see your question?" The how-to template can include a TIP callout pointing to support. The pattern varies, but the principle is constant: never leave someone stranded.

The article templates already model this. When you follow a template, you inherit the escape hatch. That is deliberate -- it means every help center built with these templates has contact paths baked in from day one.

## Common mistakes

These come up in almost every knowledge base I have ever audited. Avoid them:

- **Writing essays instead of answers.** The reader is not here for your prose. Opening context, then immediately into steps or the answer. If your introduction is longer than your procedure, flip them.
- **Collections mapped to org charts.** "Engineering," "Platform," "Backend Services" -- meaningless to customers. If it sounds like a Jira project name, rename it.
- **Buried cancellation and deletion articles.** Hiding these erodes trust. Cancellation belongs in Billing, front and center. Account deletion belongs in Account settings, clearly named. People who cannot find how to leave will leave angrier.
- **FAQ as a dumping ground.** One giant FAQ with 40 unrelated questions is not a knowledge base -- it is a junk drawer. Theme your FAQ articles by topic (billing questions, getting started questions, security questions). If an answer needs more than 3 sentences, it deserves its own article.
- **Screenshots without descriptive captions.** A screenshot with no context is a picture of someone else's screen. Always include a caption that tells the reader what they are looking at: "Screenshot: The billing settings page showing the Change Plan button."
- **Too many Getting Started articles.** Four is the maximum. If it takes more than four articles to get someone to their first meaningful output, the onboarding needs work, not more documentation.

## What to avoid

- Do not describe internal implementation details users do not need.
- Do not write about hypothetical future behavior or upcoming features.
- Do not pad articles with preamble. Start with the first useful sentence.
- Do not use passive voice for instructions. "Click **Save**" not "The Save button can be clicked."
- Do not write "Note:" or "Warning:" as plain text. Use callout blocks instead -- they render with visual styling that readers can scan.
- Do not overuse bold. Bold key terms on first use in a section. If every other word is bold, nothing stands out.
