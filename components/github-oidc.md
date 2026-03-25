# Composant GitHub OIDC

## Objectif

Ce document décrit la fédération **GitHub Actions -> AWS** via **OIDC** utilisée pendant la phase AWS validée du lab.

Ce composant est une des briques les plus crédibles du projet car il démontre une intégration CI/CD vers AWS sans secrets statiques.

## Rôle du composant

Le rôle d’OIDC était de permettre aux workflows GitHub Actions :

- d’assumer un rôle AWS
- d’accéder aux services nécessaires au pipeline
- de publier les images sans stocker d’`AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` longue durée

## Pourquoi c’est important

Ce composant démontre une approche plus propre que :

- l’usage de clés AWS statiques en secrets GitHub
- le partage de credentials longue durée
- des pratiques de CI moins sécurisées

## Intégration dans le lab

Dans la phase AWS, GitHub OIDC était utilisé pour :

- l’authentification du pipeline applicatif vers AWS
- l’accès à ECR
- la continuité du workflow de publication d’image

## Ce que cela prouve

Le composant GitHub OIDC montre :

- une bonne compréhension des patterns d’authentification cloud modernes
- une approche DevSecOps plus propre du pipeline
- une vraie intégration GitHub / AWS

## Place dans la phase active

La phase K3s active ne dépend plus de ce composant pour son runtime principal.

Néanmoins, ce composant reste une preuve forte de la maturité de la phase AWS.

## Résumé

GitHub OIDC est un composant validé de la phase cloud du lab.  
Il apporte une vraie crédibilité au projet en matière d’intégration CI/CD vers AWS.
