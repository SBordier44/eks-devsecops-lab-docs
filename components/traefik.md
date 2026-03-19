# Traefik Component

## Purpose

This document describes the Traefik component of the EKS DevSecOps Lab.

It explains:

- the role of Traefik in the platform
- how it is deployed
- how it fits into the ingress and HTTPS model
- what is already implemented
- what remains planned for later maturity stages

## Current Role

Traefik is the current ingress controller of the lab.

Its role is to:

- receive external traffic for Kubernetes ingress resources
- route requests to services inside the cluster
- act as the main traffic entrypoint for the demo application
- work together with cert-manager for HTTPS exposure

Traefik is therefore the current public routing layer of the platform.

## Current Deployment Model

Traefik is deployed by ArgoCD as a dedicated application.

The current deployment uses the official Traefik Helm chart.

This means Traefik is managed declaratively through the GitOps model rather than installed manually.

## Current Integration in the Platform

The demo application ingress currently uses:

- `ingressClassName: traefik`
- a public hostname
- cert-manager annotation for certificate issuance
- TLS secret configuration

This means Traefik is already part of a fully working public routing path.

## Current Benefits

Traefik is a good fit for the current lab stage because it provides:

- a modern ingress controller
- simple Kubernetes integration
- a clean HTTPS routing path
- a good balance between features and operational simplicity

This makes it a pragmatic and credible choice for the current platform baseline.

## Current Strengths

The current Traefik component already demonstrates:

- GitOps-based deployment
- real ingress controller integration
- support for public application exposure
- clean integration with cert-manager
- practical use in front of a real workload

## Current Constraints

The current implementation remains intentionally focused.

Examples include:

- one main ingress controller
- one main application routing scenario
- no broader traffic governance layer yet
- no advanced routing patterns documented yet
- no policy checks around ingress definitions yet

These constraints are acceptable for the current maturity stage of the lab.

## Planned Evolution

The target direction for the ingress layer may include:

- stronger ingress governance
- policy validation around public exposure
- richer documentation of routing assumptions and security expectations
- future platform guardrails for ingress resources

These items are part of the planned architecture rather than the validated current baseline.

## Gap Analysis

Main gaps between the current Traefik baseline and the target direction include:

- ingress governance remains limited
- advanced routing and security documentation is still minimal
- no policy engine currently validates ingress practices
- operational runbooks still need expansion

## Next Steps

The next logical steps for the Traefik component are:

1. document ingress and troubleshooting workflows more explicitly
2. keep the current ingress path stable
3. later introduce governance around public exposure
4. document future ingress policy expectations without complicating the current baseline too early

## Summary

The Traefik component is already a strong part of the current platform baseline.

It provides a simple, modern, and credible ingress layer for the lab while keeping the public application routing model understandable.
