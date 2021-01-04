---
title: Erstellen/Planen von Pipelines, Kettenaktivitäten in Data Factory
description: Es wird beschrieben, wie Sie eine Datenpipeline in Azure Data Factory erstellen, um Daten zu verschieben und zu transformieren. Erstellen Sie einen datengesteuerten Workflow zum Erzeugen von fertigen Informationen.
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
ms.openlocfilehash: 83b4d14d46677c731b7fb9faae2217492368d4b2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96496057"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipelines und Aktivitäten in Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-create-pipelines.md)
> * [Version 2 (aktuelle Version)](../concepts-pipelines-activities.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Pipelines in V2](../concepts-pipelines-activities.md).

In diesem Artikel erhalten Sie Informationen zu Pipelines und Aktivitäten in Azure Data Factory und erfahren, wie diese zum Erstellen datengesteuerter lückenloser Workflows für Ihre Datenverschiebungs- und Datenverarbeitungsszenarien genutzt werden können.

> [!NOTE]
> Dieser Artikel setzt voraus, dass Sie die [Einführung in den Azure Data Factory-Dienst](data-factory-introduction.md)gelesen haben. Wenn Sie noch nicht über praktische Erfahrung mit dem Erstellen von Data Factorys verfügen, erleichtert Ihnen das Tutorial [Erstellen Ihrer ersten Pipeline zur Transformierung von Daten mithilfe eines Hadoop-Clusters](data-factory-build-your-first-pipeline.md) oder [Kopieren von Daten aus Blob Storage in SQL-Datenbank mithilfe von Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) das Verständnis des vorliegenden Artikels.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Übersicht
Eine Data Factory kann eine oder mehrere Pipelines haben. Bei einer Pipeline handelt es sich um eine logische Gruppierung von Aktivitäten, die zusammen eine Aufgabe bilden. Die Aktivitäten in einer Pipeline definieren Aktionen, die Sie auf Ihre Daten anwenden. Sie können beispielsweise eine Kopieraktivität zum Kopieren von Daten aus einer SQL Server-Datenbank in eine Azure Blob Storage-Instanz verwenden. Verwenden Sie dann eine Hive-Aktivität, die ein Hive-Skript auf einen Azure HDInsight-Cluster anwendet, um Daten aus dem Blob Storage zu verarbeiten/transformieren, um Ausgabedaten zu produzieren. Kopieren Sie schließlich die Ausgabedaten mit einer zweiten Kopieraktivität in Azure Synapse Analytics, auf dessen Basis Business Intelligence-Berichtslösungen (BI) erstellt werden.

Eine Aktivität kann über null oder mehr [Eingabedatasets](data-factory-create-datasets.md) verfügen und ein oder mehrere [Ausgabedatasets](data-factory-create-datasets.md) erstellen. Das folgende Diagramm zeigt die Beziehung zwischen Pipeline, Aktivität und Dataset in der Data Factory an:

![Beziehung zwischen Pipeline, Aktivität und Dataset](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Eine Pipeline ermöglicht Ihnen das Verwalten der Aktivitäten als Gruppe – anstatt für jede Aktivität einzeln. Sie können eine Pipeline z.B. bereitstellen, planen, anhalten und fortsetzen, anstatt mit Aktivitäten in der Pipeline unabhängig umzugehen.

Data Factory unterstützt zwei Arten von Aktivitäten: Datenverschiebungen und Datentransformationen. Jede Aktivität kann über null oder mehr [Eingabedatasets](data-factory-create-datasets.md) verfügen und ein oder mehrere Ausgabedatasets erstellen.

Ein Eingabedataset stellt die Eingabe für eine Aktivität in der Pipeline dar, und ein Ausgabedataset stellt die Ausgabe für die Aktivität dar. Datasets bestimmen Daten in verschiedenen Datenspeichern, z.B. Tabellen, Dateien, Ordnern und Dokumenten. Nachdem Sie ein Dataset erstellt haben, können Sie es zusammen mit Aktivitäten in einer Pipeline verwenden. Bei einem Dataset kann es sich beispielsweise um ein Eingabe-/Ausgabedataset einer Kopieraktivität oder einer HDInsightHive-Aktivität handeln. Weitere Informationen über Datasets finden Sie im Artikel [Datasets in Azure Data Factory](data-factory-create-datasets.md).

### <a name="data-movement-activities"></a>Datenverschiebungsaktivitäten
Die Kopieraktivität in Data Factory kopiert die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher. Data Factory unterstützt die folgenden Datenspeicher. Daten aus beliebigen Quellen können in beliebige Senken geschrieben werden. Klicken Sie auf einen Datenspeicher, um zu erfahren, wie Daten in diesen/aus diesem Speicher kopiert werden.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Datenspeicher mit * können lokal oder in Azure IaaS verfügbar sein. Für ihre Verwendung müssen Sie das [Datenverwaltungsgateway](data-factory-data-management-gateway.md) auf einem lokalen oder einem Azure IaaS-Computer installieren.

Weitere Informationen finden Sie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Datentransformationsaktivitäten
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Weitere Informationen finden Sie im Artikel [Datentransformationsaktivitäten](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Benutzerdefinierte .NET-Aktivitäten
Falls Sie Daten in einen/aus einem Datenspeicher verschieben müssen, der von der Kopieraktivität nicht unterstützt wird, oder Daten mit Ihrer eigenen Logik transformieren möchten, erstellen Sie eine **benutzerdefinierte .NET-Aktivität**. Informationen zum Erstellen und Verwenden einer benutzerdefinierten Aktivität finden Sie unter [Verwenden von benutzerdefinierten Aktivitäten in einer Azure Data Factory-Pipeline](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>Planen von Pipelines
Eine Pipeline ist nur zwischen ihrer **start**- und ihrer **end**-Zeit aktiv. Sie wird weder vor der Startzeit noch nach der Endzeit ausgeführt. Wenn die Pipeline angehalten wird, wird sie unabhängig von Start- und Endzeit nicht ausgeführt. Wenn eine Pipeline ausgeführt werden soll, darf sie nicht angehalten werden. Informationen zur Planung und Ausführung in Azure Data Factory finden Sie unter [Planung und Ausführung](data-factory-scheduling-and-execution.md) .

## <a name="pipeline-json"></a>Pipeline-JSON
Sehen wir uns an, wie eine Pipeline im JSON-Format definiert wird. Die generische Struktur für eine Pipeline sieht wie folgt aus:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>",
        "isPaused": true/false,
        "pipelineMode": "scheduled/onetime",
        "expirationTime": "15.00:00:00",
        "datasets":
        [
        ]
    }
}
```

| Tag | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| name |Name der Pipeline. Geben Sie einen Namen an, der die Aktion darstellt, die die Pipeline durchführt. <br/><ul><li>Maximale Anzahl von Zeichen: 260</li><li>Muss mit einem Buchstaben, einer Zahl oder einem Unterstrich (\_) beginnen.</li><li>Folgende Zeichen sind nicht zulässig: “.”, “+”, “?”, “/”, “<”,”>”,”\*”,”%”,”&”,”:”,”\\”</li></ul> |Ja |
| description | Geben Sie den Text an, der beschreibt, wofür die Pipeline verwendet wird. |Ja |
| activities | Im Abschnitt **activities** kann mindestens eine Aktivität definiert werden. Weitere Informationen zum JSON-Element der Aktivitäten finden Sie im nächsten Abschnitt. | Ja |
| start | Startdatum/-uhrzeit für die Pipeline. Muss im [ISO-Format](https://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: `2016-10-14T16:32:41Z`. <br/><br/>Es ist möglich, eine lokale Zeit anzugeben, z. B. eine EST-Zeit. Beispiel: `2016-02-27T06:00:00-05:00`. Entspricht 6 Uhr EST.<br/><br/>Die Eigenschaften "start" und "end" geben zusammen den aktiven Zeitraum der Pipeline an. Ausgabeslices werden nur in diesem aktiven Zeitraum erstellt. |Nein<br/><br/>Wenn Sie einen Wert für die Endeigenschaft angeben, müssen Sie auch einen Wert für die Starteigenschaft angeben.<br/><br/>Sowohl die Start- als auch die Endzeiten zum Erstellen einer Pipeline können leer sein. Sie müssen beide Werte angeben, um für die Pipeline einen aktiven Zeitraum für die Ausführung festzulegen. Wenn Sie beim Erstellen einer Pipeline keine Start- und Endzeit angeben, können Sie später zum Festlegen der Werte das Cmdlet „Set-AzDataFactoryPipelineActivePeriod“ verwenden. |
| end | Datum und Uhrzeit für das Ende der Pipeline. Muss, falls gewünscht, im ISO-Format angegeben werden. Beispiel: `2016-10-14T17:32:41Z` <br/><br/>Es ist möglich, eine lokale Zeit anzugeben, z. B. eine EST-Zeit. Beispiel: `2016-02-27T06:00:00-05:00`. Entspricht 6 Uhr EST.<br/><br/>Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie „9999-09-09“ als Wert für die Endeigenschaft an. <br/><br/> Eine Pipeline ist nur zwischen ihrer Start- und ihrer Endzeit aktiv. Sie wird weder vor der Startzeit noch nach der Endzeit ausgeführt. Wenn die Pipeline angehalten wird, wird sie unabhängig von Start- und Endzeit nicht ausgeführt. Wenn eine Pipeline ausgeführt werden soll, darf sie nicht angehalten werden. Informationen zur Planung und Ausführung in Azure Data Factory finden Sie unter [Planung und Ausführung](data-factory-scheduling-and-execution.md) . |Nein <br/><br/>Wenn Sie einen Wert für die Starteigenschaft angeben, müssen Sie auch einen Wert für die Endeigenschaft angeben.<br/><br/>Lesen Sie auch die Hinweise zur **Start** -Eigenschaft. |
| isPaused | Bei der Einstellung „true“ wird die Pipeline nicht ausgeführt. Sie befindet sich im Status „angehalten“. Standardwert = false. Sie können diese Eigenschaft zum Aktivieren oder Deaktivieren einer Pipeline verwenden. |Nein |
| pipelineMode | Die Methode zum Planen von Ausführungen für die Pipeline. Zulässige Werte sind: „scheduled“ (Standard) und „onetime“.<br/><br/>„scheduled“ bedeutet, dass die Pipeline in einem bestimmten Zeitintervall gemäß ihrem aktiven Zeitraum (Start- und Endzeit) ausgeführt wird. „onetime“ bedeutet, dass die Pipeline nur einmal ausgeführt wird. Pipelines mit einmaliger Ausführung können derzeit nach der Erstellung nicht geändert oder aktualisiert werden. Informationen zur Einstellung der einmaligen Ausführung finden Sie unter [Pipeline mit einmaliger Ausführung](#onetime-pipeline) . |Nein |
| expirationTime | Zeitraum, für den die [Pipeline mit einmaliger Ausführung](#onetime-pipeline) nach der Erstellung gültig ist und für den die Bereitstellung aufrechterhalten werden sollte. Wenn die Pipeline keine aktiven, fehlerhaften oder ausstehenden Ausführungen enthält, wird sie bei Erreichen der Ablaufzeit automatisch gelöscht. Der Standardwert: `"expirationTime": "3.00:00:00"`.|Nein |
| datasets |Liste der Datasets, die von den in der Pipeline definierten Aktivitäten verwendet werden sollen. Diese Eigenschaft kann verwendet werden, um Datasets zu definieren, die spezifisch für diese Pipeline und nicht innerhalb der Data Factory definiert sind. In dieser Pipeline definierte Datasets können nicht gemeinsam genutzt, sondern nur von dieser Pipeline verwendet werden. Informationen hierzu finden Sie unter [Zugeordnete Datasets](data-factory-create-datasets.md#scoped-datasets) . |Nein |

## <a name="activity-json"></a>JSON-Definition der Aktivität
Im Abschnitt **activities** kann mindestens eine Aktivität definiert werden. Jede Aktivität besitzt auf oberster Ebene die folgende Struktur:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs": "[]",
    "outputs": "[]",
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

In der folgenden Tabelle werden Eigenschaften in der JSON-Definition der Aktivität beschrieben:

| Tag | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| name | Der Name der Aktivität. Geben Sie einen Namen an, der die Aktion darstellt, die die Aktivität durchführt. <br/><ul><li>Maximale Anzahl von Zeichen: 260</li><li>Muss mit einem Buchstaben, einer Zahl oder einem Unterstrich (\_) beginnen.</li><li>Folgende Zeichen sind nicht zulässig: „.“, „+“, „?“, „/“, „<“, „>“, „*“, „%“, „&“, „:“, „\\“.</li></ul> |Ja |
| description | Ein Text, der beschreibt, wofür die Aktivität verwendet wird. |Ja |
| type | Der Typ der Aktivität. Verschiedene Typen von Aktivitäten finden Sie in den Abschnitten [Datenverschiebungsaktivitäten](#data-movement-activities) und [Transformationsaktivitäten von Daten](#data-transformation-activities). |Ja |
| inputs |Von der Aktivität verwendete Eingabetabellen<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Ja |
| outputs |Von der Aktivität verwendete Ausgabetabellen.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Ja |
| linkedServiceName |Name des verknüpften Diensts, der von der Aktivität verwendet wird. <br/><br/>Für eine Aktivität kann es erforderlich sein, den verknüpften Dienst anzugeben, der mit der erforderlichen Computeumgebung verknüpft ist. |„Ja“ für die HDInsight-Aktivität und die Batchbewertungsaktivität für Azure Machine Learning Studio (klassisch) <br/><br/>„Nein“ für alle übrigen |
| typeProperties |Eigenschaften im Abschnitt **typeProperties** sind abhängig vom Typ der Aktivität. Um Typeigenschaften für eine Aktivität anzuzeigen, klicken Sie auf die Links zur Aktivität im vorhergehenden Abschnitt. | Nein |
| policy |Richtlinien, die das Laufzeitverhalten der Aktivität beeinflussen. Falls dies nicht angegeben wird, werden Standardrichtlinien verwendet. |Nein |
| scheduler | Die „scheduler“-Eigenschaft wird verwendet, um die gewünschte Planung für die Aktivität zu definieren. Die untergeordneten Eigenschaften sind identisch mit denen der [availability-Eigenschaft in einem Dataset](data-factory-create-datasets.md#dataset-availability). |Nein |

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

## <a name="sample-copy-pipeline"></a>Beispiel einer Kopierpipeline
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
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```

Beachten Sie folgende Punkte:

* Der Abschnitt „Activities“ enthält nur eine Aktivität, deren **Typ** auf **Copy** festgelegt ist.
* Die Eingabe für die Aktivität ist auf **InputDataset** und die Ausgabe für die Aktivität ist auf **OutputDataset** festgelegt. Informationen zum Definieren von Datasets im JSON-Format finden Sie im Artikel [Datasets](data-factory-create-datasets.md).
* Im Abschnitt **typeProperties** ist **BlobSource** als Quelltyp und **SqlSink** als Senkentyp angegeben. Klicken Sie im Abschnitt [Datenverschiebungsaktivitäten](#data-movement-activities) auf den Datenspeicher, den Sie als Quelle oder Senke verwenden möchten, um weitere Informationen zum Verschieben von Daten in den/aus dem Datenspeicher zu erhalten.

Eine ausführliche exemplarische Vorgehensweise zum Erstellen dieser Pipeline finden Sie im [Tutorial: Kopieren von Daten aus Blob Storage in SQL-Datenbank](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="sample-transformation-pipeline"></a>Beispiel einer Transformationspipeline
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
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

Beachten Sie folgende Punkte:

* Der Abschnitt „Activities“ enthält nur eine Aktivität, deren **Typ** auf **HDInsightHive** festgelegt ist.
* Die Hive-Skriptdatei **partitionweblogs.hql** ist im Azure-Speicherkonto (das durch den scriptLinkedService-Dienst namens **AzureStorageLinkedService** angegeben ist) und im Ordner **script** im Container **adfgetstarted** gespeichert.
* Der Abschnitt `defines` wird zum Angeben der Runtimeeinstellungen verwendet, die als Hive-Konfigurationswerte an das Hive-Skript übergeben werden (z.B. `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Der Abschnitt **typeProperties** ist für jede Transformation unterschiedlich. Um weitere Informationen zu Typeigenschaften zu erhalten, die für eine Transformationsaktivität unterstützt werden, klicken Sie in der Tabelle [Datentransformationsaktivitäten](#data-transformation-activities) auf die Transformationsaktivität.

Eine ausführliche exemplarische Vorgehensweise zum Erstellen dieser Pipeline finden Sie im [Tutorial: Erstellen der ersten Pipeline zum Verarbeiten von Daten mithilfe eines Hadoop-Clusters](data-factory-build-your-first-pipeline.md).

## <a name="multiple-activities-in-a-pipeline"></a>Mehrere Aktivitäten in einer Pipeline
Die vorherigen beiden Beispielpipelines enthalten nur jeweils eine Aktivität. Sie können mehrere Aktivitäten in einer Pipeline verwenden.

Wenn in einer Pipeline mehrere Aktivitäten vorliegen und die Ausgabe einer Aktivität nicht die Eingabe für eine andere Aktivität ist, können die Aktivitäten parallel ausgeführt werden, sofern Eingabedatenslices für die Aktivitäten bereitstehen.

Sie können zwei Aktivitäten verketten, indem Sie das Ausgabedataset einer Aktivität als Eingabedataset der anderen Aktivität verwenden. Die zweite Aktivität wird nur ausgeführt, wenn die erste erfolgreich abgeschlossen wurde.

![Verketten von Aktivitäten in derselben Pipeline](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

In diesem Beispiel enthält die Pipeline zwei Aktivitäten: „Activity1“ und „Activity2“. „Activity1“ akzeptiert „Dataset1“ als Eingabe und erzeugt die Ausgabe „Dataset2“. Die Aktivität akzeptiert „Dataset2“ als Eingabe und erzeugt die Ausgabe „Dataset3“. Da die Ausgabe von „Activity1“ („Dataset2“) die Eingabe von „Activity2“ ist, wird „Activity2“ nur ausgeführt, nachdem die Aktivität erfolgreich abgeschlossen wurde und den Slice „Dataset2“ erzeugt hat. Wenn bei „Activity1“ aus beliebigen Gründen ein Fehler auftritt, und der Slice „Dataset2“ nicht erzeugt wird, wird „Activity2“ nicht für diesen Slice ausgeführt (z. B. 9 Uhr bis 10 Uhr).

Sie können auch Aktivitäten verketten, die sich in verschiedenen Pipelines befinden.

![Verketten von Aktivitäten in zwei Pipelines](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

In diesem Beispiel hat „Pipeline1“ nur eine Aktivität, die „Dataset1“ als Eingabe akzeptiert und „Dataset2“ als Ausgabe erzeugt. „Pipeline2“ hat ebenfalls nur eine Aktivität, die „Dataset2“ als Eingabe akzeptiert und „Dataset3“ als Ausgabe erzeugt.

Weitere Informationen finden Sie unter [Planung und Ausführung](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).
## <a name="create-and-monitor-pipelines"></a>Erstellen und Überwachen von Pipelines
Sie können mit einem dieser Tools oder SDKs Pipelines erstellen.

- Kopier-Assistent
- Visual Studio
- Azure PowerShell
- Azure Resource Manager-Vorlage
- REST-API
- .NET API

In den folgenden Tutorials finden Sie schrittweise Anleitungen zum Erstellen von Pipelines mit einem dieser Tools oder SDKs.

- [Tutorial: Erstellen Ihrer ersten Pipeline zur Transformierung von Daten mithilfe eines Hadoop-Clusters](data-factory-build-your-first-pipeline.md)
- [Tutorial: Kopieren von Daten aus Blob Storage in SQL-Datenbank mithilfe von Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Sobald eine Pipeline erstellt/bereitgestellt ist, können Sie sie mit den Blättern des Azure-Portals oder der App „Überwachung und Verwaltung“ überwachen und verwalten. In den nächsten Themen finden Sie schrittweise Anweisungen.

- [Überwachen und Verwalten von Azure Data Factory-Pipelines mit dem Azure-Portal und PowerShell](data-factory-monitor-manage-pipelines.md).
- [Überwachen und Verwalten von Azure Data Factory-Pipelines mit der App „Überwachung und Verwaltung“](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>Pipeline mit einmaliger Ausführung
Sie können eine Pipeline erstellen und zur regelmäßigen Ausführung (z.B. stündlich oder täglich) innerhalb der in der Pipelinedefinition angegebenen Start- und Endzeiten planen. Weitere Informationen finden Sie unter „Planen von Aktivitäten“. Sie können auch eine Pipeline erstellen, die nur einmal ausgeführt wird. Legen Sie zu diesem Zweck wie im folgenden JSON-Beispiel gezeigt die **pipelineMode**-Eigenschaft in der Pipelinedefinition auf **onetime** (einmalig) fest. Der Standardwert für diese Eigenschaft lautet **scheduled** (geplant).

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
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
                "name": "CopyActivity-0"
            }
        ],
        "pipelineMode": "OneTime"
    }
}
```

Beachten Sie Folgendes:

* Es werden keine Start- und Endzeiten (**start** und **end**) für die Pipeline angegeben.
* Die Verfügbarkeit (**availability**) von Ein- und Ausgabedatasets (**frequency** und **interval**) wird angegeben, auch wenn die Werte von Data Factory nicht verwendet werden.
* Die Diagrammansicht zeigt einmalig ausgeführte Pipelines nicht an. Dieses Verhalten ist beabsichtigt.
* Einmalige Pipelines können nicht aktualisiert werden. Sie können eine einmalige Pipeline klonen, umbenennen, deren Eigenschaften aktualisieren und sie bereitstellen, um eine andere Pipeline zu erstellen.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen über Datasets finden Sie im Artikel [Datasets in Azure Data Factory](data-factory-create-datasets.md).
- Weitere Informationen über die Planung und Ausführung von Pipelines finden Sie im Artikel [Data Factory – Planung und Ausführung](data-factory-scheduling-and-execution.md).
