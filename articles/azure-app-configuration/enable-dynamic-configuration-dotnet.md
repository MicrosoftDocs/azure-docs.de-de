---
title: '.NET Framework-Tutorial: dynamische Konfiguration in Azure App Configuration'
description: In diesem Tutorial wird beschrieben, wie Sie die Konfigurationsdaten für .NET Framework-Apps mithilfe von Azure App Configuration dynamisch aktualisieren.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: alkemper
ms.openlocfilehash: 03940a86176d0bc93c5066977fdc87de5c456060
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932760"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Tutorial: Verwenden der dynamischen Konfiguration in einer .NET Framework-App

Die App Configuration-.NET-Clientbibliothek unterstützt die Aktualisierung einer Reihe von Konfigurationseinstellungen bei Bedarf, ohne dass eine Anwendung neu gestartet werden muss. Dies kann implementiert werden, indem zuerst eine Instanz von `IConfigurationRefresher` aus den Optionen für den Konfigurationsanbieter abgerufen und anschließend `TryRefreshAsync` für diese Instanz an einer beliebigen Stelle Ihres Codes aufgerufen wird.

Um die Einstellungen auf dem aktuellen Stand zu halten und zu viele Aufrufe des Konfigurationsspeichers zu vermeiden, wird ein Cache für jede Einstellung verwendet. Bis der zwischengespeicherte Wert einer Einstellung abgelaufen ist, wird der Wert vom Aktualisierungsvorgang nicht aktualisiert. Dies gilt auch, wenn sich der Wert im Konfigurationsspeicher geändert hat. Die Standardablaufzeit jeder Anforderung beträgt 30 Sekunden, aber dies kann bei Bedarf außer Kraft gesetzt werden.

In diesem Tutorial wird veranschaulicht, wie Sie dynamische Konfigurationsupdates in Ihrem Code implementieren können. Dies baut auf der App auf, die in den Schnellstartanleitungen vorgestellt wurde. Durchlaufen Sie zuerst die Schnellstartanleitung zum [Erstellen einer .NET Framework-App mit Azure App Configuration](./quickstart-dotnet-app.md), bevor Sie fortfahren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten Ihrer .NET Framework-App für die Aktualisierung der Konfiguration als Reaktion auf Änderungen in einem App Configuration-Speicher
> * Einfügen der aktuellen Konfiguration in Ihre Anwendung
## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 oder höher](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Erstellen eines App Configuration-Speichers

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Wählen Sie **Konfigurations-Explorer** >  **+ Erstellen** > **Schlüssel-Wert** aus, um die folgenden Schlüssel-Wert-Paare hinzuzufügen:

    | Schlüssel | Wert |
    |---|---|
    | TestApp:Settings:Message | Daten aus Azure App Configuration |

    Lassen Sie **Bezeichnung** und **Inhaltstyp** vorerst leer.

8. Wählen Sie **Übernehmen**.

## <a name="create-a-net-framework-console-app"></a>Erstellen einer .NET Framework-Konsolen-App

1. Starten Sie Visual Studio, und wählen Sie **Datei** > **Neu** > **Projekt** aus.

1. Filtern Sie unter **Neues Projekt erstellen** nach dem Projekttyp **Konsole**, und klicken Sie auf **Console App (.NET Framework)** (Konsolen-App (.NET Framework)). Klicken Sie auf **Weiter**.

1. Geben Sie unter **Neues Projekt konfigurieren** einen Projektnamen ein. Wählen Sie unter **Framework** die Option **.NET Framework 4.7.1** oder höher aus. Klicken Sie auf **Erstellen**.

## <a name="reload-data-from-app-configuration"></a>Erneutes Laden von Daten aus App Configuration
1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **NuGet-Pakete verwalten** aus. Suchen Sie auf der Registerkarte **Durchsuchen** nach dem NuGet-Paket *Microsoft.Extensions.Configuration.AzureAppConfiguration*, und fügen Sie es Ihrem Projekt hinzu. Wenn Sie es nicht finden können, aktivieren Sie das Kontrollkästchen **Vorabversion einbeziehen**.

1. Öffnen Sie *Program.cs*, und fügen Sie einen Verweis auf den App Configuration-Anbieter für .NET Core hinzu.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Fügen Sie zwei Variablen zum Speichern konfigurationsbezogener Objekte hinzu.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Aktualisieren Sie mit den angegebenen Aktualisierungsoptionen die Methode `Main`, um eine Verbindung mit App Configuration herzustellen.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });

            _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }
    ```
    Die `ConfigureRefresh`-Methode wird genutzt, um die Einstellungen anzugeben, die zum Aktualisieren der Konfigurationsdaten mit dem App Configuration-Speicher verwendet werden, wenn ein Aktualisierungsvorgang ausgelöst wird. Eine Instanz von `IConfigurationRefresher` kann durch das Aufrufen der `GetRefresher`-Methode in den Optionen der `AddAzureAppConfiguration`-Methode abgerufen werden. Die `TryRefreshAsync`-Methode dieser Instanz kann verwendet werden, um einen Aktualisierungsvorgang an einer beliebigen Stelle Ihres Codes auszulösen.

    > [!NOTE]
    > Die Standardablaufzeit für eine Konfigurationseinstellung beträgt 30 Sekunden, aber dieser Wert kann außer Kraft gesetzt werden. Rufen Sie hierzu die `SetCacheExpiration`-Methode im Initialisierer für die Optionen auf, die als Argument an die `ConfigureRefresh`-Methode übergeben wird.

1. Fügen Sie eine Methode namens `PrintMessage()` hinzu, die eine manuelle Aktualisierung der Konfigurationsdaten aus App Configuration auslöst.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Legen Sie eine Umgebungsvariable mit dem Namen **ConnectionString** fest, und geben Sie dafür den Zugriffsschlüssel für Ihren App Configuration-Speicher an. Führen Sie bei Verwendung einer Windows-Eingabeaufforderung den folgenden Befehl aus, und starten Sie die Eingabeaufforderung neu, damit die Änderung wirksam wird:

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Führen Sie bei Verwendung von Windows PowerShell den folgenden Befehl aus:

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Starten Sie Visual Studio, damit die Änderung wirksam wird. 

1. Drücken Sie STRG+F5, um die Konsolen-App zu erstellen und auszuführen.

    ![Lokaler App-Start](./media/dotnet-app-run.png)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Klicken Sie auf **Alle Ressourcen**, und wählen Sie dann die Instanz des App Configuration-Speichers aus, die Sie in der Schnellstartanleitung erstellt haben.

1. Wählen Sie den **Konfigurations-Explorer** aus, und aktualisieren Sie die Werte der folgenden Schlüssel:

    | Schlüssel | Wert |
    |---|---|
    | TestApp:Settings:Message | Daten aus Azure App Configuration: Aktualisiert |

1. Drücken Sie in der ausgeführten Anwendung die EINGABETASTE, um eine Aktualisierung auszulösen, und geben Sie den aktualisierten Wert in der Eingabeaufforderung oder im PowerShell-Fenster aus.

    ![Lokale App-Aktualisierung](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Da die Cacheablaufzeit beim Angeben der Konfiguration für den Aktualisierungsvorgang mit der `SetCacheExpiration`-Methode auf zehn Sekunden festgelegt wurde, wird der Wert für die Konfigurationseinstellung erst aktualisiert, wenn seit der letzten Aktualisierung für diese Einstellung mindestens zehn Sekunden verstrichen sind.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Ihre .NET Framework-App aktiviert, um Konfigurationseinstellungen dynamisch aus App Configuration zu aktualisieren. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie eine von Azure verwaltete Identität hinzufügen, um den Zugriff auf App Configuration zu optimieren.

> [!div class="nextstepaction"]
> [Integration der verwalteten Identität](./howto-integrate-azure-managed-service-identity.md)
