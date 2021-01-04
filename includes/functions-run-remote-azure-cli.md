---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d1931614356a313334d712713965346e843a403d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424769"
---
## <a name="invoke-the-function-on-azure"></a>Aufrufen der Funktion in Azure

Da für Ihre Funktion ein HTTP-Trigger verwendet wird, führen Sie das Aufrufen durch, indem Sie per Browser eine HTTP-Anforderung an die entsprechende URL senden oder ein Tool wie curl verwenden. 

# <a name="browser"></a>[Browser](#tab/browser)

Kopieren Sie die vollständige **Aufruf-URL** , die in der Ausgabe des Befehls zum Veröffentlichen (publish) angezeigt wird, in eine Browseradressleiste, und fügen Sie den Abfrageparameter `&name=Functions` an. Im Browser sollte eine ähnliche Ausgabe wie bei der lokalen Ausführung der Funktion angezeigt werden.

![Ausgabe der in Azure ausgeführten Funktion in einem Browser](./media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Führen Sie [`curl`](https://curl.haxx.se/) mit der **Aufruf-URL** aus, und fügen Sie den Parameter `&name=Functions` an. Die Ausgabe des Befehls sollte der Text „Hello Functions“ sein.

![Ausgabe der in Azure per curl ausgeführten Funktion](./media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---
