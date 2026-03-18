# Architecture Roadmap

## Purpose

This document tracks the evolution of the EKS DevSecOps Lab from its current validated baseline to its target platform direction.

Its goal is to distinguish clearly between:

- what is already implemented and validated
- what is planned next
- what belongs to the broader target architecture vision

This distinction keeps the documentation transparent, professional, and useful for engineering review, operations, and portfolio presentation.

## Delivery Model

The lab evolves progressively.

New capabilities are introduced one at a time, validated, and documented before moving to the next platform concern.

This approach is intentional:

- it keeps the platform understandable
- it reduces unnecessary complexity
- it preserves stability of the current baseline
- it makes architecture decisions easier to explain and justify

## Status Legend

- **Implemented**: deployed and validated in the lab
- **In progress**: currently being implemented or documented
- **Planned**: identified and expected in upcoming iterations
- **Candidate**: under consideration, not yet committed

## Current Implemented Baseline

The lab currently includes the following validated capabilities.

### Infrastructure Foundation
Status: **Implemented**

- AWS infrastructure provisioned with Terraform and Terragrunt
- remote state management with S3 and DynamoDB
- VPC and networking foundation
- Amazon EKS cluster
- Amazon ECR registry
- GitHub OIDC federation to AWS

### Application Delivery
Status: **Implemented**

- demo application source repository
- container image build pipeline
- image publication to Amazon ECR
- GitOps image update flow

### GitOps Platform
Status: **Implemented**

- ArgoCD bootstrap
- GitOps deployment model
- demo application deployment through GitOps

### Ingress and TLS
Status: **Implemented**

- Traefik ingress controller
- cert-manager
- Let's Encrypt production certificates
- HTTPS exposure for the demo application

### Secrets Management
Status: **Implemented**

- External Secrets Operator
- IRSA-based AWS access from Kubernetes
- AWS Secrets Manager integration
- ExternalSecret to Kubernetes Secret synchronization

## Current Documentation Baseline

Status: **In progress**

The documentation repository now has a structured foundation with architecture, component, runbook, ADR, and roadmap sections.

The next objective is to progressively document the entire lab architecture in a way that is both technically accurate and portfolio-grade.

## Planned Next Capabilities

### Platform Governance with Kyverno
Status: **Planned**

Goal:
Introduce policy validation and governance progressively without destabilizing the current platform baseline.

Expected scope:
- Kyverno deployment through GitOps
- first non-blocking audit policies
- later progressive enforcement of selected controls

### Secure Container Supply Chain
Status: **Planned**

Goal:
Strengthen the security posture of the application delivery pipeline and deployment chain.

Expected themes:
- stronger build security controls
- image scanning improvements
- signature and attestation strategy
- policy-aware deployment controls

### Broader Platform Security Controls
Status: **Planned**

Goal:
Increase platform maturity with additional guardrails and clearer security boundaries.

Expected themes:
- stronger workload governance
- additional Kubernetes policy controls
- clearer runtime security expectations
- more explicit platform hardening decisions

### Observability and Operations
Status: **Planned**

Goal:
Improve platform operability and troubleshooting depth.

Expected themes:
- richer runbooks
- stronger troubleshooting workflows
- broader operational guidance
- more complete architecture views

## Candidate Future Topics

Status: **Candidate**

These topics may become relevant later depending on the evolution of the lab:

- advanced policy sets
- stronger multi-environment design
- broader compliance-oriented controls
- more advanced platform team patterns
- additional security automation layers

## Gap Analysis

The lab already demonstrates a strong end-to-end baseline, but several gaps remain between the current validated platform and the target platform direction.

### Current Strengths
- real AWS infrastructure as code
- real GitOps workflow
- real HTTPS exposure with automated certificates
- real AWS-to-Kubernetes secret synchronization
- clear repository separation by responsibility

### Main Gaps
- Kyverno is not yet introduced
- secure container supply chain is not yet documented as implemented
- governance and policy layers are still limited
- runbooks are not yet complete
- several architecture documents still need to be written

## Near-Term Next Steps

The next logical documentation and platform steps are:

1. complete the architecture documentation baseline
2. document the implemented infrastructure and delivery flows in more depth
3. introduce Kyverno in a dedicated next session
4. document the future secure container supply chain direction explicitly as planned architecture
5. expand the component-level and runbook-level documentation

## Documentation Rule

Every architecture document in this repository should, whenever relevant, distinguish between:

- **Current state**
- **Planned capabilities**
- **Gap analysis**
- **Next steps**

This rule ensures consistency across the whole documentation set.

## Relationship with Repository READMEs

Some project repositories already describe future capabilities that are not yet fully implemented.

This roadmap uses those elements as part of the **target platform direction**, while keeping the **implemented baseline** tied to validated, real platform behavior.

## Summary

The EKS DevSecOps Lab is intentionally evolving from a strong practical baseline toward a broader platform engineering target.

The purpose of this roadmap is not only to plan future work, but also to show architectural maturity by making implementation status explicit and traceable.
