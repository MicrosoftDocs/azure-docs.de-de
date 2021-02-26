---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 1a0521f76a2cf986f7036d1f701a40a156d16ee7
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493471"
---
## <a name="run-the-function-locally"></a>Lokales Ausführen der Funktion

Dank der Integration zwischen Visual Studio Code und [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) können Sie dieses Projekt vor der Veröffentlichung in Azure zunächst auf Ihrem lokalen Entwicklungscomputer ausführen.

1. Drücken Sie zum Aufrufen Ihrer Funktion <kbd>F5</kbd>, um das Funktions-App-Projekt zu starten. Die Ausgabe der Core Tools wird im Bereich **Terminal** angezeigt. Ihre App wird im **Terminal**-Bereich gestartet. Der lokal ausgeführte URL-Endpunkt Ihrer über HTTP ausgelösten Funktion wird angezeigt.

    ![VS Code-Ausgabe der lokalen Funktion](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

    Sollten bei der Ausführung unter Windows Probleme auftreten, vergewissern Sie sich, dass das Standardterminal für Visual Studio Code nicht auf **WSL Bash** festgelegt ist.

1. Navigieren Sie bei ausgeführten Core Tools zum Bereich **Azure: Functions**. Erweitern Sie unter **Functions** die Option **Lokales Projekt** > **Functions**. Klicken Sie unter Windows mit der rechten Maustaste (unter macOS <kbd>STRG</kbd>+Mausklick) auf die Funktion `HttpExample`, und wählen Sie **Funktion jetzt ausführen...** aus.

    :::image type="content" source="media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="„Funktion jetzt ausführen“ in Visual Studio Code":::

1. Unter **Enter request body** (Anforderungstext eingeben) wird `{ "name": "Azure" }` als Wert für den Text der Anforderungsnachricht angezeigt. Drücken Sie die EINGABETASTE, um diese Anforderungsnachricht an Ihre Funktion zu senden.  

1. Wenn die Funktion lokal ausgeführt wird und eine Antwort zurückgibt, wird in Visual Studio Code eine Benachrichtigung ausgelöst. Informationen zur Funktionsausführung werden im **Terminal**-Bereich angezeigt.

1. Drücken Sie <kbd>STRG+C</kbd>, um die Core Tools zu beenden und die Verbindung mit dem Debugger zu trennen.
