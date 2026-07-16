# Deal-Phase Data Governance
*Cascade Regional Bank + Fieldstone Bank — what's allowed depends entirely on what day it is | July 2026*

---

## Purpose

Every other scenario in this portfolio starts from the assumption that whoever's doing the work already has full, legitimate access to the data in question. This one doesn't — for a meaningful stretch of the engagement, Cascade and Fieldstone are still legally separate, still competing banks, and sharing the wrong data at the wrong moment isn't just a security problem, it's a federal antitrust violation. This doc governs what every other doc in this repo is even allowed to do, and when — nothing in `platform_and_schema_reconciliation.md` or `identity_and_access_consolidation.md` happens on a technical timeline; it happens on a legal one.

---

## Pre-Close: Two Competitors, Not One Company Yet

Under the Hart-Scott-Rodino Act, Cascade and Fieldstone must remain operationally separate until the deal legally closes — the acquirer cannot begin directing the target's business activities, and the two cannot combine operations or hold themselves out as a single company, regardless of how confident everyone is that the deal will close. Violating this isn't a compliance technicality: HSR Act violations carry civil penalties that can run well past a million dollars in aggregate, calculated per day of violation.

**The clean team.** Data sharing during due diligence is restricted to a small, designated clean team — explicitly excluding anyone involved in competitive planning, pricing, or strategy at either bank — reviewing information inside a restricted clean room (typically a dedicated virtual data room) under strict prohibitions on exporting what they see. This isn't a data engineering convenience; it's the specific legal mechanism that makes any pre-close data work possible at all without violating HSR.

**What the clean team can actually do with data before close.** Aggregated, anonymized, or otherwise non-competitively-sensitive analysis — enough to validate the technical scope of `platform_and_schema_reconciliation.md`'s eventual work (confirming, for instance, that Fieldstone's core banking system really is SQL Server, and getting a row-count-level sense of scale) without touching individual customer records, live pricing data, or anything that would let either bank act on the other's competitive position before the deal closes. This is deliberately a scoping and readiness exercise, not the actual migration — the actual migration is post-close work by definition.

---

## Close: The Legal Switch Flips, the Technical Work Doesn't Start Instantly

Legal close ends the HSR-driven separation requirement, but it doesn't mean `platform_and_schema_reconciliation.md`'s full technical work begins the same day. What changes immediately is legal permission; what still takes real time is everything downstream in this portfolio's other docs — discovery depth comparable to `cloud_retrofit`'s or `dc_decomm`'s assessment work, now finally possible against both banks' real systems rather than a clean room's restricted view.

---

## Day 1 vs. Day 2+: Readiness Is Not Integration

A distinction worth stating explicitly, because conflating them is a common and expensive mistake: **Day 1** is what has to work the moment the deal closes — customers of both banks can still access their accounts, branches are open, ATMs work, nothing customer-facing breaks. **Day 2 and beyond** is the actual data platform consolidation this repo is built around — `platform_and_schema_reconciliation.md`'s schema crosswalk, `identity_and_access_consolidation.md`'s IAM merge — which realistically takes months, not a weekend.

Day 1 readiness typically means the two banks' systems continue operating largely in parallel, bridged only where customer-facing continuity absolutely requires it, while the real consolidation work happens deliberately and carefully behind the scenes. Rushing full technical consolidation to hit a Day 1 deadline is how integration mistakes become customer-facing outages at the worst possible moment — right when regulators, customers, and the market are all watching the deal most closely.

---

## What This Doc Deliberately Doesn't Cover

This isn't legal advice, and the specific HSR waiting period, clean team scope, and close conditions for any real transaction depend on deal-specific counsel — not a reference architecture doc. What this doc does establish is the shape of the constraint, so `platform_and_schema_reconciliation.md` and `identity_and_access_consolidation.md` are read correctly: as post-close work, sequenced deliberately, not as a technical plan that could simply start on day one of due diligence.

---

## Sources

- Internal: `regulatory_considerations.md`, `platform_and_schema_reconciliation.md`, `identity_and_access_consolidation.md`
- [FTC — Avoiding antitrust pitfalls during pre-merger negotiations and due diligence](https://www.ftc.gov/enforcement/competition-matters/2018/03/avoiding-antitrust-pitfalls-during-pre-merger-negotiations-due-diligence)
- [On Sharing and Managing Competitively Sensitive Information in M&A Transactions](https://www.mintz.com/insights-center/viewpoints/2871/2022-09-19-sharing-and-managing-competitively-sensitive-information)
