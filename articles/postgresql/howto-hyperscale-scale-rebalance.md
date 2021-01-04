---
title: Ausgleichen von Shards – Hyperscale (Citus) – Azure Database for PostgreSQL
description: Verteilen Sie Shards gleichmäßig auf Server, um die Leistung zu verbessern.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: b69c4fc3ff16cf30181a3529f61af7126b92950b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026385"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Ausgleichen von Shards in einer Hyperscale (Citus)-Servergruppe

Um neu hinzugefügte Knoten optimal zu nutzen, müssen Sie die verteilte [Shard](concepts-hyperscale-distributed-data.md#shards)tabelle ausgleichen. Das bedeutet, dass einige Shards von vorhandenen Knoten auf neue Knoten verschoben werden. Vergewissern Sie sich zunächst, dass die Bereitstellung der neuen Worker erfolgreich abgeschlossen wurde. Starten Sie dann den Shardausgleich, indem Sie über „psql“ eine Verbindung mit dem Clusterkoordinatorknoten herstellen und Folgendes ausführen:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

Die [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards)-Funktion gleicht alle Tabellen in der [Colocation](concepts-hyperscale-colocation.md)-Gruppe der im Argument genannten Tabelle aus. Daher müssen Sie die Funktion nicht für jede verteilte Tabelle aufrufen, sondern einfach nur für eine repräsentative Tabelle aus jeder Zusammenstellungsgruppe.

**Nächste Schritte**


- Erfahren Sie mehr über [Leistungsoptionen](concepts-hyperscale-configuration-options.md) für Servergruppen.
- [Zentrales oder horizontales Hochskalieren einer Servergruppe](howto-hyperscale-scale-grow.md)
- Weitere Informationen finden Sie in den Referenzmaterialien zur [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards)-Funktion.
