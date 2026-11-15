# Submission preparation

This is a **working draft**, not a complete verified submission. It includes the original repository, CSVs copied to `include/data/nammamart/`, profiling outputs, draft ETL/ELT DAGs, SQL transforms, and a `SUBMISSION.md` documenting gaps.

To test: install Docker Desktop, Astro CLI and the learner setup; run `astro dev start`, then `astro dev run dags list-import-errors`, and trigger both DAGs. Airflow UI is http://localhost:8080. The trainer expects **one GitHub repository link**; upload this project to your own fork/branch only after verifying it.

Known gaps: ELT SQL is preliminary; ETL/ELT incremental loads, full quality scorecards, failure audits, all 10 experiments, and screenshots are not finished. Never submit these as completed without testing.
