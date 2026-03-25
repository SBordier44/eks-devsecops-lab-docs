# Composant External Secrets

## Objectif

Ce document décrit le rôle d’**External Secrets Operator** dans le lab.

Il explique :

- son rôle pendant la phase AWS validée
- son intégration avec AWS Secrets Manager
- l’usage d’IRSA
- pourquoi il est aujourd’hui documenté comme composant validé plutôt que comme composant actif du runtime K3s

## Rôle historique

External Secrets Operator était le composant de synchronisation de secrets de la phase AWS.

Il faisait le lien entre :

- AWS Secrets Manager
- Kubernetes
- la consommation applicative de secrets

## Pourquoi cette brique est importante

Elle démontre un modèle de secrets beaucoup plus crédible que :

- des secrets en clair dans Git
- des manifests enrichis manuellement
- des credentials AWS statiques dans les workloads

## Déploiement dans la phase AWS

Pendant la phase AWS validée, ESO était déployé via le dépôt GitOps et géré par ArgoCD.

Le dispositif comprenait :

- les CRDs séparées
- l’opérateur
- le `ClusterSecretStore` AWS
- les `ExternalSecret` côté workloads

## Intégration AWS

La phase AWS reposait sur :

- AWS Secrets Manager
- IRSA
- un rôle IAM dédié au service account de l’opérateur

## Intégration workload

La demo-app consommait un secret synchronisé depuis AWS Secrets Manager grâce à un `ExternalSecret`.

Cette partie n’était donc pas seulement installée : elle a réellement été validée dans le runtime.

## Position actuelle

External Secrets n’est pas encore réintroduit dans le runtime K3s actif.

C’est volontaire.  
La plateforme active a d’abord été reconstruite autour du minimum stable avant de choisir un nouveau standard de gestion des secrets.

## Résumé

External Secrets est un composant **validé et important** de la phase AWS.  
La phase K3s attend encore son modèle actif de gestion des secrets.
