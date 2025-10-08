# GCP Load Balancing & Autoscaling Cheat Sheet (Focused on Missed Topics)

## 1. Cross-Region Traffic & Health Checks

### Key Concept

* **Global HTTP(S) Load Balancers** automatically handle cross-region routing.
* They only send traffic to **healthy backends**.
* If a **health check is deleted**, the backend service marks all instances **unhealthy** until a new check is attached.

### Fix & Prevention

* Always define health checks at the backend service level.
* Validate:

  * Firewall allows health check IPs (`35.191.0.0/16`, `130.211.0.0/22`)
  * Correct protocol (HTTP vs TCP)
  * Startup time fits within health check timeout

### Exam Tip

> "Traffic only goes to one region" → Check **health check status**, not a configuration flag.

---

## 2. Internal vs External Load Balancers

| Type                               | Layer | Protocol   | Reachability      | Use Case                        |
| ---------------------------------- | ----- | ---------- | ----------------- | ------------------------------- |
| **Internal TCP/UDP (Passthrough)** | 4     | TCP/UDP    | Same VPC / Hybrid | Private apps, hybrid networking |
| **Internal HTTP(S)**               | 7     | HTTP/HTTPS | Same VPC only     | Private web apps (no Peering)   |
| **External HTTP(S)**               | 7     | HTTP/HTTPS | Global            | Public web apps, CDN            |

### Key Rules

* Internal HTTP(S) **not accessible via VPC Peering**.
* Internal TCP/UDP supports **VPN/Interconnect** (hybrid connectivity).

### Exam Tip

> "Need internal LB across VPN" → Use **Internal TCP/UDP LB**.

---

## 3. Proxy vs Passthrough Network Load Balancers

| Type                                      | Connection Behavior          | Preserves Source IP | Supports Hybrid?  |
| ----------------------------------------- | ---------------------------- | ------------------- | ----------------- |
| **Proxy NLB (TCP/SSL Proxy)**             | Terminates client connection | ❌                   | ❌                 |
| **Passthrough NLB (External / Internal)** | Forwards connection directly | ✅                   | ✅ (Internal only) |

### Quick Rule

* **Need client IP** → Passthrough NLB
* **Need TLS termination** → Proxy NLB

---

## 4. Regional vs Global Load Balancers

| LB Type                              | Scope              | Example Use Case                          |
| ------------------------------------ | ------------------ | ----------------------------------------- |
| **Global External HTTP(S)**          | Multi-region       | Worldwide traffic distribution            |
| **Regional External Application LB** | Single region      | Regional, compliance-restricted workloads |
| **Internal HTTP(S)**                 | Regional (private) | Private internal web apps                 |

### Exam Tip

> "Traffic must stay in one region" → **Regional External Application LB**.

---

## 5. Hybrid NEGs (Network Endpoint Groups)

### Key Concept

* **Hybrid NEGs** allow attaching **non-GCE backends** (on-premises, other clouds).
* Supported **only** by **External Application Load Balancers (HTTP/HTTPS)**.

### Use Cases

* Hybrid workloads (part on-prem, part in GCP)
* Gradual cloud migrations

### Exam Tip

> "Need to send traffic to on-prem and GCE VMs" → Use **External Application LB + Hybrid NEGs**.

---

## 6. Health Check Timeout & Application Startup

### Issue

If VMs take longer to start, health checks might fail prematurely.

### Fix

Increase parameters:

```bash
gcloud compute health-checks update http my-check \
  --check-interval=20s \
  --timeout=10s \
  --unhealthy-threshold=5
```

### Exam Tip

> "App healthy manually but failing health checks" → **Timeout or protocol mismatch**.

---

## 7. Autoscaling Cooldown & Cost Optimization

### Key Concept

* **Cooldown** = wait time after scaling before next evaluation.
* High cooldown → slower reaction → higher cost.

### Fix

* Lower cooldown for faster scale down.
* Use pre-baked images for quick boot.

### Exam Tip

> "Scaling down too slowly, increasing cost" → **Lower cooldown period**.

---

## Quick Mnemonics & Decision Table

| Symptom                      | Correct Answer                   |
| ---------------------------- | -------------------------------- |
| Private access only          | Internal LB                      |
| Hybrid/on-prem connectivity  | Internal TCP/UDP LB              |
| HTTPS or CDN required        | External HTTP(S) LB              |
| Preserve client IP           | Passthrough NLB                  |
| Limit traffic to one region  | Regional External Application LB |
| Cross-region not working     | Health check issue               |
| Startup failing health check | Increase timeout                 |

---

### Author Notes

This cheat sheet was generated for **Google Cloud Associate Cloud Engineer (ACE)** exam prep. Focus areas:

* Load Balancer design patterns
* Autoscaling tuning
* Troubleshooting & IAM permissions

**Goal:** Improve recall on LB scope, health checks, and hybrid connectivity for exam readiness.
