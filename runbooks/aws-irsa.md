# AWS IRSA Runbook

## Purpose

This runbook provides practical guidance for operating and troubleshooting IRSA in the EKS DevSecOps Lab.

It focuses on the current cluster-to-AWS authentication model and helps answer questions such as:

- is IRSA configured correctly for a Kubernetes service account?
- does the IAM role trust relationship match the service account?
- why can a pod not access an AWS service even though the service account is annotated?
- where should I look first when AWS access from Kubernetes fails?

## Scope

This runbook covers:

- IRSA validation basics
- service account annotation checks
- OIDC provider checks
- IAM role trust policy validation
- policy attachment validation
- common failure paths in the current lab

It does not attempt to document every EKS IAM integration pattern.

## Current Context

The lab currently uses IRSA to allow Kubernetes workloads to access AWS services without storing long-lived static AWS credentials in manifests.

The most important current use case is:

- External Secrets Operator
- AWS Secrets Manager
- IAM role assumption through the `external-secrets` service account

The current cluster is:

- cluster name: `eksdsl-dev`
- region: `eu-west-3`

## Basic Validation Workflow

When IRSA is suspected to be broken, use this order:

1. check the Kubernetes service account
2. check the IAM role annotation on the service account
3. check the EKS cluster OIDC issuer
4. check the IAM role trust policy
5. check attached IAM permissions
6. validate the workload using the role
7. only then inspect the higher-level component that depends on IRSA

This sequence prevents mixing Kubernetes-side and AWS-side issues too early.

## Check the Kubernetes Service Account

Start by checking the service account used by the workload.

Example for External Secrets:

```bash
kubectl -n external-secrets get sa external-secrets -o yaml
```

What to look for:

- the service account exists
- the namespace is correct
- the annotation `eks.amazonaws.com/role-arn` is present
- the role ARN matches the expected IAM role

If the annotation is missing or incorrect, the workload will not assume the expected IAM role.

## Check the EKS OIDC Issuer

IRSA relies on the EKS OIDC identity provider.

Check the cluster issuer URL:

```bash
aws eks describe-cluster   --region eu-west-3   --name eksdsl-dev   --query "cluster.identity.oidc.issuer"   --output text
```

Expected pattern:

- an issuer URL under `oidc.eks.eu-west-3.amazonaws.com/id/...`

If this does not match the IAM trust policy, role assumption will fail.

## Check the IAM Role

Confirm that the IAM role exists:

```bash
aws iam get-role --role-name external-secrets-irsa-role
```

What to look for:

- the role exists
- the role ARN matches the service account annotation
- the assume role policy document is present

## Check the Trust Policy

The trust policy is one of the most common sources of IRSA errors.

Check it with:

```bash
aws iam get-role   --role-name external-secrets-irsa-role   --query 'Role.AssumeRolePolicyDocument'   --output json
```

What to validate:

- the federated principal points to the correct EKS OIDC provider
- the `aud` condition is `sts.amazonaws.com`
- the `sub` condition matches the exact Kubernetes service account identity

For the current External Secrets setup, the expected `sub` is:

```text
system:serviceaccount:external-secrets:external-secrets
```

If the namespace or service account name differs, IRSA will fail even if everything else looks correct.

## Check Attached IAM Policies

After validating the trust policy, check the permissions attached to the role:

```bash
aws iam list-attached-role-policies --role-name external-secrets-irsa-role
```

What to look for:

- the expected policy is attached
- the role actually has permission to call the AWS service it needs

For the current External Secrets use case, the role needs read access to AWS Secrets Manager for the expected secret path.

## Typical Troubleshooting Scenarios

### Scenario 1 — Service account annotated, but AWS access still fails

Possible reasons:

- trust policy mismatch
- wrong service account name in the trust policy
- wrong namespace in the trust policy
- wrong OIDC provider
- missing IAM permissions on the role

Validation path:

1. check service account annotation
2. check role trust policy
3. check attached policies

### Scenario 2 — Trust policy looks correct, but access still fails

Possible reasons:

- policy does not allow the right AWS API actions
- policy resource scope is too narrow
- the workload is not actually using the expected service account

Validation path:

- confirm the role permissions
- confirm the pod is running with the expected service account
- inspect the higher-level component error messages

### Scenario 3 — Workload component reports provider or authentication errors

Possible reasons:

- IRSA is broken underneath
- the dependent component is healthy, but cannot authenticate to AWS
- the wrong IAM role is being used

Example current case:

- External Secrets operator running
- ClusterSecretStore not ready
- provider errors pointing back to AWS access

In that situation, validate IRSA first before changing the higher-level Kubernetes resource definitions.

## Current Good Practices

For the current lab, the most useful practices are:

- use IRSA instead of static AWS credentials inside Kubernetes
- keep role scopes focused on real workload needs
- match the trust policy `sub` exactly to the Kubernetes service account identity
- manage the Kubernetes-side configuration declaratively through GitOps
- document the final validated values used by the cluster

## Known Current Constraints

The current IRSA model is intentionally focused.

Examples:

- one main documented use case
- one main operator integration pattern
- limited number of workload-to-AWS examples
- broader IAM runbooks still need expansion

This is acceptable for the current maturity of the lab.

## Next Steps

The next logical improvements for this runbook are:

1. add more role-specific examples if new AWS-integrated workloads are introduced
2. document the Terraform-side IAM setup more deeply
3. connect future governance controls with workload identity expectations
4. expand validation examples as more platform components use IRSA

## Summary

IRSA is a key cloud-native security building block of the lab.

When it fails, the best approach is to validate the full chain in order: service account, annotation, OIDC issuer, IAM role trust policy, attached permissions, and finally the workload that depends on it.
