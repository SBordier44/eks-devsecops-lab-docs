# Runbook Kyverno

## Objectif

Ce runbook décrit les vérifications courantes autour de **Kyverno** dans le lab.

Il couvre :

- les contrôles de base
- la lecture des policies actives
- la vérification de `verifyImages`
- les premiers réflexes de dépannage
- le retour d’expérience connu autour de Cosign et Kyverno

## Vérifications de base

### Vérifier les pods Kyverno

```bash
kubectl get pods -n kyverno
```

### Vérifier les policies

```bash
kubectl get cpol
```

### Vérifier les policy reports

```bash
kubectl get policyreport -A
kubectl get clusterpolicyreport
```

## Vérifier les policies actives du lab

### Resources

```bash
kubectl describe cpol require-container-resources-demo-app
```

### Security context

```bash
kubectl describe cpol require-container-securitycontext-demo-app
```

### Signature d’image

```bash
kubectl describe cpol verify-signed-demo-app-image
```

## Vérifier la partie `verifyImages`

Contrôler les événements récents liés à Kyverno :

```bash
kubectl get events -A --sort-by='.lastTimestamp' | grep -i kyverno
```

Consulter les logs du contrôleur d’admission :

```bash
kubectl logs -n kyverno deploy/kyverno-admission-controller --tail=200
```

Vérifier la présence du secret utilisé pour l’accès GHCR :

```bash
kubectl get secret ghcr-kyverno -n kyverno
```

Contrôler l’image réellement utilisée par la demo-app :

```bash
kubectl get deploy demo-app -n demo-app-dev -o yaml
```

## Lecture correcte de l’état actuel

Dans la phase K3s active, le socle Kyverno n’est plus entièrement en `Audit`.

L’état visé dans le dépôt GitOps est :

- `require-container-resources-demo-app` en `Enforce`
- `require-container-securitycontext-demo-app` en `Enforce`
- `verify-signed-demo-app-image` en `Audit`

Cette lecture est importante pour diagnostiquer correctement un refus de workload ou un simple écart remonté en audit.

## Cas de dépannage important : `no signatures found`

### Symptôme

Une erreur de type ci-dessous peut apparaître lors de la vérification d’image :

```text
no signatures found
```

### Lecture correcte dans le contexte du lab

Dans le lab, ce message ne doit pas être interprété trop vite comme une absence certaine de signature.

Un cas réel a déjà été rencontré où :

- l’image était bien signée
- la chaîne attendue ne validait plus correctement le résultat
- le problème provenait d’un écart de compatibilité avec une version plus récente de Cosign

### Vérifications à faire en priorité

1. vérifier la version de Cosign utilisée dans la CI
2. vérifier que la version attendue reste bien la version pinnée et validée
3. vérifier que l’image a bien été signée sur son digest publié
4. vérifier que la policy cible bien le bon registre et le bon pattern d’image
5. vérifier que le `subject` keyless attendu correspond bien au workflow GitHub Actions prévu
6. relire les logs de `kyverno-admission-controller`

## Philosophie d’usage dans le lab

Le lab applique une logique progressive :

- introduire un contrôle utile
- le valider en conditions réelles
- documenter les comportements observés
- ne renforcer qu’après stabilisation

Cette logique s’applique aussi à `verifyImages`.

## Points d’attention

- ne pas élargir trop vite le périmètre
- ne pas modifier Cosign sans revalidation complète
- ne pas conclure à une absence de signature sans vérifier la compatibilité de version
- ne pas passer une règle en `Enforce` sans justification claire
- garder les policies lisibles, ciblées et traçables

## Résumé

Kyverno reste un outil de gouvernance progressive dans le lab.

Mais sur la phase K3s active, il ne sert plus seulement à observer : il applique déjà un premier socle `Enforce` et complète la supply chain avec une vérification d’image signée côté admission.
