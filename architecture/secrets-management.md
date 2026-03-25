# Architecture de gestion des secrets

## Objectif

Ce document explique comment les secrets sont gérés dans le lab.

Il couvre :

- le modèle validé dans la phase AWS
- la position actuelle sur le runtime K3s
- ce qui est déjà démontré et ce qui reste à définir

## Pourquoi cette partie compte

Une plateforme réaliste doit éviter :

- de stocker des secrets applicatifs en clair dans Git
- de gérer des valeurs sensibles manuellement dans Kubernetes
- de distribuer des credentials longs termes dans les manifests

Le lab garde cette exigence, même si le runtime a évolué.

## Modèle AWS validé

La phase AWS validée utilisait le flux suivant :

1. un secret est stocké dans **AWS Secrets Manager**
2. **External Secrets Operator** s’authentifie à AWS via **IRSA**
3. un `ClusterSecretStore` définit la connexion au provider AWS
4. un `ExternalSecret` définit quelles données synchroniser
5. Kubernetes reçoit un `Secret` généré
6. le workload consomme ce `Secret`

Ce modèle a été réellement implémenté et validé.

## Composants historiques de la phase AWS

Le socle validé comprenait :

- les CRDs External Secrets
- le contrôleur External Secrets Operator
- un `ClusterSecretStore` AWS
- un `ExternalSecret` dans l’overlay applicatif
- un rôle IAM dédié via IRSA
- la synchronisation effective entre AWS Secrets Manager et Kubernetes

## Position actuelle sur K3s

Le runtime actif **K3s** ne réutilise pas actuellement le modèle AWS de gestion des secrets.

C’est un choix volontaire.

La migration vers K3s a d’abord reconstruit le minimum stable :

- K3s
- ArgoCD
- Traefik
- cert-manager
- Kyverno
- demo-app

La gestion des secrets côté K3s n’a pas encore été réintroduite à ce stade.

## Pourquoi c’est acceptable

Cette approche reste saine pour plusieurs raisons :

- les dépendances AWS-specific n’ont pas été copiées aveuglément
- la base de plateforme a été stabilisée avant d’ajouter une nouvelle brique sensible
- l’écart est explicite et documenté, pas caché

## Ce que le lab démontre déjà malgré tout

Même dans cet état transitoire, le lab démontre :

- un vrai modèle externalisé de gestion de secrets dans la phase AWS
- une séparation claire entre phase validée et phase active
- une migration de runtime disciplinée
- une capacité à documenter précisément ce qui reste à faire

## Contraintes actuelles

La phase active K3s a pour limites actuelles :

- pas de synchronisation de secrets active encore en place
- pas encore de backend de secrets retenu comme standard K3s
- pas encore d’exemple applicatif actif consommant des secrets côté K3s

## Évolution prévue

La suite logique consiste à introduire une stratégie de secrets compatible K3s qui reste :

- simple
- compréhensible
- compatible avec GitOps
- cohérente avec la taille actuelle du lab

## Résumé

La gestion des secrets est déjà une capacité **validée** du lab grâce à la phase AWS.  
Ce qui reste ouvert n’est pas la capacité à faire proprement, mais le choix du modèle qui deviendra le standard actif de la phase K3s.
