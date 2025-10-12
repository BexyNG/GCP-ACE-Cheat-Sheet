# 🧠 GKE Core Concepts — Simplified Guide

---

## 📑 Table of Contents

- [Cluster](#️-cluster)
- [Nodes](#-nodes)
- [Pods](#-pods)
- [Images](#-images)
- [Namespaces](#️-namespaces)
- [Mental Model](#-mental-model-visual)
- [Quick Recap](#-quick-recap)
- [Final Tip](#-final-tip)

---

## ☁️ Cluster

**What it is**

A **Cluster** is the entire Kubernetes system managed by GKE. Think of it as a restaurant that contains kitchens (nodes), dishes (pods), and sections (namespaces).

**In GKE**

- Google manages the control plane.
- You manage the worker nodes (VMs) that run your containers.

**Commands**

```bash
# list all clusters
gcloud container clusters list

# connect kubectl to a cluster
gcloud container clusters get-credentials my-cluster --region us-central1

# verify you're connected
kubectl get nodes
```

---

## 🧱 Nodes

Nodes are Compute Engine VMs that run your workloads (pods). Nodes are grouped into node pools (sets of similar VMs).

**Commands**

```bash
# list nodes in a cluster
kubectl get nodes -o wide

# cordon (stop scheduling new pods)
kubectl cordon <node>

# drain (evict pods for maintenance)
kubectl drain <node> --ignore-daemonsets
```

---

## 🍱 Pods

Pods are the smallest runnable units in Kubernetes — one or more containers that run together. Containers in a pod share network and storage. Pods are usually managed by higher-level controllers (Deployments, StatefulSets).

**Commands**

```bash
kubectl get pods -o wide          # show pods and their nodes
kubectl logs <pod-name>           # view application logs
kubectl describe pod <pod-name>   # detailed events & status
```

---

## 📦 Images

Images are templates for containers (code + runtime + dependencies). Store images in Artifact Registry (GCR for older projects).

**Commands**

```bash
# build image locally
docker build -t us-central1-docker.pkg.dev/PROJECT/REPO/myapp:1.0 .

# push to Artifact Registry
gcloud auth configure-docker us-central1-docker.pkg.dev
docker push us-central1-docker.pkg.dev/PROJECT/REPO/myapp:1.0
```

GKE nodes need the IAM role `roles/artifactregistry.reader` so they can pull images.

---

## 🗂️ Namespaces

Namespaces divide a cluster into logical areas (dev/test/prod). They hold pods, services, and configuration resources.

**Commands**

```bash
kubectl get ns                 # list namespaces
kubectl create ns dev          # create a new namespace
kubectl get all -n dev         # list resources in that namespace
kubectl delete ns dev          # deletes everything inside 'dev'
```

**Rule of thumb:** deleting a namespace deletes everything inside it, but not cluster-scoped resources (nodes, disks).

---

## 🧩 Mental Model (Visual)

```
[ Cluster 🍽️ ]  --> the entire restaurant
 ├─ [ Node 🍳 ]  --> kitchens (VMs) where cooking happens
 │     └─ [ Pod 🍱 ]  --> lunchboxes holding containers
 │           └─ [ Container 🥘 ]  --> running app, built from an image
 │                 └─ [ Image 📦 ]  --> recipe stored in Artifact Registry
 └─ [ Namespace 🗂️ ]  --> shelves organizing everything (dev, test, prod)
```

---

## 🚀 Quick Recap

| Concept | What it is | Analogy | Created / Managed by | Example command |
|---------|-----------|---------|---------------------|-----------------|
| Cluster | Entire Kubernetes system | Restaurant 🍽️ | GKE (control plane) | `gcloud container clusters list` |
| Node | VM that runs workloads | Kitchen 🍳 | Node pool / GCE | `kubectl get nodes` |
| Pod | Unit that runs containers | Lunchbox 🍱 | Deployment / ReplicaSet | `kubectl get pods` |
| Image | Template for container | Recipe 📦 | Docker / Artifact Registry | `docker push` |
| Namespace | Logical grouping | Shelf 🗂️ | Kubernetes | `kubectl get ns` |

---

## 🧠 Final Tip

Think in layers: **Image → Pod → Node → Cluster**. Namespaces wrap groups of resources. GKE manages the control plane so you focus on workloads.