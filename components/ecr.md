# ECR Component

## Purpose

This document describes the Amazon ECR component used in the EKS DevSecOps Lab.

It explains:

- the current role of ECR in the platform
- the current repository configuration
- how ECR integrates with CI and GitOps
- what security controls are already present
- what is planned for future supply chain maturity

## Current Role

Amazon ECR is the current container registry of the lab.

Its role is to store the demo application image built by GitHub Actions and consumed by the Kubernetes platform through the GitOps delivery flow.

The current application repository name is:

- `eks-devsecops-lab-dev-app`

## Current Provisioning Model

The ECR repository is provisioned by Terraform through the `live/dev/ecr` Terragrunt stack.

This means the registry itself is part of the infrastructure-as-code baseline and not a manually created service dependency.

## Current Configuration

The current ECR repository includes:

- immutable tags
- scan on push enabled
- `AES256` encryption
- lifecycle policy enabled

These controls already make the registry more disciplined and realistic than a minimal “push anything” container registry setup.

## Current Lifecycle Policy

The current lifecycle policy includes two important behaviors:

- untagged images expire after 7 days
- only the latest 20 tagged images are kept for selected tag prefixes

This helps control storage growth while keeping a useful set of recent images.

## Current Integration in the Delivery Chain

The current image delivery model is:

1. GitHub Actions builds the image from the application repository
2. the image is pushed to ECR
3. the GitOps repository is updated with the new image tag
4. ArgoCD reconciles the cluster state
5. Kubernetes pulls the image from ECR

This means ECR is a critical link between CI and runtime.

## Current Security Value

The ECR setup already demonstrates several useful security and operational practices:

- image immutability
- scan on push
- encryption at rest
- lifecycle cleanup
- infrastructure-as-code provisioning
- repository-level scoping for CI permissions

These controls support the current secure delivery baseline of the lab.

## Current Constraints

The current ECR implementation remains intentionally focused.

Examples:

- one main application repository
- one simple promotion model
- no signature verification yet
- no attestation workflow yet
- no broader registry policy documentation yet

This is aligned with the current maturity of the platform.

## Planned Evolution

The target direction for the registry and supply chain area includes:

- stronger vulnerability management processes
- image trust improvements
- signature and attestation strategy
- richer documentation around artifact promotion and deployment trust

These topics belong to the planned supply chain maturity path rather than the validated current baseline.

## Gap Analysis

Main gaps between the current ECR baseline and the target platform direction include:

- no image signing workflow yet
- no attestation or provenance model yet
- no deeper artifact trust policy yet
- no advanced multi-stage promotion design documented yet

## Next Steps

The next logical steps for the ECR area are:

1. document the current CI-to-ECR workflow in more operational detail
2. document the current lifecycle and cleanup strategy in the runbooks
3. connect future supply chain decisions to ADRs
4. keep the current registry baseline stable while maturity increases progressively

## Summary

The ECR component is already a solid and credible registry foundation for the lab.

It supports the current GitOps delivery model well and provides useful security and lifecycle controls without unnecessary complexity.
