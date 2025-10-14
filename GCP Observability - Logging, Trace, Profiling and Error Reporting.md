# GCP Observability (Exam Focus)

## 🔍 Cloud Logging

Stores logs from GCP services, agents, and custom apps.

**Key roles:**
- `roles/logging.logWriter` → write logs
- `roles/logging.viewer` → read logs

**Cost tips:** Use exclusion filters and sinks (export to BigQuery, Storage, Pub/Sub).

**Logs-based metrics:** Turn log patterns into numeric metrics for alerting.

## 📈 Cloud Monitoring

Collects metrics, creates dashboards & alerting policies.

Supports uptime checks, custom metrics, log-based metrics.

**Alert setup:** Choose metric → set threshold → choose duration (e.g., 5 min).

Integrates with Notification Channels (email, SMS, Slack, PagerDuty).

## 💥 Error Reporting

Groups identical stack traces automatically.

Flags new vs recurring errors.

Integrates with Logging and Monitoring notifications.

Ideal for "uncaught exceptions" and runtime crashes.

## ⚡ Cloud Trace

Distributed tracing → latency visualization across services.

Shows percentiles (p50/p90/p99) and bottlenecks per request.

Requires sampling (may miss some traces).

Great for identifying where delays happen.

## 🧩 Cloud Profiler

Continuous profiling for CPU and heap usage.

Identifies why code consumes CPU/memory.

Minimal overhead (samples live processes).

Best used after Trace identifies the slow service.

## 💡 Common Exam Differentiators

| Task | Service |
|------|---------|
| Find which service adds latency | Trace |
| Find which function consumes CPU | Profiler |
| Get notified on new exception | Error Reporting |
| Alert on threshold | Monitoring |
| Reduce logging cost | Logging exclusions |
| Combine logs & metrics | Logs-based metric + Monitoring alert |