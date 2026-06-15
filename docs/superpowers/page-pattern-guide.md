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

## Em dashes (DECIDED)

Remove **most** em dashes, on both new and existing pages as we touch them.
This is not a blind delete: rewrite each sentence so it reads naturally without
the dash — use a comma, colon, period, or restructure the clause. Keep an em
dash only in the rare case where removing it genuinely hurts readability. The
goal is consistency across the combined site, not mechanical purging.

## Source provenance comment (every migrated page)

Immediately after the frontmatter, add an MDX comment recording the GitBook
source(s) so traceability is checkable in the file:

```mdx
{/* Source: foundations/why-reppo.md + foundations/fixing-ai-data-labor-incentives.md (consolidated) */}
```

MDX comments use `{/* ... */}` (not HTML `<!-- -->`) and do not render.

## Reviewing consolidated / merged pages — what to look for

When a page combines two or more GitBook sources (Consolidate or Merge actions),
call it out explicitly at review time ("this is X + Y combined") and check:

- **No duplication:** the two sources often make overlapping points (e.g. both
  critique the vendor model). Merge them once, do not repeat.
- **Logical flow:** the seam between the two halves should read smoothly, not
  like two pages stapled together.
- **No contradictions:** figures, claims, and terminology must agree across the
  merged sources.
- **All cross-links re-pointed:** links from BOTH sources updated to new Mintlify paths.
- **Nothing important dropped:** every substantive point from each source is
  represented somewhere on the combined page.

Pages with consolidation/merge in this migration: Why Reppo (T2), How Reppo
Works (T3), Token Distribution (T7), Introduction (T15), Datanets (T16), Votes &
Curation (T17).
