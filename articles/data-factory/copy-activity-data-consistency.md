---
title: Datenkonsistenzprüfung in der Kopieraktivität
description: Hier erfahren Sie, wie Sie die Datenkonsistenzprüfung in der Kopieraktivität in Azure Data Factory aktivieren.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: b71657f67c1b9c623d6d48f33b986ac43533cca6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373015"
---
#  <a name="data-consistency-verification-in-copy-activity"></a>Datenkonsistenzprüfung in der Kopieraktivität

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Wenn Sie Daten aus dem Quell- in den Zielspeicher verschieben, bietet die Azure Data Factory-Kopieraktivität eine Option, mit der Sie eine zusätzliche Datenkonsistenzprüfung durchführen können. Damit stellen Sie sicher, dass die Daten nicht nur erfolgreich aus dem Quell- in den Zielspeicher kopiert werden, sondern auch dass überprüft wird, ob sie zwischen Quell- und Zielspeicher konsistent sind. Wenn während der Datenverschiebung inkonsistente Dateien gefunden werden, können Sie entweder die Kopieraktivität abbrechen oder auch noch die restlichen Dateien kopieren. Hierzu aktivieren Sie die Einstellung für Fehlertoleranz, damit inkonsistente Daten übersprungen werden. Die Namen der übersprungenen Dateien können Sie durch Aktivieren der Einstellung für das Sitzungsprotokoll in der Kopieraktivität abrufen. Weitere Informationen finden Sie im [Sitzungsprotokoll in der Kopieraktivität](copy-activity-log.md).

## <a name="supported-data-stores-and-scenarios"></a>Unterstützte Datenspeicher und Szenarien

-   Die Datenkonsistenzprüfung wird von allen Connectors außer FTP, SFTP und HTTP unterstützt. 
-   Die Datenkonsistenzprüfung wird im Stagingkopierszenario nicht unterstützt.
-   Beim Kopieren von Binärdateien ist die Datenkonsistenzprüfung nur verfügbar, wenn in der Kopieraktivität das Verhalten „PreserveHierarchy“ festgelegt ist.
-   Wenn Sie mehrere Binärdateien in einer einzigen Kopieraktivität mit aktivierter Datenkonsistenzprüfung kopieren möchten, stehen Ihnen zwei Optionen zur Verfügung: Sie können entweder die Kopieraktivität abbrechen oder die restlichen Dateien weiter kopieren, indem Sie die Fehlertoleranzeinstellung so einrichten, dass inkonsistente Dateien übersprungen werden. 
-   Beim Kopieren einer Tabelle in einer einzigen Kopieraktivität mit aktivierter Datenkonsistenzprüfung tritt bei der Kopieraktivität ein Fehler auf, wenn die Anzahl der aus der Quelle gelesenen Zeilen sich von der Anzahl der in das Ziel kopierten Zeilen plus der Anzahl der inkompatiblen und übersprungenen Zeilen unterscheidet.


## <a name="configuration"></a>Konfiguration
Das folgende Beispiel zeigt eine JSON-Definition, mit der die Datenkonsistenzprüfung in der Kopieraktivität aktiviert wird: 

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
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
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
validateDataConsistency | Wenn Sie diese Eigenschaft auf TRUE festlegen, prüft die Kopieraktivität beim Kopieren von Binärdateien die Dateigröße, das Datum der letzten Änderung und die MD5-Prüfsumme für jede aus dem Quell- in den Zielspeicher kopierte Binärdatei, um die Datenkonsistenz zwischen den beiden Speichern sicherzustellen. Beim Kopieren von Tabellendaten überprüft die Kopieraktivität nach Abschluss des Auftrags die Gesamtzeilenzahl, um sicherzustellen, dass die Gesamtanzahl der aus der Quelle gelesenen Zeilen mit der Anzahl der in das Ziel kopierten Zeilen plus der Anzahl der inkompatiblen und übersprungenen Zeilen übereinstimmt. Beachten Sie, dass die Kopierleistung durch Aktivieren dieser Option beeinträchtigt wird.  | True<br/>False (Standardwert) | Nein
dataInconsistency | Eines der Schlüssel-Wert-Paare in der skipErrorFile-Eigenschaftensammlung zur Bestimmung, ob Sie die inkonsistenten Dateien überspringen möchten. <br/> – TRUE: Sie möchten den Rest kopieren, indem Sie inkonsistente Dateien überspringen.<br/> – FALSE: Sie möchten die Kopieraktivität abbrechen, sobald inkonsistente Dateien gefunden wurden.<br/>Beachten Sie, dass diese Eigenschaft nur gültig ist, wenn Sie Binärdateien kopieren und „validateDataConsistency“ auf TRUE festlegen.  | True<br/>False (Standardwert) | Nein
logSettings | Eine Gruppe von Eigenschaften, die angegeben werden können, um das Sitzungsprotokoll zum Protokollieren übersprungener Dateien zu aktivieren. | | Nein
linkedServiceName | Der verknüpfte Dienst von [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) oder [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) zum Speichern der Sitzungsprotokolldateien. | Die Namen eines verknüpften Diensts vom Typ `AzureBlobStorage` oder `AzureBlobFS`, der auf die Instanz verweist, in der Sie die Protokolldateien speichern. | Nein
path | Der Pfad der Protokolldateien. | Geben Sie den Pfad an, in dem die Protokolldateien gespeichert werden sollen. Wenn Sie keinen Pfad angeben, erstellt der Dienst automatisch einen Container. | Nein

>[!NOTE]
>- Beim Kopieren von Binärdateien aus oder in Azure-Blobs oder Azure Data Lake Storage Gen2 überprüft ADF unter Verwendung der [Azure-Blob-API](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy&preserve-view=true) und der [Azure Data Lake Storage Gen2-API](/rest/api/storageservices/datalakestoragegen2/path/update#request-headers) die MD5-Prüfsumme auf Blockebene. Wenn ContentMD5 in Dateien im Azure-Blob oder in Azure Data Lake Storage Gen2 als Datenquelle vorhanden ist, führt ADF nach dem Lesen der Dateien auch eine Überprüfung der MD5-Prüfsumme auf Dateiebene durch. Nach dem Kopieren von Dateien in das Azure-Blob oder in Azure Data Lake Storage Gen2 als Datenziel schreibt ADF ContentMD5 in das Ziel. Dies kann in Downstreamanwendungen zur Überprüfung der Datenkonsistenz weiterverwendet werden.
>- Beim Kopieren von Binärdateien zwischen Speichern führt ADF eine Überprüfung der Dateigröße durch.

## <a name="monitoring"></a>Überwachung

### <a name="output-from-copy-activity"></a>Ausgabe aus der Kopieraktivität
Nach Abschluss aller Kopieraktivitätsausführungen wird das Ergebnis der Datenkonsistenzprüfung aus der Ausgabe der jeweiligen Ausführung angezeigt:

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
Hier können Sie die Details der Datenkonsistenzprüfung aus der Eigenschaft „dataConsistencyVerification“ sehen.

Wert von **VerificationResult**: 
-   **Überprüft**:  Ihre kopierten Daten wurden auf Konsistenz zwischen Quell- und Zielspeicher überprüft. 
-   **NotVerified** (NichtÜberprüft): Ihre kopierten Daten wurden nicht auf Konsistenz überprüft, weil Sie „validateDataConsistency“ in der Kopieraktivität nicht aktiviert haben. 
-   **Nicht unterstützt**: Ihre kopierten Daten wurden nicht auf Konsistenz überprüft, weil die Datenkonsistenzprüfung bei diesem bestimmten Kopierpaar nicht unterstützt wird. 

Wert von **InconsistentData**: 
-   **Gefunden**: Die ADF-Kopieraktivität hat inkonsistente Daten gefunden. 
-   **Übersprungen**: Die ADF-Kopieraktivität hat inkonsistente Daten gefunden und übersprungen. 
-   **Keine:** Die ADF-Kopieraktivität hat keine inkonsistenten Daten gefunden. Der Grund: Bei der Überprüfung wurde festgestellt, dass Ihre Daten zwischen Quell- und Zielspeicher konsistent sind, oder Sie haben „validateDataConsistency“ in der Kopieraktivität deaktiviert. 

### <a name="session-log-from-copy-activity"></a>Sitzungsprotokoll aus der Kopieraktivität

Wenn Sie so konfigurieren, dass die inkonsistente Datei protokolliert wird, können Sie die Protokolldatei unter diesem Pfad finden: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`.  Die Protokolldateien sind die CSV-Dateien. 

Das Schema einer Protokolldatei lautet wie folgt:

Column | BESCHREIBUNG 
-------- | -----------  
Timestamp | Der Zeitstempel, der angibt, wann ADF die inkonsistenten Dateien überspringt.
Ebene | Die Protokollstufe dieses Elements. Sie lautet bei dem Element, mit dem das Überspringen der Datei angezeigt wird, „Warning“ (Warnung).
Vorgangsname | Das Vorgangsverhalten der ADF-Kopieraktivität für jede Datei. Es lautet „FileSkip“ zur Angabe der Datei, die übersprungen werden soll.
OperationItem | Der Name der zu überspringenden Datei.
`Message` | Weitere Informationen zu den Ursachen für das Überspringen von Dateien.

Das Beispiel für eine Protokolldatei lautet wie folgt: 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
In der vorstehenden Protokolldatei können Sie sehen, dass die Datei „sample1.csv“ übersprungen wurde, weil die Konsistenz zwischen Quell- und Zielspeicher nicht überprüft werden konnte. Weitere Details zu dem Grund, aus dem „sample1.csv“ inkonsistent wird: Die Datei wurde von anderen Anwendungen bei gleichzeitiger Ausführung der ADF-Kopieraktivität geändert. 



## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den anderen Artikeln zur Kopieraktivität:

- [Kopieraktivität – Übersicht](copy-activity-overview.md)
- [Copy activity fault tolerance (Fehlertoleranz der Kopieraktivität)](copy-activity-fault-tolerance.md)