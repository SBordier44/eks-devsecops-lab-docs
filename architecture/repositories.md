# Repository Architecture

## Purpose

The lab is intentionally split across multiple repositories to separate concerns and make the platform easier to understand, operate, and evolve.

This structure also mirrors a more realistic engineering setup where infrastructure, application code, deployment state, and documentation are managed independently.

## Repository List

### 1. eks-devsecops-lab-infra

This repository is responsible for provisioning and managing the AWS infrastructure layer.

It includes:

- Terraform modules
- Terragrunt live configuration
- VPC provisioning
- EKS provisioning
- ECR provisioning
- GitHub OIDC federation resources
- remote state configuration strategy

This repository does **not** deploy applications into Kubernetes.

---

### 2. eks-devsecops-lab-app

This repository contains the demo application and its delivery pipeline.

It includes:

- Go application source code
- Dockerfile
- GitHub Actions workflow for image build and push
- image publication to Amazon ECR
- GitOps repository update after image publication

This repository does **not** own the Kubernetes deployment manifests.

---

### 3. eks-devsecops-lab-gitops

This repository is the Kubernetes deployment source of truth.

It includes:

- ArgoCD bootstrap resources
- ArgoCD applications
- Kustomize bases and overlays
- demo application manifests
- ingress resources
- cert-manager resources
- External Secrets resources

This repository is responsible for describing the desired Kubernetes state.

---

### 4. eks-devsecops-lab-docs

This repository contains the documentation of the lab.

It includes:

- architecture documentation
- diagrams
- ADRs
- operational runbooks
- design rationale

This repository explains how the platform works and why key choices were made.

## Why This Split Matters

This separation provides several benefits:

- cleaner ownership boundaries
- clearer Git history by concern
- easier reasoning about change impact
- better alignment with GitOps principles
- improved documentation quality
- stronger portfolio presentation for real-world engineering practices

## Interaction Between Repositories

The main flow between repositories is the following:

1. the **infra repository** provisions AWS resources
2. the **app repository** builds and publishes a container image to ECR
3. the **app repository** updates the image tag in the **GitOps repository**
4. **ArgoCD** reconciles the desired state from the **GitOps repository**
5. the **docs repository** documents architecture, decisions, and operations

## Implemented State vs Repository Intent

Some repositories already include README sections describing future phases or target capabilities that are not yet fully implemented.

This documentation intentionally separates:

- the **implemented state**, based on the actual repository content and validated platform behavior
- the **repository intent**, based on roadmap elements and future architecture directions already documented in project README files

Both views are useful:

- the implemented state proves hands-on delivery
- the repository intent shows architectural direction and platform maturity goals

## Source of Truth by Topic

### AWS infrastructure
Source of truth: `eks-devsecops-lab-infra`

### Container image build and publication
Source of truth: `eks-devsecops-lab-app`

### Kubernetes deployment state
Source of truth: `eks-devsecops-lab-gitops`

### Architecture, rationale, and runbooks
Source of truth: `eks-devsecops-lab-docs`

## Current Implemented Scope by Repository

### Infrastructure repository
Implemented today:
- AWS VPC and networking
- Amazon EKS
- Amazon ECR
- GitHub OIDC federation
- remote state setup

Planned or evolving topics:
- stronger IAM scoping
- additional hardening layers
- future platform security enhancements

### Application repository
Implemented today:
- demo application
- container build pipeline
- push to ECR
- GitOps image update flow

Planned or evolving topics:
- stronger secure supply chain controls
- additional CI security hardening

### GitOps repository
Implemented today:
- ArgoCD bootstrap
- demo application deployment
- Traefik ingress
- cert-manager and Let's Encrypt
- External Secrets integration

Planned or evolving topics:
- Kyverno
- additional governance controls
- broader platform policies

### Documentation repository
Implemented today:
- baseline architecture structure
- ADR foundation
- architecture documentation effort in progress

Planned or evolving topics:
- richer diagrams
- fuller component documentation
- stronger runbooks
- broader architecture storytelling

## Practical Reading Order

For someone discovering the project, the recommended order is:

1. `eks-devsecops-lab-docs`
2. `eks-devsecops-lab-infra`
3. `eks-devsecops-lab-app`
4. `eks-devsecops-lab-gitops`

This order gives context first, then infrastructure, then delivery, then runtime state.
