# Composant demo-app

## Objectif

Ce document décrit la **demo-app** du lab.

Elle ne cherche pas à démontrer une logique métier complexe.  
Son rôle principal est de valider la plateforme de bout en bout.

## Rôle actuel

La demo-app sert de workload de référence pour vérifier :

- le build d’image
- la publication dans le registre
- la mise à jour GitOps
- le déploiement ArgoCD
- l’exposition via ingress
- le TLS
- la gouvernance Kyverno

## Modèle applicatif

L’application est volontairement minimale.

Elle expose des endpoints simples, suffisants pour tester :

- la disponibilité
- le déploiement
- le routage
- la santé de la plateforme

## Modèle conteneur

L’application utilise un build multi-stage et une image runtime légère avec exécution non-root.

C’est un point important du projet car cela démontre une base saine côté image.

## Flux de delivery actif

Le flux actif est le suivant :

1. push de code dans le dépôt application
2. build de l’image par GitHub Actions
3. publication dans le registre utilisé par la phase active
4. mise à jour du dépôt GitOps
5. déploiement sur K3s via ArgoCD

## Flux validé historique

La phase AWS utilisait le même principe général, mais avec :

- un registre ECR
- un runtime EKS

Cette partie reste pleinement valide.

## Déploiement Kubernetes

La demo-app est déployée via le dépôt GitOps avec Kustomize.

Le modèle comprend :

- une `base` commune
- un overlay historique orienté AWS
- un overlay actif `k3s-dev`

L’overlay `k3s-dev` gère notamment :

- le namespace
- l’ingress
- le hostname actif
- l’override d’image
- les labels d’environnement

## Exposition active

La demo-app est actuellement exposée publiquement en HTTPS via :

- Traefik
- cert-manager
- Let’s Encrypt
- le runtime K3s

Hostname actif :

- `srv712424.hstgr.cloud`

## Gestion des secrets

La phase AWS validée comportait un `ExternalSecret` pour la demo-app.

La phase K3s active n’a pas encore réintroduit cette brique, ce qui est assumé et documenté comme prochaine étape.

## Résumé

La demo-app est volontairement simple, mais stratégiquement essentielle.  
Elle prouve la plateforme de bout en bout, aussi bien dans la phase AWS validée que dans la phase K3s active.
