---
title: Erste Schritte mit Table Storage und verbundenen Visual Studio-Diensten (ASP.NET Core) | Microsoft-Dokumentation
description: Machen Sie sich mit den ersten Schritten mit Azure Table Storage in einem ASP.NET Core-Projekt in Visual Studio vertraut, nachdem Sie mithilfe von verbundenen Visual Studio-Diensten eine Verbindung mit einem Speicherkonto hergestellt haben.
services: storage
author: ghogen
manager: douge
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 1f90ce71084ba3acbf5a0aec5c7b8e9683323766
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60362117"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Erste Schritte mit Azure-Tabellenspeicher und verbundenen Visual Studio-Diensten

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

In diesem Artikel wird beschrieben, wie die ersten Schritte beim Verwenden von Azure Table Storage in Visual Studio aussehen, nachdem Sie mit dem Feature **Verbundene Dienste** in einem ASP.NET Core-Projekt ein Azure Storage-Konto erstellt oder auf ein solches Konto verwiesen haben. Beim Vorgang für **Verbundene Dienste** werden die entsprechenden NuGet-Pakete installiert, um auf Azure Storage in Ihrem Projekt zuzugreifen, und die Verbindungszeichenfolge für das Speicherkonto wird in Ihren Konfigurationsdateien des Projekts hinzugefügt. (Allgemeine Informationen zu Azure Storage finden Sie in der [Speicherdokumentation](https://azure.microsoft.com/documentation/services/storage/).)

Der Azure-Tabellenspeicherdienst ermöglicht das Speichern großer Mengen von strukturierten Daten. Der Dienst ist ein NoSQL-Datenspeicher, der authentifizierte Aufrufe von innerhalb und außerhalb der Azure-Cloud akzeptiert. Azure-Tabellen sind hervorragend zur Speicherung strukturierter nicht relationaler Daten geeignet. Weitere allgemeine Informationen zur Verwendung von Azure Table Storage finden Sie unter [Erste Schritte mit Azure Table Storage mit .NET](../storage/storage-dotnet-how-to-use-tables.md).

Damit Sie beginnen können, müssen Sie zuerst eine Tabelle in Ihrem Speicherkonto erstellen. In diesem Artikel wird dann beschrieben, wie Sie eine Tabelle in C# erstellen und grundlegende Tabellenvorgänge durchführen, z.B. Hinzufügen, Ändern, Lesen und Entfernen von Tabelleneinträgen.  Im Code wird die Azure Storage-Clientbibliothek für .NET verwendet. Weitere Informationen zu ASP.NET finden Sie unter [ASP.NET](https://www.asp.net).

Einige Azure Storage-APIs sind asynchron, und im Code in diesem Artikel wird vorausgesetzt, dass asynchrone Methoden verwendet werden. Weitere Informationen finden Sie unter [Asynchrone Programmierung](https://docs.microsoft.com/dotnet/csharp/async).

## <a name="access-tables-in-code"></a>Zugreifen auf Tabellen im Code

Um auf Tabellen in ASP.NET Core-Projekten zuzugreifen, müssen Sie jeder C#-Quelldatei, in der auf Azure Table Storage zugegriffen wird, die folgenden Elemente hinzufügen.

1. Fügen Sie die erforderlichen `using`-Anweisungen hinzu:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. Rufen Sie ein `CloudStorageAccount`-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code unter Verwendung des Namens Ihres Speicherkontos und des Kontoschlüssels, den Sie im Speicherverbindungszeichenfolge in appSettings.json finden:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. Rufen Sie ein `CloudTableClient`-Objekt ab, um auf die Tabellenobjekte in Ihrem Speicherkonto zu verweisen:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Rufen Sie ein `CloudTable`-Verweisobjekt ab, um auf eine bestimmte Tabelle und bestimmte Entitäten zu verweisen:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Erstellen einer Tabelle in Code

Um die Azure-Tabelle zu erstellen, erstellen Sie eine asynchrone Methode, und rufen Sie darin `CreateIfNotExistsAsync()` auf:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Hinzufügen einer Entität zu einer Tabelle

Sie erstellen eine Klasse, mit der die Eigenschaften der Entität definiert werden, um einer Tabelle eine Entität hinzuzufügen. Mit dem folgenden Code wird die Entitätsklasse `CustomerEntity` definiert, die den Vornamen des Kunden als Zeilenschlüssel und den Nachnamen als Partitionsschlüssel verwendet.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Für Tabellenvorgänge, die Entitäten betreffen, wird das `CloudTable`-Objekt genutzt, das Sie zuvor unter [Zugreifen auf Tabellen in Code](#access-tables-in-code) erstellt haben. Das `TableOperation`-Objekt stellt den auszuführenden Vorgang dar. Im folgenden Codebeispiel wird veranschaulicht, wie Sie ein `CloudTable`-Objekt und ein `CustomerEntity`-Objekt erstellen. Um den Vorgang vorzubereiten, wird eine `TableOperation` zum Einfügen der Kundenentität in die Tabelle erstellt. Abschließend wird der Vorgang ausgeführt, indem `CloudTable.ExecuteAsync` aufgerufen wird.

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Einfügen eines Entitätsbatchs

Sie können mehrere Entitäten mit einem Schreibvorgang in eine Tabelle einfügen. Das folgende Codebeispiel erstellt zwei Entitätsobjekte („Jeff Smith“ und „Ben Smith“), fügt diese mithilfe der `Insert`-Methode einem `TableBatchOperation`-Objekt hinzu und startet den Vorgang dann durch einen Aufruf von `CloudTable.ExecuteBatchAsync`.

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
await peopleTable.ExecuteBatchAsync(batchOperation);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Abrufen aller Entitäten in einer Partition

Verwenden Sie ein `TableQuery`-Objekt, um für eine Tabelle alle Entitäten einer Partition abzufragen. Im folgenden Codebeispiel wird ein Filter für Entitäten erstellt, wobei "Smith" der Partitionsschlüssel ist. In diesem Beispiel werden die Felder der einzelnen Entitäten in den Abfrageergebnissen an die Konsole ausgegeben.

```csharp
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);
```

## <a name="get-a-single-entity"></a>Abrufen einer einzelnen Entität

Sie können eine Abfrage schreiben, um eine bestimmte Entität abzurufen. Der folgende Code verwendet ein `TableOperation`-Objekt, um den Kunden „Ben Smith“ anzugeben. Mit der Methode wird nur eine Entität anstelle einer Sammlung zurückgegeben, und der zurückgegebene Wert in `TableResult.Result` ist ein `CustomerEntity`-Objekt. Das gemeinsame Angeben des Partitions- und Zeilenschlüssels in einer Abfrage ist die schnellste Möglichkeit, um eine einzelne Entität aus dem `Table`-Dienst abzurufen.

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
   Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Löschen einer Entität

Sie können eine Entität nach dem Abrufen löschen. Im folgenden Code wird nach der Kundenentität „Ben Smith“ gesucht, die dann gelöscht wird:

```csharp
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation and then execute it.
if (deleteEntity != null)
{
   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

   // Execute the operation.
   await peopleTable.ExecuteAsync(deleteOperation);

   Console.WriteLine("Entity deleted.");
}

else
   Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>Nächste Schritte
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
