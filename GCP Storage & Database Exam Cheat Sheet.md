# ☁️ GCP Storage & Database Exam Cheat Sheet

## Table of Contents

- [Bigtable vs Spanner vs Firestore](#-bigtable-vs-spanner-vs-firestore)
- [Cloud SQL vs AlloyDB](#-cloud-sql-vs-alloydb)
- [Cloud Storage Classes](#-cloud-storage-classes)
- [Quick Exam Triggers](#-quick-exam-triggers)

---

Quick reference for **Google Cloud Associate Cloud Engineer** exam.

## 🌐 Bigtable vs Spanner vs Firestore

### 🔹 Cloud Spanner

* **What it is:** Globally distributed, relational database.
* **Best for:** SQL, strong consistency, schemas, ACID transactions across regions.
* **Think of it as:** *Cloud SQL’s global, enterprise big brother.*
* **Exam Trigger Words:** `global`, `relational schema`, `ACID`, `multi-region`

### 🔹 Bigtable

* **What it is:** Wide-column NoSQL DB for huge scale and time-series.
* **Best for:** Massive write throughput, low-latency reads, billions of rows.
* **Think of it as:** *A giant spreadsheet optimized for speed.*
* **Exam Trigger Words:** `time-series`, `millions of writes/sec`, `IoT`, `real-time`

### 🔹 Firestore

* **What it is:** Document-based NoSQL DB with strong consistency.
* **Best for:** JSON docs, offline sync, auto-scaling, web/mobile apps.
* **Think of it as:** *A JSON database with mobile/web SDKs.*
* **Exam Trigger Words:** `JSON`, `documents`, `mobile`, `offline sync`

### 🧠 Memory Hack

* **Global + SQL + Transactions = Spanner**
* **Time-series + Scale = Bigtable**
* **Docs + JSON + Mobile = Firestore**

---

## 🗄️ Cloud SQL vs AlloyDB

### 🔹 Cloud SQL

* Fully managed MySQL, PostgreSQL, SQL Server.
* Best for: Small/medium workloads, single-region apps.
* Cost-effective and simple.
* **Limit:** Limited PostgreSQL extension support.
* **Exam Trigger Words:** `lift-and-shift`, `small/medium`, `single region`

### 🔹 AlloyDB

* PostgreSQL-compatible with enterprise performance.
* Supports **custom PostgreSQL extensions**.
* Best for: Enterprise-scale workloads, Oracle migrations.
* **Exam Trigger Words:** `enterprise`, `PostgreSQL extensions`, `Oracle migration`

---

## 🗃️ Cloud Storage Classes

### 🔹 Standard

* Frequent access.
* **Use for:** Active apps, websites, ML training datasets.

### 🔹 Nearline

* Accessed < 1x per month.
* **Use for:** Backups, occasional ML jobs.

### 🔹 Coldline

* Accessed < 1x per quarter.
* **Use for:** Disaster recovery, quarterly data retrieval.

### 🔹 Archive

* Rarely accessed, long-term storage (years).
* **Use for:** Compliance logs, 7+ year audit retention.

### 🧠 Memory Hack

**Ignore what the data is called (backups, logs, etc.). Always pick based on ACCESS FREQUENCY.**

* Frequent → Standard
* Monthly → Nearline
* Quarterly → Coldline
* Rare/Years → Archive

---

## 🔑 Quick Exam Triggers

* **Global + Relational + ACID → Cloud Spanner**
* **Time-series + Scale → Bigtable**
* **JSON + Mobile → Firestore**
* **Single-region SQL → Cloud SQL**
* **Enterprise PostgreSQL + Extensions → AlloyDB**
* **Rare access storage → Nearline/Coldline/Archive depending on frequency**
