# Runbook External Secrets

## Objectif

Ce runbook documente les opérations de vérification autour d’External Secrets Operator dans la phase AWS validée du lab.

Il reste utile comme référence, même si la phase active K3s n’utilise pas encore de stratégie de secrets équivalente.

## Vérifications de base

### Vérifier l’opérateur

```bash
kubectl get pods -n external-secrets
```

### Vérifier les CRDs

```bash
kubectl get crd | grep external-secrets
```

### Vérifier les SecretStore / ClusterSecretStore

```bash
kubectl get clustersecretstore
kubectl describe clustersecretstore <nom>
```

### Vérifier les ExternalSecret

```bash
kubectl get externalsecret -A
kubectl describe externalsecret <nom> -n <namespace>
```

### Vérifier les Secret générés

```bash
kubectl get secret -n <namespace>
```

## Points de diagnostic

- état du `ClusterSecretStore`
- statut de l’`ExternalSecret`
- permissions IAM / IRSA
- existence de la clé côté AWS Secrets Manager
- logs de l’opérateur

## Logs utiles

```bash
kubectl logs -n external-secrets deploy/external-secrets
```

## Résumé

Ce runbook reste la référence opérationnelle de la phase AWS validée pour ESO.
