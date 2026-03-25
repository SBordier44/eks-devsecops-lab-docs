# Composant Amazon EKS

## Objectif

Ce document décrit le rôle du composant **Amazon EKS** dans la phase AWS validée du lab.

## Rôle du composant

EKS était le runtime Kubernetes principal de la première phase du projet.

Il hébergeait :

- ArgoCD
- Traefik
- cert-manager
- External Secrets Operator
- Kyverno
- la demo-app

## Pourquoi EKS

Le choix d’EKS permettait de travailler sur :

- un Kubernetes managé crédible
- une bonne intégration IAM / IRSA
- un vrai terrain d’exercice DevOps / DevSecOps sur AWS
- des patterns réalistes de delivery cloud

## Ce que cette brique démontre

EKS démontre :

- la capacité à déployer un cluster AWS cohérent
- la capacité à l’intégrer avec GitOps
- la capacité à y connecter des composants plateforme réels
- la capacité à y faire tourner une application exposée en HTTPS

## Transition

Le runtime actif a ensuite été déplacé vers K3s pour continuer le lab à coût maîtrisé.  
Cela ne retire rien à la valeur du composant EKS, qui reste une partie validée et importante du projet.

## Résumé

EKS est le cœur du runtime de la phase AWS validée.  
Il représente la partie cloud la plus visible et la plus démonstrative du lab.
