---
title: 'Tutorial: Verwenden der Key Vault-Verweise von Azure App Configuration in einer ASP.NET Core-App | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie die Key Vault-Verweise von Azure App Configuration in einer ASP.NET Core-App verwenden.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 94bab9506d2bf7c29f997bcbfd400a412d5ac041
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932233"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Tutorial: Verwenden von Key Vault-Verweisen in einer ASP.NET Core-App

In diesem Tutorial erfahren Sie, wie Sie den Azure App Configuration-Dienst zusammen mit Azure Key Vault verwenden. Die Dienste App Configuration und Key Vault ergänzen sich gegenseitig und werden bei den meisten Anwendungsbereitstellungen gemeinsam eingesetzt.

App Configuration unterstützt Sie beim parallelen Verwenden der Dienste, indem Schlüssel erstellt werden, mit denen auf in Key Vault gespeicherte Werte verwiesen wird. Wenn mit App Configuration Schlüssel dieser Art erstellt werden, werden nicht die eigentlichen Werte, sondern die URIs der Key Vault-Werte gespeichert.

Ihre Anwendung nutzt den App Configuration-Clientanbieter, um Key Vault-Verweise abzurufen, wie dies auch für alle anderen Schlüssel der Fall ist, die in App Configuration gespeichert sind. In diesem Fall sind die in App Configuration gespeicherten Werte URIs, mit denen auf die Werte in Key Vault verwiesen wird. Es sind keine Key Vault-Werte oder -Anmeldeinformationen. Da der Clientanbieter die Schlüssel als Key Vault-Verweise erkennt, wird Key Vault zum Abrufen der zugehörigen Werte verwendet.

Ihre Anwendung ist für die ordnungsgemäße Authentifizierung sowohl bei App Configuration als auch bei Key Vault zuständig. Die beiden Dienste kommunizieren nicht direkt.

In diesem Tutorial wird veranschaulicht, wie Sie Key Vault-Verweise in Ihrem Code implementieren. Dies baut auf der Web-App auf, die in den Schnellstartanleitungen vorgestellt wurde. Durchlaufen Sie zuerst die Schnellstartanleitung zum [Erstellen einer ASP.NET Core-App mit Azure App Configuration](./quickstart-aspnet-core-app.md), bevor Sie fortfahren.

Für die Ausführung der Schritte dieses Tutorials können Sie einen beliebigen Code-Editor verwenden. [Visual Studio Code](https://code.visualstudio.com/) ist beispielsweise ein plattformübergreifender Code-Editor, der für die Betriebssysteme Windows, macOS und Linux verfügbar ist.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines App Configuration-Schlüssels, der auf einen in Key Vault gespeicherten Wert verweist
> * Zugreifen auf den Wert dieses Schlüssels über eine ASP.NET Core-Webanwendung

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie das [.NET Core SDK](https://dotnet.microsoft.com/download), bevor Sie mit diesem Tutorial beginnen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Erstellen eines Tresors

1. Wählen Sie oben links im Azure-Portal die Option **Ressource erstellen** aus:

    ![Screenshot: Option „Ressource erstellen“ im Azure-Portal](./media/quickstarts/search-services.png)
1. Geben Sie **Key Vault** in das Suchfeld ein.
1. Wählen Sie in der Ergebnisliste links **Key Vault** aus.
1. Wählen Sie unter **Schlüsseltresore** die Option **Hinzufügen** aus.
1. Geben Sie auf der rechten Seite unter **Schlüsseltresor erstellen** die folgenden Informationen ein:
    - Wählen Sie die Option **Abonnement** aus, um ein Abonnement auszuwählen.
    - Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, und geben Sie einen Namen für die Ressourcengruppe ein.
    - Unter **Schlüsseltresorname** müssen Sie einen eindeutigen Namen eingeben. Geben Sie für dieses Tutorial **Contoso-vault2** ein.
    - Wählen Sie in der Dropdownliste **Region** einen Ort aus.
1. Übernehmen Sie für die anderen Optionen unter **Schlüsseltresor erstellen** die Standardwerte.
1. Klicken Sie auf **Erstellen**.

An diesem Punkt ist nur Ihr Azure-Konto für den Zugriff auf diesen neuen Tresor autorisiert.

![Screenshot: Schlüsseltresor](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Hinzufügen eines Geheimnisses zu Key Vault

Zum Hinzufügen eines Geheimnisses zum Tresor müssen Sie lediglich einige zusätzliche Schritte ausführen. In diesem Fall fügen Sie eine Nachricht hinzu, die Sie verwenden können, um den Key Vault-Abruf zu testen. Die Nachricht hat den Namen **Nachricht**, und Sie speichern darin den Wert „Hallo von Key Vault“.

1. Wählen Sie auf den Key Vault-Eigenschaftenseiten die Option **Geheimnisse** aus.
1. Wählen Sie die Option **Generieren/Importieren** aus.
1. Geben Sie im Bereich **Geheimnis erstellen** die folgenden Werte ein:
    - **Uploadoptionen**: Geben Sie **Manuell** ein.
    - **Name**: Geben Sie **Nachricht** ein.
    - **Value**: Geben Sie **Hallo von Key Vault** ein.
1. Übernehmen Sie für die anderen Eigenschaften unter **Geheimnis erstellen** die Standardwerte.
1. Klicken Sie auf **Erstellen**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Hinzufügen eines Key Vault-Verweises zu App Configuration

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie die Option **Alle Ressourcen** und dann die Instanz des App Configuration-Speichers aus, die Sie in der Schnellstartanleitung erstellt haben.

1. Wählen Sie **Konfigurations-Explorer** aus.

1. Wählen Sie **+ Erstellen** > **Schlüsseltresorverweis** aus, und geben Sie dann die folgenden Werte an:
    - **Key**: Wählen Sie **TestApp:Settings:KeyVaultMessage** aus.
    - **Bezeichnung:** Lassen Sie diesen Wert leer.
    - **Abonnement**, **Ressourcengruppe** und **Schlüsseltresor**: Geben Sie die Werte ein, die den Werten des im vorherigen Abschnitt erstellten Schlüsseltresors entsprechen.
    - **Geheimnis**: Wählen Sie das Geheimnis mit dem Namen **Nachricht** aus, das Sie im vorherigen Abschnitt erstellt haben.

## <a name="connect-to-key-vault"></a>Verbindung mit Key Vault herstellen

1. In diesem Tutorial verwenden Sie einen Dienstprinzipal für die Authentifizierung bei Key Vault. Verwenden Sie zum Erstellen dieses Dienstprinzipals den Azure CLI-Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac):

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Daraufhin wird eine Reihe von Schlüssel-Wert-Paaren zurückgegeben:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Führen Sie den folgenden Befehl aus, um dem Dienstprinzipal den Zugriff auf Ihren Schlüsseltresor zu erlauben:

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Fügen Sie Umgebungsvariablen zum Speichern der Werte *clientId*, *clientSecret* und *tenantId* hinzu.

    #### <a name="windows-command-prompt"></a>[Windows-Eingabeaufforderung](#tab/cmd)

    ```cmd
    setx AZURE_CLIENT_ID <clientId-of-your-service-principal>
    setx AZURE_CLIENT_SECRET <clientSecret-of-your-service-principal>
    setx AZURE_TENANT_ID <tenantId-of-your-service-principal>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    $Env:AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    $Env:AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    export AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    export AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    ---

    > [!NOTE]
    > Diese Key Vault-Anmeldeinformationen werden nur innerhalb der Anwendung verwendet. Ihre Anwendung authentifiziert sich mit diesen Anmeldeinformationen direkt bei Key Vault. Sie werden nie an den App Configuration-Dienst übermittelt.

1. Starten Sie das Terminal neu, um diese neuen Umgebungsvariablen zu laden.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Aktualisieren des Codes für die Verwendung eines Key Vault-Verweises

1. Führen Sie den folgenden Befehl aus, um einen Verweis auf die erforderlichen NuGet-Pakete hinzuzufügen:

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. Öffnen Sie die Datei *Program.cs*, und fügen Sie Verweise auf die folgenden erforderlichen Pakete hinzu:

    ```csharp
    using Azure.Identity;
    ```

1. Aktualisieren Sie die `CreateWebHostBuilder`-Methode für die Verwendung von App Configuration, indem Sie die Methode `config.AddAzureAppConfiguration` aufrufen. Fügen Sie die Option `ConfigureKeyVault` ein, und übergeben Sie die richtigen Anmeldeinformationen an Key Vault.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

1. Wenn Sie die Verbindung mit App Configuration initialisiert haben, richten Sie die Verbindung mit Key Vault ein, indem Sie die Methode `ConfigureKeyVault` aufrufen. Nach der Initialisierung können Sie auf die Werte der Key Vault-Verweise genauso zugreifen, wie Sie bei den Werten regulärer App Configuration-Schlüssel vorgehen.

    Um diesen Prozess in Aktion zu sehen, öffnen Sie die Datei *Index.cshtml* im Ordner **Ansichten** > **Home**. Ersetzen Sie ihren Inhalt durch den folgenden Code:

    ```html
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

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Sie greifen auf den Wert des Key Vault-Verweises **TestApp:Settings:KeyVaultMessage** genauso wie auf den Konfigurationswert von **TestApp:Settings:Message** zu.

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Führen Sie den folgenden Befehl in der Befehlsshell aus, um die App mithilfe der .NET Core-CLI zu erstellen:

    ```dotnetcli
    dotnet build
    ```

1. Verwenden Sie nach Abschluss des Buildvorgangs den folgenden Befehl, um die Web-App lokal auszuführen:

    ```dotnetcli
    dotnet run
    ```

1. Öffnen Sie ein Browserfenster, und navigieren Sie zu `http://localhost:5000`. Dies ist die Standard-URL für die lokal gehostete Web-App.

    ![Schnellstart: Starten der lokalen App](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen App Configuration-Schlüssel erstellt, mit dem auf einen in Key Vault gespeicherten Wert verwiesen wird. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie eine von Azure verwaltete Dienstidentität hinzufügen, mit der der Zugriff auf App Configuration und Key Vault optimiert wird.

> [!div class="nextstepaction"]
> [Integration der verwalteten Identität](./howto-integrate-azure-managed-service-identity.md)
