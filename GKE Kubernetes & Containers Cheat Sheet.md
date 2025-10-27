# 🎯 GCP Associate Cloud Engineer – Kubernetes & Containers Cheat Sheet
### *(Personalized for Weak Areas)*

---

## 📚 Table of Contents

1. [Namespaces & Resource Scope](#-1️⃣-namespaces--resource-scope)
2. [Node Maintenance & Scheduling](#-2️⃣-node-maintenance--scheduling)
3. [kubectl Debugging & Events](#-3️⃣-kubectl-debugging--events)
4. [Ephemeral Debugging Containers](#-4️⃣-ephemeral-debugging-containers)
5. [GKE Node Pools & Cluster Changes](#-5️⃣-gke-node-pools--cluster-changes)
6. [Network Policies & Pod Communication](#-6️⃣-network-policies--pod-communication)
7. [Metrics, Monitoring & Troubleshooting](#-7️⃣-metrics-monitoring--troubleshooting)
8. [“Which Tool Do I Use?” Quick Reference](#-8️⃣-which-tool-do-i-use--quick-reference)
9. [Pod Lifecycle Cheat Sheet](#-9️⃣-pod-lifecycle-cheat-sheet)
10. [Service & Exposure Summary](#-🔟-service--exposure-summary)
11. [Final Exam Mindset](#-final-exam-mindset)

---

## 🧩 1️⃣ Namespaces & Resource Scope

**Key Rule:**  
Deleting a namespace deletes *everything inside it* — but not global resources.

| Type | Deleted with Namespace? | Notes |
|------|--------------------------|-------|
| Pods | ✅ | Terminated immediately |
| Services | ✅ | Endpoints removed |
| Deployments | ✅ | Controllers removed |
| ConfigMaps / Secrets | ✅ | Lost unless backed up |
| PersistentVolumeClaims (PVCs) | ✅ | Volume detached but disk may remain |
| Persistent Disks (PDs) | ❌ | GCE resource; not namespaced |
| Nodes | ❌ | Cluster-level resources remain |

**Command Tip:**

```bash
kubectl get all -n <namespace>
```

---

## 🧰 2️⃣ Node Maintenance & Scheduling

Node Maintenance Commands

| Action | Command | Meaning |
|---|---|---|
| 🟡 Stop scheduling new Pods | `kubectl cordon <node>` | Safely mark node unschedulable |
| 🔴 Evict Pods + drain node | `kubectl drain <node>` | Prepare node for shutdown/upgrades |
| 🟢 Resume scheduling | `kubectl uncordon <node>` | Node back in rotation |

Taints vs Labels

| Concept | Used For | Example |
|---|---|---|
| Taints/Tolerations | Keep unwanted Pods away | `kubectl taint nodes key=value:NoSchedule` |
| Labels + nodeSelector | Direct Pods to specific nodes | Example below 👇 |

```yaml
nodeSelector:
	pool: standard
```

🧠 Mnemonic: “Taint keeps Pods away, label pulls Pods toward.”

---

## 🕵️‍♀️ 3️⃣ kubectl Debugging & Events

View Events

```bash
kubectl get events -n <namespace> --sort-by=.metadata.creationTimestamp
```

Common Event Issues

- ImagePullBackOff → Bad image name / IAM issue
- FailedScheduling → Node selector or taint conflict
- CrashLoopBackOff → App crash / bad entrypoint

Logs & Describe

```bash
kubectl logs <pod> [-c container-name]
kubectl describe pod <pod>
```

Logs show container output, describe shows lifecycle & events.

---

## 🧑‍🔬 4️⃣ Ephemeral Debugging Containers

Run a temporary debugging container in a Pod:

```bash
kubectl debug <pod> -it --image=busybox
```

✅ Perfect for diagnosing image issues or missing tools like curl or bash.

---

## ⚙️ 5️⃣ GKE Node Pools & Cluster Changes

Immutable Node Pools:
You cannot change a machine type in-place.

Correct Workflow:

```bash
# 1. Create new node pool
gcloud container node-pools create new-pool \
	--cluster=my-cluster --machine-type=e2-standard-4

# 2. Migrate workloads
kubectl cordon <old-node>
kubectl drain <old-node> --ignore-daemonsets

# 3. Delete old pool
gcloud container node-pools delete old-pool --cluster=my-cluster
```

---

## 📡 6️⃣ Network Policies & Pod Communication

When Network Policies are enabled, traffic is denied by default.

Allow frontend → backend communication:

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

## 📊 7️⃣ Metrics, Monitoring & Troubleshooting

| Task | Command | Notes |
|---|---|---|
| Pod CPU/memory usage | `kubectl top pods` | Requires metrics-server |
| Node usage | `kubectl top nodes` | Detects resource bottlenecks |
| View events | `kubectl get events -n <namespace>` | First step for issues |
| Resource details | `kubectl describe <resource>` | Shows status, conditions, and events |

---

## 🧠 8️⃣ “Which Tool Do I Use?” — Quick Reference

| Task | Use This |
|---|---|
| Manage cluster / node pools | `gcloud container ...` |
| Manage workloads inside cluster | `kubectl ...` |
| View rollouts / logs / metrics | `kubectl ...` |
| Manage IAM roles | `gcloud projects add-iam-policy-binding` |

---

## 💡 9️⃣ Pod Lifecycle Cheat Sheet

| State | Meaning | Typical Fix |
|---|---|---|
| Pending | No node fits / lacks resources | Check taints/selectors |
| ContainerCreating | Image pull or volume issue | Check image, registry auth, PVC status |
| CrashLoopBackOff | Container crashing repeatedly | Inspect logs, fix entrypoint/app |
| ImagePullBackOff | Registry auth or wrong image path | Fix image path or registry credentials / IAM |

---

## 🧩 🔟 Service & Exposure Summary

| Type | Access Scope | Use Case |
|---|---|---|
| ClusterIP | Internal only | Pod ↔ Pod |
| NodePort | Node’s IP:Port | Testing or dev access |
| LoadBalancer | Public IP (via GCP LB) | Production exposure |
| ExternalName | DNS alias | Link to external APIs |

---

## 🧭 Final Exam Mindset

For troubleshooting → start with `kubectl get events`, `kubectl describe`, `kubectl logs`, `kubectl top`.

For deployment changes → use `kubectl rollout restart` or `kubectl rollout undo`.

For infrastructure → use `gcloud`, not `kubectl`.

For connectivity issues → check Service selectors → Pod labels → NetworkPolicies.