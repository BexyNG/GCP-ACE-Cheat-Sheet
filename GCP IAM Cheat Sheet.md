# 🚀 GCP IAM Cheat Sheet (ACE Exam)

## 1. Roles in IAM

### 🔹 Basic Roles (legacy, project‑wide)
- **Owner** → Full control + IAM changes.  
- **Editor** → Modify resources, **NOT** IAM.  
- **Viewer** → Read‑only.  

⚠️ **Exam trap:** Editor ≠ IAM management.

### 🔹 Predefined Roles (granular, recommended)
- Resource‑specific (e.g., `roles/compute.instanceAdmin.v1`, `roles/storage.objectAdmin`, `roles/logging.logWriter`).  
- **Best practice** → use for **least privilege**.

### 🔹 Custom Roles
- Build when predefined roles don’t fit.  
- Watch for **missing permissions** causing errors.

---

## 2. IAM Conditions vs Organization Policies

### 🔹 IAM Conditions (micro)
- Constraints on **specific role grants**.  
- Examples:  
  - Time‑based → expire after 30 days.  
  - Resource‑based → only for bucket `projectX-logs`.

### 🔹 Organization Policies (macro)
- Org‑wide guardrails.  
- Examples:  
  - Block service‑account key creation.  
  - Enforce CMEK (Customer‑Managed Encryption Keys).  
  - Restrict external resource sharing.

⚠️ **Conditions** = narrow. **Org Policies** = global.

---

## 3. Audit Logs in Cloud Logging

- **Admin Activity Logs** → Always on. Capture IAM/config changes, resource create/delete.  
- **Data Access Logs** → Optional. Record reads/writes to **data** (e.g., BigQuery query, GCS download).

⚠️ **Exam trap:** IAM changes = **Admin Activity**, not Data Access.

---

## 4. Service Account Best Practices

- Attach service accounts to workloads (VMs, Cloud Run, Functions).  
- **Never upload JSON keys** into VMs/code.  
- Grant roles to the **service account**, not the developer.  
- Deleting a service account → running VMs may keep working until restart (cached tokens), then fail.  
- Undelete possible within 30 days.

---

## 5. Extra Notes / Exam Traps

- **Separation of duties** → Split responsibilities (one creates SAs, another assigns roles).  
- **IAM Recommender** → Suggests least‑privilege roles.  
- **Storage nuance:**  
  - `roles/storage.objectAdmin` = CRUD objects.  
  - Listing objects requires `roles/storage.objectViewer` at bucket level.  
- **Cross‑service IAM:**  
  - Pub/Sub → Cloud Function needs `roles/cloudfunctions.invoker` on Pub/Sub SA.  
  - Cloud Run/Functions → grant storage/logging roles to **service account**, not dev.

---

✅ **Memorize these distinctions — they’re common ACE exam traps!**