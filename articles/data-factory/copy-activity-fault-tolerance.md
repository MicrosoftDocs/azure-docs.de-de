---
title: Fehlertoleranz der Kopieraktivität in Azure Data Factory
description: Hier erfahren Sie, wie Sie der Kopieraktivität in Azure Data Factory Fehlertoleranz hinzufügen, indem die inkompatiblen Daten übersprungen werden.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: yexu
ms.openlocfilehash: 0fe1470661c006399ea176af1112d271524b2a1f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390962"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Fehlertoleranz der Kopieraktivität in Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Aktuelle Version](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Wenn Sie Daten aus dem Quell- in den Zielspeicher kopieren, bietet die Azure Data Factory-Kopieraktivität eine bestimmte Ebene von Fehlertoleranzen, um eine Unterbrechung aufgrund Fehlern während der Datenverschiebung zu verhindern. Sie kopieren beispielsweise Millionen von Zeilen aus dem Quell- in den Zielspeicher. In der Zieldatenbank wurde ein Primärschlüssel (Primary Key, PK) erstellt, für die Quelldatenbank aber wurden keine Primärschlüssel definiert. Wenn Sie doppelte Zeilen aus der Quelle in das Ziel kopieren, wird diese PK-Verletzung in der Zieldatenbank angezeigt. Dann bietet Ihnen die Kopieraktivität zwei Möglichkeiten zur Behandlung solcher Fehler: 
- Sie können die Kopieraktivität abbrechen, sobald ein Fehler aufgetreten ist. 
- Sie können den Rest weiter kopieren, indem Sie die Fehlertoleranz aktivieren, um die inkompatiblen Daten zu überspringen. Überspringen Sie in diesem Fall beispielsweise die doppelte Zeile. Außerdem können Sie die übersprungenen Daten protokollieren, indem Sie das Sitzungsprotokoll innerhalb der Kopieraktivität aktivieren. Ausführlichere Informationen finden Sie im [Sitzungsprotokoll in der Copy-Aktivität](copy-activity-log.md).

## <a name="copying-binary-files"></a>Kopieren von Binärdateien 

ADF unterstützt die folgenden Fehlertoleranzszenarien beim Kopieren von Binärdateien. Sie können die Kopieraktivität wahlweise abbrechen oder den Rest in den folgenden Szenarien weiterhin kopieren:

1. Gleichzeitig werden die Dateien, die von ADF kopiert werden sollen, von anderen Anwendungen gelöscht.
2. Einige bestimmte Ordner oder Dateien erlauben ADF nicht den Zugriff, weil ACLs dieser Dateien oder Ordner eine höhere Berechtigungsstufe als die in ADF konfigurierten Verbindungsinformationen erfordern.
3. Mindestens eine Datei wird nicht überprüft, ob sie zwischen Quell- und Zielspeicher konsistent ist, wenn Sie die Einstellung für Datenkonsistenzprüfung in ADF aktivieren.

### <a name="configuration"></a>Konfiguration 
Wenn Sie Binärdateien zwischen Storage-Speichern kopieren, können Sie die Fehlertoleranz so aktivieren: 

```json
"typeProperties": { 
    "source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
            } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
    }, 
    "skipErrorFile": { 
        "fileMissing": true, 
        "fileForbidden": true, 
        "dataInconsistency": true,
        "invalidFileName": true        
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
skipErrorFile | Eine Gruppe von Eigenschaften zur Angabe der Fehlertypen, die während der Datenverschiebung übersprungen werden sollen. | | Nein
fileMissing | Eines der Schlüssel-Wert-Paare in der „skipErrorFile“-Eigenschaftensammlung zur Bestimmung, ob Sie von anderen Anwendungen gelöschte Dateien überspringen möchten, wenn ADF in der Zwischenzeit kopiert. <br/> – True: Sie möchten den Rest kopieren, indem Sie die von anderen Anwendungen gelöschten Dateien überspringen. <br/> – False: Die Kopieraktivität soll abgebrochen werden, sobald alle Dateien während der Datenverschiebung aus dem Quellspeicher gelöscht werden. <br/>Beachten Sie, dass diese Eigenschaft auf „true“ als Standardwert festgelegt wird. | True (Standard) <br/>False | Nein
fileForbidden | Eines der Schlüssel-Wert-Paare in der „skipErrorFile“-Eigenschaftensammlung zur Bestimmung, ob Sie die jeweiligen Dateien überspringen möchten, wenn die ACLs dieser Dateien oder Ordner eine höhere Berechtigungsstufe als die in ADF konfigurierte Verbindung erfordern. <br/> – True: Sie möchten den Rest kopieren, indem Sie die Dateien überspringen. <br/> – False: Sie möchten die Kopieraktivität abbrechen, nachdem Ihnen das Berechtigungsproblem bei Ordnern oder Dateien mitgeteilt wurde. | True <br/>False (Standard) | Nein
dataInconsistency | Eines der Schlüssel-Wert-Paare in der „skipErrorFile“-Eigenschaftensammlung zur Bestimmung, ob Sie die inkonsistenten Daten zwischen Quell- und Zielspeicher überspringen möchten. <br/> – True: Sie möchten den Rest kopieren, indem Sie inkonsistente Daten überspringen. <br/> – False: Sie möchten die Kopieraktivität abbrechen, sobald inkonsistente Daten gefunden wurden. <br/>Beachten Sie, dass diese Eigenschaft nur gültig ist, wenn Sie „validateDataConsistency“ als „True“ festlegen. | True <br/>False (Standard) | Nein
invalidFileName | Eines der Schlüssel-Wert-Paare in der skipErrorFile-Eigenschaftensammlung zur Bestimmung, ob Sie bestimmte Dateien überspringen möchten, wenn die Dateinamen im Zielspeicher ungültig sind. <br/> \- TRUE: Sie möchten den Rest kopieren, indem Sie die Dateien mit ungültigen Dateinamen überspringen. <br/> - FALSE: Sie möchten die Kopieraktivität abbrechen, wenn Dateien ungültige Dateinamen aufweisen. <br/>Beachten Sie, dass diese Eigenschaft nur funktioniert, wenn Sie Binärdateien aus einem Speicher in ADLS Gen2 oder aus AWS S3 in einen beliebigen Speicher kopieren. | True <br/>False (Standard) | Nein
logSettings  | Eine Gruppe von Eigenschaften, die angegeben werden können, wenn Sie die Namen der übersprungenen Objekte protokollieren möchten. | &nbsp; | Nein
linkedServiceName | Der verknüpfte Dienst von [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) oder [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) zum Speichern der Sitzungsprotokolldateien. | Die Namen eines verknüpften Diensts vom Typ `AzureBlobStorage` oder `AzureBlobFS`, der auf die Instanz verweist, in der Sie die Protokolldatei speichern. | Nein
path | Der Pfad der Protokolldateien. | Geben Sie den Pfad an, in dem Sie die Protokolldateien speichern. Wenn Sie keinen Pfad angeben, erstellt der Dienst automatisch einen Container. | Nein

> [!NOTE]
> Die folgenden Punkte sind die Voraussetzungen, um Fehlertoleranz bei der Kopieraktivität beim Kopieren von Binärdateien zu ermöglichen.
> Zum Überspringen bestimmter Dateien, wenn sie aus dem Quellspeicher gelöscht werden:
> - Das Quelldataset und das Senkendataset müssen im Binärformat vorliegen, und der Kompressionstyp darf nicht angegeben werden. 
> - Die unterstützten Datenspeichertypen sind Azure Blob Storage, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure File Storage, File System, FTP, SFTP, Amazon S3, Google Cloud Storage und HDFS.
> - Nur bei der Angabe mehrerer Dateien im Quelldataset, bei dem es sich um einen Ordner, einen Platzhalter oder eine Liste von Dateien handeln kann, kann die Kopieraktivität die jeweiligen fehlerhaften Dateien überspringen. Wenn im Quelldataset eine einzelne Datei angegeben ist, die an das Ziel kopiert werden soll, schlägt die Kopieraktivität fehl, wenn ein Fehler aufgetreten ist.
>
> Zum Überspringen bestimmter Dateien, wenn ihr Zugriff vom Quellspeicher aus unzulässig ist:
> - Das Quelldataset und das Senkendataset müssen im Binärformat vorliegen, und der Kompressionstyp darf nicht angegeben werden. 
> - Die unterstützten Datenspeichertypen sind Azure Blob Storage, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure File Storage, SFTP, Amazon S3 und HDFS.
> - Nur bei der Angabe mehrerer Dateien im Quelldataset, bei dem es sich um einen Ordner, einen Platzhalter oder eine Liste von Dateien handeln kann, kann die Kopieraktivität die jeweiligen fehlerhaften Dateien überspringen. Wenn im Quelldataset eine einzelne Datei angegeben ist, die an das Ziel kopiert werden soll, schlägt die Kopieraktivität fehl, wenn ein Fehler aufgetreten ist.
>
> Zum Überspringen bestimmter Dateien, wenn überprüft wird, dass sie zwischen Quell- und Zielspeicher inkonsistent sind:
> - Weitere Informationen finden Sie [hier](./copy-activity-data-consistency.md) im Dokument zur Datenkonsistenz.

### <a name="monitoring"></a>Überwachung 

#### <a name="output-from-copy-activity"></a>Ausgabe aus der Kopieraktivität
Sie können die Anzahl der Dateien, die gelesen, geschrieben und übersprungen werden, über die Ausgabe der jeweiligen Kopieraktivitätsausführung erhalten. 

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

#### <a name="session-log-from-copy-activity"></a>Sitzungsprotokoll aus der Kopieraktivität

Wenn Sie so konfigurieren, dass die Namen der übersprungenen Dateien protokolliert werden, können Sie die Protokolldatei unter diesem Pfad finden: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Die Protokolldateien müssen die CSV-Dateien sein. Das Schema der Protokolldatei lautet wie folgt:

Column | BESCHREIBUNG 
-------- | -----------  
Timestamp | Der Zeitstempel, der angibt, wann ADF die Datei überspringt.
Ebene | Die Protokollstufe dieses Elements. Sie lautet bei dem Element, mit dem das Überspringen der Datei angezeigt wird, „Warning“ (Warnung).
Vorgangsname | Das Vorgangsverhalten der ADF-Kopieraktivität für jede Datei. Es lautet „FileSkip“ zur Angabe der Datei, die übersprungen werden soll.
OperationItem | Die Namen der zu überspringenden Dateien.
`Message` | Weitere Informationen zu den Ursachen für das Überspringen der Datei.

Das Beispiel für eine Protokolldatei lautet wie folgt: 
```
Timestamp,Level,OperationName,OperationItem,Message 
2020-03-24 05:35:41.0209942,Warning,FileSkip,"bigfile.csv","File is skipped after read 322961408 bytes: ErrorCode=UserErrorSourceBlobNotExist,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=The required Blob is missing. ContainerName: https://transferserviceonebox.blob.core.windows.net/skipfaultyfile, path: bigfile.csv.,Source=Microsoft.DataTransfer.ClientLibrary,'." 
2020-03-24 05:38:41.2595989,Warning,FileSkip,"3_nopermission.txt","File is skipped after read 0 bytes: ErrorCode=AdlsGen2OperationFailed,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=ADLS Gen2 operation failed for: Operation returned an invalid status code 'Forbidden'. Account: 'adlsgen2perfsource'. FileSystem: 'skipfaultyfilesforbidden'. Path: '3_nopermission.txt'. ErrorCode: 'AuthorizationPermissionMismatch'. Message: 'This request is not authorized to perform this operation using this permission.'. RequestId: '35089f5d-101f-008c-489e-01cce4000000'..,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Operation returned an invalid status code 'Forbidden',Source=,''Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message='Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message=Operation returned an invalid status code 'Forbidden',Source=Microsoft.DataTransfer.ClientLibrary,',Source=Microsoft.DataTransfer.ClientLibrary,'." 
```
Im vorstehenden Protokoll können Sie sehen, dass „bigfile.csv“ übersprungen wurde, weil eine andere Anwendung diese Datei gelöscht hat, als sie von ADF kopiert wurde. Und „3_nopermission.txt“ wurde übersprungen, weil ADF aufgrund eines Berechtigungsproblems nicht darauf zugreifen darf.


## <a name="copying-tabular-data"></a>Kopieren von Tabellendaten 

### <a name="supported-scenarios"></a>Unterstützte Szenarios
Die Kopieraktivität unterstützt drei Szenarien zum Erkennen, Überspringen und Protokollieren von Tabellendaten:

- **Inkompatibilität zwischen dem Quelldatentyp und dem nativen Senkentyp**. 

    Beispiel: Kopieren von Daten aus einer CSV-Datei in Blob Storage in eine SQL-Datenbank mit einer Schemadefinition, die drei Spalten vom Typ „INT“ enthält. Die Zeilen der CSV-Datei, die numerische Daten wie z.B. 123,456,789 enthalten, werden erfolgreich in den Senkenspeicher kopiert. Die Zeilen mit nicht numerischen Werten, z.B. 123,456, abc, werden dagegen als nicht kompatibel erkannt und übersprungen.

- **Fehlende Übereinstimmung bei der Anzahl der Spalten zwischen der Quelle und der Senke**.

    Beispiel: Kopieren von Daten aus einer CSV-Datei in Blob Storage in eine SQL-Datenbank mit einer Schemadefinition, die sechs Spalten enthält. Die Zeilen der CSV-Datei, die sechs Spalten enthalten, werden erfolgreich in den Senkenspeicher kopiert. Die Zeilen der CSV-Datei mit mehr als sechs Spalten werden als inkompatibel erkannt und übersprungen.

- **Primärschlüsselverletzung beim Schreiben in SQL Server/Azure SQL-Datenbank/Azure Cosmos DB**

    Beispiel: Kopieren von Daten von einer SQL Server-Instanz in eine SQL-Datenbank. In der SQL-Datenbank der Senke ist ein Primärschlüssel definiert, in der SQL Server-Instanz der Quelle ist dagegen kein Primärschlüssel definiert. Die doppelten Zeilen, die in der Quelle vorhanden sind, können nicht in die Senke kopiert werden. Die Kopieraktivität kopiert nur die erste Zeile der Quelldaten in die Senke. Die nachfolgenden Quellzeilen, die den doppelten Primärschlüsselwert enthalten, werden als inkompatibel erkannt und übersprungen.

>[!NOTE]
>- Wenn Sie Daten mit PolyBase in Azure Synapse Analytics laden möchten, konfigurieren Sie die nativen Fehlertoleranzeinstellungen von PolyBase, indem Sie in der Kopieraktivität über [polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink) Ablehnungsrichtlinien angeben. Sie können für nicht mit PolyBase kompatible Zeilen weiterhin die Umleitung an ein Blob oder ADLS aktivieren, wie unten gezeigt.
>- Dieses Feature ist nicht anwendbar, wenn die Kopieraktivität zum Aufruf von [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) konfiguriert ist.
>- Dieses Feature ist nicht anwendbar, wenn die Kopieraktivität dafür konfiguriert ist, eine [gespeicherte Prozedur aus einer SQL-Senke](./connector-azure-sql-database.md#invoke-a-stored-procedure-from-a-sql-sink) aufzurufen.

### <a name="configuration"></a>Konfiguration
Das folgende Beispiel zeigt eine JSON-Definition, mit der das Überspringen der inkompatiblen Zeilen in der Kopieraktivität konfiguriert wird:

```json
"typeProperties": { 
    "source": { 
        "type": "AzureSqlSource" 
    }, 
    "sink": { 
        "type": "AzureSqlSink" 
    }, 
    "enableSkipIncompatibleRow": true, 
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
}, 
```

Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Gibt an, ob nicht kompatible Zeilen beim Kopieren übersprungen werden sollen. | True<br/>False (Standardwert) | Nein
logSettings | Eine Gruppe von Eigenschaften, die angegeben werden können, wenn Sie die inkompatiblen Zeilen protokollieren möchten. | &nbsp; | Nein
linkedServiceName | Der verknüpfte Dienst von [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) oder [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) zum Speichern des Protokolls mit den übersprungenen Zeilen. | Die Namen eines verknüpften Diensts vom Typ `AzureBlobStorage` oder `AzureBlobFS`, der auf die Instanz verweist, in der Sie die Protokolldatei speichern. | Nein
path | Der Pfad der Protokolldateien mit den übersprungenen Zeilen. | Geben Sie den gewünschten Pfad für die Protokollierung der inkompatiblen Daten an. Wenn Sie keinen Pfad angeben, erstellt der Dienst automatisch einen Container. | Nein

### <a name="monitor-skipped-rows"></a>Überwachen der übersprungenen Zeilen
Nach Abschluss der Ausführung der Kopieraktivität wird die Anzahl übersprungener Zeilen in der Ausgabe der Kopieraktivität angezeigt:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```

Wenn Sie so konfigurieren, dass die inkompatiblen Zeilen protokolliert werden, können Sie die Protokolldatei unter diesem Pfad finden: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Die Protokolldateien sind die CSV-Dateien. Das Schema der Protokolldatei lautet wie folgt:

Column | BESCHREIBUNG 
-------- | -----------  
Timestamp | Der Zeitstempel, der angibt, wann ADF die inkompatiblen Zeilen überspringt.
Ebene | Die Protokollstufe dieses Elements. Sie lautet „Warning“ (Warnung), wenn dieses Element die übersprungenen Zeilen anzeigt.
Vorgangsname | Das Vorgangsverhalten der ADF-Kopieraktivität für jede Zeile. Es lautet „TabularRowSkip“, um anzugeben, dass die betreffende inkompatible Zeile übersprungen wurde.
OperationItem | Die übersprungenen Zeilen aus dem Quelldatenspeicher.
`Message` | Weitere Informationen zu den Ursachen für die Inkompatibilität dieser bestimmten Zeile.


Der Inhalt der Protokolldatei kann beispielsweise wie folgt aussehen:

```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:32.2586581, Warning, TabularRowSkip, """data1"", ""data2"", ""data3""," "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'." 
2020-02-26 06:22:33.2586351, Warning, TabularRowSkip, """data4"", ""data5"", ""data6"",", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)." 
```

In der vorstehenden Beispielprotokolldatei können Sie sehen, dass eine einzige Zeile – „data1, data2, data3“ – aufgrund eines Typkonvertierungsproblems zwischen Quell- und Zielspeicher übersprungen wurde. Eine andere Zeile – „data4, data5, data6“ – wurde aufgrund eines PK-Verletzungsproblems aus dem Quell- in den Zielspeicher übersprungen. 


## <a name="copying-tabular-data-legacy"></a>Kopieren von Tabellendaten (Legacy):

Im Folgenden wird die Legacy-Methode zum Aktivieren der Fehlertoleranz gezeigt, wenn nur Tabellendaten kopiert werden. Wenn Sie eine neue Pipeline oder Aktivität erstellen, wird empfohlen, stattdessen von [hier](#copying-tabular-data) aus zu beginnen.

### <a name="configuration"></a>Konfiguration
Das folgende Beispiel zeigt eine JSON-Definition, mit der das Überspringen der inkompatiblen Zeilen in der Kopieraktivität konfiguriert wird:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Gibt an, ob nicht kompatible Zeilen beim Kopieren übersprungen werden sollen. | True<br/>False (Standardwert) | Nein
redirectIncompatibleRowSettings | Eine Gruppe von Eigenschaften, die angegeben werden können, wenn Sie die inkompatiblen Zeilen protokollieren möchten. | &nbsp; | Nein
linkedServiceName | Der verknüpfte Dienst von [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) oder [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) zum Speichern des Protokolls mit den übersprungenen Zeilen | Der Name eines verknüpften Diensts vom Typ `AzureStorage` oder `AzureDataLakeStore`, der auf die Instanz verweist, in der Sie die Protokolldatei speichern möchten. | Nein
path | Der Pfad der Protokolldatei, die die übersprungenen Zeilen enthält. | Geben Sie den gewünschten Pfad für die Protokollierung der inkompatiblen Daten an. Wenn Sie keinen Pfad angeben, erstellt der Dienst automatisch einen Container. | Nein

### <a name="monitor-skipped-rows"></a>Überwachen der übersprungenen Zeilen
Nach Abschluss der Ausführung der Kopieraktivität wird die Anzahl übersprungener Zeilen in der Ausgabe der Kopieraktivität angezeigt:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Wenn Sie das Protokollieren der nicht kompatiblen Zeilen konfigurieren, finden Sie die Protokolldatei in diesem Pfad: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Die Protokolldateien können nur CSV-Dateien sein. Die ursprünglichen übersprungenen Daten werden bei Bedarf mit Komma als Spaltentrennzeichen protokolliert. Wir fügen den ursprünglichen Quelldaten in der Protokolldatei zwei weitere Spalten „ErrorCode“ und „ErrorMessage“ hinzu, die die Grundursache der Inkompatibilität angeben. „ErrorCode“ und „ErrorMessage“ werden in doppelte Anführungszeichen gesetzt. 

Der Inhalt der Protokolldatei kann beispielsweise wie folgt aussehen:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den anderen Artikeln zur Kopieraktivität:

- [Kopieraktivität – Übersicht](copy-activity-overview.md)
- [Leistung der Kopieraktivität](copy-activity-performance.md)