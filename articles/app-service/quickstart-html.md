---
title: 'Schnellstart: Erstellen einer statischen HTML-Web-App'
description: Stellen Sie in wenigen Minuten Ihre erste HTML-App vom Typ „Hallo Welt“ in Azure App Service bereit. Die Bereitstellung erfolgt mithilfe von Git. Dies ist eine von zahlreichen verschiedenen Möglichkeiten für die Bereitstellung in App Service.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ms.openlocfilehash: ec3929219a6297bd4a3d8d3e0eff93d2ed847ba0
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835219"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Erstellen einer statischen HTML-Web-App in Azure

Von [Azure App Service](overview.md) wird ein hochgradig skalierbarer Webhostingdienst mit Self-Patching bereitgestellt. In dieser Schnellstartanleitung wird erläutert, wie Sie eine einfache Website mit HTML und CSS in Azure App Service bereitstellen. In dieser Schnellstartanleitung wird [Cloud Shell](../cloud-shell/overview.md) verwendet. Die Befehle können aber auch lokal über die [Azure CLI](/cli/azure/install-azure-cli) ausgeführt werden.

![Startseite der Beispiel-App](media/quickstart-html/hello-world-in-browser-az.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Herunterladen des Beispiels

Erstellen Sie in Cloud Shell ein Schnellstartverzeichnis, und wechseln Sie dorthin.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Führen Sie als Nächstes den folgenden Befehl aus, um das Beispiel-App-Repository in Ihrem Schnellstartverzeichnis zu klonen.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="create-a-web-app"></a>Erstellen einer Web-App

Wechseln Sie zum Verzeichnis mit dem Beispielcode, und führen Sie den Befehl `az webapp up` aus. Ersetzen Sie im folgenden Beispiel „<app_name>“ durch einen eindeutigen App-Namen. Statischer Inhalt wird durch das Flag `--html` angegeben.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

Der Befehl `az webapp up` bewirkt Folgendes:

- Erstellen einer Standardressourcengruppe

- Erstellen eines standardmäßigen App Service-Plans

- Erstellen einer App mit dem angegebenen Namen

- [Bereitstellen von ZIP-Dateien](./deploy-zip.md) aus dem aktuellen Arbeitsverzeichnis für die Web-App

Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen. Während der Ausführung werden Informationen angezeigt, die den Informationen im folgenden Beispiel ähneln:

<pre>
{
  "app_url": "https://&lt;app_name&gt;.azurewebsites.net",
  "location": "westeurope",
  "name": "&lt;app_name&gt;",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/&lt;username&gt;/quickstart/html-docs-hello-world ",
  &lt; JSON data removed for brevity. &gt;
}
</pre>

Notieren Sie sich den Wert für `resourceGroup`. Sie benötigen ihn im Abschnitt [Bereinigen von Ressourcen](#clean-up-resources).

## <a name="browse-to-the-app"></a>Navigieren zur App

Wechseln Sie in einem Browser zur URL der App: `http://<app_name>.azurewebsites.net`.

Die Seite wird als Azure App Service-Web-App ausgeführt.

![Startseite der Beispiel-App](media/quickstart-html/hello-world-in-browser-az.png)

**Glückwunsch!** Sie haben Ihre erste HTML-App für App Service bereitgestellt.

## <a name="update-and-redeploy-the-app"></a>Aktualisieren und erneutes Bereitstellen der App

Geben Sie in Cloud Shell `nano index.html` ein, um den Nano-Text-Editor zu öffnen. Ändern Sie im Tag der Überschrift `<h1>` „Azure App Service – Statische HTML-Beispielsite“ in „Azure App Service“ wie nachfolgend gezeigt:

![Nano index.html](media/quickstart-html/nano-index-html.png)

Speichern Sie Ihre Änderungen, und beenden Sie Nano. Verwenden Sie `^O` zum Speichern und `^X` zum Beenden.

Nun stellen Sie die App mit dem gleichen `az webapp up`-Befehl erneut bereit.

```bash
az webapp up --location westeurope --name <app_name> --html
```

Wechseln Sie nach Abschluss der Bereitstellung wieder zu dem Browserfenster, das im Schritt **Navigieren zur App** geöffnet wurde, und aktualisieren Sie die Seite.

![Aktualisierte Startseite der Beispiel-App](media/quickstart-html/hello-azure-in-browser-az.png)

## <a name="manage-your-new-azure-app"></a>Verwalten Ihrer neuen Azure-App

Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **App Services**, und wählen Sie diese Option aus, um die von Ihnen erstellte Web-App zu verwalten. 

![Auswählen von „App Services“ im Azure-Portal](./media/quickstart-html/portal0.png)

Wählen Sie auf der Seite **App Services** den Namen Ihrer Azure-App aus.

![Portalnavigation zur Azure-App](./media/quickstart-html/portal1.png)

Die Übersichtsseite Ihrer Web-App wird angezeigt. Hier können Sie einfache Verwaltungsaufgaben wie Durchsuchen, Beenden, Neustarten und Löschen durchführen.

![App Service-Blatt im Azure-Portal](./media/quickstart-html/portal2.png)

Im linken Menü werden verschiedene Seiten für die Konfiguration Ihrer App angezeigt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Schritten haben Sie Azure-Ressourcen in einer Ressourcengruppe erstellt. Wenn Sie diese Ressourcen in Zukunft nicht mehr benötigen, löschen Sie die Ressourcengruppe, indem Sie den folgenden Befehl in Cloud Shell ausführen. Denken Sie daran, dass der Name der Ressourcengruppe im Schritt [Erstellen einer Web-App](#create-a-web-app) automatisch für Sie generiert wurde.

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

Die Ausführung dieses Befehls kann eine Minute in Anspruch nehmen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Zuordnen einer benutzerdefinierten Domäne](app-service-web-tutorial-custom-domain.md)
