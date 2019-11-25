---
title: Docker
description: Exercice 2
---

## Créer une image docker
Le but de cet exercice est d'apprivoiser le Dockerfile et de bâtir et exécuter une première image.
Après chaque étape, testez votre image avec la commande `docker build`.

1.  Créer un projet C# avec .NET Core 2.2 Console.
2.  Modifier le projet afin qu'il affiche "Bonjour [Votre nom]"
3.  Créer un fichier `Dockerfile` au même niveau que le projet
4.  Trouvez l'image de base à partir de laquelle on partira.
5.  Copiez les sources vers l'image
6.  Installez les packages NuGet dans l'image (`dotnet restore`)
7.  Compilez les sources (`dotnet build`)
8.  Faites exécuter le projet à partir du résultat de la compilation
9.  Publier le project (`dotnet publish`)
10. Faites exécuter le projet à partir du résultat de la publication

## Utilisez une génération à plusieurs niveaux
1.  Créer un point de départ dans les outils SDK
2.  Copiez-y les fichiers du résultat de la publication
3.  Faites exécuter le projet à partir du résultat de la publication

## Utilisez vos fichiers de développement locaux
1.  Pour bâtir l'image, on peut utiliser le paramètre `--target` pour bâtir un niveau particulier
2.  Ensuite, il s'agit d'exécuter un conteneur avec les paramètres `-v` pour lier les sources et résultats de compilation aux répertoires des points précédents.