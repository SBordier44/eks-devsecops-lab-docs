# Runbook ingress et TLS

## Objectif

Ce runbook décrit les vérifications et dépannages courants autour de Traefik, cert-manager et Let’s Encrypt.

Il est surtout utile pour la phase active K3s, tout en restant cohérent avec les principes déjà validés sur AWS.

## Vérifications de base

### Vérifier Traefik

```bash
kubectl get pods -n traefik
kubectl get svc -n traefik
kubectl get ingress -A
```

### Vérifier cert-manager

```bash
kubectl get pods -n cert-manager
kubectl get clusterissuer
kubectl get certificate -A
kubectl get certificaterequest -A
```

### Vérifier les événements

```bash
kubectl get events -A --sort-by=.lastTimestamp
```

## Vérifier l’ingress de la demo-app

```bash
kubectl get ingress -n demo-app-dev
kubectl describe ingress demo-app -n demo-app-dev
```

## Vérifier le certificat

```bash
kubectl get certificate -n demo-app-dev
kubectl describe certificate -n demo-app-dev
```

## Vérifier la résolution publique

- vérifier que le DNS pointe bien vers le VPS
- vérifier l’ouverture des ports 80 et 443
- vérifier la joignabilité du hostname public

## Symptômes fréquents

### Ingress présent mais inaccessible

- service Traefik non exposé correctement
- DNS non pointé vers le VPS
- ports fermés côté firewall / provider

### Certificat non émis

- `ClusterIssuer` non prêt
- challenge HTTP-01 non routé
- DNS incorrect
- trafic HTTP bloqué

### TLS valide mais application indisponible

- service applicatif non prêt
- pods en erreur
- ingress route mal définie

## Résumé

Ce runbook permet de diagnostiquer rapidement la chaîne publique `Traefik -> cert-manager -> application`.
