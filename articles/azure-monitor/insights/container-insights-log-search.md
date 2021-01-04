---
title: Abfragen von Protokollen aus Azure Monitor für Container | Microsoft-Dokumentation
description: Azure Monitor für Container erfasst Metriken und Protokolldaten. In diesem Artikel werden die Datensätze und einige Beispielabfragen beschrieben.
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 08c42fab84cb5180497f8da4f077b9bd82283ad4
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95747678"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Abfragen von Protokollen aus Azure Monitor für Container

Azure Monitor erfasst für Container Leistungsmetriken, Inventurdaten und Integritätszustandsinformationen der Containerhosts und Container. Die Daten werden alle drei Minuten erfasst und an den Log Analytics-Arbeitsbereich in Azure Monitor weitergeleitet. Diese Daten stehen in Azure Monitor für [Abfragen](../log-query/log-query-overview.md) zur Verfügung. Diese Daten können in verschiedenen Szenarios von Nutzen sein, z.B. bei der Migrationsplanung, Kapazitätsanalyse, Ermittlung und Ad-hoc-Behebung von Leistungsproblemen.

## <a name="container-records"></a>Containerdatensätze

In der folgenden Tabelle finden Sie Informationen zu Datensätzen, die von Azure Monitor für Container erfasst werden. Eine Auflistung der Spaltenbeschreibungen finden Sie in der Referenz zu den Tabellen [ContainerInventory](/azure/azure-monitor/reference/tables/containerinventory) und [ContainerLog](/azure/azure-monitor/reference/tables/containerlog).

| Daten | Datenquelle | Datentyp | Felder |
|------|-------------|-----------|--------|
| Containerinhalt | Kubelet | `ContainerInventory` | TimeGenerated, Computer, Containername, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Containerprotokoll | Docker | `ContainerLog` | TimeGenerated, Computer, Image-ID, Containername, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Containerknotenbestand | Kube-API | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Bestand der Pods in einem Kubernetes-Cluster | Kube-API | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Bestand der Knoten als Teil eines Kubernetes-Clusters | Kube-API | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes-Ereignisse | Kube-API | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Dienste im Kubernetes-Cluster | Kube-API | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Leistungsmetriken für die zum Kubernetes-Cluster zugehörigen Knoten | Nutzungsmetriken werden von cAdvisor und Grenzwerte von der Kube-API abgerufen | Perf &#124; where ObjectName == "K8SNode" | Computer, ObjectName, CounterName &#40;cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Leistungsmetriken für die zum Kubernetes-Cluster zugehörigen Container | Nutzungsmetriken werden von cAdvisor und Grenzwerte von der Kube-API abgerufen | Perf &#124; where ObjectName == "K8SContainer" | CounterName &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Benutzerdefinierte Metriken ||`InsightsMetrics` | Computer, Name, Namespace, Origin, SourceSystem, Tags<sup>1</sup>, TimeGenerated, Type, Va, _ResourceId | 

<sup>1</sup> Die Eigenschaft *Tags* repräsentiert [mehrere Dimensionen](../platform/data-platform-metrics.md#multi-dimensional-metrics) für die entsprechende Metrik. Weitere Informationen über die erfassten Metriken, die in der `InsightsMetrics`-Tabelle gespeichert werden, und eine Beschreibung der Datensatzeigenschaften finden Sie in der [Übersicht über InsightsMetrics](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

## <a name="search-logs-to-analyze-data"></a>Suchen von Protokollen zur Datenanalyse

Mit Azure Monitor-Protokollen können Sie nach Trends suchen, Engpässe diagnostizieren, Prognosen erstellen oder Daten korrelieren, die Ihnen die Einschätzung ermöglichen, ob die aktuelle Clusterkonfiguration optimal funktioniert. Ihnen werden vordefinierte Protokollsuchen für die sofortige Verwendung bereitgestellt. Alternativ können Sie diese so anpassen, dass die Informationen auf die gewünschte Weise zurückgegeben werden.

Im Arbeitsbereich können Sie interaktive Datenanalysen durchführen, indem Sie im Vorschaubereich in der Dropdownliste **In Analytics anzeigen** die Option **Kubernetes-Ereignisprotokolle anzeigen** oder **Containerprotokolle anzeigen** auswählen. Die Seite **Protokollsuche** wird rechts von der Azure-Portalseite angezeigt, die Sie besucht haben.

![Analysieren von Daten in Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)

Die an Ihren Arbeitsbereich weitergeleiteten Containerprotokollausgaben sind STDOUT und STDERR. Da Azure Monitor AKS (Azure Kubernetes Service) überwacht, wird „kube-system“ aufgrund des großen Umfangs der generierten Daten zurzeit nicht gesammelt. 

### <a name="example-log-search-queries"></a>Beispielabfragen für die Protokollsuche

Es ist oft hilfreich, die Abfrageerstellung ausgehend von einem oder zwei Beispielen zu beginnen und diese dann den Anforderungen entsprechend anzupassen. Sie können mit den folgenden Beispielabfragen experimentieren, um komplexere Abfragen zu erstellen:

| Abfrage | BESCHREIBUNG | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | Liste mit Lebenszyklusinformationen aller Container| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Kubernetes-Ereignisse|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | Imagebestand | 
| **Wählen Sie die Anzeigeoption Liniendiagramm aus**:<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Container-CPU | 
| **Wählen Sie die Anzeigeoption Liniendiagramm aus**:<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Containerspeicher |
| InsightsMetrics<br> &#124; where Name == "requests_count"<br> &#124; summarize Val=any(Val) by TimeGenerated=bin(TimeGenerated, 1m)<br> &#124; sort by TimeGenerated asc<br> &#124; project RequestsPerMinute = Val - prev(Val), TimeGenerated <br> &#124; render barchart  | Anforderungen pro Minute mit benutzerdefinierten Metriken |

## <a name="query-prometheus-metrics-data"></a>Abfragen von Prometheus-Metrikdaten

Das folgende Beispiel zeigt eine Prometheus-Metrikabfrage mit Datenträgerlesevorgänge pro Sekunde und Datenträger pro Knoten.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Zum Anzeigen von Prometheus-Metriken, die von Azure Monitor nach Namespace gefiltert abgerufen wurden, geben Sie „prometheus“ an. Hier sehen Sie eine Beispielabfrage zum Anzeigen von Prometheus-Metriken aus dem `default`-Kubernetes-Namespace.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Prometheus-Daten können außerdem direkt anhand des Namens abgefragt werden.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Abfragen von Konfigurations- oder Abruffehlern

Zum Untersuchen von Konfigurations- oder Abruffehlern werden mit der folgenden Beispielabfrage Informationsereignisse aus der Tabelle `KubeMonAgentEvents` zurückgegeben.

```
KubeMonAgentEvents | where Level != "Info" 
```

Die Ausgabe enthält Ergebnisse, die dem folgenden Beispiel ähneln:

![Protokollabfrageergebnisse von Informationsereignissen vom Agent](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Nächste Schritte

Azure Monitor für Container umfasst keinen vordefinierten Satz von Warnungen. Informationen zum Erstellen von empfohlenen Warnungen für hohe CPU- und Arbeitsspeicherauslastung zur Unterstützung Ihrer DevOps oder Betriebsprozesse und -verfahren finden Sie unter [Erstellen von Leistungswarnungen mit Azure Monitor für Container](./container-insights-log-alerts.md).