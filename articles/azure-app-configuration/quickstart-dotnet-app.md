---
title: Schnellstart für Azure App Configuration mit .NET Framework | Microsoft-Dokumentation
description: Enthält eine Schnellstartanleitung für die Verwendung von Azure App Configuration mit .NET Framework-Apps.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: f5837e06f347a4f822ac007dfe54c99fc7457dbb
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872977"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Schnellstart: Erstellen einer .NET Framework-App mit Azure App Configuration

Azure App Configuration ist ein verwalteter Konfigurationsdienst in Azure. Mit diesem Dienst können Sie Ihre gesamten Anwendungseinstellungen komfortabel an einem zentralen Ort speichern und verwalten, der von Ihrem Code getrennt ist. In dieser Schnellstartanleitung wird veranschaulicht, wie Sie den Dienst in eine .NET Framework-basierte Windows-Desktop-Konsolen-App einbinden.

![Schnellstart – Lokale Durchführung](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie zum Durchführen dieser Schnellstartanleitung [Visual Studio 2019](https://visualstudio.microsoft.com/vs) und [.NET Framework 4.7.1](https://dotnet.microsoft.com/download) oder höher, falls Sie dies noch nicht getan haben.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Erstellen eines App-Konfigurationsspeichers

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wählen Sie **Konfigurations-Explorer** >  **+ Erstellen** aus, um die folgenden Schlüssel-Wert-Paare hinzuzufügen:

    | Schlüssel | Wert |
    |---|---|
    | TestApp:Settings:Message | Daten aus Azure App Configuration |

    Lassen Sie **Bezeichnung** und **Inhaltstyp** vorerst leer.

## <a name="create-a-net-console-app"></a>Erstellen einer .NET-Konsolen-App

1. Starten Sie Visual Studio, und wählen Sie **Datei** > **Neu** > **Projekt** aus.

2. Wählen Sie in **Neues Projekt** **Installiert** > **Visual C#**  > **Windows-Desktop** aus. Wählen Sie **Konsolen-App (.NET Framework)** aus, und geben Sie einen Namen für Ihr Projekt ein. Wählen Sie **.NET Framework 4.7.1** oder höher aus, und wählen Sie **OK** aus.

## <a name="connect-to-an-app-configuration-store"></a>Herstellen einer Verbindung mit einem App-Konfigurationsspeicher

1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **NuGet-Pakete verwalten** aus. Suchen Sie auf der Registerkarte **Durchsuchen** die folgenden NuGet-Pakete, und fügen Sie sie Ihrem Projekt hinzu. Wenn Sie sie nicht finden können, aktivieren Sie das Kontrollkästchen **Vorabversion einbeziehen**.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. Aktualisieren Sie die Datei *App.config* Ihres Projekts wie folgt:

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   Die Verbindungszeichenfolge Ihres App-Konfigurationsspeichers wird aus der Umgebungsvariablen `ConnectionString` gelesen. Fügen Sie den Konfigurations-Generator `Environment` vor `MyConfigStore` in der Eigenschaft `configBuilders` des Abschnitts `appSettings` ein.

3. Öffnen Sie die Datei *Program.cs*, und aktualisieren Sie die `Main`-Methode für die Verwendung von App Configuration, indem Sie `ConfigurationManager` aufrufen.

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Legen Sie eine Umgebungsvariable mit dem Namen **ConnectionString** auf die Verbindungszeichenfolge Ihres App-Konfigurationsspeichers fest. Führen Sie bei Verwendung der Windows-Eingabeaufforderung den folgenden Befehl aus:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Führen Sie bei Verwendung der Windows PowerShell den folgenden Befehl aus:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Starten Sie Visual Studio, damit die Änderung wirksam wird. Drücken Sie STRG+F5, um die Konsolen-App zu erstellen und auszuführen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen neuen App-Konfigurationsspeicher erstellt und mit einer .NET Framework-Konsolen-App verwendet. Weitere Informationen zur Verwendung von App Configuration finden Sie im nächsten Tutorial, in dem es um die Authentifizierung geht.

> [!div class="nextstepaction"]
> [Integration der verwalteten Identität](./howto-integrate-azure-managed-service-identity.md)
