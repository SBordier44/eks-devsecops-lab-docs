# Architecture de la supply chain conteneur

## Objectif

Ce document décrit la chaîne de fabrication, publication et déploiement des images de conteneurs utilisées par le lab.

La supply chain n’est pas encore à son niveau de maturité cible, mais elle constitue déjà un axe central du projet.

## Ce que le lab démontre déjà

Le lab démontre déjà une chaîne claire :

- build applicatif depuis le dépôt `eks-devsecops-lab-app`
- construction d’une image conteneur
- publication dans un registre
- mise à jour du dépôt GitOps
- déploiement réconcilié par ArgoCD

Cette chaîne a existé et a été validée dans deux variantes :

- une variante **AWS** avec **Amazon ECR**
- une variante **phase 2** avec un registre adapté au runtime actif

## Phase AWS validée

Dans la phase cloud validée, la chaîne reposait sur :

- GitHub Actions
- construction de l’image
- publication vers **Amazon ECR**
- mise à jour du dépôt GitOps
- déploiement sur **EKS**

Cette phase démontre déjà une supply chain cloud de bout en bout.

## Phase active K3s

Dans la phase active, le lab a été sorti du runtime AWS afin de continuer son évolution sur K3s.

La chaîne de delivery reste fondamentalement la même :

- GitHub Actions construit l’image
- le registre cible de la phase active héberge l’image
- le dépôt GitOps est mis à jour
- ArgoCD déploie dans K3s

Le registre actif retenu pour cette phase est **GHCR**.

## Image applicative

La demo-app utilise une image construite de manière pragmatique et orientée sécurité :

- build multi-stage
- image finale légère
- exécution non-root
- séparation claire entre build et runtime

Cela apporte déjà une base saine en matière de sécurité et d’empreinte runtime.

## Forces actuelles

La supply chain actuelle démontre :

- une séparation entre code source et état de déploiement
- un pipeline CI lisible
- une publication d’image dans un registre dédié
- une propagation GitOps propre du changement
- une continuité de delivery entre la phase AWS et la phase K3s

## Limites actuelles

La supply chain n’a pas encore atteint son état cible.

Les points restant à renforcer incluent :

- la production et l’exploitation d’un **SBOM**
- la signature des images
- une politique de vérification côté cluster
- un storytelling de confiance plus complet entre build, registre et déploiement

## Direction cible

L’orientation prévue pour le lab est de faire évoluer la supply chain vers quelque chose de plus robuste en matière de sécurité :

- génération de SBOM
- signature d’images
- documentation plus claire de la chaîne de confiance
- éventuelle intégration de contrôles d’admission liés à la provenance ou aux signatures

## Résumé

La supply chain du lab est déjà sérieuse et cohérente.  
La phase AWS prouve le chemin cloud complet, et la phase K3s prouve que la chaîne de delivery peut continuer à vivre hors AWS sans perdre sa logique GitOps.
