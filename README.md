# Documentation du lab EKS DevSecOps

Ce dépôt contient la documentation d’architecture, les ADR et les runbooks du projet **eks-devsecops-lab**.

Le lab a été construit en deux phases complémentaires :

- **Phase 1 validée sur AWS** : infrastructure provisionnée avec **Terraform** et **Terragrunt**, cluster **Amazon EKS**, registre **Amazon ECR**, authentification **GitHub OIDC**, gestion des secrets avec **External Secrets Operator**, **AWS Secrets Manager** et **IRSA**
- **Phase 2 active sur VPS K3s** : continuation du lab sur un **VPS K3s** afin de poursuivre l’évolution de la plateforme avec **ArgoCD**, **Traefik**, **cert-manager**, **Let’s Encrypt**, **Kyverno**, **Sealed Secrets** et la **demo-app** déployée en GitOps via **GHCR**

L’objectif du projet est de démontrer une capacité réelle à **concevoir**, **déployer**, **opérer** et **faire évoluer** une plateforme Kubernetes de type production-like, avec une approche **DevSecOps**, **GitOps** et **platform engineering**, tout en gardant une trajectoire progressive et économiquement cohérente.

## Objectifs

Le lab sert à démontrer des compétences concrètes sur :

- la conception d’infrastructure cloud avec **Terraform** et **Terragrunt**
- l’exploitation d’une plateforme Kubernetes avec **Amazon EKS** puis **K3s**
- les déploiements déclaratifs avec **ArgoCD**
- la chaîne de livraison de conteneurs avec **GitHub Actions**, **GHCR**, **Trivy**, **SBOM**, **Cosign** et **Kyverno**
- l’exposition sécurisée en HTTPS avec **Traefik**, **cert-manager** et **Let’s Encrypt**
- la gestion des secrets avec **External Secrets Operator**, **AWS Secrets Manager** et **IRSA** dans la phase AWS, puis avec **Sealed Secrets** dans la phase K3s active
- la gouvernance et le durcissement progressif de workloads avec **Kyverno**
- l’évolution d’une architecture sans repartir de zéro

## Cartographie des dépôts

Le lab est réparti en quatre dépôts :

- **eks-devsecops-lab-infra**  
  Provisionnement de l’infrastructure AWS avec Terraform et Terragrunt

- **eks-devsecops-lab-app**  
  Application de démonstration et pipeline CI/CD de build, contrôle de sécurité, signature et publication d’image

- **eks-devsecops-lab-gitops**  
  Manifests Kubernetes, applications ArgoCD et overlays Kustomize

- **eks-devsecops-lab-docs**  
  Documentation d’architecture, ADR et guides opérationnels

## Portée actuelle de la documentation

Cette documentation distingue volontairement trois niveaux :

- **Capacités validées** : éléments réellement implémentés et validés pendant la phase AWS
- **Capacités actives** : éléments actuellement utilisés sur la plateforme K3s
- **Capacités planifiées** : éléments identifiés comme prochaines étapes du lab

Cette séparation est importante pour rester transparente, crédible et professionnelle.

## Phase 1 validée : plateforme cloud AWS

La phase AWS, conservée comme preuve forte du projet, comprend :

- réseau AWS et fondation VPC
- remote state Terraform avec S3 et DynamoDB
- cluster Amazon EKS avec managed node group
- registre d’images Amazon ECR
- authentification GitHub Actions vers AWS via OIDC
- bootstrap ArgoCD et modèle GitOps
- déploiement de la demo-app via GitOps
- exposition via Traefik
- automatisation TLS avec cert-manager et Let’s Encrypt
- synchronisation de secrets depuis AWS Secrets Manager vers Kubernetes avec External Secrets Operator et IRSA
- installation de Kyverno via ArgoCD
- premières policies Kyverno en mode `Audit` sur `demo-app-dev`

## Phase 2 active : runtime K3s sur VPS

La phase active du lab comprend désormais :

- cluster **K3s single-node** sur VPS
- bootstrap ArgoCD dédié à K3s
- réconciliation GitOps active depuis le dépôt GitOps
- **Traefik** pour l’ingress
- **cert-manager** avec certificats Let’s Encrypt en production
- **Sealed Secrets** déployé via ArgoCD
- **Kyverno** avec un socle mixte de policies `Enforce` et `Audit`
- déploiement GitOps de la **demo-app** sur l’overlay `k3s-dev`
- exposition publique de l’application avec TLS valide
- registre d’images cible de la phase 2 : **GHCR**
- pipeline CI/CD incluant :
  - scan Trivy bloquant
  - upload SARIF vers GitHub Security
  - génération de SBOM
  - signature Cosign
  - vérification Cosign
  - policy Kyverno `verifyImages` côté cluster

## Capacités planifiées

Les prochaines étapes du lab incluent notamment :

- élargir progressivement la couverture Kyverno au-delà de la demo-app
- faire évoluer certaines règles encore en `Audit` lorsque cela sera justifié
- renforcer l’immuabilité du déploiement applicatif par digest dans le flux GitOps
- enrichir la documentation et la gouvernance de la gestion des secrets sur K3s
- ajouter d’autres contrôles de sécurité plateforme
- enrichir l’observabilité et les runbooks

## Structure de la documentation

### Architecture

- `architecture/overview.md`
- `architecture/repositories.md`
- `architecture/aws-terraform-terragrunt.md`
- `architecture/eks-cluster.md`
- `architecture/gitops-argocd.md`
- `architecture/networking-ingress-tls.md`
- `architecture/container-supply-chain.md`
- `architecture/secrets-management.md`
- `architecture/security-model.md`
- `architecture/roadmap.md`

### Components

- `components/vpc.md`
- `components/ecr.md`
- `components/github-oidc.md`
- `components/eks.md`
- `components/argocd.md`
- `components/demo-app.md`
- `components/traefik.md`
- `components/cert-manager.md`
- `components/external-secrets.md`
- `components/kyverno.md`

### Runbooks

- `runbooks/terraform-terragrunt.md`
- `runbooks/argocd.md`
- `runbooks/ingress-tls.md`
- `runbooks/external-secrets.md`
- `runbooks/aws-irsa.md`
- `runbooks/kyverno.md`

### ADR

Les **Architecture Decision Records** documentent les choix structurants, leurs raisons et leurs compromis.

### Schémas

Des visuels d’architecture peuvent être ajoutés progressivement lorsqu’ils apportent une vraie valeur. Lorsque c’est pertinent, des formats simples et maintenables comme **Mermaid** sont à privilégier.

## Principes de conception

Le lab suit quelques principes simples :

- garder la plateforme compréhensible
- avancer par étapes sans sur-ingénierie
- utiliser Git comme source de vérité
- séparer infrastructure, application, déploiement et documentation
- privilégier la reproductibilité, la traçabilité et la clarté opérationnelle
- équilibrer amélioration de la sécurité et maîtrise des coûts

## Public visé

Ce dépôt s’adresse à :

- des recruteurs et hiring managers
- des clients évaluant des compétences DevOps / DevSecOps / plateforme
- des ingénieurs relisant l’architecture
- le propriétaire du projet comme référence long terme

## Statut

Le lab évolue de manière itérative.

La documentation a vocation à refléter :

- l’état réellement validé
- l’état actuellement actif
- les prochaines évolutions envisagées

Le but est de documenter à la fois la réalité du delivery et l’intention d’architecture, sans les mélanger.

## Dépôts liés

- Infrastructure : `eks-devsecops-lab-infra`
- Application : `eks-devsecops-lab-app`
- GitOps : `eks-devsecops-lab-gitops`
