---
title: Konfigurieren von ASP.NET Core-Apps
description: Erfahren Sie, wie Sie in Azure App Service eine ASP.NET Core-App in den nativen Windows-Instanzen oder in einem vordefinierten Linux-Container konfigurieren. In diesem Artikel werden die gängigsten Konfigurationsaufgaben vorgestellt.
ms.devlang: dotnet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 0f7047638aa2e2b4a9ac6ffade82fdc117b56cfb
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744179"
---
# <a name="configure-an-aspnet-core-app-for-azure-app-service"></a>Konfigurieren einer ASP.NET Core-App für Azure App Service

> [!NOTE]
> Informationen zu ASP.NET in .NET Framework finden Sie unter [Konfigurieren einer ASP.NET-App für Azure App Service](configure-language-dotnet-framework.md).

ASP.NET Core-Apps müssen in Azure App Service als kompilierte Binärdateien bereitgestellt werden. Das Veröffentlichungstool für Visual Studio erstellt die Projektmappe und stellt dann die kompilierten Binärdateien direkt bereit, während die App Service-Bereitstellungs-Engine zuerst das Coderepository und dann die Binärdateien kompiliert.

Dieser Leitfaden enthält wichtige Konzepte und Anweisungen für ASP.NET Core-Entwickler. Wenn Sie Azure App Service noch nie verwendet haben, folgen Sie zunächst dem [ASP.NET Core-Schnellstart](quickstart-dotnetcore.md) und dem [Tutorial zu ASP.NET Core mit SQL-Datenbank](tutorial-dotnetcore-sqldb-app.md).

::: zone pivot="platform-windows"  

## <a name="show-supported-net-core-runtime-versions"></a>Anzeigen unterstützter .NET Core-Runtimeversionen

In App Service sind auf den Windows-Instanzen bereits alle unterstützten .NET Core-Versionen installiert. Navigieren Sie zu `https://<app-name>.scm.azurewebsites.net/DebugConsole`, und führen Sie den folgenden Befehl in der browserbasierten Konsole aus, um die für Sie verfügbaren .NET Core-Runtime- und .NET Core SDK-Versionen anzuzeigen:

```azurecli-interactive
dotnet --info
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="show-net-core-version"></a>Anzeigen der .NET Core-Version

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um die aktuelle .NET Core-Version anzuzeigen:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um alle unterstützten .NET Core-Version anzuzeigen:

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

::: zone-end

## <a name="set-net-core-version"></a>Festlegen der .NET Core-Version

::: zone pivot="platform-windows"  

Legen Sie das Zielframework in der Projektdatei für das ASP.NET Core-Projekt fest. Weitere Informationen finden Sie unter [Auswählen der zu verwendenden .NET Core-Version](/dotnet/core/versions/selection) in der .NET Core-Dokumentation.

::: zone-end

::: zone pivot="platform-linux"

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um die .NET Core-Version auf 3.1 festzulegen:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|3.1"
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Anpassen der Buildautomatisierung

Wenn Sie Ihre App mithilfe von Git- oder ZIP-Paketen mit aktivierter Buildautomatisierung bereitstellen, durchläuft die App Service-Buildautomatisierung die Schritte der folgenden Sequenz:

1. Ausführen eines benutzerdefinierten Skripts, falls mittels `PRE_BUILD_SCRIPT_PATH` angegeben.
1. Ausführen von `dotnet restore`, um NuGet-Abhängigkeiten wiederherzustellen.
1. Ausführen von `dotnet publish`, um eine Binärdatei für die Produktion zu erstellen.
1. Ausführen eines benutzerdefinierten Skripts, falls mittels `POST_BUILD_SCRIPT_PATH` angegeben.

`PRE_BUILD_COMMAND` und `POST_BUILD_COMMAND` sind Umgebungsvariablen, die standardmäßig leer sind. Um Präbuildbefehle auszuführen, definieren Sie `PRE_BUILD_COMMAND`. Um Postbuildbefehle auszuführen, definieren Sie `POST_BUILD_COMMAND`.

Im folgenden Beispiel werden die beiden Variablen für eine Reihe von Befehlen angegeben, die durch Kommas getrennt sind.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Weitere Umgebungsvariablen zum Anpassen der Buildautomatisierung finden Sie unter [Oryx-Konfiguration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Weitere Informationen, wie App Service ASP.NET Core-Apps in Linux ausführt und erstellt, finden Sie unter [Oryx-Dokumentation: Erkennen und Erstellen von .NET Core-Apps](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md).

::: zone-end

## <a name="access-environment-variables"></a>Zugreifen auf Umgebungsvariablen

In App Service können Sie [App-Einstellungen außerhalb Ihres App-Codes festlegen](configure-common.md#configure-app-settings). Anschließend können Sie in jeder Klasse mithilfe des standardmäßigen ASP.NET Core-Abhängigkeitsinjektionsmusters auf sie zugreifen:

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Wenn Sie beispielsweise eine App-Einstellung mit demselben Namen in App Service und in *appsettings.json* konfigurieren, hat der App Service-Wert Vorrang vor dem Wert für *appsettings.json* . Mit dem lokalen Wert für *appsettings.json* können Sie die App lokal debuggen, aber mit dem Wert für App Service können Sie die App im Produkt mit Produktionseinstellungen ausführen. Verbindungszeichenfolgen funktionieren auf dieselbe Weise. Auf diese Weise können Sie Ihre Anwendungsgeheimnisse außerhalb Ihres Coderepositorys aufbewahren und auf die entsprechenden Werte zugreifen, ohne Ihren Code zu ändern.

> [!NOTE]
> Beachten Sie, dass auf die [hierarchischen Konfigurationsdaten](/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data) in der Datei *appsettings.json* mit dem `:`-Trennzeichen zugegriffen wird, das standardmäßig in .NET Core Verwendung findet. Wenn Sie eine bestimmte hierarchische Konfigurationseinstellung in App Service außer Kraft setzen möchten, legen Sie den Namen der App-Einstellung mit dem gleichen Trennzeichenformat im Schlüssel fest. Sie können das folgende Beispiel in der [Cloud Shell](https://shell.azure.com) ausführen:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>Bereitstellen von Projektmappen mit mehreren Projekten

Wenn eine Visual Studio-Projektmappe mehrere Projekte enthält, umfasst der Veröffentlichungsprozess von Visual Studio bereits das Auswählen des bereitzustellenden Projekts. Wenn Sie für die App Service-Bereitstellungs-Engine bereitstellen, z. B. mit Git oder einer ZIP-Bereitstellung, und dabei die Buildautomatisierung aktiviert ist, wählt die App Service-Bereitstellungs-Engine das erste Website- oder Webanwendungsprojekt aus, das als App Service-App gefunden wird. Sie können angeben, welches Projekt App Service verwenden soll, indem Sie die App-Einstellung `PROJECT` angeben. Führen Sie beispielsweise Folgendes in der [Cloud Shell](https://shell.azure.com) aus:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Zugreifen auf Diagnoseprotokolle

ASP.NET Core bietet einen [integrierten Protokollanbieter für App Service](/aspnet/core/fundamentals/logging/#azure-app-service). Fügen Sie in der Datei *Program.cs* Ihres Projekts den Anbieter mithilfe der `ConfigureLogging`-Erweiterungsmethode zu Ihrer Anwendung hinzu, wie dies im folgenden Beispiel gezeigt wird:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Anschließend können Sie Protokolle mit dem [standardmäßigen .NET Core-Muster](/aspnet/core/fundamentals/logging) konfigurieren und generieren.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Weitere Informationen zur Problembehandlung von ASP.NET Core-Apps in App Service finden Sie unter [Problembehandlung bei ASP.NET Core in Azure App Service und IIS](/aspnet/core/test/troubleshoot-azure-iis).

## <a name="get-detailed-exceptions-page"></a>Abrufen einer detaillierten Ausnahmenseite

Wenn Ihre ASP. NET Core-App eine Ausnahme im Visual Studio-Debugger erzeugt, zeigt der Browser eine detaillierte Ausnahmeseite an, aber in App Service wird diese Seite durch einen allgemeinen **HTTP 500** -Fehler oder durch die Meldung **Fehler beim Bearbeiten Ihrer Anforderung aufgetreten** ersetzt. Meldung. Um die detaillierte Ausnahmenseite in App Service anzuzeigen, fügen Sie die App-Einstellung `ASPNETCORE_ENVIRONMENT` zu Ihrer App hinzu, indem Sie den folgenden Befehl in der <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> ausführen.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Erkennen einer HTTPS-Sitzung

In App Service erfolgt die [SSL-Terminierung](https://wikipedia.org/wiki/TLS_termination_proxy) in den Modulen für den Netzwerklastenausgleich, sodass alle HTTPS-Anforderungen Ihre App als unverschlüsselte HTTP-Anforderungen erreichen. Wenn Ihre App-Logik wissen muss, ob die Benutzeranforderungen verschlüsselt sind, konfigurieren Sie die Forwarded Headers Middleware in *Startup.cs* :

- Konfigurieren Sie die Middleware mit [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions), um die Header `X-Forwarded-For` und `X-Forwarded-Proto` in `Startup.ConfigureServices` weiterzuleiten.
- Fügen Sie den bekannten Netzwerken private IP-Adressbereiche hinzu, damit die Middleware dem App Service-Lastenausgleich vertrauen kann.
- Rufen Sie die Methode [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) in `Startup.Configure` auf, bevor Sie andere Middleware aufrufen.

Wenn Sie alle drei Elemente zusammensetzen, sieht Ihr Code wie im folgenden Beispiel aus:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

Weitere Informationen finden Sie unter [Konfigurieren von ASP.NET Core für die Arbeit mit Proxyservern und Lastenausgleichen](/aspnet/core/host-and-deploy/proxy-load-balancer).

::: zone pivot="platform-linux"

## <a name="open-ssh-session-in-browser"></a>Öffnen einer SSH-Sitzung im Browser

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: ASP.NET Core-App mit SQL-Datenbank](tutorial-dotnetcore-sqldb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [Häufig gestellte Fragen (FAQ) zu Azure App Service unter Linux](faq-app-service-linux.md)

::: zone-end
