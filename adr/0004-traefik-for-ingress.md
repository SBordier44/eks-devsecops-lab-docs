# ADR-0004 Traefik for Ingress

## Context
The lab requires an ingress controller to expose Kubernetes services publicly over HTTP and HTTPS.

The chosen solution needs to be:

- simple to operate
- Kubernetes-native
- compatible with GitOps
- suitable for a progressive lab without unnecessary complexity

## Decision
Traefik is used as the ingress controller of the platform.

It is deployed through ArgoCD using the official Helm chart and is used as the current ingress class for the demo application.

Traefik is integrated with:

- Kubernetes ingress resources
- cert-manager
- the current public application exposure path

## Consequences
This decision provides:

- a modern and practical ingress layer
- a simple integration path for public application exposure
- a clean fit with the current GitOps model
- good operational simplicity for the current maturity stage of the lab

It also means that future ingress governance and policy controls will build on Traefik as the current public routing baseline.
