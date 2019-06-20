---
title: Schnellstartanleitung zum Hinzufügen von Featureflags zu ASP.NET Core | Microsoft-Dokumentation
description: In dieser Schnellstartanleitung wird erläutert, wie Sie ASP.NET Core-Apps Featureflags hinzufügen und diese in Azure App Configuration verwalten.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: f83283dbf2a78b3717ba719f21dd9249a8a09d1f
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393289"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Schnellstart: Hinzufügen von Featureflags zu einer ASP.NET Core-App

Sie können die Featureverwaltung in ASP.NET Core aktivieren, indem Sie Ihre Anwendung mit Azure App Configuration verbinden. Mit diesem verwalteten Dienst können Sie alle Featureflags speichern und ihren jeweiligen Zustand zentral steuern. In dieser Schnellstartanleitung erfahren Sie, wie Sie App Configuration in eine ASP.NET Core-Web-App integrieren, um eine End-to-End-Implementierung der Featureverwaltung zu erzielen.

Die .NET Core-Bibliotheken für die Featureverwaltung erweitern das Framework um umfassende Unterstützung für Featureflags. Diese Bibliotheken bauen auf dem .NET Core-Konfigurationssystem auf. Über ihren .NET Core-Konfigurationsanbieter lassen sie sich nahtlos in App Configuration integrieren.

Die Schritte in dieser Schnellstartanleitung können mit einem beliebigen Code-Editor ausgeführt werden. [Visual Studio Code](https://code.visualstudio.com/) ist eine hervorragende Option, die auf Windows-, macOS- und Linux-Plattformen verfügbar ist.

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie das [.NET Core SDK](https://dotnet.microsoft.com/download), um diesen Schnellstart durchführen zu können.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Erstellen eines App Configuration-Speichers

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wählen Sie **Feature-Manager** >  **+Erstellen** aus, um die folgenden Featureflags hinzuzufügen:

    | Schlüssel | Zustand |
    |---|---|
    | Beta | Aus |

## <a name="create-an-aspnet-core-web-app"></a>Erstellen einer ASP.NET Core-Web-App

Sie verwenden die [.NET Core-Befehlszeilenschnittstelle (CLI)](https://docs.microsoft.com/dotnet/core/tools/), um ein neues Projekt vom Typ „ASP.NET Core MVC-Web-App“ zu erstellen. Die .NET Core-CLI bietet gegenüber Visual Studio den Vorteil, dass sie für alle Windows-, macOS- und Linux-Plattformen verfügbar ist.

1. Erstellen Sie einen neuen Ordner für Ihr Projekt. Verwenden Sie für diese Schnellstartanleitung den Namen *TestFeatureFlags*.

1. Führen Sie im neuen Ordner den folgenden Befehl aus, um ein neues Projekt vom Typ „ASP.NET Core MVC-Web-App“ zu erstellen:

   ```    
   dotnet new mvc
   ```

## <a name="add-secret-manager"></a>Hinzufügen des Geheimnis-Managers

Fügen Sie Ihrem Projekt das [Geheimnis-Manager-Tool](https://docs.microsoft.com/aspnet/core/security/app-secrets) hinzu. Im Geheimnis-Manager-Tool werden sensible Daten für die Entwicklungsarbeit außerhalb Ihrer Projektstruktur gespeichert. Mit diesem Ansatz können Sie verhindern, dass App-Geheimnisse versehentlich im Quellcode angegeben werden.

1. Öffnen Sie die Datei *.csproj*.
1. Fügen Sie, wie im folgenden Beispiel gezeigt, ein `UserSecretsId`-Element hinzu, und ersetzen Sie dessen Wert durch Ihren eigenen Wert – in der Regel eine GUID:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
        <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
    </ItemGroup>

    </Project>
    ```

1. Speichern Sie die Datei .

## <a name="connect-to-an-app-configuration-store"></a>Herstellen einer Verbindung mit einem App Configuration-Speicher

1. Führen Sie die folgenden Befehle aus, um Verweise auf die NuGet-Pakete `Microsoft.Extensions.Configuration.AzureAppConfiguration` und `Microsoft.FeatureManagement` hinzuzufügen:

    ```
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 1.0.0-preview-008520001

    dotnet add package Microsoft.FeatureManagement.AspNetCore --version 1.0.0-preview-008560001-910
    ```

1. Führen Sie den folgenden Befehl aus, um Pakete für Ihr Projekt wiederherzustellen:

    ```
    dotnet restore
    ```

1. Fügen Sie dem Geheimnis-Manager ein Geheimnis mit dem Namen **ConnectionStrings:AppConfig** hinzu.

    Dieses Geheimnis enthält die Verbindungszeichenfolge für den Zugriff auf Ihren App Configuration-Speicher. Ersetzen Sie den Wert `<your_connection_string>` im folgenden Befehl durch die Verbindungszeichenfolge für Ihren App Configuration-Speicher.

    Dieser Befehl muss in dem Verzeichnis ausgeführt werden, in dem die *.csproj*-Datei enthalten ist.

    ```
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Sie verwenden den Geheimnis-Manager nur zum lokalen Testen der Web-App. Wenn Sie die App z.B. in [Azure App Service](https://azure.microsoft.com/services/app-service) bereitstellen, verwenden Sie statt des Geheimnis-Managers eine Anwendungseinstellung namens **Verbindungszeichenfolgen** in App Service, um die Verbindungszeichenfolge zu speichern.

    Sie können mit der App Configuration-API auf dieses Geheimnis zugreifen. Ein Doppelpunkt (:) funktioniert bei der App Configuration-API im Konfigurationsnamen auf allen unterstützten Plattformen. Siehe [Konfiguration nach Umgebung](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. Öffnen Sie *Program.cs*, und fügen Sie einen Verweis auf den App Configuration-Anbieter für .NET Core hinzu:

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Aktualisieren Sie die `CreateWebHostBuilder`-Methode für die Verwendung von App Configuration, indem Sie die Methode `config.AddAzureAppConfiguration()` aufrufen.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .UseFeatureFlags();
                });
            })
            .UseStartup<Startup>();
    ```

1. Öffnen Sie *Startup.cs*, und fügen Sie Verweise auf den .NET Core-Feature-Manager hinzu:

    ```csharp
    using Microsoft.FeatureManagement.AspNetCore;
    ```

1. Aktualisieren Sie die `ConfigureServices`-Methode, und fügen Sie Unterstützung für Featureflags hinzu, indem Sie die `services.AddFeatureManagement()`-Methode aufrufen. Optional können Sie durch Aufrufen von `services.AddFeatureFilter<FilterType>()` auch Filter einschließen, die mit Featureflags verwendet werden sollen:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
    ```

1. Fügen Sie eine *MyFeatureFlags.cs*-Datei hinzu:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Fügen Sie dem Verzeichnis *Controllers* die Datei *BetaController.cs* hinzu:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement.AspNetCore;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager)
            {
                _featureManager = featureManager;
            }

            [Feature(MyFeatureFlags.Beta)]
            public IActionResult Index()
            {
                return View();
            }
        }
    }
    ```

1. Öffnen Sie *_ViewImports.cshtml* im Verzeichnis *Views*, und fügen Sie das Taghilfsprogramm für den Feature-Manager hinzu:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Öffnen Sie die Datei *_Layout.cshtml*“ im Verzeichnis *Views*\\*Shared*, und ersetzen Sie den `<nav>`-Code für die Navigationsleiste unter `<body>` > `<header>` durch den folgenden Code:

    ```html
    <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
        <div class="container">
            <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">TestFeatureFlags</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target=".navbar-collapse" aria-controls="navbarSupportedContent"
            aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="navbar-collapse collapse d-sm-inline-flex flex-sm-row-reverse">
                <ul class="navbar-nav flex-grow-1">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Index">Home</a>
                    </li>
                    <feature name="Beta">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Beta" asp-action="Index">Beta</a>
                    </li>
                    </feature>
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    ```

1. Erstellen Sie unter *Views* das Verzeichnis *Beta*, und fügen Sie diesem Verzeichnis die Datei *Index.cshtml* hinzu:

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Führen Sie den folgenden Befehl in der Befehlsshell aus, um die App mithilfe der .NET Core-CLI zu erstellen:

    ```
    dotnet build
    ```

1. Führen Sie nach der erfolgreichen Erstellung den folgenden Befehl aus, um die Web-App lokal auszuführen:

    ```
    dotnet run
    ```

1. Öffnen Sie ein Browserfenster, und navigieren Sie zu `https://localhost:5001`. Dies ist die Standard-URL für die lokal gehostete Web-App.

    ![Schnellstartanleitung: Lokales Starten der App](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Klicken Sie auf **Alle Ressourcen**, und wählen Sie dann die Instanz des App Configuration-Speichers aus, die Sie in der Schnellstartanleitung erstellt haben.

1. Wählen Sie **Feature-Manager** aus, und ändern Sie den Status des **Beta**-Schlüssels zu **Ein**:

    | Schlüssel | Zustand |
    |---|---|
    | Beta | Andererseits |

1. Aktualisieren Sie die Browserseite, um die neuen Konfigurationseinstellungen anzuzeigen.

    ![Schnellstartanleitung: Lokales Starten der App](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen neuen App Configuration-Speicher erstellt und diesen zur Verwaltung von Features in einer ASP.NET Core-Web-App über die [Feature-Manager-Bibliotheken](https://go.microsoft.com/fwlink/?linkid=2074664) verwendet.

- Weitere Informationen über die [Featureverwaltung](./concept-feature-management.md)
- [Verwalten von Featureflags](./manage-feature-flags.md)
- [Verwenden von Featureflags in einer ASP.NET Core-App](./use-feature-flags-dotnet-core.md)
