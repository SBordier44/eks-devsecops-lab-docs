# EKS DevSecOps Lab Documentation

This repository contains the architecture documentation, ADRs, and operational guides for the **EKS DevSecOps Lab**. Architecture visuals may be added progressively when they provide clear value.

The goal of this lab is to design, build, and operate a **production-like Kubernetes platform on AWS** while applying practical **DevSecOps**, **GitOps**, and **platform engineering** practices.

## Objectives

This lab is used to demonstrate hands-on capabilities in:
- AWS infrastructure design with **Terraform** and **Terragrunt**
- Kubernetes platform operations with **Amazon EKS**
- GitOps deployment workflows with **ArgoCD**
- Secure container delivery with **GitHub Actions** and **Amazon ECR**
- HTTPS exposure with **Traefik**, **cert-manager**, and **Let's Encrypt**
- Secret management with **External Secrets Operator**, **AWS Secrets Manager**, and **IRSA**
- Progressive platform hardening and security controls with **Kyverno**

## Repository Map

The lab is split across four repositories:
- **eks-devsecops-lab-infra**  
  AWS infrastructure provisioning with Terraform and Terragrunt
- **eks-devsecops-lab-app**  
  Demo application and CI pipeline used to build and publish a container image
- **eks-devsecops-lab-gitops**  
  Kubernetes manifests and ArgoCD applications used to deploy workloads to the cluster
- **eks-devsecops-lab-docs**  
  Architecture documentation, diagrams, ADRs, and runbooks

## Current vs Target Platform Scope

This documentation intentionally distinguishes between:
- **Implemented capabilities**: features currently deployed and validated in the lab
- **Planned capabilities**: features already identified in repository roadmaps or README files, but not yet implemented
- **Target platform direction**: the broader platform engineering vision for the lab

This distinction is important to keep the documentation transparent, credible, and professionally useful.

### Implemented Capabilities

The lab currently includes:
- AWS networking and Kubernetes infrastructure
- Terraform remote state with S3 and DynamoDB
- Amazon EKS cluster with managed node group
- Amazon ECR container registry
- GitHub Actions to AWS authentication through OIDC
- ArgoCD bootstrap and GitOps deployment model
- demo application deployment through GitOps
- Ingress management with Traefik
- TLS automation with cert-manager and Let's Encrypt
- Secret synchronization from AWS Secrets Manager to Kubernetes with External Secrets Operator and IRSA
- Kyverno deployment through ArgoCD
- first Kyverno `Audit` policies for workload governance on `demo-app-dev`

### Planned Capabilities

The target platform direction currently includes:
- broader Kyverno policy coverage beyond the initial demo application scope
- later progressive enforcement of selected governance controls
- secure container supply chain hardening
- additional platform security controls
- richer observability and operational runbooks
- broader policy, compliance, and platform guardrails

## Documentation Structure

### Architecture

High-level and detailed technical views of the platform:
- `architecture/overview.md`
- `architecture/repositories.md`
- `architecture/aws-terraform-terragrunt.md`
- `architecture/eks-cluster.md`
- `architecture/gitops-argocd.md`
- `architecture/networking-ingress-tls.md`
- `architecture/container-supply-chain.md`
- `architecture/secrets-management.md`
- `architecture/security-model.md`
- `architecture/roadmap.md`

### Components

One document per major platform building block:
- `components/vpc.md`
- `components/ecr.md`
- `components/github-oidc.md`
- `components/eks.md`
- `components/argocd.md`
- `components/demo-app.md`
- `components/traefik.md`
- `components/cert-manager.md`
- `components/external-secrets.md`
- `components/kyverno.md`

### Runbooks

Operational and troubleshooting guides:
- `runbooks/terraform-terragrunt.md`
- `runbooks/argocd.md`
- `runbooks/ingress-tls.md`
- `runbooks/external-secrets.md`
- `runbooks/aws-irsa.md`
- `runbooks/kyverno.md`

### ADR

Architecture Decision Records documenting major choices and trade-offs.

### Diagrams

Architecture visuals may be added progressively when they provide clear value. When useful, lightweight Markdown-friendly formats such as Mermaid will be preferred.

## Design Principles

This lab follows a few simple principles:
- **Keep the platform understandable**
- **Prefer progressive implementation over over-engineering**
- **Use Git as the source of truth**
- **Separate infrastructure, application, deployment, and documentation concerns**
- **Favor reproducibility, traceability, and operational clarity**
- **Balance security improvements with cost awareness**

## Who This Repository Is For

This repository is intended for:
- recruiters and hiring managers
- clients evaluating platform engineering or DevSecOps capabilities
- engineers reviewing the lab architecture
- the project owner as a long-term operational reference

## Status

The lab is evolving iteratively. Documentation is built to reflect:
- the **real implemented state**
- the **planned next capabilities**
- the **target platform direction**

This repository aims to document both delivery and architectural intent without mixing them.

## Related Repositories

- Infrastructure: [eks-devsecops-lab-infra](https://github.com/SBordier44/eks-devsecops-lab-infra)
- Application: [eks-devsecops-lab-app](https://github.com/SBordier44/eks-devsecops-lab-app)
- GitOps: [eks-devsecops-lab-gitops](https://github.com/SBordier44/eks-devsecops-lab-gitops)
