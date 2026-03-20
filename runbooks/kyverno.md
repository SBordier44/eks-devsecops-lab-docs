# Kyverno Runbook

## Purpose

This runbook provides practical guidance for operating and troubleshooting Kyverno in the EKS DevSecOps Lab.

It focuses on the current audit-first policy model and helps answer questions such as:
- is Kyverno installed and healthy?
- are the current policies present?
- are policy reports being generated?
- why does a policy show failures?
- how should Kyverno changes be rolled back safely?

## Scope

This runbook covers:
- Kyverno installation health checks
- ArgoCD application checks
- ClusterPolicy validation
- PolicyReport inspection
- common troubleshooting scenarios in the current lab
- simple rollback guidance

It does not attempt to cover all Kyverno features in depth.

## Current Context

The current Kyverno setup includes:
- one ArgoCD application for the Kyverno component
- one ArgoCD application for Kyverno policies
- one current target namespace: `demo-app-dev`
- two current `ClusterPolicy` resources
- audit-only validation rules

The current objective is visibility and progressive validation, not broad enforcement.

## Basic Health Checks

### Check ArgoCD applications

```bash
kubectl -n argocd get applications
kubectl -n argocd describe application kyverno
kubectl -n argocd describe application kyverno-policies
```

What to look for:
- both applications exist
- both applications are `Synced`
- both applications are `Healthy`

### Check Kyverno namespace resources

```bash
kubectl -n kyverno get pods
kubectl -n kyverno get svc
kubectl -n kyverno get deploy
```

What to look for:
- pods are running
- no crash loops
- no repeated restarts
- the admission controller is healthy

### Check Kyverno logs

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --tail=200
```

What to look for:
- no repeated startup failures
- no resource discovery errors
- no obvious admission controller crash pattern

## Check the Current Policies

```bash
kubectl get clusterpolicy
kubectl describe clusterpolicy require-container-resources-demo-app
kubectl describe clusterpolicy require-container-securitycontext-demo-app
```

What to look for:
- both policies exist
- both policies are accepted by the cluster
- `failureAction` is `Audit`
- rule matching scope is limited to `demo-app-dev`

## Check Policy Reports

```bash
kubectl get policyreport -n demo-app-dev
kubectl describe policyreport -n demo-app-dev
```

What to look for:
- at least one `PolicyReport` is present
- results are being generated for the current workload
- entries show either `pass` or `fail`
- failures are informative and do not block deployment because current policies are in `Audit`

## Validate the Current Demo Workload

```bash
kubectl -n demo-app-dev get deploy
kubectl -n demo-app-dev describe deployment demo-app
```

What to look for:
- the deployment exists
- container resource requests and memory limit are present
- `runAsNonRoot: true` is present
- `allowPrivilegeEscalation: false` is present

This check is useful when a report failure appears and must be correlated with the live workload spec.

## Typical Troubleshooting Scenarios

### Scenario 1 — Kyverno application is OutOfSync or not Healthy

Possible reasons:
- Git change not reconciled yet
- Helm chart rendering issue
- namespace or RBAC issue
- ArgoCD reconciliation problem

Useful commands:

```bash
kubectl -n argocd get applications
kubectl -n argocd describe application kyverno
kubectl -n argocd describe application kyverno-policies
kubectl -n kyverno get all
```

### Scenario 2 — Kyverno pods are running but no policies appear

Possible reasons:
- the `kyverno-policies` ArgoCD application is not synced
- the policy path in the GitOps repository is incorrect
- a YAML rendering or formatting issue prevents reconciliation

Useful commands:

```bash
kubectl -n argocd describe application kyverno-policies
kubectl get clusterpolicy
```

### Scenario 3 — Policies exist but no PolicyReport appears

Possible reasons:
- the targeted namespace has no matching resources
- the resource kind does not match the policy scope
- the background evaluation has not completed yet
- the report is present under a different generated name

Useful commands:

```bash
kubectl get policyreport -n demo-app-dev
kubectl -n demo-app-dev get deploy
kubectl describe clusterpolicy require-container-resources-demo-app
kubectl describe clusterpolicy require-container-securitycontext-demo-app
```

### Scenario 4 — Policy report shows failures

Possible reasons:
- resource requests are missing
- memory limit is missing
- `runAsNonRoot` is not set to `true`
- `allowPrivilegeEscalation` is not set to `false`

Useful commands:

```bash
kubectl describe policyreport -n demo-app-dev
kubectl -n demo-app-dev get deployment demo-app -o yaml
```

The current expected remediation path is:
1. identify the missing or incorrect field
2. correct the workload manifest in the GitOps repository
3. let ArgoCD reconcile the change
4. verify that the next report result becomes compliant

### Scenario 5 — Need to roll back a policy quickly

Current rollback approach:
- remove the policy file from the GitOps repository, or remove its reference from the Kyverno policy kustomization
- let ArgoCD reconcile the change
- verify that the policy disappears from the cluster

Useful commands:

```bash
kubectl get clusterpolicy
kubectl -n argocd get applications
```

## Validation Sequence for the Current Lab

When debugging Kyverno, the most useful order is:
1. check ArgoCD application health
2. check Kyverno pods
3. check admission controller logs
4. check `ClusterPolicy` resources
5. check `PolicyReport` resources
6. inspect the targeted workload manifest
7. only then change policy scope or policy behavior

This sequence keeps troubleshooting structured and prevents unnecessary changes.

## Good Practices

For the current lab, the most useful practices are:
- keep new policies in `Audit` first
- scope new policies narrowly before broadening them
- validate against one real workload before expanding coverage
- use GitOps as the only source of truth for policy changes
- avoid manual ad hoc policy changes directly in the cluster
- document the reason for any future transition from `Audit` to `Enforce`

## Known Current Constraints

The current Kyverno baseline is intentionally limited:
- one namespace is targeted
- only validation policies are used
- policy coverage is small
- no enforcement mode is enabled
- no broader governance model is documented yet

This is acceptable because the goal is to keep the platform stable and understandable while proving the governance path.

## Next Steps

The next logical improvements for this runbook are:
1. add more examples of PolicyReport interpretation
2. document controlled expansion to additional policies
3. document future `Audit` to `Enforce` decision criteria
4. connect Kyverno guidance with broader security and supply chain documentation later

## Summary

Kyverno is now part of the validated platform baseline.

The safest operating model for the current lab is:
- keep the component healthy
- introduce one policy at a time
- stay in `Audit` first
- read reports carefully
- correct workloads in Git
- only broaden scope after repeated stable validation
