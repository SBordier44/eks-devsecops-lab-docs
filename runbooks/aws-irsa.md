# Runbook AWS IRSA

## Objectif

Ce runbook documente les vérifications autour d’**IRSA** dans la phase AWS validée.

IRSA a permis au lab de connecter proprement Kubernetes et AWS, notamment pour External Secrets Operator.

## Vérifications de base

### Vérifier le service account annoté

```bash
kubectl get sa -n <namespace> <serviceaccount> -o yaml
```

### Vérifier le rôle IAM ciblé

Contrôler :

- l’ARN du rôle
- la trust policy OIDC
- les permissions attachées

### Vérifier le provider OIDC du cluster

S’assurer que le cluster EKS et le provider OIDC sont correctement alignés.

## Symptômes fréquents

### Accès AWS refusé

- mauvais rôle IAM
- trust policy incorrecte
- annotation manquante ou incorrecte
- namespace / service account non alignés

### ESO ne récupère pas les secrets

- permissions insuffisantes
- rôle non assumé
- store mal configuré

## Résumé

Ce runbook reste la référence opérationnelle IRSA pour la phase AWS validée.
