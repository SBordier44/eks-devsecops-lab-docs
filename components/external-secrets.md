# Composant External Secrets

## Objectif

Ce document décrit le rôle d’**External Secrets Operator** dans le lab.

Il explique :

- son rôle pendant la phase AWS validée
- son intégration avec AWS Secrets Manager
- l’usage d’IRSA
- sa place actuelle dans l’architecture globale du projet

## Rôle historique

External Secrets Operator était le composant de synchronisation de secrets de la phase AWS.

Il faisait le lien entre :

- AWS Secrets Manager
- Kubernetes
- la consommation applicative de secrets

## Pourquoi cette brique est importante

Cette brique démontre un modèle de secrets beaucoup plus crédible que :

- des secrets en clair dans Git
- des manifests enrichis manuellement
- des credentials AWS statiques dans les workloads

Elle reste donc importante comme preuve de capacité validée sur la phase AWS.

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

External Secrets n’est pas le composant de secrets actif sur le runtime K3s actuel.

La phase K3s active s’appuie désormais sur **Sealed Secrets** pour le cas démontré de la demo-app.

ESO reste donc documenté comme :

- une brique **validée** de la phase AWS
- une référence de conception utile
- un composant non actif dans le runtime K3s courant

## Résumé

External Secrets est un composant **validé et important** de la phase AWS.

La phase K3s active suit aujourd’hui une autre stratégie, plus simple, avec Sealed Secrets.  
Cela ne retire rien à la valeur de la démonstration AWS déjà réalisée.
