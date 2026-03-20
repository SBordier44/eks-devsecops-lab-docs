# Security Model

## Purpose

This document describes the current and target security model of the EKS DevSecOps Lab.

Its goal is to explain:
- which security controls are already implemented
- which security choices are deliberate trade-offs
- which security capabilities are still planned
- how security is being introduced progressively across the platform

## Security Philosophy

The lab is not presented as a fully hardened enterprise platform.

Instead, it follows a progressive security approach:
- establish a clean and realistic technical baseline
- introduce practical controls early where they bring clear value
- avoid over-engineering the platform too early
- make trade-offs explicit
- improve security iteratively as the platform matures

This approach is intentional and aligned with the overall architecture principles of the lab.

## Current Implemented Security Baseline

The current validated platform already includes meaningful security-related controls across several layers.

### Infrastructure Security

Current implemented elements include:
- infrastructure as code with Terraform and Terragrunt
- remote state stored in S3 with DynamoDB locking
- no long-lived AWS credentials in GitHub for CI access
- GitHub OIDC federation to AWS
- IRSA enabled for Kubernetes-to-AWS access patterns

### Registry and Image Security

Current implemented elements include:
- Amazon ECR as the image registry
- immutable tags
- scan on push
- encryption at rest
- lifecycle rules to control image sprawl

### Application Runtime Security

Current implemented elements include:
- a non-root runtime container image for the demo application
- a distroless final image approach
- Kubernetes deployment through GitOps rather than manual ad hoc runtime operations
- runtime resource requests and limits defined on the demo application
- workload-level `securityContext` already defined for the demo application

### Delivery and Change Security

Current implemented elements include:
- separation of concerns between infra, app, GitOps, and docs repositories
- Git-based desired state for Kubernetes
- ArgoCD automated reconciliation
- traceable deployment changes through Git commits

### Exposure and TLS Security

Current implemented elements include:
- public HTTPS exposure
- automated certificate lifecycle with cert-manager
- Let's Encrypt production certificates
- ingress routing through Traefik

### Secrets Security

Current implemented elements include:
- AWS Secrets Manager as the upstream secret source
- External Secrets Operator
- IRSA-based authentication from Kubernetes to AWS
- no static AWS access keys stored in Kubernetes manifests for this integration

### Governance and Admission Security

Current implemented elements include:
- Kyverno deployed through ArgoCD
- GitOps-managed policy definitions
- an audit-first validation strategy
- first `ClusterPolicy` controls applied to `demo-app-dev`
- current checks on workload resource definitions and selected container `securityContext` fields

## Current Security Strengths

The current platform already demonstrates several strong security signals for a lab environment:
- modern cloud authentication patterns
- reduction of static credentials
- safer runtime image choices
- real TLS automation
- externalized secret management
- GitOps-based deployment discipline
- initial admission-time governance controls
- clean repository separation by responsibility

## Current Security Trade-Offs

The lab also includes explicit trade-offs.

These are not hidden and should be understood as part of the current maturity stage.

Examples include:
- the platform is optimized for clarity and learning value, not maximum enterprise hardening
- current Kyverno coverage remains intentionally narrow
- the first policies are non-blocking and limited to `Audit`
- the current environment focus remains primarily on a single development baseline
- advanced runtime and admission controls are only partially introduced
- supply chain hardening is still at an early stage

These trade-offs are acceptable as long as they are documented honestly.

## Planned Security Evolution

The target platform direction includes several important planned security improvements.

### Platform Governance

Planned:
- broader Kyverno policy coverage
- additional audit-first controls
- later progressive policy enforcement
- stronger workload governance beyond the initial demo scope

### Secure Container Supply Chain

Planned:
- stronger CI security controls
- better artifact trust strategy
- image signing or attestation direction
- tighter coupling between supply chain state and cluster policy

### Platform Hardening

Planned:
- more explicit workload security expectations
- stronger policy coverage
- richer security ADRs
- expanded security runbooks

### Operational Security Documentation

Planned:
- troubleshooting guides
- validation procedures
- security-oriented operational checks
- documentation of security assumptions and limitations

## Gap Analysis

Main gaps between the current security baseline and the target security model include:
- only a small initial subset of workload security rules is currently codified
- secure container supply chain controls remain limited
- runtime governance is still partial
- security documentation is growing but not yet complete
- the lab still needs more explicit codification of security guardrails

## Why This Security Model Is Credible

This security model is credible because it does not pretend that every security topic is already solved.

Instead, it shows:
- real implemented controls
- real design decisions
- real cloud-native patterns
- real trade-offs
- a clear and professional maturity path

That is more valuable than presenting an unrealistic “fully secure by default” narrative.

## Next Steps

The next logical steps for the security model are:
1. complete the current architecture documentation baseline
2. document the current Kyverno controls more explicitly in component and runbook documents
3. keep the first audit policies stable before widening scope
4. document the secure container supply chain target architecture more deeply
5. expand the ADR set to capture major security decisions

## Summary

The current security model of the lab is already meaningful and technically credible.

It is best understood as a progressive DevSecOps baseline: strong enough to demonstrate real engineering practices today, and structured enough to evolve toward broader governance and security maturity over time.
