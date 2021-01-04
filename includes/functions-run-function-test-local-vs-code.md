---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: a3cb0986637d5ce238930fb87aef71fed684097a
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842376"
---
## <a name="run-the-function-locally"></a>Lokales Ausführen der Funktion

Dank der Integration zwischen Visual Studio Code und [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) können Sie dieses Projekt vor der Veröffentlichung in Azure zunächst auf Ihrem lokalen Entwicklungscomputer ausführen.

1. Drücken Sie zum Aufrufen Ihrer Funktion <kbd>F5</kbd>, um das Funktions-App-Projekt zu starten. Die Ausgabe der Core Tools wird im Bereich **Terminal** angezeigt. Sollten bei der Ausführung unter Windows Probleme auftreten, vergewissern Sie sich, dass das Standardterminal für Visual Studio Code nicht auf **WSL Bash** festgelegt ist.

1. Falls Sie Azure Functions Core Tools noch nicht installiert haben, wählen Sie bei entsprechender Aufforderung die Option **Installieren** aus. Wenn die Core Tools installiert sind, wird Ihre App im Bereich **Terminal** gestartet. Der lokal ausgeführte URL-Endpunkt Ihrer über HTTP ausgelösten Funktion wird angezeigt.

    ![VS Code-Ausgabe der lokalen Funktion](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Navigieren Sie zur folgenden URL, während die Core Tools ausgeführt werden, um eine GET-Anforderung mit der Abfragezeichenfolge `?name=Functions` auszuführen.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Die zurückgegebene Antwort sieht in einem Browser in etwa wie folgt aus:

    ![Browser: Localhost-Beispielausgabe](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Informationen zur Anforderung werden in Bereich **Terminal** angezeigt.

    ![Taskhoststart: VS Code-Terminalausgabe](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Drücken Sie <kbd>STRG+C</kbd>, um die Core Tools zu beenden und die Verbindung mit dem Debugger zu trennen.
