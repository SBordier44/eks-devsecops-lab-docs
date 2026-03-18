# Secrets Management Architecture

## Purpose

This document explains how secrets are managed in the EKS DevSecOps Lab.

It covers:

- the current secret management model
- the role of AWS Secrets Manager
- the role of External Secrets Operator
- the role of IRSA
- how secrets are synchronized into Kubernetes
- what is currently implemented versus what is planned next

## Why Secrets Management Matters

A realistic platform should avoid storing application secrets directly in Git or managing them manually inside Kubernetes whenever possible.

The current lab uses a progressive but practical model:

- secret values are stored in AWS Secrets Manager
- Kubernetes reads them through External Secrets Operator
- AWS access from Kubernetes is granted through IRSA
- the application consumes Kubernetes Secrets generated from external sources

This provides a better security and operations model than keeping sensitive values directly in manifests.

## Current Implemented Model

The current secret flow is:

1. a secret is stored in AWS Secrets Manager
2. External Secrets Operator authenticates to AWS using IRSA
3. a `ClusterSecretStore` defines the AWS provider connection
4. an `ExternalSecret` defines which external secret data to synchronize
5. Kubernetes receives a generated `Secret`
6. the workload can consume that generated secret

This model is already implemented and validated in the lab.

## Current Platform Components

The current implemented secret management baseline includes:

- External Secrets CRDs installed through ArgoCD
- External Secrets Operator deployed through ArgoCD
- a dedicated AWS `ClusterSecretStore`
- an `ExternalSecret` in the demo application overlay
- AWS IAM role integration through IRSA
- successful synchronization from AWS Secrets Manager to Kubernetes

## Current IRSA Role

IRSA is a key part of the design.

Its role is to let the External Secrets Kubernetes service account assume an AWS IAM role without using long-lived static AWS credentials stored in Kubernetes.

This provides:
- short-lived credentials
- better security posture
- clearer access boundaries
- a more realistic cloud-native AWS integration pattern

## Current Demo Application Example

The current demo application `dev` overlay contains an `ExternalSecret` named `demo-app-secret`.

That resource points to:

- `ClusterSecretStore`: `aws-secretsmanager`
- remote key: `eks-devsecops-lab/dev/demo-app`
- property: `DEMO_MESSAGE`

This means the lab already includes a real, validated end-to-end secret synchronization path.

## Why This Design Was Chosen

This design was chosen because it is:

- simple enough for a progressive lab
- realistic enough for platform engineering practice
- safer than storing secret values in Git
- compatible with the GitOps model already used in the lab

The current model also keeps responsibilities clear:

- AWS owns the secret source
- External Secrets owns synchronization
- Kubernetes owns the runtime secret object
- the application consumes the final secret

## Current Strengths

The current secrets baseline already demonstrates:

- real external secret source integration
- Kubernetes-to-AWS authentication with IRSA
- secret synchronization without static AWS keys in manifests
- a practical and modern Kubernetes secret management pattern

## Current Constraints

The current implementation remains intentionally narrow in scope.

Examples:
- one main secret flow demonstrated
- one operator-driven integration pattern
- no broad secret governance yet
- no advanced multi-tenant secret model
- no deeper secret rotation narrative documented yet

This is acceptable for the current maturity stage of the lab.

## Planned Evolution

The target platform direction may later include:

- broader secret management documentation
- stronger governance around secret usage
- tighter policy controls on Kubernetes resource definitions
- more explicit workload security expectations
- stronger documentation around rotation and operational procedures

Those topics belong to the planned architecture rather than the validated current baseline.

## Gap Analysis

Main gaps between the current implementation and the target platform direction include:

- only a limited number of secret use cases are documented today
- operational runbooks for troubleshooting and lifecycle management still need to be expanded
- governance around secret consumption is still limited
- policy-based controls are not yet introduced

## Next Steps

The next logical steps for this area are:

1. add a dedicated troubleshooting runbook for External Secrets and IRSA
2. document the exact validation and debugging workflow used during the lab
3. later complement this area with policy and governance controls
4. keep the current flow stable and understandable before adding more complexity

## Summary

The secret management layer is now a validated part of the platform baseline.

It demonstrates a practical AWS-native and Kubernetes-native integration pattern that is both credible for a portfolio and useful as a real platform building block.
