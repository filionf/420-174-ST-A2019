---
title: Exercice formatif 3
description: Migration d’un modèle de données
---

## Objectif
Apprendre le fonctionnement de base des migrations de modèles de données avec Entity Framework

## Travail à effectuer

1. ### Modèle initial
   1. Démarrez un projet ASP.NET Core Application (Empty)
   2. Ajoutez les classes nécessaires pour gérer une base de données simple de produits
      1. Un contexte de base de données
      2. Un DbSet pour les produits
      3. Un produit doit contenir un id, un nom et un prix
   3. Générez votre modèle au démarrage et appliquez votre changement en utilisant l’outil “console”
   4. Assurez-vous que le modèle est bien créé
2. ### Migration
   1. Ajoutez une longueur maximale sur le nom du produit de 50 caractères (attribut MaxLength)
   2. Ajoutez une colonne catégorie au produit 
   3. Générez la migration et exécutez l’application pour appliquer les changements
3. ### Retour en arrière. On aurait dû mettre une limite de 100 caractères pour le nom de produit.
   1. Annulez vos changements
   2. Enlevez votre dernière migration
   3. Modifiez le nombre de caractères maximal
   4. Générez la migration et exécutez l’application pour appliquer les changements
4. ### Scripts
   1. Générez un script SQL
   2. Validez qu’il est bien généré


