# Architecture GitOps et ArgoCD

## Objectif

Ce document décrit la mise en œuvre de GitOps dans le lab avec **ArgoCD**.

Il explique :

- la structure GitOps actuelle
- le bootstrap ArgoCD
- la réconciliation des composants plateforme et applicatifs
- le lien entre le pipeline CI/CD et le dépôt GitOps
- l’évolution du modèle GitOps entre la phase AWS et la phase K3s

## Pourquoi GitOps

GitOps est utilisé pour rendre les déploiements Kubernetes :

- déclaratifs
- traçables
- reproductibles
- auditables
- plus simples à relire et à corriger

Le principe est de ne pas déployer directement depuis le pipeline CI dans le cluster.  
Le pipeline met à jour Git, puis **ArgoCD** réconcilie Git vers le cluster.

## Rôle du dépôt GitOps

Le dépôt `eks-devsecops-lab-gitops` est la source de vérité de l’état désiré Kubernetes.

Il contient :

- les ressources de bootstrap ArgoCD
- les applications ArgoCD
- les bases et overlays Kustomize
- les manifests de la demo-app
- les ressources Traefik
- les ressources cert-manager
- les `ClusterIssuer`
- les ressources Kyverno
- les chemins historiques AWS et le chemin actif K3s

## Modèle de bootstrap

Le lab possède désormais **deux chemins de bootstrap importants**.

### Bootstrap AWS validé

La phase AWS validée utilisait une application racine ArgoCD pointant vers `argocd/`.

Ce chemin reste documenté, car il correspond à un état réel qui a été validé dans le cluster EKS.

### Bootstrap K3s actif

La phase active utilise une application racine dédiée, pointant vers `argocd-k3s/`.

Cela permet de poursuivre le lab sur K3s sans embarquer de dépendances runtime encore liées à AWS.

## Périmètre ArgoCD actif

Le périmètre ArgoCD actif côté K3s comprend :

- le projet `apps`
- Traefik
- cert-manager
- les `ClusterIssuer` staging et production
- Kyverno
- les policies Kyverno
- l’application `demo-app-k3s-dev`

## Périmètre ArgoCD validé côté AWS

La phase AWS validée comprenait également :

- External Secrets CRDs
- External Secrets Operator
- le `ClusterSecretStore` AWS
- le chemin de déploiement AWS de la demo-app

## Modèle applicatif

La demo-app active est déployée via une application ArgoCD dédiée à K3s.

Le dépôt GitOps contient désormais :

- une `base` réutilisable
- un overlay historique orienté AWS
- un overlay actif `k3s-dev`

Cette approche évite de casser la structure existante tout en gardant une séparation propre.

## Modèle Kustomize

La `base` contient les ressources communes du workload.  
L’overlay `k3s-dev` ajoute :

- le namespace
- l’ingress
- l’override d’image
- le label d’environnement
- la compatibilité runtime K3s

## Flux de delivery actif

Le flux actif est le suivant :

1. du code est poussé dans le dépôt application
2. GitHub Actions construit l’image
3. l’image est publiée dans le registre cible
4. le dépôt GitOps est mis à jour avec le nouveau tag
5. ArgoCD réconcilie cet état désiré dans K3s
6. la nouvelle version de l’application est déployée

## Forces actuelles

Le modèle GitOps du lab démontre :

- un vrai moteur de réconciliation
- un bootstrap par application racine
- la synchro automatique
- le self-heal et le prune
- la séparation claire entre CI et déploiement cluster
- la continuité de la plateforme malgré le changement de runtime

## Contraintes actuelles

L’implémentation reste volontairement simple :

- un cluster actif principal
- une application principale
- pas d’architecture multi-cluster
- pas d’ApplicationSet complexe
- pas de stratégie active de secrets K3s encore réintroduite

## Évolutions prévues

Les prochaines étapes GitOps / ArgoCD les plus logiques sont :

- réintroduire une stratégie de secrets cohérente dans le runtime K3s
- enrichir les runbooks de rollback et de troubleshooting
- mieux documenter les patterns de promotion et de changement
- élargir progressivement la gouvernance autour des composants plateforme

## Résumé

ArgoCD est l’un des piliers du lab.  
Il démontre un vrai fonctionnement GitOps et, surtout, la capacité de faire évoluer la cible d’exécution du projet sans perdre la discipline de delivery.
