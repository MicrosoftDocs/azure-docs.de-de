---
title: Warnungen aus Azure Monitor für VMs
description: Hier wird beschrieben, wie Sie Warnungsregeln anhand von Leistungsdaten erstellen, die von Azure Monitor für VMs gesammelt wurden.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: f582f0dc7547a607351fcfc4ff9d39e8c5a077df
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686176"
---
# <a name="how-to-create-alerts-from-azure-monitor-for-vms"></a>Erstellen von Warnungen aus Azure Monitor für VMs
[Warnungen in Azure Monitor](../platform/alerts-overview.md) informieren Sie proaktiv über interessante Daten und Muster in Ihren Überwachungsdaten. Azure Monitor für VMs enthält keine vorkonfigurierten Warnungsregeln, doch Sie können eigene Regeln basierend auf den gesammelten Daten erstellen. Dieser Artikel enthält Anleitungen zum Erstellen von Warnungsregeln sowie eine Reihe von Beispielabfragen.

> [!IMPORTANT]
> Die in diesem Artikel beschriebenen Warnungen basieren auf Protokollabfragen für Daten, die von Azure Monitor für VMs gesammelt wurden. Diese unterscheiden sich von den Warnungen, die durch das [Feature „Gastintegrität“ von Azure Monitor für VMs](vminsights-health-overview.md) erstellt werden, das sich derzeit in der Public Preview-Phase befindet. Wenn dieses Feature allgemein verfügbar wird, werden die Informationen für Warnungen konsolidiert.


## <a name="alert-rule-types"></a>Warnungsregeltypen
Azure Monitor verfügt über [verschiedene Typen von Warnungsregeln](../platform/alerts-overview.md#what-you-can-alert-on) basierend auf den Daten, die zum Erstellen der Warnung verwendet werden. Alle von Azure Monitor für VMs gesammelten Daten werden in Azure Monitor-Protokollen gespeichert, die [Protokollwarnungen](../platform/alerts-log.md) unterstützen. Sie können derzeit keine [Metrikwarnungen](../platform/alerts-log.md) für von Azure Monitor für VMs gesammelte Leistungsdaten verwenden, da die Daten nicht in Azure Monitor-Metriken erfasst werden. Zum Sammeln von Daten für Metrikwarnungen installieren Sie die [Diagnoseerweiterung](../platform/diagnostics-extension-overview.md) für Windows-VMs oder den [Telegraf-Agent](../platform/collect-custom-metrics-linux-telegraf.md) für Linux-VMs, um Leistungsdaten in Metriken zu erfassen.

In Azure Monitor gibt es zwei Typen von Protokollwarnungen:

- Bei [Warnungen des Typs „Anzahl von Ergebnissen“](../platform/alerts-unified-log.md#count-of-the-results-table-rows) wird eine einzelne Warnung erzeugt, wenn eine Abfrage mindestens eine angegebene Anzahl von Datensätzen zurückgibt. Diese eignen sich ideal für nicht numerische Daten, wie z. B. Windows- und Syslog-Ereignisse, die vom [Log Analytics-Agent](../platform/log-analytics-agent.md) gesammelt werden, oder zum Analysieren von Leistungstrends auf mehreren Computern.
- Bei [Warnungen des Typs „Metrische Maßeinheit“](../platform/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) wird eine separate Warnung für jeden Datensatz in einer Abfrage erzeugt, dessen Wert einen in der Warnungsregel definierten Schwellenwert überschreitet. Diese Warnungsregeln eignen sich ideal für Leistungsdaten, die von Azure Monitor für VMs gesammelt werden, da hiermit für jeden Computer individuelle Warnungen erstellt werden können.


## <a name="alert-rule-walkthrough"></a>Exemplarische Vorgehensweise für Warnungsregeln
In diesem Abschnitt wird die Erstellung einer Warnungsregel des Typs „Metrische Maßeinheit“ mithilfe von Leistungsdaten aus Azure Monitor für VMs erläutert. Sie können diese grundlegende Vorgehensweise für eine Vielzahl von Protokollabfragen verwenden und so Warnungen für verschiedene Leistungsindikatoren erstellen.

Erstellen Sie zunächst eine neue Warnungsregel anhand des unter [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../platform/alerts-log.md) beschriebenen Verfahrens. Wählen Sie als **Ressource** den Log Analytics-Arbeitsbereich aus, den Azure Monitor für VMs in Ihrem Abonnement verwendet. Da es sich bei der Zielressource für Protokollwarnungsregeln immer um einen Log Analytics-Arbeitsbereich handelt, muss die Protokollabfrage einen Filter für bestimmte virtuelle Computer oder VM-Skalierungsgruppen enthalten. 

Verwenden Sie für die **Bedingung** der Warnungsregel eine der Abfragen im [folgenden Abschnitt](#sample-alert-queries) als **Suchabfrage**. Die Abfrage muss eine numerische Eigenschaft mit dem Namen *AggregatedValue* zurückgeben. Hiermit sollten die Daten nach Computer zusammengefasst werden, damit Sie für jeden virtuellen Computer, der den Schwellenwert überschreitet, eine separate Warnung erstellen können.

Wählen Sie unter **Warnungslogik** die Option **Metrische Maßeinheit** aus, und geben Sie dann einen **Schwellenwert** an. Geben Sie im Feld **Warnung auslösen basierend auf** an, wie oft der Schwellenwert überschritten werden muss, bevor eine Warnung erzeugt wird. Wahrscheinlich ist es für Sie nicht wichtig, wenn der Prozessor einen Schwellenwert einmal überschreitet und dann in den Normalzustand zurückkehrt. Es ist jedoch von Bedeutung, wenn der Schwellenwert bei mehreren aufeinander folgenden Messungen überschritten wird.

Im Abschnitt **Auswertung basierend auf** ist festgelegt, wie oft die Abfrage ausgeführt wird, und auch das Zeitfenster für die Abfrage definiert. Im unten gezeigten Beispiel wird die Abfrage alle 15 Minuten ausgeführt, und es werden die in den letzten 15 Minuten gesammelten Leistungswerte ausgewertet.


![Warnungsregel des Typs „Metrische Maßeinheit“](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>Beispielabfragen für Warnungen
Die folgenden Abfragen können bei einer Warnungsregel des Typs „Metrische Maßeinheit“ verwendet werden, die auf von Azure Monitor für VMs gesammelten Leistungsdaten basiert. Es werden jeweils die Daten nach Computer zusammengefasst, sodass für jeden Computer mit einem Wert, der den Schwellenwert überschreitet, eine Warnung erzeugt wird.

### <a name="cpu-utilization"></a>CPU-Auslastung

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>Verfügbarer Arbeitsspeicher in MB

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>Verfügbarer Arbeitsspeicher in Prozent

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>Nutzung von logischem Datenträger – alle Datenträger auf jedem Computer

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>Nutzung von logischem Datenträger – einzelne Datenträger

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>IOPS des logischen Datenträgers

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>Datenrate des logischen Datenträgers

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>An Netzwerkschnittstellen empfangene Bytes – alle Schnittstellen

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>An Netzwerkschnittstellen empfangene Bytes – einzelne Schnittstellen

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>An Netzwerkschnittstellen gesendete Bytes – alle Schnittstellen

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>An Netzwerkschnittstellen gesendete Bytes – einzelne Schnittstellen

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>VM-Skalierungsgruppe
Ändern Sie die Abfrage mit Ihrer Abonnement-ID, der Ressourcengruppe und dem Namen der VM-Skalierungsgruppe.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>Bestimmte virtuelle Maschine
Ändern Sie die Abfrage mit Ihrer Abonnement-ID, der Ressourcengruppe und dem VM-Namen.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>CPU-Auslastung für alle Computeressourcen in einem Abonnement
Ändern Sie die Abfrage mit Ihrer Abonnement-ID.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>CPU-Auslastung für alle Computeressourcen in einer Ressourcengruppe
Ändern Sie die Abfrage mit Ihrer Abonnement-ID und der Ressourcengruppe.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Warnungen in Azure Monitor](../platform/alerts-overview.md).
- Erfahren Sie mehr über [Protokollabfragen mithilfe von Daten aus Azure Monitor für VMs](vminsights-log-search.md).
