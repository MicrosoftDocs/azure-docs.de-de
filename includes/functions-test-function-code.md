---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: a3f75b7273164abc5318f16e9ab8d9883ff0c0aa
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132107"
---
## <a name="test"></a>Testen der Funktion in Azure

Testen Sie die bereitgestellte Funktion mit cURL. Fügen Sie unter Verwendung der URL, die Sie im vorherigen Schritt kopiert haben, die Abfragezeichenfolge `&name=<yourname>` an die URL an, wie im folgenden Beispiel gezeigt:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

![Verwenden von cURL zum Aufrufen der Funktion in Azure](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

Sie können auch die kopierte URL in die Adressleiste des Webbrowsers einfügen. Hängen Sie die Abfragezeichenfolge `&name=<yourname>` erneut an die URL an, bevor Sie die Anforderung ausführen.

![Verwenden eines Webbrowsers zum Aufrufen der Funktion](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
