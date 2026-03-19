# Demo Application Component

## Purpose

This document describes the demo application component of the EKS DevSecOps Lab.

It explains:

- the role of the demo application in the platform
- how it is built and deployed
- how it integrates with the GitOps model
- how it validates the current platform baseline
- what remains planned for future maturity

## Current Role

The demo application is the reference workload of the lab.

Its role is not to demonstrate business complexity. Its role is to validate the platform end to end.

It currently serves as the practical test workload for:

- container image build and publication
- GitOps-driven deployment
- ingress exposure
- TLS automation
- secret synchronization from AWS Secrets Manager
- Kubernetes runtime validation

The demo application is therefore a platform validation workload first, and an application sample second.

## Current Application Model

The application repository contains a minimal Go application.

The current application exposes:

- a root endpoint
- a health endpoint

This is intentionally simple.

The goal is to keep the application understandable and stable so that the focus remains on the platform architecture and delivery chain.

## Current Container Model

The application uses a multi-stage Docker build.

The final runtime image is based on a distroless non-root image.

This is an important current strength because it demonstrates:

- a cleaner runtime image
- reduced attack surface
- non-root execution
- a more realistic container security baseline

## Current Delivery Model

The application delivery flow is currently the following:

1. code is pushed to the application repository
2. GitHub Actions builds the container image
3. the image is pushed to Amazon ECR
4. the GitOps repository is updated with the new image tag
5. ArgoCD reconciles the desired state into the cluster
6. the new version of the application is deployed

This gives the demo application a real CI to registry to GitOps to cluster delivery path.

## Current Kubernetes Deployment Model

The application is deployed through the GitOps repository using Kustomize.

The current model includes:

- a reusable base
- a `dev` overlay

The `dev` overlay currently defines:

- the namespace
- the application ingress
- the image override pointing to Amazon ECR
- the ExternalSecret integration
- the environment label

This keeps the application deployment simple and understandable while still demonstrating real GitOps layering.

## Current Exposure Model

The demo application is currently exposed publicly through:

- Traefik
- cert-manager
- Let's Encrypt
- OVH DNS

The current public hostname is:

- `app-demo.sedecy-it.net`

This means the application is already part of a real HTTPS delivery path.

## Current Secret Consumption Model

The application overlay includes an `ExternalSecret` resource that allows secret synchronization from AWS Secrets Manager into Kubernetes.

This demonstrates that the application is already part of the current secrets management baseline.

The current secret integration validates:

- IRSA
- External Secrets Operator
- AWS Secrets Manager connectivity
- synchronization into Kubernetes Secrets

## Current Strengths

The demo application already demonstrates:

- clean separation from infrastructure code
- CI-driven image build
- ECR-based image distribution
- GitOps-based deployment updates
- public HTTPS exposure
- secret synchronization integration
- a practical workload for validating platform components

## Current Constraints

The current demo application is intentionally minimal.

Examples:

- very small feature set
- one main deployment overlay
- no broader business logic scope
- no advanced application-level observability model documented yet
- no multi-service architecture

These constraints are intentional because the lab is focused on platform engineering rather than application complexity.

## Planned Evolution

The target direction for the demo application component may include:

- richer examples of secret consumption
- stronger application-level operational documentation
- future alignment with additional governance controls
- support for future policy validation scenarios

These items belong to future platform maturity rather than the validated current baseline.

## Gap Analysis

Main gaps between the current demo application baseline and the target direction include:

- the workload remains intentionally simple
- application-level operational documentation can be expanded
- broader policy-driven validation is not yet in place
- the application is currently more a platform validation workload than a richer service example

## Next Steps

The next logical steps for the demo application component are:

1. document the deployment and validation flow more explicitly in runbooks
2. keep the current workload stable as a platform validation target
3. later use the application as a test subject for future governance controls
4. expand documentation of secret consumption patterns if needed

## Summary

The demo application is a deliberately simple but strategically important component of the lab.

It already validates a significant part of the platform baseline and serves as the current reference workload for GitOps, ingress, TLS, and secret management.
