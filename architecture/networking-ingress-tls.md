# Networking, Ingress, and TLS Architecture

## Purpose

This document explains how application exposure works in the EKS DevSecOps Lab.

It covers:

- the current networking baseline
- ingress handling with Traefik
- DNS integration with OVH
- TLS automation with cert-manager and Let's Encrypt
- the current implemented state and the next planned improvements

## Scope

This document focuses on the application exposure path from the end user to the demo application.

It does not attempt to document every AWS networking detail in depth. Those lower-level details belong primarily to the infrastructure documentation.

## Current Exposure Model

The demo application is exposed through Kubernetes ingress.

The current exposure chain is:

1. the public DNS name resolves through OVH DNS
2. traffic reaches the Kubernetes ingress layer
3. Traefik routes the request to the demo application service
4. TLS is managed by cert-manager with Let's Encrypt

The public hostname currently used by the demo application is:

- `app-demo.sedecy-it.net`

## Current Ingress Controller Choice

The lab currently uses **Traefik** as the ingress controller.

This choice fits the current lab goals because it provides:

- a simple and modern ingress controller
- easy integration with Kubernetes ingress resources
- clean HTTPS routing support
- a good balance between capability and operational simplicity

At the current maturity stage of the lab, Traefik is a pragmatic choice.

## Current Ingress Configuration

The demo application `dev` overlay defines an ingress resource that includes:

- `ingressClassName: traefik`
- routing to the `demo-app` service on port 80
- TLS configuration for `app-demo.sedecy-it.net`
- cert-manager annotation referencing the `letsencrypt-prod` ClusterIssuer

This means the ingress object is already the convergence point between:
- routing
- hostname exposure
- certificate issuance

## Current TLS Model

TLS is currently automated through:

- **cert-manager**
- **Let's Encrypt**
- a production `ClusterIssuer`

The ingress resource references the production issuer through annotation and defines the TLS secret name used by the application host.

This gives the lab a real HTTPS exposure model rather than a self-signed or manually managed certificate approach.

## Why This Matters

This is a strong portfolio point because it demonstrates:

- real public DNS exposure
- real certificate automation
- real Kubernetes ingress management
- practical integration between ingress, DNS, and certificate automation

It moves the lab beyond a purely internal Kubernetes demo.

## Current DNS Role

OVH DNS is part of the current exposure chain.

Its responsibility is to resolve the public hostname toward the public entrypoint serving the Kubernetes ingress layer.

This makes DNS an external but essential dependency of the delivery platform.

## Current cert-manager Role

cert-manager is responsible for:

- requesting certificates
- handling ACME interactions with Let's Encrypt
- creating the TLS secret used by the ingress
- renewing certificates automatically

This removes the need for manual certificate lifecycle management.

## Current Strengths

The current networking and exposure baseline already demonstrates:

- a real public hostname
- automated TLS certificate issuance
- Kubernetes-native ingress routing
- a working integration between Traefik and cert-manager
- a realistic end-user access path over HTTPS

## Current Constraints

The current implementation intentionally remains small in scope.

Examples:
- one main exposed application
- one main hostname
- a simple ingress model
- no advanced traffic policies
- no service mesh
- no multi-environment public routing strategy yet

This is appropriate for the current lab stage.

## Planned Evolution

The target platform direction may later include:

- stronger ingress governance
- additional platform-level routing guardrails
- policy validation around ingress or TLS resources
- more explicit security controls around public exposure

At the moment, those remain planned topics rather than implemented capabilities.

## Gap Analysis

Main gaps between the current exposure baseline and the target platform direction include:

- ingress governance is still minimal
- no policy engine currently validates exposure patterns
- operational runbooks for networking and TLS are still incomplete
- architecture documentation is being expanded

## Next Steps

The next logical steps for this area are:

1. document ingress and TLS troubleshooting as a runbook
2. keep the current exposure path stable
3. later introduce governance controls progressively
4. document future policy expectations without complicating the current implementation too early

## Summary

The networking, ingress, and TLS layer is already one of the strongest parts of the lab baseline.

It provides a real external access path, real HTTPS, and a good demonstration of Kubernetes platform integration on AWS, while remaining simple enough to understand and maintain.
