# Modèle de sécurité de la plateforme

## Objectif

Ce document décrit le modèle de sécurité appliqué dans le lab, en distinguant ce qui a déjà été validé de ce qui est en cours d’extension.

Le lab n’a pas vocation à prétendre couvrir toute la sécurité d’une plateforme d’entreprise, mais à montrer une trajectoire crédible, progressive et techniquement cohérente.

## Principes

Le modèle de sécurité du lab repose sur quelques idées simples :

- améliorer la sécurité par couches
- éviter la complexité gratuite
- privilégier les mécanismes natifs et compréhensibles
- garder une traçabilité forte par Git
- ajouter les garde-fous progressivement plutôt que tout bloquer d’un coup

## Sécurité déjà démontrée

Le lab démontre déjà plusieurs briques de sécurité :

- provisioning d’infrastructure par code
- séparation claire des dépôts
- usage de GitOps pour la réconciliation
- exposition TLS automatique
- image applicative minimale et non-root
- authentification GitHub vers AWS via OIDC dans la phase cloud
- utilisation d’IRSA dans la phase AWS
- policies Kyverno initiales en mode `Audit`

## Couche runtime

### Phase AWS validée

Pendant la phase AWS, la sécurité du runtime s’appuyait notamment sur :

- IAM / OIDC
- IRSA
- un registre ECR dédié
- ESO branché sur AWS Secrets Manager
- Kyverno déployé via GitOps

### Phase K3s active

Dans la phase active K3s, le socle de sécurité repose sur :

- GitOps avec ArgoCD
- Traefik et TLS Let’s Encrypt
- policies Kyverno initiales
- base applicative simple et lisible
- séparation claire entre ce qui est déjà actif et ce qui reste à faire

## Gouvernance des workloads avec Kyverno

Le lab applique déjà une première gouvernance Kyverno sur `demo-app-dev`.

Les premières règles sont non bloquantes et tournent en mode `Audit`, ce qui permet :

- d’observer les écarts
- d’éviter la casse brutale
- de renforcer progressivement la politique plateforme

Cette approche progressive est volontaire.

## Secrets

La phase AWS validée inclut un modèle de secrets propre et réaliste.  
La phase K3s active n’a pas encore son modèle cible. Ce point est assumé et documenté comme prochaine étape.

## Supply chain

Le lab dispose déjà d’une supply chain claire, mais son durcissement sécurité reste encore en progression.

Les prochaines étapes attendues concernent notamment :

- SBOM
- signature d’images
- meilleure formalisation de la chaîne de confiance

## Limites assumées

Le modèle de sécurité actuel n’est pas exhaustif.

Il manque encore, selon la cible finale :

- une stratégie de secrets active sur K3s
- un périmètre de policies plus large
- davantage de garde-fous réseau et RBAC si le lab continue d’évoluer
- une observabilité sécurité plus riche

## Résumé

Le modèle de sécurité du lab est déjà crédible et cohérent.  
La phase AWS prouve une intégration cloud sérieuse, et la phase K3s poursuit le durcissement de manière pragmatique, sans prétendre avoir déjà atteint l’état final.
