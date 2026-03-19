# VPC Component

## Purpose

This document describes the VPC component of the EKS DevSecOps Lab.

It explains:

- the current VPC design
- how the subnet layout is built
- how the VPC supports EKS and public application exposure
- what is implemented today
- what remains planned for future improvements

## Current Role

The VPC is the foundational networking component of the lab.

Its role is to provide:

- a dedicated AWS network boundary
- public and private subnets
- Internet and outbound connectivity
- subnet tagging compatible with Kubernetes load balancer behavior
- a clean base for EKS and related platform services

## Current Configuration

The current development VPC is provisioned from the `live/dev/vpc` Terragrunt stack.

Current characteristics include:

- VPC CIDR: `10.0.0.0/16`
- 2 Availability Zones
- public subnets
- private subnets
- Internet Gateway
- one NAT Gateway
- S3 Gateway Endpoint enabled
- interface VPC endpoints disabled in the current live configuration

## Current Subnet Strategy

The VPC module derives subnet ranges from the `/16` VPC CIDR.

The current design creates:

- public `/20` subnets
- private `/20` subnets

The layout is intentionally simple and easy to understand.

This is sufficient for the current lab while still being realistic enough for Kubernetes platform work.

## Current Kubernetes Integration

The VPC subnets are tagged for Kubernetes integration.

Public subnets are tagged for external load balancer usage.

Private subnets are tagged for internal load balancer usage.

This means the VPC is already prepared for standard Kubernetes and EKS networking behavior.

## Current Connectivity Model

The current networking model is intentionally pragmatic.

It provides:

- inbound public reachability through public networking paths
- outbound Internet access through a single NAT Gateway
- reduced S3-related NAT dependency through the S3 Gateway Endpoint

This keeps the design relatively cost-aware while still supporting the current platform scope.

## Current Security and Simplicity Trade-Off

The VPC design uses a **single NAT Gateway**.

This is a deliberate lab trade-off:

- lower AWS cost
- simpler setup
- lower resilience than a full NAT-per-AZ production-grade design

This choice is acceptable for the current stage of the lab and should be understood as intentional rather than accidental.

## Current Strengths

The current VPC baseline already demonstrates:

- infrastructure as code network provisioning
- clean separation of public and private networking
- Kubernetes-compatible subnet tagging
- realistic multi-AZ layout
- cost-aware endpoint strategy
- a good base for EKS and public application exposure

## Current Constraints

The current VPC remains intentionally limited in scope.

Examples:

- one environment-centric layout
- no private interface endpoints enabled yet
- one NAT Gateway only
- no advanced network segmentation strategy
- no network policy layer documented here

These constraints are aligned with the lab principle of progressive implementation.

## Planned Evolution

Future networking evolution may include:

- additional private interface endpoints
- stronger cost versus security trade-off documentation
- richer governance around workload exposure patterns
- more explicit documentation of ingress-related network assumptions

These topics are currently part of the broader target architecture rather than the implemented baseline.

## Gap Analysis

Main gaps between the current VPC baseline and the target platform direction include:

- interface VPC endpoints are not yet enabled in the current environment
- the lab does not yet document advanced network isolation strategies
- networking runbooks are still limited
- platform-level governance over network exposure is not yet introduced

## Next Steps

The next logical steps for the VPC area are:

1. document how the VPC supports EKS and ingress more explicitly
2. document Terraform and Terragrunt operational workflows for networking changes
3. later revisit endpoint strategy when platform maturity increases
4. keep the current VPC baseline stable and well documented

## Summary

The VPC component is a strong, pragmatic, and Kubernetes-ready network foundation for the lab.

It balances simplicity, clarity, and affordability while still providing a realistic AWS base for the platform.
