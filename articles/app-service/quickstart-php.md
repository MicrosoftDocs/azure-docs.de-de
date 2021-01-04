---
title: 'Schnellstart: Erstellen einer PHP-Web-App'
description: Stellen Sie in wenigen Minuten Ihre erste PHP-App vom Typ „Hallo Welt“ in Azure App Service bereit. Die Bereitstellung erfolgt mithilfe von Git. Dies ist eine von zahlreichen verschiedenen Möglichkeiten für die Bereitstellung in App Service.
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.topic: quickstart
ms.date: 08/01/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: f6876d0aef0d3d87e038b623c395f8368a14e90c
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561850"
---
# <a name="create-a-php-web-app-in-azure-app-service"></a>Erstellen einer PHP-Web-App in Azure App Service

::: zone pivot="platform-windows"  
Von [Azure App Service](overview.md) wird ein hochgradig skalierbarer Webhostingdienst mit Self-Patching bereitgestellt.  In diesem Schnellstarttutorial wird erläutert, wie Sie eine PHP-App in Azure App Service unter Windows bereitstellen.
::: zone-end  

::: zone pivot="platform-linux"
Von [Azure App Service](overview.md) wird ein hochgradig skalierbarer Webhostingdienst mit Self-Patching bereitgestellt.  In diesem Schnellstart-Tutorial wird erläutert, wie Sie eine PHP-App in Azure App Service unter Linux bereitstellen.
::: zone-end  

Sie erstellen die Web-App mithilfe der [Azure-Befehlszeilenschnittstelle](/cli/azure/get-started-with-azure-cli) in Cloud Shell und stellen mit Git PHP-Beispielcode für die Web-App bereit.

![In Azure ausgeführte Beispiel-App](media/quickstart-php/hello-world-in-browser.png)

Die folgenden Schritte können unter Mac, Windows oder Linux ausgeführt werden. Nachdem die erforderlichen Komponenten installiert wurden, können die Schritte in etwa fünf Minuten durchgeführt werden.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

So führen Sie diesen Schnellstart durch:

* <a href="https://git-scm.com/" target="_blank">Installation von Git</a>
* <a href="https://php.net/manual/install.php" target="_blank">Installieren Sie PHP.</a>

## <a name="download-the-sample-locally"></a>Lokales Herunterladen des Beispiels

Führen Sie in einem Terminalfenster die folgenden Befehle aus. Dadurch wird die Beispielanwendung auf Ihren lokalen Computer geklont und zum Verzeichnis mit dem Beispielcode gewechselt. 

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>Lokales Ausführen der App

Führen Sie die Anwendung lokal aus, damit Sie sehen, wie sie beim Bereitstellen in Azure aussehen sollte. Öffnen Sie ein Terminalfenster, und verwenden Sie den Befehl `php`, um den integrierten PHP-Webserver zu starten.

```bash
php -S localhost:8080
```

Öffnen Sie einen Webbrowser, und navigieren Sie zu der Beispielapp auf `http://localhost:8080`.

Die Nachricht **Hello World!** aus der Beispiel-App wird auf der Seite angezeigt.

![Lokal ausgeführte Beispiel-App](media/quickstart-php/localhost-hello-world-in-browser.png)

Drücken Sie in Ihrem Terminalfenster **STRG+C**, um den Webserver zu beenden.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

::: zone pivot="platform-windows"  
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]
::: zone-end  

::: zone pivot="platform-linux"
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]
::: zone-end

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)]

## <a name="create-a-web-app"></a>Erstellen einer Web-App

Erstellen Sie in Cloud Shell mit dem Befehl [`az webapp create`](/cli/azure/webapp#az-webapp-create) eine Web-App im App Service-Plan `myAppServicePlan`. 

Ersetzen Sie im folgenden Beispiel `<app-name>` durch einen global eindeutigen App-Namen (gültige Zeichen sind `a-z`, `0-9` und `-`). Die Runtime ist auf `PHP|7.4` festgelegt. Führen Sie [`az webapp list-runtimes`](/cli/azure/webapp#az-webapp-list-runtimes) aus, um alle unterstützten Laufzeiten anzuzeigen. 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.4" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.4" --deployment-local-git
```

> [!NOTE]
> Das Symbol zum Beenden der Analyse `(--%)`, das in PowerShell 3.0 eingeführt wurde, weist PowerShell an, Eingaben nicht als PowerShell-Befehle oder -Ausdrücke zu interpretieren.
>

Nach Erstellung der Web-App zeigt die Azure CLI eine Ausgabe wie im folgenden Beispiel an:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

Sie haben eine leere neue Web-App mit aktivierter Git-Bereitstellung erstellt.

> [!NOTE]
> Die URL des Git-Remotespeicherorts wird in der `deploymentLocalGitUrl`-Eigenschaft im Format `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git` angezeigt. Speichern Sie diese URL, da Sie sie später noch benötigen.
>

Wechseln Sie zu Ihrer neu erstellten Web-App. Ersetzen Sie _&lt;app-name>_ durch Ihren eindeutigen App-Namen, den Sie im vorherigen Schritt erstellt haben.

```bash
http://<app-name>.azurewebsites.net
```

Ihre neue Web-App sollte nun wie folgt aussehen:

![Leere Web-App-Seite](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
   cc39b1e..25f1805  main -> main
</pre>

## <a name="browse-to-the-app"></a>Navigieren zur App

Navigieren Sie in Ihrem Webbrowser zu der bereitgestellten Anwendung.

```
http://<app-name>.azurewebsites.net
```

Der PHP-Beispielcode wird in einer Azure App Service-Web-App ausgeführt.

![In Azure ausgeführte Beispiel-App](media/quickstart-php/hello-world-in-browser.png)

**Glückwunsch!** Sie haben Ihre erste PHP-App für App Service bereitgestellt.

## <a name="update-locally-and-redeploy-the-code"></a>Lokales Aktualisieren und erneutes Bereitstellen des Codes

Öffnen Sie die Datei `index.php` innerhalb der PHP-App mit einem lokalen Text-Editor, und ändern Sie den Text in der Zeichenfolge neben `echo` geringfügig:

```php
echo "Hello Azure!";
```

Committen Sie Ihre Änderungen im lokalen Terminalfenster in Git, und übertragen Sie die Codeänderungen mithilfe von Push an Azure.

```bash
git commit -am "updated output"
git push azure main
```

Wechseln Sie nach Abschluss der Bereitstellung wieder zu dem Browserfenster, das im Schritt **Navigieren zur App** geöffnet wurde, und aktualisieren Sie die Seite.

![In Azure ausgeführte aktualisierte Beispiel-App](media/quickstart-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Verwalten Ihrer neuen Azure-App

1. Wechseln Sie zum <a href="https://portal.azure.com" target="_blank">Azure-Portal</a>, um die erstellte Web-App zu verwalten. Suchen Sie nach **App Services**, und wählen Sie diese Option aus.

    ![Suchen nach App Services, Azure-Portal, Erstellen einer PHP-Web-App](media/quickstart-php/navigate-to-app-services-in-the-azure-portal.png)

2. Wählen Sie den Namen Ihrer Azure-App aus.

    ![Portalnavigation zur Azure-App](./media/quickstart-php/php-docs-hello-world-app-service-list.png)

    Die Seite **Übersicht** für Ihre Web-App wird angezeigt. Hier können Sie einfache Verwaltungsaufgaben wie **Durchsuchen**, **Beenden**, **Neustarten** und **Löschen** durchführen.

    ![App Service-Seite im Azure-Portal](media/quickstart-php/php-docs-hello-world-app-service-detail.png)

    Im Web-App-Menü werden verschiedene Konfigurationsoptionen für Ihre App angezeigt. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [PHP mit MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Konfigurieren einer PHP-App](configure-language-php.md)
