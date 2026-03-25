# ADR 0001 - Structure du projet en plusieurs dépôts

## Statut

Accepté

## Contexte

Le lab couvre plusieurs responsabilités distinctes :

- infrastructure
- application
- déploiement GitOps
- documentation

Un dépôt unique aurait rapidement mélangé ces responsabilités.

## Décision

Le projet est structuré en plusieurs dépôts :

- `eks-devsecops-lab-infra`
- `eks-devsecops-lab-app`
- `eks-devsecops-lab-gitops`
- `eks-devsecops-lab-docs`

## Conséquences

### Positives

- responsabilités plus claires
- historique Git plus propre
- meilleure lisibilité portfolio
- alignement avec les pratiques réelles

### Négatives

- navigation initiale un peu plus éclatée
- coordination inter-dépôts à documenter

## Remarque de phase 2

La migration vers un runtime K3s ne remet pas en cause cette décision.  
Au contraire, elle la renforce, car la séparation des dépôts a permis de faire évoluer la cible d’exécution sans tout reconstruire.
