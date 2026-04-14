# Architecture de gestion des secrets

## Objectif

Ce document explique comment les secrets sont gérés dans le lab.

Il couvre :

- le modèle validé dans la phase AWS
- le modèle actif dans la phase K3s
- ce que le lab démontre déjà
- ce qui reste encore à enrichir

## Pourquoi cette partie compte

Une plateforme réaliste doit éviter :

- de stocker des secrets applicatifs en clair dans Git
- de gérer des valeurs sensibles manuellement dans Kubernetes
- de distribuer des credentials longs termes dans les manifests

Le lab garde cette exigence, même si le runtime a évolué entre la phase AWS et la phase K3s.

## Modèle AWS validé

La phase AWS validée utilisait le flux suivant :

1. un secret est stocké dans **AWS Secrets Manager**
2. **External Secrets Operator** s’authentifie à AWS via **IRSA**
3. un `ClusterSecretStore` définit la connexion au provider AWS
4. un `ExternalSecret` définit quelles données synchroniser
5. Kubernetes reçoit un `Secret` généré
6. le workload consomme ce `Secret`

Ce modèle a été réellement implémenté et validé.

## Modèle K3s actif

La phase K3s active utilise désormais un modèle différent, plus simple et plus léger :

1. **Sealed Secrets** est déployé via ArgoCD
2. la demo-app versionne un `SealedSecret` dans l’overlay `k3s-dev`
3. le contrôleur génère un `Secret` Kubernetes à partir de ce manifeste scellé
4. le workload consomme ce `Secret` via `secretKeyRef`

Pour la demo-app, ce flux est déjà actif avec :

- un `SealedSecret` nommé `demo-app-config`
- une clé `DEMO_MESSAGE`
- un patch de déploiement qui injecte cette valeur dans le conteneur

## Lecture correcte des deux phases

Le lab ne doit pas être lu comme un projet ayant abandonné la gestion propre des secrets après la phase AWS.

Il démontre désormais deux approches différentes :

- une approche **AWS-native** et externalisée pendant la phase AWS validée
- une approche **GitOps-compatible** et plus simple avec **Sealed Secrets** dans la phase K3s active

Ces deux modèles répondent à des contraintes différentes et sont documentés comme tels.

## Pourquoi le modèle K3s actuel est cohérent

Le modèle K3s actuel reste sain pour plusieurs raisons :

- il évite de stocker le secret en clair dans Git
- il reste simple à comprendre et à opérer
- il s’intègre naturellement au flux GitOps
- il suffit au périmètre actuel du lab

Le but n’est pas encore de démontrer un backend externe complexe pour K3s, mais de garder une base stable et propre.

## Forces actuelles

La gestion des secrets du lab démontre désormais :

- un vrai modèle externalisé pendant la phase AWS
- un vrai modèle actif sur K3s
- une séparation claire entre secret source, manifeste GitOps et consommation applicative
- une capacité à adapter le modèle de secrets au runtime sans casser la base du projet

## Contraintes actuelles

Le modèle K3s actif reste volontairement limité :

- le périmètre démontré concerne surtout la demo-app
- le modèle repose sur un secret scellé dans Git, pas sur un backend externe
- la rotation et les procédures de cycle de vie ne sont pas encore détaillées
- la gouvernance autour de l’usage des secrets reste encore légère

## Évolution prévue

Les prochaines améliorations possibles sur cette brique sont :

- enrichir la documentation opérationnelle autour de Sealed Secrets
- documenter plus finement les procédures de rotation et de renouvellement
- décider plus tard si un backend externe devient utile sur K3s
- garder la solution actuelle tant qu’elle reste adaptée à la taille du lab

## Résumé

La gestion des secrets est déjà une capacité démontrée du lab.

- **AWS validé** : External Secrets + AWS Secrets Manager + IRSA
- **K3s actif** : Sealed Secrets + Secret Kubernetes consommé par la demo-app

Le sujet n’est donc plus l’absence de stratégie de secrets sur K3s, mais la consolidation et l’enrichissement progressif du modèle déjà en place.
