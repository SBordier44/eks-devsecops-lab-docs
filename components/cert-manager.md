# Composant cert-manager

## Objectif

Ce document décrit le rôle de **cert-manager** dans le lab.

## Rôle du composant

cert-manager gère :

- l’émission de certificats
- le renouvellement automatique
- l’intégration avec Let’s Encrypt
- la consommation de certificats par les ingress

## Phase AWS validée

Dans la phase AWS, cert-manager fonctionnait déjà avec Let’s Encrypt pour automatiser le TLS de la demo-app.

## Phase K3s active

Dans la phase active, cert-manager continue à jouer exactement ce rôle sur K3s.

Le fait de retrouver la même logique d’automatisation TLS malgré le changement de runtime est un point fort du projet.

## Intégration

cert-manager fonctionne avec :

- Traefik
- des `ClusterIssuer`
- les ressources `Ingress`

## Résumé

cert-manager est un composant plateforme important du lab.  
Il démontre une exposition HTTPS propre, automatisée et réaliste dans les deux phases du projet.
