---
title: Erste Schritte mit Table Storage und verbundenen Visual Studio-Diensten (Cloud Services) | Microsoft Docs
description: Erfahren Sie etwas über die ersten Schritte mit Azure-Tabellenspeicher in einem Clouddienstprojekt in Visual Studio, nachdem Sie mithilfe von verbundenen Visual Studio-Diensten eine Verbindung mit einem Speicherkonto hergestellt haben.
services: storage
author: ghogen
manager: douge
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: cec8ab9d678ff559176580fa8eccc261f449f4c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60361978"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Erste Schritte mit Azure-Tabellenspeicher und verbundenen Visual Studio-Diensten (Clouddienstprojekte)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Übersicht
Dieser Artikel beschreibt, wie die ersten Schritte des Verwendens von Azure-Tabellenspeicher in Visual Studio aussehen, nachdem Sie über das Visual Studio-Dialogfeld **Verbundene Dienste hinzufügen** in einem Clouddienst-Projekt ein Azure-Speicherkonto erstellt oder auf ein solches Konto verwiesen haben. Beim Vorgang **Verbundene Dienste hinzufügen** werden die entsprechenden NuGet-Pakete installiert, um auf Azure-Speicher in Ihrem Projekt zuzugreifen, und Ihren Projektkonfigurationsdateien die Verbindungszeichenfolge für das Speicherkonto hinzugefügt.

Der Azure-Tabellenspeicherdienst ermöglicht das Speichern großer Mengen von strukturierten Daten. Der Dienst ist ein NoSQL-Datenspeicher, der authentifizierte Aufrufe von innerhalb und außerhalb der Azure-Cloud akzeptiert. Azure-Tabellen sind hervorragend zur Speicherung strukturierter nicht relationaler Daten geeignet.

Damit Sie beginnen können, müssen Sie zuerst eine Tabelle in Ihrem Speicherkonto erstellen. Es wird gezeigt, wie Sie eine Azure-Tabelle in Code erstellen, und außerdem, wie Sie grundlegende Tabellen- und Entitätsvorgänge, etwa Hinzufügen, Ändern und Lesen von Tabellenentitäten, ausführen. Die Beispiele sind in C\# geschrieben und greifen auf die [Microsoft Azure Storage-Clientbibliothek für .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) zurück.

**HINWEIS:** Einige der APIs, die Aufrufe an Azure Storage ausführen, arbeiten asynchron. Unter [Asynchrone Programmierung mit Async und Await](https://msdn.microsoft.com/library/hh191443.aspx) finden Sie weitere Informationen. Im folgenden Code wird die Programmierung mit Async-Methoden angenommen.

* Weitere Informationen zum programmgesteuerten Bearbeiten von Tabellen finden Sie unter [Erste Schritte mit Azure Table Storage mit .NET](../storage/storage-dotnet-how-to-use-tables.md) .
* Allgemeine Informationen zum Azure-Speicher finden Sie unter [Speicherdokumentation](https://azure.microsoft.com/documentation/services/storage/) .
* Allgemeine Informationen zu Azure-Clouddiensten finden Sie unter [Cloud Services-Dokumentation](https://azure.microsoft.com/documentation/services/cloud-services/) .
* Weitere Informationen über das Programmieren von ASP.NET-Anwendungen finden Sie unter [ASP.NET](https://www.asp.net) .

## <a name="access-tables-in-code"></a>Zugreifen auf Tabellen im Code
Um auf Tabellen in Clouddienst-Projekten zuzugreifen, müssen Sie die folgenden Elemente zu jeder C#-Quelldatei hinzufügen, in der auf Azure-Tabellenspeicher zugegriffen wird.

1. Vergewissern Sie sich, dass die Namespacedeklarationen am Anfang der C#-Datei diese **using** -Anweisungen enthalten.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Rufen Sie ein **CloudStorageAccount** -Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um die Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > Verwenden Sie den gesamten obigen Code vor dem Code in den folgenden Beispielen.
   > 
   > 
3. Rufen Sie ein **CloudTableClient** -Objekt ab, um auf die Tabellenobjekte in Ihrem Speicherkonto zu verweisen.
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Rufen Sie ein **CloudTable** -Verweisobjekt ab, um auf eine bestimmte Tabelle und auf bestimmte Entitäten zu verweisen.
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Erstellen einer Tabelle in Code
Um die Azure-Tabelle zu erstellen, fügen Sie einfach einen Aufruf von **CreateIfNotExistsAsync** hinzu, nachdem Sie wie im Abschnitt „Zugriff auf Tabellen in Code“ beschrieben ein **CloudTable**-Objekt abgerufen haben.

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Hinzufügen einer Entität zu einer Tabelle
Erstellen Sie eine Klasse, mit der die Eigenschaften der Entität definiert werden, um eine Entität zu einer Tabelle hinzuzufügen. Der folgenden Code definiert eine Entitätsklasse namens **CustomerEntity** , die den Vornamen des Kunden als Zeilenschlüssel und den Nachnamen als Partitionsschlüssel verwendet.

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

Tabellenvorgänge, die Entitäten betreffen, werden mit dem **CloudTable** -Objekt ausgeführt, das Sie zuvor unter "Zugreifen auf Tabellen im Code" erstellt haben. Das **TableOperation** -Objekt stellt den auszuführenden Vorgang dar. Das folgende Codebeispiel zeigt das Erstellen eines **CloudTable**-Objekts und eines **CustomerEntity**-Objekts. Um den Vorgang vorzubereiten, wird eine **TableOperation** zum Einfügen der Kundenentität in die Tabelle erstellt. Schließlich wird der Vorgang durch einen Aufruf von **CloudTable.ExecuteAsync**ausgeführt.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Einfügen eines Entitätsbatchs
Sie können mehrere Entitäten mit einem Schreibvorgang in eine Tabelle einfügen. Das folgende Codebeispiel erstellt zwei Entitätsobjekte („Jeff Smith“ und „Ben Smith“), fügt diese mithilfe der Insert-Methode einem **TableBatchOperation**-Objekt hinzu und startet den Vorgang dann durch einen Aufruf von **CloudTable.ExecuteBatchAsync**.

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

## <a name="get-all-of-the-entities-in-a-partition"></a>Abrufen aller Entitäten in einer Partition
Verwenden Sie ein **TableQuery** -Objekt, um eine Tabelle nach allen Entitäten in einer Partition abzufragen. Im folgenden Codebeispiel wird ein Filter für Entitäten erstellt, wobei "Smith" der Partitionsschlüssel ist. In diesem Beispiel werden die Felder der einzelnen Entitäten in den Abfrageergebnissen an die Konsole ausgegeben.

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

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

    return View();


## <a name="get-a-single-entity"></a>Abrufen einer einzelnen Entität
Sie können eine Abfrage schreiben, um eine bestimmte Entität abzurufen. Der folgende Code verwendet ein **TableOperation** -Objekt, um einen Kunden namens "Ben Smith" anzugeben. Diese Methode gibt anstelle einer Sammlung nur eine Entität zurück, und bei dem zurückgegebenen Wert in **TableResult.Result** handelt es sich um ein **CustomerEntity**-Objekt. Die Angabe beider Schlüssel (für Partition und Zeile) in einer Abfrage ist die schnellste Möglichkeit, um eine einzelne Entität aus dem **Tabellenspeicherdienst** abzurufen.

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>Löschen einer Entität
Sie können eine Entität nach dem Abrufen löschen. Der folgende Code sucht nach einer Kundenentität namens "Ben Smith". Wenn diese gefunden wird, wird sie gelöscht.

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

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

## <a name="next-steps"></a>Nächste Schritte
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

