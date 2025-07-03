# 🚀 Kubernetes GitOps Setup with Argo CD, KEDA, Helm, and Kyverno

This repository provides a complete GitOps-based Kubernetes setup using:

- [Argo CD](https://argo-cd.readthedocs.io/) for continuous delivery  
- [Helm](https://helm.sh/) for application packaging  
- [KEDA](https://keda.sh/) for event-driven autoscaling  
- [Kyverno](https://kyverno.io/) for policy enforcement  

---

## 📁 Project Structure

k8s-infra/
├── argocd/ # Argo CD Applications (App of Apps structure)
│ ├── parent-app.yaml # Parent Argo CD app to manage the whole stack
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

- 🔧 Install and manage [KEDA](https://keda.sh/) via Helm using Argo CD  
- 🚀 Deploy an application that autoscales based on external metrics (e.g., Kafka)  
- 🔐 Enforce best practices using [Kyverno](https://kyverno.io/) policies  
- ⚙️ Manage all of this via GitOps using Argo CD and this repository  

---

## 🛠️ Prerequisites

Before starting, ensure the following:

- A working Kubernetes cluster (GKE, EKS, AKS, or Minikube/KIND)  
- Argo CD is installed in the cluster  
- `kubectl` is configured to talk to your cluster  
- (Optional) `helm` CLI for local testing or dry-run  
- (Optional but recommended) [Kyverno](https://kyverno.io/docs/installation/) installed  

---

## 🚀 Setup Instructions

## 1️⃣ Clone the Repository

```bash
git clone https://github.com/your-org/k8s-infra.git
cd k8s-infra
```

2️⃣ Bootstrap Argo CD with Parent Application
This parent app manages both KEDA and your autoscaler app:
```bash
kubectl apply -f argocd/parent-app.yaml
```

✅ This will automatically:

Deploy KEDA into the keda namespace via Helm

Deploy your custom autoscaling application to the autoscaler namespace

3️⃣ Customize Your Helm Values
Edit the file charts/keda-autoscaler/values.yaml to provide your app config:
```yaml

image:
  repository: your-dockerhub-user/your-app
  tag: latest

resources:
  requests:
    cpu: "250m"
    memory: "256Mi"
  limits:
    cpu: "500m"
    memory: "512Mi"

keda:
  trigger:
    type: kafka
    metadata:
      bootstrapServers: my-kafka.kafka.svc:9092
      topic: my-topic
      consumerGroup: my-group
      lagThreshold: "10"
```
4️⃣ (Optional) Apply Kyverno Policies
To validate security and enforce best practices:

```bash
kubectl apply -f kyverno-policies/

```

Kyverno policies include:

Requiring resource requests and limits on pods

Restricting usage of the default service account

🔍 Verifying the Setup

Task	Command
View Argo CD apps	kubectl get applications -n argocd
Check KEDA deployment	kubectl get pods -n keda
View your app pods	kubectl get pods -n autoscaler
View HPA created by KEDA	kubectl get hpa -n autoscaler
View KEDA CRDs	kubectl get scaledobject

