---
title: Verteilte Daten in Azure Database for PostgreSQL – Hyperscale (Citus) (Vorschauversion)
description: In der Servergruppe verteilte Tabellen und Shards.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 9020ee690d93a1b477471fac4a482a909fca5935
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080426"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Verteilte Daten in Azure Database for PostgreSQL – Hyperscale (Citus) (Vorschauversion)

In diesem Artikel werden die drei in Hyperscale (Citus) verfügbaren Tabellentypen beschrieben.
Es wird erläutert, wie verteilte Tabellen als Shards gespeichert und Shards auf Knoten platziert werden.

## <a name="table-types"></a>Tabellentypen

In einer Hyperscale-Servergruppe gibt es drei Typen von Tabellen, die jeweils für unterschiedliche Zwecke verwendet werden.

### <a name="type-1-distributed-tables"></a>Typ 1: Verteilte Tabellen

Beim ersten und am häufigsten verwendeten Typ handelt es sich um *verteilte* Tabellen. Sie sehen wie normale Tabellen für SQL-Anweisungen aus, sind jedoch auf Workerknoten horizontal *partitioniert*. Dies bedeutet, dass die Zeilen der Tabelle auf verschiedenen Knoten gespeichert werden – in Fragmenttabellen, so genannten *Shards*.

Hyperscale führt nicht nur SQL-, sondern auch DDL-Anweisungen im gesamten Cluster aus. Daher werden beim Ändern des Schemas einer verteilten Tabelle alle Shards der Tabelle auf allen Workerknoten aktualisiert.

#### <a name="distribution-column"></a>Verteilungsspalte

Hyperscale verwendet algorithmisches Sharding, um Shards Zeilen zuzuweisen. Die Zuweisung erfolgt deterministisch basierend auf dem Wert einer Tabellenspalte, die als *Verteilungsspalte* bezeichnet wird. Der Clusteradministrator muss diese Spalte beim Verteilen einer Tabelle festlegen.
Für Leistung und Funktionalität ist es wichtig, die richtige Entscheidung zu treffen.

### <a name="type-2-reference-tables"></a>Typ 2: Verweistabellen

Eine Verweistabelle ist ein Typ von verteilter Tabelle, deren gesamter Inhalt in einem einzigen Shard konzentriert ist. Der Shard wird auf jedem Worker repliziert, sodass Abfragen für jeden Worker lokal auf die Verweisinformationen zugreifen können, ohne den Netzwerkaufwand für das Anfordern von Zeilen von einem anderen Knoten. Verweistabellen weisen keine Verteilungsspalte auf, weil nicht zwischen separaten Shards pro Zeile unterschieden werden muss.

Verweistabellen sind in der Regel klein. Sie werden zum Speichern von Daten verwendet, die für Abfragen relevant sind, die auf allen Workerknoten ausgeführt werden. Beispiele hierfür sind Enumerationswerte wie Auftragsstatus oder Produktkategorien.

### <a name="type-3-local-tables"></a>Typ 3: Lokale Tabellen

Wenn Sie Hyperscale verwenden, handelt es sich bei dem Koordinatorknoten, mit dem Sie eine Verbindung herstellen, um eine reguläre PostgreSQL-Datenbank. Sie können auf dem Koordinatorknoten normale Tabellen erstellen und diese nicht horizontal partitionieren.

Kleine Verwaltungstabellen, die nicht an Join-Abfragen teilnehmen, würden sich gut als lokale Tabellen eignen. Ein Beispiel hierfür ist eine Benutzertabelle für die Anmeldung und Authentifizierung von Anwendungen.

## <a name="shards"></a>Shards

Im vorherigen Abschnitt wurde beschrieben, wie verteilte Tabellen als Shards auf Workerknoten gespeichert werden. In diesem Abschnitt werden die technischen Details näher erläutert.

Die Metadatentabelle `pg_dist_shard` auf dem Koordinatorknoten enthält eine Zeile für jeden Shard jeder verteilten Tabelle im System. Die Zeile stimmt mit einer Shard-ID mit einem Bereich von ganzen Zahlen in einem Hashbereich (shardminvalue, shardmaxvalue) überein:

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

Wenn der Koordinatorknoten ermitteln möchte, welcher Shard eine Zeile von `github_events` enthält, berechnet er den Hashwert der Verteilungsspalte in der Zeile und überprüft, welcher Bereich des Shards den Hashwert enthält. (Die Bereiche sind so definiert, dass das Abbild der Hashfunktion ihre disjunkte Vereinigung ist.)

### <a name="shard-placements"></a>Shardplatzierungen

Angenommen, Shard 102027 ist der betreffenden Zeile zugeordnet. Die Zeile wird in einer Tabelle mit dem Namen `github_events_102027` in einem der Worker gelesen oder geschrieben. Um welchen Worker handelt es sich dabei? Das wird vollständig durch die Metadatentabellen bestimmt, und die Zuordnung von Shard zu Worker wird als Shard*platzierung* bezeichnet.

Der Koordinatorknoten schreibt Abfragen in Fragmente um, die auf die bestimmten Tabellen verweisen (z. B. `github_events_102027`), und führt diese Fragmente auf den entsprechenden Workerknoten aus. Nachfolgend sehen Sie ein Beispiel für eine Abfrage, die im Hintergrund ausgeführt wird, um den Knoten zu ermitteln, der die Shard-ID 102027 enthält.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

    ┌─────────┬───────────┬──────────┐
    │ shardid │ nodename  │ nodeport │
    ├─────────┼───────────┼──────────┤
    │  102027 │ localhost │     5433 │
    └─────────┴───────────┴──────────┘

## <a name="next-steps"></a>Nächste Schritte
- [Auswählen einer Verteilungsspalte](concepts-hyperscale-choose-distribution-column.md) für verteilte Tabellen
