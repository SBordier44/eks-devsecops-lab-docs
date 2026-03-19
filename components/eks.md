# EKS Component

## Purpose

This document describes the Amazon EKS component of the EKS DevSecOps Lab.

It explains:

- the current role of EKS in the platform
- how the cluster is provisioned and configured
- how it supports the platform runtime
- what is already implemented
- what remains planned for future maturity

## Current Role

Amazon EKS is the runtime foundation of the lab.

Its role is to host:

- ArgoCD
- the demo application
- Traefik
- cert-manager
- External Secrets Operator
- future platform components such as Kyverno

EKS is therefore the central execution layer of the platform.

## Current Cluster Identity

The current development cluster is:

- cluster name: `eksdsl-dev`
- region: `eu-west-3`
- Kubernetes version: `1.35`

The cluster is provisioned from the infrastructure repository through Terraform and Terragrunt.

## Current Provisioning Model

The EKS component is deployed from the `live/dev/eks` Terragrunt stack.

This stack depends on the VPC stack and consumes:

- the VPC ID
- the private subnet IDs

The cluster is therefore intentionally attached to the network layer created by the infrastructure baseline.

## Current API Access Model

The cluster currently enables both:

- private endpoint access
- public endpoint access

The public endpoint is currently open broadly for operational convenience in the lab.

This is a deliberate trade-off:
- easier access during platform iteration
- less restrictive than a hardened production-style access model

## Current Node Model

The cluster uses one managed node group.

Current characteristics include:

- worker instance type: `t3.medium`
- desired size: `2`
- minimum size: `2`
- maximum size: `2`

This provides a small but stable capacity baseline for the current platform scope.

## Current Addon Baseline

The cluster uses a focused set of managed addons:

- CoreDNS
- kube-proxy
- VPC CNI
- EKS Pod Identity Agent

This keeps the cluster baseline minimal while still supporting the current AWS and Kubernetes integration needs of the lab.

## Current AWS Integration Capabilities

A key current capability of the cluster is **IRSA**.

IRSA allows Kubernetes service accounts to access AWS services through IAM roles without static AWS credentials stored in workloads.

This is already used in the lab for the External Secrets integration with AWS Secrets Manager.

## Current Strengths

The current EKS component already demonstrates:

- infrastructure as code provisioning
- dependency on a dedicated VPC layer
- managed node group usage
- managed addon baseline
- IRSA support
- compatibility with GitOps-based runtime management
- support for real ingress, TLS, and secret synchronization use cases

## Current Constraints

The current implementation remains intentionally limited.

Examples include:

- one main development cluster
- one node group
- no advanced autoscaling strategy documented yet
- public API endpoint access still permissive
- no policy engine deployed yet
- no multi-cluster or multi-environment runtime model

These constraints are aligned with the current maturity stage of the lab.

## Planned Evolution

The target direction for the EKS component includes:

- stronger policy and governance controls
- Kyverno integration
- richer cluster runbooks
- tighter cluster security expectations
- stronger documentation of operational and security assumptions

These topics are part of the planned platform direction rather than the current validated baseline.

## Gap Analysis

Main gaps between the current EKS baseline and the target direction include:

- API exposure can be tightened
- cluster governance is still limited
- workload policy controls are not yet introduced
- operational procedures still need broader documentation
- future platform guardrails are not yet implemented

## Next Steps

The next logical steps for the EKS component are:

1. document the remaining platform components around the cluster
2. document cluster-oriented runbooks
3. introduce Kyverno progressively
4. keep the current cluster stable while platform governance matures

## Summary

The EKS component is already a strong runtime foundation for the lab.

It is intentionally small and understandable, but already supports a credible platform baseline with GitOps, ingress, TLS, and AWS-integrated secret management.
