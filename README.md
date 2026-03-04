# eks-devsecops-lab-docs

This repository contains the documentation for the **EKS DevSecOps Lab**.

The goal of this lab is to design and operate a **production-like Kubernetes platform on AWS** while applying DevSecOps best practices.

## Contents

- Architecture diagrams
- Architecture Decision Records (ADR)
- Security design
- Incident simulations
- Platform documentation

## Architecture overview

The platform is composed of:

- AWS infrastructure provisioned with Terraform/Terragrunt
- Amazon EKS Kubernetes cluster
- GitOps deployment with ArgoCD
- Secure container supply chain
- Kubernetes security policies

## Related repositories

Infrastructure: [eks-devsecops-lab-infra](https://github.com/SBordier44/eks-devsecops-lab-infra)

Application code: [eks-devsecops-lab-app](https://github.com/SBordier44/eks-devsecops-lab-app)

GitOps: [eks-devsecops-lab-gitops](https://github.com/SBordier44/eks-devsecops-lab-gitops)
