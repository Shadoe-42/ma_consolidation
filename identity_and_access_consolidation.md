# Identity and Access Consolidation
*Cascade Regional Bank + Fieldstone Bank — merging who can see what | July 2026*

---

## Purpose

Two banks means two Active Directory forests, two IAM models, two sets of entitlements built up over years at each institution independently. `platform_and_schema_reconciliation.md` merges the data; this doc merges who's allowed to touch it — and the obvious-looking shortcut here is also the most dangerous one available.

---

## The Anti-Pattern: Grant Everyone Access to Everything

Under deadline pressure to hit Day 1 readiness (`deal_phase_data_governance.md`), the fastest way to avoid access-related outages is to broadly grant both banks' staff access to both banks' systems and sort out the entitlement model properly later. This is worth naming directly as the wrong answer, not a shortcut worth taking even temporarily: it's a direct violation of the Interagency Guidelines Establishing Information Security Standards in progress (access controls scaled to actual need, not blanket-granted) — the framework Cascade and Fieldstone are actually examined under as banks, not the FTC Safeguards Rule, and it's the specific failure mode `cloud_retrofit`'s IAM Access Analyzer findings already demonstrated elsewhere in this portfolio — over-broad access granted under time pressure doesn't get cleaned up later, it becomes the new normal until an audit or an incident finds it.

The correct default is closer to the opposite: nobody gets access to the other bank's systems on Day 1 beyond what specific, identified roles genuinely require for customer-facing continuity, with every exception explicitly justified and time-bounded rather than granted as a blanket policy.

---

## Reconciling Two Entitlement Models

The harder, slower work: Cascade and Fieldstone almost certainly have different role definitions for functionally similar jobs — a "branch manager" role at one bank may have permissions that don't map cleanly to the equivalent role at the other, in the same way `platform_and_schema_reconciliation.md`'s product schemas don't map cleanly either. The same crosswalk discipline applies here, structurally: a governed mapping table from each bank's roles to a unified role model, reviewed and attributed, not assumed identical because the job titles sound similar.

```sql
-- The same crosswalk pattern as the product schema, applied to roles
CREATE TABLE GOVERNANCE.ROLE_CROSSWALK (
    source_bank        STRING,
    source_role        STRING,
    source_permissions ARRAY,
    unified_role        STRING,
    unified_permissions ARRAY,
    _reviewed_by         STRING,
    _reviewed_at          TIMESTAMP_NTZ
);
```

Permissions that exist at one bank but not the other need an explicit decision, not a default — does the unified role inherit the more permissive bank's access, or the more restrictive one's? Consistent with `regulatory_considerations.md`'s "resolve toward the stronger standard" principle for GLBA program gaps, the default here is the more restrictive of the two, with expansion requiring active justification rather than inherited by accident because one bank's role happened to be broader.

---

## SSO and Federation Consolidation

The mechanical end state — one identity provider, one SSO flow, both banks' staff authenticating through a single federated system rather than maintaining two parallel login paths indefinitely — follows the same IAM Identity Center / Workload Identity Federation patterns `account_landing_zone_guardrails.md` already establishes in the sibling `data_sec` project for a single organization. What's different here isn't the target architecture, it's the migration path to it: two existing AD forests have to be assessed, trust relationships established or the forests consolidated outright, and every existing account mapped through the role crosswalk above before it's provisioned in the unified system — not bulk-imported with whatever permissions each account happened to already have.

---

## Timing Against Deal Phase

None of this starts before close, per `deal_phase_data_governance.md` — pre-close, the two banks' IAM systems have no legitimate reason to be connected at all, since connecting them would itself be a form of premature operational combination. Day 1 readiness means the minimum necessary bridging for customer continuity, explicitly scoped and time-bounded. The actual entitlement reconciliation and SSO consolidation described above is Day 2+ work, proceeding at the same deliberate pace `platform_and_schema_reconciliation.md`'s schema crosswalk does — both are the kind of work that's genuinely dangerous to rush toward an artificial deadline.

---

## Sources

- Internal: `deal_phase_data_governance.md`, `regulatory_considerations.md`, `platform_and_schema_reconciliation.md`; `account_landing_zone_guardrails.md` (sibling repo `data_sec`); `current_state_discovery.md` (sibling repo `cloud_retrofit`, for the IAM Access Analyzer over-broad-access finding pattern)
