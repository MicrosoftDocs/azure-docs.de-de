---
title: 'Tutorial: Hosten einer RESTful-API mit CORS'
description: Hier erfahren Sie, wie Azure App Service Ihnen beim Hosten Ihrer RESTful-APIs mit CORS-Unterstützung hilft. App Service kann sowohl Front-End-Web-Apps als auch Back-End-APIs hosten.
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/28/2020
ms.custom: devx-track-csharp, mvc, devcenter, seo-javascript-september2019, seo-javascript-october2019, seodec18
ms.openlocfilehash: 9481b6d2740d27b8c3d1309e205edda6017868fa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005747"
---
# <a name="tutorial-host-a-restful-api-with-cors-in-azure-app-service"></a>Tutorial: Hosten einer RESTful-API mit CORS in Azure App Service

Von [Azure App Service](overview.md) wird ein hochgradig skalierbarer Webhostingdienst mit Self-Patching bereitgestellt. Darüber hinaus bietet App Service integrierte Unterstützung für die [Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) für RESTful-APIs. In diesem Tutorial erfahren Sie, wie Sie eine ASP.NET Core-API-App mit CORS-Unterstützung in App Service bereitstellen. Die App wird mithilfe von Befehlszeilentools konfiguriert und unter Verwendung von Git bereitgestellt. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen von App Service-Ressourcen mithilfe der Azure-Befehlszeilenschnittstelle
> * Bereitstellen einer RESTful-API in Azure mithilfe von Git
> * Aktivieren der App Service-CORS-Unterstützung

Die Schritte in diesem Tutorial können unter macOS, Linux und Windows ausgeführt werden.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* <a href="https://git-scm.com/" target="_blank">Installation von Git</a>
 * <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Installation des aktuellen .NET Core 3.1 SDK</a>

## <a name="create-local-aspnet-core-app"></a>Erstellen einer lokalen ASP.NET Core-App

In diesem Schritt richten Sie das lokale ASP.NET Core-Projekt ein. App Service unterstützt den gleichen Workflow für APIs in anderen Sprachen.

### <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Wechseln Sie im Terminalfenster mit `cd` in ein Arbeitsverzeichnis.  

Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. 

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
```

Dieses Repository enthält eine App, die auf der Grundlage des folgenden Tutorials erstellt wurde: [ASP.NET Core-Web-API-Hilfeseiten mit Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio). Diese App verwendet einen Swagger-Generator, um die [Swagger-Benutzeroberfläche](https://swagger.io/swagger-ui/) und den Swagger-JSON-Endpunkt bereitzustellen.

### <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die folgenden Befehle aus, um die erforderlichen Pakete zu installieren, Datenbankmigrationen auszuführen und die Anwendung zu starten.

```bash
cd dotnet-core-api
dotnet restore
dotnet run
```

Navigieren Sie in einem Browser zu `http://localhost:5000/swagger`, um ein wenig mit der Swagger-Benutzeroberfläche zu experimentieren.

![Lokal ausgeführte ASP.NET Core-API](./media/app-service-web-tutorial-rest-api/azure-app-service-local-swagger-ui.png)

Navigieren Sie zu `http://localhost:5000/api/todo`, um eine Liste mit JSON-ToDo-Elementen anzuzeigen.

Navigieren Sie zu `http://localhost:5000`, und experimentieren Sie ein wenig mit der Browser-App. Später verweisen Sie in der Browser-App auf eine Remote-API in App Service, um die CORS-Funktion zu testen. Code für die Browser-App finden Sie im Verzeichnis _wwwroot_ des Repositorys.

Sie können ASP.NET Core jederzeit beenden, indem Sie im Terminal `Ctrl+C` drücken.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Bereitstellen von Apps in Azure

In diesem Schritt stellen Sie die mit der SQL-Datenbank verbundene .NET Core-Anwendung für App Service bereit.

### <a name="configure-local-git-deployment"></a>Konfigurieren der lokalen Git-Bereitstellung

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-an-app-service-plan"></a>Wie erstelle ich einen Plan?

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Erstellen einer Web-App

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="push-to-azure-from-git"></a>Übertragen von Git an Azure mithilfe von Push

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 83, done.
Counting objects: 100% (83/83), done.
Delta compression using up to 8 threads
Compressing objects: 100% (78/78), done.
Writing objects: 100% (83/83), 22.15 KiB | 3.69 MiB/s, done.
Total 83 (delta 26), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '509236e13d'.
remote: Generating deployment script.
remote: Project file path: .\TodoApi.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
To https://&lt;app_name&gt;.scm.azurewebsites.net/&lt;app_name&gt;.git
 * [new branch]      master -> master
</pre>

### <a name="browse-to-the-azure-app"></a>Navigieren zur Azure-App

Navigieren Sie in einem Browser zu `http://<app_name>.azurewebsites.net/swagger`, und experimentieren Sie ein wenig mit der Swagger-Benutzeroberfläche.

![In Azure App Service ausgeführte ASP.NET Core-API](./media/app-service-web-tutorial-rest-api/azure-app-service-browse-app.png)

Navigieren Sie zu `http://<app_name>.azurewebsites.net/swagger/v1/swagger.json`, um _swagger.json_ für Ihre bereitgestellte API anzuzeigen.

Navigieren Sie zu `http://<app_name>.azurewebsites.net/api/todo`, um Ihre bereitgestellte API in Aktion zu sehen.

## <a name="add-cors-functionality"></a>Hinzufügen der CORS-Funktion

Als Nächstes aktivieren Sie die integrierte CORS-Unterstützung in App Service für Ihre API.

### <a name="test-cors-in-sample-app"></a>Testen von CORS in der Beispiel-App

Öffnen Sie _wwwroot/index.html_ in Ihrem lokalen Repository.

Legen Sie die Variable `apiEndpoint` in Zeile 51 auf die URL Ihrer bereitgestellten API (`http://<app_name>.azurewebsites.net`) fest. Ersetzen Sie _\<appname>_ durch den Namen Ihrer App in App Service.

Führen Sie die Beispiel-App erneut in Ihrem lokalen Terminalfenster aus.

```bash
dotnet run
```

Navigieren Sie zur Browser-App (`http://localhost:5000`). Öffnen Sie in Ihrem Browser das Fenster mit den Entwicklertools (`Ctrl`+`Shift`+`i` in Chrome für Windows), und sehen Sie sich die Registerkarte **Konsole** an. Hier sollte nun die folgende Fehlermeldung angezeigt werden: `No 'Access-Control-Allow-Origin' header is present on the requested resource`.

![CORS-Fehler im Browserclient](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-error.png)

Aufgrund des Domänenkonflikts zwischen Browser-App (`http://localhost:5000`) und Remoteressource (`http://<app_name>.azurewebsites.net`) und der Tatsache, dass Ihre API in App Service nicht den Header `Access-Control-Allow-Origin` sendet, hat der Browser das Laden domänenübergreifender Inhalte in Ihrer Browser-App verhindert.

In der Produktionsumgebung verfügt Ihre Browser-App anstelle der localhost-URL über eine öffentliche URL. Die Vorgehensweise zum Aktivieren von CORS ist jedoch in beiden Fällen identisch.

### <a name="enable-cors"></a>Aktivieren von CORS 

Aktivieren Sie CORS in Cloud Shell mithilfe des Befehls [`az webapp cors add`](/cli/azure/webapp/cors#az-webapp-cors-add) für die URL Ihres Clients. Ersetzen Sie den Platzhalter _&lt;app-name>_ .

```azurecli-interactive
az webapp cors add --resource-group myResourceGroup --name <app-name> --allowed-origins 'http://localhost:5000'
```

In `properties.cors.allowedOrigins` können mehrere Client-URLs festgelegt werden (`"['URL1','URL2',...]"`). Sie können auch `"['*']"` verwenden, um alle Client-URLs zu aktivieren.

> [!NOTE]
> Falls für Ihre App Anmeldeinformationen wie Cookies oder Authentifizierungstoken gesendet werden müssen, erfordert der Browser unter Umständen den Header `ACCESS-CONTROL-ALLOW-CREDENTIALS` in der Antwort. Wenn Sie dies in App Service aktivieren möchten, legen Sie in der CORS-Konfiguration `properties.cors.supportCredentials` auf `true` fest. Die Aktivierung ist nicht möglich, wenn `'*'` in `allowedOrigins` enthalten ist.

### <a name="test-cors-again"></a>Erneutes Testen von CORS

Aktualisieren Sie Ihre Browser-App unter `http://localhost:5000`. Die Fehlermeldung im Fenster **Konsole** ist nun nicht mehr vorhanden. Stattdessen werden Daten der bereitgestellten API angezeigt, mit denen Sie interagieren können. Ihre Remote-API unterstützt jetzt CORS für Ihre lokal ausgeführte Browser-App. 

![Erfolgreiche CORS-Verwendung im Browserclient](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-success.png)

Sie verfügen nun über eine API in Azure App Service mit CORS-Unterstützung.

## <a name="app-service-cors-vs-your-cors"></a>App Service-CORS im Vergleich zu eigenem CORS

Wenn Sie mehr Flexibilität benötigen, können Sie anstelle von App Service-CORS auch eigene CORS-Hilfsprogramme verwenden. Dies kann beispielsweise erforderlich sein, wenn Sie verschiedene zulässige Ursprünge für verschiedene Routen oder Methoden angeben möchten. Da bei App Service-CORS für alle API-Routen und -Methoden nur ein einzelner Satz von zulässigen Ursprüngen angegeben werden kann, müssten Sie in diesem Fall Ihren eigenen CORS-Code verwenden. Informationen zur Umsetzung in ASP.NET Core finden Sie unter [Enabling Cross-Origin Requests (CORS)](/aspnet/core/security/cors) (Aktivieren von CORS).

> [!NOTE]
> Versuchen Sie nicht, App Service-CORS und Ihren eigenen CORS-Code parallel zu verwenden. In diesem Fall hat App Service-CORS Vorrang, und Ihr CORS-Code hat keinerlei Wirkung.
>
>

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Nächste Schritte

Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen von App Service-Ressourcen mithilfe der Azure-Befehlszeilenschnittstelle
> * Bereitstellen einer RESTful-API in Azure mithilfe von Git
> * Aktivieren der App Service-CORS-Unterstützung

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Benutzer authentifizieren und autorisieren.

> [!div class="nextstepaction"]
> [Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service](tutorial-auth-aad.md)
