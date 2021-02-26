---
title: Transformieren von Daten mit der Hadoop-Streamingaktivität
description: Erfahren Sie, wie Sie die Hadoop-Streamingaktivität in Azure Data Factory verwenden können, um Daten durch die Ausführung von Hadoop-Streamingprogrammen in einem Hadoop-Cluster zu transformieren.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: e2a9bc9d664ba15da3cdefa5cf28519ab703d6ce
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361433"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformieren von Daten mit der Hadoop-Streamingaktivität in Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Aktuelle Version](transform-data-using-hadoop-streaming.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Die HDInsight-Streamingaktivität in einer Data Factory-[Pipeline](concepts-pipelines-activities.md) wendet Hadoop-Streamingprogramme auf [Ihren eigenen](compute-linked-services.md#azure-hdinsight-linked-service) oder [bedarfsgesteuerten](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-Cluster an. Dieser Artikel baut auf dem Artikel zu [Datentransformationsaktivitäten](transform-data.md) auf, der eine allgemeine Übersicht über die Datentransformation und die unterstützten Transformationsaktivitäten bietet.

Wenn Sie noch nicht mit Azure Data Factory vertraut sind, lesen Sie zunächst den Artikel [Einführung in Azure Data Factory](introduction.md), und durchlaufen Sie anschließend das Tutorial [Transformieren von Daten](tutorial-transform-data-spark-powershell.md), bevor Sie diesen Artikel lesen. 

## <a name="json-sample"></a>JSON-Beispiel
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Syntaxdetails

| Eigenschaft          | BESCHREIBUNG                              | Erforderlich |
| ----------------- | ---------------------------------------- | -------- |
| name              | Der Name der Aktivität                     | Ja      |
| description       | Ein Text, der beschreibt, wofür die Aktivität verwendet wird. | Nein       |
| type              | Für die Hadoop-Streamingaktivität ist der Aktivitätstyp „HDInsightStreaming“. | Ja      |
| linkedServiceName | Verweis auf den HDInsight-Cluster, der als verknüpfter Dienst in Data Factory registriert ist. Weitere Informationen zu diesem verknüpften Dienst finden Sie im Artikel [Von Azure Data Factory unterstützten Compute-Umgebungen](compute-linked-services.md). | Ja      |
| mapper            | Gibt den Namen der ausführbaren Zuordnungsdatei (Mapper) an. | Ja      |
| reducer           | Gibt den Namen der ausführbaren Reduzierungsdatei (Reducer) an. | Ja      |
| combiner          | Gibt den Namen der ausführbaren Kombinierungsdatei (Combiner) an. | Nein       |
| fileLinkedService | Verweis auf einen verknüpften Azure Storage-Dienst, der zum Speichern der Mapper-, Combiner- und Reducer-Programme verwendet wird. Hier werden nur die verknüpften **[Azure Blob Storage](./connector-azure-blob-storage.md)** und **[ADLS Gen2](./connector-azure-data-lake-storage.md)** -Dienste unterstützt. Wenn Sie diesen verknüpften Dienst nicht angeben, wird der im verknüpften HDInsight-Dienst definierte verknüpfte Azure Storage-Dienst genutzt. | Nein       |
| filePath          | Geben Sie ein Array mit Pfaden zu den Mapper-, Combiner- und Reducer-Programmen an, die im Azure Storage-Speicher gespeichert sind, auf den „fileLinkedService“ verweist. Der Pfad berücksichtigt die Groß- und Kleinschreibung. | Ja      |
| input             | Gibt den WASB-Pfad zur Eingabedatei für den Mapper an. | Ja      |
| output            | Gibt den WASB-Pfad zur Ausgabedatei für den Reducer an. | Ja      |
| getDebugInfo      | Gibt an, ob die Protokolldateien in den Azure Storage-Speicher kopiert werden, der vom HDInsight-Cluster verwendet (oder) von „scriptLinkedService“ angegeben wird. Zulässige Werte: „None“, „Always“ oder „Failure“. Standardwert: Keine. | Nein       |
| Argumente         | Gibt ein Array von Argumenten für einen Hadoop-Auftrag an. Die Argumente werden als Befehlszeilenargumente an jeden Vorgang übergeben. | Nein       |
| defines           | Geben Sie Parameter als Schlüssel-Wert-Paare für Verweise innerhalb des Hive-Skripts an. | Nein       | 

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln erfahren Sie, wie Daten auf andere Weisen transformiert werden: 

* [U-SQL-Aktivität](transform-data-using-data-lake-analytics.md)
* [Hive-Aktivität](transform-data-using-hadoop-hive.md)
* [Pig-Aktivität](transform-data-using-hadoop-pig.md)
* [MapReduce-Aktivität](transform-data-using-hadoop-map-reduce.md)
* [Spark-Aktivität](transform-data-using-spark.md)
* [Benutzerdefinierte .NET-Aktivität](transform-data-using-dotnet-custom-activity.md)
* [Batchausführungsaktivität für Azure Machine Learning Studio (klassisch)](transform-data-using-machine-learning.md)
* [Aktivität „Gespeicherte Prozedur“](transform-data-using-stored-procedure.md)