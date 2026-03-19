# Terraform and Terragrunt Runbook

## Purpose

This runbook provides practical guidance for operating and troubleshooting Terraform and Terragrunt in the EKS DevSecOps Lab.

It focuses on the current infrastructure workflow and helps answer questions such as:

- how should infrastructure changes be applied safely?
- in which order should Terragrunt stacks be executed?
- how can remote state issues be diagnosed?
- what should be checked first when a stack fails?
- how should dependency-related problems be approached?

## Scope

This runbook covers:

- the current Terragrunt live structure
- basic execution workflow
- stack ordering and dependencies
- remote state checks
- common troubleshooting scenarios
- good operational practices for the current lab

It does not attempt to be a full Terraform or Terragrunt reference.

## Current Context

The infrastructure of the lab is managed from the `eks-devsecops-lab-infra` repository.

The current model is based on:

- Terraform modules under `modules/`
- Terragrunt live configuration under `live/dev/`

The current main development stacks include:

- `vpc`
- `eks`
- `ecr`
- `github-oidc`

The current environment is centered on:

- environment: `dev`
- region: `eu-west-3`
- project: `eks-devsecops-lab`

Remote state is currently stored in:

- an S3 bucket for Terraform state
- a DynamoDB table for Terraform locking

## Current Operating Principle

The infrastructure workflow should follow a simple rule:

- **read first**
- **plan second**
- **apply last**

In practice, infrastructure changes should be introduced progressively and validated at each step.

Because the lab uses Terragrunt stack dependencies, changes should also respect the relationship between foundational and dependent stacks.

## Current Live Structure

The current live environment is organized under `live/dev/`.

At a high level:

- `vpc` provides the network foundation
- `eks` depends on VPC outputs
- `ecr` provides the application container registry
- `github-oidc` depends on existing AWS resources and CI trust relationships

This means not every stack should be applied blindly or in arbitrary order.

## Basic Workflow

A safe workflow for the current lab is:

1. go to the infrastructure repository
2. review the intended change
3. run formatting and validation if relevant
4. run Terragrunt plan on the targeted stack
5. review outputs and dependency implications
6. apply only after the plan is understood
7. validate the resulting AWS resources

## Useful Command Patterns

### Initialize a stack

```bash
terragrunt init
```

### Validate a stack

```bash
terragrunt validate
```

### Plan a stack

```bash
terragrunt plan
```

### Apply a stack

```bash
terragrunt apply
```

### Destroy a stack

```bash
terragrunt destroy
```

### Run a command across multiple stacks

```bash
terragrunt run --all plan
```

Use `run --all` carefully. It is useful for visibility, but targeted execution is usually safer when changing a specific part of the lab.

## Recommended Stack Order

For the current lab, the most natural dependency order is:

1. `vpc`
2. `eks`
3. `ecr`
4. `github-oidc`

This order matters because:

- EKS needs VPC outputs
- GitHub OIDC and CI permissions may depend on existing AWS-side resources such as ECR
- foundational changes should be validated before dependent layers

## Remote State Checks

Remote state problems are among the most important operational issues to understand.

The current backend strategy uses:

- S3 for Terraform state storage
- DynamoDB for state locking

### What to check first

If Terraform or Terragrunt behaves unexpectedly, validate:

- the S3 bucket exists
- the DynamoDB table exists
- AWS credentials or assumed role are correct
- the current stack points to the expected backend configuration
- there is no stale lock preventing execution

## Typical Troubleshooting Scenarios

### Scenario 1 — Dependency outputs are missing

Typical symptom:
- a stack depending on another stack fails because outputs are unavailable

This commonly happens when:

- the dependency stack was not applied yet
- outputs are not defined as expected
- Terragrunt dependency configuration points to the wrong path

Recommended checks:

1. verify the dependency stack was applied successfully
2. inspect the dependency outputs
3. confirm the `config_path` is correct in the dependent stack
4. confirm the output names match what the dependent stack expects

This type of issue already appeared in the lab around early stack dependency resolution, so it is a known operational pattern.

### Scenario 2 — Backend or lock issue

Typical symptom:
- Terraform or Terragrunt reports backend or lock problems

Possible reasons:

- wrong AWS identity
- missing S3 bucket
- missing DynamoDB table
- stale lock entry
- backend configuration mismatch

Recommended checks:

1. confirm the remote state resources exist
2. confirm the AWS account and region are correct
3. inspect lock state if a previous command failed
4. avoid manual cleanup unless the situation is understood

### Scenario 3 — Plan works but apply fails

Possible reasons:

- IAM permissions are insufficient
- AWS-side quota or service limitation
- dependency resources are inconsistent
- provider assumptions do not match real AWS state

Recommended checks:

1. read the exact AWS error message
2. identify whether the failure is permissions, dependency, or resource-state related
3. verify whether the target resource already exists in AWS
4. avoid retrying blindly without understanding the first failure

### Scenario 4 — EKS-related changes succeed in Terraform but platform behavior is wrong

Possible reasons:

- infrastructure was created correctly
- but downstream platform validation was not done
- Kubernetes-side components are now inconsistent with the infrastructure change

In that case, the problem may not be Terraform itself.

Validation path:

1. confirm the stack applied successfully
2. validate the AWS resource state
3. validate the cluster from the Kubernetes side
4. then inspect ArgoCD or platform components if needed

### Scenario 5 — GitHub OIDC workflow no longer authenticates to AWS

Possible reasons:

- trust policy mismatch
- repository or branch conditions changed
- GitHub environment conditions changed
- role permissions are insufficient

Recommended checks:

1. inspect the IAM role trust policy
2. confirm the GitHub repository and branch/environment conditions still match
3. validate which workflow is assuming which role
4. confirm the required AWS permissions are attached

## Validation After Apply

After any important infrastructure apply, validate the result explicitly.

### For VPC changes

Check:

- VPC exists
- subnets exist
- route tables and NAT are correct
- Kubernetes-related subnet tags are still present

### For EKS changes

Check:

- cluster is active
- node group is healthy
- kubeconfig still works
- cluster addons are healthy

Useful commands:

```bash
aws eks describe-cluster --region eu-west-3 --name eksdsl-dev
kubectl get nodes
kubectl get pods -A
```

### For ECR changes

Check:

- repository exists
- lifecycle policy is present
- CI can still push images

Useful commands:

```bash
aws ecr describe-repositories --region eu-west-3
```

### For GitHub OIDC changes

Check:

- IAM OIDC provider exists
- IAM role trust policies are correct
- GitHub workflows can still assume roles

## Current Good Practices

For the current lab, the most useful practices are:

- prefer targeted stack execution over large uncontrolled applies
- respect dependency order
- review plans before apply
- validate AWS-side results after apply
- document non-obvious infrastructure issues when they occur
- keep the infrastructure baseline stable while the platform evolves

## Known Current Constraints

The current infrastructure model is intentionally focused:

- one main environment
- one main cluster
- a limited number of infrastructure stacks
- progressive rather than enterprise-wide complexity

This is acceptable because the lab prioritizes clarity, reproducibility, and learning value.

## Next Steps

The next logical improvements for this runbook are:

1. add more stack-specific validation examples
2. document common recovery procedures in more detail
3. add explicit examples for remote state troubleshooting
4. connect this runbook more deeply with future ADRs and security documentation

## Summary

Terraform and Terragrunt are the operational foundation of the infrastructure layer in this lab.

The best approach is to work progressively, respect stack dependencies, validate plans carefully, and always confirm the real AWS result after apply.
