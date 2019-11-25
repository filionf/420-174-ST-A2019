---
title: Docker
description: Images
---

Un peu comme une image de PC nous permet de rétablir une configuration sur un autre PC, les images Docker permettent d'instancier un conteneur.

Plusieurs images sont disponibles sur le [Docker Hub](https://hub.docker.com/){:target="_blank"}. Parmi celles-ci, plusieurs peuvent être utilisés tels quels, mais d'autres sont également utilisées comme image de base pour en créer de nouvelles.

## Créer une image

La création d'une image débute par un fichier "[Dockerfile](https://docs.docker.com/engine/reference/builder/){:target="_blank"}". 

Une fois le fichier prêt, on utilise la commande [docker build](https://docs.docker.com/engine/reference/commandline/build/){:target="_blank"} pour bâtir l'image et [docker push](https://docs.docker.com/engine/reference/commandline/push/){:target="_blank"} pour publier l'image sur un registre de conteneurs.


## Images à plusieurs niveaux

Un des objectifs importants à réussir lorsqu'on bâtit une image est d'obtenir une image de la taille la plus petite possible pour des raisons de performances. Ainsi, la méthode préconisée est d'utiliser la [génération à plusieurs niveaux](https://docs.docker.com/develop/develop-images/multistage-build/){:target="_blank"}. C'est-à-dire qu'une première image servira à bâtir l'application, elle aura donc besoin des outils de développements alors que l'image finale n'aura besoin que du runtime comme base, et utilisera le résultat des étapes précédentes.


## Principales commandes

| Commande | Description |
|----------|-------------|
| [docker build](https://docs.docker.com/engine/reference/commandline/build/){:target="_blank"} | Crée une image |
| [docker push](https://docs.docker.com/engine/reference/commandline/push/){:target="_blank"} | Publie une image |
| [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/){:target="_blank"} | Supprime une image |
