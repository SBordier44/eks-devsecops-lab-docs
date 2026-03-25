# Runbook ArgoCD

## Objectif

Ce runbook décrit les opérations courantes autour d’ArgoCD dans le lab.

Il couvre principalement la phase active K3s, tout en rappelant les éléments utiles de la phase AWS.

## Vérifications de base

### Lister les applications

```bash
kubectl get applications -n argocd
```

### Vérifier les pods ArgoCD

```bash
kubectl get pods -n argocd
```

### Décrire une application

```bash
kubectl describe application <nom> -n argocd
```

## Forcer un refresh

```bash
kubectl annotate application <nom> -n argocd argocd.argoproj.io/refresh=hard --overwrite
```

## Lire l’état de synchro

Points à vérifier :

- `Sync Status`
- `Health Status`
- conditions de type `ComparisonError` ou erreurs de rendu

## Bootstrap K3s

Le runtime actif s’appuie sur une application racine dédiée pointant vers le layout `argocd-k3s/`.

Cela permet de ne pas embarquer les dépendances AWS encore historiques dans le bootstrap actif.

## Cas fréquents de panne

### `Sync Status: Unknown`

Vérifier :

- chemin Git
- rendu Kustomize
- erreurs de ressource hors répertoire d’application
- contenu exact de `kubectl describe application`

### Application `Degraded`

Vérifier :

- événements Kubernetes du namespace concerné
- `Deployment`, `Service`, `Ingress`, `Certificate`
- état des pods et des probes

### Drift Helm / OutOfSync temporaire

Attendre quelques instants après bootstrap ou upgrade, puis relancer un refresh si nécessaire.

## Résumé

ArgoCD est la clef de voûte du lab.  
Ce runbook sert à diagnostiquer et maintenir la couche GitOps du runtime actif.
