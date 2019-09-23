---
title: Modèles
---

## Introduction
Toute bonne application/site web est supporté par une base de données. Le .NET Framework/Core fourni un O/RM qui nous aide à bien supporter ces bases de données: Entity Framework (EF). Nous allons travailler avec EF Core, mais sachez qu’il existe d’autres O/RM sur le marché. 
Il existe plusieurs façons de créer un modèle de données pour EF Core, mais nous allons voir l’approche Code-First.
[Pour plus d’informations sur EF Core](https://docs.microsoft.com/en-us/ef/core/){:target="_blank"}
## EF Core – Code-First
Le scénario idéal pour l’approche Code-First est lorsque nous démarrons un nouveau projet sans base de données existante. Nous allons donc modéliser la base de données à travers des classes C#, et c’est EF Core qui s’occupera de créer la base de données pour nous.

### Microsoft.EntityFrameworkCore.DbContext
Lorsqu’on démarre un modèle EF Core, on doit d’avoir définir un contexte de base de données. Ce contexte est en fait une classe qui hérite de DbContext. C’est à l’intérieur de celui-ci que les différents jeux de données seront définis.

```cs
public class ContexteProduits
  : Microsoft.EntityFrameworkCore.DbContext
{
  public ContexteProduits() : base() { }
  public ContexteProduits(DbContextOptions options)
    : base(options) { }

  public Microsoft.EntityFrameworkCore.DbSet<Produit> Produits { get; set; }

  protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
  {
    if (!optionsBuilder.IsConfigured)
    {
      optionsBuilder.UseInMemoryDatabase("ParDéfaut");
    }
  }
}
```

### Microsoft.EntityFrameworkCore.DbSet
Lorsque déclarées à l’intérieur d’un DbContext, les propriétés de type DbSet représentent un jeu de données, une table. Les informations relatives à la création de cette table sont définies par le type `T` associé. 

### POCO
Plain Old CLR Object. Il s’agit d’un concept très utilisé dans Entity Framework qui veut dire qu’on travaille avec de simples classes .NET. Lorsqu’on travaille avec ces classes, on devrait donc les manipuler de la même façon, qu’elles appartiennent à une base de données ou non. Il y a beaucoup de magie derrière cette approche, mais elle a quelques avantages non négligeables:
-	Simplicité
-	Meilleure testabilité
-	Couplage faible
-	Permets de créer un mappage entre les objets et la base de données.

## Utilisation d’EF Core (de base)
En utilisant l’approche en 2.2, il est facile de créer une base de données en mémoire. Il sera vu plus tard comment se connecter à une vraie base de données.

### Connexion
```cs
using (var contexte = new ContexteProduits())
{
}
```
### Création (CRUD)
```cs
using (var contexte = new ContexteProduits())
{
  var produit = new Produit();
  contexte.Produits.Add(produit);
  contexte.SaveChanges();
}
```

### Lecture (CRUD)
```cs
var requete = from produit in contexte.Produits
              where produit.Nom.StartsWith("P")
              orderby produit.Cout descending
              select produit.Nom;
```
### Mise à jour (CRUD)
```cs
var produitAJour = requete.First();
produitAJour.Cout *= 1.20;
contexte.SaveChanges();
```
### Suppression (CRUD)
```cs
var produitAEnlever = requete.First();
contexte.Produits.Remove(produitAEnlever);
contexte.SaveChanges();
```

## Configuration des contextes
En 2.2 le contexte se configure automatiquement pour être en mémoire lorsqu’il n’est pas configuré, c’est-à-dire lorsque le constructeur par défaut est utilisé.

On peut configurer le contexte qui sera utilisé par l’injection de dépendance au Startup.
```cs
services.AddDbContext<Models.ContexteProduits>(options =>
{
    options.UseSqlServer(this.Configuration.GetConnectionString("Default"));
});
```

Cette configuration fait référence à l’item “ConnectionStrings” dans appsettings.json.
Voici un exemple de configuration pour EF Core, qui réfère à une base de données de développement:
```json
  "ConnectionStrings": {
    "Default": "Server=(localdb)\\mssqllocaldb;Database=Produits;Trusted_Connection=True;MultipleActiveResultSets=true"
  }
```

On pourra ainsi utiliser différentes bases de données lorsqu’on développe l’application et lorsqu’on la déploie. Avant de l’utiliser, il faut s’assurer qu’elle a été bien été créée: 
```cs
Context.Database.EnsureCreated();
```
Cette ligne créera la `db.mdf` et `db.ldf` dans le répertoire `C:\Users\{Username}`. On verra plus tard comment faire des migrations et mieux contrôler la création.

[Pour plus d’information sur les Connection Strings](https://www.connectionstrings.com/sql-server/){:target="_blank"}

## Seed
Que ce soit dans des scénarios de tests, ou simplement pour bien démarrer une application la première fois, il est possible de fournir des [données initiales](https://docs.microsoft.com/en-us/ef/core/modeling/data-seeding){:target="_blank"} pour bien initialiser la base de données. Cette fonctionnalité n'est disponible que depuis .NET Core 2.1.

Pour ce faire, il faut surcharger la fonction `OnModelCreating` et s'assurer que la base de données soit créé soit en utilisant `context.Database.EnsureCreated()`, soit en utilisant les migrations. 

Lorsque des ids sont générés automatiquement, il faudra possiblement les générer manuellement (selon le type de bases de données - en mémoire, SQL, ...). Il sera alors préférable d'utiliser des ids négatifs afin d'éviter les conflits.

```cs
public class MonContexte : DbContext 
{
  ...

  protected override void OnModelCreating(ModelBuilder modelBuilder)
  {
    base.OnModelCreating(modelBuilder);
    modelBuilder.Entity<MonEntite>().HasData(SeedDonnees());
  }

  private IEnumerable<MonEntite> SeedDonnees() 
  {
    ... 
  }

  ...
}

```

## Mappage

### Schéma
Certains fournisseurs de base de données, comme SQL Server, supportent les schémas. Un schéma peut être comparé à un namespace en C#. Il sera utilisé pour regrouper de façon logique vos tables et données. Par défaut, avec SQL Serveur, les objets sont créés dans le schéma dbo. Il est toutefois possible de changer le schéma par défaut dans la méthode OnModelCreating du contexte.

```cs
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
  modelBuilder.HasDefaultSchema("produits");
}
```
[Plus d'infos sur les schémas](https://docs.microsoft.com/en-us/ef/core/modeling/relational/default-schema){:target="_blank"}

### Annotations
Les annotations servent à nous aider à configurer l’accès à la base de données. Elles sont faciles à utiliser et disponibles autant lors de la création d’un modèle “Code-First” qu’avec l’utilisation d’une modèle de données existant.

#### Table
Par défaut, le nom de la table est dérivé du nom de la propriété (DbSet) du contexte. Il est toutefois possible de modifier ce nom ainsi que le schéma par défaut avec l’annotation [`Table`](https://docs.microsoft.com/en-us/ef/core/modeling/relational/tables){:target="_blank"}.
```cs
using System.ComponentModel.DataAnnotations.Schema;

[Table("produit", Schema = "modèle")]
public class Produit { ... }
```

#### Column
De façon similaire à l’annotation de table, l’annotation de [`colonne`](https://docs.microsoft.com/en-us/ef/core/modeling/relational/columns){:target="_blank"} permet de changer le nom de notre colonne. Elle permet également de changer la position à laquelle la colonne sera créée dans la table (Order) ou le type de données généré si jamais celui par défaut ne convient pas.
```cs
using System.ComponentModel.DataAnnotations.Schema;

[Column("Name", Order = 0, TypeName = "nvarchar(100)")]
public string Nom { get; set; }
```

#### NotMapped
Parfois, il y a des classes ou propriétés qui seront associées à nos entités, mais nous ne désirons pas que ces valeurs se retrouvent dans la base de données, elles ne font partie que de notre modèle d’application. C’est à cela que sert l’attribut `NotMapped`, à ignorer ces [classes](https://docs.microsoft.com/en-us/ef/core/modeling/included-types){:target="_blank"} ou [propriétés](https://docs.microsoft.com/en-us/ef/core/modeling/included-properties){:target="_blank"}. 

#### Key
Pour être créée, chaque table doit posséder une clé primaire. Elle peut se faire à l’aide de cette annotation. Pour une clé à plusieurs colonnes, il faut utiliser le Fluent API. Une clé multiple peut également avoir plusieurs colonnes. Dans ce cas, il suffit de mettre l’attribut sur chacune des colonnes. Les propriétés [`Key`](https://docs.microsoft.com/en-us/ef/core/modeling/keys){:target="_blank"} sont automatiquement `Required`.

```cs
using System.ComponentModel.DataAnnotations;

[Key()]
public string Nom { get; set; }
```


#### Valeurs autogénérées
Parfois, on ne veut pas se soucier de la valeur de la clé et ne désire seulement qu’une valeur soit [générée automatiquement](https://docs.microsoft.com/en-us/ef/core/modeling/generated-properties){:target="_blank"}.
```cs
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```


#### Required
Pour indiquer que la [valeur est requise](https://docs.microsoft.com/en-us/dotnet/api/system.componentmodel.dataannotations.requiredattribute){:target="_blank"}.
```cs
using System.ComponentModel.DataAnnotations;

[Required()]
public string Nom { get; set; }
```


### Fluent API
Le Fluent API est une façon un peu plus complexe à utiliser que les annotations, mais beaucoup plus flexible. La bonne nouvelle est que les deux techniques peuvent être combinées. Chaque annotation ci-dessus possède un équivalent en mode Fluent (non montré). Le code relativement à cet API

#### Clés multiples
On a vu qu’on pouvait utiliser l’attribut `Key` pour définir une clé. Toutefois, si une [clé multiple](https://docs.microsoft.com/en-us/ef/core/modeling/keys){:target="_blank"} doit être utilisée, il faut utiliser le Fluent API. 
```cs
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Produit>()
        .HasKey(_ => new { _.Id1, _.Id2 });
}
```

#### Indexes
Fonctionne de manière très similaire à la création d’une clé. [Indexes](https://docs.microsoft.com/en-us/ef/core/modeling/indexes){:target="_blank"}
```cs
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Produit>()
        .HasIndex(b => b.Nom);
}
```

#### Valeurs par défaut
[Valeur utilisée](https://docs.microsoft.com/en-us/ef/core/modeling/relational/default-values){:target="_blank"} si aucune valeur n’est fournie. Souvent utilisé en combinaison avec l’attribut `Required`.
```cs
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Produit>()
        .Property(_ => _.Cout).HasDefaultValue(0);
}
```


### Héritage
EF supporte l’[héritage](https://docs.microsoft.com/en-us/ef/core/modeling/inheritance){:target="_blank"}. Les deux types peuvent être inclus dans un DbSet du contexte, ou être configurés avec le Fluent API
```cs
modelBuilder.Entity<ProduitComestible>().HasBaseType<Produit>();
modelBuilder.Entity<ProduitEnPlastique>().HasBaseType<Produit>();
```


### Relations (Foreign Key)
Pour des [relations](https://docs.microsoft.com/en-us/ef/core/modeling/relationships){:target="_blank"} simples, elles seront créées automatiquement en ajoutant les propriétés aux objets concernés. EF créera automatiquement les champs nécessaires à la relation.
Pour des relations obligatoires (ex. 1 – 1), il suffit de mettre la propriété `Required`. 
Pour des relations où il y a plusieurs objets du type référencé, on n’a qu’à utiliser une propriété de type `ICollection`. 
Les relations de type n – n ne sont pas supportées. Pour y arriver, il doit y avoir une table intermédiaire.


## Migrations
Jusqu’à maintenant, lorsque notre modèle change, on s’est contenté de supprimer la base de données et de la laisser se recréer par Entity Framework. Il y a plus intelligent! 
Il est possible de créer des scripts de migration afin de conserver les données et pour nous aider dans le processus, on devra utiliser les outils à la console.

Lorsqu’on modifie les modèles de données, on 
-	Création d’un point de départ
-	Migration de modèle
-	Annulation d’une migration de modèle
-	Appliquer les migrations à l’exécution
-	Appliquer les migrations manuellement

Il faut savoir que le processus de migration ne fonctionne pas bien avec le `EnsureCreated` parce que le `EnsureCreated` va créer la dernière version connue de la base de données. C’est toutefois une méthode de gestion de modèle plus robuste.

[Plus d'infos sur les migrations](https://docs.microsoft.com/en-ca/ef/core/managing-schemas/migrations/){:target="_blank"}


### Préparation du projet pour les migrations
Si les outils dotnet ef ne sont pas installé, il est possible de les ajouter au projet.
Il faut d’abord ajouter les outils au projet en exécutant la commande suivante
```console
dotnet add package Microsoft.EntityFrameworkCore.Tools
```
Puis, dans le csproj, s’assurer que l’élément DotNetCliToolReference suivant est à l’intérieur d’une balise ItemGroup.
```xml
<ItemGroup>
  <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.tools.DotNet" 
                          Version= "2.0.0" >
</ItemGroup>
```


### Création du point de départ.
Lorsque notre modèle est prêt, on peut générer une première version du modèle 
		
dotnet ef migrations add [NomPointDeDépart]

Cette opération ajoutera un répertoire « Migrations » à notre modèle. Il contiendra un script de migration, en C#, puis un “snapshot” du modèle.
Le script de migration contient deux méthodes:
- Up: Méthode de mise à jour vers ce modèle.
- Down: Méthode pour revenir en arrière, au modèle précédent. Cette fonction annule les changements de la méthode Up.
Le snapshot est mis à jour à chaque migration, il sert de point de départ pour créer le prochain point de migration.

### Créer une migration
La création d’une migration se fait de la même façon que le point de départ, il suffit de modifier notre modèle puis, quand tout est prêt, de lancer la commande suivante
```console
dotnet ef migrations add [NomMigration]
```

### Appliquer les changements

On peut appliquer les changements de plusieurs façons.
1. À l’exécution, en appellant la fonction Migrate de la base de données\
   ```console
   contexte.Database.Migrate();
   ```
2. Manuellement, en utilisant l’outil de migration d’Entity Framework
   ```console
   dotnet ef database update
   ```
3. Manuellement, en exécutant un script de migration.
   ```console
   dotnet ef migrations script
   ```

### Revenir en arrière
Si nous avons omis une modification lors de la migration, il est aussi possible de revenir en arrière et de recréer la modification.
1. On commence par revenir en arrière sur le modèle
   ```console
   dotnet ef database update [Nom de la migration]
   dotnet ef migrations remove
   ```
1. On ajuste notre modèle de données en C#
2. On génère notre nouveau script de migration
3. On applique la migration

### __EFMigrationsHistory
Cette table sert à garder une trace des migrations appliquées. Elle est très simple et elle peut être pratique pour simuler qu’une migration a été appliquée si jamais on a gaffé...

Il est aussi possible de changer le nom de cette table. On le fera surtout si on peut avoir plusieurs modèles de données dans la même base de données afin d’éviter les conflits.

```cs
options.UseSqlServer(connectionString,
                     (sqlServerOptions) => 
                         sqlServerOptions.MigrationsHistoryTable("_MigrationProduits")
);
```

[Plus d'infos sur l'historique](https://docs.microsoft.com/en-us/ef/core/managing-schemas/migrations/history-table){:target="_blank"}


