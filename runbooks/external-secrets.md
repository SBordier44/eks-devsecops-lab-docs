# External Secrets Runbook

## Purpose

This runbook provides practical guidance for operating and troubleshooting External Secrets in the EKS DevSecOps Lab.

It focuses on the current AWS-backed secret synchronization model and helps answer questions such as:

- is External Secrets running correctly?
- is the operator healthy?
- is IRSA working?
- is the ClusterSecretStore ready?
- why is a Kubernetes Secret not being created?

## Scope

This runbook covers:

- operator checks
- CRD checks
- ClusterSecretStore validation
- ExternalSecret validation
- Kubernetes Secret validation
- common failure patterns observed in the current lab

## Current Context

The current secret management chain is:

1. secret stored in AWS Secrets Manager
2. External Secrets Operator authenticates to AWS through IRSA
3. a ClusterSecretStore defines the AWS secret backend
4. an ExternalSecret requests specific secret data
5. Kubernetes receives a generated Secret
6. the application consumes that Secret

This flow is already validated in the current lab.

## Basic Health Checks

### Check operator pods

```bash
kubectl -n external-secrets get pods
```

What to look for:

- pods are running
- no crash loops
- no repeated restarts

### Check CRDs

```bash
kubectl get crd | grep external-secrets
```

What to look for:

- External Secrets CRDs are present
- SecretStore and ClusterSecretStore CRDs exist
- operator-related resources are available to the cluster

This check is especially important because CRD ordering issues were part of the original installation troubleshooting.

## Check the Service Account and IRSA Annotation

```bash
kubectl -n external-secrets get sa external-secrets -o yaml
```

What to look for:

- the service account exists
- the IRSA annotation is present
- the role ARN is the expected one

Expected pattern:

- service account name: `external-secrets`
- namespace: `external-secrets`
- annotation key: `eks.amazonaws.com/role-arn`

## Check the ClusterSecretStore

```bash
kubectl get clustersecretstore
kubectl describe clustersecretstore aws-secretsmanager
```

What to look for:

- the `aws-secretsmanager` store exists
- the store is ready
- there are no authentication or provider errors

If the store is not ready, the operator usually cannot reach AWS correctly.

## Check the ExternalSecret Resource

```bash
kubectl -n demo-app-dev get externalsecret
kubectl -n demo-app-dev describe externalsecret demo-app-secret
```

What to look for:

- the resource exists
- the status indicates successful synchronization
- there are no provider, property, or access errors

## Check the Resulting Kubernetes Secret

```bash
kubectl -n demo-app-dev get secret demo-app-secret -o yaml
```

What to look for:

- the Secret exists
- the expected data keys exist

Example test for the current lab flow:

```bash
kubectl -n demo-app-dev get secret demo-app-secret -o jsonpath='{.data.DEMO_MESSAGE}' | base64 -d && echo
```

This should return the synchronized value stored in AWS Secrets Manager.

## Typical Troubleshooting Scenarios

### Scenario 1 — Operator pods are crashing

Possible reasons:

- CRDs missing or not installed correctly
- controller startup order problem
- operator deployment issue

Checks:

```bash
kubectl -n external-secrets get pods
kubectl -n external-secrets logs deployment/external-secrets
kubectl get crd | grep external-secrets
```

### Scenario 2 — ClusterSecretStore not ready

Possible reasons:

- IRSA not working
- IAM role trust policy issue
- missing AWS permissions
- service account mismatch

Checks:

```bash
kubectl describe clustersecretstore aws-secretsmanager
kubectl -n external-secrets get sa external-secrets -o yaml
```

### Scenario 3 — ExternalSecret exists but Secret not created

Possible reasons:

- wrong remote key
- wrong property name
- AWS permission issue
- ClusterSecretStore not ready
- synchronization error

Checks:

```bash
kubectl -n demo-app-dev describe externalsecret demo-app-secret
kubectl describe clustersecretstore aws-secretsmanager
```

### Scenario 4 — IRSA looks configured in Kubernetes but still fails

Possible reasons:

- IAM role trust policy mismatch
- wrong OIDC provider
- wrong `sub` value in the trust policy
- missing AWS policy permissions on Secrets Manager

Checks outside the cluster are then required, especially against the IAM role and trust relationship.

## Validation Sequence for the Current Lab

When debugging External Secrets, the most useful order is:

1. check operator pods
2. check CRDs
3. check service account annotation
4. check ClusterSecretStore readiness
5. check ExternalSecret status
6. check the resulting Kubernetes Secret
7. only then inspect AWS-side IAM or Secrets Manager details if needed

This sequence keeps the troubleshooting flow structured and efficient.

## Good Practices

For the current lab, the most useful practices are:

- keep secret source values in AWS Secrets Manager
- manage store and external secret definitions through GitOps
- avoid manual Kubernetes secret creation for managed flows
- validate operator health after any related platform change
- document the exact failure mode when troubleshooting

## Known Current Constraints

The current implementation is intentionally narrow:

- one main AWS-backed secret flow
- one current reusable ClusterSecretStore
- limited number of workload examples
- limited governance around secret usage

This is acceptable because the goal is to keep the secret model understandable and validated before broadening scope.

## Next Steps

The next logical improvements for this runbook are:

1. add AWS IAM troubleshooting in more depth
2. add more examples of status interpretation
3. document secret lifecycle and update behavior more explicitly
4. later connect this area to future governance controls

## Summary

External Secrets is now a validated platform component of the lab.

The best troubleshooting approach is to move step by step from operator health to store readiness to external secret status to final Kubernetes secret creation.
