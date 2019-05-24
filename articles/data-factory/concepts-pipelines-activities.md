---
title: Pipelines und Aktivitäten in Azure Data Factory | Microsoft-Dokumentation
description: Informationen zu Pipelines und Aktivitäten in Azure Data Factory
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 63a86fb9498c7c1b1cd527accca84c83a28e01c3
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788669"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipelines und Aktivitäten in Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-create-pipelines.md)
> * [Aktuelle Version](concepts-pipelines-activities.md)

In diesem Artikel erhalten Sie Informationen zu Pipelines und Aktivitäten in Azure Data Factory und erfahren, wie diese zum Erstellen datengesteuerter lückenloser Workflows für Ihre Datenverschiebungs- und Datenverarbeitungsszenarien genutzt werden können.

## <a name="overview"></a>Übersicht
Eine Data Factory kann eine oder mehrere Pipelines haben. Bei einer Pipeline handelt es sich um eine logische Gruppierung von Aktivitäten, die zusammen eine Aufgabe bilden. Eine Pipeline kann beispielsweise eine Gruppe von Aktivitäten enthalten, die Protokolldaten erfassen und bereinigen. Anschließend wird ein Spark-Auftrag in einem HDInsight-Cluster gestartet, um die Protokolldaten zu analysieren. Der Vorteil hierbei besteht darin, dass Sie mit der Pipeline nicht jede Aktivität einzeln verwalten müssen, sondern dies als Gruppe tun können. Beispielsweise können Sie die Pipeline bereitstellen und planen, anstatt dies für die Aktivitäten jeweils individuell durchzuführen.

Die Aktivitäten in einer Pipeline definieren Aktionen, die Sie auf Ihre Daten anwenden. Sie können z.B. mit einer Kopieraktivität Daten aus einer lokalen SQL Server-Instanz in eine Instanz von Azure Blob Storage kopieren. Verwenden Sie dann eine Hive-Aktivität, die ein Hive-Skript auf einen Azure HDInsight-Cluster anwendet, um Daten aus dem Blob Storage zu verarbeiten/transformieren, um Ausgabedaten zu produzieren. Kopieren Sie die Ausgabedaten schließlich mit einer zweiten Kopieraktivität in ein Azure SQL Data Warehouse, auf Basis dessen Business Intelligence-Berichtslösungen (BI) erstellt werden.

Data Factory unterstützt drei Arten von Aktivitäten: [Datenverschiebungsaktivitäten](copy-activity-overview.md), [Datentransformationsaktivitäten](transform-data.md) und [Steuerungsaktivitäten](control-flow-web-activity.md). Eine Aktivität kann über null oder mehr [Eingabedatasets](concepts-datasets-linked-services.md) verfügen und ein oder mehrere [Ausgabedatasets](concepts-datasets-linked-services.md) erstellen. Das folgende Diagramm zeigt die Beziehung zwischen Pipeline, Aktivität und Dataset in der Data Factory an:

![Beziehung zwischen Dataset, Aktivität und Pipeline](media/concepts-pipelines-activities/relationship-between-dataset-pipeline-activity.png)

Ein Eingabedataset stellt die Eingabe für eine Aktivität in der Pipeline dar, und ein Ausgabedataset stellt die Ausgabe für die Aktivität dar. Datasets bestimmen Daten in verschiedenen Datenspeichern, z.B. Tabellen, Dateien, Ordnern und Dokumenten. Nachdem Sie ein Dataset erstellt haben, können Sie es zusammen mit Aktivitäten in einer Pipeline verwenden. Bei einem Dataset kann es sich beispielsweise um ein Eingabe-/Ausgabedataset einer Kopieraktivität oder einer HDInsightHive-Aktivität handeln. Weitere Informationen über Datasets finden Sie im Artikel [Datasets in Azure Data Factory](concepts-datasets-linked-services.md).

## <a name="data-movement-activities"></a>Datenverschiebungsaktivitäten
Die Kopieraktivität in Data Factory kopiert die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher. Data Factory unterstützt die in der Tabelle in diesem Abschnitt aufgeführten Datenspeicher. Daten aus beliebigen Quellen können in beliebige Senken geschrieben werden. Klicken Sie auf einen Datenspeicher, um zu erfahren, wie Daten in diesen/aus diesem Speicher kopiert werden.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

Weitere Informationen finden Sie im Artikel [Übersicht über die Kopieraktivität](copy-activity-overview.md).

## <a name="data-transformation-activities"></a>Datentransformationsaktivitäten
Azure Data Factory unterstützt die folgenden Transformationsaktivitäten, die Pipelines entweder einzeln oder mit einer anderen Aktivität verkettet hinzugefügt werden können.

Datentransformationsaktivität | Compute-Umgebung
---------------------------- | -------------------
[Hive](transform-data-using-hadoop-hive.md) | HDInsight [Hadoop]
[Pig](transform-data-using-hadoop-pig.md) | HDInsight [Hadoop]
[MapReduce](transform-data-using-hadoop-map-reduce.md) | HDInsight [Hadoop]
[Hadoop-Datenströme](transform-data-using-hadoop-streaming.md) | HDInsight [Hadoop]
[Spark](transform-data-using-spark.md) | HDInsight [Hadoop]
[Machine Learning-Aktivitäten: Batchausführung und Ressourcenaktualisierung](transform-data-using-machine-learning.md) | Azure-VM
[Gespeicherte Prozedur](transform-data-using-stored-procedure.md) | Azure SQL, Azure SQL Data Warehouse oder SQL Server
[U-SQL](transform-data-using-data-lake-analytics.md) | Azure Data Lake Analytics
[Benutzerdefinierter Code](transform-data-using-dotnet-custom-activity.md) | Azure Batch
[Databricks-Notebook](transform-data-databricks-notebook.md) | Azure Databricks

Weitere Informationen finden Sie im Artikel [Datentransformationsaktivitäten](transform-data.md).

## <a name="control-activities"></a>Steuerungsaktivitäten
Die folgenden Steuerungsablaufaktivitäten werden unterstützt:

Steuerungsaktivität | BESCHREIBUNG
---------------- | -----------
[Aktivität „Pipeline ausführen“](control-flow-execute-pipeline-activity.md) | Mit der Aktivität „Pipeline ausführen“ kann eine Data Factory-Pipeline eine andere Pipeline aufrufen.
[Aktivität „ForEach“](control-flow-for-each-activity.md) | Mit der ForEach-Aktivität wird eine wiederholte Ablaufsteuerung in Ihrer Pipeline definiert. Diese Aktivität wird verwendet, um eine Sammlung zu durchlaufen. Sie führt die angegebenen Aktivitäten in einer Schleife aus. Die Schleifenimplementierung dieser Aktivität ähnelt der Foreach-Schleifenstruktur in Programmiersprachen.
[Aktivität „Web“](control-flow-web-activity.md) | Die Web-Aktivität kann verwendet werden, um einen benutzerdefinierten REST-Endpunkt aus einer Data Factory-Pipeline aufzurufen. Sie können Datasets und verknüpfte Dienste zur Verwendung und für den Zugriff durch die Aktivität übergeben.
[Aktivität „Lookup“](control-flow-lookup-activity.md) | Mit der Lookup-Aktivität können Sie einen Datensatz/Tabellennamen/Wert in einer externen Quelle lesen oder suchen. Auf die Ausgabe kann durch nachfolgende Aktivitäten verwiesen werden.
[Aktivität „Metadaten abrufen“](control-flow-get-metadata-activity.md) | Die Aktivität „Metadaten abrufen“ kann zum Abrufen von Metadaten für alle Daten in Azure Data Factory verwendet werden.
[Until-Aktivität](control-flow-until-activity.md) | Es wird eine „Wiederholen bis“-Schleife implementiert, die der Struktur einer Do-Until-Schleife in Programmiersprachen ähnelt. Sie führt eine Reihe von Aktivitäten in einer Schleife aus, bis die der Aktivität zugeordnete Bedingung als „true“ ausgewertet wird. In Data Factory können Sie einen Timeoutwert für die Until-Aktivität angeben.
[Aktivität „If Condition“](control-flow-if-condition-activity.md) | „If Condition“ kann zum Einrichten einer Verzweigung für Bedingungen verwendet werden, die als TRUE oder FALSE ausgewertet werden. Die Aktivität „If Condition“ bietet die gleiche Funktionalität wie eine If-Anweisung in Programmiersprachen. Sie wertet eine Aktivitätengruppe aus, wenn die Bedingung als `true` ausgewertet wird, und eine weitere Aktivitätengruppe, wenn die Bedingung als `false` ausgewertet wird.
[Aktivität „Warten“](control-flow-wait-activity.md) | Wenn Sie eine Warteaktivität in einer Pipeline verwenden, wartet die Pipeline den angegebenen Zeitraum, bevor Sie die Ausführung nachfolgender Aktivitäten fortsetzt.

## <a name="pipeline-json"></a>Pipeline-JSON
Eine Pipeline wird wie folgt im JSON-Format definiert:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities":
        [
        ],
        "parameters": {
         }
    }
}
```

Tag | BESCHREIBUNG | Type | Erforderlich
--- | ----------- | ---- | --------
name | Name der Pipeline. Geben Sie einen Namen an, der die Aktion darstellt, die die Pipeline durchführt. <br/><ul><li>Maximale Anzahl von Zeichen: 140</li><li>Muss mit einem Buchstaben, einer Zahl oder einem Unterstrich (\_) beginnen.</li><li>Folgende Zeichen sind nicht zulässig: „.“, „+“, „?“, „/“, „<“, „>“, „*“, „%“, „&“, „:“, „\“.</li></ul> | Zeichenfolge | Ja
description | Geben Sie den Text an, der beschreibt, wofür die Pipeline verwendet wird. | Zeichenfolge | Nein 
Aktivitäten | Im Abschnitt **activities** kann mindestens eine Aktivität definiert werden. Weitere Informationen zum JSON-Element der Aktivitäten finden Sie im Abschnitt [Aktivitäts-JSON](#activity-json). | Array | Ja
parameters | Im Abschnitt **Parameter** kann mindestens ein Parameter in der Pipeline definiert werden. Dadurch wird die Pipeline flexibel wiederverwendbar. | Auflisten | Nein 

## <a name="activity-json"></a>JSON-Definition der Aktivität
Im Abschnitt **activities** kann mindestens eine Aktivität definiert werden. Es gibt zwei Haupttypen von Aktivitäten: Ausführungs- und Steuerungsaktivitäten.

### <a name="execution-activities"></a>Ausführungsaktivitäten
Ausführungsaktivitäten beinhalten [Datenverschiebungsaktivitäten](#data-movement-activities) und [Datentransformationsaktivitäten](#data-transformation-activities). Sie besitzen auf oberster Ebene die folgende Struktur:

```json
{
    "name": "Execution Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "linkedServiceName": "MyLinkedService",
    "policy":
    {
    },
    "dependsOn":
    {
    }
}
```

In der folgenden Tabelle werden Eigenschaften in der JSON-Definition der Aktivität beschrieben:

Tag | BESCHREIBUNG | Erforderlich
--- | ----------- | ---------
name | Der Name der Aktivität. Geben Sie einen Namen an, der die Aktion darstellt, die die Aktivität durchführt. <br/><ul><li>Maximale Anzahl von Zeichen: 55</li><li>Muss mit einem Buchstaben, einer Zahl oder einem Unterstrich (\_) beginnen.</li><li>Folgende Zeichen sind nicht zulässig: „.“, „+“, „?“, „/“, „<“, „>“, „*“, „%“, „&“, „:“, „\“. | Ja</li></ul>
description | Ein Text, der beschreibt, wofür die Aktivität verwendet wird. | Ja
type | Der Typ der Aktivität. Die verschiedenen Aktivitätstypen finden Sie in den Abschnitten [Datenverschiebungsaktivitäten](#data-movement-activities), [Datentransformationsaktivitäten](#data-transformation-activities) und [Steuerungsaktivitäten](#control-activities). | Ja
linkedServiceName | Name des verknüpften Diensts, der von der Aktivität verwendet wird.<br/><br/>Für eine Aktivität kann es erforderlich sein, den verknüpften Dienst anzugeben, der mit der erforderlichen Computeumgebung verknüpft ist. | „Ja“ für HDInsight-Aktivität, Azure Machine Learning-Aktivität für die Batchbewertung und Aktivität vom Typ „Gespeicherte Prozedur“. <br/><br/>„Nein“ für alle übrigen
typeProperties | Eigenschaften im Abschnitt „typeProperties“ sind abhängig vom jeweiligen Typ der Aktivität. Um Typeigenschaften für eine Aktivität anzuzeigen, klicken Sie auf die Links zur Aktivität im vorhergehenden Abschnitt. | Nein 
policy | Richtlinien, die das Laufzeitverhalten der Aktivität beeinflussen. Diese Eigenschaft enthält Zeitlimit- und Wiederholungsverhalten. Falls kein Wert angegeben wird, werden die Standardwerte verwendet. Weitere Informationen finden Sie im Abschnitt [Aktivitätsrichtlinie](#activity-policy). | Nein 
dependsOn | Diese Eigenschaft wird zur Definition von Aktivitätsabhängigkeiten und von Abhängigkeiten zwischen nachfolgenden und vorherigen Aktivitäten verwendet. Weitere Informationen finden Sie im Abschnitt [Aktivitätsabhängigkeit](#activity-dependency). | Nein 

### <a name="activity-policy"></a>Aktivitätsrichtlinie
Richtlinien beeinflussen das Laufzeitverhalten einer Aktivität und bieten Konfigurationsoptionen. Aktivitätsrichtlinien sind nur für Ausführungsaktivitäten verfügbar.

### <a name="activity-policy-json-definition"></a>JSON-Definition der Aktivitätsrichtlinien

```json
{
    "name": "MyPipelineName",
    "properties": {
      "activities": [
        {
          "name": "MyCopyBlobtoSqlActivity"
          "type": "Copy",
          "typeProperties": {
            ...
          },
         "policy": {
            "timeout": "00:10:00",
            "retry": 1,
            "retryIntervalInSeconds": 60,
            "secureOutput": true
         }
        }
      ],
        "parameters": {
           ...
        }
    }
}
```

JSON-Name | BESCHREIBUNG | Zulässige Werte | Erforderlich
--------- | ----------- | -------------- | --------
timeout | Gibt das Zeitlimit für die Ausführung der Aktivität an. | Timespan |  Nein. Das Standard-Zeitlimit beträgt 7 Tage.
retry | Maximale Anzahl der Wiederholungsversuche. | Integer |  Nein. Der Standardwert ist 0.
retryIntervalInSeconds | Verzögerung zwischen den Wiederholungsversuchen in Sekunden. | Integer |  Nein. Der Standardwert ist 30 Sekunden.
secureOutput | Bei Festlegung auf „true“ wird die Ausgabe der Aktivität als sicher betrachtet und von der Überwachung nicht protokolliert. | Boolean |  Nein. Die Standardeinstellung ist "false".

### <a name="control-activity"></a>Steuerungsaktivität
Steuerungsaktivitäten besitzen auf oberster Ebene die folgende Struktur:

```json
{
    "name": "Control Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "dependsOn":
    {
    }
}
```

Tag | BESCHREIBUNG | Erforderlich
--- | ----------- | --------
name | Der Name der Aktivität. Geben Sie einen Namen an, der die Aktion darstellt, die die Aktivität durchführt.<br/><ul><li>Maximale Anzahl von Zeichen: 55</li><li>Muss mit einem Buchstaben, einer Zahl oder einem Unterstrich (\_) beginnen.</li><li>Folgende Zeichen sind nicht zulässig: „.“, „+“, „?“, „/“, „<“, „>“, „*“, „%“, „&“, „:“, „\“. | Ja</li><ul>
description | Ein Text, der beschreibt, wofür die Aktivität verwendet wird. | Ja
type | Der Typ der Aktivität. Die verschiedenen Aktivitätstypen finden Sie in den Abschnitten [Datenverschiebungsaktivitäten](#data-movement-activities), [Datentransformationsaktivitäten](#data-transformation-activities) und [Steuerungsaktivitäten](#control-activities). | Ja
typeProperties | Eigenschaften im Abschnitt „typeProperties“ sind abhängig vom jeweiligen Typ der Aktivität. Um Typeigenschaften für eine Aktivität anzuzeigen, klicken Sie auf die Links zur Aktivität im vorhergehenden Abschnitt. | Nein 
dependsOn | Diese Eigenschaft wird zur Definition der Aktivitätsabhängigkeit und von Abhängigkeiten zwischen nachfolgenden und vorherigen Aktivitäten verwendet. Weitere Informationen finden Sie im Abschnitt [Aktivitätsabhängigkeit](#activity-dependency). | Nein 

### <a name="activity-dependency"></a>Aktivitätsabhängigkeit
Mit der Aktivitätsabhängigkeit wird definiert, wie nachfolgende Aktivitäten von vorherigen Aktivitäten abhängen. Dabei wird bestimmt, bei welcher Bedingung mit der nächsten Aufgabe fortgefahren wird. Eine Aktivität kann mit unterschiedlichen Abhängigkeitsbedingungen von einer oder mehreren vorherigen Aktivitäten abhängen.

Die unterschiedlichen Abhängigkeitsbedingungen lauten: „Erfolgreich“, „Fehlgeschlagen“, „Übersprungen“ und „Abgeschlossen“.

Bei einer Pipeline mit Aktivität A -> Aktivität B lauten die möglichen Szenarios beispielsweise folgendermaßen:

- Die Abhängigkeitsbedingung von Aktivität B zu Aktivität A lautet **Erfolgreich**: Aktivität B wird nur ausgeführt, wenn Aktivität A den Endstatus „Erfolgreich“ aufweist.
- Die Abhängigkeitsbedingung von Aktivität B zu Aktivität A lautet **Fehlgeschlagen**: Aktivität B wird nur ausgeführt, wenn Aktivität A den Endstatus „Fehlgeschlagen“ aufweist.
- Die Abhängigkeitsbedingung von Aktivität B zu Aktivität A lautet **Abgeschlossen**: Aktivität B wird nur ausgeführt, wenn Aktivität A den Endstatus „Erfolgreich“ oder „Fehlgeschlagen“ aufweist.
- Die Abhängigkeitsbedingung von Aktivität B zu Aktivität A lautet **Übersprungen**: Aktivität B wird nur ausgeführt, wenn Aktivität A den Endstatus „Übersprungen“ aufweist. Die Bedingung „Übersprungen“ tritt im Szenario Aktivität X -> Aktivität Y -> Aktivität Z auf. Hier wird eine Aktivität nur ausgeführt, wenn die vorherige Aktivität erfolgreich war. Tritt bei Aktivität X ein Fehler auf, erhält Aktivität Y den Status „Übersprungen“, da sie nicht ausgeführt wird. Dementsprechend erhält auch Aktivität Z den Status „Übersprungen“.

#### <a name="example-activity-2-depends-on-the-activity-1-succeeding"></a>Beispiel: Aktivität 2 hängt von der erfolgreich abgeschlossenen Aktivität 1 ab

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities": [
         {
            "name": "MyFirstActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            }
        },
        {
            "name": "MySecondActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            },
            "dependsOn": [
            {
                "activity": "MyFirstActivity",
                "dependencyConditions": [
                    "Succeeded"
                ]
            }
          ]
        }
      ],
      "parameters": {
       }
    }
}

```

## <a name="sample-copy-pipeline"></a>Beispiel einer Kopierpipeline
In der folgenden Beispielpipeline gibt es im Abschnitt **Copy** in the **Aktivitäten** . In diesem Beispiel kopieren Sie mit der [Kopieraktivität](copy-activity-overview.md) Daten aus Azure Blob Storage in eine Azure SQL-Datenbank.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "policy": {
          "retry": 2,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Beachten Sie folgende Punkte:

- Der Abschnitt „Activities“ enthält nur eine Aktivität, deren **Typ** auf **Copy** festgelegt ist.
- Die Eingabe für die Aktivität ist auf **InputDataset** und die Ausgabe für die Aktivität ist auf **OutputDataset** festgelegt. Informationen zum Definieren von Datasets im JSON-Format finden Sie im Artikel [Datasets](concepts-datasets-linked-services.md).
- Im Abschnitt **typeProperties** ist **BlobSource** als Quelltyp und **SqlSink** als Senkentyp angegeben. Klicken Sie im Abschnitt [Datenverschiebungsaktivitäten](#data-movement-activities) auf den Datenspeicher, den Sie als Quelle oder Senke verwenden möchten, um weitere Informationen zum Verschieben von Daten in den/aus dem Datenspeicher zu erhalten.

Eine ausführliche exemplarische Vorgehensweise zum Erstellen dieser Pipeline finden Sie unter [Schnellstart: Erstellen einer Data Factory](quickstart-create-data-factory-powershell.md).

## <a name="sample-transformation-pipeline"></a>Beispiel einer Transformationspipeline
In der folgenden Beispielpipeline gibt es im Abschnitt **HDInsightHive** in the **Aktivitäten** . In diesem Beispiel transformiert die [HDInsight Hive-Aktivität](transform-data-using-hadoop-hive.md) Daten aus Azure Blob Storage durch Anwenden einer Hive-Skriptdatei auf einen Azure HDInsight Hadoop-Cluster.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ]
    }
}
```
Beachten Sie folgende Punkte:

- Der Abschnitt „Activities“ enthält nur eine Aktivität, deren **Typ** auf **HDInsightHive** festgelegt ist.
- Die Hive-Skriptdatei **partitionweblogs.hql** ist im Azure-Speicherkonto (das durch den scriptLinkedService-Dienst namens „AzureStorageLinkedService“ angegeben ist) und im Skriptordner im Container `adfgetstarted` gespeichert.
- Der Abschnitt `defines` wird zum Angeben der Laufzeiteinstellungen verwendet, die als Hive-Konfigurationswerte (z.B. $`{hiveconf:inputtable}`, `${hiveconf:partitionedtable}`) an das Hive-Skript übergeben werden.

Der Abschnitt **typeProperties** ist für jede Transformation unterschiedlich. Um weitere Informationen zu Typeigenschaften zu erhalten, die für eine Transformationsaktivität unterstützt werden, klicken Sie bei den [Datentransformationsaktivitäten](#data-transformation-activities) auf die Transformationsaktivität.

Eine ausführliche exemplarische Vorgehensweise zum Erstellen dieser Pipeline finden Sie im [Tutorial: Transformieren von Daten mit Spark](tutorial-transform-data-spark-powershell.md).

## <a name="multiple-activities-in-a-pipeline"></a>Mehrere Aktivitäten in einer Pipeline
Die vorherigen beiden Beispielpipelines enthalten nur jeweils eine Aktivität. Sie können mehrere Aktivitäten in einer Pipeline verwenden. Bei mehreren Aktivitäten in einer Pipeline, bei denen die nachfolgenden Aktivitäten nicht von den vorherigen abhängig sind, können die Aktivitäten parallel ausgeführt werden.

Sie können zwei Aktivitäten mithilfe der [Aktivitätsabhängigkeit](#activity-dependency) verketten, die definiert, wie nachfolgende Aktivitäten von vorherigen Aktivitäten abhängen. Dabei wird bestimmt, bei welcher Bedingung mit der nächsten Aufgabe fortgefahren wird. Eine Aktivität kann mit unterschiedlichen Abhängigkeitsbedingungen von einer oder mehreren vorherigen Aktivitäten abhängen.

## <a name="scheduling-pipelines"></a>Planen von Pipelines
Pipelines werden von Triggern geplant. Es gibt verschiedene Arten von Triggern (Planer-Trigger, bei denen Pipelines nach einem Zeitplan ausgelöst werden, sowie manuelle Trigger, bei denen Pipelines bei Bedarf ausgelöst werden). Weitere Informationen zu Triggern finden Sie im Artikel [Pipelineausführung und -trigger](concepts-pipeline-execution-triggers.md).

Damit der Trigger die Ausführung der Pipeline startet, müssen Sie in die Triggerdefinition einen Pipelineverweis auf die jeweilige Pipeline einschließen. Zwischen Pipelines und Triggern besteht eine n:m-Beziehung. Mehrere Trigger können eine einzelne Pipeline starten, und ein einzelner Trigger kann mehrere Pipelines starten. Sobald der Trigger definiert wurde, müssen Sie ihn starten, damit er mit dem Auslösen der Pipeline beginnen kann. Weitere Informationen zu Triggern finden Sie im Artikel [Pipelineausführung und -trigger](concepts-pipeline-execution-triggers.md).

Angenommen, Sie verfügen über einen Planertrigger „Trigger A“, der die Pipeline „MyCopyPipeline“ starten soll. Definieren Sie den Trigger wie in folgendem Beispiel gezeigt:

### <a name="trigger-a-definition"></a>Definition Trigger A

```json
{
  "name": "TriggerA",
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      ...
      }
    },
    "pipeline": {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "MyCopyPipeline"
      },
      "parameters": {
        "copySourceName": "FileSource"
      }
    }
  }
}
```



## <a name="next-steps"></a>Nächste Schritte
In den folgenden Tutorials finden Sie schrittweise Anleitungen zum Erstellen von Pipelines mit Aktivitäten:

- [Erstellen einer Pipeline mit einer Kopieraktivität](quickstart-create-data-factory-powershell.md)
- [Tutorial: Erstellen Ihrer ersten Pipeline zur Transformierung von Daten mithilfe eines Hadoop-Clusters](tutorial-transform-data-spark-powershell.md)
