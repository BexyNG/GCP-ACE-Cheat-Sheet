# 📌 GCP Monitoring & Observability Cheat Sheet

---

## **1. Cloud Logging**

* **Purpose**: Collect, store, and query logs from GCP services, apps, and agents.
* **Key Features**:

  * Logs Explorer (query/filter logs).
  * **Logs-based metrics** (counter/distribution) → usable in Cloud Monitoring alerts.
  * **Log sinks** → export logs to BigQuery (analytics), Pub/Sub (streaming), Cloud Storage (archive).
* **Exam tip**:

  * *Metrics* → alerting/dashboards.
  * *Sinks* → exporting logs.
  * Long-term storage → **Cloud Storage with retention policy**.

---

## **2. Cloud Monitoring**

* **Purpose**: Metrics, dashboards, alerting, uptime checks, SLOs.
* **Key Features**:

  * Collects system metrics (CPU, memory, disk, GKE, APIs).
  * **Alerting policies** → thresholds + alignment periods.
  * **Uptime checks** → synthetic availability tests from global regions.
  * **Dashboards** → custom charts, uptime check results, group metrics by labels.
* **Exam tip**:

  * Regional downtime → **uptime checks**.
  * Alert noise → adjust **alignment/aggregation window**.
  * Native channels: **Email, Webhook, PagerDuty** (❌ no SMS directly).

---

## **3. Cloud Trace**

* **Purpose**: Distributed tracing of requests.
* **Key Features**:

  * Shows spans across microservices (latency bottlenecks).
  * Displays **p50, p90, p99 latency distributions**.
  * Can correlate trace IDs with logs.
* **Exam tip**:

  * Use for **latency problems**.
  * ❌ Not for profiling CPU/memory.

---

## **4. Cloud Profiler**

* **Purpose**: Continuous low-overhead profiling of live apps.
* **Key Features**:

  * Tracks **CPU/memory usage per function/method**.
  * Runs in production with <1% overhead.
  * Supports Java, Go, Node.js, Python.
* **Exam tip**:

  * Use for **memory leaks or CPU hotspots**.
  * ❌ Does NOT show system-level CPU (only app code).

---

## **5. Error Reporting**

* **Purpose**: Detects and groups uncaught exceptions.
* **Key Features**:

  * Groups errors with similar **stack traces**.
  * Integrates with Cloud Logging.
  * Sends **real-time notifications** for new errors.
* **Exam tip**:

  * Use for **500 errors, runtime exceptions**.
  * ❌ Not for latency or performance.

---

## **6. Cloud Debugger**

* **Purpose**: Debug live applications without stopping them.
* **Key Features**:

  * Capture **snapshots of variables/state** at specific breakpoints.
* **Exam tip**:

  * Use for debugging **live code logic**.
  * ❌ Not used for metrics, errors, or latency.

---

## **7. Cloud SQL Insights**

* **Purpose**: Analyze SQL query performance.
* **Key Features**:

  * Shows **slow queries by latency/cost**.
  * Provides execution plans.
* **Exam tip**:

  * If it’s **SQL latency** → **SQL Insights**, not Trace/Profiler.

---

## **8. IAM & Audit Logs**

* **Audit log types**:

  * **Admin Activity** → always on, free.
  * **System Events** → autoscaling, VM restarts.
  * **Data Access** → disabled by default, billable.
* **IAM Roles**:

  * `roles/logging.viewer` → standard logs.
  * `roles/logging.privateLogViewer` → **Data Access logs**.
  * `roles/monitoring.viewer` → dashboards & metrics.
  * `roles/monitoring.dashboardViewer` → dashboards only.

---

## ⚡ Quick Exam Mnemonics

* **Latency → Trace**
* **CPU/Memory leaks → Profiler**
* **Exceptions → Error Reporting**
* **Live debugging → Debugger**
* **Audit logs access → privateLogViewer**
* **Regional downtime → Uptime checks**
* **Archive logs long-term → Cloud Storage**
* **Analytics on logs → BigQuery sink**
