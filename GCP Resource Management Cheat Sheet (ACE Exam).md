# 🚀 GCP Resource Management Cheat Sheet (ACE Exam)

## Table of Contents

- [Resource Hierarchy](#-resource-hierarchy)
- [Quotas](#-quotas)
- [Organization Policies](#-organization-policies)
- [Billing](#-billing)
- [Quotas vs Budgets vs Policies](#-quotas-vs-budgets-vs-policies)
- [Labels](#-labels)
- [Project Creation](#-project-creation)

---

## 🔹 Resource Hierarchy

* **Organization** = top node (1 per company).
* **Folders** = group projects (optional).
* **Projects** = container for resources, IAM, billing.
* **Resources** = actual services (VMs, buckets, DBs).

👉 IAM & policies flow **top → down** (inheritance).

---

## 🔹 Quotas

* **What**: Numeric limits (per project, per region).
* **Types**:

  * **Allocation** → # CPUs, # GPUs, static IPs.
  * **Rate** → API requests/sec.
* **Purpose**: Prevent abuse + runaway spend.
* **Errors**: `"Quota exceeded"` → 403.
* **Fix**: Delete resources or **request quota increase**.
* **Exam Keyword Trigger**: *“Limit number of resources”* = **Quota**.

---

## 🔹 Organization Policies

* **What**: Governance rules, not numeric caps.
* **Examples**:

  * Restrict regions (`only us-central1`).
  * Disallow external IPs on VMs.
  * Require labels (`env=prod`).
  * Restrict which services can be enabled.
* **Apply**: Org → Folder → Project (inherit down).
* **Exam Keyword Trigger**: *“Restrict where/what resources can be created”* = **Org Policy**.

---

## 🔹 Billing

* Each **project must link to exactly 1 billing account** (for paid services).
* **Billing Roles**:

  * `Billing Viewer` → see costs only.
  * `Billing User` → attach projects to billing accounts.
  * `Billing Admin` → manage accounts & payments.
* **Budgets & Alerts**: Notify when usage > thresholds (e.g. 80%, 100%).

  * ⚠️ Budgets **do not stop resources**.

---

## 🔹 Quotas vs Budgets vs Policies

* **Quotas = HARD CAP** → “you can’t use more than 24 CPUs.”
* **Budgets = ALERT ONLY** → “spend hit $5000, notify Finance.”
* **Org Policies = RULES** → “VMs must not have external IPs.”

---

## 🔹 Labels

* **What**: Key-value pairs on resources (`env=prod`).
* **Use**: Cost allocation, filtering in billing reports.
* **Don’t inherit** from projects/org.
* **Enforcement**: Use Org Policy → “required labels.”
* **Exam Trap**: IAM has nothing to do with labels.

---

## 🔹 Project Creation

* **Role**: `roles/resourcemanager.projectCreator` at **organization level**.
* **Owner role** only works inside an existing project (can’t create new ones).
* **Org Admin** can move projects between folders.

---

## 🧠 Memory Triggers

* “Limit how much?” → **Quota**.
* “Restrict where/what?” → **Org Policy**.
* “Notify me when spending spikes?” → **Budget**.
* “Track cost by team/env?” → **Labels**.
* “Who can make projects?” → **Project Creator** at org.
* “Top of hierarchy?” → **Organization node**.
