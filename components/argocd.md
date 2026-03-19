# ArgoCD Component

## Purpose

This document describes the ArgoCD component of the EKS DevSecOps Lab.

It explains:

- the role of ArgoCD in the platform
- the current bootstrap and reconciliation model
- the relationship between the GitOps repository and the cluster
- what is already implemented
- what remains planned for future maturity

## Current Role

ArgoCD is the GitOps reconciliation engine of the lab.

Its role is to:

- read Kubernetes desired state from the GitOps repository
- reconcile that desired state into the cluster
- deploy both workloads and selected platform services
- continuously correct drift when the cluster differs from Git

ArgoCD is therefore one of the most central components of the current platform baseline.

## Current Bootstrap Model

The platform currently uses a root ArgoCD application.

The bootstrap entrypoint is an application named `gitops-root`, pointing to the `argocd` directory of the GitOps repository.

This creates a simple and understandable model:

- bootstrap ArgoCD once
- let ArgoCD manage the rest from Git

## Current GitOps Scope

The current ArgoCD layer already manages:

- the demo application deployment
- Traefik
- cert-manager
- Let's Encrypt ClusterIssuer resources
- External Secrets CRDs
- External Secrets Operator
- the AWS ClusterSecretStore resource

This shows that ArgoCD already owns both:
- workload delivery
- selected platform service delivery

## Current Reconciliation Model

The current ArgoCD applications use automated sync.

That means the current model already includes:

- automated synchronization
- pruning
- self-healing
- namespace creation where relevant

This is an important strength because it makes the platform continuously converge toward Git-declared desired state.

## Current Repository Relationship

ArgoCD consumes the `eks-devsecops-lab-gitops` repository as the source of truth for Kubernetes runtime state.

This is a deliberate separation of concerns:

- the application repository builds the artifact
- the GitOps repository owns deployment intent
- ArgoCD owns cluster reconciliation

This separation is one of the most valuable architectural characteristics of the lab.

## Current Strengths

The ArgoCD component already demonstrates:

- real GitOps reconciliation
- root application bootstrap
- automated synchronization
- workload and platform component management
- clean separation between CI and cluster reconciliation
- a practical and realistic GitOps operating model

## Current Constraints

The current ArgoCD scope intentionally remains simple.

Examples include:

- one main environment focus
- one main application workload
- no advanced multi-cluster model
- no complex ApplicationSet architecture
- no broad policy-driven governance layer yet

These constraints are deliberate and aligned with the lab philosophy of progressive implementation.

## Planned Evolution

The target direction for ArgoCD and GitOps includes:

- future governance integration with Kyverno
- richer rollback and troubleshooting documentation
- clearer policy layering around platform components
- stronger documentation of change and promotion patterns

These topics are part of the target platform direction rather than the currently validated baseline.

## Gap Analysis

Main gaps between the current ArgoCD baseline and the target direction include:

- governance and policy controls are still limited
- advanced operational runbooks are not yet complete
- secure supply chain enforcement is not yet connected to cluster admission
- the GitOps model is still intentionally narrow in scope

## Next Steps

The next logical steps for the ArgoCD component are:

1. expand operational runbooks for sync, drift, and troubleshooting
2. document the failure and recovery paths more explicitly
3. later integrate future governance components progressively
4. keep the current GitOps model simple and stable

## Summary

The ArgoCD component is already a major strength of the lab.

It provides a real GitOps operating model with clean separation between build, desired state management, and runtime reconciliation.
