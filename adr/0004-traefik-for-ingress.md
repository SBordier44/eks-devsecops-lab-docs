# ADR 0004 - Utiliser Traefik pour l’ingress

## Statut

Accepté

## Contexte

Le lab avait besoin d’un contrôleur ingress :

- simple
- moderne
- facile à intégrer
- compatible avec un fonctionnement GitOps

## Décision

Utiliser **Traefik** comme ingress controller principal.

## Conséquences

### Positives

- bonne lisibilité
- intégration fluide avec Kubernetes
- bon support HTTPS avec cert-manager
- continuité entre la phase AWS et la phase K3s

### Négatives

- moins standard dans certains environnements que d’autres alternatives
- besoin de bien comprendre la logique d’exposition des services

## Remarque de phase 2

La continuité de Traefik entre EKS et K3s confirme que ce choix était robuste pour le projet.
