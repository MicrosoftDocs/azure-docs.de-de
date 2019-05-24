---
title: Hosten der RESTful-API mit CORS – Azure App Service | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Azure App Service Ihnen beim Hosten Ihrer RESTful-APIs mit CORS-Unterstützung hilft.
services: app-service\api
documentationcenter: dotnet
author: cephalin
manager: cfowler
editor: ''
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/21/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b8c1130a45f60b9caaacd365cd1c256f50ed7675
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66138607"
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

* [Git installieren](https://git-scm.com/).
* [Installieren Sie .NET Core.](https://www.microsoft.com/net/core/)

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

![Lokal ausgeführte ASP.NET Core-API](./media/app-service-web-tutorial-rest-api/local-run.png)

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

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-app"></a>Navigieren zur Azure-App

Navigieren Sie in einem Browser zu `http://<app_name>.azurewebsites.net/swagger`, und experimentieren Sie ein wenig mit der Swagger-Benutzeroberfläche.

![In Azure App Service ausgeführte ASP.NET Core-API](./media/app-service-web-tutorial-rest-api/azure-run.png)

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

![CORS-Fehler im Browserclient](./media/app-service-web-tutorial-rest-api/cors-error.png)

Aufgrund des Domänenkonflikts zwischen Browser-App (`http://localhost:5000`) und Remoteressource (`http://<app_name>.azurewebsites.net`) und der Tatsache, dass Ihre API in App Service nicht den Header `Access-Control-Allow-Origin` sendet, hat der Browser das Laden domänenübergreifender Inhalte in Ihrer Browser-App verhindert.

In der Produktionsumgebung verfügt Ihre Browser-App anstelle der localhost-URL über eine öffentliche URL. Die Vorgehensweise zum Aktivieren von CORS ist jedoch in beiden Fällen identisch.

### <a name="enable-cors"></a>Aktivieren von CORS 

Aktivieren Sie CORS in Cloud Shell mithilfe des Befehls [`az resource update`](/cli/azure/resource#az-resource-update) für die URL Ihres Clients. Ersetzen Sie den Platzhalter _&lt;appname>_.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.cors.allowedOrigins="['http://localhost:5000']" --api-version 2015-06-01
```

In `properties.cors.allowedOrigins` können mehrere Client-URLs festgelegt werden (`"['URL1','URL2',...]"`). Sie können auch `"['*']"` verwenden, um alle Client-URLs zu aktivieren.

> [!NOTE]
> Falls für Ihre App Anmeldeinformationen wie Cookies oder Authentifizierungstoken gesendet werden müssen, erfordert der Browser unter Umständen den Header `ACCESS-CONTROL-ALLOW-CREDENTIALS` in der Antwort. Wenn Sie dies in App Service aktivieren möchten, legen Sie in der CORS-Konfiguration `properties.cors.supportCredentials` auf `true` fest. Die Aktivierung ist nicht möglich, wenn `'*'` in `allowedOrigins` enthalten ist.

### <a name="test-cors-again"></a>Erneutes Testen von CORS

Aktualisieren Sie Ihre Browser-App unter `http://localhost:5000`. Die Fehlermeldung im Fenster **Konsole** ist nun nicht mehr vorhanden. Stattdessen werden Daten der bereitgestellten API angezeigt, mit denen Sie interagieren können. Ihre Remote-API unterstützt jetzt CORS für Ihre lokal ausgeführte Browser-App. 

![Erfolgreiche CORS-Verwendung im Browserclient](./media/app-service-web-tutorial-rest-api/cors-success.png)

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
> [Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service](app-service-web-tutorial-auth-aad.md)
