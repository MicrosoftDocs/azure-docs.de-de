---
title: Referenz zum Überwachen von Azure Machine Learning-Daten | Microsoft-Dokumentation
description: Referenzdokumentation zur Überwachung von Azure Machine Learning Erfahren Sie mehr über die in Azure Monitor gesammelten und verfügbaren Daten und Ressourcen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/02/2020
ms.openlocfilehash: 6da9c6775a6d5c96bc52183fefc3e5330272e9ff
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585125"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Überwachen von Azure Machine Learning-Daten – Referenz

Erfahren Sie mehr über die Daten und Ressourcen, die von Azure Monitor aus Ihrem Azure Machine Learning-Arbeitsbereich gesammelt werden. Ausführliche Informationen über das Sammeln und Analysieren von Überwachungsdaten finden Sie unter [Überwachen von Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="metrics"></a>Metriken

In diesem Abschnitt werden alle automatisch erfassten Plattformmetriken aufgeführt, die für Azure Machine Learning gesammelt werden. Der Ressourcenanbieter für diese Metriken ist [Microsoft.MachineLearningServices/workspaces](../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces).

**Modell**

| Metrik | Einheit | BESCHREIBUNG |
| ----- | ----- | ----- |
| Model deploy failed (Fehler bei der Modellimplementierung) | Anzahl | Die Anzahl der fehlerhaften Modellimplementierungen |
| Model deploy started (Gestartete Modellimplementierungen) | Anzahl | Die Anzahl der gestarteten Modellimplementierungen |
| Model deploy succeeded (Erfolgreiche Modellimplementierungen) | Anzahl | Die Anzahl der erfolgreichen Modellimplementierungen |
| Model register failed (Fehler bei der Modellregistrierung) | Anzahl | Die Anzahl der fehlerhaften Modellregistrierungen |
| Model register succeeded (Erfolgreiche Modellregistrierungen) | Anzahl | Die Anzahl der erfolgreichen Modellregistrierungen |

**Kontingent**

Kontingentinformationen gelten nur für Azure Machine Learning Compute.

| Metrik | Einheit | BESCHREIBUNG |
| ----- | ----- | ----- |
| Active cores (Aktive Kerne) | Anzahl | Die Anzahl der aktiven Compute-Kerne |
| Active nodes (Aktive Knoten) | Anzahl | Die Anzahl der aktiven Knoten |
| Idle cores (Kerne im Leerlauf) | Anzahl | Die Anzahl der Compute-Kerne im Leerlauf |
| Idle nodes (Knoten im Leerlauf) | Anzahl | Die Anzahl der Serverknoten (Compute-Knoten) im Leerlauf |
| Leaving cores (Verbleibende Kerne) | Anzahl | Die Anzahl der verbleibenden Kerne |
| Leaving nodes (Verbleibende Knoten) | Anzahl | Die Anzahl der verbleibenden Knoten |
| Preempted cores (Kerne mit präemptivem Multitasking) | Anzahl | Die Anzahl der Kerne mit präemptivem Multitasking |
| Vorzeitig entfernte Knoten | Anzahl | Die Anzahl der Knoten mit präemptivem Multitasking |
| Quota utilization percentage (Prozentsatz der Kontingentnutzung) | Percent | Der Prozentsatz, mit dem das Kontingent genutzt wird |
| Total cores (Kerne gesamt) | Anzahl | Die Gesamtzahl der Kerne |
| Total nodes (Knoten gesamt) | Anzahl | Die Gesamtzahl der Knoten |
| Unusable cores (Nicht verwendbare Kerne) | Anzahl | Die Anzahl der nicht verwendbaren Kerne |
| Unusable nodes (Nicht verwendbare Knoten) | Anzahl | Die Anzahl der nicht verwendbaren Knoten |

**Ressource**

| Metrik | Einheit | BESCHREIBUNG |
| ----- | ----- | ----- |
| CpuUtilization | Percent | Wie viel Prozent der CPU-Auslastung für einen bestimmten Knoten während eines Laufs/eines Auftrags verwendet wurden. Diese Metrik wird nur veröffentlicht, wenn ein Auftrag auf einem Knoten ausgeführt wird. Ein Auftrag kann einen oder mehrere Knoten verwenden. Diese Metrik wird pro Knoten veröffentlicht. |
| GpuUtilization | Percent | Gibt an, welcher Prozentsatz der GPU-Auslastung für einen bestimmten Knoten während eines Laufs/eines Auftrags verwendet wurde. Ein Knoten kann eine oder mehrere GPUs aufweisen. Diese Metrik wird pro GPU pro Knoten veröffentlicht. |

**Ausführen**

Informationen zu Trainingsausführungen

| Metrik | Einheit | BESCHREIBUNG |
| ----- | ----- | ----- |
| Completed runs (Abgeschlossene Ausführungen) | Anzahl | Die Anzahl der abgeschlossenen Ausführungen |
| Failed runs (Ausführungen mit Fehlern) | Anzahl | Die Anzahl der Ausführungen mit Fehlern |
| Started runs (Gestartete Ausführungen) | Anzahl | Die Anzahl der gestarteten Ausführungen |

## <a name="metric-dimensions"></a>Metrikdimensionen

Weitere Informationen zu Metrikdimensionen finden Sie unter [Mehrdimensionale Metriken](../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics).

Den Metriken in Azure Machine Learning sind die folgenden Dimensionen zugeordnet.

| Dimension | Beschreibung |
| ---- | ---- |
| Clustername | Der Name der Computeclusterressource. Diese Dimension ist für alle Kontingentmetriken verfügbar. |
| Vm Family Name (Name der VM-Familie) | Der Name der vom Cluster verwendeten VM-Familie Diese Dimension ist für die Metrik „Quota Utilization Percentage“ (Prozentsatz der Kontingentnutzung) verfügbar. |
| Vm Priority (VM-Priorität) | Die Priorität des virtuellen Computers (VM) Diese Dimension ist für die Metrik „Quota Utilization Percentage“ (Prozentsatz der Kontingentnutzung) verfügbar.
| CreatedTime | Diese Dimension ist nur für CpuUtilization und GpuUtilization verfügbar. |
| deviceId | ID des Geräts (GPU). Nur für GpuUtilization verfügbar. |
| NodeId | ID des erstellten Knotens, auf dem der Auftrag ausgeführt wird. Diese Dimension ist nur für CpuUtilization und GpuUtilization verfügbar. |
| RunId | ID des Laufs/Auftrags. Diese Dimension ist nur für CpuUtilization und GpuUtilization verfügbar. |
| ComputeType | Der für die Ausführung verwendete Computetyp Diese Dimension ist nur für die Metriken „Abgeschlossene Ausführungen“, „Failed Runs“ (Ausführungen mit Fehler) und „Started Runs“ (Begonnene Ausführungen) verfügbar. |
| PipelineStepType | Der Typ des [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?preserve-view=true&view=azure-ml-py)-Objekts, das in der Ausführung verwendet wurde Diese Dimension ist nur für die Metriken „Abgeschlossene Ausführungen“, „Failed Runs“ (Ausführungen mit Fehler) und „Started Runs“ (Begonnene Ausführungen) verfügbar. |
| PublishedPipelineId | Die ID der veröffentlichten Pipeline, die in der Ausführung verwendet wurde Diese Dimension ist nur für die Metriken „Abgeschlossene Ausführungen“, „Failed Runs“ (Ausführungen mit Fehler) und „Started Runs“ (Begonnene Ausführungen) verfügbar. |
| RunType | Der Typ der Ausführung. Diese Dimension ist nur für die Metriken „Abgeschlossene Ausführungen“, „Failed Runs“ (Ausführungen mit Fehler) und „Started Runs“ (Begonnene Ausführungen) verfügbar. |

Gültige Werte für die RunType-Dimension sind:

| Wert | Beschreibung |
| ----- | ----- |
| Experiment | Nicht-Pipelineausführungen |
| PipelineRun | Eine Pipelineausführung, die das übergeordnete Element einer Schrittausführung (StepRun) ist |
| StepRun | Eine Ausführung für einen Pipelineschritt |
| ReusedStepRun | Eine Ausführung für einen Pipelineschritt, in dem eine vorherige Ausführung erneut verwendet wird |

## <a name="activity-log"></a>Aktivitätsprotokoll

In der folgenden Tabelle finden Sie die Vorgänge in Zusammenhang mit Azure Machine Learning, die im Aktivitätsprotokoll erstellt werden können.

| Vorgang | BESCHREIBUNG |
|:---|:---|
| Creates or updates a Machine Learning workspace (Machine Learning-Arbeitsbereich erstellen oder aktualisieren) | Ein Arbeitsbereich wurde erstellt oder aktualisiert. |
| CheckComputeNameAvailability | Hiermit wird überprüft, ob ein Computename bereits verwendet wird. |
| Creates or updates the compute resources (Computeressourcen erstellen oder aktualisieren) | Eine Computeressource wurde erstellt oder aktualisiert. |
| Deletes the compute resources (Computeressourcen löschen) | Eine Computeressource wurde gelöscht. |
| Auflisten geheimer Schlüssel | Hiermit werden Geheimnisse für einen Vorgang für einen Machine Learning-Arbeitsbereich aufgelistet. |

## <a name="resource-logs"></a>Ressourcenprotokolle

In diesem Abschnitt werden die Typen der Ressourcenprotokolle aufgeführt, die für einen Azure Machine Learning-Arbeitsbereich erfasst werden können.

Ressourcenanbieter und Typ: [Microsoft.MachineLearningServices/workspace](../azure-monitor/platform/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces).

| Kategorie | Anzeigename |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |

## <a name="schemas"></a>Schemas

Die folgenden Schemas werden von Azure Machine Learning verwendet.

### <a name="amlcomputejobevents-table"></a>Tabelle „AmlComputeJobEvents“

| Eigenschaft | BESCHREIBUNG |
|:--- |:---|
| TimeGenerated | Zeitpunkt, zu dem der Protokolleintrag generiert wurde |
| Vorgangsname | Name des Vorgangs, der dem Protokollereignis zugeordnet ist |
| Kategorie | Name des Protokollereignisses, AmlComputeClusterNodeEvent |
| JobId | ID des übermittelten Auftrags |
| ExperimentId | ID des Experiments |
| ExperimentName | Name des Experiments |
| CustomerSubscriptionId | Abonnement-ID, unter der das Experiment und der Auftrag übermittelt wurden |
| WorkspaceName | Name des Machine Learning-Arbeitsbereichs |
| ClusterName | Name des Clusters |
| ProvisioningState | Status der Auftragsübermittlung |
| ResourceGroupName | Name der Ressourcengruppe |
| JobName | Name des Auftrags |
| ClusterId | ID des Clusters |
| EventType | Hierbei handelt es sich um den Typ des Auftragsereignisses. Beispiele: JobSubmitted, JobRunning, JobFailed oder JobSucceeded. |
| ExecutionState | Hierbei handelt es sich um den Status des Auftrags (der Ausführung). Beispiele: Queued, Running, Succeeded oder Failed. |
| ErrorDetails | Details zum Auftragsfehler |
| CreationApiVersion | API-Version, mit der der Auftrag erstellt wurde |
| ClusterResourceGroupName | Ressourcengruppenname des Clusters |
| TFWorkerCount | Anzahl der TF-Worker |
| TFParameterServerCount | Anzahl der TF-Parameterserver |
| ToolType | Typ des verwendeten Tools |
| RunInContainer | Flag, das beschreibt, ob der Auftrag in einem Container ausgeführt werden soll |
| JobErrorMessage | Ausführliche Meldung für den Auftragsfehler |
| NodeId | ID des erstellten Knotens, auf dem der Auftrag ausgeführt wird |

### <a name="amlcomputeclusterevents-table"></a>Tabelle „AmlComputeClusterEvents“

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| TimeGenerated | Zeitpunkt, zu dem der Protokolleintrag generiert wurde |
| Vorgangsname | Name des Vorgangs, der dem Protokollereignis zugeordnet ist |
| Kategorie | Name des Protokollereignisses, AmlComputeClusterNodeEvent |
| ProvisioningState | Bereitstellungsstatus des Clusters |
| ClusterName | Name des Clusters |
| ClusterType | Typ des Clusters |
| CreatedBy | Benutzer, der den Cluster erstellt hat |
| CoreCount | Anzahl der Kerne im Cluster |
| VmSize | VM-Größe des Clusters |
| VmPriority | Priorität der Knoten, die in einem Cluster erstellt wurden (Dedicated/LowPriority) |
| ScalingType | Typ der Clusterskalierung (manual/auto) |
| InitialNodeCount | Anfängliche Knotenanzahl des Clusters |
| MinimumNodeCount | Kleinste Knotenanzahl des Clusters |
| MaximumNodeCount | Größte Knotenanzahl des Clusters |
| NodeDeallocationOption | Art und Weise, wie die Zuordnung des Knotens aufgehoben werden soll |
| Herausgeber | Herausgeber des Clustertyps |
| Angebot | Angebot, mit dem der Cluster erstellt wird |
| Sku | SKU des im Cluster erstellten Knotens/virtuellen Computers |
| Version | Version des Images, das beim Erstellen des Knotens/virtuellen Computers verwendet wird |
| SubnetId | Subnetz-ID des Clusters |
| AllocationState | Zuweisungsstatus des Clusters |
| CurrentNodeCount | Aktuelle Knotenanzahl des Clusters |
| TargetNodeCount | Zielknotenanzahl des Clusters beim zentralen Hoch-/Herunterskalieren |
| EventType | Typ des Ereignisses während der Clustererstellung |
| NodeIdleTimeSecondsBeforeScaleDown | Leerlaufzeit in Sekunden vor horizontalem Herunterskalieren des Clusters |
| PreemptedNodeCount | Anzahl der vorzeitig entfernten Knoten des Clusters |
| IsResizeGrow | Flag, das angibt, dass der Cluster zentral hochskaliert wird |
| VmFamilyName | Name der VM-Familie der Knoten, die im Cluster erstellt werden können |
| LeavingNodeCount | Anzahl der verbliebenen Knoten des Clusters |
| UnusableNodeCount | Anzahl der nicht verwendbaren Knoten des Clusters |
| IdleNodeCount | Anzahl der nicht verwendbaren Knoten des Clusters |
| RunningNodeCount | Anzahl der ausführenden Knoten des Clusters |
| PreparingNodeCount | Anzahl der vorzubereitenden Knoten des Clusters |
| QuotaAllocated | Zugeordnetes Kontingent für den Cluster |
| QuotaUtilized | Genutztes Kontingent für den Cluster |
| AllocationStateTransitionTime | Übergangszeit von einem Zustand in einen anderen |
| ClusterErrorCodes | Fehlercode, der während der Clustererstellung oder Skalierung empfangen wurde |
| CreationApiVersion | API-Version, die beim Erstellen des Clusters verwendet wurde |

### <a name="amlcomputeclusternodeevents-table"></a>Tabelle „AmlComputeClusterNodeEvents“

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| TimeGenerated | Zeitpunkt, zu dem der Protokolleintrag generiert wurde |
| Vorgangsname | Name des Vorgangs, der dem Protokollereignis zugeordnet ist |
| Kategorie | Name des Protokollereignisses, AmlComputeClusterNodeEvent |
| ClusterName | Name des Clusters |
| NodeId | ID des erstellten Clusterknotens |
| VmSize | Größe des virtuellen Computers des Knotens |
| VmFamilyName | VM-Familie, zu der der Knoten gehört |
| VmPriority | Priorität des erstellten Knotens (Dedicated/LowPriority) |
| Herausgeber | Hierbei handelt es sich um den Herausgeber des VM-Image. Beispiel: microsoft-dsvm. |
| Angebot | Angebot, das mit der VM-Erstellung verknüpft ist |
| Sku | SKU des erstellten Knotens/virtuellen Computers |
| Version | Version des Images, das beim Erstellen des Knotens/virtuellen Computers verwendet wird |
| ClusterCreationTime | Zeitpunkt, zu dem der Cluster erstellt wurde |
| ResizeStartTime | Zeitpunkt, zu dem das zentrale Hoch-/Herunterskalieren des Clusters gestartet wurde |
| ResizeEndTime | Zeitpunkt, zu dem das zentrale Hoch-/Herunterskalieren des Clusters beendet wurde |
| NodeAllocationTime | Zeitpunkt, zu dem der Knoten zugeordnet wurde |
| NodeBootTime | Zeitpunkt, zu dem der Knoten hochgefahren wurde |
| StartTaskStartTime | Zeitpunkt, zu dem die Aufgabe einem Knoten zugewiesen und gestartet wurde |
| StartTaskEndTime | Zeitpunkt, zu dem die einem Knoten zugewiesene Aufgabe beendet wurde |
| TotalE2ETimeInSeconds | Gesamtzeit, die der Knoten aktiv war |


## <a name="see-also"></a>Weitere Informationen

- Eine Beschreibung der Überwachung von Azure Machine Learning finden Sie unter [Überwachen von Azure Machine Learning](monitor-azure-machine-learning.md).
- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md).
