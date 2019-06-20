---
title: Automatisieren von Azure Application Insights-Prozessen mit Microsoft Flow
description: In diesem Artikel erfahren Sie, wie Sie Microsoft Flow zur schnellen Automatisierung von wiederholbaren Prozessen mit dem Application Insights-Connector verwenden.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mbullwin
ms.openlocfilehash: 15299be83758c157bf3bc7d9fb27b50763b9148e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60903547"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatisieren von Azure Application Insights-Prozessen mit dem Connector für Microsoft Flow

Ertappen Sie sich dabei, wie Sie wiederholt dieselben Abfragen von Ihren Telemetriedaten ausführen, um zu überprüfen, ob Ihr Dienst ordnungsgemäß ausgeführt wird? Möchten Sie diese Abfragen für die Suche nach Trends und Anomalien automatisieren und eigene Workflows dafür erstellen? Dann benötigen Sie den Azure Application Insights-Connector für Microsoft Flow.

Mit dieser Integration können Sie nun zahlreiche Prozesse automatisieren, ohne eine einzige Codezeile zu schreiben. Nachdem Sie einen Flow mit einer Application Insights-Aktion erstellt haben, wird Ihre Application Insights Analytics-Abfrage automatisch ausgeführt. 

Sie können auch weitere Aktionen hinzufügen. Mit Microsoft Flow stehen Ihnen Hunderte von Aktionen zur Verfügung. So können Sie mit Microsoft Flow beispielsweise automatisch eine E-Mail-Benachrichtigung senden oder einen Fehler in Azure DevOps protokollieren. Außerdem können Sie eine der zahlreichen [Vorlagen](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) verwenden, die für den Connector für Microsoft Flow zur Verfügung stehen. Diese Vorlagen beschleunigen den Vorgang zum Erstellen eines Flow. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Erstellen eines Flows für Application Insights

In diesem Tutorial erfahren Sie, wie Sie einen Flow erstellen, der zum Gruppieren von Attributen in den Daten für eine Webanwendung den Autoclusteralgorithmus von Analytics verwendet. Die Ergebnisse werden automatisch per E-Mail gesendet. Dies ist nur eins von vielen Beispielen für die gemeinsame Verwendung von Microsoft Flow und Application Insights Analytics. 

### <a name="step-1-create-a-flow"></a>Schritt 1: Erstellen eines Datenflusses
1. Melden Sie sich bei [Microsoft Flow](https://flow.microsoft.com) an, und wählen Sie **Meine Flows** aus.
2. Klicken Sie auf **Neu** und dann auf **Ohne Vorlage erstellen**.

    ![Neuen Flow ohne Vorlage erstellen](./media/automate-with-flow/1createflow.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>Schritt 2: Erstellen eines Triggers für Ihren Flow
1. Wählen Sie auf der Registerkarte „Integriert“ die Option **Zeitplan** und anschließend **Zeitplan – Wiederholung** aus.

    ![„Zeitplan“ unter „Integriert“ auswählen](./media/automate-with-flow/2schedule.png)

1. Geben Sie im Feld **Intervall** den Wert **1** ein, und wählen Sie im Feld **Häufigkeit** die Option **Tag** aus.
2. Klicken Sie auf **Neuer Schritt**.

    ![Wiederholung des Zeitplans durch Eingabe der Häufigkeit und des Intervalls einrichten](./media/automate-with-flow/3schedulerecurrence.png)


### <a name="step-3-add-an-application-insights-action"></a>Schritt 3: Hinzufügen einer Application Insights-Aktion
1. Suchen Sie nach **Azure Application Insights**.
2. Klicken Sie auf **Azure Application Insights – Analyseabfrage visualisieren**.
 
    ![Aktion auswählen: Azure Application Insights – Analyseabfrage visualisieren](./media/automate-with-flow/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Schritt 4: Herstellen einer Verbindung mit einer Application Insights-Ressource

Für diesen Schritt benötigen Sie eine Anwendungs-ID und einen API-Schlüssel für Ihre Ressource. Diese können Sie über das Azure-Portal abrufen, wie in der folgenden Abbildung zu sehen:

![Anwendungs-ID im Azure-Portal](./media/automate-with-flow/5apiaccess.png)

![API-Schlüssel im Azure-Portal](./media/automate-with-flow/6apikey.png)

- Geben Sie einen Namen für Ihre Verbindung sowie die Anwendungs-ID und den API-Schlüssel ein.

    ![Fenster für die Microsoft Flow-Verbindung](./media/automate-with-flow/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Schritt 5: Angeben der Analytics-Abfrage und des Diagrammtyps
Diese Beispielabfrage wählt die nicht erfolgreichen Anforderungen des letzten Tages aus und gleicht sie mit Ausnahmen ab, die im Zusammenhang mit dem Vorgang aufgetreten sind. Analytics führt den Abgleich auf der Grundlage des Bezeichners „operation_Id“ durch. Die Ergebnisse werden anschließend mithilfe des automatischen Clusteralgorithmus segmentiert. 

Vergewissern Sie sich beim Erstellen eigener Abfragen, dass sie ordnungsgemäß in Analytics funktionieren, bevor Sie sie zu Ihrem Flow hinzufügen.

- Fügen Sie die folgenden Analytics-Abfrage hinzu, und wählen Sie als Diagrammtyp „HTML-Tabelle“. Wählen Sie dann **Neuer Schritt** aus.

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```
    
    ![Konfigurationsfenster für Analytics-Abfragen](./media/automate-with-flow/8query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Schritt 6: Konfigurieren des Flows zum Senden von E-Mails

1. Suchen Sie nach **Office 365 Outlook**.
2. Klicken Sie auf **Office 365 Outlook – E-Mail senden**.

    ![Fenster zum Auswählen von Office 365 Outlook](./media/automate-with-flow/9outlookaction.png)

1. Gehen Sie im Fenster **E-Mail senden** wie folgt vor:

   a. Geben Sie die E-Mail-Adresse des Empfängers ein.

   b. Geben Sie einen Betreff für die E-Mail ein.

   c. Klicken Sie auf das Feld **Text**, und wählen Sie im dynamischen Inhaltsmenü, das auf der rechten Seite geöffnet wird, das Feld **Text** aus.

   d. Klicken Sie auf **Erweiterte Optionen anzeigen**.

    ![Konfiguration von Office 365 Outlook](./media/automate-with-flow/10sendemailbody.png)

1. Gehen Sie im dynamischen Inhaltsmenü wie folgt vor:

    a. Wählen Sie **Anlagenname** aus.

    b. Wählen Sie **Anlageninhalt** aus.
    
    c. Wählen Sie im Feld **Ist HTML** die Option **Ja** aus.

    ![Fenster zum Konfigurieren von Office 365-E-Mails](./media/automate-with-flow/11emailattachment.png)

### <a name="step-7-save-and-test-your-flow"></a>Schritt 7: Speichern und Testen Ihres Flows
- Geben Sie im Feld **Flowname** einen Namen für den Flow ein, und klicken Sie anschließend auf **Speichern**.

    ![Flow benennen und speichern](./media/automate-with-flow/12nameflow.png)

Sie können warten, bis der Trigger die Aktion ausführt, oder den Flow anhand der Schritte unter [Ausführen des Triggers bei Bedarf](https://flow.microsoft.com/blog/run-now-and-six-more-services/) sofort ausführen.

Wenn der Flow ausgeführt wird, erhalten die Empfänger, die Sie in der E-Mail-Liste festgelegt haben, eine E-Mail, die in etwa wie folgt aussieht:

![Beispiel-E-Mail](./media/automate-with-flow/flow9.png)


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die Erstellung von [Analytics-Abfragen](../../azure-monitor/log-query/get-started-queries.md).
- Erfahren Sie mehr über [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





