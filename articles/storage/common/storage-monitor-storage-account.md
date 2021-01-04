---
title: Überwachen eines Azure Storage-Kontos im Azure-Portal | Microsoft-Dokumentation
description: Hier erfahren Sie, wie ein Speicherkonto in Azure über das Azure-Portal und mithilfe von Azure Storage Analytics überwacht wird.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: bfecae4775655f732df8fd6cffea613cd39fb828
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782007"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Überwachen eines Speicherkontos im Azure-Portal

[Azure-Speicheranalyse](storage-analytics.md) enthält Metriken für alle Speicherdienste und Protokolle für Blobs, Warteschlangen und Tabellen. Sie können im [Azure-Portal](https://portal.azure.com) konfigurieren, welche Metriken und Protokolle für Ihr Konto aufgezeichnet werden, und Diagramme konfigurieren, die visuelle Darstellungen der Metrikdaten bereitstellen. 

Es wird empfohlen, [Azure Monitor für Storage](../../azure-monitor/insights/storage-insights-overview.md) (Vorschau) zu überprüfen. Dieses Feature von Azure Monitor ermöglicht eine umfassende Überwachung ihrer Azure Storage-Konten, indem eine einheitliche Ansicht der Leistung, Kapazität und Verfügbarkeit Ihrer Azure Storage-Dienste bereitgestellt wird. Sie müssen nichts aktivieren oder konfigurieren und können diese Metriken aus den vordefinierten interaktiven Diagrammen und anderen darin enthaltenen Visualisierungen sofort anzeigen.

> [!NOTE]
> Für die Untersuchung von Überwachungsdaten im Azure-Portal fallen Kosten an. Weitere Informationen finden Sie unter [Storage Analytics](storage-analytics.md).
>
> Azure Files unterstützt derzeit Storage Analytics-Metriken, allerdings noch keine Protokollierung.
>
> Für Blockblob-Speicherkonten mit Premium-Leistung werden keine Storage Analytics-Metriken unterstützt, sondern nur die Protokollierung. Sie können die Protokollierung programmgesteuert per REST-API oder über die Clientbibliothek aktivieren. Wenn Sie Metriken über Blockblob-Speicherkonten mit Premium-Leistung anzeigen möchten, können Sie die Verwendung von [Azure Storage-Metriken in Azure Monitor](../blobs/monitor-blob-storage.md) erwägen.
>
> Eine ausführliche Anleitung zum Verwenden der Speicheranalyse sowie weiterer Tools, um Azure Storage-bezogene Probleme zu identifizieren, zu diagnostizieren und zu beheben, finden Sie unter [Microsoft Azure Storage: Überwachung, Diagnose und Problembehandlung](storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Konfigurieren der Überwachung für ein Speicherkonto

1. Wählen Sie im [Azure-Portal](https://portal.azure.com)**Speicher** und dann den Speicherkontonamen, um das Kontodashboard zu öffnen.
1. Wählen Sie im Abschnitt **ÜBERWACHUNG** des Menüblatts die Option **Diagnose** aus.

    ![Screenshot: Hervorgehobene Option „Diagnoseeinstellungen (klassisch)“ im Abschnitt „Überwachung (klassisch)“](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Wählen Sie den **Typ** der Metrikdaten für jeden **Dienst** , den Sie überwachen möchten, und die **Aufbewahrungsrichtlinie** für die Daten. Sie können die Überwachung auch deaktivieren, indem Sie für **Status** **Aus** festlegen.

    ![Überwachungsoptionen](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Zum Festlegen der Datenaufbewahrungsrichtlinie verschieben Sie den Schieberegler **Aufbewahrung (Tage)** , oder geben Sie die Anzahl der Tage ein, für die Daten aufbewahrt werden sollen (zwischen 1 und 365). Die Standardeinstellung für neue Speicherkonten beträgt sieben Tage. Wenn Sie keine Aufbewahrungsrichtlinie festlegen möchten, geben Sie null (0) ein. Ist keine Aufbewahrungsrichtlinie festgelegt, müssen Sie die Überwachungsdaten selbst löschen.

   > [!WARNING]
   > Ihnen werden Gebühren berechnet, wenn Sie Metrikdaten manuell löschen. Veraltete Analysedaten (Daten, die älter sind als die Aufbewahrungsrichtlinie) werden vom System kostenlos gelöscht. Wir empfehlen, die Aufbewahrungsrichtlinie entsprechend dem Zeitraum festzulegen, den Sie Speicheranalysedaten für Ihr Konto behalten möchten. Weitere Informationen finden Sie unter [Abrechnung für Speichermetriken](storage-analytics-metrics.md#billing-on-storage-metrics).
   >

1. Wählen Sie nach Abschluss der Überwachungskonfiguration **Speichern**.

Ein Standardsatz von Metriken wird in Diagrammen auf dem Speicherkontoblatt angezeigt, wie auch die Blätter der einzelnen Dienste (Blob, Warteschlange, Tabelle und Datei). Nachdem Sie die Metriken für einen Dienst aktiviert haben, kann es bis zu einer Stunde dauern, bis Daten in den Diagrammen angezeigt werden. Sie können **Bearbeiten** auf jedem beliebigen Metrikdiagramm auswählen, um zu konfigurieren, welche Metriken im Diagramm angezeigt werden.

Sie können die Sammlung von Metriken und die Protokollierung deaktivieren, indem Sie **Status** auf **Aus** setzen.

> [!NOTE]
> Azure Storage verwendet [Tabellenspeicher](storage-introduction.md#table-storage), um die Metriken für Ihr Speicherkonto zu speichern, und speichert die Metriken in Tabellen in Ihrem Konto. Weitere Informationen finden Sie weiter oben unter [Speichern von Metriken](storage-analytics-metrics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Anpassen von Metrikendiagrammen

Verwenden Sie das folgende Verfahren, um auszuwählen, welche Speichermetriken in einem Metrikdiagramm angezeigt werden.

1. Beginnen Sie mit der Anzeige eines Speichermetrikdiagramms im Azure-Portal. Diagramme finden Sie auf dem **Speicherkontoblatt** und dem Blatt **Metriken** eines individuellen Diensts (Blob, Warteschlange, Tabelle, Datei).

   In diesem Beispiel wird das folgende auf dem **Speicherkontoblatt** angezeigte Diagramm verwendet:

   ![Diagrammauswahl im Azure-Portal](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Klicken Sie auf eine beliebige Stelle innerhalb des Diagramms, um es zu bearbeiten.

1. Wählen Sie als Nächstes den **Zeitraum** der Metriken, die im Diagramm angezeigt werden sollen, und den **Dienst** (Blob, Warteschlange, Tabelle, Datei) aus, dessen Metriken angezeigt werden sollen. Hier sind die Metriken für den Blobdienst der vergangenen Woche zur Anzeige ausgewählt:

   ![Auswahl von Zeitraum und Dienst auf dem Blatt „Diagramm bearbeiten“](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Wählen Sie die einzelnen **Metriken** aus, die im Diagramm angezeigt werden sollen, und klicken Sie dann auf **OK**.

   ![Individuelle Auswahl von Metriken auf dem Blatt „Diagramm bearbeiten“](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

Ihre Diagrammeinstellungen haben keine Auswirkung auf Sammlung, Aggregierung oder Speicherung von Überwachungsdaten im Speicherkonto.

### <a name="metrics-availability-in-charts"></a>Verfügbarkeit von Metriken in Diagrammen

Die Liste der verfügbaren Metriken ändert sich je nach dem Dienst, den Sie in der Dropdownliste ausgewählt haben, und dem Einheitentyp des Diagramms, das Sie gerade bearbeiten. Sie können z.B. prozentuale Metriken wie *PercentNetworkError* und *PercentThrottlingError* nur dann auswählen, wenn Sie ein Diagramm bearbeiten, das Einheiten in Prozent angezeigt:

![Fehlerprozentsatz-Diagramm für Anforderung im Azure-Portal](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Auflösung von Metriken

Die Metriken, die Sie in **Diagnose** ausgewählt haben, bestimmen die Auflösung der Metriken, die für Ihr Konto verfügbar sind:

* **Aggregieren** -Überwachung erfasst Metriken wie Eingang/Ausgang, Verfügbarkeit, Latenz sowie Erfolgsprozentwerte. Diese Metriken werden aus Blob-, Tabellen-, Datei- und Warteschlangendienst aggregiert.
* **Pro API** bietet eine feinere Auflösung mit Metriken, die für einzelne Speicheroperationen verfügbar sind, in Ergänzung der Aggregate auf Dienstebene.

## <a name="configure-metrics-alerts"></a>Konfigurieren von Metrikenwarnungen

Sie können Warnungen erstellen, damit Sie benachrichtigt werden, wenn Speicherressourcenmetriken Schwellenwerte erreichen.

1. Um das ‚Blatt **Warnungsregeln** zu öffnen, scrollen Sie zum Abschnitt **ÜBERWACHUNG** des **Menüblatts** nach unten, und wählen Sie **Warnungen (klassisch)** .
2. Wählen Sie **Metrikwarnung hinzufügen (klassisch)** , um das Blatt **Warnungsregel hinzufügen** zu öffnen.
3. Geben Sie einen **Namen** und eine **Beschreibung** für Ihre neue Warnungsregel ein.
4. Wählen Sie die **Metrik** , für die Sie eine Warnung hinzufügen möchten, eine **Bedingung** für die Warnung und einen **Schwellenwert**. Der Schwellenwerteinheiten-Typ ändert sich je nach der Metrik, die Sie ausgewählt haben. Beispielsweise ist „count“ der Einheitentyp für *ContainerCount* , die Einheit für die Metrik *PercentNetworkError* ist dagegen ein Prozentsatz.
5. Wählen Sie den **Zeitraum** aus. Metriken, die den Schwellenwert innerhalb des Zeitraums erreichen oder überschreiten, lösen eine Warnung aus.
6. (Optional) Konfigurieren Sie **E-Mail** - und **Webhook** -Benachrichtigungen. Weitere Informationen über Webhooks finden Sie unter [Konfigurieren eines Webhooks für eine Azure-Metrikwarnung](../../azure-monitor/platform/alerts-webhooks.md). Wenn Sie keine E-Mail- oder Webhook-Benachrichtigungen konfigurieren, werden Warnungen nur im Azure-Portal angezeigt.

![Blatt „Warnungsregel hinzufügen“ im Azure-Portal](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Hinzufügen von Metrikdiagrammen zum Portaldashboard

Sie können Ihrem Portaldashboard Azure Storage-Metrikdiagramme für beliebige Ihrer Speicherkonten hinzufügen.

1. Wählen Sie **Dashboard bearbeiten** , während Ihr Dashboard im [Azure-Portal](https://portal.azure.com) angezeigt wird.
1. Wählen Sie im **Kachelkatalog** die Option **Kacheln suchen nach:**  > **Typ**.
1. Wählen Sie **Typ** > **Speicherkonten**.
1. Wählen Sie in **Ressourcen** das Speicherkonto aus, dessen Metriken Sie dem Dashboard hinzufügen möchten.
1. Wählen Sie **Kategorien** > **Überwachung**.
1. Ziehen Sie die Diagrammkachel für die Metrik, die Sie anzeigen möchten, auf Ihr Dashboard, und legen Sie sie ab. Wiederholen Sie dies für alle Metriken, die Sie auf dem Dashboard anzeigen möchten. In der folgenden Abbildung ist das Diagramm „Blobs – Anforderungen insgesamt“ als Beispiel hervorgehoben, aber alle Diagramme sind für die Platzierung auf Ihrem Dashboard verfügbar.

   ![Kachelkatalog im Azure-Portal](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Wählen Sie **Anpassung abgeschlossen** am oberen Rand des Dashboards aus, wenn Sie keine weiteren Diagramme hinzufügen möchten.

Nachdem Sie Ihrem Dashboard Diagramme hinzugefügt haben, können Sie sie weiter anpassen, wie in „Anpassen von Metrikdiagrammen“ beschrieben.

## <a name="configure-logging"></a>Konfigurieren der Protokollierung

Sie können Azure Storage anweisen, Diagnoseprotokolle für Lese-, Schreib- und Löschanforderungen für die Blob-, Tabellen- und Warteschlangendienste zu speichern. Die Datenaufbewahrungsrichtlinie, die Sie festlegen, gilt auch für diese Protokolle.

> [!NOTE]
> Azure Files unterstützt derzeit Storage Analytics-Metriken, allerdings noch keine Protokollierung.
>

1. Wählen Sie im [Azure-Portal](https://portal.azure.com)**Speicherkonten** und dann den Speicherkontonamen, um das Speicherkontoblatt zu öffnen.
1. Wählen Sie im Abschnitt **Überwachung (klassisch)** des Menüblatts die Option **Diagnostics settings (classic)** (Diagnoseeinstellungen (klassisch)) aus.

    ![Element des Menüs „Diagnose“ unter ÜBERWACHUNG im Azure-Portal.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Stellen Sie sicher, dass **Status** auf **Ein** festgelegt ist, und wählen Sie die **Dienste** , für die Sie die Protokollierung aktivieren möchten.

    ![Konfigurieren Sie die Protokollierung im Azure-Portal.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Klicken Sie auf **Speichern**.

Die Diagnoseprotokolle werden in einem Blobcontainer namens *$logs* in Ihrem Speicherkonto gespeichert. Sie können die Protokolldaten über einen Speicher-Explorer wie [Microsoft Azure Storage-Explorer](https://storageexplorer.com) oder programmgesteuert mit der Speicherclientbibliothek oder PowerShell anzeigen.

Informationen zum Zugreifen auf den Container „$logs“ finden Sie unter [Protokollierung durch die Speicheranalyse](storage-analytics-logging.md).

## <a name="next-steps"></a>Nächste Schritte

* Hier finden Sie weitere Informationen zu [Metriken, Protokollierung und Abrechnung](storage-analytics.md) für die Speicheranalyse.