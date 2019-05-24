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
ms.openlocfilehash: 20dfc2413ed437fe6b08797c9c91acb5498dca23
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66138593"
---
Erstellen Sie eine [Web-App](../articles/app-service/containers/app-service-linux-intro.md) im App Service-Plan `myAppServicePlan`. 

In Cloud Shell können Sie den Befehl [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) verwenden. Ersetzen Sie im folgenden Beispiel `<app-name>` durch einen global eindeutigen App-Namen (gültige Zeichen sind `a-z`, `0-9` und `-`). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-local-git
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

> [!NOTE]
> Die URL des Git-Remotespeicherorts wird in der `deploymentLocalGitUrl`-Eigenschaft im Format `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git` angezeigt. Speichern Sie diese URL, da Sie sie später noch benötigen.
>
