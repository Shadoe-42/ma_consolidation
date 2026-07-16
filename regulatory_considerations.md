# Regulatory Considerations
*Cascade Regional Bank + Fieldstone Bank — what's genuinely different about bank data | July 2026*

---

## Purpose

The sibling `data_sec` project's `healthcare_hipaa_scenario.md` is a full standalone doc, deliberately, because HIPAA is a genuinely different compliance framework applied to the same architecture. This doc is intentionally lighter — the point of this repo is consolidation mechanics, and the regulatory framing here is real but secondary to that. What follows is what's actually different about bank data specifically, not a full crosswalk repeating work `soc2_csf_compliance_crosswalk.md` already did in the sibling project.

**Not legal advice** — the same disclaimer the HIPAA doc carries, restated here because it applies with equal force to banking regulation.

---

## GLBA Safeguards Rule

The Gramm-Leach-Bliley Act's Safeguards Rule (16 CFR Part 314) is the data-security-specific piece of GLBA that both Cascade and Fieldstone are independently subject to today, and that the merged entity remains subject to afterward — consolidation doesn't create a new compliance obligation, but it does mean two separately-compliant information security programs have to become one, and gaps between them (one bank stronger on a given control than the other) need to be resolved toward the stronger standard, not averaged.

Requirements directly relevant to this repo's consolidation work: a designated "qualified individual" overseeing the security program (a role that has to be decided for the merged entity, not assumed to default to whichever bank's person held it before); written risk assessments, which `deal_phase_data_governance.md`'s post-close discovery work directly feeds; encryption of customer information in transit and at rest — a concrete requirement for `platform_and_schema_reconciliation.md`'s Oracle-to-Snowflake and SQL Server-to-Snowflake extraction pipelines specifically, not just the steady-state platform; and breach notification to the FTC within 30 days of discovery for incidents affecting 500 or more consumers — a timeline worth naming alongside HIPAA's 60-day rule in the sibling project, since the two frameworks genuinely differ here and conflating them would be a real, checkable error.

---

## Banking Regulator Merger Approval

Bank mergers require approval from the relevant federal banking regulator (the OCC, the Federal Reserve, or the FDIC, depending on the banks' charters) before they can close — a review that considers financial stability, competitive effects, and the combined institution's ability to serve its community, and that also expects a credible plan for how customer data and operations will actually be consolidated post-close. This is the direct regulatory link to `deal_phase_data_governance.md`'s Day 1/Day 2+ distinction: a merger approval that assumed operational readiness the bank can't actually deliver on Day 1 is itself a regulatory risk, not just an execution one.

---

## Where This Connects Back

Both requirements above are why `deal_phase_data_governance.md`'s sequencing matters as much as it does — a GLBA-compliant security program and a regulator-approved integration plan both depend on `platform_and_schema_reconciliation.md` and `identity_and_access_consolidation.md` being real, executable plans by the time of close, not aspirational ones. This doc doesn't add new technical work; it's the reason the technical work in the rest of this repo has a real deadline and a real audience beyond the two banks themselves.

---

## Sources

- Internal: `deal_phase_data_governance.md`, `platform_and_schema_reconciliation.md`; `soc2_csf_compliance_crosswalk.md`, `healthcare_hipaa_scenario.md` (sibling repo `data_sec`, for the HIPAA breach-notification timeline comparison)
- [Safeguards Rule — Federal Trade Commission](https://www.ftc.gov/legal-library/browse/rules/safeguards-rule)
