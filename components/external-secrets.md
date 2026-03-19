# External Secrets Component

## Purpose

This document describes the External Secrets component of the EKS DevSecOps Lab.

It explains:

- the role of External Secrets Operator in the platform
- how it integrates with AWS Secrets Manager
- how IRSA is used for authentication
- what is already implemented and validated
- what remains planned for future maturity

## Current Role

External Secrets Operator is the secret synchronization component of the lab.

Its role is to bridge:

- AWS Secrets Manager
- Kubernetes secret consumption
- GitOps-managed secret references

It allows the platform to retrieve secret data from AWS and materialize it as Kubernetes Secrets without storing raw secret values in Git.

## Why This Component Matters

This is an important platform capability because it demonstrates a more realistic cloud-native secret model than:

- hardcoding secrets in manifests
- storing clear secret values in Git
- managing long-lived AWS keys inside workloads

The component also proves that the lab already supports real AWS-to-Kubernetes runtime integration.

## Current Deployment Model

External Secrets is deployed through the GitOps repository and managed by ArgoCD.

The current installation is structured so that:

- CRDs are installed separately
- the operator itself is deployed through a dedicated ArgoCD application
- the AWS `ClusterSecretStore` is managed declaratively
- application-level `ExternalSecret` resources are part of workload overlays

This reflects lessons learned during implementation and contributes to a more stable GitOps setup.

## Current AWS Integration Model

The current AWS integration relies on:

- AWS Secrets Manager
- IRSA
- a dedicated IAM role for the operator service account

This means the operator authenticates to AWS without static access keys stored in Kubernetes manifests.

This is one of the strongest current integration points between the EKS cluster and AWS services.

## Current Secret Store Model

The current setup includes an AWS-backed `ClusterSecretStore`.

This store defines how External Secrets connects to AWS Secrets Manager and is shared as a reusable platform-level secret source.

This design is a good fit for the current lab because it keeps the platform-level secret provider separate from workload-level secret references.

## Current Workload Integration

The demo application currently includes an `ExternalSecret` resource in its `dev` overlay.

That resource synchronizes data from AWS Secrets Manager into a Kubernetes Secret that can then be consumed by the application.

This means the component is not only installed — it is already validated in a real end-to-end use case.

## Current Strengths

The current External Secrets baseline already demonstrates:

- GitOps-managed deployment
- AWS-backed secret source
- IRSA-based authentication
- Kubernetes secret synchronization
- real workload-level consumption path
- a practical secret management pattern compatible with the rest of the platform

## Current Constraints

The current implementation remains intentionally narrow in scope.

Examples include:

- one main validated AWS secret flow
- one current operator-based integration pattern
- limited governance around secret usage
- no broader policy control over secret consumption yet
- no deeper lifecycle or rotation documentation yet

These constraints are appropriate for the current maturity stage of the lab.

## Planned Evolution

The target direction for this area may include:

- broader secret consumption documentation
- more explicit secret governance expectations
- stronger operational procedures
- future policy-based controls around workload definitions and secret usage

These items belong to the planned platform direction rather than the validated current baseline.

## Gap Analysis

Main gaps between the current External Secrets baseline and the target direction include:

- only a limited number of real secret use cases are currently documented
- runbooks still need to be expanded
- governance around secret usage remains limited
- no policy engine currently validates secret-related workload expectations

## Next Steps

The next logical steps for the External Secrets component are:

1. document troubleshooting and validation procedures in dedicated runbooks
2. document the current IRSA integration path more explicitly
3. later connect this area to governance and policy controls
4. keep the current secret flow stable and understandable before broadening scope

## Summary

The External Secrets component is now a validated and meaningful building block of the platform.

It already demonstrates a credible AWS-native and Kubernetes-native secret management pattern, which is a strong signal of real platform engineering capability.
