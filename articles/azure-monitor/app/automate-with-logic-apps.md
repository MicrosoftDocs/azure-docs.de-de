---
title: Automatisieren von Azure Application Insights-Prozessen mithilfe von Logic Apps
description: In diesem Artikel erfahren Sie, wie Sie wiederholbare Prozesse schnell automatisieren können, indem Sie Ihrer Logik-App den Application Insights-Connector hinzufügen.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: mbullwin
ms.openlocfilehash: 61215adc2aee5cef3693d119bf0efb36526d748b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60904352"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatisieren von Application Insights-Prozessen mithilfe von Logic Apps

Ertappen Sie sich dabei, wie Sie wiederholt dieselben Abfragen von Ihren Telemetriedaten ausführen, um zu überprüfen, ob Ihr Dienst ordnungsgemäß funktioniert? Möchten Sie diese Abfragen für die Suche nach Trends und Anomalien automatisieren und eigene Workflows dafür erstellen? Dann benötigen Sie den Azure Application Insights-Connector für Logic Apps.

Mit dieser Integration können Sie zahlreiche Prozesse automatisieren, ohne eine einzige Codezeile zu schreiben. Mit dem Application Insights-Connector können Sie Logik-Apps erstellen und schnell beliebige Application Insights-Prozesse automatisieren. 

Sie können auch weitere Aktionen hinzufügen. Das Logic Apps-Feature von Azure App Service ermöglicht Hunderte von Aktionen. So können Sie mit einer Logik-App beispielsweise automatisch eine E-Mail-Benachrichtigung senden oder einen Fehler in Azure DevOps protokollieren. Darüber hinaus können Sie eine der zahlreichen verfügbaren [Vorlagen](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) verwenden, um die Erstellung Ihrer Logik-App zu beschleunigen. 

## <a name="create-a-logic-app-for-application-insights"></a>Erstellen einer Logik-App für Application Insights

In diesem Tutorial erfahren Sie, wie Sie eine Logik-App erstellen, die den automatischen Clusteralgorithmus von Analytics verwendet, um Attribute in den Daten für eine Webanwendung zu gruppieren. Die Ergebnisse werden automatisch per E-Mail gesendet. Dies ist nur eins von vielen Beispielen für die gemeinsame Verwendung von Application Insights Analytics und Logic Apps. 

### <a name="step-1-create-a-logic-app"></a>Schritt 1: Erstellen einer Logik-App
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im Bereich **Ressource erstellen** die Kategorie **Web + Mobil** und anschließend **Logik-App** aus.

    ![Fenster für eine neue Logik-App](./media/automate-with-logic-apps/1createlogicapp.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Schritt 2: Erstellen eines Triggers für Ihre Logik-App
1. Wählen Sie im Fenster **Logik-App-Designer** unter **Starten Sie mit einem gängigen Trigger** die Option **Wiederholung** aus.

    ![Fenster „Logik-App-Designer“](./media/automate-with-logic-apps/2logicappdesigner.png)

1. Geben Sie im Feld **Intervall** den Wert **1** ein, und wählen Sie anschließend im Feld **Häufigkeit** die Option **Tag** aus.

    ![Logik-App-Designer-Fenster „Wiederholung“](./media/automate-with-logic-apps/3recurrence.png)

### <a name="step-3-add-an-application-insights-action"></a>Schritt 3: Hinzufügen einer Application Insights-Aktion
1. Klicken Sie auf **Neuer Schritt**.

1. Geben Sie im Suchfeld **Aktion auswählen** den Text **Azure Application Insights** ein.

1. Klicken Sie unter **Aktionen** auf **Azure Application Insights – Analytics-Abfrage visualisieren**.

    ![Logik-App-Designer-Fenster „Aktion auswählen“](./media/automate-with-logic-apps/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Schritt 4: Herstellen einer Verbindung mit einer Application Insights-Ressource

Für diesen Schritt benötigen Sie eine Anwendungs-ID und einen API-Schlüssel für Ihre Ressource. Diese können Sie über das Azure-Portal abrufen, wie in der folgenden Abbildung zu sehen:

![Anwendungs-ID im Azure-Portal](./media/automate-with-logic-apps/5apiaccess.png)

![Anwendungs-ID im Azure-Portal](./media/automate-with-logic-apps/6apikey.png)

Geben Sie einen Namen für Ihre Verbindung sowie die Anwendungs-ID und den API-Schlüssel ein.

![Verbindungsfenster für den Logik-App-Designer-Flow](./media/automate-with-logic-apps/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Schritt 5: Angeben der Analytics-Abfrage und des Diagrammtyps
Im folgenden Beispiel wählt die Abfrage die nicht erfolgreichen Anforderungen des letzten Tages aus und gleicht sie mit Ausnahmen ab, die im Zusammenhang mit dem Vorgang aufgetreten sind. Analytics gleicht die nicht erfolgreichen Anforderungen auf der Grundlage des Bezeichners „operation_Id“ ab. Die Ergebnisse werden anschließend mithilfe des automatischen Clusteralgorithmus segmentiert. 

Vergewissern Sie sich beim Erstellen eigener Abfragen, dass sie ordnungsgemäß in Analytics funktionieren, bevor Sie sie zu Ihrem Flow hinzufügen.

1. Fügen Sie im Feld **Abfrage** die folgende Analytics-Abfrage hinzu:

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

1. Wählen Sie im Feld **Diagrammtyp** die Option **HTML-Tabelle** aus.

    ![Konfigurationsfenster für Analytics-Abfragen](./media/automate-with-logic-apps/8query.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Schritt 6: Konfigurieren der Logik-App zum Senden von E-Mails

1. Klicken Sie auf **Neuer Schritt**.

1. Geben Sie im Suchfeld den Text **Office 365 Outlook** ein.

1. Klicken Sie auf **Office 365 Outlook – E-Mail senden**.

    ![Auswählen von Office 365 Outlook](./media/automate-with-logic-apps/9sendemail.png)

1. Gehen Sie im Fenster **E-Mail senden** wie folgt vor:

   a. Geben Sie die E-Mail-Adresse des Empfängers ein.

   b. Geben Sie einen Betreff für die E-Mail ein.

   c. Klicken Sie auf das Feld **Text**, und wählen Sie im dynamischen Inhaltsmenü, das auf der rechten Seite geöffnet wird, das Feld **Text** aus.
    
   d. Klicken Sie auf die Dropdownliste **Neuen Parameter hinzufügen**, und wählen Sie „Anhänge“ und „Ist HTML“ aus.

      ![Konfiguration von Office 365 Outlook](./media/automate-with-logic-apps/10emailbody.png)

      ![Konfiguration von Office 365 Outlook](./media/automate-with-logic-apps/11emailparameter.png)

1. Gehen Sie im dynamischen Inhaltsmenü wie folgt vor:

    a. Wählen Sie **Anlagenname** aus.

    b. Wählen Sie **Anlageninhalt** aus.
    
    c. Wählen Sie im Feld **Ist HTML** die Option **Ja** aus.

      ![Bildschirm „Konfiguration von Office 365-E-Mails“](./media/automate-with-logic-apps/12emailattachment.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Schritt 7: Speichern und Testen Ihrer Logik-App
* Klicken Sie zum Speichern der Änderungen auf **Speichern**.

Sie können warten, bis die Logik-App durch den Trigger ausgeführt wird, oder **Ausführen** auswählen, um sie sofort auszuführen.

![Bildschirm für die Logik-App-Erstellung](./media/automate-with-logic-apps/13save.png)

Wenn Ihre Logik-App ausgeführt wird, erhalten die Empfänger, die Sie in der E-Mail-Liste festgelegt haben, eine E-Mail, die in etwa wie folgt aussieht:

![E-Mail der Logik-App](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die Erstellung von [Analytics-Abfragen](../../azure-monitor/log-query/get-started-queries.md).
- Informieren Sie sich ausführlicher über [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->





