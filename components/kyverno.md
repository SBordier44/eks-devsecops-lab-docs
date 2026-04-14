# Composant Kyverno

## Objectif

Ce document décrit le rôle de **Kyverno** dans le lab.

## Rôle du composant

Kyverno apporte une couche de gouvernance sur les workloads Kubernetes.

Le lab l’utilise pour :

- contrôler des exigences minimales sur les workloads
- structurer une politique plateforme progressive
- renforcer la qualité et la sécurité des manifests
- relier le durcissement de la supply chain à l’admission Kubernetes

## Modèle de déploiement

Dans la phase K3s active, Kyverno est déployé via ArgoCD.

Le modèle est séparé en deux applications :

- une application pour installer **Kyverno**
- une application pour appliquer les **policies** du lab depuis le dépôt GitOps

Cette séparation garde le socle clair et facilite l’évolution progressive des règles.

## Policies actives sur K3s

Dans l’état actuellement déclaré sur K3s, le périmètre `demo-app-dev` comprend trois policies principales :

- `require-container-resources-demo-app`
- `require-container-securitycontext-demo-app`
- `verify-signed-demo-app-image`

Le mode de fonctionnement est désormais mixte :

- `require-container-resources-demo-app` en `Enforce`
- `require-container-securitycontext-demo-app` en `Enforce`
- `verify-signed-demo-app-image` en `Audit`

## Policy de vérification d’image signée

La policy `verify-signed-demo-app-image` vise l’image :

- `ghcr.io/sbordier44/eks-devsecops-lab-dev-app*`

Elle vérifie une signature **Cosign keyless** attendue, produite par le workflow GitHub Actions prévu pour la demo-app.

Dans l’état actuel :

- le périmètre est volontairement ciblé
- la policy reste en `Audit`
- le secret `ghcr-kyverno` est utilisé pour l’accès au registre
- l’objectif est de stabiliser la chaîne avant tout durcissement supplémentaire

## Historique AWS / état K3s

Kyverno a été introduit pendant la phase AWS du lab avec un premier socle orienté observation.

La phase K3s active ne se contente plus de reprendre cet état à l’identique :

- le socle de conformité workload est désormais passé en `Enforce`
- la vérification d’image signée a été ajoutée
- le dépôt GitOps K3s contient donc un état plus avancé que le chemin historique AWS

## Philosophie d’adoption

Le choix du lab reste progressif, mais il n’est plus limité à un simple démarrage en `Audit`.

La logique désormais appliquée est :

- `Enforce` lorsque la règle est simple, comprise et déjà absorbée par l’existant
- `Audit` lorsque la vérification mérite encore de l’observation ou une future consolidation

Cette approche garde la plateforme lisible et évite la casse inutile.

## Résumé

Kyverno est l’un des axes principaux de durcissement du lab.

Sur la phase K3s active, il ne sert plus seulement à observer.  
Il applique déjà un premier socle de gouvernance en `Enforce` et relie désormais la supply chain à l’admission Kubernetes via `verifyImages`.
