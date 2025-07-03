# 🚀 Kubernetes GitOps Setup with KEDA, Helm, Argo CD & Kyverno

This repository contains a complete Kubernetes GitOps setup using:

- [Argo CD](https://argo-cd.readthedocs.io/) for continuous delivery
- [Helm](https://helm.sh/) for packaging Kubernetes applications
- [KEDA](https://keda.sh/) for event-driven autoscaling
- [Kyverno](https://kyverno.io/) for enforcing security policies

---

## 📦 Project Structure

k8s-infra/
├── argocd/ # Argo CD Applications (App of Apps structure)
│ ├── parent-app.yaml # Parent ArgoCD app to manage the whole stack
│ ├── keda-install.yaml # Installs KEDA via Helm chart
│ └── keda-autoscaler.yaml # Deploys your autoscaled app via Helm chart
│
├── charts/ # Your custom Helm chart for the application
│ └── keda-autoscaler/
│ ├── Chart.yaml
│ ├── values.yaml
│ └── templates/
│ ├── deployment.yaml
│ ├── service.yaml
│ └── scaledobject.yaml
│
├── kyverno-policies/ # Kyverno ClusterPolicies
│ ├── validate-resource-requests-limits.yaml
│ └── restrict-serviceaccount-access.yaml
│
└── README.md # Documentation (this file)


---

## 🎯 Objectives

This setup will:
- Deploy [KEDA](https://keda.sh/) into the cluster using Argo CD + Helm
- Deploy your app with `Deployment`, `Service`, and a `ScaledObject` for autoscaling
- Automatically scale your app based on external event sources like Kafka
- Enforce best-practice policies via [Kyverno](https://kyverno.io/)
- Be fully managed via GitOps using Argo CD

---

## 🛠️ Prerequisites

Make sure the following tools are installed:

- `kubectl` (v1.20+)
- A Kubernetes cluster (EKS, GKE, AKS, or local like Minikube/KIND)
- [Helm v3+](https://helm.sh/docs/intro/install/)
- [Argo CD installed](https://argo-cd.readthedocs.io/en/stable/getting_started/)
- (Optional but recommended) [Kyverno installed](https://kyverno.io/docs/installation/)

---

## 🚀 How It Works

1. The `parent-app.yaml` is the top-level Argo CD application.
2. It points to the `argocd/` folder which contains:
   - `keda-install.yaml` – Installs the KEDA Helm chart
   - `keda-autoscaler.yaml` – Installs your custom Helm-based workload
3. Argo CD ensures both apps are always in sync with this Git repo.
4. Your app includes a KEDA `ScaledObject` that defines how/when it scales (e.g., based on Kafka lag).
5. Kyverno policies enforce secure and validated configurations across the cluster.

---

## 🔄 Setup Instructions

### 1. Clone This Repo

bash

git clone https://github.com/your-org/k8s-infra.git

cd k8s-infra

Add Argo CD Parent Application

kubectl apply -f argocd/parent-app.yaml

Argo CD will now automatically install:

1 KEDA into the keda namespace

2 Your app into the autoscaler namespace

Customize Helm Values
Edit charts/keda-autoscaler/values.yaml to set

Optional) Apply Kyverno Policies

kubectl apply -f kyverno-policies/

These policies enforce:

  * Required resources.requests and limits

  *  Restriction on using the default service account


