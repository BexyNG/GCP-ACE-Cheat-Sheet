# 🔍 Weak Areas Identified

## Table of Contents

- [HA VPN vs Classic VPN vs Interconnect](#ha-vpn-vs-classic-vpn-vs-interconnect)
- [VPC Peering vs Shared VPC vs Cloud VPN](#vpc-peering-vs-shared-vpc-vs-cloud-vpn)
- [Google API Access Options](#google-api-access-options)
- [IPv6 Support](#ipv6-support)
- [Routing Behavior](#routing-behavior)
- [Cheat Sheet (1-Pager)](#-cheat-sheet-1-pager)

### HA VPN vs Classic VPN vs Interconnect

* **HA VPN** = SLA + encryption, dual tunnels, supports BGP.
* **Classic VPN** = deprecated, no SLA.
* **Interconnect (Dedicated/Partner)** = SLA, no encryption.
* **Carrier/Direct Peering** = no SLA.

### VPC Peering vs Shared VPC vs Cloud VPN

* **Shared VPC** = centralized IAM + billing across projects.
* **VPC Peering** = network connectivity only (no IAM, no billing).
* **Cloud VPN** = only way to connect VPCs across orgs.

### Google API Access Options

* **Private Google Access** = internal VM IPs → Cloud APIs only.
* **Carrier Peering** = Cloud APIs + Google Workspace APIs (Gmail, Drive, etc.).
* **Private Service Connect (PSC)** = private access to SaaS providers + Google APIs.

### IPv6 Support

* **Carrier Peering** supports IPv6.
* **Cloud VPN (Classic/HA)** does **not** support IPv6.

### Routing Behavior

* Custom routes in VPC Peering must be **explicitly shared**.
* BGP session loss = routes are **withdrawn** even if VPN tunnel stays up.

---

# 📄 🚀 Cheat Sheet (1-Pager)

### 🔑 Connectivity Options

* **Classic VPN** → Deprecated, no SLA.
* **HA VPN** → SLA + encryption, dual tunnels, supports BGP.
* **Cloud VPN (policy-based)** → For overlapping IPs.
* **Dedicated Interconnect** → SLA, private, requires colocation (10/100 Gbps).
* **Partner Interconnect** → SLA, via ISP partner, flexible bandwidth (50 Mbps – 10 Gbps).
* **Direct Peering** → Public Google services, no SLA, direct at Google edge.
* **Carrier Peering** → Public Google + Workspace APIs, SLA from ISP, supports IPv6.

### 🔑 VPC Connectivity

* **VPC Peering** → Global, no overlapping CIDRs, no transitive routing, can share custom routes (explicitly).
* **Shared VPC** → Centralized IAM + billing across projects.
* **Cloud VPN** → Required for **cross-org connectivity**.

### 🔑 API Access

* **Private Google Access** → Internal VMs → Google Cloud APIs only.
* **Carrier Peering** → Google Cloud + Workspace APIs, via ISP.
* **Private Service Connect (PSC)** → Private access to SaaS providers + Google APIs.

### 🔑 Routing

* **Cloud Router + BGP** = Dynamic routing (on-prem ↔ GCP).
* **Global routing mode** = Dynamic routes across regions.
* **BGP drop** = routes withdrawn.
* **Custom routes in peering** = must be explicitly shared.
