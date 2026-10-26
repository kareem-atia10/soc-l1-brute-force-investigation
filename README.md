# SOC L1 Project: Brute-Force Attack Investigation

## Purpose
Practice Tier 1 triage of repeated failed logons and a subsequent successful logon.

## Important honesty note
This repository artifact is a **simulated case study**. The CSV is synthetic training data. The SPL queries have not been run against a live Splunk instance, and the visual preview is illustrative rather than a real Splunk screenshot.

## Files
- `authentication_events.csv` — 21 synthetic authentication events.
- `investigation_queries.spl` — example SPL searches.
- `incident_report.md` — investigation summary, triage decision, limitations, and recommendations.
- `results_summary.csv` — summarized counts calculated from the synthetic dataset.

## Findings from the sample
- 12 failed logons for `john` from `185.199.110.23`.
- A successful logon for `john` from the same IP about five minutes after the last recorded failure.
- Decision: suspicious; escalate for validation. Not confirmed compromise.

## Suggested workflow in Splunk (when an environment is available)
1. In Search & Reporting, ingest `authentication_events.csv` into an index you control.
2. Confirm field extraction and timestamp parsing.
3. Adjust `index=main` in the sample SPL if your chosen index differs.
4. Run each query and compare results with the report.
5. Replace the illustrative result visuals with genuine screenshots only after executing the queries.
