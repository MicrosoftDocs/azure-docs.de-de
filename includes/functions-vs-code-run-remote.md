---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 55a75651b724a4fe975f655958e36fbd40e35db7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96748237"
---
## <a name="run-the-function-in-azure"></a>Ausführen der Funktion in Azure

1. Zurück im Bereich **Azure: Funktionen** auf der Seitenleiste die neue Funktions-App unter Ihrem Abonnement. Erweitern Sie **Funktionen**, klicken Sie mit der rechten Maustaste (Windows), bzw. klicken Sie bei gedrückter <kbd>CTRL-TASTE</kbd> (macOS) auf **HttpExample**, und wählen Sie **Copy function URL** (Funktions-URL kopieren) aus.

    ![Kopieren Sie die Funktions-URL für den neuen HTTP-Trigger.](./media/functions-vs-code-run-remote/function-copy-endpoint-url.png)

1. Fügen Sie diese URL für die HTTP-Anforderung in die Adressleiste Ihres Browsers ein, fügen Sie die Abfragezeichenfolge `name` als `?name=Functions` am Ende der URL hinzu, und führen Sie dann die Anforderung aus. Die URL, über die Ihre per HTTP ausgelöste Funktion aufgerufen wird, sollte das folgende Format haben:

    ```http
    http://<FUNCTION_APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions
    ```

    Das folgende Beispiel zeigt die von der Funktion im Browser zurückgegebene Antwort auf die GET-Remoteanforderung:

    ![Funktionsantwort im Browser](./media/functions-vs-code-run-remote/functions-test-remote-browser.png)
