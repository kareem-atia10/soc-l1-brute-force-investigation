# SOC L1 Incident Report — Brute-Force Login Investigation

**Case ID:** SOC-L1-2026-001  
**Status:** Simulated case study — investigation completed on sample data  
**Severity:** Medium (provisional; subject to environment and account criticality)  
**Detection window:** 2026-10-26 09:01:03–09:12:19 (timestamps in the training dataset; timezone not specified)  
**Primary tool modeled:** Splunk Search & Reporting  
**Data source:** Synthetic Windows-style authentication CSV created for this exercise

## 1. Executive summary
The sample dataset contains 12 failed logon events (EventCode 4625) for account `john` from source IP `185.199.110.23`, followed by a successful logon (EventCode 4624) from the same IP approximately five minutes later. This sequence is suspicious and is consistent with a possible password-guessing/brute-force attempt followed by account access. The evidence does **not** prove that the source is malicious or that the successful session was unauthorized. Escalation for further validation is recommended.

## 2. Scope and evidence
Reviewed 21 synthetic authentication records:
- 12 failures for `john` from `185.199.110.23`.
- 1 successful logon for `john` from that same IP.
- 5 failures for `administrator` from `103.45.67.89`.
- 3 failures for `test` from private IP `192.168.1.45`.

The dataset is illustrative and was not collected from a real environment. No actual Splunk search was executed; the SPL queries are provided for use if the CSV is ingested into a Splunk index.

## 3. Timeline (primary case)
- 09:01:03 — first recorded failed logon for `john` from `185.199.110.23`.
- 09:02:31 — twelfth recorded failed logon for `john` from the same IP.
- 09:07:36 — successful logon for `john` from the same IP, about 5 minutes after the last recorded failure.

## 4. Analysis
**Indicators of concern**
1. Repeated failures against one account from one source.
2. The subsequent successful logon from the same source.
3. The event sequence warrants checking whether the source and login were expected.

**Alternative explanations / false positives**
- A user or application may have repeatedly supplied an outdated password.
- A VPN, NAT gateway, proxy, or shared egress address may make multiple users appear under one IP.
- The sample fields omit key Windows details such as Logon Process, Authentication Package, workstation name in original event XML, status/substatus codes, and session/process correlation.

## 5. Triage decision
**Decision: Suspicious — escalate for validation.**  
Do not label this as a confirmed brute-force attack based only on this sample. Confirm whether the account owner recognizes the login, validate source-IP reputation and asset context using approved internal tools, review MFA and conditional-access logs, and correlate endpoint/network activity around the successful session.

## 6. Recommended next actions
1. Validate the successful logon with the account owner or service owner through the established process.
2. Review the full Windows Security event details for 4625/4624, including status/substatus, Logon Type, authentication package, workstation, and target host.
3. Correlate VPN, firewall, identity provider, MFA, EDR, and other SIEM events around the detection window.
4. If unauthorized access is supported by evidence, follow the incident-response playbook: contain the session/source as approved, reset credentials where appropriate, revoke active sessions/tokens, and assess for lateral movement.
5. Create/tune a detection for repeated failures per account/source over a defined time window, with exceptions for known scanners and approved systems.
6. Preserve relevant logs and document timestamps, timezone, query, and evidence for auditability.

## 7. Detection logic (example)
A starting point is to alert on 5 or more failed logons for the same account and source within 5 minutes. Tune the threshold against baseline behavior and avoid treating a threshold hit as proof of compromise. Correlate a subsequent successful logon from the same source/account to raise priority.

## 8. Limitations
- This is a synthetic learning scenario, not a real incident.
- Results were calculated from the supplied sample CSV, not from a live Splunk instance.
- The source IPs are fictional placeholders for this exercise; do not infer real-world reputation from them.
- Timezone and original Windows event metadata were not supplied.
- No evidence was provided for MFA, endpoint execution, privilege changes, data access, or lateral movement.

## 9. Final assessment
The primary sequence is a **credible suspicious pattern in the training dataset**: 12 failures for `john` from one source, then a success from that source. The appropriate SOC L1 action is to document the evidence, assess context, and escalate for validation—not to assert confirmed compromise.

## Appendix A — SPL queries
See `investigation_queries.spl`.
