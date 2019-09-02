---
title: Middleware
---

## Introduction
De façon générale, les middlewares utilisent le patron "Chaine de commandes". C’est une procédure qui s’exécute à chaque requête et qui peut nous permettre de valider certaines opérations.
Exemples d’utilisation dans le web:
- Gestion d’erreur
- Journalisation des requêtes
- Conversion/Traitement de données
- Validation des requêtes
- Authentification
- ...

## "Identity" Middleware
Simplement pour montrer la syntaxe, le code suivant crée un middleware qui ne fait rien.
```cs
app.Use(async (httpContext, next) =>
{
  await next.Invoke();
});
```

En utilisant le `app.Use`, on reçoit deux paramètres:
- Le contexte HTTP
- La prochaine fonction à exécuter.

On peut utiliser le contexte HTTP pour analyser la requête ou la réponse. La méthode next n’a pas besoin d’être appelé absolument. Si on l’appelle, elle cherchera la prochaine utilisation du `app.Use` ou `app.Run` et l’exécutera. Si notre middleware retourne une réponse, il ne devrait pas exécuter les prochaines étapes, on ne devrait pas appeler la méthode next. Sinon, c’est qu’on laisse le soin au prochain middleware de traiter la requête.

## Middleware conditionnel
Il est possible de changer l’exécution des middlewares selon les requêtes.

### Map
À partir d’une route
```cs
app.Map("/test", (appHandler) => {
  appHandler.Run(context => context.Response.WriteAsync("test"));
});
```  
[Plus d'infos](https://docs.microsoft.com/en-ca/dotnet/api/microsoft.aspnetcore.builder.mapextensions.map){:target="_blank"}

### MapWhen
Similaire à Map, mais au lieu d’avoir une route, on va utiliser le HttpContext pour valider les conditions (plus flexible).  
[Plus d'infos](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions){:target="_blank"}


### IMiddleware
De façon plus formelle, il est possible de se créer une classe qui implémentera l’interface `IMiddleware`. L’utilisation est un peu plus complexe, mais à l’avantage de garder un “Startup” plus simple. Il ne faut pas oublier de l’enregistrer avant de l’utiliser.

```cs
public void ConfigureServices(IServiceCollection services)
{
  ...
  services.AddTransient<IdentityMiddleware>();
}

public void Configure(IApplicationBuilder app)
{
  ...
  app.UseMiddleware<IdentityMiddleware>();
  ...
}
```
