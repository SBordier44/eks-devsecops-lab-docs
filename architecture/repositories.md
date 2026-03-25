# Architecture des dépôts

## Objectif

Le lab est volontairement réparti en plusieurs dépôts afin de séparer les responsabilités et de rendre la plateforme plus lisible, plus simple à faire évoluer et plus proche d’une organisation d’ingénierie réaliste.

Cette structuration reflète un fonctionnement courant où l’infrastructure, le code applicatif, l’état de déploiement et la documentation vivent dans des dépôts distincts.

## Liste des dépôts

### 1. eks-devsecops-lab-infra

Ce dépôt est responsable de la **phase AWS validée**.

Il contient :

- les modules Terraform
- la structure Terragrunt du live
- le provisionnement du VPC
- le provisionnement EKS
- le provisionnement ECR
- les ressources GitHub OIDC
- la stratégie de remote state Terraform

Ce dépôt ne déploie pas directement les applications dans Kubernetes.

### 2. eks-devsecops-lab-app

Ce dépôt contient la **demo-app** et son pipeline de delivery.

Il comprend :

- le code source Go
- le Dockerfile
- le workflow GitHub Actions de build et de publication d’image
- la logique de mise à jour du dépôt GitOps après publication

Ce dépôt ne porte pas les manifests Kubernetes.

### 3. eks-devsecops-lab-gitops

Ce dépôt est la **source de vérité Kubernetes**.

Il comprend :

- les ressources de bootstrap ArgoCD
- les applications ArgoCD
- les bases et overlays Kustomize
- les manifests de la demo-app
- les ressources d’ingress
- les ressources cert-manager
- les ressources Kyverno
- les chemins GitOps de la phase AWS et de la phase K3s

Ce dépôt décrit l’état désiré de la plateforme Kubernetes.

### 4. eks-devsecops-lab-docs

Ce dépôt contient la documentation du lab.

Il comprend :

- la documentation d’architecture
- les ADR
- les runbooks
- les explications de conception
- l’histoire d’évolution du projet

## Pourquoi cette séparation est utile

Cette séparation apporte plusieurs avantages :

- des frontières de responsabilité plus claires
- un historique Git plus lisible
- une meilleure compréhension de l’impact des changements
- une meilleure cohérence avec GitOps
- une documentation plus propre
- une présentation portfolio plus crédible

## Interaction entre les dépôts

Le flux principal du lab actif est le suivant :

1. le dépôt **app** construit et publie une image de conteneur
2. le dépôt **app** met à jour le tag d’image dans le dépôt **GitOps**
3. **ArgoCD** réconcilie l’état désiré depuis le dépôt **GitOps**
4. le cluster **K3s** reçoit la nouvelle version
5. le dépôt **docs** documente l’architecture, les choix et l’exploitation

La phase AWS reste documentée par le dépôt **infra**, mais n’est plus le runtime principal utilisé au quotidien.

## État validé, état actif et intention

Cette documentation sépare volontairement :

- l’**état validé**, fondé sur des capacités réellement mises en œuvre et testées dans la phase AWS
- l’**état actif**, fondé sur le runtime K3s actuellement utilisé
- l’**intention d’architecture**, fondée sur la roadmap et les évolutions visées

Les trois dimensions sont utiles :

- l’état validé prouve la profondeur technique
- l’état actif prouve la continuité d’exploitation
- l’intention montre la maturité et la trajectoire du projet

## Source de vérité par sujet

### Phase infrastructure AWS

Source de vérité : `eks-devsecops-lab-infra`

### Build applicatif et publication d’image

Source de vérité : `eks-devsecops-lab-app`

### État Kubernetes désiré

Source de vérité : `eks-devsecops-lab-gitops`

### Architecture, rationales et runbooks

Source de vérité : `eks-devsecops-lab-docs`

## Périmètre actuel par dépôt

### Dépôt infrastructure

Périmètre validé :

- VPC et réseau AWS
- Amazon EKS
- Amazon ECR
- GitHub OIDC
- remote state Terraform

### Dépôt application

Périmètre actif :

- demo-app
- pipeline de build
- publication d’image
- mise à jour du dépôt GitOps

### Dépôt GitOps

Périmètre actif :

- bootstrap ArgoCD pour K3s
- déploiement de la demo-app sur K3s
- Traefik
- cert-manager et Let’s Encrypt
- Kyverno et policies initiales

Périmètre validé mais non actif :

- External Secrets Operator
- ressources AWS-oriented de la phase EKS
- overlays et bootstrap liés à AWS

### Dépôt documentation

Périmètre actuel :

- documentation de la phase AWS validée
- documentation de la phase active K3s
- narration de transition de plateforme
- ADR et runbooks

## Ordre de lecture recommandé

Pour une personne qui découvre le projet, l’ordre de lecture conseillé est :

1. `eks-devsecops-lab-docs`
2. `eks-devsecops-lab-infra`
3. `eks-devsecops-lab-app`
4. `eks-devsecops-lab-gitops`

Cet ordre donne d’abord le contexte, puis la phase cloud validée, ensuite le delivery applicatif, puis enfin l’état de déploiement.
