# ☁️ GCP Load Balancing & Autoscaling Cheat Sheet

This cheat sheet focuses on load balancer selection, health checks, and autoscaling for the **Google Cloud Associate Cloud Engineer (ACE)** exam.

---

## 1. Which Load Balancer Should I Use?

### 🔹 External vs. Internal

*   **External LBs**: For traffic from the internet. Have a public IP address.
*   **Internal LBs**: For traffic inside your GCP network (VPC, peered VPCs, connected on-prem networks). Have a private IP address.

### 🔹 Global vs. Regional

*   **Global LBs**: Distribute traffic to backends in **multiple regions**. Use for worldwide apps.
*   **Regional LBs**: Distribute traffic to backends in a **single region**. Use for regional apps or compliance needs.

### 🔹 Layer 7 (Application) vs. Layer 4 (Network)

*   **Layer 7 (HTTP/S)**: Make routing decisions based on HTTP headers, paths, etc. (e.g., `/video` goes to video backends). Supports SSL offload and CDN.
*   **Layer 4 (TCP/UDP)**: Make routing decisions based on IP and port. Forwards traffic without inspecting the content.

---

## 2. Load Balancer Decision Table

| Load Balancer Type | Scope | Layer | Use Case & Keywords |
| :--- | :--- | :--- | :--- |
| **Global External Application LB** | Global | 7 | Public website, CDN, SSL offload, multi-region failover, `Hybrid NEGs` (on-prem). |
| **Regional External Application LB** | Regional | 7 | Traffic must stay in one region (e.g., `compliance`), SSL offload. |
| **External TCP/SSL Proxy LB** | Global | 4 | **Non-HTTP(S)** traffic (e.g., IMAP, custom TCP). Terminates TCP/SSL. **Hides client IP**. |
| **External Passthrough Network LB** | Regional | 4 | High-performance, **preserves client IP**. Forwards traffic directly to VMs. |
| **Internal Application LB** | Regional | 7 | Internal microservices, web apps. Accessible from same VPC, peered VPCs, on-prem. |
| **Internal Passthrough Network LB** | Regional | 4 | Internal Layer 4 load balancing. **Preserves client IP**. Accessible from on-prem via VPN/Interconnect. |

---

## 3. Health Checks

*   **Purpose**: LBs only send traffic to backends that pass health checks.
*   **Common Failure Reasons**:
    *   **Firewall Rules**: Missing rule to allow health check IPs (`35.191.0.0/16`, `130.211.0.0/22`).
    *   **Application Startup Time**: App takes too long to start, causing initial checks to fail.
    *   **Protocol Mismatch**: Health check uses HTTP but the app listens on a raw TCP socket.
*   **Troubleshooting Tip**: If a Global LB sends traffic to only one region, it's almost always because the backends in the other region are **failing their health checks**.
*   **Fixing Startup Failures**: Increase the health check's `timeout` or `check-interval` to give the application more time to boot.

    ```bash
    gcloud compute health-checks update http my-health-check \
      --timeout=15s \
      --check-interval=20s \
      --unhealthy-threshold=5
    ```

---

## 4. Autoscaling

*   **Purpose**: Automatically add or remove VMs from a Managed Instance Group (MIG) based on load.
*   **Key Metrics**:
    *   Average CPU utilization (most common).
    *   Load balancing capacity.
    *   Cloud Monitoring metrics (e.g., Pub/Sub queue size).
*   **Cooldown Period**: The time the autoscaler waits after a scaling event before collecting metrics again.
    *   **Symptom**: "Scaling down too slowly, increasing costs."
    *   **Fix**: **Lower the cooldown period** to make the autoscaler more responsive.
*   **Startup Time**: Use **pre-baked custom images** instead of startup scripts to ensure VMs boot quickly and can serve traffic faster.

---

## 5. Key Exam Scenarios & Triggers

| If you need to... | Then use... | Because... |
| :--- | :--- | :--- |
| Load balance a **global public website** | **Global External Application LB** | It provides a single anycast IP and integrates with Cloud CDN. |
| Keep traffic **within a single region** for compliance | **Regional External Application LB** | It ensures both the LB and backends are in the same region. |
| Load balance internal traffic **across a VPN/Interconnect** | **Internal Passthrough Network LB** | It's designed for hybrid connectivity and preserves the client IP. |
| **Preserve the original client IP** for external traffic | **External Passthrough Network LB** | It's a passthrough LB that forwards packets directly without proxying. |
| Load balance traffic to **on-premise servers** | **Global External Application LB** + **Hybrid NEGs** | This is the only combination that supports non-GCP backends. |
| Troubleshoot why a **region isn't receiving traffic** | **Check Health Checks & Firewalls** | Unhealthy backends are the most common cause of traffic imbalance. |
| **Reduce costs** from slow scale-down events | **Lower the Autoscaler Cooldown Period** | A shorter cooldown allows the autoscaler to react faster to load decreases. |
