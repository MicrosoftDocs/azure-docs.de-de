---
title: Visuelles Überwachen von Azure Data Factory
description: Erfahren Sie, wie Sie Azure Data Factorys visuell überwachen.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 4248969a5de384810f9718648ef8ac8f5f6d4129
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373163"
---
# <a name="visually-monitor-azure-data-factory"></a>Visuelles Überwachen von Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Nachdem Sie eine Pipeline in Azure Data Factory erstellt und veröffentlicht haben, können Sie ihr einen Trigger zuordnen oder manuell eine Ad-hoc-Ausführung starten. Sie können Ihre gesamten Pipelineausführungen nativ über die Azure Data Factory-Benutzeroberfläche überwachen. Um die Benutzeroberfläche für die Überwachung zu öffnen, wählen Sie die Kachel **Überwachen und verwalten** im Blatt „Data Factory“ des [Azure-Portals](https://portal.azure.com/) aus. Wenn Sie sich bereits auf der Benutzeroberfläche von ADF befinden, klicken Sie auf der linken Seitenleiste auf das Symbol **Überwachen**.

Alle Data Factory-Ausführungen werden standardmäßig in der lokalen Zeitzone des Browsers angezeigt. Wenn Sie die Zeitzone ändern, werden alle Datums- und Zeitfelder auf die ausgewählte Zeitzone ausgerichtet.

## <a name="monitor-pipeline-runs"></a>Überwachen der Pipelineausführungen

Die standardmäßige Überwachungsansicht ist eine Liste der ausgelösten Pipelineausführungen im ausgewählten Zeitraum. Sie können den Zeitbereich ändern und nach Status, Pipelinename oder Anmerkung filtern. Zeigen Sie auf die jeweilige Pipelineausführung, um ausführungsspezifische Aktionen wie das erneute Ausführen und den Verbrauchsbericht anzuzeigen.

![Listenansicht zum Überwachen von Pipelineausführungen](media/monitor-visually/pipeline-runs.png)

Das Raster der Pipelineausführungen enthält die folgenden Spalten:

| **Spaltenname** | **Beschreibung** |
| --- | --- |
| Pipeline Name | Name der Pipeline |
| Run Start | Startdatum und -uhrzeit der Pipelineausführung (MM/TT/JJJJ, HH:MM:SS AM/PM) |
| Ausführungsende | Enddatum und -uhrzeit der Pipelineausführung (MM/TT/JJJJ, HH:MM:SS AM/PM) |
| Duration | Dauer der Ausführung (hh:mm:ss) |
| Triggered By | Name des Triggers, der die Pipeline gestartet hat |
| Status | **Fehlerhaft**, **Erfolgreich**, **In Bearbeitung**, **Abgebrochen** oder **In Warteschlange** |
| Anmerkungen | Einer Pipeline zugeordnete filterbare Tags  |
| Parameter | Parameter für die Pipelineausführung (Name-Wert-Paare) |
| Fehler | Bei einem Fehler der Pipeline ist dies der Ausführungsfehler |
| Run ID | ID der Pipelineausführung |

Sie müssen die Schaltfläche **Aktualisieren** manuell auswählen, um die Liste der Pipeline- und Aktivitätsausführungen zu aktualisieren. Eine automatische Aktualisierung wird derzeit nicht unterstützt.

![Schaltfläche „Aktualisieren“](media/monitor-visually/refresh.png)

Um die Ergebnisse einer Debugausführung anzuzeigen, wählen Sie die Registerkarte **Debuggen** aus.

![Auswählen des Symbols zum Anzeigen aktiver Debugausführungen](media/iterative-development-debugging/view-debug-runs.png)

## <a name="monitor-activity-runs"></a>Überwachung der Aktivitätsausführungen

Um eine ausführliche Ansicht der einzelnen Aktivitätsausführungen einer bestimmten Pipelineausführung anzuzeigen, klicken Sie auf den Namen der Pipeline.

![Anzeigen der Aktivitätsausführungen](media/monitor-visually/view-activity-runs.png)

In der Listenansicht werden die Aktivitätsausführungen angezeigt, die den einzelnen Pipelineausführungen entsprechen. Zeigen Sie auf die jeweilige Aktivitätsausführung, um ausführungsspezifische Informationen wie die JSON-Eingabe, die JSON-Ausgabe und detaillierte aktivitätsspezifische Überwachungsfunktionen anzuzeigen.

![Es gibt Informationen zu SalesAnalyticsMLPipeline, gefolgt von einer Liste der Aktivitätsausführungen.](media/monitor-visually/activity-runs.png)

| **Spaltenname** | **Beschreibung** |
| --- | --- |
| Activity Name | Name der Aktivität innerhalb der Pipeline |
| Activity Type | Der Typ der Aktivität, z. B. **Copy**, **ExecuteDataFlow** oder **AzureMLExecutePipeline** |
| Aktionen | Symbole, die es Ihnen ermöglichen, JSON-Eingabeinformationen, JSON-Ausgabeinformationen oder detaillierte aktivitätsspezifische Benutzeroberflächen für die Überwachung anzuzeigen. | 
| Run Start | Startdatum und -uhrzeit der Aktivitätsausführung (MM/TT/JJJJ, HH:MM:SS AM/PM) |
| Duration | Dauer der Ausführung (hh:mm:ss) |
| Status | **Fehlerhaft**, **Erfolgreich**, **In Bearbeitung** oder **Abgebrochen** |
| Integration Runtime | Für welche Integration Runtime die Aktivität ausgeführt wurde |
| Benutzereigenschaften | Benutzerdefinierte Eigenschaften der Aktivität |
| Fehler | Bei einer fehlerhaften Aktivität ist dies der Ausführungsfehler |
| Run ID | ID der Aktivitätsausführung |

Wenn eine Aktivität fehlerhaft war, können Sie auf das Symbol in der Fehlerspalte klicken, um die ausführliche Fehlermeldung anzuzeigen. 

![Eine Benachrichtigung mit Fehlerdetails einschließlich Fehlercode, Fehlertyp und Fehlerdetails wird angezeigt.](media/monitor-visually/activity-run-error.png)

### <a name="promote-user-properties-to-monitor"></a>Heraufstufen von Benutzereigenschaften für die Überwachung

Stufen Sie jede Pipelineaktivitätseigenschaft als Benutzereigenschaft höher, sodass sie zu einer von Ihnen überwachten Entität wird. Sie können z. B. die Eigenschaften **Quelle** und **Ziel** der Kopieraktivität in der Pipeline als Benutzereigenschaften höher stufen.

> [!NOTE]
> Sie können nur bis zu fünf Pipelineaktivitätseigenschaften als Benutzereigenschaften höher stufen.

![Erstellen von Benutzereigenschaften](media/monitor-visually/promote-user-properties.png)

Nachdem Sie die Benutzereigenschaften erstellt haben, können Sie sie in den Überwachungslistenansichten überwachen.

![Hinzufügen von Spalten für Benutzereigenschaften zur Liste der Aktivitätsausführungen](media/monitor-visually/choose-user-properties.png)

 Wenn ein Tabellenname als Quelle für die Kopieraktivität dient, können Sie den Namen der Quelltabelle als Spalte in der Listenansicht für Aktivitätsausführungen überwachen.

![Aktivitätsausführungenliste mit Spalten für Benutzereigenschaften](media/monitor-visually/view-user-properties.png)

## <a name="rerun-pipelines-and-activities"></a>Erneutes Ausführen von Pipelines und Aktivitäten

Um eine Pipeline, die bereits ausgeführt wurde, von Anfang an erneut auszuführen, zeigen Sie auf die jeweilige Pipelineausführung, und wählen Sie **Erneut ausführen** aus. Wenn Sie mehrere Pipelines auswählen, können Sie über die Schaltfläche **Erneut ausführen** alle erneut ausführen.

![Erneutes Ausführen einer Pipeline](media/monitor-visually/rerun-pipeline.png)

Wenn Sie die erneute Ausführung an einem bestimmten Punkt starten möchten, können Sie dies über die Ansicht der Aktivitätsausführungen erledigen. Wählen Sie die Aktivität aus, ab der Sie beginnen möchten, und wählen Sie **Aktivität erneut ausführen** aus. 

![Wiederholen einer Aktivitätsausführung](media/monitor-visually/rerun-activity.png)

### <a name="rerun-from-failed-activity"></a>Ab fehlgeschlagener Aktivität erneut ausführen

Wenn eine Aktivität fehlschlägt, das Zeitlimit überschritten wird oder die Aktivität abgebrochen wird, können Sie die Pipeline ab der fehlgeschlagenen Aktivität erneut ausführen, indem Sie **Ab fehlgeschlagener Aktivität erneut ausführen** auswählen.

![Fehlgeschlagene Aktivität erneut ausführen](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Anzeigen des Verlaufs für erneute Ausführungen

Der Verlauf für erneute Ausführungen kann für alle Pipelineausführungen in der Listenansicht angezeigt werden.

![Anzeigen des Verlaufs](media/monitor-visually/rerun-history-1.png)

Sie können aber auch den Verlauf für erneute Ausführungen für eine bestimmte Pipelineausführung anzeigen.

![Anzeigen des Verlaufs für eine Pipelineausführung](media/monitor-visually/view-rerun-history.png)

## <a name="monitor-consumption"></a>Überwachen des Verbrauchs

Sie können die durch eine Pipelineausführung verbrauchten Ressourcen anzeigen, indem Sie neben der Ausführung auf das Symbol für Verbrauch klicken. 

![Der Screenshot zeigt, wo Sie die von einer Pipeline verbrauchten Ressourcen sehen können.](media/monitor-visually/monitor-consumption-1.png)

Wenn Sie auf das Symbol klicken, wird ein Verbrauchsbericht zu den durch diese Pipelineausführung genutzten Ressourcen geöffnet. 

![Überwachen des Verbrauchs](media/monitor-visually/monitor-consumption-2.png)

Sie können diese Werte mit dem [Azure-Preisrechner](https://azure.microsoft.com/pricing/details/data-factory/) verbinden, um die Kosten für die Pipelineausführung zu schätzen. Weitere Informationen zu Azure Data Factory-Preisen finden Sie unter [Grundlegendes zu Preisen](pricing-concepts.md).

> [!NOTE]
> Diese vom Preisrechner zurückgegebenen Werte sind eine Schätzung. Sie geben nicht den genauen Betrag an, der Ihnen von Azure Data Factory in Rechnung gestellt wird. 

## <a name="gantt-views"></a>Gantt-Ansichten

Ein Gantt-Diagramm ist eine Ansicht, über die Sie den Ausführungsverlauf über einen Zeitraum anzeigen können. Wenn Sie zu einer Gantt-Ansicht wechseln, werden alle Pipelineausführungen gruppiert nach dem Namen als Balken in Bezug auf die Ausführungsdauer dargestellt. Sie können auch nach Anmerkungen/Tags gruppieren, die Sie in Ihrer Pipeline erstellt haben. Die Gantt-Ansicht ist auch auf Ebene der Aktivitätsausführungen verfügbar.

![Beispiel für ein Gantt-Diagramm](media/monitor-visually/select-gantt.png)

Die Länge des Balkens informiert über die Dauer der Pipeline. Sie können auch den Balken auswählen, um weitere Details anzuzeigen.

![Gantt-Diagramm zur Dauer](media/monitor-visually/view-gantt-run.png)

## <a name="alerts"></a>Alerts

Sie können Warnungen auf der Grundlage unterstützter Metriken in Data Factory auslösen. Wählen Sie dazu auf der Überwachungsseite von Data Factory die Option **Überwachen** > **Warnungen und Metriken** aus.

![Data Factory-Überwachungsseite](media/monitor-visually/start-page.png)

Das folgende Video enthält eine siebenminütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Erstellen von Warnungen

1.  Wählen Sie **Neue Warnungsregel** aus, um eine neue Warnung zu erstellen.

    ![Schaltfläche „Neue Warnungsregel“](media/monitor-visually/new-alerts.png)

1.  Geben Sie den Namen der Warnungsregel an, und wählen Sie den Schweregrad der Warnung aus.

    ![Felder für Regelname und Schweregrad](media/monitor-visually/name-and-severity.png)

1.  Wählen Sie die Warnungskriterien aus.

    ![Feld für Zielkriterien](media/monitor-visually/add-criteria-1.png)

    ![Der Screenshot zeigt, wo Sie eine Metrik auswählen, um die Warnungsbedingung einzurichten.](media/monitor-visually/add-criteria-2.png)

    ![Liste der Kriterien](media/monitor-visually/add-criteria-3.png)

    Sie können Warnungen für verschiedene Metriken erstellen, z. B. für Anzahl und Größe der ADF-Entität, Aktivitäts-, Pipeline- und Triggerausführung, CPU-Auslastung, Speicher, Knotenanzahl und Warteschlange von Integration Runtime (IR) sowie für SSIS-Paketausführungen und Vorgänge zum Starten und Beenden von SSIS IR.

1.  Konfigurieren Sie die Warnungslogik. Sie können eine Warnung für die ausgewählte Metrik für alle Pipelines und entsprechenden Aktivitäten erstellen. Sie können außerdem einen bestimmten Aktivitätstyp, Aktivitätsnamen, Pipelinenamen oder Fehlertyp auswählen.

    ![Optionen zum Konfigurieren der Warnungslogik](media/monitor-visually/alert-logic.png)

1.  Konfigurieren Sie E-Mail-, SMS-, Push- und Sprachbenachrichtigungen für die Warnung. Erstellen Sie eine Aktionsgruppe für die Warnungsbenachrichtigungen, oder wählen Sie eine vorhandene Aktionsgruppe aus.

    ![Optionen zum Konfigurieren von Benachrichtigungen](media/monitor-visually/configure-notification-1.png)

    ![Optionen zum Hinzufügen einer Benachrichtigung](media/monitor-visually/configure-notification-2.png)

1.  Erstellen Sie die Warnungsregel.

    ![Optionen zum Erstellen einer Warnungsregel](media/monitor-visually/create-alert-rule.png)

## <a name="next-steps"></a>Nächste Schritte

Im Artikel [Programmgesteuertes Überwachen und Verwalten von Pipelines](./monitor-programmatically.md) erfahren Sie mehr über das Überwachen und Verwalten von Pipelines.