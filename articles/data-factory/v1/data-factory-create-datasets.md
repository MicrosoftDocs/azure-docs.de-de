---
title: Erstellen von Datasets in Azure Data Factory
description: Anhand von Beispielen, in denen Eigenschaften wie „offset“ und „anchorDateTime“ verwendet werden, wird beschrieben, wie Sie Datasets in Azure Data Factory erstellen.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 4b222b387dad1c078cfe2a063ed310ef463b192e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376835"
---
# <a name="datasets-in-azure-data-factory-version-1"></a>Datasets in Azure Data Factory (Version 1)
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-create-datasets.md)
> * [Version 2 (aktuelle Version)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Datasets in V2](../concepts-datasets-linked-services.md).

In diesem Artikel ist beschrieben, was Datasets sind, wie sie im JSON-Format definiert werden, und wie sie in Azure Data Factory-Pipelines verwendet werden. Der Artikel enthält ausführliche Informationen zu jedem Abschnitt (z. B. „structure“, „availability“ und „policy“) in der JSON-Definition eines Datasets. Außerdem werden im Artikel Beispiele dazu bereitgestellt, wie die Eigenschaften **offset**, **anchorDateTime** und **style** in der Definition eines Datasets verwendet werden.

> [!NOTE]
> Wenn Sie noch nicht mit Data Factory vertraut sind, sollten Sie [Einführung in Azure Data Factory](data-factory-introduction.md) lesen, um eine Übersicht zu erhalten. Wenn Sie keine praktische Erfahrung mit dem Erstellen von Data Factorys haben, können Sie sich ein besseres Verständnis aneignen, indem Sie das [Tutorial zu Datentransformation](data-factory-build-your-first-pipeline.md) und das [Tutorial zu Datenverschiebung](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) lesen.

## <a name="overview"></a>Übersicht
Eine Data Factory kann eine oder mehrere Pipelines haben. Bei einer **Pipeline** handelt es sich um eine logische Gruppierung von **Aktivitäten**, die zusammen eine Aufgabe bilden. Die Aktivitäten in einer Pipeline definieren Aktionen, die Sie auf Ihre Daten anwenden. Sie können beispielsweise eine Kopieraktivität zum Kopieren von Daten aus einer SQL Server-Datenbank in Azure Blob Storage verwenden. Anschließend könnten Sie eine Hive-Aktivität verwenden, die ein Hive-Skript für einen Azure HDInsight-Cluster ausführt, um Daten aus dem Blob Storage zu verarbeiten, um Ausgabedaten zu produzieren. Schließlich könnten Sie die Ausgabedaten mit einer zweiten Kopieraktivität in Azure Synapse Analytics kopieren, auf dessen Basis Business Intelligence-Berichtslösungen (BI) erstellt werden. Weitere Informationen zu Pipelines und Aktivitäten finden Sie unter [Pipelines und Aktivitäten in Azure Data Factory](data-factory-create-pipelines.md).

Eine Aktivität kann über null oder mehr **Eingabedatasets** verfügen und ein oder mehrere Ausgabedatasets erstellen. Ein Eingabedataset entspricht der Eingabe für eine Aktivität in der Pipeline, und ein Ausgabedataset entspricht der Ausgabe für die Aktivität. Datasets bestimmen Daten in verschiedenen Datenspeichern, z.B. Tabellen, Dateien, Ordnern und Dokumenten. Ein Azure-Blobdataset kann beispielsweise den Blobcontainer und -ordner in Blob Storage angeben, aus dem die Pipeline die Daten lesen soll.

Bevor Sie ein Dataset erstellen, erstellen Sie einen **verknüpften Dienst**, um Ihren Datenspeicher mit der Data Factory zu verknüpfen. Verknüpfte Dienste ähneln Verbindungszeichenfolgen, mit denen die Verbindungsinformationen definiert werden, die für Data Factory zum Herstellen einer Verbindung mit externen Ressourcen erforderlich sind. Datasets bestimmen Daten in den verknüpften Datenspeichern, z.B. SQL-Tabellen, Dateien, Ordnern und Dokumenten. Ein mit Azure Storage verknüpfter Dienst verbindet z.B. ein Speicherkonto mit der Data Factory. Ein Azure-Blob-Dataset stellt den Blobcontainer und den Ordner dar, der die zu verarbeitenden Eingabeblobs enthält.

Hier ist ein Beispielszenario. Erstellen Sie zwei verknüpfte Dienste, um Daten aus dem Blobspeicher in SQL-Datenbank zu kopieren: Azure Storage und Azure SQL-Datenbank. Erstellen Sie anschließend zwei Datasets: Azure-Blobdataset (das sich auf den mit Azure Storage verknüpften Dienst bezieht) und Azure SQL-Tabellendataset (das sich auf den mit Azure SQL-Datenbank verknüpften Dienst bezieht). Die mit Azure Storage und Azure SQL-Datenbank verknüpften Dienste enthalten Verbindungszeichenfolgen, die Data Factory zur Laufzeit verwendet, um die Verbindung mit Ihrem Azure Storage bzw. mit Ihrer Instanz von Azure SQL-Datenbank herzustellen. Das Azure-Blobdataset gibt den Blobcontainer und Blobordner an, der die Eingabeblobs in Ihrer Blob Storage-Instanz enthält. Das Azure SQL-Tabellendataset gibt die SQL-Tabelle in Ihrer SQL-Datenbank an, in die die Daten kopiert werden sollen.

Das folgende Diagramm zeigt die Beziehung zwischen Pipeline, Aktivität, Dataset und verknüpftem Dienst in der Data Factory an:

![Beziehung zwischen Pipeline, Aktivität, Dataset und verknüpften Diensten](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>JSON-DataSet
Ein Dataset in Data Factory wird wie folgt im JSON-Format definiert:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": "<boolean flag to indicate external data. only for input datasets>",
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
| name |Name des Datasets. Unter [Azure Data Factory – Benennungsregeln](data-factory-naming-rules.md) finden Sie die Benennungsregeln. |Ja |Nicht verfügbar |
| type |Typ des Datasets. Geben Sie einen der von Data Factory unterstützten Typen an (z.B. AzureBlob, AzureSqlTable). <br/><br/>Weitere Informationen finden Sie unter [Dataset: type](#Type). |Ja |Nicht verfügbar |
| structure |Schema des Datasets.<br/><br/>Unter [Dataset: structure](#Structure) finden Sie weitere Details. |Nein |Nicht verfügbar |
| typeProperties | Die Typeigenschaften unterscheiden sich je nach Typ (z.B. Azure-Blob, Azure SQL-Tabelle). Ausführliche Informationen über die unterstützten Typen und deren Eigenschaften finden Sie unter [Dataset: type](#Type). |Ja |Nicht verfügbar |
| external | Boolesches Flag, das angibt, ob ein Dataset explizit durch eine Data Factory-Pipeline erstellt wird oder nicht. Wenn das Eingabedataset für eine Aktivität nicht durch die aktuelle Pipeline erstellt wird, legen Sie für dieses Flag „true“ fest. Legen Sie für das Eingabedataset der ersten Aktivität in der Pipeline für dieses Flag „true“ fest.  |Nein |false |
| availability | Definiert das Verarbeitungsfenster (beispielsweise stündlich oder täglich) oder das Modell für das Aufteilen in Slices für die Datasetproduktion. Jede Einheit von Daten, die durch eine Aktivitätsausführung genutzt und erstellt wird, heißt Datenslice. Wenn die Verfügbarkeit des Ausgabedatasets auf täglich (frequency - Day, interval - 1) festgelegt ist, wird täglich ein Slice erstellt. <br/><br/>Ausführlichere Informationen finden Sie unter „Dataset-Verfügbarkeit“. <br/><br/>Weitere Informationen zum Modell für das Aufteilen von Datasets in Slices finden Sie im Artikel [Planung und Ausführung](data-factory-scheduling-and-execution.md). |Ja |Nicht verfügbar |
| policy |Definiert die Kriterien oder die Bedingung, die die Dataset-Slices erfüllen müssen. <br/><br/>Weitere Informationen finden Sie im Abschnitt [Dataset: policy](#Policy). |Nein |Nicht verfügbar |

## <a name="dataset-example"></a>Datasetbeispiel
Im folgenden Beispiel stellt das Dataset eine Tabelle namens **MyTable** in einer SQL-Datenbank dar.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Beachten Sie folgende Punkte:

* **type** ist auf „AzureSqlTable“ festgelegt.
* Die Typeigenschaft **tableName** (speziell für den Typ „AzureSplTable“) ist auf „MyTable“ festgelegt.
* **linkedServiceName** verweist auf einen verknüpften Dienst vom Typ „AzureSqlDatabase“, der im nächsten JSON-Codeausschnitt definiert ist.
* Die Verfügbarkeitshäufigkeit (**availability frequency**) ist auf „Day“ und **interval** ist auf „1“ festgelegt. Dies bedeutet, dass der Datasetslice täglich erzeugt wird.

**AzureSqlLinkedService** ist wie folgt definiert:

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

Im vorherigen JSON-Codeausschnitt:

* **type** wird auf „AzureSqlDatabase“ festgelegt.
* Die **connectionString**-Typeigenschaft gibt Informationen zur Verbindung mit einer SQL-Datenbank an.

Wie Sie sehen, definiert der verknüpfte Dienst das Herstellen einer Verbindung mit einer SQL-Datenbank. Das Dataset definiert, welche Tabelle als Eingabe und Ausgabe für die Aktivität in einer Pipeline verwendet wird.

> [!IMPORTANT]
> Falls ein Dataset nicht von der Pipeline erzeugt wird, sollte es als **external** markiert werden. Diese Einstellung gilt im Allgemeinen für Eingaben der ersten Aktivität in einer Pipeline.

## <a name="dataset-type"></a><a name="Type"></a> Dataset: type
Der Typ des Datasets hängt von dem Datenspeicher ab, den Sie verwenden. In der folgenden Tabelle finden Sie eine Liste der von Data Factory unterstützten Datenspeicher. Klicken Sie auf einen Datenspeicher, um Informationen zum Erstellen eines verknüpften Diensts und eines Datasets für diesen Datenspeicher zu erhalten.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Datenspeicher mit * können lokal oder auf Azure IaaS (Infrastructure-as-a-Service) vorhanden sein. Für diese Datenspeicher müssen Sie [Datenverwaltungsgateway](data-factory-data-management-gateway.md) installieren.

Im vorherigen Abschnitt ist im Beispiel der Typ des Datasets auf **AzureSqlTable** festgelegt. Ebenso ist für ein Azure-Blobdataset der Typ des Datasets auf **AzureBlob** festgelegt, wie im folgenden JSON-Code dargestellt:

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

## <a name="dataset-structure"></a><a name="Structure"></a>Dataset: structure
Der Abschnitt **structure** ist optional. In ihm ist das Schema des Datasets in einer Sammlung der Namen und Datentypen der Spalten definiert. Im Abschnitt „structure“ legen Sie Typinformationen fest, die zum Konvertieren von Typen und Zuordnen von Spalten von der Quelle zum Ziel bereitgestellt werden. Im folgenden Beispiel hat das Dataset drei Spalten: `slicetimestamp`, `projectname` und `pageviews`. Die Spalten haben jeweils den folgenden Typ: „String“, „String“, „Decimal“.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Jede Spalte im Abschnitt „structure“ enthält die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| name |Name der Spalte. |Ja |
| type |Datentyp der Spalte.  |Nein |
| culture |Zu verwendendes .NET-basiertes Gebietsschema, wenn der Typ ein .NET-Typ ist: `Datetime` oder `Datetimeoffset`. Der Standardwert lautet `en-us`. |Nein |
| format |Zu verwendende Formatzeichenfolge, wenn der Typ ein .NET-Typ ist: `Datetime` oder `Datetimeoffset`. |Nein |

Anhand der folgenden Anleitungen können Sie entscheiden, wann der Abschnitt **structure** mit welchen Informationen verwendet werden sollte.

* **Für strukturierte Datenquellen** geben Sie den Abschnitt „structure“ nur an, wenn Sie Quellspalten zu Zielspalten zuordnen möchten und die Spaltennamen nicht identisch sind. Diese Art von strukturierter Datenquelle speichert Informationen zu Datenschema und Datentyp zusammen mit den Daten selbst. Zu Beispielen für strukturierte Datenquellen gehören SQL Server-, Oracle- und Azure-Tabellen.
  
    Da Typinformationen für strukturierte Datenquellen bereits verfügbar sind, sollten Sie keine Typinformationen einschließen, wenn Sie sich für die Verwendung des Abschnitts „structure“ entscheiden.
* **Für das Schema von Lesedatenquellen (insbesondere Blob Storage)** können Sie Daten speichern, ohne Schema- oder Typinformationen mit den Daten zu speichern. Für diese Datenquellentypen geben Sie „structure“ an, wenn Sie Quellspalten zu Zielspalten zuordnen möchten. Geben Sie „structure“ auch an, wenn das Dataset eine Eingabe für eine Kopieraktivität ist und die Datentypen des Quelldatasets in systemeigene Typen für das Ziel (Senke) konvertiert werden müssen.
    
    Data Factory unterstützt die folgenden Werte für das Bereitstellen von Typinformationen in der Struktur: **„Int16“, „Int32“, „Int64“, „Single“, „Double“, „Decimal“, „Byte[]“, „Boolean“, „String“, „Guid“, „Datetime“, „Datetimeoffset“ und „Timespan“** . Diese Werte sind CLS-kompatible (Common Language Specification) .NET-basierte Typwerte.

Data Factory führt beim Verschieben von Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher automatisch Typkonvertierungen durch.

## <a name="dataset-availability"></a>Dataset: availability
Der Abschnitt **availability** in einem Dataset definiert das Verarbeitungsfenster (etwa stündlich, täglich oder wöchentlich) für das Dataset. Weitere Informationen zum Aktivitätsfenster finden Sie unter [Planung und Ausführung](data-factory-scheduling-and-execution.md).

Der folgende „availability“-Abschnitt gibt an, dass entweder das Ausgabedataset stündlich erstellt wird oder das Eingabedataset stündlich verfügbar ist:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Wenn die Pipeline die folgende Start- und Endzeit hat:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

Das Ausgabedataset wird zwischen Start- und Endzeit der Pipeline stündlich erzeugt. Aus diesem Grund werden fünf Datasetslices von dieser Pipeline erzeugt, einer für jedes Aktivitätsfenster (00:00 Uhr – 01:00 Uhr, 01:00 Uhr – 02:00 Uhr, 02:00 Uhr – 03:00 Uhr, 3:00 Uhr – 4:00 Uhr, 4:00 Uhr – 5: 00 Uhr).

In der folgenden Tabelle werden die Eigenschaften beschrieben, die Sie im Abschnitt „availability“ verwenden können:

| Eigenschaft | BESCHREIBUNG | Erforderlich | Standard |
| --- | --- | --- | --- |
| frequency |Gibt die Zeiteinheit für die Erstellung der Datasetslices an.<br/><br/><b>Unterstützte Häufigkeit</b>: „Minute“, „Hour“, „Day“, „Week“, „Month“ |Ja |Nicht verfügbar |
| interval |Gibt einen Multiplikator für die Häufigkeit an.<br/><br/>„frequency x interval“ bestimmt, wie oft der Slice erzeugt wird. Wenn Sie das Dataset beispielsweise auf Stundenbasis in Slices aufteilen möchten, legen Sie <b>frequency</b> auf <b>Hour</b> und <b>interval</b> auf <b>1</b> fest.<br/><br/>Wenn Sie **frequency** als **Minute** angeben, sollten Sie „interval“ auf mindestens „15“ festlegen. |Ja |Nicht verfügbar |
| style |Gibt an, ob der Slice am Anfang oder am Ende des Intervalls erzeugt werden soll.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Wenn **frequency** auf **Month** und **style** auf **EndOfInterval** festgelegt ist, wird der Slice am letzten Tag des Monats erstellt. Wenn **style** auf **StartOfInterval** festgelegt ist, wird der Slice am ersten Tag des Monats erstellt.<br/><br/>Wenn **frequency** auf **Day** und **style** auf **EndOfInterval** festgelegt ist, wird der Slice in der letzten Stunde des Tages erstellt.<br/><br/>Wenn **frequency** auf **Hour** und **style** auf **EndOfInterval** festgelegt ist, wird der Slice am Ende der Stunde erstellt. Ein Slice für den Zeitraum 13:00 bis 14:00 Uhr wird z. B. um 14.00 Uhr erstellt. |Nein |EndOfInterval |
| anchorDateTime |Definiert die absolute Position in der Zeit, die der Scheduler benötigt, um Dataset-Slicegrenzen zu berechnen. <br/><br/>Beachten Sie, dass, wenn diese Eigenschaft Datumsteile hat, die detaillierter als die angegebene Häufigkeit sind, die detaillierteren Teile ignoriert werden. Wenn **interval** beispielsweise auf **hourly** festgelegt ist (frequency: hour, interval: 1) und das **AnchorDateTime**-Element **Minuten und Sekunden** enthält, wird der Teil mit den Minuten und Sekunden von **anchorDateTime** ignoriert. |Nein |01/01/0001 |
| offset |Zeitspanne, um die Anfang und Ende aller Datasetslices verschoben werden. <br/><br/>Wenn sowohl **anchorDateTime** als auch **offset** angegeben sind, ist das Ergebnis die kombinierte Verschiebung. |Nein |Nicht verfügbar |

### <a name="offset-example"></a>Beispiel zu Offset
Standardmäßig beginnen tägliche (`"frequency": "Day", "interval": 1`) Slices um 00: 00 Uhr (Mitternacht) koordinierte Weltzeit (UTC). Wenn die Startzeit 6:00 Uhr UTC-Zeit sein soll, legen Sie den Versatz wie im folgenden Codeausschnitt gezeigt fest:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>Beispiel zu „anchorDateTime“
Im folgenden Beispiel wird das Dataset einmal alle 23 Stunden erstellt. Der erste Slice beginnt zu dem durch **anchorDateTime** angegebenen Zeitpunkt, der auf `2017-04-19T08:00:00` (UTC) festgelegt ist.

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>Beispiel zu „offset“/„style“
Das folgende Dataset wird monatlich am dritten Tage jedes Monats um 8:00 Uhr erstellt (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="dataset-policy"></a><a name="Policy"></a>Dataset: policy
Der Abschnitt **policy** in der Datasetdefinition definiert die Kriterien oder die Bedingung, die die Datasetslices erfüllen müssen.

### <a name="validation-policies"></a>Überprüfungsrichtlinien
| Richtlinienname | BESCHREIBUNG | Angewendet auf | Erforderlich | Standard |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Überprüft, ob die Daten in **Azure Blob Storage** die minimalen Größenanforderungen (in MB) erfüllen. |Azure Blob Storage |Nein |Nicht verfügbar |
| minimumRows |Überprüft, ob die Daten in einer **Azure SQL-Datenbank** oder einer **Azure-Tabelle** die minimale Anzahl von Zeilen enthalten. |<ul><li>Azure SQL-Datenbank</li><li>Azure-Tabelle</li></ul> |Nein |Nicht verfügbar |

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

**minimumRows:**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Externe Datasets
Externe Datasets werden nicht durch eine Pipeline erstellt, die in der Data Factory ausgeführt wird. Wenn das Dataset als **extern** gekennzeichnet ist, kann die **ExternalData**-Richtlinie definiert werden, um das Verhalten der Dataset-Sliceverfügbarkeit zu beeinflussen.

Ein Dataset sollte, sofern es nicht von Data Factory erzeugt wird, es als **extern** markiert werden. Diese Einstellung gilt im Allgemeinen für die Eingaben der ersten Aktivität in einer Pipeline, wenn weder Aktivitäts- noch Pipelineverkettung genutzt wird.

| Name | BESCHREIBUNG | Erforderlich | Standardwert |
| --- | --- | --- | --- |
| dataDelay |Die Zeitspanne, um die die Prüfung der Verfügbarkeit der externen Daten für den angegebenen Slice verzögert wird. Beispielsweise können Sie eine stündliche Überprüfung verzögern, indem Sie diese Einstellung verwenden.<br/><br/>Die Einstellung gilt nur für die aktuelle Zeit. Beispiel: Wenn es gerade 13:00 Uhr ist und dieser Wert 10 Minuten beträgt, beginnt die Überprüfung um 13:10 Uhr.<br/><br/>Diese Einstellung wirkt sich nicht auf Slices in der Vergangenheit aus. Slices mit **Segmentendzeit** + **dataDelay** < **Jetzt** werden ohne Verzögerung verarbeitet.<br/><br/>Zeiträume, die länger als 23 Stunden 59 Minuten sind, müssen im Format `day.hours:minutes:seconds` angegeben werden. Um beispielsweise 24 Stunden anzugeben, verwenden Sie nicht 24:00:00. Verwenden Sie stattdessen 1.00:00:00. Wenn Sie 24:00:00 verwenden, wird dies als 24 Tage (24.00:00:00) gewertet. Für 1 Tag und 4 Stunden geben Sie „1:04:00:00“ an. |Nein |0 |
| retryInterval |Die Wartezeit zwischen einem Fehler und dem nächsten Versuch. Diese Einstellung gilt für die aktuelle Zeit. Wenn beim vorherigen Versuch ein Fehler auftrat, wird der nächste Versuch nach Verstreichen des **retryInterval**-Zeitraums durchgeführt. <br/><br/>Wenn es gerade 13:00 Uhr ist, beginnt der erste Versuch. Wenn die Ausführung der ersten Überprüfung 1 Minute gedauert hat und ein Fehler aufgetreten ist, findet die nächste Wiederholung um 13:00 + 1 Min. (Dauer) + 1 Min. (Wiederholungsintervall) = 13:02 Uhr statt. <br/><br/>Für Slices in der Vergangenheit gibt es keine Verzögerung. Der erneute Versuch erfolgt sofort. |Nein |00:01:00 (1 Minute) |
| retryTimeout |Das Timeout für die einzelnen Wiederholungsversuche.<br/><br/>Wenn diese Eigenschaft auf 10 Minuten festgelegt ist, sollte die Überprüfung innerhalb von 10 Minuten abgeschlossen werden. Wenn die Ausführung der Überprüfung länger als 10 Minuten dauert, wird das Timeout für die Wiederholung wirksam.<br/><br/>Wenn für alle Überprüfungsversuche ein Timeout aufgetreten ist, wird der Slice als **TimedOut** gekennzeichnet. |Nein |00:10:00 (10 Minuten) |
| maximumRetry |Gibt an, wie oft die Verfügbarkeit der externen Daten überprüft werden soll. Der zulässige größte Wert ist „10“. |Nein |3 |


## <a name="create-datasets"></a>Erstellen von Datasets
Sie können Datasets mit einem dieser Tools oder SDKs erstellen:

- Kopier-Assistent
- Visual Studio
- PowerShell
- Azure Resource Manager-Vorlage
- REST-API
- .NET API

In den folgenden Tutorials finden Sie schrittweise Anleitungen zum Erstellen von Pipelines und Datasets mit einem dieser Tools oder SDKs:

- [Tutorial: Erstellen Ihrer ersten Pipeline zur Transformierung von Daten mithilfe eines Hadoop-Clusters](data-factory-build-your-first-pipeline.md)
- [Tutorial: Kopieren von Daten aus Blob Storage in SQL-Datenbank mithilfe von Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Sobald eine Pipeline erstellt und bereitgestellt ist, können Sie diese über die Blätter des Azure-Portals oder über die App „Überwachung und Verwaltung“ überwachen und verwalten. In den nächsten Themen finden Sie schrittweise Anweisungen:

- [Überwachen und Verwalten von Azure Data Factory-Pipelines mit dem Azure-Portal und PowerShell](data-factory-monitor-manage-pipelines.md)
- [Überwachen und Verwalten von Azure Data Factory-Pipelines mit der App „Überwachung und Verwaltung“](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Zugeordnete Datasets
Mit der Eigenschaft **datasets** können Sie Datasets erstellen, die einer Pipeline zugeordnet sind. Diese Datasets können nur von Aktivitäten innerhalb dieser Pipeline, nicht von Aktivitäten in anderen Pipelines verwendet werden. Das folgende Beispiel definiert eine Pipeline mit zwei Datasets („InputDataset-rdc“ und „OutputDataset-rdc“), die in der Pipeline verwendet werden sollen.

> [!IMPORTANT]
> Zugeordnete Datasets werden nur mit einmalig ausgeführten Pipelines (dabei ist **pipelineMode** auf **OneTime** festgelegt) unterstützt. Weitere Informationen finden Sie unter [Pipeline mit einmaliger Ausführung](data-factory-create-pipelines.md#onetime-pipeline) .
>
>

```json
{
    "name": "CopyPipeline-rdc",
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
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu Pipelines finden Sie unter [Pipelines und Aktivitäten in Azure Data Factory](data-factory-create-pipelines.md).
- Weitere Informationen über die Planung und Ausführung von Pipelines finden Sie unter [Data Factory – Planung und Ausführung](data-factory-scheduling-and-execution.md).
