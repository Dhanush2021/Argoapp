🚀 Kubernetes GitOps Setup with Argo CD, KEDA, Helm, and Kyverno
This repository provides a complete GitOps-based Kubernetes setup using:

Argo CD for continuous delivery

Helm for application packaging

KEDA for event-driven autoscaling

Kyverno for policy enforcement

📁 Project Structure
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

🎯 Objectives
🔧 Install and manage KEDA via Helm using Argo CD

🚀 Deploy an application that autoscales based on external metrics (e.g., Kafka)

🔐 Enforce best practices using Kyverno policies

⚙️ Manage all of this via GitOps using Argo CD and this repository

🛠️ Prerequisites
Before starting, ensure the following:

A working Kubernetes cluster (GKE, EKS, AKS, or Minikube/KIND)

Argo CD is installed in the cluster

kubectl is configured to talk to your cluster

(Optional) helm CLI for local testing or dry-run

(Optional but recommended) Kyverno installed

🚀 Setup Instructions
1️⃣ Clone the Repository
bash
Copy
Edit
git clone https://github.com/your-org/k8s-infra.git
cd k8s-infra
2️⃣ Bootstrap Argo CD with Parent Application
This parent app manages both KEDA and your autoscaler app:

bash
Copy
Edit
kubectl apply -f argocd/parent-app.yaml
✅ This will automatically:

Deploy KEDA into the keda namespace via Helm

Deploy your custom autoscaling application to the autoscaler namespace

3️⃣ Customize Your Helm Values
Edit the file charts/keda-autoscaler/values.yaml to provide your app config:

yaml
Copy
Edit
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
4️⃣ (Optional) Apply Kyverno Policies
To validate security and enforce best practices:

bash
Copy
Edit
kubectl apply -f kyverno-policies/
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

🔄 Testing Autoscaling (Example: Kafka)
Send messages to the Kafka topic configured in your values.yaml.

KEDA will detect lag and trigger horizontal scaling.

Run:

bash
Copy
Edit
kubectl get hpa -n autoscaler -w
You should see your deployment scale up/down based on Kafka metrics.

🧠 How the App of Apps Pattern Works
argocd/parent-app.yaml is the main entry point

It tracks all child Argo CD apps inside argocd/, including:

keda-install.yaml — Installs KEDA via Helm

keda-autoscaler.yaml — Installs your Helm-based workload

This ensures declarative, version-controlled, and repeatable deployment.

📚 Resources
KEDA Documentation

Helm Charts Guide

Argo CD Docs

Kyverno Policy Library
