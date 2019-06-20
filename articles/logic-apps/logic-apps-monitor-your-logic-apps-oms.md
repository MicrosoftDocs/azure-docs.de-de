---
title: Überwachen von Logik-Apps mit Azure Monitor-Protokollen – Azure Logic Apps | Microsoft-Dokumentation
description: Sammeln Sie mit Azure Log Analytics Erkenntnisse und Debuggingdaten für die Problembehandlung und Diagnose Ihrer Logik-App-Ausführungen.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 3f890e6cabd757fdd38374befaaccd1a10c9bd96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62106209"
---
# <a name="monitor-logic-apps-with-azure-monitor-logs"></a>Überwachen von Logik-Apps mit Azure Monitor-Protokollen

Um Ihre Logik-Apps zu überwachen und umfassendere Details über sie zu erhalten, aktivieren Sie [Azure Monitor-Protokolle](../log-analytics/log-analytics-overview.md), wenn Sie die Logik-App erstellen. Azure Monitor-Protokolle bieten Diagnoseprotokollierung und -überwachung für Ihre Logik-Apps, wenn Sie die Lösung Logic Apps-Verwaltung im Azure-Portal installieren. Diese Lösung bietet auch aggregierte Informationen für Ihre Logik-App-Ausführungen mit spezifischen Details wie Status, Ausführungszeit, Status der erneuten Übermittlung und Korrelations-IDs. In diesem Artikel wird veranschaulicht, wie Sie Azure Monitor-Protokolle aktivieren, um Laufzeitereignisse und Daten für Ihre Logik-App-Ausführung anzuzeigen.

Führen Sie diese Schritte zum [Aktivieren der Diagnoseprotokollierung und Senden von Logik-App-Laufzeitdaten an Azure Monitor-Protokolle](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics) aus, um Azure Monitor-Protokolle für vorhandene Logik-Apps zu aktivieren.

> [!NOTE]
> Auf dieser Seite wurden zuvor Schritte zum Ausführen dieser Aufgaben mit der Microsoft Operations Management Suite (OMS) beschrieben. Diese wird [im Januar 2019 eingestellt](../azure-monitor/platform/oms-portal-transition.md), und stattdessen werden Schritte mit Azure Log Analytics angegeben. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, benötigen Sie einen Log Analytics-Arbeitsbereich. Erfahren Sie mehr über das [Erstellen eines Log Analytics-Arbeitsbereichs](../azure-monitor/learn/quick-create-workspace.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Aktivieren der Diagnoseprotokollierung beim Erstellen von Logik-Apps

1. Erstellen Sie im [Azure-Portal](https://portal.azure.com) eine Logik-App. Wählen Sie **Ressource erstellen** > **Integration** > **Logik-App** aus.

   ![Erstellen einer Logik-App](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

1. Führen Sie unter **Logik-App erstellen** wie gezeigt die folgenden Schritte aus:

   1. Geben Sie einen Namen für Ihre Logik-App an, und wählen Sie Ihr Azure-Abonnement aus. 

   1. Erstellen Sie eine Azure-Ressourcengruppe, oder wählen Sie sie aus.

   1. Legen Sie **Log Analytics** auf **Ein** fest. 

   1. Wählen Sie in der Liste der Log Analytics-Arbeitsbereiche den Arbeitsbereich aus, an den Sie Daten für Ihre Logik-App-Ausführungen senden möchten. 

      ![Erstellen einer Logik-App](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      Nach Abschluss dieses Schritts erstellt Azure Ihre Logik-App, die jetzt Ihrem Log Analytics-Arbeitsbereich zugeordnet ist. 
      Mit diesem Schritt wird auch die Lösung für die Logik-App-Verwaltung automatisch in Ihrem Arbeitsbereich installiert.

   1. Wenn Sie fertig sind, wählen Sie **Erstellen** aus.

1. Fahren Sie mit [diesen Schritten](#view-logic-app-runs-oms) fort, um Ihre Logik-App-Ausführungen anzuzeigen.

## <a name="install-logic-apps-management-solution"></a>Installieren der Lösung Logic Apps-Verwaltung

Überspringen Sie diesen Schritt, falls Sie Azure Monitor-Protokolle bereits beim Erstellen Ihrer Logik-App aktiviert haben. Sie haben die Lösung für die Logik-App-Verwaltung bereits installiert.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Alle Dienste**. Geben Sie im Suchfeld „log analytics“ ein, und wählen Sie **Log Analytics** aus.

   ![Auswählen von „Log Analytics“](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. Wählen Sie unter **Log Analytics** Ihren Log Analytics-Arbeitsbereich aus. 

   ![Auswählen Ihres Log Analytics-Arbeitsbereichs](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. Wählen Sie unter **Erste Schritte mit Log Analytics** > **Überwachungslösungen konfigurieren** die Option **Lösungen anzeigen** aus.

   ![Auswählen von „Lösungen anzeigen“](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. Wählen Sie auf der Seite „Übersicht“ die Option **Hinzufügen** aus, um die Liste **Verwaltungslösungen** zu öffnen. Wählen Sie in dieser Liste die Option **Logic Apps-Verwaltung** aus. 

   ![Auswählen von „Logic Apps-Verwaltung“](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

   Wenn Sie die Lösung nicht finden können, wählen Sie am unteren Rand der Liste **Weitere laden** aus, bis die Lösung angezeigt wird.

1. Wählen Sie **Erstellen** aus, bestätigen Sie den Log Analytics-Arbeitsbereich, in dem Sie die Lösung installieren möchten, und wählen Sie erneut **Erstellen** aus.   

   ![Auswählen von „Erstellen“ für „Logic Apps-Verwaltung“](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

   Wenn Sie keinen vorhandenen Arbeitsbereich verwenden möchten, können Sie zu diesem Zeitpunkt auch einen neuen Arbeitsbereich erstellen.

   Nach Abschluss wird die Lösung Logic Apps-Verwaltung auf der Seite „Übersicht“ angezeigt. 

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>Anzeigen der Informationen zur Ausführung der Logik-App

Nachdem Ihre Logik-App ausgeführt wird, können Sie den Status und die Anzahl der Ausführungen auf der Kachel **Logic Apps-Verwaltung** anzeigen. 

1. Wechseln Sie zu Ihrem Log Analytics-Arbeitsbereich, und öffnen Sie die Seite „Übersicht“. Wählen Sie **Logic Apps-Verwaltung** aus. 

   ![Ausführungsstatus und -anzahl für die Logik-App](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

   Hier sind Ihre Logik-App-Ausführungen nach Name oder Ausführungsstatus gruppiert. 
   Auf dieser Seite finden Sie auch Details zu Fehlern in Aktionen oder Triggern für die Logik-App-Ausführungen.

   ![Statuszusammenfassung für Ihre Logik-App-Ausführungen](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
1. Wählen Sie die Zeile für eine Logik-App oder einen Status aus, um alle Ausführungen für eine bestimmte Logik-App bzw. einen Status anzuzeigen.

   Hier ist ein Beispiel angegeben, bei dem alle Ausführungen für eine bestimmte Logik-App aufgeführt werden:

   ![Anzeigen von Ausführungen für eine Logik-App oder einen Status](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Diese Seite enthält diese erweiterten Optionen:

   * **Nachverfolgte Eigenschaften:**

     In dieser Spalte werden nachverfolgte Eigenschaften für die Logik-App nach Aktionen gruppiert angezeigt. Wählen Sie zum Anzeigen der überwachten Eigenschaften **Ansicht** aus. 
     Sie können die nachverfolgten Eigenschaften mit dem Spaltenfilter durchsuchen.
   
     ![Anzeigen der überwachten Eigenschaften für eine Logik-App](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     Alle neu hinzugefügten überwachten Eigenschaften werden möglicherweise erst nach 10 bis 15 Minuten erstmalig angezeigt. Erfahren Sie mehr über das [Hinzufügen überwachter Eigenschaften zu Ihrer Logik-App](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Erneut übermitteln:** Sie können eine oder mehrere Logik-App-Ausführungen erneut übermitteln, die fehlgeschlagen sind, erfolgreich waren oder noch ausgeführt werden. Aktivieren Sie die Kontrollkästchen für die Ausführungen, die Sie erneut übermitteln möchten, und wählen Sie **Erneut übermitteln** aus. 

     ![Erneutes Übermitteln von Logik-App-Ausführungen](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Zum Filtern dieser Ergebnisse können Sie sowohl die clientseitige als auch die serverseitige Filterung durchführen.

   * **Clientseitiger Filter**: Wählen Sie für jede Spalte die gewünschten Filter aus. Beispiel:

     ![Beispiel für Spaltenfilter](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Serverseitiger Filter**: Verwenden Sie das Steuerelement für die Bereichsauswahl oben auf der Seite, um ein bestimmtes Zeitfenster auszuwählen oder die Anzahl von angezeigten Ausführungen zu begrenzen. Standardmäßig werden nur jeweils 1.000 Datensätze angezeigt.
   
     ![Ändern des Zeitfensters](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
1. Um alle Aktionen und deren Details für eine bestimmte Ausführung anzuzeigen, wählen Sie eine Zeile für eine Logik-App-Ausführung aus.

   Hier ist ein Beispiel mit allen Aktionen für eine bestimmte Logik-App-Ausführung dargestellt:

   ![Anzeigen von Aktionen für eine Logik-App-Ausführung](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
1. Um auf einer Ergebnisseite die Abfrage hinter den Ergebnissen anzuzeigen oder um alle Ergebnisse anzuzeigen, wählen Sie **Alle anzeigen** aus. Damit wird die Seite der Protokollsuche geöffnet.
   
   ![„Alle anzeigen“ auf Ergebnisseiten](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   Auf der Seite „Protokollsuche“:

   * Um die Abfrageergebnisse in einer Tabelle anzuzeigen, wählen Sie **Tabelle** aus.

   * Sie können die Abfragezeichenfolge in der Suchleiste bearbeiten, um die Abfrage zu ändern. 
   Wählen Sie **Erweiterte Analyse**, falls Sie mehr Optionen benötigen.

     ![Anzeigen von Aktionen und Details für eine Logik-App-Ausführung](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     Auf der Log Analytics-Seite können Sie Abfragen aktualisieren und die Ergebnisse aus der Tabelle anzeigen. Für diese Abfrage wird die [Kusto-Abfragesprache](https://aka.ms/LogAnalyticsLanguageReference) verwendet. Sie können diese Sprache bearbeiten, wenn Sie unterschiedliche Ergebnisse anzeigen möchten. 

     ![Log Analytics – Abfrageansicht](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen von B2B-Nachrichten](../logic-apps/logic-apps-monitor-b2b-message.md)