---
title: Datenflussaktivität
description: 'Gewusst wie: Ausführen von Datenflüssen aus einer Data Factory-Pipeline heraus.'
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.author: makromer
ms.date: 01/03/2021
ms.openlocfilehash: 0663690318773ccad3bddfaaa03e456c2f58895e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383380"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Datenflussaktivität in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Verwenden Sie die Datenflussaktivität, um Daten mithilfe von Mapping Data Flow zu transformieren und zu verschieben. Wenn Sie mit Datenflüssen noch nicht vertraut sind, finden Sie weitere Informationen in der [Übersicht über Mapping Data Flow](concepts-data-flow-overview.md).

## <a name="syntax"></a>Syntax

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "traceLevel": "Fine",
      "runConcurrently": true,
      "continueOnError": true,      
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Typeigenschaften

Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich
-------- | ----------- | -------------- | --------
dataflow | Der Verweis auf den Datenfluss, der ausgeführt wird. | DataFlowReference | Ja
integrationRuntime | Die Computeumgebung, in der der Datenfluss ausgeführt wird. Ohne Angabe wird die Azure Integration Runtime mit automatischer Auflösung verwendet. | IntegrationRuntimeReference | Nein
compute.coreCount | Die Anzahl von Kernen, die im Spark-Cluster verwendet werden. Kann nur angegeben werden, wenn die Azure Integration Runtime mit automatischer Auflösung verwendet wird. | 8, 16, 32, 48, 80, 144, 272 | Nein
compute.computeType | Der Computetyp, der im Spark-Cluster verwendet wird. Kann nur angegeben werden, wenn die Azure Integration Runtime mit automatischer Auflösung verwendet wird. | "General", "ComputeOptimized", "MemoryOptimized" | Nein
staging.linkedService | Geben Sie das für PolyBase-Staging verwendete Speicherkonto an, wenn Sie eine Azure Synapse Analytics-Quelle oder -Senke verwenden.<br/><br/>Wenn Ihre Azure Storage-Instanz mit einem VNET-Dienstendpunkt konfiguriert ist, müssen Sie die Authentifizierung per verwalteter Identität mit für das Speicherkonto aktivierter Option „Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben“ verwenden. Informationen hierzu finden Sie unter [Auswirkungen der Verwendung von VNET-Dienstendpunkten mit Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage). Außerdem erhalten Sie Informationen zu den erforderlichen Konfigurationen für [Azure Blob](connector-azure-blob-storage.md#managed-identity) bzw. [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity).<br/> | LinkedServiceReference | Nur wenn der Datenfluss Daten in Azure Synapse Analytics liest oder schreibt
staging.folderPath | Der für das PolyBase-Staging verwendete Ordnerpfad im Blobspeicherkonto, wenn Sie eine Azure Synapse Analytics-Quelle oder -Senke verwenden. | String | Nur wenn der Datenfluss Daten in Azure Synapse Analytics liest oder schreibt
traceLevel | Festlegen des Protokolliergrads für die Ausführung Ihrer Datenflussaktivität | Fein, Grob, Keine | Nein

![Ausführen eines Datenflusses](media/data-flow/activity-data-flow.png "Ausführen eines Datenflusses")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Dynamisches Bestimmen der Größe der Computekapazität für den Datenfluss zur Laufzeit

Die Eigenschaften „Anzahl Kerne“ und „Computetyp“ können dynamisch festgelegt werden, um die Größe der eingehenden Quelldaten zur Laufzeit anzupassen. Verwenden Sie Pipelineaktivitäten wie „Nachschlagen“ oder „Metadaten abrufen“, um die Größe der Daten im Quelldatenset zu bestimmen. Verwenden Sie dann in den Eigenschaften der Datenflussaktivität „Dynamischen Inhalt hinzufügen“.

![Dynamischer Datenfluss](media/data-flow/dyna1.png "Dynamischer Datenfluss")

[Es folgt ein kurzes Videotutorial, das diese Technik erläutert.](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Datenfluss-Integration Runtime

Wählen Sie die Integration Runtime aus, die für die Ausführung Ihrer Datenflussaktivität verwendet werden soll. Standardmäßig verwendet Data Factory die Azure Integration Runtime mit automatischer Auflösung, vier Workerkernen und ohne Gültigkeitsdauer (TTL). Diese Integration Runtime weist einen allgemeinen Computetyp auf und wird in derselben Region wie Ihre Factory ausgeführt. Sie können Ihre eigene Azure Integration Runtime erstellen, die bestimmte Regionen, den Computetyp, die Kernanzahl und die Gültigkeitsdauer (TTL) für die Ausführung Ihrer Datenflussaktivität definiert.

Bei Pipelineausführungen ist der Cluster ein Auftragscluster, der einige Minuten zum Starten braucht, bevor die Ausführung gestartet werden kann. Wenn keine Gültigkeitsdauer festgelegt ist, wird diese Startzeit bei jeder Pipelineausführung benötigt. Wenn Sie eine Gültigkeitsdauer angeben, bleibt ein „warmer“ Clusterpool über die angegebene Zeit nach der letzten Ausführung aktiv. Dies führt zu kürzeren Startzeiten. Wenn Sie beispielsweise eine Gültigkeitsdauer von 60 Minuten festgelegt haben und einmal pro Stunde einen Datenfluss ausführen, bleibt der Clusterpool aktiv. Weitere Informationen finden Sie unter [Azure Integration Runtime](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!IMPORTANT]
> Die Auswahl von Integration Runtime in der Datenflussaktivität bezieht sich nur auf *getriggerte Ausführungen* Ihrer Pipeline. Das Debuggen Ihrer Pipeline mit Datenflüssen wird auf dem in der Debugsitzung angegebenen Cluster ausgeführt.

### <a name="polybase"></a>PolyBase

Wenn Sie Azure Synapse Analytics als Senke oder Quelle verwenden, müssen Sie einen Stagingspeicherort für Ihren PolyBase-Batchladevorgang auswählen. PolyBase ermöglicht das Batchladen per Massenvorgang, anstatt die Daten zeilenweise zu laden. PolyBase verkürzt die Ladezeit in Azure Synapse Analytics erheblich.

## <a name="logging-level"></a>Protokolliergrad

Wenn Sie nicht voraussetzen, dass jede Pipelineausführung Ihrer Datenflussaktivitäten alle ausführlichen Telemetrieprotokolle vollständig protokolliert, können Sie den Protokolliergrad optional auf „Standard“ oder „Kein“ festlegen. Wenn Sie Ihre Datenflüsse im Modus „Ausführlich“ (Standard) ausführen, fordern Sie an, dass ADF die Aktivität während der Datentransformation auf den einzelnen Partitionsebenen vollständig protokolliert. Da dies ein kostspieliger Vorgang sein kann, kann nur die ausschließliche Aktivierung von „Ausführlich“ bei der Problembehandlung den gesamten Datenfluss und die Pipelineleistung verbessern. Der Modus „Standard“ protokolliert nur die Transformationszeitspannen, während „Kein“ nur eine Zusammenfassung der Zeitspannen bietet.

![Protokolliergrad](media/data-flow/logging.png "Festlegen des Protokolliergrads")

## <a name="sink-properties"></a>Senkeneigenschaften

Mit der Gruppierungsfunktion in Datenflüssen können Sie sowohl die Ausführungsreihenfolge der Senken festlegen als auch Senken unter Verwendung derselben Gruppennummer gruppieren. Um die Verwaltung von Gruppen zu erleichtern, können Sie ADF auffordern, Senken in der gleichen Gruppe parallel auszuführen. Sie können die Senkengruppe auch so festlegen, dass sie selbst dann fortgesetzt wird, wenn bei einer der Senken ein Fehler auftritt.

Beim Standardverhalten von Datenflusssenken wird jede Senke sequenziell nacheinander ausgeführt, und der Datenfluss schlägt fehl, wenn ein Fehler in der Senke auftritt. Außerdem werden alle Senken standardmäßig der gleichen Gruppe zugeordnet, es sei denn, Sie bearbeiten die Datenflusseigenschaften und legen unterschiedliche Prioritäten für die Senken fest.

![Senkeneigenschaften](media/data-flow/sink-properties.png "Festlegen von Senkeneigenschaften")

## <a name="parameterizing-data-flows"></a>Parametrisieren von Datenflüssen

### <a name="parameterized-datasets"></a>Parametrisierte Datasets

Wenn im Datenfluss parametrisierte Datasets verwendet werden, legen Sie die Parameterwerte auf der Registerkarte **Einstellungen** fest.

![Ausführen von Datenflussparametern](media/data-flow/params.png "Parameter")

### <a name="parameterized-data-flows"></a>Parametrisierte Datenflüsse

Wenn der Datenfluss parametrisiert ist, legen Sie die dynamischen Werte der Datenflussparameter auf der Registerkarte **Parameter** fest. Sie können entweder die Ausdruckssprache für die ADF-Pipeline oder die Ausdruckssprache für Datenflüsse verwenden, um dynamische oder literale Parameterwerte zuzuweisen. Weitere Informationen finden Sie unter [Datenflussparameter](parameters-data-flow.md).

### <a name="parameterized-compute-properties"></a>Parametrisierte Compute-Eigenschaften.

Sie können die Anzahl von Kernen oder den Computetyp parametrisieren, wenn Sie die Azure Integration Runtime mit automatischer Auflösung verwenden und Werte für compute.corecount und compute.computetype angeben.

![Ausführen von Datenflussparametern (Beispiel)](media/data-flow/parameterize-compute.png "Parameterbeispiel")

## <a name="pipeline-debug-of-data-flow-activity"></a>Debuggen der Pipeline der Datenflussaktivität

Wenn Sie ein Debugging der Pipeline mit einer Datenflussaktivität ausführen möchten, müssen Sie den Datenfluss-Debugmodus über den Schieberegler **Datenfluss debuggen** auf der oberen Leiste aktivieren. Im Debugmodus können Sie den Datenfluss für einen aktiven Spark-Cluster ausführen. Weitere Informationen finden Sie unter [Debugmodus](concepts-data-flow-debug-mode.md).

![Schaltfläche „Debuggen“](media/data-flow/debugbutton.png "Schaltfläche „Debuggen“")

Das Debuggen der Pipeline wird für den aktiven Debugcluster und nicht für die Integration Runtime-Umgebung ausgeführt, die in den Einstellungen für die Datenflussaktivität angegeben ist. Beim Starten des Debugmodus können Sie die Computeumgebung für das Debuggen auswählen.

## <a name="monitoring-the-data-flow-activity"></a>Überwachen der Datenflussaktivität

Die Datenflussaktivität verfügt über eine besondere Überwachungsoberfläche, auf der Sie Informationen zu Partitionierung, Phasenzeit und Datenherkunft anzeigen können. Sie öffnen den Überwachungsbereich über das Brillensymbol unter **Aktionen**. Weitere Informationen finden Sie unter [Überwachen von Datenflüssen](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Verwenden der Ergebnisse der Datenflussaktivität in einer nachfolgenden Aktivität

Die Datenflussaktivität gibt Metriken bezüglich der Anzahl der Zeilen aus, die in jeder Senke geschrieben werden und die in jeder Quelle gelesen werden. Diese Ergebnisse werden im Abschnitt `output` des Ergebnisses der Aktivitätsausführung zurückgegeben. Die zurückgegebenen Metriken weisen das Format des folgenden JSON auf.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

Verwenden Sie beispielsweise `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`, um die Anzahl der in eine Senke namens „sink1“ in einer Aktivität namens „dataflowActivity“ geschriebenen Zeile zu erhalten.

Verwenden Sie `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`, um die Anzahl der von einer Quelle namens „source1“ gelesenen Zeile abzurufen, die in dieser Senke verwendet wurde.

> [!NOTE]
> Wenn für eine Senke keine Zeilen geschrieben wurden, wird sie nicht in Metriken angezeigt. Vorhandene Zeilen können durch die `contains`-Funktion überprüft werden. Beispielsweise überprüft `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')`, ob Zeilen in sink1 geschrieben wurden.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über Ablaufsteuerungsaktivitäten, die von Data Factory unterstützt werden: 

- [Aktivität „If Condition“](control-flow-if-condition-activity.md)
- [Aktivität „Pipeline ausführen“](control-flow-execute-pipeline-activity.md)
- [ForEach-Aktivität](control-flow-for-each-activity.md)
- [Aktivität „Metadaten abrufen“](control-flow-get-metadata-activity.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [Webaktivität](control-flow-web-activity.md)
- [Until-Aktivität](control-flow-until-activity.md)
