# NammaMart — submission working draft

**Status: PARTIALLY IMPLEMENTED; NOT YET VERIFIED IN AIRFLOW. Do not claim all acceptance tests have passed.**

## Part 0 — Profiling

See `DATA_PROFILE.csv` (six files) and `DATA_ISSUES_LOG.csv` (detected issues and quality dimensions). These are automated preliminary findings; manually inspect and supplement the log for every file.

Questions for the client:
1. Is a negative quantity always invalid, or does it represent a return?
2. For returned orders, should the original September revenue be restated or should both historical and current views be available?
3. Should Cash on Delivery be reconciled to the payment gateway, or to a separate cash settlement ledger?

## Part C — Schedule

Cron `0 9 * * 1-6`, timezone `Asia/Kolkata`, equals **03:30 UTC**, Monday–Saturday. `catchup=False` avoids automatically backfilling all missed historical schedules. If the start date were interpreted as UTC, the 9:00 run would execute at 14:30 IST instead. If the laptop/Docker is off, a local scheduler cannot execute at the intended time; deploy to an always-on environment for production SLA.

## Part E — Governance (draft)

- Owners: operations owns orders/stores; finance owns payments and reconciliation; CRM owns customers; merchandising owns products; data platform owns warehouse operations. Owners and finance approve changes to critical quality thresholds.
- PII: `full_name`, `email`, `phone`, and potentially address-related `area`, `pincode`, and customer identifiers. Reporting customer email and phone are masked. Raw customer access should be restricted to authorized data stewards.
- Lineage: daily revenue is a sum of delivered, validated order amounts, linked to store IDs from `orders.csv` and zones from `stores.csv`. Raw ELT makes replay easier; ETL quarantine preserves rejected source records.
- Access: only data engineers and designated compliance staff should access raw and quarantine; analysts may access masked staging; executives access aggregate marts.
- Retention: agree on a documented retention schedule with legal and business owners; delete or anonymize data at expiry, including backups and quarantine files.
- Secrets: use Airflow Variables or a secrets backend, never hardcode credentials or commit them.
- Indian law: the Digital Personal Data Protection Act, 2023, and applicable rules/commencement provisions require lawful handling and safeguards for personal data. Confirm current obligations with legal counsel.
- Audit: compare quarantined `order_id` and failure reason with ETL fact exclusions and ELT staging `failure_reason`.
- Corrections: a later RETURNED status should adjust current net revenue; preserve historical snapshots/event history separately to show what was known at the earlier reporting date.

## Part F — Observability

**Not executed.** Required experiments: happy path, rerun, increment, missing file, invalid rows, schema change, volume anomaly, distribution drift, freshness, recovery. Record Expected / Observed / Evidence / Improvement after real Airflow runs. Freshness, volume, schema, distribution and lineage checks require additional instrumentation.

## Part G — Preliminary recommendation to Sameer, CTO

Recommend an ELT-first architecture with immutable raw records and strict quality-gated marts, provided finance is restricted to validated marts. It improves traceability, replay, and flexibility as source definitions evolve. The ETL pipeline remains a useful finance-oriented comparison and a fallback. This is provisional until actual timing, cost and reconciliation results are recorded. Top risks: unmasked raw PII access; inconsistent rule implementations between ETL and ELT; and local Docker availability at 9 AM.

## Remaining work before final submission

- Run both DAGs under Astro and fix any import/runtime errors.
- Complete `dq_results`, robust `pipeline_runs` failure audit, SQL-only ELT incremental MERGE, and ETL incremental corrections.
- Run all 10 experiments for **both** DAGs, record actual results and screenshots.
- Produce SQL queries **and verified results** for six business questions from each pipeline.
- Capture Graph view, schedule, quarantine, and DuckDB query screenshots.
- Push a fork or branch to GitHub and submit **its URL**.
