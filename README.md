# DevOps EKS Platform (Production-Style CI/CD on AWS)

## Overview

This project demonstrates a complete end-to-end DevOps platform built on AWS using Infrastructure as Code, Kubernetes, and GitHub Actions.

The system provisions production-ready infrastructure with Terraform, builds and scans container images, and deploys versioned applications to Amazon EKS using Helm and a multi-stage CI/CD pipeline.

This project focuses on:

- Reproducible infrastructure
- Secure authentication (OIDC, no static AWS keys)
- Deterministic deployments (commit-based image tagging)
- Environment separation
- Production-oriented Kubernetes configuration

---

## Architecture

### Infrastructure Layer (Terraform)

- Custom VPC (10.0.0.0/16)
- Public and Private subnets across two AZs
- NAT Gateways for outbound internet access from private subnets
- Amazon EKS cluster
- Managed node group
- Amazon ECR repository (scan on push enabled)
- Remote Terraform backend (S3 + DynamoDB locking)

### Application Layer

- Dockerized Nginx application
- Helm chart for Kubernetes deployment
- Resource limits and requests
- Liveness and readiness probes
- Horizontal Pod Autoscaler (HPA)

### CI/CD Layer (GitHub Actions)

Developer Push → GitHub → Pipeline executes:

1. Build Docker image
2. Tag image using Git commit SHA
3. Push image to Amazon ECR
4. Scan image for vulnerabilities (Trivy)
5. Deploy using Helm upgrade
6. Authenticate to AWS using OIDC (no stored AWS credentials)

---

## Why These Design Decisions?

### Private Subnets for Worker Nodes
Nodes run in private subnets for security. They access the internet through NAT gateways but are not directly exposed.

### Commit SHA Image Tagging
Using commit SHA ensures:
- Traceable deployments
- Deterministic rollbacks
- No ambiguity from `latest` tag usage

### OIDC Instead of Access Keys
GitHub Actions assumes an IAM role via OIDC.
This eliminates long-lived AWS credentials and follows least-privilege security principles.

### Remote Terraform Backend
State is stored in S3 with DynamoDB locking to:
- Prevent concurrent state corruption
- Enable collaborative infrastructure management

### Helm Instead of kubectl apply
Helm enables:
- Versioned releases
- Easy rollbacks
- Parameterized deployments
- Consistent upgrade strategy

---

## Project Structure
```text
.
├── terraform/ # Infrastructure as Code
├── app/ # Application source and Dockerfile
├── helm/ # Helm chart for Kubernetes deployment
├── .github/workflows # CI/CD pipeline
├── README.md
└── .gitignore '''

---

## Environments

Kubernetes namespaces are used for environment isolation:

- dev
- staging
- production

Each deployment uses versioned container images and Helm upgrades for controlled rollout.

---

## CI/CD Stages

### Build
Builds Docker image and tags using commit SHA.

### Scan
Performs vulnerability scanning using Trivy. Pipeline fails on high/critical vulnerabilities.

### Deploy
Authenticates via OIDC, updates kubeconfig, and performs Helm upgrade in target namespace.

---

## Scaling Strategy

- Horizontal Pod Autoscaler adjusts replicas based on CPU utilization.
- Rolling updates ensure zero downtime deployments.
- Resource requests and limits prevent noisy neighbor issues.

---

## Security Practices Implemented

- No static AWS credentials
- IAM least privilege roles
- ECR scan on push
- Trivy image scanning in pipeline
- Private worker nodes
- Terraform state locking

---

## How to Provision Infrastructure

---

## Environments

Kubernetes namespaces are used for environment isolation:

- dev
- staging
- production

Each deployment uses versioned container images and Helm upgrades for controlled rollout.

---

## CI/CD Stages

### Build
Builds Docker image and tags using commit SHA.

### Scan
Performs vulnerability scanning using Trivy. Pipeline fails on high/critical vulnerabilities.

### Deploy
Authenticates via OIDC, updates kubeconfig, and performs Helm upgrade in target namespace.

---

## Scaling Strategy

- Horizontal Pod Autoscaler adjusts replicas based on CPU utilization.
- Rolling updates ensure zero downtime deployments.
- Resource requests and limits prevent noisy neighbor issues.

---

## Security Practices Implemented

- No static AWS credentials
- IAM least privilege roles
- ECR scan on push
- Trivy image scanning in pipeline
- Private worker nodes
- Terraform state locking

---

## How to Provision Infrastructure
cd terraform
terraform init
terraform plan
terraform apply


---

## How to Deploy Application

Push code to main branch.  
Pipeline will automatically:

- Build
- Scan
- Push
- Deploy

---

## Key Skills Demonstrated

- Infrastructure as Code (Terraform)
- Kubernetes operations
- Secure CI/CD design
- Cloud networking architecture
- IAM and OIDC authentication
- Helm-based release management
- Production deployment strategy
