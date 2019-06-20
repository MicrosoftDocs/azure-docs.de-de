---
title: 'Serverloses Szenario: Erstellen eines Customer Insights-Dashboards mit Azure-Diensten | Microsoft-Dokumentation'
description: Verwalten von Kundenfeedback, Daten aus sozialen Netzwerken und Ähnlichem durch Erstellen eines Kundendashboards mit Azure Logic Apps und Azure Functions
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: 5dd9d8eac7d65815d7c98a28b0d5af55f19cec47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65464428"
---
# <a name="create-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Erstellen eines Streaming-Customer Insights-Dashboards mit Azure Logic Apps und Azure Functions

Azure bietet [serverlose](https://azure.microsoft.com/solutions/serverless/) Tools, mit denen Sie Apps schnell erstellen und in der Cloud hosten können, ohne sich um die Infrastruktur kümmern zu müssen. In diesem Tutorial können Sie ein Dashboard erstellen, das bei Kundenfeedback ausgelöst wird, Feedback mittels Machine Learning analysiert und Erkenntnisse in einer Quelle wie Power BI oder Azure Data Lake veröffentlicht.

Für diese Lösung verwenden Sie die folgenden Azure-Schlüsselkomponenten für serverlose Apps: [Azure Functions](https://azure.microsoft.com/services/functions/) und [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).
Azure Logic Apps stellt ein serverloses Workflowmodul in der Cloud bereit, sodass Sie Orchestrierungen für serverlose Komponenten erstellen und eine Verbindung mit mehr als 200 Diensten und APIs herstellen können. Azure Functions ermöglicht serverloses Computing in der Cloud. Diese Lösung verwendet Azure Functions, um Kundentweets anhand von vordefinierten Schlüsselwörtern zu kennzeichnen.

In diesem Szenario erstellen Sie eine Logik-App, die ausgelöst wird, wenn Feedback von Kunden gefunden wird. Zu den Connectors, die Sie beim Reagieren auf Kundenfeedback unterstützen, zählen Outlook.com, Office 365, Survey Monkey, Twitter und eine [HTTP-Anforderung aus einem Webformular](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/). Der von Ihnen erstellte Workflow überwacht ein Hashtag auf Twitter.

Sie können [die gesamte Lösung in Visual Studio erstellen](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) und [die Lösung mit einer Azure Resource Manager-Vorlage bereitstellen](../logic-apps/logic-apps-create-deploy-template.md). Eine exemplarische Vorgehensweise zur Erstellung der Lösung wird in [diesem Channel 9-Video](https://aka.ms/logicappsdemo) gezeigt. 

## <a name="trigger-on-customer-data"></a>Auslösung durch Kundendaten

1. Erstellen Sie im Azure-Portal oder in Visual Studio eine leere Logik-App. 

   Falls Sie noch nicht mit Logik-Apps gearbeitet haben, lesen Sie die [Schnellstartanleitung für das Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md) oder [Quickstart: Automate tasks and processes with Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) (Schnellstart: Automatisieren von Aufgaben und Prozessen mit Azure Logic Apps – Visual Studio).

2. Suchen Sie im Logik-App-Designer nach dem Twitter-Trigger mit der folgenden Aktion: **Wenn ein neuer Tweet gepostet wird**

3. Richten Sie den Trigger so ein, dass er basierend auf einem Schlüsselwort oder Hashtag auf Tweets lauscht.

   Bei abrufbasierten Triggern wie dem Twitter-Trigger bestimmt die Serieneigenschaft, wie oft die Logik-App eine Überprüfung auf neue Elemente durchführt.

   ![Beispiel für Twitter-Trigger][1]

Diese Logik-App wird nun bei allen neuen Tweets ausgelöst. Sie können die Tweetdaten anschließend analysieren, um die zum Ausdruck gebrachte Stimmung besser zu verstehen. 

## <a name="analyze-tweet-text"></a>Analysieren von Tweettext

Um die Stimmungslage zu erkennen, die sich hinter einem Text verbirgt, können Sie [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) verwenden.

1. Klicken Sie im Logik-App-Designer unter dem Trigger auf **Neuer Schritt**.

2. Suchen Sie nach dem Connector **Textanalyse**.

3. Wählen Sie die Aktion **Detect Sentiment** (Stimmung erkennen) aus.

4. Geben Sie einen gültigen Cognitive Services-Schlüssel für den Textanalysedienst an, wenn Sie dazu aufgefordert werden.

5. Wählen Sie unter **Anforderungstext** das Feld **Tweettext** aus, das den Tweettext als Eingabe für die Analyse enthält.

Nachdem Sie die Tweetdaten und Erkenntnisse zum Tweet abgerufen haben, können Sie mehrere andere relevante Connectors und deren Aktionen verwenden:

* **Power BI – Zeilen zum Streamingdataset hinzufügen**: Eingehende Tweets in einem Power BI-Dashboard anzeigen
* **Azure Data Lake – Datei anfügen**: Kundendaten zu einem Azure Data Lake-Dataset hinzufügen, um sie in Analyseaufträge einzubinden
* **SQL – Zeilen hinzufügen**: Daten zur späteren Wiederverwendung in einer Datenbank speichern
* **Slack – Nachricht senden**: Einen Slack-Kanal über negatives Feedback benachrichtigen, das möglicherweise eine Aktion erfordert

Sie können auch eine Azure-Funktion erstellen und hinzufügen, um eine benutzerdefinierte Verarbeitung Ihrer Daten auszuführen. 

## <a name="process-data-with-azure-functions"></a>Verarbeiten von Daten mit Azure Functions

Erstellen Sie vor dem Erstellen einer Funktion eine Funktions-App in Ihrem Azure-Abonnement. Darüber hinaus muss die Funktion, damit Ihre Logik-App eine Funktion direkt aufrufen kann, eine HTTP-Triggerbindung aufweisen (verwenden Sie beispielsweise die Vorlage **HttpTrigger**). Erfahren Sie, [wie Sie Ihre erste Funktions-App im Azure-Portal erstellen](../azure-functions/functions-create-first-azure-function-azure-portal.md).

Für dieses Szenario verwenden Sie den Tweettext als Anforderungstext für Ihre Azure-Funktion. Definieren Sie im Funktionscode die Logik, die ermittelt, ob der Tweettext ein Schlüsselwort oder einen Schlüsselbegriff enthält. Verwenden Sie für das Szenario je nach Bedarf eine einfache oder komplexe Funktion.
Geben Sie am Ende der Funktion eine Antwort mit einigen Daten an die Logik-App zurück, beispielsweise einen einfachen booleschen Wert wie `containsKeyword` oder ein komplexes Objekt.

> [!TIP]
> Verwenden Sie die Aktion **JSON analysieren**, um in einer Logik-App auf eine komplexe Antwort einer Funktion zuzugreifen.

Speichern Sie abschließend die Funktion, und fügen Sie sie dann in der Logik-App, die Sie erstellen, als Aktion hinzu.

## <a name="add-azure-function-to-logic-app"></a>Hinzufügen der Azure-Funktion zur Logik-App

1. Klicken Sie im Logik-App-Designer unter der Aktion **Stimmung erkennen** auf **Neuer Schritt**.

2. Suchen Sie nach dem Connector **Azure Functions**, und wählen Sie dann die von Ihnen erstellte Funktion aus.

3. Wählen Sie unter **Anforderungstext** die Option **Tweettext** aus.

![Schritt zum Konfigurieren der Azure-Funktion][2]

## <a name="run-and-monitor-your-logic-app"></a>Ausführen und Überwachen Ihrer Logik-App

Sie können alle aktuellen und vorherigen Ausführungen Ihrer Logik-App mithilfe der umfassenden Debug- und Überwachungsfunktionen überprüfen, die Azure Logic Apps im Azure-Portal, in Visual Studio oder über die Azure-REST-APIs und SDKs bereitstellt.

Sie können Ihre Logik-App ganz einfach testen, indem Sie im Logik-App-Designer auf **Trigger ausführen** klicken. Der Trigger ruft Tweets basierend auf dem angegebenen Zeitplan ab, bis ein mit Ihren Kriterien übereinstimmender Tweet gefunden wird. Während der Ausführung zeigt der Designer eine Liveansicht für diese Ausführung an.

So zeigen Sie einen Verlauf vorheriger Ausführungen in Visual Studio oder im Azure-Portal an: 

* Öffnen Sie Visual Studio Cloud-Explorer. Suchen Sie nach Ihrer Logik-App, und öffnen Sie das Kontextmenü der App. Wählen Sie **Ausführungsverlauf öffnen** aus.

  > [!NOTE]
  > Für Visual Studio 2019 kann der Cloud-Explorer den Logik-App-Designer im Azure-Portal öffnen, jedoch noch nicht den eingebetteten Logik-App-Designer oder den Ausführungsverlauf.

* Suchen Sie im Azure-Portal nach Ihrer Logik-App. Klicken Sie im Menü der Logik-App auf **Übersicht**. 

## <a name="create-automated-deployment-templates"></a>Erstellen von automatisierten Bereitstellungsvorlagen

Nachdem Sie eine Logik-App-Lösung erstellt haben, können Sie die App als [Azure Resource Manager-Vorlage](../azure-resource-manager/resource-group-overview.md#template-deployment) in jeder Azure-Region auf der Welt erfassen und bereitstellen. Mit dieser Funktion können Sie sowohl Parameter für die Erstellung verschiedener Versionen Ihrer App ändern als auch die Lösung in Azure Pipelines integrieren. Sie können Azure Functions auch in Ihre Bereitstellungsvorlage einbinden, damit die gesamte Lösung mit allen Abhängigkeiten als einzelne Vorlage verwaltet werden kann. Erfahren Sie, wie Sie [Logik-App-Bereitstellungsvorlagen erstellen](../logic-apps/logic-apps-create-deploy-template.md).

Ein Beispiel für eine Bereitstellungsvorlage mit einer Azure-Funktion finden Sie im [Repository mit den Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Beispiele und Szenarien für Azure Logic Apps](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png
