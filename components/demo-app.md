# Composant demo-app

## Objectif

Ce document décrit la **demo-app** du lab.

Elle ne cherche pas à démontrer une logique métier complexe.  
Son rôle principal est de valider la plateforme de bout en bout.

## Rôle dans le lab

La demo-app sert de workload de référence pour vérifier :

- le build d’image
- la publication dans le registre
- les contrôles de sécurité dans la CI
- la mise à jour GitOps
- le déploiement ArgoCD
- l’exposition via ingress
- le TLS
- la gestion de secret applicatif
- la gouvernance Kyverno

## Modèle applicatif

L’application reste volontairement minimale.

Elle fournit un point de test simple pour valider :

- la disponibilité du service
- le déploiement
- le routage
- l’état de santé du workload

Le déploiement Kubernetes expose notamment une probe `readiness` et une probe `liveness` sur `/healthz`.

## Modèle conteneur

L’image de la demo-app repose sur un modèle simple et sain :

- build **Go** en multi-stage
- binaire compilé dans une image builder
- image finale **distroless**
- exécution **non-root**
- écoute applicative sur le port `8080`

Ce choix sert autant la simplicité que le durcissement de base du runtime.

## Flux de delivery actif

Le flux actif est le suivant :

1. push de code dans le dépôt applicatif
2. build de l’image par GitHub Actions
3. publication sur **GHCR**
4. génération du **SBOM**
5. scan Trivy bloquant et upload **SARIF**
6. signature Cosign de l’image
7. vérification Cosign sur le digest publié
8. mise à jour du dépôt GitOps
9. déploiement sur K3s via ArgoCD

## Flux validé historique

La phase AWS utilisait le même principe général, mais avec :

- un registre **ECR**
- un runtime **EKS**

Cette partie reste pleinement valide comme preuve du parcours cloud initial du lab.

## Déploiement Kubernetes

La demo-app est déployée via le dépôt GitOps avec Kustomize.

Le modèle comprend :

- une `base` commune
- un overlay historique orienté AWS
- un overlay actif `k3s-dev`

L’overlay `k3s-dev` gère notamment :

- le namespace `demo-app-dev`
- l’ingress actif
- l’override d’image vers **GHCR**
- le tag d’image déployé
- le `SealedSecret` de configuration
- le patch d’injection du secret dans le pod
- les labels d’environnement

## Alignement avec Kyverno

La base applicative est déjà préparée pour respecter le socle Kyverno actif.

Le déploiement définit :

- un `securityContext` non-root
- `allowPrivilegeEscalation: false`
- `capabilities.drop: [ALL]`
- des `requests` CPU / mémoire
- une limite mémoire

Cela permet à la demo-app d’être cohérente avec les policies `Enforce` actuellement déclarées dans le dépôt GitOps.

## Gestion des secrets active

Dans la phase K3s active, la demo-app consomme un secret applicatif issu de **Sealed Secrets**.

Le flux est le suivant :

- un `SealedSecret` `demo-app-config` est versionné dans l’overlay `k3s-dev`
- le contrôleur génère un `Secret` Kubernetes
- le déploiement injecte `DEMO_MESSAGE` via `secretKeyRef`

La demo-app démontre donc aussi un flux simple mais réel de consommation de secret côté runtime K3s.

## Exposition active

La demo-app est actuellement exposée publiquement en HTTPS via :

- Traefik
- cert-manager
- Let’s Encrypt
- le runtime K3s

Hostname actif :

- `srv712424.hstgr.cloud`

## Résumé

La demo-app est volontairement simple, mais stratégiquement essentielle.

Elle prouve la plateforme de bout en bout, aussi bien dans la phase AWS validée que dans la phase K3s active, avec en plus désormais :

- une supply chain renforcée
- une consommation de secret active sur K3s
- une gouvernance Kyverno réellement appliquée
