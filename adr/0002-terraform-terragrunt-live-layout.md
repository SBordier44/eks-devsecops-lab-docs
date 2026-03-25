# ADR 0002 - Utiliser Terraform avec Terragrunt et une structure live dédiée

## Statut

Accepté

## Contexte

L’infrastructure AWS du lab devait rester :

- modulaire
- lisible
- maintenable
- raisonnable en duplication

## Décision

Utiliser :

- **Terraform** pour les ressources
- **Terragrunt** pour la structure live, la centralisation de configuration et la gestion plus propre des dépendances

## Conséquences

### Positives

- meilleure organisation des stacks
- configuration partagée centralisée
- dépendances explicites
- meilleure évolutivité

### Négatives

- un outil supplémentaire à maîtriser
- une structure plus élaborée qu’un simple Terraform monolithique

## Remarque de phase 2

Cette décision reste pleinement valable comme socle de la phase AWS validée, même si le runtime actif est ensuite sorti d’AWS.
