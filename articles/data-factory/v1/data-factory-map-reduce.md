---
title: Aufrufen eines MapReduce-Programms über Azure Data Factory
description: Erfahren Sie, wie Sie Daten verarbeiten, indem Sie MapReduce-Programme mithilfe einer Azure Data Factory auf einen Azure HDInsight-Cluster anwenden.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 8bdcaf20330a3700681fd96f858370dd7dcdf4c7
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495428"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Aufrufen von MapReduce-Programmen über Data Factory
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
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Transformieren von Daten mithilfe der MapReduce-Aktivität in Data Factory](../transform-data-using-hadoop-map-reduce.md).


Die HDInsight MapReduce-Aktivität in einer Data Factory-[Pipeline](data-factory-create-pipelines.md) wendet MapReduce-Programme auf [Ihren eigenen](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) oder einen [bedarfsgesteuerten](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows-/Linux-basierten HDInsight-Cluster an. Dieser Artikel baut auf dem Artikel zu [Datentransformationsaktivitäten](data-factory-data-transformation-activities.md) auf, der eine allgemeine Übersicht über die Datentransformation und die unterstützten Transformationsaktivitäten bietet.

> [!NOTE] 
> Wenn Sie noch nicht mit Azure Data Factory vertraut sind, lesen Sie zunächst den Artikel [Einführung in Azure Data Factory](data-factory-introduction.md), und durchlaufen Sie anschließen das Tutorial [Erstellen Ihrer ersten Pipeline](data-factory-build-your-first-pipeline.md), bevor Sie diesen Artikel lesen.  

## <a name="introduction"></a>Einführung
Eine Pipeline in einer Azure Data Factory verarbeitet Daten in verknüpften Speicherdiensten mithilfe verknüpfter Compute Services. Sie enthält eine Abfolge von Aktivitäten, wobei jede Aktivität einen bestimmten Verarbeitungsvorgang ausführt. In diesem Artikel wird die Verwendung der HDInsight-Aktivität „MapReduce“ beschrieben.

Weitere Informationen zum Ausführen von Pig-/Hive-Skripts auf einem Windows-/Linux-basierten HDInsight-Cluster über eine Pipeline mithilfe von Pig-/Hive-HDInsight-Aktivitäten finden Sie unter [Pig](data-factory-pig-activity.md) und [Hive](data-factory-hive-activity.md). 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON für die HDInsight-Aktivität „MapReduce“
Gehen Sie für die JSON-Definition der HDInsight-Aktivität so vor: 

1. Legen Sie den **Typ** der **Aktivität** auf **HDInsight** fest.
2. Geben Sie für die Eigenschaft **className** den Namen der Klasse an.
3. Geben Sie den Pfad zur JAR-Datei an, einschließlich des Dateinamens für die Eigenschaft **jarFilePath** .
4. Geben Sie den verknüpften Dienst an, der auf die Azure Blob Storage-Instanz verweist, die die JAR-Datei für die **jarLinkedService**-Eigenschaft enthält.   
5. Geben Sie im Abschnitt **arguments** Argumente für das MapReduce-Programm an. Zur Laufzeit werden ein paar zusätzliche Argumente aus dem MapReduce-Framework angezeigt (z.B.: mapreduce.job.tags). Um Ihre Argumente mit den MapReduce-Argumenten zu unterscheiden, sollten Sie erwägen, sowohl Option als auch Wert als Argumente zu verwenden, wie im folgenden Beispiel gezeigt („-s“, „--input“, „--output“ usw. sind Optionen, denen ihre Werte unmittelbar folgen).

    ```JSON   
    {
        "name": "MahoutMapReduceSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "-s",
                            "SIMILARITY_LOGLIKELIHOOD",
                            "--input",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                            "--output",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                            "--maxSimilaritiesPerItem",
                            "500",
                            "--tempDir",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                        ]
                    },
                    "inputs": [
                        {
                            "name": "MahoutInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "MahoutOutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MahoutActivity",
                    "description": "Custom Map Reduce to generate Mahout result",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-01-03T00:00:00Z",
            "end": "2017-01-04T00:00:00Z"
        }
    }
    ```
   Mit der HDInsight-Aktivität „MapReduce“ können Sie beliebige MapReduce-JAR-Dateien auf einem HDInsight-Cluster ausführen. In der folgenden JSON-Beispieldefinition einer Pipeline wird die HDInsight-Aktivität für die Ausführung einer Mahout-JAR-Datei konfiguriert.

## <a name="sample-on-github"></a>Beispiel auf GitHub
Unter [Data Factory-Beispiele auf GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON/MapReduce_Activity_Sample)können Sie ein Beispiel für die Verwendung der HDInsight-Aktivität „MapReduce“ herunterladen.  

## <a name="running-the-word-count-program"></a>Ausführen des Wortzählungsprogramms
Die Pipeline in diesem Beispiel führt das Zuordnungs-/Reduzierungsprogramm für die Wortzählung auf Ihrem Azure HDInsight-Cluster aus.   

### <a name="linked-services"></a>Verknüpfte Dienste
Erstellen Sie zunächst einen verknüpften Dienst, um die vom Azure HDInsight-Cluster verwendete Azure Storage-Instanz mit der Azure Data Factory zu verknüpfen. Denken Sie beim Kopieren und Einfügen des folgenden Codes daran, **account name** **account key** durch den Namen und Schlüssel Ihrer Azure Storage-Instanz zu ersetzen. 

#### <a name="azure-storage-linked-service"></a>Mit Azure Storage verknüpfter Dienst

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Mit Azure HDInsight verknüpfter Dienst
Erstellen Sie anschließend einen verknüpften Dienst, um Ihren Azure HDInsight-Cluster mit der Azure Data Factory zu verknüpfen. Ersetzen Sie beim Kopieren und Einfügen des folgenden Codes den HDInsight-Clusternamen ( **HDInsight cluster name** ) durch den Namen des HDInsight-Clusters, und ändern Sie die Werte für Benutzername und Kennwort.   

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>Datasets
#### <a name="output-dataset"></a>Ausgabedataset
Die Pipeline in diesem Beispiel akzeptiert keine Eingaben. Geben Sie für die HDInsight-Aktivität „MapReduce“ ein Ausgabedataset an. Dieses Dataset ist nur ein für die Pipeline erforderliches Dummydataset.  

```JSON
{
    "name": "MROutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "WordCountOutput1.txt",
            "folderPath": "example/data/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
Die Pipeline in diesem Beispiel besitzt nur eine einzelne Aktivität vom Typ „HDInsightMapReduce“. Einige wichtige Eigenschaften in JSON: 

| Eigenschaft | Notizen |
|:--- |:--- |
| type |Der Typ muss auf **HDInsightMapReduce** festgelegt werden. |
| className |Der Name der Klasse lautet **wordcount** |
| jarFilePath |Der Pfad zu der JAR-Datei mit der angegebenen Klasse. Denken Sie beim Kopieren und Einfügen des folgenden Codes daran, den Namen des Clusters zu ändern. |
| jarLinkedService |Der mit Azure Storage verknüpfte Dienst mit der JAR-Datei. Dieser verknüpfte Dienst verweist auf den Speicher, der dem HDInsight-Cluster zugeordnet ist. |
| Argumente |Das Wortzählungsprogramm akzeptiert zwei Argumente: eine Eingabe und eine Ausgabe. Die Eingabedatei ist die Datei „davinci.txt“. |
| frequency/interval |Die Werte für diese Eigenschaften entsprechen dem Ausgabedataset. |
| linkedServiceName |Bezieht sich auf den mit HDInsight verknüpften Dienst, den Sie vorhin erstellt haben. |

```JSON
{
    "name": "MRSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run the Word Count Program",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "wordcount",
                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": [
                        "/example/data/gutenberg/davinci.txt",
                        "/example/data/WordCountOutput1"
                    ]
                },
                "outputs": [
                    {
                        "name": "MROutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "MRActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-03T00:00:00Z",
        "end": "2014-01-04T00:00:00Z"
    }
}
```

## <a name="run-spark-programs"></a>Aufrufen von Spark-Programmen
Sie können die MapReduce-Aktivität verwenden, um Spark-Programme in Ihrem HDInsight Spark-Cluster auszuführen. Weitere Informationen finden Sie unter [Invoke Spark programs from Azure Data Factory](data-factory-spark.md) (Aufrufen von Spark-Programmen aus Azure Data Factory).  

[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: /previous-versions/azure/dn834987(v=azure.100)
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>Weitere Informationen
* [Hive-Aktivität](data-factory-hive-activity.md)
* [Pig-Aktivität](data-factory-pig-activity.md)
* [Hadoop-Streamingaktivität](data-factory-hadoop-streaming-activity.md)
* [Invoke Spark programs (Aufrufen von Spark-Programmen)](data-factory-spark.md)
* [Invoke R scripts (Aufrufen von R-Skripts)](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)