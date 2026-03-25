# Architecture du cluster EKS

## Objectif

Ce document décrit le design du cluster **Amazon EKS** utilisé pendant la phase cloud validée du lab.

Même si le runtime actif a ensuite migré vers K3s, cette partie reste essentielle, car elle démontre la construction d’une plateforme Kubernetes cloud crédible, déployée et exploitée sur AWS.

## Rôle du cluster EKS

Le cluster EKS servait de cible d’exécution principale pour la première phase du lab.

Il hébergeait :

- ArgoCD
- Traefik
- cert-manager
- la demo-app
- External Secrets Operator
- Kyverno
- les ressources applicatives et plateforme gérées par GitOps

## Choix d’EKS

EKS a été retenu pour plusieurs raisons :

- service managé Kubernetes largement utilisé
- bonne intégration avec IAM et IRSA
- compatibilité naturelle avec les patterns GitOps
- très bon support des composants cloud-native
- intérêt fort pour un portfolio DevOps / DevSecOps orienté AWS

## Principes de conception

Le cluster a été conçu pour rester :

- réaliste
- compréhensible
- exploitable
- raisonnable en coût

Le lab n’avait pas vocation à reproduire toute la complexité d’une plateforme multi-cluster d’entreprise, mais à construire une base sérieuse et cohérente.

## Structure du cluster

Le design EKS comprenait :

- un cluster dédié au projet
- un node group managé
- un VPC préparé pour Kubernetes
- des subnets publics et privés adaptés à l’exposition des services
- les prérequis nécessaires à IRSA

## Add-ons et capacités

Le cluster était conçu pour supporter :

- les add-ons managés EKS
- l’ingress public via Traefik
- la gestion automatique des certificats
- la consommation de secrets depuis AWS Secrets Manager via ESO
- les policies Kyverno en mode progressif

## Intégration GitOps

Le cluster EKS était piloté par **ArgoCD**.

Le dépôt GitOps décrivait l’état désiré du cluster, et ArgoCD assurait :

- la synchronisation automatique
- le self-heal
- le prune
- la gestion déclarative des composants plateforme et applicatifs

## Intégration réseau

Le VPC et les subnets ont été taggés pour l’intégration avec les besoins réseau Kubernetes / EKS.  
L’exposition des applications reposait ensuite sur Traefik et sur le DNS public du projet.

## Intégration IAM / IRSA

Un point fort de cette phase a été l’utilisation de **IAM Roles for Service Accounts**.

Cela a permis d’éviter des credentials AWS statiques dans les pods et de donner à External Secrets Operator un accès propre à AWS Secrets Manager.

## Gestion des coûts

Le cluster a été pensé pour rester crédible sans exploser le budget :

- architecture simple
- nombre de nœuds raisonnable
- NAT Gateway unique
- logs du control plane gérés avec prudence
- progression étape par étape

## Limites assumées

Le cluster EKS du lab n’avait pas vocation à reproduire tous les mécanismes d’une plateforme entreprise complète.

Par exemple :

- pas de multi-cluster
- pas de stratégie avancée de DR
- pas d’observabilité complète dès le départ
- pas de politiques sécurité exhaustives immédiatement

Ces limites étaient volontaires afin de privilégier la progression maîtrisée.

## Place de cette phase aujourd’hui

Le cluster EKS n’est plus le runtime actif, mais cette phase ne perd pas sa valeur :

- elle prouve que la plateforme a bien existé sur AWS
- elle prouve une intégration cloud réelle
- elle prouve un vrai chemin complet du provisioning à l’exploitation GitOps

La phase K3s vient prolonger le projet, pas annuler ce qui a été fait.

## Résumé

Le cluster EKS du lab constitue une **phase cloud validée**, sérieuse et structurée.  
Il représente la base AWS du projet et reste un élément fort de la démonstration technique globale.
