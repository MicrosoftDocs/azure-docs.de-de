---
title: Includedatei
description: Includedatei
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 48bb91b3b2e9a31de63e515edb857bc2a170ea79
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132256"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Bereitstellen des Funktions-App-Projekts in Azure

Nach dem Erstellen der Funktions-App in Azure können Sie den Core Tools-Befehl [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) verwenden, um Ihren Projektcode in Azure bereitzustellen. Ersetzen Sie im folgenden Befehl `<APP_NAME>` durch den Namen Ihrer App aus dem vorherigen Schritt.

```bash
func azure functionapp publish <APP_NAME>
```

Es wird in etwa die folgende Ausgabe angezeigt, die zur Verbesserung der Lesbarkeit gekürzt wurde.

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Kopieren Sie den Aufruf-URL-Wert für Ihren „HttpTrigger“, den Sie nun zum Testen Ihrer Funktion in Azure verwenden können. Die URL enthält einen `code`-Abfragezeichenfolgenwert, der Ihr Funktionsschlüssel ist. Dieser Schlüssel erschwert es anderen Benutzern, Ihren HTTP-Triggerendpunkt in Azure aufzurufen.