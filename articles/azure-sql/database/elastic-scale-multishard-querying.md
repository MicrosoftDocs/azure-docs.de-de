---
title: Abfragen von Sharddatenbanken
description: Führen Sie mithilfe der Clientbibliothek für elastische Datenbanken Abfragen mehrerer Shards durch.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: how-to
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 5a0dd12efb9d94bda264b3bd04b05cdc3df917e5
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92786631"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Abfragen mehrerer Shards mithilfe der Tools für elastische Datenbanken
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="overview"></a>Übersicht

Mit den [Tools für elastische Datenbanken](elastic-scale-introduction.md)können Sie die horizontal partitionierten Datenbanklösungen erstellen. **Abfragen von mehreren Shards** wird für Aufgaben wie Datensammlung/Berichterstellung verwendet, für die das Ausführen einer Abfrage über mehrere Shards erforderlich ist. (Vergleichen Sie dies mit dem [datenabhängigen Routing](elastic-scale-data-dependent-routing.md), bei dem die gesamte Arbeit in einem einzigen Shard durchgeführt wird.)

1. Rufen Sie **RangeShardMap** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) oder **ListShardMap** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) mithilfe der Methode **TryGetRangeShardMap** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), **TryGetListShardMap** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) oder **GetShardMap** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) ab. Weitere Informationen finden Sie unter [Erstellen eines ShardMapManager](elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) und [Abrufen einer RangeShardMap oder ListShardMap](elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Erstellen Sie ein **MultiShardConnection** -Objekt ( [Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)).
3. Erstellen Sie ein **MultiShardStatement- oder MultiShardCommand** -Objekt ( [Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
4. Legen Sie die **CommandText** -Eigenschaft ( [Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) auf einen T-SQL-Befehl fest.
5. Führen Sie den Befehl durch Aufrufen der Methode **ExecuteQueryAsync oder ExecuteReader** ( [Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) auf.
6. Zeigen Sie die Ergebnisse mithilfe der Klasse **MultiShardResultSet oder MultiShardDataReader** ( [Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)) an.

## <a name="example"></a>Beispiel

Das folgende Codebeispiel veranschaulicht die Verwendung von Abfragen mehrerer Shards mithilfe eines bestimmten **ShardMap** -Elements mit dem Namen *myShardMap*.

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString))
{
    using (MultiShardCommand cmd = conn.CreateCommand())
    {
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable";
        cmd.CommandType = CommandType.Text;
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn;
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults;

        using (MultiShardDataReader sdr = cmd.ExecuteReader())
        {
            while (sdr.Read())
            {
                var c1Field = sdr.GetString(0);
                var c2Field = sdr.GetFieldValue<int>(1);
                var c3Field = sdr.GetFieldValue<Int64>(2);
            }
        }
    }
}
```

Ein wichtiger Unterschied ist die Erstellung von Verbindungen mit mehreren Shards. Wenn **SqlConnection** in einer Einzeldatenbank ausgeführt wird, nimmt **MultiShardConnection** eine **_Shardsammlung_*als Eingabe. Füllen Sie die Sammlung von Shards aus einer Shard-Map auf. Die Abfrage wird dann mithilfe der* UNION ALL** -Semantik für die Shardsammlung ausgeführt, um ein einziges Gesamtergebnis zu erhalten. Optional kann der Name des Shards, aus dem die Zeile stammt, mithilfe der **ExecutionOptions** -Eigenschaft im Befehl der Ausgabe hinzugefügt werden.

Beachten Sie, dass **myShardMap.GetShards()** aufgerufen wird. Diese Methode ruft alle Shards aus der Shard-Zuordnung ab und stellt damit eine einfache Möglichkeit zum Ausführen einer Abfrage an alle Datenbanken bereit. Die Sammlung von Shards für eine Abfrage mehrerer Shards kann durch Ausführen einer LINQ-Abfrage über die Sammlung, die von dem Aufruf von **myShardMap.GetShards()** zurückgegeben wird, weiter optimiert werden. In Kombination mit der Teilergebnisrichtlinie wurde die aktuelle Funktion bei der Abfrage mehrerer Shards so entwickelt, dass diese gut mit bis zu Hunderten von Shards funktioniert.

Eine Einschränkung von Abfragen mehrerer Shards ist derzeit die fehlende Überprüfung von Shards und Shardlets, die abgefragt werden. Während beim datenabhängigem Routing überprüft wird, dass ob ein bestimmtes Shard Teil der Shard-Map zum Zeitpunkt der Abfrage ist, führen Abfragen mehrerer Shards dieses Überprüfung nicht durch. Dies kann dazu führen, dass Abfragen mehrerer Shards für Datenbanken ausgeführt werden, die aus der Shardzuordnung entfernt wurden.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Abfragen mehrerer Shards und Aufteilungs-/Zusammenführungsvorgänge

Beim Abfragen mehrerer Shards wird nicht überprüft, ob Shardlets im abgefragten Shard an laufenden Aufteilungs-/Zusammenführungsvorgängen teilnehmen. (Siehe [Skalierung mit dem Split-Merge-Tool für elastische Datenbanken](elastic-scale-overview-split-and-merge.md).) Dies kann zu Inkonsistenzen führen, wenn Zeilen aus demselben Shardlet für mehrere Datenbanken in derselben Abfrage mehrerer Shards angezeigt werden. Beachten Sie diese Einschränkungen, und ziehen Sie beim Ausführen von Abfragen mehrerer Shards einen Ausgleich laufender Aufteilungs-/Zusammenführungsvorgänge und Änderungen an der Shard-Zuordnung in Betracht.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]