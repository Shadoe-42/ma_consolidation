# M&A Data Platform Consolidation — Reference Architecture
*Cascade Regional Bank acquires Fieldstone Bank — one data platform out of two*

A post-merger data platform consolidation reasoning framework for two regional banks becoming one — the legal constraints that govern what data work is even allowed before and after close, what's genuinely different about bank data regulation, and the technical core of the problem: reconciling two heterogeneous core banking platforms (Oracle and SQL Server, neither already on Snowflake) that represent overlapping business concepts differently, including the customers who bank at both.

## What this is, and isn't

This is a **sibling project to [`data_sec`](https://github.com/Shadoe-42/data_sec), [`db_migration`](https://github.com/Shadoe-42/db_migration), [`finops_remediation`](https://github.com/Shadoe-42/finops_remediation), [`cloud_retrofit`](https://github.com/Shadoe-42/cloud_retrofit), and [`dc_decomm`](https://github.com/Shadoe-42/dc_decomm)**, with two new fictional entities — **Cascade Regional Bank** (the acquirer) and **Fieldstone Bank** (the target) — rather than Meridian, Solstice, or Ironwood. M&A needs two companies becoming one, and the financial-services framing is deliberate: bank and financial-firm consolidation is real, current market activity, not an arbitrary choice of industry.

As with the rest of this portfolio: real technology, fictional companies. Every tool and regulation referenced (Snowflake's native fuzzy-matching functions, the Interagency Guidelines Establishing Information Security Standards, the Hart-Scott-Rodino Act) is real and current as of this writing; Cascade, Fieldstone, and their merger are not. This doc set is **not legal advice** — deal-specific antitrust and regulatory guidance requires actual counsel, not a reference architecture repo.

## Structure

| Path | What's there |
|---|---|
| `deal_phase_timeline_hld.svg` / `.png` | Architecture diagram — pre-close through Day 2+, with the HSR legal boundary drawn explicitly between what's technically possible and what's legally permitted |
| `deal_phase_data_governance.md` | The constraint that governs everything else — pre-close antitrust/clean-room restrictions on data sharing between two still-separate competitors, versus what's possible after legal close, and Day 1 readiness versus Day 2+ integration |
| `regulatory_considerations.md` | What's genuinely different about bank data — the Interagency Guidelines Establishing Information Security Standards (not the FTC Safeguards Rule, a common misapplication) and banking regulator merger approval, kept proportionate rather than a full compliance crosswalk repeat |
| `platform_and_schema_reconciliation.md` | The technical core: extracting from Cascade's Oracle and Fieldstone's SQL Server core banking systems, reconciling schemas that represent the same products differently, and resolving customers who bank at both institutions under unrelated IDs |
| `identity_and_access_consolidation.md` | Merging two IAM/AD models into one, entitlement reconciliation, and why "grant everyone access to everything" is the wrong shortcut even temporarily |

## Related

Sibling repos in the same portfolio, all reusing or extending `data_sec`'s architecture rather than duplicating it:

- [`data_sec`](https://github.com/Shadoe-42/data_sec) — the classification and IAM patterns this consolidation reconciles two banks' independent versions of
- [`db_migration`](https://github.com/Shadoe-42/db_migration) — the extraction mechanics for Oracle and SQL Server this project builds on rather than re-deriving
- [`finops_remediation`](https://github.com/Shadoe-42/finops_remediation) — sibling scenario, same portfolio
- [`cloud_retrofit`](https://github.com/Shadoe-42/cloud_retrofit) — sibling scenario; the over-broad-access failure mode referenced in `identity_and_access_consolidation.md`
- [`dc_decomm`](https://github.com/Shadoe-42/dc_decomm) — sibling scenario, same portfolio

## License

MIT — see `LICENSE`.
