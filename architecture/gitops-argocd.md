# GitOps and ArgoCD Architecture

## Purpose

This document describes how GitOps is implemented in the EKS DevSecOps Lab using ArgoCD.

It explains:

- the current GitOps structure
- how ArgoCD is bootstrapped
- how applications and platform components are reconciled
- how the application delivery pipeline interacts with the GitOps repository
- what is already implemented versus what is planned next

## Why GitOps

GitOps is used in this lab to make Kubernetes delivery:

- declarative
- traceable
- reproducible
- easier to review
- easier to roll back

Instead of deploying workloads directly from CI to the cluster, the desired Kubernetes state is stored in Git and reconciled by ArgoCD.

This separation helps keep delivery workflows cleaner and closer to platform engineering practices used in real teams.

## Current Repository Role

The `eks-devsecops-lab-gitops` repository is the source of truth for Kubernetes desired state.

It currently contains:

- ArgoCD bootstrap resources
- ArgoCD applications
- Kustomize bases and overlays
- demo application manifests
- Traefik deployment definition through ArgoCD
- cert-manager deployment definition through ArgoCD
- Let's Encrypt ClusterIssuer resources
- External Secrets resources

## Current ArgoCD Bootstrap Model

The lab uses a root ArgoCD application.

The bootstrap entry point is a single ArgoCD `Application` named `gitops-root`, pointing to the `argocd` directory of the GitOps repository.

This means ArgoCD is configured to reconcile its own higher-level application definitions from Git.

This creates a simple and understandable bootstrapping model:
- bootstrap ArgoCD once
- let ArgoCD manage the rest from the GitOps repository

## Current ArgoCD Resource Layout

The current `argocd/kustomization.yaml` assembles:

- the `apps` project
- the `demo-app-dev` application
- the `traefik` application
- the `cert-manager` application
- Let's Encrypt staging and production issuers
- External Secrets CRD application
- External Secrets application
- AWS ClusterSecretStore resource

This shows that the current GitOps baseline already covers both:
- workload deployment
- selected platform services

## Current Application Model

The demo application is deployed through an ArgoCD `Application` called `demo-app-dev`.

That application points to:

- repository: `eks-devsecops-lab-gitops`
- path: `apps/demo-app/overlays/dev`

This means the workload is deployed through a standard Kustomize overlay model.

## Current Kustomize Model

The demo application uses:

- a reusable `base`
- a `dev` overlay

The current `dev` overlay includes:

- namespace definition
- the base manifests
- ingress definition
- external secret definition
- image override
- environment label

This is a simple and effective structure for the current lab scope.

## Current Delivery Flow

The current application delivery flow is the following:

1. code is pushed to the application repository
2. GitHub Actions builds the container image
3. the image is pushed to Amazon ECR
4. the workflow checks out the GitOps repository
5. Kustomize updates the image reference in the `dev` overlay
6. the workflow commits and pushes the GitOps change
7. ArgoCD detects the Git change and reconciles the cluster state

This means CI is responsible for publishing the artifact and updating desired state, while ArgoCD is responsible for cluster reconciliation.

## Why This Model Matters

This delivery model keeps a clear separation between:

- **build responsibility**
- **deployment state responsibility**
- **runtime reconciliation responsibility**

That separation is valuable because it:

- makes change intent visible in Git
- preserves traceability of deployed versions
- reduces direct CI coupling to the cluster
- aligns with GitOps operating principles

## Current Sync Strategy

The current ArgoCD applications use automated synchronization with:

- pruning enabled
- self-healing enabled
- namespace creation enabled where relevant

This makes the platform capable of continuously reconciling drift back to the state declared in Git.

## Current Strengths

The current GitOps implementation already demonstrates:

- clear GitOps repository separation
- root application bootstrap model
- Kustomize-based workload customization
- automated deployment reconciliation
- platform service deployment through ArgoCD
- a practical flow between CI and GitOps

## Current Constraints

The current GitOps model intentionally remains simple.

Notable characteristics:
- one main environment focus
- one demo application
- no advanced multi-cluster design
- no complex ApplicationSet strategy
- no broad governance layer yet

This is deliberate and aligned with the lab principle of progressive implementation.

## Planned Evolution

The target GitOps direction includes:

- Kyverno integration through GitOps
- broader platform governance
- more explicit policy layering
- stronger secure supply chain controls
- richer operational documentation around reconciliation and rollback

These topics belong to the planned platform direction, not to the currently validated baseline.

## Gap Analysis

Main gaps between current GitOps maturity and target platform direction include:

- governance controls are still limited
- policy-driven enforcement is not yet in place
- secure supply chain controls are still early
- GitOps architecture documentation is still being expanded
- runbooks for failure scenarios need to be enriched

## Next Steps

The next logical steps for this area are:

1. fully document the current GitOps baseline
2. add a dedicated runbook for ArgoCD operations and troubleshooting
3. introduce Kyverno progressively through GitOps
4. document future governance patterns without over-engineering the current setup

## Summary

The current GitOps implementation is intentionally simple, clean, and effective.

It already proves a real delivery chain from source code to running workload through Git-driven reconciliation, while leaving room for progressive governance and security improvements in future iterations.
