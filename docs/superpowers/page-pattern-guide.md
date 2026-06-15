# Reppo Docs — Page Pattern Guide

Derived from the cleanest existing pages (`introduction.mdx`, `concepts/datanets.mdx`,
`guides/create-datanet.mdx`). Every migrated page is built to this mold so the
combined site reads as one consistent voice.

## Frontmatter (every page)

```yaml
---
title: "Descriptive page title — can be a question (e.g. 'What is a Datanet on Reppo?')"
sidebarTitle: "Short nav label"
description: "One sentence, keyword-rich for search/SEO, names the key terms."
---
```

- `title` is longer/descriptive; `sidebarTitle` is the short label shown in the nav.
- `description` is a single sentence.

## Page opening

- Open with a 1-2 sentence definition of the topic, **bolding the key term** on
  first use (e.g. "A **datanet** is the core organizational primitive...").
- Then move into sections. No "Overview" heading needed — the opening paragraph is the overview.

## Headings

- **Sentence case**, not Title Case: "What datanet owners control", "How content flows through a datanet".
- Use `##` for sections, `###` for sub-sections.

## Voice

- Second person ("you"), active voice, short declarative sentences.
- Confident but honest; state current limits plainly.

## Components (favor built-in Mintlify components over custom)

| Content type | Component |
|---|---|
| Ordered procedure / flow | `<Steps>` with `<Step title="...">` |
| Prerequisite / important callout | `<Note>` |
| Caution | `<Warning>` |
| Helpful aside | `<Tip>` |
| Navigation hub / "explore next" | `<CardGroup cols={2}>` with `<Card title icon href>` |
| Settings, schemas, comparisons | Markdown tables |
| Requests / responses | fenced ` ```bash ` and ` ```json ` blocks |

- Internal links use root-relative paths: `/concepts/evof`, `/authentication`.

## Terminology (prose only — NEVER touch API paths or JSON field names)

- `subnet` → `datanet`
- `miner` → `publisher`
- `validator` → `voter`
- Leave API endpoint paths (`/api/v1/me/subnets`), JSON field names (`subnetId`,
  `subnetName`), and contract labels untouched — those are backend contracts.

## OPEN QUESTION — em dashes

The master-reference style rule says "no em dashes anywhere," but the existing
pages use them liberally (e.g. `datanets.mdx` line 7). For new pages this guide
follows the stated rule (no em dashes; use commas/colons/periods instead). Flag
to Joey whether to (a) keep new pages em-dash-free and leave existing pages as
is, (b) also strip them from existing pages for consistency, or (c) relax the
rule. Pending that decision, new pages are written em-dash-free.
