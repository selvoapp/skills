# Article Templates

Seven templates for generated articles. Choose based on the article's purpose.

Every template ends with a "What to read next" section. Internal links between articles improve navigation and help readers discover related content.

---

## 1. Getting Started

Use for: initial setup, onboarding, first-time workflows. The reader is doing this for the first time.

```markdown
# [Title]

[1-2 sentence introduction: what the reader will accomplish and why.]

## What you need

- [Requirement 1 -- be specific: plan level, permission, prerequisite step]
- [Requirement 2]

## [Main procedure heading]

<Steps>

<Step title="[Action verb + object]">

[Instructions with specific UI paths. Example: "Go to **Settings > API Keys** and click **New Key**."]

</Step>

<Step title="[Action verb + object]">

[Instructions. Include real config values or defaults from the codebase.]

</Step>

<Step title="[Action verb + object]">

[Instructions. End with what the reader should see when done: "Your help center is now live at `your-slug.hc.selvo.co`."]

</Step>

</Steps>

## What to read next

- [Link to natural next article]
- [Link to related article]
```

**Rules:**
- Lead with the outcome, not the product name.
- Use the Steps block for 3+ sequential steps.
- "What you need" only if there are real prerequisites. Skip it if there are none.
- End each step with what the reader should see or expect.
- Maximum 500 words.
- Getting Started is a PATH, not a MENU. Articles should be sequential -- each one picks up where the last left off.
- Four articles maximum. If it takes more, the product's onboarding needs work, not more documentation.
- No feature tours. One goal: the customer's first meaningful output. They should end up with something visible they can show someone -- a published page, a sent message, a created project.
- Skip concept explanations here. Save those for "Understanding [Feature]" informational articles elsewhere.

---

## 2. How-To

Use for: specific tasks and workflows the reader returns to repeatedly. The most common article type.

```markdown
# [Title -- task description like "Connecting a custom domain" or "Inviting team members"]

[1-2 sentence context: what this is and when you would do it.]

## [Procedure heading]

<Steps>

<Step title="[Action verb + object]">

[Instructions with specific UI paths.]

</Step>

<Step title="[Action verb + object]">

[Instructions.]

</Step>

</Steps>

> [!TIP]
> [Optional: a tip that saves time or prevents a common mistake.]

## [Optional: advanced usage or configuration]

[Additional options or less common workflows. Use an accordion if there are multiple provider-specific variations.]

## What to read next

- [Link to related article]
```

**Rules:**
- Title describes the task, not the feature name.
- One task per article. If it exceeds 800 words, split it.
- Include the "why" only briefly -- focus on the "how."
- Use Steps block for sequential procedures, numbered lists for simpler sequences, bullets for options.
- Use accordions for provider-specific instructions or advanced details most readers can skip.

---

## 3. Informational

Use for: explaining a concept, feature, or system. No procedural steps -- the goal is understanding. Examples: "Understanding collections," "How search works," "Roles and permissions."

```markdown
# [Title -- "Understanding [concept]" or "[Concept name]"]

[2-3 sentence explanation: what this is and why it matters to the reader.]

## [Aspect 1 of the concept]

[Explanation. Use concrete examples, not abstract descriptions.]

## [Aspect 2 of the concept]

[Explanation. Use a table if comparing options, settings, or types.]

| [Column A] | [Column B] | [Column C] |
| --- | --- | --- |
| [Value] | [Value] | [Value] |

## [Optional: how this relates to other features]

[Brief connection to related concepts, with links.]

## What to read next

- [Link to the how-to article for this concept]
- [Link to related concept]
```

**Rules:**
- No step-by-step instructions. If the reader needs to DO something, link to the how-to article.
- Use concrete examples to illustrate abstract concepts.
- Tables work well for comparing types, roles, plan features, or options.
- Keep it focused. If the concept has distinct sub-topics, write separate articles.

---

## 4. Reference

Use for: settings pages, API parameters, configuration options, keyboard shortcuts. Dense, scannable, table-heavy.

```markdown
# [Title -- e.g., "API reference" or "Keyboard shortcuts"]

[Brief description: what this reference covers.]

## [Section 1]

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `KEY_NAME` | string | -- | What this controls |
| `OTHER_KEY` | boolean | `false` | What enabling this does |

## [Section 2]

[More tables, code examples, or lists as appropriate.]

## [Optional: complete example]

```[language]
# Example showing real values
KEY_NAME=actual_value
OTHER_KEY=true
```
```

**Rules:**
- Tables are the primary format. Prose is secondary.
- Include types and defaults for every option.
- Use the exact names from the codebase -- copy them, do not paraphrase.
- Examples use realistic values, not `YOUR_VALUE_HERE`.

---

## 5. Troubleshooting

Use for: error messages, common failure modes, things that go wrong. The reader is frustrated -- respect their time.

```markdown
# [Title -- "Troubleshooting [area]" or "Common issues with [feature]"]

[Optional: 1 sentence orienting the reader.]

## [Symptom as the reader would describe it]

**Cause:** [Why this happens, in plain language.]

**Solution:**

1. [Specific fix step]
2. [Next step if needed]

> [!TIP]
> [Optional: how to prevent this from happening again.]

## [Next symptom]

**Cause:** [...]

**Solution:** [...]

## Still not working?

[Escalation path -- contact support link, community forum, etc. Never make "contact support" the ONLY option for any problem above.]
```

**Rules:**
- Section headings use the symptom from the reader's perspective: "My custom domain is not working" not "DNS resolution failure."
- Cause explains the technical reason in plain language.
- Solution is always actionable. Every problem gets a fix the reader can try.
- Order by frequency: most common problems first.
- Use the WARNING callout if a solution involves destructive actions.

---

## 6. FAQ

Use for: multiple related questions that are each too short for their own article. Use sparingly -- most questions deserve a full article.

```markdown
# [Title -- "Common questions about [topic]" or "[Topic] FAQ"]

[Optional: 1 sentence scope.]

<details data-icon="help-circle">
<summary>[Question phrased as the reader would ask it?]</summary>

[Answer. Keep it to 1-3 sentences. Link to a full article if the answer is complex.]

</details>

<details data-icon="help-circle">
<summary>[Next question?]</summary>

[Answer.]

</details>

<details data-icon="help-circle">
<summary>[Next question?]</summary>

[Answer.]

</details>
```

**Rules:**
- Questions in the reader's voice: "Can I use my own domain?" not "Custom domain availability."
- Answers are brief. If an answer needs more than 3 sentences, it should be its own article.
- Use the accordion block with the `help-circle` icon for visual consistency.
- Order by frequency: most-asked questions first.
- Link to full articles from answers where relevant.
- Do not create one giant FAQ. Theme by topic: billing questions, getting started questions, security questions. A single page with 40 unrelated questions is a junk drawer, not documentation.
- If you find yourself writing a 3+ sentence answer, stop. That question deserves its own how-to or informational article. Link to it from the FAQ instead.

---

## 7. Integration Guide

Use for: connecting the product with another tool, embedding a widget, using an API endpoint.

```markdown
# [Title -- "Connecting [tool name]" or "Adding the widget to your website"]

[1-2 sentences: what this integration does and why you would set it up.]

## What you need

- [Requirement: account on the other service, plan level, API key, etc.]
- [Requirement]

## Set up the connection

<Steps>

<Step title="[Action in the external service]">

[Instructions for the other tool's side of the integration.]

</Step>

<Step title="[Action in this product]">

[Instructions for this product's side.]

</Step>

<Step title="Verify the connection">

[How to confirm it worked. What the reader should see.]

</Step>

</Steps>

## [Optional: configuration options]

[Settings, parameters, or customization available after setup.]

## Troubleshooting

[1-2 common issues specific to this integration, with solutions.]

## What to read next

- [Link to related article]
```

**Rules:**
- Both sides of the integration get clear instructions.
- Always include a verification step -- the reader needs to know it worked.
- Link to the external service's documentation for their side when instructions are complex.
- Include a short troubleshooting section for integration-specific issues.
