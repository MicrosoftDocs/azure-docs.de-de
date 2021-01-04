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
ms.openlocfilehash: e876ca028532bb3721146e90a91d68c4c12bf79f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096076"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>Migrieren Ihrer Daten zum Azure Cosmos DB-Konto für die Tabellen-API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

In diesem Tutorial erfahren Sie, wie Sie Daten für die Verwendung mit der [Table-API](table-introduction.md) von Azure Cosmos DB importieren. Wenn Sie Daten in Azure Table Storage gespeichert haben, können Sie entweder das Datenmigrationstool oder AzCopy verwenden, um Ihre Daten für die Azure Cosmos DB-Tabellen-API zu importieren. Wenn Sie Daten in einem Konto der Tabellen-API von Azure Cosmos DB (Vorschauversion) gespeichert haben, müssen Sie Ihre Daten mit dem Datenmigrationstool migrieren. 

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Importieren von Daten mit dem Datenmigrationstool
> * Importieren von Daten mit AzCopy
> * Migrieren von der Tabellen-API (Vorschauversion) zur Tabellen-API 

## <a name="prerequisites"></a>Voraussetzungen

* **Erhöhung des Durchsatzes:** Die Dauer der Datenmigration richtet sich nach dem Durchsatzwert, den Sie für einen einzelnen Container oder eine Gruppe von Containern einrichten. Achten Sie darauf, dass Sie den Durchsatz für größere Datenmigrationen erhöhen. Nachdem die Migration abgeschlossen ist, können Sie den Durchsatz wieder verringern, um Kosten zu sparen. Weitere Informationen zur Erhöhung des Durchsatzes im Azure-Portal finden Sie unter „Leistungsstufen und Tarife in Azure Cosmos DB“.

* **Erstellen von Azure Cosmos DB-Ressourcen:** Bevor Sie mit der Migration von Daten beginnen, erstellen Sie zunächst alle Ihre Tabellen im Azure-Portal. Wenn Sie als Migrationsziel ein Azure Cosmos DB-Konto mit Durchsatz auf Datenbankebene verwenden, geben Sie beim Erstellen der Azure Cosmos DB-Tabellen einen Partitionsschlüssel an.

## <a name="data-migration-tool"></a>Datenmigrationstool

Das Datenmigrationstool von Azure Cosmos DB für die Befehlszeile („dt.exe“) kann verwendet werden, um Ihre vorhandenen Azure Table Storage-Daten in ein Konto der allgemein verfügbaren Tabellen-API zu importieren oder um Daten aus einem Konto der Tabellen-API (Vorschauversion) zu einem Konto der allgemein verfügbaren Version der Tabellen-API zu migrieren. Andere Quellen werden derzeit nicht unterstützt. Das UI-basierte Datenmigrationstool (dtui.exe) wird derzeit für Tabellen-API-Konten nicht unterstützt. 

Um eine Migration von Tabellendaten durchzuführen, führen Sie die folgenden Aufgaben aus:

1. Laden Sie das Migrationstool von [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool) herunter.
2. Führen Sie `dt.exe` mit den Befehlszeilenargumenten für Ihr Szenario aus. `dt.exe` akzeptiert einen Befehl im folgenden Format:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

Für diesen Befehl werden die folgenden Optionen unterstützt:

* **/ErrorLog:** Optional. Name der CSV-Datei zum Umleiten bei Datenübertragungsfehlern
* **/OverwriteErrorLog:** Optional. Überschreiben der Fehlerprotokolldatei
* **/ProgressUpdateInterval:** Optional. Standardwert ist „00:00:01“. Zeitintervall für die Aktualisierung der Statusanzeige bei der Datenübertragung.
* **/ErrorDetails:** Optional. Standardwert ist „None“. Gibt an, dass für die folgenden Fehler ausführliche Fehlerinformationen angezeigt werden sollen: „None“, „Critical“, „All“
* **/EnableCosmosTableLog:** Optional. Dient zum Weiterleiten des Protokolls an ein Cosmos-Tabellenkonto. Wenn diese Option festgelegt ist, wird standardmäßig die Verbindungszeichenfolge des Zielkontos verwendet, sofern nicht auch „/CosmosTableLogConnectionString“ angegeben ist. Dies ist hilfreich, wenn mehrere Instanzen von DT gleichzeitig ausgeführt werden.
* **/CosmosTableLogConnectionString:** Optional. „ConnectionString“, um das Protokoll an ein Cosmos-Tabellenkonto an einem Remotespeicherort zu leiten.

### <a name="command-line-source-settings"></a>Befehlszeilen-Quelleinstellungen

Verwenden Sie die folgenden Quelloptionen, wenn Sie Azure Table Storage oder die Vorschauversion der Tabellen-API als Quelle für die Migration festlegen.

* **/s:AzureTable:** Lesen von Daten aus Azure-Tabellenspeicher
* **/s.ConnectionString:** Verbindungszeichenfolge für den Tabellenendpunkt. Sie kann über das Azure-Portal abgerufen werden.
* **/s.LocationMode:** Optional. Standardwert ist „PrimaryOnly“. Gibt an, welcher Standortmodus beim Herstellen einer Verbindung mit Azure-Tabellenspeicher verwendet werden soll: „PrimaryOnly“, „PrimaryThenSecondary“, „SecondaryOnly“, „SecondaryThenPrimary“
* **/s.Table:** Name der Azure-Tabelle
* **/s.InternalFields:** Legen Sie diese Option für die Tabellenmigration auf „All“ fest, da „RowKey“ und „PartitionKey“ für den Import benötigt werden.
* **/s.Filter:** Optional. Anzuwendende Filterzeichenfolge.
* **/s.Projection:** Optional. Liste mit auswählbaren Spalten.

Um die Verbindungszeichenfolge für die Quelle beim Importieren aus Azure Table Storage abzurufen, öffnen Sie das Azure-Portal, klicken Sie auf **Speicherkonten** > **Konto** > **Zugriffsschlüssel** , und verwenden Sie dann die Schaltfläche „Kopieren“, um die **Verbindungszeichenfolge** zu kopieren.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Screenshot der Optionen „Speicherkonten“ > „Konto“ > „Zugriffsschlüssel“ mit hervorgehobener Schaltfläche „Kopieren“":::

Um die Verbindungszeichenfolge für die Quelle beim Importieren aus der Tabellen-API von Azure Cosmos DB abzurufen, öffnen Sie das Azure-Portal, klicken Sie auf **Azure Cosmos DB** > **Konto** > **Verbindungszeichenfolge** , und kopieren Sie dann die **Verbindungszeichenfolge** über die Schaltfläche „Kopieren“.

:::image type="content" source="./media/table-import/cosmos-connection-string.png" alt-text="Screenshot der Optionen für HBase-Quellen":::

[Beispielbefehl für Azure Table Storage](#azure-table-storage)

[Beispielbefehl für Azure Cosmos DB-Tabellen-API (Vorschauversion)](#table-api-preview)

### <a name="command-line-target-settings"></a>Befehlszeilen-Zieleinstellungen

Verwenden Sie die folgenden Zieloptionen, wenn Sie die Tabellen-API von Azure Cosmos DB als Ziel für die Migration festlegen.

* **/t:TableAPIBulk:** Lädt Daten in Batches in Azure CosmosDB-Tabellen hoch.
* **/t.ConnectionString:** Verbindungszeichenfolge für den Tabellenendpunkt.
* **/t.TableName:** Gibt den Namen der Tabelle an, in die geschrieben werden soll.
* **/t.Overwrite:** Optional. Standardwert ist „false“. Gibt an, ob vorhandene Werte überschrieben werden sollen.
* **/t.MaxInputBufferSize:** Optional. Standardwert ist „1 GB“. Ungefähre Schätzung für die zu puffernden Eingabebytes, bevor Daten in die Senke geleert werden.
* **/t.Throughput:** Optional. Standardwerte des Diensts, wenn nichts angegeben ist. Gibt den Durchsatz für die Konfiguration der Tabelle an.
* **/t.MaxBatchSize:** Optional. Standardwert ist „2 MB“. Gibt die Batchgröße in Byte an.

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Beispiel für einen Befehl: Quelle ist Azure Table Storage

Hier finden Sie ein Beispiel für eine Befehlszeile zum Importieren aus Azure Table Storage in die Tabellen-API:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Beispiel für einen Befehl: Quelle ist Azure Cosmos DB-Tabellen-API (Vorschauversion)

Hier finden Sie ein Beispiel für eine Befehlszeile zum Importieren aus der Vorschauversion der Tabellen-API in die allgemein verfügbare Version:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Migrieren von Daten mithilfe von AzCopy

Die andere Option zum Migrieren von Daten aus Azure Table Storage in die Table-API von Azure Cosmos DB ist das AzCopy-Befehlszeilenprogramm. Bei Verwendung von AzCopy exportieren Sie Ihre Daten zunächst wie in [Exportieren von Daten aus Table Storage](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage) beschrieben und importieren sie anschließend wie in [Tabellen-API von Azure Cosmos DB](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage) beschrieben in Azure Cosmos DB.

Das folgende Beispiel zeigt das Importieren in Azure Cosmos DB. Beachten Sie, dass für „/dest“ der Wert „cosmosdb“ und nicht „core“ verwendet wird.

Beispiel für den import-Befehl:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migrieren von der Tabellen-API (Vorschauversion) zur Tabellen-API

> [!WARNING]
> Wenn Sie sofort von den Vorteilen der allgemein verfügbaren Tabellen profitieren möchten, migrieren Sie Ihre vorhandenen Tabellen aus der Vorschauversion wie in diesem Abschnitt beschrieben. Andernfalls führen wir in den kommenden Wochen automatische Migrationen für bestehende Kunden der Vorschauversion durch. Beachten Sie jedoch, dass für die automatisch migrierten Tabellen aus der Vorschauversion bestimmte Einschränkungen gelten, die neu erstellte Tabellen nicht betreffen.

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