---
title: Razor
---

## Syntaxe Razor de base
Nous utiliserons la syntaxe [Razor](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/razor){:target="_blank"} avec des pages `*.cshtml`. De base, une page Razor qui ne contient que du HTML sera affichée comme si c'était une page HTML. C'est l'idée de base, on veut améliorer le HTML en utilisant du code serveur!

### Expression C#
C'est le symbole `@` qui sert à ajouter des instructions serveur. Si le symbole @ doit être inséré dans le HTML, on n'a qu'à le dupliquer (`@@`). Lorsqu'une expression est évaluée, sa valeur est affichée dans la page web après avoir été encodée à l'aide du `HtmlEncode`.
Les expressions simples peuvent généralement être utilisées telles quelles.
```
@Modele.Nom
```

D'autres expressions plus complexes peuvent nécessiter l'utilisation de parenthèses.
```
@(Scope.Get<IHostingEnvironment>().EnvironmentName)
```

### Bloc d'instructions
Si les instructions ne font pas exactement ce que nous désirons, nous pouvons toujours utiliser les blocs pour des instructions encore plus complexes. Le résultat de ceux-ci ne sera toutefois pas converti en HTML.

```
@{
  var deux = 2;
}
```

### HTML à l'intérieur d'un bloc
Avec la syntaxe Razor, il devient extrêmement facile d'insérer du HTML, même si nous sommes dans un bloc d'instructions. Il suffit d'y insérer l'élément HTML et la transition du langage C# vers HTML se fait automatiquement. Si tout ce que l'on veut est d'insérer du texte, sans balise, à ce moment, on peut utiliser le pseudo-élément text.
```
@foreach (var index in Enumerable.Range(0, 10))
{
  <text>Element no. @index</text>
}
```

## Directives
### @using
Similaire au using en C#. Permets d'utiliser des classes et namespaces sans les référencer explicitement.	
### @model
Avec la syntaxe Razor, nous avons accès à un modèle. Dans le cas des pages Razor, ce modèle est habituellement du type de base `PageModel`.
### @inject
Permets d'injecter un service dans la page et de l'utiliser.

## Pages Razor
Les [pages Razor](https://docs.microsoft.com/en-us/aspnet/core/razor-pages/){:target="_blank"} sont apparues après les pages MVC. Elles sont plus simples à utiliser, parfaites pour des pages simples.

### Répertoire "Pages"
En ajoutant au projet le routage MVC (voir intro), les pages Razor dans le répertoire `Pages` sont automatiquement trouvées. La page par défaut est la page `Index.cshtml` et les répertoires sont automatiquement convertis en partie de chemin d'accès. Aucun besoin d'ajouter l'extension du fichier pour accéder à la page, il suffit d'entrer le nom. Par exemple, si on a une page test.cshtml dans le dossier `Pages`, on pourra y accéder avec cette URL: `http://site/test`

### Page simple
Exemple de contenu d'un fichier `*.cshtml`.

```html
<!-- La directive @@page indique que ceci est une page Razor. 
    Elle doit être la première directive de la page -->
@model AboutModel
<!-- La directive @@model indique le type de la propriété @@Model, 
    habituellement une classe dérivée de PageModel -->
@{
  ViewData["Title"] = "About"; // Indique le titre
  /* Ces commentaires sont en C# ??? */
}
<h2>@ViewData["Title"]</h2>
<h3>@Model.Message</h3>

<p>Contenu de la page ici</p>
```

### Code-Behind
Chaque page Razor vient habituellement avec un fichier `cs`. Ce fichier déclare une classe qui hérite de PageModel et permet d'y référencer dans le `cshtml`. C'est l'approche recommandée pour ne pas surcharger le fichier `cshtml`.

### _ViewImports.cshtml
Le contenu de ce fichier est automatiquement chargé pour la page. Ce fichier sert à nous éviter de répéter les mêmes lignes pour toutes les pages. La syntaxe est Razor, mais ne permet que d'y insérer certaines directives. Il peut y avoir un fichier `_ViewImports` par répertoire, ils seront appliqués en suivant la hiérarchie.

### _ViewStart.cshtml
À la différence de `_ViewImports`, ce fichier sert à insérer du code au début de chaque page. Un bon exemple d'utilisation est d'assigner le Layout à chaque page.

```html
@{
  Layout = "_Layout";
}
```

### Layout
Le [layout](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/layout){:target="_blank"} est l'équivalent du MasterPage en WebForms, il définit la structure de la page.

### <environment>
Permet de contrôler ce qui apparait dans la page selon l'environnement. Pratique pour avoir des fichier “Debug” en mode développement

```
<environment include="Development">
    <link rel="stylesheet" href="~/css/site.css" />
</environment>
<environment exclude="Development">
    <link rel="stylesheet" href="~/css/site.min.css" />
</environment>
```

### Vues partielles
Permet de réutiliser du code HTML facilement. Il est possible d'utiliser les vues partielles en HTML ou en C#. Ce sont en fait des pages `cshtml`. La convention est de préfixer ces pages par un souligné. Il est aussi possible de définir un modèle pour chacune de ces pages.
Comparativement aux vraies page Razor, les vues partielles:
- Ne possèdent pas de directive @page
- Peuvent posséder un type de modèle différent de PageModel

#### En C#
```cs
@await Html.PartialAsync("_NomDeLaVuePartielleSansLeCSHTML")
@await Html.PartialAsync("_NomDeLaVuePartielleSansLeCSHTML", modele)
```

#### HTML
```html
<partial name="_NomDeLaVuePartielleSansLeCSHTML" model="modele" />
```

### Atelier
Une page Razor se compile en fait en une classe C#, qui va générer du HTML. Le tout peut être observé dans le répertoire "obj". 

## Exécution
Les pages Razor héritent de `PageModel`. Lorsqu'une page est demandée, c'est d'abord son constructeur qui sera appelé, ce qui nous permet d'utiliser l'injection de dépendances. Ensuite, selon la méthode utilisée pour accéder à la page, la fonction correspondante sera appelée selon le format `On[VERB]` / `On[VERB]Async`.
Les méthodes ne sont pas obligatoires, mais seront appelées avant d'exécuter la page si elles sont disponibles. Les méthodes peuvent être de type void ou retourner un `IActionResult`.

### OnGet / OnGetAsync
```cs
public void OnGet() { }
public IActionResult OnGet() { }
public async Task OnGetASync() { }
public async Task<IActionResult> OnGetAsync() { }
```

### OnPost / OnPostAsync
```cs
public void OnPost() { }
public IActionResult OnPost() { }
public async Task OnPostASync() { }
public async Task<IActionResult> OnPostAsync() { }
```

### Routes paramétrées
Lorsque nous utilisons des entités, il arrivera de vouloir paramétrer les routes pour aller chercher un item en particulier. Cette utilisation à besoin de deux étapes.

#### `cshtml`
Il faut modifier la directive `@page` pour lui permettre d'avoir un paramètre. Par exemple, le code suivant permet d'avoir optionnellement un id.
```html
@page "{id:int?}"
```

#### `C#`
Le code du `PageModel` devra également être ajusté pour supporter le paramètre.
```cs
public async Task<IActionResult> OnGetAsync(int? id) { }
```

## Opérations CRUD (EntityFramework)
Les opérations CRUD vont être souvent utilisé en combinaison avec les actions HTTP (Post, Put, Delete, Get).

### BindProperty
Les données des formulaires qui sont transigées par les requêtes `GET` / `POST` sont disponibles à travers la propriété `ModelState` de la page. Mais lorsqu'on travaille avec une seule entité, il peut être plus facile d'utiliser l'annotation `BindProperty`, cette annotation créera automatiquement un objet pour nous et lui assignera les propriétés en tenant compte de la configuration du modèle.

```cs
[BindProperty]
public Utilisateur Utilisateur { get; set; }
```
### Sauvegarde
Les données obtenues via le `BindProperty` nous donnent un objet .NET, une instance de classe. Cette instance n'est toutefois pas associée à la base de données, du moins, le Framework n'en est pas conscient. Pour l'aider, avant de sauvegarder notre entité, nous devrons l'attacher à son `DbSet`. Ainsi, Entity Framework comprendra que l'instance est en fait une entité et qu'il doit l'espionner pour appliquer les modifications lors du `SaveChanges`. Puisque notre objet arrive d'un `POST`, il est possible qu'aucune modification ne soit à faire sur l'objet. On peut généralement assumer qu'il a été modifié par l'utilisateur. On peut donc indiquer à Entity Framework que c'est le cas afin qu'il sache qu'il doit la sauvegarder.
```cs
EntityEntry entite = Contexte.Attach(Utilisateur);
entite.State = EntityState.Modified;
```

### Validation
La page contient également une propriété IsValid. Avant de faire des modifications à la base de données, on devrait toujours valider cette propriété.
```cs
  if (ModelState.IsValid) { ... }
```

## Helpers
ASP.NET fournit quelques outils pour simplifier la gestion entre le modèle et le HTML. Ils vont permettre de générer du HTML et d'appliquer un certain lien entre nos éléments HTML et notre modèle.

### HTML Helpers
Le `HtmlHelper` peut être utilisé à l'aide de l'instruction `@Html`. L'objet `HtmlHelper` implémente l'interface [`IHtmlHelper`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper){:target="_blank"}. Mais il existe aussi quelques classes d'extensions.
Par exemple, pour créer un label sur la propriété `Message`:
```html
@Html.LabelFor(_ => _.Message)
```

### Tag Helpers
Les [Tag Helpers](https://docs.microsoft.com/en-ca/aspnet/core/mvc/views/tag-helpers/){:target="_blank"} fournissent une fonctionnalité similaire aux Html Helpers, mais avec une syntaxe plus près du HTML. Ils peuvent s'appliquer sur un élément ou un attribut. C'est une façon simple de modifier le HTML qui sera généré. Par exemple, créer un label sur la propriété Message:
```html
<label asp-for="Message"></label>
```

Même si on semble utiliser des chaînes de caractères pour spécifier l'objet référé, ce sont en fait des expressions C#. La validation se fait à la compilation et si la propriété n'existe pas, il y aura une erreur de compilation.

Avant d'utiliser les Tag Helpers, il faut s'assurer les rendre disponibles à la vue. Cette opération se fait à l'aide de la directive @addTagHelper
```html
@addTagHelper [ * | Nom du Tag ], [Assembly]
```

Il existe plusieurs Tag Helpers, voici quelques exemples de fonctionnalité :
- Redirections vers la bonne route (`a`, `form`)
- Génération de formulaires
- Validations de formulaires

[Liste de tag helpers existants](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/built-in){:target="_blank"}

