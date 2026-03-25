# ADR 0005 - Utiliser cert-manager avec Let’s Encrypt

## Statut

Accepté

## Contexte

Le lab devait démontrer une vraie exposition HTTPS, automatisée et réaliste.

## Décision

Utiliser **cert-manager** avec **Let’s Encrypt** pour l’émission et le renouvellement automatique des certificats.

## Conséquences

### Positives

- exposition HTTPS crédible
- automatisation complète du TLS
- bonne intégration avec Traefik
- continuité entre AWS et K3s

### Négatives

- dépendance au DNS et à la joignabilité publique
- nécessité de diagnostiquer les challenges HTTP-01 en cas d’erreur

## Remarque de phase 2

Le fait d’avoir pu conserver la même logique TLS lors du passage sur K3s renforce la solidité de cette décision.
