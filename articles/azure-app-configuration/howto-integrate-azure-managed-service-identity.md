---
title: Integrieren mit verwalteten Azure-Identitäten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie verwaltete Azure-Identitäten verwenden, um die Authentifizierung für Azure App Configuration durchzuführen und Zugriff darauf zu erhalten.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 3977991386dbcd07e92f21d1ac541f486b4f7f0a
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393655"
---
# <a name="integrate-with-azure-managed-identities"></a>Integrieren mit verwalteten Azure-Identitäten

Mit [verwalteten Identitäten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) von Azure Active Directory wird die Verwaltung von Geheimnissen für Ihre Cloudanwendung vereinfacht. Mit einer verwalteten Identität können Sie Ihren Code für die Nutzung des Dienstprinzipals einrichten, der für den Azure-Computedienst erstellt wurde, auf dem die Ausführung erfolgt. Eine verwaltete Identität wird anstelle von separaten Anmeldeinformationen verwendet, die in Azure Key Vault oder in einer lokalen Verbindungszeichenfolge gespeichert sind. 

Azure App Configuration und die zugehörigen .NET Core-, .NET- und Java Spring-Clientbibliotheken verfügen über integrierte MSI-Unterstützung (Managed Service Identity, Verwaltete Dienstidentität). Zwar müssen Sie MSI nicht verwenden, doch entfällt hierbei die Notwendigkeit für das Zugriffstoken mit Geheimnissen. Ihr Code muss nur den Dienstendpunkt für einen App-Konfigurationsspeicher kennen, um darauf zuzugreifen. Sie können diese URL direkt in Ihren Code einbetten, ohne sich Sorgen machen zu müssen, dass geheime Schlüssel offengelegt werden.

In diesem Tutorial wird veranschaulicht, wie Sie MSI für den Zugriff auf App Configuration nutzen können. Dies baut auf der Web-App auf, die in den Schnellstartanleitungen vorgestellt wurde. Führen Sie zuerst den Schnellstart [Erstellen einer ASP.NET Core-App mit Azure App Configuration](./quickstart-aspnet-core-app.md) durch, bevor Sie fortfahren.

Für die Ausführung der Schritte dieses Tutorials können Sie einen beliebigen Code-Editor verwenden. [Visual Studio Code](https://code.visualstudio.com/) ist eine hervorragende Option, die auf Windows-, macOS- und Linux-Plattformen verfügbar ist.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Gewähren des Zugriffs auf App Configuration für eine verwaltete Identität.
> * Konfigurieren Ihrer App für die Verwendung einer verwalteten Identität bei der Verbindungsherstellung mit App Configuration.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Konfigurierte Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Hinzufügen einer verwalteten Identität

Um eine verwaltete Entität im Portal einzurichten, erstellen Sie wie gewohnt zuerst eine Anwendung und aktivieren dann das Feature.

1. Erstellen Sie wie gewohnt im [Azure-Portal](https://portal.azure.com) eine App. Wechseln Sie im Portal zu dieser App.

2. Scrollen Sie im linken Bereich nach unten zur Gruppe **Einstellungen**, und wählen Sie **Identität**.

3. Ändern Sie auf der Registerkarte **Systemseitig zugewiesen** den **Status** in **Ein**, und wählen Sie **Speichern** aus.

    ![Festlegen der verwalteten Identität in App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Gewähren des Zugriffs auf App Configuration

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) **Alle Ressourcen** aus, und wählen Sie dann den App-Konfigurationsspeicher aus, den Sie in der Schnellstartanleitung erstellt haben.

2. Wählen Sie die Option **Zugriffssteuerung (IAM)** .

3. Wählen Sie auf der Registerkarte **Zugriff überprüfen** im Kartenelement **Rollenzuweisung hinzufügen** den Befehl **Hinzufügen** aus.

4. Wählen Sie unter **Rolle** die Option **Mitwirkender**. Wählen Sie unter **Zugriff zuweisen zu** unter **Systemseitig zugewiesene verwaltete Identität** die Option **App Service** aus.

5. Wählen Sie unter **Abonnement** Ihr Azure-Abonnement aus. Wählen Sie die App Service-Ressource für Ihre App aus.

6. Wählen Sie **Speichern** aus.

    ![Hinzufügen einer verwalteten Identität](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>Verwenden einer verwalteten Identität

1. Öffnen Sie die Datei *appsettings.json*, und fügen Sie das folgende Skript hinzu. Ersetzen Sie *\<service_endpoint>* (einschließlich der spitzen Klammern) durch die URL zu Ihrem App-Konfigurationsspeicher:

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

2. Öffnen Sie die Datei *Program.cs*, und aktualisieren Sie die `CreateWebHostBuilder`-Methode, indem Sie die `config.AddAzureAppConfiguration()`-Methode ersetzen.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Bereitstellen über lokales Git

Die einfachste Möglichkeit zum Aktivieren einer lokalen Git-Bereitstellung für Ihre App mit dem Kudu-Buildserver ist die Verwendung von Azure Cloud Shell.

### <a name="configure-a-deployment-user"></a>Konfigurieren eines Bereitstellungsbenutzers

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Aktivieren von lokalem Git mit Kudu

Zum Aktivieren einer lokalen Git-Bereitstellung für Ihre App mit dem Kudu-Buildserver führen Sie [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) in Cloud Shell aus.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Wenn Sie stattdessen eine Git-fähige App erstellen möchten, führen Sie [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) in Cloud Shell mit dem Parameter `--deployment-local-git` aus.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

Die Ausgabe des Befehls `az webapp create` sieht ungefähr wie folgt aus:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Bereitstellen des Projekts

Kehren Sie zum _lokalen Terminalfenster_ zurück, und fügen Sie Ihrem lokalen Git-Repository einen Azure-Remotespeicherort hinzu. Ersetzen Sie _\<Url>_ durch die URL des Git-Remoterepositorys, die Sie beim [Aktivieren von Git für die App](#enable-local-git-with-kudu) erhalten haben.

```bash
git remote add azure <url>
```

Führen Sie einen Pushvorgang zum Azure-Remotespeicherort durch, um Ihre App mit dem folgenden Befehl bereitzustellen. Geben Sie bei Aufforderung zur Eingabe eines Kennworts das Kennwort ein, das Sie in [Konfigurieren eines Bereitstellungsbenutzers](#configure-a-deployment-user) erstellt haben. Verwenden Sie nicht das Kennwort, mit dem Sie sich beim Azure-Portal anmelden.

```bash
git push azure master
```

Die Ausgabe enthält u. U. laufzeitspezifische Automatisierungen wie MSBuild für ASP.NET, `npm install` für Node.js und `pip install` für Python.

### <a name="browse-to-the-azure-web-app"></a>Navigieren Sie zur Azure-Web-App.

Navigieren Sie in einem Browser zu Ihrer Web-App, um sicherzustellen, dass der Inhalt bereitgestellt wird.

```bash
http://<app_name>.azurewebsites.net
```

![In App Service ausgeführte App](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Verwenden einer verwalteten Identität in anderen Sprachen

App Configuration-Anbieter für .NET Framework und Java Spring verfügen auch über integrierte Unterstützung für verwaltete Identitäten. In diesen Fällen verwenden Sie beim Konfigurieren eines Anbieters anstelle der vollständigen Verbindungszeichenfolge den URL-Endpunkt Ihres App-Konfigurationsspeichers. Für die in der Schnellstartanleitung erstellte .NET Framework-Konsolen-App geben Sie beispielsweise in der Datei *App.config* die folgenden Einstellungen an:

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [CLI-Beispiele](./cli-samples.md)
