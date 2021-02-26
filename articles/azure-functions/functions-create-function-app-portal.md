---
title: Erstellen Ihrer ersten Funktion im Azure-Portal
description: Erfahren Sie, wie Sie Ihre erste Azure-Funktion für die serverlose Ausführung mit dem Azure-Portal erstellen.
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-csharp, mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 63e9c87d1d94d6b803c27862bc9f2755e02f3111
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980938"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Erstellen Ihrer ersten Funktion im Azure-Portal

Mit Azure Functions können Sie Ihren Code in einer serverlosen Umgebung ausführen, ohne vorher einen virtuellen Computer zu erstellen oder eine Webanwendung zu veröffentlichen. In diesem Artikel erfahren Sie, wie Sie Azure Functions verwenden können, um eine HTTP-Triggerfunktion „Hello World“ im Azure-Portal zu erstellen.

>[!NOTE]
>Die Bearbeitung im Portal wird nur für JavaScript-, PowerShell-, TypeScript- und C#-Skriptfunktionen unterstützt.<br><br>Für Funktionen der C#-Klassenbibliothek und für Java- und Python-Funktionen können Sie die Funktions-App im Portal erstellen, Sie müssen die Funktionen jedoch auch lokal erstellen und sie dann für Azure veröffentlichen. 

Es wird empfohlen, [Ihre Funktionen stattdessen lokal zu entwickeln](functions-develop-local.md) und in einer Funktions-App in Azure zu veröffentlichen.  
Die folgenden Links bieten Informationen zum Einstieg mit der von Ihnen bevorzugten lokalen Entwicklungsumgebung und Sprache:

| Visual Studio Code | Terminal/Eingabeaufforderung | Visual Studio |
| --- | --- | --- |
|  &bull;&nbsp;[Erste Schritte mit C#](./create-first-function-vs-code-csharp.md)<br/>&bull;&nbsp;[Erste Schritte mit Java](./create-first-function-vs-code-java.md)<br/>&bull;&nbsp;[Erste Schritte mit JavaScript](./create-first-function-vs-code-node.md)<br/>&bull;&nbsp;[Erste Schritte mit PowerShell](./create-first-function-vs-code-powershell.md)<br/>&bull;&nbsp;[Erste Schritte mit Python](./create-first-function-vs-code-python.md) |&bull;&nbsp;[Erste Schritte mit C#](./create-first-function-cli-csharp.md)<br/>&bull;&nbsp;[Erste Schritte mit Java](./create-first-function-cli-java.md)<br/>&bull;&nbsp;[Erste Schritte mit JavaScript](./create-first-function-cli-node.md)<br/>&bull;&nbsp;[Erste Schritte mit PowerShell](./create-first-function-cli-powershell.md)<br/>&bull;&nbsp;[Erste Schritte mit Python](./create-first-function-cli-python.md) | [Erste Schritte mit C#](functions-create-your-first-function-visual-studio.md) |

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-function-app"></a>Erstellen einer Funktionen-App

Sie müssen über eine Funktionen-App verfügen, die die Ausführung Ihrer Funktionen in Azure hostet. Sie können mit einer Funktions-App Funktionen zu logischen Einheiten gruppieren. Dies erleichtert die Verwaltung, Bereitstellung, Skalierung und Freigabe von Ressourcen.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Erstellen Sie als Nächstes eine Funktion in der neuen Funktions-App.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Erstellen einer HTTP-Triggerfunktion

1. Wählen Sie im linken Menü des Fensters **Funktionen** die Option **Funktionen** aus, und wählen Sie dann im obersten Menü **Hinzufügen** aus. 
 
1. Wählen Sie im Fenster **Neue Funktion** die Option **HTTP-Trigger** aus.

    ![Auswählen der HTTP-Triggerfunktion](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. Akzeptieren Sie im Fenster **Neue Funktion** den Standardnamen für **Neue Funktion**, oder geben Sie einen neuen Namen ein. 

1. Wählen Sie **Anonym** in der Dropdownliste **Autorisierungsstufe** aus, und wählen Sie dann **Funktion erstellen** aus.

    Azure erstellt die HTTP-Triggerfunktion. Nun können Sie die neue Funktion ausführen, indem Sie eine HTTP-Anforderung senden.

## <a name="test-the-function"></a>Testen der Funktion

1. Wählen Sie in Ihrer neuen HTTP-Triggerfunktion im Menü auf der linken Seite **Code + testen** aus, und wählen Sie dann im obersten Menü **Funktions-URL abrufen** aus.

    ![Auswählen von „Funktions-URL abrufen“](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. Wählen Sie im Dialogfeld **Funktions-URL abrufen** im Dropdownmenü die Option **default** (Standard) und anschließend das Symbol **In Zwischenablage kopieren** aus. 

    ![Kopieren der URL der Funktion aus dem Azure-Portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Fügen Sie die URL der Funktion in die Adressleiste Ihres Browsers ein. Fügen Sie den Wert der Abfragezeichenfolge `?name=<your_name>` am Ende der URL hinzu, und drücken Sie die EINGABETASTE, um die Anforderung auszuführen. 

    Das folgende Beispiel zeigt die Antwort im Browser:

    ![Funktionsantwort im Browser.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    Wenn die Anforderungs-URL einen [Zugriffsschlüssel](functions-bindings-http-webhook-trigger.md#authorization-keys) (`?code=...`) enthielt, bedeutet dies, dass Sie beim Erstellen der Funktion die Zugriffsebene **Funktion** anstelle der Zugriffsebene **Anonym** auswählen. In diesem Fall sollte stattdessen `&name=<your_name>` angefügt werden.

1. Wenn Ihre Funktion ausgeführt wird, werden Nachverfolgungsinformationen in die Protokolle geschrieben. Um die Nachverfolgungsausgabe anzuzeigen, kehren Sie zur Seite **Code + testen** im Portal zurück, und erweitern Sie den Pfeil **Protokolle** am unteren Rand der Seite.

   ![Viewer der Funktionsprotokolle im Azure-Portal](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
