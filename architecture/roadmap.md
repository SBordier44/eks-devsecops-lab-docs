# Roadmap d’architecture

## Objectif

Ce document suit l’évolution du **EKS DevSecOps Lab** depuis sa base AWS validée jusqu’à son runtime actif K3s, puis vers sa prochaine trajectoire de maturité.

Le but est de distinguer clairement :

- ce qui est déjà implémenté et validé
- ce qui est actuellement actif
- ce qui est planifié
- ce qui reste simplement à l’étude

## Modèle de progression

Le lab évolue progressivement.

Les nouvelles capacités sont introduites une par une, validées, puis documentées avant de passer à la suivante.

Cette approche est volontaire :

- elle garde la plateforme compréhensible
- elle réduit le risque de complexité inutile
- elle préserve la stabilité
- elle rend les choix d’architecture plus faciles à expliquer
- elle garde le projet financièrement soutenable

## Légende

- **Validé** : implémenté et validé dans le lab
- **Actif** : actuellement utilisé par la plateforme en fonctionnement
- **Planifié** : clairement identifié comme prochaine étape
- **Candidat** : idée envisagée mais non encore engagée

## Socle validé

### Fondation infrastructure AWS
Statut : **Validé**

- infrastructure AWS provisionnée avec Terraform et Terragrunt
- remote state avec S3 et DynamoDB
- VPC et réseau
- cluster Amazon EKS
- registre Amazon ECR
- fédération GitHub OIDC vers AWS

### Intégration AWS-native des secrets
Statut : **Validé**

- External Secrets Operator
- accès AWS depuis Kubernetes via IRSA
- AWS Secrets Manager
- synchronisation d’`ExternalSecret` vers `Secret`

### Delivery GitOps sur AWS
Statut : **Validé**

- bootstrap ArgoCD
- déploiement de la demo-app via GitOps
- Traefik
- cert-manager
- Let’s Encrypt
- chemin complet build -> registre -> GitOps -> EKS

## Socle actif

### Runtime plateforme
Statut : **Actif**

- cluster K3s single-node sur VPS
- bootstrap ArgoCD pour le runtime actif
- modèle GitOps actif
- déploiement de la demo-app via GitOps

### Ingress et TLS
Statut : **Actif**

- Traefik
- cert-manager
- certificats Let’s Encrypt en production
- exposition HTTPS de la demo-app sur le runtime K3s

### Delivery applicatif
Statut : **Actif**

- dépôt application
- pipeline de build conteneur
- publication d’image pour le runtime actif
- mise à jour du dépôt GitOps
- déploiement de l’overlay `k3s-dev`

### Gouvernance plateforme avec Kyverno
Statut : **Actif**

- déploiement de Kyverno via ArgoCD
- application ArgoCD dédiée pour Kyverno
- application ArgoCD dédiée pour les policies
- premières policies en mode `Audit`
- périmètre initial limité à `demo-app-dev`

### Documentation
Statut : **Actif**

- documentation de la phase AWS validée
- documentation de la phase K3s active
- runbooks et ADR
- narration de transition de plateforme

## Prochaines capacités planifiées

### Stratégie de secrets compatible K3s
Statut : **Planifié**

Objectif : introduire une gestion des secrets stable sur le runtime actif sans réintroduire une complexité inutile.

Périmètre attendu :

- choix d’un modèle de source de secrets
- implémentation runtime compatible K3s
- documentation claire de la différence entre phase AWS validée et phase K3s active

### Gouvernance Kyverno élargie
Statut : **Planifié**

Objectif : étendre progressivement la couverture de policies sans déstabiliser le socle.

Périmètre attendu :

- couverture de namespaces ou workloads plus large
- policies supplémentaires en `Audit`
- évolution progressive de certaines règles vers `Enforce`
- meilleure structuration des layers de gouvernance

### Supply chain conteneur renforcée
Statut : **Planifié**

Objectif : renforcer la sécurité de la chaîne de build et de déploiement.

Périmètre attendu :

- documentation plus forte de la sécurité image
- SBOM
- signature d’images
- chaîne de confiance mieux formalisée

### Sécurité plateforme et exploitation
Statut : **Planifié**

Objectif : faire monter la maturité générale de la plateforme.

Périmètre attendu :

- runbooks plus riches
- observabilité plus poussée
- meilleures capacités de diagnostic et reprise
- garde-fous plateforme élargis

## Candidats de plus long terme

### Multi-environnements plus riches
Statut : **Candidat**

### Guardrails plus avancés
Statut : **Candidat**

### Observabilité complète
Statut : **Candidat**

### Modèle secrets plus industrialisé
Statut : **Candidat**

## Résumé

Le lab ne raconte plus une histoire linéaire unique.  
Il repose désormais sur :

- une **phase AWS validée**
- une **phase K3s active**
- une **trajectoire de durcissement planifiée**

Ce découpage n’est pas une faiblesse.  
C’est au contraire une des forces du projet, car il montre à la fois de la profondeur technique et une vraie capacité d’évolution d’architecture.
