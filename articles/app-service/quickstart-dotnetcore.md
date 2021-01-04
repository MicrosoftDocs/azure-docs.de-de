---
title: 'Schnellstart: Erstellen einer C#-ASP.NET Core-App'
description: Hier erfahren Sie, wie Sie Web-Apps in Azure App Service ausführen, indem Sie Ihre erste ASP.NET Core-App bereitstellen.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: d7b7d8845eda87132cbfa7981c20c39bb210aa5c
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032693"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Schnellstart: Erstellen von ASP.NET Core-Web-Apps in Azure

::: zone pivot="platform-windows"  

In dieser Schnellstartanleitung wird beschrieben, wie Sie Ihre erste ASP.NET Core-Web-App erstellen und für [Azure App Service](overview.md) bereitstellen. Von App Service werden .NET. 5.0-Apps unterstützt.

Am Ende verfügen Sie über eine Azure-Ressourcengruppe, die einen App Service-Hostingplan und eine App Service-Instanz mit einer bereitgestellten Webanwendung umfasst.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/dotnet/).
- Installieren Sie <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> mit der Workload **ASP.NET und Webentwicklung**.

  Sie haben Visual Studio 2019 bereits installiert:

  - Installieren Sie in Visual Studio die neuesten Updates, indem Sie **Hilfe** > **Nach Updates suchen** auswählen. Die neuesten Updates enthalten das .NET 5.0 SDK.
  - Fügen Sie die Workload hinzu. Wählen Sie dazu **Tools** > **Tools und Features abrufen** aus.


## <a name="create-an-aspnet-core-web-app"></a>Erstellen einer ASP.NET Core-Web-App

Führen Sie zum Erstellen einer ASP.NET Core-Web-App in Visual Studio die folgenden Schritte aus:

# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Öffnen Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.

1. Wählen Sie unter **Neues Projekt erstellen** die Option **ASP.NET Core-Webanwendung** aus, und vergewissern Sie sich, dass **C#** als Sprache für diese Option aufgeführt ist. Wählen Sie anschließend **Weiter** aus.

1. Geben Sie Ihrem Webanwendungsprojekt unter **Neues Projekt konfigurieren** den Namen *myFirstAzureWebApp*, und wählen Sie **Erstellen** aus.

   ![Konfigurieren Ihres Web-App-Projekts](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. Sie können einen beliebigen Typ von ASP.NET Core-Web-App für Azure bereitstellen, aber für diese Schnellstartanleitung sollten Sie die Vorlage **Webanwendung** auswählen. Stellen Sie sicher, dass unter **Authentifizierung** die Option **Keine Authentifizierung** ausgewählt und keine andere Option festgelegt ist. Wählen Sie anschließend **Erstellen**.

   ![Erstellen einer neuen ASP.NET Core-Web-App](./media/quickstart-dotnetcore/create-aspnet-core-web-app.png) 
   
1. Wählen Sie im Visual Studio-Menü **Debuggen** > **Starten ohne Debugging** aus, um Ihre Web-App lokal auszuführen.

   ![Lokal ausgeführte Web-App](./media/quickstart-dotnetcore/web-app-running-locally.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Öffnen Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.

1. Wählen Sie unter **Neues Projekt erstellen** die Option **ASP.NET Core-Webanwendung** aus, und vergewissern Sie sich, dass **C#** als Sprache für diese Option aufgeführt ist. Wählen Sie anschließend **Weiter** aus.

1. Geben Sie Ihrem Webanwendungsprojekt unter **Neues Projekt konfigurieren** den Namen *myFirstAzureWebApp*, und wählen Sie **Erstellen** aus.

   ![Konfigurieren Ihres Web-App-Projekts](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. Wählen Sie für eine .NET 5.0-App in der Dropdownliste die Option **ASP.NET Core 5.0** aus.

1. Sie können eine beliebige Art von ASP.NET Core-Web-App für Azure bereitstellen. In dieser Schnellstartanleitung wird allerdings die Vorlage **ASP.NET Core-Web-App** verwendet. Stellen Sie sicher, dass unter **Authentifizierung** die Option **Keine Authentifizierung** ausgewählt und keine andere Option festgelegt ist. Wählen Sie anschließend **Erstellen**.

   ![Erstellen einer neuen ASP.NET Core-Web-App](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1. Wählen Sie im Visual Studio-Menü **Debuggen** > **Starten ohne Debugging** aus, um Ihre Web-App lokal auszuführen.

   ![Lokal ausgeführte Web-App](./media/quickstart-dotnetcore/web-app-running-locally.png)

---

## <a name="publish-your-web-app"></a>Veröffentlichen Ihrer Web-App

Zum Veröffentlichen Ihrer Web-App müssen Sie zuerst eine neue App Service-Instanz erstellen und konfigurieren, auf der Sie Ihre App veröffentlichen können. 

Erstellen Sie bei der Einrichtung der App Service-Instanz Folgendes:

- Eine neue [Ressourcengruppe](../azure-resource-manager/management/overview.md#terminology), die alle Azure-Ressourcen für den Dienst enthalten soll.
- Einen neuen [Hostingplan](./overview-hosting-plans.md), mit dem der Standort, die Größe und die Funktionen der Webserverfarm zum Hosten Ihrer App angegeben werden.

Führen Sie die folgenden Schritte aus, um Ihre App Service-Instanz zu erstellen und Ihre Web-App zu veröffentlichen:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **myFirstAzureWebApp**, und wählen Sie **Veröffentlichen** aus. 

1. Wählen Sie unter **Veröffentlichen** die Option **Azure** aus, und klicken Sie auf **Weiter**.

1. Ihre Optionen hängen davon ab, ob Sie bereits bei Azure angemeldet sind und ob Sie über ein Visual Studio-Konto verfügen, das mit einem Azure-Konto verknüpft ist. Wählen Sie entweder **Konto hinzufügen** oder **Anmelden** aus, um sich bei Ihrem Azure-Abonnement anzumelden. Wenn Sie bereits angemeldet sind, wählen Sie das gewünschte Konto aus.

   ![Anmelden bei Azure](./media/quickstart-dotnetcore/sign-in-azure-vs2019.png)

1. Klicken Sie rechts von **App Service-Instanzen** auf **+** .

   ![Neue App Service-App](./media/quickstart-dotnetcore/publish-new-app-service.png)

1. Akzeptieren Sie unter **Abonnement** das angegebene Abonnement, oder wählen Sie in der Dropdownliste ein neues Abonnement aus.

1. Wählen Sie unter **Ressourcengruppe** die Option **Neu** aus. Geben Sie unter **Name der neuen Ressourcengruppe** den Namen *myResourceGroup* ein, und wählen Sie **OK** aus. 

1. Wählen Sie unter **Hostingplan** die Option **Neu** aus. 

1. Geben Sie im Dialogfeld **Hostingplan: Neu erstellen** die Werte ein, die in der folgenden Tabelle angegeben sind:

   | Einstellung  | Empfohlener Wert | BESCHREIBUNG |
   | -------- | --------------- | ----------- |
   | **Hostingplan**  | *myFirstAzureWebAppPlan* | Name des App Service-Plans. |
   | **Location**      | *Europa, Westen* | Das Rechenzentrum, in dem die Web-App gehostet wird. |
   | **Größe**          | *Free* | Der [Tarif](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) bestimmt die Hostingfeatures. |
   
   ![Erstellen eines neuen Hostingplans](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. Geben Sie unter **Name** einen eindeutigen App-Namen ein, der nur aus den folgenden zulässigen Zeichen besteht: `a-z`, `A-Z`, `0-9` und `-`. Sie können den automatisch generierten eindeutigen Namen übernehmen. Die URL der Web-App lautet `http://<app-name>.azurewebsites.net`, wobei `<app-name>` der Name Ihrer App ist.

2. Wählen Sie **Erstellen** aus, um die Azure-Ressourcen zu erstellen.

   ![Erstellen der App-Ressourcen](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

   Nach dem Abschluss des Assistenten werden die Azure-Ressourcen für Sie erstellt, und Sie können sie veröffentlichen.

3. Wählen Sie **Fertig stellen** aus, um den Assistenten zu schließen.

1. Klicken Sie auf der Seite **Veröffentlichen** auf **Veröffentlichen**. Visual Studio erstellt, packt und veröffentlicht die App in Azure und startet sie anschließend im Standardbrowser.

   ![In Azure ausgeführte veröffentlichte ASP.NET-Web-App](./media/quickstart-dotnetcore/web-app-running-live.png)

**Glückwunsch!** Ihre ASP.NET Core-Web-App wird live in Azure App Service ausgeführt.

## <a name="update-the-app-and-redeploy"></a>Aktualisieren der App und erneutes Bereitstellen

Führen Sie die folgenden Schritte aus, um Ihre Web-App zu aktualisieren und erneut bereitzustellen:

1. Öffnen Sie im **Projektmappen-Explorer** unter Ihrem Projekt **Seiten** > **Index.cshtml**.

1. Ersetzen Sie das gesamte `<div>`-Tag durch den folgenden Code:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Klicken Sie zur erneuten Bereitstellung in Azure im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **myFirstAzureWebApp**, und wählen Sie **Veröffentlichen** aus.

1. Wählen Sie auf der Zusammenfassungsseite **Veröffentlichen** die Option **Veröffentlichen** aus.

   <!-- ![Publish update to web app](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png) -->

    Nach Abschluss der Veröffentlichung wird in Visual Studio ein Browser mit der URL der Web-App gestartet.

    ![In Azure ausgeführte aktualisierte ASP.NET-Web-App](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Verwalten der Azure-App

Wechseln Sie zum Verwalten Ihrer Web-App zum [Azure-Portal](https://portal.azure.com), und suchen Sie nach **App Services**. Wählen Sie diese Option anschließend aus.

![Auswählen von „App Services“](./media/quickstart-dotnetcore/app-services.png)

Wählen Sie auf der Seite **App Services** den Namen Ihrer Web-App aus.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Screenshot: Seite „App Services“ mit einer ausgewählten Beispiel-Web-App":::

Die Seite **Übersicht** für Ihre Web-App enthält Optionen für die grundlegende Verwaltung, z. B. Durchsuchen, Beenden, Starten, Neustarten und Löschen. Im linken Menü können Sie auf weitere Seiten für die Konfiguration Ihrer App zugreifen.

![App Service im Azure-Portal](./media/quickstart-dotnetcore/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Visual Studio verwendet, um eine ASP.NET Core-Web-App zu erstellen und für Azure App Service bereitzustellen.

Fahren Sie mit dem nächsten Artikel fort, um sich darüber zu informieren, wie Sie eine .NET Core-App erstellen und dafür eine Verbindung mit einer SQL-Datenbank herstellen:

> [!div class="nextstepaction"]
> [ASP.NET Core mit SQL-Datenbank](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurieren der ASP.NET Core-App](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
[App Service unter Linux](overview.md#app-service-on-linux) bietet einen hochgradig skalierbaren Webhostingdienst mit Self-Patching unter Linux-Betriebssystemen. In diesem Schnellstartartikel wird erläutert, wie Sie eine [.NET Core](/aspnet/core/)-App in App Service unter Linux erstellen. Sie erstellen die App mithilfe der [Azure CLI](/cli/azure/get-started-with-azure-cli) und stellen mit Git den .NET Core-Code für die App bereit.

![In Azure ausgeführte Beispiel-App](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Die Schritte in diesem Artikel können unter Mac, Windows oder Linux ausgeführt werden.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-initial-environment"></a>Einrichten der anfänglichen Umgebung

# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

So führen Sie diesen Schnellstart durch:

* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Installieren Sie das aktuelle .NET Core 3.1 SDK.</a>
* <a href="/cli/azure/install-azure-cli" target="_blank">Installieren Sie die neueste Azure CLI</a>.

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

So führen Sie diesen Schnellstart durch:

* <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">Installieren Sie das aktuelle .NET 5.0 SDK.</a>
* <a href="/cli/azure/install-azure-cli" target="_blank">Installieren Sie die neueste Azure CLI</a>.

---

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="create-the-app-locally"></a>Lokales Erstellen der App

Erstellen Sie in einem Terminalfenster auf Ihrem Computer ein Verzeichnis mit dem Namen `hellodotnetcore`, und wechseln Sie dorthin.

```bash
mkdir hellodotnetcore
cd hellodotnetcore
```

Erstellen Sie eine neue .NET Core-App.

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>Lokales Ausführen der App

Führen Sie die Anwendung lokal aus, damit Sie sehen, wie sie beim Bereitstellen in Azure aussehen sollte. 

```bash
dotnet run
```

Öffnen Sie einen Webbrowser, und navigieren Sie zu der App auf `http://localhost:5000`.

Auf der Seite wird die Nachricht **Hello World** aus der Beispiel-App angezeigt.

![Testen mit dem Browser](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="sign-into-azure"></a>Anmelden bei Azure
Melden Sie sich in Ihrem Terminalfenster mithilfe des folgenden Befehls bei Azure an:

```azurecli
az login
```

## <a name="deploy-the-app"></a>Bereitstellen der App

Stellen Sie den Code mithilfe des Befehls `az webapp up` in Ihrem lokalen Ordner (*hellodotnetcore*) bereit:

```azurecli
az webapp up --sku F1 --name <app-name> --os-type linux
```

- Wird der Befehl `az` nicht erkannt, sollten Sie sich vergewissern, dass die Azure CLI wie unter [Einrichten der anfänglichen Umgebung](#set-up-your-initial-environment) beschrieben installiert wurde.
- Ersetzen Sie `<app-name>` durch einen Namen, der innerhalb von Azure eindeutig ist (*gültige Zeichen: `a-z`, `0-9` und `-`* ). Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einer App-ID.
- Mit dem Argument `--sku F1` wird die Web-App im Tarif „Free“ erstellt. Lassen Sie dieses Argument weg, um einen schnelleren Premium-Tarif zu verwenden. Dieser verursacht jedoch stündlich Kosten.
- Optional können Sie das Argument `--location <location-name>` einfügen, wobei `<location-name>` eine verfügbare Azure-Region ist. Sie können eine Liste der zulässigen Regionen für Ihr Azure-Konto abrufen, indem Sie den Befehl [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) ausführen.

Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen. Bei der Ausführung werden Nachrichten zur Erstellung der Ressourcengruppe, des App Service-Plans und der Hosting-App, zur Konfiguration der Protokollierung und zur anschließenden ZIP-Bereitstellung bereitgestellt. Anschließend wird die Meldung „You can launch the app at http://&lt;app-name&gt;.azurewebsites.net“ (Sie können die App unter http://<App-Name>.azurewebsites.net starten.) angezeigt. Dabei handelt es sich um die URL der App in Azure.

# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Beispielausgabe des Befehls „az webapp up“](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

<!-- Deploy the code in your local folder (*hellodotnetcore*) using the `az webapp up` command:

```azurecli
az webapp up --sku B1 --name <app-name> --os-type linux
```

- If the `az` command isn't recognized, be sure you have the Azure CLI installed as described in [Set up your initial environment](#set-up-your-initial-environment).
- Replace `<app-name>` with a name that's unique across all of Azure (*valid characters are `a-z`, `0-9`, and `-`*). A good pattern is to use a combination of your company name and an app identifier.
- The `--sku B1` argument creates the web app in the Basic pricing tier, which incurs an hourly cost. Omit this argument to use a faster premium tier, which costs more.
- You can optionally include the argument `--location <location-name>` where `<location-name>` is an available Azure region. You can retrieve a list of allowable regions for your Azure account by running the [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) command.

The command may take a few minutes to complete. While running, it provides messages about creating the resource group, the App Service plan and hosting app, configuring logging, then performing ZIP deployment. It then gives the message, "You can launch the app at http://&lt;app-name&gt;.azurewebsites.net", which is the app's URL on Azure. -->

![Beispielausgabe des Befehls „az webapp up“](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Navigieren zur App

Navigieren Sie in Ihrem Webbrowser zu der bereitgestellten Anwendung.

```bash
http://<app_name>.azurewebsites.net
```

Der .NET Core-Beispielcode wird in App Service unter Linux mit einem integrierten Image ausgeführt.

![In Azure ausgeführte Beispiel-App](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Glückwunsch!** Sie haben Ihre erste .NET Core-App für App Service unter Linux bereitgestellt.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="update-and-redeploy-the-code"></a>Aktualisieren und erneutes Bereitstellen des Codes

Öffnen Sie im lokalen Verzeichnis die Datei _Startup.cs_. Nehmen Sie eine kleine Änderung am Text im Methodenaufruf `context.Response.WriteAsync` vor:

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Speichern Sie Ihre Änderungen, und stellen Sie die App dann mit dem Befehl `az webapp up` erneut bereit:

```azurecli
az webapp up --os-type linux
```

In diesem Befehl werden lokal zwischengespeicherte Werte aus der Datei *.azure/config* verwendet (einschließlich App-Name, Ressourcengruppe und App Service-Plan).

Wechseln Sie nach Abschluss der Bereitstellung wieder zu dem Browserfenster, das im Schritt **Navigieren zur App** geöffnet wurde, und wählen Sie die Option „Aktualisieren“ aus.

![In Azure ausgeführte aktualisierte Beispiel-App](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="manage-your-new-azure-app"></a>Verwalten Ihrer neuen Azure-App

Wechseln Sie zum <a href="https://portal.azure.com" target="_blank">Azure-Portal</a>, um die erstellte App zu verwalten.

Klicken Sie im linken Menü auf **App Services** und anschließend auf den Namen Ihrer Azure-App.

:::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Screenshot: Seite „App Services“ mit einer ausgewählten Beispiel-Azure-App":::

Die Übersichtsseite Ihrer App wird angezeigt. Hier können Sie einfache Verwaltungsaufgaben wie Durchsuchen, Beenden, Neustarten und Löschen durchführen. 

![App Service-Seite im Azure-Portal](media/quickstart-dotnetcore/portal-app-overview-up.png)

Im linken Menü werden verschiedene Seiten für die Konfiguration Ihrer App angezeigt. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: ASP.NET Core-App mit SQL-Datenbank](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurieren der ASP.NET Core-App](configure-language-dotnetcore.md)

::: zone-end
