# Production-Style DevOps Platform on AWS (EKS + Terraform + GitHub Actions)

## Project Overview

This project demonstrates a production-oriented DevOps platform built on AWS using Infrastructure as Code and Kubernetes best practices.

The platform provisions infrastructure using Terraform, builds and scans container images, and deploys applications to Amazon EKS using Helm through a gated multi-stage GitHub Actions pipeline.

This project focuses on:

- Reproducible infrastructure provisioning
- Secure authentication using OIDC (no static AWS credentials)
- Deterministic deployments using commit-based image tagging
- Multi-environment promotion (staging → production)
- Production deployment controls

---

## Architecture

### Infrastructure Layer (Terraform)

- Custom VPC (10.0.0.0/16)
- Public and Private subnets across two Availability Zones
- NAT Gateways for outbound internet access from private subnets
- Amazon EKS cluster
- Managed Node Group
- Amazon ECR repository (scan-on-push enabled)
- Remote Terraform backend (S3 + DynamoDB locking)

Worker nodes run in private subnets for improved security isolation.

---

### Application Layer

- Dockerized Nginx application
- Helm chart for Kubernetes deployment
- Resource requests and limits
- Liveness and readiness probes
- Rolling update strategy
- Horizontal Pod Autoscaler (HPA)

Container images are tagged using Git commit SHA for traceability and deterministic rollbacks.

---

### CI/CD Pipeline (GitHub Actions)

Pipeline stages:

1. Build Docker image
2. Tag image using commit SHA
3. Push image to Amazon ECR
4. Run vulnerability scan using Trivy
5. Deploy automatically to staging
6. Require manual approval before deploying to production

Production deployments are protected using GitHub Environment approval rules.

Authentication to AWS is implemented using OIDC-based IAM role assumption, eliminating long-lived AWS credentials.

---

## Environment Strategy

Kubernetes namespaces are used for environment isolation:

- dev
- staging
- production

Deployment flow:

Developer Push → Build → Scan → Deploy Staging → Manual Approval → Deploy Production

---

## Security Practices Implemented

- No static AWS access keys
- OIDC-based GitHub authentication
- Private worker nodes
- ECR image scanning enabled
- Trivy vulnerability scanning in CI pipeline
- Terraform remote state with locking
- Production approval gate before release

---

## Scaling Strategy

- Horizontal Pod Autoscaler based on CPU utilization
- Rolling updates ensure zero-downtime deployments
- Resource requests and limits prevent resource contention

---

## Repository Structure
.  
├── terraform/ Infrastructure as Code (AWS + EKS)  
├── app/ Application source & Dockerfile  
├── helm/ Helm chart for Kubernetes deployment  
├── .github/workflows CI/CD pipeline definition  
├── .gitignore  
└── README.md

---

## Provision Infrastructure
cd terraform  
terraform init  
terraform plan  
terraform apply

---

## Deploy Application

Push changes to the main branch.

The pipeline will automatically:
- Build
- Scan
- Push
- Deploy to staging
- Wait for production approval
- Deploy to production
---
## Key Concepts Demonstrated

- Infrastructure as Code (Terraform)
- Kubernetes production deployment patterns
- Secure CI/CD pipeline design
- Cloud networking architecture
- IAM and OIDC authentication
- Helm-based release management
- Multi-environment promotion strategy
- Deployment protection controls

