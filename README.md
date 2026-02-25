# DevOps EKS CI/CD Project

## Overview
End-to-end DevOps pipeline using:
- Terraform (Infrastructure)
- Docker (Containerization)
- ECR (Image Registry)
- EKS (Kubernetes Cluster)
- GitHub Actions (CI/CD)

## Architecture
Developer → GitHub → GitHub Actions → ECR → EKS

## Features Implemented
- Custom Docker image
- ECR push
- Kubernetes Deployment + Service
- HPA
- Rolling updates
- Multi-stage CI pipeline

## Next Steps
- Helm conversion
- Argo CD (GitOps)
- Monitoring
- OIDC authentication
