# GitBook → Mintlify Docs Migration — Design

**Date:** 2026-06-15
**Author:** Joey (with Claude)
**Status:** Draft for review

## Goal

Make `docs.reppo.ai` (Mintlify) the single source of truth so the GitBook can be
retired. Port the content that lives only on GitBook into the Mintlify site, fix
clear terminology errors, and rewrite the one page whose content is stale —
without revamping pages that already work.

This is RG's "one source of truth" ask: GitBook holds the protocol thesis,
Mintlify holds the technical docs; unify them on Mintlify, then repoint the
`reppo.xyz` Docs link and decommission the GitBook subscription.

## Scope principle

Minimal, targeted change. We only:

1. **Add** the GitBook-only content that has no Mintlify home.
2. **Add nav groups** only where incoming content has nowhere sensible to live
   (Trust & Security, Tokenomics, Ecosystem, Resources).
3. **Fix** clear errors (subnet→datanet in prose, homepage typo and framing).
4. **Rewrite** exactly one page (Verification & Provenance) whose content is stale.

We do **not** reshuffle working pages, rename things without a real need, or
restructure the existing Get Started / Core Concepts / Guides flow beyond
inserting new pages.

## Combined information architecture (end state)

`★` = new page/group · `▢` = reserved-but-empty Orquestra slot (filled later)

```
[Homepage / index.mdx]   ← front door: fix typo, broaden framing, two-path fork

Get Started
  • Introduction          keep (light reconcile with GitBook thesis)
  • Why Reppo          ★  new — consolidates "Fixing AI Data Labor Incentives"
  • Authentication        keep
  • Quickstart            keep

Core Concepts
  • How Reppo Works    ★  new — consolidates "Participants and Roles"
  • Datanets              keep — reconcile/dedupe with GitBook
  • Pods                  keep
  • Votes & Curation      keep — merge in Stake-Assured Human Feedback + Collection Methods
  • Staking & Emissions   keep — reconcile Performance Pool numbers with Revenue Model
  • EVOF                  keep
  • Orquestra          ▢  reserved — concept + technical on one page (Ana to write)

Trust & Security  ★ NEW GROUP
  • Adversarial Robustness     ★  new (move)
  • Verification & Provenance  ★  new (move — port as-is with light cleanup; do NOT rewrite)
  • Privacy & Security         ★  new (move)

Guides
  • Create a Datanet      keep
  • Publish a Pod         keep
  • Mint a Tweet          keep
  • Claim Emissions       keep
  • Agent Integration     keep
  • Run an Orquestra Swarm ▢ reserved — setup/how-to guide (Ana to write)

Tokenomics  ★ NEW GROUP
  • Token Utility        ★  new (move)
  • Revenue Model        ★  new (move + reconcile)
  • Token Distribution   ★  new — consolidates Token Breakdown + Token Allocation Breakdown

Ecosystem  ★ NEW GROUP
  • Product Roadmap      ★  new (move)
  • Data Exchange        ★  new (move)

Resources  ★ NEW GROUP
  • FAQ                  ★  new (move + subnet→datanet prose cleanup)
  • Glossary             ★  new (move)
  • Team                 ★  new (move from "Core Contributors")
  • Whitepapers          ★  new — Network + MiCA as PDF links
  • Audit Reports        ★  new — V1 + V2 as PDF links
```

**API Reference tab: unchanged.** Optional separate pass for subnet→datanet in
API *prose*; endpoint paths and field names are out of scope (see below).

Narrative arc: understand it → learn how it works → trust it → build with it →
understand the economics → see the ecosystem → look things up.

## Homepage (`index.mdx`)

Currently API-only ("Everything you need to integrate with the Reppo API") with
a typo in the title (`"Reppo Documention"`). Reframe as the site front door:

- Fix the title typo.
- Broaden the hero/description from "integrate with the API" to "understand and
  build on Reppo."
- Add a clear two-path fork as the primary cards:
  - **Documentation** → `/introduction` — "Understand the protocol and start participating"
  - **API Reference** → `/api/overview` — "Integrate programmatically — auth, pods, emissions"
- Keep a short secondary row of popular jump-offs (Quickstart, Datanets,
  Run an Orquestra Swarm, Agent Integration).
- Move the detailed "How Reppo works" Steps off the homepage; the authoritative
  version lives on the new *How Reppo Works* concept page. Homepage links to it.

## Page-by-page migration actions

Source = live GitBook (`reppo-labs-xyz.gitbook.io/reppo-labs`), verified against
its `llms.txt` index on 2026-06-15.

| GitBook page | Action | Mintlify destination |
| --- | --- | --- |
| Introduction | Merge | Get Started: Introduction (reconcile thesis into existing) |
| Why Reppo | Move | Get Started: Why Reppo (new) |
| Fixing AI Data Labor Incentives | Consolidate | into Why Reppo |
| How Reppo Works | Move | Core Concepts: How Reppo Works (new) |
| Participants and Roles | Consolidate | into How Reppo Works |
| Datanets | Merge | Core Concepts: Datanets (dedupe) |
| Stake-Assured Human Feedback | Merge | Core Concepts: Votes & Curation |
| Collection Methods | Consolidate | into Votes & Curation |
| Adversarial Robustness | Move | Trust & Security: Adversarial Robustness (new) |
| Verification, Provenance, and Reputation | Move | Trust & Security: Verification & Provenance — port with light cleanup; do NOT rewrite. FLAG: GitBook version describes an EigenLayer AVS research-phase plan; confirm with team whether to keep as-is before publishing. |
| Privacy and Security | Move | Trust & Security: Privacy & Security (new) |
| Token Utility | Move | Tokenomics: Token Utility (new) |
| Revenue Model | Move + reconcile | Tokenomics: Revenue Model |
| Token Breakdown | Consolidate | Tokenomics: Token Distribution (new) |
| Token Allocation Breakdown | Consolidate | Tokenomics: Token Distribution (new) |
| Product Roadmap | Move | Ecosystem: Product Roadmap (new) |
| Data Exchange | Move | Ecosystem: Data Exchange (new) |
| FAQ | Move + cleanup | Resources: FAQ (subnet→datanet) |
| Glossary | Move | Resources: Glossary |
| Core Contributors | Move | Resources: Team |
| Network Whitepaper | Link-out | Resources: Whitepapers (PDF) |
| MiCA Whitepaper | Link-out | Resources: Whitepapers (PDF) |
| Audit Report V1 | Link-out | Resources: Audit Reports (PDF) |
| Audit Report V2 | Link-out | Resources: Audit Reports (PDF) |

Action types: **Move** (no Mintlify equivalent, lift with light cleanup),
**Merge** (equivalent exists, reconcile both into one), **Consolidate** (thin
page folds into a related page), **Rewrite** (stale content, rewrite to current
reality), **Link-out** (keep as external PDF link, not a prose page).

**Correction vs. the original Notion map:** "Privacy and Security" is a
substantive GitBook page (commit-reveal voting from V1, GDPR/CCPA, encryption,
role-based access, wallet auth) that the Notion map omitted. It is included here
in the Trust & Security group. Audit Reports V1/V2 are already external links in
the GitBook sidebar (absent from `llms.txt`), consistent with the Link-out
action.

## Key decisions

- **Naming (SAHF vs Votes & Curation):** Keep the page title **Votes & Curation**
  and the existing slug `concepts/votes-curation` (no broken links). Introduce
  **Stake-Assured Human Feedback** as the named mechanism *inside* the page. Both
  terms live; SAHF is the brand term, Votes & Curation is the descriptive title.
- **subnet → datanet:** Fix in **prose only** (page text, titles, descriptions).
  Do **not** touch API endpoint paths (`/api/v1/me/subnets`) or JSON field names
  (`subnetId`, `subnetName`, `totalActiveSubnets`, etc.) — those are backend
  contracts and require engineering, not a docs edit. Contract labels (e.g.
  "Subnet Manager" contract name) are proper nouns; leave unless engineering
  renames them.
- **miner → publisher, validator → voter:** Fix in prose where they refer to the
  same role.
- **Reconcile by hand (not bulk copy):** Introduction, Datanets, Votes & Curation,
  Staking & Emissions. Each has content on both sites that must be compared and
  merged, not overwritten.
- **Style:** Mintlify house voice — punchy, developer-facing, second person,
  short declarative sentences, MDX components (Steps, Note, Warning, Card,
  CardGroup, ParamField). Sentence case headings. **No em dashes anywhere.**

## Deferred (planned, not built in this pass)

- **Orquestra content.** Two reserved slots: a Core Concepts **Orquestra** page
  (high-level concept + technical, one page) and a Guides **Run an Orquestra
  Swarm** page (setup/how-to). Ana Julia Bittencourt owns this content; we add it
  after the rest ships. The reserved slots mean adding it later touches nothing
  else. Disregard the older detailed Orquestra docs plan.
- **API prose subnet→datanet pass.** Optional follow-up, separate from this work.

Note: Verification & Provenance is moved over with light cleanup (NOT rewritten).
Its GitBook content describes an EigenLayer AVS research-phase plan; flag to the
team whether that is still accurate before publishing, but do not rewrite it as
part of this pass.

## Out of scope

- Any change to API endpoint paths or JSON field names.
- Rewriting or restructuring existing pages beyond the merges/reconciles listed.
- Orquestra content (reserved slots only).
- Selling node licenses or any gating — the network is permissionless.

## Approval & review

- **Rohan** is the design/structure review gate before anything ships. RG does
  not need to approve individual pages.
- Deployment: push to `main`, Mintlify bot auto-deploys.
