# Vue d’ensemble de l’architecture

## Objectif

Le **EKS DevSecOps Lab** est un projet de platform engineering pratique conçu pour démontrer une plateforme Kubernetes réaliste, avec une forte logique **DevSecOps**, **GitOps** et **cloud-native**.

Le projet s’appuie aujourd’hui sur **deux phases d’architecture complémentaires** :

- une **phase AWS validée**, construite autour de Terraform, Terragrunt, Amazon EKS, Amazon ECR, GitHub OIDC, External Secrets Operator, AWS Secrets Manager et IRSA
- une **phase K3s active**, hébergée sur un VPS, qui permet de continuer l’évolution du lab avec les mêmes principes de delivery, de gouvernance et d’exploitation, tout en maîtrisant le coût du runtime

Cette documentation n’a pas pour but de masquer ce changement, mais au contraire de l’expliquer proprement.

## Périmètre

L’architecture du lab est décrite à travers quatre couches :

1. **Couche infrastructure**  
   Infrastructure AWS validée et infrastructure de runtime active sur VPS

2. **Couche delivery applicatif**  
   Build d’image, publication dans un registre et propagation du changement vers GitOps

3. **Couche GitOps**  
   Orchestration des déploiements Kubernetes avec ArgoCD

4. **Couche services plateforme**  
   Ingress, TLS, gouvernance des workloads, sécurité progressive, gestion des secrets

## Principes de conception

Le lab suit volontairement une approche pragmatique et progressive :

- garder l’architecture lisible
- éviter l’abstraction inutile trop tôt
- utiliser Git comme source de vérité
- séparer clairement les responsabilités entre dépôts
- améliorer la sécurité par étapes
- conserver l’historique validé au lieu de le réécrire
- maintenir un équilibre entre réalisme, simplicité et coût

## Architecture de haut niveau

Le fonctionnement global du lab est désormais le suivant :

- le dépôt **infra** documente et a permis de valider la phase AWS
- le dépôt **app** construit l’image de la demo-app et la publie dans le registre utilisé par la phase active
- le dépôt **GitOps** contient l’état désiré Kubernetes
- **ArgoCD** réconcilie cet état désiré dans le cluster actif
- **Traefik** expose les applications
- **cert-manager** gère l’obtention et le renouvellement des certificats **Let’s Encrypt**
- **Kyverno** applique une première gouvernance non bloquante sur les workloads

## Modèle de maturité d’architecture

Le lab doit être lu à travers trois vues complémentaires.

### 1. Architecture validée

Elle couvre les composants réellement implémentés et validés durant la phase AWS.

### 2. Architecture active

Elle couvre la plateforme actuellement en fonctionnement sur K3s.

### 3. Direction cible

Elle couvre les capacités identifiées comme prochaines étapes : stratégie de secrets adaptée à K3s, supply chain plus robuste, politiques plus larges, observabilité renforcée, runbooks plus riches.

## Responsabilités des dépôts

### Dépôt infrastructure

Porte la phase AWS validée : VPC, EKS, ECR, GitHub OIDC et stratégie de remote state Terraform.

### Dépôt application

Porte le code de la demo-app, le Dockerfile, le pipeline CI et la publication de l’image.

### Dépôt GitOps

Porte les manifests Kubernetes, les applications ArgoCD, les overlays Kustomize, les ressources d’ingress, les policies Kyverno et les chemins de bootstrap.

### Dépôt documentation

Porte la description d’architecture, les ADR et les runbooks.

## État actif actuel

La plateforme active comprend aujourd’hui :

- un cluster K3s single-node sur VPS
- un bootstrap ArgoCD dédié
- le déploiement GitOps de la demo-app
- Traefik
- cert-manager et Let’s Encrypt
- Kyverno avec policies initiales
- un overlay `k3s-dev` pour la demo-app
- une exposition publique TLS fonctionnelle

## État validé historique

La phase AWS validée comprenait :

- réseau AWS et VPC
- cluster Amazon EKS
- registre Amazon ECR
- authentification GitHub Actions vers AWS par OIDC
- External Secrets Operator
- AWS Secrets Manager
- IRSA
- un chemin complet cloud de build, publication et déploiement

Cette phase reste centrale dans le projet car elle démontre de vraies compétences cloud.

## Thèmes d’évolution

Les prochaines grandes directions du lab sont :

- réintroduire une gestion des secrets cohérente dans la phase K3s
- élargir la couverture Kyverno
- durcir progressivement la supply chain
- enrichir les garde-fous de sécurité et les runbooks
- mieux illustrer l’architecture et les flux

## Écart entre état actif et cible

Le lab possède déjà une base solide, mais certaines briques restent à compléter dans la phase active :

- la gestion des secrets côté K3s n’est pas encore réintroduite
- la couverture des policies reste volontairement limitée
- la supply chain sécurisée est encore à un niveau de maturité intermédiaire
- l’observabilité et l’exploitation peuvent être enrichies
- d’autres garde-fous plateforme restent à mettre en place

## Suite logique

Les prochaines étapes les plus cohérentes sont :

- documenter clairement la phase active K3s sans dévaloriser la phase AWS
- définir un modèle de secrets stable pour K3s
- élargir la gouvernance et le durcissement de sécurité
- continuer à améliorer les runbooks et la narration d’architecture

## Source de vérité

Cette architecture s’appuie sur le contenu des dépôts :

- `eks-devsecops-lab-infra`
- `eks-devsecops-lab-app`
- `eks-devsecops-lab-gitops`
- `eks-devsecops-lab-docs`

En cas d’écart entre documentation et implémentation, l’implémentation doit être considérée comme la source de vérité pour l’état validé ou actif.
