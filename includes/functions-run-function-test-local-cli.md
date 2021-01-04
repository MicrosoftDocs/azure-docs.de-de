---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 11d426016cfe1a8a9ff843da518f57c08881be5d
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842373"
---
## <a name="run-the-function-locally"></a>Lokales Ausführen der Funktion

1. Führen Sie Ihre Funktion aus, indem Sie den lokalen Azure Functions-Runtimehost im Ordner *LocalFunctionProj* starten:

    ```
    func start
    ```

    Gegen Ende der Ausgabe sollten die folgenden Zeilen angezeigt werden: 
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Sollte „HttpExample“ nicht wie oben dargestellt angezeigt werden, haben Sie den Host wahrscheinlich außerhalb des Stammordners des Projekts gestartet. Drücken Sie in diesem Fall **STRG**+**C**, um den Host zu beenden. Navigieren Sie anschließend zum Stammordner des Projekts, und führen Sie den vorherigen Befehl erneut aus.

1. Kopieren Sie die URL Ihrer `HttpExample`-Funktion aus dieser Ausgabe in einen Browser, und fügen Sie die Abfragezeichenfolge `?name=<YOUR_NAME>` an. Die vollständige URL lautet dann wie folgt: `http://localhost:7071/api/HttpExample?name=Functions`. Im Browser sollte eine Meldung wie `Hello Functions` angezeigt werden:

    ![Ergebnis der lokal im Browser ausgeführten Funktion](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. Im Terminal, in dem Sie Ihr Projekt gestartet haben, wird beim Senden von Anforderungen auch die Protokollausgabe angezeigt.

1. Wenn Sie fertig sind, drücken Sie **STRG**+**C**, und wählen Sie `y` aus, um den Funktionshost zu beenden.
