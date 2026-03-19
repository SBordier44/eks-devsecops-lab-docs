# ADR-0005 cert-manager with Let's Encrypt

## Context
The lab requires real HTTPS exposure for public application access.

Manual certificate management would add operational overhead and would not reflect modern Kubernetes platform practices.

The solution needs to be:

- automated
- Kubernetes-native
- compatible with GitOps
- realistic enough for a portfolio-grade platform

## Decision
cert-manager is used for certificate lifecycle automation.

Let's Encrypt is used as the certificate authority.

The platform currently defines:

- a staging ClusterIssuer
- a production ClusterIssuer

The demo application ingress references the production issuer and uses cert-manager-managed TLS secrets.

## Consequences
This decision provides:

- automated certificate issuance and renewal
- a real public HTTPS exposure model
- reduced manual certificate management effort
- a stronger and more realistic runtime baseline for the lab

It also creates a clean foundation for future ingress and TLS governance controls.
