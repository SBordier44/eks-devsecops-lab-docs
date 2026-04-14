# Modèle de sécurité de la plateforme

## Objectif

Ce document décrit le modèle de sécurité appliqué dans le lab, en distinguant :

- ce qui a déjà été validé
- ce qui est actuellement actif
- ce qui reste encore à renforcer

Le lab n’a pas vocation à prétendre couvrir tout le périmètre d’une plateforme d’entreprise.  
Son objectif est de démontrer une trajectoire crédible, progressive et techniquement cohérente.

## Principes

Le modèle de sécurité du lab repose sur quelques principes simples :

- améliorer la sécurité par couches
- éviter la complexité gratuite
- privilégier les mécanismes natifs et compréhensibles
- garder une traçabilité forte par Git
- ajouter les garde-fous progressivement plutôt que tout bloquer d’un coup

## Sécurité déjà démontrée

Le lab démontre déjà plusieurs briques de sécurité solides :

- provisioning d’infrastructure par code
- séparation claire des dépôts
- usage de GitOps pour la réconciliation
- exposition TLS automatique
- image applicative minimale, non-root et orientée runtime léger
- authentification GitHub vers AWS via OIDC dans la phase cloud
- utilisation d’IRSA dans la phase AWS
- gestion des secrets via External Secrets dans la phase AWS
- gestion des secrets via Sealed Secrets dans la phase K3s active
- scan Trivy bloquant, SARIF et SBOM dans la CI
- signature et vérification Cosign dans la CI
- gouvernance Kyverno active sur la demo-app

## Couche runtime

### Phase AWS validée

Pendant la phase AWS, la sécurité du runtime s’appuyait notamment sur :

- IAM / OIDC
- IRSA
- un registre ECR dédié
- External Secrets Operator branché sur AWS Secrets Manager
- Kyverno déployé via GitOps

### Phase K3s active

Dans la phase active K3s, le socle de sécurité repose sur :

- GitOps avec ArgoCD
- Traefik et TLS Let’s Encrypt
- Sealed Secrets pour la gestion active du secret applicatif démontré
- GHCR comme registre cible
- Kyverno avec un périmètre ciblé sur la demo-app
- une base applicative simple, lisible et déjà durcie au niveau conteneur

## Gouvernance des workloads avec Kyverno

Le lab applique une gouvernance Kyverno ciblée sur `demo-app-dev`.

Dans l’état actif visible dans le dépôt GitOps :

- `require-container-resources-demo-app` est en `Enforce`
- `require-container-securitycontext-demo-app` est en `Enforce`
- `verify-signed-demo-app-image` est en `Audit`

La stratégie adoptée n’est donc plus un simple démarrage en `Audit` généralisé.  
Le lab utilise désormais une combinaison pragmatique :

- `Enforce` pour des règles de base déjà stabilisées
- `Audit` pour la vérification de signature, encore observée de manière progressive

## Gestion des secrets

La gestion des secrets doit être lue en distinguant les deux phases du lab.

### Phase AWS validée

La phase AWS démontre un modèle réaliste basé sur :

- AWS Secrets Manager
- External Secrets Operator
- IRSA
- synchronisation vers des `Secret` Kubernetes consommés par les workloads

### Phase K3s active

La phase K3s active démontre aujourd’hui un modèle plus simple, mais fonctionnel, basé sur :

- le déploiement de **Sealed Secrets** via ArgoCD
- un `SealedSecret` versionné dans Git pour la demo-app
- la consommation du `Secret` généré par le workload via `secretKeyRef`

Cette approche est volontairement plus simple que le modèle AWS.  
Elle reste cohérente avec la taille actuelle du lab et son objectif de progression maîtrisée.

## Supply chain

Le durcissement de la supply chain n’est plus un objectif théorique.

Le lab démontre déjà :

- un scan Trivy bloquant
- un upload SARIF vers GitHub Security
- un SBOM CycloneDX
- une signature Cosign keyless
- une vérification Cosign explicite
- une policy Kyverno `verifyImages` côté cluster

Le retour d’expérience sur la compatibilité Cosign / Kyverno fait désormais partie du modèle de sécurité opérationnel du lab.  
La version de Cosign retenue est donc volontairement figée tant qu’une nouvelle validation complète n’a pas été menée.

## Limites assumées

Le modèle de sécurité actuel n’est pas exhaustif.

Il manque encore, selon la prochaine cible raisonnable du lab :

- une immuabilité complète par digest dans le flux GitOps
- un périmètre Kyverno plus large
- davantage de garde-fous réseau et RBAC si le lab continue d’évoluer
- une observabilité sécurité plus riche
- une documentation plus détaillée sur la gestion opérationnelle des secrets côté K3s

## Résumé

Le modèle de sécurité du lab est déjà crédible et cohérent.

La phase AWS prouve une intégration cloud sérieuse.  
La phase K3s active poursuit le durcissement de manière pragmatique avec :

- TLS
- Sealed Secrets
- GitOps
- Kyverno
- scan, SBOM, signature et vérification d’image

La prochaine étape la plus logique reste le renforcement de l’immuabilité du déploiement applicatif, sans remettre en cause la stabilité de la base actuelle.
