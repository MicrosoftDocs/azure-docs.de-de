---
title: Schnellstart für Azure App Configuration mit .NET Core | Microsoft-Dokumentation
description: In dieser Schnellanleitung erstellen Sie mit Azure App Configuration eine .NET Core-App, um die Speicherung und Verwaltung von Anwendungseinstellungen getrennt von Ihrem Code zu zentralisieren.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 1a5e756e05543d9f19775f4e2d1abcee82794f9e
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932063"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Schnellstart: Erstellen einer .NET Core-App mit App Configuration

In dieser Schnellanleitung integrieren Sie Azure App Configuration in eine .NET Core-Konsolen-App, um die Speicherung und Verwaltung von Anwendungseinstellungen getrennt von Ihrem Code zu zentralisieren.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/dotnet)
- [.NET Core SDK](https://dotnet.microsoft.com/download) – auch in [Azure Cloud Shell](https://shell.azure.com) verfügbar.

## <a name="create-an-app-configuration-store"></a>Erstellen eines App Configuration-Speichers

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Wählen Sie **Konfigurations-Explorer** > **Erstellen** > **Schlüssel-Wert** aus, um die folgenden Schlüssel-Wert-Paare hinzuzufügen:

    | Schlüssel | Wert |
    |---|---|
    | TestApp:Settings:Message | Daten aus Azure App Configuration |

    Lassen Sie **Bezeichnung** und **Inhaltstyp** vorerst leer.

8. Wählen Sie **Übernehmen**.

## <a name="create-a-net-core-console-app"></a>Erstellen einer .NET Core-Konsolen-App

Sie verwenden die [.NET Core-Befehlszeilenschnittstelle (CLI)](/dotnet/core/tools/), um ein neues Projekt für eine .NET Core-Konsolen-App zu erstellen. Der Vorteil bei Verwendung der .NET Core-CLI gegenüber Visual Studio ist, dass sie für alle Windows-, macOS- und Linux-Plattformen verfügbar ist.  Verwenden Sie alternativ die vorinstallierten Tools, die in [Azure Cloud Shell](https://shell.azure.com) verfügbar sind.

1. Erstellen Sie einen neuen Ordner für Ihr Projekt.

2. Führen Sie im neuen Ordner den folgenden Befehl aus, um ein neues Konsolen-App-Projekt vom Typ „.NET Core“ zu erstellen:

    ```dotnetcli
    dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>Herstellen einer Verbindung mit einem App Configuration-Speicher

1. Fügen Sie einen Verweis auf das NuGet-Paket `Microsoft.Extensions.Configuration.AzureAppConfiguration` hinzu, indem Sie den folgenden Befehl ausführen:

    ```dotnetcli
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration
    ```

2. Führen Sie den folgenden Befehl aus, um Pakete für Ihr Projekt wiederherzustellen:

    ```dotnetcli
    dotnet restore
    ```

3. Öffnen Sie *Program.cs*, und fügen Sie einen Verweis auf den App Configuration-Anbieter für .NET Core hinzu.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Aktualisieren Sie die `Main`-Methode für die Verwendung von App Configuration, indem Sie die Methode `builder.AddAzureAppConfiguration()` aufrufen.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Legen Sie eine Umgebungsvariable mit dem Namen **ConnectionString** fest, und geben Sie dafür den Zugriffsschlüssel für Ihren App Configuration-Speicher an. Führen Sie an der Befehlszeile den folgenden Befehl aus:

    ```cmd
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Führen Sie bei Verwendung von Windows PowerShell den folgenden Befehl aus:

    ```azurepowershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Führen Sie bei Verwendung von macOS oder Linux den folgenden Befehl aus:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

    Starten Sie die Eingabeaufforderung neu, damit die Änderung wirksam wird. Geben Sie den Wert der Umgebungsvariablen aus, um zu überprüfen, ob er richtig festgelegt wurde.

2. Führen Sie den folgenden Befehl aus, um die Konsolen-App zu erstellen:

    ```dotnetcli
    dotnet build
    ```

3. Führen Sie nach der erfolgreichen Erstellung den folgenden Befehl aus, um die App lokal auszuführen:

    ```dotnetcli
    dotnet run
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie über den [Anbieter App Configuration](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration) einen neuen App Configuration-Speicher erstellt und mit einer .NET Core-Konsolen-App verwendet. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihre .NET Core-App für das dynamische Aktualisieren der Konfigurationseinstellungen konfigurieren.

> [!div class="nextstepaction"]
> [Tutorial: Verwenden der dynamischen Konfiguration in einer .NET Framework-App](./enable-dynamic-configuration-dotnet-core.md)