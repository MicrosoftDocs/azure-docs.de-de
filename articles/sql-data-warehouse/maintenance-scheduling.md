---
title: Azure-Wartungszeitpläne (Vorschau) | Microsoft-Dokumentation
description: Wartungszeitpläne ermöglichen es Kunden, die notwendigen geplanten Wartungsereignisse in ihre Planungen einzubeziehen, mit denen der Azure SQL Data Warehouse-Dienst neue Features, Upgrades und Patches einführt.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 03/13/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: b97e27b86ecad1f7f87a6de4d43b09d69c167c6f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61075327"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Verwenden von Wartungszeitplänen zum Verwalten der Updates und Wartung von Diensten

Wartungszeitpläne sind jetzt für alle Azure SQL Data Warehouse-Regionen verfügbar. Dieses Feature kann in Benachrichtigungen über eine geplante Wartung von Service Health, in Resource Health Check Monitor und in den Zeitplanungsdienst für Azure SQL Data Warehouse-Wartungen integriert werden.

Sie verwenden Wartungszeitpläne, um ein passendes Zeitfenster für den Erhalt neuer Features, Upgrades und Patches zu wählen. Sie wählen ein primäres und ein sekundäres Wartungsfenster für einen Zeitraum von sieben Tagen. Ein Beispiel ist Samstag 22:00 bis Sonntag 01:00 Uhr als primäres Fenster und Mittwoch 19:00 bis 22:00 Uhr als sekundäres Fenster. Wenn SQL Data Warehouse die Wartung nicht während Ihres primären Wartungsfensters durchführen kann, wird versucht, die Wartung während des sekundären Wartungsfensters durchzuführen. Dienstwartungen können sowohl im primären als auch im sekundären Fenster auftreten. Um einen schnellen Abschluss aller Wartungsarbeiten zu gewährleisten, könnten DW400(c) und niedrigere Data Warehouse-Ebenen die Wartung außerhalb eines bestimmten Wartungsfensters abschließen.

Auf alle neu erstellten Azure SQL Data Warehouse-Instanzen muss während der Bereitstellung ein systemdefinierter Wartungszeitplan angewendet werden. Der Zeitplan kann direkt nach Abschluss der Bereitstellung bearbeitet werden.

Jedes Wartungsfenster kann zwischen drei und acht Stunden lang sein. Die Wartung kann zu einem beliebigen Zeitpunkt des Fensters erfolgen. Es kommt ggf. zu einem kurzzeitigen Verlust der Konnektivität, während der Dienst neuen Code in Ihrem Data Warehouse bereitstellt.

Zum Verwenden dieses Features müssen Sie ein primäres und sekundäres Fenster in separaten Tagesbereiche festlegen. Alle Wartungsvorgänge sollten innerhalb der geplanten Wartungsfenster abgeschlossen werden. Außerhalb der angegebenen Wartungsfenster werden ohne vorherige Benachrichtigung keine Wartungen durchgeführt. Wenn Ihr Data Warehouse während einer geplanten Wartung angehalten wird, wird es während des Wiederaufnahmevorgangs aktualisiert.  

## <a name="alerts-and-monitoring"></a>Benachrichtigungen und Überwachung

Dank der Integration in Service Health-Benachrichtigungen und in Resource Health Check Monitor sind Kunden immer über bevorstehende Wartungsaktivitäten informiert. Für die neue Automatisierung wird Azure Monitor genutzt. Sie können entscheiden, wie Sie über anstehende Wartungsereignisse benachrichtigt werden möchten. Darüber hinaus können Sie auch entscheiden, welche automatisierten Abläufe für Sie beim Verwalten der Ausfallzeit und Verringern der Auswirkung auf Ihre Vorgänge hilfreich sein können.

Eine Benachrichtigung 24 Stunden im Voraus erfolgt für alle Wartungsereignisse, mit Ausnahme von DW400c und niedrigeren Ebenen. Stellen Sie zum Verringern der Ausfallzeiten von Instanzen sicher, dass Ihr Data Warehouse vor dem gewählten Wartungszeitraum keine Transaktionen mit langer Ausführungsdauer aufweist. Wenn die Wartung beginnt, werden alle aktiven Sitzungen abgebrochen. Für Transaktionen, für die kein Commit durchgeführt wurde, wird ein Rollback ausgeführt, und für Ihr Data Warehouse kommt es zu einem kurzzeitigen Verlust der Konnektivität. Sie erhalten sofort eine Benachrichtigung, nachdem die Wartung für Ihr Data Warehouse abgeschlossen ist.

Wenn Sie eine Vorankündigung zur Durchführung von Wartungsarbeiten erhalten haben, die Wartung von SQL Data Warehouse im vorgesehenen Zeitraum aber nicht durchgeführt werden kann, erhalten Sie eine Widerrufsbenachrichtigung. Die Wartung wird dann während des nächsten geplanten Wartungszeitraums durchgeführt.

Alle aktiven Wartungsereignisse werden im Abschnitt **Service Health – Geplante Wartung** angezeigt. Der Service Health-Verlauf enthält eine vollständige Aufzeichnung der vergangenen Ereignisse. Sie können die Wartung während eines aktiven Ereignisses über das Dashboard des Azure Service Health Check-Portals überwachen.

### <a name="maintenance-schedule-availability"></a>Verfügbarkeit des Wartungszeitplans

Auch wenn Wartungszeitpläne in Ihrer ausgewählten Region noch nicht verfügbar sind, können Sie Ihren Wartungszeitplan jederzeit anzeigen und bearbeiten. Sobald Wartungszeitpläne in Ihrer Region verfügbar sind, wird der identifizierte Zeitplan in Ihrem Data Warehouse sofort aktiv.

## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen](viewing-maintenance-schedule.md) zum Anzeigen eines Wartungszeitplans
- [Weitere Informationen](changing-maintenance-schedule.md) zum Ändern eines Wartungszeitplans
- [Weitere Informationen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) zum Erstellen, Anzeigen und Verwalten von Warnungen mit Azure Monitor
- [Weitere Informationen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) zu Webhookaktionen für Protokollwarnungsregeln
- [Weitere Informationen ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) zum Erstellen und Verwalten von Aktionsgruppen.
- [Weitere Informationen](https://docs.microsoft.com/azure/service-health/service-health-overview) zu Azure Service Health
