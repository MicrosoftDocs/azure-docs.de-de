---
title: Data Factory – Funktionen und Systemvariablen
description: Enthält eine Liste der Funktionen und Systemvariablen von Azure Data Factory.
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9b5f91655367e866858a04b941cec4ee61dfe180
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495649"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory – Funktionen und Systemvariablen
> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Systemvariablen in Data Factory](../control-flow-system-variables.md).

Dieser Artikel enthält Informationen zu Funktionen und Variablen, die von Azure Data Factory unterstützt werden.

## <a name="data-factory-system-variables"></a>Data Factory-Systemvariablen

| Variablenname | BESCHREIBUNG | Objektbereich | JSON-Bereich und Anwendungsfälle |
| --- | --- | --- | --- |
| WindowStart |Anfang des Zeitfensters der aktuellen Aktivitätsausführung |activity |<ol><li>Geben Sie Abfragen zur Datenauswahl an. Informationen finden Sie in den Artikeln zu Connectors, auf die im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) verwiesen wird).</li> |
| WindowEnd |Ende des Zeitfensters der aktuellen Aktivitätsausführung |activity |Identisch mit „WindowStart“ |
| SliceStart |Anfang des Zeitfensters für den zu erstellenden Datenslice |activity<br/>dataset |<ol><li>Geben Sie bei der Arbeit mit [Azure-Blob](data-factory-azure-blob-connector.md) und [Dateisystem-Datasets](data-factory-onprem-file-system-connector.md) dynamische Pfade und Dateinamen an.</li><li>Geben Sie Eingabeabhängigkeiten mit Data Factory-Funktionen in der Auflistung der Aktivitätseingaben an.</li></ol> |
| SliceEnd |Ende des Zeitfensters für den aktuellen Datenslice |activity<br/>dataset |Identisch mit „SliceStart“ |

> [!NOTE]
> Derzeit ist es für Data Factory erforderlich, dass der in der Aktivität angegebene Zeitplan exakt mit dem Zeitplan übereinstimmt, der unter „availability“ für das Ausgabedataset angegeben ist. Daher sind „WindowStart“, „WindowEnd“, „SliceStart“ und „SliceEnd“ immer dem gleichen Zeitraum und einem einzelnen Ausgabeslice zugeordnet.
> 

### <a name="example-for-using-a-system-variable"></a>Beispiel für die Verwendung einer Systemvariablen
Im folgenden Beispiel werden Jahr, Monat, Tag und Uhrzeit von **SliceStart** in separate Variablen extrahiert, die von den Eigenschaften **folderPath** und **fileName** verwendet werden.

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Data Factory-Funktionen
Sie können Funktionen in Data Factory zusammen mit Systemvariablen für folgende Zwecke verwenden:

1. Angeben von Abfragen zur Datenauswahl (siehe die Artikel zu Connectors, auf die im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) verwiesen wird).
   
   Die Syntax zum Aufrufen einer Data Factory-Funktion ist **$$\<function>** für Abfragen zur Datenauswahl und andere Eigenschaften in der Aktivität und den Datasets.  
2. Angeben von Eingabeabhängigkeiten mit Data Factory-Funktionen in der Sammlung der Aktivitätseingaben.
   
    $$ ist nicht erforderlich, um Eingabeabhängigkeitsausdrücke anzugeben.     

Im folgenden Beispiel wird die **sqlReaderQuery**-Eigenschaft in einer JSON-Datei einem Wert zugewiesen, der von der `Text.Format`-Funktion zurückgegeben wird. Dieses Beispiel verwendet ebenfalls die Systemvariable **WindowStart**, die die Startzeit des Zeitfensters für die Aktivitätsausführung darstellt.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Informationen zu verschiedenen verfügbaren Formatierungsoptionen (beispielsweise „yy“ oder „yyyy“) finden Sie im Thema [Benutzerdefinierte Formatzeichenfolgen für Datum und Uhrzeit](/dotnet/standard/base-types/custom-date-and-time-format-strings). 

### <a name="functions"></a>Functions
In den folgenden Tabellen werden alle Funktionen in Azure Data Factory aufgelistet:

| Category | Funktion | Parameter | BESCHREIBUNG |
| --- | --- | --- | --- |
| Time |AddHours(X,Y) |X: Datetime <br/><br/>Y: int |Fügt Y Stunden der angegebenen Uhrzeit X hinzu. <br/><br/>Beispiel: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Time |AddMinutes(X,Y) |X: Datetime <br/><br/>Y: int |Fügt Y Minuten zu X hinzu.<br/><br/>Beispiel: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Time |StartOfHour(X) |X: Datetime |Ruft die Startzeit der Stunde ab, die von der Stundenkomponente X dargestellt wird. <br/><br/>Beispiel: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Date |AddDays(X,Y) |X: Datetime<br/><br/>Y: int |Addiert Y Tage zu X. <br/><br/>Beispiel: 15.09.2013 12:00:00 PM + 2 Tage = 17.09.2013 12:00:00 PM.<br/><br/>Sie können auch Tage subtrahieren, indem Sie Y als negative Zahl angeben.<br/><br/>Beispiel: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Date |AddMonths(X,Y) |X: Datetime<br/><br/>Y: int |Fügt Y Monate zu X hinzu.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Sie können auch Monate subtrahieren, indem Sie Y als negative Zahl angeben.<br/><br/>Beispiel: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Date |AddQuarters(X,Y) |X: Datetime <br/><br/>Y: int |Fügt Y * 3 Monate zu X hinzu<br/><br/>Beispiel: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Date |AddWeeks(X,Y) |X: Datetime<br/><br/>Y: int |Addiert Y * 7 Tage zu X<br/><br/>Beispiel: 15.09.2013 12:00:00 PM + 1 Woche = 22.09.2013 12:00:00 PM<br/><br/>Sie können auch Wochen subtrahieren, indem Sie Y als negative Zahl angeben.<br/><br/>Beispiel: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Date |AddYears(X,Y) |X: Datetime<br/><br/>Y: int |Fügt Y Jahre zu X hinzu.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Sie können auch Jahre subtrahieren, indem Sie Y als negative Zahl angeben.<br/><br/>Beispiel: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Date |Day(X) |X: Datetime |Ruft die Komponente "Tag" von X ab.<br/><br/>Beispiel: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Date |DayOfWeek(X) |X: Datetime |Ruft den Tag der Komponente "Woche" von X ab.<br/><br/>Beispiel: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Date |DayOfYear(X) |X: Datetime |Ruft den Tag des Jahres ab, der von der Komponente "Jahr" von X dargestellt wird.<br/><br/>Beispiele:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Date |DaysInMonth(X) |X: Datetime |Ruft die Tage des Monats ab, die von der Komponente "Monat" des Parameters X dargestellt werden.<br/><br/>Beispiel: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Date |EndOfDay(X) |X: Datetime |Ruft die Datum/Uhrzeit-Angabe ab, die das Ende des Tages (Komponente "Tag") von X darstellt.<br/><br/>Beispiel: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Date |EndOfMonth(X) |X: Datetime |Ruft das Ende des Monats ab, das von der Komponente "Monat" des Parameters X dargestellt wird. <br/><br/>Beispiel: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (Datum/Uhrzeit-Angabe, die das Ende des Monats September darstellt) |
| Date |StartOfDay(X) |X: Datetime |Ruft den Beginn des Tages ab, der von der Komponente "Tag" des Parameters X dargestellt wird.<br/><br/>Beispiel: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| Datetime |From(X) |X: String |Analysieren der Zeichenfolge X in einen Datum/Uhrzeit-Wert. |
| Datetime |Ticks(X) |X: Datetime |Ruft die Zeiteinheitseigenschaft des Parameters X ab. Eine Zeiteinheit entspricht 100 Nanosekunden. Der Wert dieser Eigenschaft stellt die Anzahl der Zeiteinheiten dar, die seit Mitternacht am 1. Januar 0001 verstrichen sind. |
| Text |Format(X) |X: Zeichenfolgenvariable |Formatiert den Text (verwenden Sie `\\'` in Kombination mit dem Escapezeichen `'`)|

> [!IMPORTANT]
> Bei Verwenden einer Funktion in einer anderen Funktion müssen Sie für die innere Funktion nicht das Präfix **$$** verwenden. Beispiel: $$Text.Format('PartitionKey eq \\'my_pkey_filter_value\\' and RowKey ge \\'{0: yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). Wie Sie sehen, wird das Präfix **$$** bei diesem Beispiel nicht für die **Time.AddHours**-Funktion verwendet wird. 

#### <a name="example"></a>Beispiel
Im folgenden Beispiel werden die Eingabe- und Ausgabeparameter für die Hive-Aktivität mithilfe der `Text.Format`-Funktion und der Systemvariablen „SliceStart“ bestimmt. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>Beispiel 2

Im folgenden Beispiel wird der DateTime-Parameter für die Aktivität „Gespeicherte Prozedur“ mithilfe der Text. „Format“-Funktion und der Systemvariablen „SliceStart“ bestimmt. 

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
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>Beispiel 3
Um Daten vom vorherigen Tag statt von dem durch „SliceStart“ dargestellten Tag zu lesen, verwenden Sie die AddDays-Funktion, wie im folgenden Beispiel gezeigt: 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
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

Informationen zu verschiedenen verfügbaren Formatierungsoptionen (beispielsweise „yy“ oder „yyyy“) finden Sie im Thema [Benutzerdefinierte Formatzeichenfolgen für Datum und Uhrzeit](/dotnet/standard/base-types/custom-date-and-time-format-strings).