---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: c99cac6626cb40b8fd368e4fc1d8454bb2195521
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424739"
---
## <a name="deploy-the-function-project-to-azure"></a>Bereitstellen des Funktionsprojekts in Azure

Nachdem Sie Ihre Funktions-App in Azure erfolgreich erstellt haben, können Sie nun Ihr lokales Funktionsprojekt bereitstellen, indem Sie den Befehl [func azure functionapp publish](../articles/azure-functions/functions-run-local.md#project-file-deployment) verwenden.  

Ersetzen Sie im folgenden Beispiel `<APP_NAME>` durch den Namen Ihrer App.

```console
func azure functionapp publish <APP_NAME>
```

Mit dem Befehl zum Veröffentlichen (publish) werden Ergebnisse wie in der folgenden (gekürzten) Ausgabe angezeigt:

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>
