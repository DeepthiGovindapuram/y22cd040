# Incident Response Playbook — Suspicious Cloud API Key Activity

Scope
Detect and respond to suspicious/high-volume API key usage that may indicate compromised credentials.

Detection triggers
- Spike in API usage from a single key across regions or services
- Access to sensitive APIs not seen previously for this key
- Concurrent usage from distinct IP ranges with impossible travel

Initial automated actions (safe, reversible)
1. Enrich: resolve key owner, associated IAM user/role, recent activity, asset context.
2. Notify: create alert in SIEM and notify on-call via PagerDuty/Slack with context.
3. Quarantine read-only: temporarily reduce privileges of the key to read-only (if supported) OR apply restrictive IAM policy for a short time window.
4. Block: add source IPs to blocklist for ingress (if traffic is malicious and IPs are static).
5. Snapshot: take forensic snapshots of affected VMs/containers and store logs to cold storage.
6. Escalate: if high-confidence compromise (model confidence + asset criticality), require human approval to rotate/delete keys and revoke sessions.

Human-in-the-loop steps
- Security analyst reviews the enriched context, logs, and network indicators.
- Analyst chooses to rotate keys, rollback policies, or fully revoke sessions.
- All actions are recorded in audit log and tagged to the incident.

Post-incident
- Root cause analysis and timeline
- Update detection rules/models with labeled event
- Update runbook and adjust thresholds to reduce false positives
- Notify asset owner and update CMDB

Safety & rollback
- All automated privilege changes have automatic rollback after X minutes unless approved.
- Destructive actions (delete key, terminate instance) require explicit two-person approval.

Metrics to capture
- Time from detection to quarantine (goal: < 5 minutes)
- Number of false positives
- Analyst time to resolution