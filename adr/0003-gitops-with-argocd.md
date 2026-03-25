# ADR 0003 - Adopter GitOps avec ArgoCD

## Statut

Accepté

## Contexte

Le lab devait démontrer un mode de déploiement Kubernetes :

- déclaratif
- traçable
- reproductible
- proprement séparé du pipeline de build

## Décision

Adopter **ArgoCD** comme moteur GitOps, avec un dépôt dédié contenant l’état désiré du cluster.

## Conséquences

### Positives

- séparation claire entre CI et déploiement
- meilleur audit des changements
- self-heal et prune
- forte cohérence d’exploitation

### Négatives

- couche supplémentaire à opérer
- nécessité d’un dépôt GitOps bien structuré

## Remarque de phase 2

Cette décision est l’une de celles qui ont le mieux résisté à l’évolution du projet : elle reste valable aussi bien dans la phase AWS que dans la phase K3s.
