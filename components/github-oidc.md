# GitHub OIDC Component

## Purpose

This document describes the GitHub OIDC federation component used in the EKS DevSecOps Lab.

It explains:

- how GitHub Actions authenticates to AWS
- which repositories are currently covered
- what roles are created
- why this design matters for security
- what remains planned for future maturity

## Why This Component Exists

A modern CI/CD pipeline should avoid long-lived AWS access keys stored in GitHub secrets whenever possible.

The lab uses GitHub OIDC federation so that GitHub Actions can obtain short-lived AWS credentials through IAM roles instead of static credentials.

This is one of the strongest current security choices in the infrastructure layer.

## Current Provisioning Model

The GitHub OIDC integration is provisioned by Terraform through the `live/dev/github-oidc` Terragrunt stack.

This stack creates:

- the GitHub OIDC provider
- one IAM role for the application repository
- one IAM role for the infrastructure repository

## Current OIDC Provider

The current design provisions an IAM OpenID Connect provider for:

- `https://token.actions.githubusercontent.com`

It uses `sts.amazonaws.com` as the client ID audience for role assumption.

This is the standard base for GitHub Actions to AWS federation.

## Current Roles

The component currently creates two roles:

### Application Role
Used by the application repository workflow.

Current scope:
- GitHub owner: `SBordier44`
- repository: `eks-devsecops-lab-app`
- environment-based subject
- ECR-focused permissions

### Infrastructure Role
Used by the infrastructure repository workflow.

Current scope:
- GitHub owner: `SBordier44`
- repository: `eks-devsecops-lab-infra`
- branch-based subject on `main`

This split is already a good sign of separation between delivery concerns.

## Current Permission Model

The application role is granted permissions specifically to interact with ECR.

This includes:
- getting an ECR authorization token
- pushing and pulling images on the targeted ECR repository

This keeps the application CI permissions narrowly focused on its expected job.

## Current Security Value

The GitHub OIDC design already provides major benefits:

- no long-lived AWS keys stored in GitHub for these workflows
- short-lived credentials issued at runtime
- repository-scoped trust relationships
- better alignment with least-privilege intent
- stronger CI security posture than static secrets

This is a very strong portfolio-grade design choice.

## Current Constraints

The current implementation remains intentionally focused.

Examples:

- only the app and infra repositories are explicitly covered
- role scoping can still be refined further over time
- workflow-level role segmentation is not yet deeply developed
- broader CI security hardening is still part of the target direction

These constraints are normal for the current stage of the lab.

## Planned Evolution

The target direction for this area may include:

- stronger role separation by workflow purpose
- tighter permission scoping
- richer documentation of trust boundaries
- stronger linkage with future secure container supply chain controls

These items are planned evolution topics rather than current validated capabilities.

## Gap Analysis

Main gaps between the current GitHub OIDC baseline and the target direction include:

- workflow-specific AWS roles could be split further
- CI trust and permission documentation can be expanded
- broader CI hardening remains part of the future roadmap
- the secure supply chain architecture is not yet fully codified

## Next Steps

The next logical steps for this area are:

1. document the current role assumption model in the runbooks
2. document the current trust conditions more explicitly for operations and auditability
3. connect future CI hardening themes to the supply chain documentation
4. later refine role segmentation if the platform scope grows

## Summary

The GitHub OIDC component is already a strong and modern security building block of the lab.

It shows that the platform does not rely on static CI cloud credentials and already adopts a more mature AWS authentication model for automation workflows.
