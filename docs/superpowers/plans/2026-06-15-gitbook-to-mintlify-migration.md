# GitBook → Mintlify Docs Migration Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Port all GitBook-only content into the Mintlify docs at `docs.reppo.ai` so the GitBook can be retired, without revamping pages that already work.

**Architecture:** Additive migration. New `.mdx` pages and four new nav groups (Trust & Security, Tokenomics, Ecosystem, Resources) are added to the existing structure; four existing pages are reconciled by hand; one page is rewritten; the homepage is reframed. Each page is validated locally with `mint dev` before commit. Orquestra gets two reserved slots filled in a later pass.

**Tech Stack:** Mintlify (MDX + `docs.json`), Mintlify CLI via `npx mint` (no global install — system Node blocks `-g`).

**Design spec:** `docs/superpowers/specs/2026-06-15-gitbook-to-mintlify-migration-design.md`

---

## Conventions used throughout this plan

**GitBook source base URL:** `https://reppo-labs-xyz.gitbook.io/reppo-labs/`
Append the `.md` path (e.g. `foundations/why-reppo.md`) and fetch it to get clean markdown source for any page.

**New folder layout** (mirrors existing `concepts/` and `guides/`):
- `trust-security/` — Trust & Security group
- `tokenomics/` — Tokenomics group
- `ecosystem/` — Ecosystem group
- `resources/` — Resources group
- Get Started pages stay at repo root (like `introduction.mdx`); Core Concepts pages stay in `concepts/`.

**Frontmatter pattern** (copy on every new page — derived from existing pages):
```yaml
---
title: "Full Page Title"
sidebarTitle: "Short Nav Label"
description: "One sentence, search-and-SEO friendly, mentions key terms."
---
```

**Terminology rules (prose only — NEVER touch API paths or JSON field names):**
- `subnet` → `datanet`
- `miner` → `publisher`
- `validator` → `voter`

**Em dashes:** Remove most em dashes on every page we create or touch, rewriting
each sentence so it reads naturally without the dash (comma, colon, period, or
restructure). Not a blind delete; keep one only where removal genuinely hurts
readability.

**Source provenance comment:** Immediately after the frontmatter on every
migrated page, add `{/* Source: <gitbook path(s)> [(consolidated)] */}` so the
origin is traceable in the file. MDX comments use `{/* ... */}` and do not render.

**Per-page verification (the "test" for docs work):**
1. `npx mint broken-links` — must report no broken links.
2. `npx mint dev` running → open `http://localhost:3000/<page-path>` → confirm it renders, components display, headings nest correctly.

**Reserved Orquestra slots:** `concepts/orquestra.mdx` and `guides/run-orquestra-swarm.mdx` are NOT created and NOT added to `docs.json` in this plan (a page in nav with no file errors the build). They are added in a later pass when Ana delivers content.

---

## Phase 0: Foundation

### Task 0: Start the preview server

**Files:** none

- [ ] **Step 1: Launch the dev server (leave running in a background terminal)**

Run: `npx -y mint dev`
Expected: serves at `http://localhost:3000`, hot-reloads on file save. Keep this running for every subsequent visual check.

- [ ] **Step 2: Baseline broken-links check**

Run: `npx -y mint broken-links`
Expected: passes clean on the current repo (records the baseline before we add pages).

---

### Task 1: Write the page pattern guide

**Files:**
- Create: `docs/superpowers/page-pattern-guide.md`

- [ ] **Step 1: Read the three cleanest existing pages for patterns**

Read: `introduction.mdx`, `concepts/datanets.mdx`, `guides/create-datanet.mdx`
Note their frontmatter shape, which components they use for which content (`Note`, `Steps`, `Card`/`CardGroup`, `ParamField`, `ResponseField`), heading hierarchy (sentence case), and tone.

- [ ] **Step 2: Write the guide capturing the house pattern**

Write a short reference covering: frontmatter template; "use `Steps` for ordered procedures, `Note`/`Warning` for callouts, `CardGroup` for navigation hubs, plain prose + bullets for concepts"; sentence-case headings; second person; no em dashes; the terminology rules above. This is the mold every migrated page is built to.

- [ ] **Step 3: Commit**

```bash
git add docs/superpowers/page-pattern-guide.md
git commit -m "docs: add page pattern guide for migration consistency"
```

---

### Task 2: Pilot page — Why Reppo (validates the whole pattern)

**Files:**
- Create: `why-reppo.mdx`
- Modify: `docs.json` (add to Get Started group, after `introduction`)
- Source: `foundations/why-reppo.md` + `foundations/fixing-ai-data-labor-incentives.md`

- [ ] **Step 1: Fetch both GitBook sources**

Fetch `https://reppo-labs-xyz.gitbook.io/reppo-labs/foundations/why-reppo.md` and `https://reppo-labs-xyz.gitbook.io/reppo-labs/foundations/fixing-ai-data-labor-incentives.md`.

- [ ] **Step 2: Write `why-reppo.mdx`**

Consolidate both sources into one page. Lead with the motivation/thesis, fold "Fixing AI Data Labor Incentives" in as a section. Apply frontmatter pattern, terminology rules, no em dashes. Convert any narrative callouts to `Note` components and any ordered steps to `Steps`.

- [ ] **Step 3: Add to `docs.json`**

In the "Get Started" group `pages` array, insert `"why-reppo"` immediately after `"introduction"`.

- [ ] **Step 4: Verify render + links**

Run: `npx -y mint broken-links` → expect clean.
Open `http://localhost:3000/why-reppo` → confirm it renders cleanly and matches the pattern guide.

- [ ] **Step 5: CHECKPOINT — human/visual review**

Have Joey preview `why-reppo` in `mint dev`. This is the pattern-validation gate: confirm component choices, frontmatter, tone, and layout look right BEFORE fanning out to the remaining pages. Adjust the pattern guide if anything is off.

- [ ] **Step 6: Commit**

```bash
git add why-reppo.mdx docs.json
git commit -m "docs: add Why Reppo page (migrated from GitBook)"
```

---

## Phase 1: Straight "Move" pages

These pages have no Mintlify equivalent and need no cross-site reconcile. They all follow the **shared Move procedure** below. Each task names its exact source, destination, and any page-specific notes.

**Shared Move procedure (apply to every task in this phase):**
1. Fetch the GitBook source `.md`.
2. Create the destination `.mdx` with the frontmatter pattern.
3. Convert prose to MDX: ordered procedures → `Steps`, callouts → `Note`/`Warning`, link hubs → `CardGroup`. Keep GitBook's directness.
4. Apply terminology rules; remove em dashes; re-point any internal links to new Mintlify paths.
5. Add the page to its group in `docs.json` (create the group if it does not exist yet — Task 21 finalizes and verifies the full nav, but add groups incrementally as needed).
6. `npx -y mint broken-links` → clean.
7. Visually check the page at `http://localhost:3000/<path>`.
8. Commit with message `docs: add <Page> (migrated from GitBook)`.

### Task 3: How Reppo Works
**Dest:** `concepts/how-reppo-works.mdx` · **Group:** Core Concepts (top of group, before `concepts/datanets`)
**Source:** `foundations/how-reppo-works.md` + `foundations/participants-and-roles.md`
- [ ] Consolidate both sources. Make "Participants and Roles" a roles section (publishers, voters, datanet owners, AI teams). Note: `introduction.mdx` already has a roles table — keep this page the authoritative system-flow version and have the homepage/intro link here rather than duplicating.
- [ ] Run shared Move procedure steps 6-8.

### Task 4: Adversarial Robustness
**Dest:** `trust-security/adversarial-robustness.mdx` · **Group:** Trust & Security (new group, page 1)
**Source:** `protocol-mechanics/adversarial-robustness.md`
- [ ] Run shared Move procedure. This task creates the `trust-security/` folder and the "Trust & Security" group in `docs.json`.

### Task 5: Privacy & Security
**Dest:** `trust-security/privacy-security.mdx` · **Group:** Trust & Security (page 3)
**Source:** `trust-and-security/privacy-and-security.md`
- [ ] Run shared Move procedure. (This is the page the original Notion map omitted — confirm content is current: commit-reveal voting from V1, GDPR/CCPA, role-based access, wallet auth.)

### Task 6: Token Utility
**Dest:** `tokenomics/token-utility.mdx` · **Group:** Tokenomics (new group, page 1)
**Source:** `economics/token-utility.md`
- [ ] Run shared Move procedure. This task creates the `tokenomics/` folder and the "Tokenomics" group in `docs.json`.

### Task 7: Token Distribution (consolidate two pages)
**Dest:** `tokenomics/token-distribution.mdx` · **Group:** Tokenomics (page 3)
**Source:** `economics/token-breakdown.md` + `economics/token-allocation-breakdown.md`
- [ ] Consolidate both into one page (overview of the token + allocation breakdown). Use a table for allocation figures.
- [ ] Run shared Move procedure steps 6-8.

### Task 8: Product Roadmap
**Dest:** `ecosystem/product-roadmap.mdx` · **Group:** Ecosystem (new group, page 1)
**Source:** `roadmap/product-roadmap.md`
- [ ] Run shared Move procedure. This task creates the `ecosystem/` folder and the "Ecosystem" group in `docs.json`. Do NOT add an Orquestra milestone yet (deferred).

### Task 9: Data Exchange
**Dest:** `ecosystem/data-exchange.mdx` · **Group:** Ecosystem (page 2)
**Source:** `roadmap/data-exchange.md`
- [ ] Run shared Move procedure steps 1-8.

### Task 10: FAQ
**Dest:** `resources/faq.mdx` · **Group:** Resources (new group, page 1)
**Source:** `resources/faq.md`
- [ ] Run shared Move procedure. This task creates the `resources/` folder and the "Resources" group in `docs.json`. Apply subnet→datanet cleanup carefully throughout (prose only). Consider Mintlify `Accordion`/`AccordionGroup` for Q&A format.

### Task 11: Glossary
**Dest:** `resources/glossary.mdx` · **Group:** Resources (page 2)
**Source:** `resources/glossary.md`
- [ ] Run shared Move procedure. Do NOT add Orquestra terms yet (deferred). Apply terminology rules.

### Task 12: Team
**Dest:** `resources/team.mdx` · **Group:** Resources (page 3)
**Source:** `about/core-contributors.md`
- [ ] Run shared Move procedure. Rename page title from "Core Contributors" to "Team".

### Task 13: Whitepapers and Audit Reports (link-out pages)
**Dest:** `resources/whitepapers.mdx`, `resources/audit-reports.mdx` · **Group:** Resources (pages 4-5)
**Source:** GitBook external PDF links (Network WP, MiCA WP, Audit V1, Audit V2)
- [ ] Create two short pages, each a `CardGroup` of `Card` components linking out to the PDFs. These are link hubs, not prose pages.
- [ ] **BLOCKER CHECK:** the actual PDF URLs are needed. If not available, leave a clearly-marked placeholder Card and flag to Joey; do not invent URLs.
- [ ] Run shared Move procedure steps 6-8.

---

## Phase 2: Verification & Provenance (MOVE — do NOT rewrite)

### Task 14: Verification & Provenance
**Dest:** `trust-security/verification-provenance.mdx` · **Group:** Trust & Security (page 2, between Adversarial Robustness and Privacy & Security)
**Source:** `trust-and-security/verification-provenance-and-reputation.md`

- [ ] **Step 1: Fetch the GitBook source** and port it over with light cleanup only — MDX conversion, terminology rules, no em dashes. Do NOT rewrite or restructure the content.
- [ ] **Step 2: FLAG, do not fix** — the GitBook version describes an EigenLayer AVS research-phase flow. Leave the content as written, but note to Joey that this may be stale vs. the shipped Base + IPFS reality, for a separate accuracy pass later. Do not invent replacement content.
- [ ] **Step 3: Add to `docs.json`** in Trust & Security as page 2.
- [ ] **Step 4: Verify** — `npx -y mint broken-links` clean; render check at `/trust-security/verification-provenance`.
- [ ] **Step 5: Commit** `docs: add Verification and Provenance (moved from GitBook)`

---

## Phase 3: Reconcile-by-hand pages (compare both sites, merge — do NOT overwrite)

Each task: fetch the GitBook source, read the existing Mintlify page in full, merge so the result keeps the best of both with no duplication, then verify + commit.

### Task 15: Introduction
**Modify:** `introduction.mdx` · **Source:** `foundations/readme.md`
- [ ] Read existing `introduction.mdx` (already strong, dev-flavored). Fetch GitBook intro (protocol thesis). Merge the thesis framing into the existing page without bloating it or duplicating the Key concepts already present. Keep the existing cards/tables.
- [ ] Verify + commit `docs: reconcile Introduction with GitBook thesis`.

### Task 16: Datanets
**Modify:** `concepts/datanets.mdx` · **Source:** `protocol-mechanics/datanets.md`
- [ ] Read existing page, fetch GitBook version, dedupe and merge. Clean the existing `subnet` prose (e.g. line ~7 "also called a subnet", line ~75 "Subnet name"). Leave the `/me/subnets` endpoint references intact (API contract).
- [ ] Verify + commit `docs: reconcile Datanets with GitBook`.

### Task 17: Votes & Curation (naming decision applies here)
**Modify:** `concepts/votes-curation.mdx` · **Source:** `protocol-mechanics/stake-assured-human-feedback.md` + `protocol-mechanics/collection-methods.md`
- [ ] Keep the title "Votes & Curation" and slug `concepts/votes-curation` (no broken links). Introduce **Stake-Assured Human Feedback** as the named mechanism inside the page. Fold "Collection Methods" in as the feedback-types section.
- [ ] Verify + commit `docs: merge Stake-Assured Human Feedback into Votes and Curation`.

### Task 18: Staking & Emissions
**Modify:** `concepts/staking-emissions.mdx` · **Source:** `economics/revenue-model.md`
- [ ] Reconcile Performance Pool numbers so this page and the new `tokenomics/revenue-model.mdx` (Task 19) agree exactly. Source of truth for the figures: confirm with the design spec / RG if the two GitBook/Mintlify numbers disagree.
- [ ] Verify + commit `docs: reconcile Staking and Emissions Performance Pool numbers`.

### Task 19: Revenue Model
**Dest:** `tokenomics/revenue-model.mdx` · **Group:** Tokenomics (page 2) · **Source:** `economics/revenue-model.md`
- [ ] Move the Revenue Model page into Tokenomics. Ensure Performance Pool figures match Task 18 exactly (40% EVOF weighting; pool funded by 50% spin-up fees + 10% publishing + 10% data access; every third epoch distribute 5%, split 80% stakers / 20% datanet owners — verify against live sources before publishing).
- [ ] Verify + commit `docs: add Revenue Model to Tokenomics (reconciled with Staking and Emissions)`.

---

## Phase 4: Homepage + nav finalization

### Task 20: Reframe the homepage
**Modify:** `index.mdx`
- [ ] **Step 1: Fix the title typo** — `"Reppo Documention"` → `"Reppo Documentation"`.
- [ ] **Step 2: Broaden the description** from "integrate with the Reppo API" to a line covering understanding AND building on Reppo.
- [ ] **Step 3: Replace the top card row with the two-path fork:**
```mdx
<CardGroup cols={2}>
  <Card title="Documentation" icon="book" href="/introduction">
    Understand the protocol and start participating — datanets, pods, curation, and emissions.
  </Card>
  <Card title="API Reference" icon="code" href="/api/overview">
    Integrate programmatically — authentication, pods, minting, and emissions endpoints.
  </Card>
</CardGroup>
```
- [ ] **Step 4: Keep a short secondary jump-off row** (Quickstart, Datanets, Agent Integration). Do NOT link Run an Orquestra Swarm yet (reserved).
- [ ] **Step 5: Remove the inline "How Reppo works" Steps** — point readers to `/concepts/how-reppo-works` instead so there is one authoritative version.
- [ ] Verify + commit `docs: reframe homepage as Documentation/API fork`.

### Task 21: Final nav review + full link check
**Modify:** `docs.json` (verify only)
- [ ] **Step 1: Read the full `docs.json`** and confirm the Documentation tab groups are in narrative order: Get Started → Core Concepts → Trust & Security → Guides → Tokenomics → Ecosystem → Resources. (Guides sits after Trust & Security.) Confirm NO reserved Orquestra pages are referenced.
- [ ] **Step 2: Full broken-links sweep** — `npx -y mint broken-links` → must be clean across the whole site.
- [ ] **Step 3: Full visual pass** — click through every new group in `mint dev`; confirm sidebar order and every page renders.
- [ ] **Step 4: Commit** any nav ordering fixes `docs: finalize combined navigation order`.

---

## Phase 5: Final terminology sweep (prose only)

### Task 22: Audit remaining subnet/miner/validator prose
**Files:** all `.mdx` outside `api/`
- [ ] **Step 1: List remaining hits** — `grep -rin "subnet\|miner\|validator" --include="*.mdx" . | grep -v "^./api/"`
- [ ] **Step 2: For each hit, classify** — prose (fix) vs. API path / field name / contract proper-noun (leave). When unsure, leave it and flag to Joey.
- [ ] **Step 3: Fix the prose hits**, re-running `npx -y mint broken-links` after.
- [ ] **Step 4: Commit** `docs: subnet to datanet prose cleanup across migrated pages`.

### Task 23: Em-dash sweep across all pages
**Files:** all `.mdx` (new + existing, excluding API paths/fields)
- [ ] **Step 1: List em-dash hits** — `grep -rln "—" --include="*.mdx" .`
- [ ] **Step 2: For each page, remove most em dashes** by rewriting the sentence so it reads naturally (comma, colon, period, or restructure). Do NOT mechanically delete. Keep one only where removal hurts readability.
- [ ] **Step 3: Verify** — `npx -y mint broken-links` clean; spot-check rewritten sentences in `mint dev`.
- [ ] **Step 4: Commit** `docs: remove most em dashes for consistency across the site`.

> Note: API Reference prose subnet→datanet is explicitly OUT OF SCOPE for this plan (separate follow-up). Endpoint paths and JSON field names require engineering, not docs.

---

## Reserved for the later Orquestra pass (NOT in this plan)
- `concepts/orquestra.mdx` — concept + technical, added to Core Concepts after EVOF.
- `guides/run-orquestra-swarm.mdx` — setup guide, added to Guides after Agent Integration.
- Orquestra milestone on Product Roadmap; Orquestra terms in Glossary; Run an Orquestra Swarm link on homepage.
Ana Julia Bittencourt owns this content.

---

## Self-review notes
- **Spec coverage:** every spec section maps to a task — homepage (T20), Why Reppo (T2), How Reppo Works (T3), Trust & Security group (T4/T5/T14; Verification & Provenance moved, not rewritten), Tokenomics (T6/T7/T19), Ecosystem (T8/T9), Resources (T10-T13), reconciles (T15-T18), naming decision (T17), terminology (T22), reserved Orquestra slots (documented, not built).
- **Known blocker:** PDF URLs for Whitepapers/Audit Reports (T13) — flagged, do not invent.
- **Numbers to verify before publishing:** Performance Pool figures shared between T18 and T19 must agree and be confirmed against live sources.
