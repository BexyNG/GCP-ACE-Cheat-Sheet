# 🎯 GCP Associate Cloud Engineer — Kubernetes & Containers Cheat Sheet

*(Personalized for weak areas; quick reference for the GCP ACE exam)*

---

## Table of contents

1. [Namespaces & Resource Scope](#namespaces--resource-scope)
2. [Node maintenance & scheduling](#node-maintenance--scheduling)
3. [kubectl debugging & events](#kubectl-debugging--events)
4. [Ephemeral debugging containers](#ephemeral-debugging-containers)
5. [GKE node pools & cluster changes](#gke-node-pools--cluster-changes)
6. [Network policies & pod communication](#network-policies--pod-communication)
7. [Metrics, monitoring & troubleshooting](#metrics-monitoring--troubleshooting)
8. [Which tool do I use? (quick reference)](#which-tool-do-i-use-quick-reference)
9. [Pod lifecycle cheat sheet](#pod-lifecycle-cheat-sheet)
10. [Service & exposure summary](#service--exposure-summary)
11. [Final exam mindset](#final-exam-mindset)

---

## Namespaces & Resource Scope

**Key rule:** deleting a namespace deletes everything inside it (pods, services, deployments, ConfigMaps, Secrets, PVCs), but not cluster-scoped resources (Persistent Disks, Nodes, etc.).

| Resource | Deleted with namespace? | Notes |
|---|---:|---|
| Pods | ✅ | Terminated immediately |
| Services | ✅ | Endpoints removed |
| Deployments | ✅ | Controllers removed |
| ConfigMaps / Secrets | ✅ | Lost unless backed up |
| PersistentVolumeClaims (PVCs) | ✅ | PVC removed; underlying disk may remain depending on reclaim policy |
| Persistent Disks (PDs) | ❌ | GCE resource; not namespaced |
| Nodes | ❌ | Cluster-level resources remain |

**Command tip**

```bash
kubectl get all -n <namespace>
```

---

## Node maintenance & scheduling

Node maintenance workflow and commands.

| Action | Command | Meaning |
|---|---|---|
| Stop scheduling new pods | `kubectl cordon <node>` | Mark node Unschedulable |
| Evict pods and drain node | `kubectl drain <node>` | Prepare node for reboot/upgrade (use `--ignore-daemonsets`) |
| Resume scheduling | `kubectl uncordon <node>` | Node back in rotation |

Taints vs labels

- Taints/Tolerations: keep unwanted pods away (example: `kubectl taint nodes key=value:NoSchedule`).
- Labels + `nodeSelector` / `nodeAffinity`: direct pods to specific nodes.

Example nodeSelector snippet:

```yaml
nodeSelector:
  pool: standard
```

Mnemonic: "Taint keeps pods away, label pulls pods toward."

---

## kubectl debugging & events

View recent events (sorted oldest→newest by timestamp):

```bash
kubectl get events -n <namespace> --sort-by=.metadata.creationTimestamp
```

Common event issues

- ImagePullBackOff → bad image name, registry auth, or IAM issue
- FailedScheduling → no matching node, taint/selector conflict
- CrashLoopBackOff → application crash or bad entrypoint

Useful commands

```bash
kubectl logs <pod> [-c container-name]
kubectl describe pod <pod>
```

Logs show container stdout/stderr; `describe` shows lifecycle, conditions and events.

---

## Ephemeral debugging containers

Run a temporary debugging container attached to a running pod:

```bash
kubectl debug <pod> -it --image=busybox -- /bin/sh
```

Great for troubleshooting missing tools (curl, bash) or testing network connectivity from inside the pod.

---

## GKE node pools & cluster changes

Node pools are effectively immutable for machine-type changes. Recommended workflow:

1. Create a new node pool with the desired machine type:

```bash
gcloud container node-pools create new-pool \
  --cluster=my-cluster --machine-type=e2-standard-4
```

2. Cordon and drain old nodes, migrate workloads:

```bash
kubectl cordon <old-node>
kubectl drain <old-node> --ignore-daemonsets
```

3. Delete the old node pool:

```bash
gcloud container node-pools delete old-pool --cluster=my-cluster
```

---

## Network policies & pod communication

When Calico/network policies are enabled, traffic is denied by default. Use NetworkPolicy resources to allow specific traffic.

Allow all traffic from pods in `frontend` namespace to pods in `backend` namespace:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-backend
  namespace: backend
spec:
  podSelector: {}
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: frontend
```

---

## Metrics, monitoring & troubleshooting

| Task | Command | Notes |
|---|---|---|
| Pod CPU/memory usage | `kubectl top pods` | Requires metrics-server or GKE metrics enabled |
| Node usage | `kubectl top nodes` | Check resource pressure |
| View events | `kubectl get events -n <namespace>` | First step in troubleshooting |
| Resource details | `kubectl describe <resource>` | Shows conditions and events |

---

## Which tool do I use? (quick reference)

| Task | Tool |
|---|---|
| Manage cluster / node pools | `gcloud container` (or GKE console) |
| Manage workloads inside cluster | `kubectl` |
| View rollouts / logs / metrics | `kubectl` |
| Manage IAM roles / project-level resources | `gcloud projects add-iam-policy-binding` |

---

## Pod lifecycle cheat sheet

| State | Meaning | Typical fix |
|---|---|---|
| Pending | No node fits / lacks resources | Check taints, selectors, resource requests |
| ContainerCreating | Image pull or volume mount issue | Check image, registry auth, PVC status |
| CrashLoopBackOff | Container crashing repeatedly | Inspect logs, fix entrypoint/app |
| ImagePullBackOff | Registry auth or wrong image path | Fix image path or registry credentials / IAM |

---

## Service & exposure summary

| Type | Access scope | Use case |
|---|---|---|
| ClusterIP | Internal only | Pod-to-pod communication |
| NodePort | Node's IP:Port | Simple test or dev access |
| LoadBalancer | Public IP via cloud LB | Production exposure |
| ExternalName | DNS alias | Redirect to external service |

---

## Final exam mindset

- For troubleshooting start with: `kubectl get events`, `kubectl describe`, `kubectl logs`, `kubectl top`.
- For deployment changes use: `kubectl rollout restart` / `kubectl rollout undo`.
- For infrastructure changes use `gcloud` (node pools, networking, IAM).
- For connectivity issues check: Service selectors → Pod labels → NetworkPolicies.

Focus: node maintenance (cordon/drain), events & debugging, network policies, namespace behavior, and node pool updates.

---

If you want, I can add a printable PDF export, internal anchor links from a top-level index, or convert this into slides for quick review.
