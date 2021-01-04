---
title: Hinzufügen eines Shards mithilfe der Tools für elastische Datenbanken
description: Erfahren Sie, wie Sie einem Shard-Satz mithilfe der Elastic Scale-APIs neue Shards hinzufügen.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: efab0234d428a8283845946289cdd1e8a17ded26
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792054"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Hinzufügen eines Shards mithilfe der Tools für elastische Datenbanken
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>So fügen Sie einen Shard für einen neuen Bereich oder Schlüssel hinzu

Anwendungen müssen häufig neue Shards hinzufügen, um Daten zu verwalten, die von neuen Schlüsseln oder Schlüsselbereichen für eine Shardzuordnung erwartet werden, die bereits vorhanden ist. Eine Anwendung beispielsweise, bei der Sharding über die Mandanten-ID durchgeführt wird, muss unter Umständen einen neuen Shard für einen neuen Mandanten bereitstellen, oder Daten, bei denen das Sharding monatlich durchgeführt wird, benötigen möglicherweise einen neuen Shard, der vor dem Start eines jeweils neuen Monats bereitgestellt wird.

Wenn der neue Schlüsselwertbereich nicht bereits Teil einer vorhandenen Zuordnung (Mapping) ist, ist es einfach, den neuen Shard hinzuzufügen und den neuen Schlüssel oder Bereich mit dem Shard zu verknüpfen.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Beispiel: Hinzufügen eines Shards und seines Bereichs zu einer vorhandenen Shardzuordnung

In diesem Beispiel werden die Methoden TryGetShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard), [.NET](/previous-versions/azure/dn823929(v=azure.100))), CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)) und CreateRangeMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) verwendet, und es wird eine Instanz der ShardLocation-Klasse ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation)) erstellt. Im Beispiel unten wurden eine Datenbank namens **sample_shard_2** mit allen erforderlichen darin enthaltenen Schemaobjekten so erstellt, dass sie den Bereich [300, 400) abdecken.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range being added.
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
}

// Create the mapping and associate it with the new shard
sm.CreateRangeMapping(new RangeMappingCreationInfo<long>
                            (new Range<long>(300, 400), shard2, MappingStatus.Online));
```

Für die .NET-Version können Sie auch PowerShell verwenden, um einen neuen Shardzuordnungs-Manager zu erstellen. Ein Beispiel ist [hier](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)verfügbar.

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>So fügen Sie einen Shard für einen leeren Teil eines vorhandenen Bereichs hinzu

In einigen Fällen haben Sie einem Shard vielleicht bereits einen Bereich zugeordnet und diesen teilweise mit Daten gefüllt. Jetzt möchten Sie aber weitere Daten an einen anderen Shard umleiten. Angenommen, Sie führen das Sharding nach einem Bereich von Tagen durch und haben einem Shard bereits 50 Tage zugeordnet. Am 24. Tag sollen zukünftige Daten jedoch an einen anderen Shard weitergeleitet werden. Das [Split-Merge-Tool](elastic-scale-overview-split-and-merge.md) für elastische Datenbanken kann diesen Vorgang durchführen. Wenn jedoch keine Datenverschiebung erforderlich ist (weil noch keine Daten für den Bereich der Tage [25, 50), d.h. einschließlich Tag 25 bis ausschließlich Tag 50, vorliegen), können Sie den ganzen Vorgang direkt mithilfe der Verwaltungs-APIs für die Shardzuordnung durchführen.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Beispiel: Aufteilen eines Bereichs und Zuweisen des leeren Teils zu einem neu hinzugefügten Shard

Eine Datenbank namens „sample_shard_2“ sowie alle erforderlichen, darin enthaltenen Schemaobjekte wurden erstellt.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range we will move
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer,"sample_shard_2"));  
}

// Split the Range holding Key 25
sm.SplitMapping(sm.GetMappingForKey(25), 25);

// Map new range holding [25-50) to different shard:
// first take existing mapping offline
sm.MarkMappingOffline(sm.GetMappingForKey(25));

// now map while offline to a different shard and take online
RangeMappingUpdate upd = new RangeMappingUpdate();
upd.Shard = shard2;
sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd));
```

**Wichtig** :  Verwenden Sie dieses Verfahren nur, wenn Sie sicher sind, dass der Bereich für die aktualisierte Zuordnung leer ist.  Durch die oben genannten Methoden werden keine Daten für den verschobenen Bereich überprüft. Deshalb empfiehlt es sich, Prüfroutinen im Code zu implementieren.  Wenn der verschobene Bereich Zeilen enthält, stimmt die tatsächliche Datenverteilung nicht mit der aktualisierten Shard Map überein. Verwenden Sie das [Split-Merge-Tool](elastic-scale-overview-split-and-merge.md), um den Vorgang durchzuführen.  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]