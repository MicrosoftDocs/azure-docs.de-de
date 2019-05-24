---
title: Konfigurieren von ASP.NET Core-Apps – Azure App Service | Microsoft-Dokumentation
description: Informationen zum Konfigurieren von ASP.NET Core-Apps, damit sie in Azure App Service funktionieren
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: f2781e3cc2433f73ba7ff33e5c452e29de746adf
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956203"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Konfigurieren einer Linux-ASP.NET Core-App für Azure App Service

ASP.NET Core-Apps müssen als kompilierte Binärdateien bereitgestellt werden. Das Veröffentlichungstool für Visual Studio erstellt die Projektmappe und stellt dann die kompilierten Binärdateien direkt bereit, während die App Service-Bereitstellungs-Engine zuerst das Coderepository und dann die Binärdateien kompiliert.

Dieser Leitfaden enthält wichtige Konzepte und Anleitungen für ASP.NET Core-Entwickler, die in App Service einen integrierten Linux-Container verwenden. Wenn Sie Azure App Service noch nie verwendet haben, folgen Sie zunächst dem [ASP.NET Core-Schnellstart](quickstart-dotnetcore.md) und dem [Tutorial zu ASP.NET Core mit SQL-Datenbank](tutorial-dotnetcore-sqldb-app.md).

## <a name="show-net-core-version"></a>Anzeigen der .NET Core-Version

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um die aktuelle .NET Core-Version anzuzeigen:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um alle unterstützten .NET Core-Version anzuzeigen:

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Festlegen der .NET Core-Version

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um die .NET Core-Version auf 2.1 festzulegen:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Zugreifen auf Umgebungsvariablen

In App Service können Sie [App-Einstellungen außerhalb Ihres App-Codes festlegen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings). Anschließend können Sie darauf unter Verwendung des ASP.NET -Standardmusters zugreifen:

```csharp
include Microsoft.Extensions.Configuration;
// retrieve App Service app setting
System.Configuration.ConfigurationManager.AppSettings["MySetting"]
// retrieve App Service connection string
Configuration.GetConnectionString("MyDbConnection")
```

Wenn Sie eine App-Einstellung mit demselben Namen in App Service und in *Web.config* konfigurieren, hat der App Service-Wert Vorrang vor dem Wert für „Web.config“. Mit dem Wert für Web.config können Sie die App lokal debuggen, aber mit dem Wert für App Service können Sie die App im Produkt mit Produktionseinstellungen ausführen. Verbindungszeichenfolgen funktionieren auf dieselbe Weise. Auf diese Weise können Sie Ihre Anwendungsgeheimnisse außerhalb Ihres Coderepositorys aufbewahren und auf die entsprechenden Werte zugreifen, ohne Ihren Code zu ändern.

## <a name="get-detailed-exceptions-page"></a>Abrufen einer detaillierten Ausnahmenseite

Wenn Ihre ASP. NET-App eine Ausnahme im Visual Studio-Debugger erzeugt, zeigt der Browser eine detaillierte Ausnahmeseite an, aber in App Service wird diese Seite durch einen allgemeinen **HTTP 500**-Fehler oder durch **Fehler beim Bearbeiten Ihrer Anforderung aufgetreten** ersetzt. Meldung. Um die detaillierte Ausnahmenseite in App Service anzuzeigen, fügen Sie die App-Einstellung `ASPNETCORE_ENVIRONMENT` zu Ihrer App hinzu, indem Sie den folgenden Befehl in der <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> ausführen.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Erkennen einer HTTPS-Sitzung

In App Service erfolgt die [SSL-Beendigung](https://wikipedia.org/wiki/TLS_termination_proxy) in den Modulen für den Netzwerklastenausgleich, sodass alle HTTPS-Anforderungen Ihre App als unverschlüsselte HTTP-Anforderungen erreichen. Wenn Ihre App-Logik wissen muss, ob die Benutzeranforderungen verschlüsselt sind, konfigurieren Sie die Forwarded Headers Middleware in *Startup.cs*:

- Konfigurieren Sie die Middleware mit [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions), um die Header `X-Forwarded-For` und `X-Forwarded-Proto` in `Startup.ConfigureServices` weiterzuleiten.
- Fügen Sie den bekannten Netzwerken private IP-Adressbereiche hinzu, damit die Middleware dem App Service-Lastenausgleich vertrauen kann.
- Rufen Sie die Methode [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) in `Startup.Configure` auf, bevor Sie andere Middleware aufrufen.

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

Weitere Informationen finden Sie unter [Konfigurieren von ASP.NET Core für die Arbeit mit Proxyservern und Lastenausgleichen](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Bereitstellen von Projektmappen mit mehreren Projekten

Wenn Sie ein ASP.NET-Repository mit einer *.csproj*-Datei im Stammverzeichnis für die Bereitstellungs-Engine bereitstellen, wird das Projekt von der Engine implementiert. Wenn Sie ein ASP.NET-Repository mit einer *.sln*-Datei im Stammverzeichnis bereitstellen, wählt die Engine die erste Website oder das erste Webanwendungsprojekt aus, das sie als App Service-App findet. Es ist möglich, dass die Engine nicht das von Ihnen gewünschte Projekt auswählt.

Zum Bereitstellen einer Projektmappe mit mehreren Projekten können Sie das Projekt, das im App Service verwendet werden soll, auf zwei verschiedene Arten angeben:

### <a name="using-deployment-file"></a>Mithilfe einer .deployment-Datei

Fügen Sie eine *.deployment*-Datei zum Stamm des Repositorys und dann den folgenden Code hinzu:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Mithilfe von App-Einstellungen

Fügen Sie in der <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a> Ihrer App Service-App eine App-Einstellung hinzu, indem Sie den folgenden CLI-Befehl ausführen. Ersetzen Sie *\<Anwendungsname>*, *\<Ressourcengruppenname>* und *\<Projektname>* durch die entsprechenden Werte.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Zugreifen auf Diagnoseprotokolle

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Öffnen einer SSH-Sitzung im Browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: ASP.NET Core-App mit SQL-Datenbank](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [App Service unter Linux – Häufig gestellte Fragen](app-service-linux-faq.md)