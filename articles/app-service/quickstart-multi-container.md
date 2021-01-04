---
title: 'Schnellstart: Erstellen einer App mit mehreren Containern'
description: Führen Sie erste Schritte mit Apps mit mehreren Containern in Azure App Service aus, indem Sie Ihre erste App mit mehreren Containern bereitstellen.
keywords: Azure App Service, Web-App, Linux, Docker, Compose, mehrere Container, Web-App für Container, Container, Wordpress, Azure Database for MySQL, Produktionsdatenbank mit Containern
author: msangapu-msft
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 2ba42e5e800ae607631e00aee50954bf2638ae43
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007136"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Erstellen einer App mit mehreren Containern (Vorschauversion) mithilfe einer Docker Compose-Konfiguration

> [!NOTE]
> Apps mit mehreren Containern befinden sich in der Vorschauphase.

[Web-App für Container](overview.md#app-service-on-linux) umfasst eine flexible Möglichkeit zum Verwenden von Docker-Images. In dieser Schnellstartanleitung wird veranschaulicht, wie Sie eine App mit mehreren Containern (Vorschauversion) für Web-App für Container in [Cloud Shell](../cloud-shell/overview.md) per Docker Compose-Konfiguration bereitstellen.

![Beispiel-App mit mehreren Containern in Web-App für Container][1]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Für diesen Artikel ist mindestens Version 2.0.32 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="download-the-sample"></a>Herunterladen des Beispiels

Für diesen Schnellstart verwenden Sie die Compose-Datei von [Docker](https://docs.docker.com/compose/wordpress/#define-the-project). Die Konfigurationsdatei finden Sie in den [Beispielen für Azure](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

Erstellen Sie in Cloud Shell ein Schnellstartverzeichnis, und wechseln Sie dorthin.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Führen Sie als Nächstes den folgenden Befehl aus, um das Beispiel-App-Repository in Ihrem Schnellstartverzeichnis zu klonen. Wechseln Sie anschließend in das Verzeichnis `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Erstellen Sie in Cloud Shell mit dem Befehl [`az group create`](/cli/azure/group#az-group-create) eine Ressourcengruppe. Im folgenden Beispiel wird am Standort *South Central US* (USA, Süden-Mitte) die Ressourcengruppe *myResourceGroup* erstellt. Wenn Sie alle unterstützten Standorte für App Service unter Linux im **Standard**-Tarif anzeigen möchten, führen Sie den Befehl [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice#az-appservice-list-locations) aus.

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Im Allgemeinen erstellen Sie Ressourcengruppen und Ressourcen in einer Region in Ihrer Nähe.

Nach Ausführung dieses Befehls werden die Ressourcengruppeneigenschaften in einer JSON-Ausgabe angezeigt.

## <a name="create-an-azure-app-service-plan"></a>Erstellen eines Azure App Service-Plans

Erstellen Sie in Cloud Shell mit dem Befehl [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) einen App Service-Plan in der Ressourcengruppe.

Im folgenden Beispiel wird ein App Service-Plan mit dem Namen `myAppServicePlan` in einem Linux-Container (`--is-linux`) für den Tarif **Standard** (`--sku S1`) erstellt.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Nach Erstellung des App Service-Plans zeigt die Azure-Befehlszeilenschnittstelle Informationen wie im folgenden Beispiel an:

<pre>
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
</pre>

## <a name="create-a-docker-compose-app"></a>Erstellen einer Docker Compose-App

> [!NOTE]
> Docker Compose unter Azure App Service verfügt derzeit über ein Limit von 4.000 Zeichen.

Erstellen Sie im Cloud Shell-Terminal eine [Web-App](overview.md#app-service-on-linux) mit mehreren Containern im App Service-Plan `myAppServicePlan`, indem Sie den Befehl [az webapp create](/cli/azure/webapp#az-webapp-create) verwenden. Vergessen Sie nicht, _\<app_name>_ durch einen eindeutigen App-Namen zu ersetzen (gültige Zeichen sind `a-z`, `0-9` und `-`).

```azurecli
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Nach Erstellung der Web-App zeigt die Azure CLI eine Ausgabe wie im folgenden Beispiel an:

<pre>
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="browse-to-the-app"></a>Navigieren zur App

Navigieren Sie zur bereitgestellten App unter `http://<app_name>.azurewebsites.net`. Das Laden der App kann einige Minuten dauern. Wenn Sie einen Fehler erhalten, sollten Sie einige Minuten warten und dann den Browser aktualisieren.

![Beispiel-App mit mehreren Containern in Web-App für Container][1]

**Glückwunsch!** Sie haben in Web-App für Container eine App mit mehreren Containern erstellt.

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: WordPress-App mit mehreren Containern](tutorial-multi-container-app.md)

> [!div class="nextstepaction"]
> [Konfigurieren eines benutzerdefinierten Containers](configure-custom-container.md)

<!--Image references-->
[1]: media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
