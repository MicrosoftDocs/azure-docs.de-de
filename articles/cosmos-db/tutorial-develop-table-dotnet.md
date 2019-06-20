---
title: Erste Schritte mit der Azure Cosmos DB-Tabellen-API mit .NET Standard SDK
description: Speichern Sie mit der Azure Cosmos DB-Tabellen-API strukturierte Daten in der Cloud.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 05/20/2019
ms.openlocfilehash: dc29cc6d3cc2a07214fb638a10039a4c3ea2d92b
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65953623"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Erste Schritte mit der Azure Cosmos DB-Tabellen-API und Azure Table Storage mit .NET SDK

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Sie können die Azure Cosmos DB-Tabellen-API oder Azure Table Storage verwenden, um strukturierte NoSQL-Daten in der Cloud zu speichern. Dazu wird ein Schlüssel--Attribut-Speicher mit einem schemalosen Design angeboten. Aufgrund der Schemalosigkeit der Azure Cosmos DB-Tabellen-API und von Table Storage ist es einfach, Ihre Daten an die Entwicklung Ihrer Anwendungen anzupassen. Mit der Azure Cosmos DB-Tabellen-API oder Table Storage können Sie flexible Datasets wie Benutzerdaten für Webanwendungen, Adressbücher, Geräteinformationen und andere Arten von Metadaten speichern, die Ihr Dienst benötigt. 

Dieses Tutorial beschreibt ein Beispiel dafür, wie Sie die [Microsoft Azure Cosmos DB-Tabellenbibliothek für .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) in Szenarien mit der Azure Cosmos DB-Tabellen-API und Azure Table Storage verwenden. Sie müssen die spezifische Verbindung für den Azure-Dienst verwenden. Diese Szenarien werden anhand von C#-Beispielen erläutert, die veranschaulichen, wie Sie Tabellen erstellen, Daten einfügen/aktualisieren, Daten abfragen und die Tabellen löschen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Beispiel benötigen Sie Folgendes:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure Cosmos DB-Tabellenbibliothek für .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): Diese Bibliothek ist derzeit für .NET Standard und das .NET Framwork verfügbar. 

* [Konto für die Azure Cosmos DB-Tabellen-API](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Erstellen eines Kontos für die Azure Cosmos DB-Tabellen-API

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Erstellen eines .NET-Konsolenprojekts

Erstellen Sie in Visual Studio eine neue .NET-Konsolenanwendung. In den folgenden Schritten wird veranschaulicht, wie Sie eine Konsolenanwendung in Visual Studio 2019 erstellen. Sie können die Azure Cosmos DB-Tabellenbibliothek in jeder Art von .NET-Anwendung nutzen, z.B. einem Azure-Clouddienst, einer Azure-Web-App, einer Desktopanwendung oder einer mobilen Anwendung. In diesem Leitfaden verwenden wir der Einfachheit halber eine Konsolenanwendung.

1. Wählen Sie **Datei** > **Neu** > **Projekt**.

1. Wählen Sie **Konsolen-App (.NET Core)** und dann **Weiter** aus.

1. Geben Sie im Feld **Projektname** einen Namen für Ihre Anwendung ein, z. B. **CosmosTableSamples**. (Sie können bei Bedarf einen anderen Namen angeben.)

1. Klicken Sie auf **Erstellen**.

Alle Codebeispiele in diesem Beispiel können in der Datei **Program.cs** Ihrer Konsolenanwendung der „Main()“-Methode hinzugefügt werden.

## <a name="install-the-required-nuget-package"></a>Installieren der erforderlichen NuGet-Pakete

Führen Sie die folgenden Schritte aus, um die NuGet-Pakete abzurufen:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.

1. Suchen Sie online nach `Microsoft.Azure.Cosmos.Table`, `Microsoft.Extensions.Configuration`, `Microsoft.Extensions.Configuration.Json`, `Microsoft.Extensions.Configuration.Binder` und wählen Sie **Installieren** aus, um die Microsoft Azure Cosmos DB-Tabellenbibliothek zu installieren.

## <a name="configure-your-storage-connection-string"></a>Konfigurieren der Speicherverbindungszeichenfolge

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem Azure Cosmos-Konto oder Ihrem Table Storage-Konto. 

1. Öffnen Sie einen der Bereiche **Verbindungszeichenfolge** oder **Zugriffsschlüssel**. Kopieren Sie mithilfe der Kopierschaltfläche im rechten Fensterbereich die **PRIMÄRE VERBINDUNGSZEICHENFOLGE**.

   ![Anzeigen und Kopieren der primären Verbindungszeichenfolge im Bereich „Verbindungszeichenfolge“](./media/create-table-dotnet/connection-string.png)
   
1. Um Ihre Verbindungszeichenfolge zu konfigurieren, klicken Sie in Visual Studio mit der rechten Maustaste auf Ihr Projekt **CosmosTableSamples**.

1. Wählen Sie **Hinzufügen** > **Neues Element** aus. Erstellen Sie eine neue Datei **Settings.json** mit dem Dateityp als **TypeScript-JSON-Konfiguration**. 

1. Ersetzen Sie den Code in der Datei „Settings.json“ durch den folgenden Code, und weisen Sie Ihre primäre Verbindungszeichenfolge zu:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt **CosmosTableSamples**. Wählen Sie **Hinzufügen** > **Neues Element**, und fügen Sie eine Klasse mit dem Namen **AppSettings.cs** hinzu.

1. Fügen Sie AppSettings.cs den folgenden Code hinzu: Diese Datei liest die Verbindungszeichenfolge aus der Datei „Settings.json“ und weist sie dem Konfigurationsparameter zu:

   ```csharp
   namespace CosmosTableSamples
   {
    using Microsoft.Extensions.Configuration;
    public class AppSettings
    {
        public string StorageConnectionString { get; set; }
        public static AppSettings LoadAppSettings()
        {
            IConfigurationRoot configRoot = new ConfigurationBuilder()
                .AddJsonFile("Settings.json")
                .Build();
            AppSettings appSettings = configRoot.Get<AppSettings>();
            return appSettings;
        }
    }
   }
   ```

## <a name="parse-and-validate-the-connection-details"></a>Analysieren und Überprüfen der Verbindungsdetails 

1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt **CosmosTableSamples**. Wählen Sie **Hinzufügen** > **Neues Element**, und fügen Sie eine Klasse mit dem Namen **Common.cs** hinzu. Sie schreiben Code, um die Verbindungsdetails zu überprüfen und eine Tabelle innerhalb dieser Klasse zu erstellen.

1. Definieren Sie eine Methode `CreateStorageAccountFromConnectionString`, wie unten dargestellt. Diese Methode analysiert die Details der Verbindungszeichenfolge und überprüft, ob der Kontoname und die Schlüsseldaten des Kontos, die in der Datei „Settings.json“ angegeben sind, gültig sind. 

   ```csharp
   public static CloudStorageAccount CreateStorageAccountFromConnectionString(string storageConnectionString)
    {
            CloudStorageAccount storageAccount;
            try
            {
                storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            }
            catch (FormatException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the application.");
                throw;
            }
            catch (ArgumentException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the sample.");
                Console.ReadLine();
                throw;
            }

            return storageAccount;
        }
   ```


## <a name="create-a-table"></a>Erstellen einer Tabelle 

Mit der [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) -Klasse können Sie im Tabellenspeicher gespeicherte Tabellen und Entitäten abrufen. Da wir keine Tabellen im Konto der Cosmos DB-Tabellen-API haben, fügen wir die Methode `CreateTableAsync` zur Klasse **Common.cs** hinzu, um eine Tabelle zu erstellen:

```csharp
public static async Task<CloudTable> CreateTableAsync(string tableName)
  {
    string storageConnectionString = AppSettings.LoadAppSettings().StorageConnectionString;

    // Retrieve storage account information from connection string.
    CloudStorageAccount storageAccount = CreateStorageAccountFromConnectionString(storageConnectionString);

    // Create a table client for interacting with the table service
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient(new TableClientConfiguration());

    Console.WriteLine("Create a Table for the demo");

    // Create a table client for interacting with the table service 
    CloudTable table = tableClient.GetTableReference(tableName);
    if (await table.CreateIfNotExistsAsync())
    {
      Console.WriteLine("Created Table named: {0}", tableName);
    }
    else
    {
      Console.WriteLine("Table {0} already exists", tableName);
    }

    Console.WriteLine();
    return table;
}
```

## <a name="define-the-entity"></a>Definieren der Entität 

Entitäten werden C#-Objekten zugeordnet, indem eine benutzerdefinierte Klasse verwendet wird, die aus [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity)abgeleitet wurde. Erstellen Sie eine Klasse, mit der die Eigenschaften der Entität definiert werden, um eine Entität zu einer Tabelle hinzuzufügen.

Klicken Sie mit der rechten Maustaste auf Ihr Projekt **CosmosTableSamples**. Wählen Sie **Hinzufügen**, **Neuer Ordner**,und nennen Sie ihn **Modell**. Fügen Sie im Ordner „Modell“ eine Klasse namens **CustomerEntity.cs** hinzu, und fügen Sie den folgenden Code hinzu.

```csharp
namespace CosmosTableSamples.Model
{
    using Microsoft.Azure.Cosmos.Table;
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity()
        {
        }

        public CustomerEntity(string lastName, string firstName)
        {
            PartitionKey = lastName;
            RowKey = firstName;
        }

        public string Email { get; set; }
        public string PhoneNumber { get; set; }
    }
}
```

Mit dem Code wird eine Entitätsklasse definiert, die den Vornamen des Kunden als Zeilenschlüssel und den Nachnamen als Partitionsschlüssel verwendet. Eine Entität in der Tabelle wird durch die Kombination aus Partitions- und Zeilenschlüssel eindeutig identifiziert. Entitäten mit dem gleichen Partitionsschlüssel können schneller abgerufen werden als Entitäten mit unterschiedlichen Partitionsschlüsseln, die Verwendung verschiedener Partitionsschlüssel ermöglicht jedoch eine größere Skalierbarkeit paralleler Vorgänge. Bei Entitäten, die in Tabellen gespeichert werden sollen, muss es sich um einen unterstützten Typ handeln (beispielsweise abgeleitet von der [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity)-Klasse). Bei Entitätseigenschaften, die Sie in einer Tabelle speichern möchten, muss es sich um öffentliche Eigenschaften des Typs handeln, und sie müssen sowohl das Abrufen als auch das Festlegen von Werten unterstützen. Darüber hinaus muss der Entitätstyp einen parameterlosen Konstruktor verfügbar machen.

## <a name="insert-or-merge-an-entity"></a>Einfügen oder Zusammenführen einer Entität

Das folgende Codebeispiel erstellt ein Entitätsobjekt und fügt es der Tabelle hinzu. Die Methode „InsertOrMerge“ innerhalb der Klasse [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) wird verwendet, um eine Entität einzufügen oder zusammenzuführen. Danach wird die Methode [CloudTable.ExecuteAsyn](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) aufgerufen, um den Vorgang auszuführen. 

Klicken Sie mit der rechten Maustaste auf Ihr Projekt **CosmosTableSamples**. Wählen Sie **Hinzufügen** > **Neues Element**, und fügen Sie eine Klasse mit dem Namen **SamplesUtils.cs** hinzu. Diese Klasse speichert den gesamten Code, der für die Durchführung von CRUD-Operationen an den Entitäten erforderlich ist. 

```csharp
public static async Task<CustomerEntity> InsertOrMergeEntityAsync(CloudTable table, CustomerEntity entity)
    {
      if (entity == null)
    {
       throw new ArgumentNullException("entity");
    }
    try
    {
       // Create the InsertOrReplace table operation
       TableOperation insertOrMergeOperation = TableOperation.InsertOrMerge(entity);

       // Execute the operation.
       TableResult result = await table.ExecuteAsync(insertOrMergeOperation);
       CustomerEntity insertedCustomer = result.Result as CustomerEntity;
        
        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
          {
            Console.WriteLine("Request Charge of InsertOrMerge Operation: " + result.RequestCharge);
          }

        return insertedCustomer;
        }
        catch (StorageException e)
        {
          Console.WriteLine(e.Message);
          Console.ReadLine();
          throw;
        }
    }
```

### <a name="get-an-entity-from-a-partition"></a>Abrufen einer Entität aus einer Partition

Sie können eine Entität von einer Partition abrufen, indem Sie die Retrieve-Methode der Klasse [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) verwenden. Das folgende Codebeispiel ruft den Zeilenschlüssel des Partitionsschlüssels, die E-Mail-Adresse und die Telefonnummer einer Kundenentität ab. Dieses Beispiel druckt auch die Anforderungseinheiten aus, die für die Abfrage nach der Entität benötigt werden. Um eine Entität abzufragen, fügen Sie den folgenden Code an die Datei **SamplesUtils.cs** an: 

```csharp
public static async Task<CustomerEntity> RetrieveEntityUsingPointQueryAsync(CloudTable table, string partitionKey, string rowKey)
    {
      try
      {
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>(partitionKey, rowKey);
        TableResult result = await table.ExecuteAsync(retrieveOperation);
        CustomerEntity customer = result.Result as CustomerEntity;
        if (customer != null)
        {
          Console.WriteLine("\t{0}\t{1}\t{2}\t{3}", customer.PartitionKey, customer.RowKey, customer.Email, customer.PhoneNumber);
        }

        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
        {
           Console.WriteLine("Request Charge of Retrieve Operation: " + result.RequestCharge);
        }

        return customer;
        }
        catch (StorageException e)
        {
           Console.WriteLine(e.Message);
           Console.ReadLine();
           throw;
        }
    }
```

## <a name="delete-an-entity"></a>Löschen einer Entität

Eine Entität kann nach dem Abrufen problemlos gelöscht werden. Verwenden Sie dazu das gleiche Muster wie beim Aktualisieren einer Entität. Durch den nachstehenden Code wird eine Kundenentität aufgerufen und gelöscht. Um eine Entität zu löschen, fügen Sie den folgenden Code an die Datei **SamplesUtils.cs** an: 

```csharp
public static async Task DeleteEntityAsync(CloudTable table, CustomerEntity deleteEntity)
   {
     try
     {
        if (deleteEntity == null)
     {
        throw new ArgumentNullException("deleteEntity");
     }

    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    TableResult result = await table.ExecuteAsync(deleteOperation);

    // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
    if (result.RequestCharge.HasValue)
    {
       Console.WriteLine("Request Charge of Delete Operation: " + result.RequestCharge);
    }

    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="execute-the-crud-operations-on-sample-data"></a>Ausführen der CRUD-Vorgänge für Beispieldaten

Nachdem Sie die Methoden zum Erstellen von Tabellen, Einfügen oder Zusammenführen von Entitäten definiert haben, führen Sie diese Methoden für Beispieldaten aus. Klicken Sie dafür mit der rechten Maustaste auf Ihr Projekt **CosmosTableSamples**. Wählen Sie **Hinzufügen** > **Neues Element** aus, fügen Sie eine Klasse mit dem Namen **BasicSamples.cs** hinzu, und fügen Sie der Klasse den folgenden Code hinzu. Dieser Code erstellt eine Tabelle und fügt Entitäten hinzu. Wenn Sie die Entität und Tabelle am Ende des Projekts löschen möchten, entfernen Sie die Kommentare von den Methoden `table.DeleteIfExistsAsync()` und `SamplesUtils.DeleteEntityAsync(table, customer)` aus dem folgenden Code:

```csharp
using System;
namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Model;

    class BasicSamples
    {
        public async Task RunSamples()
        {
            Console.WriteLine("Azure Cosmos DB Table - Basic Samples\n");
            Console.WriteLine();

            string tableName = "demo" + Guid.NewGuid().ToString().Substring(0, 5);

            // Create or reference an existing table
            CloudTable table = await Common.CreateTableAsync(tableName);

            try
            {
                // Demonstrate basic CRUD functionality 
                await BasicDataOperationsAsync(table);
            }
            finally
            {
                // Delete the table
                // await table.DeleteIfExistsAsync();
            }
        }

        private static async Task BasicDataOperationsAsync(CloudTable table)
        {
            // Create an instance of a customer entity. See the Model\CustomerEntity.cs for a description of the entity.
            CustomerEntity customer = new CustomerEntity("Harp", "Walter")
            {
                Email = "Walter@contoso.com",
                PhoneNumber = "425-555-0101"
            };

            // Demonstrate how to insert the entity
            Console.WriteLine("Insert an Entity.");
            customer = await SamplesUtils.InsertOrMergeEntityAsync(table, customer);

            // Demonstrate how to Update the entity by changing the phone number
            Console.WriteLine("Update an existing Entity using the InsertOrMerge Upsert Operation.");
            customer.PhoneNumber = "425-555-0105";
            await SamplesUtils.InsertOrMergeEntityAsync(table, customer);
            Console.WriteLine();

            // Demonstrate how to Read the updated entity using a point query 
            Console.WriteLine("Reading the updated Entity.");
            customer = await SamplesUtils.RetrieveEntityUsingPointQueryAsync(table, "Harp", "Walter");
            Console.WriteLine();

            // Demonstrate how to Delete an entity
            //Console.WriteLine("Delete the entity. ");
            //await SamplesUtils.DeleteEntityAsync(table, customer);
            //Console.WriteLine();
        }
    }
}
```

Der vorherige Code erstellt eine Tabelle, die mit „demo“ beginnt, und die generierte GUID wird an den Tabellennamen angehängt. Anschließend wird eine Kundenentität mit Vor- und Nachnamen als „Walter Harp“ hinzugefügt und später die Telefonnummer dieses Benutzers aktualisiert. 

In diesem Tutorial haben Sie Code erstellt, um grundlegende CRUD-Vorgänge an den im Tabellen-API-Konto gespeicherten Daten durchzuführen. Sie können auch erweiterte Operationen durchführen. Beispiele hierfür sind das Einfügen von Daten per Batchvorgang, das Abfragen aller Daten oder eines Datenbereichs innerhalb einer Partition oder das Auflisten der im Konto vorhandenen Tabellen, deren Namen mit dem angegebenen Präfix beginnen. Sie können das komplette Beispiel aus dem [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)-GitHub-Repositoy herunterladen. Die [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) -Klasse verfügt über mehr Vorgänge, die Sie für die Daten ausführen können.  

## <a name="run-the-project"></a>Ausführen des Projekts

Nun erstellen Sie die Projektmappe, und drücken F5, um das Projekt auszuführen. Wenn das Projekt ausgeführt wird, sehen Sie die folgende Ausgabe in der Eingabeaufforderung:

![Ausgabe in der Eingabeaufforderung](./media/tutorial-develop-table-standard/output-from-sample.png)

Wenn der Fehler angezeigt wird, dass die Datei „Settings.json“ beim Ausführen des Projekts nicht gefunden werden kann, können Sie das Problem lösen, indem Sie den folgenden XML-Eintrag zu den Projekteinstellungen hinzufügen. Klicken Sie mit der rechten Maustaste auf „CosmosTableSamples“, wählen Sie „CosmosTableSamples.csproj bearbeiten“ aus, und fügen Sie die folgende „itemGroup“ hinzu: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Jetzt können Sie sich beim Azure-Portal anmelden und überprüfen, ob die Daten in der Tabelle vorhanden sind. 

![Ergebnisse im Portal](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>Nächste Schritte

Sie können nun mit dem nächsten Tutorial fortfahren, in dem Sie erfahren, wie Sie Daten in das Azure Cosmos DB-Tabellen-API-Konto migrieren können. 

> [!div class="nextstepaction"]
>[Gewusst wie: Abfragen von Daten](../cosmos-db/table-import.md)
