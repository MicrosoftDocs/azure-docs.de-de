---
title: Sitzungsprotokoll in der Kopieraktivität
description: Hier erfahren Sie, wie Sie ein Sitzungsprotokoll in der Kopieraktivität in Azure Data Factory aktivieren.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: yexu
ms.openlocfilehash: e56a840da07a2f6e966867699506f0122a0e7956
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593642"
---
#  <a name="session-log-in-copy-activity"></a>Sitzungsprotokoll in der Kopieraktivität

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Sie können die kopierten Dateinamen in der Kopieraktivität protokollieren, um sicherzustellen, dass die Daten nicht nur erfolgreich aus dem Quell- in den Zielspeicher kopiert werden, sondern auch zwischen Quell- und Zielspeicher konsistent sind. Dazu überprüfen Sie die kopierten Dateien in den Sitzungsprotokollen der Kopieraktivität.  

Wenn Sie die Fehlertoleranzeinstellung in der Kopieraktivität aktivieren, um fehlerhafte Daten zu überspringen, können auch die übersprungenen Dateien und die übersprungenen Zeilen protokolliert werden.  Weitere Informationen finden Sie unter [Fehlertoleranz der Kopieraktivität](copy-activity-fault-tolerance.md). 

## <a name="configuration"></a>Konfiguration
Das folgende Beispiel zeigt eine JSON-Definition, mit der das Sitzungsprotokoll in der Kopieraktivität aktiviert wird: 

```json
"typeProperties": {
    "source": {
        "type": "BinarySource",
        "storeSettings": {
            "type": "AzureDataLakeStoreReadSettings",
            "recursive": true
        },
        "formatSettings": {
            "type": "BinaryReadSettings"
        }
    },
    "sink": {
        "type": "BinarySink",
        "storeSettings": {
            "type": "AzureBlobFSWriteSettings"
        }
    },                    
    "skipErrorFile": {
        "fileForbidden": true,
        "dataInconsistency": true
    },
    "validateDataConsistency": true,
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    }
}
```

Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich
-------- | ----------- | -------------- | -------- 
enableCopyActivityLog | Wenn diese Eigenschaft auf „true“ festgelegt wird, haben Sie die Möglichkeit, kopierte Dateien, übersprungene Dateien oder übersprungene Zeilen zu protokollieren.  | True<br/>False (Standardwert) | Nein
logLevel | Mit „Info“ werden alle kopierten Dateien, übersprungenen Dateien und übersprungenen Zeilen protokolliert. Mit „Warning“ werden nur übersprungene Dateien und übersprungene Zeilen protokolliert.  | Info<br/>Warnung (Standardeinstellung) | Nein
enableReliableLogging | Wenn diese Eigenschaft auf „true“ festgelegt ist, werden Protokolle bei der Kopieraktivität im zuverlässigen Modus sofort geleert, sobald jede einzelne Datei in das Ziel kopiert wird.  Wenn Sie große Mengen an Dateien mit aktiviertem zuverlässigem Protokollierungsmodus in der Kopieraktivität kopieren, ist von einer Beeinträchtigung des Kopierdurchsatzes auszugehen, da für jede Dateikopie doppelte Schreibvorgänge erforderlich sind. Eine Anforderung wird an den Zielspeicher und eine weitere Anforderung an den Protokollspeicher geleitet.  Bei einer Kopieraktivität im Modus für beste Leistung werden Protokolle mit einem Batch von Datensätzen innerhalb eines bestimmten Zeitraums geleert, wodurch die Beeinträchtigung des Kopierdurchsatzes wesentlich geringer ist. Die Vollständigkeit und Aktualität der Protokollierung sind in diesem Modus nicht gewährleistet, da es einige Möglichkeiten gibt, dass der letzte Batch von Protokollereignissen nicht in die Protokolldatei geleert wurde, wenn bei der Kopieraktivität ein Fehler auftrat. In diesem Moment werden einige Dateien, die in das Ziel kopiert werden, nicht protokolliert.  | True<br/>False (Standardwert) | Nein
logLocationSettings | Eine Gruppe von Eigenschaften, mit denen der Speicherort für die Sitzungsprotokolle angegeben werden kann. | | Nein
linkedServiceName | Der verknüpfte Dienst von [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) oder [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) zum Speichern der Sitzungsprotokolldateien. | Die Namen eines verknüpften Diensts vom Typ `AzureBlobStorage` oder `AzureBlobFS`, der auf die Instanz verweist, in der Sie die Protokolldateien speichern. | Nein
path | Der Pfad der Protokolldateien. | Geben Sie den Pfad an, in dem die Protokolldateien gespeichert werden sollen. Wenn Sie keinen Pfad angeben, erstellt der Dienst automatisch einen Container. | Nein


## <a name="monitoring"></a>Überwachung

### <a name="output-from-copy-activity"></a>Ausgabe aus der Kopieraktivität
Nach Abschluss aller Kopieraktivitätsausführungen wird der Pfad der Protokolldateien in der Ausgabe der jeweiligen Ausführung angezeigt. Sie finden die Protokolldateien im Pfad `https://[your-blob-account].blob.core.windows.net/[logFilePath]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`.  Die Protokolldateien sind die CSV-Dateien. 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

### <a name="the-schema-of-the-log-file"></a>Schema der Protokolldatei

Nachfolgend ist das Schema einer Protokolldatei aufgeführt.

Column | BESCHREIBUNG 
-------- | -----------  
Timestamp | Der Zeitstempel, wenn ADF das Objekt liest, schreibt oder überspringt.
Ebene | Die Protokollstufe dieses Elements. Der Wert kann „Warning“ oder „Info“ lauten.
Vorgangsname | Das Vorgangsverhalten der ADF-Kopieraktivität für jedes Objekt. Dies kann „FileRead“, „FileWrite“, „FileSkip“ oder „TabularRowSkip“ lauten.
OperationItem | Die Dateinamen oder übersprungenen Zeilen.
`Message` | Weitere Informationen zur Angabe, ob die Datei aus dem Quellspeicher gelesen oder in den Zielspeicher geschrieben wurde. Es kann auch der Grund angegeben sein, warum die Datei oder die Zeilen übersprungen wurden.

Es folgt ein Beispiel einer Protokolldatei. 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-10-19 08:39:13.6688152,Info,FileRead,"sample1.csv","Start to read file: {""Path"":""sample1.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:39:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
2020-10-19 08:40:13.6688152,Info,FileRead,"sample2.csv","Start to read file: {""Path"":""sample2.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:40:13.9003981,Info,FileWrite,"sample2.csv","Start to write file from source file: sample2.csv."
2020-10-19 08:45:17.6508407,Info,FileRead,"sample2.csv","Complete reading file successfully. "
2020-10-19 08:45:28.7390083,Info,FileWrite,"sample2.csv","Complete writing file from source file: sample2.csv. File is successfully copied."
```
In der vorstehenden Protokolldatei können Sie sehen, dass die Datei „sample1.csv“ übersprungen wurde, weil die Konsistenz zwischen Quell- und Zielspeicher nicht überprüft werden konnte. Weitere Details zu dem Grund, aus dem „sample1.csv“ inkonsistent wird: Die Datei wurde von anderen Anwendungen bei gleichzeitiger Ausführung der ADF-Kopieraktivität geändert. Sie können auch sehen, dass „sample2.csv“ erfolgreich aus dem Quell- in den Zielspeicher kopiert wurde.

Zur weiteren Analyse der Protokolldateien können Sie mehrere Analyse-Engines verwenden.  Nachfolgend sehen Sie einige Beispiele für die Verwendung von SQL-Abfragen zum Analysieren der Protokolldatei. Hierbei wird eine CSV-Protokolldatei in SQL-Datenbank importiert, wobei der Tabellenname „SessionLogDemo“ lauten kann.  

-   Ausgeben der Liste der kopierten Dateien 
```sql
select OperationItem from SessionLogDemo where Message like '%File is successfully copied%'
```

-   Ausgeben der Liste der kopierten Dateien innerhalb eines bestimmten Zeitraums 
```sql
select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%'
```

-   Ausgeben einer bestimmten Datei mit zugehöriger Kopierzeit und Metadaten 
```sql
select * from SessionLogDemo where OperationItem='<file name>'
```

-   Ausgeben einer Liste von Dateien mit deren Metadaten, die innerhalb eines Zeitraums kopiert wurden 
```sql
select * from SessionLogDemo where OperationName='FileRead' and Message like 'Start to read%' and OperationItem in (select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%')
```

-   Ausgeben der Liste der übersprungenen Dateien 
```sql
select OperationItem from SessionLogDemo where OperationName='FileSkip'
```

-   Ausgeben das Grunds, warum eine bestimmte Datei übersprungen wurde 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip'
```

-   Ausgeben einer Liste der Dateien, die aus demselben Grund übersprungen wurden: „Blobdatei ist nicht vorhanden“ 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip' and Message like '%UserErrorSourceBlobNotExist%'
```

-   Ausgeben des Dateinamens, der die längste Zeit zum Kopieren benötigt
```sql
select top 1 OperationItem, CopyDuration=DATEDIFF(SECOND, min(TIMESTAMP), max(TIMESTAMP)) from SessionLogDemo group by OperationItem order by CopyDuration desc
```


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den anderen Artikeln zur Kopieraktivität:

- [Kopieraktivität – Übersicht](copy-activity-overview.md)
- [Copy activity fault tolerance (Fehlertoleranz der Kopieraktivität)](copy-activity-fault-tolerance.md)
- [Datenkonsistenz in der Kopieraktivität](copy-activity-data-consistency.md)