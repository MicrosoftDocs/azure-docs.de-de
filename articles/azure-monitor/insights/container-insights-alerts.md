---
title: Erstellen von Leistungswarnungen mit Azure Monitor für Container | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie mithilfe von Azure Monitor für Container benutzerdefinierte Warnungen basierend auf Protokollabfragen für die Arbeitsspeicher- und CPU-Auslastung erstellen können.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2019
ms.author: magoedte
ms.openlocfilehash: 46ac6794272728069d50479f8cd097185bfeeb1a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072388"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Einrichten von Warnungen für Leistungsprobleme in Azure Monitor für Container
Azure Monitor für Container überwacht die Leistung von Containerworkloads, die in Azure Container Instances oder in Managed Kubernetes-Clustern bereitgestellt sind, die im Azure Kubernetes Service (AKS) gehostet werden.

In diesem Artikel wird beschrieben, wie Sie Warnungen für die folgenden Situationen aktivieren:

- Wenn die CPU- oder Arbeitsspeicherauslastung für Clusterknoten einen Schwellenwert überschreitet
- Wenn die CPU- oder Arbeitsspeicherauslastung für einen Container innerhalb eines Controllers einen Schwellenwert relativ zu einem für die entsprechende Ressource festgelegten Grenzwert überschreitet
- Knotenanzahl mit Status *NotReady*
- Podphasenanzahl mit Status *Failed*, *Pending*, *Unknown*, *Running* oder *Succeeded*
- Wenn der freie Speicherplatz für Clusterknoten einen Schwellenwert überschreitet 

Verwenden Sie zum Auslösen einer Warnung bei hoher CPU- oder Arbeitsspeicherauslastung oder wenig freiem Speicherplatz für Clusterknoten die bereitgestellten Abfragen, um eine Metrikwarnung oder eine Warnung aufgrund von metrischen Messungen zu erstellen. Bei Metrikwarnungen gibt es eine kürzere Wartezeit als bei Protokollwarnungen. Allerdings ermöglichen Protokollwarnungen erweiterte Abfragen und größere Professionalität. Protokollwarnungsabfragen vergleichen mithilfe des *now*-Operators einen „datetime“-Wert mit dem vorhandenen Wert und gehen um eine Stunde zurück. (Azure Monitor für Container speichert alle Datumsangaben im Format der koordinierten Weltzeit (Coordinated Universal Time, UTC).)

Wenn Sie mit Azure Monitor-Warnungen nicht vertraut sind, lesen Sie zunächst den [Überblick über Warnungen in Microsoft Azure](../platform/alerts-overview.md). Weitere Informationen zu Warnungen, bei denen Protokollabfragen verwendet werden, finden Sie unter [Protokollwarnungen in Azure Monitor](../platform/alerts-unified-log.md). Weitere Informationen zu Metrikwarnungen finden Sie unter [Metrikwarnungen in Azure Monitor](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Suchabfragen für die Ressourcenverwendung
Die Abfragen in diesem Abschnitt unterstützen die einzelnen Warnungsszenarien. Sie werden in Schritt 7 des Abschnitts [Erstellen einer Warnung](#create-an-alert-rule) in diesem Artikel verwendet.

Die folgende Abfrage berechnet die durchschnittliche CPU-Auslastung als durchschnittliche CPU-Auslastung von Memberknoten pro Minute.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

Die folgende Abfrage berechnet die durchschnittliche Arbeitsspeicherauslastung als durchschnittliche Arbeitsspeicherauslastung von Memberknoten pro Minute.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>Die folgenden Abfragen verwenden die Platzhalterwerte \<<Ihr-Cluster-Name> und <Ihr-Controller-Name>\< zur Darstellung Ihres Clusters und Controllers. Ersetzen Sie sie durch spezifische Werte für Ihre Umgebung, wenn Sie Warnungen einrichten.

Die folgende Abfrage berechnet die durchschnittliche CPU-Auslastung aller Container in einem Controller als durchschnittliche CPU-Auslastung der einzelnen Containerinstanzen in einem Controller pro Minute. Die Maßeinheit ist ein Prozentsatz des für einen Container eingerichteten Grenzwerts.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Die folgende Abfrage berechnet die durchschnittliche Arbeitsspeicherauslastung aller Container in einem Controller als durchschnittliche Arbeitsspeicherauslastung der einzelnen Containerinstanzen in einem Controller pro Minute. Die Maßeinheit ist ein Prozentsatz des für einen Container eingerichteten Grenzwerts.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Die folgende Abfrage gibt alle Knoten und deren Anzahl zurück, bei denen der Status *Ready* und *NotReady* lautet.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
Die folgende Abfrage gibt die Podphasenanzahl, basierend auf allen Phasen, zurück: *Failed*, *Pending*, *Unknown*, *Running* oder *Succeeded*.  

```kusto
let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let clusterName = '<your-cluster-name>';
    KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                 by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Ändern Sie zum Ausgeben von Warnungen bei bestimmten Podphasen (z.B. *Pending*, *Failed* oder *Unknown*) die letzte Zeile der Abfrage. Verwenden Sie beispielsweise zum Ausgeben einer Warnung für *FailedCount*: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

Die folgende Abfrage gibt Clusterknotendatenträger mit einer Speicherplatzbelegung von über 90 Prozent zurück. Führen Sie zum Abrufen der Cluster-ID zunächst die folgende Abfrage aus, und kopieren Sie den Wert aus der Eigenschaft `ClusterId`:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Erstellen einer Warnungsregel
Führen Sie die folgenden Schritte aus, um eine Protokollwarnung in Azure Monitor mit einer der zuvor angegebenen Regeln für die Protokollsuche zu erstellen.  

>[!NOTE]
>Das folgende Verfahren zum Erstellen einer Warnungsregel für die Auslastung von Containerressourcen erfordert, dass Sie – wie unter [Wechseln der API-Einstellung für Protokollwarnungen](../platform/alerts-log-api-switch.md) beschrieben – zu einer neuen Protokollwarnungs-API wechseln.
>

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Bereich auf der linken Seite **Monitor** aus. Wählen Sie unter **Insights** die Option **Container** aus.
3. Wählen Sie auf der Registerkarte **Überwachte Cluster** den gewünschten Cluster aus der Liste aus.
4. Wählen Sie im Bereich auf der linken Seite unter **Überwachung** die Option **Protokolle** aus, um die Seite mit Azure Monitor-Protokollen zu öffnen. Über diese Seite können Sie Azure Log Analytics-Abfragen schreiben und ausführen.
5. Wählen Sie auf der Seite **Protokolle** die Option **+Neue Warnungsregel** aus.
6. Wählen Sie im Abschnitt **Bedingung** die vordefinierte benutzerdefinierte Protokollbedingung **Immer wenn die benutzerdefinierte Protokollsuche ist \<<Logik nicht definiert>** aus. Der Signaltyp **benutzerdefinierte Protokollsuche** ist automatisch ausgewählt, weil eine Warnungsregel direkt über die Seite mit Azure Monitor-Protokollen erstellt wird.  
7. Fügen Sie eine der zuvor bereitgestellten [Abfragen](#resource-utilization-log-search-queries) in das Feld **Suchabfrage** ein.
8. Konfigurieren Sie die Warnung folgendermaßen:

    1. Wählen Sie in der Dropdownliste **Basierend auf** die Option **Metrische Maßeinheit** aus. Mit „Metrische Maßeinheit“ wird eine Warnung für jedes Objekt in der Abfrage erstellt, dessen Wert über dem angegebenen Schwellenwert liegt.
    1. Wählen Sie unter **Bedingung** den Eintrag **Größer als** aus, und geben Sie als **Schwellenwert** der anfänglichen Baseline für die CPU- und Arbeitsspeicherauslastungswarnungen den Wert **75** ein. Geben Sie für die Warnung bei wenig freiem Speicherplatz den Wert **90** ein. Oder geben Sie einen anderen Wert ein, der Ihren Kriterien entspricht.
    1. Wählen Sie im Abschnitt **Warnung auslösen basierend auf** die Option **Aufeinanderfolgende Sicherheitsverletzungen** aus. Wählen Sie aus der Dropdown-Liste **Größer als** aus, und geben Sie **2** ein.
    1. Wenn Sie eine Warnung für die CPU- oder Arbeitsspeicherauslastung des Containers konfigurieren möchten, wählen Sie unter **Aggregieren auf** die Option **ContainerName** aus. Wählen Sie **ClusterId** aus, um die Warnung bei wenig freiem Speicherplatz für den Clusterknoten zu konfigurieren.
    1. Legen Sie im Abschnitt **Auswertung basierend auf** den Wert **Zeitraum** auf **60 Minuten** fest. Die Regel wird alle 5 Minuten ausgeführt und gibt Datensätze zurück, die innerhalb der letzten Stunde aus dem aktuellen Zeitbereich erstellt wurden. Durch Festlegen des Zeitraums auf ein breites Zeitfenster wird eine potenzielle Datenlatenz berücksichtigt. Außerdem wird dadurch sichergestellt, dass die Abfrage Daten zurückgibt, und so ein falsch negatives Ergebnis vermieden, bei dem die Warnung nie ausgelöst wird.

9. Wählen Sie **Fertig** aus, um die Warnungsregel fertig zu stellen.
10. Geben Sie im Feld **Name der Warnungsregel** einen Namen ein. Geben Sie eine **Beschreibung** mit Details zur Warnung an. Und wählen Sie einen entsprechenden Schweregrad aus den bereitgestellten Optionen aus.
11. Wenn Sie die Warnungsregel sofort aktivieren möchten, übernehmen Sie den Standardwert für **Regel beim Erstellen aktivieren**.
12. Wählen Sie eine vorhandene **Aktionsgruppe** aus, oder erstellen Sie eine neue Gruppe. Dadurch wird sichergestellt, dass bei jeder Auslösung einer Warnung die gleichen Aktionen ausgeführt werden. Führen Sie die Konfiguration basierend darauf aus, wie Ihr IT- oder DevOps-Betriebsteam Vorfälle verwaltet.
13. Wählen Sie **Warnungsregel erstellen** aus, um die Warnungsregel fertig zu stellen. Die Ausführung beginnt sofort.

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Beispiele zu Protokollabfragen](container-insights-log-search.md#search-logs-to-analyze-data) an, die auch vordefinierte Abfragen enthalten. Mit diesen Materialien können Sie Auswertungen von bzw. Anpassungen für Warnungen, Visualisierungen und Analysen von Clustern vornehmen.
- Weitere Informationen zu Azure Monitor und zum Überwachen anderer Aspekte Ihres AKS-Clusters finden Sie unter [Anzeigen der Azure Kubernetes Service-Integrität](container-insights-analyze.md).
