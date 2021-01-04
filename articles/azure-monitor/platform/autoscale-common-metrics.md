---
title: Allgemeine Metriken für die automatische Skalierung
description: Erfahren Sie, welche Metriken häufig für die automatische Skalierung von Cloud Services, Virtual Machines und Web-Apps verwendet werden.
ms.topic: conceptual
ms.date: 12/6/2016
ms.subservice: autoscale
ms.openlocfilehash: e1a77fc1b40faca0a339c5e1aaceb71dec8de8bd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004635"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Allgemeine Metriken für die automatische Skalierung in Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Mit der automatischen Skalierung in Azure Monitor können Sie die Anzahl der ausgeführten Instanzen basierend auf Telemetriedaten (Metriken) zentral hoch- oder herunterskalieren. Dieses Dokument beschreibt allgemeine Metriken, die Sie verwenden möchten. Im Azure-Portal können Sie die Metrik der Ressource für die Skalierung auswählen. Sie können jedoch auch eine Metrik aus einer anderen Ressource für die Skalierung auswählen.

Die automatische Skalierung von Azure Monitor gilt nur für [VM.Skalierungsgruppen](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Clouddienste](https://azure.microsoft.com/services/cloud-services/), [App Service – Web-Apps](https://azure.microsoft.com/services/app-service/web/) und [API Management-Dienste](../../api-management/api-management-key-concepts.md). Andere Azure-Dienste verwenden andere Skalierungsmethoden.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Berechnen von Metriken für Resource Manager-basierte virtuelle Computer
Standardmäßig geben Resource Manager-basierte virtuelle Computer und VM-Skalierungsgruppen grundlegende Metriken (also Metriken auf der Hostebene) aus. Wenn Sie die Erfassung von Diagnosedaten für einen virtuellen Azure-Computer und eine VM-Skalierungsgruppe konfigurieren, gibt die Azure-Diagnoseerweiterung darüber hinaus Leistungsmetriken für das Gastbetriebssystem (so genannte „Gastbetriebssystem-Metriken“) aus.  Diese Metriken werden alle in Regeln für die automatische Skalierung verwendet.

Sie können die `Get MetricDefinitions`-API/PoSH/CLI zum Anzeigen der Metriken für Ihre verfügbare VMSS-Ressource verwenden.

Wenn Sie VM-Skalierungsgruppen verwenden und eine bestimmte Metrik nicht aufgeführt wird, ist sie wahrscheinlich in der Diagnoseerweiterung *deaktiviert*.

Wenn eine bestimmte Metrik nicht mit der gewünschten Häufigkeit geprüft oder übertragen wird, können Sie die Diagnosekonfiguration aktualisieren.

Wenn einer der oben genannten Fälle zutrifft, lesen Sie unter [Aktivieren der Azure-Diagnose auf einem virtuellen Azure-Computer unter Windows mithilfe von PowerShell](../../virtual-machines/extensions/diagnostics-windows.md) weiter, um mehr darüber zu erfahren, wie Sie mit PowerShell die Azure-VM-Diagnoseerweiterung zur Aktivierung der Metrik konfigurieren und aktualisieren. Dieser Artikel enthält auch eine beispielhafte Diagnosekonfigurationsdatei.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Hostmetriken für Resource Manager-basierte virtuelle Windows- und Linux-Computer
Die folgenden Metriken auf Hostebene werden standardmäßig für virtuelle Azure-Computer und VM-Skalierungsgruppen ausgegeben (sowohl in Windows- als auch in Linux-Instanzen). Diese Metriken beschreiben Ihren virtuellen Azure-Computer, werden aber nicht per installiertem Agent auf dem virtuellen Gastcomputer, sondern vom Azure-VM-Host erfasst. Sie können diese Metriken in Regeln für die automatische Skalierung verwenden.

- [Hostmetriken für Resource Manager-basierte virtuelle Windows- und Linux-Computer](./metrics-supported.md#microsoftcomputevirtualmachines)
- [Hostmetriken für Resource Manager-basierte VM Scale Sets-Instanzen unter Windows oder Linux](./metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-for-resource-manager-based-windows-vms"></a>Gastbetriebssystem-Metriken für Resource Manager-basierte virtuelle Windows-Computer
Wenn Sie in Azure einen virtuellen Computer erstellen, wird die Diagnose durch Verwenden der Diagnoseerweiterung aktiviert. Die Diagnoseerweiterung gibt einen Satz von Metriken aus dem virtuellen Computer aus. Das bedeutet, dass Sie für die automatische Skalierung Metriken verwenden können, die standardmäßig nicht ausgegeben werden.

Sie können eine Liste der Metriken mithilfe des folgenden Befehls in PowerShell generieren.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Sie können eine Warnung für die folgenden Metriken erstellen:

| Metrikname | Einheit |
| --- | --- |
| \Processor(_Total)\%Prozessorzeit |Percent |
| \Processor(_Total)\%Privilegierte Zeit |Percent |
| \Processor(_Total)\%Benutzerzeit |Percent |
| \Processor Informationen(_Total)\Prozessorfrequenz |Anzahl |
| \System\Prozesse |Anzahl |
| \Process(_Total)\Threadanzahl |Anzahl |
| \Process(_Total)\Handleanzahl |Anzahl |
| \Memory\%Verwendete zugesicherte Bytes |Percent |
| \Memory\Verfügbare Bytes |Byte |
| \Memory\Zugesicherte Bytes |Byte |
| \Memory\Zusagegrenze |Byte |
| \Memory\Auslagerungsseiten (Bytes) |Byte |
| \Memory\Nicht-Auslagerungsseiten (Bytes) |Byte |
| \PhysicalDisk(_Total)\%Zeit |Percent |
| \PhysicalDisk(_Total)\%Lesezeit |Percent |
| \PhysicalDisk(_Total)\%Schreibzeit |Percent |
| \Physikalischer Datenträger(_Total)\Übertragungen/s |Anzahl pro Sekunde |
| \PhysicalDisk(_Total)\Lesevorgänge/s |Anzahl pro Sekunde |
| \PhysicalDisk(_Total)\Schreibvorgänge/s |Anzahl pro Sekunde |
| \PhysicalDisk(_Total)\Bytes/s |Bytes pro Sekunde |
| \PhysicalDisk(_Total)\Byte gelesen/s |Bytes pro Sekunde |
| \PhysicalDisk(_Total)\Byte geschrieben/s |Bytes pro Sekunde |
| \PhysicalDisk(_Total)\Durchschnittl. Warteschlangenlänge des Datenträgers |Anzahl |
| \PhysicalDisk(_Total)\Durchschnittl. Warteschlangenlänge der Datenträger-Lesevorgänge |Anzahl |
| \PhysicalDisk(_Total)\Durchschnittl. Warteschlangenlänge der Datenträger-Schreibvorgänge |Anzahl |
| \LogicalDisk(_Total)\%Freier Speicherplatz |Percent |
| \LogicalDisk(_Total)\MB frei |Anzahl |

### <a name="guest-os-metrics-linux-vms"></a>Gastbetriebssystem-Metriken für virtuelle Linux-Computer
Wenn Sie in Azure einen virtuellen Computer erstellen, wird die Diagnose standardmäßig durch Verwenden der Diagnoseerweiterung aktiviert.

Sie können eine Liste der Metriken mithilfe des folgenden Befehls in PowerShell generieren.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Sie können eine Warnung für die folgenden Metriken erstellen:

| Metrikname | Einheit |
| --- | --- |
| \Memory\AvailableMemory |Byte |
| \Memory\PercentAvailableMemory |Percent |
| \Memory\UsedMemory |Byte |
| \Memory\PercentUsedMemory |Percent |
| \Memory\PercentUsedByCache |Percent |
| \Memory\PagesPerSec |Anzahl pro Sekunde |
| \Memory\PagesReadPerSec |Anzahl pro Sekunde |
| \Memory\PagesWrittenPerSec |Anzahl pro Sekunde |
| \Memory\AvailableSwap |Byte |
| \Memory\PercentAvailableSwap |Percent |
| \Memory\UsedSwap |Byte |
| \Memory\PercentUsedSwap |Percent |
| \Processor\PercentIdleTime |Percent |
| \Processor\PercentUserTime |Percent |
| \Processor\PercentNiceTime |Percent |
| \Processor\PercentPrivilegedTime |Percent |
| \Processor\PercentInterruptTime |Percent |
| \Processor\PercentDPCTime |Percent |
| \Processor\PercentProcessorTime |Percent |
| \Processor\PercentIOWaitTime |Percent |
| \PhysicalDisk\BytesPerSecond |Bytes pro Sekunde |
| \PhysicalDisk\ReadBytesPerSecond |Bytes pro Sekunde |
| \PhysicalDisk\WriteBytesPerSecond |Bytes pro Sekunde |
| \PhysicalDisk\TransfersPerSecond |Anzahl pro Sekunde |
| \PhysicalDisk\ReadsPerSecond |Anzahl pro Sekunde |
| \PhysicalDisk\WritesPerSecond |Anzahl pro Sekunde |
| \PhysicalDisk\AverageReadTime |Sekunden |
| \PhysicalDisk\AverageWriteTime |Sekunden |
| \PhysicalDisk\AverageTransferTime |Sekunden |
| \PhysicalDisk\AverageDiskQueueLength |Anzahl |
| \NetworkInterface\BytesTransmitted |Byte |
| \NetworkInterface\BytesReceived |Byte |
| \NetworkInterface\PacketsTransmitted |Anzahl |
| \NetworkInterface\PacketsReceived |Anzahl |
| \NetworkInterface\BytesTotal |Byte |
| \NetworkInterface\TotalRxErrors |Anzahl |
| \NetworkInterface\TotalTxErrors |Anzahl |
| \NetworkInterface\TotalCollisions |Anzahl |

## <a name="commonly-used-app-service-server-farm-metrics"></a>Häufig verwendete App Service-Metriken (Serverfarm)
Sie können die automatische Skalierung auch basierend auf allgemeinen Webservermetriken wie der HTTP-Warteschlangenlänge ausführen. Der Metrikname ist **HttpQueueLength**.  Im folgenden Abschnitt sind die verfügbaren Serverfarmmetriken (App Service) aufgeführt.

### <a name="web-apps-metrics"></a>Web-Apps-Metriken
Sie können eine Liste der Web-Apps-Metriken mithilfe des folgenden Befehls in PowerShell generieren.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Anhand dieser Metriken können Sie Warnungen ausgeben oder skalieren.

| Metrikname | Einheit |
| --- | --- |
| CpuPercentage |Percent |
| MemoryPercentage |Percent |
| DiskQueueLength |Anzahl |
| HttpQueueLength |Anzahl |
| BytesReceived |Byte |
| BytesSent |Byte |

## <a name="commonly-used-storage-metrics"></a>Häufig verwendete Speichermetriken
Sie können nach Speicherwarteschlangenlänge skalieren, wobei es sich um die Anzahl der Nachrichten in der Speicherwarteschlange handelt. Die Speicherwarteschlangenlänge ist eine spezielle Metrik, und der Schwellenwert entspricht der Anzahl von Nachrichten pro Instanz. Ein Beispiel: Bei zwei Instanzen und einem Schwellenwert von 100 kommt es zu einer Skalierung, wenn die Gesamtanzahl von Nachrichten in der Warteschlange 200 beträgt. Hierbei kann es sich dann um 100 Nachrichten pro Instanz, um 120 und 80 Nachrichten oder um eine beliebige andere Kombination handeln, die zusammengenommen mindestens 200 ergibt.

Diese Einstellung kann im Azure-Portal auf dem Blatt **Einstellungen** konfiguriert werden. Für VM-Skalierungsgruppen können Sie die Einstellung für die automatische Skalierung in der Resource Manager-Vorlage aktualisieren, sodass *metricName* als *ApproximateMessageCount* verwendet und die ID der Speicherwarteschlange als *metricResourceUri* übergeben wird.

Bei einem klassischen Speicherkonto würde die metricTrigger-Einstellung für die automatische Skalierung Folgendes enthalten:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

Bei einem (nicht klassischen) Speicherkonto würde metricTrigger Folgendes enthalten:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Häufig verwendete Service Bus-Metriken
Sie können nach Service Bus-Warteschlangenlänge skalieren, wobei es sich um die Anzahl der Nachrichten in der Service Bus-Warteschlange handelt. Die Service Bus-Warteschlangenlänge ist eine spezielle Metrik, und der Schwellenwert entspricht der Anzahl von Nachrichten pro Instanz. Ein Beispiel: Bei zwei Instanzen und einem Schwellenwert von 100 kommt es zu einer Skalierung, wenn die Gesamtanzahl von Nachrichten in der Warteschlange 200 beträgt. Hierbei kann es sich dann um 100 Nachrichten pro Instanz, um 120 und 80 Nachrichten oder um eine beliebige andere Kombination handeln, die zusammengenommen mindestens 200 ergibt.

Für VM-Skalierungsgruppen können Sie die Einstellung für die automatische Skalierung in der Resource Manager-Vorlage aktualisieren, sodass *metricName* als *ApproximateMessageCount* verwendet und die ID der Speicherwarteschlange als *metricResourceUri* übergeben wird.

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Für Service Bus gibt es das Konzept einer Ressourcengruppe nicht, Azure Resource Manager erstellt jedoch eine Standardressourcengruppe pro Region. Die Ressourcengruppe hat in der Regel das Format „Default-ServiceBus-[Region]“. Beispiel: „Default-ServiceBus-EastUS“, „Default-ServiceBus-WestUS“, „Default-ServiceBus-AustraliaEast“ usw.
>
>

