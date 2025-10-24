
# 🔍 Interconnecting Networks — GCP ACE Exam Study Guide

## Table of Contents

- [Overview & When to Use Each Option](#overview--when-to-use-each-option)
- [VPNs on GCP: Classic vs HA vs Cloud VPN Details](#vpns-on-gcp-classic-vs-ha-vs-cloud-vpn-details)
- [Interconnect: Dedicated & Partner](#interconnect-dedicated--partner)
- [Peering Options: VPC Peering, Shared VPC, Direct & Carrier Peering](#peering-options-vpc-peering-shared-vpc-direct--carrier-peering)
- [Routing: Static, Dynamic (BGP) & Cloud Router](#routing-static-dynamic-bgp--cloud-router)
- [Private Google Access & Private Service Connect (PSC)](#private-google-access--private-service-connect-psc)
- [IPv6, MTU, Performance & Limits](#ipv6-mtu-performance--limits)
- [Troubleshooting & Exam Tips](#troubleshooting--exam-tips)
- [Common CLI Examples](#common-cli-examples)
- [Quick Comparison Table & Mnemonics](#quick-comparison-table--mnemonics)

---

## Overview & When to Use Each Option

GCP offers multiple ways to connect networks (on-premises, other clouds, or other GCP projects). Choosing the right option depends on bandwidth, SLA needs, encryption, management complexity, and whether you need private access to Google APIs.

- Cloud VPN (HA or classic) — encrypted IPsec tunnels over the public internet. Good for low-to-moderate bandwidth and quick setup.
- Dedicated Interconnect / Partner Interconnect — private, high-bandwidth connections with guaranteed SLAs; use for production, low-latency, high-throughput workloads.
- VPC Peering / Shared VPC — connect GCP projects/networks internally (no egress across internet). Use for GCP-to-GCP connectivity.
- Direct & Carrier Peering — peer with Google at edge locations for public Google services and some APIs; carrier peering also supports IPv6 and Workspace APIs in some cases.

Use-case examples:

- Hybrid infra with moderate bandwidth: HA VPN + Cloud Router (BGP).
- Large-scale data transfer between on-prem and GCP: Dedicated Interconnect or Partner Interconnect.
- Cross-project microservices within same org: Shared VPC (centralized IAM + billing).

---

## VPNs on GCP: Classic vs HA vs Cloud VPN Details

### Key distinctions

- Classic VPN (legacy): older product, limited features, effectively deprecated for new designs. No SLA and fewer capabilities.
- HA VPN (recommended for production): provides high-availability using two redundant tunnels across two interfaces. Supports dynamic routing with Cloud Router (BGP). Has SLA options and better throughput.
- Cloud VPN (policy vs route-based): GCP uses both policy- and route-based tunnels depending on configuration; HA VPN uses route-based (recommended). Policy-based may be used for specific interoperability with legacy on-prem devices.

### Capabilities and limits

- Encryption: IPsec (ESP). IKEv1 and IKEv2 support depending on configuration; HA VPN commonly uses IKEv2.
- Redundancy: HA VPN uses two tunnels that terminate in different Google edge points — if one fails, traffic fails over to the other.
- Scaling & Throughput: HA VPN supports higher throughput and can be paired with multiple tunnels per VPN gateway; Dedicated Interconnect offers far greater throughput.

### When to pick HA VPN vs Interconnect

- HA VPN: when you need secure connectivity quickly, moderate throughput, and encrypted transit over the internet. Good for dev/test or smaller production workloads.
- Dedicated/Partner Interconnect: when you need predictable, high-throughput, low-latency connectivity with SLAs and dedicated bandwidth.

---

## Interconnect: Dedicated & Partner

### Dedicated Interconnect

- Provides a physical connection between your on-prem network and Google's network at a colocation facility.
- High bandwidth (10 Gbps and 100 Gbps options) and strong SLA.
- Requires a VLAN attachment (VLAN attachment = interconnect attachment) and configuration of partner/router.

Pros: predictable performance, low latency, suitable for data-intensive workloads. Cons: higher setup time and cost, requires colo.

### Partner Interconnect

- For customers who cannot colocate directly. A service provider (partner) provides the connection to Google.
- Bandwidth options start lower (e.g., 50 Mbps) up to multiple Gbps depending on the partner.

Pros: faster onboarding and flexible bandwidth; Cons: depends on partner for SLAs and provisioning.

---

## Peering Options: VPC Peering, Shared VPC, Direct & Carrier Peering

### VPC Peering

- Connects two VPC networks so workloads can communicate privately using internal RFC 1918 IPs.
- No transitive peering: A–B and B–C does NOT allow A–C.
- Global peering (peering across regions) is supported for some network types; check the docs for exact limitations.
- Custom routes must be explicitly exported/imported if sharing custom routes is needed.

### Shared VPC

- A host project shares subnets with service projects. Useful for centralizing network administration, security, and billing.
- IAM is centralized; service projects use host project subnets.

### Direct Peering & Carrier Peering

- Direct Peering: you peer with Google at an edge location to reach Google public services. No SLA from Google for direct peering.
- Carrier Peering: you peer via a carrier; may expose additional APIs (Workspace) and often supports IPv6.

Security note: Peering does not provide encryption — use VPN/Interconnect for encrypted paths if needed.

---

## Routing: Static, Dynamic (BGP) & Cloud Router

### Static routing

- Configure explicit routes on both ends. Simpler but does not adapt automatically to path changes.

### Dynamic routing with BGP (recommended for production VPN/Interconnect)

- Cloud Router handles BGP sessions and exchanges routes between GCP and your on-prem router.
- Benefits: automatic route propagation, multi-path failover, simplified management.

Important behaviors:

- If BGP session is lost, routes advertised by the peer are withdrawn; traffic will not flow until re-advertised or a static route exists.
- Advertised prefixes must adhere to size and prefix limits; Cloud Router limits apply.

---

## Private Google Access & Private Service Connect (PSC)

- Private Google Access: allows VM instances without external IP addresses to reach Google APIs via internal IP (no internet egress). Useful for locking down workloads.
- Private Service Connect (PSC): provides private endpoints for SaaS and Google-managed services; you can consume a service privately via an internal IP.

Exam nuance: Private Google Access is for Google APIs only; PSC is for private connectivity to services (including third-party SaaS) inside Google's network.

---

## IPv6, MTU, Performance & Limits

- IPv6: Carrier Peering supports IPv6. Most other connectivity options (Cloud VPN, Interconnect) are IPv4-centric; check the latest GCP docs for incremental IPv6 support.
- MTU: Typical IPsec overhead reduces MTU (fragmentation risk). For VPNs, lower MTU (e.g., 1460) may be recommended when experiencing fragmentation.
- Performance: Use Interconnect for high throughput; HA VPN performance varies based on tunnel count and encryption overhead.

---

## Troubleshooting & Exam Tips

- Health checks: For hybrid apps, ensure firewall rules allow health checks and BGP/peer IPs.
- Common exam trap: “VPC Peering is transitive” — false. Peering is not transitive.
- If a region is not receiving traffic from a global LB, first check health checks and routing.
- BGP lost routes: expect route withdrawal; check Cloud Router logs and BGP status.
- For connectivity failures: verify shared VPC configuration, IAM permissions, and firewall rules (ingress/egress on relevant ports).

Quick checklist when answering exam questions:

1. Is encryption required? If yes → VPN or application-level encryption.
2. Required bandwidth & SLA? If high → Dedicated Interconnect.
3. Is it cross-project only? → VPC Peering or Shared VPC.
4. Need access to Google APIs from private VMs? → Private Google Access.

---

## Common CLI Examples

Create an HA VPN gateway (example):

```bash
# Create Cloud Router
gcloud compute routers create my-router --network=default --region=us-central1

# Create HA VPN gateway and tunnels (simplified)
gcloud compute vpn-gateways create my-ha-vpn --network=default --region=us-central1

# Create BGP peer on Cloud Router
gcloud compute routers add-bgp-peer my-router --peer-name=onprem-peer --peer-asn=65001 --interface=my-interface --region=us-central1 --peer-ip-address=169.254.1.2 --advertised-route-priority=100
```

VPC peering example:

```bash
gcloud compute networks peerings create peer-to-other-vpc --network=my-vpc --peer-network=projects/OTHER_PROJECT/global/networks/other-vpc --auto-create-routes
```

Private Service Connect (consumer) example (conceptual):

```bash
gcloud compute forwarding-rules create psc-forward --network=default --address=10.0.0.10 --target-service-attachment=projects/PROVIDER_PROJECT/regions/us-central1/serviceAttachments/sa-1
```

---

## Quick Comparison Table & Mnemonics

| Option | Encryption | SLA | Throughput | Typical Use |
|---|---:|---:|---:|---|
| Classic VPN | Yes (IPsec) | No | Low | Legacy / quick test setups |
| HA VPN | Yes (IPsec) | Yes (when configured) | Moderate | Production VPN over internet |
| Partner Interconnect | N/A (private) | Yes | Medium–High | Fast on-ramp via partner |
| Dedicated Interconnect | N/A (private) | Yes | High (10/100 Gbps) | Large data transfer, low latency |
| VPC Peering | No (no encryption) | N/A | N/A (internal) | GCP–GCP private connectivity |

Mnemonic: "VPN for privacy, Interconnect for performance, Peering for internal talk."

---

