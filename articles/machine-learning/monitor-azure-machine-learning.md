---
title: Überwachen von Azure Machine Learning | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Monitor verwenden können, um Metriken aus Azure Machine Learning anzuzeigen und zu erstellen sowie Warnungen zu solchen Metriken zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/01/2020
ms.openlocfilehash: a77f9c8f7e37d2c5a040a48b6bd96bef11d51f14
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533479"
---
# <a name="monitor-azure-machine-learning"></a>Überwachen von Azure Machine Learning

Wenn Sie über unternehmenskritische Anwendungen und Geschäftsprozesse verfügen, die auf Azure-Ressourcen beruhen, sollten Sie Verfügbarkeit, Leistung und Betrieb dieser Ressourcen überwachen. In diesem Artikel wird das Überwachen von Daten beschrieben, die von Azure Machine Learning generiert werden. Außerdem erfahren Sie, wie Sie diese Daten mit Azure Monitor analysieren und Warnungen für diese erstellen.

> [!TIP]
> Die Informationen in diesem Dokument richten sich in erster Linie an __Administratoren__, da hier die Überwachung für Azure Machine Learning Service und zugehörige Azure-Dienste beschrieben wird. Wenn Sie __Datenanalyst__ oder __Entwickler__ sind und spezifische Informationen zu Ihren *Modelltrainingsausführungen* überwachen möchten, sehen Sie sich die folgenden Artikel an:
>
> * [Starten, Überwachen und Abbrechen von Trainingsausführungen in Python](how-to-manage-runs.md)
> * [Protokollieren von Metriken für Trainingsausführungen](how-to-track-experiments.md)
> * [Nachverfolgen von Experimenten mit MLflow](how-to-use-mlflow.md)
> * [Visualisieren von Experimentausführungen und -metriken mit TensorBoard und Azure Machine Learning](how-to-monitor-tensorboard.md)
>
> Wenn Sie Informationen überwachen möchten, die von Modellen generiert werden, die als Webdienste oder IoT Edge-Module bereitgestellt werden, finden Sie weitere Informationen unter [Sammeln von Modelldaten](how-to-enable-data-collection.md) und [Überwachen mit Application Insights](how-to-enable-app-insights.md).

## <a name="what-is-azure-monitor"></a>Was ist Azure Monitor?

Azure Machine Learning erstellt Überwachungsdaten mit [Azure Monitor](../azure-monitor/overview.md), wobei es sich um einen Azure-Dienst zur vollständigen Stapelüberwachung handelt. Azure Monitor bietet einen vollständigen Satz von Funktionen zum Überwachen Ihrer Azure-Ressourcen. Mit Azure Monitor können außerdem Ressourcen in anderen Clouds und lokal überwacht werden.

Beginnen Sie mit dem Artikel [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md), in dem die folgenden Konzepte beschrieben werden:

- Was ist Azure Monitor?
- Kosten für die Überwachung
- In Azure gesammelte Überwachungsdaten
- Konfigurieren der Datensammlung
- Standardtools in Azure zum Analysieren von Überwachungsdaten sowie zum Generieren von Warnungen

Die folgenden Abschnitte bauen auf diesem Artikel auf, indem die spezifischen Daten beschrieben werden, die für Azure Machine Learning erfasst werden. In diesen Abschnitten finden Sie außerdem Beispiele für die Konfiguration der Datensammlung und die Analyse der Daten mit Azure-Tools.

> [!TIP]
> Informationen zu den mit Azure Monitor verbundenen Kosten finden Sie unter [Überwachen der Nutzung und geschätzten Kosten in Azure Monitor](../azure-monitor/platform/usage-estimated-costs.md). Informationen hinsichtlich der Zeit, die benötigt wird, bis Ihre Daten in Azure Monitor angezeigt werden, finden Sie unter [Protokolldatenerfassungszeit in Azure Monitor](../azure-monitor/platform/data-ingestion-time.md).

## <a name="monitoring-data-from-azure-machine-learning"></a>Überwachen von Daten aus Azure Machine Learning

Azure Machine Learning erfasst dieselben Arten von Überwachungsdaten wie andere Azure-Ressourcen, die unter [Überwachen von Daten aus Azure-Ressourcen](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data) beschrieben werden. 

Eine ausführliche Referenz zu den Protokollen und Metriken, die von Azure Machine Learning erstellt werden, finden Sie unter [Überwachen von Azure Machine Learning-Daten – Referenz](monitor-resource-reference.md).

<a id="configuration"></a>

## <a name="collection-and-routing"></a>Sammlung und Routing

Plattformmetriken und das Aktivitätsprotokoll werden automatisch erfasst und gespeichert, können jedoch mithilfe einer Diagnoseeinstellung an andere Speicherorte weitergeleitet werden.  

Ressourcenprotokolle werden erst erfasst und gespeichert, sobald Sie eine Diagnoseeinstellung erstellt und an einen oder mehrere Standorte weitergeleitet haben.

Ausführliche Informationen zum Erstellen einer Diagnoseeinstellung über das Azure-Portal, die Befehlszeilenschnittstelle oder PowerShell finden Sie unter [Erstellen einer Diagnoseeinstellung zum Sammeln von Plattformprotokollen und Metriken in Azure](../azure-monitor/platform/diagnostic-settings.md). Wenn Sie eine Diagnoseeinstellung erstellen, legen Sie fest, welche Kategorien von Protokollen gesammelt werden sollen. Eine Liste der Kategorien für Azure Machine Learning finden Sie in der [Referenz zu Azure Machine Learning-Überwachungsdaten](monitor-resource-reference.md#resource-logs).

> [!IMPORTANT]
> Ein Aktivieren dieser Einstellungen erfordert zusätzliche Azure-Dienste (Speicherkonto, Event Hub oder Log Analytics). Dadurch können sich Ihre Kosten erhöhen. Um geschätzte Kosten zu berechnen, wechseln Sie zum [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator).

Sie können die folgenden Protokolle für Azure Machine Learning konfigurieren:

| Kategorie | Beschreibung |
|:---|:---|
| AmlComputeClusterEvent | Ereignisse von Azure Machine Learning-Computeclustern |
| AmlComputeClusterNodeEvent | Ereignisse von Knoten in einem Azure Machine Learning-Computecluster |
| AmlComputeJobEvent | Ereignisse von Knoten, die in Azure Machine Learning-Compute ausgeführt werden |

> [!NOTE]
> Wenn Sie Metriken in einer Diagnoseeinstellung aktivieren, sind Dimensionsinformationen derzeit nicht in den Informationen enthalten, die an ein Speicherkonto, an einen Event Hub oder an Log Analytics gesendet werden.

In den folgenden Abschnitten werden die Metriken und Protokolle behandelt, die Sie erfassen können.

## <a name="analyzing-metrics"></a>Analysieren von Metriken

Sie können Metriken für Azure Machine Learning mit Metriken von anderen Azure-Diensten analysieren, indem Sie **Metriken** über das Menü **Azure Monitor** öffnen. Ausführliche Informationen zur Verwendung dieses Tools finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](../azure-monitor/platform/metrics-getting-started.md).

Eine Liste der erfassten Plattformmetriken finden Sie in der [Referenz zur Überwachung von Azure Machine Learning-Datenmetriken](monitor-resource-reference.md#metrics).

Alle Metriken für Azure Machine Learning befinden sich im Namespace **Machine Learning Service-Arbeitsbereich**.

![Metrik-Explorer mit ausgewähltem Machine Learning Service-Arbeitsbereich](./media/monitor-azure-machine-learning/metrics.png)

Sie können zur Referenz auf eine Liste [aller in Azure Monitor unterstützter Ressourcenmetriken](../azure-monitor/platform/metrics-supported.md) anzeigen.

### <a name="filtering-and-splitting"></a>Filtern und Teilen

Für Metriken, die Dimensionen unterstützen, können Sie Filter mit einem Dimensionswert anwenden. Beispielsweise können Sie **Active Cores** (Aktive Kerne) nach dem **Clusternamen**`cpu-cluster` filtern. 

Sie können eine Metrik auch nach Dimension teilen, um visuell darzustellen, wie verschiedene Segmente der Metrik miteinander zu vergleichen sind. Beispielsweise können Sie den **Pipeline Step Type** (Pipelineschritttyp) teilen, um die Anzahl der in der Pipeline verwendeten Typen von Schritten anzuzeigen.

Weitere Informationen zum Filtern und Teilen finden Sie unter [Erweiterte Funktionen von Azure Metrik-Explorer](../azure-monitor/platform/metrics-charts.md).

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>Analysieren von Protokollen

Um Azure Monitor Log Analytics verwenden zu können, müssen Sie eine Diagnosekonfiguration erstellen und __Send information to Log Analytics__ (Informationen an Log Analytics senden) aktivieren. Weitere Informationen finden Sie im Abschnitt [Erfassung und Weiterleitung](#collection-and-routing).

Daten in Azure Monitor-Protokollen werden in Tabellen gespeichert, wobei jede Tabelle ihren eigenen Satz eindeutiger Eigenschaften hat. In Azure Machine Learning werden Daten in den folgenden Tabellen gespeichert:

| Tabelle | Beschreibung |
|:---|:---|
| AmlComputeClusterEvent | Ereignisse von Azure Machine Learning-Computeclustern |
| AmlComputeClusterNodeEvent | Ereignisse von Knoten in einem Azure Machine Learning-Computecluster |
| AmlComputeJobEvent | Ereignisse von Knoten, die in Azure Machine Learning-Compute ausgeführt werden |

> [!IMPORTANT]
> Wenn Sie **Protokolle** im Menü von Azure Machine Learning auswählen, wird Log Analytics geöffnet, wobei der Abfragebereich auf den aktuellen Arbeitsbereich festgelegt ist. Dies bedeutet, dass Protokollabfragen nur Daten aus dieser Ressource umfassen. Wenn Sie eine Abfrage ausführen möchten, die Daten aus anderen Datenbanken oder Daten aus anderen Azure-Diensten enthält, wählen Sie im Menü **Azure Monitor** die Option **Protokolle** aus. Ausführliche Informationen finden Sie unter [Protokollabfragebereich und Zeitbereich in Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md).

Eine ausführliche Referenz zu den Protokollen und Metriken finden Sie unter [Überwachen von Azure Machine Learning-Daten – Referenz](monitor-resource-reference.md).

### <a name="sample-kusto-queries"></a>Kusto-Beispielabfragen

> [!IMPORTANT]
> Wenn Sie **Protokolle** im [service-name]-Menü auswählen, wird Log Analytics geöffnet, wobei der Abfragebereich auf den aktuellen Azure Machine Learning-Arbeitsbereich festgelegt ist. Dies bedeutet, dass Protokollabfragen nur Daten aus dieser Ressource umfassen. Wenn Sie eine Abfrage ausführen möchten, die Daten aus anderen Arbeitsbereichen oder anderen Azure-Diensten enthält, klicken Sie im Menü **Azure Monitor** auf **Protokolle**. Ausführliche Informationen finden Sie unter [Protokollabfragebereich und Zeitbereich in Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md).

Die folgenden Abfragen sind Abfragen, mit denen Sie Ihre Azure Machine Learning-Ressourcen überwachen können: 

+ Abrufen der Aufträge, die in den letzten fünf Tagen fehlerhaft waren:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Abrufen der Datensätze für einen bestimmten Auftragsnamen:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Abrufen von Clusterereignissen, die in den letzten fünf Tagen für Cluster aufgetreten sind, in denen die VM-Größe gleich „Standard_D1_V2“ ist:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Abrufen der Knoten, die in den letzten acht Tagen zugeordnet wurden:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="alerts"></a>Warnungen

Sie können auf Warnungen für Azure Machine Learning zugreifen, indem Sie **Warnungen** über das **Azure Monitor**-Menü öffnen. Ausführliche Informationen zum Erstellen von Warnungen finden Sie unter [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](../azure-monitor/platform/alerts-metric.md).

In der folgenden Tabelle sind allgemeine und empfohlene Metrikwarnungsregeln für Azure Machine Learning aufgeführt:

| Warnungstyp | Bedingung | Beschreibung |
|:---|:---|:---|
| Model Deploy Failed (Fehler bei der Modellimplementierung) | Aggregationstyp: Total (Gesamt), Operator: Größer als, Schwellenwert: 0 | Mindestens eine Modellimplementierung ist fehlgeschlagen. |
| Quota Utilization Percentage (Prozentsatz der Kontingentnutzung) | Aggregationstyp: Average (Mittelwert), Operator: Größer als, Schwellenwert: 90| Trifft zu, wenn die Kontingentnutzung größer als 90 % ist. |
| Unusable Nodes (Nicht verwendbare Knoten) | Aggregationstyp: Total (Gesamt), Operator: Größer als, Schwellenwert: 0 | Es gibt mindestens einen nicht verwendbaren Knoten. |

## <a name="next-steps"></a>Nächste Schritte

- Eine Referenz zu den Protokollen und Metriken finden Sie in der [Referenz zur Überwachung von Azure Machine Learning-Daten](monitor-resource-reference.md).
- Informationen zum Arbeiten mit Kontingenten im Zusammenhang mit Azure Machine Learning finden Sie unter [Verwalten und Anfordern von Kontingenten für Azure-Ressourcen](how-to-manage-quotas.md).
- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md).
