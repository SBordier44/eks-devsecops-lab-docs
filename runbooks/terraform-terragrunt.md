# Runbook Terraform / Terragrunt

## Objectif

Ce runbook décrit comment manipuler la phase AWS du lab avec Terraform et Terragrunt.

Même si le runtime actif du projet est désormais K3s, ce runbook reste utile pour :

- relire la phase AWS validée
- réexécuter si nécessaire le provisioning historique
- comprendre la structure d’automatisation infrastructure

## Pré-requis

- accès AWS valide
- Terraform et Terragrunt installés
- variables / credentials adaptés
- accès au dépôt infrastructure

## Structure générale

Le live Terragrunt est organisé par stacks, notamment :

- `vpc`
- `eks`
- `ecr`
- `github-oidc`

## Commandes utiles

### Initialisation

```bash
terragrunt run --all init
```

### Plan

```bash
terragrunt run --all plan
```

### Apply

```bash
terragrunt run --all apply
```

### Destroy

```bash
terragrunt run --all destroy
```

## Ordre conseillé

En pratique, il est préférable de garder la logique de dépendances et de vérifier les outputs avant d’enchaîner sur l’ensemble des stacks.

## Points de vigilance

- bien vérifier les outputs de dépendances
- surveiller les coûts AWS avant de laisser vivre l’infrastructure
- vérifier les paramètres réseau EKS
- éviter les destructions partielles mal maîtrisées

## Résumé

Ce runbook reste la référence opérationnelle de la phase AWS validée côté infrastructure as code.
