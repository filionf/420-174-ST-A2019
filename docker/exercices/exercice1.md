---
title: Docker
description: Exercice 1
---

## Exécuter une image docker
Le but de cet exercice est d'apprivoiser la ligne de commande avec Docker et de commencer à comprendre comment les images et conteneurs fonctionnent.

1. Démarrez une console et exécutez la commande suivante
  ```bash
  docker run alpine
  ```

2. Affichez les conteneurs actifs
   ```bash
   docker ps
   ```

3. Affichez les conteneurs
   ```bash
   docker ps -a
   ```

4. Exécutez l'image alpine et exécutez la commande `sh`
5. Affichez la liste des processus
6. Quittez le conteneur (`CTRL+PQ`)
7. Affichez la liste des conteneurs
8. Exécutez un autre `sh` sur le conteneur
9. Affichez la liste de processus
10. Ajoutez le fichier `/opt/bonjour.txt`
11. Insérez-y le texte `Bonjour [votre nom]` avec `nano` ou `vim`. Le package manager de la distribution `alpine` est `apk`.
12. Exécutez une autre image `alpine`. Vérifiez si le fichier `/opt/bonjour.txt` y est.
13. Arrêtez et supprimez tous les conteneurs


## Et si on utilisait une image dotnet?
1. Trouver l'image qui supporte .NET Core?
2. En exécutant l'image, sur quel système d'exploitation est-elle basée?
3. Qu'affiche `dotnet --info`