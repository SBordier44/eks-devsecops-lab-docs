# AWS Infrastructure with Terraform and Terragrunt

## Purpose

This document describes how the AWS infrastructure layer of the lab is designed and managed using Terraform and Terragrunt.

The main goals are:

- reproducibility
- clarity of environment structure
- modularity
- cost control
- secure access patterns
- progressive evolution without over-engineering

## Why Terraform and Terragrunt

Terraform is used to define and provision AWS resources as code.

Terragrunt is used to structure the live environment, centralize shared configuration, reduce duplication, and manage stack dependencies more cleanly.

This combination makes the infrastructure easier to maintain as the lab grows.

## Repository Structure

The infrastructure repository is split into two major parts:

- `modules/` for reusable Terraform modules
- `live/dev/` for the concrete development environment

Current live stacks include:

- `vpc`
- `eks`
- `ecr`
- `github-oidc`

## Environment Configuration

The `live/dev/root.hcl` file centralizes key shared values:

- environment: `dev`
- region: `eu-west-3`
- project: `eks-devsecops-lab`

It also configures Terraform remote state with:

- S3 bucket for Terraform state
- DynamoDB table for Terraform locking
- encryption enabled

This provides both state durability and locking protection during concurrent operations.

## VPC Design

The VPC stack provisions:

- a dedicated VPC
- 2 Availability Zones
- public subnets
- private subnets
- Internet Gateway
- a single NAT Gateway
- an S3 Gateway Endpoint

### Subnet Strategy

The VPC layout is intentionally simple and Kubernetes-ready.

It provides a clean network foundation for:

- worker nodes
- public ingress exposure
- private workloads
- future platform growth

### Kubernetes Integration

Subnets are tagged to support Kubernetes load balancer integration and EKS networking expectations.

### Cost Control Choice

The platform uses **one single NAT Gateway** instead of one NAT Gateway per Availability Zone.

This is a deliberate trade-off:

- lower AWS cost
- simpler lab setup
- lower resilience than a fully multi-AZ NAT design

### Endpoint Strategy

S3 connectivity is optimized through a Gateway Endpoint.

This reduces part of the NAT-related traffic and fits the lab’s cost-aware design.

## EKS Cluster Design

The EKS stack provisions:

- a dedicated cluster for the lab
- managed worker nodes
- IAM Roles for Service Accounts support
- core managed add-ons

### Cluster Philosophy

The EKS design aims to be:

- realistic enough for platform engineering practice
- simple enough to remain understandable
- affordable enough to keep the lab sustainable

### Enabled Capabilities

The infrastructure already supports:

- IRSA
- managed add-ons
- GitOps deployment compatibility
- future platform component integration

### Logging and Cost Awareness

CloudWatch and control plane logging choices are intentionally conservative to keep the lab affordable while still allowing progressive observability improvements later.

## ECR Design

The ECR stack provisions the application image registry used by the demo application delivery pipeline.

Current characteristics include:

- immutable tags
- image scanning on push
- managed encryption
- lifecycle policy support

### Why This Matters

This provides a clean base for:

- reproducible deployments
- safer image management
- progressive secure supply chain improvements

## GitHub OIDC Federation Design

The `github-oidc` stack establishes trust between GitHub Actions and AWS without storing long-lived AWS credentials in GitHub.

This is a key security design choice in the lab.

### Current Benefits

This approach provides:

- short-lived credentials
- improved CI security posture
- cleaner repository security model
- better alignment with modern cloud delivery practices

### Current Usage

OIDC is used to allow GitHub Actions workflows to interact with AWS services such as ECR and infrastructure-related resources.

## Dependency Model

The Terragrunt live configuration defines dependencies between stacks so that infrastructure can be applied in a coherent order.

This makes the environment easier to reason about and supports safer iterative changes.

## Security Principles

The infrastructure layer follows several important principles:

- no long-lived AWS credentials stored in repositories
- remote state protection and locking
- least-privilege intent where possible
- immutable image registry behavior
- short-lived CI authentication with OIDC
- IRSA readiness for Kubernetes-to-AWS access patterns

## Cost and Simplicity Trade-Offs

This lab is intentionally not designed as a full enterprise production platform.

It favors a controlled balance between realism and affordability:

- a small development-oriented footprint
- simplified networking trade-offs
- conservative log settings
- one primary environment
- progressive platform maturity instead of full upfront complexity

## Current Strengths

The current infrastructure design already demonstrates:

- layered IaC organization
- reusable Terraform modules
- Terragrunt live environment management
- secure CI-to-AWS authentication with OIDC
- Kubernetes-ready network preparation
- cost-aware AWS decisions
- a strong foundation for future platform hardening

## Planned Infrastructure Evolution

The current infrastructure already provides a strong AWS foundation, but several platform and security topics are part of the target evolution.

These include:

- stronger IAM scoping and role separation
- additional private connectivity patterns where relevant
- more advanced policy and governance controls
- future platform security services
- stronger secure supply chain controls around image build, verification, and deployment

These topics are intentionally documented as future evolution items rather than current implemented capabilities.

## Gap Analysis

The current infrastructure baseline is solid, but it does not yet fully cover all the ambitions described in the broader platform direction.

Main gaps include:

- infrastructure-level hardening can be pushed further
- secure supply chain controls are still at an early maturity level
- policy and governance controls are not yet fully introduced
- future platform security integrations remain to be implemented
- operational architecture documentation is still being expanded

## Next Steps

The next logical improvements for the infrastructure layer are:

- complete architecture documentation for each AWS-related component
- document the real Terragrunt dependency flow more explicitly
- strengthen the narrative around security choices and trade-offs
- support future Kyverno and platform governance integration
- progressively document secure supply chain design decisions

## Source of Truth

This document is based on the current implementation in `eks-devsecops-lab-infra`.

For the current state, implementation should be treated as the primary source of truth.

For future direction, repository README files, roadmap elements, and upcoming architecture decisions should be used as complementary planning inputs.
