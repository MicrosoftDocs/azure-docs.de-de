---
title: Tutorial zur Verwendung von Featureflags in einer .NET Core-App | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie Featureflags in .NET Core-Apps implementieren.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: fc5215f71af45d3273da437fc796bf0d396ba3f9
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393511"
---
# <a name="tutorial-use-feature-flags-in-a-net-core-app"></a>Tutorial: Verwenden von Featureflags in einer .NET Core-App

Die .NET Core-Featureverwaltungsbibliotheken bieten idiomatische Unterstützung für die Implementierung von Featureflags in einer .NET- oder ASP.NET Core-Anwendung. Dank dieser Bibliotheken können Sie Ihrem Code Featureflags auf deklarative Weise hinzufügen, um nicht sämtliche Anweisungen vom Typ `if` manuell schreiben zu müssen.

Die Featureverwaltungsbibliotheken verwalten darüber hinaus Featureflag-Lebenszyklen im Hintergrund. Die Bibliotheken können Flagzustände aktualisieren und zwischenspeichern, garantieren, dass ein Flagzustand während eines Anforderungsaufrufs unveränderlich ist, und Ähnliches. Darüber hinaus bietet die ASP.NET Core-Bibliothek sofort einsetzbare Integrationen – einschließlich MVC-Controlleraktionen, Ansichten, Routen und Middleware.

Unter [Schnellstart: Hinzufügen von Featureflags zu einer ASP.NET Core-App](./quickstart-feature-flag-aspnet-core.md) werden mehrere Methoden gezeigt, mit denen Sie Featureflags in einer ASP.NET Core-Anwendung hinzufügen können. Diese Methoden werden im vorliegenden Tutorial näher erläutert. Eine umfassende Referenz finden Sie in der [Featureverwaltungsdokumentation für ASP.NET Core](https://go.microsoft.com/fwlink/?linkid=2091410).

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen von Featureflags in wichtigen Teilen Ihrer Anwendung, um die Verfügbarkeit von Features zu steuern
> * Integrieren in App Configuration, wenn Sie damit Featureflags verwalten

## <a name="set-up-feature-management"></a>Einrichten der Featureverwaltung

Der .NET Core-Feature-Manager `IFeatureManager` ruft Featureflags aus dem nativen Konfigurationssystem des Frameworks ab. Dadurch können Sie die Featureflags Ihrer Anwendung mit einer beliebigen, von .NET Core unterstützten Konfigurationsquelle konfigurieren – unter anderem mit der lokalen Datei *appsettings.json* oder mit Umgebungsvariablen. `IFeatureManager` basiert auf der .NET Core-Abhängigkeitsinjektion. Die Featureverwaltungsdienste können unter Verwendung von Standardkonventionen registriert werden:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

Der Feature-Manager ruft Featureflags standardmäßig aus dem Abschnitt `"FeatureManagement"` der .NET Core-Konfigurationsdaten ab. Im folgenden Beispiel wird er angewiesen, sie stattdessen aus einem Abschnitt namens `"MyFeatureFlags"` zu lesen:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

Wenn Sie in Ihren Featureflags Filter verwenden, müssen Sie eine zusätzliche Bibliothek einschließen und registrieren. Das folgende Beispiel zeigt, wie Sie einen integrierten Featurefilter namens `PercentageFilter` verwenden:

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Featureflags sollten sich außerhalb der Anwendung befinden und separat verwaltet werden. Dadurch können Sie Flagzustände jederzeit ändern, und die Änderungen werden in der Anwendung sofort wirksam. Mit App Configuration können Sie alle Ihre Featureflags über eine dedizierte Portalbenutzeroberfläche an einem zentralen Ort organisieren und steuern. App Configuration übermittelt darüber hinaus die Flags über seine .NET Core-Clientbibliotheken direkt an Ihre Anwendung.

Die Verbindung zwischen Ihrer ASP.NET Core-Anwendung und App Configuration lässt sich am einfachsten über den Konfigurationsanbieter `Microsoft.Extensions.Configuration.AzureAppConfiguration` herstellen. Fügen Sie zur Verwendung dieses NuGet-Pakets den folgenden Code zur Datei *Program.cs* hinzu:

```csharp
using Microsoft.Extensions.Configuration.AzureAppConfiguration;

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
           .ConfigureAppConfiguration((hostingContext, config) => {
               var settings = config.Build();
               config.AddAzureAppConfiguration(options => {
                   options.Connect(settings["ConnectionStrings:AppConfig"])
                          .UseFeatureFlags();
                });
           })
           .UseStartup<Startup>();
```

Die Werte von Featureflags ändern sich üblicherweise im Laufe der Zeit. Der Feature-Manager aktualisiert die Werte von Featureflags standardmäßig alle 30 Sekunden. Der folgende Code zeigt, wie Sie das Abrufintervall im Aufruf `options.UseFeatureFlags()` in fünf Sekunden ändern:

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.PollInterval = TimeSpan.FromSeconds(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Featureflagdeklaration

Jedes Featureflag besteht aus zwei Teilen: einem Namen und einer Filterliste, anhand der ausgewertet wird, ob ein Feature *aktiviert* ist (der Wert also `True` lautet). Ein Filter definiert einen Anwendungsfall für die Aktivierung eines Features.

Verfügt ein Featureflag über mehrere Filter, wird die Filterliste in der angegebenen Reihenfolge durchlaufen, bis einer der Filter angibt, dass das Feature aktiviert werden soll. Daraufhin ist das Featureflag *aktiviert*, und alle weiteren Filterergebnisse werden übersprungen. Falls durch keinen Filter angegeben wird, dass das Feature aktiviert werden soll, ist das Featureflag *deaktiviert*.

Der Feature-Manager unterstützt *appsettings.json* als Konfigurationsquelle für Featureflags. Das folgende Beispiel zeigt die Einrichtung von Featureflags in einer JSON-Datei:

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

Der Abschnitt `FeatureManagement` dieses JSON-Dokuments wird konventionsgemäß für Featureflageinstellungen verwendet. Das vorherige Beispiel enthält drei Featureflags, deren Filter in der Eigenschaft `EnabledFor` definiert sind:

* `FeatureA` ist *aktiviert*.
* `FeatureB` ist *deaktiviert*.
* `FeatureC` gibt einen Filter namens `Percentage` mit einer Eigenschaft vom Typ `Parameters` an. `Percentage` ist ein konfigurierbarer Filter. In diesem Beispiel gibt `Percentage` an, dass das Flag `FeatureC` mit einer Wahrscheinlichkeit von 50 Prozent *aktiviert* ist.

## <a name="feature-flag-references"></a>Featureflag-Verweise

Damit Sie im Code einfach auf Featureflags verweisen können, sollten Sie sie als `enum`-Variablen definieren:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Überprüfen von Featureflags

Bei der Featureverwaltung wird grundsätzlich zunächst geprüft, ob ein Featureflag *aktiviert* ist. Ist dies der Fall, führt der Feature-Manager die im Feature enthaltenen Aktionen aus. Beispiel:

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Abhängigkeitsinjektion

In ASP.NET Core MVC kann auf den Feature-Manager `IFeatureManager` mittels Abhängigkeitsinjektion zugegriffen werden:

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Controlleraktionen

In MVC-Controllern steuern Sie mithilfe eines Attributs vom Typ `Feature`, ob eine gesamte Controllerklasse oder eine spezifische Aktion aktiviert wird. Für den folgenden Controller `HomeController` muss `FeatureA` *aktiviert* sein, damit eine der in der Controllerklasse enthaltenen Aktionen ausgeführt werden kann:

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Für die folgende Aktion `Index` muss `FeatureA` *aktiviert* sein, damit sie ausgeführt werden kann:

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Ist ein MVC-Controller oder eine Aktion blockiert, weil das steuernde Featureflag *deaktiviert* ist, wird eine registrierte Schnittstelle (`IDisabledFeaturesHandler`) aufgerufen. Die Standardschnittstelle `IDisabledFeaturesHandler` gibt den Statuscode 404 ohne Antworttext an den Client zurück.

## <a name="mvc-views"></a>MVC-Ansichten

In MVC-Ansichten können Sie ein Tag vom Typ `<feature>` verwenden, um das Rendern von Inhalten vom Aktivierungsstatus eines Featureflags abhängig zu machen:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC-Filter

Sie können MVC-Filter so einrichten, dass sie abhängig vom Zustand eines Featureflags aktiviert werden. Im folgenden Code wird ein MVC-Filter namens `SomeMvcFilter` hinzugefügt. Dieser Filter wird innerhalb der MVC-Pipeline nur ausgelöst, wenn `FeatureA` aktiviert ist.

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="routes"></a>Routen

Sie können Featureflags verwenden, um Routen dynamisch verfügbar zu machen. Im folgenden Code wird eine Route hinzugefügt, die `Beta` als den Standardcontroller festlegt – aber nur, wenn `FeatureA` aktiviert ist:

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>Middleware

Featureflags ermöglichen außerdem das bedingte Hinzufügen von Anwendungsbranches und Middleware. Im folgenden Code wird nur dann eine Middlewarekomponente in die Anforderungspipeline eingefügt, wenn `FeatureA` aktiviert ist:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Dieser Code baut auf der allgemeineren Verzweigung der gesamten Anwendung auf der Grundlage eines Featureflags auf:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie Featureflags in Ihrer ASP.NET Core-Anwendung unter Verwendung der Bibliotheken vom Typ `Microsoft.FeatureManagement` implementieren. Die folgenden Ressourcen enthalten weitere Informationen zur Unterstützung der Featureverwaltung in ASP.NET Core und App Configuration:

* [Beispielcode für ASP.NET Core-Featureflag](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Dokumentation für „Microsoft.FeatureManagement“](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Verwalten von Featureflags](./manage-feature-flags.md)
