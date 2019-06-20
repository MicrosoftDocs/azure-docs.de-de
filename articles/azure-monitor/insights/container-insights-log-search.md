---
title: Abfragen von Protokollen aus Azure Monitor für Container | Microsoft-Dokumentation
description: Azure Monitor für Container erfasst Metriken und Protokolldaten. In diesem Artikel werden die Datensätze und einige Beispielabfragen beschrieben.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: 66fc55d8c3dbb8487d1e796d5f30b08a94f717f6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60494768"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Abfragen von Protokollen aus Azure Monitor für Container
Azure Monitor für Container erfasst Leistungsmetriken, Inventurdaten und Informationen zum Integritätsstatus von Containerhosts und Containern und leitet diese an den Log Analytics-Arbeitsbereich in Azure Monitor weiter. Alle drei Minuten werden Daten gesammelt. Diese Daten stehen in Azure Monitor für [Abfragen](../../azure-monitor/log-query/log-query-overview.md) zur Verfügung. Diese Daten können in verschiedenen Szenarios von Nutzen sein, z.B. bei der Migrationsplanung, Kapazitätsanalyse, Ermittlung und Ad-hoc-Behebung von Leistungsproblemen.

## <a name="container-records"></a>Containerdatensätze

Die folgende Tabelle zeigt Beispiele für Datensätze, die von Azure Monitor für Container gesammelt werden, und Datentypen, die in Protokollsuchergebnissen angezeigt werden:

| Datentyp | Datentyp in der Protokollsuche | Felder |
| --- | --- | --- |
| Leistung von Hosts und Containern | `Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Containerinhalt | `ContainerInventory` | TimeGenerated, Computer, Containername, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Containerprotokoll | `ContainerLog` | TimeGenerated, Computer, Image-ID, Containername, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Containerknotenbestand | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Bestand der Pods in einem Kubernetes-Cluster | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Bestand der Knoten als Teil eines Kubernetes-Clusters | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes-Ereignisse | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Dienste im Kubernetes-Cluster | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Leistungsmetriken für die zum Kubernetes-Cluster zugehörigen Knoten | Perf &#124; where ObjectName == “K8SNode” | Computer, ObjectName, CounterName &#40;cpuAllocatableBytes, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Leistungsmetriken für die zum Kubernetes-Cluster zugehörigen Container | Perf &#124; where ObjectName == “K8SContainer” | CounterName &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| InsightsMetrics | Computer, Name, Namespace, Origin, SourceSystem, Tags, TimeGenerated, Type, Value |

## <a name="search-logs-to-analyze-data"></a>Suchen von Protokollen zur Datenanalyse
Mit Azure Monitor-Protokollen können Sie nach Trends suchen, Engpässe diagnostizieren, Prognosen erstellen oder Daten korrelieren, die Ihnen die Einschätzung ermöglichen, ob die aktuelle Clusterkonfiguration optimal funktioniert. Ihnen werden vordefinierte Protokollsuchen für die sofortige Verwendung bereitgestellt. Alternativ können Sie diese so anpassen, dass die Informationen auf die gewünschte Weise zurückgegeben werden. 

Im Arbeitsbereich können Sie interaktive Datenanalysen durchführen, indem Sie im Vorschaubereich die Option **Kubernetes-Ereignisprotokolle anzeigen** oder **Containerprotokolle anzeigen** auswählen. Die Seite **Protokollsuche** wird rechts von der Azure-Portalseite angezeigt, die Sie besucht haben.

![Analysieren von Daten in Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

Die an Ihren Arbeitsbereich weitergeleiteten Containerprotokollausgaben sind STDOUT und STDERR. Da Azure Monitor AKS (Azure Kubernetes Service) überwacht, wird „kube-system“ aufgrund des großen Umfangs der generierten Daten zurzeit nicht gesammelt. 

### <a name="example-log-search-queries"></a>Beispielabfragen für die Protokollsuche
Es ist oft hilfreich, die Abfrageerstellung ausgehend von einem oder zwei Beispielen zu beginnen und diese dann den Anforderungen entsprechend anzupassen. Sie können mit den folgenden Beispielabfragen experimentieren, um komplexere Abfragen zu erstellen:

| Abfragen | BESCHREIBUNG | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | Liste mit Lebenszyklusinformationen aller Container| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Kubernetes-Ereignisse|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | Imagebestand | 
| **Wählen Sie die Anzeigeoption Liniendiagramm aus**:<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Container-CPU | 
| **Wählen Sie die Anzeigeoption Liniendiagramm aus**:<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Containerspeicher |

## <a name="next-steps"></a>Nächste Schritte
Azure Monitor für Container umfasst keinen vordefinierten Satz von Warnungen. Informationen zum Erstellen von empfohlenen Warnungen für hohe CPU- und Arbeitsspeicherauslastung zur Unterstützung Ihrer DevOps oder Betriebsprozesse und -verfahren finden Sie unter [Erstellen von Leistungswarnungen mit Azure Monitor für Container](container-insights-alerts.md). 