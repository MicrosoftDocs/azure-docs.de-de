---
title: Überwachen von Containern mit Azure Monitor-Protokollen
description: Verwenden Sie Azure Monitor-Protokolle zum Überwachen von Containern, die in Azure Service Fabric-Clustern ausgeführt werden.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: 6217569dc50517c88a5a8a7bc0f3752e7e327f4e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626655"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Überwachen von Containern mit Azure Monitor-Protokollen
 
Dieser Artikel behandelt die Schritte, die erforderlich sind, um die für die Containerüberwachungslösung zu verwendenden Azure Monitor-Protokolle einzurichten, damit Containerereignisse angezeigt werden können. Informationen zum Einrichten des Clusters zum Erfassen von Containerereignissen finden Sie in diesem [schrittweisen Tutorial](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Einrichten der Containerüberwachungslösung

> [!NOTE]
> Azure Monitor-Protokolle müssen für Ihren Cluster eingerichtet und der Log Analytics-Agent muss in Ihren Knoten bereitgestellt sein. Ist dies nicht der Fall, führen Sie zuerst die Schritte unter [Einrichten von Azure Monitor-Protokollen](service-fabric-diagnostics-oms-setup.md) und [Hinzufügen des Log Analytics-Agents zu einem Cluster](service-fabric-diagnostics-oms-agent.md) aus.

1. Sobald Azure Monitor-Protokolle und der Log Analytics-Agent für Ihren Cluster eingerichtet sind, stellen Sie Ihre Container bereit. Warten Sie, bis die Container bereitgestellt wurden, bevor Sie mit dem nächsten Schritt fortfahren.

2. Suchen Sie in Azure Marketplace nach *Containerüberwachungslösung*, und klicken Sie auf die Ressource **Containerüberwachungslösung**, die unter der Kategorie „Überwachung + Verwaltung“ angezeigt wird.

    ![Hinzufügen der Containerlösung](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Erstellen Sie die Lösung im gleichen Arbeitsbereich, der bereits für den Cluster erstellt wurde. Durch diese Änderung wird der Agent automatisch veranlasst, mit dem Sammeln von Docker-Daten in den Containern zu beginnen. Nach etwa 15 Minuten sollten in der Lösung eingehende Protokolle und Statistiken angezeigt werden. Die Abbildung unten zeigt dies.

    ![Das grundlegende Log Analytics-Dashboard](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Der Agent ermöglicht das Sammeln verschiedener containerspezifischer Protokolle, die in Azure Monitor-Protokollen abgefragt oder zur Visualisierung von Leistungsindikatoren verwendet werden können. Folgende Protokolltypen werden gesammelt:

* ContainerInventory: zeigt Informationen über Containerspeicherort, -name und -images an
* ContainerImageInventory: zeigt Informationen zu bereitgestellten Images an, einschließlich IDs und Größen
* ContainerLog: zeigt bestimmte Fehlerprotokolle, Docker-Protokolle (stdout usw.) und andere Einträge an
* ContainerServiceLog: zeigt die Docker-Daemon-Befehle an, die ausgeführt wurden
* Perf: zeigt Leistungsindikatoren einschließlich Container-CPU, Arbeitsspeicher, Netzwerkdatenverkehr, Datenträger-E/A und benutzerdefinierter Metriken von den Hostcomputern an



## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [Containerüberwachungslösung in Log Analytics](../azure-monitor/containers/containers.md).
* Weitere Informationen zur Containerorchestrierung in Service Fabric finden Sie unter [Service Fabric und Container](service-fabric-containers-overview.md).
* Machen Sie sich mit den Funktionen zur [Protokollsuche und -abfrage](../azure-monitor/logs/log-query-overview.md) in Azure Monitor-Protokolle vertraut.
* Konfigurieren Sie Azure Monitor-Protokolle für die Einrichtung von Regeln zu [automatisierten Warnungen](../azure-monitor/alerts/alerts-overview.md) zur Unterstützung bei der Erkennung und Diagnose.
