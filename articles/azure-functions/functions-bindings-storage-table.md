---
title: Azure Table Storage-Bindungen für Azure Functions
description: Hier erfahren Sie, wie Sie Azure Table Storage-Bindungen in Azure Functions verwenden.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure Functions, Funktionen, Ereignisverarbeitung, dynamisches Compute, serverlose Architektur
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: 0c9cd513f4d5842d14077bb7470ebd18c7a46340
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64511869"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Table Storage-Bindungen für Azure Functions

In diesem Artikel erfahren Sie, wie Sie Azure Table Storage-Bindungen in Azure Functions verwenden. Azure Functions unterstützt Eingabe- und Ausgabebindungen für Azure-Tabellenspeicher.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakete: Functions 1.x

Die Tabellenspeicherbindungen werden im NuGet-Paket [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), Version 2.x bereitgestellt. Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Pakete: Functions 2.x

Die Tabellenspeicherbindungen werden im NuGet-Paket [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage), Version 3.x bereitgestellt. Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Eingabe

Verwenden Sie die Azure Table Storage-Eingabebindung, um eine Tabelle in einem Azure Storage-Konto zu lesen.

## <a name="input---example"></a>Eingabe: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#: Lesen einer einzelnen Entität](#input---c-example---one-entity)
* [C#: Binden an IQueryable](#input---c-example---iqueryable)
* [C#: Binden an CloudTable](#input---c-example---cloudtable)
* [C#: Lesen einer einzelnen Entität mit einem Skript](#input---c-script-example---one-entity)
* [C#: Binden an IQueryable mit einem Skript](#input---c-script-example---iqueryable)
* [C#: Binden an CloudTable mit einem Skript](#input---c-script-example---cloudtable)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)
* [Java](#input---java-example)

### <a name="input---c-example---one-entity"></a>Eingabe: C#-Beispiel – eine Entität

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md) zum Lesen einer einzelnen Tabellenzeile. 

Der Zeilenschlüsselwert „{queueTrigger}“ gibt an, dass der Zeilenschlüssel aus der Zeichenfolge der Warteschlangennachricht stammt.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="input---c-example---iqueryable"></a>Eingabe: C#-Beispiel – IQueryable

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md) zum Lesen mehrerer Tabellenzeilen. Die Klasse `MyPoco` leitet sich von `TableEntity` ab.

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="input---c-example---cloudtable"></a>Eingabe: C#-Beispiel – CloudTable

`IQueryable` wird in der [Laufzeit von Functions v2](functions-versions.md) nicht unterstützt. Als Alternative kann beispielsweise ein `CloudTable`-Methodenparameter verwendet werden, um die Tabelle unter Verwendung des Azure Storage SDKs zu lesen. Hier finden Sie ein Beispiel einer 2.x-Funktion, die eine Azure Functions-Protokolltabelle abfragt:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

Weitere Informationen zur Verwendung von „CloudTable“ finden Sie unter [Erste Schritte mit Azure Table Storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Wenn Sie versuchen, eine Bindung an `CloudTable` herzustellen, und eine Fehlermeldung erhalten, stellen Sie sicher, dass ein Verweis auf [die richtige Storage SDK-Version](#azure-storage-sdk-version-in-functions-1x) vorliegt.

### <a name="input---c-script-example---one-entity"></a>Eingabe: C#-Skriptbeispiel – eine Entität

Das folgende Beispiel zeigt eine Tabelleneingabebindung in einer Datei vom Typ *function.json* sowie [C#-Skriptcode](functions-reference-csharp.md), der die Bindung verwendet. Die Funktion verwendet einen Warteschlangentrigger zum Lesen einer einzelnen Tabellenzeile. 

Die Datei *function.json* gibt jeweils einen Wert für `partitionKey` und `rowKey` zurück. Der `rowKey`-Wert „{queueTrigger}“ gibt an, dass der Zeilenschlüssel aus der Zeichenfolge der Warteschlangennachricht stammt.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#input---configuration).

Der C#-Skriptcode sieht wie folgt aus:

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="input---c-script-example---iqueryable"></a>Eingabe: C#-Skriptbeispiel – IQueryable

Das folgende Beispiel zeigt eine Tabelleneingabebindung in einer Datei vom Typ *function.json* sowie [C#-Skriptcode](functions-reference-csharp.md), der die Bindung verwendet. Die Funktion liest Entitäten für einen Partitionsschlüssel, der in einer Warteschlangenmeldung angegeben ist.

Die Datei *function.json* sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#input---configuration).

Der C#-Skriptcode fügt einen Verweis auf das Azure Storage SDK hinzu, sodass der Entitätstyp von `TableEntity` abgeleitet werden kann:

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="input---c-script-example---cloudtable"></a>Eingabe: C#-Skriptbeispiel – CloudTable

`IQueryable` wird in der [Laufzeit von Functions v2](functions-versions.md) nicht unterstützt. Als Alternative kann beispielsweise ein `CloudTable`-Methodenparameter verwendet werden, um die Tabelle unter Verwendung des Azure Storage SDKs zu lesen. Hier finden Sie ein Beispiel einer 2.x-Funktion, die eine Azure Functions-Protokolltabelle abfragt:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

Weitere Informationen zur Verwendung von „CloudTable“ finden Sie unter [Erste Schritte mit Azure Table Storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Wenn Sie versuchen, eine Bindung an `CloudTable` herzustellen, und eine Fehlermeldung erhalten, stellen Sie sicher, dass ein Verweis auf [die richtige Storage SDK-Version](#azure-storage-sdk-version-in-functions-1x) vorliegt.

### <a name="input---f-example"></a>Eingabe: F#-Beispiel

Das folgende Beispiel zeigt eine Tabelleneingabebindung in einer Datei vom Typ *function.json* sowie [F#-Skriptcode](functions-reference-fsharp.md), der die Bindung verwendet. Die Funktion verwendet einen Warteschlangentrigger zum Lesen einer einzelnen Tabellenzeile. 

Die Datei *function.json* gibt jeweils einen Wert für `partitionKey` und `rowKey` zurück. Der `rowKey`-Wert „{queueTrigger}“ gibt an, dass der Zeilenschlüssel aus der Zeichenfolge der Warteschlangennachricht stammt.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#input---configuration).

Der F#-Code lautet wie folgt:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.LogInformation(sprintf "Name in Person entity: %s" personEntity.Name)
```

### <a name="input---javascript-example"></a>Eingabe: JavaScript-Beispiel

Das folgende Beispiel zeigt eine Tabelleneingabebindung in einer Datei vom Typ *function.json* sowie [JavaScript-Code](functions-reference-node.md), der die Bindung verwendet. Die Funktion verwendet einen Warteschlangentrigger zum Lesen einer einzelnen Tabellenzeile. 

Die Datei *function.json* gibt jeweils einen Wert für `partitionKey` und `rowKey` zurück. Der `rowKey`-Wert „{queueTrigger}“ gibt an, dass der Zeilenschlüssel aus der Zeichenfolge der Warteschlangennachricht stammt.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#input---configuration).

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

### <a name="input---java-example"></a>Eingabe: Java-Beispiel

Das folgende Beispiel zeigt eine durch HTTP ausgelöste Funktion, die die Gesamtanzahl der Elemente in einer angegebenen Partition in Table Storage zurückgibt.

```java
@FunctionName("getallcount")
public int run(
   @HttpTrigger(name = "req",
                 methods = {HttpMethod.GET},
                 authLevel = AuthorizationLevel.ANONYMOUS) Object dummyShouldNotBeUsed,
   @TableInput(name = "items",
                tableName = "mytablename",  partitionKey = "myparkey",
                connection = "myconnvarname") MyItem[] items
) {
    return items.length;
}
```


## <a name="input---attributes"></a>Eingabe: Attribute
 
Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) die folgenden Attribute, um eine Tabelleneingabebindung zu konfigurieren:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Der Konstruktor des Attributs akzeptiert den Tabellennamen, Partitionsschlüssel und Zeilenschlüssel. Er kann für einen out-Parameter oder für den Rückgabewert der Funktion verwendet werden, wie im folgenden Beispiel zu sehen:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Durch Festlegen der Eigenschaft `Connection` können Sie das zu verwendende Speicherkonto angeben, wie im folgenden Beispiel zu sehen:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Ein vollständiges Beispiel finden Sie unter „Eingabe: C#-Beispiel“.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Eine weitere Möglichkeit zum Angeben des zu verwendenden Speicherkontos. Der Konstruktor akzeptiert den Namen einer App-Einstellung mit einer Speicherverbindungszeichenfolge. Das Attribut kann auf Parameter-, Methoden- oder Klassenebene angewendet werden. Das folgende Beispiel zeigt die Anwendung auf Klassen- und Methodenebene:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Das zu verwendende Speicherkonto wird anhand von Folgendem bestimmt (in der angegebenen Reihenfolge):

* Die Eigenschaft `Connection` des Attributs `Table`.
* Das Attribut `StorageAccount`, das auf den gleichen Parameter angewendet wird wie das Attribut `Table`.
* Das Attribut `StorageAccount`, das auf die Funktion angewendet wird.
* Das Attribut `StorageAccount`, das auf die Klasse angewendet wird.
* Das Standardspeicherkonto für die Funktions-App (App-Einstellung „AzureWebJobsStorage“).

## <a name="input---java-annotations"></a>Eingabe: Java-Anmerkungen

Verwenden Sie die `@TableInput`-Anmerkung in der [Laufzeitbibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) für Parameter, deren Wert von Table Storage empfangen wird.  Diese Anmerkung kann mit nativen Java-Typen, POJOs oder Werten mit Optional<T>, die NULL-Werte annehmen können, verwendet werden. 

## <a name="input---configuration"></a>Eingabe: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `Table` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf `table` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie die Bindung im Azure Portal erstellen.|
|**direction** | – | Muss auf `in` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie die Bindung im Azure Portal erstellen. |
|**name** | – | Der Name der Variablen, die die Tabelle oder Entität im Funktionscode darstellt. | 
|**tableName** | **TableName** | Der Name der Tabelle.| 
|**partitionKey** | **PartitionKey** |Optional. Der Partitionsschlüssel der zu lesenden Tabellenentität. Informationen zur Verwendung dieser Eigenschaft finden Sie im Abschnitt [Verwendung](#input---usage).| 
|**rowKey** |**Zeilenschlüssel** | Optional. Der Zeilenschlüssel der zu lesenden Tabellenentität. Informationen zur Verwendung dieser Eigenschaft finden Sie im Abschnitt [Verwendung](#input---usage).| 
|**take** |**Take** | Optional. Die maximale Anzahl von Entitäten, die in JavaScript gelesen werden sollen. Informationen zur Verwendung dieser Eigenschaft finden Sie im Abschnitt [Verwendung](#input---usage).| 
|**filter** |**Filter** | Optional. Ein OData-Filterausdruck für die Tabelleneingabe in JavaScript. Informationen zur Verwendung dieser Eigenschaft finden Sie im Abschnitt [Verwendung](#input---usage).| 
|**Verbindung** |**Connection** | Der Name einer App-Einstellung, die die Storage-Verbindungszeichenfolge für diese Bindung enthält. Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie hier nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyStorage“ festlegen, sucht die Functions-Laufzeit nach einer App-Einstellung namens „AzureWebJobsMyStorage“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Storage-Verbindungszeichenfolge aus der App-Einstellung `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Eingabe: Verwendung

Die Table Storage-Eingabebindung unterstützt folgende Szenarien:

* **Lesen einer einzelnen Zeile in C# oder C#-Skript**

  Legen Sie `partitionKey` und `rowKey` fest. Verwenden Sie für den Zugriff auf die Tabellendaten einen Methodenparameter vom Typ `T <paramName>`. In C#-Skripts ist `paramName` der Wert, der in der Eigenschaft `name` von *function.json* angegeben ist. `T` ist üblicherweise ein Typ, der `ITableEntity` implementiert oder von `TableEntity` abgeleitet wird. Die Eigenschaften `filter` und `take` werden in diesem Szenario nicht verwendet. 

* **Lesen von Zeilen in C# oder C#-Skript**

  Verwenden Sie für den Zugriff auf die Tabellendaten einen Methodenparameter vom Typ `IQueryable<T> <paramName>`. In C#-Skripts ist `paramName` der Wert, der in der Eigenschaft `name` von *function.json* angegeben ist. Bei `T` muss es sich um einen Typ handeln, der `ITableEntity` implementiert oder von `TableEntity` abgeleitet wird. Für ggf. erforderliche Filtervorgänge können Sie `IQueryable`-Methoden verwenden. Die Eigenschaften `partitionKey`, `rowKey`, `filter` und `take` werden in diesem Szenario nicht verwendet.  

  > [!NOTE]
  > `IQueryable` wird in der [Laufzeit von Functions v2](functions-versions.md) nicht unterstützt. Als Alternative kann beispielsweise ein [CloudTable paramName-Methodenparameter verwendet werden](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable), um die Tabelle unter Verwendung des Azure Storage SDKs zu lesen. Wenn Sie versuchen, eine Bindung an `CloudTable` herzustellen, und eine Fehlermeldung erhalten, stellen Sie sicher, dass ein Verweis auf [die richtige Storage SDK-Version](#azure-storage-sdk-version-in-functions-1x) vorliegt.

* **Lesen von Zeilen in JavaScript**

  Legen Sie die Eigenschaften `filter` und `take` fest. Legen Sie `partitionKey` oder `rowKey` nicht fest. Verwenden Sie `context.bindings.<name>`, um auf die Eingabetabellenentität(en) zuzugreifen. Die deserialisierten Objekte verfügen über die Eigenschaften `RowKey` und `PartitionKey`.

## <a name="output"></a>Output

Verwenden Sie eine Azure Table Storage-Ausgabebindung, um Entitäten in eine Tabelle in einem Azure Storage-Konto zu schreiben.

> [!NOTE]
> Das Aktualisieren vorhandener Entitäten wird von dieser Ausgabebindung nicht unterstützt. Verwenden Sie den `TableOperation.Replace`-Vorgang [aus dem Azure Storage-SDK](https://docs.microsoft.com/azure/cosmos-db/table-storage-how-to-use-dotnet#replace-an-entity) zum Aktualisieren einer vorhandenen Entität.   

## <a name="output---example"></a>Ausgabe: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#](#output---c-example)
* [C#-Skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Ausgabe: C#-Beispiel

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md) mit einem HTTP-Trigger zum Schreiben einer einzelnen Tabellenzeile. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### <a name="output---c-script-example"></a>Ausgabe: C#-Skriptbeispiel

Das folgende Beispiel zeigt eine Tabellenausgabebindung in einer Datei vom Typ *function.json* sowie [C#-Skriptcode](functions-reference-csharp.md), der die Bindung verwendet. Die Funktion schreibt mehrere Tabellenentitäten.

Die Datei *function.json* sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#output---configuration).

Der C#-Skriptcode sieht wie folgt aus:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

### <a name="output---f-example"></a>Ausgabe: F#-Beispiel

Das folgende Beispiel zeigt eine Tabellenausgabebindung in einer Datei vom Typ *function.json* sowie [F#-Skriptcode](functions-reference-fsharp.md), der die Bindung verwendet. Die Funktion schreibt mehrere Tabellenentitäten.

Die Datei *function.json* sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#output---configuration).

Der F#-Code lautet wie folgt:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: ILogger) =
    for i = 1 to 10 do
        log.LogInformation(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### <a name="output---javascript-example"></a>Ausgabe: JavaScript-Beispiel

Das folgende Beispiel zeigt eine Tabellenausgabebindung in einer Datei vom Typ *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet. Die Funktion schreibt mehrere Tabellenentitäten.

Die Datei *function.json* sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#output---configuration).

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

## <a name="output---attributes"></a>Ausgabe: Attribute

In [C#-Klassenbibliotheken](functions-dotnet-class-library.md) verwenden Sie die [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Der Konstruktor des Attributs akzeptiert den Tabellennamen. Er kann für einen `out`-Parameter oder für den Rückgabewert der Funktion verwendet werden, wie im folgenden Beispiel zu sehen:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Durch Festlegen der Eigenschaft `Connection` können Sie das zu verwendende Speicherkonto angeben, wie im folgenden Beispiel zu sehen:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Ein vollständiges Beispiel finden Sie unter [Ausgabe: C#-Beispiel](#output---c-example).

Mit dem Attribut `StorageAccount` können Sie das Speicherkonto auf Klassen-, Methoden- oder Parameterebene angeben. Weitere Informationen finden Sie unter [Eingabe: Attribute](#input---attributes).

## <a name="output---configuration"></a>Ausgabe: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `Table` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf `table` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie die Bindung im Azure Portal erstellen.|
|**direction** | – | Muss auf `out` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie die Bindung im Azure Portal erstellen. |
|**name** | – | Der Variablenname, der in Funktionscode zur Darstellung der Tabelle oder Entität verwendet wird. Legen Sie diesen Wert auf `$return` fest, um auf den Rückgabewert der Funktion zu verweisen.| 
|**tableName** |**TableName** | Der Name der Tabelle.| 
|**partitionKey** |**PartitionKey** | Der Partitionsschlüssel der zu schreibenden Tabellenentität. Informationen zur Verwendung dieser Eigenschaft finden Sie im Abschnitt [Verwendung](#output---usage).| 
|**rowKey** |**Zeilenschlüssel** | Der Zeilenschlüssel der zu schreibenden Tabellenentität. Informationen zur Verwendung dieser Eigenschaft finden Sie im Abschnitt [Verwendung](#output---usage).| 
|**Verbindung** |**Connection** | Der Name einer App-Einstellung, die die Storage-Verbindungszeichenfolge für diese Bindung enthält. Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie hier nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyStorage“ festlegen, sucht die Functions-Laufzeit nach einer App-Einstellung namens „AzureWebJobsMyStorage“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Storage-Verbindungszeichenfolge aus der App-Einstellung `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Ausgabe: Verwendung

Die Table Storage-Ausgabebindung unterstützt folgende Szenarien:

* **Schreiben einer einzelnen Zeile in einer beliebigen Sprache**

  Verwenden Sie in C# und C#-Skripts einen Methodenparameter wie `out T paramName` oder den Rückgabewert der Funktion, um auf die Ausgabetabellenentität zuzugreifen. In C#-Skripts ist `paramName` der Wert, der in der Eigenschaft `name` von *function.json* angegeben ist. `T` kann ein beliebiger serialisierbarer Typ sein, wenn der Partitionsschlüssel und der Zeilenschlüssel durch die Datei *function.json* oder das Attribut `Table` angegeben werden. Andernfalls muss `T` ein Typ sein, der die Eigenschaften `PartitionKey` und `RowKey` enthält. In diesem Szenario implementiert `T` in der Regel `ITableEntity` oder wird von `TableEntity` abgeleitet. Dies ist jedoch nicht zwingend erforderlich.

* **Lesen einer oder mehrerer Zeilen in C# oder C#-Skript**

  Verwenden Sie in C# und C#-Skripts einen Methodenparameter vom Typ `ICollector<T> paramName` oder `IAsyncCollector<T> paramName`, um auf die Ausgabetabellenentität zuzugreifen. In C#-Skripts ist `paramName` der Wert, der in der Eigenschaft `name` von *function.json* angegeben ist. `T` gibt das Schema der hinzuzufügenden Entitäten an. In der Regel leitet sich `T` von `TableEntity` ab oder implementiert `ITableEntity`, dies ist aber nicht zwingend erforderlich. Die Partitionsschlüssel- und Zeilenschlüsselwerte in *function.json* oder im Konstruktor des Attributs `Table` werden in diesem Szenario nicht verwendet.

  Als Alternative kann beispielsweise ein `CloudTable`-Methodenparameter verwendet werden, um unter Verwendung des Azure Storage SDKs in die Tabelle zu schreiben. Wenn Sie versuchen, eine Bindung an `CloudTable` herzustellen, und eine Fehlermeldung erhalten, stellen Sie sicher, dass ein Verweis auf [die richtige Storage SDK-Version](#azure-storage-sdk-version-in-functions-1x) vorliegt. Beispielcode für das Binden von `CloudTable` finden Sie unter in den Beispielen für Eingabebindungen für[C#](#input---c-example---cloudtable) oder [C#-Skripts](#input---c-script-example---cloudtable) weiter oben in diesem Artikel.

* **Schreiben von Zeilen in JavaScript**

  In JavaScript-Funktionen erfolgt der Zugriff auf die Tabellenausgabe mithilfe von `context.bindings.<name>`.

## <a name="exceptions-and-return-codes"></a>Ausnahmen und Rückgabecodes

| Bindung | Verweis |
|---|---|
| Table | [Tabellenfehlercodes](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, Tabelle, Warteschlange | [Speicherfehlercodes](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tabelle, Warteschlange | [Problembehandlung](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md)
