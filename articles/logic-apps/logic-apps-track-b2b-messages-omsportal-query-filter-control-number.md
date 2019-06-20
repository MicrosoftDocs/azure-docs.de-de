---
title: 'Erstellen von Nachverfolgungsabfragen für B2B-Nachrichten in Azure Monitor-Protokolle: Azure Logic Apps | Microsoft-Dokumentation'
description: Erstellen von Abfragen zum Nachverfolgen von AS2-, X12- und EDIFACT-Nachrichten in Azure Log Analytics für Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: d4a94e75de34bbafd3bc8f1c1a0d1a6817245e5f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60846511"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-monitor-logs-for-azure-logic-apps"></a>Erstellen von Nachverfolgungsabfragen für B2B-Nachrichten in Azure Monitor-Protokolle für Azure Logic Apps

Sie können Abfragen erstellen, die Aktionen auf der Grundlage bestimmter Kriterien filtern, um AS2-, X12- oder EDIFACT-Nachrichten zu finden, die Sie mithilfe von [Azure Monitor-Protokolle](../log-analytics/log-analytics-overview.md) nachverfolgen. So können Sie beispielsweise anhand einer bestimmten Austauschkontrollnummer nach Nachrichten suchen.

> [!NOTE]
> Auf dieser Seite wurden zuvor Schritte zum Ausführen dieser Aufgaben mit der Microsoft Operations Management Suite (OMS) beschrieben. Diese wird [im Januar 2019 eingestellt](../azure-monitor/platform/oms-portal-transition.md), und stattdessen werden Schritte mit Azure Log Analytics angegeben. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Eine Logik-App, für die die Diagnoseprotokollierung eingerichtet ist. Informieren Sie sich über das [Erstellen einer Logik-App](quickstart-create-first-logic-app-workflow.md) und das [Einrichten der Protokollierung für diese Logik-App](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Ein Integrationskonto, für das die Überwachung und Protokollierung eingerichtet ist. Informieren Sie sich über das [Erstellen eines Integrationskontos](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) und das [Einrichten der Überwachung und Protokollierung für dieses Konto](../logic-apps/logic-apps-monitor-b2b-message.md).

* [Veröffentlichen Sie Diagnosedaten in Azure Monitor-Protokolle](../logic-apps/logic-apps-track-b2b-messages-omsportal.md), und [richten Sie die Nachrichtennachverfolgung in Azure Monitor-Protokolle ein](../logic-apps/logic-apps-track-b2b-messages-omsportal.md), sofern noch nicht geschehen.

## <a name="create-queries-with-filters"></a>Erstellen von Abfragen mit Filtern

Um Nachrichten basierend auf bestimmten Eigenschaften oder Werten zu suchen, können Sie Abfragen erstellen, die Filter verwenden. 

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Alle Dienste**. Geben Sie im Suchfeld „log analytics“ ein, und wählen Sie **Log Analytics** aus.

   ![Auswählen von „Log Analytics“](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. Wählen Sie unter **Log Analytics** Ihren Log Analytics-Arbeitsbereich aus. 

   ![Auswählen des Log Analytics-Arbeitsbereichs](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. Wählen Sie in Ihrem Arbeitsbereichsmenü unter **Allgemein** entweder **Protokolle (klassisch)** oder **Protokolle** aus. 

   In diesem Beispiel wird die Verwendung der Ansicht für klassische Protokolle veranschaulicht. 
   Wenn Sie im Abschnitt **Schöpfen Sie das Potenzial von Log Analytics voll aus** unter **Protokolle durchsuchen und analysieren** die Option **Protokolle anzeigen** auswählen, gelangen Sie zur Ansicht **Protokolle (klassisch)** . 

   ![Anzeigen klassischer Protokolle](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. Beginnen Sie im Feld für die Abfragebearbeitung mit der Eingabe des Feldnamens, den Sie suchen möchten. Wenn Sie mit der Eingabe beginnen, werden im Abfrage-Editor mögliche Übereinstimmungen und verfügbare Vorgänge angezeigt. Wählen Sie nach dem Erstellen Ihrer Abfrage **Ausführen**, oder drücken Sie die EINGABETASTE.

   In diesem Beispiel wird nach Übereinstimmungen für **LogicAppB2B** gesucht. 
   Informieren Sie sich über das [Suchen nach Daten in Azure Monitor-Protokolle](../log-analytics/log-analytics-log-searches.md).

   ![Beginnen mit der Eingabe einer Abfragezeichenfolge](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. Wenn Sie den anzuzeigenden Zeitraum ändern möchten, wählen Sie im linken Bereich in der Liste für die Dauer eine Option aus, oder ziehen Sie den Schieberegler. 

   ![Ändern des Zeitraums](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. Wenn Sie der Abfrage einen Filter hinzufügen möchten, wählen Sie **Hinzufügen** aus. 

   ![Hinzufügen eines Filters zur Abfrage](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. Geben Sie unter **Filter hinzufügen** den Filternamen ein, den Sie suchen möchten. Wenn Sie den Filter gefunden haben, wählen Sie ihn aus. Wählen Sie im linken Bereich erneut **Hinzufügen** aus.

   Hier sehen Sie beispielsweise eine andere Abfrage, die nach Ereignissen vom Typ **Type=="AzureDiagnostics"** sucht und durch Auswahl des Filters **event_record_messageProperties_interchangeControlNumber_s** Ergebnisse auf der Grundlage der Austauschkontrollnummer zurückgibt.

   ![Auswählen des Filterwerts](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   Nach dem Auswählen von **Hinzufügen** wird Ihre Abfrage mit dem ausgewählten Filterereignis und -wert aktualisiert. 
   Die vorherigen Ergebnisse werden nun ebenfalls gefiltert. 

   Diese Abfrage sucht beispielsweise nach **Type=="AzureDiagnostics"** und gibt unter Verwendung des Filters **event_record_messageProperties_interchangeControlNumber_s** Ergebnisse zurück, die auf der Austauschkontrollnummer basieren.

   ![Gefilterte Ergebnisse](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>Speichern der Abfrage

Führen Sie zum Speichern Ihrer Abfrage in der Ansicht**Protokolle (klassisch)** die folgenden Schritte aus:

1. Wählen Sie in Ihrer Abfrage auf der Seite **Protokolle (klassisch)** die Option **Analytics** aus. 

   ![Auswählen von „Analytics“](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. Wählen Sie auf der Symbolleiste der Abfrage **Speichern** aus.

   ![Auswählen von „Save“ (Speichern)](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. Geben Sie Details zu Ihrer Abfrage ein. Geben Sie etwa einen Namen für die Abfrage ein, wählen Sie **Abfrage** aus, und geben Sie einen Kategorienamen an. Wenn Sie fertig sind, wählen Sie **Speichern** aus.

   ![Auswählen von „Save“ (Speichern)](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. Gehen Sie zum Anzeigen gespeicherter Abfragen zurück zur Abfrageseite. Wählen Sie auf der Symbolleiste der Abfrage **Gespeicherte Suchvorgänge** aus.

   ![Auswählen von „Gespeicherte Suchvorgänge“](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. Wählen Sie unter **Gespeicherte Suchvorgänge** Ihre Abfrage aus, um die entsprechenden Ergebnisse anzuzeigen. 

   ![Auswählen Ihrer Abfrage](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   Sie können die Abfrage bearbeiten, um andere Ergebnisse zu erhalten.

## <a name="find-and-run-saved-queries"></a>Suchen und Ausführen gespeicherter Abfragen

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Alle Dienste**. Geben Sie im Suchfeld „log analytics“ ein, und wählen Sie **Log Analytics** aus.

   ![Auswählen von „Log Analytics“](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. Wählen Sie unter **Log Analytics** Ihren Log Analytics-Arbeitsbereich aus. 

   ![Auswählen des Log Analytics-Arbeitsbereichs](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. Wählen Sie in Ihrem Arbeitsbereichsmenü unter **Allgemein** entweder **Protokolle (klassisch)** oder **Protokolle** aus. 

   In diesem Beispiel wird die Verwendung der Ansicht für klassische Protokolle veranschaulicht. 

1. Wählen Sie auf der geöffneten Abfrageseite auf der Symbolleiste der Abfrage die Option **Gespeicherte Suchvorgänge** aus.

   ![Auswählen von „Gespeicherte Suchvorgänge“](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. Wählen Sie unter **Gespeicherte Suchvorgänge** Ihre Abfrage aus, um die entsprechenden Ergebnisse anzuzeigen. 

   ![Auswählen Ihrer Abfrage](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   Die Abfrage wird automatisch ausgeführt. Ist dies aus irgendeinem Grund nicht der Fall, wählen Sie im Abfrage-Editor die Option **Ausführen** aus.

## <a name="next-steps"></a>Nächste Schritte

* [AS2-Nachverfolgungsschemas](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-Nachverfolgungsschemas](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Benutzerdefinierte Nachverfolgungsschemas](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)