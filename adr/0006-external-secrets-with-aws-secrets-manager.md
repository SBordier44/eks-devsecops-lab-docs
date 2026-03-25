# ADR 0006 - Utiliser External Secrets Operator avec AWS Secrets Manager

## Statut

Accepté

## Contexte

Le lab devait démontrer un modèle de gestion des secrets plus crédible que :

- des secrets en clair dans Git
- des secrets manipulés manuellement dans Kubernetes
- des credentials AWS statiques

## Décision

Utiliser **External Secrets Operator** avec **AWS Secrets Manager** et **IRSA** dans la phase AWS.

## Conséquences

### Positives

- modèle de secrets externalisé et réaliste
- intégration propre avec AWS
- pas de credentials AWS longue durée dans les pods

### Négatives

- dépendance forte au runtime AWS
- modèle non réutilisable tel quel sur K3s sans adaptation

## Remarque de phase 2

Cette décision reste valide pour la phase AWS.  
La phase K3s impose simplement de choisir un nouveau standard actif pour les secrets.
