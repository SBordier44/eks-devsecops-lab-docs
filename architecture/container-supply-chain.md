# Architecture de la supply chain conteneur

## Objectif

Ce document décrit la chaîne de fabrication, de contrôle, de publication et de déploiement des images de conteneurs utilisées par le lab.

Il couvre l’état réellement validé de la supply chain applicative, d’abord sur AWS puis dans la phase K3s active.

## Vue d’ensemble

La supply chain du lab repose sur une séparation nette entre :

- le dépôt applicatif
- le pipeline CI/CD
- le registre d’images
- le dépôt GitOps
- le cluster cible

Le dépôt applicatif produit l’image.  
Le pipeline la contrôle et la signe.  
Le dépôt GitOps déclare l’état voulu.  
ArgoCD réconcilie cet état vers le cluster.  
Kyverno applique ensuite une vérification complémentaire côté admission.

## Phase AWS validée

La première variante validée du lab reposait sur :

- GitHub Actions
- construction de l’image
- publication vers **Amazon ECR**
- mise à jour du dépôt GitOps
- déploiement sur **EKS**

Cette phase reste une preuve valable de chaîne cloud de bout en bout.

## Phase K3s active

La phase active conserve la même logique générale, mais avec un runtime et un registre différents :

- le pipeline GitHub Actions construit l’image
- l’image est publiée sur **GHCR**
- le dépôt GitOps est mis à jour sur l’overlay `apps/demo-app/overlays/k3s-dev`
- ArgoCD déploie la demo-app sur **K3s**
- Kyverno applique une policy `verifyImages` sur la charge ciblée

## Contrôles actuellement en place

Dans l’état actuellement validé, le pipeline applicatif réalise :

- le build de l’image
- un scan Trivy bloquant sur les vulnérabilités `HIGH` et `CRITICAL`
- l’upload du résultat SARIF vers GitHub Security
- la génération d’un **SBOM CycloneDX**
- la signature **Cosign keyless**
- une vérification **Cosign** de l’image publiée
- la mise à jour du dépôt GitOps

Côté cluster, le dépôt GitOps déclare :

- deux policies Kyverno de conformité workload en `Enforce`
- une policy Kyverno `verifyImages` en `Audit` pour la demo-app

## Chaîne active détaillée

Dans la phase K3s active, la chaîne se déroule ainsi :

1. un commit sur le dépôt applicatif déclenche le pipeline
2. l’image est construite et poussée sur **GHCR**
3. Trivy génère un **SBOM CycloneDX**
4. Trivy exécute un scan bloquant et publie un **SARIF**
5. Cosign signe l’image publiée en mode keyless
6. Cosign vérifie cette image sur son digest
7. le dépôt GitOps est mis à jour pour l’overlay `k3s-dev`
8. ArgoCD réconcilie l’état vers K3s
9. Kyverno vérifie la signature attendue à l’admission sur le périmètre ciblé

## Chaîne de confiance actuelle

La chaîne de confiance démontrée par le lab n’est plus limitée au simple build et au push d’image.

Elle couvre désormais :

- le contrôle de sécurité pendant le pipeline
- la traçabilité de l’image publiée
- la signature de l’image
- la vérification explicite de cette signature dans la CI
- une vérification complémentaire côté cluster via Kyverno

Cela constitue déjà une supply chain crédible, progressive et réellement démontrée.

## Contrainte technique connue : compatibilité Cosign / Kyverno

Un retour d’expérience important a été observé pendant la mise en place de la vérification de signature.

### Symptôme observé

Avec une version plus récente de Cosign que celle finalement retenue, la chaîne de vérification ne se comportait plus comme attendu et l’erreur rencontrée était de type :

```text
no signatures found
```

### Décision retenue

Dans l’état validé du lab, la version de Cosign est donc volontairement **pinnée** sur une version compatible.

Cette décision est assumée, car elle permet de :

- préserver une chaîne de confiance réellement fonctionnelle
- éviter une régression silencieuse côté vérification
- documenter une contrainte observée en conditions réelles

### Règle de gestion

Le lab ne met pas à jour Cosign uniquement pour suivre la dernière version disponible.

Toute évolution future de version doit être précédée d’une revalidation bout en bout incluant au minimum :

1. signature réussie en CI
2. vérification Cosign réussie
3. admission Kubernetes correcte pour une image signée valide
4. refus ou écart attendu pour une image non signée ou invalide
5. contrôle explicite des logs et événements Kyverno

## Limites actuelles

La supply chain est déjà sérieuse, mais elle n’a pas encore atteint son état cible.

Les limites visibles dans l’existant sont notamment :

- le flux GitOps déploie encore la demo-app via **tag** et non via **digest**
- la policy `verifyImages` est encore en `Audit`
- la policy `verifyImages` n’impose pas encore `verifyDigest`
- l’immuabilité complète du déploiement n’est donc pas encore activée de bout en bout

## Prochaine évolution la plus logique

La prochaine amélioration la plus pertinente, sans complexifier inutilement la base actuelle, consiste à renforcer l’immuabilité du déploiement applicatif :

- propagation du **digest** dans GitOps
- alignement plus fort entre image signée, image vérifiée et image effectivement déployée
- éventuelle évolution de `verifyDigest` lorsque la chaîne complète aura été revalidée

## Résumé

La supply chain applicative du lab comprend désormais :

- build
- scan bloquant
- SARIF
- SBOM
- signature Cosign
- vérification Cosign
- déploiement GitOps sur K3s
- vérification Kyverno côté cluster

Le durcissement n’est donc plus un sujet à démarrer.  
Il est déjà engagé et validé.  
La suite logique consiste surtout à renforcer proprement l’immuabilité du flux existant.
