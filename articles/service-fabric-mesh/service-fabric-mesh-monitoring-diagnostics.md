---
title: Überwachung und Diagnose in Azure Service Fabric Mesh-Apps
description: Weitere Informationen zur Überwachung und Diagnose von Service Fabric Mesh-Anwendungen in Azure
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: eda0b62729343b0a138d027548d8750b1e0fc74f
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844402"
---
# <a name="monitoring-and-diagnostics"></a>Überwachung und Diagnose
Azure Service Fabric Mesh ist ein vollständig verwalteter Dienst, der es Entwicklern ermöglicht, Microservicesanwendungen zu implementieren, ohne virtuelle Computer, Speicher oder Netzwerke verwalten zu müssen. Die Überwachung und Diagnose für Service Fabric Mesh besteht aus drei Hauptkategorien von Diagnosedaten:

- Anwendungsprotokolle: Diese sind definiert als die Protokolle aus Ihren Containeranwendungen, basierend darauf, wie Sie Ihre Anwendung instrumentiert haben (z. B. Docker-Protokolle).
- Plattformereignisse: Die für Containervorgänge relevanten Ereignisse der Mesh-Plattform umfassen derzeit Containeraktivierung, -deaktivierung und -beendigung.
- Containermetriken: Diese umfassen die Ressourcenverwendung und Leistungsmetriken für Ihre Container (Docker-Statistiken).

Dieser Artikel beschreibt die Überwachungs- und Diagnoseoptionen der aktuellen Vorschauversion.

## <a name="application-logs"></a>Anwendungsprotokolle

Sie können Ihre Docker-Protokolle von Ihren bereitgestellten Containern aus pro Container einsehen. Im Service Fabric Mesh-Anwendungsmodell ist jeder Container ein Codepaket in Ihrer Anwendung. Verwenden Sie den folgenden Befehl, um die zugehörigen Protokolle mit einem Codepaket anzuzeigen:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Mit dem Befehl „az mesh service-replica“ können Sie den Namen des Replikats abrufen. Replikatnamen sind inkrementelle Integerwerte ab 0.

So sehen die Protokolle aus dem VotingWeb.Code-Container aus der Abstimmungsanwendung aus:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Containermetriken 

Die Mesh-Umgebung stellt eine Reihe von Metriken bereit, die angeben, wie sich Ihre Container verhalten. Die folgenden Metriken sind über das Azure-Portal und die Azure Monitor CLI verfügbar:

| Metrik | BESCHREIBUNG | Units|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu als Prozentsatz | % |
| MemoryUtilization | ActualMem/AllocatedMem als Prozentsatz | % |
| AllocatedCpu | Zugeordnete CPU gemäß der Azure Resource Manager-Vorlage | Millicores |
| AllocatedMemory | Zugeordneter Speicher gemäß der Azure Resource Manager-Vorlage | MB |
| ActualCpu | CPU-Auslastung | Millicores |
| ActualMemory | Speicherauslastung | MB |
| ContainerStatus | 0 – Ungültig: Der Containerstatus ist unbekannt. <br> 1 – Ausstehend: Der Start des Containers wurde geplant. <br> 2 – Wird gestartet: Der Container wird aktuell gestartet. <br> 3 – Gestartet: Der Container wurde erfolgreich gestartet. <br> 4 – Wird beendet: Der Container wird aktuell beendet. <br> 5 – Beendet: Der Container wurde erfolgreich beendet. | – |
| ApplicationStatus | 0 – Unbekannt: Der Status ist nicht abrufbar. <br> 1 – Bereit: Die Anwendung wird erfolgreich ausgeführt. <br> 2 – Wird aktualisiert: Aktuell wird ein Upgrade ausgeführt. <br> 3 – Wird erstellt: Die Anwendung wird aktuell erstellt. <br> 4 – Wird gelöscht: Die Anwendung wird aktuell gelöscht. <br> 5 – Fehler: Die Anwendung konnte nicht bereitgestellt werden. | – |
| ServiceStatus | 0 – Ungültig: Der Dienst weist zurzeit keinen Integritätsstatus auf. <br> 1 – OK: Der Dienst ist integer.  <br> 2 – Warnung: Es gibt möglicherweise ein Problem, das eine Untersuchung erfordert. <br> 3 – Fehler: Es gibt möglicherweise ein Problem, das eine Untersuchung erfordert. <br> 4 – Unbekannt: Der Status ist nicht abrufbar. | – |
| ServiceReplicaStatus | 0 – Ungültig: Das Replikat weist zurzeit keinen Integritätsstatus auf. <br> 1 – OK: Der Dienst ist integer.  <br> 2 – Warnung: Es gibt möglicherweise ein Problem, das eine Untersuchung erfordert. <br> 3 – Fehler: Es gibt möglicherweise ein Problem, das eine Untersuchung erfordert. <br> 4 – Unbekannt: Der Status ist nicht abrufbar. | – | 
| RestartCount | Die Anzahl der Containerneustarts. | – |

> [!NOTE]
> Die Werte für ServiceStatus und ServiceReplicaStatus sind identisch mit [HealthState](/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) in Service Fabric. 

Jede Metrik ist für verschiedene Dimensionen verfügbar, sodass Sie Aggregate auf verschiedenen Ebenen anzeigen können. Dies ist die aktuelle Liste der Dimensionen:

* ApplicationName
* Dienstname
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> Die CodePackageName-Dimension ist für Linux-Anwendungen nicht verfügbar. 

Jede Dimension entspricht verschiedenen Komponenten des [Service Fabric-Anwendungsmodells](service-fabric-mesh-service-fabric-resources.md#applications-and-services).

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

Eine vollständige Liste der Befehle ist in der [Azure Monitor CLI-Dokumentation](/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) verfügbar, aber wir haben unten einige hilfreiche Beispiele aufgeführt. 

In jedem Beispiel folgt die Ressourcen-ID diesem Muster.

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* CPU-Auslastung der Container in einer Anwendung

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Arbeitsspeicherverwendung für jedes Dienstreplikat
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Neustarts für jeden Container in einem Fenster von einer Stunde 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Durchschnittliche CPU-Auslastung über Dienste hinweg mit dem Namen „VotingWeb“ in einem Fenster von einer 1 Stunde
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Metrik-Explorer

Der Metrik-Explorer ist ein Blatt im Portal, auf dem Sie alle Metriken für Ihre Mesh-Anwendung visualisieren können. Auf dieses Blatt kann auf der Seite der Anwendung im Portal oder über das Blatt „Azure Monitor“ zugegriffen werden, mit dem Sie Metriken für alle Ihre Azure-Ressourcen anzeigen können, die Azure Monitor unterstützen. 

![Metrik-Explorer](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Nächste Schritte
* In der [Übersicht über Azure Service Fabric Mesh](service-fabric-mesh-overview.md) erfahren Sie mehr über Service Fabric Mesh.
* Weitere Informationen zu den Metrikbefehlen von Azure Monitor finden Sie in der [Dokumentation zur Azure Monitor-CLI](/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).
