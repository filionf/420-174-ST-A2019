<style>
  ol ol > li {
    list-style-type: lower-alpha;
    margin-left: 1.5em;
  }
</style>

## Exercice formatif 2

### Objectifs
Pratiquer les concepts relatifs à l’utilisation dans une page web.

Pour faire cet exercice, vous devez connaître les notions suivantes:
- POCO
- DbContext, DbSet
- LINQ

### Travail à effectuer
1. Démarrez un projet ASP.NET Core Application (Razor)
2. Ajoutez les classes nécessaires pour gérer une base de données simple de films.
    <ol>
      <li>Un contexte de base de données</li>
      <li>Un DbSet pour les films.</li>
      <li>Un film doit contenir un id généré automatiquement, un nom, une année d’affiche et un synopsis.</li>
    </ol>
3. Créer les pages pour nécessaires pour gérer les films, donc une page pour la liste de films, une autre pour créer, voir les détails, modifier et supprimer un film. Je vous recommende fortement d'utiliser le scaffolding pour ce faire.
4. Affichez les films par année d'affiche, les plus récents en premier.

