# Composant VPC

## Objectif

Ce document décrit le composant **VPC** utilisé dans la phase AWS validée du lab.

Le VPC constitue la fondation réseau de la plateforme cloud. Même si le runtime actif se trouve désormais sur K3s, cette brique reste importante car elle démontre la capacité à construire une base AWS propre pour Kubernetes.

## Rôle du composant

Le VPC fournit :

- l’isolation réseau du lab
- les subnets nécessaires au cluster EKS
- la connectivité Internet
- le support réseau pour l’exposition des workloads
- la base réseau pour les futures intégrations plateforme

## Éléments provisionnés

Le VPC du lab comprenait :

- un VPC dédié
- 2 Availability Zones
- des subnets publics
- des subnets privés
- un Internet Gateway
- un NAT Gateway unique
- un endpoint S3 Gateway

## Choix de conception

Le design réseau a été volontairement gardé lisible :

- assez réaliste pour un lab cloud crédible
- assez simple pour rester compréhensible
- assez léger pour rester soutenable financièrement

## Subnets et intégration Kubernetes

Les subnets ont été préparés pour le fonctionnement d’EKS et pour l’intégration avec les mécanismes d’exposition réseau attendus côté Kubernetes.

Les tags de subnets ont permis une bonne intégration avec les besoins du cluster et des load balancers.

## Choix de coût

Le recours à un seul NAT Gateway est un compromis assumé :

- coût plus faible
- architecture plus simple
- résilience inférieure à un NAT par AZ

Ce choix est cohérent avec la philosophie générale du lab.

## Place du composant aujourd’hui

Le composant VPC n’est plus utilisé par le runtime actif K3s, mais il reste pleinement valide comme partie de la phase AWS du projet.

Il témoigne d’une base réseau cloud réelle et non d’une simple démonstration locale.

## Résumé

Le VPC est la fondation réseau de la phase AWS validée.  
Il reste un composant important dans la valeur globale du lab.
