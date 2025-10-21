# 🧾 Cheat Sheet — GCP Data & Infra Services (Exam Focus)
## Table of Contents

- [BigQuery](#🧮-bigquery)
- [Dataflow](#⚙️-dataflow)
- [Dataproc](#🧰-dataproc)
- [Dataprep](#🧼-dataprep)
- [Terraform on GCP](#🏗️-terraform-on-gcp)
- [Google Cloud Marketplace](#🛒-google-cloud-marketplace)
- [Common Exam Differentiators](#💡-common-exam-differentiators)

## 🧮 BigQuery

Serverless, cost-optimized data warehouse

### 🔹 Performance Tips

* 🕒 **Partition** → Use for time-based columns (`_PARTITIONTIME`)
* 🌍 **Cluster** → Use for categorical fields (e.g., region, product_id)
* ⚡ **Materialized Views** → Cache query results, refresh automatically
* 🔍 **EXPLAIN Plan** → Analyze stages, bottlenecks, and slot usage

### 🔹 Cost Optimization

* Query only needed partitions (`WHERE _PARTITIONDATE`)
* Use flat-rate slots for predictable cost
* Export infrequently used data to Cloud Storage

### 🔹 IAM Quick Roles

* `roles/bigquery.dataEditor` → Modify tables
* `roles/bigquery.jobUser` → Run queries/jobs

## ⚙️ Dataflow

Serverless batch + streaming data pipelines (Apache Beam)

### 🔹 Use Cases

* 📊 **Batch** → ETL for historical data
* 🔁 **Streaming** → Real-time (e.g., Pub/Sub → BigQuery)

### 🔹 Reliability & Scaling

* ✅ **Exactly-once delivery** → Use `insertId` for deduplication
* ⚙️ **Autoscaling** → Adjusts workers automatically
* ⏱️ **Windowing** → Tune to balance latency vs throughput
* 🧠 **Templates + Cloud Scheduler** → Automate recurring jobs

### 🔹 Exam Tip

Dataflow = "Managed Spark + Pub/Sub + BigQuery integration" with autoscaling and no cluster management.

## 🧰 Dataproc

Managed Hadoop/Spark for custom or legacy jobs

### 🔹 Best Practices

* ⚡ **Use ephemeral clusters** → Auto-delete after job completion
* 📦 **Store intermediate data in GCS** (gs://)
* 🌐 **Always co-locate** cluster + GCS in same region
* 💸 **Use preemptible workers** for cost savings (not masters!)

### 🔹 Use When

You already have Spark/Hadoop scripts and want minimal refactoring.

## 🧼 Dataprep

No-code visual data cleaning + transformation

### 🔹 Core Capabilities

* 🧩 Clean, standardize, and join datasets (Cloud Storage / BigQuery sources)
* 🎨 Auto-detect data types and missing values
* ⏰ Schedule jobs for recurring transformations
* 🚫 No code required (visual interface powered by Trifacta)

### 🔹 Exam Tip

Dataprep = "Data wrangling before analysis" — not for streaming or large-scale ETL.

## 🏗️ Terraform on GCP

Infrastructure as Code for consistent deployments

### 🔹 Common Commands

* 🧠 `terraform plan` → Preview changes
* 🚀 `terraform apply` → Deploy infrastructure
* 🧹 `terraform destroy` → Clean up resources

### 🔹 Design Patterns

* 📦 **Modules** → Reusable configs across projects
* 🌍 **Workspaces** → Separate environments (dev/prod)
* ☁️ **Remote Backend (GCS)** → Centralized, shared state with locking
* 🔐 **Enforce IaC** via Org Policies to prevent manual drift

### 🔹 Exam Tip

If it says "share state securely across teams" → Answer = Remote backend (GCS).

## 🛒 Google Cloud Marketplace

Preconfigured apps and solutions (VMs, APIs, containers)

### 🔹 Deployment Flow

1. 🔑 Subscribe in Marketplace UI
2. 🚀 Deploy directly to GCP (Compute Engine, GKE, etc.)
3. 🔍 Verify billing + license permissions
4. 🌐 Ensure same VPC or private connection for dependent services (e.g., Cloud SQL)

### 🔹 IAM & Network Reminders

* Check if app's service account has needed roles
* Private IP or VPC Peering required for SQL/Service integrations

### 🔹 Exam Tip

If you see "preconfigured third-party solution" → think Marketplace.

## 💡 Common Exam Differentiators

| Scenario | Correct Tool |
|----------|--------------|
| Stream data from Pub/Sub → BigQuery | Dataflow (streaming) |
| Clean messy CSVs visually | Dataprep |
| Run Spark job with minimal migration | Dataproc |
| Reuse Terraform config across projects | Module |
| Securely share Terraform state | Remote backend (GCS) |
| Speed up repeated BigQuery queries | Materialized View |
| Debug BigQuery query performance | EXPLAIN plan |
| Marketplace app can't reach Cloud SQL | VPC / Private IP check |