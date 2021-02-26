---
title: Verwenden der Abhängigkeitsinjektion in Azure Functions (.NET)
description: Verwenden von Abhängigkeitsinjektionen für die Registrierung und Verwendung von Diensten in .NET-Funktionen
author: ggailey777
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 01/27/2021
ms.author: glenga
ms.reviewer: jehollan
ms.openlocfilehash: 66e2cd22f4bcb95be65d6d04345dcac622436a04
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955087"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Verwenden der Abhängigkeitsinjektion in Azure Functions (.NET)

Azure Functions unterstützt das Softwareentwurfsmuster „Abhängigkeitsinjektion“ (Dependency Injection, DI). Damit kann eine [Umkehrung der Steuerung (Inversion of Control, IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) zwischen Klassen und ihren Abhängigkeiten erreicht werden.

- Die Abhängigkeitsinjektion in Azure Functions basiert auf den .NET Core-Features für die Abhängigkeitsinjektion. Vertrautheit mit der [.NET Core- Abhängigkeitsinjektion](/aspnet/core/fundamentals/dependency-injection) wird empfohlen. Es bestehen Unterschiede darin, wie Sie Abhängigkeiten überschreiben und wie Konfigurationswerte mit Azure Functions im Verbrauchstarif gelesen werden.

- Die Unterstützung der Abhängigkeitsinjektion beginnt mit Azure Functions 2.x.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die Abhängigkeitsinjektion verwenden können, müssen Sie die folgenden NuGet-Pakete installieren:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions-Paket](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/), Version 1.0.28 oder höher

- [Microsoft.Extensions.DependencyInjection](https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection/) (derzeit werden nur Version 3.x und frühere unterstützt)

## <a name="register-services"></a>Registrieren von Diensten

Um Dienste zu registrieren, erstellen Sie eine Methode zum Konfigurieren und Hinzufügen von Komponenten zu einer `IFunctionsHostBuilder`-Instanz.  Der Azure Functions-Host erstellt eine Instanz von `IFunctionsHostBuilder` und übergibt diese direkt an Ihre Methode.

Fügen Sie zum Registrieren der Methode das `FunctionsStartup`-Assembly-Attribut hinzu, mit dem der Typname angegeben wird, der beim Starten verwendet wird.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton<IMyService>((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

In diesem Beispiel wird das Paket [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) verwendet, das beim Start `HttpClient` registrieren muss.

### <a name="caveats"></a>Vorbehalte

Eine Reihe von Registrierungsschritten wird vor und nach dem Verarbeiten der Startup-Klasse ausgeführt. Berücksichtigen Sie daher die folgenden Punkte:

- *Die Startup-Klasse ist nur für Setup und Registrierung vorgesehen.* Vermeiden Sie die Verwendung von Diensten, die beim Start während des Startvorgangs registriert werden. Versuchen Sie beispielsweise nicht, eine Nachricht in einer Protokollierung zu protokollieren, die während des Starts registriert wird. Dieser Zeitpunkt des Registrierungsprozesses ist zu früh, damit ihre Dienste zur Verwendung verfügbar sind. Nachdem die `Configure`-Methode ausgeführt wurde, registriert die Azure Functions-Laufzeit weiterhin zusätzliche Abhängigkeiten, die sich den Betrieb ihrer Dienste auswirken können.

- *Der Container für die Abhängigkeitsinjektion enthält nur explizit registrierte Typen*. Die einzigen Dienste, die als injizierbare Typen verfügbar sind, sind diejenigen, die in der `Configure`-Methode eingerichtet werden. Folglich sind Azure Functions-spezifische Typen wie `BindingContext` und `ExecutionContext` während des Setups oder als injizierbare Typen nicht verfügbar.

## <a name="use-injected-dependencies"></a>Verwenden von eingefügten Abhängigkeiten

Die Konstruktorinjektion wird verwendet, um Ihre Abhängigkeiten in einer Funktion verfügbar zu machen. Bei Verwendung der Konstruktorinjektion dürfen keine statischen Klassen für eingefügte Dienste oder für Ihre Funktionsklassen verwendet werden.

Im folgenden Beispiel wird veranschaulicht, wie die Abhängigkeiten `IMyService` und `HttpClient` in eine per HTTP ausgelöste Funktion eingefügt werden.

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;
using System.Threading.Tasks;

namespace MyNamespace
{
    public class MyHttpTrigger
    {
        private readonly HttpClient _client;
        private readonly IMyService _service;

        public MyHttpTrigger(HttpClient httpClient, IMyService service)
        {
            this._client = httpClient;
            this._service = service;
        }

        [FunctionName("MyHttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            var response = await _client.GetAsync("https://microsoft.com");
            var message = _service.GetMessage();

            return new OkObjectResult("Response from function with injected dependencies.");
        }
    }
}
```

In diesem Beispiel wird das Paket [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) verwendet, das beim Start `HttpClient` registrieren muss.

## <a name="service-lifetimes"></a>Dienstlebensdauer

Azure Functions-Apps bieten dieselbe Dienstlebensdauer wie die [ASP.NET-Abhängigkeitsinjektion](/aspnet/core/fundamentals/dependency-injection#service-lifetimes). Bei einer Funktions-App weisen die verschiedenen Dienstlebensdauern die folgenden Verhalten auf:

- **Vorübergehend**: Bei jeder Lösung des Diensts werden vorübergehende Dienste erstellt.
- **Bereichsbezogen**: Die bereichsbezogene Lebensdauer eines Diensts entspricht der Ausführungslebensdauer einer Funktion. Bereichsbezogene Dienste werden einmal pro Funktionsausführung erstellt. In späteren Anforderungen für diesen Dienst während der Ausführung wird die vorhandene Dienstinstanz wiederverwendet.
- **Singleton**: Die Lebensdauer eines Singletondiensts entspricht der Lebensdauer des Hosts und wird für Funktionsausführungen dieser Instanz wiederverwendet. Dienste mit Singleton-Lebensdauer werden für Verbindungen und Clients empfohlen, z. B. Instanzen von `DocumentClient` oder `HttpClient`.

Sie können auf GitHub ein [Beispiel für verschiedene Dienstlebensdauern](https://github.com/Azure/azure-functions-dotnet-extensions/tree/main/src/samples/DependencyInjection/Scopes) anzeigen bzw. herunterladen.

## <a name="logging-services"></a>Protokollierungsdienste

Wenn Sie einen eigenen Protokollierungsanbieter benötigen, registrieren Sie einen benutzerdefinierten Typ als eine Instanz von [`ILoggerProvider`](/dotnet/api/microsoft.extensions.logging.iloggerfactory) (verfügbar über das NuGet-Paket [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)).

Application Insights wird von Azure Functions automatisch hinzugefügt.

> [!WARNING]
> - Fügen Sie `AddApplicationInsightsTelemetry()`, wodurch Dienste registriert werden, die zu Konflikten mit den von der Umgebung bereitgestellten Diensten führen können, nicht der Dienstsammlung hinzu.
> - Registrieren Sie keine eigene Instanz von `TelemetryConfiguration` oder `TelemetryClient`, wenn Sie die integrierten Application Insights-Funktionen verwenden. Wenn Sie Ihre eigene `TelemetryClient`-Instanz konfigurieren müssen, erstellen Sie diese über die eingefügte `TelemetryConfiguration`, wie in [Protokollieren von benutzerdefinierter Telemetrie in C#-Funktionen](functions-dotnet-class-library.md?tabs=v2%2Ccmd#log-custom-telemetry-in-c-functions) gezeigt.

### <a name="iloggert-and-iloggerfactory"></a>„ILogger<T>“ und „ILoggerFactory“

Vom Host werden die Dienste `ILogger<T>` und `ILoggerFactory` in Konstruktoren eingefügt.  Diese neuen Protokollierungsfilter werden jedoch standardmäßig aus den Funktionsprotokollen herausgefiltert.  Sie müssen die Datei `host.json` ändern, um zusätzliche Filter und Kategorien verwenden zu können.

Im folgenden Beispiel wird ein Element vom Typ `ILogger<HttpTrigger>` mit Protokollen hinzugefügt, die für den Host verfügbar gemacht werden:

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

In der folgenden Beispieldatei `host.json` wird der Protokollfilter hinzugefügt:

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingSettings": {
                "isEnabled": true,
                "excludedTypes": "Request"
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

Weitere Informationen zu Protokollgraden finden Sie unter [Konfigurieren von Protokollgraden](configure-monitoring.md#configure-log-levels).

## <a name="function-app-provided-services"></a>Dienste, die von Funktions-App bereitgestellt werden

Der Funktionshost registriert viele Dienste. Es ist sicher, die folgenden Dienste als Abhängigkeit in Ihrer Anwendung zu verwenden:

|Diensttyp|Lebensdauer|BESCHREIBUNG|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Laufzeitkonfiguration|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Verantwortlich für die Bereitstellung der Hostinstanz-ID|

[Erstellen Sie ein Issue, und schlagen Sie es auf GitHub vor](https://github.com/azure/azure-functions-host), wenn Sie eine Abhängigkeit von anderen Diensten erstellen möchten.

### <a name="overriding-host-services"></a>Überschreiben von Hostdiensten

Das Überschreiben der vom Host bereitgestellten Dienste wird derzeit nicht unterstützt.  [Erstellen Sie ein Issue, und schlagen Sie es auf GitHub vor](https://github.com/azure/azure-functions-host), falls andere Dienste vorhanden sind, die Sie überschreiben möchten.

## <a name="working-with-options-and-settings"></a>Arbeiten mit Optionen und Einstellungen

Werte, die in [App-Einstellungen](./functions-how-to-use-azure-function-app-settings.md#settings) definiert sind, sind in einer `IConfiguration`-Instanz verfügbar, die es Ihnen ermöglicht, App-Einstellungswerte in der Startup-Klasse zu lesen.

Sie können Werte aus der `IConfiguration`-Instanz in einen benutzerdefinierten Typ extrahieren. Wenn Sie App-Einstellungswerte in einen benutzerdefinierten Typ kopieren, können Sie Ihre Dienste mühelos testen, indem Sie diese Werte injizierbar gestalten. Die in die Konfigurationsinstanz eingelesenen Einstellungen müssen einfache Schlüssel/Wert-Paare sein.

Beachten Sie die folgende Klasse, die eine Eigenschaft mit dem Namen „consistent“ mit einer App-Einstellung enthält:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

Sowie eine Datei `local.settings.json`, die die benutzerdefinierte Einstellung möglicherweise wie folgt strukturiert:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

Innerhalb der `Startup.Configure`-Methode können Sie Werte aus der `IConfiguration`-Instanz mit dem folgenden Code in Ihren benutzerdefinierten Typ extrahieren:

```csharp
builder.Services.AddOptions<MyOptions>()
    .Configure<IConfiguration>((settings, configuration) =>
    {
        configuration.GetSection("MyOptions").Bind(settings);
    });
```

Durch den Aufruf von `Bind` werden Werte mit übereinstimmenden Eigenschaftsnamen aus der Konfiguration in die benutzerdefinierte Instanz kopiert. Die Optionsinstanz ist nun im IoC-Container zum Injizieren in eine Funktion verfügbar.

Das Optionsobjekt wird als Instanz der generischen `IOptions`-Schnittstelle in die Funktion injiziert. Verwenden Sie die `Value`-Eigenschaft, um auf die in Ihrer Konfiguration gefundenen Werte zuzugreifen.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Weitere Informationen zum Arbeiten mit Optionen finden Sie unter [Optionsmuster in ASP.NET Core](/aspnet/core/fundamentals/configuration/options).

## <a name="using-aspnet-core-user-secrets"></a>Verwenden von geheimen ASP.NET Core-Benutzerschlüsseln

Für die lokale Entwicklung stellt ASP.NET Core das [Secret Manager-Tool](/aspnet/core/security/app-secrets#secret-manager) bereit, mit dem Sie Informationen zu Geheimnissen außerhalb des Stammverzeichnisses des Projekts speichern können. Dadurch sinkt die Wahrscheinlichkeit, dass geheime Schlüssel versehentlich an die Quellcodeverwaltung übertragen werden. Azure Functions Core Tools (Version 3.0.3233 oder höher) liest automatisch geheime Schlüssel, die vom ASP.NET Core Secret Manager erstellt wurden.

Führen Sie den folgenden Befehl im Stammverzeichnis des .NET Azure Functions-Projekts aus, um es für die Verwendung von geheimen Benutzerschlüsseln zu konfigurieren.

```bash
dotnet user-secrets init
```

Verwenden Sie dann den Befehl `dotnet user-secrets set`, um geheime Schlüssel zu erstellen oder zu aktualisieren.

```bash
dotnet user-secrets set MySecret "my secret value"
```

Verwenden Sie `IConfiguration` oder `IOptions`, um auf die Werte der geheimen Benutzerschlüssel in Ihrem Funktions-App-Code zuzugreifen.

## <a name="customizing-configuration-sources"></a>Anpassen von Konfigurationsquellen

> [!NOTE]
> Die Anpassung von Konfigurationsquellen ist ab Azure Functions-Hostversionen 2.0.14192.0 und 3.0.14191.0 verfügbar.

Zum Angeben zusätzlicher Konfigurationsquellen überschreiben Sie die `ConfigureAppConfiguration`-Methode in der `StartUp`-Klasse Ihrer Funktions-App.

Im folgenden Beispiel werden Konfigurationswerte aus einer Basisdatei und einer Datei mit optionalen umgebungsspezifischen App-Einstellungen hinzugefügt.

```csharp
using System.IO;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
        {
            FunctionsHostBuilderContext context = builder.GetContext();

            builder.ConfigurationBuilder
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, "appsettings.json"), optional: true, reloadOnChange: false)
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, $"appsettings.{context.EnvironmentName}.json"), optional: true, reloadOnChange: false)
                .AddEnvironmentVariables();
        }
    }
}
```

Fügen Sie der Eigenschaft `ConfigurationBuilder` von `IFunctionsConfigurationBuilder` Konfigurationsanbieter hinzu. Weitere Informationen zur Verwendung von Konfigurationsanbietern finden Sie unter [Konfiguration in ASP.NET Core](/aspnet/core/fundamentals/configuration/#configuration-providers).

`FunctionsHostBuilderContext` wird aus `IFunctionsConfigurationBuilder.GetContext()` abgerufen. Verwenden Sie diesen Kontext, um den aktuellen Umgebungsnamen abzurufen und den Speicherort der Konfigurationsdateien in Ihrem Funktions-App-Ordner aufzulösen.

Standardmäßig werden Konfigurationsdateien wie *appsettings.json* nicht automatisch in den Ausgabeordner der Funktions-App kopiert. Aktualisieren Sie die Datei mit der Endung *.csproj* entsprechend dem folgenden Beispiel, um sicherzustellen, dass die Dateien kopiert werden.

```xml
<None Update="appsettings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>      
</None>
<None Update="appsettings.Development.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
</None>
```

> [!IMPORTANT]
> Bei Funktions-Apps, die im Nutzungs- oder Premium-Plan ausgeführt werden, können Änderungen an Konfigurationswerten, die in Triggern verwendet werden, zu Skalierungsfehlern führen. Alle Änderungen an diesen Eigenschaften durch die `FunctionsStartup`-Klasse führen zu einem Startfehler der Funktions-App.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

- [Überwachen Ihrer Funktions-App](functions-monitoring.md)
- [Bewährte Methoden für Funktionen](functions-best-practices.md)
