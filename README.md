# Quantity Measurement App - GitOps Infrastructure

Welcome to the **Infrastructure as Code (IaC)** repository for the Quantity Measurement Application. 

This repository serves as the single source of truth for the Kubernetes deployment architecture. It completely decouples our infrastructure blueprints from our application source code, adhering to modern Enterprise GitOps best practices.

## 🏗️ Architecture Overview

The application is deployed to an **Amazon EKS (Elastic Kubernetes Service)** cluster and consists of three core microservices:

1. **Frontend (React)**: Exposed via a public AWS Network LoadBalancer.
2. **Backend (Spring Boot)**: A stateless API hosted securely within the cluster on a `ClusterIP` service.
3. **Database (MySQL)**: A stateful database container providing persistence for the Spring Boot application.

## 📂 Repository Structure

```text
├── k8s/
│   └── qma-k8s.yaml      # The master Kubernetes deployment manifest
└── README.md             # This documentation file
```

## 🚀 CI/CD Pipeline Integration

Our CI/CD pipeline (hosted on Jenkins) interacts with three distinct repositories during a deployment:

1. [Frontend Repository](https://github.com/ahkharsha/QuantityMeasurementApp-React)
2. [Backend Repository](https://github.com/ahkharsha/QuantityMeasurementApp-Springboot)
3. **Infrastructure Repository** (This Repo)

### The Deployment Flow:
1. Jenkins pulls the source code from the Frontend and Backend repositories.
2. Jenkins builds the Docker images and pushes them securely to AWS Elastic Container Registry (ECR).
3. Jenkins pulls the deployment blueprints (`qma-k8s.yaml`) from **this Infrastructure repository**.
4. Jenkins applies the blueprints to the Amazon EKS cluster using `kubectl`.
5. Kubernetes performs a Zero-Downtime Rolling Update to swap out the old pods for the newly built images.

## 🛠️ Modifying the Infrastructure

To make changes to the live infrastructure (e.g., scaling the number of frontend replicas or adding new environment variables), simply edit the `k8s/qma-k8s.yaml` file in this repository and commit your changes. 

Because we follow Infrastructure as Code (IaC) principles, **all manual `kubectl edit` commands are strictly forbidden on the live server.** The live cluster must always mirror the exact state of this repository.
