# cert-manager Component

## Purpose

This document describes the cert-manager component of the EKS DevSecOps Lab.

It explains:

- the role of cert-manager in the platform
- how certificate automation works
- how cert-manager integrates with Traefik and Let's Encrypt
- what is already implemented
- what remains planned for future maturity

## Current Role

cert-manager is the certificate automation component of the lab.

Its role is to:

- request certificates
- interact with the ACME provider
- create and renew Kubernetes TLS secrets
- support HTTPS exposure for ingress resources

cert-manager removes the need for manually managing certificate lifecycle inside the platform.

## Current Deployment Model

cert-manager is deployed by ArgoCD as a dedicated application.

The current deployment uses the official Jetstack Helm chart and enables the cert-manager CRDs.

This means the certificate management layer is fully integrated into the GitOps model.

## Current Integration in the Platform

cert-manager currently works together with:

- Traefik as the ingress controller
- Let's Encrypt as the certificate authority
- dedicated ClusterIssuer resources
- the demo application ingress definition

The ingress references the production issuer and TLS secret configuration, allowing certificate issuance and renewal to happen automatically.

## Current Certificate Model

The platform already includes:

- a staging Let's Encrypt ClusterIssuer
- a production Let's Encrypt ClusterIssuer
- working production HTTPS for the demo application

This is an important practical strength because it proves that the platform handles real certificate issuance rather than a simulated or self-signed setup.

## Current Strengths

The current cert-manager component already demonstrates:

- GitOps-managed installation
- CRD-based Kubernetes integration
- automated certificate lifecycle management
- real ACME integration
- real HTTPS for a public hostname
- successful integration with Traefik

## Current Constraints

The current implementation remains intentionally simple.

Examples include:

- one main public application flow
- limited number of issuers
- no broader certificate governance model yet
- no policy-based validation around TLS resources yet

These constraints are normal for the current stage of the lab.

## Planned Evolution

The target direction for this area may include:

- stronger governance around ingress and TLS resources
- richer operational documentation for certificate lifecycle issues
- future policy validation of certificate-related resources
- broader platform guardrails around public exposure

These items are part of the planned architecture rather than the current validated baseline.

## Gap Analysis

Main gaps between the current cert-manager baseline and the target direction include:

- certificate governance is still limited
- TLS-related runbooks are not yet fully detailed
- no policy engine currently validates TLS-related resource patterns
- the current exposure model remains intentionally small in scope

## Next Steps

The next logical steps for the cert-manager component are:

1. document certificate troubleshooting workflows more explicitly
2. keep the current HTTPS path stable
3. later introduce governance and validation progressively
4. document operational assumptions around renewal and issuer usage

## Summary

The cert-manager component is already a strong and credible part of the platform.

It gives the lab real automated TLS capabilities and helps move the platform from a simple Kubernetes demo toward a more realistic cloud-native runtime environment.
