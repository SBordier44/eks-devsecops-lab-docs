# ADR-0002 Terraform and Terragrunt Live Layout

## Context
The lab infrastructure is managed as code on AWS and needs to stay modular, readable, and easy to evolve.

The platform includes multiple infrastructure concerns such as:

- networking
- EKS
- ECR
- GitHub OIDC federation

A simple flat Terraform structure would become harder to maintain as the lab grows.

## Decision
Terraform modules are separated from the live environment configuration.

The infrastructure repository uses:

- reusable Terraform modules under `modules/`
- Terragrunt live configuration under `live/dev/`

The current live stacks are organized by concern:

- `vpc`
- `eks`
- `ecr`
- `github-oidc`

Remote state is centralized in AWS using:

- S3 for Terraform state storage
- DynamoDB for state locking

## Consequences
This layout provides:

- clearer separation between reusable building blocks and environment-specific configuration
- cleaner dependency management between infrastructure stacks
- a more realistic infrastructure-as-code structure for a DevSecOps platform
- safer collaboration through remote state locking
- a better foundation for future growth without over-engineering the current lab
