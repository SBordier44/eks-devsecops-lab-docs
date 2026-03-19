# ADR-0006 External Secrets with AWS Secrets Manager

## Context
The platform needs a secret management model that avoids:

- storing raw secret values in Git
- managing AWS credentials manually inside workloads
- hardcoding application secrets in Kubernetes manifests

The solution needs to fit the current AWS and EKS environment while remaining understandable and progressive.

## Decision
The platform uses:

- AWS Secrets Manager as the external secret source
- External Secrets Operator inside Kubernetes
- IRSA for Kubernetes-to-AWS authentication

The current setup includes:

- External Secrets CRDs
- the operator deployment through ArgoCD
- an AWS-backed ClusterSecretStore
- an ExternalSecret in the demo application overlay

## Consequences
This decision provides:

- a more realistic cloud-native secret management model
- no static AWS access keys in Kubernetes manifests for this integration
- a clean separation between secret source, synchronization, and runtime consumption
- a strong foundation for future policy and governance controls around secret usage

It also confirms IRSA as an important platform capability of the lab.
