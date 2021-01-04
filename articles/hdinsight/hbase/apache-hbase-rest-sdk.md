---
title: Verwenden des HBase .NET SDK – Azure HDInsight
description: Verwenden Sie das HBase .NET SDK zum Erstellen und Löschen von Tabellen und zum Lesen und Schreiben von Daten.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/02/2019
ms.openlocfilehash: 17c3f07fe553e363d1eb2a997287feb77296a621
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540311"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Verwenden des .NET SDK für Apache HBase

[Apache HBase](apache-hbase-overview.md) bietet zwei primäre Optionen für die Arbeit mit Ihren Daten: [Apache Hive-Abfragen und Aufrufe der RESTful-API von HBase](apache-hbase-tutorial-get-started-linux.md). Sie können direkt mit der REST-API arbeiten, indem Sie den Befehl `curl` oder ein ähnliches Hilfsprogramm verwenden.

Für C#- und .NET-Anwendungen bietet die [Microsoft HBase REST-Clientbibliothek für .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) eine Clientbibliothek zusätzlich zu der HBase REST-API.

## <a name="install-the-sdk"></a>Installieren des SDKs

Das HBase .NET SDK wird als NuGet-Paket bereitgestellt, das mit folgendem Befehl über die **NuGet-Paket-Manager-Konsole** in Visual Studio installiert werden kann:

```console
Install-Package Microsoft.HBase.Client
```

## <a name="instantiate-a-new-hbaseclient-object"></a>Instanziieren eines neuen HBaseClient-Objekts

Um das SDK zu verwenden, instanziieren Sie ein neues `HBaseClient`-Objekt, indem Sie als Eingabe `ClusterCredentials` übergeben, die aus dem `Uri` zu Ihrem Cluster und dem Hadoop-Benutzernamen und -Kennwort bestehen.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Ersetzen Sie CLUSTERNAME durch Ihren HDInsight HBase-Clusternamen, und USERNAME und PASSWORD durch die bei der Erstellung des Clusters angegebenen Apache Hadoop-Anmeldeinformationen. Der Standardbenutzername für Hadoop ist **admin** .

## <a name="create-a-new-table"></a>Erstellen einer neuen Tabelle

HBase speichert Daten in Tabellen. Eine Tabelle besteht aus einem *Rowkey* , dem primären Schlüssel, und einer oder mehreren Gruppen von Spalten, die als *Spaltenfamilien* bezeichnet werden. Die Daten werden in jeder Tabelle durch einen Rowkey-Bereich horizontal in *Regionen* verteilt. Jede Region hat einen Start- und Endschlüssel. Eine Tabelle kann eine oder mehrere Regionen enthalten. Mit der Zunahme der Daten in der Tabelle teilt HBase große Bereiche in kleinere Regionen auf. Regionen werden in *Regionsservern* gespeichert, wobei ein Regionsserver mehrere Regionen speichern kann.

Physisch werden die Daten in *HFiles* gespeichert. Eine einzelne HFile enthält Daten für eine Tabelle, eine Region und eine Spaltenfamilie. Zeilen in HFiles werden sortiert nach Rowkey gespeichert. Jede HFile verfügt über einen *B+ Tree* -Index für schnelles Abrufen der Zeilen.

Um eine neue Tabelle zu erstellen, geben Sie ein `TableSchema` und Spalten an. Der folgende Code überprüft, ob die Tabelle „RestSDKTable“ bereits vorhanden ist – falls nicht, wird die Tabelle erstellt.

```csharp
if (!client.ListTablesAsync().Result.name.Contains("RestSDKTable"))
{
    // Create the table
    var newTableSchema = new TableSchema {name = "RestSDKTable" };
    newTableSchema.columns.Add(new ColumnSchema() {name = "t1"});
    newTableSchema.columns.Add(new ColumnSchema() {name = "t2"});

    await client.CreateTableAsync(newTableSchema);
}
```

Diese neue Tabelle enthält zwei Spaltenfamilien, „t1“ und „t2“. Da Spaltenfamilien getrennt in verschiedenen HFiles gespeichert werden, ist es sinnvoll, eine separate Spaltenfamilie für häufig abgefragte Daten zu haben. Im folgenden Beispiel [Einfügen von Daten](#insert-data) werden der Spaltenfamilie „t1“ Spalten hinzugefügt.

## <a name="delete-a-table"></a>Löschen einer Tabelle

So löschen Sie eine Tabelle

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Einfügen von Daten

Um Daten einzufügen, geben Sie einen eindeutigen Zeilenschlüssel als Zeilenbezeichner an. Alle Daten werden in einem `byte[]`-Array gespeichert. Der folgende Code definiert und fügt der Spaltenfamilie „t1“ die Spalten `title`, `director` und `release_date` hinzu, da am häufigsten auf diese Spalten zugegriffen wird. Die Spalten `description` und `tagline` werden der Spaltenfamilie „t2“ hinzugefügt. Sie können Ihre Daten ganz nach Bedarf in Spaltenfamilien unterteilen.

```csharp
var key = "fifth_element";
var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };
var value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:title"),
    data = Encoding.UTF8.GetBytes("The Fifth Element")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:director"),
    data = Encoding.UTF8.GetBytes("Luc Besson")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:release_date"),
    data = Encoding.UTF8.GetBytes("1997")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:description"),
    data = Encoding.UTF8.GetBytes("In the colorful future, a cab driver unwittingly becomes the central figure in the search for a legendary cosmic weapon to keep Evil and Mr Zorg at bay.")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:tagline"),
    data = Encoding.UTF8.GetBytes("The Fifth is life")
};
row.values.Add(value);

var set = new CellSet();
set.rows.Add(row);

await client.StoreCellsAsync("RestSDKTable", set);
```

HBase implementiert [Cloud BigTable](https://cloud.google.com/bigtable/), daher ähnelt das Datenformat der folgenden Abbildung:

![Apache HBase – Beispieldatenausgabe](./media/apache-hbase-rest-sdk/hdinsight-table-roles.png)

## <a name="select-data"></a>Auswählen von Daten

Zum Lesen von Daten aus einer HBase-Tabelle übergeben Sie den Tabellennamen und den Zeilenschlüssel an die `GetCellsAsync`-Methode, um das `CellSet` zurückzugeben.

```csharp
var key = "fifth_element";

var cells = await client.GetCellsAsync("RestSDKTable", key);
// Get the first value from the row.
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
// Get the value for t1:title
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:title").data));
// With the previous insert, it should yield: "The Fifth Element"
```

In diesem Fall gibt der Code nur die erste übereinstimmende Zeile zurück, da bei einem eindeutigen Schlüssel nur eine Zeile vorhanden sein sollte. Der zurückgegebene Wert wird aus dem `byte[]`-Array-Format in das `string`-Format umgewandelt. Sie können den Wert auch in andere Typen konvertieren, z. B. eine ganze Zahl (Integer) für das Veröffentlichungsdatum des Films:

```csharp
var releaseDateField = cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:release_date");
int releaseDate = 0;

if (releaseDateField != null)
{
    releaseDate = Convert.ToInt32(Encoding.UTF8.GetString(releaseDateField.data));
}
Console.WriteLine(releaseDate);
// Should return 1997
```

## <a name="scan-over-rows"></a>Scannen über Zeilen hinweg

HBase verwendet `scan`, um eine oder mehrere Zeilen abzurufen. In diesem Beispiel werden mehrere Zeilen in Gruppen von 10 angefordert, und Daten, deren Schlüsselwerte zwischen 25 und 35 liegen, werden abgerufen. Nach dem Abrufen aller Zeilen löschen Sie den Scanner, um Ressourcen zu bereinigen.

```csharp
var tableName = "mytablename";

// Assume the table has integer keys and we want data between keys 25 and 35
var scanSettings = new Scanner()
{
    batch = 10,
    startRow = BitConverter.GetBytes(25),
    endRow = BitConverter.GetBytes(35)
};
RequestOptions scanOptions = RequestOptions.GetDefaultOptions();
scanOptions.AlternativeEndpoint = "hbaserest0/";
ScannerInformation scannerInfo = null;
try
{
    scannerInfo = await client.CreateScannerAsync(tableName, scanSettings, scanOptions);
    CellSet next = null;
    while ((next = client.ScannerGetNextAsync(scannerInfo, scanOptions).Result) != null)
    {
        foreach (var row in next.rows)
        {
            // ... read the rows
        }
    }
}
finally
{
    if (scannerInfo != null)
    {
        await client.DeleteScannerAsync(tableName, scannerInfo, scanOptions);
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit einem Apache HBase-Beispiel in HDInsight](apache-hbase-tutorial-get-started-linux.md)
* Erstellen einer End-to-End-Anwendung mit [Analyse der Twitter-Stimmung in Echtzeit mit Apache HBase](./apache-hbase-tutorial-get-started-linux.md)