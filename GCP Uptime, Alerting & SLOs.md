# 🧾 Custom 1-Page Cheat Sheet: Uptime, Alerting & SLOs

## 🔍 Core Concepts

| Term | Meaning |
|------|---------|
| **SLI** | Actual measurement (e.g., % of requests <200 ms). |
| **SLO** | Target goal for SLI (e.g., 99%). |
| **SLA** | External commitment based on SLO (with penalties). |
| **Error Budget** | 1 − SLO; allowable failure margin. |

## 🌐 Uptime Checks

* **Public uptime checks** = test external HTTP(S) endpoints.
* **Private uptime checks** = monitor internal IPs via collectors.
* Ensure firewalls allow Monitoring probe IPs.
* For Cloud Run, allow unauthenticated access or use private check.

## 🚨 Alerting Policies

* **Metric threshold**: alert when a metric exceeds a limit.
* **Metric absence**: alert when a resource stops reporting.
* **AND logic** = all conditions must be true.
* **OR logic** = any condition triggers.
* **Alignment period** = window for metric aggregation.
* **Duration** = how long the condition must persist.
* **Notification cooldown** = prevents spam while incident is open.

## 📏 Error Budgets & Responses

| Burn Rate | SRE Response |
|-----------|--------------|
| Low | Proceed normally |
| Moderate | Increase caution, monitor closely |
| High | Pause releases, focus on reliability |
| Breach | SLO violated, postmortem & SLA check |

## 🧮 Downtime Allowance Quick Table

| SLO | Max Downtime / Month |
|-----|----------------------|
| 99% | ~7h 12m |
| 99.5% | ~3h 36m |
| 99.9% | ~43m |
| 99.99% | ~4m 23s |

## 🧠 Exam Tips

* Watch for authentication issues in uptime check scenarios.
* Remember: SLI → SLO → SLA (increasing formality).
* Alerts always have evaluation delay due to alignment + sampling.
* For multi-region checks, use "count ≥ N" trigger to reduce noise.
* Verify notification channels — alerts won't send otherwise.