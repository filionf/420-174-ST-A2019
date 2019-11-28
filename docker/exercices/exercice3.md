---
title: Docker
description: Exercice 3
---

# Publier notre librairie musicale.
Pour les étapes suivantes, nous allons utiliser la méthode facile, c'est-à-dire laisser Visual Studio faire le travail.

## Ajouter le support Docker

Nous avons vu comment créer une image et exécuter un conteneur. Avec Visual Studio, c'est un peu plus facile! Il suffit d'ajouter le support pour les conteneurs Docker et Visual Studio fera une bonne partie du travail. L'ajout peut se faire en utilisant le clic de droite sur le projet, puis `Ajouter`, puis trouver le support pour Docker.

## Créer un registre pour recevoir notre image
Visual Studio va nous aider à créer un registre, mais on peut aussi créer le registre en utilisant le portail Azure.
[https://docs.microsoft.com/fr-ca/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/fr-ca/azure/container-registry/container-registry-get-started-portal){:target="_blank"}

## Publier notre image
Avant de pouvoir déployer notre image, il faut la publier sur un registre d'image. Elle doit devenir accessible aux services de conteneurs.
On peut, entre autres, la déployer en utilisant les outils de déploiement Visual Studio. On pourrait également utiliser la commande `docker push`.


## Déployer notre image
Encore une fois, en utilisant les outils de déploiement, mais cette fois-ci en sélectionnant l'option `App Service Linux`. Cette option permet également d'intégrer les options précédentes, c'est la plus facile!
Faites attention au forfait utilisé, certains coûtent plus cher que d'autres.