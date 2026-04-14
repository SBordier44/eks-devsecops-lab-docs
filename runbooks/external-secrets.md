# Runbook External Secrets

## Objectif

Ce runbook documente les opérations de vérification autour d’**External Secrets Operator** dans la phase AWS validée du lab.

Il reste utile comme référence historique et opérationnelle pour la phase AWS.  
Il ne correspond pas au modèle actif de gestion des secrets sur K3s, qui repose aujourd’hui sur **Sealed Secrets**.

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

Les points prioritaires à vérifier sont :

- l’état du `ClusterSecretStore`
- le statut de l’`ExternalSecret`
- les permissions IAM / IRSA
- l’existence de la clé côté AWS Secrets Manager
- les logs de l’opérateur

## Logs utiles

```bash
kubectl logs -n external-secrets deploy/external-secrets
```

## Lecture correcte par phase

Ce runbook concerne la **phase AWS validée**.

Pour la phase K3s active :

- ne pas chercher ici un diagnostic du modèle courant
- se référer à la documentation de gestion des secrets K3s et au déploiement Sealed Secrets actif

## Résumé

Ce runbook reste la référence opérationnelle de la phase AWS validée pour **External Secrets Operator**.

La phase K3s active suit aujourd’hui une autre stratégie de secrets, plus simple et déjà en place.
