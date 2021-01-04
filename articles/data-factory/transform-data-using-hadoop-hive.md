---
title: Transformieren von Daten mit der Hadoop Hive-Aktivität
description: Erfahren Sie, wie Sie die Hive-Aktivität in Azure Data Factory verwenden können, um Hive-Abfragen in einem bedarfsgesteuerten/eigenen HDInsight-Cluster auszuführen.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 05/08/2019
ms.openlocfilehash: 20deb1a235843bf5fe5c42c513e1d8d2117c65c5
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637903"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Transformieren von Daten mit der Hadoop Hive-Aktivität in Azure Data Factory

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-hive-activity.md)
> * [Aktuelle Version](transform-data-using-hadoop-hive.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Die HDInsight Hive-Aktivität in einer Data Factory-[Pipeline](concepts-pipelines-activities.md) wendet Hive-Abfragen auf [Ihren eigenen](compute-linked-services.md#azure-hdinsight-linked-service) oder [bedarfsgesteuerten](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-Cluster an. Dieser Artikel baut auf dem Artikel zu [Datentransformationsaktivitäten](transform-data.md) auf, der eine allgemeine Übersicht über die Datentransformation und die unterstützten Transformationsaktivitäten bietet.

Wenn Sie noch nicht mit Azure Data Factory vertraut sind, lesen Sie zunächst den Artikel [Einführung in Azure Data Factory](introduction.md), und durchlaufen Sie anschließend das Tutorial [Transformieren von Daten](tutorial-transform-data-spark-powershell.md), bevor Sie diesen Artikel lesen. 

## <a name="syntax"></a>Syntax

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
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
| Eigenschaft            | BESCHREIBUNG                                                  | Erforderlich |
| ------------------- | ------------------------------------------------------------ | -------- |
| name                | Der Name der Aktivität                                         | Ja      |
| description         | Ein Text, der beschreibt, wofür die Aktivität verwendet wird.                | Nein       |
| type                | Für die Hive-Aktivität ist der Aktivitätstyp „HDInsightHive“.        | Ja      |
| linkedServiceName   | Verweis auf den HDInsight-Cluster, der als verknüpfter Dienst in Data Factory registriert ist. Weitere Informationen zu diesem verknüpften Dienst finden Sie im Artikel [Von Azure Data Factory unterstützten Compute-Umgebungen](compute-linked-services.md). | Ja      |
| scriptLinkedService | Verweis auf einen verknüpften Azure Storage-Dienst, der zum Speichern des auszuführenden Hive-Skripts verwendet wird. Hier werden nur die verknüpften **[Azure Blob Storage](./connector-azure-blob-storage.md)** und **[ADLS Gen2](./connector-azure-data-lake-storage.md)** -Dienste unterstützt. Wenn Sie diesen verknüpften Dienst nicht angeben, wird der im verknüpften HDInsight-Dienst definierte verknüpfte Azure Storage-Dienst genutzt.  | Nein       |
| scriptPath          | Geben Sie den Pfad der Skriptdatei an, die im Azure Storage-Speicher gespeichert ist, auf den „scriptLinkedService“ verweist. Beim Dateinamen muss die Groß-/Kleinschreibung beachtet werden. | Ja      |
| getDebugInfo        | Gibt an, ob die Protokolldateien in den Azure Storage-Speicher kopiert werden, der vom HDInsight-Cluster verwendet (oder) von „scriptLinkedService“ angegeben wird. Zulässige Werte: „None“, „Always“ oder „Failure“. Standardwert: Keine. | Nein       |
| Argumente           | Gibt ein Array von Argumenten für einen Hadoop-Auftrag an. Die Argumente werden als Befehlszeilenargumente an jeden Vorgang übergeben. | Nein       |
| defines             | Geben Sie Parameter als Schlüssel-Wert-Paare für Verweise innerhalb des Hive-Skripts an. | Nein       |
| queryTimeout        | Abfragetimeoutwert (in Minuten). Zutreffend, wenn der HDInsight-Cluster mit dem Enterprise-Sicherheitspaket versehen ist. | Nein       |

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln erfahren Sie, wie Daten auf andere Weisen transformiert werden: 

* [U-SQL-Aktivität](transform-data-using-data-lake-analytics.md)
* [Pig-Aktivität](transform-data-using-hadoop-pig.md)
* [MapReduce-Aktivität](transform-data-using-hadoop-map-reduce.md)
* [Hadoop-Streamingaktivität](transform-data-using-hadoop-streaming.md)
* [Spark-Aktivität](transform-data-using-spark.md)
* [Benutzerdefinierte .NET-Aktivität](transform-data-using-dotnet-custom-activity.md)
* [Batchausführungsaktivität für Azure Machine Learning Studio (Classic)](transform-data-using-machine-learning.md)
* [Aktivität „Gespeicherte Prozedur“](transform-data-using-stored-procedure.md)