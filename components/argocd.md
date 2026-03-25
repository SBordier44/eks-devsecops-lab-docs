# Composant ArgoCD

## Objectif

Ce document décrit le rôle d’**ArgoCD** dans le lab.

Il explique :

- le rôle d’ArgoCD dans la plateforme
- le modèle de bootstrap
- le fonctionnement de la réconciliation
- la coexistence entre la phase AWS validée et la phase K3s active

## Rôle du composant

ArgoCD est le moteur de réconciliation GitOps du lab.

Il permet de :

- lire l’état désiré depuis Git
- réconcilier cet état dans le cluster
- déployer les composants plateforme et applicatifs
- corriger la dérive entre Git et le cluster

## Modèle de bootstrap

Le projet comporte deux points d’entrée GitOps importants :

### Phase AWS validée

Une application racine pointait vers le layout GitOps AWS-oriented.

### Phase K3s active

Une application racine dédiée pointe vers le layout `argocd-k3s/`.

Cette séparation permet de poursuivre le lab sur K3s sans embarquer directement les ressources encore liées à AWS.

## Périmètre actif

ArgoCD gère actuellement sur K3s :

- Traefik
- cert-manager
- les `ClusterIssuer`
- Kyverno
- les policies Kyverno
- la demo-app

## Forces du composant

ArgoCD démontre :

- un vrai modèle GitOps
- la synchro automatique
- le prune
- le self-heal
- la séparation CI / réconciliation cluster
- la capacité à conserver le même modèle malgré un changement de runtime

## Limites actuelles

Le périmètre reste volontairement raisonnable :

- un cluster actif principal
- une application principale
- pas d’architecture multi-cluster
- pas encore de stratégie active de secrets côté K3s

## Résumé

ArgoCD est un des composants les plus solides du lab.  
Il assure la continuité d’exploitation entre la phase AWS validée et la phase K3s active.
