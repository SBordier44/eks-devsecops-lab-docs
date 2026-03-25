# Architecture réseau, ingress et TLS

## Objectif

Ce document explique comment l’exposition applicative fonctionne dans le lab.

Il couvre :

- le socle réseau actif
- la gestion de l’ingress avec Traefik
- l’automatisation TLS avec cert-manager et Let’s Encrypt
- le chemin d’exposition validé sur AWS
- le chemin d’exposition actif sur K3s

## Périmètre

Ce document se concentre sur la chaîne d’exposition entre un utilisateur final et la demo-app.

Il ne cherche pas à documenter tous les détails bas niveau de l’infrastructure.

## Modèle d’exposition actif

La demo-app est actuellement exposée via Kubernetes ingress sur le runtime **K3s**.

Le chemin d’exposition actif est :

1. le nom DNS public pointe vers le VPS
2. le trafic atteint la couche ingress Kubernetes
3. **Traefik** route la requête vers le service applicatif
4. **cert-manager** gère les certificats TLS avec **Let’s Encrypt**

Le hostname public actif est :

- `srv712424.hstgr.cloud`

## Modèle d’exposition AWS validé

La phase AWS validée utilisait un chemin d’exposition différent :

- DNS public
- cluster EKS
- Traefik
- cert-manager
- Let’s Encrypt

Le hostname utilisé dans cette phase était :

- `app-demo.sedecy-it.net`

Cette étape reste documentée comme une phase réelle du lab.

## Choix de Traefik

Le lab utilise **Traefik** comme ingress controller.

Ce choix est cohérent avec les objectifs du projet :

- contrôleur ingress moderne et simple
- intégration fluide avec les ressources `Ingress`
- support clair du HTTPS
- bon compromis entre fonctionnalités et complexité opérationnelle

## Configuration d’ingress active

L’overlay `k3s-dev` de la demo-app définit une ressource `Ingress` avec :

- `ingressClassName: traefik`
- le routage vers le service `demo-app` sur le port 80
- une configuration TLS pour `srv712424.hstgr.cloud`
- une annotation cert-manager pointant vers `letsencrypt-prod`

L’ingress reste donc le point de convergence entre :

- le routage
- le hostname
- l’émission du certificat

## Modèle TLS

Le TLS est automatisé par :

- **cert-manager**
- **Let’s Encrypt**
- un `ClusterIssuer` de production

C’est un point fort du lab, car cela prouve une vraie exposition publique HTTPS et non une simple démonstration locale ou auto-signée.

## Pourquoi c’est important

Cette partie démontre :

- une exposition DNS publique réelle
- une automatisation de certificats réelle
- un routage Kubernetes réel
- une intégration pratique entre ingress, DNS et TLS
- une continuité de l’exposition HTTPS malgré le changement de runtime

## Forces actuelles

Le socle actuel démontre :

- un hostname public actif
- l’émission automatique de certificat
- le routage Kubernetes via Traefik
- l’intégration fonctionnelle avec cert-manager
- une migration réussie du chemin public hors AWS

## Contraintes actuelles

Le runtime reste volontairement simple :

- un hostname public principal
- un workload principal
- pas de stratégie multi-ingress complexe
- pas de segmentation avancée du trafic

Ces contraintes sont acceptables à ce stade du lab.

## Évolutions prévues

Plus tard, cette partie pourra être enrichie avec :

- des runbooks DNS plus détaillés
- du troubleshooting TLS plus riche
- davantage de scénarios de validation réseau
- une documentation réseau plateforme plus poussée

## Résumé

Le lab expose aujourd’hui sa demo-app via un chemin **K3s + Traefik + cert-manager + Let’s Encrypt** propre et fonctionnel.  
La phase AWS reste une étape validée, mais le runtime public actif est désormais hébergé sur VPS.
