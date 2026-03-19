# EKS Cluster Architecture

## Purpose

This document describes the current and target architecture of the Amazon EKS cluster used in the EKS DevSecOps Lab.

It explains:

- how the cluster is provisioned
- how the current node and addon model works
- how the cluster supports GitOps and platform components
- what is already implemented
- what remains planned for later maturity stages

## Current Cluster Identity

The current development cluster is:

- cluster name: `eksdsl-dev`
- region: `eu-west-3`
- Kubernetes version: `1.35`

This cluster is provisioned through the infrastructure repository using Terraform and Terragrunt.

## Current Provisioning Model

The cluster is provisioned from the `live/dev/eks` Terragrunt stack.

This stack depends on the VPC stack and consumes:

- the VPC ID
- the private subnet IDs

This means the cluster is intentionally deployed into the private networking layer of the lab.

The infrastructure uses a reusable Terraform module based on the AWS EKS module.

## Current API Endpoint Strategy

The current cluster enables both:

- private API endpoint access
- public API endpoint access

The current public access CIDR is open to `0.0.0.0/0`.

This is functional and practical for a learning and portfolio lab, but it should be understood as a deliberate simplification rather than a hardened target-state configuration.

## Current Node Model

The current cluster uses one EKS managed node group.

Current node group characteristics:

- node group name pattern: `eksdsl-dev-workers`
- instance type: `t3.medium`
- desired size: `2`
- minimum size: `2`
- maximum size: `2`
- AMI type: `AL2023_x86_64_STANDARD`

This results in a small but stable baseline for platform experiments and GitOps-driven deployment.

## Current Addon Model

The current EKS module enables a focused set of managed addons:

- `coredns`
- `kube-proxy`
- `vpc-cni`
- `eks-pod-identity-agent`

This is a clean baseline that keeps the cluster minimal while still preparing it for platform features and AWS integration patterns.

## Current IAM and AWS Integration

The cluster has **IRSA** enabled.

This is an important architectural choice because it allows Kubernetes service accounts to access AWS services through IAM roles without static AWS credentials stored in workloads.

This capability is already used in the lab for External Secrets Operator and AWS Secrets Manager integration.

## Current Role in the Platform

The cluster acts as the runtime target for the GitOps platform.

Its current responsibilities include:

- hosting ArgoCD-managed workloads
- running the demo application
- running ingress and TLS components
- running External Secrets Operator
- receiving container images published to ECR through the GitOps flow

The cluster is therefore not just a raw Kubernetes environment. It is already the central runtime layer of the platform.

## Current Strengths

The current EKS design already demonstrates several strong engineering choices:

- infrastructure as code provisioning
- clean dependency on a dedicated VPC
- managed node group usage
- managed addons baseline
- IRSA support
- compatibility with GitOps and platform components
- a pragmatic size and cost profile for a lab

## Current Constraints

The current cluster remains intentionally limited in scope.

Examples:

- one main development cluster
- one node group
- fixed size rather than autoscaling-focused behavior
- public API exposure still permissive
- no advanced governance layer yet
- no multi-environment or multi-cluster strategy

These constraints are aligned with the current objective of keeping the platform understandable and affordable.

## Planned Evolution

The target platform direction for the EKS layer includes:

- stronger governance controls
- Kyverno integration
- clearer cluster security guardrails
- more explicit operational runbooks
- stronger linkage between supply chain and cluster policy decisions

These items are part of the planned platform maturity path, not the current validated baseline.

## Gap Analysis

Main gaps between the current cluster baseline and the target direction include:

- API exposure can be tightened further
- policy governance is not yet introduced
- workload guardrails are still limited
- cluster operations are not yet fully documented through runbooks
- the cluster architecture still needs richer component-level documentation

## Next Steps

The next logical steps for the EKS layer are:

1. document the main cluster-related components individually
2. document the operational lifecycle of the cluster
3. introduce Kyverno progressively
4. document future governance assumptions explicitly
5. keep the current cluster stable while the platform documentation grows

## Summary

The current EKS cluster is a strong practical baseline for the lab.

It is intentionally small and understandable, but already credible as a platform runtime foundation because it supports GitOps, ingress, TLS, and AWS-integrated secret management.
