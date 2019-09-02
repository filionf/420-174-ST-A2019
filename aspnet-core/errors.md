---
title: Gestion d'erreurs
---

## Introduction
Évidemment, les concepts de base de [gestion d’erreurs de .NET](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/error-handling){:target="_blank"} s’appliquent aussi à ASP.NET Core. Utilisez les try/catch là où vous le pouvez, mais il y a d’autres concepts qui peuvent également être utilisés.


## Page d’erreur pour développeurs
En utilisant les stratégie Middleware, MVC fournit une page d’exception pour développeurs.
Cette page ne doit être utilisée qu’en mode développement et jamais en mode production puisqu’elle pourrait donner trop d’information sur le système aux utilisateurs ("hackers"), mais surtout, pour ne pas donner une mauvaise impression aux utilisateurs.
Comme il s’agit d’un middleware, l’utilisation de cette page se fait dans le `Configure`, mais doit aussi être utilisée avant les autre configurations.
```cs
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
}
```

## Page d’erreur standards
Les pages d’erreurs standards fonctionnent selon le même principe que les pages d’erreurs pour développeur. C’est-à-dire qu’elles sont utilisées comme middleware, que la configuration doit se faire dans le `Configure`, mais doit aussi être utilisée avant les autre configurations. Si notre projet supporte l’authentification, on devrait s’assurer que ces pages soient disponibles lorsque l’usager est anonyme.

### UseExceptionHandler
Pour faire simple, on peut afficher une page d’erreur lorsque nécessaire avec la méthode `UseExceptionHandler`. La page référencée doit être très simple, pour avoir le plus de chance qu’elle fonctionne bien. 
```cs
app.UseExceptionHandler("/error");
```

### UseStatusCodePagesWithRedirects
Lorsqu’on utilise ce middleware, le client recevra un `302` pour rediriger vers cette page. Par exemple, si un doit générer une erreur `404` que nous avons configurer notre site avec le code suivant, le client recevra un `302` vers `/error/404`.
```cs
app.UseStatusCodePagesWithRedirects("/error/{0}");
```
Cette méthode ne supporte pas les erreurs de programmation, les `500`. Si vous voulez les supporter, vous devez utiliser cette méthode en combinaison avec la méthode `UseExceptionHandler`.

### UseStatusCodePagesWithReExecute
De façon similaire à UseStatusCodePagesWithRedirects, cette méthode va permettre de personnaliser la page d’erreur selon le code, ne changera pas l’adresse dans le navigateur.
```cs
app.UseStatusCodePagesWithReExecute("/error/{0}");
```

## Journalisation (logging)

Par défaut (`CreateDefaultBuilder`), une application web va journaliser les informations dans la console et dans la fenêtre de débogage. On peut configurer la journalisation dans le Configure du startup en injectant un `ILoggerFactory`, mais en la configurant à cet endroit, on pourrait manquer les erreurs qui arrivent avant que l’application démarre.
```cs
WebHost.CreateDefaultBuilder(args)
  .ConfigureLogging((hostingContext, logging) =>
  {
    logging.AddConfiguration(
      hostingContext.Configuration.GetSection("Logging")
    );
    logging.AddConsole();
    logging.AddDebug();
    logging.AddEventSourceLogger();
  })
  .UseStartup<Startup>()
  .Build();
```

Pour ajouter des informations à la journalisation, il suffit d’injecter un `ILogger<Category>`.
```cs
public class LogPageModel : PageModel
{
  public LogPageModel(ILogger<LogPageModel> logger)
  {
    Logger.LogInformation("Message");
  }
}
```
Les loggers peuvent être configuré dans la section `Logging` du `appsettings.json`. Par exemple, on va souvent changer les niveaux de log entre les environnements développement et production. Chaque logger peut également avoir ces propres options.
Le framework fourni plusieurs outils de journalisation et il en existe d’autres qui peuvent être installés dans votre projet. Vous devriez être capable de bien vous débrouiller avec ce qui existe, mais sachez qu’il serait possible de créer un "Logger" personnalisé.

[Plus d'infos](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging/){:target="_blank"}
