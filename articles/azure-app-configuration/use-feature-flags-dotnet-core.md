---
title: Tutorial zur Verwendung von Featureflags in einer .NET Core-App | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie Featureflags in .NET Core-Apps implementieren.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 701fe4ffc6147086dde740bfdb2dc7db92508e28
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380235"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Tutorial: Verwenden von Featureflags in einer ASP.NET Core-App

Die .NET Core-Featureverwaltungsbibliotheken bieten idiomatische Unterstützung für die Implementierung von Featureflags in einer .NET- oder ASP.NET Core-Anwendung. Dank dieser Bibliotheken können Sie Ihrem Code auf deklarative Weise Featureflags hinzufügen und sich das manuelle Schreiben von Code zum Aktivieren oder Deaktivieren von Features mit Anweisungen vom Typ `if` sparen.

Die Featureverwaltungsbibliotheken verwalten darüber hinaus Featureflag-Lebenszyklen im Hintergrund. Die Bibliotheken können Flagzustände aktualisieren und zwischenspeichern, garantieren, dass ein Flagzustand während eines Anforderungsaufrufs unveränderlich ist, und Ähnliches. Darüber hinaus bietet die ASP.NET Core-Bibliothek sofort einsetzbare Integrationen – einschließlich MVC-Controlleraktionen, Ansichten, Routen und Middleware.

Unter [Schnellstart: Hinzufügen von Featureflags zu einer ASP.NET Core-App](./quickstart-feature-flag-aspnet-core.md) finden Sie ein einfaches Beispiel für die Verwendung von Featureflags in einer ASP.NET Core-Anwendung. In diesem Tutorial werden weitere Einrichtungsoptionen und Funktionen der Featureverwaltungsbibliotheken gezeigt. Sie können die in der Schnellstartanleitung erstellte Beispiel-App verwenden, um den in diesem Tutorial gezeigten Beispielcode zu testen. 

Die Referenzdokumentation zur Featureverwaltungs-API von ASP.NET Core finden Sie unter [Namespace „Microsoft.FeatureManagement“](/dotnet/api/microsoft.featuremanagement).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen von Featureflags in wichtigen Teilen Ihrer Anwendung, um die Verfügbarkeit von Features zu steuern
> * Integrieren in App Configuration, wenn Sie damit Featureflags verwalten

## <a name="set-up-feature-management"></a>Einrichten der Featureverwaltung

Für den Zugriff auf den .NET Core-Feature-Manager muss Ihre App über Verweise auf das NuGet-Paket `Microsoft.FeatureManagement.AspNetCore` verfügen.

Der .NET Core-Feature-Manager wird über das native Konfigurationssystem des Frameworks konfiguriert. Dadurch können Sie die Featureflageinstellungen Ihrer Anwendung mit einer beliebigen, von .NET Core unterstützten Konfigurationsquelle konfigurieren – unter anderem mit der lokalen Datei *appsettings.json* oder mit Umgebungsvariablen.

Der Feature-Manager ruft die Featureflagkonfiguration standardmäßig aus dem Abschnitt `"FeatureManagement"` der .NET Core-Konfigurationsdaten ab. Wenn Sie den Standardspeicherort für die Konfiguration verwenden möchten, können Sie die Methode [AddFeatureManagement](/dotnet/api/microsoft.featuremanagement.servicecollectionextensions.addfeaturemanagement) der Sammlung **IServiceCollection** aufrufen, die an die Methode **ConfigureServices** der Klasse **Startup** übergeben wurde.


```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement();
    }
}
```

Soll die Featureverwaltungskonfiguration aus einem anderen Konfigurationsabschnitt abgerufen werden, rufen Sie [Configuration.GetSection](/dotnet/api/microsoft.web.administration.configuration.getsection) auf, und übergeben Sie den Namen des gewünschten Abschnitts. Im folgenden Beispiel wird er angewiesen, sie stattdessen aus einem Abschnitt namens `"MyFeatureFlags"` zu lesen:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement(Configuration.GetSection("MyFeatureFlags"));
    }
}
```


Wenn Sie in Ihren Featureflags Filter verwenden, müssen Sie den Namespace [Microsoft.FeatureManagement.FeatureFilters](/dotnet/api/microsoft.featuremanagement.featurefilters) einschließen, einen Aufruf von [AddFeatureFilters](/dotnet/api/microsoft.featuremanagement.ifeaturemanagementbuilder.addfeaturefilter) hinzufügen und den Typnamen des gewünschten Filters als generischen Typ der Methode angeben. Weitere Informationen zur Verwendung von Featurefiltern zum dynamischen Aktivieren und Deaktivieren von Funktionen finden Sie unter [Aktivieren des gestaffelten Rollouts von Features für Zielgruppen](/azure/azure-app-configuration/howto-targetingfilter-aspnet-core).

Das folgende Beispiel zeigt, wie Sie einen integrierten Featurefilter namens `PercentageFilter` verwenden:



```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Featureflags sollten sich außerhalb der Anwendung befinden und separat verwaltet werden, anstatt sie in Ihrer Anwendung hart zu codieren. Dadurch können Sie Flagzustände jederzeit ändern, und die Änderungen werden in der Anwendung sofort wirksam. Der Azure App Configuration-Dienst bietet eine dedizierte Portalbenutzeroberfläche, über die Sie alle Ihre Featureflags verwalten können. Darüber hinaus werden die Featureflags durch den Azure App Configuration-Dienst unter Verwendung der zugehörigen .NET Core-Clientbibliotheken direkt an Ihre Anwendung übermittelt.

Die Verbindung zwischen Ihrer ASP.NET Core-Anwendung und App Configuration lässt sich am einfachsten über den im NuGet-Paket `Microsoft.Azure.AppConfiguration.AspNetCore` enthaltenen Konfigurationsanbieter herstellen. Nachdem Sie einen Verweis auf das Paket eingeschlossen haben, können Sie dieses NuGet-Paket wie folgt verwenden:

1. Öffnen Sie die Datei *Program.cs*, und fügen Sie folgenden Code hinzu.
    > [!IMPORTANT]
    > `CreateHostBuilder` ersetzt `CreateWebHostBuilder` in .NET Core 3.x. Wählen Sie auf der Grundlage ihrer Umgebung die richtige Syntax aus.

    ### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
            }).UseStartup<Startup>());
    ```
        
    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration(config =>
                    {
                        var settings = config.Build();
                        config.AddAzureAppConfiguration(options =>
                            options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                    }).UseStartup<Startup>();
    ```
    ---

2. Öffnen Sie *Startup.cs*, und aktualisieren Sie die Methoden `Configure` und `ConfigureServices`, um die integrierte Middleware namens `UseAzureAppConfiguration` hinzuzufügen. Diese Middleware gestattet die regelmäßige Aktualisierung der Featureflagwerte, während die ASP.NET Core-Web-App weiterhin Anforderungen empfängt.



    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseAzureAppConfiguration();
    }
    ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddAzureAppConfiguration();
   }
   ```
   
In einem typischen Szenario aktualisieren Sie die Featureflagwerte regelmäßig, um verschiedene Features Ihrer Anwendung bereitzustellen und zu aktivieren. Standardmäßig werden die Featureflagwerte für einen Zeitraum von 30 Sekunden zwischengespeichert, daher würde ein Aktualisierungsvorgang, der ausgelöst wird, wenn die Middleware eine Anforderung empfängt, den Wert erst nach Ablauf des zwischengespeicherten Werts aktualisieren. Der folgende Code zeigt, wie Sie die Cacheablaufzeit oder das Abrufintervall unter Verwendung von [CacheExpirationInterval](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.featuremanagement.featureflagoptions.cacheexpirationinterval) im Aufruf von **UseFeatureFlags** auf fünf Minuten festlegen.


    
```csharp
config.AddAzureAppConfiguration(options =>
    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags(featureFlagOptions => {
        featureFlagOptions.CacheExpirationInterval = TimeSpan.FromMinutes(5);
    }));
});
```


## <a name="feature-flag-declaration"></a>Featureflagdeklaration

Jede Featureflagdeklaration besteht aus zwei Teilen: einem Namen und einer Filterliste, anhand der ausgewertet wird, ob ein Feature *aktiviert* ist (der Wert also `True` lautet). Ein Filter definiert ein Kriterium für die Aktivierung eines Features.

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
* `FeatureC` gibt einen Filter namens `Percentage` mit einer Eigenschaft vom Typ `Parameters` an. `Percentage` ist ein konfigurierbarer Filter. In diesem Beispiel gibt `Percentage` an, dass das Flag `FeatureC` mit einer Wahrscheinlichkeit von 50 Prozent *aktiviert* ist. Eine Anleitung zur Verwendung von Featurefiltern finden Sie unter [Verwenden von Featurefiltern, um bedingte Featureflags zu aktivieren](/azure/azure-app-configuration/howto-feature-filters-aspnet-core).




## <a name="use-dependency-injection-to-access-ifeaturemanager"></a>Verwenden von Abhängigkeitsinjektion für den Zugriff auf „IFeatureManager“ 

Bei einigen Vorgängen (etwa beim manuellen Überprüfen von Featureflagwerten) muss eine Instanz von [IFeatureManager](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement.ifeaturemanager?view=azure-dotnet-preview) abgerufen werden. In ASP.NET Core MVC kann auf den Feature-Manager `IFeatureManager` mittels Abhängigkeitsinjektion zugegriffen werden. Im folgenden Beispiel wird der Signatur des Konstruktors für einen Controller ein Argument vom Typ `IFeatureManager` hinzugefügt. Die Runtime löst den Verweis automatisch auf und stellt beim Aufrufen des Konstruktors eine Instanz der Schnittstelle bereit. Wenn Sie eine Anwendungsvorlage verwenden, in der für den Controller bereits mindestens ein Abhängigkeitsinjektionsargument im Konstruktor (beispielsweise `ILogger`) vorhanden ist, können Sie einfach `IFeatureManager` als zusätzliches Argument hinzufügen:

### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)
    
```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```
    
### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

---

## <a name="feature-flag-references"></a>Featureflag-Verweise

Definieren Sie Featureflags als Zeichenfolgenvariablen, um im Code auf sie verweisen zu können:

```csharp
public static class MyFeatureFlags
{
    public const string FeatureA = "FeatureA";
    public const string FeatureB = "FeatureB";
    public const string FeatureC = "FeatureC";
}
```

## <a name="feature-flag-checks"></a>Überprüfen von Featureflags

Bei der Featureverwaltung wird häufig folgendes Muster verwendet: Es wird überprüft, ob ein Featureflag *aktiviert* ist, und ein Codeabschnitt ausgeführt, wenn dies der Fall ist. Beispiel:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(MyFeatureFlags.FeatureA))
{
    // Run the following code
}
```

## <a name="controller-actions"></a>Controlleraktionen

Bei Verwendung von MVC-Controllern können Sie mithilfe des Attributs `FeatureGate` steuern, ob eine gesamte Controllerklasse oder eine spezifische Aktion aktiviert werden soll. Für den folgenden Controller `HomeController` muss `FeatureA`*aktiviert* sein, damit eine der in der Controllerklasse enthaltenen Aktionen ausgeführt werden kann:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Für die folgende Aktion `Index` muss `FeatureA`*aktiviert* sein, damit sie ausgeführt werden kann:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Ist ein MVC-Controller oder eine Aktion blockiert, da das steuernde Featureflag *deaktiviert* ist, wird eine registrierte Schnittstelle vom Typ [IDisabledFeaturesHandler](/dotnet/api/microsoft.featuremanagement.mvc.idisabledfeatureshandler?view=azure-dotnet-preview) aufgerufen. Die Standardschnittstelle `IDisabledFeaturesHandler` gibt den Statuscode 404 ohne Antworttext an den Client zurück.

## <a name="mvc-views"></a>MVC-Ansichten

Öffnen Sie *_ViewImports.cshtml* im Verzeichnis *Views*, und fügen Sie das Taghilfsprogramm für den Feature-Manager hinzu:

```html
@addTagHelper *, Microsoft.FeatureManagement.AspNetCore
```

In MVC-Ansichten können Sie ein Tag vom Typ `<feature>` verwenden, um das Rendern von Inhalten vom Aktivierungsstatus eines Featureflags abhängig zu machen:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Um alternative Inhalte anzuzeigen, wenn die Anforderungen nicht erfüllt sind, kann das `negate`-Attribut verwendet werden.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

Das `<feature>`-Tag kann auch verwendet werden, um Inhalte anzuzeigen, wenn ein oder alle Features in einer Liste aktiviert sind.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC-Filter

Sie können MVC-Filter so einrichten, dass sie abhängig vom Zustand eines Featureflags aktiviert werden. Diese Funktion ist auf Filter beschränkt, von denen [IAsyncActionFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncactionfilter) implementiert wird. Im folgenden Code wird ein MVC-Filter namens `ThirdPartyActionFilter` hinzugefügt. Dieser Filter wird innerhalb der MVC-Pipeline nur ausgelöst, wenn `FeatureA` aktiviert ist.  

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<ThirdPartyActionFilter>(MyFeatureFlags.FeatureA);
    });
}
```

## <a name="middleware"></a>Middleware

Featureflags ermöglichen außerdem das bedingte Hinzufügen von Anwendungsbranches und Middleware. Im folgenden Code wird nur dann eine Middlewarekomponente in die Anforderungspipeline eingefügt, wenn `FeatureA` aktiviert ist:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(MyFeatureFlags.FeatureA);
```

Dieser Code baut auf der allgemeineren Verzweigung der gesamten Anwendung auf der Grundlage eines Featureflags auf:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie Featureflags in Ihrer ASP.NET Core-Anwendung unter Verwendung der Bibliotheken vom Typ `Microsoft.FeatureManagement` implementieren. Die folgenden Ressourcen enthalten weitere Informationen zur Unterstützung der Featureverwaltung in ASP.NET Core und App Configuration:

* [Beispielcode für ASP.NET Core-Featureflag](./quickstart-feature-flag-aspnet-core.md)
* [Dokumentation für „Microsoft.FeatureManagement“](/dotnet/api/microsoft.featuremanagement)
* [Verwalten von Featureflags](./manage-feature-flags.md)
