---
title: Exercice formatif 4
description: Page et syntaxe Razor
---


## Objectif
Apprendre à créer et modifier des pages Razor qui utilisent un modèle de données.

## Travail à effectuer
Pour apprendre la syntaxe Razor, nous allons créer un portail permettant la gestion d'utilisateur (un vrai portail serait plus compliqué)

1. ### Démarrer un projet
   Utilisez un projet ASP.NET Core Application (Razor Pages)

2. ### Intégrer une base de données
   Ajoutez les classes nécessaires pour gérer une base de données d'utilisateurs.
   Elle peut être en mémoire ou SQL, à votre guise.  
   Vous aurez donc besoin d'un contexte de base de données et d'un DbSet pour les utilisateurs. Les utilisateurs auront les champs suivants:
   - Courriel
   - Prénom
   - Nom
   - Numéro de téléphone
   - Rôle: administrateur, collaborateur, lecture seule
   
   Je vous conseille d'ajouter au moins un utilisateur par défaut pour aider à la prochaine étape (seed) 

3. ### Ajouter les pages (sans générations automatiques)
   1. Liste des utilisateurs (Index)  

      Commencez par ajouter une page qui vous donne la liste des usagers. N'utilisez pas une table comme nous sommes habitués avec les générations automatiques, ça ne fonctionne pas bien avec les appareils mobiles.
      Affichez toutes les informations disponibles.

      Ajoutez ensuite un lien vers cette page à partir de la barre de navigation.

      
   2. Suppression des utilisateurs (Delete)
      
      Pour chaque utilisateur, ajoutez un bouton qui le supprimera. Pour faire simple, créez une nouvelle page pour gérer la suppression. Le bouton redirigera vers cette page de confirmation et, lorsque confirmé, l'usager sera supprimé puis nous serons redirigés vers la liste.      

   3. Création des utilisateurs (Create)
      
      Créez d'abord la page vide, ensuite ajoutez un lien vers cette page à partir de la liste.

      Puis, ajoutez le code nécessaire à la création et à la validation de l'utilisateur.

