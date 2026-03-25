# Runbook Kyverno

## Objectif

Ce runbook décrit les vérifications courantes autour de **Kyverno**.

Il est utile aussi bien pour la lecture de l’état actuel que pour les prochaines étapes de durcissement.

## Vérifications de base

### Vérifier les pods Kyverno

```bash
kubectl get pods -n kyverno
```

### Vérifier les policies

```bash
kubectl get cpol,pol -A
```

### Vérifier les policy reports

```bash
kubectl get policyreport -A
kubectl get clusterpolicyreport
```

## Vérifier une policy précise

```bash
kubectl describe cpol <nom>
```

## Vérifier les violations

Selon la version et les CRDs installées :

- consulter les policy reports
- consulter les événements
- relire les ressources ciblées

## Philosophie d’usage dans le lab

Le lab commence en `Audit` pour :

- observer
- corriger les manifests
- stabiliser la base
- préparer éventuellement l’`Enforce`

## Points d’attention

- ne pas élargir trop vite le périmètre
- ne pas passer en `Enforce` sans validation préalable
- garder les policies lisibles et ciblées

## Résumé

Kyverno doit rester un outil de gouvernance progressive.  
Ce runbook sert à garder cette montée en maturité sous contrôle.
