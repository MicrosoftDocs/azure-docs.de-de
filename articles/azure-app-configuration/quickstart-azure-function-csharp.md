---
title: Schnellstart für Azure App Configuration mit Azure Functions | Microsoft-Dokumentation
description: Enthält eine Schnellstartanleitung für die Verwendung von Azure App Configuration mit Azure Functions.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: Azure Functions
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: c09cb0f93f05a9574543ebabb398148638092c73
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864794"
---
# <a name="quickstart-create-an-azure-function-with-app-configuration"></a>Schnellstart: Erstellen einer Azure-Funktion mit App Configuration

Azure App Configuration ist ein verwalteter Konfigurationsdienst in Azure. Mit diesem Dienst können Sie Ihre gesamten Anwendungseinstellungen komfortabel an einem zentralen Ort speichern und verwalten, der von Ihrem Code getrennt ist. In dieser Schnellstartanleitung wird veranschaulicht, wie Sie den Dienst in eine Azure-Funktion einbinden. 

Für die Ausführung der Schritte dieser Schnellstartanleitung können Sie einen beliebigen Code-Editor verwenden. [Visual Studio Code](https://code.visualstudio.com/) ist eine hervorragende Option, die auf Windows-, macOS- und Linux-Plattformen verfügbar ist.

![Schnellstart – Lokale Durchführung](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie [Visual Studio 2019](https://visualstudio.microsoft.com/vs), um diese Schnellstartanleitung auszuführen. Vergewissern Sie sich, dass auch die Workload **Azure-Entwicklung** installiert ist. Installieren Sie außerdem die [neuesten Azure Functions-Tools](../azure-functions/functions-develop-vs.md#check-your-tools-version).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Erstellen eines App-Konfigurationsspeichers

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wählen Sie **Konfigurations-Explorer** >  **+ Erstellen** aus, um die folgenden Schlüssel-Wert-Paare hinzuzufügen:

    | Schlüssel | Wert |
    |---|---|
    | TestApp:Settings:Message | Daten aus Azure App Configuration |

    Lassen Sie **Bezeichnung** und **Inhaltstyp** vorerst leer.

## <a name="create-a-function-app"></a>Erstellen einer Funktionen-App

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Herstellen einer Verbindung mit einem App-Konfigurationsspeicher

1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **NuGet-Pakete verwalten** aus. Suchen Sie auf der Registerkarte **Durchsuchen** die folgenden NuGet-Pakete, und fügen Sie sie Ihrem Projekt hinzu. Wenn Sie sie nicht finden können, aktivieren Sie das Kontrollkästchen **Vorabversion einbeziehen**.

    ```
    Microsoft.Extensions.Configuration.AzureAppConfiguration 1.0.0 preview or later
    ```

2. Öffnen Sie *Function1.cs*, und fügen Sie einen Verweis auf den App Configuration-Anbieter für .NET Core hinzu.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Aktualisieren Sie die `Run`-Methode für die Verwendung von App Configuration, indem Sie `builder.AddAzureAppConfiguration()` aufrufen.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        var config = builder.Build();
        string message = config["TestApp:Settings:Message"];
        message = message ?? req.Query["message"];

        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
        message = message ?? data?.message;

        return message != null
            ? (ActionResult) new OkObjectResult(message)
            : new BadRequestObjectResult("Please pass a message from a configuration store, on the query string or in the request body");
    }
    ```

## <a name="test-the-function-locally"></a>Lokales Testen der Funktion

1. Legen Sie eine Umgebungsvariable mit dem Namen **ConnectionString** fest, und geben Sie dafür den Zugriffsschlüssel für Ihren App-Konfigurationsspeicher an. Führen Sie bei Verwendung einer Windows-Eingabeaufforderung den folgenden Befehl aus, und starten Sie die Eingabeaufforderung neu, damit die Änderung wirksam wird:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Führen Sie bei Verwendung von Windows PowerShell den folgenden Befehl aus:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Führen Sie bei Verwendung von macOS oder Linux den folgenden Befehl aus:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Drücken Sie F5, um Ihre Funktion zu testen. Akzeptieren Sie die entsprechende Aufforderung von Visual Studio zum Herunterladen und Installieren der **Azure Functions Core (CLI)** -Tools. Sie müssen möglicherweise auch eine Firewallausnahme aktivieren, damit die Tools HTTP-Anforderungen verarbeiten können.

3. Kopieren Sie die URL Ihrer Funktion aus der Azure Functions-Laufzeitausgabe.

    ![Schnellstart: Debuggen von Funktionen in VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Fügen Sie die URL der HTTP-Anforderung in die Adresszeile des Browsers ein. In der folgenden Abbildung sehen Sie die Antwort des Browsers auf die von der Funktion zurückgegebene lokale GET-Anforderung.

    ![Schnellstart: Lokales Starten von Funktionen](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen neuen App-Konfigurationsspeicher erstellt und mit einer Azure-Funktion verwendet. Weitere Informationen zur Verwendung von App Configuration finden Sie im nächsten Tutorial, in dem es um die Authentifizierung geht.

> [!div class="nextstepaction"]
> [Integration der verwalteten Identität](./howto-integrate-azure-managed-service-identity.md)
