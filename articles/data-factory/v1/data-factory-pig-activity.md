---
title: Transformieren von Daten mit der Pig-Aktivität in Azure Data Factory
description: Erfahren Sie, wie Sie die Pig-Aktivität in Azure Data Factory v1 verwenden können, um Pig-Skripte in einem bedarfsgesteuerten/eigenen HDInsight-Cluster auszuführen.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 2f59734b5452b5a06b49583954f8851e84e8f84d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387171"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Transformieren von Daten mit der Pig-Aktivität in Azure Data Factory
> [!div class="op_single_selector" title1="Transformationsaktivitäten"]
> * [Hive-Aktivität](data-factory-hive-activity.md) 
> * [Pig-Aktivität](data-factory-pig-activity.md)
> * [MapReduce-Aktivität](data-factory-map-reduce.md)
> * [Hadoop-Streamingaktivität](data-factory-hadoop-streaming-activity.md)
> * [Spark-Aktivität](data-factory-spark.md)
> * [Batchausführungsaktivität für Azure Machine Learning Studio (klassisch)](data-factory-azure-ml-batch-execution-activity.md)
> * [Ressourcenaktualisierungsaktivität für Azure Machine Learning Studio (klassisch)](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivität „Gespeicherte Prozedur“](data-factory-stored-proc-activity.md)
> * [U-SQL-Aktivität für Data Lake Analytics](data-factory-usql-activity.md)
> * [Benutzerdefinierte .NET-Aktivität](data-factory-use-custom-activities.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [transform data using Pig activity in Data Factory](../transform-data-using-hadoop-pig.md) (Transformieren von Daten mithilfe der Pig-Aktivität in Data Factory).


Die HDInsight Pig-Aktivität in einer Data Factory-[Pipeline](data-factory-create-pipelines.md) wendet Pig-Abfragen auf [Ihren eigenen](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) oder [bedarfsgesteuerten](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows-/Linux-basierten HDInsight-Cluster an. Dieser Artikel baut auf dem Artikel zu [Datentransformationsaktivitäten](data-factory-data-transformation-activities.md) auf, der eine allgemeine Übersicht über die Datentransformation und die unterstützten Transformationsaktivitäten bietet.

> [!NOTE] 
> Wenn Sie noch nicht mit Azure Data Factory vertraut sind, lesen Sie zunächst den Artikel [Einführung in Azure Data Factory](data-factory-introduction.md), und durchlaufen Sie anschließen das Tutorial [Erstellen Ihrer ersten Pipeline](data-factory-build-your-first-pipeline.md), bevor Sie diesen Artikel lesen. 

## <a name="syntax"></a>Syntax

```JSON
{
  "name": "HiveActivitySamplePipeline",
  "properties": {
    "activities": [
      {
        "name": "Pig Activity",
        "description": "description",
        "type": "HDInsightPig",
        "inputs": [
          {
            "name": "input tables"
          }
        ],
        "outputs": [
          {
            "name": "output tables"
          }
        ],
        "linkedServiceName": "MyHDInsightLinkedService",
        "typeProperties": {
          "script": "Pig script",
          "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
      }
    ]
  }
}
```

## <a name="syntax-details"></a>Syntaxdetails

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| name |Der Name der Aktivität |Ja |
| description |Ein Text, der beschreibt, wofür die Aktivität verwendet wird. |Nein |
| type |HDInsightPig |Ja |
| inputs |Mindestens eine von der Pig-Aktivität genutzte Eingabe |Nein |
| outputs |Mindestens eine von der Pig-Aktivität erzeugte Ausgabe |Ja |
| linkedServiceName |Verweis auf den HDInsight-Cluster, der als verknüpfter Dienst in Data Factory registriert ist. |Ja |
| script |Angabe des Pig-Skripts inline |Nein |
| scriptPath |Speichern Sie das Pig-Skript in Azure Blob Storage, und geben Sie den Pfad zur Datei an. Verwenden Sie die Eigenschaft "script" oder "scriptPath". Beide können nicht zusammen verwendet werden. Beim Dateinamen muss die Groß-/Kleinschreibung beachtet werden. |Nein |
| defines |Geben Sie Parameter als Schlüssel-Wert-Paare für Verweise innerhalb des Pig-Skripts an. |Nein |

## <a name="example"></a>Beispiel
Betrachten wir ein Beispiel mit Analysen von Spielprotokollen, in dem Sie die Zeit ermitteln möchten, die Benutzer mit den Spielen Ihres Unternehmens verbringen.

Das folgende Beispielspielprotokoll ist eine durch Komma (,) getrennte Datei. Es enthält die folgenden Felder: ProfileID, SessionStart, Duration, SrcIPAddress und GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

Das **Pig-Skript** zur Verarbeitung dieser Daten sieht folgendermaßen aus:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Um dieses Pig-Skript in einer Data Factory-Pipeline auszuführen, führen Sie die folgenden Schritte aus:

1. Erstellen Sie einen verknüpften Dienst, um [Ihren eigenen HDInsight-Computecluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) zu registrieren oder einen [bedarfsgesteuerten HDInsight-Computecluster](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) zu konfigurieren. Wir nennen diesen verknüpften Dienst **HDInsightLinkedService**.
2. Erstellen Sie einen [verknüpften Dienst](data-factory-azure-blob-connector.md), um die Verbindung mit Azure Blob Storage zu konfigurieren, in dem die Daten gehostet werden. Wir nennen diesen verknüpften Dienst **StorageLinkedService**.
3. Erstellen Sie [Datasets](data-factory-create-datasets.md) , die auf die Eingabe- und die Ausgabedaten verweisen. Wir nennen das Eingabedataset **PigSampleIn** und das Ausgabedataset **PigSampleOut**.
4. Kopieren Sie die Pig-Abfrage in eine Datei in die in Schritt 2 konfigurierte Azure Blob Storage-Instanz. Wenn der Azure-Speicher, der die Daten hostet, sich von dem Speicher unterscheidet, der die Abfragedatei hostet, erstellen Sie einen separaten mit verknüpften Azure Storage-Dienst. Informationen dazu finden Sie im verknüpften Dienst in der Aktivitätskonfiguration. Verwenden Sie **scriptPath**, um den Pfad zur Pig-Skriptdatei und zu **scriptLinkedService** anzugeben. 
   
   > [!NOTE]
   > Sie können das Pig-Skript auch inline in der Aktivitätsdefinition bereitstellen, indem Sie die **script** -Eigenschaft verwenden. Dieser Ansatz wird jedoch nicht empfohlen, da alle Sonderzeichen im Skript mit Escapezeichen versehen werden müssen und zu Debuggingproblemen führen können. Die bewährte Methode ist, Schritt 4 auszuführen.
   >
   >
5. Erstellen Sie die Pipeline mit der HDInsightPig-Aktivität. Diese Aktivität verarbeitet die Eingabedaten durch Ausführen des Pig-Skripts im HDInsight-Cluster.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
              "scriptLinkedService": "StorageLinkedService"
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
6. Stellen Sie die Pipeline bereit. Weitere Informationen finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md) . 
7. Überwachen Sie die Pipeline mithilfe der Überwachungs- und Verwaltungsansichten von Data Factory. Weitere Informationen finden Sie im Artikel [Überwachen und Verwalten von Data Factory-Pipelines](data-factory-monitor-manage-pipelines.md) .

## <a name="specifying-parameters-for-a-pig-script"></a>Angeben der Parameter für ein Pig-Skript
Betrachten Sie das folgende Beispiel: Spielprotokolle werden täglich in Azure Blob Storage erfasst und in einem basierend auf Datum und Uhrzeit partitionierten Ordner gespeichert. Sie möchten das Pig-Skript parametrisieren, den Eingabeordnerpfad dynamisch während der Laufzeit übergeben und zudem die Ausgabe partitioniert mit Datum und Uhrzeit erzeugen.

Gehen Sie folgendermaßen vor, um das parametrisierte Pig-Skript zu verwenden:

* Legen Sie die Parameter in **defines** fest.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0:MM}/dayno={0: dd}/',SliceStart)",
                "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
* Verweisen Sie im Pig-Skript mit " **$parameterName**" wie im folgenden Beispiel gezeigt auf die Parameter:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Weitere Informationen
* [Hive-Aktivität](data-factory-hive-activity.md)
* [MapReduce-Aktivität](data-factory-map-reduce.md)
* [Hadoop-Streamingaktivität](data-factory-hadoop-streaming-activity.md)
* [Invoke Spark programs (Aufrufen von Spark-Programmen)](data-factory-spark.md)
* [Invoke R scripts (Aufrufen von R-Skripts)](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)
