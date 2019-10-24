---
title: Authentification
---

## Authentification In-App
La première façon de gérer l'authentification est d'utiliser l'[authentification "in-app"](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity){:target="_blank"}. C'est-à-dire que l'application gérera elle-même les usagers et mots de passe qui ont accès à l'application.

### Configuration des services
Pour utiliser l'authentification, il faut la configurer dans le `ConfigureServices`. Évidemment, on aura besoin d'une base de données pour conserver ces informations, d'où le code suivant
```cs
services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));
services.AddDefaultIdentity<IdentityUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

Attention, le contexte peux être le même que votre base de données existante si vous en avez une, mais il doit toutefois hériter de `IdentityDbContext`.

### Personnalisation de l'interface
Si on désire utiliser l'interface de connexion qui vient avec ASP.NET, on peut également modifier le AddDefaultIdentity ainsi.
```cs
services.AddDefaultIdentity<IdentityUser>()
    .AddDefaultUI(UIFramework.Bootstrap4)
    .AddEntityFrameworkStores<ApplicationDbContext>();
```
Cette ligne additionnelle ajoute, entre autres, deux chemin d'accès pour se connecter ou créer un compte, soit `/Identity/Account/Login` et `/Identity/Account/Register`. Bien qu'on ne les voit pas dans le projet, plusieurs fichiers sont servient par ASP.NET pour gérer l'authentification. S'ils existaient, ils seraient sous le répertoire `Areas/Identity/`. Nous avons toutefois besoin d'ajouter un fichier essentiel au fonctionnement de ces routes, le fichier `_LoginPartial.cshtml` (Shared). Voici le contenu qui vient avec certains modèles de projet 2.2, mais vous devriez être capable de le faire générer automatiquement. Cette version est conçue pour être utilisée dans la barre de navigation avec le projet par défaut.

```cs
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager

<ul class="navbar-nav">
  @if (SignInManager.IsSignedIn(User))
  {
    <li class="nav-item">
      <a class="nav-link text-dark" asp-area="Identity" asp-page="/Account/Manage/Index" title="Manage">Hello @User.Identity.Name!</a>
    </li>
    <li class="nav-item">
      <form class="form-inline" asp-area="Identity" asp-page="/Account/Logout" asp-route-returnUrl="@Url.Page("/", new { area = "" })" method="post">
        <button type="submit" class="nav-link btn btn-link text-dark">Logout</button>
      </form>
    </li>
  }
  else
  {
    <li class="nav-item">
      <a class="nav-link text-dark" asp-area="Identity" asp-page="/Account/Register">Register</a>
    </li>
    <li class="nav-item">
      <a class="nav-link text-dark" asp-area="Identity" asp-page="/Account/Login">Login</a>
    </li>
  }
</ul>
```

Ensuite, pour personnaliser l'une ou l'autre des pages reliées à IdentityFramework, vous pouvez ajouter un "Scaffolded Item" de type identity et suivez les instructions!

### Personnalisation des usagers
Le `ApplicationDbContext` qui vient avec le modèle hérite de `DbContext`. Ça nous donne donc un modèle Entity Framework comme ce qu'on a vu jusqu'à présent et on peut personnaliser notre usager. Pour ce faire, il faut suivre quelques étapes.

1. Hériter `IdentityUser`

   Le type qui représentera l'usager doit utiliser hériter de `IdentityUser`, c'est sur cet usager que l'on peut ajouter les propriétés nécessaires à notre application.

2. Modifier `AddDefaultIdentity`

   Dans `ConfigureServices`, la ligne `AddDefaultIdentity` est générique, ce paramètre est le type de notre usager.

3. Modifier `ApplicationDbContext`

   Le `ApplicationDbContext` prend également quelques paramètres génériques, le premier étant le type de l'usager.

4. Modifier les pages

   Certaines pages ont comme modèle le `IdentityUser`, il faut les modifier pour qu'elles utilisent le nouveau type que nous avons créé.

5. Appliquer les migrations nécessaires

   Comme ce que nous avons vu précédemment, il ne faut pas oublier de faire nos migrations de modèle.

## Supporter différents types d'utilisateurs (rôles)
Supposons que vous vouliez avoir différents accès selon les utilisateurs. Identity Core supporte également cette fonctionnalité, mais il y a quelques étapes à faire pour l'activer.
En utilisant l'authentification In-App, les rôles sont conservés dans la table `AspNetRoles` et les rôles qu'un usager possède sont conservés dans la table `AspNetUserRoles`. La classe de base représentant les rôles est `IdentityRole` et fonctionne de façon similaire à `IdentityUser`.
La gestion des rôles est faite à l'aide de l'objet `RoleManager` et il s'agit d'une classe générique. Il peut donc être injecté ainsi si les rôles n'ont pas été personnalisés: `RoleManager<IdentityRole>`.

### Vérifier l'existence d'un rôle
```cs
await roleManager.RoleExistsAsync("role")
```
### Créer un rôle
```cs
await roleManager.CreateAsync(new IdentityRole("role"))
```
### Ajouter un rôle à un usager
```cs
await userManager.AddToRoleAsync(user, "role");
```
### Enlever un rôle à un usager
```cs
await userManager.RemoveFromRoleAsync(user, "role");
```
### Valider l'appartenance d'un usager à un rôle
```cs
await userManager.IsInRoleAsync(user, "role");
```

## Contrôler l'autorisation
### Connecté vs. Anonyme
Avec les pages Razor, on peut contrôler si l'usager qui accède à la page doit être authentifié par page pour par répertoire. Toutefois, s'il y a un conflit entre 2 autorisations pour une page, accès anonyme versus accès authentifié, c'est l'accès anonyme qui gagne.
```cs
services
  .AddMvc()
  .AddRazorPagesOptions(options =>
  {
    options.Conventions.AuthorizePage("/Page1");
    options.Conventions.AuthorizeFolder("/RépertoireSécurisé");
    options.Conventions.AllowAnonymousToPage("/RépertoireSécurisé/Page2");
    options.Conventions.AllowAnonymousToFolder("/RépertoireSécurisé/Achat");
  });
```

[Plus d'infos](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/razor-pages-authorization){:target="_blank"}

### En utilisant les rôles
Lorsqu'un usager est connecté, c'est son token d'authentification qui sert à valider les droits. Pour contrôler l'autorisation à l'aide des rôles, il faut d'abord ajouter ces rôles au token. Cette configuration se fait dans la fonction `ConfigureServices` du `Startup`. Il faut faire attention ici car les modifications à la génération de tokens n'affectent pas ceux qui ont été générés précédemment.
```cs
services.AddScoped<
  IUserClaimsPrincipalFactory<Usager>,
  UserClaimsPrincipalFactory<Usager, IdentityRole>>();
```
Ensuite, l'attribut `Authorize` peut être ajouté sur les méthodes ou les classes représentant les pages.
```cs
[Authorize(Roles = "admin")]
public class EditModel : PageModel { ... }
```

## Ajouter une autorisation externe
Pour simplifier la gestion de mots de passe, il peut être préférable de déléguer l'authentification à des fournisseurs externes tels que Microsoft, Google, Twitter, Facebook et autres.
L'ajout d'une application se fait généralement en suivant les étapes suivantes, mais qui peuvent varier selon le fournisseur utilisé.
L'exemple utilisé se base sur l'authentification Microsoft.
### Ajouter le package NuGet
```console
dotnet add package Microsoft.AspNetCore.Authentication.MicrosoftAccount
```
### Activer l'authentification externe dans le Startup
```cs
services
    .AddAuthentication()
    .AddMicrosoftAccount(opts =>
{
    opts.ClientId = Configuration["Authentication:Microsoft:ApplicationId"];
    opts.ClientSecret = Configuration["Authentication:Microsoft:Password"];
});
```
Comme vous pouvez le remarquer, nous aurons à modifier le appsettings.json pour avoir une configuration fonctionnelle.

### Enregistrer votre application sur le Portail Microsoft
Suivez les instructions : [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/){:target="_blank"}
Générez un mot de passe d'application, puis ajustez votre `appsettings.json`

### Ajouter une plateforme (web)
La plateforme demande une URL de retour. Cette URL sert à renvoyer le code d'accès. Dans le cas de l'authentification Microsoft, la valeur par défaut est `/signin-microsoft`. Puisque nous sommes en mode développement, il suffit d'entrer notre URL de développement (qui doit être HTTPS) mais si ne faut pas oublier d'ajouter l'URL de production au déploiement.

