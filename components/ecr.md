# Composant Amazon ECR

## Objectif

Ce document décrit le rôle d’**Amazon ECR** dans la phase AWS validée du lab.

Même si la phase active a ensuite basculé vers un registre mieux adapté à un runtime hors AWS, ECR reste une brique validée et importante du projet.

## Rôle du composant

ECR servait de registre d’images pour la demo-app pendant la phase AWS.

Le workflow général était :

1. build de l’image depuis GitHub Actions
2. push vers Amazon ECR
3. mise à jour du dépôt GitOps
4. déploiement sur EKS via ArgoCD

## Pourquoi ce choix était pertinent

ECR était un choix naturel pour la phase cloud car il apportait :

- une intégration native à AWS
- une cohérence avec EKS
- une bonne lisibilité du pipeline
- un registre dédié au projet

## Ce que cette brique démontre

La présence d’ECR dans le lab prouve :

- la capacité à intégrer un registre cloud réel
- la capacité à faire un pipeline CI/CD cohérent avec AWS
- la capacité à faire converger build, registre, GitOps et runtime

## Transition vers la phase K3s

Lorsque le runtime actif a été déplacé hors AWS, le registre actif a lui aussi évolué pour éviter de conserver une dépendance runtime inutile à AWS.

Cela ne retire rien à la valeur d’ECR : la brique a bien été mise en place et validée dans un vrai chemin de delivery.

## Résumé

ECR est un composant **validé de la phase AWS**.  
Il fait partie du socle cloud du lab, même si le runtime actif n’en dépend plus aujourd’hui.
