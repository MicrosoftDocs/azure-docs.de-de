---
title: 'Tutorial: Datenbankmigrationstool für Azure Cosmos DB'
description: 'Tutorial: Erfahren Sie, wie Sie das Open-Source-basierte Azure Cosmos DB-Datenmigrationstool verwenden, um Daten aus verschiedenen Quellen (beispielsweise MongoDB, SQL Server, Azure Table Storage, Amazon DynamoDB, CSV- und JSON-Dateien) in Azure Cosmos DB zu importieren. CSV-zu-JSON-Konvertierung.'
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: dech
ms.openlocfilehash: 1cee4d2ad1bc7f362a045a5991624ec43521b8d2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341647"
---
# <a name="tutorial-use-data-migration-tool-to-migrate-your-data-to-azure-cosmos-db"></a>Tutorial: Migrieren Ihrer Daten zu Azure Cosmos DB mithilfe des Datenmigrationstools
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Tutorial erfahren Sie, wie Sie das Azure Cosmos DB-Datenmigrationstool verwenden, mit dem Sie Daten aus verschiedenen Quellen in Azure Cosmos-Container und -Tabellen importieren können. Sie können Daten aus JSON-Dateien, CSV-Dateien, SQL-, MongoDB-, Azure Table Storage-, Amazon DynamoDB- und sogar Azure Cosmos DB-SQL-API-Sammlungen importieren. Sie migrieren diese Daten zu Sammlungen und Tabellen, die in Azure Cosmos DB verwendet werden können. Das Datenmigrationstool kann auch für die Migration von einer Sammlung mit einer einzelnen Partition zu einer Sammlung mit mehreren Partitionen für die SQL-API verwendet werden.

> [!NOTE]
> Das Azure Cosmos DB-Datenmigrationstool ist ein Open-Source-Tool, das für kleine Migrationen konzipiert ist. Informationen zu größeren Migrationen finden Sie im [Leitfaden zum Erfassen von Daten](cosmosdb-migrationchoices.md).

* **[SQL-API:](./introduction.md)** Sie können eine beliebige Quelloption des Datenmigrationstools verwenden, um Daten in kleinem Umfang zu importieren. [Erfahren Sie mehr über Migrationsoptionen zum Importieren von Daten in großem Umfang](cosmosdb-migrationchoices.md).
* **[Tabellen-API:](table-introduction.md)** Daten können mithilfe des Datenmigrationstools oder mithilfe von [AzCopy](table-import.md#migrate-data-by-using-azcopy) importiert werden. Weitere Informationen finden Sie unter [Importieren von Daten für die Verwendung mit der Tabellen-API von Azure Cosmos DB](table-import.md).
* **[Azure Cosmos DB-API für MongoDB:](mongodb-introduction.md)** Das Datenmigrationstool weist keine Unterstützung der Azure Cosmos DB-API für MongoDB auf (weder als Quelle noch als Ziel). Wenn Sie die Daten in Azure Cosmos DB in oder aus Sammlungen migrieren möchten, hilft Ihnen die Anleitung unter [Migrieren von MongoDB-Daten zu einer Cosmos-Datenbank mit der Azure Cosmos DB-API für MongoDB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) weiter. Sie können weiterhin das Datenmigrationstool verwenden, um Daten von MongoDB in Azure Cosmos DB-SQL-API-Sammlungen für die Verwendung mit der SQL-API zu exportieren.
* **[Cassandra-API:](graph-introduction.md)** Das Datenmigrationstool wird nicht als Importtool für Cassandra-API-Konten unterstützt. [Erfahren Sie mehr über Migrationsoptionen zum Importieren von Daten in die Cassandra-API](cosmosdb-migrationchoices.md#azure-cosmos-db-cassandra-api).
* **[Gremlin-API:](graph-introduction.md)** Das Datenmigrationstool wird derzeit nicht als Importtool für Gremlin-API-Konten unterstützt. [Erfahren Sie mehr über Migrationsoptionen zum Importieren von Daten in die Gremlin-API](cosmosdb-migrationchoices.md#other-apis). 

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Installieren des Datenmigrationstools
> * Importieren von Daten aus verschiedenen Datenquellen
> * Exportieren aus Azure Cosmos DB zu JSON

## <a name="prerequisites"></a><a id="Prerequisites"></a>Voraussetzungen

Bevor Sie die Anweisungen in diesem Artikeln befolgen, müssen Sie die folgenden Schritte ausführen:

* **Installieren** Sie [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) oder höher.

* **Erhöhung des Durchsatzes:** Die Dauer der Datenmigration richtet sich nach dem Durchsatzwert, den Sie für eine einzelne Sammlung oder eine Gruppe von Sammlungen einrichten. Achten Sie darauf, dass Sie den Durchsatz für größere Datenmigrationen erhöhen. Nachdem die Migration abgeschlossen ist, können Sie den Durchsatz wieder verringern, um Kosten zu sparen. Weitere Informationen zum Erhöhen des Durchsatzes im Azure-Portal finden Sie in den Artikeln zu den [Leistungsebenen](performance-levels.md) und [Tarifen](https://azure.microsoft.com/pricing/details/cosmos-db/) in Azure Cosmos DB.

* **Erstellen von Azure Cosmos DB-Ressourcen:** Bevor Sie mit der Migration von Daten beginnen, erstellen Sie zunächst alle Ihre Sammlungen im Azure-Portal. Wenn Sie eine Migration zu einem Azure Cosmos DB-Konto mit Durchsatz auf Datenbankebene durchführen möchten, geben Sie beim Erstellen der Azure Cosmos-Container einen Partitionsschlüssel an.

> [!IMPORTANT]
> Um sicherzustellen, dass das Datenmigrationstool beim Herstellen einer Verbindung mit ihren Azure Cosmos-Konten Transport Layer Security (TLS) 1.2 verwendet, verwenden Sie die .NET Framework-Version 4.7, oder befolgen Sie die Anweisungen in [diesem Artikel](/dotnet/framework/network-programming/tls).

## <a name="overview"></a><a id="Overviewl"></a>Übersicht

Das Datenmigrationstool ist eine Open-Source-Lösung, mit der Daten aus verschiedensten Quellen in Azure Cosmos DB importiert werden können. Zu diesen Quellen zählen unter anderem:

* JSON-Dateien
* MongoDB
* SQL Server
* CSV-Dateien
* Azure-Tabellenspeicher
* Amazon DynamoDB
* hbase
* Azure Cosmos-Container

Das Importtool verfügt zwar über eine grafische Benutzeroberfläche (dtui.exe), kann aber auch über die Befehlszeile (dt.exe) gesteuert werden. In der Tat gibt es eine Option, mit der nach dem Einrichten eines Imports über die Benutzeroberfläche der zugehörige Befehl ausgegeben werden kann. Sie können Quelldaten im Tabellenformat (z. B. SQL Server- oder CSV-Dateien) transformieren, um während des Imports hierarchische Beziehungen (Unterdokumente) zu erstellen. Hier erhalten Sie weitere Informationen zu Quelloptionen, zu Beispielbefehlen für den Import aus den einzelnen Quellen, zu Zieloptionen sowie zum Anzeigen der Importergebnisse.

> [!NOTE]
> Sie sollten das Azure Cosmos DB-Migrationstool nur für kleine Migrationen verwenden. Informationen zu großen Migrationen finden Sie im [Leitfaden zum Erfassen von Daten](cosmosdb-migrationchoices.md).

## <a name="installation"></a><a id="Install"></a>Installation

Der Quellcode des Migrationstools ist auf GitHub in [diesem Repository](https://github.com/azure/azure-documentdb-datamigrationtool) verfügbar. Sie können die Lösung herunterladen und lokal kompilieren oder eine [vorkompilierte Binärdatei herunterladen](https://aka.ms/csdmtool), und dann eine der folgenden Dateien ausführen:

* **Dtui.exe**: GUI-Version des Tools
* **Dt.exe**: Befehlszeilenversion des Tools

## <a name="select-data-source"></a>Auswählen einer Datenquelle

Nachdem Sie das Tool installiert haben, können Ihre Daten importiert werden. Welche Art von Daten möchten Sie importieren?

* [JSON-Dateien](#JSON)
* [MongoDB](#MongoDB)
* [MongoDB-Exportdateien](#MongoDBExport)
* [SQL Server](#SQL)
* [CSV-Dateien](#CSV)
* [Azure Table Storage](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Blob](#BlobImport)
* [Azure Cosmos-Container](#SQLSource)
* [HBase](#HBaseSource)
* [Azure Cosmos DB-Massenimport](#SQLBulkTarget)
* [Sequenzieller Datensatzimport mit Azure DB Cosmos](#SQLSeqTarget)

## <a name="import-json-files"></a><a id="JSON"></a>Importieren von JSON-Dateien

Mit der Importprogrammoption für JSON-Dateiquellen können Sie eine oder mehrere JSON-Dateien mit einem einzelnen Dokument oder JSON-Dateien, die jeweils ein Array von JSON-Dokumenten enthalten, importieren. Wenn Sie Ordner hinzufügen, die zu importierende JSON-Dateien enthalten, können Sie in den Unterordnern rekursiv nach Dateien suchen.

:::image type="content" source="./media/import-data/jsonsource.png" alt-text="Screenshot der Optionen für JSON-Dateiquellen – Datenbank-Migrationstools":::

Die Verbindungszeichenfolge hat folgendes Format:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>`

* `<CosmosDB Endpoint>` ist der Endpunkt-URI. Diesen Wert können Sie im Azure-Portal ermitteln. Navigieren Sie zu Ihrem Azure Cosmos-Konto. Öffnen Sie den Bereich **Übersicht**, und kopieren Sie den Wert von **URI**.
* `<AccountKey>` ist das „Kennwort“ oder der **PRIMÄRSCHLÜSSEL**. Diesen Wert können Sie im Azure-Portal ermitteln. Navigieren Sie zu Ihrem Azure Cosmos-Konto. Öffnen Sie einen der Bereiche **Verbindungszeichenfolgen** oder **Schlüssel**, und kopieren Sie das „Kennwort“ oder den Wert von **PRIMÄRSCHLÜSSEL**.
* `<CosmosDB Database>` ist der Name der Cosmos DB-Datenbank.

Beispiel: `AccountEndpoint=https://myCosmosDBName.documents.azure.com:443/;AccountKey=wJmFRYna6ttQ79ATmrTMKql8vPri84QBiHTt6oinFkZRvoe7Vv81x9sn6zlVlBY10bEPMgGM982wfYXpWXWB9w==;Database=myDatabaseName`

> [!NOTE]
> Verwenden Sie den Verify-Befehl, um sicherzustellen, dass auf das im Feld „Verbindungszeichenfolge“ angegebene Cosmos DB-Konto zugegriffen werden kann.

Hier finden Sie einige Befehlszeilenbeispiele zum Importieren von JSON-Dateien:

```console
#Import a single JSON file
dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory of JSON files
dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory (including sub-directories) of JSON files
dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

#Import a directory (single), directory (recursive), and individual JSON files
dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

#Import a single JSON file and partition the data across 4 collections
dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500
```

## <a name="import-from-mongodb"></a><a id="MongoDB"></a>Importieren aus MongoDB

> [!IMPORTANT]
> Wenn Sie den Import in ein Cosmos-Konto durchführen, das mit der Azure Cosmos DB-API für MongoDB konfiguriert wurde, hilft Ihnen diese [Anleitung](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) weiter.

Mit der Importprogrammoption für MongoDB-Dateiquellen können Sie Dateien aus einer einzelnen MongoDB-Sammlung importieren, Dokumente optional mit einer Abfrage filtern und die Dokumentstruktur mithilfe einer Projektion ändern.  

:::image type="content" source="./media/import-data/mongodbsource.png" alt-text="Screenshot der Optionen für MongoDB-Quellen":::

Die Verbindungszeichenfolge weist das MongoDB-Standardformat auf:

`mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>`

> [!NOTE]
> Verwenden Sie den Befehl "Überprüfen", um sicherzustellen, dass auf die im Feld "Verbindungszeichenfolge" angegebene MongoDB-Instanz zugegriffen werden kann.

Geben Sie den Namen der Sammlung ein, aus der Daten importiert werden sollen. Optional können Sie eine Datei für eine Abfrage (z. B. `{pop: {$gt:5000}}`) oder eine Projektion (z. B. `{loc:0}`) angeben oder bereitstellen, um die zu importierenden Daten zu filtern und zu strukturieren.

Hier finden Sie einige Befehlszeilenbeispiele zum Importieren aus MongoDB:

```console
#Import all documents from a MongoDB collection
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

#Import documents from a MongoDB collection which match the query and exclude the loc field
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500
```

## <a name="import-mongodb-export-files"></a><a id="MongoDBExport"></a>Importieren von MongoDB-Exportdateien

> [!IMPORTANT]
> Falls Sie Daten in ein Azure Cosmos DB-Konto mit Unterstützung für MongoDB importieren, befolgen Sie diese [Anweisungen](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json).

Mit der Importprogrammoption für JSON-Dateiquellen und MongoDB-Exportdateien können Sie eine oder mehrere JSON-Dateien importieren, die mit dem mongoexport-Dienstprogramm erzeugt wurden.  

:::image type="content" source="./media/import-data/mongodbexportsource.png" alt-text="Screenshot der Optionen für MongoDB-Exportquellen":::

Wenn Sie Ordner hinzufügen, die zu importierende JSON-Dateien aus einem MongoDB-Export enthalten, können Sie in den Unterordnern rekursiv nach Dateien suchen.

Hier finden Sie ein Befehlszeilenbeispiel zum Importieren von JSON-Dateien aus einem MongoDB-Export:

```console
dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500
```

## <a name="import-from-sql-server"></a><a id="SQL"></a>Importieren von SQL Server

Mit der Importprogrammoption für SQL-Quellen können Sie Datensätze aus einer einzelnen SQL Server-Datenbank importieren und mithilfe einer Abfrage filtern. Darüber hinaus können Sie die Dokumentstruktur ändern, indem Sie ein Schachtelungstrennzeichen angeben (mehr dazu weiter unten).  

:::image type="content" source="./media/import-data/sqlexportsource.png" alt-text="Screenshot der Optionen für SQL-Dateiquellen – Datenbank-Migrationstools":::

Die Verbindungszeichenfolge weist das standardmäßige Format für SQL-Verbindungszeichenfolgen auf.

> [!NOTE]
> Verwenden Sie den Befehl "Überprüfen", um sicherzustellen, dass auf die im Feld "Verbindungszeichenfolge" angegebene SQL Server-Instanz zugegriffen werden kann.

Die Schachtelungstrennzeicheneigenschaft wird zum Erstellen hierarchischer Beziehungen (Filialdokumente) während des Imports verwendet. Betrachten Sie die folgende SQL-Abfrage:

`select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'`

Diese Abfrage gibt die folgenden (partiellen) Ergebnisse zurück:

:::image type="content" source="./media/import-data/sqlqueryresults.png" alt-text="Screenshot der SQL-Abfrageergebnisse":::

Beachten Sie die Aliase, wie z. B. Address.AddressType und Address.Location.StateProvinceName. Indem Sie das Schachtelungstrennzeichen „.“ angeben, erstellt das Importtool während des Imports die Filialdokumente „Address“ und „Address.Location“. Hier finden Sie ein Beispiel für ein Zieldokument in Azure Cosmos DB:

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

Hier finden Sie einige Befehlszeilenbeispiele zum Importieren aus SQL Server:

```console
#Import records from SQL which match a query
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

#Import records from sql which match a query and create hierarchical relationships
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500
```

## <a name="import-csv-files-and-convert-csv-to-json"></a><a id="CSV"></a>Importieren von CSV-Dateien und Konvertieren von CSV in JSON

Mit der Importprogrammoption für CSV-Dateiquellen können Sie eine oder mehrere CSV-Dateien importieren. Wenn Sie Ordner hinzufügen, die zu importierende CSV-Dateien enthalten, können Sie in den Unterordnern rekursiv nach Dateien suchen.

:::image type="content" source="media/import-data/csvsource.png" alt-text="Screenshot der Optionen für CSV-Quellen – CSV in JSON":::

Ähnlich wie bei SQL-Quelldateien wird die Schachtelungstrennzeicheneigenschaft zum Erstellen hierarchischer Beziehungen (Filialdokumente) während des Imports verwendet. Betrachten Sie folgende CSV-Kopfzeile und Datenzeilen:

:::image type="content" source="./media/import-data/csvsample.png" alt-text="Screenshot der CSV-Beispieldatensätze – CSV in JSON":::

Beachten Sie die Aliase wie z. B."DomainInfo.Domain_Name" und "RedirectInfo.Redirecting". Durch Angabe des Schachtelungstrennzeichens „.“ erstellt das Importtool während des Imports die Filialdokumente „DomainInfo“ und „RedirectInfo“. Hier finden Sie ein Beispiel für ein Zieldokument in Azure Cosmos DB:

*{ "DomainInfo": { "Domain_Name": "ACUS.GOV", "Domain_Name_Address": "https:\//www.ACUS.GOV" }, "Federal Agency": "Administrative Conference of the United States", "RedirectInfo": { "Redirecting": "0", "Redirect_Destination": "" }, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }*

Das Importtool versucht, Typinformationen für Werte ohne Anführungszeichen in CSV-Dateien abzuleiten. (Werte in Anführungszeichen werden immer als Zeichenfolgen behandelt.)  Typen werden in der folgenden Reihenfolge identifiziert: number, datetime, boolean.  

In Bezug auf CSV-Importe gibt es zwei weitere Dinge zu beachten:

1. Standardmäßig werden Tabstopps und Anführungszeichen aus Werten ohne Anführungszeichen immer gekürzt, während Werte in Anführungszeichen unverändert beibehalten werden. Dieses Verhalten kann mithilfe des Kontrollkästchens „Trim quoted values“ (Werte in Anführungszeichen kürzen) oder der Befehlszeilenoption „/s.TrimQuoted“ überschrieben werden.
2. Standardmäßig wird eine Null ohne Anführungszeichen als Nullwert behandelt. Dieses Verhalten kann mithilfe des Kontrollkästchens „Treat unquoted NULL as string“ (NULL-Wert ohne Anführungszeichen als Zeichenfolge behandeln) oder der Befehlszeilenoption „/s.NoUnquotedNulls“ außer Kraft gesetzt werden, sodass ein NULL-Wert ohne Anführungszeichen als NULL-Zeichenfolge behandelt wird.

Hier finden Sie ein Beispiel für eine Befehlszeile zum Importieren von CSV-Dateien:

```console
dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500
```

## <a name="import-from-azure-table-storage"></a><a id="AzureTableSource"></a>Importieren aus dem Azure-Tabellenspeicher

Mit der Importprogrammoption für Azure Table Storage-Quellen können Sie Daten aus einer einzelnen Azure Table Storage-Tabelle importieren. Optional können Sie die zu importierenden Tabellenentitäten filtern.

Sie können aus Azure Table Storage importierte Daten zur Verwendung mit der Tabellen-API in Azure Cosmos DB-Tabellen und -Entitäten ausgeben. Importierte Daten können zur Verwendung mit der SQL-API auch in Sammlungen und Dokumente ausgegeben werden. Die Tabellen-API ist jedoch nur als Ziel im Befehlszeilenprogramm verfügbar. Es ist nicht möglich, Daten über die Benutzeroberfläche des Datenmigrationstools in die Tabellen-API zu exportieren. Weitere Informationen finden Sie unter [Importieren von Daten für die Verwendung mit der Tabellen-API von Azure Cosmos DB](table-import.md).

:::image type="content" source="./media/import-data/azuretablesource.png" alt-text="Screenshot der Optionen für Azure-Tabellenspeicherquellen":::

Die Verbindungszeichenfolge für den Azure-Tabellenspeicher weist folgendes Format auf:

`DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;`

> [!NOTE]
> Verwenden Sie den Befehl "Überprüfen", um sicherzustellen, dass auf die im Feld "Verbindungszeichenfolge" angegebene Azure-Tabellenspeicherinstanz zugegriffen werden kann.

Geben Sie den Namen der Azure-Tabelle ein, aus der Daten importiert werden sollen. Optional können Sie einen [Filter](/visualstudio/azure/vs-azure-tools-table-designer-construct-filter-strings)angeben.

Die Importprogrammoption für Azure-Tabellenspeicherquellen weist die folgenden zusätzlichen Optionen auf:

1. Interne Felder einschließen
   1. Alle - alle internen Felder einschließen (PartitionKey, RowKey und Timestamp)
   2. Keine - alle externen Felder ausschließen
   3. RowKey - nur das RowKey-Feld einschließen
2. Spalten auswählen
   1. Azure Table Storage-Filter unterstützen keine Projektionen. Wenn Sie nur bestimmte Eigenschaften der Azure-Tabellenentität importieren möchten, fügen Sie sie zur Liste "Spalten auswählen" hinzu. Alle anderen Entitätseigenschaften werden ignoriert.

Hier finden Sie ein Beispiel für eine Befehlszeile zum Importieren aus Azure Table Storage:

```console
dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500
```

## <a name="import-from-amazon-dynamodb"></a><a id="DynamoDBSource"></a>Importieren von Amazon DynamoDB

Mit der Importprogrammoption für Amazon DynamoDB-Dateiquellen können Sie Daten aus einer einzelnen Amazon DynamoDB-Tabelle importieren. Optional können die zu importierenden Entitäten gefiltert werden. Mehrere Vorlagen werden für einen einfachen Import bereitgestellt.

:::image type="content" source="./media/import-data/dynamodbsource1.png" alt-text="Screenshot der Optionen für Amazon DynamoDB-Quellen – Datenbank-Migrationstools":::

:::image type="content" source="./media/import-data/dynamodbsource2.png" alt-text="Screenshot der Optionen für Amazon DynamoDB-Quellen mit Vorlage – Datenbank-Migrationstools":::

Das Format der Amazon-DynamoDB-Verbindungszeichenfolge ist:

`ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;`

> [!NOTE]
> Verwenden Sie den Befehl „Überprüfen“, um sicherzustellen, dass auf die im Feld „Verbindungszeichenfolge“ angegebene Amazon DynamoDB-Instanz zugegriffen werden kann.

Hier finden Sie ein Beispiel für eine Befehlszeile zum Importieren aus Amazon DynamoDB:

```console
dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500
```

## <a name="import-from-azure-blob-storage"></a><a id="BlobImport"></a>Importieren aus Azure Blob Storage

Mit den Importprogrammoptionen für JSON-Dateien, MongoDB-Exportdateien und die CSV-Dateien können Sie eine oder mehrere Dateien aus Azure Blob Storage importieren. Nach Angabe von Blob-Container-URL und Kontoschlüssel geben Sie einen regulären Ausdruck ein, um die zu importierenden Dateien auszuwählen.

:::image type="content" source="./media/import-data/blobsource.png" alt-text="Screenshot der Optionen für Blob-Dateiquellen":::

Hier finden Sie ein Beispiel für eine Befehlszeile zum Importieren von JSON-Dateien aus Azure Blob Storage:

```console
dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest
```

## <a name="import-from-a-sql-api-collection"></a><a id="SQLSource"></a>Importieren aus einer SQL-API-Sammlung

Mit der Importprogrammoption für Azure Cosmos DB-Quellen können Sie Daten aus Azure Cosmos-Containern importieren und optional Dokumente mithilfe einer Abfrage filtern.  

:::image type="content" source="./media/import-data/documentdbsource.png" alt-text="Screenshot der Optionen für Azure Cosmos DB-Quellen":::

Die Azure Cosmos DB-Verbindungszeichenfolge weist folgendes Format auf:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Sie können die Verbindungszeichenfolge für das Azure Cosmos DB-Konto wie unter [Verwalten eines Azure Cosmos DB-Kontos](./how-to-manage-database-account.md) beschrieben auf der Seite „Schlüssel“ im Azure-Portal abrufen. Der Name der Datenbank muss jedoch im folgenden Format an die Verbindungszeichenfolge angefügt werden:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Verwenden Sie den Befehl „Überprüfen“, um sicherzustellen, dass auf die im Feld „Verbindungszeichenfolge“ angegebene Azure Cosmos DB-Instanz zugegriffen werden kann.

Zum Importieren von Daten aus einem einzelnen Azure Cosmos-Container geben Sie den Namen der Sammlung ein, aus der Daten importiert werden sollen. Zum Importieren von Daten aus mehreren Azure Cosmos-Containern geben Sie einen regulären Ausdruck für den Namen von mindestens einer Sammlung an (Beispiel: Sammlung01 | Sammlung02 | Sammlung03). Optional können Sie eine Datei für eine Abfrage angeben oder bereitstellen, um die zu importierenden Daten zu filtern und zu strukturieren.

> [!NOTE]
> Wenn Sie Daten aus einer einzelnen Sammlung importieren, deren Name in regulären Ausdrücken verwendete Zeichen enthält, müssen diese Zeichen mit Escapezeichen versehen werden, da für das Feld „Sammlung“ reguläre Ausdrücke zulässig sind.

Die Importprogrammoption für Azure Cosmos DB-Quellen weist die folgenden erweiterten Optionen auf:

1. Include Internal Fields (Interne Felder einschließen): Gibt an, ob Azure Cosmos DB-Dokumentsystemeigenschaften (beispielsweise „_rid“ oder „_ts“) in den Export eingeschlossen werden sollen.
2. Number of Retries on Failure (Anzahl der Wiederholungsversuche bei Fehler) Gibt an, wie häufig bei einem vorübergehenden Fehler (beispielsweise bei einer Unterbrechung der Netzwerkverbindung) versucht werden soll, eine Verbindung mit Azure Cosmos DB herzustellen.
3. Wiederholungsintervall: Gibt an, wie lange bei einem vorübergehenden Fehler (beispielsweise bei einer Unterbrechung der Netzwerkverbindung) gewartet werden soll, bevor erneut versucht wird, eine Verbindung mit Azure Cosmos DB herzustellen.
4. Verbindungsmodus: Gibt den Verbindungsmodus an, der mit Azure Cosmos DB verwendet werden soll. Verfügbare Optionen sind DirectTcp, DirectHttps und Gateway. Die Direktverbindungsmodi sind schneller, der Gatewaymodus ist besser für Firewalls geeignet, da nur Port 443 verwendet wird.

:::image type="content" source="./media/import-data/documentdbsourceoptions.png" alt-text="Screenshot der erweiterten Optionen für Azure Cosmos DB-Quellen":::

> [!TIP]
> Standardmäßig verwendet das Importtool den Verbindungsmodus DirectTcp. Wenn Firewallprobleme auftreten, wechseln Sie zum Gatewaymodus, da dieser nur Port 443 erfordert.

Hier finden Sie einige Beispiele für Befehlszeilen zum Importieren aus Azure Cosmos DB:

```console
#Migrate data from one Azure Cosmos container to another Azure Cosmos containers
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

#Migrate data from more than one Azure Cosmos container to a single Azure Cosmos container
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

#Export an Azure Cosmos container to a JSON file
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite
```

> [!TIP]
> Das Azure Cosmos DB-Tool zum Importieren von Daten unterstützt auch das Importieren von Daten aus dem [Azure Cosmos DB Emulator](local-emulator.md). Legen Sie beim Importieren von Daten aus einem lokalen Emulator den Endpunkt auf `https://localhost:<port>` fest.

## <a name="import-from-hbase"></a><a id="HBaseSource"></a>Importieren von HBase

Die Importprogrammoption für HBase-Quellen können Sie zum Importieren von Daten aus einer HBase-Tabelle und optional zum Filtern der Daten verwenden. Mehrere Vorlagen werden für einen einfachen Import bereitgestellt.

:::image type="content" source="./media/import-data/hbasesource1.png" alt-text="Screenshot der Optionen für HBase-Quellen":::

:::image type="content" source="./media/import-data/hbasesource2.png" alt-text="Screenshot der Optionen für HBase-Quellen mit erweitertem Kontextmenü „Filter“":::

Das Format der Verbindungszeichenfolge „HBase Stargate“ lautet:

`ServiceURL=<server-address>;Username=<username>;Password=<password>`

> [!NOTE]
> Verwenden Sie den Befehl „Überprüfen“, um sicherzustellen, dass auf die im Feld „Verbindungszeichenfolge“ angegebene HBase-Instanz zugegriffen werden kann.

Hier finden Sie ein Beispiel für eine Befehlszeile zum Importieren ausHBase:

```console
dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport
```

## <a name="import-to-the-sql-api-bulk-import"></a><a id="SQLBulkTarget"></a>Importieren in die SQL-API (Massenimport)

Mit dem Azure Cosmos DB-Massenimportprogramm können Sie Daten aus allen verfügbaren Quelloptionen importieren und dabei zur Erhöhung der Effizienz eine gespeicherte Azure Cosmos DB-Prozedur verwenden. Das Tool unterstützt den Import in einen Azure Cosmos-Container mit einer einzelnen Partition. Das Tool unterstützt auch den Shardimport, bei dem Daten über mehrere Azure Cosmos-Container mit einer einzelnen Partition hinweg partitioniert werden. Weitere Informationen zur Partitionierung von Daten finden Sie unter [Partitionieren und Skalieren von Daten in Azure Cosmos DB](partitioning-overview.md). Mit dem Tool wird die gespeicherte Prozedur erstellt, ausgeführt und anschließend aus den Zielsammlungen gelöscht.  

:::image type="content" source="./media/import-data/documentdbbulk.png" alt-text="Screenshot der Optionen für Azure Cosmos DB-Massenvorgänge":::

Die Azure Cosmos DB-Verbindungszeichenfolge weist folgendes Format auf:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Die Verbindungszeichenfolge für das Azure Cosmos DB-Konto kann im Azure-Portal auf der Seite „Schlüssel“ abgerufen werden. Eine Beschreibung hierzu finden Sie unter [Verwalten eines Azure Cosmos DB-Kontos](./how-to-manage-database-account.md). Der Name der Datenbank muss im folgenden Format an das Ende der Verbindungszeichenfolge angehängt werden:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Verwenden Sie den Befehl „Überprüfen“, um sicherzustellen, dass auf die im Feld „Verbindungszeichenfolge“ angegebene Azure Cosmos DB-Instanz zugegriffen werden kann.

Zum Importieren von Daten in eine einzelne Sammlung geben Sie den Namen der Sammlung ein, aus der Daten importiert werden sollen, und klicken dann auf die Schaltfläche „Hinzufügen“. Zum Importieren von Daten in mehrere Sammlungen geben Sie entweder jeden Sammlungsnamen einzeln ein, oder geben Sie mit der folgenden Syntax mehrere Sammlungen an: *Sammlungspräfix*[Startindex - Endindex]. Wenn Sie mehrere Sammlungen mit der obigen Syntax angeben, müssen Sie folgende Richtlinien beachten:

1. Nur Namensmuster im ganzzahligen Bereich werden unterstützt. Durch Angabe von „Sammlung[0-3]“ werden beispielsweise folgende Sammlungen erstellt: Sammlung0, Sammlung1, Sammlung2, Sammlung3.
2. Sie können eine abgekürzte Syntax verwenden: Durch Angabe von „Sammlung[3]“ wird der gleiche Satz von Sammlungen erstellt wie in Schritt 1.
3. Mehrere Ersetzungen können bereitgestellt werden. „Sammlung[0-1] [0-9]“ generiert beispielsweise 20 Sammlungsnamen mit führenden Nullen (Sammlung01, ...02, ...03).

Nachdem Sie den bzw. die Sammlungsnamen angegeben haben, wählen Sie den gewünschten Durchsatz für die Sammlung(en) aus (400 RUs bis 10.000 RUs). Wählen Sie einen höheren Durchsatz, um die beste Importleistung zu erreichen. Weitere Informationen zu Leistungsebenen finden Sie unter [Leistungsebenen in Azure Cosmos DB](performance-levels.md).

> [!NOTE]
> Die Einstellung für den Leistungsdurchsatz gilt nur für die Sammlungserstellung. Wenn die angegebene Sammlung bereits vorhanden ist, wird ihr Durchsatz nicht geändert.

Beim Importieren von Daten in mehrere Sammlungen unterstützt das Importtool Sharding auf Hashbasis. Geben Sie in diesem Szenario die Dokumenteigenschaft an, die als Partitionsschlüssel verwendet werden soll. (Ohne Angabe des Partitionsschlüssels erfolgt das Sharding der Dokumente nach dem Zufallsprinzip über die Zielsammlungen hinweg.)

Optional können Sie angeben, welches Feld in der Importquelle während des Imports als Azure Cosmos DB-Dokument-ID-Eigenschaft verwendet werden soll. Falls Dokumente diese Eigenschaft nicht enthalten, generiert das Importtool eine GUID als ID-Eigenschaftswert.

Während des Imports steht eine Reihe von erweiterten Optionen zur Verfügung. Erstens: Zwar enthält das Tool eine standardmäßige gespeicherte Prozedur für den Massenimport (BulkInsert.js), Sie können jedoch auch eine eigene gespeicherte Prozedur für den Import angeben.

 :::image type="content" source="./media/import-data/bulkinsertsp.png" alt-text="Screenshot der Azure Cosmos DB-Option zum Masseneinfügen mit gespeicherten Prozeduren":::

Darüber hinaus können Sie beim Importieren von Datentypen (beispielsweise aus SQL Server oder MongoDB) zwischen drei Importoptionen wählen:

 :::image type="content" source="./media/import-data/datetimeoptions.png" alt-text="Screenshot der Optionen für den Azure Cosmos DB-Import von Datum und Uhrzeit":::

* Zeichenfolge: Als Zeichenfolgenwert beibehalten
* Epoche: Als numerischen Epochenwert beibehalten
* Beide: Sowohl als Zeichenfolgen- als auch als numerischen Epochenwert beibehalten Diese Option erstellt ein Unterdokument. Beispiel: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Das Azure Cosmos DB-Massenimportprogramm weist die folgenden erweiterten Optionen auf:

1. Batchgröße: Das Tool verwendet standardmäßig eine Batchgröße von 50.  Wenn die zu importierenden Dokumente groß sind, erwägen Sie, die Batchgröße zu verringern. Wenn die zu importierenden Dokumente dagegen klein sind, erwägen Sie, die Batchgröße zu erhöhen.
2. Max Script Size (bytes) (Maximale Skriptgröße (Bytes)): Das Tool verwendet standardmäßig eine maximale Skriptgröße von 512 KB.
3. Disable Automatic Id Generation (Automatische ID-Generierung deaktivieren): Wenn jedes zu importierende Dokument ein ID-Feld enthält, kann die Leistung durch Auswahl dieser Option verbessert werden. Dokumente ohne Feld für eine eindeutige ID werden nicht importiert.
4. Update Existing Documents (Vorhandene Dokumente aktualisieren): Das Tool ersetzt vorhandene Dokumente mit ID-Konflikten standardmäßig nicht. Bei Auswahl dieser Option können bereits vorhandene Dokumente mit übereinstimmenden IDs überschrieben werden. Diese Funktion ist hilfreich für geplante Datenmigrationen, die vorhandene Dokumente aktualisieren.
5. Number of Retries on Failure (Anzahl der Wiederholungsversuche bei Fehler) Die Anzahl von Verbindungsversuchen, die bei einem vorübergehenden Fehler (beispielsweise bei einer Unterbrechung der Netzwerkverbindung) ausgeführt werden, um eine Verbindung mit Azure Cosmos DB herzustellen.
6. Wiederholungsintervall: Gibt an, wie lange bei einem vorübergehenden Fehler (beispielsweise bei einer Unterbrechung der Netzwerkverbindung) gewartet werden soll, bevor erneut versucht wird, eine Verbindung mit Azure Cosmos DB herzustellen.
7. Verbindungsmodus: Gibt den Verbindungsmodus an, der mit Azure Cosmos DB verwendet werden soll. Verfügbare Optionen sind DirectTcp, DirectHttps und Gateway. Die Direktverbindungsmodi sind schneller, der Gatewaymodus ist besser für Firewalls geeignet, da nur Port 443 verwendet wird.

:::image type="content" source="./media/import-data/docdbbulkoptions.png" alt-text="Screenshot der erweiterten Optionen für den Azure Cosmos DB-Massenimport":::

> [!TIP]
> Standardmäßig verwendet das Importtool den Verbindungsmodus DirectTcp. Wenn Firewallprobleme auftreten, wechseln Sie zum Gatewaymodus, da dieser nur Port 443 erfordert.

## <a name="import-to-the-sql-api-sequential-record-import"></a><a id="SQLSeqTarget"></a>Importieren in die SQL-API (sequenzieller Datensatzimport)

Mit dem Programm für den Import von sequenziellen Azure Cosmos DB-Datensätzen können Sie Datensätze einzeln aus einer verfügbaren Quelloption importieren. Sie können diese Option auswählen, wenn Sie Datensätze in eine vorhandene Sammlung importieren, die das Kontingent an gespeicherten Prozeduren erreicht hat. Das Tool unterstützt den Import in einen einzelnen Azure Cosmos-Container (mit einer Partition und mit mehreren Partitionen). Das Tool unterstützt auch den Shardimport, bei dem Daten über mehrere Azure Cosmos-Container mit einer einzelnen Partition oder mehreren Partitionen hinweg partitioniert werden. Weitere Informationen zur Partitionierung von Daten finden Sie unter [Partitionieren und Skalieren von Daten in Azure Cosmos DB](partitioning-overview.md).

:::image type="content" source="./media/import-data/documentdbsequential.png" alt-text="Screenshot der Optionen für den sequenziellen Azure Cosmos DB-Datensatzimport":::

Die Azure Cosmos DB-Verbindungszeichenfolge weist folgendes Format auf:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Sie können die Verbindungszeichenfolge für das Azure Cosmos DB-Konto wie unter [Verwalten eines Azure Cosmos DB-Kontos](./how-to-manage-database-account.md) beschrieben auf der Seite „Schlüssel“ im Azure-Portal abrufen. Der Name der Datenbank muss jedoch im folgenden Format an die Verbindungszeichenfolge angefügt werden:

`Database=<Azure Cosmos database>;`

> [!NOTE]
> Verwenden Sie den Befehl „Überprüfen“, um sicherzustellen, dass auf die im Feld „Verbindungszeichenfolge“ angegebene Azure Cosmos DB-Instanz zugegriffen werden kann.

Zum Importieren in eine einzelne Sammlung geben Sie den Namen der Sammlung ein, in die die Daten importiert werden sollen, und klicken Sie dann auf die Schaltfläche „Hinzufügen“. Zum Importieren von Daten in mehrere Sammlungen geben Sie jeden Sammlungsnamen einzeln ein. Sie können auch die folgende Syntax verwenden, um mehrere Sammlungen anzugeben: *Sammlungspräfix*[Startindex - Endindex]. Wenn Sie mehrere Sammlungen mit der obigen Syntax angeben, müssen Sie folgende Richtlinien beachten:

1. Nur Namensmuster im ganzzahligen Bereich werden unterstützt. Durch Angabe von „Sammlung[0-3]“ werden beispielsweise folgende Sammlungen erstellt: Sammlung0, Sammlung1, Sammlung2, Sammlung3.
2. Sie können eine abgekürzte Syntax verwenden: Durch Angabe von „Sammlung[3]“ wird der gleiche Satz von Sammlungen erstellt wie in Schritt 1.
3. Mehrere Ersetzungen können bereitgestellt werden. „Sammlung[0-1] [0-9]“ erstellt beispielsweise 20 Sammlungsnamen mit führenden Nullen (Sammlung01, ...02, ...03).

Nachdem Sie den bzw. die Sammlungsnamen angegeben haben, wählen Sie den gewünschten Durchsatz für die Sammlung(en) aus (400 RUs bis 250.000 RUs). Wählen Sie einen höheren Durchsatz, um die beste Importleistung zu erreichen. Weitere Informationen zu Leistungsebenen finden Sie unter [Leistungsebenen in Azure Cosmos DB](performance-levels.md). Importvorgänge in Sammlungen mit einem Durchsatz von mehr als 10.000 RUs erfordern einen Partitionsschlüssel. Ab 250.000 RUs müssen Sie im Portal eine Erhöhungsanfrage für Ihr Konto stellen.

> [!NOTE]
> Die Durchsatzeinstellung gilt nur für die Sammlung oder Sammlungserstellung. Wenn die angegebene Sammlung bereits vorhanden ist, wird ihr Durchsatz nicht geändert.

Beim Importieren von Daten in mehrere Sammlungen unterstützt das Importtool Sharding auf Hashbasis. Geben Sie in diesem Szenario die Dokumenteigenschaft an, die als Partitionsschlüssel verwendet werden soll. (Ohne Angabe des Partitionsschlüssels erfolgt das Sharding der Dokumente nach dem Zufallsprinzip über die Zielsammlungen hinweg.)

Optional können Sie angeben, welches Feld in der Importquelle während des Imports als Azure Cosmos DB-Dokument-ID-Eigenschaft verwendet werden soll. (Falls Dokumente diese Eigenschaft nicht enthalten, generiert das Importtool eine GUID als ID-Eigenschaftswert.)

Während des Imports steht eine Reihe von erweiterten Optionen zur Verfügung. Beim Importieren von Datentypen (beispielsweise aus SQL Server oder MongoDB) können Sie zunächst zwischen drei Importoptionen wählen:

 :::image type="content" source="./media/import-data/datetimeoptions.png" alt-text="Screenshot der Optionen für den Azure Cosmos DB-Import von Datum und Uhrzeit":::

* Zeichenfolge: Als Zeichenfolgenwert beibehalten
* Epoche: Als numerischen Epochenwert beibehalten
* Beide: Sowohl als Zeichenfolgen- als auch als numerischen Epochenwert beibehalten Diese Option erstellt ein Unterdokument. Beispiel: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Das Programm für den Import von sequenziellen Azure Cosmos DB-Datensätzen weist die folgenden erweiterten Optionen auf:

1. Number of Parallel Requests (Anzahl paralleler Anforderungen): Das Programm verwendet standardmäßig zwei parallele Anforderungen. Wenn die zu importierenden Dokumente klein sind, erwägen Sie, die Anzahl paralleler Anforderungen zu erhöhen. Ist die Anzahl zu groß, wird für den Importvorgang unter Umständen eine Ratenbegrenzung festgelegt.
2. Disable Automatic Id Generation (Automatische ID-Generierung deaktivieren): Wenn jedes zu importierende Dokument ein ID-Feld enthält, kann die Leistung durch Auswahl dieser Option verbessert werden. Dokumente ohne Feld für eine eindeutige ID werden nicht importiert.
3. Update Existing Documents (Vorhandene Dokumente aktualisieren): Das Tool ersetzt vorhandene Dokumente mit ID-Konflikten standardmäßig nicht. Bei Auswahl dieser Option können bereits vorhandene Dokumente mit übereinstimmenden IDs überschrieben werden. Diese Funktion ist hilfreich für geplante Datenmigrationen, die vorhandene Dokumente aktualisieren.
4. Number of Retries on Failure (Anzahl der Wiederholungsversuche bei Fehler) Die Anzahl von Verbindungsversuchen, die bei einem vorübergehenden Fehler (beispielsweise bei einer Unterbrechung der Netzwerkverbindung) ausgeführt werden, um eine Verbindung mit Azure Cosmos DB herzustellen.
5. Wiederholungsintervall: Gibt an, wie lange bei einem vorübergehenden Fehler (beispielsweise bei einer Unterbrechung der Netzwerkverbindung) gewartet werden soll, bevor erneut versucht wird, eine Verbindung mit Azure Cosmos DB herzustellen.
6. Verbindungsmodus: Gibt den Verbindungsmodus an, der mit Azure Cosmos DB verwendet werden soll. Verfügbare Optionen sind DirectTcp, DirectHttps und Gateway. Die Direktverbindungsmodi sind schneller, der Gatewaymodus ist besser für Firewalls geeignet, da nur Port 443 verwendet wird.

:::image type="content" source="./media/import-data/documentdbsequentialoptions.png" alt-text="Screenshot der erweiterten Optionen für den sequenziellen Azure Cosmos DB-Datensatzimport":::

> [!TIP]
> Standardmäßig verwendet das Importtool den Verbindungsmodus DirectTcp. Wenn Firewallprobleme auftreten, wechseln Sie zum Gatewaymodus, da dieser nur Port 443 erfordert.

## <a name="specify-an-indexing-policy"></a><a id="IndexingPolicy"></a>Angeben einer Indizierungsrichtlinie

Wenn Sie zulassen, dass das Migrationstool während des Imports Azure Cosmos DB-SQL-API-Sammlungen erstellt, können Sie die Indizierungsrichtlinie der Sammlungen angeben. Navigieren Sie in den erweiterten Optionen für den Azure Cosmos DB-Massenimport und den sequenziellen Azure Cosmos DB-Datensatzimport zum Abschnitt zur Indizierungsrichtlinie.

:::image type="content" source="./media/import-data/indexingpolicy1.png" alt-text="Screenshot der erweiterten Optionen für die Azure Cosmos DB-Indizierungsrichtlinie":::

Mit der erweiterten Option der Indizierungsrichtlinie können Sie eine Indizierungsrichtliniendatei auswählen, manuell eine Indizierungsdatei eingeben oder aus einer Reihe von Standardvorlagen wählen (durch Rechtsklick auf das Textfeld der Indizierungsrichtlinie).

Das Tool bietet folgende Richtlinienvorlagen:

* Standard. Diese Richtlinie empfiehlt sich, wenn Sie Gleichheitsabfragen für Zeichenfolgen ausführen. Sie funktioniert auch bei ORDER BY-, Bereichs- und Gleichheitsabfragen für Zahlen. Der Indexspeicheraufwand dieser Richtlinie ist geringer als bei „Bereich“.
* Bereich. Verwenden Sie diese Richtlinie, wenn Sie ORDER BY-, Bereichs- und Gleichheitsabfragen für Zahlen und Zeichenfolgen ausführen. Der Indexspeicheraufwand dieser Richtlinie ist höher als bei „Standard“ oder „Hash“.

:::image type="content" source="./media/import-data/indexingpolicy2.png" alt-text="Screenshot der erweiterten Optionen für die Azure Cosmos DB-Indizierungsrichtlinie mit Angabe von Zielinformationen":::

> [!NOTE]
> Wenn Sie keine Indizierungsrichtlinie angeben, wird die Standardrichtlinie verwendet. Weitere Informationen zu den Indizierungsrichtlinien finden Sie unter [Indizierungsrichtlinien für Azure Cosmos DB](index-policy.md).

## <a name="export-to-json-file"></a>Exportieren in JSON-Datei

Mit dem Azure Cosmos DB-JSON-Exportprogramm können Sie alle verfügbaren Quelloptionen in eine JSON-Datei exportieren, die ein Array von JSON-Dokumenten enthält. Das Tool verarbeitet den Export für Sie. Alternativ können Sie den resultierenden Migrationsbefehl anzeigen und den Befehl selbst ausführen. Die generierte JSON-Datei kann lokal oder in Azure Blob Storage gespeichert werden.

:::image type="content" source="./media/import-data/jsontarget.png" alt-text="Screenshot der Option zum Erstellen lokaler Azure Cosmos DB JSON-Dateiexporte":::

:::image type="content" source="./media/import-data/jsontarget2.png" alt-text="Screenshot der Option zum Erstellen von Azure Cosmos DB JSON Azure Blob Storage-Exporten":::

Optional können Sie die generierte JSON-Datei optimieren. Dadurch wird die Größe des resultierenden Dokuments reduziert und die Lesbarkeit der Inhalte verbessert.

* Standardmäßiger JSON-Export

  ```JSON
  [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]
  ```

* Optimierter JSON-Export

  ```JSON
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]
  ```

Hier ist ein Befehlszeilenbeispiel zum Exportieren der JSON-Datei in Azure-Blobspeicher angegeben:

```console
dt.exe /ErrorDetails:All /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB database_name>" /s.Collection:<CosmosDB collection_name>
/t:JsonFile /t.File:"blobs://<Storage account key>@<Storage account name>.blob.core.windows.net:443/<Container_name>/<Blob_name>"
/t.Overwrite
```

## <a name="advanced-configuration"></a>Erweiterte Konfiguration

Geben Sie auf dem Bildschirm "Erweiterte Konfiguration" den Speicherort der Protokolldatei an, in die Fehler geschrieben werden sollen. Für diese Seite gelten folgende Regeln:

1. Wenn kein Dateiname angegeben ist, werden alle Fehler auf der Ergebnisseite zurückgegeben.
2. Bei Angabe eines Dateinamens ohne Verzeichnis wird die Datei im aktuellen Umgebungsverzeichnis erstellt (oder überschrieben).
3. Wenn Sie eine vorhandene Datei auswählen, wird die Datei überschrieben. Eine Option zum Anfügen ist nicht verfügbar.
4. Wählen Sie dann, ob alle, nur die kritischen oder gar keine Fehlermeldungen protokolliert werden sollen. Entscheiden Sie zum Schluss, wie häufig der Fortschritt in der Bildschirmmeldung zur Übertragung aktualisiert werden soll.

   :::image type="content" source="./media/import-data/AdvancedConfiguration.png" alt-text="Screenshot des Bildschirms für die erweiterte Konfiguration":::

## <a name="confirm-import-settings-and-view-command-line"></a>Bestätigen der Importeinstellungen und Anzeigen der Befehlszeile

1. Nachdem Sie die Quell- und Zielinformationen sowie die erweiterte Konfiguration angegeben haben, überprüfen Sie die Migrationszusammenfassung, und zeigen Sie ggf. den resultierenden Migrationsbefehl an, oder kopieren Sie den Befehl. (Das Kopieren des Befehls ist hilfreich, wenn Sie Importvorgänge automatisieren möchten.)

    :::image type="content" source="./media/import-data/summary.png" alt-text="Screenshot des Übersichtsbildschirms":::

    :::image type="content" source="./media/import-data/summarycommand.png" alt-text="Screenshot des Übersichtsbildschirms mit Befehlszeilenvorschau":::

2. Wenn Sie die Quell- und Zieloptionen zu Ihrer Zufriedenheit eingerichtet haben, klicken Sie auf **Importieren**. Während des Importvorgangs werden die verstrichene Zeit, die Anzahl von Übertragungen sowie Fehlerinformationen (sofern Sie in der erweiterten Konfiguration keinen Dateinamen angegeben haben) aktualisiert. Nach Abschluss des Vorgangs können Sie die Ergebnisse exportieren (um beispielsweise Importfehler zu behandeln).

    :::image type="content" source="./media/import-data/viewresults.png" alt-text="Screenshot der Option zum Erstellen von Azure Cosmos DB JSON-Exporten":::

3. Sie können auch einen neuen Import starten, indem Sie entweder alle Werte zurücksetzen oder die vorhandenen Einstellungen beibehalten. (Sie können beispielsweise die Informationen zur Verbindungszeichenfolge, die Auswahl von Quelle und Ziel usw. beibehalten.)

    :::image type="content" source="./media/import-data/newimport.png" alt-text="Screenshot der Option zum Erstellen von Azure Cosmos DB JSON-Exporten mit dem Bestätigungsdialogfeld für neuen Import":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die folgenden Aufgaben ausgeführt:

> [!div class="checklist"]
> * Installieren des Datenmigrationstools
> * Importieren von Daten aus verschiedenen Datenquellen
> * Exportieren aus Azure Cosmos DB zu JSON

Sie können nun mit dem nächsten Tutorial fortfahren und sich darüber informieren, wie Sie Daten mithilfe von Azure Cosmos DB abfragen können.

> [!div class="nextstepaction"]
>[Abfragen von Daten](../cosmos-db/tutorial-query-sql-api.md)
