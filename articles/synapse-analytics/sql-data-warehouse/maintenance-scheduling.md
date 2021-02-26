---
title: Wartungszeitpläne für den Synapse-SQL-Pool
description: Wartungszeitpläne ermöglichen Kunden, die notwendigen geplanten Wartungsereignisse in ihre Planungen einzubeziehen, mit denen Azure Synapse Analytics neue Features, Upgrades und Patches einführt.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: e1565d4f7792dca5242965d11c58faa90e2222d3
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920374"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Verwenden von Wartungszeitplänen zum Verwalten der Updates und Wartung von Diensten

Dieses Wartungszeitplan-Feature integriert innerhalb von Azure Synapse Analytics Benachrichtigungen über eine geplante Wartung von Service Health, die Überwachung der Ressourcenintegritätsprüfung und den Zeitplanungsdienst für den Synapse-SQL-Pool (Data Warehouse).

Sie sollten Wartungszeitpläne verwenden, um ein passendes Zeitfenster für den Erhalt neuer Features, Upgrades und Patches zu wählen. Sie müssen ein primäres und ein sekundäres Wartungsfenster innerhalb eines Zeitraums von sieben Tagen auswählen, wobei jedes Fenster separate Tagesbereiche abdecken muss.

Sie können beispielsweise ein primäres Fenster von Samstag 22:00 Uhr bis Sonntag 01:00 Uhr und dann ein sekundäres Fenster von Mittwoch 19:00 Uhr bis 22:00 Uhr planen. Wenn die Wartung nicht während Ihres primären Wartungsfensters nicht erfolgen kann, wird versucht, die Wartung während des sekundären Wartungsfensters durchzuführen. Dienstwartungen können gelegentlich sowohl im primären als auch im sekundären Fenster auftreten. Um einen schnellen Abschluss aller Wartungsarbeiten zu gewährleisten, können DW400c und niedrigere Data Warehouse-Ebenen die Wartung außerhalb eines bestimmten Wartungsfensters abschließen.

Allen neu erstellten Data Warehouse-Instanzen muss während der Bereitstellung ein systemdefinierter Wartungszeitplan zugewiesen werden. Der Zeitplan kann direkt nach Abschluss der Bereitstellung bearbeitet werden.

Obwohl ein Wartungsfenster zwischen drei und acht Stunden lang sein kann, bedeutet das nicht, dass das Data Warehouse für die Dauer offline geschaltet ist. Die Wartung kann jederzeit innerhalb dieses Zeitfensters durchgeführt werden. In dieser Zeit müssen Sie mit einer einzelnen Verbindungsunterbrechung von etwa fünf bis sechs Minuten rechnen, da der Dienst neuen Code für Ihr Data Warehouse bereitstellt. Bei DW400c und niedrigeren Ebenen können während des Wartungsfensters mehrere kurzzeitige Verbindungsverluste zu verschiedenen Zeitpunkten auftreten. Beim Start der Wartung werden alle aktiven Sitzungen abgebrochen, und für Transaktionen, für die kein Commit ausgeführt wurde, wird ein Rollback durchgeführt. Stellen Sie zum Verringern der Ausfallzeiten von Instanzen sicher, dass Ihr Data Warehouse vor dem gewählten Wartungszeitraum keine Transaktionen mit langer Ausführungsdauer aufweist.

Alle Wartungsvorgänge sollten innerhalb der angegebenen Wartungsfenster abgeschlossen werden, es sei denn, wir müssen ein zeitkritisches Update bereitstellen. Wenn Ihr Data Warehouse während einer geplanten Wartung angehalten wird, wird es während des Wiederaufnahmevorgangs aktualisiert. Sie werden umgehend benachrichtigt, wenn die Wartung für Ihr Data Warehouse abgeschlossen wurde.

## <a name="alerts-and-monitoring"></a>Benachrichtigungen und Überwachung

Dank der Integration in Service Health-Benachrichtigungen und in Resource Health Check Monitor sind Kunden immer über bevorstehende Wartungsaktivitäten informiert. Für diese Automatisierung wird Azure Monitor genutzt. Sie können entscheiden, wie Sie über anstehende Wartungsereignisse benachrichtigt werden möchten. Außerdem können Sie auswählen, welche automatisierten Abläufe Ihnen helfen, Ausfallzeiten zu bewältigen und die Auswirkungen auf den Betrieb zu minimieren.

Eine Benachrichtigung 24 Stunden im Voraus geht allen Wartungsereignissen voran, die nicht für DWC400c und niedrigere Ebenen gelten.

> [!NOTE]
> Wenn ein zeitkritisches Update erforderlich ist, können die Vorabbenachrichtigung deutlich früher gesendet werden. Dieser Fehler kann außerhalb eines festgelegten Wartungsfensters auftreten, wenn es sich um ein kritisches Update handelt.

Wenn Sie eine Vorankündigung erhalten haben, dass die Wartung erfolgt, die Wartung aber nicht während des in der Benachrichtigung angegebenen Zeitraums durchgeführt werden kann, erhalten Sie eine Stornierungsbenachrichtigung. Die Wartung wird dann während des nächsten geplanten Wartungszeitraums durchgeführt.

Alle aktiven Wartungsereignisse werden im Abschnitt **Service Health – Geplante Wartung** angezeigt. Der Service Health-Verlauf enthält eine vollständige Aufzeichnung der vergangenen Ereignisse. Sie können die Wartung während eines aktiven Ereignisses über das Dashboard des Azure Service Health Check-Portals überwachen.

### <a name="maintenance-schedule-availability"></a>Verfügbarkeit des Wartungszeitplans

Auch wenn Wartungszeitpläne in Ihrer ausgewählten Region noch nicht verfügbar sind, können Sie Ihren Wartungszeitplan jederzeit anzeigen und bearbeiten. Sobald Wartungszeitpläne in Ihrer Region verfügbar sind, wird der identifizierte Zeitplan in Ihrem Synapse-SQL-Pool sofort aktiv.

## <a name="view-a-maintenance-schedule"></a>Anzeigen eines Wartungszeitplans

Alle neu erstellten Data Warehouse-Instanzen verfügen über ein achtstündiges primäres und sekundäres Wartungsfenster, das während der Bereitstellung angewendet wird. Wie oben angegeben, können Sie die Fenster nach Abschluss der Bereitstellung ändern. Außerhalb der angegebenen Wartungsfenster werden ohne vorherige Benachrichtigung keine Wartungen durchgeführt.

Führen Sie die folgenden Schritte aus, um den Wartungszeitplan anzuzeigen, der Ihrem Synapse-SQL-Pool zugewiesen wurde:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie den Synapse-SQL-Pool aus, den Sie anzeigen möchten.
3. Der ausgewählte Synapse-SQL-Pool wird auf dem Blatt „Übersicht“ geöffnet. Der Wartungszeitplan, der auf das Data Warehouse angewandt wurde, wird unter **Wartungszeitplan** angezeigt.

![Blatt „Übersicht“](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Ändern des Wartungszeitplans

Ein Wartungszeitplan kann jederzeit aktualisiert oder geändert werden. Falls die ausgewählte Instanz gerade einen aktiven Wartungszyklus durchläuft, werden die Einstellungen gespeichert. Sie werden dann im nächsten identifizierten Wartungszeitraum angewendet. Weitere Informationen zum Überwachen Ihrer Data Warehouse-Daten während eines aktiven Verwaltungsereignisses finden Sie [hier](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="identifying-the-primary-and-secondary-windows"></a>Angeben des primären und sekundären Fensters

Primäre und sekundäre Fenster müssen jeweils separate Tagesbereiche besitzen. Ein Beispiel wäre etwa ein primäres Fenster von Dienstag bis Donnerstag und ein sekundäres Fenster von Samstag bis Sonntag.

Führen Sie die folgenden Schritte aus, um den Wartungszeitplan für Ihren Synapse-SQL-Pool zu ändern:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie den Synapse-SQL-Pool aus, den Sie aktualisieren möchten. Die Seite wird auf dem Blatt „Übersicht“ geöffnet.
Klicken Sie auf dem Übersichtsblatt auf den Link **Wartungszeitplan – Zusammenfassung**, um die Seite mit den Wartungszeitplaneinstellungen zu öffnen. Alternativ können Sie auch im Ressourcenmenü auf der linken Seite auf **Wartungszeitplan** klicken.

    ![Optionen auf dem Blatt „Übersicht“](./media/maintenance-scheduling/maintenance-change-option.png)

3. Geben Sie mithilfe der Optionen im oberen Seitenbereich die bevorzugte Zeitspanne (in Tagen) für Ihr primäres Wartungsfenster an. Dadurch bestimmen Sie, ob Ihr primäres Fenster Werktage oder Wochenendtage abdeckt. Die Dropdownwerte werden auf der Grundlage Ihrer Auswahl aktualisiert.
Während der Vorschauphase unterstützen einige Regionen ggf. noch nicht alle verfügbaren Optionen für **Tag**.

   ![Blatt „Wartungseinstellungen“](./media/maintenance-scheduling/maintenance-settings-page.png)

4. Wählen Sie über die Dropdown-Listenfelder Ihr bevorzugtes primäres und sekundäres Wartungsfenster aus:
   - **Tag**: Der bevorzugte Tag, an dem die Wartungsarbeiten innerhalb des ausgewählten Zeitfensters durchgeführt werden sollen.
   - **Startzeit**: Die bevorzugte Startzeit für das Wartungsfenster.
   - **Zeitfenster**: Die bevorzugte Dauer des Zeitfensters.

   Der Bereich **Zusammenfassung des Zeitplans** am unteren Rand des Blatts wird anhand der ausgewählten Werte aktualisiert.
  
5. Wählen Sie **Speichern** aus. Eine Meldung bestätigt, dass Ihr neuer Zeitplan jetzt aktiv ist.

   Wenn Sie einen Zeitplan in einer Region speichern, in der keine Wartungszeitpläne unterstützt werden, wird die folgende Meldung angezeigt. Ihre Einstellungen werden gespeichert und angewendet, sobald das Feature auch in Ihrer Region verfügbar ist.

   ![Meldung zur regionalen Verfügbarkeit](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen](../../azure-monitor/platform/alerts-metric.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) zum Erstellen, Anzeigen und Verwalten von Warnungen mit Azure Monitor
- [Weitere Informationen](../..//azure-monitor/platform/alerts-log-webhook.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) zu Webhookaktionen für Protokollwarnungsregeln
- [Weitere Informationen ](../..//azure-monitor/platform/action-groups.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) zum Erstellen und Verwalten von Aktionsgruppen.
- [Weitere Informationen](../../service-health/service-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) zu Azure Service Health
