---
title: Stockage
---

## Introduction
Il existe plusieurs moyens pour conserver les données afin de bien faire fonctionner notre application.
Nous avons déjà vu les bases de données, mais il existe aussi:
- Les témoins (cookies)
- L'état de la session
- Le contexte HTTP
- Une cache

Lorsque vous apprendrez le JavaScript, vous verrez qu'il existe aussi d'autres mécanismes au niveau du navigateur.

## Les cookies
Les témoins peuvent conserver des données à travers chacune des requêtes. Ils peuvent avoir une expiration et un domaine d'application, c'est-à-dire être valide pour le domaine au complet, ou simplement à l'intérieur d'un sous-domaine. Les témoins sont envoyés par le serveur, conservés par le client, puis retournés pour chacune des requêtes. 

Les cookies peuvent être créés à l'aide de la propriété [`HttpResponse.Cookies`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.http.iresponsecookies){:target="_blank"}.  
Et ils peuvent être accédés à l'aide de la propriété [`HttpRequest.Cookies`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.http.irequestcookiecollection){:target="_blank"}.



## L'état de la session
La session est un endroit de stockage serveur qui sert à conserver des données sur la session en cours. Il faut donc faire attention à notre méthode de stockage, surtout si plusieurs serveurs sont utilisés pour notre application (il existe également un concept de "sticky sessions").

Avec ASP.NET Core, ce sont les témoins qui permettent aux serveurs de retrouver la session. Il faut donc bien comprendre comment les témoins fonctionnent pour bien utiliser les sessions.

Une fois configuré, les données de la session sont disponible en utilisant l'objet [`HttpContext.Session`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.http.isession){:target="_blank"}

### Configuration

1. Pour utiliser la session, le package `Microsoft.AspNetCore.Session` dans être inclus dans le projet.
2. Ensuite, on veut ajouter le middleware, donc ajouter `app.UseSession()` dans la fonction `Configure`.
3. Pour que le middleware soit disponible, il faut également configurer les services. Le [système de cache distribuée](https://docs.microsoft.com/en-us/aspnet/core/performance/caching/distributed){:target="_blank"} et la [façon dont les témoins se comporteront](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession){:target="_blank"} doivent être configurés.


### Exemple

Voici un exemple de configuration qui utilise une session en mémoire.
```cs
public class Startup
{
  public void ConfigureServices(IServiceCollection services)
  {
    services.AddDistributedMemoryCache();
    services.AddSession(options =>
    {
      options.Cookie.HttpOnly = true;
      options.Cookie.IsEssential = true;
    });
  }

  public void Configure(IApplicationBuilder app, IHostingEnvironment env)
  {
    app.UseSession();
  }
}
```

## Le contexte HTTP
[HttpContext.Items](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.http.httpcontext.items){:target="_blank"} permets de conserver des données tout au long de la requête. Une fois la requête traitée, les données n'existent plus. Chaque requête possède son propre dictionnaire de données.


## Une cache

Il existe plusieurs mécanismes de cache dans le web, mais celui-ci est plutôt une cache côté serveur. Il existe plusieurs systèmes de cache et vous pourriez facilement fabriquer votre propre système. Voici ceux de .NET, qui ne sont pas spécifiques à ASP.
- [`System.Runtime.Caching`](https://docs.microsoft.com/fr-ca/dotnet/api/system.runtime.caching){:target="_blank"}
- [`System.Runtime.Caching.MemoryCache`](https://docs.microsoft.com/fr-ca/dotnet/api/system.runtime.caching.memorycache){:target="_blank"}

