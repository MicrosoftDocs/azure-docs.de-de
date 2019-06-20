---
title: Phoenix Query Server REST SDK – Azure HDInsight
description: Installieren und verwenden Sie das REST-SDK für den Phoenix Query Server in Azure HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2017
ms.openlocfilehash: 1f468cac29579d8748f61a47b548a67d36ff8279
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64695949"
---
# <a name="apache-phoenix-query-server-rest-sdk"></a>Apache Phoenix Query Server REST SDK

[Apache Phoenix](https://phoenix.apache.org/) ist eine relationale Open-Source-Datenbankschicht mit hochgradig parallelisierter Verarbeitung, die auf [Apache HBase](apache-hbase-overview.md) basiert. Mit Phoenix können Sie SQL-ähnliche Abfragen mit HBase über SSH-Tools wie beispielsweise [SQLLine](apache-hbase-phoenix-squirrel-linux.md) verwenden. Phoenix verfügt auch über einen HTTP-Server, der als Phoenix Query Server (PQS) bezeichnet wird. Es handelt sich um eine Thin Client-Komponente, die zwei Transportmechanismen für die Clientkommunikation unterstützt: JSON und Protokollpuffer. Protokollpuffer sind der standardmäßige Mechanismus, der eine effizientere Kommunikation als JSON bietet.

In diesem Artikel wird beschrieben, wie Sie das PQS REST SDK zum Erstellen von Tabellen, das Durchführen des Upsert-Vorgangs für Zeilen (einzeln und als Massenvorgang) und das Auswählen von Daten mit SQL-Anweisungen nutzen. In den Beispielen wird der [Microsoft .NET-Treiber für Apache Phoenix Query Server](https://www.nuget.org/packages/Microsoft.Phoenix.Client) verwendet. Dieses SDK basiert auf den [Avatica-APIs von Apache Calcite](https://calcite.apache.org/avatica/), bei denen ausschließlich Protokollpuffer für das Serialisierungsformat eingesetzt werden.

Weitere Informationen finden Sie unter [Apache Calcite Avatica Protocol Buffers Reference](https://calcite.apache.org/avatica/docs/protobuf_reference.html) (Avatica-Protokollpuffer von Apache Calcite – Referenz).

## <a name="install-the-sdk"></a>Installieren des SDKs

Der Microsoft .NET-Treiber für Apache Phoenix Query Server wird als NuGet-Paket bereitgestellt, das mit dem folgenden Befehl über die **NuGet-Paket-Manager-Konsole** in Visual Studio installiert werden kann:

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>Instanziieren des neuen PhoenixClient-Objekts

Instanziieren Sie zum Verwenden der Bibliothek zunächst ein neues `PhoenixClient`-Objekt, und übergeben Sie `ClusterCredentials` mit dem `Uri` Ihre Clusters und dem Apache Hadoop-Benutzernamen und -Kennwort des Clusters.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Ersetzen Sie CLUSTERNAME durch Ihren HDInsight HBase-Clusternamen, und USERNAME und PASSWORD durch die bei der Erstellung des Clusters angegebenen Hadoop-Anmeldeinformationen. Der Standardbenutzername für Hadoop ist **admin**.

## <a name="generate-unique-connection-identifier"></a>Generieren eines eindeutigen Verbindungsbezeichners

Zum Senden von Anforderungen an PQS müssen Sie einen eindeutigen Verbindungsbezeichner einfügen, um die Anforderungen der Verbindung zuzuordnen.

```csharp
string connId = Guid.NewGuid().ToString();
```

Für jedes Beispiel wird zuerst die `OpenConnectionRequestAsync`-Methode aufgerufen und der eindeutige Verbindungsbezeichner übergeben. Definieren Sie als Nächstes `ConnectionProperties` und `RequestOptions`, und übergeben Sie diese Objekte und den generierten Verbindungsbezeichner an die `ConnectionSyncRequestAsync`-Methode. Mit dem `ConnectionSyncRequest`-Objekt von PQS wird sichergestellt, dass sowohl der Client als auch der Server über eine einheitliche Sicht der Datenbankeigenschaften verfügen.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest und dazugehörige ConnectionProperties

Übergeben Sie zum Aufrufen von `ConnectionSyncRequestAsync` ein `ConnectionProperties`-Objekt.

```csharp
ConnectionProperties connProperties = new ConnectionProperties
{
    HasAutoCommit = true,
    AutoCommit = true,
    HasReadOnly = true,
    ReadOnly = false,
    TransactionIsolation = 0,
    Catalog = "",
    Schema = "",
    IsDirty = true
};
await client.ConnectionSyncRequestAsync(connId, connProperties, options);
```

Hier sind einige wichtige Eigenschaften aufgeführt:

| Eigenschaft | BESCHREIBUNG |
| -- | -- |
| AutoCommit | Ein boolescher Wert, mit dem angegeben wird, ob `autoCommit` für Phoenix-Transaktionen aktiviert ist. |
| ReadOnly | Ein boolescher Wert, der angibt, ob die Verbindung schreibgeschützt ist. |
| TransactionIsolation | Ein Integer-Wert, mit dem der Grad der Transaktionsisolation gemäß JDBC-Spezifikation angegeben wird (siehe folgende Tabelle).|
| Katalog | Der Name des Katalogs, der zum Abrufen der Verbindungseigenschaften verwendet wird. |
| Schema | Der Name des Schemas, das zum Abrufen der Verbindungseigenschaften verwendet wird. |
| IsDirty | Ein boolescher Wert, der angibt, ob die Eigenschaften geändert wurden. |

Hier sind die `TransactionIsolation`-Werte aufgeführt:

| Isolationswert | BESCHREIBUNG |
| -- | -- |
| 0 | Transaktionen werden nicht unterstützt. |
| 1 | Es können „Dirty Reads“, nicht wiederholbare Lesevorgänge und Phantomlesevorgänge auftreten. |
| 2 | „Dirty Reads“ werden verhindert, aber nicht wiederholbare Lesevorgänge und Phantomlesevorgänge können auftreten. |
| 4 | „Dirty Reads“ und nicht wiederholbare Lesevorgänge werden verhindert, aber Phantomlesevorgänge können auftreten. |
| 8 | „Dirty Reads“, nicht wiederholbare Lesevorgänge und Phantomlesevorgänge werden verhindert. |

## <a name="create-a-new-table"></a>Erstellen einer neuen Tabelle

Bei HBase werden Daten in Tabellen gespeichert, wie dies bei allen Managementsystemen für relationale Datenbanken (RDBMS) der Fall ist. Für Phoenix werden SQL-Standardabfragen zum Erstellen von neuen Tabellen verwendet, während die Primärschlüssel- und Spaltentypen definiert werden.

In diesem und allen nachfolgenden Beispielen wird das instanziierte `PhoenixClient`-Objekt gemäß der Definition unter [Instanziieren des neuen PhoenixClient-Objekts](#instantiate-new-phoenixclient-object) verwendet.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// You can set certain request options, such as timeout in milliseconds:
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
CreateStatementResponse createStatementResponse = null;
OpenConnectionResponse openConnResponse = null;

try
{
    // Opening connection
    var info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Create the statement
    createStatementResponse = client.CreateStatementRequestAsync(connId, options).Result;
    
    // Create the table if it does not exist
    string sql = "CREATE TABLE IF NOT EXISTS Customers (Id varchar(20) PRIMARY KEY, FirstName varchar(50), " +
        "LastName varchar(100), StateProvince char(2), Email varchar(255), Phone varchar(15))";
    await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    Console.WriteLine($"Table \"Customers\" created.");
}
catch (Exception e)
{
    Console.WriteLine(e);
    throw;
}
finally
{
    if (createStatementResponse != null)
    {
        client.CloseStatementRequestAsync(connId, createStatementResponse.StatementId, options).Wait();
        createStatementResponse = null;
    }

    if (openConnResponse != null)
    {
        client.CloseConnectionRequestAsync(connId, options).Wait();
        openConnResponse = null;
    }
}
```

Im vorherigen Beispiel wird eine neue Tabelle mit dem Namen `Customers` mithilfe der Option `IF NOT EXISTS` erstellt. Mit dem Aufruf von `CreateStatementRequestAsync` wird im Avitica-Server (PQS) eine neue Anweisung erstellt. Mit dem Block `finally` werden das zurückgegebene `CreateStatementResponse`-Element und die `OpenConnectionResponse`-Objekte geschlossen.

## <a name="insert-data-individually"></a>Individuelles Einfügen von Daten

In diesem Beispiel wird das individuelle Einfügen von Daten veranschaulicht, indem die Sammlung `List<string>` mit Abkürzungen amerikanischer Bundesstaaten und Gebiete verwendet wird:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

Der Spaltenwert `StateProvince` der Tabelle wird in einem nachfolgenden Auswahlvorgang genutzt.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = await client.PrepareRequestAsync(connId, sql, 100, options);
    statementHandle = prepareResponse.Statement;
    
    var r = new Random();

    // Insert 300 rows
    for (int i = 0; i < 300; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0,1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        Console.WriteLine("Inserting customer " + i);

        await client.ExecuteRequestAsync(statementHandle, list, long.MaxValue, true, options);
    }

    await client.CommitRequestAsync(connId, options);

    Console.WriteLine("Upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

Die Struktur zum Ausführen einer INSERT-Anweisung ähnelt dem Erstellen einer neuen Tabelle. Beachten Sie, dass für die Transaktion am Ende des Blocks `try` ein expliziter Commit durchgeführt wird. In diesem Beispiel wird eine INSERT-Transaktion 300-mal wiederholt. Im folgenden Beispiel wird ein effizienterer Batcheinfügeprozess veranschaulicht.

## <a name="batch-insert-data"></a>Einfügen von Daten als Batches

Der folgende Code ist nahezu identisch mit dem Code zum individuellen Einfügen von Daten. In diesem Beispiel wird das `UpdateBatch`-Objekt in einem Aufruf von `ExecuteBatchRequestAsync` verwendet, anstatt wiederholt `ExecuteRequestAsync` mit einer vorbereiteten Anweisung aufzurufen.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
CreateStatementResponse createStatementResponse = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Creating statement
    createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = client.PrepareRequestAsync(connId, sql, long.MaxValue, options).Result;
    var statementHandle = prepareResponse.Statement;
    var updates = new pbc.RepeatedField<UpdateBatch>();

    var r = new Random();

    // Insert 300 rows
    for (int i = 300; i < 600; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0, 1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        var batch = new UpdateBatch
        {
            ParameterValues = list
        };
        updates.Add(batch);

        Console.WriteLine($"Added customer {i} to batch");
    }

    var executeBatchResponse = await client.ExecuteBatchRequestAsync(connId, statementHandle.Id, updates, options);

    Console.WriteLine("Batch upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

In einer Testumgebung dauerte das individuelle Einfügen von 300 neuen Datensätzen fast zwei Minuten. Das Einfügen von 300 Datensätzen als Batch dauerte dagegen nur sechs Sekunden.

## <a name="select-data"></a>Auswählen von Daten

In diesem Beispiel wird veranschaulicht, wie Sie eine Verbindung zum Ausführen mehrerer Abfragen wiederverwenden:

1. Wählen Sie alle Datensätze aus, und rufen Sie die restlichen Datensätze dann ab, nachdem die Standardhöchstmenge von 100 zurückgegeben wurde.
2. Verwenden Sie eine SELECT-Anweisung für die Gesamtzahl von Zeilen, um das Ergebnis mit dem einzelnen Skalarwert abzurufen.
3. Führen Sie eine SELECT-Anweisung aus, mit der die Gesamtzahl von Kunden pro Bundesstaat oder Gebiet zurückgegeben wird.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;

try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);
    var createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "SELECT * FROM Customers";
    ExecuteResponse executeResponse = await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> rows = executeResponse.Results[0].FirstFrame.Rows;
    // Loop through all of the returned rows and display the first two columns
    for (int i = 0; i < rows.Count; i++)
    {
        Row row = rows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + " " + row.Value[1].ScalarValue.StringValue);
    }
    
    // 100 is hard-coded on the server side as the default firstframe size
    // FetchRequestAsync is called to get any remaining rows
    Console.WriteLine("");
    Console.WriteLine($"Number of rows: {rows.Count}");

    // Fetch remaining rows, offset is not used, simply set to 0
    // When FetchResponse.Frame.Done is true, all rows were fetched
    FetchResponse fetchResponse = await client.FetchRequestAsync(connId, createStatementResponse.StatementId, 0, int.MaxValue, options);
    Console.WriteLine($"Frame row count: {fetchResponse.Frame.Rows.Count}");
    Console.WriteLine($"Fetch response is done: {fetchResponse.Frame.Done}");
    Console.WriteLine("");

    // Running query 2
    string sql2 = "select count(*) from Customers";
    ExecuteResponse countResponse = await client.PrepareAndExecuteRequestAsync(connId, sql2, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);
    long count = countResponse.Results[0].FirstFrame.Rows[0].Value[0].ScalarValue.NumberValue;

    Console.WriteLine($"Total customer records: {count}");
    Console.WriteLine("");

    // Running query 3
    string sql3 = "select StateProvince, count(*) as Number from Customers group by StateProvince order by Number desc";
    ExecuteResponse groupByResponse = await client.PrepareAndExecuteRequestAsync(connId, sql3, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> stateRows = groupByResponse.Results[0].FirstFrame.Rows;
    for (int i = 0; i < stateRows.Count; i++)
    {
        Row row = stateRows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + ": " + row.Value[1].ScalarValue.NumberValue);
    }
}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

Die Ausgabe der `select`-Anweisungen sollte zum folgenden Ergebnis führen:

```
id0 first0
id1 first1
id10 first10
id100 first100
id101 first101
id102 first102
. . .
id185 first185
id186 first186
id187 first187
id188 first188

Number of rows: 100
Frame row count: 500
Fetch response is done: True

Total customer records: 600

NJ: 21
CA: 19
GU: 17
NC: 16
IN: 16
MA: 16
AZ: 16
ME: 16
IL: 15
OR: 15
. . . 
MO: 10
HI: 10
GA: 10
DC: 9
NM: 9
MD: 9
MP: 9
SC: 7
AR: 7
MH: 6
FM: 5
```

## <a name="next-steps"></a>Nächste Schritte 

* [Apache Phoenix in HDInsight](../hdinsight-phoenix-in-hdinsight.md)
* [Verwenden des Apache HBase REST SDK](apache-hbase-rest-sdk.md)
