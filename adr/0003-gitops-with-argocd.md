# ADR-0003 GitOps with ArgoCD

## Context
The lab needs a deployment model that is:

- declarative
- traceable
- reproducible
- easy to review

Deploying directly from CI to Kubernetes would tightly couple build workflows and runtime changes, and would reduce visibility of the desired cluster state.

## Decision
The platform uses GitOps with ArgoCD.

The desired Kubernetes state is stored in the dedicated GitOps repository.

ArgoCD is bootstrapped with a root application and reconciles:

- the demo application
- Traefik
- cert-manager
- Let's Encrypt issuers
- External Secrets resources

CI builds and publishes the application image, then updates the GitOps repository instead of deploying directly to the cluster.

## Consequences
This decision provides:

- a clear separation between build and deployment responsibilities
- Git as the source of truth for Kubernetes state
- better traceability of deployed versions
- automated reconciliation and drift correction
- a more credible platform engineering and GitOps operating model
