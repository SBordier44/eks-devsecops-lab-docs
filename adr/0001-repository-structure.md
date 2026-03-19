# ADR-0001 Repository Structure

## Context

The lab is designed as a platform engineering and DevSecOps project rather than as a single application repository.

Its scope includes several distinct concerns:

- AWS infrastructure provisioning
- application source code and container build
- Kubernetes desired state and GitOps reconciliation
- architecture documentation and operational knowledge

Keeping all of these concerns in a single repository would make ownership boundaries less clear and would reduce the overall readability of the platform.

It would also make the project less representative of real-world engineering environments, where infrastructure, application delivery, deployment state, and documentation are often managed independently.

## Decision

The project is split into four repositories:

- `eks-devsecops-lab-infra`
- `eks-devsecops-lab-app`
- `eks-devsecops-lab-gitops`
- `eks-devsecops-lab-docs`

Each repository has a distinct role:

- the infrastructure repository provisions AWS resources with Terraform and Terragrunt
- the application repository owns the demo application source code and container build pipeline
- the GitOps repository owns Kubernetes desired state and ArgoCD-managed deployment resources
- the documentation repository owns architecture documents, ADRs, runbooks, and technical explanations

## Consequences

This structure provides several benefits:

- clearer separation of concerns
- cleaner Git history by responsibility
- better alignment with GitOps principles
- easier reasoning about change impact
- improved documentation quality
- a more realistic DevSecOps and platform engineering project structure

It also strengthens the portfolio value of the lab by showing a delivery model closer to what is used in real engineering teams, where infrastructure, application delivery, deployment state, and documentation are treated as separate but connected domains.
