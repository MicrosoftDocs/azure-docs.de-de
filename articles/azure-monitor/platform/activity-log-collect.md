---
title: Erfassen und Analysieren von Azure-Aktivitätsprotokollen im Log Analytics-Arbeitsbereich | Microsoft-Dokumentation
description: Erfassen Sie das Azure-Aktivitätsprotokoll in Azure Monitor-Protokollen, und verwenden Sie die Überwachungslösung, um das Azure-Aktivitätsprotokoll in all Ihren Azure-Abonnements zu analysieren und zu durchsuchen.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.openlocfilehash: 5839fd40a128097e400f13acbe4fb6ef90c656b7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245595"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Erfassen und Analysieren von Azure-Aktivitätsprotokollen im Log Analytics-Arbeitsbereich in Azure Monitor
Das [Azure-Aktivitätsprotokoll](activity-logs-overview.md) bietet Einblick in Ereignisse auf Abonnementebene, die in Ihrem Azure-Abonnement aufgetreten sind. Dieser Artikel beschreibt, wie Sie das Aktivitätsprotokoll in einem Log Analytics-Arbeitsbereich erfassen und die [Überwachungslösung](../insights/solutions.md) der Aktivitätsprotokollanalyse verwenden, die Protokollabfragen und Ansichten zum Analysieren dieser Daten bereitstellt. 

Das Verbinden des Aktivitätsprotokolls mit einem Log Analytics-Arbeitsbereich bietet folgende Vorteile:

- Konsolidieren des Aktivitätsprotokolls mehrerer Azure-Abonnements an einem einzigen Ort zur Analyse
- Speichern von Aktivitätsprotokolleinträgen für mehr als 90 Tage
- Korrelieren von Aktivitätsprotokolldaten mit anderen von Azure Monitor gesammelten Überwachungsdaten
- Verwenden von [Protokollabfragen](../log-query/log-query-overview.md) zum Ausführen komplexer Analysen und Erhalten tiefer Einblicke in Aktivitätsprotokolleinträge

## <a name="connect-to-log-analytics-workspace"></a>Verbinden mit einem Log Analytics-Arbeitsbereich
Ein Aktivitätsprotokoll kann mit nur einem Arbeitsbereich verbunden werden, doch kann ein einzelner Arbeitsbereich mit dem Aktivitätsprotokoll für mehrere Abonnements in demselben Azure-Mandanten verbunden werden. Informationen zur Erfassung für mehrere Mandanten finden Sie unter [Abonnementübergreifendes Erfassen von Azure-Aktivitätsprotokollen in einem Log Analytics-Arbeitsbereich in verschiedenen Azure Active Directory-Mandanten](activity-log-collect-tenants.md).

Gehen Sie folgendermaßen vor, um das Aktivitätsprotokoll mit Ihrem Log Analytics-Arbeitsbereich zu verbinden:

1. Wählen Sie im Azure-Portal im Menü **Log Analytics-Arbeitsbereiche** den Arbeitsbereich zum Erfassen des Aktivitätsprotokolls aus.
1. Wählen Sie im Menü des Arbeitsbereichs im Abschnitt **Arbeitsbereichsdatenquellen** die Option **Azure-Aktivitätsprotokoll** aus.
1. Klicken Sie auf das Abonnement, mit dem Sie eine Verbindung herstellen möchten.

    ![Arbeitsbereiche](media/activity-log-export/workspaces.png)

1. Klicken Sie auf **Verbinden**, um das Aktivitätsprotokoll im Abonnement mit dem ausgewählten Arbeitsbereich zu verbinden. Wenn das Abonnement bereits mit einem anderen Arbeitsbereich verbunden ist, klicken Sie zuerst auf **Trennen**, um die Verbindung zu trennen.

    ![Verbinden von Arbeitsbereichen](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Analysieren im Log Analytics-Arbeitsbereich
Wenn Sie ein Aktivitätsprotokoll mit einem Log Analytics-Arbeitsbereich verbinden, werden Einträge in den Arbeitsbereich in eine Tabelle namens **AzureActivity** geschrieben, die Sie mit einer [Protokollabfrage](../log-query/log-query-overview.md) abrufen können. Die Struktur dieser Tabelle ist je nach [Kategorie des Protokolleintrags](activity-logs-overview.md#categories-in-the-activity-log) verschieden. Eine Beschreibung der einzelnen Kategorien finden Sie unter [Ereignisschema des Azure-Aktivitätsprotokolls](activity-log-schema.md).

## <a name="activity-logs-analytics-monitoring-solution"></a>Überwachungslösung der Aktivitätsprotokollanalyse
Die Azure Log Analytics-Überwachungslösung umfasst mehrere Protokollabfragen und Ansichten zum Analysieren der Aktivitätsprotokolldatensätze in Ihrem Log Analytics-Arbeitsbereich.

### <a name="install-the-solution"></a>Installieren der Lösung
Verwenden Sie das unter [Installieren einer Überwachungslösung](../insights/solutions.md#install-a-monitoring-solution) beschriebene Verfahren, um die Überwachungslösung der **Aktivitätsprotokollanalyse** zu installieren. Es ist keine zusätzliche Konfiguration erforderlich.

### <a name="use-the-solution"></a>Verwenden der Lösung
Auf Überwachungslösungen wird im Azure-Portal über das Menü **Monitor** zugegriffen. Wählen Sie im Abschnitt **Insights** die Option **Mehr** aus, um die Seite **Übersicht** mit den Lösungskacheln zu öffnen. Die Kachel **Azure-Aktivitätsprotokolle** zeigt die Anzahl der **AzureActivity**-Datensätze in Ihrem Arbeitsbereich.

![Kachel „Azure-Aktivitätsprotokolle“](media/collect-activity-logs/azure-activity-logs-tile.png)


Klicken Sie auf die Kachel **Azure-Aktivitätsprotokolle**, um die Ansicht **Azure-Aktivitätsprotokolle** zu öffnen. Die Ansicht enthält die Visualisierungskomponenten, die in der folgenden Tabelle aufgeführt sind. Für jede Komponente sind bis zu 10 Einträge aufgeführt, die den Kriterien dieser Komponente für den angegebenen Zeitraum entsprechen. Sie können eine Protokollabfrage ausführen, die alle übereinstimmenden Datensätze zurückgibt. Dazu klicken Sie am unteren Rand der Komponente auf **Alle anzeigen**.

![Dashboard „Azure-Aktivitätsprotokolle“](media/collect-activity-logs/activity-log-dash.png)

| Visualisierungskomponente | BESCHREIBUNG |
| --- | --- |
| Azure Activity Log Entries (Azure-Aktivitätsprotokolleinträge) | Zeigt ein Balkendiagramm aller Datensätze des ersten Azure-Aktivitätsprotokolleintrags für den ausgewählten Datumsbereich sowie eine Liste der ersten zehn Aktivitätsaufrufer an. Klicken Sie auf das Balkendiagramm, um eine Protokollsuche für `AzureActivity` durchzuführen. Klicken Sie auf ein Aufruferelement, um eine Protokollsuche durchzuführen, mit der alle Aktivitätsprotokolleinträge für das Element zurückgegeben werden. |
| Activity Logs by Status (Aktivitätsprotokolle nach Status) | Zeigt ein Ringdiagramm für den Status der Azure-Aktivitätsprotokolle für den ausgewählten Datumsbereich sowie eine Liste der ersten zehn Statuseinträge an. Klicken Sie auf das Diagramm, um eine Protokollabfrage für `AzureActivity | summarize AggregatedValue = count() by ActivityStatus` durchzuführen. Klicken Sie auf ein Statuselement, um eine Protokollsuche durchzuführen, mit der alle Aktivitätsprotokolleinträge für das Statuselement zurückgegeben werden. |
| Activity Logs by Resource (Aktivitätsprotokolle nach Ressource) | Zeigt die Gesamtanzahl der Ressourcen mit Aktivitätsprotokollen an und listet die ersten zehn Ressourcen mit der Anzahl der Datensätze für jede Ressource auf. Klicken Sie auf den Gesamtbereich, um eine Protokollsuche für `AzureActivity | summarize AggregatedValue = count() by Resource` durchzuführen, in der alle für die Lösung verfügbaren Azure-Ressourcen angezeigt werden. Klicken Sie auf eine Ressource, um eine Protokollabfrage durchzuführen, mit der alle Aktivitätsdatensätze für die Ressource zurückgegeben werden. |
| Activity Logs by Resource Provider (Aktivitätsprotokolle nach Ressourcenanbieter) | Zeigt die Gesamtanzahl der Ressourcenanbieter an, die Aktivitätsprotokolle generieren, und listet die ersten zehn Anbieter auf. Klicken Sie auf den Gesamtbereich, um eine Protokollabfrage für `AzureActivity | summarize AggregatedValue = count() by ResourceProvider` durchzuführen, in der alle Azure-Ressourcenanbieter angezeigt werden. Klicken Sie auf einen Ressourcenanbieter, um eine Protokollabfrage durchzuführen, mit der alle Aktivitätsdatensätze für den Anbieter zurückgegeben werden. |

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich ausführlicher über das [Aktivitätsprotokoll](activity-logs-overview.md).
- Informieren Sie sich über die [Azure Monitor-Datenplattform](data-platform.md).
- Verwenden Sie [Protokollabfragen](../log-query/log-query-overview.md) zum Anzeigen ausführlicher Informationen aus dem Aktivitätsprotokoll.
