---
title: Verwenden von Azure Table Storage oder der Azure Cosmos DB-Tabellen-API über Java
description: Speichern Sie mit Azure Table Storage oder der Azure Cosmos DB-Tabellen-API über Java strukturierte Daten in der Cloud.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: Java
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.custom: devx-track-java
ms.openlocfilehash: 1f3f5a35beeac6c683aeb6db16a417b897755666
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079766"
---
# <a name="how-to-use-azure-table-storage-or-azure-cosmos-db-table-api-from-java"></a>Verwenden von Azure Table Storage oder der Azure Cosmos DB-Tabellen-API aus Java
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

In diesem Artikel erfahren Sie, wie Sie Tabellen erstellen, Daten speichern und CRUD-Vorgänge für die Daten ausführen. Wählen Sie entweder den Azure Table-Dienst oder die Azure Cosmos DB-Tabellen-API aus. Die Beispiele wurden in Java geschrieben und verwenden das [Azure Storage-SDK für Java][Azure Storage SDK for Java]. Die erläuterten Szenarien umfassten das **Erstellen** , **Auflisten** und **Löschen** von Tabellen sowie das **Einfügen** , **Abfragen** , **Ändern** und **Löschen** von Entitäten in einer Tabelle. Weitere Informationen zu Tabellen finden Sie im Abschnitt [Nächste Schritte](#next-steps) .

> [!NOTE]
> Ein SDK steht für Entwickler zur Verfügung, die Azure Storage auf Android-Geräten verwenden. Weitere Informationen finden Sie unter [Azure Storage-SDK für Android][Azure Storage SDK for Android].
>

## <a name="create-an-azure-service-account"></a>Erstellen eines Azure-Dienstkontos

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Erstellen eines Azure-Speicherkontos**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Erstellen eines Azure Cosmos DB-Kontos**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-java-application"></a>Erstellen einer Java-Anwendung

In diesem Leitfaden verwenden Sie Speicherfunktionen, die lokal innerhalb einer Java-Anwendung oder in Code innerhalb einer Webrolle oder Workerrolle in Azure ausgeführt werden können.

Um die Beispiele in diesem Artikel zu verwenden, installieren Sie das Java Development Kit (JDK), und erstellen Sie dann ein Azure-Speicherkonto oder ein Azure Cosmos DB-Konto in Ihrem Azure-Abonnement. Sobald Sie dies erledigt haben, stellen Sie sicher, dass Ihr Entwicklungssystem die minimalen Anforderungen und Abhängigkeiten erfüllt, die im Repository [Azure Storage-SDK für Java][Azure Storage SDK for Java] auf GitHub aufgeführt sind. Wenn Ihr System diese Anforderungen erfüllt, können Sie die Anweisungen für das Herunterladen und Installieren der Azure Storage-Bibliotheken für Java auf Ihr System von diesem Repository befolgen. Nach Abschluss dieser Aufgaben können Sie eine Java-Anwendung erstellen, die die Beispiele in diesem Artikel verwendet.

## <a name="configure-your-application-to-access-table-storage"></a>Konfigurieren Ihrer Anwendung für den Zugriff auf Tabellenspeicher

Fügen Sie folgende import-Anweisungen am Anfang der Java-Datei dort ein, wo Azure Storage-APIs oder die Azure Cosmos DB-Tabellen-API auf Tabellen zugreifen sollen:

```java
// Include the following imports to use table APIs
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.table.*;
import com.microsoft.azure.storage.table.TableQuery.*;
```

## <a name="add-your-connection-string"></a>Hinzufügen der Verbindungszeichenfolge

Sie können entweder eine Verbindung mit dem Azure-Speicherkonto oder dem Konto für die Azure Cosmos DB-Tabellen-API herstellen. Rufen Sie die Verbindungszeichenfolge basierend auf dem jeweils verwendeten Kontotyp ab.

### <a name="add-an-azure-storage-connection-string"></a>Hinzufügen einer Azure-Speicherverbindungszeichenfolge

Ein Azure-Speicherclient verwendet eine Speicherverbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen für den Zugriff auf Datenverwaltungsdienste. Bei der Ausführung in einer Clientanwendung muss die Speicherverbindungszeichenfolge in dem unten gezeigten Format angegeben werden. Dabei müssen der Name Ihres Speicherkontos und der primäre Zugriffsschlüssel für das im [Azure-Portal](https://portal.azure.com) aufgeführte Speicherkonto als Werte für **AccountName** und **AccountKey** eingegeben werden. 

Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Verbindungszeichenfolge deklarieren:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

### <a name="add-an-azure-cosmos-db-table-api-connection-string"></a>Hinzufügen einer Verbindungszeichenfolge für die Azure Cosmos DB-Tabellen-API

Ein Azure Cosmos DB-Konto verwendet eine Verbindungszeichenfolge, um den Endpunkt für die Tabelle und Ihre Anmeldeinformationen zu speichern. Bei der Ausführung in einer Clientanwendung muss die Azure Cosmos DB-Verbindungszeichenfolge in dem unten gezeigten Format angegeben werden. Dabei müssen der Name Ihres Azure Cosmos DB-Kontos und der primäre Zugriffsschlüssel für das im [Azure-Portal](https://portal.azure.com) aufgeführte Konto als Werte für **AccountName** und **AccountKey** eingegeben werden. 

Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Azure Cosmos DB-Verbindungszeichenfolge deklarieren:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" + 
    "AccountName=your_cosmosdb_account;" + 
    "AccountKey=your_account_key;" + 
    "TableEndpoint=https://your_endpoint;" ;
```

In einer Anwendung, die in einer Azure-Rolle ausgeführt wird, können Sie diese Zeichenfolge in der Dienstkonfigurationsdatei *ServiceConfiguration.cscfg* speichern. Der Zugriff darauf kann dann durch Aufruf der Methode **RoleEnvironment.getConfigurationSettings** erfolgen. Dieses Beispiel zeigt, wie die Verbindungszeichenfolge von einem **Setting** -Element mit der Bezeichnung *StorageConnectionString* in der Dienstkonfigurationsdatei abgerufen wird:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Sie können die Verbindungszeichenfolge auch in der Datei „config.properties“ Ihres Projekts speichern:

```java
StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=your_account;AccountKey=your_account_key;TableEndpoint=https://your_table_endpoint/
```

In den folgenden Beispielen wird davon ausgegangen, dass Sie eine dieser Methoden verwendet haben, um die Speicherverbindungszeichenfolge abzurufen.

## <a name="create-a-table"></a>Erstellen einer Tabelle

Mit einem `CloudTableClient`-Objekt können Sie Referenzobjekte für Tabellen und Entitäten abrufen. Der folgende Code erstellt ein `CloudTableClient`-Objekt und verwendet es zum Erstellen eines neuen `CloudTable`-Objekts, das eine Tabelle mit der Bezeichnung „people“ darstellt. 

> [!NOTE]
> `CloudStorageAccount`-Objekte können auch auf andere Weise erstellt werden. Weitere Informationen finden Sie in der [Referenz für Azure Storage-Client-SDKs] unter `CloudStorageAccount`.
>

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create the table if it doesn't exist.
    String tableName = "people";
    CloudTable cloudTable = tableClient.getTableReference(tableName);
    cloudTable.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-tables"></a>Auflisten der Tabellen

Rufen Sie zum Abrufen einer Liste von Tabellen die Methode **CloudTableClient.listTables()** auf, um eine wiederholbare Liste der Tabellennamen abzurufen.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Loop through the collection of table names.
    for (String table : tableClient.listTables())
    {
        // Output each table name.
        System.out.println(table);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="add-an-entity-to-a-table"></a>Hinzufügen einer Entität zu einer Tabelle

Entitäten werden mithilfe einer benutzerdefinierten Klasse, die `TableEntity` implementiert, Java-Objekten zugeordnet. Der Einfachheit halber stellt die `TableServiceEntity`-Klasse `TableEntity` bereit und ordnet den für die Eigenschaften genannten Getter- und Setter-Methoden durch Reflexion Eigenschaften zu. Erstellen Sie zunächst eine Klasse, mit der die Eigenschaften der Entität definiert werden, um eine Entität zu einer Tabelle hinzuzufügen. Mit dem folgenden Code wird eine Entitätsklasse definiert, die den Vornamen des Kunden als Zeilenschlüssel und den Nachnamen als Partitionsschlüssel verwendet. In Kombination miteinander wird mit dem Partitions- und Zeilenschlüssel eine Entität in der Tabelle eindeutig identifiziert. Entitäten mit demselben Partitionsschlüssel können schneller abgefragt werden als Entitäten mit verschiedenen Schlüsseln.

```java
public class CustomerEntity extends TableServiceEntity {
    public CustomerEntity(String lastName, String firstName) {
        this.partitionKey = lastName;
        this.rowKey = firstName;
    }

    public CustomerEntity() { }

    String email;
    String phoneNumber;

    public String getEmail() {
        return this.email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return this.phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }
}
```

Tabellenvorgänge, die Entitäten umfassen, benötigen ein `TableOperation`-Objekt. Dieses Objekt definiert den für eine Entität vorgesehenen Vorgang, der mit einem `CloudTable`-Objekt ausgeführt werden kann. Der folgende Code erstellt eine neue Instanz der `CustomerEntity`-Klasse mit einigen zu speichernden Kundendaten. Als Nächstes wird im Code `TableOperation`.insertOrReplace** aufgerufen, um ein `TableOperation`-Objekt zum Einfügen einer Entität in eine Tabelle zu erstellen, und das neue `CustomerEntity`-Objekt zugeordnet. Abschließend ruft der Code die `execute`-Methode für das `CloudTable`-Objekt auf und gibt dabei die Tabelle „people“ und das neue `TableOperation`-Objekt an, das dann eine Anforderung an den Speicherdienst sendet, die neue Kundenentität in die Tabelle „people“ einzufügen oder die Entität zu ersetzen, wenn sie bereits vorhanden ist.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer1);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-a-batch-of-entities"></a>Einfügen eines Entitätsbatchs

Sie können einen Entitätsbatch in einem Schreibvorgang in den Tabellendienst einfügen. Mit dem folgenden Code wird ein `TableBatchOperation`-Objekt erstellt, und anschließend werden dafür drei Einfügevorgänge hinzugefügt. Für jeden Einfügevorgang wird ein neues Entitätsobjekt erstellt, dessen Werte werden festgelegt, und dann wird die `insert`-Methode für das `TableBatchOperation`-Objekt aufgerufen und die Entität einem neuen Einfügevorgang zugeordnet. Anschließend ruft der Code die `execute`-Methode für das `CloudTable`-Objekt auf und gibt dabei die Tabelle „people“ und das `TableBatchOperation`-Objekt an, das dann den Batch mit Tabellenvorgängen in einer einzelnen Anforderung an den Speicherdienst sendet.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Define a batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a customer entity to add to the table.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insertOrReplace(customer);

    // Create another customer entity to add to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insertOrReplace(customer2);

    // Create a third customer entity to add to the table.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insertOrReplace(customer3);

    // Execute the batch of operations on the "people" table.
    cloudTable.execute(batchOperation);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Beachten Sie im Zusammenhang mit Batchvorgängen Folgendes:

* Bis zu 100 Einfüge-, Lösch-, Zusammenführungs-, Ersetzungs-, Einfüge- oder Zusammenführungs- und Einfüge- oder Ersetzungsvorgänge können in beliebiger Kombination in einem einzelnen Batch ausgeführt werden.
* Ein Batchvorgang kann einen Abfragevorgang enthalten, allerdings nur als einzigen Vorgang in diesem Batch.
* Alle Entitäten in einem Batchvorgang müssen über denselben Partitionsschlüssel verfügen.
* Ein Batchvorgang ist auf eine Datennutzlast von 4 MB beschränkt.

## <a name="retrieve-all-entities-in-a-partition"></a>Abrufen aller Entitäten einer Partition

Zum Abrufen einer Tabelle für Entitäten in einer Partition können Sie ein `TableQuery`-Objekt verwenden. Rufen Sie `TableQuery.from` auf, um eine Abfrage für eine bestimmten Tabelle zu erstellen, die einen bestimmen Ergebnistyp zurückgibt. Im folgenden Code wird ein Filter für Entitäten erstellt, wobei Smith der Partitionsschlüssel ist. `TableQuery.generateFilterCondition` ist eine Hilfsmethode zum Erstellen von Filtern für Abfragen. Rufen Sie `where` für den von der `TableQuery.from`-Methode zurückgegebenen Verweis auf, um den Filter auf die Abfrage anzuwenden. Wenn die Abfrage mit einem Aufruf von `execute` für das `CloudTable`-Objekt ausgeführt wird, wird ein `Iterator` mit dem angegebenen Ergebnistyp `CustomerEntity` zurückgegeben. Anschließend können Sie den zurückgegebenen `Iterator` in einer „ForEach“-Schleife zum Einbinden der Ergebnisse verwenden. In diesem Code werden die Felder der einzelnen Entitäten in den Abfrageergebnissen an die Konsole ausgegeben.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Specify a partition query, using "Smith" as the partition key filter.
    TableQuery<CustomerEntity> partitionQuery =
        TableQuery.from(CustomerEntity.class)
        .where(partitionFilter);

    // Loop through the results, displaying information about the entity.
    for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Abrufen eines Entitätsbereichs in einer Partition

Wenn Sie nicht alle Entitäten in einer Partition abrufen möchten, können Sie durch Verwendung von Vergleichsoperatoren einen Bereich angeben. Im folgenden Code werden zwei Filter kombiniert, um alle Entitäten in der Partition "Smith" abzurufen, deren Zeilenschlüssel (Vorname) mit einem Buchstaben vor dem Buchstaben "E" im Alphabet beginnen. Danach werden die Abfrageergebnisse ausgegeben. Wenn Sie die im Bereich Batch-Einfügungen dieser Anleitung erstellten Entitäten verwenden, gibt diese Abfrage nur zwei Entitäten zurück (Ben und Denise Smith); Jeff Smith wird nicht zurückgegeben.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Create a filter condition where the row key is less than the letter "E".
    String rowFilter = TableQuery.generateFilterCondition(
        ROW_KEY,
        QueryComparisons.LESS_THAN,
        "E");

    // Combine the two conditions into a filter expression.
    String combinedFilter = TableQuery.combineFilters(partitionFilter,
        Operators.AND, rowFilter);

    // Specify a range query, using "Smith" as the partition key,
    // with the row key being up to the letter "E".
    TableQuery<CustomerEntity> rangeQuery =
        TableQuery.from(CustomerEntity.class)
        .where(combinedFilter);

    // Loop through the results, displaying information about the entity
    for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-single-entity"></a>Abrufen einer einzelnen Entität

Sie können eine Abfrage schreiben, um eine einzelne bestimmte Entität abzurufen. Im folgenden Code wird der Kunde „Jeff Smith“ nicht durch Erstellen einer `Table Query` und Verwendung von Filtern übergeben, sondern durch Aufruf von `TableOperation.retrieve` mit Partitions- und Zeilenschlüsselparametern. Bei der Ausführung übergibt dieser Abfragevorgang nicht eine ganze Sammlung von Entitäten, sondern nur eine einzelne Entität. Die `getResultAsType`-Methode wandelt das Ergebnis in den Typ des Zuweisungsziels um, ein `CustomerEntity`-Objekt. Wenn dieser Typ mit dem für die Abfrage spezifizierten Typ nicht kompatibel ist, wird eine Ausnahme ausgelöst. Wenn keine Entität mit exakter Partitions- und Zeilenübereinstimmung existiert, wird ein Nullwert zurückgegeben. Die Angabe beider Schlüssel, Partition und Zeile, in einer Abfrage ist die schnellste Möglichkeit, um eine einzelne Entität aus dem Tabellenspeicherdienst abzurufen.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Output the entity.
    if (specificEntity != null)
    {
        System.out.println(specificEntity.getPartitionKey() +
            " " + specificEntity.getRowKey() +
            "\t" + specificEntity.getEmail() +
            "\t" + specificEntity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="modify-an-entity"></a>Ändern einer Entität

Um eine Entität zu ändern, rufen Sie sie aus dem Tabellendienst ab, ändern Sie das Entitätsobjekt, und speichern Sie die Änderungen dann mit einem Ersetzungs- oder Zusammenführungsvorgang im Tabellendienst. Mit dem folgenden Code wird die Telefonnummer eines vorhandenen Kunden geändert. In diesem Fall wird nicht wie beim Einfügen **TableOperation.insert** , sondern **TableOperation.replace** aufgerufen. Die Methode **CloudTable.execute** ruft den Tabellendienst auf, und die Entität wird ersetzt, sofern sie nicht nach dem Aufruf durch diese Anwendung in der Zwischenzeit von einer anderen Anwendung geändert wurde. In diesem Fall wird eine Ausnahme ausgelöst, und die Entität muss erneut aufgerufen, geändert und gespeichert werden. Dieses auf dem "Optimistic Concurrency"-Verfahren (optimistisches Locking) basierende Muster für Wiederholungsversuche ist in verteilten Speichersystemen häufig anzutreffen.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Specify a new phone number.
    specificEntity.setPhoneNumber("425-555-0105");

    // Create an operation to replace the entity.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Submit the operation to the table service.
    cloudTable.execute(replaceEntity);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="query-a-subset-of-entity-properties"></a>Abfragen einer Teilmenge von Entitätseigenschaften

Mit einer Abfrage einer Tabelle können nur einige wenige Eigenschaften einer Entität aufgerufen werden. Mit dieser Technik, der sogenannten Projektion, wird die Bandbreite reduziert und die Abfrageleistung gesteigert, vor allem bei großen Entitäten. Für die Abfrage im folgenden Code wird die `select`-Methode verwendet, um nur die E-Mail-Adressen von Entitäten in der Tabelle zurückzugeben. Die Ergebnisse werden mit Unterstützung eines `Entity Resolver`, der die Typumwandlung der vom Server übergebenen Entitäten übernimmt, in eine Sammlung von `String`-Objekten projiziert. Weitere Informationen zur Projektion finden Sie in [Azure Tables: Introducing Upsert and Query Projection (Azure-Tabellen: Einführung in Upsert- und Abfrageprojektion)][Azure Tables: Introducing Upsert and Query Projection]. Die Projektion wird auf dem lokalen Speicheremulator nicht unterstützt. Deshalb wird dieser Code nur bei Verwendung eines Kontos für den Tabellendienst ausgeführt.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Define a projection query that retrieves only the Email property
    TableQuery<CustomerEntity> projectionQuery =
        TableQuery.from(CustomerEntity.class)
        .select(new String[] {"Email"});

    // Define an Entity resolver to project the entity to the Email value.
    EntityResolver<String> emailResolver = new EntityResolver<String>() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
    };

    // Loop through the results, displaying the Email values.
    for (String projectedString :
        cloudTable.execute(projectionQuery, emailResolver)) {
            System.out.println(projectedString);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-or-replace-an-entity"></a>Einfügen oder Ersetzen einer Entität

Es kommt häufig vor, dass Sie eine Entität in eine Tabelle einfügen möchten, ohne zuvor prüfen zu müssen, ob diese Entität bereits in der Tabelle vorhanden ist. Mit einem Einfügen-oder-Ersetzen-Vorgang genügt eine einzelne Anforderung, um eine nicht vorhandene Entität einzufügen oder eine vorhandene zu ersetzen. Ausgehend von vorherigen Beispielen wird durch diesen Code die Entität für "Walter Harp" eingefügt oder ersetzt. Nachdem eine neue Entität erstellt worden ist, ruft dieser Code die Methode **TableOperation.insertOrReplace** auf. Anschließend ruft der Code die Methode **execute** für das **Cloud Table** -Objekt auf und spezifiziert als Parameter die Tabelle und den Einfügen-oder-Ersetzen-Vorgang. Wenn nur ein Teil einer Entität aktualisiert werden sollen, kann stattdessen die Methode **TableOperation.insertOrMerge** verwendet werden. Einfügen-oder-Ersetzen-Vorgänge werden auf dem lokalen Speicheremulator nicht unterstützt. Deshalb wird dieser Code nur bei Verwendung eines Kontos für den Tabellendienst ausgeführt. Weitere Informationen zu Einfügen-oder-Ersetzen- und Einfügen-oder-Zusammenfügen-Vorgängen finden Sie unter [Azure Tables: Introducing Upsert and Query Projection (Azure-Tabellen: Einführung in Upsert- und Abfrageprojektion)][Azure Tables: Introducing Upsert and Query Projection].

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer5);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-an-entity"></a>Löschen einer Entität

Sie können eine Entität problemlos nach dem Abrufen löschen. Rufen Sie nach dem Abrufen der Entität die Methode `TableOperation.delete`mit der zu löschenden Entität auf. Anschließend rufen Sie `execute` für das `CloudTable`-Objekt auf. Durch den nachstehenden Code wird eine Kundenentität aufgerufen und gelöscht.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    CustomerEntity entitySmithJeff =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Create an operation to delete the entity.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Submit the delete operation to the table service.
    cloudTable.execute(deleteSmithJeff);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-table"></a>Löschen einer Tabelle

Schließlich wird mit dem folgenden Code eine Tabelle aus einem Speicherkonto gelöscht. Etwa 40 Sekunden nach dem Löschen einer Tabelle können Sie sie nicht neu erstellen. 

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Delete the table and all its data if it exists.
    CloudTable cloudTable = tableClient.getTableReference("people");
    cloudTable.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

[!INCLUDE [storage-check-out-samples-java](../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit dem Azure-Tabellenspeicherdienst in Java](https://github.com/Azure-Samples/storage-table-java-getting-started)
* Beim [Microsoft Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) handelt es sich um eine kostenlose eigenständige App von Microsoft, über die Sie ganz einfach visuell mit Azure Storage-Daten arbeiten können – unter Windows, MacOS und Linux.
* [Azure Storage-SDK für Java][Azure Storage SDK for Java]
* [Referenz für Azure Storage-Client-SDKs][Azure Storage Client SDK Reference]
* [Azure Storage-REST-API][Azure Storage REST API]
* [Azure Storage-Teamblog][Azure Storage Team Blog]

Weitere Informationen finden Sie im Artikel [Azure für Java-Entwickler](/java/azure).

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Referenz für Azure Storage-Client-SDKs]: https://azure.github.io/azure-storage-java/
[Azure Storage REST API]: /rest/api/storageservices/
[Azure Storage Team Blog]: https://blogs.msdn.microsoft.com/windowsazurestorage/