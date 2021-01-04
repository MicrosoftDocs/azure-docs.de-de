---
title: Verwenden von Azure Table Storage und der Azure Cosmos DB-Tabellen-API mit C++
description: Speichern Sie mit Azure Table Storage oder der Azure Cosmos DB-Tabellen-API über C++ strukturierte Daten in der Cloud.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: sakash279
ms.author: akshanka
ms.openlocfilehash: 79b58b76954cf15289e85dbf763b7a399897635d
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489878"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Verwenden von Azure Table Storage und der Azure Cosmos DB-Tabellen-API mit C++
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

In diesem Leitfaden werden häufige Szenarien veranschaulicht, indem der Azure-Tabellenspeicherdienst oder die Azure Cosmos DB-Tabellen-API verwendet werden. Die Beispiele sind in C++ geschrieben und greifen auf die [Azure-Speicherclientbibliothek für C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)zurück. In diesem Artikel werden die folgenden Szenarien beschrieben:

* Erstellen und Löschen einer Tabelle
* Verwenden von Tabellenentitäten

> [!NOTE]
> Diese Anleitung gilt für die Azure Storage-Clientbibliothek für C++ in der Version 1.0.0 und höher. Die empfohlene Version ist Storage-Clientbibliothek 2.2.0, die über [NuGet](https://www.nuget.org/packages/wastorage) oder [GitHub](https://github.com/Azure/azure-storage-cpp/) verfügbar ist.
>

## <a name="create-accounts"></a>Erstellen von Konten

### <a name="create-an-azure-service-account"></a>Erstellen eines Azure-Dienstkontos

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Erstellen eines Kontos für die Azure Cosmos DB-Tabellen-API

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Erstellen einer C++-Anwendung

In diesem Leitfaden verwenden Sie Speicherfunktionen einer C++-Anwendung. Installieren Sie hierfür die Azure Storage-Clientbibliothek für C++.

Verwenden Sie zum Installieren der Azure-Speicherclientbibliothek für C++ die folgenden Methoden:

* **Linux:** Befolgen Sie die Anweisungen auf der Seite [README-Datei zur Azure Storage-Clientbibliothek für C++: Erste Schritte unter Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux).
* **Windows:** Verwenden Sie unter Windows [vcpkg](https://github.com/microsoft/vcpkg) als Abhängigkeits-Manager. Befolgen Sie zum Initialisieren von vcpkg die Anweisungen im [Schnellstart](https://github.com/microsoft/vcpkg#quick-start). Verwenden Sie dann folgenden Befehl, um die Bibliothek zu installieren:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Eine Anleitung zum Erstellen des Quellcodes und Exportieren in NuGet finden Sie in der [README-Datei](https://github.com/Azure/azure-storage-cpp#download--install).

### <a name="configure-access-to-the-table-client-library"></a>Konfigurieren des Zugriffs auf die Table-Clientbibliothek

Fügen Sie für die Verwendung der Azure Storage-APIs zum Zugreifen auf Tabellen oben in der C++-Datei die folgenden `include`-Anweisungen hinzu:

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Ein Azure Storage-Client oder Cosmos DB-Client verwendet eine Verbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen für den Zugriff auf Datenverwaltungsdienste. Beim Ausführen einer Clientanwendung müssen Sie die Storage-Verbindungszeichenfolge oder die Azure Cosmos DB-Verbindungszeichenfolge im richtigen Format angeben.

### <a name="set-up-an-azure-storage-connection-string"></a>Einrichten einer Azure-Speicherverbindungszeichenfolge

Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Azure Storage-Verbindungszeichenfolge deklarieren:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

Verwenden Sie für `<your_storage_account>` den Namen Ihres Speicherkontos. Verwenden Sie für „<your_storage_account_key>“ den Zugriffsschlüssel für das Speicherkonto, das im [Azure-Portal](https://portal.azure.com) angegeben ist. Weitere Informationen zu Storage-Konten und Zugriffsschlüsseln finden Sie unter [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md).

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Einrichten einer Azure Cosmos DB-Verbindungszeichenfolge

Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Azure Cosmos DB-Verbindungszeichenfolge deklarieren:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

Verwenden Sie für `<your_cosmos_db_account>` den Namen Ihres Azure Cosmos DB-Kontos. Geben Sie für `<your_cosmos_db_account_key>` Ihren Primärschlüssel ein. Geben Sie für `<your_cosmos_db_endpoint>` den Endpunkt ein, der im [Azure-Portal](https://portal.azure.com) angegeben ist.

Zum Testen der Anwendung auf Ihrem lokalen Windows-basierten Computer können Sie den Azure-Speicheremulator verwenden, der mit dem [Azure SDK](https://azure.microsoft.com/downloads/) installiert wird. Der Speicheremulator ist ein Dienstprogramm, das die in Azure verfügbaren Blob-, Warteschlangen- und Tabellenspeicherdienste auf dem lokalen Entwicklungscomputer simuliert. Im folgenden Beispiel ist dargestellt, wie Sie ein statisches Feld zur Übergabe der Verbindungszeichenfolge an den lokalen Speicheremulator deklarieren:  

```cpp
// Define the connection string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Wählen Sie auf Ihrem Windows-Desktop die Schaltfläche **Start** oder die Windows-Taste aus, um den Azure-Speicheremulator zu starten. Geben Sie *Microsoft Azure-Speicheremulator* ein, und führen Sie ihn aus. Weitere Informationen finden Sie unter [Verwenden des Azure-Speicheremulators für Entwicklung und Tests](../storage/common/storage-use-emulator.md).

### <a name="retrieve-your-connection-string"></a>Abrufen der Verbindungszeichenfolge

Sie können Ihre Speicherkontoinformationen mit der `cloud_storage_account`-Klasse angeben. Verwenden Sie zum Abrufen von Speicherkontoinformationen aus der Speicherverbindungszeichenfolge die `parse`-Methode.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Rufen Sie als Nächstes einen Verweis auf eine `cloud_table_client`-Klasse ab. Mit dieser Klasse können Sie Referenzobjekte für Tabellen und Entitäten abrufen, die im Tabellenspeicherdienst gespeichert sind. Mit dem folgenden Code wird ein `cloud_table_client`-Objekt erstellt, indem das zuvor abgerufene Speicherkontoobjekt verwendet wird:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>Erstellen und Hinzufügen von Entitäten zu einer Tabelle

### <a name="create-a-table"></a>Erstellen einer Tabelle

Mit einem `cloud_table_client`-Objekt können Sie Referenzobjekte für Tabellen und Entitäten abrufen. Mit dem folgenden Code wird ein `cloud_table_client`-Objekt erstellt und zum Erstellen einer neuen Tabelle verwendet.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();  
```

### <a name="add-an-entity-to-a-table"></a>Hinzufügen einer Entität zu einer Tabelle

Erstellen Sie zum Hinzufügen einer Entität zu einer Tabelle ein neues `table_entity`-Objekt, und übergeben Sie es an `table_operation::insert_entity`. Im folgenden Code wird der Vorname des Kunden als Zeilenschlüssel und der Nachname als Partitionsschlüssel verwendet. In Kombination miteinander wird mit dem Partitions- und Zeilenschlüssel eine Entität in der Tabelle eindeutig identifiziert. Entitäten mit dem gleichen Partitionsschlüssel können schneller abgefragt werden als Entitäten mit verschiedenen Schlüsseln. Die Nutzung von unterschiedlichen Partitionsschlüsseln ermöglicht eine bessere Skalierbarkeit paralleler Vorgänge. Weitere Informationen finden Sie unter [Checkliste zu Leistung und Skalierbarkeit von Microsoft Azure Storage](../storage/blobs/storage-performance-checklist.md).

Mit dem folgenden Code wird eine neue Instanz von `table_entity` mit einigen zu speichernden Kundendaten erstellt. Als Nächstes wird im Code `table_operation::insert_entity` aufgerufen, um ein `table_operation`-Objekt zum Einfügen einer Entität in eine Tabelle zu erstellen und die neue Tabellenentität zuzuordnen. Abschließend wird im Code die `execute`-Methode für das `cloud_table`-Objekt aufgerufen. Mit dem neuen `table_operation`-Element wird eine Anforderung zum Einfügen der neuen Kundenentität in die Tabelle `people` an den Tabellenspeicherdienst gesendet.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();

// Create a new customer entity.
azure::storage::table_entity customer1(U("Harp"), U("Walter"));

azure::storage::table_entity::properties_type& properties = customer1.properties();
properties.reserve(2);
properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

// Create the table operation that inserts the customer entity.
azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

// Execute the insert operation.
azure::storage::table_result insert_result = table.execute(insert_operation);
```

### <a name="insert-a-batch-of-entities"></a>Einfügen eines Entitätsbatchs

Sie können einen Entitätsbatch in einem Schreibvorgang in den Tabellenspeicherdienst einfügen. Mit dem folgenden Code wird ein `table_batch_operation`-Objekt erstellt, und anschließend werden dafür drei Einfügevorgänge hinzugefügt. Für jeden Einfügevorgang wird ein neues Entitätsobjekt erstellt, dessen Werte werden festgelegt, und dann wird die `insert`-Methode für das `table_batch_operation`-Objekt aufgerufen und die Entität einem neuen Einfügevorgang zugeordnet. Anschließend ruft der Code `cloud_table.execute` auf, um den Vorgang auszuführen.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define a batch operation.
azure::storage::table_batch_operation batch_operation;

// Create a customer entity and add it to the table.
azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

azure::storage::table_entity::properties_type& properties1 = customer1.properties();
properties1.reserve(2);
properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

// Create another customer entity and add it to the table.
azure::storage::table_entity customer2(U("Smith"), U("Ben"));

azure::storage::table_entity::properties_type& properties2 = customer2.properties();
properties2.reserve(2);
properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

// Create a third customer entity to add to the table.
azure::storage::table_entity customer3(U("Smith"), U("Denise"));

azure::storage::table_entity::properties_type& properties3 = customer3.properties();
properties3.reserve(2);
properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

// Add customer entities to the batch insert operation.
batch_operation.insert_or_replace_entity(customer1);
batch_operation.insert_or_replace_entity(customer2);
batch_operation.insert_or_replace_entity(customer3);

// Execute the batch operation.
std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);
```

Beachten Sie im Zusammenhang mit Batchvorgängen Folgendes:

* Sie können in einem Batch eine beliebige Kombination von bis zu 100 Vorgängen vom Typ `insert`, `delete`, `merge`, `replace`, `insert-or-merge` und `insert-or-replace` ausführen.  
* Ein Batchvorgang kann einen Abfragevorgang enthalten, sofern es der einzige Vorgang im Batch ist.  
* Alle Entitäten in einem Batchvorgang müssen über denselben Partitionsschlüssel verfügen.  
* Ein Batchvorgang ist auf eine Datennutzlast von 4 MB beschränkt.  

## <a name="query-and-modify-entities"></a>Abfragen und Ändern von Entitäten

### <a name="retrieve-all-entities-in-a-partition"></a>Abrufen aller Entitäten einer Partition

Verwenden Sie ein `table_query`-Objekt, um für eine Tabelle alle Entitäten einer Partition abzufragen. Im folgenden Codebeispiel wird ein Filter für Entitäten erstellt, wobei `Smith` der Partitionsschlüssel ist. In diesem Beispiel werden die Felder der einzelnen Entitäten in den Abfrageergebnissen an die Konsole ausgegeben.  

> [!NOTE]
> Diese Methoden werden für C++ in Azure Cosmos DB derzeit nicht unterstützt.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Construct the query operation for all customer entities where PartitionKey="Smith".
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Print the fields for each customer.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

Die Abfrage in diesem Beispiel gibt alle Entitäten zurück, die den Filterkriterien entsprechen. Wenn Sie über umfangreiche Tabellen verfügen und daher die Tabellenentitäten häufig herunterladen müssen, wird empfohlen, die Daten stattdessen in Azure-Speicher-BLOBs zu speichern.

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>Abrufen eines Entitätsbereichs in einer Partition

Falls Sie nicht alle Entitäten einer Partition abfragen möchten, können Sie einen Bereich angeben. Kombinieren Sie den Partitionsschlüsselfilter mit einem Zeilenschlüsselfilter. Im folgenden Codebeispiel werden zwei Filter eingesetzt, um alle Entitäten in der Partition `Smith` abzurufen, deren Zeilenschlüssel (Vorname) mit einem Buchstaben vor dem Buchstaben `E` im Alphabet beginnen. Danach werden die Abfrageergebnisse gedruckt.  

> [!NOTE]
> Diese Methoden werden für C++ in Azure Cosmos DB derzeit nicht unterstützt.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table query.
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
    azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
    azure::storage::query_comparison_operator::equal, U("Smith")),
    azure::storage::query_logical_operator::op_and,
    azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Loop through the results, displaying information about the entity.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

### <a name="retrieve-a-single-entity"></a>Abrufen einer einzelnen Entität

Sie können eine Abfrage schreiben, um eine einzelne bestimmte Entität abzurufen. Im folgenden Code wird `table_operation::retrieve_entity` verwendet, um den Kunden `Jeff Smith` anzugeben. Bei dieser Methode wird nur eine Entität anstelle einer Sammlung zurückgegeben, und der zurückgegebene Wert befindet sich in `table_result`. Die Angabe beider Schlüssel, Partition und Zeile, in einer Abfrage ist die schnellste Möglichkeit, um eine einzelne Entität aus dem Tabellenspeicherdienst abzurufen.  

```cpp
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Output the entity.
azure::storage::table_entity entity = retrieve_result.entity();
const azure::storage::table_entity::properties_type& properties = entity.properties();

std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
    << U(", Property1: ") << properties.at(U("Email")).string_value()
    << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
```

### <a name="replace-an-entity"></a>Ersetzen einer Entität

Um eine Entität zu ersetzen, rufen Sie sie aus dem Tabellenspeicherdienst ab, ändern Sie das Entitätsobjekt, und speichern Sie die Änderungen dann im Tabellenspeicherdienst. Mit dem folgenden Code werden Telefonnummer und E-Mail-Adresse eines vorhandenen Kunden geändert. Anstatt `table_operation::insert_entity` aufzurufen, wird in diesem Code `table_operation::replace_entity` verwendet. Bei diesem Ansatz wird die Entität auf dem Server vollständig ersetzt, sofern sich die Entität auf dem Server seit dem letzten Abruf nicht geändert hat. Wenn sie sich geändert hat, ist der Vorgang nicht erfolgreich. Durch diesen Fehler wird für Ihre Anwendung verhindert, dass eine Änderung außer Kraft gesetzt wird, die zwischen Abruf und Update von einer anderen Komponente vorgenommen wurde. Die richtige Vorgehensweise in diesem Fall besteht darin, die Entität erneut abzurufen, die Änderungen vorzunehmen – falls diese noch gültig sind – und dann einen weiteren `table_operation::replace_entity`-Vorgang durchzuführen.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Replace an entity.
azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
properties_to_replace.reserve(2);

// Specify a new phone number.
properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

// Specify a new email address.
properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

// Create an operation to replace the entity.
azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

// Submit the operation to the Table service.
azure::storage::table_result replace_result = table.execute(replace_operation);
```

### <a name="insert-or-replace-an-entity"></a>Einfügen oder Ersetzen einer Entität

`table_operation::replace_entity`-Vorgänge sind nicht erfolgreich, wenn die Entität seit dem letzten Abruf vom Server geändert wurde. Darüber hinaus müssen Sie zuerst die Entität vom Server abrufen, damit der `table_operation::replace_entity`-Vorgang erfolgreich ist. In einigen Fällen wissen Sie nicht, ob die Entität auf dem Server vorhanden ist. Die darin derzeit gespeicherten Werte sind irrelevant, da alle durch Ihr Update außer Kraft gesetzt werden. Verwenden Sie einen `table_operation::insert_or_replace_entity`-Vorgang, um dies zu erreichen. Mit diesem Vorgang wird die Entität eingefügt, falls sie nicht vorhanden ist. Wenn sie vorhanden ist, wird sie bei diesem Vorgang ersetzt. Im folgenden Codebeispiel wird die Kundenentität für `Jeff Smith` abgerufen, anschließend aber mit `table_operation::insert_or_replace_entity` erneut auf dem Server gespeichert. Alle Änderungen, die zwischen dem Abruf- und Aktualisierungsvorgang an der Entität vorgenommen wurden, werden überschrieben.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert or replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert or replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

### <a name="query-a-subset-of-entity-properties"></a>Abfragen einer Teilmenge von Entitätseigenschaften

Mit einer Abfrage einer Tabelle können nur einige wenige Eigenschaften einer Entität aufgerufen werden. Für die Abfrage im folgenden Code wird die `table_query::set_select_columns`-Methode verwendet, um nur die E-Mail-Adressen von Entitäten in der Tabelle zurückzugeben.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define the query, and select only the Email property.
azure::storage::table_query query;
std::vector<utility::string_t> columns;

columns.push_back(U("Email"));
query.set_select_columns(columns);

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Display the results.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

    const azure::storage::table_entity::properties_type& properties = it->properties();
    for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
    {
        std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
    }

    std::wcout << std::endl;
}
```

> [!NOTE]
> Das Abfragen einiger Eigenschaften einer Entität ist effizienter als das Abrufen aller Eigenschaften.
>

## <a name="delete-content"></a>Löschen des Inhalts

### <a name="delete-an-entity"></a>Löschen einer Entität

Sie können eine Entität löschen, nachdem Sie sie abgerufen haben. Rufen Sie nach dem Abrufen einer Entität `table_operation::delete_entity` mit der zu löschenden Entität ab. Rufen Sie anschließend die `cloud_table.execute`-Methode auf. Mit dem folgenden Code wird eine Entität mit dem Partitionsschlüssel `Smith` und dem Zeilenschlüssel `Jeff` abgerufen und gelöscht.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Create an operation to delete the entity.
azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

// Submit the delete operation to the Table service.
azure::storage::table_result delete_result = table.execute(delete_operation);  
```

### <a name="delete-a-table"></a>Löschen einer Tabelle

Schließlich wird mit dem folgenden Codebeispiel eine Tabelle aus einem Speicherkonto gelöscht. Eine gelöschte Tabelle kann während eines bestimmten Zeitraums nach dem Löschvorgang nicht neu erstellt werden.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Delete the table if it exists
if (table.delete_table_if_exists())
{
    std::cout << "Table deleted!";
}
else
{
    std::cout << "Table didn't exist";
}
```

## <a name="troubleshooting"></a>Problembehandlung

Für Visual Studio Community Edition: Falls für Ihr Projekt aufgrund der Includedateien *storage_account.h* und *table.h* Buildfehler auftreten, sollten Sie den Compilerschalter **/permissive-** entfernen:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Eigenschaften**.
1. Erweitern Sie im Dialogfeld **Eigenschaftenseiten** die Option **Konfigurationseigenschaften** und dann **C/C++** , und wählen Sie die Option **Sprache**.
1. Legen Sie **Konformitätsmodus** auf **Nein** fest.

## <a name="next-steps"></a>Nächste Schritte

Beim [Microsoft Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) handelt es sich um eine kostenlose eigenständige App von Microsoft, über die Sie ganz einfach visuell mit Azure Storage-Daten arbeiten können – unter Windows, MacOS und Linux.

Verwenden Sie diese Links, um weitere Informationen zu Azure Storage und der Tabellen-API in Azure Cosmos DB zu erhalten:

* [Einführung in die Tabellen-API von Azure Cosmos DB](table-introduction.md)
* [Auflisten von Azure Storage-Ressourcen in C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Referenz zur Speicherclientbibliothek für C++](https://azure.github.io/azure-storage-cpp)
* [Azure Storage-Dokumentation](https://azure.microsoft.com/documentation/services/storage/)
