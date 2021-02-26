---
title: Problembehandlung für Azure Data Factory-Connectors
description: Es wird beschrieben, wie Sie in Azure Data Factory Connectorprobleme beheben.
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/08/2021
ms.author: jingwang
ms.custom: has-adal-ref
ms.openlocfilehash: 63a690ffaaefc506de296d43e7de13020fbd584a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366924"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Problembehandlung für Azure Data Factory-Connectors

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel werden gängige Methoden zum Beheben von Problemen mit Azure Data Factory-Connectors behandelt.

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code-azurebloboperationfailed"></a>Fehlercode: AzureBlobOperationFailed

- **Meldung:** „Fehler beim Blobvorgang. ContainerName: %containerName;, Pfad: %path;.“

- **Ursache:** Problem beim Speichervorgang für Blobs.

- **Empfehlung**:  Informationen zum Überprüfen der Fehlerdetails finden Sie unter [Blob Storage-Fehlercodes](https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes). Wenden Sie sich an das Blob Storage-Team, um weitere Hilfe zu erhalten.


### <a name="invalid-property-during-copy-activity"></a>Ungültige Eigenschaft während der Copy-Aktivität

- **Meldung**: `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **Ursache:** Der im Dataset definierte Typ stimmt nicht mit dem in der Copy-Aktivität definierten Quell- oder Senkentyp überein.

- **Lösung:** Bearbeiten Sie die JSON-Definition für das Dataset oder die Pipeline, um die Typen zu vereinheitlichen, und führen Sie die Bereitstellung dann erneut aus.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Fehlermeldung: Anforderung ist zu groß.

- **Symptome:** Wenn Sie Daten in Azure Cosmos DB mit einer Standardgröße für Schreibbatches kopieren, erhalten Sie den folgenden Fehler: `Request size is too large.`

- **Ursache:** Azure Cosmos DB schränkt die Größe einer einzelnen Anforderung auf 2 MB ein. Die Formel lautet: *Anforderungsgröße = Einzeldokumentgröße \* Schreibbatchgröße*. Wenn Ihr Dokument zu groß ist, ist beim Standardverhalten auch die Anforderung zu groß. Sie können die Batchgröße für Schreibvorgänge optimieren.

- **Lösung:** Reduzieren Sie in der Senke der Kopieraktivität den Wert für die *Schreibbatchgröße* (Standardwert ist „10.000“).

### <a name="error-message-unique-index-constraint-violation"></a>Fehlermeldung: Einschränkungsverletzung für eindeutigen Index

- **Symptome:** Wenn Sie Daten in Azure Cosmos DB kopieren, erhalten Sie den folgenden Fehler:

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **Ursache:** Es gibt zwei mögliche Ursachen:

    - Ursache 1: Wenn Sie **Einfügen** als Schreibverhalten verwenden, bedeutet dieser Fehler, dass Ihre Quelldaten Zeilen oder Objekte mit der gleichen ID enthalten.
    - Ursache 2: Wenn Sie **Upsert** als Schreibverhalten verwenden und einen anderen eindeutigen Schlüssel für den Container festlegen, bedeutet dieser Fehler, dass Ihre Quelldaten über Zeilen oder Objekte mit unterschiedlichen IDs verfügen, aber für den definierten eindeutigen Schlüssel den gleichen Wert aufweisen.

- **Lösung:** 

    - Legen Sie für Ursache 1 die Option **Upsert** als Schreibverhalten fest.
    - Stellen Sie für Ursache 2 sicher, dass jedes Dokument über einen anderen Wert für den definierten eindeutigen Schlüssel verfügt.

### <a name="error-message-request-rate-is-large"></a>Fehlermeldung: Anforderungsrate ist hoch

- **Symptome:** Wenn Sie Daten in Azure Cosmos DB kopieren, erhalten Sie den folgenden Fehler:

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **Ursache:** Die Anzahl der verwendeten Anforderungseinheiten (Request Units, RUs) ist größer als die verfügbaren RUs, die in Azure Cosmos DB konfiguriert sind. Informationen dazu, wie Azure Cosmos DB RUs berechnet, finden Sie unter [Anforderungseinheiten in Azure Cosmos DB](../cosmos-db/request-units.md#request-unit-considerations).

- **Lösung:** Versuchen Sie es mit einer der beiden folgenden Lösungen:

    - Legen Sie die Anzahl der *Container-RUs* in Azure Cosmos DB auf einen größeren Wert fest. Mit dieser Lösung wird die Leistung der Kopieraktivität verbessert, aber es fallen Mehrkosten in Azure Cosmos DB an. 
    - Legen Sie *writeBatchSize* auf einen niedrigeren Wert (z. B. auf 1.000) fest, und verringern Sie den Wert von *parallelCopies* (z. B. auf 1). Mit dieser Lösung wird die Leistung der Kopierausführung verringert, aber es fallen keine Mehrkosten in Azure Cosmos DB an.

### <a name="columns-missing-in-column-mapping"></a>Fehlenden Spalte in Spaltenzuordnung

- **Symptome:** Beim Importieren eines Schemas für Azure Cosmos DB für die Spaltenzuordnung fehlen einige Spalten. 

- **Ursache:** Data Factory leitet das Schema aus den ersten zehn Cosmos DB-Dokumenten ab. Wenn einige Dokumentspalten oder -eigenschaften keine Werte enthalten, wird das Schema nicht von Data Factory erkannt und folglich auch nicht angezeigt.

- **Lösung:** Sie können die Abfrage wie im folgenden Code gezeigt optimieren, um zu erzwingen, dass die Spaltenwerte im Resultset mit leeren Werten angezeigt werden. Nehmen Sie an, dass die Spalte *impossible* in den ersten zehn Dokumenten fehlt. Alternativ können Sie die Spalte auch manuell für die Zuordnung hinzufügen.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Fehlermeldung: GuidRepresentation für den Reader ist CSharpLegacy

- **Symptome:** Wenn Sie Daten aus Azure Cosmos DB MongoAPI oder MongoDB mit dem UUID-Feld (Universally Unique Identifier) kopieren, erhalten Sie den folgenden Fehler:

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **Ursache:** Es gibt zwei Möglichkeiten, die UUID in BSON (Binary JSON) darzustellen: UuidStardard und UuidLegacy. Standardmäßig wird UuidLegacy zum Lesen von Daten verwendet. Sie erhalten einen Fehler, wenn für Ihre UUID-Daten in MongoDB UuidStandard festgelegt ist.

- **Lösung:** Fügen Sie der MongoDB-Verbindungszeichenfolge die Option *uuidRepresentation=standard* hinzu. Weitere Informationen finden Sie im Artikel zur [MongoDB-Verbindungszeichenfolge](connector-mongodb.md#linked-service-properties).
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL-API)

### <a name="error-code-cosmosdbsqlapioperationfailed"></a>Fehlercode: CosmosDbSqlApiOperationFailed

- **Meldung**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Ursache:** Beim CosmosDbSqlApi-Vorgang ist ein Problem aufgetreten.

- **Empfehlung**:  Um die Fehlerdetails zu überprüfen, lesen Sie das [Azure Cosmos DB-Hilfedokument](../cosmos-db/troubleshoot-dot-net-sdk.md). Um weitere Unterstützung zu erhalten, nehmen Sie Kontakt mit dem Azure Cosmos DB-Team auf.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Fehlermeldung: Die zugrunde liegende Verbindung wurde geschlossen: Es konnte keine Vertrauensstellung für den sicheren SSL/TLS-Kanal eingerichtet werden.

- **Symptome:** Bei der Kopieraktivität tritt der folgende Fehler auf: 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **Ursache:** Bei der Zertifikatüberprüfung ist während des TLS-Handshakes ein Fehler aufgetreten.

- **Lösung:** Um dieses Problem zu umgehen, können Sie die Überprüfung von TLS (Transport Layer Security) für Azure Data Lake Storage Gen1 mithilfe der gestagten Kopie überspringen. Sie müssen dieses Problem reproduzieren und die Netzwerküberwachungs-Ablaufverfolgung (netmon) erfassen. Anschließend prüfen Sie zusammen mit dem Netzwerkteam die Konfiguration des lokalen Netzwerks.

    ![Abbildung: Azure Data Lake Storage Gen1-Verbindungen zum Beheben von Problemen.](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Fehlermeldung: The remote server returned an error: (403) Unzulässig

- **Symptome:** Für die Kopieraktivität tritt der folgende Fehler auf: 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **Ursache:** Eine mögliche Ursache ist, dass der von Ihnen verwendete Dienstprinzipal oder die verwaltete Identität nicht über die Berechtigung zum Zugreifen auf bestimmte Ordner oder Dateien verfügt.

- **Lösung:** Gewähren Sie die entsprechenden Berechtigungen für alle Ordner und Unterordner, die Sie kopieren müssen. Weitere Informationen finden Sie unter [Kopieren von Daten nach und aus Azure Data Lake Storage Gen1 mithilfe von Azure Data Factory](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Fehlermeldung: Fehler beim Abrufen des Zugriffstokens mit dem Dienstprinzipal. ADAL-Fehler: service_unavailable

- **Symptome:** Bei der Kopieraktivität tritt der folgende Fehler auf:

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **Ursache:** Wenn der Tokenserver des Diensts von Azure Active Directory nicht verfügbar ist (also die Auslastung für die Verarbeitung von Anforderungen zu hoch ist), wird der HTTP-Fehler 503 zurückgegeben. 

- **Lösung:** Führen Sie die Kopieraktivität nach einigen Minuten erneut aus.


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>Fehlercode: ADLSGen2OperationFailed

- **Meldung**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Ursachen und Empfehlungen:** Dieser Fehler kann verschiedene Ursachen haben. In der Liste unten finden Sie mögliche Ursachenanalysen und Empfehlungen.

  | Ursachenanalyse                                               | Empfehlung                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Wenn Azure Data Lake Storage Gen2 diesen Fehler auslöst, ist für einen Vorgang ein Fehler aufgetreten.| Überprüfen Sie die von Azure Data Lake Storage Gen2 ausgegebene detaillierte Fehlermeldung. Wenn der Fehler ein vorübergehender Fehler ist, wiederholen Sie den Vorgang. Um weitere Hilfe zu erhalten, wenden Sie sich an den Azure Storage-Support, und geben Sie die Anforderungs-ID in der Fehlermeldung an. |
  | Wenn die Fehlermeldung die Zeichenfolge „Forbidden“ (Verboten) enthält, verfügt der von Ihnen verwendete Dienstprinzipal oder die verwaltete Identität möglicherweise nicht über die erforderliche Berechtigung, um auf Azure Data Lake Storage Gen2 zuzugreifen. | Um diesen Fehler zu beheben, lesen Sie [Kopieren und Transformieren von Daten in Azure Data Lake Storage Gen2 mithilfe von Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication). |
  | Wenn die Fehlermeldung die Zeichenfolge „InternalServerError“ enthält, wird der Fehler von Azure Data Lake Storage Gen2 zurückgegeben. | Dieser Fehler wird möglicherweise durch einen vorübergehenden Fehler verursacht. Wenn dies so ist, wiederholen Sie den Vorgang. Wenden Sie sich an den Azure Storage-Support, und geben Sie die Anforderungs-ID aus der Fehlermeldung an, wenn das Problem weiterhin besteht. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>Timeoutfehler bei der Anforderung des Azure Data Lake Storage Gen2-Kontos

- **Nachricht**: 
  * Fehlercode = `UserErrorFailedBlobFSOperation`
  * Fehlermeldung = `BlobFS operation failed for: A task was canceled.`

- **Ursache:** Das Problem wird durch den Timeoutfehler der Azure Data Lake Storage Gen2-Senke verursacht, der in der Regel auf dem selbstgehosteten IR-Computer (Integration Runtime) auftritt.

- **Empfehlung**: 

    - Platzieren Sie den Computer mit der selbstgehosteten IR und das Azure Data Lake Storage Gen2-Zielkonto nach Möglichkeit in derselben Region. Dadurch vermeiden Sie einen zufälligen Timeoutfehler und erzielen eine bessere Leistung.

    - Überprüfen Sie, ob spezielle Netzwerkeinstellungen wie ExpressRoute vorliegen, und stellen Sie sicher, dass das Netzwerk über genügend Bandbreite verfügt. Es wird empfohlen, die Einstellung für gleichzeitige Aufträge der selbstgehosteten IR zu verringern, wenn die Gesamtbandbreite gering ist. Dies kann dazu beitragen, den Wettbewerb von Netzwerkressourcen über mehrere gleichzeitige Aufträge hinweg zu vermeiden.

    - Wenn die Datei mittelgroß oder klein ist, verwenden Sie für nicht binäre Kopiervorgänge eine kleinere Blockgröße, um einen derartigen Timeoutfehler zu vermeiden. Weitere Informationen finden Sie unter [Blob Storage: Put-Block](/rest/api/storageservices/put-block).

       Um die benutzerdefinierte Blockgröße anzugeben, bearbeiten Sie die Eigenschaft im JSON-Datei-Editor wie hier gezeigt:
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-files-storage"></a>Azure Files-Speicher

### <a name="error-code-azurefileoperationfailed"></a>Fehlercode: AzureFileOperationFailed

- **Meldung**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Ursache:** Problem beim Speichervorgang von Azure Files.

- **Empfehlung**:  Informationen zum Überprüfen der Fehlerdetails finden Sie unter [Azure Files-Hilfe](https://docs.microsoft.com/rest/api/storageservices/file-service-error-codes). Um weitere Unterstützung zu erhalten, nehmen Sie Kontakt mit dem Azure Files-Team auf.


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure Synapse Analytics, Azure SQL-Datenbank und SQL Server

### <a name="error-code-sqlfailedtoconnect"></a>Fehlercode: SqlFailedToConnect

- **Meldung**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **Ursachen und Empfehlungen:** Dieser Fehler kann verschiedene Ursachen haben. In der Liste unten finden Sie mögliche Ursachenanalysen und Empfehlungen.

    | Ursachenanalyse                                               | Empfehlung                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Wenn die Fehlermeldung die Zeichenfolge „SqlErrorNumber=47073“ enthält, bedeutet dies für Azure SQL, dass der öffentliche Netzwerkzugriff in der Konnektivitätseinstellung verweigert wird. | Legen Sie in der Azure SQL-Firewall die Option **Öffentlichen Netzwerkzugriff verweigern** auf *Nein* fest. Weitere Informationen finden Sie unter [Azure SQL-Konnektivitätseinstellungen](https://docs.microsoft.com/azure/azure-sql/database/connectivity-settings#deny-public-network-access). |
    | Wenn die Fehlermeldung bei Azure SQL einen SQL-Fehlercode wie „SqlErrorNumber=[errorcode]“ enthält, finden Sie weitere Informationen im Leitfaden zur Problembehandlung von Azure SQL. | Eine Empfehlung finden Sie unter [Beheben von Konnektivitätsproblemen und anderen Fehlern mit Azure SQL-Datenbank und Azure SQL Managed Instance](https://docs.microsoft.com/azure/azure-sql/database/troubleshoot-common-errors-issues). |
    | Überprüfen Sie, ob Port 1433 in der Zulassungsliste der Firewall enthalten ist. | Weitere Informationen finden Sie unter [Von SQL Server verwendete Ports](https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-). |
    | Wenn die Fehlermeldung die Zeichenfolge „SqlException“ enthält, bedeutet dies für SQL-Datenbank, dass für einen bestimmten Vorgang ein Fehler aufgetreten ist. | Um weitere Informationen dazu zu erhalten, suchen Sie in [Datenbank-Engine-Fehler](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors) nach dem SQL-Fehlercode. Wenn Sie weitere Hilfe benötigen, wenden Sie sich an den Azure SQL-Support. |
    | Wenn dies ein vorübergehendes Problem ist (z. B. eine instabile Netzwerkverbindung), fügen Sie zur Behebung in der Aktivitätsrichtlinie einen Wiederholungsversuch hinzu. | Weitere Informationen finden Sie unter [Pipelines und Aktivitäten in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities#activity-policy). |
    | Wenn die Fehlermeldung die Zeichenfolge „Client with IP address '…' is not allowed to access the server“ enthält und Sie versuchen, eine Verbindung zu Azure SQL-Datenbank herzustellen, wird dieser Fehler in der Regel durch ein Problem mit der Firewall von Azure SQL-Datenbank verursacht. | Aktivieren Sie in der Azure SQL Server-Firewallkonfiguration die Option **Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten**. Weitere Informationen finden Sie unter [IP-Firewallregeln für Azure SQL-Datenbank und Azure Synapse](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure). |
    
### <a name="error-code-sqloperationfailed"></a>Fehlercode: SqlOperationFailed

- **Meldung**: `A database operation failed. Please search error to get more details.`

- **Ursachen und Empfehlungen:** Dieser Fehler kann verschiedene Ursachen haben. In der Liste unten finden Sie mögliche Ursachenanalysen und Empfehlungen.

    | Ursachenanalyse                                               | Empfehlung                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Wenn die Fehlermeldung die Zeichenfolge „SqlException“ enthält, löst SQL-Datenbank einen Fehler aus, der angibt, dass für einen bestimmten Vorgang ein Fehler aufgetreten ist. | Wenn der SQL-Fehler nicht eindeutig ist, versuchen Sie, die Datenbank auf den aktuellen Kompatibilitätsgrad „150“ zu ändern. Es können SQL-Fehler der neuesten Version ausgelöst werden. Weitere Informationen finden Sie in der [Dokumentation](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat). <br/> Um weitere Informationen zur Problembehandlung von SQL-Problemen zu erhalten, suchen Sie in [Datenbank-Engine-Fehler](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors) nach dem SQL-Fehlercode. Wenn Sie weitere Hilfe benötigen, wenden Sie sich an den Azure SQL-Support. |
    | Wenn die Fehlermeldung die Zeichenfolge „PdwManagedToNativeInteropException“ enthält, wird der Fehler in der Regel durch einen Konflikt zwischen den Spaltengrößen von Quelle und Senke verursacht. | Überprüfen Sie die Größe der Quell- und Senkenspalten. Wenn Sie weitere Hilfe benötigen, wenden Sie sich an den Azure SQL-Support. |
    | Wenn die Fehlermeldung die Zeichenfolge „InvalidOperationException“ enthält, wird der Fehler normalerweise durch ungültige Eingabedaten verursacht. | Wenn Sie ermitteln möchten, in welcher Zeile das Problem auftritt, aktivieren Sie die Fehlertoleranzfunktion für die Kopieraktivität, die problematische Zeilen zur weiteren Untersuchung in den Speicher umleiten kann. Weitere Informationen finden Sie unter [Fehlertoleranz der Kopieraktivität in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance). |


### <a name="error-code-sqlunauthorizedaccess"></a>Fehlercode: SqlUnauthorizedAccess

- **Meldung**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Ursache:** Die Anmeldeinformationen sind falsch, oder das Anmeldekonto kann nicht auf SQL-Datenbank zugreifen.

- **Empfehlung**:  Überprüfen Sie, ob das Anmeldekonto über ausreichende Berechtigungen für den Zugriff auf die SQL-Datenbank verfügt.


### <a name="error-code-sqlopenconnectiontimeout"></a>Fehlercode: SqlOpenConnectionTimeout

- **Meldung**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Ursache:** Das Problem könnte ein vorübergehender SQL-Datenbank-Fehler sein.

- **Empfehlung**:  Wiederholen Sie den Vorgang, um die Verbindungszeichenfolge für den verknüpften Dienst mit einem größeren Timeoutwert für die Verbindung zu aktualisieren.


### <a name="error-code-sqlautocreatetabletypemapfailed"></a>Fehlercode: SqlAutoCreateTableTypeMapFailed

- **Meldung**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Ursache:** Die Tabelle für die automatische Erstellung kann die Quellanforderung nicht erfüllen.

- **Empfehlung**:  Aktualisieren Sie den Spaltentyp in *mappings* (Zuordnungen), oder erstellen Sie manuell die Senkentabelle auf dem Zielserver.


### <a name="error-code-sqldatatypenotsupported"></a>Fehlercode: SqlDataTypeNotSupported

- **Meldung**: `A database operation failed. Check the SQL errors.`

- **Ursache:** Wenn das Problem in der SQL-Quelle auftritt und der Fehler im Zusammenhang mit dem SqlDateTime-Überlauf steht, liegt der Datenwert über dem logischen Eingabebereich (01.01.1753 12:00:00 Uhr bis 31.12.9999 23:59:59 Uhr).

- **Empfehlung**:  Wandeln Sie den Typ in der SQL-Quellabfrage in eine Zeichenfolge um, oder ändern Sie in der Spaltenzuordnung der Kopieraktivität den Spaltentyp in *String*.

- **Ursache:** Wenn das Problem in der SQL-Senkentabelle auftritt und der Fehler im Zusammenhang mit dem SqlDateTime-Überlauf steht, liegt der Datenwert über dem zulässigen Bereich in der Senkentabelle.

- **Empfehlung**:  Aktualisieren Sie den entsprechenden Spaltentyp in der Senkentabelle auf den Typ *datetime2*.


### <a name="error-code-sqlinvaliddbstoredprocedure"></a>Fehlercode: SqlInvalidDbStoredProcedure

- **Meldung**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Ursache:** Die angegebene gespeicherte Prozedur ist ungültig. Dies kann daran liegen, dass die gespeicherte Prozedur keine Daten zurückgibt.

- **Empfehlung**:  Überprüfen Sie die gespeicherte Prozedur mithilfe von SQL-Tools. Stellen Sie sicher, dass die gespeicherte Prozedur Daten zurückgeben kann.


### <a name="error-code-sqlinvaliddbquerystring"></a>Fehlercode: SqlInvalidDbQueryString

- **Meldung**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Ursache:** Die angegebene SQL-Abfrage ist ungültig. Dies kann daran liegen, dass die Abfrage keine Daten zurückgibt.

- **Empfehlung**:  Überprüfen Sie die SQL-Abfrage mithilfe von SQL-Tools. Stellen Sie sicher, dass die Abfrage Daten zurückgeben kann.


### <a name="error-code-sqlinvalidcolumnname"></a>Fehlercode: SqlInvalidColumnName

- **Meldung**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Ursache:** Die Spalte kann nicht gefunden werden, weil die Konfiguration möglicherweise falsch ist.

- **Empfehlung**:  Überprüfen Sie die Spalte in der Abfrage: *structure* (Struktur) im Dataset und *mappings* (Zuordnungen) in der Aktivität.


### <a name="error-code-sqlbatchwritetimeout"></a>Fehlercode: SqlBatchWriteTimeout

- **Meldung**: `Timeouts in SQL write operation.`

- **Ursache:** Das Problem kann durch einen vorübergehenden SQL-Datenbank-Fehler verursacht werden.

- **Empfehlung**:  Wiederholen Sie den Vorgang. Sollte das Problem weiterhin bestehen, wenden Sie sich an den Azure SQL-Support.


### <a name="error-code-sqlbatchwritetransactionfailed"></a>Fehlercode: SqlBatchWriteTransactionFailed

- **Meldung**: `SQL transaction commits failed.`

- **Ursache:** Wenn die Ausnahmedetails konsistent ein Transaktionstimeout angeben, ist die Netzwerklatenz zwischen Integration Runtime und Datenbank höher als der Standardschwellenwert von 30 Sekunden.

- **Empfehlung**:  Aktualisieren Sie die Verbindungszeichenfolge für den verknüpften SQL-Dienst mit einem Wert für *Verbindungstimeout*, der gleich oder größer als 120 ist, und führen Sie die Aktivität erneut aus.

- **Ursache:** Wenn die Ausnahmedetails zeitweilig angeben, dass die SQL-Verbindung unterbrochen ist, liegt möglicherweise ein vorübergehender Netzwerkfehler oder ein Problem mit der SQL-Datenbank vor.

- **Empfehlung**:  Wiederholen Sie die Aktivität, und überprüfen Sie die Metriken der SQL-Datenbank.


### <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>Fehlercode: SqlBulkCopyInvalidColumnLength

- **Meldung**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Ursache:** Es ist ein Fehler beim SQL-Massenkopiervorgang aufgetreten, da vom BCP-Client (Massenkopierhilfsprogramm) eine ungültige Spaltenlänge empfangen wurde.

- **Empfehlung**:  Um zu ermitteln, in welcher Zeile das Problem aufgetreten ist, aktivieren Sie die Funktion für Fehlertoleranz für die Kopieraktivität. Dadurch können problematische Zeilen zur weiteren Untersuchung an den Speicher umgeleitet werden. Weitere Informationen finden Sie unter [Fehlertoleranz der Kopieraktivität in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance).


### <a name="error-code-sqlconnectionisclosed"></a>Fehlercode: SqlConnectionIsClosed

- **Meldung**: `The connection is closed by SQL Database.`

- **Ursache:** Die SQL-Verbindung wird durch SQL-Datenbank getrennt, wenn viele Vorgänge gleichzeitig durchgeführt werden und der Server die Verbindung trennt.

- **Empfehlung**:  Wiederholen Sie den Verbindungsversuch. Sollte das Problem weiterhin bestehen, wenden Sie sich an den Azure SQL-Support.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Fehlermeldung: Fehler beim Konvertieren einer Zeichenfolge in „uniqueidentifier“.

- **Symptome:** Wenn Sie Daten aus der Tabellendatenquelle (z. B. SQL Server) in Azure Synapse Analytics kopieren, indem Sie das gestaffelte Kopieren und PolyBase verwenden, erhalten Sie den folgenden Fehler:

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **Ursache:** Mit Azure Synapse Analytics PolyBase kann eine leere Zeichenfolge nicht in eine GUID konvertiert werden.

- **Lösung:** Legen Sie in der Senke der Kopieraktivität unter den PolyBase-Einstellungen die Option **use type default** auf *false* fest.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Fehlermeldung: Erwarteter Datentyp: DECIMAL(x,x), Auslösender Wert

- **Symptome:** Wenn Sie Daten aus der Tabellendatenquelle (z. B. SQL Server) in Azure Synapse Analytics kopieren, indem Sie das gestaffelte Kopieren und PolyBase verwenden, erhalten Sie den folgenden Fehler:

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **Ursache:** Azure Synapse Analytics PolyBase kann in eine Dezimalspalte keine leere Zeichenfolge (NULL-Wert) einfügen.

- **Lösung:** Legen Sie in der Senke der Kopieraktivität unter den PolyBase-Einstellungen die Option **use type default** auf „false“ fest.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Fehlermeldung: Java-Ausnahmemeldung: HdfsBridge::CreateRecordReader

- **Symptome:** Sie kopieren Daten mit PolyBase in Azure Synapse Analytics und erhalten den folgenden Fehler:

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **Ursache:** Eine mögliche Ursache ist, dass das Schema (gesamte Spaltenbreite) zu groß ist (größer als 1 MB). Überprüfen Sie das Schema der Azure Synapse Analytics-Zieltabelle, indem Sie die Größe aller Spalten hinzufügen:

    - Int = 4 Byte
    - Bigint = 8 Byte
    - Varchar(n),char(n),binary(n), varbinary(n) = n Byte
    - Nvarchar(n), nchar(n) = n*2 Byte
    - Date = 6 Byte
    - Datetime/(2), smalldatetime = 16 Byte
    - Datetimeoffset = 20 Byte
    - Decimal = 19 Byte
    - Float = 8 Byte
    - Money = 8 Byte
    - Smallmoney = 4 Byte
    - Real = 4 Byte
    - Smallint = 2 Byte
    - Time = 12 Byte
    - Tinyint = 1 Byte

- **Lösung**: 
    - Reduzieren Sie die Spaltenbreite auf weniger als 1 MB.
    - Sie können auch das Masseneinfügen verwenden, indem Sie PolyBase deaktivieren.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Fehlermeldung: Die mit bedingtem HTTP-Header angegebene Bedingung ist nicht erfüllt.

- **Symptome:** Sie verwenden die SQL-Abfrage zum Abrufen von Daten per Pullvorgang aus Azure Synapse Analytics und erhalten den folgenden Fehler:

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **Ursache:** Für Azure Synapse Analytics ist beim Abfragen der externen Tabelle in Azure Storage ein Fehler aufgetreten.

- **Lösung:** Führen Sie dieselbe Abfrage in SQL Server Management Studio (SSMS) aus, und überprüfen Sie, ob Sie das gleiche Ergebnis erhalten. Wenn dies der Fall ist, erstellen Sie ein Supportticket für Azure Synapse Analytics, und geben Sie den Namen Ihres Azure Synapse Analytics-Servers und der Datenbank an.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>Die Leistungsstufe ist niedrig und führt zu einem Kopierfehler

- **Symptome:** Sie kopieren Daten in Azure SQL-Datenbank und erhalten den folgenden Fehler: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Ursache:** Für Azure SQL-Datenbank s1 gelten Grenzwerte für die Eingabe/Ausgabe (E/A).

- **Lösung:** Führen Sie ein Upgrade der Azure SQL-Datenbank-Leistungsstufe durch, um das Problem zu beheben. 


### <a name="sql-table-cant-be-found"></a>Die SQL-Tabelle kann nicht gefunden werden. 

- **Symptome:** Sie kopieren Daten aus einer Hybridumgebung in eine lokale SQL Server-Tabelle und erhalten den folgenden Fehler:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Ursache:** Das aktuelle SQL-Konto verfügt nicht über ausreichende Berechtigungen zum Ausführen von Anforderungen, die von .NET SqlBulkCopy.WriteToServer ausgegeben wurden.

- **Lösung:** Wechseln Sie zu einem SQL-Konto mit höheren Berechtigungen.


### <a name="error-message-string-or-binary-data-is-truncated"></a>Fehlermeldung: String- oder binary-Daten werden abgeschnitten.

- **Symptome:** Beim Kopieren von Daten in eine lokale Azure SQL Server-Tabelle tritt ein Fehler auf. 

- **Ursache:** Die Definition des Cx-SQL-Tabellenschemas weist mindestens eine Spalte mit einer geringeren Länge als erwartet auf.

- **Lösung:** Versuchen Sie Folgendes, um das Problem zu beheben:

    1. Wenden Sie [Fehlertoleranz](./copy-activity-fault-tolerance.md) für die SQL-Senke (insbesondere „redirectIncompatibleRowSettings“) an, um die Zeilen mit dem Problem zu korrigieren.

        > [!NOTE]
        > Eine Fehlertoleranz kann zusätzliche Ausführungszeit mit sich bringen, was möglicherweise zu höheren Kosten führt.

    2. Überprüfen Sie die Spaltenlänge der umgeleiteten Daten anhand des SQL-Tabellenschema genau, um zu ermitteln, welche Spalten aktualisiert werden müssen.

    3. Aktualisieren Sie das Tabellenschema entsprechend.


## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="error-code-azuretableduplicatecolumnsfromsource"></a>Fehlercode: AzureTableDuplicateColumnsFromSource

- **Meldung**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **Ursache:** Doppelte Quellspalten können aus einem der folgenden Gründe auftreten:
   * Sie verwenden die Datenbank als Quelle und haben Tabellenjoins angewendet.
   * Sie verfügen über unstrukturierte CSV-Dateien mit doppelten Spaltennamen in der Kopfzeile.

- **Empfehlung**:  Überprüfen Sie die Quellspalten, und beheben Sie ggf. Probleme.


## <a name="db2"></a>DB2

### <a name="error-code-db2driverrunfailed"></a>Fehlercode: DB2DriverRunFailed

- **Meldung**: `Error thrown from driver. Sql code: '%code;'`

- **Ursache:** Wenn die Fehlermeldung die Zeichenfolge „SQLSTATE=51002 SQLCODE=-805“ enthält, verwenden Sie den „Tipp“ unter [Eigenschaften des verknüpften Diensts](https://docs.microsoft.com/azure/data-factory/connector-db2#linked-service-properties).

- **Empfehlung**:  Versuchen Sie, „NULLID“ in der `packageCollection`-Eigenschaft festzulegen.


## <a name="delimited-text-format"></a>Textformat mit Trennzeichen

### <a name="error-code-delimitedtextcolumnnamenotallownull"></a>Fehlercode: DelimitedTextColumnNameNotAllowNull

- **Meldung**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Ursache:** Wenn „firstrowasheader“ in der Aktivität festgelegt ist, wird die erste Zeile als Spaltenname verwendet. Dieser Fehler bedeutet, dass die erste Zeile einen leeren Wert (z. B. „ColumnA, ColumnB) enthält.

- **Empfehlung**:  Überprüfen Sie die erste Zeile, und korrigieren Sie den Wert, wenn ein leerer Wert vorhanden ist.


### <a name="error-code-delimitedtextmorecolumnsthandefined"></a>Fehlercode: DelimitedTextMoreColumnsThanDefined

- **Meldung**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Ursachen und Empfehlungen:** Dieser Fehler kann verschiedene Ursachen haben. In der Liste unten finden Sie mögliche Ursachenanalysen und Empfehlungen.

  | Ursachenanalyse                                               | Empfehlung                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Die Spaltenanzahl der problematischen Zeile ist größer als die Spaltenanzahl der ersten Zeile. Dies kann durch ein Datenproblem oder falsche Einstellungen für Spaltentrennzeichen oder Anführungszeichen verursacht werden. | Rufen Sie die Zeilenanzahl aus der Fehlermeldung ab, überprüfen Sie die Spalte in der Zeile, und korrigieren Sie die Daten. |
  | Wenn die erwartete Spaltenanzahl in einer Fehlermeldung „1“ lautet, haben Sie möglicherweise falsche Komprimierungs- oder Formateinstellungen angegeben, sodass Azure Data Factory Ihre Dateien falsch analysiert hat. | Überprüfen Sie die Formateinstellungen, um sicherzustellen, dass sie mit Ihren Quelldateien übereinstimmen. |
  | Wenn Ihre Quelle ein Ordner ist, weisen die Dateien im angegebenen Ordner möglicherweise ein anderes Schema auf. | Stellen Sie sicher, dass die Dateien im angegebenen Ordner ein identisches Schema aufweisen. |


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dynamics 365, Common Data Service, Dynamics CRM

### <a name="error-code-dynamicscreateserviceclienterror"></a>Fehlercode: DynamicsCreateServiceClientError

- **Meldung**: `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **Ursache:** Dies ist ein vorübergehendes Problem bei dem Dynamics-Server.

- **Empfehlung**:  Führen Sie die Pipeline erneut aus. Wenn erneut ein Fehler auftritt, versuchen Sie, die Parallelität zu verringern. Wenn das Problem weiterhin besteht, wenden Sie sich an den Dynamics-Support.


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Fehlende Spalten beim Importieren eines Schemas oder von Vorschaudaten

- **Symptome:** Einige Spalten fehlen beim Importieren eines Schemas oder von Vorschaudaten. Fehlermeldung: `The valid structure information (column name and type) are required for Dynamics source.`

- **Ursache:** Dieses Problem ist beabsichtigt, weil Data Factory keine Spalten anzeigen kann, die keine Werte in den ersten 10 Datensätzen enthalten. Stellen Sie sicher, dass die hinzugefügten Spalten das richtige Format aufweisen. 

- **Empfehlung**: Fügen Sie die Spalten auf der Registerkarte „Zuordnung“ manuell hinzu.


### <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>Fehlercode: DynamicsMissingTargetForMultiTargetLookupField

- **Meldung**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Ursache:** Die Zielspalte ist in der Quelle oder der Spaltenzuordnung nicht vorhanden.

- **Empfehlung**:  
  1. Stellen Sie sicher, dass die Quelle die Zielspalte enthält. 
  2. Fügen Sie die Zielspalte in der Spaltenzuordnung hinzu. Stellen Sie sicher, dass die Senkenspalte das Format *{fieldName}@EntityReference* aufweist.


### <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>Fehlercode: DynamicsInvalidTargetForMultiTargetLookupField

- **Meldung**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **Ursache:** Ein falscher Entitätsname wird als Zielentität eines Nachschlagefelds mit mehreren Zielen bereitgestellt.

- **Empfehlung**:  Geben Sie einen gültigen Entitätsnamen für das Nachschlagefeld mit mehreren Zielen an.


### <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>Fehlercode: DynamicsInvalidTypeForMultiTargetLookupField

- **Meldung**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **Ursache:** Der Wert in der Zielspalte ist keine Zeichenfolge.

- **Empfehlung**:  Geben Sie eine gültige Zeichenfolge in der Zielspalte für das Nachschlagen mit mehreren Zielen an.


### <a name="error-code-dynamicsfailedtorequetserver"></a>Fehlercode: DynamicsFailedToRequetServer

- **Meldung**: `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **Ursache:** Der Dynamics-Server ist instabil, es kann nicht darauf zugegriffen werden, oder im Netzwerk treten Probleme auf.

- **Empfehlung**:  Überprüfen Sie die Netzwerkkonnektivität, oder suchen Sie im Dynamics-Serverprotokoll nach weiteren Details. Wenden Sie sich an den Dynamics-Support, um weitere Unterstützung zu erhalten.
  

## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>Fehlercode: FtpFailedToConnectToFtpServer

- **Meldung**: `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **Ursache:** Möglicherweise wird ein falscher Typ von verknüpftem Dienst für den FTP-Server verwendet. Beispielsweise wird mit einem verknüpften SFTP-Dienst versucht, eine Verbindung mit einem FTP-Server herzustellen.

- **Empfehlung**:  Überprüfen Sie den Port des Zielservers. FTP verwendet Port 21.


## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>Fehlercode: HttpFileFailedToRead

- **Meldung**: `Failed to read data from http server. Check the error from http server：%message;`

- **Ursache:** Dieser Fehler tritt auf, wenn Azure Data Factory mit dem HTTP-Server kommuniziert, beim HTTP-Anforderungsvorgang jedoch ein Fehler auftritt.

- **Empfehlung**:  Überprüfen Sie den HTTP-Statuscode in der Fehlermeldung, und beheben Sie das Remoteserverproblem.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Fehlercode: ArgumentOutOfRangeException

- **Meldung**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Ursache:** In Data Factory werden DateTime-Werte im Bereich von 0001-01-01 00:00:00 bis 9999-12-31 23:59:59 unterstützt. Oracle unterstützt jedoch einen größeren Bereich von DateTime-Werten (z. B. Jahreszahlen vor unserer Zeitrechnung oder Minuten-/Sekundenangaben > 59). Dies kann in Data Factory zu einem Fehler führen.

- **Empfehlung**: 

    Um zu ermitteln, ob sich der Wert in Oracle im Bereich von Data Factory befindet, führen Sie `select dump(<column name>)` aus. 

    Wenn Sie die Bytereihenfolge im Ergebnis ermitteln möchten, lesen Sie [Wie werden Daten in Oracle gespeichert?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle)


## <a name="orc-format"></a>ORC-Format

### <a name="error-code-orcjavainvocationexception"></a>Fehlercode: OrcJavaInvocationException

- **Meldung**: `An error occurred when invoking Java, message: %javaException;.`
- **Ursachen und Empfehlungen:** Dieser Fehler kann verschiedene Ursachen haben. In der Liste unten finden Sie mögliche Ursachenanalysen und Empfehlungen.

    | Ursachenanalyse                                               | Empfehlung                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Wenn die Fehlermeldung die Zeichenfolge „java.lang.OutOfMemory“, „Java heap space“ und „doubleCapacity“ enthält, liegt normalerweise ein Speicherverwaltungsproblem in einer alten Version der Integration Runtime vor. | Wenn Sie die selbstgehostete Integration Runtime verwenden, empfiehlt es sich, ein Upgrade auf die neueste Version durchzuführen. |
    | Wenn die Fehlermeldung die Zeichenfolge „java.lang.OutOfMemory“ enthält, verfügt die Integration Runtime nicht über genügend Ressourcen zum Verarbeiten der Dateien. | Begrenzen Sie die gleichzeitigen Ausführungen auf die Integration Runtime. Skalieren Sie für „Selbstgehostete IR“ auf einen leistungsfähigen Computer mit einem Arbeitsspeicher von mindestens 8 GB hoch. |
    |Wenn die Fehlermeldung die Zeichenfolge „NullPointerReference“ enthält, ist die Ursache möglicherweise ein vorübergehender Fehler. | Wiederholen Sie den Vorgang. Wenden Sie sich an den Support, wenn das Problem weiterhin besteht. |
    | Wenn die Fehlermeldung die Zeichenfolge „BufferOverflowException“ enthält, ist die Ursache möglicherweise ein vorübergehender Fehler. | Wiederholen Sie den Vorgang. Wenden Sie sich an den Support, wenn das Problem weiterhin besteht. |
    | Wenn die Fehlermeldung die Zeichenfolge „java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable cannot be cast to org.apache.hadoop.io.Text“ enthält, ist die Ursache möglicherweise ein Typkonvertierungsproblem in der Java-Runtime. Normalerweise bedeutet dies, dass die Quelldaten in der Java-Laufzeit nicht ordnungsgemäß verarbeitet werden können. | Dies ist ein Datenproblem. Versuchen Sie, „string“ anstelle von „char“ oder „varchar“ in den Daten im ORC-Format zu verwenden. |

### <a name="error-code-orcdatetimeexceedlimit"></a>Fehlercode: OrcDateTimeExceedLimit

- **Meldung**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Ursache:** Wenn der DateTime-Wert „0001-01-01 00:00:00“ lautet, kann dies durch den Unterschied zwischen [julianischem Kalender und gregorianischem Kalender](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates) verursacht werden.

- **Empfehlung**:  Überprüfen Sie den Taktwert, und vermeiden Sie den DateTime-Wert „0001-01-01 00:00:00“.


## <a name="parquet-format"></a>Parquet-Format

### <a name="error-code-parquetjavainvocationexception"></a>Fehlercode: ParquetJavaInvocationException

- **Meldung**: `An error occurred when invoking java, message: %javaException;.`

- **Ursachen und Empfehlungen:** Dieser Fehler kann verschiedene Ursachen haben. In der Liste unten finden Sie mögliche Ursachenanalysen und Empfehlungen.

    | Ursachenanalyse                                               | Empfehlung                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Wenn die Fehlermeldung die Zeichenfolge „java.lang.OutOfMemory“, „Java heap space“ und „doubleCapacity“ enthält, liegt normalerweise ein Speicherverwaltungsproblem in einer alten Version der Integration Runtime vor. | Wenn Sie selbstgehostete IR verwenden und die Version älter als 3.20.7159.1 ist, wird ein Upgrade auf die neueste Version empfohlen. |
    | Wenn die Fehlermeldung die Zeichenfolge „java.lang.OutOfMemory“ enthält, verfügt die Integration Runtime nicht über genügend Ressourcen zum Verarbeiten der Dateien. | Begrenzen Sie die gleichzeitigen Ausführungen auf die Integration Runtime. Skalieren Sie für „Selbstgehostete IR“ auf einen leistungsfähigen Computer mit einem Arbeitsspeicher von mindestens 8 GB hoch. |
    | Wenn die Fehlermeldung die Zeichenfolge „NullPointerReference“ enthält, handelt es sich möglicherweise um einem vorübergehenden Fehler. | Wiederholen Sie den Vorgang. Wenden Sie sich an den Support, wenn das Problem weiterhin besteht. |

### <a name="error-code-parquetinvalidfile"></a>Fehlercode: ParquetInvalidFile

- **Meldung**: `File is not a valid Parquet file.`

- **Ursache:** Dabei handelt es sich um ein Problem mit der Parquet-Datei.

- **Empfehlung**:  Überprüfen Sie, ob die Eingabe eine gültige Parquet-Datei ist.


### <a name="error-code-parquetnotsupportedtype"></a>Fehlercode: ParquetNotSupportedType

- **Meldung**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Ursache:** Das Parquet-Format wird in Azure Data Factory nicht unterstützt.

- **Empfehlung**:  Überprüfen Sie die Quelldaten erneut anhand von [Von der Copy-Aktivität unterstützte Dateiformate und Komprimierungscodecs in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs).


### <a name="error-code-parquetmisseddecimalprecisionscale"></a>Fehlercode: ParquetMissedDecimalPrecisionScale

- **Meldung**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Ursache:** Die Zahlengenauigkeit und die Dezimalstellen wurden analysiert, doch diese Informationen wurden nicht bereitgestellt.

- **Empfehlung**:  Die Quelle gibt nicht die richtigen Genauigkeits- und Dezimalstelleninformationen zurück. Überprüfen Sie die Problemspalte auf diese Informationen.


### <a name="error-code-parquetinvaliddecimalprecisionscale"></a>Fehlercode: ParquetInvalidDecimalPrecisionScale

- **Meldung**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Ursache:** Das Schema ist ungültig.

- **Empfehlung**:  Überprüfen Sie die Problemspalte auf Genauigkeit und Dezimalstellen.


### <a name="error-code-parquetcolumnnotfound"></a>Fehlercode: ParquetColumnNotFound

- **Meldung**: `Column %column; does not exist in Parquet file.`

- **Ursache:** Das Quellschema stimmt nicht mit dem Senkenschema überein.

- **Empfehlung**:  Überprüfen Sie die Zuordnungen in der Aktivität. Stellen Sie sicher, dass die Quellspalte der richtigen Senkenspalte zugeordnet werden kann.


### <a name="error-code-parquetinvaliddataformat"></a>Fehlercode: ParquetInvalidDataFormat

- **Meldung**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Ursache:** Die Daten können nicht in den in „mappings.source“ angegebenen Typ konvertiert werden.

- **Empfehlung**:  Überprüfen Sie die Quelldaten erneut, oder geben Sie den richtigen Datentyp für diese Spalte in der Spaltenzuordnung der Kopieraktivität an. Weitere Informationen finden Sie unter [Von der Copy-Aktivität unterstützte Dateiformate und Komprimierungscodecs in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs).


### <a name="error-code-parquetdatacountnotmatchcolumncount"></a>Fehlercode: ParquetDataCountNotMatchColumnCount

- **Meldung**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Ursache:**  Ein Konflikt zwischen der Anzahl der Quellspalten und der Anzahl der Senkenspalten.

- **Empfehlung**:  Überprüfen Sie erneut, ob die Anzahl der Quellspalten mit der Anzahl der Senkenspalten in „mapping“ übereinstimmt.


### <a name="error-code-parquetdatatypenotmatchcolumntype"></a>Fehlercode: ParquetDataTypeNotMatchColumnType

- **Meldung**: `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **Ursache:** Die Daten aus der Quelle können nicht in den Typ konvertiert werden, der in der Senke definiert ist.

- **Empfehlung**:  Geben Sie in „mapping.sink“ einen richtigen Typ an.


### <a name="error-code-parquetbridgeinvaliddata"></a>Fehlercode: ParquetBridgeInvalidData

- **Meldung**: `%message;`

- **Ursache:** Der Datenwert hat den Grenzwert überschritten.

- **Empfehlung**:  Wiederholen Sie den Vorgang. Wenden Sie sich an uns, wenn das Problem weiterhin besteht.


### <a name="error-code-parquetunsupportedinterpretation"></a>Fehlercode: ParquetUnsupportedInterpretation

- **Meldung**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Ursache:** Dieses Szenario wird nicht unterstützt.

- **Empfehlung**:  „ParquetInterpretFor“ sollte nicht „sparkSql“ sein.


### <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>Fehlercode: ParquetUnsupportFileLevelCompressionOption

- **Meldung**: `File level compression is not supported for Parquet.`

- **Ursache:** Dieses Szenario wird nicht unterstützt.

- **Empfehlung**:  Entfernen Sie „CompressionType“ in der Nutzlast.


### <a name="error-code-usererrorjniexception"></a>Fehlercode: UserErrorJniException

- **Meldung**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Ursache:** Eine JVM-Instanz (Java Virtual Machine) kann nicht erstellt werden, da einige ungültige (globale) Argumente festgelegt wurden.

- **Empfehlung**:  Melden Sie sich bei dem Computer an, auf dem die *einzelnen Knoten* Ihrer selbstgehosteten IR gehostet werden. Stellen Sie wie folgt sicher, dass die Systemvariable ordnungsgemäß festgelegt ist: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G`. Starten Sie alle IR-Knoten neu, und führen Sie dann die Pipeline erneut aus.


### <a name="arithmetic-overflow"></a>Arithmetischer Überlauf

- **Symptome:** Beim Kopieren von Parquet-Dateien ist eine Fehlermeldung aufgetreten: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Ursache:** Derzeit werden beim Kopieren von Dateien aus Oracle in das Parquet-Format nur Dezimalzahlen mit einer Genauigkeit <= 38 unterstützt, deren ganzzahliger Teil eine Länge <= 20 aufweist. 

- **Lösung:** Um dieses Problem zu umgehen, können Sie alle Spalten mit diesem Problem in VARCHAR2 konvertieren.


### <a name="no-enum-constant"></a>Keine Enumerationskonstante

- **Symptome:** Beim Kopieren von Daten in das Parquet-Format ist eine Fehlermeldung aufgetreten: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` oder `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test`.

- **Ursache**: 

    Das Problem kann durch Leerzeichen oder nicht unterstützte Sonderzeichen (z. B. ,;{}()\n\t=) im Spaltennamen verursacht werden, weil dieses Format von Parquet nicht unterstützt wird. 

    Beispielsweise wird bei einem Spaltennamen wie *contoso(test)* der Typ in Klammern als [code](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` analysiert. Der Fehler wird ausgelöst, weil kein solcher Typ „test“ vorhanden ist.

    Informationen zu den unterstützten Typen finden Sie auf der GitHub-Website unter [apache/parquet-mr](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java).

- **Lösung**: 

    Prüfen Sie erneut, ob Folgendes zutrifft:
    - Im Senkenspaltennamen sind Leerzeichen enthalten.
    - Die erste Spalte mit Leerzeichen wird als Spaltenname verwendet.
    - Der Typ „OriginalType“ wird unterstützt. Versuchen Sie, diese Sonderzeichen zu vermeiden: `,;{}()\n\t=`. 


## <a name="rest"></a>REST

### <a name="error-code-restsinkcallfailed"></a>Fehlercode: RestSinkCallFailed

- **Meldung**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Ursache:** Dieser Fehler tritt auf, wenn Azure Data Factory mit dem REST-Endpunkt über das HTTP-Protokoll kommuniziert und beim Anforderungsvorgang ein Fehler auftritt.

- **Empfehlung**:  Überprüfen Sie den HTTP-Statuscode bzw. die HTTP-Statusnachricht in der Fehlermeldung, und beheben Sie das Remoteserverproblem.

### <a name="unexpected-network-response-from-the-rest-connector"></a>Unerwartete Netzwerkantwort vom REST-Connector

- **Symptome:** Der Endpunkt empfängt manchmal eine unerwartete Antwort (400/401/403/500) vom REST-Connector.

- **Ursache:** Für den REST-Quellenconnector werden beim Erstellen einer HTTP-Anforderung die URL und die HTTP-Methode (mit Header und Text) des verknüpften Diensts, des Datasets oder der Kopierquelle als Parameter verwendet. Der wahrscheinlichste Grund für das Problem sind einige Fehler in mindestens einem der angegebenen Parameter.

- **Lösung**: 
    - Verwenden Sie „curl“ in einem Eingabeaufforderungsfenster, um zu überprüfen, ob der Parameter die Ursache ist (die Header **Accept** und **User-Agent** sollten immer vorhanden sein):
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Wenn für diesen Befehl die gleiche unerwartete Antwort zurückgegeben wird, korrigieren Sie die Parameter oben mit „curl“, bis die erwartete Antwort zurückgegeben wird. 

      Sie können auch „curl--help“ verwenden, um Informationen zu den erweiterten Optionen des Befehls anzuzeigen.

    - Falls nur vom Data Factory-REST-Connector eine unerwartete Antwort zurückgegeben wird, wenden Sie sich für die weitere Problembehandlung an den Microsoft-Support.
    
    - Beachten Sie, dass „curl“ ggf. nicht geeignet ist, um ein Problem bei der Überprüfung des SSL-Zertifikats zu reproduzieren. In einigen Szenarien war die Ausführung des Befehls „curl“ erfolgreich, ohne dass Probleme bei der Überprüfung des SSL-Zertifikats aufgetreten sind. Wenn dieselbe URL dann im Browser ausgeführt wird, wird für den Client aber kein SSL-Zertifikat zurückgegeben, mit dem eine Vertrauensstellung mit dem Server eingerichtet werden kann.

      Für den obigen Fall wird die Verwendung von Tools wie **Postman** und **Fiddler** empfohlen.


## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>Fehlercode: SftpOperationFail

- **Meldung**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **Ursache:** Ein Problem mit dem SFTP-Vorgang.

- **Empfehlung**:  Überprüfung Sie die Fehlerdetails aus SFTP.


### <a name="error-code-sftprenameoperationfail"></a>Fehlercode: SftpRenameOperationFail

- **Meldung**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Ursache:** Der SFTP-Server unterstützt kein Umbenennen der temporären Datei.

- **Empfehlung**:  Legen Sie „useTempFileRename“ in der Kopiersenke auf „false“ fest, um das Hochladen in die temporäre Datei zu deaktivieren.


### <a name="error-code-sftpinvalidsftpcredential"></a>Fehlercode: SftpInvalidSftpCredential

- **Meldung**: `Invalid SFTP credential provided for '%type;' authentication type.`

- **Ursache:** Der Inhalt des privaten Schlüssels wird aus Azure Key Vault oder dem SDK abgerufen, ist aber nicht ordnungsgemäß codiert.

- **Empfehlung**:  

    Wenn der Inhalt des privaten Schlüssels aus Ihrem Schlüsseltresor stammt, kann die ursprüngliche Schlüsseldatei funktionieren, wenn Sie sie direkt in den verknüpften SFTP-Dienst hochladen.

    Weitere Informationen finden Sie unter [Kopieren von Daten von einem und auf einen SFTP-Server mithilfe von Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-sftp#using-ssh-public-key-authentication). Der Inhalt des privaten Schlüssels ist der base64-codierte Inhalt eines privaten SSH-Schlüssels.

    Codieren Sie den *gesamten* Inhalt der ursprünglichen Datei mit dem privaten Schlüssel mit base64, und speichern Sie die codierte Zeichenfolge in Ihrem Schlüsseltresor. Die ursprüngliche Datei mit dem privaten Schlüssel funktioniert bei einem verknüpften SFTP-Dienst, wenn Sie **Hochladen** in der Datei auswählen.

    Es folgen einige Beispiele, die Sie verwenden können, um die Zeichenfolge zu generieren:

    - In C#:

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - In Python:

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - Verwenden Sie ein base64-Konvertierungstool von Drittanbietern. Wir empfehlen das Tool [Encode to Base64 format](https://www.base64encode.org/).

- **Ursache:** Es wurde das falsche Format für den Schlüsselinhalt ausgewählt.

- **Empfehlung**:  

    Private SSH-Schlüssel im PKCS#8-Format (beginnend mit „-----BEGIN ENCRYPTED PRIVATE KEY-----“) werden derzeit nicht für den Zugriff auf den SFTP-Server in Data Factory unterstützt. 

    Führen Sie die folgenden Befehle aus, um den Schlüssel in ein herkömmliches SSH-Schlüsselformat zu konvertieren (beginnend mit „-----BEGIN RSA PRIVATE KEY-----“):

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Ursache:** Die Anmeldeinformationen oder der Inhalt des privaten Schlüssels ist ungültig.

- **Empfehlung**:  Überprüfen Sie mit Tools wie WinSCP erneut, ob die Schlüsseldatei und das Kennwort richtig sind.

### <a name="sftp-copy-activity-failed"></a>Fehler bei der SFTP-Kopieraktivität

- **Symptome:** 
  * Fehlercode: UserErrorInvalidColumnMappingColumnNotFound 
  * Fehlermeldung: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **Ursache:** Die Quelle enthält keine Spalte mit dem Namen „AccMngr“.

- **Lösung:** Um zu ermitteln, ob die Spalte „accmngr“ vorhanden ist, überprüfen Sie die Datasetkonfiguration erneut, indem Sie die Zieldatasetspalte zuordnen.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>Fehlercode: SftpFailedToConnectToSftpServer

- **Meldung**: `Failed to connect to SFTP server '%server;'.`

- **Ursache:** Wenn die Fehlermeldung die Zeichenfolge „Socket read operation has timed out after 30,000 milliseconds“ enthält, ist eine mögliche Ursache, dass ein falscher Typ von verknüpftem Dienst für den SFTP-Server verwendet wird. Beispielsweise wird mit einem verknüpften FTP-Dienst versucht, eine Verbindung mit dem SFTP-Server herzustellen.

- **Empfehlung**:  Überprüfen Sie den Port des Zielservers. Standardmäßig verwendet SFTP Port 22.

- **Ursache:** Wenn die Fehlermeldung die Zeichenfolge „Server response does not contain SSH protocol identification“ enthält, ist eine mögliche Ursache, dass der SFTP-Server die Verbindung gedrosselt hat. Data Factory erstellt mehrere Verbindungen, um parallele Downloads vom SFTP-Server durchführen zu können. Manchmal tritt dabei SFTP-Serverdrosselung auf. Normalerweise geben verschiedene Server unterschiedliche Fehler zurück, wenn Drosselung auftritt.

- **Empfehlung**:  

    Geben Sie die maximale Anzahl gleichzeitiger Verbindungen des SFTP-Datasets als 1 an, und führen Sie die Kopieraktivität dann erneut aus. Wenn die Aktivität erfolgreich ist, können Sie sicher sein, dass Drosselung die Ursache ist.

    Wenn Sie den niedrigen Durchsatz steigern möchten, wenden Sie sich an den SFTP-Administrator, damit dieser die Anzahl gleichzeitiger Verbindungen erhöht, oder führen Sie eine der folgenden Aktionen aus:

    * Wenn Sie selbstgehostete IR verwenden, fügen Sie die IP-Adresse des selbstgehosteten IR-Computers der Zulassungsliste hinzu.
    * Wenn Sie Azure IR verwenden, fügen Sie [IP-Adressen von Azure Integration Runtime](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses) hinzu. Wenn Sie der Zulassungsliste des SFTP-Servers keinen Bereich von IP-Adressen hinzufügen möchten, verwenden Sie stattdessen selbstgehostete IR.

## <a name="sharepoint-online-list"></a>SharePoint Online-Liste

### <a name="error-code-sharepointonlineauthfailed"></a>Fehlercode: SharePointOnlineAuthFailed

- **Meldung**: `The access token generated failed, status code: %code;, error message: %message;.`

- **Ursache:** Die Dienstprinzipal-ID und der Schlüssel sind möglicherweise nicht richtig festgelegt.

- **Empfehlung**:  Überprüfen Sie, ob die registrierte Anwendung (Dienstprinzipal-ID) und der Schlüssel richtig festgelegt sind.


## <a name="xml-format"></a>XML-Format

### <a name="error-code-xmlsinknotsupported"></a>Fehlercode: XmlSinkNotSupported

- **Meldung**: `Write data in XML format is not supported yet, choose a different format!`

- **Ursache:** Es wurde ein XML-Dataset als Senkendataset in der Kopieraktivität verwendet.

- **Empfehlung**:  Verwenden Sie ein Dataset in einem anderen Format als das Senkendataset.


### <a name="error-code-xmlattributecolumnnameconflict"></a>Fehlercode: XmlAttributeColumnNameConflict

- **Meldung**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Ursache:** Es wurde ein Attributpräfix verwendet, das den Konflikt verursacht hat.

- **Empfehlung**:  Legen Sie einen anderen Wert für die Eigenschaft „attributePrefix“ fest.


### <a name="error-code-xmlvaluecolumnnameconflict"></a>Fehlercode: XmlValueColumnNameConflict

- **Meldung**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Ursache:** Es wurde einer der untergeordneten Elementnamen als Spaltenname für den Elementwert verwendet.

- **Empfehlung**:  Legen Sie einen anderen Wert für die Eigenschaft „valueColumn“ fest.


### <a name="error-code-xmlinvalid"></a>Fehlercode: XmlInvalid

- **Meldung**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Ursache:** Die XML-Eingabedatei ist nicht wohlgeformt.

- **Empfehlung**:  Korrigieren Sie die XML-Datei, damit sie wohlgeformt ist.


## <a name="general-copy-activity-error"></a>Fehler bei allgemeiner Kopieraktivität

### <a name="error-code-jrenotfound"></a>Fehlercode: JreNotFound

- **Meldung**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Ursache:** Die selbstgehostete IR kann die Java-Laufzeit nicht finden. Die Java-Laufzeit ist zum Lesen bestimmter Quellen erforderlich.

- **Empfehlung**:  Überprüfen Sie Ihre Integration Runtime-Umgebung. Informationen dazu finden Sie unter [Verwenden einer selbstgehosteten Integration Runtime](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime).


### <a name="error-code-wildcardpathsinknotsupported"></a>Fehlercode: WildcardPathSinkNotSupported

- **Meldung**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Ursache:** Das Senkendataset unterstützt keine Platzhalterwerte.

- **Empfehlung**:  Überprüfen Sie das Senkendataset, und korrigieren Sie den Pfad, ohne einen Platzhalterwert zu verwenden.


### <a name="fips-issue"></a>FIPS-Problem

- **Symptome:** Fehler bei der Kopieraktivität auf einem auf einem selbstgehosteten IR-Computer mit aktiviertem FIPS mit folgender Fehlermeldung: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **Ursache:** Dieser Fehler kann auftreten, wenn Sie Daten mit Connectors wie Azure Blob, SFTP usw. kopieren. FIPS (Federal Information Processing Standards) definiert eine bestimmte Gruppe von Kryptografiealgorithmen, die verwendet werden können. Wenn der FIPS-Modus auf dem Computer aktiviert ist, werden einige Kryptografieklassen, von denen die Kopieraktivität abhängig ist, in einigen Szenarien blockiert.

- **Lösung:** Informieren Sie sich, [warum wir den „FIPS-Modus“ nicht mehr empfehlen](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037), und ermitteln Sie, ob Sie FIPS auf Ihrem selbstgehosteten IR-Computer deaktivieren können.

    Wenn Sie jedoch nur Azure Data Factory die Umgehung von FIPS und erfolgreiche Aktivitätsausführungen ermöglichen möchten, können Sie folgendermaßen vorgehen:

    1. Öffnen Sie den Ordner, in dem die selbstgehostete IR installiert ist. Der Pfad lautet in der Regel *C:\Programme\Microsoft Integration Runtime \<IR version>\Shared*.

    2. Öffnen Sie die Datei *diawp.exe.config*, und fügen Sie dann am Ende des `<runtime>`-Abschnitts `<enforceFIPSPolicy enabled="false"/>` wie hier gezeigt hinzu:

        ![Screenshot eines Abschnitts der Datei „diawp.exe.config“ mit deaktiviertem FIPS.](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Speichern Sie die Datei, und starten Sie den Computer mit der selbstgehosteten IR neu.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

*  [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory-Funktionsanfragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
*  [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
