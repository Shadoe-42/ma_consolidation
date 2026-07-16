# Regulatory Considerations
*Cascade Regional Bank + Fieldstone Bank — what's genuinely different about bank data | July 2026*

---

## Purpose

The sibling `data_sec` project's `healthcare_hipaa_scenario.md` is a full standalone doc, deliberately, because HIPAA is a genuinely different compliance framework applied to the same architecture. This doc is intentionally lighter — the point of this repo is consolidation mechanics, and the regulatory framing here is real but secondary to that. What follows is what's actually different about bank data specifically, not a full crosswalk repeating work `soc2_csf_compliance_crosswalk.md` already did in the sibling project.

**Not legal advice** — the same disclaimer the HIPAA doc carries, restated here because it applies with equal force to banking regulation.

---

## Information Security Standards — Interagency Guidelines, Not the FTC Safeguards Rule

Worth correcting a natural but wrong assumption directly, since it's the kind of error this doc explicitly warns against elsewhere in this repo: GLBA §505 splits data-security enforcement by regulator, and the FTC's Safeguards Rule (16 CFR Part 314) — the version most commonly cited when people say "GLBA data security" — applies specifically to *non-bank* financial institutions the FTC otherwise has no jurisdiction over (mortgage companies, non-bank lenders, payday lenders, and similar). Cascade and Fieldstone, as chartered banks, are examined by their primary federal banking regulator instead — the OCC, the Federal Reserve, or the FDIC, depending on charter — under the **Interagency Guidelines Establishing Information Security Standards** (codified as an appendix to each regulator's own rules), not the FTC Safeguards Rule. This split is structural, not a recent change, and it governs both who enforces and which specific requirements apply.

The Interagency Guidelines require substantively similar things to the FTC rule — a comprehensive written information security program covering administrative, technical, and physical safeguards, board-level involvement and reporting, risk assessment, and oversight of service-provider arrangements — but they're not identical in structure or terminology, and consolidation work should cite the framework the banks are actually examined against, not the more commonly-known FTC version. Two separately-compliant Interagency Guidelines programs have to become one merged program post-close, following the same "resolve toward the stronger standard" logic where the two banks' programs differ — that reasoning holds regardless of which framework applies, and `deal_phase_data_governance.md`'s post-close discovery work is what a real risk assessment for the merged program would draw on.

**Incident notification is where the two frameworks diverge most concretely, and it's the detail most worth getting right**: banking organizations operate under the Computer-Security Incident Notification Rule (effective 2022), which requires notifying the bank's primary federal regulator within **36 hours** of determining a "notification incident" has occurred — a materially different, much faster clock than the FTC Safeguards Rule's 30-day/500-consumer threshold that applies to non-bank financial institutions, and a different clock again from HIPAA's 60-day rule in the sibling project. All three are real, all three are different, and conflating any of them is the exact class of error worth naming explicitly rather than assuming "financial services" and "healthcare" each have one interchangeable timeline.

Encryption of customer information in transit and at rest remains a real, concrete requirement under the Interagency Guidelines' technical-safeguards component — directly relevant to `platform_and_schema_reconciliation.md`'s Oracle-to-Snowflake and SQL Server-to-Snowflake extraction pipelines specifically, not just the steady-state platform.

---

## Banking Regulator Merger Approval

Bank mergers require approval from the relevant federal banking regulator (the OCC, the Federal Reserve, or the FDIC, depending on the banks' charters) before they can close — a review that considers financial stability, competitive effects, and the combined institution's ability to serve its community, and that also expects a credible plan for how customer data and operations will actually be consolidated post-close. This is the direct regulatory link to `deal_phase_data_governance.md`'s Day 1/Day 2+ distinction: a merger approval that assumed operational readiness the bank can't actually deliver on Day 1 is itself a regulatory risk, not just an execution one.

---

## Where This Connects Back

Both requirements above are why `deal_phase_data_governance.md`'s sequencing matters as much as it does — a GLBA-compliant security program and a regulator-approved integration plan both depend on `platform_and_schema_reconciliation.md` and `identity_and_access_consolidation.md` being real, executable plans by the time of close, not aspirational ones. This doc doesn't add new technical work; it's the reason the technical work in the rest of this repo has a real deadline and a real audience beyond the two banks themselves.

---

## Sources

- Internal: `deal_phase_data_governance.md`, `platform_and_schema_reconciliation.md`; `soc2_csf_compliance_crosswalk.md`, `healthcare_hipaa_scenario.md` (sibling repo `data_sec`, for the HIPAA breach-notification timeline comparison)
- [Interagency Guidelines Establishing Information Security Standards — 12 CFR Appendix B to Part 30 (eCFR)](https://www.ecfr.gov/current/title-12/chapter-I/part-30/appendix-Appendix%20B%20to%20Part%2030)
- [Computer-Security Incident Notification Requirements for Banking Organizations — Federal Register](https://www.federalregister.gov/documents/2021/11/23/2021-25510/computer-security-incident-notification-requirements-for-banking-organizations-and-their-bank)
- [Safeguards Rule — Federal Trade Commission](https://www.ftc.gov/legal-library/browse/rules/safeguards-rule) (scope note: applies to non-bank financial institutions, not Cascade or Fieldstone directly — cited here only to draw the contrast)
