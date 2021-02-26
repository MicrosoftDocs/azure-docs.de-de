---
title: Planung und Ausführung mit Data Factory
description: Informationen zu den Planungs- und Ausführungsaspekten des Azure Data Factory-Anwendungsmodells.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: bfdae688f7736149fe7ddf04cd3833d9adf30818
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382717"
---
# <a name="data-factory-scheduling-and-execution"></a>Data Factory – Planung und Ausführung
> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen im Artikel [Pipelineausführung und Auslöser](../concepts-pipeline-execution-triggers.md).

In diesem Artikel werden die Planungs- und Ausführungsaspekte des Azure Data Factory-Anwendungsmodells erläutert. In diesem Artikel wird vorausgesetzt, dass Sie mit den Grundbegriffen des Data Factory-Anwendungsmodells vertraut sind, z.B. Aktivität, Pipelines, verknüpfte Dienste und Datasets. Grundlegende Konzepte von Azure Data Factory finden Sie in den folgenden Artikeln:

* [Einführung in Data Factory](data-factory-introduction.md)
* [Pipelines](data-factory-create-pipelines.md)
* [Datasets](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Start- und Endzeiten der Pipeline
Eine Pipeline ist nur zwischen ihrer **start**- und ihrer **end**-Zeit aktiv. Sie wird weder vor der Startzeit noch nach der Endzeit ausgeführt. Wenn die Pipeline angehalten wird, wird sie unabhängig von Start- und Endzeit nicht ausgeführt. Wenn eine Pipeline ausgeführt werden soll, darf sie nicht angehalten werden. Sie finden diese Einstellungen (Start, Ende, angehalten) in der Pipelinedefinition: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Artikel [Pipelines und Aktivitäten in Azure Data Factory](data-factory-create-pipelines.md). 


## <a name="specify-schedule-for-an-activity"></a>Festlegen des Zeitplans für eine Aktivität
Nicht die Pipeline wird ausgeführt. Es handelt sich um die Aktivitäten in der Pipeline, die im Gesamtkontext der Pipeline ausgeführt werden. Im Abschnitt **scheduler** des JSON-Codes der Aktivität können Sie eine Zeitplanserie für die Aktivität angeben. Beispielsweise können Sie wie folgt die stündliche Ausführung einer Aktivität planen:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Wie im folgenden Diagramm dargestellt, wird durch die Angabe eines Zeitplans für eine Aktivität eine Reihe rollierender Fenster zwischen Start- und Endzeit der Pipeline erzeugt. Rollierende Fenster sind eine Reihe nichtüberlappender, aneinandergrenzender Zeitintervalle fester Größe. Diese logischen rollierenden Fenster für eine Aktivität werden als **Aktivitätsfenster** bezeichnet.

![„scheduler“-Beispiel für Aktivität](media/data-factory-scheduling-and-execution/scheduler-example.png)

Die Eigenschaft **scheduler** für eine Aktivität ist optional. Wenn Sie diese Eigenschaft angeben, muss sie mit dem Rhythmus übereinstimmen, den Sie in der Definition des Ausgabedatasets für die Aktivität angeben. Derzeit steuert das Ausgabedataset den Zeitplan. Darum müssen Sie auch dann ein Ausgabedataset erstellen, wenn die Aktivität keine Ausgabe erzeugt. 

## <a name="specify-schedule-for-a-dataset"></a>Festlegen des Zeitplans für ein Dataset
Eine Aktivität in einer Data Factory-Pipeline kann über null oder mehr **Eingabedatasets** verfügen und ein oder mehrere Ausgabedatasets erstellen. Für eine Aktivität können Sie den Rhythmus angeben, mit dem die Eingabedaten verfügbar sind, bzw. die Ausgabedaten, die mithilfe des **availability**-Abschnitts in den Datasetdefinitionen erzeugt werden. 

**Frequency** im Abschnitt **availability** gibt die Zeiteinheit an. Die zulässigen Werte für die Häufigkeit sind: „Minute“, „Hour“, „Day“, „Week“ und „Month“. Die **interval**-Eigenschaft im Abschnitt „availability“ gibt einen Multiplikator für „frequency“ an. Beispiel: Wenn für ein Ausgabedataset „frequency“ auf „Day“ und „interval“ auf „1“ festgelegt ist, werden die Ausgabedaten täglich generiert. Wenn Sie für „frequency“ „Minute“ festlegen, sollten Sie für „interval“ mindestens „15“ festlegen. 

Im folgenden Beispiel ist das Eingabedataset stündlich verfügbar, und die Ausgabedaten werden stündlich erzeugt (`"frequency": "Hour", "interval": 1`). 

**Eingabedataset:** 

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```


**Ausgabedataset**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Derzeit **steuert das Ausgabedataset den Zeitplan**. Das bedeutet: Der für das Ausgabedataset angegebene Zeitplan wird zur Ausführung einer Aktivität zur Laufzeit verwendet. Darum müssen Sie auch dann ein Ausgabedataset erstellen, wenn die Aktivität keine Ausgabe erzeugt. Wenn die Aktivität keine Eingabe akzeptiert, können Sie das Erstellen des Eingabedatasets überspringen. 

In der folgenden Pipelinedefinition wird die Eigenschaft **scheduler** verwendet, um den Zeitplan für die Aktivität anzugeben. Diese Eigenschaft ist optional. Derzeit muss der Zeitplan für die Aktivität dem angegebenen Zeitplan für das Ausgabedataset entsprechen.
 
```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        ],
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

In diesem Beispiel wird die Aktivität stündlich zwischen der Start- und Endzeit der Pipeline ausgeführt. Die Ausgabedaten werden stündlich für dreistündige Fenster erzeugt (8:00 Uhr - 9:00 Uhr, 9:00 Uhr - 10:00 Uhr,und 10:00 Uhr - 11:00 Uhr). 

Jede Einheit von Daten, die von einer Aktivitätsausführung genutzt oder produziert wird, wird als **Datenslice** bezeichnet. Das folgende Diagramm zeigt das Beispiel einer Aktivität mit einem Eingabedataset und einem Ausgabedataset: 

![Scheduler für "availability"](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Im Diagramm sind die stündlichen Datenslices für das Eingabe- und Ausgabedataset dargestellt. Das Diagramm enthält drei Eingabeslices, die bereit für die Verarbeitung sind. Die Aktivität von 10 bis 11 Uhr ist in Bearbeitung und produziert den Ausgabeslice für „10-11 AM“. 

Sie können im JSON-Code des Datasets über die folgenden Variablen auf das Zeitintervall des aktuellen Slice zugreifen: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) und [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Auf ähnliche Weise können Sie mit WindowStart und WindowEnd auf das Zeitintervall zugreifen, das einem Aktivitätsfenster zugeordnet ist. Der Zeitplan einer Aktivität muss dem Zeitplan des Ausgabedatasets für die Aktivität entsprechen. Daher sind die Werte von SliceStart und SliceEnd jeweils mit den Werten von WindowStart und WindowEnd identisch. Weitere Informationen zu diesen Variablen finden Sie in den Artikeln zu [Data Factory-Funktionen und -Systemvariablen](data-factory-functions-variables.md#data-factory-system-variables).  

Sie können diese Variablen für unterschiedliche Zwecke im JSON-Code der Aktivität verwenden. Beispielsweise können Sie sie zum Auswählen von Daten aus Eingabe- und Ausgabedatasets verwenden, die Zeitreihendaten darstellen (Beispiel: 8:00 Uhr bis 9:00 Uhr). In diesem Beispiel werden **WindowStart** und **WindowEnd** auch verwendet, um relevante Daten für eine Aktivitätsausführung auszuwählen und in ein Blob mit entsprechendem **folderPath** zu kopieren. Der Ordnerpfad ( **folderPath** ) ist so parametrisiert, dass für jede Stunde ein separater Ordner angelegt wird.  

Im vorherigen Beispiel ist der Zeitplan für Eingabe- und Ausgabedataset identisch (stündlich). Wenn das Eingabedataset für die Aktivität mit einer anderen Frequenz verfügbar ist, z.B. alle 15 Minuten, wird die Aktivität, die dieses Ausgabedataset erzeugt, dennoch stündlich ausgeführt, da das Ausgabedataset den Aktivitätszeitplan steuert. Weitere Informationen finden Sie unter [Modellieren von Datasets mit unterschiedlichen Frequenzen](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Datasetverfügbarkeit und -richtlinien
Sie haben die Verwendung der Eigenschaften „frequency“ und „interval“ im Abschnitt „availability“ der Definition des Datasets kennengelernt. Es gibt einige andere Eigenschaften, die Einfluss auf Zeitplan und Ausführung einer Aktivität haben. 

### <a name="dataset-availability"></a>Dataset: availability 
In der folgenden Tabelle werden die Eigenschaften beschrieben, die Sie im Abschnitt **availability** verwenden können:

| Eigenschaft | BESCHREIBUNG | Erforderlich | Standard |
| --- | --- | --- | --- |
| frequency |Gibt die Zeiteinheit für die Erstellung der Datasetslices an.<br/><br/><b>Unterstützte Häufigkeit</b>: „Minute“, „Hour“, „Day“, „Week“, „Month“ |Ja |Nicht verfügbar |
| interval |Gibt einen Multiplikator für die Häufigkeit an<br/><br/>„Frequency x interval“ bestimmt, wie oft der Slice erzeugt wird.<br/><br/>Wenn Sie das Dataset auf Stundenbasis in Slices aufteilen möchten, legen Sie <b>Frequency</b> auf <b>Hour</b> und <b>interval</b> auf <b>1</b> fest.<br/><br/><b>Hinweis</b>: Wenn Sie „Frequency“ auf „Minute“ festlegen, sollten Sie „interval“ mindestens auf „15“ festlegen. |Ja |Nicht verfügbar |
| style |Gibt an, ob der Slice am Anfang/Ende des Intervalls erzeugt werden soll.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Wenn „Frequency“ auf „Month“ und „style“ auf „EndOfInterval“ festgelegt ist, wird der Slice am letzten Tag des Monats erstellt. Wenn „style“ auf „StartOfInterval“ festgelegt ist, wird der Slice am ersten Tag des Monats erstellt.<br/><br/>Wenn „Frequency“ auf „Day“ und „style“ auf „EndOfInterval“ festgelegt ist, wird der Slice in der letzten Stunde des Tages erstellt.<br/><br/>Wenn „Frequency“ auf „Hour“ und „style“ auf „EndOfInterval“ festgelegt ist, wird der Slice am Ende der Stunde erstellt. Ein Slice für den Zeitraum 13:00 bis 14:00 Uhr wird z. B. um 14.00 Uhr erstellt. |Nein |EndOfInterval |
| anchorDateTime |Definiert die absolute Position in der Zeit, die der Scheduler benötigt, um Dataset-Slicegrenzen zu berechnen. <br/><br/><b>Hinweis</b>: Wenn AnchorDateTime Datumsteile aufweist, die präziser als die Häufigkeit sind, werden die präziseren Teile ignoriert. <br/><br/>Wenn <b>interval</b> beispielsweise auf <b>hourly</b> festgelegt ist (frequency: hour, interval: 1) und das <b>AnchorDateTime</b>-Element <b>Minuten und Sekunden</b> enthält, wird der Teil mit den <b>Minuten und Sekunden</b> von AnchorDateTime ignoriert. |Nein |01/01/0001 |
| offset |Zeitspanne, um die Anfang und Ende aller Datasetslices verschoben werden. <br/><br/><b>Hinweis</b>: Wenn sowohl „anchorDateTime“ als auch „offset“ angegeben werden, ist das Ergebnis die kombinierte Verschiebung. |Nein |Nicht verfügbar |

### <a name="offset-example"></a>Beispiel zu Offset
Standardmäßig beginnen Slices täglich (`"frequency": "Day", "interval": 1`) um 00:00 Uhr UTC-Zeit (Mitternacht). Wenn die Startzeit 6:00 Uhr UTC-Zeit sein soll, legen Sie den Versatz wie im folgenden Codeausschnitt gezeigt fest: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>Beispiel zu „anchorDateTime“
Im folgenden Beispiel wird das Dataset einmal alle 23 Stunden erstellt. Der erste Slice beginnt zu dem durch anchorDateTime vorgegebenen Zeitpunkt, d.h. um `2017-04-19T08:00:00` (UTC-Zeit).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Beispiel zu „offset“/„style“
Das folgende Dataset ist ein monatliches Dataset und wird am Dritten jedes Monats um 8:00 Uhr erstellt (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Datasetrichtlinie
Für ein Dataset kann eine Überprüfungsrichtlinie definiert sein, die angibt, wie die von einer Sliceausführung generierten Daten überprüft werden können, ehe sie zur Nutzung bereit sind. In solchen Fällen wird nach Beendigung der Sliceausführung der Status des Ausgabeslice in **Warten** mit dem Unterstatus **Überprüfung** geändert. Nach der Überprüfung der Slices ändert sich der Slicestatus in **Bereit**. Wenn ein Datenslice erstellt wurde, aber die Überprüfung nicht bestanden hat, werden Aktivitätsausführungen für nachgelagerte, von diesem Slice abhängige Slices nicht verarbeitet. [Überwachen und Verwalten von Pipelines](data-factory-monitor-manage-pipelines.md) behandelt.

Der Abschnitt **policy** in der Datasetdefinition definiert die Kriterien oder die Bedingung, die die Datasetslices erfüllen müssen. In der folgenden Tabelle werden die Eigenschaften beschrieben, die Sie im Abschnitt **policy** verwenden können:

| Richtlinienname | BESCHREIBUNG | Angewendet auf | Erforderlich | Standard |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Überprüft, ob die Daten in einem **Azure-Blob** die minimalen Größenanforderungen (in MB) erfüllen. |Azure Blob |Nein |Nicht verfügbar |
| minimumRows | Überprüft, ob die Daten in **Azure SQL-Datenbank** oder in der **Azure-Tabelle** die Mindestanzahl von Zeilen enthalten. |<ul><li>Azure SQL-Datenbank</li><li>Azure Table</li></ul> |Nein |Nicht verfügbar |

#### <a name="examples"></a>Beispiele
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

Weitere Informationen zu diesen Eigenschaften und Beispielen finden Sie im Artikel [Datasets in Azure Data Factory](data-factory-create-datasets.md). 

## <a name="activity-policies"></a>Aktivitätsrichtlinien
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

Weitere Informationen finden Sie im Artikel [Pipelines und Aktivitäten in Azure Data Factory](data-factory-create-pipelines.md). 

## <a name="parallel-processing-of-data-slices"></a>Parallele Verarbeitung von Datenslices
Sie können das Startdatum für die Pipeline auf ein Datum in der Vergangenheit festlegen. Dann führt Data Factory automatisch eine Berechnung (einen Abgleich) aller Datenslices in der Vergangenheit aus und beginnt mit ihrer Verarbeitung. Beispiel: Sie erstellen eine Pipeline mit Startdatum 2017-04-01, und das aktuelle Datum ist 2017-04-10. Wenn für das Ausgabedataset der Rhythmus „Daily“ festgelegt ist, beginnt Data Factory sofort mit der Verarbeitung aller Slices von 2017-04-01 bis 2017-04-09, da das Startdatum in der Vergangenheit liegt. Der Slice aus 2017-04-10 wird noch nicht verarbeitet, da der Wert der style-Eigenschaft im availability-Abschnitt EndOfInterval lautet. Der älteste Slice wird zuerst verarbeitet, da OldestFirst der Standardwert von executionPriorityOrder ist. Eine Beschreibung der Style-Eigenschaft finden Sie im Abschnitt [Datasetverfügbarkeit](#dataset-availability). Eine Beschreibung des Abschnitts executionPriorityOrder finden Sie im Abschnitt [Aktivitätsrichtlinien](#activity-policies). 

Sie können abgeglichene Datenslices für eine parallele Verarbeitung konfigurieren, indem Sie die Eigenschaft **concurrency** im Abschnitt **policy** des JSON-Codes der Aktivität festlegen. Diese Eigenschaft legt die Anzahl der parallelen Ausführungen einer Aktivität fest, die für verschiedene Slices stattfinden können. Der Standardwert für die concurrency-Eigenschaft ist 1. Aus diesem Grund wird standardmäßig jeweils ein Slice verarbeitet. Der Höchstwert ist 10. Wenn eine Pipeline eine große Menge verfügbarer Daten verarbeiten muss, kann die Datenverarbeitung durch einen höheren Parallelitätswert beschleunigt werden. 

## <a name="rerun-a-failed-data-slice"></a>Wiederholen eines fehlerhaften Datenslices
Tritt ein Fehler bei der Verarbeitung eines Datenslices auf, können Sie mithilfe des Azure-Portalblatts oder der App zum Überwachen und Verwalten feststellen, warum bei der Verarbeitung des Slices ein Fehler aufgetreten ist. Ausführliche Informationen finden Sie unter [Überwachen und Verwalten von Pipelines mithilfe von Blättern im Azure-Portal](data-factory-monitor-manage-pipelines.md) oder [App „Überwachung und Verwaltung“](data-factory-monitor-manage-app.md).

Betrachten Sie das folgende Beispiel mit zwei Aktivitäten. Activity1 und Activity2. Activity1 nutzt einen Slice von Dataset1 und erzeugt einen Slice von Dataset2, der von Activity2 als Eingabe genutzt wird, um einen Slice des Final Dataset zu erzeugen.

![Fehlerhafter Slice](./media/data-factory-scheduling-and-execution/failed-slice.png)

Das Diagramm zeigt, dass bei den letzten drei Slices ein Fehler beim Erstellen des Slice „9-10 AM“ für Dataset2 aufgetreten ist. Data Factory verfolgt die Abhängigkeit für das Zeitreihen-Dataset automatisch. Aus diesem Grund wird die Aktivitätsausführung für den nachgelagerten Slice für den Zeitraum von 9 bis 10 Uhr nicht gestartet.

Mit den Data Factory-Überwachungs- und Verwaltungstools können Sie die Diagnoseprotokolle detailliert nach dem fehlerhaften Slice durchsuchen, um die Ursache des Problems leicht zu finden und zu beseitigen. Nachdem Sie das Problem behoben haben, können Sie die Aktivitätsausführung ganz einfach starten, um den fehlerhaften Slice zu erstellen. Weitere Informationen zur erneuten Ausführung und Grundlegendes zu Statusübergängen für Datenslices finden Sie unter [Überwachen und Verwalten von Pipelines mithilfe von Blättern im Azure-Portal](data-factory-monitor-manage-pipelines.md) oder [App „Überwachung und Verwaltung“](data-factory-monitor-manage-app.md).

Nachdem Sie den Slice „9-10 AM“ für **Dataset2** erneut ausgeführt haben, startet Data Factory die Ausführung für den von „9-10 AM“ abhängigen Slice im endgültigen Dataset.

![Wiederholen eines fehlerhaften Slices](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Mehrere Aktivitäten in einer Pipeline
Sie können mehrere Aktivitäten in einer Pipeline verwenden. Wenn in einer Pipeline mehrere Aktivitäten vorliegen und die Ausgabe einer Aktivität nicht die Eingabe für eine andere Aktivität ist, können die Aktivitäten parallel ausgeführt werden, sofern Eingabedatenslices für die Aktivitäten bereitstehen.

Sie können zwei Aktivitäten verketten (nacheinander ausführen), indem Sie das Ausgabedataset einer Aktivität als Eingabedataset der anderen Aktivität festlegen. Die Aktivitäten können sich in derselben Pipeline oder in verschiedenen Pipelines befinden. Die zweite Aktivität wird nur ausgeführt, wenn die erste erfolgreich abgeschlossen wurde.

Beachten Sie beispielsweise den folgenden Fall, wo eine Pipeline zwei Aktivitäten besitzt:

1. Aktivität A1, für die das externe Eingabedataset D1 erforderlich ist, und die das Ausgabedataset D2 generiert.
2. Aktivität A2, für die eine Eingabe aus dem Dataset D2 erforderlich ist, und die das Ausgabedataset D3 generiert.

In diesem Szenario befinden sich die Aktivitäten A1 und A2 in der gleichen Pipeline. Die Aktivität A1 wird ausgeführt, wenn die externen Daten verfügbar sind und die Häufigkeit für die geplante Verfügbarkeit erreicht ist. Die Aktivität A2 wird ausgeführt, wenn die geplanten Slices von D2 verfügbar werden und die Häufigkeit für die geplante Verfügbarkeit erreicht ist. Wenn ein Fehler in einem der Slices im Dataset D2 auftritt, wird A2 für diesen Slice nicht ausgeführt, bis er verfügbar wird.

Die Diagrammansicht mit beiden Aktivitäten in derselben Pipeline sieht wie im folgenden Diagramm dargestellt aus:

![Verketten von Aktivitäten in derselben Pipeline](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Wie bereits erwähnt, könnten sich die Aktivitäten in verschiedenen Pipelines befinden. Die Diagrammansicht sieht in einem solchen Szenario wie im folgenden Diagramm dargestellt aus:

![Verketten von Aktivitäten in zwei Pipelines](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Im Abschnitt „Sequenzielles Kopieren“ des Anhangs finden Sie ein Beispiel.

## <a name="model-datasets-with-different-frequencies"></a>Modellieren von Datasets mit unterschiedlichen Frequenzen
In den Beispielen waren die Frequenzen für Eingabe- und Ausgabedatasets und das Aktivitätszeitfenster identisch. Einige Szenarien erfordern die Fähigkeit, eine Ausgabe mit einer Frequenz zu erzeugen, die sich von den Frequenzen einer oder mehrerer Eingaben unterscheidet. Data Factory unterstützt die Modellierung dieser Szenarien.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Beispiel 1: Erzeugen eines täglichen Ausgabeberichts für Eingabedaten, die stündlich verfügbar sind
In diesem Szenario verwenden Sie Eingabemessdaten von Sensoren, die stündlich in Azure Blob Storage verfügbar sind. Sie möchten einen täglichen Aggregationsbericht mit Statistiken wie Mittel-, Höchst- und Mindestwert für den Tag mit der [Hive-Aktivität](data-factory-hive-activity.md)von Data Factory erstellen.

Dieses Szenario können Sie wie folgt mit Data Factory realisieren:

**Eingabedataset**

Die stündlichen Eingabedateien werden im Ordner für den jeweiligen Tag abgelegt. Für die Eingabe ist „availability“ auf **Stündlich** festgelegt (frequency: Hour, interval: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Ausgabedataset**

Jeden Tag wird eine Ausgabedatei im Ordner des jeweiligen Tags erstellt. Für die Ausgabe ist „availability“ auf **Täglich** festgelegt (frequency: Day, interval: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Aktivität: Hive-Aktivität in einer Pipeline**

Das Hive-Skript empfängt wie im folgenden Codeausschnitt dargestellt durch Verwendung der Variablen *WindowStart* die entsprechenden **DateTime** -Informationen als Parameter. Das Hive-Skript verwendet diese Variable zum Laden der Daten aus dem richtigen Ordner für den jeweiligen Tag und zum Ausführen der Aggregation, um die Ausgabe zu generieren.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
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
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                    "Month": "$$Text.Format('{0:MM}',WindowStart)",
                    "Day": "$$Text.Format('{0:dd}',WindowStart)"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },            
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 2,
                "timeout": "01:00:00"
            }
         }
     ]
   }
}
```

Das folgende Diagramm zeigt das Szenario im Hinblick auf eine Datenabhängigkeit.

![Datenabhängigkeit](./media/data-factory-scheduling-and-execution/data-dependency.png)

Der Ausgabeslice für jeden Tag hängt von 24 stündlichen Slices aus einem Eingabedataset ab. Data Factory berechnet diese Abhängigkeiten automatisch, indem die Eingabedatenslices ermittelt werden, die in demselben Zeitraum wie der zu erzeugende Ausgabeslice liegen. Ist einer der 24 Eingabeslices nicht verfügbar, wartet Data Factory, bis der Eingabeslice bereit ist. Erst dann wird die tägliche Aktivitätsausführung gestartet.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Beispiel 2: Angeben von Abhängigkeiten mit Ausdrücken und Data Factory-Funktionen
Lassen Sie uns ein weiteres Szenario betrachten. Angenommen, Sie verwenden eine Hive-Aktivität, die zwei Eingabedatasets verarbeitet. Eines davon verfügt über neue tägliche Daten, und eines erhält jede Woche neue Daten. Angenommen, Sie möchten einen Join-Vorgang auf zwei Eingaben anwenden und eine tägliche Ausgabe erzeugen.

Der einfache Ansatz, bei dem Data Factory die zu verarbeitenden Eingabeslices automatisch durch Abstimmung mit dem Zeitraum des Ausgabedatenslice bestimmt, funktioniert in diesem Fall nicht mehr.

Sie müssen angeben, dass die Data Factory für jede Aktivitätsausführung den Datenslice der letzten Woche als wöchentliches Eingabedataset verwenden soll. Mithilfe von im folgenden Codeausschnitt gezeigten Azure Data Factory-Funktionen können Sie dieses Verhalten implementieren.

**Eingabe1: Azure-Blob**

Die erste Eingabe ist das täglich aktualisierte Azure-Blob.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Eingabe2: Azure-Blob**

Die zweite Eingabe ist das wöchentlich aktualisierte Azure-Blob.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**Ausgabe: Azure-Blob**

Jeden Tag wird eine Ausgabedatei im Ordner des jeweiligen Tags erstellt. Für die Ausgabe ist „availability“ auf **Täglich** festgelegt (frequency: Day, interval: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Aktivität: Hive-Aktivität in einer Pipeline**

Die Hive-Aktivität verwendet zwei Eingaben und erzeugt täglich einen Ausgabeslice. Sie können den täglichen Ausgabeslice wie folgt so angeben, dass dieser für die wöchentliche Eingabe vom Eingabeslice der vorherigen Woche abhängt.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
            "Month": "$$Text.Format('{0:MM}',WindowStart)",
            "Day": "$$Text.Format('{0:dd}',WindowStart)"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        },            
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 2,  
          "timeout": "01:00:00"
        }
       }
     ]
   }
}
```

Unter [Data Factory – Funktionen und Systemvariablen](data-factory-functions-variables.md) finden Sie eine Liste mit den Funktionen und Systemvariablen, die von Data Factory unterstützt werden.

## <a name="appendix"></a>Anhang

### <a name="example-copy-sequentially"></a>Beispiel: Sequenzielles Kopieren
Es ist möglich, mehrere Kopiervorgänge nacheinander sequenziell/sortiert auszuführen. Sie können beispielsweise zwei Kopieraktivitäten in einer Pipeline („CopyActivity1“ und „CopyActivity2“) mit den folgenden Eingabe-/Ausgabedatasets verwenden:   

CopyActivity1

Eingabe: Dataset. Ausgabe: Dataset2.

CopyActivity2

Eingabe: Dataset2.  Ausgabe: Dataset3.

CopyActivity2 wird nur ausgeführt, wenn CopyActivity1 erfolgreich ausgeführt wurde und Dataset2 verfügbar ist.

Hier finden Sie das Beispiel für die Pipeline-JSON:

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

Beachten Sie, dass in diesem Beispiel das Ausgabedataset der ersten Kopieraktivität (Dataset2) als Eingabe für die zweite Aktivität festgelegt ist. Aus diesem Grund wird die zweite Aktivität nur ausgeführt, wenn das Ausgabedataset der ersten Aktivität bereit ist.  

Im Beispiel kann CopyActivity2 eine andere Eingabe haben, z.B. Dataset3. Sie legen aber Dataset2 als Eingabe für CopyActivity2 fest, damit die Aktivität nicht so lange ausgeführt wird, bis CopyActivity1 abgeschlossen ist. Beispiel:

CopyActivity1

Eingabe: Dataset1. Ausgabe: Dataset2.

CopyActivity2

Eingaben: Dataset3, Dataset2. Ausgabe: Dataset4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

Beachten Sie, dass im Beispiel zwei Eingabedatasets für die zweite Kopieraktivität angegeben sind. Wenn mehrere Eingaben angegeben wurden, wird nur das erste Eingabedataset zum Kopieren der Daten verwendet, aber die anderen Datasets werden als Abhängigkeiten verwendet. CopyActivity2 wird erst gestartet, nachdem die folgenden Bedingungen erfüllt sind:

* CopyActivity1 wurde erfolgreich abgeschlossen und Dataset2 ist verfügbar. Dieses Dataset wird beim Kopieren von Daten in Dataset4 nicht verwendet. Es fungiert nur als Terminplanungs-Abhängigkeit für CopyActivity2.   
* Dataset3 ist verfügbar. Dieses Dataset stellt die Daten dar, die zum Ziel kopiert werden. 
