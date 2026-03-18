# Architecture Overview

## Purpose

The EKS DevSecOps Lab is a hands-on platform engineering project designed to simulate a small but realistic cloud-native delivery platform on AWS.

It is built as a public technical portfolio to demonstrate practical skills across infrastructure automation, Kubernetes operations, GitOps, container delivery, TLS exposure, and secret management.

## Scope

This documentation describes the current and target architecture of the lab across four layers:

1. **Infrastructure layer**  
   AWS resources provisioned with Terraform and Terragrunt

2. **Application delivery layer**  
   Demo application build and publish workflow

3. **GitOps layer**  
   Kubernetes deployment orchestration with ArgoCD

4. **Platform services layer**  
   Ingress, TLS automation, secret synchronization, and future policy controls

## Design Principles

The lab is intentionally built with a progressive and pragmatic approach:

- keep the architecture understandable
- avoid unnecessary abstraction too early
- use Git as the source of truth
- separate responsibilities across repositories
- improve security incrementally
- maintain a balance between realism, simplicity, and AWS cost control

## High-Level Architecture

At a high level, the platform works as follows:

- AWS infrastructure is provisioned from the **infra repository**
- the demo application is built and pushed to **Amazon ECR** from the **app repository**
- the image tag is updated in the **GitOps repository**
- **ArgoCD** reconciles Kubernetes state from Git into the EKS cluster
- **Traefik** exposes the application
- **cert-manager** obtains and renews TLS certificates from **Let's Encrypt**
- **External Secrets Operator** pulls secrets from **AWS Secrets Manager** into Kubernetes

## Architecture Maturity Model

The lab architecture is documented through two complementary views.

### Current Implemented Architecture

This covers the components that are already provisioned, configured, deployed, and validated.

### Target Platform Architecture

This covers the next platform capabilities already identified in repository roadmaps, README files, and architecture direction, such as policy enforcement, stronger supply chain security, and additional platform guardrails.

The objective is to make the architecture transparent: what is operational today is clearly separated from what is planned next.

## Main Repositories and Responsibilities

### Infrastructure repository
Owns AWS resources such as VPC, EKS, ECR, and IAM/OIDC components.

### Application repository
Owns the demo application source code, container image build, and image publication workflow.

### GitOps repository
Owns Kubernetes manifests, ArgoCD applications, ingress resources, and secret synchronization resources.

### Documentation repository
Owns diagrams, architecture documents, ADRs, and runbooks.

## Current State

The platform currently includes:

- AWS VPC and networking
- Amazon EKS cluster
- Amazon ECR repository
- GitHub Actions OIDC federation to AWS
- ArgoCD bootstrap
- demo application deployed through GitOps
- Traefik ingress controller
- cert-manager with Let's Encrypt production certificates
- External Secrets Operator connected to AWS Secrets Manager through IRSA

## Planned Evolution Themes

The next architecture themes currently identified are:

- policy enforcement with Kyverno
- more advanced platform security controls
- secure container supply chain improvements
- broader governance and compliance guardrails
- stronger operational documentation and runbooks

## Gap Analysis

The lab already demonstrates a strong end-to-end delivery baseline, but it has not yet reached its full target platform scope.

Main gaps between the current state and the target direction include:

- admission control and policy governance are not yet implemented
- secure container supply chain controls remain limited
- platform security is progressing but not yet fully codified
- documentation is being expanded to cover the whole architecture more thoroughly
- operational maturity is improving, but runbooks and architecture visuals still need to be completed

## Next Steps

The next logical platform steps are:

- document the full current architecture baseline
- complete major diagrams for infrastructure and delivery flows
- introduce Kyverno progressively
- expand security and governance documentation
- strengthen secure supply chain coverage over time

## Source of Truth

The architecture described here is based on the current content of:

- `eks-devsecops-lab-infra`
- `eks-devsecops-lab-app`
- `eks-devsecops-lab-gitops`
- `eks-devsecops-lab-docs`

Whenever implementation and README text differ, implementation should be treated as the primary source of truth for the **current state**, while README and roadmap content should inform the **target direction**.
