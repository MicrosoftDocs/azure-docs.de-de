---
title: Integrieren von Azure App Configuration mit einer Continuous Integration- und Continuous Delivery-Pipeline
description: Hier erfahren Sie, wie Sie Continuous Integration und Continuous Delivery mithilfe von Azure App Configuration implementieren.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.date: 04/19/2020
ms.author: alkemper
ms.openlocfilehash: d076bdf09626ec9ed08fcf43b95fc63d2f4a7dd7
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928448"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrieren in eine CI/CD-Pipeline

In diesem Artikel wird erläutert, wie Sie Daten aus Azure App Configuration in einem Continuous Integration- und Continuous Deployment-System verwenden.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Verwenden von App Configuration in Ihrer Azure DevOps-Pipeline

Wenn Sie eine Azure DevOps-Pipeline besitzen, können Sie Schlüsselwerte aus App Configuration abrufen und als Taskvariablen festlegen. Die [Azure App Configuration-DevOps-Erweiterung](https://go.microsoft.com/fwlink/?linkid=2091063) ist ein Add-On-Modul, das diese Funktionalität bereitstellt. Befolgen Sie die Anweisungen zur Verwendung der Erweiterung in einer Build- oder Releasetasksequenz.

## <a name="deploy-app-configuration-data-with-your-application"></a>Bereitstellen von App Configuration-Daten mit Ihrer Anwendung

Ihre Anwendung kann unter Umständen nicht ausgeführt werden, wenn sie von Azure App Configuration abhängig ist, den Dienst aber nicht erreicht. Erhöhen Sie die Resilienz Ihrer Anwendung, indem Sie Konfigurationsdaten in eine Datei packen, die mit der Anwendung bereitgestellt und beim Starten der Anwendung lokal geladen wird. Dadurch wird sichergestellt, dass Ihre Anwendung beim Start über Standardeinstellungen verfügt. Diese Werte werden durch neuere Änderungen in einem App Configuration-Speicher überschrieben (sofern verfügbar).

Mithilfe der Azure App Configuration-Funktion [Exportieren](./howto-import-export-data.md#export-data) können Sie das Abrufen aktueller Konfigurationsdaten als einzelne Datei automatisieren. Sie können diese Datei anschließend in einen Build- oder Bereitstellungsschritt in Ihrer CI/CD-Pipeline (Continuous Integration/Continuous Deployment) einbetten.

Das folgende Beispiel zeigt, wie Sie App Configuration-Daten als Buildschritt für die in den Schnellstartanleitungen eingeführte Web-App integrieren. Durchlaufen Sie zuerst die Schnellstartanleitung zum [Erstellen einer ASP.NET Core-App mit Azure App Configuration](./quickstart-aspnet-core-app.md), bevor Sie fortfahren.

Für die Ausführung der Schritte dieses Tutorials können Sie einen beliebigen Code-Editor verwenden. [Visual Studio Code](https://code.visualstudio.com/) ist eine hervorragende Option, die auf Windows-, macOS- und Linux-Plattformen verfügbar ist.

### <a name="prerequisites"></a>Voraussetzungen

Wenn Sie die App lokal erstellen möchten, laden Sie die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli?view=azure-cli-latest) herunter, und installieren Sie sie (sofern noch nicht geschehen).

Für einen Cloud-Build (etwa mit Azure DevOps) muss die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli?view=azure-cli-latest) auf Ihrem Buildsystem installiert sein.

### <a name="export-an-app-configuration-store"></a>Exportieren eines App Configuration-Speichers

1. Öffnen Sie Ihre *CSPROJ-Datei*, und fügen Sie das folgende Skript hinzu:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```
1. Öffnen Sie *Program.cs*, und aktualisieren Sie die Methode `CreateWebHostBuilder` für die Verwendung der exportierten JSON-Datei durch Aufrufen der Methode `config.AddJsonFile()`.  Fügen Sie auch den Namespace `System.Reflection` hinzu.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

### <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Legen Sie eine Umgebungsvariable mit dem Namen **ConnectionString** fest, und geben Sie dafür den Zugriffsschlüssel für Ihren App Configuration-Speicher an. 
    Führen Sie bei Verwendung einer Windows-Eingabeaufforderung den folgenden Befehl aus, und starten Sie die Eingabeaufforderung neu, damit die Änderung wirksam wird:

    ```console
     setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Führen Sie bei Verwendung von Windows PowerShell den folgenden Befehl aus:

    ```powershell
     $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Führen Sie bei Verwendung von macOS oder Linux den folgenden Befehl aus:

    ```console
     export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Führen Sie den folgenden Befehl in der Befehlsshell aus, um die App mithilfe der .NET Core-CLI zu erstellen:

    ```console
     dotnet build
    ```

3. Führen Sie nach erfolgreicher Erstellung den folgenden Befehl aus, um die Web-App lokal auszuführen:

    ```console
     dotnet run
    ```

4. Öffnen Sie ein Browserfenster, und navigieren Sie zu `http://localhost:5000`. Dies ist die Standard-URL für die lokal gehostete Web-App.

    ![Schnellstartanleitung: Lokales Starten der App](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie in einer Bereitstellungspipeline zu verwendende Azure App Configuration-Daten exportiert. Fahren Sie mit den Azure CLI-Beispielen fort, um mehr über die Verwendung von App Configuration zu erfahren.

> [!div class="nextstepaction"]
> [Azure-Befehlszeilenschnittstelle](/cli/azure/appconfig?view=azure-cli-latest)