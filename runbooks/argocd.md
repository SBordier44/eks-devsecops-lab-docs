# ArgoCD Runbook

## Purpose

This runbook provides practical guidance for operating and troubleshooting ArgoCD in the EKS DevSecOps Lab.

It focuses on the current platform baseline and helps answer questions such as:

- is ArgoCD healthy?
- are applications synchronized?
- why is a deployment not updating?
- where should I look first when GitOps reconciliation fails?

## Scope

This runbook covers:

- ArgoCD health checks
- application synchronization checks
- GitOps reconciliation troubleshooting
- common failure paths in the current lab

It does not attempt to describe every ArgoCD feature in depth.

## Current Context

ArgoCD is the GitOps reconciliation engine of the lab.

It currently manages:

- the demo application
- Traefik
- cert-manager
- Let's Encrypt issuers
- External Secrets CRDs
- External Secrets Operator
- the AWS ClusterSecretStore resource

## Basic Health Checks

Start with the most basic validation steps.

### Check ArgoCD namespace resources

```bash
kubectl -n argocd get pods
kubectl -n argocd get svc
kubectl -n argocd get applications
```

What to look for:

- pods are running
- no obvious crash loops
- applications are listed

### Check application status

```bash
kubectl -n argocd get applications
```

What to look for:

- `Synced`
- `Healthy`

If an application is not healthy or not synced, inspect it further.

### Describe a specific application

```bash
kubectl -n argocd describe application <application-name>
```

This is often the fastest way to see:

- sync issues
- missing resources
- health errors
- reconciliation messages

## Typical Validation Workflow

When a deployment is expected to happen but does not, use this order:

1. confirm the GitOps repository contains the expected change
2. confirm ArgoCD sees the application
3. confirm the application sync status
4. inspect the application events and message fields
5. confirm the Kubernetes resources were actually applied
6. inspect the target namespace resources

This sequence avoids jumping too quickly into low-level debugging.

## Current Common Troubleshooting Scenarios

### Scenario 1 — Application OutOfSync

Possible reasons:

- Git change not yet reconciled
- Kustomize render issue
- chart issue
- resource drift in the cluster
- CRD ordering issue for operator-style components

Useful commands:

```bash
kubectl -n argocd get applications
kubectl -n argocd describe application <application-name>
```

### Scenario 2 — Application Healthy but workload not behaving correctly

Possible reasons:

- resource applied successfully but workload container failing
- secret missing at runtime
- ingress or service issue
- application-specific runtime issue

Useful commands:

```bash
kubectl -n <namespace> get all
kubectl -n <namespace> describe deployment <deployment-name>
kubectl -n <namespace> logs deployment/<deployment-name>
```

### Scenario 3 — New platform component fails during installation

Possible reasons:

- CRD not present yet
- Helm chart order issue
- namespace creation issue
- service account or IAM integration issue

This was especially relevant during the External Secrets rollout and is a good reminder that CRD-heavy operators may require extra care in GitOps ordering.

### Scenario 4 — Image updated in ECR but application did not change

Possible reasons:

- the GitOps repository was not updated
- ArgoCD did not reconcile yet
- the image field in the overlay was not changed as expected
- the deployment was not rolled out correctly

Validation path:

1. check the image reference in the GitOps repository
2. check the ArgoCD application status
3. check the deployed image in Kubernetes

Useful commands:

```bash
kubectl -n demo-app-dev get deployment demo-app -o yaml
```

## Operational Checks for the Current Lab

The following checks are useful after any important platform change.

### Check all ArgoCD-managed applications

```bash
kubectl -n argocd get applications
```

### Check target namespaces

```bash
kubectl get ns
kubectl -n demo-app-dev get all
kubectl -n external-secrets get all
```

### Check application ingress path

```bash
kubectl -n demo-app-dev get ingress
kubectl -n demo-app-dev get svc
```

## Good Practices

For the current lab, the most useful practices are:

- treat the GitOps repository as the source of truth
- avoid manual cluster changes that bypass Git
- validate application sync after each important platform change
- debug from Git intent to cluster result, not the other way around
- document recurring failure patterns when they appear

## Known Current Constraints

The current ArgoCD model is intentionally simple.

That simplicity is useful, but it also means:

- some problems are still diagnosed manually
- not all failure patterns are documented yet
- runbooks are still growing with the platform

This is acceptable as long as operational habits remain disciplined.

## Next Steps

The next logical improvements for this runbook are:

1. add more application-specific troubleshooting examples
2. add rollback-oriented procedures
3. expand diagnostics for future governance components
4. document recovery workflows for more platform services

## Summary

ArgoCD is the operational center of the GitOps model in this lab.

When something looks wrong, the best approach is to validate the path from Git intent to ArgoCD reconciliation to Kubernetes runtime state in a structured order.
