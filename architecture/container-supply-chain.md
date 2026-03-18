# Container Supply Chain Architecture

## Purpose

This document describes the current and target container supply chain model of the EKS DevSecOps Lab.

It explains:

- what is already implemented in the build and delivery chain
- how container images move from source code to Kubernetes
- which security controls are already present
- which supply chain controls are still planned

## Why Container Supply Chain Matters

A Kubernetes platform is only as trustworthy as the software delivery chain that feeds it.

For this lab, the container supply chain is treated as a progressive maturity topic rather than a fully solved problem from day one.

The objective is to show a realistic path from:

- basic but clean container build and delivery
  to
- stronger security, verification, and governance controls over time

## Current Implemented Baseline

The current implemented supply chain already includes:

- source code stored in GitHub
- container image build through GitHub Actions
- image publication to Amazon ECR
- immutable image tags in ECR
- scan on push enabled in ECR
- GitOps-based image deployment update
- workload deployment through ArgoCD
- non-root final runtime image for the demo application

This provides a real, working, and already credible baseline for a portfolio project.

## Current Build Model

The demo application repository contains:

- the application source code
- a Dockerfile
- a GitHub Actions workflow to build and push the image

The image build is triggered from GitHub Actions and published to Amazon ECR.

After the image is published, the pipeline updates the image reference in the GitOps repository so that ArgoCD can reconcile the new desired state.

This means the current build chain already separates:

- source code ownership
- artifact generation
- deployment state management
- runtime reconciliation

## Current Runtime Image Model

The demo application uses a multi-stage container build.

The current final image is based on a distroless non-root runtime image.

This is an important current security strength because it reduces the runtime attack surface and avoids running the application as root.

## Current Registry Model

Amazon ECR is the current container registry.

The infrastructure already configures useful registry-side controls, including:

- immutable tags
- image scanning on push
- encryption
- lifecycle management

This means the registry is already more than a simple image storage endpoint.

## Current Delivery Path

The current end-to-end container flow is:

1. source code is pushed to the application repository
2. GitHub Actions builds the container image
3. the image is pushed to Amazon ECR
4. the GitOps repository is updated with the new image tag
5. ArgoCD reconciles the Kubernetes desired state
6. the new container image is deployed to the cluster

This already demonstrates a real CI to registry to GitOps to cluster chain.

## Current Security Strengths

The current supply chain already demonstrates several good practices:

- no direct manual image deployment to the cluster
- image publication through CI
- immutable tags in the registry
- registry-side scan on push
- non-root runtime image
- GitOps-based deployment updates
- clear separation between build and deployment responsibilities

## Current Constraints

The current supply chain intentionally remains limited in scope.

Examples:
- one application pipeline
- no signature verification yet
- no attestations yet
- no SBOM strategy formally documented yet
- no admission policy enforcing image provenance yet
- no broader promotion workflow across multiple environments yet

This is acceptable for the current maturity stage of the lab.

## Planned Evolution

The target platform direction includes stronger container supply chain controls.

Planned themes include:

- stronger CI hardening
- better vulnerability management across the build and runtime chain
- image signature strategy
- provenance and attestation strategy
- policy-aware deployment controls
- tighter relationship between supply chain status and cluster admission decisions

These items are part of the target architecture, not the currently validated baseline.

## Gap Analysis

Main gaps between the current implemented supply chain and the target direction include:

- no image signing process yet
- no attestation or provenance workflow yet
- no documented SBOM generation and usage model yet
- no policy engine validating deployment provenance yet
- no formal secure promotion flow across multiple stages yet

## Why This Still Matters Today

Even without the advanced controls already in place, the current supply chain remains a strong foundation because it already demonstrates:

- infrastructure-backed registry design
- CI-generated images
- Git-driven deployment updates
- Kubernetes reconciliation through ArgoCD
- a realistic path for progressive hardening

This is often more valuable than pretending a full enterprise-grade supply chain already exists.

## Next Steps

The next logical steps for this area are:

1. document the current build and publish workflow in operational detail
2. document the current ECR security controls more explicitly
3. introduce the secure container supply chain as a planned architecture theme in ADRs
4. later connect future policy enforcement with deployment governance
5. progressively improve the maturity of artifact trust and verification

## Summary

The current container supply chain of the lab is already functional, credible, and aligned with a GitOps delivery model.

It is not yet a fully hardened enterprise-grade chain, but it is intentionally positioned as a strong baseline with a clear path toward more advanced supply chain security capabilities.
