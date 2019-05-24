---
title: Includedatei
description: Includedatei
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: dc3fe8375a7f9b43bc866b3f8364bc9b2acb4e50
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66138801"
---
Erstellen Sie eine [Web-App](../articles/app-service/containers/app-service-linux-intro.md) im App Service-Plan `myAppServicePlan`. 

In Cloud Shell können Sie den Befehl [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest) verwenden. Ersetzen Sie im folgenden Beispiel `<app-name>` durch einen global eindeutigen App-Namen (gültige Zeichen sind `a-z`, `0-9` und `-`). Die Runtime ist auf `NODE|6.9` festgelegt. Führen Sie [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) aus, um alle unterstützten Laufzeiten anzuzeigen. 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "NODE|6.9" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "NODE|6.9" --deployment-local-git
```

Nach Erstellung der Web-App zeigt die Azure CLI eine Ausgabe wie im folgenden Beispiel an:

```json
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Sie haben eine leere Web-App mit aktivierter Git-Bereitstellung erstellt.

> [!NOTE]
> Die URL des Git-Remotespeicherorts wird in der `deploymentLocalGitUrl`-Eigenschaft im Format `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git` angezeigt. Speichern Sie diese URL, da Sie sie später noch benötigen.
>
