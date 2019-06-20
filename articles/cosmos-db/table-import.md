---
title: Migrieren Ihrer vorhandenen Daten zu einem Tabellen-API-Konto in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie lokale Daten oder Clouddaten zu einem Azure-Tabellen-API-Konto in Azure Cosmos DB migrieren bzw. in ein solches Konto importieren.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5c828644cb03d83df38265719cd8afabc24cf739
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242568"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>Migrieren Ihrer Daten zum Azure Cosmos DB-Konto für die Tabellen-API

In diesem Tutorial erfahren Sie, wie Sie Daten für die Verwendung mit der [Table-API](table-introduction.md) von Azure Cosmos DB importieren. Wenn Sie Daten in Azure Table Storage gespeichert haben, können Sie entweder das Datenmigrationstool oder AzCopy verwenden, um Ihre Daten für die Azure Cosmos DB-Tabellen-API zu importieren. Wenn Sie Daten in einem Konto der Tabellen-API von Azure Cosmos DB (Vorschauversion) gespeichert haben, müssen Sie Ihre Daten mit dem Datenmigrationstool migrieren. 

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Importieren von Daten mit dem Datenmigrationstool
> * Importieren von Daten mit AzCopy
> * Migrieren von der Tabellen-API (Vorschauversion) zur Tabellen-API 

## <a name="prerequisites"></a>Voraussetzungen

* **Erhöhen des Durchsatzes**: Die Dauer der Datenmigration richtet sich nach dem Durchsatzwert, den Sie für einen einzelnen Container oder eine Gruppe von Containern einrichten. Achten Sie darauf, dass Sie den Durchsatz für größere Datenmigrationen erhöhen. Nachdem die Migration abgeschlossen ist, können Sie den Durchsatz wieder verringern, um Kosten zu sparen. Weitere Informationen zur Erhöhung des Durchsatzes im Azure-Portal finden Sie unter „Leistungsstufen und Tarife in Azure Cosmos DB“.

* **Erstellen von Azure Cosmos DB-Ressourcen**: Bevor Sie mit der Migration von Daten beginnen, erstellen Sie zunächst alle Ihre Tabellen im Azure-Portal. Wenn Sie als Migrationsziel ein Azure Cosmos DB-Konto mit Durchsatz auf Datenbankebene verwenden, geben Sie beim Erstellen der Azure Cosmos DB-Tabellen einen Partitionsschlüssel an.

## <a name="data-migration-tool"></a>Datenmigrationstool

Das Datenmigrationstool von Azure Cosmos DB für die Befehlszeile („dt.exe“) kann verwendet werden, um Ihre vorhandenen Azure Table Storage-Daten in ein Konto der allgemein verfügbaren Tabellen-API zu importieren oder um Daten aus einem Konto der Tabellen-API (Vorschauversion) zu einem Konto der allgemein verfügbaren Version der Tabellen-API zu migrieren. Andere Quellen werden derzeit nicht unterstützt. Das UI-basierte Datenmigrationstool (dtui.exe) wird derzeit für Tabellen-API-Konten nicht unterstützt. 

Um eine Migration von Tabellendaten durchzuführen, führen Sie die folgenden Aufgaben aus:

1. Laden Sie das Migrationstool von [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool) herunter.
2. Führen Sie `dt.exe` mit den Befehlszeilenargumenten für Ihr Szenario aus. `dt.exe` akzeptiert einen Befehl im folgenden Format:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

Optionen für den Befehl:

    /ErrorLog: Optional. Name of the CSV file to redirect data transfer failures
    /OverwriteErrorLog: Optional. Overwrite error log file
    /ProgressUpdateInterval: Optional, default is 00:00:01. Time interval to refresh on-screen data transfer progress
    /ErrorDetails: Optional, default is None. Specifies that detailed error information should be displayed for the following errors: None, Critical, All
    /EnableCosmosTableLog: Optional. Direct the log to a cosmos table account. If set, this defaults to destination account connection string unless /CosmosTableLogConnectionString is also provided. This is useful if multiple instances of DT are being run simultaneously.
    /CosmosTableLogConnectionString: Optional. ConnectionString to direct the log to a remote cosmos table account. 

### <a name="command-line-source-settings"></a>Befehlszeilen-Quelleinstellungen

Verwenden Sie die folgenden Quelloptionen, wenn Sie Azure Table Storage oder die Vorschauversion der Tabellen-API als Quelle für die Migration festlegen.

    /s:AzureTable: Reads data from Azure Table storage
    /s.ConnectionString: Connection string for the table endpoint. This can be retrieved from the Azure portal
    /s.LocationMode: Optional, default is PrimaryOnly. Specifies which location mode to use when connecting to Azure Table storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
    /s.Table: Name of the Azure Table
    /s.InternalFields: Set to All for table migration as RowKey and PartitionKey are required for import.
    /s.Filter: Optional. Filter string to apply
    /s.Projection: Optional. List of columns to select

Um die Verbindungszeichenfolge für die Quelle beim Importieren aus Azure Table Storage abzurufen, öffnen Sie das Azure-Portal, klicken Sie auf **Speicherkonten** > **Konto** > **Zugriffsschlüssel**, und verwenden Sie dann die Schaltfläche „Kopieren“, um die **Verbindungszeichenfolge** zu kopieren.

![Screenshot der Optionen für HBase-Quellen](./media/table-import/storage-table-access-key.png)

Um die Verbindungszeichenfolge für die Quelle beim Importieren aus der Tabellen-API von Azure Cosmos DB abzurufen, öffnen Sie das Azure-Portal, klicken Sie auf **Azure Cosmos DB** > **Konto** > **Verbindungszeichenfolge**, und kopieren Sie dann die **Verbindungszeichenfolge** über die Schaltfläche „Kopieren“.

![Screenshot der Optionen für HBase-Quellen](./media/table-import/cosmos-connection-string.png)

[Beispielbefehl für Azure Table Storage](#azure-table-storage)

[Beispielbefehl für Azure Cosmos DB-Tabellen-API (Vorschauversion)](#table-api-preview)

### <a name="command-line-target-settings"></a>Befehlszeilen-Zieleinstellungen

Verwenden Sie die folgenden Zieloptionen, wenn Sie die Tabellen-API von Azure Cosmos DB als Ziel für die Migration festlegen.

    /t:TableAPIBulk: Uploads data into Azure CosmosDB Table in batches
    /t.ConnectionString: Connection string for the table endpoint
    /t.TableName: Specifies the name of the table to write to
    /t.Overwrite: Optional, default is false. Specifies if existing values should be overwritten
    /t.MaxInputBufferSize: Optional, default is 1GB. Approximate estimate of input bytes to buffer before flushing data to sink
    /t.Throughput: Optional, service defaults if not specified. Specifies throughput to configure for table
    /t.MaxBatchSize: Optional, default is 2MB. Specify the batch size in bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Beispiel für einen Befehl: Quelle ist Azure Table Storage

Hier finden Sie ein Beispiel für eine Befehlszeile zum Importieren aus Azure Table Storage in die Tabellen-API:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```
<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Beispiel für einen Befehl: Quelle ist Azure Cosmos DB-Tabellen-API (Vorschauversion)

Hier finden Sie ein Beispiel für eine Befehlszeile zum Importieren aus der Vorschauversion der Tabellen-API in die allgemein verfügbare Version:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Migrieren von Daten mithilfe von AzCopy

Die andere Option zum Migrieren von Daten aus Azure Table Storage in die Table-API von Azure Cosmos DB ist das AzCopy-Befehlszeilenprogramm. Bei Verwendung von AzCopy exportieren Sie Ihre Daten zunächst wie in [Exportieren von Daten aus Table Storage](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage) beschrieben und importieren sie anschließend wie in [Tabellen-API von Azure Cosmos DB](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage) beschrieben in Azure Cosmos DB.

Das folgende Beispiel zeigt das Importieren in Azure Cosmos DB. Beachten Sie, dass für „/dest“ der Wert „cosmosdb“ und nicht „core“ verwendet wird.

Beispiel für den import-Befehl:

```
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migrieren von der Tabellen-API (Vorschauversion) zur Tabellen-API

> [!WARNING]
> Wenn Sie sofort von den Vorteilen der allgemein verfügbaren Tabellen profitieren möchten, migrieren Sie Ihre vorhandenen Tabellen aus der Vorschauversion wie in diesem Abschnitt beschrieben. Andernfalls führen wir in den kommenden Wochen automatische Migrationen für bestehende Kunden der Vorschauversion durch. Beachten Sie jedoch, dass für die automatisch migrierten Tabellen aus der Vorschauversion bestimmte Einschränkungen gelten, die neu erstellte Tabellen nicht betreffen.
> 

Die Tabellen-API ist jetzt allgemein verfügbar. Es gibt Unterschiede zwischen Tabellen der Vorschauversion und der allgemein verfügbaren Version – bei Code, der in der Cloud ausgeführt wird, ebenso wie bei Code, der auf dem Client ausgeführt wird. Es wird daher nicht empfohlen, einen Client der SDK-Vorschauversion mit einem Konto der allgemein verfügbaren Tabellen-API zu kombinieren. Benutzer der Tabellen-API, die weiterhin ihre vorhandenen Tabellen, jedoch in einer Produktionsumgebung verwenden möchten, müssen von der Vorschauversion zur allgemein verfügbaren Umgebung migrieren oder auf die automatische Migration warten. Wenn Sie auf die automatische Migration warten, werden Sie über die Einschränkungen bei den migrierten Tabellen benachrichtigt. Nach der Migration können Sie ohne Einschränkungen neue Tabellen in Ihrem vorhandenen Konto erstellen (nur für migrierte Tabellen gelten Einschränkungen).

So migrieren Sie von der Tabellen-API (Vorschauversion) zur allgemein verfügbaren Tabellen-API

1. Erstellen Sie ein neues Azure Cosmos DB-Konto, und legen Sie den API-Typ wie in [Erstellen eines Datenbankkontos](create-table-dotnet.md#create-a-database-account) beschrieben auf Azure-Tabelle fest.

2. Ändern Sie die Clients, sodass diese die allgemein verfügbare Version der [Tabellen-API SDKs](table-sdk-dotnet.md) verwenden.

3. Migrieren Sie die Clientdaten mit dem Datenmigrationstool aus den Tabellen der Vorschauversion zu den Tabellen der allgemein verfügbaren Version. Anweisungen zum Verwenden des Datenmigrationstools für diesen Zweck finden Sie unter [Datenmigrationstool](#data-migration-tool). 

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Importieren von Daten mit dem Datenmigrationstool
> * Importieren von Daten mit AzCopy
> * Migrieren von der Tabellen-API (Vorschauversion) zur Tabellen-API

Im nächsten Tutorial erfahren Sie, wie Sie Daten mithilfe der Table-API von Azure Cosmos DB abfragen. 

> [!div class="nextstepaction"]
>[Abfragen von Daten](../cosmos-db/tutorial-query-table.md)
