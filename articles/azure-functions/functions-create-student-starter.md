---
title: Erstellen einer Funktion mit Microsoft Azure for Students Starter
description: Es wird beschrieben, wie Sie unter einem Azure for Students Starter-Abonnement eine Azure-Funktion erstellen.
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: f40405c9325743da5d1963e3baea781606d2d8d9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182528"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Erstellen einer Funktion mit Microsoft Azure for Students Starter

In diesem Tutorial erstellen Sie eine HTTP-Funktion vom Typ „Hello World“ in einem Microsoft Azure for Students Starter-Abonnement. Außerdem wird einzeln beschrieben, welche Optionen in Azure Functions für diesen Abonnementtyp verfügbar sind.

Microsoft *Azure for Students Starter* unterstützt Sie bei Ihren ersten Schritten mit Azure-Produkten für die Entwicklung in der Cloud – und das kostenlos. [Erfahren Sie mehr zu diesem Angebot.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Mit Azure Functions können Sie Code in einer [serverlosen](https://azure.microsoft.com/solutions/serverless/) Umgebung ausführen, ohne vorher eine VM erstellen oder eine Webanwendung veröffentlichen zu müssen. [Erfahren Sie mehr zu Functions.](./functions-overview.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function"></a>Erstellen einer Funktion

 In diesem Artikel erfahren Sie, wie Sie Azure Functions verwenden können, um eine über HTTP ausgelöste Funktion „Hello world“ im Azure-Portal zu erstellen.

![Erstellen einer Funktionen-App im Azure-Portal](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-function-app"></a>Erstellen einer Funktionen-App

Sie müssen über eine Funktionen-App verfügen, die die Ausführung Ihrer Funktionen in Azure hostet. Sie können mit einer Funktions-App Funktionen zu logischen Einheiten gruppieren. Dies erleichtert die Verwaltung, Bereitstellung, Skalierung und Freigabe von Ressourcen.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Erstellen Sie als Nächstes eine Funktion in der neuen Funktions-App.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Erstellen einer HTTP-Triggerfunktion

1. Wählen Sie im linken Menü des Fensters **Funktionen** die Option **Funktionen** aus, und wählen Sie dann im obersten Menü **Hinzufügen** aus. 
 
1. Wählen Sie im Fenster **Neue Funktion** die Option **HTTP-Trigger** aus.

    ![Auswählen der HTTP-Triggerfunktion](./media/functions-create-student-starter/function-app-select-http-trigger.png)

1. Akzeptieren Sie im Fenster **Neue Funktion** den Standardnamen für **Neue Funktion**, oder geben Sie einen neuen Namen ein. 

1. Wählen Sie **Anonym** in der Dropdownliste **Autorisierungsstufe** aus, und wählen Sie dann **Funktion erstellen** aus.

    Azure erstellt die HTTP-Triggerfunktion. Nun können Sie die neue Funktion ausführen, indem Sie eine HTTP-Anforderung senden.

## <a name="test-the-function"></a>Testen der Funktion

1. Wählen Sie in Ihrer neuen HTTP-Triggerfunktion im Menü auf der linken Seite **Code + testen** aus, und wählen Sie dann im obersten Menü **Funktions-URL abrufen** aus.

    ![Auswählen von „Funktions-URL abrufen“](./media/functions-create-student-starter/function-app-select-get-function-url.png)

1. Wählen Sie im Dialogfeld **Funktions-URL abrufen** im Dropdownmenü die Option **default** (Standard) und anschließend das Symbol **In Zwischenablage kopieren** aus. 

    ![Kopieren der URL der Funktion aus dem Azure-Portal](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

1. Fügen Sie die URL der Funktion in die Adressleiste Ihres Browsers ein. Fügen Sie den Wert der Abfragezeichenfolge `?name=<your_name>` am Ende der URL hinzu, und drücken Sie die EINGABETASTE, um die Anforderung auszuführen. 

    Das folgende Beispiel zeigt die Antwort im Browser:

    ![Funktionsantwort im Browser.](./media/functions-create-student-starter/function-app-browser-testing.png)

    Die Anfrage-URL enthält einen Schlüssel, der standardmäßig über HTTP auf Ihre Funktion zugreifen muss.

1. Wenn Ihre Funktion ausgeführt wird, werden Nachverfolgungsinformationen in die Protokolle geschrieben. Um die Nachverfolgungsausgabe anzuzeigen, kehren Sie zur Seite **Code + testen** im Portal zurück, und erweitern Sie den Pfeil **Protokolle** am unteren Rand der Seite.

   ![Viewer der Funktionsprotokolle im Azure-Portal](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Unterstützte Funktionen in Microsoft Azure for Students Starter

In Microsoft Azure for Students Starter haben Sie Zugriff auf die meisten Features der Azure Functions-Runtime. Die wichtigsten Einschränkungen sind nachfolgend aufgeführt:

* Der HTTP-Trigger ist der einzige unterstützte Triggertyp.
    * Alle Eingabe- und Ausgabebindungen werden unterstützt! Die vollständige Liste finden Sie [hier](functions-triggers-bindings.md).
* Unterstützte Sprachen: 
    * C# (.NET Core 2)
    * JavaScript (Node.js 8 und 10)
    * F# (.NET Core 2)
    * [Informationen zu Sprachen, die für höhere Pläne unterstützt werden](supported-languages.md)
* Windows ist das einzige unterstützte Betriebssystem.
* Die Skalierung ist auf [eine Instanz vom Typ „Free-Tarif“](https://azure.microsoft.com/pricing/details/app-service/windows/) beschränkt, die pro Tag bis zu 60 Minuten lang ausgeführt werden kann. Sie können automatisch serverlos von 0 Instanzen auf 1 Instanz skalieren, wenn HTTP-Datenverkehr empfangen wird (aber nicht höher).
* Die Functions-Runtime wird erst [ab Version 2.x](functions-versions.md) unterstützt.
* Für Bearbeitungs- und Veröffentlichungsfunktionen werden alle Entwicklertools unterstützt. Dies umfasst VS Code, Visual Studio, die Azure CLI und das Azure-Portal. Falls Sie etwas anderes als das Portal nutzen möchten, müssen Sie zuerst im Portal eine App erstellen und diese dann in Ihrem bevorzugten Tool als Bereitstellungsziel auswählen.

## <a name="next-steps"></a>Nächste Schritte

Sie haben das Erstellen einer Funktions-App mit einer einfachen HTTP-Triggerfunktion abgeschlossen. Nun können Sie die lokalen Tools, weitere Sprachen, die Überwachung und Integrationen erkunden.

 * [Erstellen Ihrer ersten Funktion mit Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Erstellen Ihrer ersten Funktion mit Visual Studio Code](./create-first-function-vs-code-csharp.md)
 * [JavaScript-Entwicklerhandbuch für Azure Functions](./functions-reference-node.md)
 * [Verwenden von Azure Functions zum Herstellen einer Verbindung mit einer Azure SQL-Datenbank-Instanz](./functions-scenario-database-table-cleanup.md)
 * [Erfahren Sie mehr zu HTTP-Bindungen von Azure Functions](./functions-bindings-http-webhook.md).
 * [Überwachen von Azure Functions](./functions-monitoring.md)