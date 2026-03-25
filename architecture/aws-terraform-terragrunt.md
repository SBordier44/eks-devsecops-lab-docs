# Infrastructure AWS avec Terraform et Terragrunt

## Objectif

Ce document décrit la couche infrastructure AWS du lab, conçue et gérée avec **Terraform** et **Terragrunt**.

Même si le runtime actif du lab a ensuite été déplacé sur K3s, cette phase AWS reste une partie majeure du projet, car elle constitue une preuve concrète de conception et d’automatisation d’une plateforme cloud.

Les objectifs de cette couche étaient :

- la reproductibilité
- une structure d’environnements claire
- la modularité
- la maîtrise des coûts
- des patterns d’accès sécurisés
- une évolution progressive sans sur-ingénierie

## Pourquoi Terraform et Terragrunt

**Terraform** est utilisé pour définir et provisionner les ressources AWS en code.  
**Terragrunt** est utilisé pour structurer le live, centraliser la configuration partagée, réduire la duplication et mieux gérer les dépendances entre stacks.

Ce couple rend l’infrastructure plus maintenable à mesure que le lab grandit.

## Structure du dépôt

Le dépôt infrastructure est découpé en deux grandes parties :

- `modules/` pour les modules Terraform réutilisables
- `live/dev/` pour l’environnement concret de développement

Les stacks live comprennent :

- `vpc`
- `eks`
- `ecr`
- `github-oidc`

## Configuration d’environnement

Le fichier `live/dev/root.hcl` centralise les valeurs partagées importantes :

- environnement : `dev`
- région : `eu-west-3`
- projet : `eks-devsecops-lab`

Il configure également le remote state Terraform avec :

- un bucket S3 pour l’état Terraform
- une table DynamoDB pour le verrouillage
- le chiffrement activé

Cette approche apporte durabilité de l’état et protection contre les exécutions concurrentes.

## Conception du VPC

La stack `vpc` provisionne :

- un VPC dédié
- 2 Availability Zones
- des subnets publics
- des subnets privés
- un Internet Gateway
- un NAT Gateway unique
- un endpoint S3 de type Gateway

### Stratégie de subnets

Le layout réseau est volontairement simple et prêt pour Kubernetes.

Il fournit une base claire pour :

- les worker nodes
- l’exposition ingress publique
- les workloads privés
- la croissance future de la plateforme

### Intégration Kubernetes

Les subnets sont taggés pour faciliter l’intégration avec les load balancers Kubernetes et les attentes réseau d’EKS.

### Choix de coût

Le lab utilise **un seul NAT Gateway** au lieu d’un NAT par Availability Zone.

C’est un compromis assumé :

- coût AWS réduit
- setup de lab plus simple
- résilience inférieure à une architecture multi-AZ plus poussée

### Stratégie d’endpoint

La connectivité S3 est optimisée avec un Gateway Endpoint, ce qui limite une partie du trafic traversant le NAT Gateway.

## Conception du cluster EKS

La stack `eks` provisionne :

- un cluster dédié au lab
- des nœuds managés
- le support IRSA
- les add-ons managés principaux

### Philosophie du cluster

Le design EKS vise à être :

- suffisamment réaliste pour de la pratique platform engineering
- suffisamment simple pour rester compréhensible
- suffisamment abordable pour un lab personnel

### Capacités déjà supportées

L’infrastructure prend déjà en charge :

- IRSA
- les managed add-ons
- la compatibilité avec GitOps
- l’intégration de composants plateforme

### Logs et coût

Les choix liés à CloudWatch et aux logs du control plane ont été volontairement conservateurs afin de garder un coût soutenable tout en laissant la porte ouverte à une observabilité plus poussée plus tard.

## Conception d’ECR

La stack `ecr` provisionne le registre utilisé par la chaîne de delivery de la demo-app pendant la phase AWS.

Le fait que la phase active utilise ensuite GHCR ne retire rien à cette brique : elle a bien été conçue, déployée et validée dans un workflow complet.

## GitHub OIDC

La stack `github-oidc` permet aux workflows GitHub Actions d’obtenir un accès AWS sans stocker de clés d’accès statiques.

Ce point est important car il démontre :

- une intégration cloud-native correcte
- une approche plus propre qu’un simple secret AWS long terme
- une compréhension des patterns d’authentification CI/CD vers AWS

## Ce que cette phase prouve

La phase AWS ne doit pas être lue comme un simple brouillon abandonné.  
Elle prouve concrètement :

- la capacité à concevoir une plateforme AWS cohérente
- la capacité à l’automatiser avec Terraform et Terragrunt
- la capacité à y brancher Kubernetes, GitOps et CI/CD
- la capacité à gérer les compromis entre coût, clarté et réalisme

## Place de cette phase aujourd’hui

Cette couche AWS n’est plus le runtime actif principal du lab, mais elle reste :

- validée
- documentée
- structurante dans le portfolio technique du projet

La phase K3s ne remplace pas cette valeur. Elle permet simplement de prolonger le lab sans conserver un coût cloud permanent.

## Résumé

La couche AWS avec Terraform et Terragrunt constitue la **phase cloud validée** du lab.  
Elle démontre un socle sérieux de compétences infrastructure, Kubernetes et intégration CI/CD, sur lequel la phase K3s vient ensuite s’appuyer comme continuation pragmatique.
