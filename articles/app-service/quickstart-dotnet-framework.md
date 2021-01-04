---
title: 'Schnellstart: Erstellen einer C#-ASP.NET-App'
description: Hier erfahren Sie, wie Sie Web-Apps in Azure App Service ausführen, indem Sie die standardmäßige C#-ASP.NET-Web-App-Vorlage über Visual Studio bereitstellen.
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.topic: quickstart
ms.date: 11/20/2020
ms.custom: devx-track-csharp, mvc, devcenter, seodec18
ms.openlocfilehash: a4f7ba288bc27d6079deea9caf0ea315a55d0745
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96004060"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Erstellen einer ASP.NET Framework-Web-App in Azure

Von [Azure App Service](overview.md) wird ein hochgradig skalierbarer Webhostingdienst mit Self-Patching bereitgestellt.

Diese Schnellstartanleitung veranschaulicht die Bereitstellung Ihrer ersten ASP.NET-Web-App in Azure App Service. Wenn Sie fertig sind, verfügen Sie über einen App Service-Plan. Darüber hinaus besitzen Sie eine App Service-App mit einer bereitgestellten Webanwendung.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> mit der Workload **ASP.NET und Webentwicklung**, um dieses Tutorial abzuschließen.

Sie haben Visual Studio 2019 bereits installiert:

- Installieren Sie in Visual Studio die neuesten Updates, indem Sie **Hilfe** > **Nach Updates suchen** auswählen.
- Fügen Sie die Workload hinzu. Wählen Sie dazu **Tools** > **Tools und Features abrufen** aus.

## <a name="create-an-aspnet-web-app"></a>Erstellen einer ASP.NET-Web-App <a name="create-and-publish-the-web-app"></a>

Führen Sie zum Erstellen einer ASP.NET-Web-App die folgenden Schritte aus:

1. Öffnen Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.

2. Suchen Sie unter **Neues Projekt erstellen** die Option **ASP.NET-Webanwendung (.NET Framework)** , und wählen Sie sie aus. Wählen Sie anschließend **Weiter** aus.

3. Geben Sie der Anwendung unter **Neues Projekt konfigurieren** den Namen _myFirstAzureWebApp_, und wählen Sie dann **Erstellen** aus.

   ![Konfigurieren Ihres Web-App-Projekts](./media/quickstart-dotnet-framework/configure-web-app-project-framework.png)

4. Sie können jede Art von ASP.NET Web-App für Azure bereitstellen. Wählen Sie für diese Schnellstartanleitung die Vorlage **MVC** aus.

5. Stellen Sie sicher, dass für die Authentifizierung **Keine Authentifizierung** festgelegt ist. Klicken Sie auf **Erstellen**.

   ![Erstellen einer ASP.NET-Webanwendung](./media/quickstart-dotnet-framework/select-mvc-template-vs2019.png)

6. Wählen Sie im Visual Studio-Menü **Debuggen** > **Starten ohne Debugging** aus, um die Web-App lokal auszuführen.

   ![Lokales Ausführen der App](./media/quickstart-dotnet-framework/local-web-app.png)

## <a name="publish-your-web-app"></a>Veröffentlichen Ihrer Web-App <a name="launch-the-publish-wizard"></a>

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **myFirstAzureWebApp**, und wählen Sie **Veröffentlichen** aus.

1. Wählen Sie unter **Veröffentlichen** die Option **Azure** aus, und klicken Sie auf **Weiter**.

1. Wählen Sie **Azure App Service (Windows)** aus, und klicken Sie auf **Weiter**.

   <!-- ![Publish from project overview page](./media/quickstart-dotnet-framework/publish-app-framework-vs2019.png) -->

1. Ihre Optionen hängen davon ab, ob Sie bereits bei Azure angemeldet sind und ob Sie über ein Visual Studio-Konto verfügen, das mit einem Azure-Konto verknüpft ist. Wählen Sie entweder **Konto hinzufügen** oder **Anmelden** aus, um sich bei Ihrem Azure-Abonnement anzumelden. Wenn Sie bereits angemeldet sind, wählen Sie das gewünschte Konto aus.

   ![Anmelden bei Azure](./media/quickstart-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. Klicken Sie rechts von **App Service-Instanzen** auf **+** .

   ![Neue App Service-App](./media/quickstart-dotnet-framework/publish-new-app-service.png)

1. Wählen Sie unter **Ressourcengruppe** die Option **Neu** aus.

1. Geben Sie unter **Name der neuen Ressourcengruppe** den Namen *myResourceGroup* ein, und wählen Sie **OK** aus.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Wählen Sie unter **Hostingplan** die Option **Neu** aus.

1. Geben Sie im Dialogfeld **Hostingplan** die Werte aus der folgenden Tabelle ein, und wählen Sie anschließend **OK** aus:

   | Einstellung | Empfohlener Wert | BESCHREIBUNG |
   |-|-|-|
   | Hostingplan| myAppServicePlan | Name des App Service-Plans. |
   | Standort | Europa, Westen | Das Rechenzentrum, in dem die Web-App gehostet wird. |
   | Size | Kostenlos | Der [Tarif](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) bestimmt die Hostingfeatures. |

   ![App Service-Plan erstellen](./media/quickstart-dotnet-framework/app-service-plan-framework-vs2019.png)

1. Geben Sie unter **Name** einen eindeutigen App-Namen ein, der nur aus den folgenden zulässigen Zeichen besteht: `a-z`, `A-Z`, `0-9` und `-`. Sie können den automatisch generierten eindeutigen Namen übernehmen. Die URL der Web-App lautet `http://<app-name>.azurewebsites.net`, wobei `<app-name>` der Name Ihrer App ist.

2. Wählen Sie **Erstellen** aus, um die Azure-Ressourcen zu erstellen.

   ![Konfigurieren des App-Namens](./media/quickstart-dotnet-framework/web-app-name-framework-vs2019.png)

    Nach dem Abschluss des Assistenten werden die Azure-Ressourcen für Sie erstellt, und Sie können sie veröffentlichen.

3. Wählen Sie **Fertig stellen** aus, um den Assistenten zu schließen.

3. Klicken Sie auf der Seite **Veröffentlichen** auf **Veröffentlichen**. Visual Studio erstellt, packt und veröffentlicht die App in Azure und startet sie anschließend im Standardbrowser.

    ![Veröffentlichte ASP.NET-Web-App in Azure](./media/quickstart-dotnet-framework/published-azure-web-app.png)

Der App-Name, den Sie auf der Seite **App Service – Neues Element erstellen** angegeben haben, wird als URL-Präfix im Format `http://<app-name>.azurewebsites.net` verwendet.

**Glückwunsch!** Ihre ASP.NET-Web-App wird live in Azure App Service ausgeführt.

## <a name="update-the-app-and-redeploy"></a>Aktualisieren der App und erneutes Bereitstellen

1. Öffnen Sie im **Projektmappen-Explorer** unter Ihrem Projekt **Ansichten** > **Start** > **Index.cshtml**.

1. Suchen Sie im oberen Bereich nach dem HTML-Tag `<div class="jumbotron">`, und ersetzen Sie das gesamte Element durch folgenden Code:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Klicken Sie zur erneuten Bereitstellung in Azure im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **myFirstAzureWebApp**, und wählen Sie **Veröffentlichen** aus. Wählen Sie anschließend **Veröffentlichen** aus.

    Nach Abschluss der Veröffentlichung wird in Visual Studio ein Browser mit der URL der Web-App gestartet.

    ![Aktualisierte ASP.NET-Web-App in Azure](./media/quickstart-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Verwalten der Azure-App

1. Wechseln Sie zum Verwalten der Web-App zum [Azure-Portal](https://portal.azure.com), und suchen Sie nach **App Services**. Wählen Sie diese Option anschließend aus.

   ![Auswählen von „App Services“](./media/quickstart-dotnet-framework/app-services.png)

2. Wählen Sie auf der Seite **App Services** den Namen Ihrer Web-App aus.

   ![Portalnavigation zur Azure-App](./media/quickstart-dotnet-framework/access-portal-framework-vs2019.png)

   Die Übersichtsseite Ihrer Web-App wird angezeigt. Hier können Sie einfache Verwaltungsaufgaben wie Durchsuchen, Beenden, Starten, Neustarten und Löschen durchführen.

   ![App Service-Übersicht im Azure-Portal](./media/quickstart-dotnet-framework/web-app-general-framework-vs2019.png)

   Im linken Menü werden verschiedene Seiten für die Konfiguration Ihrer App angezeigt.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [ASP.NET mit SQL-Datenbank](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [Konfigurieren einer ASP.NET-App](configure-language-dotnet-framework.md)
