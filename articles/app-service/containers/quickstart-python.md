---
title: 'Erstellen von Python-Apps unter Linux: Azure App Service | Microsoft-Dokumentation'
description: Stellen Sie in wenigen Minuten Ihre erste Python-App „Hallo Welt“ in Azure App Service unter Linux bereit.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/29/2019
ms.author: cephalin
ms.openlocfilehash: b704e9074c8ef88d8fefd97f466884af952c46f8
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919611"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux"></a>Erstellen einer Python-App in Azure App Service für Linux

In dieser Schnellstartanleitung stellen Sie eine einfache Python-App für [App Service für Linux](app-service-linux-intro.md) bereit, mit der ein hoch skalierbarer Webhostingdienst mit Self-Patching bereitgestellt wird. Sie verwenden die Azure-Befehlszeilenschnittstelle ([Azure CLI](/cli/azure/install-azure-cli)) über den interaktiven, browserbasierten Azure Cloud Shell-Dienst und können die Schritte mit einem Mac-, Linux- oder Windows-Computer ausführen.

![In Azure ausgeführte Beispiel-App](media/quickstart-python/hello-world-in-browser.png)

## <a name="prerequisites"></a>Voraussetzungen

So führen Sie diesen Schnellstart durch:

* <a href="https://www.python.org/downloads/" target="_blank">Installation von Python 3.7</a>
* <a href="https://git-scm.com/" target="_blank">Installation von Git</a>
* Ein Azure-Abonnement. Falls Sie noch kein Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.

## <a name="download-the-sample-locally"></a>Lokales Herunterladen des Beispiels

Führen Sie in einem Terminalfenster die folgenden Befehle aus, um die Beispielanwendung auf Ihren lokalen Computer zu klonen, und navigieren Sie zum Verzeichnis mit dem Beispielcode.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

Das Repository enthält eine Datei vom Typ *application.py*. So weiß App Service, dass das Repository eine Flask-App enthält. Weitere Informationen finden Sie unter [Startprozess und Anpassungen für Container](how-to-configure-python.md).

## <a name="run-the-app-locally"></a>Lokales Ausführen der App

Führen Sie die Anwendung lokal aus, damit Sie sehen, wie sie beim Bereitstellen in Azure aussehen sollte. Öffnen Sie ein Terminalfenster, und verwenden Sie die folgenden Befehle, um die erforderlichen Abhängigkeiten zu installieren und den integrierten Entwicklungsserver zu starten. 

```bash
# In Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py flask run

# In PowerShell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

Öffnen Sie einen Webbrowser, und navigieren Sie zu der Beispielapp auf `http://localhost:5000/`.

Die Nachricht **Hello World!** aus der Beispiel-App wird auf der Seite angezeigt.

![Lokal ausgeführte Beispiel-App](media/quickstart-python/hello-world-in-browser.png)

Drücken Sie in Ihrem Terminalfenster **STRG+C**, um den Webserver zu beenden.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Herunterladen des Beispiels

Erstellen Sie in Cloud Shell ein Schnellstartverzeichnis, und wechseln Sie dorthin.

```bash
mkdir quickstart

cd quickstart
```

Führen Sie als Nächstes den folgenden Befehl aus, um das Beispiel-App-Repository in Ihrem Schnellstartverzeichnis zu klonen.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Während der Ausführung werden Informationen angezeigt, die den Informationen im folgenden Beispiel ähneln:

```bash
Cloning into 'python-docs-hello-world'...
remote: Enumerating objects: 43, done.
remote: Total 43 (delta 0), reused 0 (delta 0), pack-reused 43
Unpacking objects: 100% (43/43), done.
Checking connectivity... done.
```

## <a name="create-a-web-app"></a>Erstellen einer Web-App

Wechseln Sie zum Verzeichnis mit dem Beispielcode, und führen Sie den Befehl `az webapp up` aus.

Ersetzen Sie im folgenden Beispiel `<app-name>` durch einen eindeutigen App-Namen.

```bash
cd python-docs-hello-world

az webapp up -n <app-name>
```

Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen. Während der Ausführung werden Informationen angezeigt, die den Informationen im folgenden Beispiel ähneln:

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app-name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app-name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Navigieren zur App

Navigieren Sie in Ihrem Webbrowser zu der bereitgestellten Anwendung.

```bash
http://<app-name>.azurewebsites.net
```

Der Python-Beispielcode wird in App Service unter Linux mit einem integrierten Image ausgeführt.

![In Azure ausgeführte Beispiel-App](media/quickstart-python/hello-world-in-browser.png)

**Glückwunsch!** Sie haben Ihre erste Python-App für App Service unter Linux bereitgestellt.

## <a name="update-locally-and-redeploy-the-code"></a>Lokales Aktualisieren und erneutes Bereitstellen des Codes

Geben Sie in Cloud Shell `code application.py` ein, um den Cloud Shell-Editor zu öffnen.

![Code application.py](media/quickstart-python/code-applicationpy.png)

 Nehmen Sie eine geringfügige Änderung am Text im Aufruf für `return` vor:

```python
return "Hello Azure!"
```

Speichern Sie Ihre Änderungen, und beenden Sie den Editor. Verwenden Sie `^S` zum Speichern und `^Q` zum Beenden.

Stellen Sie die App mit dem Befehl [`az webapp up`](/cli/azure/ext/webapp/webapp?view=azure-cli-latest.md#ext-webapp-az-webapp-up) erneut bereit. Ersetzen Sie `<app-name>` durch den Namen Ihrer App, und geben Sie für `<location-name>` einen Standort an (indem Sie einen der Werte verwenden, die über den Befehl [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) angezeigt werden).

```bash
az webapp up -n <app-name> -l <location-name>
```

Wechseln Sie nach Abschluss der Bereitstellung wieder zu dem Browserfenster, das im Schritt **Navigieren zur App** geöffnet wurde, und aktualisieren Sie die Seite.

![In Azure ausgeführte aktualisierte Beispiel-App](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Verwalten Ihrer neuen Azure-App

Wechseln Sie zum <a href="https://portal.azure.com" target="_blank">Azure-Portal</a>, um die erstellte App zu verwalten.

Klicken Sie im linken Menü auf **App Services** und anschließend auf den Namen Ihrer Azure-App.

![Portalnavigation zur Azure-App](./media/quickstart-python/app-service-list.png)

Die Übersichtsseite Ihrer App wird angezeigt. Hier können Sie einfache Verwaltungsaufgaben wie Durchsuchen, Beenden, Neustarten und Löschen durchführen.

![App Service-Seite im Azure-Portal](media/quickstart-python/app-service-detail.png)

Im linken Menü werden verschiedene Seiten für die Konfiguration Ihrer App angezeigt. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Python-App mit PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurieren einer Python-App](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Tutorial: Ausführen einer Python-App in einem benutzerdefinierten Container](tutorial-custom-docker-image.md)
