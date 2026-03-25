# Composant Traefik

## Objectif

Ce document décrit le rôle de **Traefik** dans le lab.

## Rôle du composant

Traefik est l’ingress controller utilisé pour exposer les applications du cluster.

Il permet de :

- router le trafic HTTP/HTTPS
- s’intégrer proprement avec les ressources `Ingress`
- servir de point d’entrée public
- fonctionner avec cert-manager pour le TLS

## Phase AWS validée

Traefik faisait déjà partie du socle de la plateforme sur EKS.  
Il servait à exposer la demo-app via le DNS public de la phase AWS.

## Phase K3s active

Traefik est toujours utilisé dans la phase active sur K3s.  
Il est déployé via ArgoCD et sert à exposer la demo-app sur le VPS.

## Pourquoi ce choix est cohérent

Traefik reste un bon compromis pour le lab :

- simple à comprendre
- moderne
- pratique avec GitOps
- adapté à une plateforme de petite taille

## Résumé

Traefik est un composant transversal du lab.  
Il assure la continuité du modèle d’exposition entre la phase AWS validée et la phase K3s active.
