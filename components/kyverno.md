# Composant Kyverno

## Objectif

Ce document décrit le rôle de **Kyverno** dans le lab.

## Rôle du composant

Kyverno apporte une première couche de gouvernance sur les workloads Kubernetes.

Le lab l’utilise pour :

- observer les écarts de conformité
- structurer une politique plateforme progressive
- renforcer la qualité et la sécurité des manifests

## Mise en œuvre

Kyverno est installé via ArgoCD.  
Les policies sont gérées séparément, également via ArgoCD.

Les premières rules sont appliquées en mode `Audit` sur le namespace `demo-app-dev`.

## Philosophie d’adoption

Le choix a été de commencer par des policies non bloquantes afin de :

- ne pas casser le socle existant
- observer d’abord
- corriger ensuite
- envisager l’`Enforce` plus tard seulement

## Phase AWS / phase K3s

Kyverno a été introduit pendant la phase AWS du lab, puis conservé dans la phase K3s active.

Cela montre que la gouvernance n’est pas liée à un seul runtime et peut continuer à évoluer après migration.

## Résumé

Kyverno est un des axes principaux de durcissement du lab.  
Il est déjà actif, mais son périmètre reste volontairement limité pour rester progressif.
