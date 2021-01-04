---
title: Konfigurieren der PV-Überwachung mit Azure Monitor für Container | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie die Überwachung von Kubernetes-Clustern mit persistenten Volumes mit Azure Monitor für Container konfigurieren können.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: e7c547c137fc84e6e6dfb2807b871ef0329a3c13
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186846"
---
# <a name="configure-pv-monitoring-with-azure-monitor-for-containers"></a>Konfigurieren der PV-Überwachung mit Azure Monitor für Container

Ab der Agent-Version *ciprod10052020* unterstützt der integrierte Agent von Azure Monitor für Container die Überwachung der Nutzung persistenter Volumes (PVs).

## <a name="pv-metrics"></a>PV-Metriken

Azure Monitor für Container beginnt automatisch mit der Überwachung von PVs. Hierzu werden die folgenden Metriken im 60-Sekunden-Takt gesammelt und in der Tabelle **InsightMetrics** gespeichert:

|Metrikname |Metrikdimension (Tags) |Beschreibung |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|Verwendeter Speicherplatz in Bytes für ein bestimmtes persistentes Volume mit einem von einem bestimmten Pod verwendeten Anspruch. `pvCapacityBytes` wird als Dimension in das Feld „Tags“ integriert, um die Datenerfassungskosten zu verringern und Abfragen zu vereinfachen.|

## <a name="monitor-persistent-volumes"></a>Überwachen persistenter Volumes

Azure Monitor für Container enthält vorkonfigurierte Diagramme für diese Metrik in einer Arbeitsmappe für jeden Cluster. Die Diagramme befinden sich in der Arbeitsmappe **Workloaddetails** auf der Registerkarte „Persistentes Volume“. Dorthin können Sie direkt von einem AKS-Cluster aus navigieren, indem Sie im linken Bereich die Option „Arbeitsmappen“ auswählen. Alternativ können Sie in der Erkenntnis die Dropdownliste **Arbeitsmappen anzeigen** verwenden. Sie können auch eine empfohlene Warnung für die PV-Nutzung aktivieren sowie diese Metriken in Log Analytics abfragen.  

![Azure Monitor: Beispielworkbook für PV-Workload](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu erfassten PV-Metriken finden Sie [hier](./container-insights-agent-config.md).