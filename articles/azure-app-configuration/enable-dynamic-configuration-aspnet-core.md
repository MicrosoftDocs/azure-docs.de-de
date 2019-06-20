---
title: 'Tutorial: Verwenden der dynamischen Konfiguration von Azure App Configuration in einer ASP.NET Core-App | Microsoft-Dokumentation'
description: In diesem Tutorial wird beschrieben, wie Sie die Konfigurationsdaten für ASP.NET Core-Apps dynamisch aktualisieren.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 9cbdfe957587977b01bc46b46818856f789f46d8
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393613"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Tutorial: Verwenden der dynamischen Konfiguration in einer ASP.NET Core-App

ASP.NET Core verfügt über ein austauschbares Konfigurationssystem, die Konfigurationsdaten aus vielen verschiedenen Quellen lesen kann. Änderungen können nebenbei verarbeitet werden, ohne dass eine Anwendung neu gestartet werden muss. ASP.NET Core unterstützt die Bindung von Konfigurationseinstellungen an stark typisierte .NET-Klassen. Sie werden mithilfe der verschiedenen `IOptions<T>`-Muster in Ihren Code eingefügt. Eines dieser Muster (`IOptionsSnapshot<T>`) lädt die Anwendungskonfiguration automatisch neu, wenn die zugrunde liegenden Daten geändert werden.

Sie können `IOptionsSnapshot<T>` in Controller in Ihrer Anwendung einfügen, um auf die aktuellste Konfiguration zuzugreifen, die in Azure App Configuration gespeichert ist. Sie können auch die ASP.NET Core-Clientbibliothek von App Configuration einrichten, um alle Änderungen eines App-Konfigurationsspeichers kontinuierlich zu überwachen und abzurufen. Sie definieren das periodische Abrufintervall.

In diesem Tutorial wird veranschaulicht, wie Sie dynamische Konfigurationsupdates in Ihrem Code implementieren können. Dies baut auf der Web-App auf, die in den Schnellstartanleitungen vorgestellt wurde. Führen Sie zuerst den Schnellstart [Erstellen einer ASP.NET Core-App mit Azure App Configuration](./quickstart-aspnet-core-app.md) durch, bevor Sie fortfahren.

Für die Ausführung der Schritte dieses Tutorials können Sie einen beliebigen Code-Editor verwenden. [Visual Studio Code](https://code.visualstudio.com/) ist eine hervorragende Option, die auf Windows-, macOS- und Linux-Plattformen verfügbar ist.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten Ihrer Anwendung für die Aktualisierung der Konfiguration als Reaktion auf Änderungen in einem App-Konfigurationsspeicher
> * Einfügen der aktuellen Konfiguration in die Controller Ihrer Anwendung

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie für dieses Tutorial das [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Erneutes Laden von Daten aus App Configuration

1. Öffnen Sie die Datei *Program.cs*, und aktualisieren Sie die `CreateWebHostBuilder`-Methode, indem Sie die `config.AddAzureAppConfiguration()`-Methode hinzufügen.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .Watch("TestApp:Settings:BackgroundColor")
                           .Watch("TestApp:Settings:FontColor")
                           .Watch("TestApp:Settings:Message"));
            })
            .UseStartup<Startup>();
    ```

    Der zweite Parameter in der `.Watch`-Methode ist das Abrufintervall, in dem die ASP.NET-Clientbibliothek einen App-Konfigurationsspeicher abfragt. Die Clientbibliothek überprüft die spezifische Konfigurationseinstellung, um festzustellen, ob Änderungen vorgenommen wurden.
    
    > [!NOTE]
    > Das Standardabrufintervall für die Erweiterungsmethode `Watch` beträgt 30 Sekunden, wenn kein anderes Intervall angegeben wurde.

2. Fügen Sie die Datei *Settings.cs* hinzu, mit der eine neue `Settings`-Klasse definiert und implementiert wird.

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. Öffnen Sie die Datei *Startup.cs*, und aktualisieren Sie die `ConfigureServices`-Methode, um Konfigurationsdaten an die `Settings`-Klasse zu binden.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

## <a name="use-the-latest-configuration-data"></a>Verwenden der aktuellsten Konfigurationsdaten

1. Öffnen Sie *HomeController.cs* im Verzeichnis „Controllers“, und fügen Sie einen Verweis auf das Paket `Microsoft.Extensions.Options` hinzu.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Aktualisieren Sie die `HomeController`-Klasse, um `Settings` per Abhängigkeitsinjektion zu erhalten, und verwenden Sie die darin enthaltenen Werte.

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

3. Öffnen Sie im Verzeichnis „Views > Home“ die Datei *Index.cshtml*, und ersetzen Sie den Inhalt durch das folgende Skript:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Führen Sie den folgenden Befehl in der Befehlsshell aus, um die App mithilfe der .NET Core-CLI zu erstellen:

        dotnet build

2. Führen Sie nach der erfolgreichen Erstellung den folgenden Befehl aus, um die Web-App lokal auszuführen:

        dotnet run

3. Öffnen Sie ein Browserfenster, und navigieren Sie zu `http://localhost:5000`. Dies ist die Standard-URL für die lokal gehostete Web-App.

    ![Schnellstartanleitung: Lokales Starten der App](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Alle Ressourcen** aus, und wählen Sie dann die Instanz des App-Konfigurationsspeichers aus, die Sie in der Schnellstartanleitung erstellt haben.

5. Wählen Sie den **Konfigurations-Explorer** aus, und aktualisieren Sie die Werte der folgenden Schlüssel:

    | Schlüssel | Wert |
    |---|---|
    | TestAppSettings:BackgroundColor | green |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Daten aus Azure App Configuration – jetzt mit Liveupdates! |

6. Aktualisieren Sie die Browserseite, um die neuen Konfigurationseinstellungen anzuzeigen.

    ![Schnellstart: Lokale Aktualisierung der App](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine verwaltete Azure-Dienstidentität hinzugefügt, um den Zugriff auf App Configuration zu optimieren und die Verwaltung der Anmeldeinformationen für Ihre App zu verbessern. Fahren Sie mit den Azure CLI-Beispielen fort, um mehr über die Verwendung von App Configuration zu erfahren.

> [!div class="nextstepaction"]
> [CLI-Beispiele](./cli-samples.md)
