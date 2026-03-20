# Kyverno Component

## Purpose

This document describes the Kyverno component of the EKS DevSecOps Lab.

It explains:
- the role of Kyverno in the platform
- how it is currently deployed through ArgoCD
- which policies are already implemented and validated
- why the current adoption scope remains intentionally narrow
- what remains planned for future maturity

## Current Role

Kyverno is the policy engine of the lab.

Its current role is to:
- introduce Kubernetes-native governance progressively
- validate workload expectations without destabilizing the platform baseline
- provide policy visibility through audit-first controls
- prepare the platform for stronger future security and compliance guardrails

Kyverno is therefore not used yet as a broad enforcement layer. It is currently positioned as a progressive governance building block.

## Current Deployment Model

Kyverno is currently deployed through the GitOps repository and managed by ArgoCD.

The current installation model includes:
- a dedicated ArgoCD application named `kyverno`
- deployment into the `kyverno` namespace
- a separate ArgoCD application named `kyverno-policies`
- policy resources stored under a dedicated GitOps path

This separation keeps the component installation distinct from policy content and fits the current GitOps model of the lab.

## Current Policy Scope

The current Kyverno baseline is intentionally narrow.

At this stage:
- policies are limited to the `demo-app-dev` namespace
- policies target `Deployment` resources
- all current policies use `failureAction: Audit`
- the goal is to observe and validate, not to block

This approach is aligned with the overall lab philosophy:
- introduce one control at a time
- validate it on a real workload
- keep the platform stable
- avoid aggressive governance too early

## Current Policies

### require-container-resources-demo-app

This policy checks that application containers define:
- `resources.requests.cpu`
- `resources.requests.memory`
- `resources.limits.memory`

Its purpose is to establish a minimum workload resource declaration baseline.

### require-container-securitycontext-demo-app

This policy checks that application containers define:
- `securityContext.runAsNonRoot: true`
- `securityContext.allowPrivilegeEscalation: false`

Its purpose is to introduce a first runtime security expectation without enforcing a wider hardening model yet.

## Current Strengths

The current Kyverno baseline already demonstrates:
- policy engine deployment through GitOps
- clean separation between component installation and policy definitions
- non-blocking audit-first governance
- real policy evaluation against the demo workload
- a practical path toward stronger future platform controls

This is an important step because the lab now includes a real admission and policy layer rather than only planned governance intent.

## Current Constraints

The current implementation remains intentionally limited.

Examples include:
- policies only target one namespace
- policy coverage is intentionally small
- no broad multi-namespace governance model is in place yet
- no policy is currently enforced in blocking mode
- mutation and broader compliance-oriented controls are not yet introduced

These constraints are appropriate for the current maturity stage of the lab.

## Planned Evolution

The target direction for Kyverno may include:
- additional audit policies on workload security expectations
- broader namespace coverage once the baseline is proven stable
- gradual transition of selected policies from `Audit` to `Enforce`
- stronger alignment between platform security expectations and GitOps-managed workloads
- future coupling with supply chain and platform hardening topics

These items belong to the next maturity stages rather than the currently validated baseline.

## Gap Analysis

Main gaps between the current Kyverno baseline and the target direction include:
- policy scope is still limited to one application namespace
- current validation coverage is intentionally small
- no enforcement mode is used yet
- operational documentation is still lightweight
- broader governance strategy is not yet documented in depth

## Next Steps

The next logical steps for the Kyverno component are:
1. keep the current installation stable and observable
2. extend runbook guidance for policy report interpretation and troubleshooting
3. add new policies progressively and keep them in `Audit` first
4. validate several workload change cycles before considering any `Enforce` transition
5. broaden the governance scope only after the initial model has remained stable

## Summary

The Kyverno component is now an implemented and validated part of the platform.

It introduces a real policy engine into the lab while staying consistent with the project principles:
- progressive delivery
- GitOps-managed configuration
- real technical validation
- no unnecessary complexity
