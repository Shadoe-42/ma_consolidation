# Platform and Schema Reconciliation
*Cascade Regional Bank + Fieldstone Bank — same business, two different answers for everything | July 2026*

---

## Purpose

Cascade runs its core banking data on Oracle. Fieldstone runs on SQL Server. Neither is on Snowflake today; both land there as part of this consolidation. That's two migration problems layered on top of each other: getting data out of two different heterogeneous sources (a problem the sibling `db_migration` project already solved the mechanics for), and — the problem genuinely specific to M&A, with no equivalent in a single-source migration — reconciling two schemas that represent overlapping business concepts differently, and resolving which customers appear in both banks' records under entirely unrelated IDs.

---

## Extraction: Not New Work

Getting data out of Oracle and SQL Server is exactly the relational-source problem `db_migration`'s `source_pattern_playbooks.md` already covers — log-based CDC or bulk extract-and-load, chosen per source's write pattern, landing in cloud storage ahead of Snowflake ingestion per `snowflake_ingestion_landing_patterns.md`. This doc doesn't re-derive that; it starts from the assumption that Cascade's and Fieldstone's core banking data both successfully land in `RAW` schemas in Snowflake, per that sibling project's staging-before-governance discipline, and picks up from there — because landing the data isn't the hard part of an M&A consolidation. Reconciling it is.

---

## Schema Crosswalk

Fieldstone calls it a "checking account." Cascade calls the same product a "DDA" (demand deposit account). Both banks have a status field for that product, and the two enumerations don't line up — Fieldstone's `ACTIVE`/`CLOSED`/`DORMANT` against Cascade's numeric status codes that mean something similar but not identically defined (Cascade's dormancy threshold is a different number of inactive days than Fieldstone's, for instance). Multiply this across every product line both banks offer, and the crosswalk itself — a mapping table maintained as a first-class governed artifact, not a one-time spreadsheet — is a substantial share of this doc's actual work.

```sql
-- The crosswalk lives in Snowflake as queryable, governed reference data,
-- not a spreadsheet someone eventually forgets to update.
CREATE TABLE GOVERNANCE.PRODUCT_SCHEMA_CROSSWALK (
    source_bank         STRING,       -- 'CASCADE' or 'FIELDSTONE'
    source_product_code STRING,
    source_status_code  STRING,
    unified_product_type STRING,      -- the merged entity's canonical value
    unified_status       STRING,
    effective_date        DATE,
    _reviewed_by          STRING,     -- crosswalk decisions are attributed, not anonymous
    _reviewed_at           TIMESTAMP_NTZ
);

-- Applied on the way from RAW into a unified ANALYTICS schema —
-- consistent with the classify-at-the-boundary pattern data_sec
-- already established, applied here to schema unification specifically.
CREATE OR REPLACE VIEW ANALYTICS.UNIFIED_ACCOUNTS AS
SELECT
    a.account_id,
    a.source_bank,
    c.unified_product_type,
    c.unified_status,
    a.balance,
    a.opened_date
FROM RAW.ACCOUNTS_COMBINED a
JOIN GOVERNANCE.PRODUCT_SCHEMA_CROSSWALK c
    ON a.source_bank = c.source_bank
    AND a.product_code = c.source_product_code
    AND a.status_code = c.source_status_code;
```

Every crosswalk decision gets attributed and dated, because a wrong mapping discovered six months post-close needs to be traceable to a specific decision and reviewable, not silently baked into the merged data with no record of who decided what "equivalent" meant.

---

## Customer Entity Resolution

The second, harder problem: a customer who banks at both Cascade and Fieldstone exists as two completely unrelated customer IDs, under names that may not even match exactly (a maiden name at one bank, a middle initial included at one and not the other, an LLC's registered name spelled slightly differently). Exact-match joins find none of these. Snowflake's native `JAROWINKLER_SIMILARITY` and `EDITDISTANCE` functions are the practical starting mechanism — Jaro-Winkler weighted toward prefix similarity for name matching specifically, edit distance for character-level precision on things like account or address strings:

```sql
SELECT
    c.customer_id  AS cascade_id,
    f.customer_id  AS fieldstone_id,
    c.full_name,
    f.full_name,
    JAROWINKLER_SIMILARITY(c.full_name, f.full_name) AS name_similarity,
    EDITDISTANCE(c.ssn_last4, f.ssn_last4) AS ssn_tail_distance
FROM RAW.CASCADE_CUSTOMERS c
JOIN RAW.FIELDSTONE_CUSTOMERS f
    ON JAROWINKLER_SIMILARITY(c.full_name, f.full_name) > 85
    AND c.date_of_birth = f.date_of_birth
WHERE EDITDISTANCE(c.ssn_last4, f.ssn_last4, 1) <= 1;
```

This produces candidate matches, not confirmed ones — a similarity score above a threshold is a worklist for review, not an automatic merge. Two customers who happen to share a birthdate and a similar name are a false positive a fully automated pipeline would silently get wrong, and a wrongly-merged customer record at a bank is a materially worse outcome than a wrongly-merged record almost anywhere else in this portfolio, given the direct tie to account access and financial data. High-confidence matches can merge automatically; everything else routes to manual review before the two records become one.

---

## The Unified Classification Scheme

Once accounts and customers reconcile, the merged data needs one classification scheme, not two banks' independently-evolved ones — following the same tiering approach `snowflake_data_security_guardrails.md` already establishes, applied to whichever bank's classification was more rigorous where the two disagree, per the same "resolve toward the stronger standard" principle `regulatory_considerations.md` already applies to GLBA program gaps.

---

## Sources

- Internal: `deal_phase_data_governance.md`, `regulatory_considerations.md`; `source_pattern_playbooks.md`, `snowflake_ingestion_landing_patterns.md` (sibling repo `db_migration`); `snowflake_data_security_guardrails.md` (sibling repo `data_sec`)
- [JAROWINKLER_SIMILARITY](https://docs.snowflake.com/en/sql-reference/functions/jarowinkler_similarity)
- [Post-Merger Customer Deduplication for Two Customer Databases](https://dataladder.com/post-merger-customer-deduplication-for-two-customer-databases-without-losing-data-integrity/)
