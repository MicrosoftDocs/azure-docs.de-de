---
title: Azure Data Factory – JSON-Skiptreferenz
description: Stellt JSON-Schemas für Data Factory-Entitäten bereit.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 41ba08aef7aed761c3c6063f97768f22bffe3a36
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508491"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Azure Data Factory – JSON-Skiptreferenz
> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory.


Dieser Artikel enthält JSON-Schemas und -Beispiele für das Definieren von Azure Data Factory-Entitäten (Pipeline, Aktivität, Dataset und verknüpfter Dienst).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="pipeline"></a>Pipeline
Die grobe Struktur einer Pipelinedefinition lautet wie folgt:

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

In der folgenden Tabelle werden die Eigenschaften in der Pipeline-JSON-Definition beschrieben:

| Eigenschaft | BESCHREIBUNG | Erforderlich
-------- | ----------- | --------
| name | Name der Pipeline. Geben Sie einen Namen an, der die Aktion darstellt, für deren Durchführung die Aktivität oder die Pipeline konfiguriert ist.<br/><ul><li>Maximale Anzahl von Zeichen: 260</li><li>Muss mit einem Buchstaben, einer Zahl oder einem Unterstrich (\_) beginnen.</li><li>Folgende Zeichen sind nicht zulässig: „.“, „+“, „?“, „/“, „<“, „>“, „*“, „%“, „&“, „:“, „\\“.</li></ul> |Ja |
| description |Beschreibung des Verwendungszwecks der Aktivität oder der Pipeline | Nein |
| activities | Enthält eine Liste mit Aktivitäten. | Ja |
| start |Startdatum/-uhrzeit für die Pipeline. Muss im [ISO-Format](https://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41. <br/><br/>Es ist möglich, eine lokale Zeit anzugeben, z. B. eine EST-Zeit. Beispiel: `2016-02-27T06:00:00**-05:00`. Entspricht 6 Uhr EST.<br/><br/>Die Eigenschaften "start" und "end" geben zusammen den aktiven Zeitraum der Pipeline an. Ausgabeslices werden nur in diesem aktiven Zeitraum erstellt. |Nein<br/><br/>Wenn Sie einen Wert für die Endeigenschaft angeben, müssen Sie auch einen Wert für die Starteigenschaft angeben.<br/><br/>Sowohl die Start- als auch die Endzeiten zum Erstellen einer Pipeline können leer sein. Sie müssen beide Werte angeben, um für die Pipeline einen aktiven Zeitraum für die Ausführung festzulegen. Wenn Sie beim Erstellen einer Pipeline keine Start- und Endzeit angeben, können Sie später zum Festlegen der Werte das Cmdlet „Set-AzDataFactoryPipelineActivePeriod“ verwenden. |
| end |Datum und Uhrzeit für das Ende der Pipeline. Muss, falls gewünscht, im ISO-Format angegeben werden. Beispiel: 2014-10-14T17:32:41 <br/><br/>Es ist möglich, eine lokale Zeit anzugeben, z. B. eine EST-Zeit. Beispiel: `2016-02-27T06:00:00**-05:00`. Entspricht 6 Uhr EST.<br/><br/>Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie „9999-09-09“ als Wert für die Endeigenschaft an. |Nein <br/><br/>Wenn Sie einen Wert für die Starteigenschaft angeben, müssen Sie auch einen Wert für die Endeigenschaft angeben.<br/><br/>Lesen Sie auch die Hinweise zur **Start** -Eigenschaft. |
| isPaused |Bei der Einstellung „true“ wird die Pipeline nicht ausgeführt. Standardwert = false. Sie können diese Eigenschaft zum Aktivieren oder Deaktivieren verwenden. |Nein |
| pipelineMode |Die Methode zum Planen von Ausführungen für die Pipeline. Zulässige Werte sind: „scheduled“ (Standard) und „onetime“.<br/><br/>„scheduled“ bedeutet, dass die Pipeline in einem bestimmten Zeitintervall gemäß ihrem aktiven Zeitraum (Start- und Endzeit) ausgeführt wird. „onetime“ bedeutet, dass die Pipeline nur einmal ausgeführt wird. Pipelines mit einmaliger Ausführung können derzeit nach der Erstellung nicht geändert oder aktualisiert werden. Informationen zur Einstellung der einmaligen Ausführung finden Sie unter [Pipeline mit einmaliger Ausführung](data-factory-create-pipelines.md#onetime-pipeline) . |Nein |
| expirationTime |Zeitraum, für den die Pipeline nach der Erstellung gültig ist und für den die Bereitstellung aufrechterhalten werden sollte. Wenn die Pipeline keine aktiven, fehlerhaften oder ausstehenden Ausführungen enthält, wird sie bei Erreichen der Ablaufzeit automatisch gelöscht. |Nein |


## <a name="activity"></a>Aktivität
Die allgemeine Struktur für eine Aktivität in einer Pipelinedefinition (activities-Element) lautet:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    },
    "scheduler":
    {
    }
}
```

In der folgenden Tabelle werden die Eigenschaften in der JSON-Definition für die Aktivität beschrieben:

| Tag | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| name |Der Name der Aktivität. Geben Sie einen Namen an, der die Aktion darstellt, für deren Durchführung die Aktivität konfiguriert ist.<br/><ul><li>Maximale Anzahl von Zeichen: 260</li><li>Muss mit einem Buchstaben, einer Zahl oder einem Unterstrich (\_) beginnen.</li><li>Folgende Zeichen sind nicht zulässig: „.“, „+“, „?“, „/“, „<“, „>“, „*“, „%“, „&“, „:“, „\\“.</li></ul> |Ja |
| description |Ein Text, der beschreibt, wofür die Aktivität verwendet wird. |Nein |
| type |Gibt den Typ der Aktivität an. Informationen zu verschiedenen Typen von Aktivitäten finden Sie in den Abschnitten [DATENSPEICHER](#data-stores) und [DATENTRANSFORMATIONSAKTIVITÄTEN](#data-transformation-activities). |Ja |
| inputs |Von der Aktivität verwendete Eingabetabellen<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |„Nein“ für HDInsightStreaming- und SqlServerStoredProcedure-Aktivitäten <br/> <br/> „Ja“ für alle übrigen |
| outputs |Von der Aktivität verwendete Ausgabetabellen.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Ja |
| linkedServiceName |Name des verknüpften Diensts, der von der Aktivität verwendet wird. <br/><br/>Für eine Aktivität kann es erforderlich sein, den verknüpften Dienst anzugeben, der mit der erforderlichen Computeumgebung verknüpft ist. |Ja, für HDInsight-Aktivitäten, Aktivitäten in Azure Machine Learning Studio (klassisch) und Aktivitäten vom Typ „Gespeicherte Prozedur“ <br/><br/>„Nein“ für alle übrigen |
| typeProperties |Eigenschaften im Abschnitt „typeProperties“ sind abhängig vom Typ der Aktivität. |Nein |
| policy |Richtlinien, die das Laufzeitverhalten der Aktivität beeinflussen. Falls dies nicht angegeben wird, werden Standardrichtlinien verwendet. |Nein |
| scheduler |Die „scheduler“-Eigenschaft wird verwendet, um die gewünschte Planung für die Aktivität zu definieren. Die untergeordneten Eigenschaften sind identisch mit denen der [availability-Eigenschaft in einem Dataset](data-factory-create-datasets.md#dataset-availability). |Nein |

### <a name="policies"></a>Richtlinien
Richtlinien beeinflussen das Laufzeitverhalten einer Aktivität, besonders dann, wenn der Slice einer Tabelle verarbeitet wird. Die Details finden Sie in der folgenden Tabelle.

| Eigenschaft | Zulässige Werte | Standardwert | BESCHREIBUNG |
| --- | --- | --- | --- |
| concurrency |Integer <br/><br/>Maximalwert: 10 |1 |Anzahl von gleichzeitigen Ausführungen der Aktivität.<br/><br/>Legt die Anzahl der parallelen Ausführungen einer Aktivität fest, die für verschiedene Slices stattfinden können. Wenn eine Aktivität beispielsweise eine große Menge verfügbarer Daten durchlaufen muss, kann die Datenverarbeitung durch einen höheren Parallelitätswert beschleunigt werden. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Bestimmt die Reihenfolge der Datenslices, die verarbeitet werden.<br/><br/>Nehmen Sie beispielsweise an, Sie haben zwei Slices (einen um 16:00 Uhr und einen weiteren um 17:00 Uhr), und beide warten auf ihre Ausführung. Wenn Sie „executionPriorityOrder“ auf „NewestFirst“ setzen, wird der Slice von 17 Uhr zuerst verarbeitet. Wenn Sie „executionPriorityOrder“ auf „OldestFirst“ festlegen, wird der Slice von 16 Uhr verarbeitet. |
| retry |Integer<br/><br/>Höchstwert ist 10. |0 |Anzahl der Wiederholungsversuche, bevor die Datenverarbeitung für den Slice als Fehler markiert wird. Die Ausführung der Aktivitäten für einen Datenslice wird bis zur angegebenen Anzahl der Wiederholungsversuche wiederholt. Die Wiederholung erfolgt so bald wie möglich nach dem Fehler. |
| timeout |TimeSpan |00:00:00 |Timeout für die Aktivität. Beispiel: 00:10:00 (Timeout nach 10 Minuten)<br/><br/>Wenn kein Wert oder 0 angegeben wird, ist das Zeitlimit unendlich.<br/><br/>Wenn die Datenverarbeitungszeit für einen Slice den Timeoutwert überschreitet, wird der Vorgang abgebrochen, und das System versucht, die Verarbeitung zu wiederholen. Die Anzahl der Wiederholungsversuche hängt von der Eigenschaft "retry" ab. Wenn ein Timeout auftritt, lautet der Status „TimedOut“. |
| delay |TimeSpan |00:00:00 |Geben Sie die Verzögerung an, mit der die Datenverarbeitung des Slice beginnt.<br/><br/>Die Ausführung der Aktivität für einen Datenslice wird gestartet, nachdem die Verzögerung die erwartete Ausführungszeit überschreitet.<br/><br/>Beispiel: 00:10:00 (Verzögerung von 10 Minuten) |
| longRetry |Integer<br/><br/>Maximalwert: 10 |1 |Die Anzahl von langen Wiederholungsversuchen, bevor die Sliceausführung einen Fehler verursacht.<br/><br/>longRetry-Versuche werden durch longRetryInterval über einen Zeitraum verteilt. Wenn Sie eine Zeit zwischen den Wiederholungsversuchen angeben müssen, verwenden Sie "longRetry". Wenn sowohl „retry“ als auch „longRetry“ angegeben werden, umfasst jeder „longRetry“-Versuch „retry“-Versuche, und die maximale Anzahl von Versuchen errechnet sich aus „retry \* longRetry“.<br/><br/>Beispiel: Die Richtlinie für die Aktivität enthält folgende Einstellungen:<br/>Wiederholung: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Wir nehmen an, dass nur ein Slice auszuführen ist (Status lautet „Waiting“) und dass die Ausführung der Aktivität jedes Mal einen Fehler verursacht. Zunächst würden drei aufeinander folgende Ausführungsversuche durchgeführt. Nach jedem Versuch wäre der Slicestatus „Retry“. Nachdem die ersten drei Versuche durchgeführt wurden, lautet der Slicestatus „LongRetry“.<br/><br/>Nach einer Stunde (Wert von „longRetryInterval“) würden drei weitere aufeinander folgende Ausführungsversuche unternommen werden. Danach würde der Slicestatus "Failed" lauten, und es fänden keine weiteren Versuche statt. Somit wurden insgesamt sechs Versuche unternommen.<br/><br/>Bei einer erfolgreichen Ausführung lautet der Slicestatus „Ready“, und es werden keine weiteren Versuche durchgeführt.<br/><br/>„longRetry“ kann in Situationen verwendet werden, in denen abhängige Daten zu nicht festgelegten Zeiten eingehen oder die gesamte Umgebung, in der die Datenverarbeitung erfolgt, unzuverlässig ist. In solchen Fällen ist die Durchführung von aufeinander folgenden Wiederholungen möglicherweise nicht hilfreich, und die Wiederholung nach einem bestimmten Zeitraum führt vielleicht zur gewünschten Ausgabe.<br/><br/>Vorsicht: Legen Sie für „longRetry“ und „longRetryInterval“ keine hohen Werte fest. In der Regel weisen höhere Werte auf andere Systemprobleme hin. |
| longRetryInterval |TimeSpan |00:00:00 |Die Verzögerung zwischen langen Wiederholungsversuchen |

### <a name="typeproperties-section"></a>typeProperties-Abschnitt
Der Abschnitt „typeProperties“ ist für jede Aktivität unterschiedlich. Transformationsaktivitäten verfügen nur über die Typeigenschaften. Der Abschnitt [DATENTRANSFORMATIONSAKTIVITÄTEN](#data-transformation-activities) in diesem Artikel enthält JSON-Beispiele, in denen Transformationsaktivitäten in einer Pipeline definiert werden.

Die **Kopieraktivität** enthält im Abschnitt „typeProperties“ zwei Unterabschnitte: **source** und **sink**. Der Abschnitt [DATENSPEICHER](#data-stores) in diesem Artikel enthält JSON-Beispiele, in denen veranschaulicht wird, wie Sie einen Datenspeicher als Quelle bzw. Senke verwenden.

### <a name="sample-copy-pipeline"></a>Beispiel einer Kopierpipeline
In der folgenden Beispielpipeline gibt es im Abschnitt **Copy** in the **Aktivitäten** . In diesem Beispiel werden von der [Kopieraktivität](data-factory-data-movement-activities.md) Daten aus Azure Blob Storage in Azure SQL-Datenbank kopiert.

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
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

Beachten Sie folgende Punkte:

* Der Abschnitt „Activities“ enthält nur eine Aktivität, deren **Typ** auf **Copy** festgelegt ist.
* Die Eingabe für die Aktivität ist auf **InputDataset** und die Ausgabe für die Aktivität ist auf **OutputDataset** festgelegt.
* Im Abschnitt **typeProperties** ist **BlobSource** als Quelltyp und **SqlSink** als Senkentyp angegeben.

Der Abschnitt [DATENSPEICHER](#data-stores) in diesem Artikel enthält JSON-Beispiele, in denen veranschaulicht wird, wie Sie einen Datenspeicher als Quelle bzw. Senke verwenden.

Eine ausführliche exemplarische Vorgehensweise zum Erstellen dieser Pipeline finden Sie im [Tutorial: Kopieren von Daten aus Blob Storage in SQL-Datenbank](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

### <a name="sample-transformation-pipeline"></a>Beispiel einer Transformationspipeline
In der folgenden Beispielpipeline gibt es im Abschnitt **HDInsightHive** in the **Aktivitäten** . In diesem Beispiel transformiert die [HDInsight Hive-Aktivität](data-factory-hive-activity.md) Daten aus Azure Blob Storage durch Anwenden einer Hive-Skriptdatei auf einen Azure HDInsight Hadoop-Cluster.

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
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

Beachten Sie folgende Punkte:

* Der Abschnitt „Activities“ enthält nur eine Aktivität, deren **Typ** auf **HDInsightHive** festgelegt ist.
* Die Hive-Skriptdatei **partitionweblogs.hql** ist im Azure-Speicherkonto (das durch den scriptLinkedService-Dienst namens **AzureStorageLinkedService** angegeben ist) und im Ordner **script** im Container **adfgetstarted** gespeichert.
* Der Abschnitt **defines** wird zum Angeben der Runtimeeinstellungen verwendet, die als Hive-Konfigurationswerte an das Hive-Skript übergeben werden (z.B. `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Der Abschnitt [DATENTRANSFORMATIONSAKTIVITÄTEN](#data-transformation-activities) in diesem Artikel enthält JSON-Beispiele, in denen Transformationsaktivitäten in einer Pipeline definiert werden.

Eine ausführliche exemplarische Vorgehensweise zum Erstellen dieser Pipeline finden Sie im [Tutorial: Erstellen der ersten Pipeline zum Verarbeiten von Daten mithilfe eines Hadoop-Clusters](data-factory-build-your-first-pipeline.md).

## <a name="linked-service"></a>Verknüpfter Dienst
Die grobe Struktur der Definition eines verknüpften Diensts lautet wie folgt:

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

In der folgenden Tabelle werden die Eigenschaften in der JSON-Definition für die Aktivität beschrieben:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| -------- | ----------- | -------- |
| name | Name des verknüpften Diensts. | Ja |
| Eigenschaften – Typ | Typ des verknüpften Diensts. Beispiel: Azure Storage, Azure SQL-Datenbank. |
| typeProperties | Der typeProperties-Abschnitt enthält Elemente, die sich für jeden Datenspeicher bzw. jede Compute-Umgebung unterscheiden. Im Abschnitt „Datenspeicher“ sind alle verknüpften Dienste für Datenspeicher und im Abschnitt [Compute-Umgebungen](#compute-environments) alle verknüpften Dienste für Compute-Umgebungen angegeben. |

## <a name="dataset"></a>Dataset
Ein Dataset in Azure Data Factory wird wie folgt definiert:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
        "policy":
        {
        }
    }
}
```

In der folgenden Tabelle werden die Eigenschaften im obigen JSON-Code beschrieben:

| Eigenschaft | BESCHREIBUNG | Erforderlich | Standard |
| --- | --- | --- | --- |
| name | Name des Datasets. Unter [Azure Data Factory – Benennungsregeln](data-factory-naming-rules.md) finden Sie die Benennungsregeln. |Ja |Nicht verfügbar |
| type | Typ des Datasets. Geben Sie einen der von Azure Data Factory unterstützten Typen an (z.B. AzureBlob, AzureSqlTable). Der Abschnitt [DATENSPEICHER](#data-stores) enthält alle Datenspeicher und Datasettypen, die von Data Factory unterstützt werden. |
| structure | Schema des Datasets. Es enthält Spalten, die dazugehörigen Typen usw. | Nein |Nicht verfügbar |
| typeProperties | Eigenschaften, die dem ausgewählten Typ entsprechen. Der Abschnitt [DATENSPEICHER](#data-stores) enthält Informationen zu den unterstützten Typen und ihren Eigenschaften. |Ja |Nicht verfügbar |
| external | Boolesches Flag, das angibt, ob ein Dataset explizit durch eine Data Factory-Pipeline erstellt wird oder nicht. |Nein |false |
| availability | Definiert das Verarbeitungsfenster oder das Modell für das Aufteilen in Slices für die Datasetproduktion. Weitere Informationen zum Modell für das Aufteilen von Datasets in Slices finden Sie im Artikel [Planung und Ausführung](data-factory-scheduling-and-execution.md) . |Ja |Nicht verfügbar |
| policy |Definiert die Kriterien oder die Bedingung, die die Dataset-Slices erfüllen müssen. <br/><br/>Weitere Informationen finden Sie im Abschnitt „Dataset-Richtlinie“. |Nein |Nicht verfügbar |

Jede Spalte im Abschnitt **structure** enthält die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| name |Name der Spalte. |Ja |
| type |Datentyp der Spalte.  |Nein |
| culture |Zu verwendendes .NET-basiertes Gebietsschema, wenn der Typ angegeben ist, und den .NET-Typ `Datetime` oder `Datetimeoffset` hat. Der Standardwert ist `en-us`. |Nein |
| format |Zu verwendende Formatzeichenfolge, wenn der Typ angegeben ist und den .NET-Typ `Datetime` oder `Datetimeoffset` hat. |Nein |

Im folgenden Beispiel enthält das Dataset die drei Spalten `slicetimestamp`, `projectname` und `pageviews` folgenden Typs: Zeichenfolge, Zeichenfolge bzw. Dezimalzahl.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

In der folgenden Tabelle werden die Eigenschaften beschrieben, die Sie im Abschnitt **availability** verwenden können:

| Eigenschaft | BESCHREIBUNG | Erforderlich | Standard |
| --- | --- | --- | --- |
| frequency |Gibt die Zeiteinheit für die Erstellung der Datasetslices an.<br/><br/><b>Unterstützte Häufigkeit</b>: „Minute“, „Hour“, „Day“, „Week“, „Month“ |Ja |Nicht verfügbar |
| interval |Gibt einen Multiplikator für die Häufigkeit an<br/><br/>„Frequency x interval“ bestimmt, wie oft der Slice erzeugt wird.<br/><br/>Wenn Sie das Dataset auf Stundenbasis in Slices aufteilen möchten, legen Sie <b>Frequency</b> auf <b>Hour</b> und <b>interval</b> auf <b>1</b> fest.<br/><br/><b>Hinweis</b>: Wenn Sie „Frequency“ auf „Minute“ festlegen, sollten Sie „interval“ mindestens auf „15“ festlegen. |Ja |Nicht verfügbar |
| style |Gibt an, ob der Slice am Anfang/Ende des Intervalls erzeugt werden soll.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Wenn „Frequency“ auf „Month“ und „style“ auf „EndOfInterval“ festgelegt ist, wird der Slice am letzten Tag des Monats erstellt. Wenn „style“ auf „StartOfInterval“ festgelegt ist, wird der Slice am ersten Tag des Monats erstellt.<br/><br/>Wenn „Frequency“ auf „Day“ und „style“ auf „EndOfInterval“ festgelegt ist, wird der Slice in der letzten Stunde des Tages erstellt.<br/><br/>Wenn „Frequency“ auf „Hour“ und „style“ auf „EndOfInterval“ festgelegt ist, wird der Slice am Ende der Stunde erstellt. Ein Slice für den Zeitraum 13:00 bis 14:00 Uhr wird z. B. um 14.00 Uhr erstellt. |Nein |EndOfInterval |
| anchorDateTime |Definiert die absolute Position in der Zeit, die der Scheduler benötigt, um Dataset-Slicegrenzen zu berechnen. <br/><br/><b>Hinweis</b>: Wenn AnchorDateTime Datumsteile aufweist, die präziser als die Häufigkeit sind, werden die präziseren Teile ignoriert. <br/><br/>Wenn <b>interval</b> beispielsweise auf <b>hourly</b> festgelegt ist (frequency: hour, interval: 1) und das <b>AnchorDateTime</b>-Element <b>Minuten und Sekunden</b> enthält, wird der Teil mit den <b>Minuten und Sekunden</b> von AnchorDateTime ignoriert. |Nein |01/01/0001 |
| offset |Zeitspanne, um die Anfang und Ende aller Datasetslices verschoben werden. <br/><br/><b>Hinweis</b>: Wenn sowohl „anchorDateTime“ als auch „offset“ angegeben werden, ist das Ergebnis die kombinierte Verschiebung. |Nein |Nicht verfügbar |

Der folgende Abschnitt „availability“ definiert, dass das Ausgabedataset entweder stündlich erstellt wird oder das Eingabedataset stündlich verfügbar ist:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Der Abschnitt **policy** in der Datasetdefinition definiert die Kriterien oder die Bedingung, die die Datasetslices erfüllen müssen.

| Richtlinienname | BESCHREIBUNG | Angewendet auf | Erforderlich | Standard |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Überprüft, ob die Daten in einem **Azure-Blob** die minimalen Größenanforderungen (in MB) erfüllen. |Azure Blob |Nein |Nicht verfügbar |
| minimumRows |Überprüft, ob die Daten in **Azure SQL-Datenbank** oder in einer **Azure-Tabelle** die Mindestanzahl von Zeilen enthalten. |<ul><li>Azure SQL-Datenbank</li><li>Azure Table</li></ul> |Nein |Nicht verfügbar |

**Beispiel:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

Falls von Azure Data Factory kein Dataset erzeugt wird, sollte es als **extern** markiert werden. Diese Einstellung gilt im Allgemeinen für die Eingaben der ersten Aktivität in einer Pipeline, wenn nicht die Aktivität oder Pipeline-Verkettung genutzt wird.

| Name | BESCHREIBUNG | Erforderlich | Standardwert |
| --- | --- | --- | --- |
| dataDelay |Zeit, um die die Prüfung der Verfügbarkeit der externen Daten für den angegebenen Slice verzögert wird. Wenn die Daten beispielsweise stündlich verfügbar sind, kann die Überprüfung, ob die externen Daten verfügbar sind und der entsprechende Slice bereit ist, mithilfe von dataDelay verzögert werden.<br/><br/>Dies gilt nur für die aktuelle Zeit.  Beispiel: Wenn es gerade 13:00 Uhr ist und dieser Wert 10 Minuten beträgt, beginnt die Überprüfung um 13:10 Uhr.<br/><br/>Diese Einstellung wirkt sich nicht auf Slices in der Vergangenheit aus (Slices, für die gilt: Sliceendzeit + dataDelay < jetzt), sie werden ohne Verzögerung verarbeitet.<br/><br/>Zeiträume, die größer als 23:59 Stunden sind, müssen im Format `day.hours:minutes:seconds` angegeben werden. Um beispielsweise 24 Stunden anzugeben, verwenden Sie nicht 24:00:00, sondern stattdessen 1.00:00:00. Wenn Sie 24:00:00 verwenden, wird dies als 24 Tage (24.00:00:00) gewertet. Für 1 Tag und 4 Stunden geben Sie „1:04:00:00“ an. |Nein |0 |
| retryInterval |Die Wartezeit zwischen einem Fehler und dem nächsten Wiederholungsversuch. Wenn ein Versuch nicht erfolgreich ist, wird der nächste Versuch nach dem unter „retryInterval“ festgelegten Zeitraum durchgeführt. <br/><br/>Wenn es gerade 13:00 Uhr ist, beginnt der erste Versuch. Wenn die Ausführung der ersten Überprüfung eine Minute gedauert hat und ein Fehler aufgetreten ist, findet die nächste Wiederholung um 13:00 + 1 Min. (Dauer) + 1 Min. (Wiederholungsintervall) = 13:02 Uhr statt. <br/><br/>Für Slices in der Vergangenheit gibt es keine Verzögerung. Der erneute Versuch erfolgt sofort. |Nein |00:01:00 (1 Minute) |
| retryTimeout |Das Timeout für die einzelnen Wiederholungsversuche.<br/><br/>Wenn diese Eigenschaft auf 10 Minuten festgelegt ist, muss die Überprüfung innerhalb von 10 Minuten abgeschlossen werden. Wenn die Ausführung der Überprüfung länger als 10 Minuten dauert, wird das Timeout für die Wiederholung wirksam.<br/><br/>Wenn für alle Überprüfungsversuche ein Timeout wirksam wird, wird der Slice als TimedOut gekennzeichnet. |Nein |00:10:00 (10 Minuten) |
| maximumRetry |Gibt an, wie oft die Verfügbarkeit der externen Daten überprüft werden soll. Der zulässige Höchstwert ist 10. |Nein |3 |


## <a name="data-stores"></a>DATENSPEICHER
Der Abschnitt [Verknüpfte Dienste](#linked-service) enthielt Beschreibungen für JSON-Elemente, die für alle Typen von verknüpften Diensten gelten. Dieser Abschnitt enthält Details zu JSON-Elementen, die spezifisch für jeden Datenspeicher sind.

Der Abschnitt [Dataset](#dataset) enthielt Beschreibungen für JSON-Elemente, die für alle Typen von Datasets gelten. Dieser Abschnitt enthält Details zu JSON-Elementen, die spezifisch für jeden Datenspeicher sind.

Der Abschnitt [Aktivität](#activity) enthielt Beschreibungen für JSON-Elemente, die für alle Typen von Aktivitäten gelten. Dieser Abschnitt enthält Details zu JSON-Elementen, die spezifisch für jeden Datenspeicher sind, wenn er als Quelle/Senke in einer Kopieraktivität verwendet wird.

Klicken Sie auf den Link für den Speicher, an dem Sie interessiert sind, um die JSON-Schemas für den verknüpften Dienst, das Dataset und die Quelle/Senke für die Kopieraktivität anzuzeigen.

| Category | Datenspeicher
|:--- |:--- |
| **Azure** |[Azure Blob Storage](#azure-blob-storage) |
| &nbsp; |Azure Data Lake Store |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL-Datenbank](#azure-sql-database) |
| &nbsp; |[Azure Synapse Analytics](#azure-synapse-analytics) |
| &nbsp; |[Azure Cognitive Search](#azure-cognitive-search) |
| &nbsp; |[Azure Table Storage](#azure-table-storage) |
| **Datenbanken** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **File** |[Amazon S3](#amazon-s3) |
| &nbsp; |[Dateisystem](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Andere** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |Webtabelle |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>Verknüpfter Dienst
Es gibt zwei Arten von verknüpften Diensten: verknüpfter Azure Storage-Dienst und verknüpfter Azure Storage-SAS-Dienst.

#### <a name="azure-storage-linked-service"></a>Mit Azure-Speicher verknüpfter Dienst
Erstellen Sie einen verknüpften Azure Storage-Dienst, um Ihr Azure Storage-Konto per **Kontoschlüssel** mit einer Data Factory zu verknüpfen. Legen Sie den **Typ** des verknüpften Diensts auf **AzureStorage** fest, um einen verknüpften Azure Storage-Dienst zu definieren. Anschließend können Sie im Abschnitt **typeProperties** die folgenden Eigenschaften angeben:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| connectionString |Geben Sie Informationen, die zur Verbindung mit dem Azure-Speicher erforderlich sind, für die connectionString-Eigenschaft ein. |Ja |

##### <a name="example"></a>Beispiel

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Verknüpfter Azure Storage SAS-Dienst
Sie können einen mit Azure Storage SAS verknüpften Dienst verwenden, um ein Azure-Speicherkonto mithilfe eines Shared Access Signature (SAS) mit einer Azure Data Factory zu verknüpfen. Dies ermöglicht der Data Factory eingeschränkten/zeitgebundenen Zugriff auf alle bzw. bestimmte Ressourcen (Blob/Container) im Speicher. Erstellen Sie einen verknüpften Azure Storage SAS-Dienst, um Ihr Azure Storage-Konto per Shared Access Signature mit einer Data Factory zu verknüpfen. Legen Sie den **Typ** des verknüpften Diensts auf **AzureStorageSas** fest, um einen verknüpften Azure Storage SAS-Dienst zu definieren. Anschließend können Sie im Abschnitt **typeProperties** die folgenden Eigenschaften angeben:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| sasUri |Geben Sie den Shared Access Signature-URI für Azure-Speicher-Ressourcen wie BLOB, Container oder Tabelle an. |Ja |

##### <a name="example"></a>Beispiel

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Weitere Informationen zu diesen verknüpften Diensten finden Sie im Artikel [Azure Blob Storage-Connector](data-factory-azure-blob-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie den **Typ** des Datasets auf **AzureBlob** fest, um ein Azure Blob-Dataset zu definieren. Geben Sie im Abschnitt **typeProperties** anschließend die folgenden Azure Blob-spezifischen Eigenschaften ein:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| folderPath |Der Pfad zum Container und Ordner im Blobspeicher. Beispiel: myblobcontainer\myblobfolder\ |Ja |
| fileName |Der Name des Blobs. fileName ist optional, wobei seine Groß- und Kleinschreibung beachtet werden muss.<br/><br/>Wenn Sie einen Dateinamen angeben, funktioniert die Aktivität (einschließlich Kopieren) für das jeweilige Blob.<br/><br/>Wenn „fileName“ nicht angegeben ist, werden alle Blobs in folderPath für das Eingabedataset kopiert.<br/><br/>Wenn „fileName“ für ein Ausgabedataset nicht angegeben ist, hat der Name der generierten Datei folgendes Format: `Data.<Guid>.txt` (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt). |Nein |
| partitionedBy |"partitionedBy" ist eine optionale Eigenschaft. "partitionedBy" kann genutzt werden, um einen dynamischen Wert für "folderPath" oder "fileName" für Zeitreihendaten anzugeben. Beispiel: "folderPath" kann für jedes stündliche Datenaufkommen parametrisiert werden. |Nein |
| format | Die folgenden Formattypen werden unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type** -Eigenschaft unter „format“ auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format) und [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Wenn Sie **Dateien unverändert zwischen dateibasierten Speichern kopieren** möchten (binäre Kopie), können Sie den Formatabschnitt bei den Definitionen von Eingabe- und Ausgabedatasets überspringen. |Nein |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Folgende Typen werden unterstützt: **GZip**, **Deflate**, **BZip2** und **ZipDeflate**. Folgende Ebenen werden unterstützt: **Optimal** und **Fastest**. Weitere Informationen finden Sie unter [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nein |

#### <a name="example"></a>Beispiel

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
 ```


Weitere Informationen finden Sie im Artikel zum [Azure Blob-Connector](data-factory-azure-blob-connector.md#dataset-properties).

### <a name="blobsource-in-copy-activity"></a>BlobSource in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus einer Azure Blob Storage-Instanz den **Quelltyp** der Kopieraktivität auf **BlobSource** fest, und geben Sie die folgenden Eigenschaften im Abschnitt **source** an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| recursive |Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. |True (Standardwert), False |Nein |

#### <a name="example-blobsource"></a>Beispiel: **BlobSource**
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>BlobSink in der Kopieraktivität
Legen Sie beim Kopieren von Daten in eine Azure Blob Storage-Instanz den **Senkentyp** der Kopieraktivität auf **BlobSink** fest, und geben Sie im Abschnitt **sink** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| copyBehavior |Definiert das Verhalten beim Kopieren, wenn die Quelle "BlobSource" oder "FileSystem" ist. |<b>PreserveHierarchy:</b> behält die Dateihierarchie im Zielordner bei. Der relative Pfad der Quelldatei zum Quellordner entspricht dem relativen Pfad der Zieldatei zum Zielordner.<br/><br/><b>FlattenHierarchy</b>: Alle Dateien aus dem Quellordner befinden sich in der ersten Ebene des Zielordners. Für die Zieldateien wird ein automatisch ein Name erzeugt. <br/><br/><b>MergeFiles</b> (Standardwert): Führt alle Dateien aus dem Quellordner in einer Datei zusammen. Wenn der Datei-/Blob-Name angegeben wurde, entspricht der Name dem angegebenen Namen, andernfalls dem automatisch generierten Dateinamen. |Nein |

#### <a name="example-blobsink"></a>Beispiel: BlobSink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Azure Blob-Connector](data-factory-azure-blob-connector.md#copy-activity-properties).

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften Azure Data Lake Store-Diensts den Typ des verknüpften Diensts auf **AzureDataLakeStore** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **AzureDataLakeStore**. | Ja |
| dataLakeStoreUri | Geben Sie Informationen zum Azure Data Lake-Speicherkonto an. Es wird das folgende Format verwendet: `https://[accountname].azuredatalakestore.net/webhdfs/v1` oder `adl://[accountname].azuredatalakestore.net/`. | Ja |
| subscriptionId | ID des Azure-Abonnements, dem die Data Lake Store-Instanz angehört | Erforderlich für Senke |
| resourceGroupName | Name der Azure-Ressourcengruppe, der die Data Lake Store-Instanz angehört. | Erforderlich für Senke |
| servicePrincipalId | Geben Sie die Client-ID der Anwendung an. | Ja (für Dienstprinzipalauthentifizierung) |
| servicePrincipalKey | Geben Sie den Schlüssel der Anwendung an. | Ja (für Dienstprinzipalauthentifizierung) |
| tenant | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Diese können Sie abrufen, indem Sie den Mauszeiger über den rechten oberen Bereich im Azure-Portal bewegen. | Ja (für Dienstprinzipalauthentifizierung) |
| authorization | Klicken Sie im **Data Factory-Editor** auf die Schaltfläche **Autorisieren**, und geben Sie Ihre Anmeldeinformationen ein, wodurch die automatisch generierte Autorisierungs-URL dieser Eigenschaft zugewiesen wird. | Ja (für Authentifizierung mit Benutzeranmeldeinformationen)|
| sessionID | OAuth-Sitzungs-ID aus der OAuth-Autorisierungssitzung. Jede Sitzungs-ID ist eindeutig und darf nur einmal verwendet werden. Diese Einstellung wird automatisch generiert, wenn Sie den Data Factory-Editor verwenden. | Ja (für Authentifizierung mit Benutzeranmeldeinformationen) |

#### <a name="example-using-service-principal-authentication"></a>Beispiel: Verwenden der Dienstprinzipalauthentifizierung
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>Beispiel: Verwenden der Authentifizierung mit Benutzeranmeldeinformationen
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Azure Data Lake Store-Connector](data-factory-azure-datalake-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines Azure Data Lake Store-Datasets den **Typ** des Datasets auf **AzureDataLakeStore** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| folderPath |Der Pfad zum Container und Ordner im Azure Data Lake-Speicher. |Ja |
| fileName |Der Name der Datei im Azure Data Lake-Speicher. fileName ist optional, wobei seine Groß- und Kleinschreibung beachtet werden muss. <br/><br/>Wenn Sie einen Dateinamen angeben, funktioniert die Aktivität (einschließlich Kopieren) für die jeweilige Datei.<br/><br/>Wenn „fileName“ nicht angegeben ist, werden alle Dateien in „folderPath“ für das Eingabedataset kopiert.<br/><br/>Wenn „fileName“ für ein Ausgabedataset nicht angegeben ist, hat der Name der generierten Datei folgendes Format: `Data.<Guid>.txt` (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt). |Nein |
| partitionedBy |"partitionedBy" ist eine optionale Eigenschaft. "partitionedBy" kann genutzt werden, um einen dynamischen Wert für "folderPath" oder "fileName" für Zeitreihendaten anzugeben. Beispiel: "folderPath" kann für jedes stündliche Datenaufkommen parametrisiert werden. |Nein |
| format | Die folgenden Formattypen werden unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type** -Eigenschaft unter „format“ auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format) und [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Wenn Sie **Dateien unverändert zwischen dateibasierten Speichern kopieren** möchten (binäre Kopie), können Sie den Formatabschnitt bei den Definitionen von Eingabe- und Ausgabedatasets überspringen. |Nein |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Folgende Typen werden unterstützt: **GZip**, **Deflate**, **BZip2** und **ZipDeflate**. Folgende Ebenen werden unterstützt: **Optimal** und **Fastest**. Weitere Informationen finden Sie unter [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nein |

#### <a name="example"></a>Beispiel
```json
{
    "name": "AzureDataLakeStoreInput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Azure Data Lake Store-Connector](data-factory-azure-datalake-connector.md#dataset-properties).

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Azure Data Lake Store-Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus einer Azure Data Lake Store-Instanz den **Quelltyp** der Kopieraktivität auf **AzureDataLakeStoreSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:

**AzureDataLakeStoreSource** unterstützt die folgenden Eigenschaften im Abschnitt **typeProperties**:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| recursive |Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. |True (Standardwert), False |Nein |

#### <a name="example-azuredatalakestoresource"></a>Beispiel: AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Azure Data Lake Store-Connector](data-factory-azure-datalake-connector.md#copy-activity-properties).

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Azure Data Lake Store-Senke in der Kopieraktivität
Legen Sie beim Kopieren von Daten in eine Azure Data Lake Store-Instanz den **Senkentyp** der Kopieraktivität auf **AzureDataLakeStoreSink** fest, und geben Sie im Abschnitt **sink** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| copyBehavior |Gibt das Kopierverhalten an. |<b>PreserveHierarchy:</b> behält die Dateihierarchie im Zielordner bei. Der relative Pfad der Quelldatei zum Quellordner entspricht dem relativen Pfad der Zieldatei zum Zielordner.<br/><br/><b>FlattenHierarchy:</b> Alle Dateien aus dem Quellordner werden auf der ersten Ebene des Zielordners erstellt. Die Namen der Zieldateien werden automatisch generiert.<br/><br/><b>MergeFiles:</b> führt alle Dateien aus dem Quellordner in einer Datei zusammen. Wenn der Datei-/Blob-Name angegeben wurde, entspricht der Name dem angegebenen Namen, andernfalls dem automatisch generierten Dateinamen. |Nein |

#### <a name="example-azuredatalakestoresink"></a>Beispiel: AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Azure Data Lake Store-Connector](data-factory-azure-datalake-connector.md#copy-activity-properties).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften Azure Cosmos DB-Diensts den **Typ** des verknüpften Diensts auf **DocumentDb** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| **Eigenschaft** | **Beschreibung** | **Erforderlich** |
| --- | --- | --- |
| connectionString |Geben Sie die zum Verbinden mit der Azure Cosmos DB-Datenbank erforderlichen Informationen an. |Ja |

#### <a name="example"></a>Beispiel

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
Weitere Informationen finden Sie im Artikel zum [Azure Cosmos DB-Connector](data-factory-azure-documentdb-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines Azure Cosmos DB-Datasets den **Typ** des Datasets auf **DocumentDbCollection** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| **Eigenschaft** | **Beschreibung** | **Erforderlich** |
| --- | --- | --- |
| collectionName |Der Name der Azure Cosmos DB-Sammlung. |Ja |

#### <a name="example"></a>Beispiel

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
Weitere Informationen finden Sie im Artikel zum [Azure Cosmos DB-Connector](data-factory-azure-documentdb-connector.md#dataset-properties).

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Quelle der Azure Cosmos DB-Sammlung in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus einer Azure Cosmos DB-Instanz den **Quelltyp** der Kopieraktivität auf **DocumentDbCollectionSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:


| **Eigenschaft** | **Beschreibung** | **Zulässige Werte** | **Erforderlich** |
| --- | --- | --- | --- |
| Abfrage |Geben Sie die Abfrage an, um Daten zu lesen. |Von Azure Cosmos DB unterstützte Abfragezeichenfolge. <br/><br/>Beispiel: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nein <br/><br/>Falls nicht angegeben, wird folgende SQL-Anweisung ausgeführt: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Sonderzeichen, um anzugeben, dass das Dokument geschachtelt ist. |Beliebiges Zeichen. <br/><br/>Azure Cosmos DB ist ein NoSQL-Speicher für JSON-Dokumente, in denen geschachtelte Strukturen zulässig sind. Azure Data Factory ermöglicht es dem Benutzer, über einen „nestingSeparator“ eine Hierarchie anzugeben. In den obigen Beispielen ist dies „.“. Mit dem Trennzeichen generiert die Kopieraktivität das Objekt "Name" mit den drei untergeordneten Elementen "First", "Middle" und "Last" gemäß "Name.First", "Name.Middle" und "Name.Last" in der Tabellendefinition. |Nein |

#### <a name="example"></a>Beispiel

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Senke der Azure Cosmos DB-Sammlung in der Kopieraktivität
Legen Sie beim Kopieren von Daten in eine Azure Cosmos DB-Instanz den **Senkentyp** der Kopieraktivität auf **DocumentDbCollectionSink** fest, und geben Sie im Abschnitt **sink** die folgenden Eigenschaften an:

| **Eigenschaft** | **Beschreibung** | **Zulässige Werte** | **Erforderlich** |
| --- | --- | --- | --- |
| nestingSeparator |Ein Sonderzeichen im Quellspaltennamen, um anzuzeigen, dass das geschachtelte Dokument erforderlich ist. <br/><br/>Für das obige Beispiel gilt Folgendes: `Name.First` in der Ausgabetabelle erzeugt im Cosmos DB-Dokument die folgende JSON-Struktur:<br/><br/>"Name": {<br/>    "First": „John“<br/>}, |Zeichen, das zur Trennung der Schachtelungsebenen verwendet wird.<br/><br/>Standardwert ist `.` (Punkt). |Zeichen, das zur Trennung der Schachtelungsebenen verwendet wird. <br/><br/>Standardwert ist `.` (Punkt). |
| writeBatchSize |Gibt die Anzahl von parallelen Anforderungen an den Azure Cosmos DB-Dienst zum Erstellen von Dokumenten an.<br/><br/>Sie können die Leistung beim Kopieren von Daten nach bzw. aus Azure Cosmos DB mit dieser Eigenschaft optimieren. Sie können eine bessere Leistung erzielen, wenn Sie „writeBatchSize“ heraufsetzen, da mehr parallele Anforderungen an Azure Cosmos DB gesendet werden. Sie sollten aber eine Drosselung vermeiden, da dies zur Ausgabe einer Fehlermeldung führen kann: „Anforderungsrate ist hoch.“<br/><br/>Die Drosselung hängt von einer Reihe von Faktoren ab, einschließlich Größe der Dokumente, Anzahl von Begriffen in Dokumenten, Indizierung der Richtlinie der Zielsammlung usw. Für Kopiervorgänge können Sie eine bessere Sammlung (z.B. S3) verwenden, um den optimalen verfügbaren Durchsatz zu erzielen (2.500 Anforderungseinheiten/Sekunde). |Integer |Nein (Standardwert: 5) |
| writeBatchTimeout |Die Wartezeit für den Abschluss des Vorgangs. |Zeitraum<br/><br/> Beispiel: „00:30:00“ (30 Minuten). |Nein |

#### <a name="example"></a>Beispiel

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Azure Cosmos DB-Connector](data-factory-azure-documentdb-connector.md#copy-activity-properties).

## <a name="azure-sql-database"></a>Azure SQL-Datenbank

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften Azure SQL-Datenbank-Diensts den **Typ** des verknüpften Diensts auf **AzureSqlDatabase** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| connectionString |Geben Sie Informationen, die zur Verbindung mit der Azure SQL-Datenbankinstanz erforderlich sind, für die Eigenschaft „connectionString“ ein. |Ja |

#### <a name="example"></a>Beispiel
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Azure SQL-Connector](data-factory-azure-sql-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines Azure SQL-Datenbank-Datasets den **Typ** des Datasets auf **AzureSqlTable** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| tableName |Name der Tabelle oder Sicht in der Azure SQL-Datenbankinstanz, auf die der verknüpfte Dienst verweist. |Ja |

#### <a name="example"></a>Beispiel

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Weitere Informationen finden Sie im Artikel zum [Azure SQL-Connector](data-factory-azure-sql-connector.md#dataset-properties).

### <a name="sql-source-in-copy-activity"></a>SQL-Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus einer Azure SQL-Datenbank den **Quelltyp** der Kopieraktivität auf **SqlSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:


| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| sqlReaderQuery |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-Abfragezeichenfolge. Beispiel: `select * from MyTable`. |Nein |
| sqlReaderStoredProcedureName |Der Name der gespeicherten Prozedur, die Daten aus der Quelltabelle liest. |Name der gespeicherten Prozedur. |Nein |
| storedProcedureParameters |Parameter für die gespeicherte Prozedur. |Name-Wert-Paare. Die Namen und die Groß-/Kleinschreibung von Parametern müssen denen der Parameter der gespeicherten Prozedur entsprechen. |Nein |

#### <a name="example"></a>Beispiel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Weitere Informationen finden Sie im Artikel zum [Azure SQL-Connector](data-factory-azure-sql-connector.md#copy-activity-properties).

### <a name="sql-sink-in-copy-activity"></a>SQL-Senke in der Kopieraktivität
Legen Sie beim Kopieren von Daten in eine Azure SQL-Datenbank den **Senkentyp** der Kopieraktivität auf **SqlSink** fest, und geben Sie im Abschnitt **sink** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| writeBatchTimeout |Die Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird. |Zeitraum<br/><br/> Beispiel: „00:30:00“ (30 Minuten). |Nein |
| writeBatchSize |Fügt Daten in die SQL-Tabelle ein, wenn die Puffergröße "writeBatchSize" erreicht. |Integer (Gesamtanzahl von Zeilen) |Nein (Standardwert: 10.000) |
| sqlWriterCleanupScript |Geben Sie eine Abfrage für die Kopieraktivität an, bei deren Ausführung die Daten eines bestimmten Slice bereinigt werden. |Eine Abfrageanweisung. |Nein |
| sliceIdentifierColumnName |Geben Sie einen Spaltennamen an, den die Kopieraktivität mit einem automatisch generierten Slicebezeichner füllen soll, der bei erneuter Ausführung zum Bereinigen der Daten eines bestimmten Slice verwendet wird. |Spaltenname einer Spalte mit binärem Datentyp (32). |Nein |
| sqlWriterStoredProcedureName |Name der gespeicherten Prozedur, die Daten in die Zieltabelle mit dem Upsert-Vorgang einfügt oder aktualisiert. |Name der gespeicherten Prozedur. |Nein |
| storedProcedureParameters |Parameter für die gespeicherte Prozedur. |Name-Wert-Paare. Die Namen und die Groß-/Kleinschreibung von Parametern müssen denen der Parameter der gespeicherten Prozedur entsprechen. |Nein |
| sqlWriterTableType |Geben Sie einen Tabellentypnamen an, der in der gespeicherten Prozedur verwendet werden soll. Die Kopieraktivität macht die verschobenen Daten in einer temporären Tabelle mit diesem Tabellentyp verfügbar. Der gespeicherte Prozedurcode kann dann die kopierten Daten mit vorhandenen Daten zusammenführen. |Ein Tabellentypname. |Nein |

#### <a name="example"></a>Beispiel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Azure SQL-Connector](data-factory-azure-sql-connector.md#copy-activity-properties).

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften Azure Synapse Analytics-Diensts den **Typ** des verknüpften Diensts auf **AzureSqlDW** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| connectionString |Geben Sie Informationen, die zum Herstellen einer Verbindung mit der Azure Synapse Analytics-Instanz erforderlich sind, für die Eigenschaft „connectionString“ an. |Ja |



#### <a name="example"></a>Beispiel

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel [Azure Synapse Analytics-Connector](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines Azure Synapse Analytics-Datasets den **Typ** des Datasets auf **AzureSqlDWTable** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| tableName |Der Name der Tabelle oder Sicht in der Azure Synapse Analytics-Datenbank, auf die der verknüpfte Dienst verweist. |Ja |

#### <a name="example"></a>Beispiel

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Weitere Informationen finden Sie im Artikel [Azure Synapse Analytics-Connector](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties).

### <a name="azure-synapse-analytics-source-in-copy-activity"></a>Azure Synapse Analytics-Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus Azure Synapse Analytics den **Quelltyp** der Kopieraktivität auf **SqlDWSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:


| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| sqlReaderQuery |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-Abfragezeichenfolge. Beispiel: `select * from MyTable`. |Nein |
| sqlReaderStoredProcedureName |Der Name der gespeicherten Prozedur, die Daten aus der Quelltabelle liest. |Name der gespeicherten Prozedur. |Nein |
| storedProcedureParameters |Parameter für die gespeicherte Prozedur. |Name-Wert-Paare. Die Namen und die Groß-/Kleinschreibung von Parametern müssen denen der Parameter der gespeicherten Prozedur entsprechen. |Nein |

#### <a name="example"></a>Beispiel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Weitere Informationen finden Sie im Artikel [Azure Synapse Analytics-Connector](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties).

### <a name="azure-synapse-analytics-sink-in-copy-activity"></a>Azure Synapse Analytics-Senke in der Kopieraktivität
Legen Sie beim Kopieren von Daten in Azure Synapse Analytics den **Senkentyp** der Kopieraktivität auf **SqlDWSink** fest, und geben Sie im Abschnitt **sink** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Geben Sie eine Abfrage für die Kopieraktivität an, bei deren Ausführung die Daten eines bestimmten Slice bereinigt werden. |Eine Abfrageanweisung. |Nein |
| allowPolyBase |Gibt an, ob (falls zutreffend) PolyBase anstelle des BULKINSERT-Mechanismus verwendet werden soll. <br/><br/> **Zum Laden von Daten in Azure Synapse Analytics wird die Verwendung von PolyBase empfohlen.** |True <br/>False (Standardwert) |Nein |
| polyBaseSettings |Eine Gruppe von Eigenschaften, die angegeben werden können, wenn die **allowPolybase**-Eigenschaft auf **true** festgelegt ist. |&nbsp; |Nein |
| rejectValue |Gibt die Anzahl oder den Prozentsatz von Zeilen an, die abgelehnt werden können, bevor für die Abfrage ein Fehler auftritt. <br/><br/>Weitere Informationen zu den PolyBase-Ablehnungsoptionen finden Sie im Abschnitt **Argumente** des Themas [CREATE EXTERNAL TABLE (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql) . |0 (Standardwert), 1, 2, … |Nein |
| rejectType |Gibt an, ob die rejectValue-Option als Literalwert oder Prozentsatz angegeben ist. |Value (Standardwert), Percentage |Nein |
| rejectSampleValue |Gibt die Anzahl von Zeilen an, die abgerufen werden, bevor PolyBase den Prozentsatz der abgelehnten Zeilen neu berechnet. |1, 2, … |Ja, wenn für **rejectType** der Wert **percentage** festgelegt ist. |
| useTypeDefault |Gibt an, wie fehlende Werte in durch Trennzeichen getrennten Textdateien behandelt werden sollen, wenn PolyBase Daten aus der Textdatei abruft.<br/><br/>Weitere Informationen zu dieser Eigenschaft finden Sie im Abschnitt zu Argumenten im Thema [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql)verwenden können. |True/False (Standardwert) |Nein |
| writeBatchSize |Fügt Daten in die SQL-Tabelle ein, wenn die Puffergröße "writeBatchSize" erreicht. |Integer (Gesamtanzahl von Zeilen) |Nein (Standardwert: 10.000) |
| writeBatchTimeout |Die Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird. |Zeitraum<br/><br/> Beispiel: „00:30:00“ (30 Minuten). |Nein |

#### <a name="example"></a>Beispiel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Weitere Informationen finden Sie im Artikel [Azure Synapse Analytics-Connector](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties).

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften Azure Cognitive Search-Diensts den **Typ** des verknüpften Diensts auf **AzureSearch** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| -------- | ----------- | -------- |
| url | URL für den Suchdienst | Ja |
| Schlüssel | Administratorschlüssel für den Suchdienst | Ja |

#### <a name="example"></a>Beispiel

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Azure Cognitive Search-Connector](data-factory-azure-search-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines Azure Cognitive Search-Datasets den **Typ** des Datasets auf **AzureSearchIndex** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| -------- | ----------- | -------- |
| type | Die Typeigenschaft muss auf **AzureSearchIndex** eingestellt sein.| Ja |
| indexName | Name des Suchindex. Data Factory erstellt den Index nicht. Der Index muss in Azure Cognitive Search vorhanden sein. | Ja |

#### <a name="example"></a>Beispiel

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Azure Cognitive Search-Connector](data-factory-azure-search-connector.md#dataset-properties).

### <a name="azure-cognitive-search-index-sink-in-copy-activity"></a>Azure Cognitive Search-Indexsenke in der Kopieraktivität
Legen Sie beim Kopieren von Daten in einen Suchindex den **Senkentyp** der Kopieraktivität auf **AzureSearchIndexSink** fest, und geben Sie im Abschnitt **sink** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Gibt an, ob ein Dokument zusammengeführt oder ersetzt werden soll, wenn es bereits im Index vorhanden ist. | Zusammenführen (Standard)<br/>Upload| Nein |
| WriteBatchSize | Lädt Daten in den Suchindex hoch, wenn die Puffergröße writeBatchSize erreicht. | 1 bis 1.000. Der Standardwert ist 1000. | Nein |

#### <a name="example"></a>Beispiel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "AzureSearchIndexSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Azure Cognitive Search-Connector](data-factory-azure-search-connector.md#copy-activity-properties).

## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="linked-service"></a>Verknüpfter Dienst
Es gibt zwei Arten von verknüpften Diensten: verknüpfter Azure Storage-Dienst und verknüpfter Azure Storage-SAS-Dienst.

#### <a name="azure-storage-linked-service"></a>Mit Azure-Speicher verknüpfter Dienst
Erstellen Sie einen verknüpften Azure Storage-Dienst, um Ihr Azure Storage-Konto per **Kontoschlüssel** mit einer Data Factory zu verknüpfen. Legen Sie den **Typ** des verknüpften Diensts auf **AzureStorage** fest, um einen verknüpften Azure Storage-Dienst zu definieren. Anschließend können Sie im Abschnitt **typeProperties** die folgenden Eigenschaften angeben:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type |Die type-Eigenschaft muss auf Folgendes festgelegt werden: **AzureStorage** |Ja |
| connectionString |Geben Sie Informationen, die zur Verbindung mit dem Azure-Speicher erforderlich sind, für die connectionString-Eigenschaft ein. |Ja |

**Beispiel:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Verknüpfter Azure Storage SAS-Dienst
Sie können einen mit Azure Storage SAS verknüpften Dienst verwenden, um ein Azure-Speicherkonto mithilfe eines Shared Access Signature (SAS) mit einer Azure Data Factory zu verknüpfen. Dies ermöglicht der Data Factory eingeschränkten/zeitgebundenen Zugriff auf alle bzw. bestimmte Ressourcen (Blob/Container) im Speicher. Erstellen Sie einen verknüpften Azure Storage SAS-Dienst, um Ihr Azure Storage-Konto per Shared Access Signature mit einer Data Factory zu verknüpfen. Legen Sie den **Typ** des verknüpften Diensts auf **AzureStorageSas** fest, um einen verknüpften Azure Storage SAS-Dienst zu definieren. Anschließend können Sie im Abschnitt **typeProperties** die folgenden Eigenschaften angeben:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type |Die type-Eigenschaft muss auf Folgendes festgelegt werden: **AzureStorageSas**. |Ja |
| sasUri |Geben Sie den Shared Access Signature-URI für Azure-Speicher-Ressourcen wie BLOB, Container oder Tabelle an. |Ja |

**Beispiel:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Weitere Informationen zu diesen verknüpften Diensten finden Sie im Artikel zum [Azure Table Storage-Connector](data-factory-azure-table-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines Azure Table-Datasets den **Typ** des Datasets auf **AzureTable** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| tableName |Name der Tabelle in der Azure-Tabellendatenbankinstanz, auf die der verknüpfte Dienst verweist. |Ja. Wenn ein Tabellenname ohne „AzureTableSourceQuery“ angegeben wird, werden alle Datensätze aus der Tabelle an das Ziel kopiert. Bei Angabe von „AzureTableSourceQuery“ werden nur Datensätze, die der Abfrage entsprechen, aus der Tabelle an das Ziel kopiert. |

#### <a name="example"></a>Beispiel

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Weitere Informationen zu diesen verknüpften Diensten finden Sie im Artikel zum [Azure Table Storage-Connector](data-factory-azure-table-connector.md#dataset-properties).

### <a name="azure-table-source-in-copy-activity"></a>Azure Table-Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus einer Azure Table Storage-Instanz den **Quelltyp** der Kopieraktivität auf **AzureTableSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| azureTableSourceQuery |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |Abfragezeichenfolge für Azure-Tabelle. Siehe Beispiele im nächsten Abschnitt. |Nein. Wenn ein Tabellenname ohne „AzureTableSourceQuery“ angegeben wird, werden alle Datensätze aus der Tabelle an das Ziel kopiert. Bei Angabe von „AzureTableSourceQuery“ werden nur Datensätze, die der Abfrage entsprechen, aus der Tabelle an das Ziel kopiert. |
| azureTableSourceIgnoreTableNotFound |Gibt an, ob der Ausnahmefall, dass die Tabelle nicht vorhanden ist, ignoriert werden soll. |TRUE<br/>FALSE |Nein |

#### <a name="example"></a>Beispiel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Weitere Informationen zu diesen verknüpften Diensten finden Sie im Artikel zum [Azure Table Storage-Connector](data-factory-azure-table-connector.md#copy-activity-properties).

### <a name="azure-table-sink-in-copy-activity"></a>Azure Table-Senke in der Kopieraktivität
Legen Sie beim Kopieren von Daten in eine Azure Table Storage-Instanz den **Senkentyp** der Kopieraktivität auf **AzureTableSink** fest, und geben Sie im Abschnitt **sink** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Standardmäßiger Partitionsschlüsselwert, der von der Senke verwendet werden kann. |Ein Zeichenfolgenwert. |Nein |
| azureTablePartitionKeyName |Geben Sie den Namen der Spalte an, deren Werte als Partitionsschlüssel verwendet werden. Wenn dieser nicht angegeben ist, wird "AzureTableDefaultPartitionKeyValue" als Partitionsschlüssel verwendet. |Ein Spaltenname. |Nein |
| azureTableRowKeyName |Geben Sie den Namen der Spalte an, deren Werte als Zeilenschlüssel verwendet werden. Wenn nicht angegeben, verwenden Sie für jede Zeile eine GUID. |Ein Spaltenname. |Nein |
| azureTableInsertType |Der Modus zum Einfügen von Daten in eine Azure-Tabelle.<br/><br/>Diese Eigenschaft steuert, ob die Werte von vorhandenen Zeilen in der Ausgabetabelle, deren Partitions- und Zeilenschlüssel übereinstimmen, ersetzt oder zusammengeführt werden. <br/><br/>Informationen zur Funktionsweise dieser Einstellungen (Zusammenführen und Ersetzen) finden Sie in den Themen [Insert or Merge Entity](/rest/api/storageservices/Insert-Or-Merge-Entity) (Entität einfügen oder zusammenführen) und [Insert or Replace Entity](/rest/api/storageservices/Insert-Or-Replace-Entity) (Entität einfügen oder ersetzen). <br/><br> Diese Einstellung gilt auf Zeilenebene, nicht auf Tabellenebene, und keine der beiden Optionen löscht Zeilen in der Ausgabetabelle, die in der Eingabe nicht vorhanden sind. |merge (default)<br/>replace |Nein |
| writeBatchSize |Fügt Daten in die Azure-Tabelle ein, wenn "writeBatchSize" oder "writeBatchTimeout" erreicht wird. |Integer (Gesamtanzahl von Zeilen) |Nein (Standardwert: 10.000) |
| writeBatchTimeout |Fügt Daten in die Azure-Tabelle ein, wenn "writeBatchSize" oder "writeBatchTimeout" erreicht wird. |Zeitraum<br/><br/>Beispiel: „00:20:00“ (20 Minuten) |Nein (Standardmäßiger Timeoutwert von 90 Sekunden für Speicherclient) |

#### <a name="example"></a>Beispiel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Weitere Informationen zu diesen verknüpften Diensten finden Sie im Artikel zum [Azure Table Storage-Connector](data-factory-azure-table-connector.md#copy-activity-properties).

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften Amazon Redshift-Diensts den **Typ** des verknüpften Diensts auf **AmazonRedshift** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| server |IP-Adresse oder Hostname des Amazon Redshift-Servers. |Ja |
| port |Die Nummer des TCP-Ports, den der Amazon Redshift-Server verwendet, um auf Clientverbindungen zu lauschen. |Nein, Standardwert: 5439 |
| database |Der Name der Amazon Redshift-Datenbank. |Ja |
| username |Der Name des Benutzers, der Zugriff auf die Datenbank hat. |Ja |
| password |Kennwort für das Benutzerkonto. |Ja |

#### <a name="example"></a>Beispiel

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel über den Amazon Redshift-Connector.

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines Amazon Redshift-Datasets den **Typ** des Datasets auf **RelationalTable** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| tableName |Name der Tabelle in der Amazon Redshift-Datenbank, auf die der verknüpfte Dienst verweist. |Nein (wenn **query** von **RelationalSource** angegeben ist) |


#### <a name="example"></a>Beispiel

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Weitere Informationen finden Sie im Artikel über den Amazon Redshift-Connector.

### <a name="relational-source-in-copy-activity"></a>Relationale Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus Amazon Redshift den **Quelltyp** der Kopieraktivität auf **RelationalSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| Abfrage |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-Abfragezeichenfolge. Beispiel: `select * from MyTable`. |Nein (wenn **tableName** von **Dataset** angegeben ist) |

#### <a name="example"></a>Beispiel

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Weitere Informationen finden Sie im Artikel über den Amazon Redshift-Connector.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften IBM DB2-Diensts den **Typ** des verknüpften Diensts auf **OnPremisesDB2** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| server |Name des DB2-Servers. |Ja |
| database |Name der DB2-Datenbank. |Ja |
| schema |Name des Schemas in der Datenbank. Beim Schemanamen wird die Groß- und Kleinschreibung beachtet. |Nein |
| authenticationType |Typ der Authentifizierung für die Verbindung mit der DB2-Datenbank. Mögliche Werte: „Anonymous“, „Basic“ und „Windows“. |Ja |
| username |Geben Sie den Benutzernamen an, wenn Sie die Standard- oder Windows-Authentifizierung verwenden. |Nein |
| password |Geben Sie das Kennwort für das Benutzerkonto an, das Sie für den Benutzernamen angegeben haben. |Nein |
| gatewayName |Name des Gateways, das der Data Factory-Dienst zum Herstellen einer Verbindung mit der lokalen DB2-Datenbank verwenden soll. |Ja |

#### <a name="example"></a>Beispiel
```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Weitere Informationen finden Sie im Artikel über den IBM DB2-Connector.

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines DB2-Datasets den **Typ** des Datasets auf **RelationalTable** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| tableName |Name der Tabelle in der DB2-Datenbankinstanz, auf die der verknüpfte Dienst verweist. Beim Tabellennamen wird die Groß- und Kleinschreibung beachtet. |Nein (wenn **query** von **RelationalSource** angegeben ist)

#### <a name="example"></a>Beispiel
```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Weitere Informationen finden Sie im Artikel über den IBM DB2-Connector.

### <a name="relational-source-in-copy-activity"></a>Relationale Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus IBM DB2 den **Quelltyp** der Kopieraktivität auf **RelationalSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:


| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| Abfrage |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-Abfragezeichenfolge. Beispiel: `"query": "select * from "MySchema"."MyTable""`. |Nein (wenn **tableName** von **Dataset** angegeben ist) |

#### <a name="example"></a>Beispiel
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"Orders\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Weitere Informationen finden Sie im Artikel über den IBM DB2-Connector.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften MySQL-Diensts den **Typ** des verknüpften Diensts auf **OnPremisesMySql** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| server |Name des MySQL-Servers. |Ja |
| database |Name der MySQL-Datenbank. |Ja |
| schema |Name des Schemas in der Datenbank. |Nein |
| authenticationType |Typ der Authentifizierung für die Verbindung mit der MySQL-Datenbank. Mögliche Werte: `Basic`. |Ja |
| userName |Geben Sie einen Benutzernamen für das Herstellen der Verbindung mit der MySQL-Datenbank an. |Ja |
| password |Geben Sie das Kennwort für das angegebene Benutzerkonto an. |Ja |
| gatewayName |Name des Gateways, das der Data Factory-Dienst zum Herstellen einer Verbindung mit der lokalen MySQL-Datenbank verwenden soll. |Ja |

#### <a name="example"></a>Beispiel

```json
{
    "name": "OnPremMySqlLinkedService",
    "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
            "server": "<server name>",
            "database": "<database name>",
            "schema": "<schema name>",
            "authenticationType": "<authentication type>",
            "userName": "<user name>",
            "password": "<password>",
            "gatewayName": "<gateway>"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [MySQL-Connector](data-factory-onprem-mysql-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines MySQL-Datasets den **Typ** des Datasets auf **RelationalTable** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| tableName |Name der Tabelle in der MySQL-Datenbankinstanz, auf die der verknüpfte Dienst verweist. |Nein (wenn **query** von **RelationalSource** angegeben ist) |

#### <a name="example"></a>Beispiel

```json
{
    "name": "MySqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremMySqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Weitere Informationen finden Sie im Artikel zum [MySQL-Connector](data-factory-onprem-mysql-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Relationale Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus einer MySQL-Datenbank den **Quelltyp** der Kopieraktivität auf **RelationalSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:


| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| Abfrage |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-Abfragezeichenfolge. Beispiel: `select * from MyTable`. |Nein (wenn **tableName** von **Dataset** angegeben ist) |


#### <a name="example"></a>Beispiel
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Weitere Informationen finden Sie im Artikel zum [MySQL-Connector](data-factory-onprem-mysql-connector.md#copy-activity-properties).

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften Oracle-Diensts den **Typ** des verknüpften Diensts auf **OnPremisesOracle** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| driverType | Legen Sie fest, welcher Treiber für das Kopieren von Daten aus/in Oracle Database verwendet wird. Zulässige Werte sind **Microsoft** oder **ODP** (Standard). Details zu den Treibern finden Sie unter „Unterstützte Version und Installation“. | Nein |
| connectionString | Geben Sie Informationen, die zur Verbindung mit der Oracle Databaseinstanz erforderlich sind, für die Eigenschaft "connectionString" an. | Ja |
| gatewayName | Name des Gateways, das zum Herstellen einer Verbindung mit dem lokalen Oracle-Server verwendet wird |Ja |

#### <a name="example"></a>Beispiel
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Oracle-Connector](data-factory-onprem-oracle-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines Oracle-Datasets den **Typ** des Datasets auf **OracleTable** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| tableName |Name der Tabelle in der Oracle Database, auf die der verknüpfte Dienst verweist. |Nein (wenn **oracleReaderQuery** von **OracleSource** angegeben ist) |

#### <a name="example"></a>Beispiel

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2016-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Weitere Informationen finden Sie im Artikel zum [Oracle-Connector](data-factory-onprem-oracle-connector.md#dataset-properties).

### <a name="oracle-source-in-copy-activity"></a>Oracle-Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus einer Oracle-Datenbank den **Quelltyp** der Kopieraktivität auf **OracleSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| oracleReaderQuery |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-Abfragezeichenfolge. Beispiel: `select * from MyTable` <br/><br/>Falls nicht angegeben, wird folgende SQL-Anweisung ausgeführt: `select * from MyTable` |Nein (wenn **tableName** von **Dataset** angegeben ist) |

#### <a name="example"></a>Beispiel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "OracleSource",
                    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Oracle-Connector](data-factory-onprem-oracle-connector.md#copy-activity-properties).

### <a name="oracle-sink-in-copy-activity"></a>Oracle-Senke in der Kopieraktivität
Legen Sie beim Kopieren von Daten in eine Oracle-Datenbank den **Senkentyp** der Kopieraktivität auf **OracleSink** fest, und geben Sie im Abschnitt **sink** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| writeBatchTimeout |Die Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird. |Zeitraum<br/><br/> Beispiel: 00:30:00 (30 Minuten). |Nein |
| writeBatchSize |Fügt Daten in die SQL-Tabelle ein, wenn die Puffergröße "writeBatchSize" erreicht. |Integer (Gesamtanzahl von Zeilen) |Nein (Standardwert: 100) |
| sqlWriterCleanupScript |Geben Sie eine Abfrage für die Kopieraktivität an, bei deren Ausführung die Daten eines bestimmten Slice bereinigt werden. |Eine Abfrageanweisung. |Nein |
| sliceIdentifierColumnName |Geben Sie einen Spaltennamen an, den die Kopieraktivität mit einem automatisch generierten Slicebezeichner füllen soll, der bei erneuter Ausführung zum Bereinigen der Daten eines bestimmten Slices verwendet wird. |Spaltenname einer Spalte mit binärem Datentyp (32). |Nein |

#### <a name="example"></a>Beispiel
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "OracleSink"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Weitere Informationen finden Sie im Artikel zum [Oracle-Connector](data-factory-onprem-oracle-connector.md#copy-activity-properties).

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften PostgreSQL-Diensts den **Typ** des verknüpften Diensts auf **OnPremisesPostgreSql** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| server |Name des PostgreSQL-Servers. |Ja |
| database |Name der PostgreSQL-Datenbank. |Ja |
| schema |Name des Schemas in der Datenbank. Beim Schemanamen wird die Groß- und Kleinschreibung beachtet. |Nein |
| authenticationType |Typ der Authentifizierung für die Verbindung mit der PostgreSQL-Datenbank. Mögliche Werte: „Anonymous“, „Basic“ und „Windows“. |Ja |
| username |Geben Sie den Benutzernamen an, wenn Sie die Standard- oder Windows-Authentifizierung verwenden. |Nein |
| password |Geben Sie das Kennwort für das Benutzerkonto an, das Sie für den Benutzernamen angegeben haben. |Nein |
| gatewayName |Name des Gateways, das der Data Factory-Dienst zum Herstellen einer Verbindung mit der lokalen PostgreSQL-Datenbank verwenden soll. |Ja |

#### <a name="example"></a>Beispiel

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Weitere Informationen finden Sie im Artikel zum [PostgreSQL-Connector](data-factory-onprem-postgresql-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines PostgreSQL-Datasets den **Typ** des Datasets auf **RelationalTable** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| tableName |Name der Tabelle in der PostgreSQL-Datenbankinstanz, auf die der verknüpfte Dienst verweist. Beim Tabellennamen wird die Groß- und Kleinschreibung beachtet. |Nein (wenn **query** von **RelationalSource** angegeben ist) |

#### <a name="example"></a>Beispiel
```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Weitere Informationen finden Sie im Artikel zum [PostgreSQL-Connector](data-factory-onprem-postgresql-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Relationale Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus einer PostgreSQL-Datenbank den **Quelltyp** der Kopieraktivität auf **RelationalSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:


| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| Abfrage |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-Abfragezeichenfolge. Beispiel: "query": "select * from \"MySchema\".\"MyTable\"". |Nein (wenn **tableName** von **Dataset** angegeben ist) |

#### <a name="example"></a>Beispiel

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"public\".\"usstates\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Weitere Informationen finden Sie im Artikel zum [PostgreSQL-Connector](data-factory-onprem-postgresql-connector.md#copy-activity-properties).

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften SAP Business Warehouse-Diensts (BW) den **Typ** des verknüpften Diensts auf **SapBw** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich
-------- | ----------- | -------------- | --------
server | Der Name des Servers, auf dem sich die SAP BW-Instanz befindet. | Zeichenfolge | Ja
systemNumber | Die Systemnummer des SAP BW-Systems. | Zweistellige Dezimalzahl, die als Zeichenfolge angegeben ist. | Ja
clientId | Client-ID des Clients im SAP BW-System. | Dreistellige Dezimalzahl, die als Zeichenfolge angegeben ist. | Ja
username | Der Name des Benutzers, der Zugriff auf den SAP-Server hat | Zeichenfolge | Ja
password | Kennwort für den Benutzer | Zeichenfolge | Ja
gatewayName | Der Name des Gateways, das der Data Factory-Dienst zum Herstellen einer Verbindung mit der lokalen SAP BW-Instanz verwenden soll. | Zeichenfolge | Ja
encryptedCredential | Die verschlüsselte Zeichenfolge mit Anmeldeinformationen | Zeichenfolge | Nein

#### <a name="example"></a>Beispiel

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [SAP Business Warehouse-Connector](data-factory-sap-business-warehouse-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines SAP BW-Datasets den **Typ** des Datasets auf **RelationalTable** fest. Es sind keine typspezifischen Eigenschaften, die für das SAP BW-Dataset des Typs **RelationalTable** unterstützt werden.

#### <a name="example"></a>Beispiel

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Weitere Informationen finden Sie im Artikel zum [SAP Business Warehouse-Connector](data-factory-sap-business-warehouse-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Relationale Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus einer SAP Business Warehouse-Instanz den **Quelltyp** der Kopieraktivität auf **RelationalSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:


| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| Abfrage | Gibt die MDX-Abfrage an, mit der Daten aus der SAP BW-Instanz gelesen werden. | MDX-Abfrage | Ja |

#### <a name="example"></a>Beispiel

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Weitere Informationen finden Sie im Artikel zum [SAP Business Warehouse-Connector](data-factory-sap-business-warehouse-connector.md#copy-activity-properties).

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften SAP HANA-Diensts den **Typ** des verknüpften Diensts auf **SapHana** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich
-------- | ----------- | -------------- | --------
server | Der Name des Servers, auf dem sich die SAP HANA-Instanz befindet. Wenn Ihr Server einen benutzerdefinierten Port verwendet, geben Sie `server:port` an. | Zeichenfolge | Ja
authenticationType | Die Art der Authentifizierung. | string. „Basic“ oder „Windows“ | Ja
username | Der Name des Benutzers, der Zugriff auf den SAP-Server hat | Zeichenfolge | Ja
password | Kennwort für den Benutzer | Zeichenfolge | Ja
gatewayName | Der Name des Gateways, das der Data Factory-Dienst zum Herstellen einer Verbindung mit der lokalen SAP HANA-Instanz verwenden soll | Zeichenfolge | Ja
encryptedCredential | Die verschlüsselte Zeichenfolge mit Anmeldeinformationen | Zeichenfolge | Nein

#### <a name="example"></a>Beispiel

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
Weitere Informationen finden Sie im Artikel zum [SAP HANA-Connector](data-factory-sap-hana-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines SAP HANA-Datasets den **Typ** des Datasets auf **RelationalTable** fest. Es sind keine typspezifischen Eigenschaften, die für das SAP HANA-Dataset des Typs **RelationalTable** unterstützt werden.

#### <a name="example"></a>Beispiel

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Weitere Informationen finden Sie im Artikel zum [SAP HANA-Connector](data-factory-sap-hana-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Relationale Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus einem SAP HANA-Datenspeicher den **Quelltyp** der Kopieraktivität auf **RelationalSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| Abfrage | Gibt die SQL-Abfrage an, mit der Daten aus der SAP HANA-Instanz gelesen werden. | SQL-Abfrage | Ja |


#### <a name="example"></a>Beispiel


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<SQL Query for HANA>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Weitere Informationen finden Sie im Artikel zum [SAP HANA-Connector](data-factory-sap-hana-connector.md#copy-activity-properties).


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Verknüpfter Dienst
Sie erstellen einen verknüpften Dienst des Typs **OnPremisesSqlServer**, um eine SQL Server-Datenbank mit einer Data Factory zu verknüpfen. Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit SQL Server verknüpften Dienst spezifisch sind.

Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit SQL Server verknüpften Dienst spezifisch sind.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type |Legen Sie die type-Eigenschaft auf **OnPremisesSqlServer** fest. |Ja |
| connectionString |Geben Sie „connectionString“-Informationen an, die zum Herstellen einer Verbindung mit der SQL Server-Datenbank mithilfe der SQL- oder Windows-Authentifizierung benötigt werden. |Ja |
| gatewayName |Name des Gateways, das der Data Factory-Dienst zum Herstellen einer Verbindung mit der SQL Server-Datenbank verwenden soll. |Ja |
| username |Geben Sie den Benutzernamen an, wenn Sie die Windows-Authentifizierung verwenden. Beispiel: **Domainname\\username**. |Nein |
| password |Geben Sie das Kennwort für das Benutzerkonto an, das Sie für den Benutzernamen angegeben haben. |Nein |

Sie können Anmeldeinformationen mithilfe des Cmdlets **New-AzDataFactoryEncryptValue** verschlüsseln und wie im folgenden Beispiel gezeigt in der Verbindungszeichenfolge verwenden (**EncryptedCredential**-Eigenschaft):

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Beispiel: JSON-Code für die SQL Server-Authentifizierung

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Beispiel: JSON-Code für die Windows-Authentifizierung

Wenn Benutzername und Kennwort angegeben werden, werden diese Informationen vom Gateway genutzt, um die Identität des angegebenen Benutzerkontos zu übernehmen und eine Verbindung mit der SQL Server-Datenbank herzustellen. Andernfalls stellt das Gateway direkt im Sicherheitskontext des Gateways (mit dessen Startkonto) eine Verbindung mit der SQL Server-Instanz her.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [SQL Server-Connector](data-factory-sqlserver-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines SQL Server-Datasets den **Typ** des Datasets auf **SqlServerTable** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| tableName |Name der Tabelle oder Ansicht in der SQL Server-Datenbankinstanz, auf die der verknüpfte Dienst verweist. |Ja |

#### <a name="example"></a>Beispiel
```json
{
    "name": "SqlServerInput",
    "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [SQL Server-Connector](data-factory-sqlserver-connector.md#dataset-properties).

### <a name="sql-source-in-copy-activity"></a>SQL-Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus einer SQL Server-Datenbank den **Quelltyp** der Kopieraktivität auf **SqlSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:


| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| sqlReaderQuery |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-Abfragezeichenfolge. Beispiel: `select * from MyTable`. Kann auf mehrere Tabellen aus der Datenbank verweisen, auf die vom Eingabedataset verwiesen wird. Falls nicht angegeben, wird folgende SQL-Anweisung ausgeführt: "select from MyTable". |Nein |
| sqlReaderStoredProcedureName |Der Name der gespeicherten Prozedur, die Daten aus der Quelltabelle liest. |Name der gespeicherten Prozedur. |Nein |
| storedProcedureParameters |Parameter für die gespeicherte Prozedur. |Name-Wert-Paare. Die Namen und die Groß-/Kleinschreibung von Parametern müssen denen der Parameter der gespeicherten Prozedur entsprechen. |Nein |

Wenn **sqlReaderQuery** für SqlSource angegeben ist, führt die Kopieraktivität diese Abfrage in der SQL Server-Datenbankquelle aus, um die Daten abzurufen.

Alternativ dazu können Sie eine gespeicherte Prozedur angeben, indem Sie **sqlReaderStoredProcedureName** und **storedProcedureParameters** angeben (sofern die gespeicherten Prozeduren Parameter verwenden).

Wenn Sie weder sqlReaderQuery noch sqlReaderStoredProcedureName angeben, werden die im Strukturabschnitt definierten Spalten verwendet, um eine SELECT-Abfrage zur Ausführung in der SQL Server-Datenbank zu erstellen. Falls die DataSet-Definition nicht über die Struktur verfügt, werden alle Spalten der Tabelle ausgewählt.

> [!NOTE]
> Bei Verwendung von **sqlReaderStoredProcedureName** müssen Sie trotzdem einen Wert für die **tableName**-Eigenschaft in der Dataset-JSON angeben. Es finden jedoch keine Überprüfungen dieser Tabelle statt.


#### <a name="example"></a>Beispiel
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

In diesem Beispiel ist **sqlReaderQuery** für SqlSource angegeben. Mit der Kopieraktivität wird diese Abfrage in der SQL Server-Datenbankquelle ausgeführt, um die Daten abzurufen. Alternativ dazu können Sie eine gespeicherte Prozedur angeben, indem Sie **sqlReaderStoredProcedureName** und **storedProcedureParameters** angeben (sofern die gespeicherten Prozeduren Parameter verwenden). sqlReaderQuery kann auf mehrere Tabellen in der Datenbank verweisen, die vom Eingabedataset referenziert wird. Die Eigenschaft ist im Gegensatz zur typeProperty-Eigenschaft tableName des Datasets nicht auf den Tabellensatz beschränkt.

Wenn Sie sqlReaderQuery oder sqlReaderStoredProcedureName nicht angeben, werden die im Strukturabschnitt definierten Spalten verwendet, um eine SELECT-Abfrage zur Ausführung in der SQL Server-Datenbank zu erstellen. Falls die DataSet-Definition nicht über die Struktur verfügt, werden alle Spalten der Tabelle ausgewählt.

Weitere Informationen finden Sie im Artikel zum [SQL Server-Connector](data-factory-sqlserver-connector.md#copy-activity-properties).

### <a name="sql-sink-in-copy-activity"></a>SQL-Senke in der Kopieraktivität
Legen Sie beim Kopieren von Daten in eine SQL Server-Datenbank den **Senkentyp** der Kopieraktivität auf **SqlSink** fest, und geben Sie im Abschnitt **sink** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| writeBatchTimeout |Die Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird. |Zeitraum<br/><br/> Beispiel: „00:30:00“ (30 Minuten). |Nein |
| writeBatchSize |Fügt Daten in die SQL-Tabelle ein, wenn die Puffergröße "writeBatchSize" erreicht. |Integer (Gesamtanzahl von Zeilen) |Nein (Standardwert: 10.000) |
| sqlWriterCleanupScript |Geben Sie die Abfrage für die Kopieraktivität so an, dass bei Ausführung die Daten eines bestimmten Slices bereinigt werden. Weitere Informationen finden Sie im Abschnitt zur Wiederholbarkeit. |Eine Abfrageanweisung. |Nein |
| sliceIdentifierColumnName |Geben Sie einen Spaltennamen an, den die Kopieraktivität mit einem automatisch generierten Slicebezeichner füllen soll, der bei erneuter Ausführung zum Bereinigen der Daten eines bestimmten Slices verwendet wird. Weitere Informationen finden Sie im Abschnitt zur Wiederholbarkeit. |Spaltenname einer Spalte mit binärem Datentyp (32). |Nein |
| sqlWriterStoredProcedureName |Name der gespeicherten Prozedur, die Daten in die Zieltabelle mit dem Upsert-Vorgang einfügt oder aktualisiert. |Name der gespeicherten Prozedur. |Nein |
| storedProcedureParameters |Parameter für die gespeicherte Prozedur. |Name-Wert-Paare. Die Namen und die Groß-/Kleinschreibung von Parametern müssen denen der Parameter der gespeicherten Prozedur entsprechen. |Nein |
| sqlWriterTableType |Geben Sie einen Tabellentypnamen an, der in der gespeicherten Prozedur verwendet werden soll. Die Kopieraktivität macht die verschobenen Daten in einer temporären Tabelle mit diesem Tabellentyp verfügbar. Der gespeicherte Prozedurcode kann dann die kopierten Daten mit vorhandenen Daten zusammenführen. |Ein Tabellentypname. |Nein |

#### <a name="example"></a>Beispiel
Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **BlobSource** und der Typ **sink** auf **SqlSink** festgelegt.

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Weitere Informationen finden Sie im Artikel zum [SQL Server-Connector](data-factory-sqlserver-connector.md#copy-activity-properties).

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften Sybase-Diensts den **Typ** des verknüpften Diensts auf **OnPremisesSybase** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| server |Name des Sybase-Servers. |Ja |
| database |Name der Sybase-Datenbank. |Ja |
| schema |Name des Schemas in der Datenbank. |Nein |
| authenticationType |Typ der Authentifizierung für die Verbindung mit der Sybase-Datenbank. Mögliche Werte: „Anonymous“, „Basic“ und „Windows“. |Ja |
| username |Geben Sie den Benutzernamen an, wenn Sie die Standard- oder Windows-Authentifizierung verwenden. |Nein |
| password |Geben Sie das Kennwort für das Benutzerkonto an, das Sie für den Benutzernamen angegeben haben. |Nein |
| gatewayName |Name des Gateways, das der Data Factory-Dienst zum Herstellen einer Verbindung mit der lokalen Sybase-Datenbank verwenden soll. |Ja |

#### <a name="example"></a>Beispiel
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Sybase-Connector](data-factory-onprem-sybase-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines Sybase-Datasets den **Typ** des Datasets auf **RelationalTable** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| tableName |Name der Tabelle in der Sybase-Datenbankinstanz, auf die der verknüpfte Dienst verweist. |Nein (wenn **query** von **RelationalSource** angegeben ist) |

#### <a name="example"></a>Beispiel

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Sybase-Connector](data-factory-onprem-sybase-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Relationale Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus einer Sybase-Datenbank den **Quelltyp** der Kopieraktivität auf **RelationalSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:


| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| Abfrage |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-Abfragezeichenfolge. Beispiel: `select * from MyTable`. |Nein (wenn **tableName** von **Dataset** angegeben ist) |

#### <a name="example"></a>Beispiel

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Sybase-Connector](data-factory-onprem-sybase-connector.md#copy-activity-properties).

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften Teradata-Diensts den **Typ** des verknüpften Diensts auf **OnPremisesTeradata** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| server |Name des Teradata-Servers. |Ja |
| authenticationType |Typ der Authentifizierung für die Verbindung mit der Teradata-Datenbank. Mögliche Werte: „Anonymous“, „Basic“ und „Windows“. |Ja |
| username |Geben Sie den Benutzernamen an, wenn Sie die Standard- oder Windows-Authentifizierung verwenden. |Nein |
| password |Geben Sie das Kennwort für das Benutzerkonto an, das Sie für den Benutzernamen angegeben haben. |Nein |
| gatewayName |Name des Gateways, das der Data Factory-Dienst zum Herstellen einer Verbindung mit der lokalen Teradata-Datenbank verwenden soll. |Ja |

#### <a name="example"></a>Beispiel
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Teradata-Connector](data-factory-onprem-teradata-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines Teradata-Blobdatasets den **Typ** des Datasets auf **RelationalTable** fest. Zurzeit werden keine Typeigenschaften für das Teradata-Dataset unterstützt.

#### <a name="example"></a>Beispiel
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Teradata-Connector](data-factory-onprem-teradata-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Relationale Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus einer Teradata-Datenbank den **Quelltyp** der Kopieraktivität auf **RelationalSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| Abfrage |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-Abfragezeichenfolge. Beispiel: `select * from MyTable`. |Ja |

#### <a name="example"></a>Beispiel

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Teradata-Connector](data-factory-onprem-teradata-connector.md#copy-activity-properties).

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften Cassandra-Diensts den **Typ** des verknüpften Diensts auf **OnPremisesCassandra** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| host |Mindestens eine IP-Adresse oder ein Hostname von Cassandra-Servern.<br/><br/>Geben Sie eine durch Trennzeichen getrennte Liste mit IP-Adressen oder Hostnamen an, um gleichzeitig mit allen Servern Verbindungen herzustellen. |Ja |
| port |Der TCP-Port, den der Cassandra-Server verwendet, um auf Clientverbindungen zu lauschen. |Nein, Standardwert: 9042 |
| authenticationType |Basic (Standard) oder Anonymous (Anonym) |Ja |
| username |Geben Sie einen Benutzernamen für das Benutzerkonto an. |Ja, wenn authenticationType auf „Basic“ (Standard) festgelegt ist. |
| password |Geben Sie ein Kennwort für das Benutzerkonto an. |Ja, wenn authenticationType auf „Basic“ (Standard) festgelegt ist. |
| gatewayName |Der Name des Gateways, das zum Herstellen der Verbindung mit der lokalen Cassandra-Datenbank verwendet wird. |Ja |
| encryptedCredential |Anmeldeinformationen, die vom Gateway verschlüsselt werden. |Nein |

#### <a name="example"></a>Beispiel

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Cassandra-Connector](data-factory-onprem-cassandra-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines Cassandra-Datasets den **Typ** des Datasets auf **CassandraTable** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| keyspace |Name des Keyspace oder Schemas in der Cassandra-Datenbank. |Ja (wenn **query** für **CassandraSource** nicht definiert ist). |
| tableName |Name der Tabelle in der Cassandra-Datenbank. |Ja (wenn **query** für **CassandraSource** nicht definiert ist). |

#### <a name="example"></a>Beispiel

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Cassandra-Connector](data-factory-onprem-cassandra-connector.md#dataset-properties).

### <a name="cassandra-source-in-copy-activity"></a>Cassandra-Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus Cassandra den **Quelltyp** der Kopieraktivität auf **CassandraSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| Abfrage |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-92-Abfrage oder CQL-Abfrage. Weitere Informationen finden Sie in der [Referenz zu CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Geben Sie beim Verwenden der SQL-Abfrage **keyspace name.table name** für die Tabelle an, die Sie abfragen möchten. |Nein (wenn tableName und keyspace im Dataset definiert sind) |
| consistencyLevel |Mit der Konsistenzebene (consistencyLevel) wird angegeben, wie viele Replikate auf eine Leseanforderung reagieren müssen, bevor Daten an die Clientanwendung zurückgegeben werden. Cassandra überprüft die angegebene Anzahl von Replikaten auf Daten, um die Leseanforderung zu erfüllen. |ONE, TWO, THREE, QUORUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Ausführliche Informationen finden Sie unter [Configuring data consistency](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) (Konfigurieren der Datenkonsistenz). |Nein. Der Standardwert ist ONE. |

#### <a name="example"></a>Beispiel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Cassandra-Connector](data-factory-onprem-cassandra-connector.md#copy-activity-properties).

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften MongoDB-Diensts den **Typ** des verknüpften Diensts auf **OnPremisesMongoDB** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| server |IP-Adresse oder Hostname des MongoDB-Servers |Ja |
| port |Der TCP-Port, den der MongoDB-Server verwendet, um auf Clientverbindungen zu lauschen |Optional, Standardwert: 27.017 |
| authenticationType |Basic oder Anonymous |Ja |
| username |Benutzerkonto für den Zugriff auf MongoDB |Ja (wenn die Standardauthentifizierung verwendet wird) |
| password |Kennwort für den Benutzer |Ja (wenn die Standardauthentifizierung verwendet wird) |
| authSource |Der Name der MongoDB-Datenbank, die Sie zum Überprüfen Ihrer Anmeldeinformationen zur Authentifizierung verwenden möchten |Optional (wenn die Standardauthentifizierung verwendet wird). Standardwert: verwendet das Administratorkonto und die Datenbank, die mit der databaseName-Eigenschaft angegeben wird |
| databaseName |Der Name der MongoDB-Datenbank, auf die Sie zugreifen möchten |Ja |
| gatewayName |Der Name des Gateways, das auf den Datenspeicher zugreift |Ja |
| encryptedCredential |Anmeldeinformationen, die vom Gateway verschlüsselt werden |Optional |

#### <a name="example"></a>Beispiel

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [MongoDB-Connector](data-factory-on-premises-mongodb-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines MongoDB-Datasets den **Typ** des Datasets auf **MongoDbCollection** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| collectionName |Der Name der Sammlung in der MongoDB-Datenbank |Ja |

#### <a name="example"></a>Beispiel

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

Weitere Informationen finden Sie im Artikel zum [MongoDB-Connector](data-factory-on-premises-mongodb-connector.md#dataset-properties).

#### <a name="mongodb-source-in-copy-activity"></a>MongoDB-Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus MongoDB den **Quelltyp** der Kopieraktivität auf **MongoDbSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| Abfrage |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-92-Abfragezeichenfolge. Beispiel: `select * from MyTable`. |Nein (wenn **collectionName** von **dataset** angegeben ist) |

#### <a name="example"></a>Beispiel

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Weitere Informationen finden Sie im Artikel zum [MongoDB-Connector](data-factory-on-premises-mongodb-connector.md#copy-activity-properties).

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften Amazon S3-Diensts den **Typ** des verknüpften Diensts auf **AwsAccessKey** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| accessKeyID |ID des geheimen Zugriffsschlüssels. |Zeichenfolge |Ja |
| secretAccessKey |Der geheime Zugriffsschlüssel selbst. |Verschlüsselte geheime Zeichenfolge |Ja |

#### <a name="example"></a>Beispiel
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Amazon S3-Connector](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines Amazon S3-Datasets den **Typ** des Datasets auf **AmazonS3** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| bucketName |Der Name des S3-Buckets. |String |Ja |
| Schlüssel |Der S3-Objektschlüssel. |String |Nein |
| prefix |Präfix für den S3-Objektschlüssel. Objekte, deren Schlüssel mit diesem Präfix beginnen, werden ausgewählt. Gilt nur, wenn der Schlüssel leer ist. |String |Nein |
| version |Die Version des S3-Objekts, wenn S3-Versionierung aktiviert ist. |String |Nein |
| format | Die folgenden Formattypen werden unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type** -Eigenschaft unter „format“ auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format) und [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Wenn Sie **Dateien unverändert zwischen dateibasierten Speichern kopieren** möchten (binäre Kopie), können Sie den Formatabschnitt bei den Definitionen von Eingabe- und Ausgabedatasets überspringen. |Nein | |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Folgende Typen werden unterstützt: **GZip**, **Deflate**, **BZip2** und **ZipDeflate**. Die folgenden Ebenen werden unterstützt: **Optimal** und **Fastest**. Weitere Informationen finden Sie unter [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nein | |


> [!NOTE]
> „bucketName + key“ gibt den Speicherort des S3-Objekts an, wobei bucketName der Name des Stammcontainers für S3-Objekte und „key“ der vollständige Pfad zum S3-Objekt ist.

#### <a name="example-sample-dataset-with-prefix"></a>Beispiel: Beispieldataset mit Präfix

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>Beispiel: Beispieldataset (mit Version)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>Beispiel: Dynamische Pfade für S3
In diesem Beispiel verwenden wir feste Werte für die Eigenschaften „key“ und bucketName im Amazon S3-Dataset.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Mithilfe von Systemvariablen wie SliceStart können Sie „key“ und bucketName dynamisch zur Laufzeit durch Data Factory berechnen lassen.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Dies können Sie auch für die prefix-Eigenschaft eines Amazon S3-Datasets vornehmen. Unter [Data Factory – Funktionen und Systemvariablen](data-factory-functions-variables.md) finden Sie eine Liste unterstützter Funktionen und Variablen.

Weitere Informationen finden Sie im Artikel zum [Amazon S3-Connector](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Dateisystemquelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus Amazon S3 den **Quelltyp** der Kopieraktivität auf **FileSystemSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:


| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| recursive |Gibt an, ob S3-Objekte rekursiv im Verzeichnis aufgelistet werden. |TRUE/FALSE |Nein |


#### <a name="example"></a>Beispiel


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Amazon S3-Connector](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Dateisystem


### <a name="linked-service"></a>Verknüpfter Dienst
Sie können ein lokales Dateisystem mithilfe eines verknüpften Diensts vom Typ **Lokaler Dateiserver** mit einer Azure Data Factory verknüpfen. Die folgende Tabelle enthält Beschreibungen der JSON-Elemente, die für den verknüpften Dienst vom Typ „Lokaler Dateiserver“ spezifisch sind.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type |Stellen Sie sicher, dass die Eigenschaft „type“ auf **OnPremisesFileServer** festgelegt ist. |Ja |
| host |Gibt den Stammpfad des Ordners an, den Sie kopieren möchten. Verwenden Sie für Sonderzeichen in der Zeichenfolge das Escapezeichen „\“. Beispiele finden Sie unter „Beispieldefinitionen für verknüpfte Dienste und Datasets“. |Ja |
| userid |Geben Sie die ID des Benutzers an, der auf dem Server zugreifen darf. |Nein (wenn Sie "encryptedCredential" auswählen) |
| password |Geben Sie das Kennwort für das Benutzerkonto (userid) an. |Nein (wenn Sie "encryptedCredential" auswählen) |
| encryptedCredential |Geben Sie die verschlüsselten Anmeldeinformationen an. Diese können Sie durch Ausführen des Cmdlets „New-AzDataFactoryEncryptValue“ abrufen. |Nein (wenn Sie "userid" und "password" unverschlüsselt angeben) |
| gatewayName |Gibt den Name des Gateways an, das der Data Factory-Dienst zum Verbinden mit dem lokalen Dateiserver verwenden soll. |Ja |

#### <a name="sample-folder-path-definitions"></a>Beispieldefinitionen für Ordnerpfad

| Szenario | Host in der Definition des verknüpften Diensts | folderPath in der Datasetdefinition |
| --- | --- | --- |
| Lokaler Ordner auf dem Datenverwaltungsgateway-Computer: <br/><br/>Beispiele: D:\\\* oder D:\Ordner\Unterordner\\\* |D:\\\\ (für Datenverwaltungsgateway 2.0 und neuere Versionen) <br/><br/> localhost (für ältere Versionen als Datenverwaltungsgateway 2.0) |.\\\\ oder Ordner\\\\Unterordner (für Datenverwaltungsgateway 2.0 und neuere Versionen) <br/><br/>D:\\\\ oder D:\\\\Ordner\\\\Unterordner (für Gatewayversionen unter 2.0) |
| Freigegebener Remoteordner: <br/><br/>Beispiele: \\\\MeinServer\\Freigabe\\\* oder \\\\MeinServer\\Freigabe\\Ordner\\Unterordner\\\* |\\\\\\\\MeinServer\\\\Freigabe |.\\\\ oder Ordner\\\\Unterordner |


#### <a name="example-using-username-and-password-in-plain-text"></a>Beispiel: Verwenden von „username“ und „password“ im Nur-Text-Format

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>Beispiel: Verwenden von encryptedcredential

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Dateisystem-Connector](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines Dateisystem-Datasets den **Typ** des Datasets auf **FileShare** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| folderPath |Gibt den Unterpfad zum Ordner an. Verwenden Sie für Sonderzeichen in der Zeichenfolge das Escapezeichen „\“. Beispiele finden Sie unter „Beispieldefinitionen für verknüpfte Dienste und Datasets“.<br/><br/>Sie können diese Eigenschaft mit **partitionBy** kombinieren, um Ordnerpfade auf der Grundlage von Datum und Uhrzeit für Start und Ende des Slices zu erhalten. |Ja |
| fileName |Geben Sie den Namen der Datei in **folderPath** an, wenn die Tabelle auf eine bestimmte Datei im Ordner verweisen soll. Wenn Sie keine Werte für diese Eigenschaft angeben, verweist die Tabelle auf alle Dateien im Ordner.<br/><br/>Wenn der Dateiname für ein Ausgabedataset nicht angegeben ist, weist der Name der generierten Datei das folgenden Format auf: <br/><br/>`Data.<Guid>.txt` (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt). |Nein |
| fileFilter |Geben Sie einen Filter zur Auswahl einer Teilmenge der Dateien in "folderPath" statt alle Dateien an. <br/><br/>Zulässige Werte sind: `*` (mehrere Zeichen) und `?` (einzelnes Zeichen).<br/><br/>Beispiel 1: „fileFilter“: „*.log“<br/>Beispiel 2: „fileFilter“: „2016-1-?.txt“<br/><br/>Beachten Sie, dass sich „fileFilter“ für das Eingabedataset „FileShare“ eignet. |Nein |
| partitionedBy |Sie können mit „partitionedBy“ für Zeitreihendaten einen dynamischen Wert für „folderPath“ und „filename“ angeben. Beispiel: Parametrisierung von „folderPath“ für Daten nach Stunde. |Nein |
| format | Die folgenden Formattypen werden unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type** -Eigenschaft unter „format“ auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format) und [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Wenn Sie **Dateien unverändert zwischen dateibasierten Speichern kopieren** möchten (binäre Kopie), können Sie den Formatabschnitt bei den Definitionen von Eingabe- und Ausgabedatasets überspringen. |Nein |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Folgende Typen werden unterstützt: **GZIP**, **Deflate**, **BZIP2** und **ZipDeflate**. Die unterstützten Ebenen lauten: **Optimal** und **Fastest**. Weitere Informationen finden Sie unter [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nein |

> [!NOTE]
> „fileName“ und „fileFilter“ können nicht gleichzeitig verwendet werden.

#### <a name="example"></a>Beispiel

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Dateisystem-Connector](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Dateisystemquelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus dem Dateisystem den **Quelltyp** der Kopieraktivität auf **FileSystemSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| recursive |Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. |True/False (Standardwert) |Nein |

#### <a name="example"></a>Beispiel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Weitere Informationen finden Sie im Artikel zum [Dateisystem-Connector](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>Dateisystem-Senke in der Kopieraktivität
Legen Sie beim Kopieren von Daten in ein Dateisystem den **Senkentyp** der Kopieraktivität auf **FileSystemSink** fest, und geben Sie im Abschnitt **sink** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| copyBehavior |Definiert das Verhalten beim Kopieren, wenn die Quelle "BlobSource" oder "FileSystem" ist. |**PreserveHierarchy:** Behält die Dateihierarchie im Zielordner bei. Der relative Pfad der Quelldatei zum Quellordner entspricht dem relativen Pfad der Zieldatei zum Zielordner.<br/><br/>**FlattenHierarchy:** Alle Dateien aus dem Quellordner werden auf der ersten Ebene des Zielordners erstellt. Die Namen der Zieldateien werden automatisch generiert.<br/><br/>**MergeFiles:** Alle Dateien aus dem Quellordner werden in einer Datei zusammengeführt. Wenn der Datei-/Blob-Name angegeben wurde, entspricht der Name dem angegebenen Namen, andernfalls dem automatisch generierten Dateinamen. |Nein |

auto-

#### <a name="example"></a>Beispiel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "FileSystemSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Dateisystem-Connector](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften FTP-Diensts den **Typ** des verknüpften Diensts auf **FtpServer** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich | Standard |
| --- | --- | --- | --- |
| host |Name oder IP-Adresse des FTP-Servers |Ja |&nbsp; |
| authenticationType |Angeben des Authentifizierungstyps |Ja |Standard, Anonym |
| username |Der Benutzer, der Zugriff auf den FTP-Server hat |Nein |&nbsp; |
| password |Kennwort für den Benutzer (username) |Nein |&nbsp; |
| encryptedCredential |Verschlüsselte Anmeldeinformation für den Zugriff auf den FTP-Server |Nein |&nbsp; |
| gatewayName |Name des Datenverwaltungsgateways für die Verbindung mit einem lokalen FTP-Server |Nein |&nbsp; |
| port |Port, den der FTP-Server abhört |Nein |21 |
| enableSsl |Angeben, ob FTP über SSL/TSL-Kanal verwendet werden soll |Nein |true |
| enableServerCertificateValidation |Angeben, ob die Überprüfung des TLS-/SSL-Serverzertifikats aktiviert werden soll, wenn FTP über SSL-/TSL-Kanal verwendet wird |Nein |true |

#### <a name="example-using-anonymous-authentication"></a>Beispiel: Verwenden der anonymen Authentifizierung

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
            "typeProperties": {
            "authenticationType": "Anonymous",
            "host": "myftpserver.com"
        }
    }
}
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Beispiel: Verwenden von „username“ und „password“ im Nur-Text-Format für die Standardauthentifizierung

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Beispiel: Verwenden von Port, enableSsl, enableServerCertificateValidation

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Beispiel: Verwenden von encryptedCredential für die Authentifizierung und das Gateway

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

Weitere Informationen finden Sie im Artikel zum [FTP-Connector](data-factory-ftp-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines FTP-Datasets den **Typ** des Datasets auf **FileShare** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| folderPath |Unterpfad zum Ordner. Verwenden Sie für Sonderzeichen in der Zeichenfolge das Escapezeichen „\“. Beispiele finden Sie unter „Beispieldefinitionen für verknüpfte Dienste und Datasets“.<br/><br/>Sie können diese Eigenschaft mit **partitionBy** kombinieren, um Ordnerpfade auf der Grundlage von Datum und Uhrzeit für Start und Ende des Slices zu erhalten. |Ja
| fileName |Geben Sie den Namen der Datei in **folderPath** an, wenn die Tabelle auf eine bestimmte Datei im Ordner verweisen soll. Wenn Sie keine Werte für diese Eigenschaft angeben, verweist die Tabelle auf alle Dateien im Ordner.<br/><br/>Wenn „fileName“ für ein Ausgabedataset nicht angegeben ist, hat der Name der generierten Datei folgendes Format: <br/><br/>`Data.<Guid>.txt` (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt). |Nein |
| fileFilter |Geben Sie einen Filter zur Auswahl einer Teilmenge der Dateien in "folderPath" statt alle Dateien an.<br/><br/>Zulässige Werte sind: `*` (mehrere Zeichen) und `?` (einzelnes Zeichen).<br/><br/>Beispiel 1: `"fileFilter": "*.log"`<br/>Beispiel 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter eignet sich für das Eingabedataset FileShare. Diese Eigenschaft wird mit HDFS nicht unterstützt. |Nein |
| partitionedBy |Mit „partitionedBy“ kann für Zeitreihendaten ein dynamischer Wert für „folderPath“ und „filename“ angegeben werden. Beispiel: Parametrisierung von „folderPath“ für Daten nach Stunde. |Nein |
| format | Die folgenden Formattypen werden unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type** -Eigenschaft unter „format“ auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format) und [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Wenn Sie **Dateien unverändert zwischen dateibasierten Speichern kopieren** möchten (binäre Kopie), können Sie den Formatabschnitt bei den Definitionen von Eingabe- und Ausgabedatasets überspringen. |Nein |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Folgende Typen werden unterstützt: **GZIP**, **Deflate**, **BZIP2** und **ZipDeflate**. Die unterstützten Ebenen lauten: **Optimal** und **Fastest**. Weitere Informationen finden Sie unter [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nein |
| useBinaryTransfer |Gibt an, ob der binären Übertragungsmodus verwendet werden soll. Bei TRUE wird der Binärmodus und bei FALSE der ASCII-Modus verwendet. Standardwert: True. Diese Eigenschaft kann nur verwendet werden, wenn der zugehörige verknüpfte Dienst vom Typ „FtpServer“ ist. |Nein |

> [!NOTE]
> "filename" und "fileFilter" können nicht gleichzeitig verwendet werden.

#### <a name="example"></a>Beispiel

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv",
            "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [FTP-Connector](data-factory-ftp-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Dateisystemquelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten von einem FTP-Server den **Quelltyp** der Kopieraktivität auf **FileSystemSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| recursive |Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. |True/False (Standardwert) |Nein |

#### <a name="example"></a>Beispiel

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

Weitere Informationen finden Sie im Artikel zum [FTP-Connector](data-factory-ftp-connector.md#copy-activity-properties).


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften HDFS-Diensts den **Typ** des verknüpften Diensts auf **Hdfs** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type |Die type-Eigenschaft muss auf Folgendes festgelegt werden: **Hdfs**. |Ja |
| url |Die URL für das HDFS. |Ja |
| authenticationType |Anonym oder Windows. <br><br> Um Ihre lokale Umgebung zur Verwendung der **Kerberos-Authentifizierung** für den HDFS-Connector einzurichten, lesen Sie diesen Abschnitt. |Ja |
| userName |Der Benutzername für die Windows-Authentifizierung. |Ja (für die Windows-Authentifizierung) |
| password |Das Kennwort für die Windows-Authentifizierung. |Ja (für die Windows-Authentifizierung) |
| gatewayName |Der Name des Gateways, das der Data Factory-Dienst zum Verbinden mit dem HDFS verwenden soll. |Ja |
| encryptedCredential |[New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue)-Ausgabe der Zugriffsanmeldeinformationen. |Nein |

#### <a name="example-using-anonymous-authentication"></a>Beispiel: Verwenden der anonymen Authentifizierung

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>Beispiel: Verwenden der Windows-Authentifizierung

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel über den HDFS-Connector.

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines HDFS-Datasets den **Typ** des Datasets auf **FileShare** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| folderPath |Pfad zum Ordner. Beispiel: `myfolder`<br/><br/>Verwenden Sie für Sonderzeichen in der Zeichenfolge das Escapezeichen „\“. Geben Sie beispielsweise für „Ordner\Unterordner“ die Zeichenfolge „Ordner\\\\Unterordner“ und für „d:\Beispielordner“ die Zeichenfolge „d:\\\\Beispielordner“ an.<br/><br/>Sie können diese Eigenschaft mit **partitionBy** kombinieren, um Ordnerpfade auf der Grundlage von Datum und Uhrzeit für Start und Ende des Slices zu erhalten. |Ja |
| fileName |Geben Sie den Namen der Datei in **folderPath** an, wenn die Tabelle auf eine bestimmte Datei im Ordner verweisen soll. Wenn Sie keine Werte für diese Eigenschaft angeben, verweist die Tabelle auf alle Dateien im Ordner.<br/><br/>Wenn „fileName“ für ein Ausgabedataset nicht angegeben ist, hat der Name der generierten Datei folgendes Format: <br/><br/>`Data.<Guid>.txt` (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt). |Nein |
| partitionedBy |Mit „partitionedBy“ kann für Zeitreihendaten ein dynamischer Wert für „folderPath“ und „filename“ angegeben werden. Beispiel: Parametrisierung von „folderPath“ für Daten nach Stunde. |Nein |
| format | Die folgenden Formattypen werden unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type** -Eigenschaft unter „format“ auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format) und [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Wenn Sie **Dateien unverändert zwischen dateibasierten Speichern kopieren** möchten (binäre Kopie), können Sie den Formatabschnitt bei den Definitionen von Eingabe- und Ausgabedatasets überspringen. |Nein |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Folgende Typen werden unterstützt: **GZip**, **Deflate**, **BZip2** und **ZipDeflate**. Folgende Ebenen werden unterstützt: **Optimal** und **Fastest**. Weitere Informationen finden Sie unter [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nein |

> [!NOTE]
> "filename" und "fileFilter" können nicht gleichzeitig verwendet werden.

#### <a name="example"></a>Beispiel

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Weitere Informationen finden Sie im Artikel über den HDFS-Connector.

### <a name="file-system-source-in-copy-activity"></a>Dateisystemquelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus HDFS den **Quelltyp** der Kopieraktivität auf **FileSystemSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:

**FileSystemSource** unterstützt die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| recursive |Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. |True/False (Standardwert) |Nein |

#### <a name="example"></a>Beispiel

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Weitere Informationen finden Sie im Artikel über den HDFS-Connector.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften SFTP-Diensts den **Typ** des verknüpften Diensts auf **Sftp** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| host | Name oder IP-Adresse des SFTP-Servers. |Ja |
| port |Port, an dem der SFTP-Server lauscht. Der Standardwert lautet: 21 |Nein |
| authenticationType |Angeben des Authentifizierungstyps. Zulässige Werte: **Basic**, **SshPublicKey**. <br><br> Weitere Eigenschaften bzw. JSON-Beispiele finden Sie in den Abschnitten „Verwenden von Standardauthentifizierung“ und [Verwenden von Authentifizierung mit öffentlichem SSH-Schlüssel](#using-ssh-public-key-authentication). |Ja |
| skipHostKeyValidation | Angabe, ob die Überprüfung des Hostschlüssels übersprungen werden soll. | Nein. Standardwert: FALSCH |
| hostKeyFingerprint | Angabe des Fingerabdrucks des Hostschlüssels. | Ja, wenn `skipHostKeyValidation` auf FALSCH festgelegt ist.  |
| gatewayName |Name des Datenverwaltungsgateways für die Verbindung mit einen lokalen SFTP-Server. | Ja beim Kopieren von Daten von einem lokalen SFTP-Server. |
| encryptedCredential | Verschlüsselte Anmeldeinformation für den Zugriff auf den SFTP-Server. Beim Festlegen von Standardauthentifizierung (Benutzername + Kennwort) oder SshPublicKey-Authentifizierung (Benutzername + Pfad oder Inhalt des privaten Schlüssels) im Kopier-Assistenten oder im ClickOnce-Popupdialogfeld automatisch generiert. | Nein. Betrifft nur das Kopieren von Daten von einem lokalen SFTP-Server. |

#### <a name="example-using-basic-authentication"></a>Beispiel: Verwenden der Standardauthentifizierung

Legen Sie zum Verwenden der Standardauthentifizierung `authenticationType` auf `Basic` fest, und geben Sie über die im letzten Abschnitt beschriebenen allgemeinen Eigenschaften des SFTP-Connectors hinaus die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| username | Benutzer, der Zugriff auf den SFTP-Server hat. |Ja |
| password | Kennwort für den Benutzer (username) | Ja |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Beispiel: **Standardauthentifizierung (Basic) mit verschlüsselten Anmeldeinformationen**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>**Verwenden von Authentifizierung mit öffentlichem SSH-Schlüssel:**

Legen Sie zum Verwenden der Standardauthentifizierung `authenticationType` auf `SshPublicKey` fest, und geben Sie über die im letzten Abschnitt beschriebenen allgemeinen Eigenschaften des SFTP-Connectors hinaus die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| username |Benutzer, der Zugriff auf den SFTP-Server hat. |Ja |
| privateKeyPath | Geben Sie den absoluten Pfad der privaten Schlüsseldatei ein, auf die das Gateway zugreifen kann. | Geben Sie entweder `privateKeyPath` oder `privateKeyContent` an. <br><br> Betrifft nur das Kopieren von Daten von einem lokalen SFTP-Server. |
| privateKeyContent | Eine serialisierte Zeichenfolge mit dem Inhalt des privaten Schlüssels. Der Kopier-Assistent kann die private Schlüsseldatei lesen und den privaten Schlüsselinhalt automatisch extrahieren. Wenn Sie andere Tools/SDKs verwenden, nutzen Sie stattdessen die privateKeyPath-Eigenschaft. | Geben Sie entweder `privateKeyPath` oder `privateKeyContent` an. |
| passPhrase | Geben Sie die Passphrase/das Kennwort zum Entschlüsseln des privaten Schlüssels ein, wenn die Schlüsseldatei mithilfe einer Passphrase geschützt ist. | Ja, wenn die private Schlüsseldatei mithilfe einer Passphrase geschützt ist. |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Beispiel: **SshPublicKey-Authentifizierung unter Verwendung des Inhalts des privaten Schlüssels**

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [SFTP-Connector](data-factory-sftp-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines SFTP-Datasets den **Typ** des Datasets auf **FileShare** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| folderPath |Unterpfad zum Ordner. Verwenden Sie für Sonderzeichen in der Zeichenfolge das Escapezeichen „\“. Beispiele finden Sie unter „Beispieldefinitionen für verknüpfte Dienste und Datasets“.<br/><br/>Sie können diese Eigenschaft mit **partitionBy** kombinieren, um Ordnerpfade auf der Grundlage von Datum und Uhrzeit für Start und Ende des Slices zu erhalten. |Ja |
| fileName |Geben Sie den Namen der Datei in **folderPath** an, wenn die Tabelle auf eine bestimmte Datei im Ordner verweisen soll. Wenn Sie keine Werte für diese Eigenschaft angeben, verweist die Tabelle auf alle Dateien im Ordner.<br/><br/>Wenn „fileName“ für ein Ausgabedataset nicht angegeben ist, hat der Name der generierten Datei folgendes Format: <br/><br/>`Data.<Guid>.txt` (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt). |Nein |
| fileFilter |Geben Sie einen Filter zur Auswahl einer Teilmenge der Dateien in "folderPath" statt alle Dateien an.<br/><br/>Zulässige Werte sind: `*` (mehrere Zeichen) und `?` (einzelnes Zeichen).<br/><br/>Beispiel 1: `"fileFilter": "*.log"`<br/>Beispiel 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter eignet sich für das Eingabedataset FileShare. Diese Eigenschaft wird mit HDFS nicht unterstützt. |Nein |
| partitionedBy |Mit „partitionedBy“ kann für Zeitreihendaten ein dynamischer Wert für „folderPath“ und „filename“ angegeben werden. Beispiel: Parametrisierung von „folderPath“ für Daten nach Stunde. |Nein |
| format | Die folgenden Formattypen werden unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type** -Eigenschaft unter „format“ auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format) und [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Wenn Sie **Dateien unverändert zwischen dateibasierten Speichern kopieren** möchten (binäre Kopie), können Sie den Formatabschnitt bei den Definitionen von Eingabe- und Ausgabedatasets überspringen. |Nein |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Folgende Typen werden unterstützt: **GZip**, **Deflate**, **BZip2** und **ZipDeflate**. Folgende Ebenen werden unterstützt: **Optimal** und **Fastest**. Weitere Informationen finden Sie unter [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nein |
| useBinaryTransfer |Gibt an, ob der binären Übertragungsmodus verwendet werden soll. Bei TRUE wird der Binärmodus und bei FALSE der ASCII-Modus verwendet. Standardwert: True. Diese Eigenschaft kann nur verwendet werden, wenn der zugehörige verknüpfte Dienst vom Typ „FtpServer“ ist. |Nein |

> [!NOTE]
> "filename" und "fileFilter" können nicht gleichzeitig verwendet werden.

#### <a name="example"></a>Beispiel

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [SFTP-Connector](data-factory-sftp-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Dateisystemquelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus einer SFTP-Quelle den **Quelltyp** der Kopieraktivität auf **FileSystemSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| recursive |Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. |True/False (Standardwert) |Nein |



#### <a name="example"></a>Beispiel

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

Weitere Informationen finden Sie im Artikel zum [SFTP-Connector](data-factory-sftp-connector.md#copy-activity-properties).


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften HTTP-Diensts den **Typ** des verknüpften Diensts auf **Http** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| url | Basis-URL zum Webserver | Ja |
| authenticationType | Gibt den Authentifizierungstyp an. Zulässige Werte sind: **Anonymous**, **Basic**, **Digest**, **Windows**, **ClientCertificate**. <br><br> Weitere Eigenschaften und JSON-Beispiele für diese Authentifizierungstypen finden Sie in den Abschnitten nach dieser Tabelle. | Ja |
| enableServerCertificateValidation | Geben Sie an, ob die Überprüfung des TLS-/SSL-Serverzertifikats aktiviert werden soll, wenn die Quelle ein HTTPS-Webserver ist. | Nein. Der Standardwert ist TRUE. |
| gatewayName | Name des Datenverwaltungsgateways für die Verbindung mit einer lokalen HTTP-Quelle | Ja beim Kopieren von Daten von einer lokalen HTTP-Quelle |
| encryptedCredential | Verschlüsselte Anmeldeinformation für den Zugriff auf den HTTP-Endpunkt. Werden automatisch generiert, wenn Sie die Authentifizierungsinformationen im Kopier-Assistenten oder im ClickOnce-Popupdialogfeld konfigurieren. | Nein. Betrifft nur das Kopieren von Daten von einem lokalen HTTP-Server. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Beispiel: Verwenden der Authentifizierung des Typs „Basic“, „Digest“ oder „Windows“
Legen Sie für `authenticationType` die Option `Basic`, `Digest` oder `Windows` fest, und geben Sie zusätzlich zu den oben vorgestellten allgemeinen HTTP-Connectoreigenschaften die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| username | Benutzername zum Zugreifen auf den HTTP-Endpunkt | Ja |
| password | Kennwort für den Benutzer (username) | Ja |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>Beispiel: Verwenden der ClientCertificate-Authentifizierung

Legen Sie zum Verwenden der Standardauthentifizierung `authenticationType` auf `ClientCertificate` fest, und geben Sie zusätzlich zu den oben beschriebenen allgemeinen Eigenschaften des HTTP-Connectors die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| embeddedCertData | Der Base64-codierte Inhalt der Binärdaten der Personal Information Exchange-Datei (PFX) | Geben Sie entweder `embeddedCertData` oder `certThumbprint` an. |
| certThumbprint | Der Fingerabdruck des Zertifikats, das im Zertifikatspeicher des Gatewaycomputers installiert wurde. Betrifft nur das Kopieren von Daten von einer lokalen HTTP-Quelle. | Geben Sie entweder `embeddedCertData` oder `certThumbprint` an. |
| password | Das Kennwort des Zertifikats | Nein |

Wenn Sie `certThumbprint` für die Authentifizierung verwenden und das Zertifikat im persönlichen Speicher des lokalen Computers installiert wird, müssen Sie dem Gatewaydienst Leseberechtigungen gewähren:

1. Starten Sie die Microsoft Management Console (MMC). Fügen Sie das für **Lokaler Computer** vorgesehene Snap-In **Zertifikate** hinzu.
2. Erweitern Sie **Zertifikate** > **Personal** (Persönlich), und klicken Sie auf **Zertifikate**.
3. Klicken Sie im persönlichen Speicher mit der rechten Maustaste auf das Zertifikat, und wählen Sie **Alle Aufgaben**->**Private Schlüssel verwalten...** .
3. Fügen Sie auf der Registerkarte **Sicherheit** das Benutzerkonto hinzu, unter dem der Datenverwaltungsgateway-Hostdienst mit dem Lesezugriff auf das Zertifikat ausgeführt wird.

**Beispiel: Verwenden eines Clientzertifikats** Dieser verknüpfte Dienst verbindet Ihre Data Factory mit einem lokalen HTTP-Server. Er verwendet ein Clientzertifikat, das auf dem Computer mit dem Datenverwaltungsgateway installiert ist.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Beispiel: Verwenden eines Clientzertifikats in einer Datei
Dieser verknüpfte Dienst verbindet Ihre Data Factory mit einem lokalen HTTP-Server. Er verwendet eine Clientzertifikatdatei auf dem Computer, auf dem das Datenverwaltungsgateway installiert ist.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [HTTP-Connector](data-factory-http-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines HTTP-Datasets den **Typ** des Datasets auf **Http** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| relativeUrl | Eine relative URL zu der Ressource, die die Daten enthält. Wenn der Pfad nicht angegeben ist, wird nur die URL verwendet, die in der Definition des verknüpften Diensts angegeben ist. <br><br> Zum Erstellen von dynamischen URLs können Sie [Data Factory-Funktionen und -Systemvariablen](data-factory-functions-variables.md) verwenden. Beispiel: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`. | Nein |
| requestMethod | HTTP-Methode. Zulässige Werte: **GET** oder **POST** | Nein. Der Standardwert ist `GET`. |
| additionalHeaders | Zusätzliche HTTP-Anforderungsheader | Nein |
| requestBody | Text für die HTTP-Anforderung | Nein |
| format | Wenn Sie einfach **die Daten ohne Änderung von einem HTTP-Endpunkt abrufen** möchten, ohne sie zu analysieren, überspringen Sie diese Formateinstellungen. <br><br> Wenn der HTTP-Antwortinhalt während des Kopierens analysiert werden soll, werden die folgenden Formattypen unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Weitere Informationen finden Sie in den Abschnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format) und [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format). |Nein |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Folgende Typen werden unterstützt: **GZip**, **Deflate**, **BZip2** und **ZipDeflate**. Folgende Ebenen werden unterstützt: **Optimal** und **Fastest**. Weitere Informationen finden Sie unter [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nein |

#### <a name="example-using-the-get-default-method"></a>Beispiel: Verwenden der GET-Methode (Standard)

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>Beispiel: Verwenden der POST-Methode

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
Weitere Informationen finden Sie im Artikel zum [HTTP-Connector](data-factory-http-connector.md#dataset-properties).

### <a name="http-source-in-copy-activity"></a>HTTP-Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus einer HTTP-Quelle den **Quelltyp** der Kopieraktivität auf **HttpSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| -------- | ----------- | -------- |
| httpRequestTimeout | Das Timeout (TimeSpan) für die HTTP-Anforderung, um eine Antwort zu empfangen. Dabei handelt es sich um das Timeout zum Empfangen einer Antwort, nicht das Timeout zum Lesen von Antwortdaten. | Nein. Standardwert: 00:01:40 |


#### <a name="example"></a>Beispiel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Weitere Informationen finden Sie im Artikel zum [HTTP-Connector](data-factory-http-connector.md#copy-activity-properties).

## <a name="odata"></a>OData

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften OData-Diensts den **Typ** des verknüpften Diensts auf **OData** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| url |Die URL des OData-Diensts. |Ja |
| authenticationType |Typ der Authentifizierung für die Verbindung mit der OData-Quelle. <br/><br/> Mögliche Werte für den cloudbasierten OData-Dienst sind „Anonymous“, „Basic“ und „OAuth“ (beachten Sie, dass Azure Data Factory derzeit nur Azure Active Directory-basiertes OAuth unterstützt). <br/><br/> Mögliche Werte für lokales OData sind „Anonymous“, „Basic“ und „Windows“. |Ja |
| username |Geben Sie den Benutzernamen an, wenn Sie die Standardauthentifizierung (Basic) verwenden. |Ja (nur bei Verwendung der Standardauthentifizierung) |
| password |Geben Sie das Kennwort für das Benutzerkonto an, das Sie für den Benutzernamen angegeben haben. |Ja (nur bei Verwendung der Standardauthentifizierung) |
| authorizedCredential |Klicken Sie, wenn Sie OAuth verwenden, im Assistenten zum Kopieren in Data Factory bzw. im Editor auf die Schaltfläche **Autorisieren**, und geben Sie Ihre Anmeldeinformationen ein. Anschließend wird der Wert dieser Eigenschaft automatisch generiert. |Ja (nur bei Verwendung der OAuth-Authentifizierung) |
| gatewayName |Der Name des Gateways, das der Data Factory-Dienst zum Verbinden mit dem lokalen OData-Dienst verwenden soll. Geben Sie diesen nur an, wenn Sie Daten aus einer lokalen OData-Quelle kopieren. |Nein |

#### <a name="example---using-basic-authentication"></a>Beispiel: Verwenden der Standardauthentifizierung
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Beispiel: Verwenden der anonymen Authentifizierung

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Beispiel: Verwenden der Windows-Authentifizierung für den Zugriff auf lokale OData-Quellen

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Beispiel: Verwenden der OAuth-Authentifizierung für den Zugriff auf eine cloudbasierte OData-Quelle
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [OData-Connector](data-factory-odata-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines OData-Datasets den **Typ** des Datasets auf **ODataResource** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| path |Pfad zu der OData-Ressource |Nein |

#### <a name="example"></a>Beispiel

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [OData-Connector](data-factory-odata-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Relationale Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten von einer OData-Quelle den **Quelltyp** der Kopieraktivität auf **RelationalSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Beispiel | Erforderlich |
| --- | --- | --- | --- |
| Abfrage |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |"?$select=Name, Beschreibung&$top=5" |Nein |

#### <a name="example"></a>Beispiel

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

Weitere Informationen finden Sie im Artikel zum [OData-Connector](data-factory-odata-connector.md#copy-activity-properties).


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften ODBC-Diensts den **Typ** des verknüpften Diensts auf **OnPremisesOdbc** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| connectionString |Der nicht für den Zugriff bestimmte Teil der Anmeldeinformationen in der Verbindungszeichenfolge sowie optional verschlüsselte Anmeldeinformationen. Siehe Beispiele in den folgenden Abschnitten. |Ja |
| Anmeldeinformationen (credential) |Der zum Zugriff bestimmte Teil der Anmeldeinformationen in der Verbindungszeichenfolge. Er wird in einem treiberspezifischen Format in Eigenschaft und Wert angegeben. Beispiel: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |Nein |
| authenticationType |Typ der Authentifizierung für die Verbindung mit dem ODBC-Datenspeicher. Mögliche Werte: „Anonymous“ und „Basic“. |Ja |
| username |Geben Sie den Benutzernamen an, wenn Sie die Standardauthentifizierung (Basic) verwenden. |Nein |
| password |Geben Sie das Kennwort für das Benutzerkonto an, das Sie für den Benutzernamen angegeben haben. |Nein |
| gatewayName |Der Name des Gateways, das der Data Factory-Dienst zum Verbinden mit dem ODBC-Datenspeicher verwenden soll. |Ja |

#### <a name="example---using-basic-authentication"></a>Beispiel: Verwenden der Standardauthentifizierung

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Beispiel: Verwenden der Standardauthentifizierung mit verschlüsselten Anmeldeinformationen
Sie können die Anmeldeinformationen mithilfe des Cmdlets [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) verschlüsseln.

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>Beispiel: Verwenden der anonymen Authentifizierung

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [ODBC-Connector](data-factory-odbc-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines ODBC-Datasets den **Typ** des Datasets auf **RelationalTable** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| tableName |Der Name der Tabelle im ODBC-Datenspeicher. |Ja |


#### <a name="example"></a>Beispiel

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [ODBC-Connector](data-factory-odbc-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Relationale Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus einem ODBC-Datenspeicher den **Quelltyp** der Kopieraktivität auf **RelationalSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| Abfrage |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-Abfragezeichenfolge. Beispiel: `select * from MyTable`. |Ja |

#### <a name="example"></a>Beispiel

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Weitere Informationen finden Sie im Artikel zum [ODBC-Connector](data-factory-odbc-connector.md#copy-activity-properties).

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften Salesforce-Diensts den **Typ** des verknüpften Diensts auf **Salesforce** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| environmentUrl | Geben Sie die URL der Salesforce-Instanz an. <br><br> – Die Standard-URL lautet „https:\///login.salesforce.com“. <br> – Um Daten aus einer Sandbox zu kopieren, geben Sie „https://test.salesforce.com“ an. <br> - Geben Sie zum Kopieren von Daten aus der benutzerdefinierten Domäne z.B. „https://[Domäne].my.salesforce.com“ an. |Nein |
| username |Geben Sie einen Benutzernamen für das Benutzerkonto an. |Ja |
| password |Geben Sie ein Kennwort für das Benutzerkonto an. |Ja |
| securityToken |Geben Sie ein Sicherheitstoken für das Benutzerkonto an. Anweisungen zum Abrufen oder Zurücksetzen eines Sicherheitstokens finden Sie unter [Zurücksetzen Ihres Sicherheitstokens](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) . Allgemeine Informationen zu Sicherheitstoken finden Sie unter [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)(Sicherheit und die API). |Ja |

#### <a name="example"></a>Beispiel

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Salesforce-Connector](data-factory-salesforce-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines Salesforce-Datasets den **Typ** des Datasets auf **RelationalTable** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| tableName |Name der Tabelle in Salesforce. |Nein (wenn eine **Abfrage** von **RelationalSource** angegeben ist) |

#### <a name="example"></a>Beispiel

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Salesforce-Connector](data-factory-salesforce-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Relationale Quelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus Salesforce den **Quelltyp** der Kopieraktivität auf **RelationalSource** fest, und geben Sie im Abschnitt **source** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| Abfrage |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |Eine SQL-92-Abfrage oder eine Abfrage vom Typ [Salesforce Object Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Beispiel: `select * from MyTable__c`. |Nein (wenn **tableName** von **dataset** angegeben ist) |

#### <a name="example"></a>Beispiel



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

> [!IMPORTANT]
> Der Abschnitt „__c“ von „API Name“ wird für benutzerdefinierte Objekte benötigt.

Weitere Informationen finden Sie im Artikel zum [Salesforce-Connector](data-factory-salesforce-connector.md#copy-activity-properties).

## <a name="web-data"></a>Webdaten

### <a name="linked-service"></a>Verknüpfter Dienst
Legen Sie zum Definieren eines verknüpften Webdiensts den **Typ** des verknüpften Diensts auf **Web** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| url |URL der Webquelle |Ja |
| authenticationType |Anonym |Ja |


#### <a name="example"></a>Beispiel


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Webtabellen-Connector](data-factory-web-table-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Legen Sie zum Definieren eines Webdatasets den **Typ** des Datasets auf **WebTable** fest, und geben Sie im Abschnitt **typeProperties** die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type |Die Art des Datasets. Muss auf **WebTable** festgelegt sein. |Ja |
| path |Eine relative URL zu der Ressource, die die Tabelle enthält. |Nein. Wenn der Pfad nicht angegeben ist, wird nur die URL verwendet, die in der Definition des verknüpften Diensts angegeben ist. |
| Index |Der Index der Tabelle in der Ressource. Im Abschnitt „Abrufen des Indexes einer Tabelle auf einer HTML-Seite“ werden die Schritte zum Abrufen des Indexes einer Tabelle auf einer HTML-Seite beschrieben. |Ja |

#### <a name="example"></a>Beispiel

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Webtabellen-Connector](data-factory-web-table-connector.md#dataset-properties).

### <a name="web-source-in-copy-activity"></a>Webquelle in der Kopieraktivität
Legen Sie beim Kopieren von Daten aus einer Webtabelle den **Quellentyp** der Kopieraktivität auf **WebSource** fest. Wenn bei der Kopieraktivität die Quelle den Typ **WebSource** hat, werden derzeit keine zusätzlichen Eigenschaften unterstützt.

#### <a name="example"></a>Beispiel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "WebSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Weitere Informationen finden Sie im Artikel zum [Webtabellen-Connector](data-factory-web-table-connector.md#copy-activity-properties).

## <a name="compute-environments"></a>COMPUTE-UMGEBUNGEN
Die folgende Tabelle enthält eine Liste mit den Compute-Umgebungen, die von Data Factory unterstützt werden, und den Transformationsaktivitäten, die darin ausgeführt werden können. Klicken Sie auf den Link für die gewünschte Compute-Umgebung, um die JSON-Schemas für den verknüpften Dienst anzuzeigen und ihn mit einer Data Factory zu verknüpfen.

| Compute-Umgebung | activities |
| --- | --- |
| [Bedarfsgesteuerter HDInsight-Cluster](#on-demand-azure-hdinsight-cluster) oder [Eigener HDInsight-Cluster](#existing-azure-hdinsight-cluster) |[Benutzerdefinierte .NET-Aktivität](#net-custom-activity), [Hive-Aktivität](#hdinsight-hive-activity), [Pig-Aktivität](#hdinsight-pig-activity), [MapReduce-Aktivität](#hdinsight-mapreduce-activity), Hadoop-Streamingaktivität, [Spark-Aktivität](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[Benutzerdefinierte .NET-Aktivität](#net-custom-activity) |
| [Azure Machine Learning Studio (klassisch)](#azure-machine-learning-studio-classic) | [Batchausführungsaktivität für Azure Machine Learning Studio (klassisch)](#azure-machine-learning-studio-classic-batch-execution-activity), [Ressourcenaktualisierungsaktivität für Azure Machine Learning Studio (klassisch)](#azure-machine-learning-studio-classic-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Azure SQL-Datenbank](#azure-sql-database), [Azure Synapse Analytics](#azure-synapse-analytics), [SQL Server](#sql-server-stored-procedure) |[Gespeicherte Prozedur](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Bedarfsgesteuerter Azure HDInsight-Cluster
Der Azure Data Factory-Dienst kann zum Verarbeiten von Daten automatisch einen bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster erstellen. Der Cluster wird in derselben Region erstellt wie das Speicherkonto (Eigenschaft „linkedServiceName“ in JSON), das dem Cluster zugeordnet ist. Sie können für diesen verknüpften Dienst die folgenden Transformationsaktivitäten ausführen: [Benutzerdefinierte .NET-Aktivität](#net-custom-activity), [Hive-Aktivität](#hdinsight-hive-activity), [Pig-Aktivität](#hdinsight-pig-activity), [MapReduce-Aktivität](#hdinsight-mapreduce-activity), Hadoop-Streamingaktivität, [Spark-Aktivität](#hdinsight-spark-activity).

### <a name="linked-service"></a>Verknüpfter Dienst
Die folgende Tabelle enthält Beschreibungen der Eigenschaften, die in der Azure-JSON-Definition eines bedarfsgesteuerten verknüpften HDInsight-Diensts verwendet werden.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type |Legen Sie die Typeigenschaft auf **HDInsightOnDemand** fest. |Ja |
| clusterSize |Anzahl der Worker-/Datenknoten im Cluster. Der HDInsight-Cluster wird mit zwei Hauptknoten sowie der Anzahl der Workerknoten erstellt, die Sie für diese Eigenschaft angeben. Die Knoten haben die Größe Standard_D3, die vier Kerne aufweist. Ein Cluster mit vier Workerknoten nutzt also 24 Kerne (4 \* 4 = 16 für die Workerknoten + 2 \* 4 = 8 für die Hauptknoten). Ausführliche Informationen zum Standard_D3-Tarif finden Sie unter [Erstellen von Linux-basierten Hadoop-Clustern in HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). |Ja |
| timetolive |Die zulässige Leerlaufzeit für den bedarfsgesteuerten HDInsight-Cluster. Gibt an, wie lange der bedarfsgesteuerte HDInsight-Cluster nach dem Abschluss einer Aktivitätsausführung aktiv bleibt, wenn keine anderen aktiven Aufträge im Cluster vorhanden sind.<br/><br/>Beispiel: Wenn eine Aktivitätsausführung 6 Minuten dauert und „timetolive“ auf 5 Minuten festgelegt ist, bleibt der Cluster für 5 Minuten nach den 6 Minuten für die Verarbeitung der Aktivitätsausführung aktiv. Wenn eine weitere Aktivitätsausführung mit einem Zeitfenster von 6 Minuten ausgeführt wird, wird sie von demselben Cluster verarbeitet.<br/><br/>Das Erstellen eines bedarfsgesteuerten HDInsight-Clusters ist ein aufwändiger Vorgang (er kann eine Weile dauern). Verwenden Sie daher diese Einstellung bei Bedarf, um die Leistung einer Data Factory zu verbessern, indem Sie einen bedarfsgesteuerten HDInsight-Cluster wiederverwenden.<br/><br/>Wenn der timetolive-Wert auf 0 festgelegt wird, wird der Cluster gelöscht, sobald die Aktivitätsausführung verarbeitet wurde. Wenn Sie jedoch einen hohen Wert festlegen, wird der Cluster möglicherweise für einen zu langen Zeitraum im Leerlauf beibehalten, was hohe Kosten verursachen kann. Aus diesem Grund ist es wichtig, dass Sie den entsprechenden Wert basierend auf Ihren Anforderungen festlegen.<br/><br/>Wenn der Wert der Eigenschaft „timetolive“ ordnungsgemäß festgelegt wird, können mehrere Pipelines dieselbe Instanz des bedarfsgesteuerten HDInsight-Clusters verwenden. |Ja |
| version |Version des HDInsight-Clusters. Weitere Informationen finden Sie unter [Unterstützte HDInsight-Versionen in Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Nein |
| linkedServiceName |Der verknüpfte Azure Storage-Dienst, den der bedarfsgesteuerte Cluster zum Speichern und Verarbeiten von Daten nutzt. <p>Das Erstellen eines bedarfsgesteuerten HDInsight-Clusters, der Azure Data Lake Store als Speicher verwendet, ist derzeit nicht möglich. Wenn Sie die Ergebnisdaten der HDInsight-Verarbeitung in einer Azure Data Lake Store-Instanz speichern möchten, kopieren Sie die Daten mittels einer Kopieraktivität aus der Azure Blob Storage-Instanz in die Azure Data Lake Store-Instanz.</p>  | Ja |
| additionalLinkedServiceNames |Gibt zusätzliche Speicherkonten für den verknüpften HDInsight-Dienst an, damit der Data Factory-Dienst diese für Sie registrieren kann. |Nein |
| osType |Typ des Betriebssystems. Zulässige Werte sind: Windows (Standard) und Linux |Nein |
| hcatalogLinkedServiceName |Der Name des mit Azure SQL verknüpften Diensts, der auf die HCatalog-Datenbank verweist. Der bedarfsgesteuerte HDInsight-Cluster wird mit Azure SQL-Datenbank als Metastore erstellt. |Nein |

### <a name="json-example"></a>JSON-Beispiel
Die folgende JSON definiert einen bedarfsgesteuerten Linux-basierten mit HDInsight verknüpften Dienst. Der Data Factory-Dienst erstellt bei der Verarbeitung eines Datenslices automatisch einen **Linux-basierten** HDInsight-Cluster.

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel [Verknüpfte Computedienste](data-factory-compute-linked-services.md).

## <a name="existing-azure-hdinsight-cluster"></a>Vorhandener Azure HDInsight-Cluster
Sie können einen verknüpften Azure HDInsight-Dienst erstellen, um Ihren eigenen HDInsight-Cluster für Data  Factory zu registrieren. Sie können für diesen verknüpften Dienst die folgenden Datentransformationsaktivitäten ausführen: [Benutzerdefinierte .NET-Aktivität](#net-custom-activity), [Hive-Aktivität](#hdinsight-hive-activity), [Pig-Aktivität](#hdinsight-pig-activity), [MapReduce-Aktivität](#hdinsight-mapreduce-activity), Hadoop-Streamingaktivität, [Spark-Aktivität](#hdinsight-spark-activity).

### <a name="linked-service"></a>Verknüpfter Dienst
Die folgende Tabelle enthält Beschreibungen der Eigenschaften, die in der Azure-JSON-Definition eines verknüpften Azure HDInsight-Diensts verwendet werden.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type |Legen Sie die Typeigenschaft auf **HDInsight** fest. |Ja |
| clusterUri |Der URI des HDInsight-Clusters. |Ja |
| username |Geben Sie den Namen des Benutzers ein, der mit einem vorhandenen HDInsight-Cluster verbunden werden soll. |Ja |
| password |Geben Sie ein Kennwort für das Benutzerkonto an. |Ja |
| linkedServiceName | Der Name des verknüpften Azure Storage-Diensts für die von diesem HDInsight-Cluster verwendete Azure Blob Storage-Instanz. <p>Derzeit können Sie keinen verknüpften Azure Data Lake Store-Dienst für diese Eigenschaft angeben. Sie können auf Daten in Azure Data Lake Store über Hive-/Pig-Skripts zugreifen, wenn der HDInsight-Cluster Zugriff auf die Data Lake Store-Instanz hat. </p>  |Ja |

Die unterstützten HDInsight-Versionen finden Sie unter [Unterstützte HDInsight-Versionen](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory).

#### <a name="json-example"></a>JSON-Beispiel

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Azure Batch
Sie können einen verknüpften Azure Batch-Dienst erstellen, um einen Batch-Pool mit virtuellen Computern für eine Data Factory zu registrieren. Benutzerdefinierte .NET-Aktivitäten können mit Azure Batch oder Azure HDInsight ausgeführt werden. Sie können für diesen verknüpften Dienst eine [benutzerdefinierte .NET-Aktivität](#net-custom-activity) ausführen.

### <a name="linked-service"></a>Verknüpfter Dienst
Die folgende Tabelle enthält Beschreibungen der Eigenschaften, die in der Azure-JSON-Definition eines verknüpften Azure Batch-Diensts verwendet werden.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type |Legen Sie die Typeigenschaft auf **AzureBatch** fest. |Ja |
| .<Name der Region |Der Name des Azure Batch-Kontos. |Ja |
| accessKey |Der Zugriffsschlüssel für das Azure Batch-Konto. |Ja |
| poolName |Der Name des Pools mit virtuellen Computern. |Ja |
| linkedServiceName |Der Name des verknüpften Azure Storage-Diensts, der diesem verknüpften Azure Batch-Dienst zugeordnet ist. Dieser verknüpfte Dienst wird für Stagingdateien verwendet, die für die Ausführung der Aktivität und zum Speichern der Protokolle zur Aktivitätsausführung benötigt werden. |Ja |


#### <a name="json-example"></a>JSON-Beispiel

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klassisch)
Sie können einen mit Azure Machine Learning Studio (klassisch) verknüpften Dienst erstellen, um einen Batchbewertungsendpunkt von Studio (klassisch) für eine Data Factory zu registrieren. Zwei Datentransformationsaktivitäten, die für diesen verknüpften Dienst ausgeführt werden können: [Batchausführungsaktivität für Azure Machine Learning Studio (klassisch)](#azure-machine-learning-studio-classic-batch-execution-activity), [Ressourcenaktualisierungsaktivität für Azure Machine Learning Studio (klassisch)](#azure-machine-learning-studio-classic-update-resource-activity)

### <a name="linked-service"></a>Verknüpfter Dienst
Die folgende Tabelle enthält Beschreibungen der Eigenschaften, die in der Azure-JSON-Definition eines verknüpften Diensts für Studio (klassisch) verwendet werden.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type |Legen Sie die type-Eigenschaft auf **AzureML** fest. |Ja |
| mlEndpoint |Die Batchbewertungs-URL. |Ja |
| apiKey |Die veröffentlichte API des Arbeitsbereichsmodells. |Ja |

#### <a name="json-example"></a>JSON-Beispiel

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
Sie erstellen einen mit **Azure Data Lake Analytics** verknüpften Dienst, um einen Azure Data Lake Analytics-Computedienst mit einer Azure Data Factory zu verknüpfen, bevor Sie die [Data Lake Analytics-U-SQL-Aktivität](data-factory-usql-activity.md) in einer Pipeline verwenden.

### <a name="linked-service"></a>Verknüpfter Dienst

Die folgende Tabelle enthält Beschreibungen der Eigenschaften, die in der JSON-Definition eines verknüpften Azure Data Lake Analytics-Diensts verwendet werden.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type |Legen Sie die type-Eigenschaft auf **AzureDataLakeAnalytics** fest. |Ja |
| .<Name der Region |Name des Azure Data Lake Analytics-Kontos. |Ja |
| dataLakeAnalyticsUri |URI des Azure Data Lake Analytics-Kontos. |Nein |
| authorization |Der Autorisierungscode wird automatisch abgerufen, nachdem Sie im Data Factory-Editor auf die Schaltfläche **Autorisieren** geklickt und die OAuth-Anmeldung abgeschlossen haben. |Ja |
| subscriptionId |Azure-Abonnement-ID |Nein (falls nicht angegeben, wird das Abonnement der Data Factory verwendet). |
| resourceGroupName |Azure-Ressourcengruppenname |Nein (falls nicht angegeben, wird die Ressourcengruppe der Data Factory verwendet). |
| sessionID |Sitzungs-ID aus der OAuth-Autorisierungssitzung. Jede Sitzungs-ID ist eindeutig und darf nur einmal verwendet werden. Beim Verwenden des Data Factory-Editors wird diese ID automatisch generiert. |Ja |


#### <a name="json-example"></a>JSON-Beispiel
Das folgende Beispiel enthält eine JSON-Definition für einen mit Azure Data Lake Analytics verknüpften Dienst.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="sql-server-stored-procedure"></a>SQL Server-Aktivität „Gespeicherte Prozedur“

Sie erstellen einen mit SQL Server verknüpften Dienst und verwenden ihn mit der [Aktivität „Gespeicherte Prozedur“](data-factory-stored-proc-activity.md) zum Aufrufen einer gespeicherten Prozedur in einer Data Factory-Pipeline.

### <a name="linked-service"></a>Verknüpfter Dienst
Sie erstellen einen verknüpften Dienst des Typs **OnPremisesSqlServer**, um eine SQL Server-Datenbank mit einer Data Factory zu verknüpfen. Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit SQL Server verknüpften Dienst spezifisch sind.

Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit SQL Server verknüpften Dienst spezifisch sind.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type |Legen Sie die type-Eigenschaft auf **OnPremisesSqlServer** fest. |Ja |
| connectionString |Geben Sie „connectionString“-Informationen an, die zum Herstellen einer Verbindung mit der SQL Server-Datenbank mithilfe der SQL- oder Windows-Authentifizierung benötigt werden. |Ja |
| gatewayName |Name des Gateways, das der Data Factory-Dienst zum Herstellen einer Verbindung mit der SQL Server-Datenbank verwenden soll. |Ja |
| username |Geben Sie den Benutzernamen an, wenn Sie die Windows-Authentifizierung verwenden. Beispiel: **Domainname\\username**. |Nein |
| password |Geben Sie das Kennwort für das Benutzerkonto an, das Sie für den Benutzernamen angegeben haben. |Nein |

Sie können Anmeldeinformationen mithilfe des Cmdlets **New-AzDataFactoryEncryptValue** verschlüsseln und wie im folgenden Beispiel gezeigt in der Verbindungszeichenfolge verwenden (**EncryptedCredential**-Eigenschaft):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Beispiel: JSON-Code für die SQL Server-Authentifizierung

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Beispiel: JSON-Code für die Windows-Authentifizierung

Wenn Benutzername und Kennwort angegeben werden, werden diese Informationen vom Gateway genutzt, um die Identität des angegebenen Benutzerkontos zu übernehmen und eine Verbindung mit der SQL Server-Datenbank herzustellen. Andernfalls stellt das Gateway direkt im Sicherheitskontext des Gateways (mit dessen Startkonto) eine Verbindung mit der SQL Server-Instanz her.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel zum [SQL Server-Connector](data-factory-sqlserver-connector.md#linked-service-properties).

## <a name="data-transformation-activities"></a>DATENTRANSFORMATIONSAKTIVITÄTEN

Aktivität | BESCHREIBUNG
-------- | -----------
[HDInsight Hive-Aktivität](#hdinsight-hive-activity) | Die HDInsight Hive-Aktivität in einer Data Factory-Pipeline wendet Hive-Abfragen auf Ihren eigenen oder bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster an.
[HDInsight Pig-Aktivität](#hdinsight-pig-activity) | Die HDInsight Pig-Aktivität in einer Data Factory-Pipeline wendet Pig-Abfragen auf Ihren eigenen oder bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster an.
[HDInsight MapReduce-Aktivität](#hdinsight-mapreduce-activity) | Die HDInsight MapReduce-Aktivität in einer Data Factory-Pipeline wendet MapReduce-Programme auf Ihren eigenen oder bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster an.
[HDInsight-Streamingaktivität](#hdinsight-streaming-activity) | Die HDInsight-Streamingaktivität in einer Data Factory-Pipeline wendet Hadoop-Streamingprogramme auf Ihren eigenen oder bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster an.
[HDInsight Spark-Aktivität](#hdinsight-spark-activity) | Die HDInsight Spark-Aktivität in einer Data Factory-Pipeline führt Spark-Programme in Ihrem eigenen HDInsight-Cluster aus.
[Batchausführungsaktivität für Azure Machine Learning Studio (klassisch)](#azure-machine-learning-studio-classic-batch-execution-activity) | Azure Data Factory ermöglicht die einfache Erstellung von Pipelines, die einen veröffentlichten Webdienst für Studio (klassisch) für Predictive Analytics nutzen. Mithilfe der Batchausführungsaktivität in einer Azure Data Factory-Pipeline können Sie einen Webdienst für Studio (klassisch) aufrufen, um Vorhersagen zu den Daten im Batch zu machen.
[Ressourcenaktualisierungsaktivität für Azure Machine Learning Studio (klassisch)](#azure-machine-learning-studio-classic-update-resource-activity) | Im Laufe der Zeit müssen die Vorhersagemodelle in den Bewertungsexperimenten für Azure Machine Learning Studio (klassisch) mit neuen Eingabedatasets neu trainiert werden. Wenn Sie mit dem erneuten Trainieren fertig sind, sollten Sie den Bewertungswebdienst mit dem neu trainierten Machine Learning-Modell aktualisieren. Verwenden Sie die Ressourcenaktualisierungsaktivität, um den Webdienst mit dem neu trainierten Modell zu aktualisieren.
[Aktivität „Gespeicherte Prozedur“](#stored-procedure-activity) | Sie können die Aktivität „Gespeicherte Prozedur“ in einer Data Factory-Pipeline verwenden, um eine gespeicherte Prozedur in einem der folgenden Datenspeicher aufzurufen: Azure SQL-Datenbank, Azure Synapse Analytics, SQL Server-Datenbank in Ihrem Unternehmen oder auf einem virtuellen Azure-Computer.
[U-SQL-Aktivität für Data Lake Analytics](#data-lake-analytics-u-sql-activity) | Die U-SQL-Aktivität für Data Lake Analytics wendet ein U-SQL-Skript auf einen Azure Data Lake Analytics-Cluster an.
[Benutzerdefinierte .NET-Aktivität](#net-custom-activity) | Wenn Sie Daten auf eine Weise transformieren/verarbeiten müssen, die von Data Factory nicht unterstützt wird, können Sie eine benutzerdefinierte Aktivität mit Ihrer eigenen Datenverarbeitungslogik erstellen und in der Pipeline verwenden. Sie können die benutzerdefinierte .NET-Aktivität so konfigurieren, dass sie entweder mithilfe eines Azure Batch-Diensts oder eines Azure HDInsight-Clusters ausgeführt wird.


## <a name="hdinsight-hive-activity"></a>HDInsight Hive-Aktivität
Sie können in der JSON-Definition einer Hive-Aktivität die folgenden Eigenschaften angeben. Die type-Eigenschaft für die Aktivität muss wie folgt lauten: **HDInsightHive**. Es ist erforderlich, zuerst einen verknüpften HDInsight-Dienst zu erstellen und dessen Namen als Wert für die **linkedServiceName**-Eigenschaft anzugeben. Die folgenden Eigenschaften werden im Abschnitt **typeProperties** unterstützt, wenn Sie den Typ der Aktivität auf HDInsightHive festlegen:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| script |Angabe des Hive-Skripts inline |Nein |
| script path |Speichern Sie das Hive-Skript in Azure Blob Storage, und geben Sie den Pfad zur Datei an. Verwenden Sie die Eigenschaft "script" oder "scriptPath". Beide können nicht zusammen verwendet werden. Beim Dateinamen muss die Groß-/Kleinschreibung beachtet werden. |Nein |
| defines |Geben Sie Parameter als Schlüssel-Wert-Paare für Verweise innerhalb des Hive-Skripts mit "hiveconf" an. |Nein |

Diese Typeigenschaften gelten spezifisch für die Hive-Aktivität. Andere Eigenschaften (außerhalb des Abschnitts „typeProperties“) werden für alle Aktivitäten unterstützt.

### <a name="json-example"></a>JSON-Beispiel
Mit dem folgenden JSON-Code wird eine HDInsight Hive-Aktivität in einer Pipeline definiert.

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
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
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```

Weitere Informationen finden Sie im Artikel zur [Hive-Aktivität](data-factory-hive-activity.md).

## <a name="hdinsight-pig-activity"></a>HDInsight Pig-Aktivität
Sie können in der JSON-Definition einer Pig-Aktivität die folgenden Eigenschaften angeben. Die type-Eigenschaft für die Aktivität muss wie folgt lauten: **HDInsightPig**. Es ist erforderlich, zuerst einen verknüpften HDInsight-Dienst zu erstellen und dessen Namen als Wert für die **linkedServiceName**-Eigenschaft anzugeben. Die folgenden Eigenschaften werden im Abschnitt **typeProperties** unterstützt, wenn Sie den Typ der Aktivität auf HDInsightPig festlegen:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| script |Angabe des Pig-Skripts inline |Nein |
| script path |Speichern Sie das Pig-Skript in Azure Blob Storage, und geben Sie den Pfad zur Datei an. Verwenden Sie die Eigenschaft "script" oder "scriptPath". Beide können nicht zusammen verwendet werden. Beim Dateinamen muss die Groß-/Kleinschreibung beachtet werden. |Nein |
| defines |Geben Sie Parameter als Schlüssel-Wert-Paare für Verweise innerhalb des Pig-Skripts an. |Nein |

Diese Typeigenschaften gelten spezifisch für die Pig-Aktivität. Andere Eigenschaften (außerhalb des Abschnitts „typeProperties“) werden für alle Aktivitäten unterstützt.

### <a name="json-example"></a>JSON-Beispiel

```json
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

Weitere Informationen finden Sie im Artikel über die Pig-Aktivität.

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-Aktivität
Sie können in der JSON-Definition einer MapReduce-Aktivität die folgenden Eigenschaften angeben. Die type-Eigenschaft für die Aktivität muss wie folgt lauten: **HDInsightMapReduce**. Es ist erforderlich, zuerst einen verknüpften HDInsight-Dienst zu erstellen und dessen Namen als Wert für die **linkedServiceName**-Eigenschaft anzugeben. Die folgenden Eigenschaften werden im Abschnitt **typeProperties** unterstützt, wenn Sie den Typ der Aktivität auf HDInsightMapReduce festlegen:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| jarLinkedService | Name des verknüpften Diensts für die Azure Storage-Instanz, in der die JAR-Datei enthalten ist. | Ja |
| jarFilePath | Pfad zur JAR-Datei in der Azure Storage-Instanz. | Ja |
| className | Name der Hauptklasse in der JAR-Datei. | Ja |
| Argumente | Liste mit kommagetrennten Argumenten für das MapReduce-Programm. Zur Laufzeit werden ein paar zusätzliche Argumente aus dem MapReduce-Framework angezeigt (z.B.: mapreduce.job.tags). Um Ihre Argumente mit den MapReduce-Argumenten zu unterscheiden, sollten Sie erwägen, sowohl die Option als auch den Wert als Argumente zu verwenden, wie im folgenden Beispiel gezeigt („-s“, „--input“, „--output“ usw. sind Optionen, auf die unmittelbar die Werte folgen). | Nein |

### <a name="json-example"></a>JSON-Beispiel

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
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
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

Weitere Informationen finden Sie im Artikel zur [MapReduce-Aktivität](data-factory-map-reduce.md).

## <a name="hdinsight-streaming-activity"></a>HDInsight-Streamingaktivität
Sie können in der JSON-Definition einer Hadoop-Streamingaktivität die folgenden Eigenschaften angeben. Die type-Eigenschaft für die Aktivität muss wie folgt lauten: **HDInsightStreaming**. Es ist erforderlich, zuerst einen verknüpften HDInsight-Dienst zu erstellen und dessen Namen als Wert für die **linkedServiceName**-Eigenschaft anzugeben. Die folgenden Eigenschaften werden im Abschnitt **typeProperties** unterstützt, wenn Sie den Typ der Aktivität auf HDInsightStreaming festlegen:

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| mapper | Name der ausführbaren Zuordnungsdatei (Mapper). Im Beispiel ist „cat.exe“ die ausführbare Zuordnungsdatei.|
| reducer | Name der ausführbaren Reduzierungsdatei (Reducer). Im Beispiel ist „wc.exe“ die ausführbare Reduzierungsdatei. |
| input | Eingabedatei (einschließlich Speicherort) für die Zuordnung. Im Beispiel `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"` ist „adfsample“ der Blobcontainer, „example/data/Gutenberg“ der Ordner und „davinci.txt“ das Blob. |
| output | Ausgabedatei (einschließlich Speicherort) für die Reduzierung. Die Ausgabe des Hadoop-Streamingauftrags wird an dem für diese Eigenschaft angegebenen Speicherort geschrieben. |
| filePaths | Pfade zu den ausführbaren Zuordnungs- und Reduzierungsdateien. Im Beispiel „adfsample/example/apps/wc.exe“ ist „adfsample“ der Blob-Container, „example/apps“ der Ordner und „wc.exe“ die ausführbare Datei. |
| fileLinkedService | Verknüpfter Azure Storage-Dienst, der den Azure-Speicher mit den im Abschnitt „filePaths“ angegebenen Dateien darstellt. |
| Argumente | Liste mit kommagetrennten Argumenten für das MapReduce-Programm. Zur Laufzeit werden ein paar zusätzliche Argumente aus dem MapReduce-Framework angezeigt (z.B.: mapreduce.job.tags). Um Ihre Argumente mit den MapReduce-Argumenten zu unterscheiden, sollten Sie erwägen, sowohl die Option als auch den Wert als Argumente zu verwenden, wie im folgenden Beispiel gezeigt („-s“, „--input“, „--output“ usw. sind Optionen, auf die unmittelbar die Werte folgen). |
| getDebugInfo | Ein optionales Element. Wenn die Eigenschaft auf "Failure" festgelegt wird, werden die Protokolle nur bei Fehlern heruntergeladen. Wenn die Eigenschaft auf "All" festgelegt wird, werden Protokolle immer heruntergeladen, unabhängig vom Ausführungsstatus. |

> [!NOTE]
> Es ist erforderlich, in Bezug auf die Hadoop-Streamingaktivität für die **outputs**-Eigenschaft ein Ausgabedataset festzulegen. Dieses Dataset kann ein Dummydataset sein, das für den Pipelinezeitplan benötigt wird (stündlich, täglich usw.). Falls für die Aktivität keine Eingabe erforderlich ist, können Sie das Angeben eines Eingabedatasets für die Aktivität über die **inputs**-Eigenschaft überspringen.

## <a name="json-example"></a>JSON-Beispiel

```json
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

Weitere Informationen finden Sie im Artikel zur [Hadoop-Streamingaktivität](data-factory-hadoop-streaming-activity.md).

## <a name="hdinsight-spark-activity"></a>HDInsight Spark-Aktivität
Sie können in der JSON-Definition einer Spark-Aktivität die folgenden Eigenschaften angeben. Die type-Eigenschaft für die Aktivität muss wie folgt lauten: **HDInsightSpark**. Es ist erforderlich, zuerst einen verknüpften HDInsight-Dienst zu erstellen und dessen Namen als Wert für die **linkedServiceName**-Eigenschaft anzugeben. Die folgenden Eigenschaften werden im Abschnitt **typeProperties** unterstützt, wenn Sie den Typ der Aktivität auf HDInsightSpark festlegen:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| -------- | ----------- | -------- |
| rootPath | Der Azure-Blobcontainer und -ordner mit der Spark-Datei. Beim Dateinamen muss die Groß-/Kleinschreibung beachtet werden. | Ja |
| entryFilePath | Der relative Pfad zum Stammordner des Spark-Codes bzw. -Pakets. | Ja |
| className | Die Java-/Spark-Hauptklasse der Anwendung. | Nein |
| Argumente | Eine Liste der Befehlszeilenargumente für das Spark-Programm. | Nein |
| proxyUser | Das Benutzerkonto, dessen Identität angenommen werden soll, um das Spark-Programm auszuführen. | Nein |
| sparkConfig | Eigenschaften der Spark-Konfiguration. | Nein |
| getDebugInfo | Gibt an, ob die Spark-Protokolldateien in den Azure-Speicher kopiert werden, der vom HDInsight-Cluster verwendet (oder) von sparkJobLinkedService angegeben wird. Zulässige Werte: „None“, „Always“ oder „Failure“. Standardwert: Keine. | Nein |
| sparkJobLinkedService | Der verknüpfte Azure Storage-Dienst, der die Datei sowie die Abhängigkeiten und Protokolle für den Spark-Auftrag enthält.  Wenn Sie für diese Eigenschaft keinen Wert angeben, wird der Speicher verwendet, der dem HDInsight-Cluster zugeordnet ist. | Nein |

### <a name="json-example"></a>JSON-Beispiel

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
Beachten Sie folgende Punkte:

- Die **type**-Eigenschaft ist auf **HDInsightSpark** festgelegt.
- **rootPath** ist auf **adfspark\\pyFiles** festgelegt, wobei „adfspark“ den Azure-Blobcontainer und „pyFiles“ einen Dateiordner in diesem Container darstellt. In diesem Beispiel ist Azure Blob Storage der dem Spark-Cluster zugeordnete Speicher. Sie können die Datei auf einen anderen Azure-Speicher hochladen. Erstellen Sie in diesem Fall einen verknüpften Azure Storage-Dienst, der das Speicherkonto mit der Data Factory verknüpft. Geben Sie dann den Namen des verknüpften Diensts als Wert für die Eigenschaft **sparkJobLinkedService** an. Details zu dieser und anderen von der Spark-Aktivität unterstützten Eigenschaften finden Sie unter „Eigenschaften von Spark-Aktivitäten“.
- **entryFilePath** ist auf **test.py** festgelegt. Dies ist die Python-Datei.
- Die **getDebugInfo**-Eigenschaft ist auf **Always** festgelegt, das heißt, Protokolldateien werden in jedem Fall (Erfolg oder Fehler) erstellt.

    > [!IMPORTANT]
    > Es wird empfohlen, diese Eigenschaft in einer Produktionsumgebung nicht auf „Always“ festzulegen, sofern Sie kein Problem behandeln möchten.
- Der Abschnitt **outputs** weist ein Ausgabedataset auf. Sie müssen in jedem Fall ein Ausgabedataset angeben, selbst wenn das Spark-Programm keine Ausgabe erzeugt. Das Ausgabedataset bestimmt den Zeitplan für die Pipeline (stündlich, täglich usw.).

Weitere Informationen zur Aktivität finden Sie im Artikel zur [Spark-Aktivität](data-factory-spark.md).

## <a name="azure-machine-learning-studio-classic-batch-execution-activity"></a>Batchausführungsaktivität für Azure Machine Learning Studio (klassisch)
In der JSON-Definition einer Batchausführungsaktivität für Azure Machine Learning Studio (klassisch) können Sie die folgenden Eigenschaften angeben. Die type-Eigenschaft für die Aktivität muss wie folgt lauten: **AzureMLBatchExecution**. Es ist erforderlich, zuerst einen verknüpften Dienst für Studio (klassisch) zu erstellen und dessen Namen als Wert für die **linkedServiceName**-Eigenschaft anzugeben. Die folgenden Eigenschaften werden im Abschnitt **typeProperties** unterstützt, wenn Sie den Typ der Aktivität auf AzureMLBatchExecution festlegen:

Eigenschaft | BESCHREIBUNG | Erforderlich
-------- | ----------- | --------
webServiceInput | Das Dataset, das als Eingabe für den Webdienst für Studio (klassisch) übergeben wird. Dieses Dataset muss auch in die Eingaben für die Aktivität eingebunden werden. |Verwenden Sie entweder webServiceInput oder webServiceInputs. |
webServiceInputs | Geben Sie Datasets an, die als Eingabe für den Webdienst für Studio (klassisch) übergeben werden. Wenn der Webdienst mehrere Eingaben akzeptiert, verwenden Sie anstelle der webServiceInput-Eigenschaft die webServiceInputs-Eigenschaft. Datasets, auf die **webServiceInputs** verweist, müssen auch in der Aktivität **inputs** enthalten sein. | Verwenden Sie entweder webServiceInput oder webServiceInputs. |
webServiceOutputs | Die Datasets, die als Eingabe für den Webdienst für Studio (klassisch) übergeben werden. Der Webdienst gibt die Ausgabedaten in diesem Dataset zurück. | Ja |
globalParameters | Geben Sie Werte für die Webdienstparameter in diesem Abschnitt ein. | Nein |

### <a name="json-example"></a>JSON-Beispiel
In diesem Beispiel verfügt die Aktivität über das Dataset **MLSqlInput** als Eingabe und **MLSqlOutput** als Ausgabe. **MLSqlInput** wird als Eingabe an den Webdienst übergeben, indem die **webServiceInput**-JSON-Eigenschaft verwendet wird. **MLSqlOutput** wird als Ausgabe an den Webdienst übergeben, indem die **webServiceOutputs**-JSON-Eigenschaft verwendet wird.

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

Im JSON-Beispiel verwendet der bereitgestellte Webdienst für Studio (klassisch) ein Reader- und ein Writer-Modul zum Lesen und Schreiben von Daten aus und in Azure SQL-Datenbank. Dieser Webdienst stellt die vier folgenden Parameter bereit:  „Database server name“, „Database name“, „Server user account name“ und „Server user account password“.

> [!NOTE]
> Nur Eingaben und Ausgaben der AzureMLBatchExecution-Aktivität können als Parameter an den Webdienst übergeben werden. Im JSON-Codeausschnitt oben ist „MLSqlInput“ beispielsweise eine Eingabe für die AzureMLBatchExecution-Aktivität, die über den webServiceInput-Parameter als Eingabe an den Webdienst übergeben wird.

## <a name="azure-machine-learning-studio-classic-update-resource-activity"></a>Ressourcenaktualisierungsaktivität für Azure Machine Learning Studio (klassisch)
In der JSON-Definition einer Ressourcenaktualisierungsaktivität für Azure Machine Learning (klassisch) können Sie die folgenden Eigenschaften angeben. Die type-Eigenschaft für die Aktivität muss wie folgt lauten: **AzureMLUpdateResource**. Es ist erforderlich, zuerst einen verknüpften Dienst für Studio (klassisch) zu erstellen und dessen Namen als Wert für die **linkedServiceName**-Eigenschaft anzugeben. Die folgenden Eigenschaften werden im Abschnitt **typeProperties** unterstützt, wenn Sie den Typ der Aktivität auf AzureMLUpdateResource festlegen:

Eigenschaft | BESCHREIBUNG | Erforderlich
-------- | ----------- | --------
trainedModelName | Name des erneut trainierten Modells. | Ja |
trainedModelDatasetName | Dataset, das auf die iLearner-Datei verweist, die vom Vorgang für das erneute Trainieren zurückgegeben wird. | Ja |

### <a name="json-example"></a>JSON-Beispiel
Die Pipeline enthält zwei Aktivitäten: **AzureMLBatchExecution** und **AzureMLUpdateResource**. Die Batchausführungsaktivität für Studio (klassisch) verwendet die Trainingsdaten als Eingabe und erzeugt eine iLearner-Datei als Ausgabe. Die Aktivität ruft den Trainingswebdienst (das als Webdienst bereitgestellte Trainingsexperiment) mit den Trainingseingabedaten auf und empfängt die iLearner-Datei vom Webdienst. „placeholderBlob“ ist nur ein Platzhalter für ein Ausgabedataset, das für den Azure Data Factory-Dienst zum Ausführen der Pipeline erforderlich ist.


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>U-SQL-Aktivität für Data Lake Analytics
Sie können in der JSON-Definition einer U-SQL-Aktivität die folgenden Eigenschaften angeben. Die type-Eigenschaft für die Aktivität muss wie folgt lauten: **DataLakeAnalyticsU-SQL**. Es ist erforderlich, einen verknüpften Azure Data Lake Analytics-Dienst zu erstellen und dessen Namen als Wert für die **linkedServiceName**-Eigenschaft anzugeben. Die folgenden Eigenschaften werden im Abschnitt **typeProperties** unterstützt, wenn Sie den Typ der Aktivität auf DataLakeAnalyticsU-SQL festlegen:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| scriptPath |Der Pfad zum Ordner, der das U-SQL-Skript enthält. Beim Dateinamen wird Groß-/Kleinschreibung unterschieden. |Nein (wenn script verwendet wird) |
| scriptLinkedService |Verknüpfter Dienst, der den Speicher, der das Skript enthält, mit der Data Factory verknüpft. |Nein (wenn script verwendet wird) |
| script |Geben Sie anstelle von scriptPath und scriptLinkedService ein Inlineskript an. Beispiel: „script“: „CREATE DATABASE test“. |Nein (wenn scriptPath and scriptLinkedService verwendet werden) |
| degreeOfParallelism |Die maximale Anzahl von Knoten, die zum Ausführen des Auftrags gleichzeitig verwendet werden. |Nein |
| priority |Bestimmt, welche der in der Warteschlange befindlichen Aufträge als erstes ausgeführt werden. Je niedriger die Zahl, desto höher die Priorität. |Nein |
| parameters |Parameter für das U-SQL-Skript |Nein |

### <a name="json-example"></a>JSON-Beispiel

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
        "activities":
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs":
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

Weitere Informationen finden Sie unter [U-SQL-Aktivität für Data Lake Analytics](data-factory-usql-activity.md).

## <a name="stored-procedure-activity"></a>Aktivität „Gespeicherte Prozedur“
Sie können in der JSON-Definition einer Aktivität vom Typ „Gespeicherte Prozedur“ die folgenden Eigenschaften angeben. Die type-Eigenschaft für die Aktivität muss wie folgt lauten: **SqlServerStoredProcedure**. Es ist erforderlich, einen der folgenden verknüpften Dienste zu erstellen und den Namen des verknüpften Diensts als Wert für die **linkedServiceName**-Eigenschaft anzugeben:

- SQL Server
- Azure SQL-Datenbank
- Azure Synapse Analytics

Die folgenden Eigenschaften werden im Abschnitt **typeProperties** unterstützt, wenn Sie den Typ der Aktivität auf SqlServerStoredProcedure festlegen:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| storedProcedureName |Geben Sie den Namen der gespeicherten Prozedur in der Azure SQL-Datenbank- oder Azure Synapse Analytics-Instanz an, die durch den verknüpften Dienst dargestellt wird, der von der Ausgabetabelle verwendet wird. |Ja |
| storedProcedureParameters |Geben Sie Werte für Parameter der gespeicherten Prozedur an. Wenn Sie für einen Parameter Null übergeben müssen, verwenden Sie die folgende Syntax: "param1": null (nur Kleinbuchstaben). Das folgende Beispiel veranschaulicht die Verwendung dieser Eigenschaft. |Nein |

Wenn Sie ein Eingabedataset angeben, muss es (im Status „Bereit“) verfügbar sein, damit die Aktivität „Gespeicherte Prozedur“ ausgeführt wird. Das Eingabedataset kann nicht als Parameter in der gespeicherten Prozedur genutzt werden. Es wird nur verwendet, um vor dem Start der Aktivität „Gespeicherte Prozedur“ die Abhängigkeit zu überprüfen. Sie müssen ein Ausgabedataset für eine Aktivität „Gespeicherte Prozedur“ angeben.

Das Ausgabedataset gibt den **Zeitplan** für die Aktivität „Gespeicherte Prozedur“ an (stündlich, wöchentlich, monatlich usw.). Das Ausgabedataset muss einen **verknüpften Dienst** verwenden, der auf eine Azure SQL-Datenbank, eine Azure Synapse Analytics-Instanz oder eine SQL Server-Datenbank verweist, in der die gespeicherte Prozedur ausgeführt werden soll. Das Ausgabedataset kann verwendet werden, um das Ergebnis der gespeicherten Prozedur für die nachfolgende Verarbeitung durch eine andere Aktivität in der Pipeline zu übergeben ([Verketten von Aktivitäten](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)). Data Factory schreibt die Ausgabe einer gespeicherten Prozedur jedoch nicht automatisch in dieses Dataset. Die gespeicherte Prozedur schreibt die Ausgabe in eine SQL-Tabelle, auf die das Ausgabedataset verweist. In einigen Fällen kann das Ausgabedataset ein **Dummy-Dataset** sein, das nur dazu dient, den Zeitplan für die Ausführung der Aktivität „Gespeicherte Prozedur“ anzugeben.

### <a name="json-example"></a>JSON-Beispiel

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

Weitere Informationen finden Sie unter [Aktivität „Gespeicherte Prozedur“](data-factory-stored-proc-activity.md).

## <a name="net-custom-activity"></a>Benutzerdefinierte .NET-Aktivität
Sie können in der JSON-Definition einer benutzerdefinierten .NET-Aktivität die folgenden Eigenschaften angeben. Die type-Eigenschaft für die Aktivität muss wie folgt lauten: **DotNetActivity**. Es ist erforderlich, einen verknüpften Azure HDInsight-Dienst oder einen verknüpften Azure Batch-Dienst zu erstellen und den Namen des verknüpften Diensts als Wert für die **linkedServiceName**-Eigenschaft anzugeben. Die folgenden Eigenschaften werden im Abschnitt **typeProperties** unterstützt, wenn Sie den Typ der Aktivität auf DotNetActivity festlegen:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| AssemblyName | Der Name der Assembly. In diesem Beispiel ist dies: **MyDotnetActivity.dll**. | Ja |
| EntryPoint |Der Name der Klasse, die die IDotNetActivity-Schnittstelle implementiert. In diesem Beispiel ist dies: **MyDotNetActivityNS.MyDotNetActivity**, wobei MyDotNetActivityNS der Namespace und MyDotNetActivity die Klasse ist.  | Ja |
| PackageLinkedService | Der Name des verknüpften Azure Storage-Diensts, der auf den Blobspeicher verweist, in dem die ZIP-Datei mit der benutzerdefinierten Aktivität enthalten ist. In diesem Beispiel ist dies: **AzureStorageLinkedService**.| Ja |
| PackageFile | Der Name der ZIP-Datei. In diesem Beispiel ist dies **customactivitycontainer/MyDotNetActivity.zip**. | Ja |
| extendedProperties | Erweiterte Eigenschaften, die Sie definieren und an den .NET Code übergeben können. In diesem Beispiel wird die **SliceStart**-Variable auf einen Wert festgelegt, der auf der SliceStart-Systemvariablen basiert. | Nein |

### <a name="json-example"></a>JSON-Beispiel

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

Ausführliche Informationen finden Sie im Artikel [Use custom activities in Data Factory](data-factory-use-custom-activities.md) (Verwenden von benutzerdefinierten Aktivitäten in Data Factory).

## <a name="next-steps"></a>Nächste Schritte
Arbeiten Sie die folgenden Tutorials durch:

- [Tutorial: Erstellen einer Pipeline mit einer Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Tutorial: Erstellen einer Pipeline mit einer Hive-Aktivität](data-factory-build-your-first-pipeline.md)