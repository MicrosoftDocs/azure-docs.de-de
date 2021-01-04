---
title: Schnellstart für Azure App Configuration mit ASP.NET Core | Microsoft-Dokumentation
description: Erstellen Sie mit Azure App Configuration eine ASP.NET Core-App, um die Speicherung und Verwaltung von Anwendungseinstellungen für eine ASP.NET Core-Anwendung zu zentralisieren.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp, contperf-fy21q1
ms.topic: quickstart
ms.date: 09/25/2020
ms.author: alkemper
ms.openlocfilehash: d1582cda4b083623daf7bb756d0bc8aa3e74eae8
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033645"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Schnellstart: Erstellen einer ASP.NET Core-App mit Azure App Configuration

In dieser Schnellstartanleitung verwenden Sie Azure App Configuration, um die Speicherung und Verwaltung von Anwendungseinstellungen für eine ASP.NET Core-App zu zentralisieren. Mit ASP.NET Core wird ein Konfigurationsobjekt, das auf nur einem Schlüssel-Wert-Paar basiert, mit Einstellungen einer oder mehrerer Datenquellen erstellt, die von einer App angegeben werden. Diese Datenquellen werden als *Konfigurationsanbieter* bezeichnet. Da der .NET Core-Client von App Configuration als Konfigurationsanbieter implementiert wird, wird der Dienst wie jede andere Datenquelle angezeigt.

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/dotnet)
* [.NET Core SDK](https://dotnet.microsoft.com/download)

> [!TIP]
> Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Befehlszeilenanweisungen in diesem Artikel ausführen können. Für sie sind allgemeine Azure-Tools einschließlich des .NET Core SDK vorinstalliert. Wenn Sie bei Ihrem Azure-Abonnement angemeldet sind, starten Sie [Azure Cloud Shell](https://shell.azure.com) über shell.azure.com. Weitere Informationen zu Azure Cloud Shell finden Sie in der [Dokumentation](../cloud-shell/overview.md).

## <a name="create-an-app-configuration-store"></a>Erstellen eines App Configuration-Speichers

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

7. Wählen Sie **Vorgänge** > **Konfigurations-Explorer** > **Erstellen** > **Schlüssel-Wert** aus, um die folgenden Schlüssel-Wert-Paare hinzuzufügen:

    | Schlüssel                                | Wert                               |
    |------------------------------------|-------------------------------------|
    | `TestApp:Settings:BackgroundColor` | *#FFF*                              |
    | `TestApp:Settings:FontColor`       | *#000*                              |
    | `TestApp:Settings:FontSize`        | *24*                                |
    | `TestApp:Settings:Message`         | *Daten aus Azure App Configuration* |

    Lassen Sie **Bezeichnung** und **Inhaltstyp** vorerst leer. Wählen Sie **Übernehmen**.

## <a name="create-an-aspnet-core-web-app"></a>Erstellen einer ASP.NET Core-Web-App

Verwenden Sie die [.NET Core-Befehlszeilenschnittstelle (CLI)](/dotnet/core/tools), um ein neues Projekt vom Typ „ASP.NET Core MVC“ zu erstellen. Die [Azure Cloud Shell](https://shell.azure.com) stellt Ihnen diese Tools zur Verfügung. Sie sind auch auf der Windows-, macOS- und Linux-Plattform verfügbar.

Führen Sie den folgenden Befehl aus, um ein Projekt vom Typ „ASP.NET Core MVC“ in einem neuen Ordner *TestAppConfig* zu erstellen:

```dotnetcli
dotnet new mvc --no-https --output TestAppConfig
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-the-app-configuration-store"></a>Herstellen einer Verbindung mit dem App Configuration-Speicher

1. Führen Sie den folgenden Befehl aus, um einen Verweis auf das NuGet-Paket [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) hinzuzufügen:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Führen Sie den folgenden Befehl im selben Verzeichnis aus wie die *.csproj*-Datei. Der Befehl verwendet den Geheimnis-Manager, um ein Geheimnis namens `ConnectionStrings:AppConfig` zu speichern, das die Verbindungszeichenfolge für Ihren App Configuration-Speicher speichert. Ersetzen Sie den Platzhalterwert `<your_connection_string>` durch die Verbindungszeichenfolge Ihres App Configuration-Speichers. Die Verbindungszeichenfolge finden Sie im Azure-Portal unter **Zugriffsschlüssel**.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    > [!IMPORTANT]
    > Einige Shells kürzen die Verbindungszeichenfolge, sofern Sie nicht in Anführungszeichen eingeschlossen ist. Stellen Sie sicher, dass die Ausgabe des `dotnet user-secrets`-Befehls die gesamte Verbindungszeichenfolge anzeigt. Wenn dies nicht der Fall ist, führen Sie den Befehl erneut aus und schließen die Verbindungszeichenfolge in Anführungszeichen ein.

    Der Geheimnis-Manager wird nur verwendet, um die Web-App lokal zu testen. Nach dem Bereitstellen der App in [Azure App Service](https://azure.microsoft.com/services/app-service/web) verwenden Sie die Anwendungseinstellung **Verbindungszeichenfolgen** in App Service statt des Geheimnis-Managers, um die Verbindungszeichenfolge zu speichern.

    Greifen Sie mithilfe der .NET Core-Konfigurations-API auf dieses Geheimnis zu. Ein Doppelpunkt (`:`) kann im Konfigurationsnamen mit der Konfigurations-API auf allen unterstützten Plattformen verwendet werden. Weitere Informationen finden Sie unter [Konfigurationsschlüssel und -werte](/aspnet/core/fundamentals/configuration#configuration-keys-and-values).

1. Fügen Sie in *Program.cs* einen Verweis auf den Namespace der .NET Core-Konfigurations-API hinzu:

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

1. Aktualisieren Sie die `CreateWebHostBuilder`-Methode für die Verwendung von App Configuration, indem Sie die Methode `AddAzureAppConfiguration` aufrufen.

    > [!IMPORTANT]
    > `CreateHostBuilder` ersetzt `CreateWebHostBuilder` in .NET Core 3.x. Wählen Sie auf der Grundlage ihrer Umgebung die richtige Syntax aus.

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(connection);
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                var connection = settings.GetConnectionString("AppConfig");
                config.AddAzureAppConfiguration(connection);
            })
            .UseStartup<Startup>();
    ```

    ---

    Mit der vorherigen Änderung wurde der [Konfigurationsanbieter für App Configuration](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration) bei der .NET Core-Konfigurations-API registriert.

## <a name="read-from-the-app-configuration-store"></a>Lesen aus dem App Configuration-Speicher

Führen Sie die folgenden Schritte aus, um im App Configuration-Speicher gespeicherte Werte zu lesen und anzuzeigen. Der Zugriff auf den Speicher erfolgt mithilfe der .NET Core-Konfigurations-API. Zum Anzeigen der Werte des Schlüssels wird die Razor-Syntax verwendet.

Öffnen Sie *\<app root>/Views/Home/Index.cshtml*, und ersetzen Sie ihren Inhalt durch den folgenden Code:

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<style>
    body {
        background-color: @Configuration["TestApp:Settings:BackgroundColor"]
    }
    h1 {
        color: @Configuration["TestApp:Settings:FontColor"];
        font-size: @Configuration["TestApp:Settings:FontSize"]px;
    }
</style>

<h1>@Configuration["TestApp:Settings:Message"]</h1>
```

Im vorangehenden Code werden die Schlüssel des App Configuration-Speichers wie folgt verwendet:

* Der Wert des Schlüssels `TestApp:Settings:BackgroundColor` wird der CSS-Eigenschaft `background-color` zugewiesen.
* Der Wert des Schlüssels `TestApp:Settings:FontColor` wird der CSS-Eigenschaft `color` zugewiesen.
* Der Wert des Schlüssels `TestApp:Settings:FontSize` wird der CSS-Eigenschaft `font-size` zugewiesen.
* Der Wert des Schlüssels `TestApp:Settings:Message` wird als Überschrift angezeigt.

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Um die App mit der .NET Core-CLI zu erstellen, navigieren Sie zum Stammverzeichnis Ihres Projekts. Führen Sie den folgenden Befehl in der Befehlsshell aus:

    ```dotnetcli
    dotnet build
    ```

1. Führen Sie nach erfolgreicher Erstellung den folgenden Befehl aus, um die Web-App lokal auszuführen:

    ```dotnetcli
    dotnet run
    ```

1. Wenn Sie auf dem lokalen Computer arbeiten, verwenden Sie einen Browser, um zu `http://localhost:5000` zu navigieren. Diese Adresse ist die Standard-URL für die lokal gehostete Web-App. Wenn Sie in der Azure Cloud Shell arbeiten, wählen Sie die Schaltfläche **Webvorschau** und dann **Konfigurieren** aus.

    ![Position der Schaltfläche „Webvorschau“](./media/quickstarts/cloud-shell-web-preview.png)

    Wenn Sie zum Konfigurieren des Ports für die Vorschau aufgefordert werden, geben Sie *5000* ein, und wählen Sie **Öffnen und durchsuchen** aus. Auf der Webseite ist „Daten aus Azure App Configuration“ zu lesen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung führen Sie die folgenden Schritte aus:

* Bereitstellen eines neuen App Configuration-Speichers.
* Registrieren des .NET Core-Konfigurationsanbieter des App Configuration-Speichers.
* Lesen der Schlüssel des App Configuration-Speichers mit dem Konfigurationsanbieter.
* Anzeigen der Schlüsselwerte des App Configuration-Speichers mithilfe der Razor-Syntax.

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihre ASP.NET Core-App für das dynamische Aktualisieren der Konfigurationseinstellungen konfigurieren.

> [!div class="nextstepaction"]
> [Tutorial: Verwenden der dynamischen Konfiguration in einer .NET Framework-App](./enable-dynamic-configuration-aspnet-core.md)