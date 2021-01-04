---
title: SQL-Funktionen – Hyperscale (Citus) – Azure Database for PostgreSQL
description: Funktionen in der SQL-API für Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: f324ef44d002f50bf27c08072e904c1d92b5512f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026232"
---
# <a name="functions-in-the-hyperscale-citus-sql-api"></a>Funktionen in der SQL-API für Hyperscale (Citus)

Dieser Abschnitt enthält Referenzinformationen zu den benutzerdefinierten Funktionen, die von Hyperscale (Citus) bereitgestellt werden. Diese Funktionen helfen dabei, für Hyperscale (Citus) neben den Standard-SQL-Befehlen zusätzliche verteilte Funktionalität bereitzustellen.

> [!NOTE]
>
> Hyperscale (Citus)-Servergruppen, auf denen ältere Versionen der Citus-Engine ausgeführt werden, bieten möglicherweise nicht alle unten aufgeführten Funktionen.

## <a name="table-and-shard-ddl"></a>Tabellen- und Shard-DDL

### <a name="create_distributed_table"></a>create\_distributed\_table

Die Funktion create\_distributed\_table() wird verwendet, um eine verteilte Tabelle zu definieren und deren Shards zu erstellen, wenn es sich um eine Tabelle mit Hashverteilung handelt. Diese Funktion nimmt einen Tabellennamen, die Verteilungsspalte und eine optionale Verteilungsmethode an und fügt geeignete Metadaten ein, um die Tabelle als verteilt zu kennzeichnen. Die Funktion ist standardmäßig auf die Verteilung „hash“ festgelegt, wenn keine Verteilungsmethode angegeben wird. Wenn die Tabelle Hashverteilung aufweist, erstellt die Funktion auch Workershards auf der Grundlage der Shardanzahl und der Konfigurationswerte für den Shardreplikationsfaktor. Wenn die Tabelle Zeilen enthält, werden diese automatisch an Workerknoten verteilt.

Diese Funktion ersetzt die Verwendung von master\_create\_distributed\_table(), gefolgt von master\_create\_worker\_shards().

#### <a name="arguments"></a>Argumente

**table\_name:** Der Name der Tabelle, die verteilt werden muss.

**distribution\_column:** Die Spalte, für die die Tabelle verteilt werden soll.

**distribution\_type:** (Optional) Die Methode, nach der die Tabelle verteilt werden soll. Zulässige Werte sind „append“ oder „hash“ mit dem Standardwert „hash“.

**colocate\_with:** (Optional) Schließt die aktuelle Tabelle in die Colocationsgruppe einer anderen Tabelle ein. Standardmäßig wird für Tabellen Colocation verwendet, wenn sie durch Spalten desselben Typs verteilt werden, die gleiche Shardanzahl und denselben Replikationsfaktor aufweisen. Mögliche Werte für `colocate_with` sind `default`, `none`, um eine neue Colocationsgruppe zu starten, oder der Name einer anderen Tabelle, für die Colocation mit dieser Tabelle verwendet werden soll.  (Weitere Informationen finden Sie unter [Tabellencolocation](concepts-hyperscale-colocation.md).)

Beachten Sie, dass der Standardwert `colocate_with` eine implizite Colocation vornimmt. [Colocation](concepts-hyperscale-colocation.md) kann eine hervorragende Lösung sein, wenn Tabellen in Beziehung stehen oder verknüpft werden sollen.  Wenn jedoch zwei Tabellen nicht miteinander in Beziehung stehen, aber zufällig den gleichen Datentyp für ihre Verteilungsspalten verwenden, kann die Leistung bei [Shardausgleich](howto-hyperscale-scale-rebalance.md) durch eine versehentliche Colocation beeinträchtigt werden.  Die Tabellenshards werden unnötig in einer \"Kaskade\" miteinander verschoben.

Wenn eine neue verteilte Tabelle nicht in Beziehung mit anderen Tabellen steht, ist es am besten, `colocate_with => 'none'`anzugeben.

#### <a name="return-value"></a>Rückgabewert

Nicht zutreffend

#### <a name="example"></a>Beispiel

Dieses Beispiel informiert die Datenbank, dass die Tabelle github\_events durch Hash auf die Spalte repo\_id verteilt werden soll.

```postgresql
SELECT create_distributed_table('github_events', 'repo_id');

-- alternatively, to be more explicit:
SELECT create_distributed_table('github_events', 'repo_id',
                                colocate_with => 'github_repo');
```

### <a name="create_reference_table"></a>create\_reference\_table

Die create\_reference\_table()-Funktion wird verwendet, um eine kleine Verweis- oder Dimensionstabelle zu definieren. Diese Funktion nimmt einen Tabellennamen an und erstellt eine verteilte Tabelle mit nur einem Shard, der auf jeden Workerknoten repliziert wird.

#### <a name="arguments"></a>Argumente

**table\_name:** Der Name der kleinen Dimensions- oder Verweistabelle, die verteilt werden muss.

#### <a name="return-value"></a>Rückgabewert

Nicht zutreffend

#### <a name="example"></a>Beispiel

Dieses Beispiel informiert die Datenbank, dass die Tabelle „nation“ als Verweistabelle definiert werden sollte.

```postgresql
SELECT create_reference_table('nation');
```

### <a name="upgrade_to_reference_table"></a>upgrade\_to\_reference\_table

Die upgrade\_to\_reference\_table()-Funktion nimmt eine vorhandene verteilte Tabelle mit einer Shardanzahl von 1 an und aktualisiert sie in eine erkannte Verweistabelle. Nach dem Aufruf dieser Funktion wirkt die Tabelle so, als ob sie mit [create_reference_table](#create_reference_table) erstellt worden wäre.

#### <a name="arguments"></a>Argumente

**table\_name:** Der Name der verteilten Tabelle (mit Shardanzahl = 1), die als Verweistabelle verteilt wird.

#### <a name="return-value"></a>Rückgabewert

Nicht zutreffend

#### <a name="example"></a>Beispiel

Dieses Beispiel informiert die Datenbank, dass die Tabelle „nation“ als Verweistabelle definiert werden sollte.

```postgresql
SELECT upgrade_to_reference_table('nation');
```

### <a name="mark_tables_colocated"></a>mark\_tables\_colocated

Die \_tables\_colocated()-Funktion nimmt eine verteilte Tabelle (die Quelle) und eine Liste von anderen Tabellen (die Ziele) an und fügt die Ziele in die gleiche Colocationsgruppe wie die Quelle ein. Wenn sich die Quelle noch nicht in einer Gruppe befindet, erstellt diese Funktion eine Gruppe und weist ihr die Quelle und die Ziele zu.

Die Colocation von Tabellen sollte zur Zeit der Tabellenverteilung über den Parameter `colocate_with` von [create_distributed_table](#create_distributed_table) erfolgen, aber `mark_tables_colocated` kann diese Aufgabe später übernehmen, falls erforderlich.

#### <a name="arguments"></a>Argumente

**source\_table\_name:** Der Name der verteilten Tabelle, deren Colocationsgruppe die Ziele zugewiesen werden, der übereinstimmen soll.

**target\_table\_names:** Ein Array von Namen der verteilten Zieltabellen, das nicht leer sein darf. Diese verteilten Tabellen müssen der Quelltabelle in den folgenden Elementen entsprechen:

> -   distribution-Methode
> -   Verteilungsspaltentyp
> -   Replikationstyp
> -   Shardanzahl

Wenn keines der oben genannten Elemente zutrifft, gibt Hyperscale (Citus) einen Fehler aus. Beispielsweise führt der Versuch, für die Tabellen `apples` und `oranges`, deren Verteilungsspaltentypen sich unterscheiden, eine Colocation auszuführen, zu folgenden Ergebnissen:

```
ERROR:  XX000: cannot colocate tables apples and oranges
DETAIL:  Distribution column types don't match for apples and oranges.
```

#### <a name="return-value"></a>Rückgabewert

Nicht zutreffend

#### <a name="example"></a>Beispiel

In diesem Beispiel werden `products` und `line_items` in derselben Colocationsgruppe wie `stores` platziert. Das Beispiel geht davon aus, dass diese Tabellen alle auf eine Spalte mit übereinstimmendem Typ verteilt werden, sehr wahrscheinlich eine \"store id.\"

```postgresql
SELECT mark_tables_colocated('stores', ARRAY['products', 'line_items']);
```

### <a name="create_distributed_function"></a>create\_distributed\_function

Gibt eine Funktion vom Koordinatorknoten an Worker weiter und markiert sie für verteilte Ausführung. Wenn eine verteilte Funktion für den Koordinator aufgerufen wird, verwendet Hyperscale (Citus) den Wert des \"distribution-Arguments\", um einen Workerknoten zum Ausführen der Funktion auszuwählen. Das Ausführen der Funktion für Worker erhöht die Parallelität und kann den Code in Shards näher an Daten bringen, um geringere Latenz zu erzielen.

Der Postgres-Suchpfad wird während der Ausführung verteilter Funktionen nicht vom Koordinator an Worker weitergegeben. Daher sollte der Code der verteilten Funktion die Namen von Datenbankobjekten als vollqualifizierte Namen angeben. Außerdem werden dem Benutzer keine Benachrichtigungen angezeigt, die von den Funktionen ausgegeben werden.

#### <a name="arguments"></a>Argumente

**function\_name:** Der Name der zu verteilenden Funktion. Der Name muss die Parametertypen der Funktion in Klammern enthalten, da mehrere Funktionen in PostgreSQL denselben Namen haben können. Beispielsweise unterscheidet sich `'foo(int)'` von `'foo(int, text)'`.

**distribution\_arg\_name:** (Optional) Der Argumentname, nach dem verteilt werden soll. Zur einfacheren Handhabung (oder wenn die Funktionsargumente keine Namen besitzen) ist ein Positionsplatzhalter zulässig, z. B. `'$1'`. Wenn dieser Parameter nicht angegeben wird, wird die Funktion, die von `function_name` benannt wird, nur für die Worker erstellt. Wenn Workerknoten zu einem späteren Zeitpunkt hinzugefügt werden, wird die Funktion auf ihnen ebenfalls automatisch erstellt.

**colocate\_with:** (Optional) Wenn die verteilte Funktion Lese- oder Schreibvorgänge für eine verteilte Tabelle (oder allgemeiner für eine Colocationgruppe) ausführt, stellen Sie sicher, dass Sie diese Tabelle mithilfe des Parameters `colocate_with` benennen. Anschließend wird jeder Aufruf der Funktion auf dem Workerknoten ausgeführt, der relevante Shards enthält.

#### <a name="return-value"></a>Rückgabewert

Nicht zutreffend

#### <a name="example"></a>Beispiel

```postgresql
-- an example function which updates a hypothetical
-- event_responses table which itself is distributed by event_id
CREATE OR REPLACE FUNCTION
  register_for_event(p_event_id int, p_user_id int)
RETURNS void LANGUAGE plpgsql AS $fn$
BEGIN
  INSERT INTO event_responses VALUES ($1, $2, 'yes')
  ON CONFLICT (event_id, user_id)
  DO UPDATE SET response = EXCLUDED.response;
END;
$fn$;

-- distribute the function to workers, using the p_event_id argument
-- to determine which shard each invocation affects, and explicitly
-- colocating with event_responses which the function updates
SELECT create_distributed_function(
  'register_for_event(int, int)', 'p_event_id',
  colocate_with := 'event_responses'
);
```

## <a name="metadata--configuration-information"></a>Metadaten/Konfigurationsinformationen

### <a name="master_get_table_metadata"></a>master\_get\_table\_metadata

Die master\_get\_table\_metadata()-Funktion kann verwendet werden, um verteilungsrelevante Metadaten für eine verteilte Tabelle zurückzugeben. Diese Metadaten umfassen die Beziehungs-ID, den Speichertyp, die Verteilungsmethode, die Verteilungsspalte, die Replikationsanzahl, die maximale Shardgröße und die Shardplatzierungsrichtlinie für die Tabelle. Im Hintergrund fragt diese Funktion Metadatentabellen von Citus (Hyperscale) ab, um die erforderlichen Informationen abzurufen, und verkettet sie in einem Tupel, bevor sie an den Benutzer zurückgegeben werden.

#### <a name="arguments"></a>Argumente

**table\_name:** Der Name der verteilten Tabelle, für die Sie Metadaten abrufen möchten.

#### <a name="return-value"></a>Rückgabewert

Ein Tupel, das die folgenden Informationen enthält:

**logical\_relid:** Die OID der verteilten Tabelle. Sie verweist auf die relfilenode-Spalte in der pg\_class-Systemkatalogtabelle.

**part\_storage\_type:** Der für die Tabelle verwendete Speicher. Kann „t“ (Standardtabelle), „f“ (Fremdtabelle) oder „c“ (Spaltentabelle) sein.

**part\_method:** Die Verteilungsmethode, die für die Tabelle verwendet wird. Kann „a“ (append, anfügen) oder „h“ (hash) sein.

**part\_key:** Die Verteilungsspalte für die Tabelle.

**part\_replica\_count:** Die aktuelle Anzahl von Shardreplikationen.

**part\_max\_size:** Die aktuelle maximale Shardgröße in Bytes.

**part\_placement\_policy:** Die Shardplatzierungsrichtlinie, die zum Platzieren der Shards der Tabelle verwendet wird. Kann 1 (local-node-first) oder 2 (round-robin) sein.

#### <a name="example"></a>Beispiel

Das folgende Beispiel ruft die Tabellenmetadaten für die github\_events-Tabelle ab und zeigt diese an.

```postgresql
SELECT * from master_get_table_metadata('github_events');
 logical_relid | part_storage_type | part_method | part_key | part_replica_count | part_max_size | part_placement_policy 
---------------+-------------------+-------------+----------+--------------------+---------------+-----------------------
         24180 | t                 | h           | repo_id  |                  2 |    1073741824 |                     2
(1 row)
```

### <a name="get_shard_id_for_distribution_column"></a>get\_shard\_id\_for\_distribution\_column

Hyperscale (Citus) weist jede Zeile einer verteilten Tabelle einem Shard basierend auf dem Wert der Verteilungsspalte der Zeile und der Verteilungsmethode der Tabelle zu. In den meisten Fällen handelt es sich bei der exakten Zuordnung um Details auf niedriger Ebene, die vom Datenbankadministrator ignoriert werden können. Allerdings kann es hilfreich sein, den Shard einer Zeile zu bestimmen, entweder für manuelle Datenbankverwaltungsaufgaben oder nur aus Neugier. Die `get_shard_id_for_distribution_column`-Funktion stellt diese Informationen für Tabellen mit Hash- und Bereichsverteilung sowie für Verweistabellen bereit. Dies funktioniert nicht für die Anfügeverteilung.

#### <a name="arguments"></a>Argumente

**table\_name:** Die verteilte Tabelle.

**distribution\_value:** Der Wert der Verteilungsspalte.

#### <a name="return-value"></a>Rückgabewert

Die Shard-ID,die Hyperscale (Citus) mit dem Wert der Verteilungsspalte für die angegebene Tabelle verknüpft.

#### <a name="example"></a>Beispiel

```postgresql
SELECT get_shard_id_for_distribution_column('my_table', 4);

 get_shard_id_for_distribution_column
--------------------------------------
                               540007
(1 row)
```

### <a name="column_to_column_name"></a>column\_to\_column\_name

Übersetzt die `partkey`-Spalte von `pg_dist_partition` in einen Textspaltennamen. Die Übersetzung ist hilfreich, um die Verteilungsspalte einer verteilten Tabelle zu ermitteln.

Eine ausführlichere Erläuterung finden Sie unter [Auswählen einer Verteilungsspalte](concepts-hyperscale-choose-distribution-column.md).

#### <a name="arguments"></a>Argumente

**table\_name:** Die verteilte Tabelle.

**column\_var\_text:** Der Wert von `partkey` in der `pg_dist_partition`-Tabelle.

#### <a name="return-value"></a>Rückgabewert

Der Name der Verteilungsspalte von `table_name`.

#### <a name="example"></a>Beispiel

```postgresql
-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

Ausgabe:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ company_id    │
└───────────────┘
```

### <a name="citus_relation_size"></a>citus\_relation\_size

Ruft den von allen Shards der angegebenen verteilten Tabelle verwendeten Speicherplatz auf dem Datenträger ab.
Der Speicherplatz auf dem Datenträger umfasst die Größe der \"Hauptverzweigung,\" schließt jedoch die Sichtbarkeitszuordnung und die Zuordnung des freien Speicherplatzes für die Shards aus.

#### <a name="arguments"></a>Argumente

**logicalrelid:** Der Name einer verteilten Tabelle.

#### <a name="return-value"></a>Rückgabewert

Die Größe in Bytes als bigint-Wert.

#### <a name="example"></a>Beispiel

```postgresql
SELECT pg_size_pretty(citus_relation_size('github_events'));
```

```
pg_size_pretty
--------------
23 MB
```

### <a name="citus_table_size"></a>citus\_table\_size

Ruft den Speicherplatz auf dem Datenträger ab, der von allen Shards der angegebenen verteilten Tabelle verwendet wird, ausschließlich der Indizes (jedoch einschließlich TOAST, Zuordnung des freien Speicherplatzes und Sichtbarkeitszuordnung).

#### <a name="arguments"></a>Argumente

**logicalrelid:** Der Name einer verteilten Tabelle.

#### <a name="return-value"></a>Rückgabewert

Die Größe in Bytes als bigint-Wert.

#### <a name="example"></a>Beispiel

```postgresql
SELECT pg_size_pretty(citus_table_size('github_events'));
```

```
pg_size_pretty
--------------
37 MB
```

### <a name="citus_total_relation_size"></a>citus\_total\_relation\_size

Ruft den Speicherplatz auf dem Datenträger ab, der von allen Shards der angegebenen verteilten Tabelle verwendet wird, einschließlich aller Indizes und TOAST-Daten.

#### <a name="arguments"></a>Argumente

**logicalrelid:** Der Name einer verteilten Tabelle.

#### <a name="return-value"></a>Rückgabewert

Die Größe in Bytes als bigint-Wert.

#### <a name="example"></a>Beispiel

```postgresql
SELECT pg_size_pretty(citus_total_relation_size('github_events'));
```

```
pg_size_pretty
--------------
73 MB
```

### <a name="citus_stat_statements_reset"></a>citus\_stat\_statements\_reset

Entfernt alle Zeilen aus [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table).
Diese Funktion funktioniert unabhängig von `pg_stat_statements_reset()`. Um alle Statistiken zurückzusetzen, rufen Sie beide Funktionen auf.

#### <a name="arguments"></a>Argumente

Nicht zutreffend

#### <a name="return-value"></a>Rückgabewert

Keiner

## <a name="server-group-management-and-repair"></a>Verwaltung und Reparatur von Servergruppen

### <a name="master_copy_shard_placement"></a>master\_copy\_shard\_placement

Wenn eine Shardplatzierung bei einem Änderungsbefehl oder einem DDL-Vorgang nicht aktualisiert werden kann, wird sie als inaktiv markiert. Die master\_copy\_shard\_placement-Funktion kann dann aufgerufen werden, um eine inaktive Shardplatzierung mithilfe von Daten aus einer fehlerfreien Platzierung zu reparieren.

Zum Reparieren eines Shards löscht die Funktion zuerst die fehlerhafte Shardplatzierung und erstellt sie mithilfe des Schemas für den Koordinator neu. Nachdem die Shardplatzierung erstellt wurde, kopiert die Funktion Daten aus der fehlerfreien Platzierung und aktualisiert die Metadaten, um die neue Shardplatzierung als fehlerfrei zu markieren. Diese Funktion stellt sicher, dass der Shard vor gleichzeitigen Änderungen während der Reparatur geschützt wird.

#### <a name="arguments"></a>Argumente

**shard\_id:** Die ID des zu reparierenden Shards.

**source\_node\_name:** Der DNS-Name des Knotens, auf dem die fehlerfreie Shardplatzierung vorhanden ist (\"Quell\"knoten).

**source\_node\_port:** Der Port auf dem Quellworkerknoten, auf dem der Datenbankserver lauscht.

**target\_node\_name:** Der DNS-Name des Knotens, auf dem die ungültige Shardplatzierung vorhanden ist (\"Ziel\"knoten).

**target\_node\_port:** Der Port auf dem Zielworkerknoten, auf dem der Datenbankserver lauscht.

#### <a name="return-value"></a>Rückgabewert

Nicht zutreffend

#### <a name="example"></a>Beispiel

Im folgenden Beispiel wird eine inaktive Shardplatzierung von Shard 12345 repariert, die sich auf dem Datenbankserver befindet, der für „bad\_host“ an Port 5432 ausgeführt wird. Um sie zu reparieren, werden Daten aus einer fehlerfreien Shardplatzierung auf dem Server verwendet, der für „good\_host“ an Port 5432 ausgeführt wird.
5432.

```postgresql
SELECT master_copy_shard_placement(12345, 'good_host', 5432, 'bad_host', 5432);
```

### <a name="master_move_shard_placement"></a>master\_move\_shard\_placement

Diese Funktion verschiebt einen angegebenen Shard (und darin verbundene Shards) von einem Knoten auf einen anderen. Sie wird in der Regel indirekt beim Shardausgleich verwendet, anstatt direkt von einem Datenbankadministrator aufgerufen zu werden.

Es gibt zwei Möglichkeiten, die Daten zu verschieben: blockierend oder nicht blockierend. Der Blockierungsansatz bedeutet, dass während der Verschiebung alle Änderungen am Shard angehalten werden.
Die zweite Möglichkeit, mit der das Blockieren von Shardschreibvorgängen vermieden wird, beruht auf logischer Postgres 10-Replikation.

Nach einem erfolgreichen Verschiebevorgang werden Shards im Quellknoten gelöscht. Wenn die Verschiebung zu einem beliebigen Zeitpunkt fehlschlägt, löst diese Funktion einen Fehler aus, und die Quell- und Zielknoten bleiben unverändert.

#### <a name="arguments"></a>Argumente

**shard\_id:** Die ID des zu verschiebenden Shards.

**source\_node\_name:** Der DNS-Name des Knotens, auf dem die fehlerfreie Shardplatzierung vorhanden ist (\"Quell\"knoten).

**source\_node\_port:** Der Port auf dem Quellworkerknoten, auf dem der Datenbankserver lauscht.

**target\_node\_name:** Der DNS-Name des Knotens, auf dem die ungültige Shardplatzierung vorhanden ist (\"Ziel\"knoten).

**target\_node\_port:** Der Port auf dem Zielworkerknoten, auf dem der Datenbankserver lauscht.

**shard\_transfer\_mode:** (Optional) Geben Sie die Replikationsmethode an: logische PostgreSQL-Replikation oder einen workerübergreifenden COPY-Befehl. Mögliche Werte sind:

> -   `auto`: Erfordert Replikatidentität, wenn logische Replikation möglich ist, andernfalls wird das Legacyverhalten (für Shardreparatur z. B. PostgreSQL 9.6) verwendet. Dies ist der Standardwert.
> -   `force_logical`: Verwendet logische Replikation auch dann, wenn die Tabelle keine Replikatidentität besitzt. Alle gleichzeitigen update/delete-Anweisungen für die Tabelle schlagen während der Replikation fehl.
> -   `block_writes`: Verwenden Sie COPY (blockierende Schreibvorgänge) für Tabellen, die keinen Primärschlüssel oder keine Replikatidentität aufweisen.

#### <a name="return-value"></a>Rückgabewert

Nicht zutreffend

#### <a name="example"></a>Beispiel

```postgresql
SELECT master_move_shard_placement(12345, 'from_host', 5432, 'to_host', 5432);
```

### <a name="rebalance_table_shards"></a>rebalance\_table\_shards

Die rebalance\_table\_shards()-Funktion verschiebt Shards der angegebenen Tabelle, damit sie gleichmäßig auf die Worker verteilt werden. Die Funktion berechnet zunächst die Liste der erforderlichen Verschiebungen, um sicherzustellen, dass die Servergruppe innerhalb des angegebenen Schwellenwerts ausgeglichen ist. Anschließend werden die Shardplatzierungen nacheinander vom Quellknoten auf den Zielknoten verschoben, und die entsprechenden Shardmetadaten werden aktualisiert, um die Verschiebung widerzuspiegeln.

Jedem Shard werden Kosten zugewiesen, wenn ermittelt wird, ob Shards \"gleichmäßig verteilt sind.\" Standardmäßig weist jeder Shard die gleichen Kosten (den Wert 1) auf. Daher ist die Verteilung zum Ausgleichen der Kosten auf mehrere Worker identisch mit dem Ausgleichen der Anzahl der Shards für jeden Worker. Die konstante Kostenstrategie wird als \"by\_shard\_count\" bezeichnet und ist die standardmäßige Ausgleichsstrategie.

Die Standardstrategie ist unter den folgenden Umständen angemessen:

*  Die Shards weisen ungefähr dieselbe Größe auf.
*  Die Shards erhalten ungefähr die gleiche Menge an Datenverkehr.
*  Die Workerknoten weisen alle dieselbe Größe bzw. denselben Typ auf.
*  Shards wurden nicht an bestimmte Worker angeheftet.

Wenn eine dieser Annahmen nicht zutrifft, dann kann der standardmäßige Neuausgleich zu einem schlechten Plan führen. In diesem Fall können Sie die Strategie anpassen, indem Sie den Parameter `rebalance_strategy` verwenden.

Es empfiehlt sich, [get_rebalance_table_shards_plan](#get_rebalance_table_shards_plan) aufzurufen, bevor Sie rebalance\_table\_shards ausführen, um die auszuführenden Aktionen anzuzeigen und zu überprüfen.

#### <a name="arguments"></a>Argumente

**table\_name:** (Optional) Der Name der Tabelle, deren Shards ausgeglichen werden müssen. Wenn der Wert NULL ist, werden alle vorhandenen Colocationsgruppen ausgeglichen.

**threshold:** (Optional) Eine Gleitkommazahl zwischen 0,0 und 1,0, die das maximale Differenzverhältnis der Knotenverwendung von der durchschnittlichen Auslastung angibt. Wenn Sie z. B. 0,1 angeben, versucht der Shardrebalancer, alle Knoten so auszugleichen, dass sie die gleiche Anzahl von Shards (± 10 %) enthalten.
Der Shardrebalancer versucht insbesondere, die Auslastung aller Workerknoten auf den (1 - Schwellenwert) \* average\_utilization \.... (1
+ Schwellenwert) \* average\_utilization zu konvergieren.

**max\_shard\_moves:** (Optional) Die maximale Anzahl der zu verschiebenden Shards.

**excluded\_shard\_list:** (Optional) Bezeichner von Shards, die während des Vorgangs zum erneuten Ausgleichen nicht verschoben werden sollten.

**shard\_transfer\_mode:** (Optional) Geben Sie die Replikationsmethode an: logische PostgreSQL-Replikation oder einen workerübergreifenden COPY-Befehl. Mögliche Werte sind:

> -   `auto`: Erfordert Replikatidentität, wenn logische Replikation möglich ist, andernfalls wird das Legacyverhalten (für Shardreparatur z. B. PostgreSQL 9.6) verwendet. Dies ist der Standardwert.
> -   `force_logical`: Verwendet logische Replikation auch dann, wenn die Tabelle keine Replikatidentität besitzt. Alle gleichzeitigen update/delete-Anweisungen für die Tabelle schlagen während der Replikation fehl.
> -   `block_writes`: Verwenden Sie COPY (blockierende Schreibvorgänge) für Tabellen, die keinen Primärschlüssel oder keine Replikatidentität aufweisen.

**drain\_only:** (Optional) Wenn TRUE, werden Shards von Workerknoten verschoben, für die `shouldhaveshards` in [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table) auf FALSE festgelegt ist. Andere Shards werden nicht verschoben.

**rebalance\_strategy:** (Optional) Der Name einer Strategie in [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table).
Wenn dieses Argument ausgelassen wird, wählt die Funktion die Standardstrategie (wie in der Tabelle angegeben) aus.

#### <a name="return-value"></a>Rückgabewert

Nicht zutreffend

#### <a name="example"></a>Beispiel

Im folgenden Beispiel wird versucht, die Shards der github\_events-Tabelle innerhalb des Standardschwellenwerts auszugleichen.

```postgresql
SELECT rebalance_table_shards('github_events');
```

In diesem Beispiel wird versucht, die github\_events-Tabelle neu auszugleichen, ohne Shards mit der ID 1 und 2 zu verschieben.

```postgresql
SELECT rebalance_table_shards('github_events', excluded_shard_list:='{1,2}');
```

### <a name="get_rebalance_table_shards_plan"></a>get\_rebalance\_table\_shards\_plan

Gibt die geplanten Shardbewegungen von [rebalance_table_shards](#rebalance_table_shards) aus, ohne sie auszuführen.
Es ist zwar unwahrscheinlich, aber get\_rebalance\_table\_shards\_plan kann einen etwas anderen Plan ausgeben als ein rebalance\_table\_shards-Aufruf mit den gleichen Argumenten. Die Aufrufe werden nicht gleichzeitig ausgeführt, sodass sich Fakten zur Servergruppe \-z. B. der Speicherplatz auf dem Datenträger\- möglicherweise zwischen den Aufrufen unterscheiden.

#### <a name="arguments"></a>Argumente

Die gleichen Argumente wie für rebalance\_table\_shards: relation, threshold, max\_shard\_moves, excluded\_shard\_list und drain\_only. Die Bedeutung der Argumente finden Sie in der Dokumentation der jeweiligen Funktion.

#### <a name="return-value"></a>Rückgabewert

Tupel, die die folgenden Spalten enthalten:

-   **table\_name**: Die Tabelle, deren Shards verschoben werden.
-   **shardid**: Der jeweilige Shard.
-   **shard\_size**: Die Größe in Bytes.
-   **sourcename**: Der Hostname des Quellknotens.
-   **sourceport**: Der Port des Quellknotens.
-   **targetname**: Der Hostname des Zielknotens.
-   **targetport**: Der Port des Zielknotens.

### <a name="get_rebalance_progress"></a>get\_rebalance\_progress

Nachdem ein Shardausgleich begonnen wurde, listet die `get_rebalance_progress()` -Funktion den Fortschritt aller beteiligten Shards auf. Sie überwacht die geplanten und von `rebalance_table_shards()` ausgeführten Verschiebungen.

#### <a name="arguments"></a>Argumente

Nicht zutreffend

#### <a name="return-value"></a>Rückgabewert

Tupel, die die folgenden Spalten enthalten:

-   **sessionid**: Die Postgres-PID des Ausgleichsmonitors.
-   **table\_name**: Die Tabelle, deren Shards verschoben werden.
-   **shardid**: Der jeweilige Shard.
-   **shard\_size**: Die Größe in Bytes.
-   **sourcename**: Der Hostname des Quellknotens.
-   **sourceport**: Der Port des Quellknotens.
-   **targetname**: Der Hostname des Zielknotens.
-   **targetport**: Der Port des Zielknotens.
-   **progress**: 0 = auf Verschiebung wird gewartet, 1 = wird verschoben; 2 = abgeschlossen

#### <a name="example"></a>Beispiel

```sql
SELECT * FROM get_rebalance_progress();
```

```
┌───────────┬────────────┬─────────┬────────────┬───────────────┬────────────┬───────────────┬────────────┬──────────┐
│ sessionid │ table_name │ shardid │ shard_size │  sourcename   │ sourceport │  targetname   │ targetport │ progress │
├───────────┼────────────┼─────────┼────────────┼───────────────┼────────────┼───────────────┼────────────┼──────────┤
│      7083 │ foo        │  102008 │    1204224 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102009 │    1802240 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102018 │     614400 │ n2.foobar.com │       5432 │ n4.foobar.com │       5432 │        1 │
│      7083 │ foo        │  102019 │       8192 │ n3.foobar.com │       5432 │ n4.foobar.com │       5432 │        2 │
└───────────┴────────────┴─────────┴────────────┴───────────────┴────────────┴───────────────┴────────────┴──────────┘
```

### <a name="citus_add_rebalance_strategy"></a>citus\_add\_rebalance\_strategy

Fügt [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) eine Zeile hinzu.

#### <a name="arguments"></a>Argumente

Weitere Informationen zu diesen Argumenten finden Sie in den entsprechenden Spaltenwerten in `pg_dist_rebalance_strategy`.

**name:** Bezeichner für die neue Strategie.

**shard\_cost\_function:** Identifiziert die Funktion, mit der die \"Kosten\" der einzelnen Shards bestimmt werden.

**node\_capacity\_function:** Identifiziert die Funktion zum Messen der Knotenkapazitätapazität.

**shard\_allowed\_on\_node\_function:** Identifiziert die Funktion, die ermittelt, welche Shards auf welchen Knoten platziert werden können.

**default\_threshold:** Ein Gleitkommaschwellenwert, der optimiert, wie genau die kumulierten Shardkosten zwischen den Knoten ausgeglichen werden sollen.

**minimum\_threshold:** (Optional) Eine Schutzspalte, die den Mindestwert enthält, der für das Schwellenwertargument von rebalance\_table\_shards() zulässig ist. Der Standardwert ist 0.

#### <a name="return-value"></a>Rückgabewert

Nicht zutreffend

### <a name="citus_set_default_rebalance_strategy"></a>citus\_set\_default\_rebalance\_strategy

Aktualisiert die [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table)-Tabelle und ändern die durch das Argument benannte Strategie so, dass sie beim erneuten Ausgleichen von Shards als Standard ausgewählt wird.

#### <a name="arguments"></a>Argumente

**name:** Der Name der Strategie in pg\_dist\_rebalance\_strategy.

#### <a name="return-value"></a>Rückgabewert

Nicht zutreffend

#### <a name="example"></a>Beispiel

```postgresql
SELECT citus_set_default_rebalance_strategy('by_disk_size');
```

### <a name="citus_remote_connection_stats"></a>citus\_remote\_connection\_stats

Die citus\_remote\_connection\_stats-Funktion zeigt die Anzahl der aktiven Verbindungen mit den einzelnen Remoteknoten an.

#### <a name="arguments"></a>Argumente

Nicht zutreffend

#### <a name="example"></a>Beispiel

```postgresql
SELECT * from citus_remote_connection_stats();
```

```
    hostname    | port | database_name | connection_count_to_node
----------------+------+---------------+--------------------------
 citus_worker_1 | 5432 | postgres      |                        3
(1 row)
```

### <a name="master_drain_node"></a>master\_drain\_node

Mit der master\_drain\_node()-Funktion werden Shards aus dem angegebenen Knoten und auf andere Knoten verschoben, für die `shouldhaveshards` in [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table) auf TRUE festgelegt ist. Rufen Sie die Funktion auf, bevor Sie einen Knoten aus der Servergruppe entfernen und den physischen Server des Knotens deaktivieren.

#### <a name="arguments"></a>Argumente

**nodename:** Der hostname-Name des auszugleichenden Knotens.

**nodeport:** Die Portnummer des auszugleichenden Knotens.

**shard\_transfer\_mode:** (Optional) Geben Sie die Replikationsmethode an: logische PostgreSQL-Replikation oder einen workerübergreifenden COPY-Befehl. Mögliche Werte sind:

> -   `auto`: Erfordert Replikatidentität, wenn logische Replikation möglich ist, andernfalls wird das Legacyverhalten (für Shardreparatur z. B. PostgreSQL 9.6) verwendet. Dies ist der Standardwert.
> -   `force_logical`: Verwendet logische Replikation auch dann, wenn die Tabelle keine Replikatidentität besitzt. Alle gleichzeitigen update/delete-Anweisungen für die Tabelle schlagen während der Replikation fehl.
> -   `block_writes`: Verwenden Sie COPY (blockierende Schreibvorgänge) für Tabellen, die keinen Primärschlüssel oder keine Replikatidentität aufweisen.

**rebalance\_strategy:** (Optional) Der Name einer Strategie in [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table).
Wenn dieses Argument ausgelassen wird, wählt die Funktion die Standardstrategie (wie in der Tabelle angegeben) aus.

#### <a name="return-value"></a>Rückgabewert

Nicht zutreffend

#### <a name="example"></a>Beispiel

Dies sind die typischen Schritte zum Entfernen eines einzelnen Knotens (z. B. „10.0.0.1“ an einem PostgreSQL-Standardport):

1.  Entfernen Sie den Knoten.

    ```postgresql
    SELECT * from master_drain_node('10.0.0.1', 5432);
    ```

2.  Warten Sie, bis der Befehl abgeschlossen wurde.

3.  Entfernen Sie den Knoten.

Beim Ausgleichen mehrerer Knoten empfiehlt es sich, stattdessen [rebalance_table_shards](#rebalance_table_shards) zu verwenden. Auf diese Weise kann Hyperscale (Citus) vorausplanen und Shards so wenig wie möglich verschieben.

1.  Führen Sie Folgendes für jeden Knoten aus, den Sie entfernen möchten:

    ```postgresql
    SELECT * FROM master_set_node_property(node_hostname, node_port, 'shouldhaveshards', false);
    ```

2.  Gleichen Sie alle Knoten gleichzeitig mit [rebalance_table_shards](#rebalance_table_shards) aus.

    ```postgresql
    SELECT * FROM rebalance_table_shards(drain_only := true);
    ```

3.  Warten Sie, bis der Ausgleich abgeschlossen ist.

4.  Entfernen Sie die Knoten.

### <a name="replicate_table_shards"></a>replicate\_table\_shards

Die replicate\_table\_shards()-Funktion repliziert die unterreplizierten Shards der angegebenen Tabelle. Die Funktion berechnet zunächst die Liste der unterreplizierten Shards und der Speicherorte, von denen sie für die Replikation abgerufen werden können. Die Funktion kopiert dann diese Shards und aktualisiert die entsprechenden Shardmetadaten, um den Kopiervorgang widerzuspiegeln.

#### <a name="arguments"></a>Argumente

**table\_name:** Der Name der Tabelle, deren Shards repliziert werden müssen.

**shard\_replication\_factor:** (Optional) Der gewünschte Replikationsfaktor, der für jeden Shard erreicht werden soll.

**max\_shard\_copies:** (Optional) Maximale Anzahl der zu kopierenden Shards, um den gewünschten Replikationsfaktor zu erreichen.

**excluded\_shard\_list:** (Optional) Bezeichner von Shards, die während des Replikationsvorgangs nicht kopiert werden sollen.

#### <a name="return-value"></a>Rückgabewert

Nicht zutreffend

#### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird versucht, die Shards der github\_events-Tabelle in shard\_replication\_factor zu replizieren.

```postgresql
SELECT replicate_table_shards('github_events');
```

In diesem Beispiel wird versucht, die Shards der github\_events-Tabelle mit maximal 10 Shardkopien auf den gewünschten Replikationsfaktor zu bringen. Der Rebalancer kopiert maximal 10 Shards, wenn versucht wird, den gewünschten Replikationsfaktor zu erreichen.

```postgresql
SELECT replicate_table_shards('github_events', max_shard_copies:=10);
```

### <a name="isolate_tenant_to_new_shard"></a>isolate\_tenant\_to\_new\_shard

Diese Funktion erstellt einen neuen Shard, der Zeilen mit einem bestimmten einzelnen Wert in der Verteilungsspalte enthält. Dies ist besonders nützlich für den Anwendungsfall von mehrinstanzenfähigem Hyperscale (Citus), bei dem ein großer Mandant allein in seinem eigenen Shard und damit auf seinem eigenen physischen Knoten platziert werden kann.

#### <a name="arguments"></a>Argumente

**table\_name:** Der Name der Tabelle, um einen neuen Shard abzurufen.

**tenant\_id:** Der Wert der Verteilungsspalte, der dem neuen Shard zugewiesen wird.

**cascade\_option:** (Optional) Wenn diese Option auf \"CASCADE\" festgelegt ist, wird auch einen Shard aus allen Tabellen in der [Colocationsguppe](concepts-hyperscale-colocation.md) der aktuellen Tabelle isoliert.

#### <a name="return-value"></a>Rückgabewert

**shard\_id:** Die Funktion gibt die eindeutige ID zurück, die dem neu erstellten Shard zugewiesen ist.

#### <a name="examples"></a>Beispiele

Erstellen Sie einen neuen Shard, der die lineitems für den Mandanten 135 enthalten soll:

```postgresql
SELECT isolate_tenant_to_new_shard('lineitem', 135);
```

```
┌─────────────────────────────┐
│ isolate_tenant_to_new_shard │
├─────────────────────────────┤
│                      102240 │
└─────────────────────────────┘
```

## <a name="next-steps"></a>Nächste Schritte

* Viele der Funktionen in diesem Artikel ändern [Metadatentabellen](reference-hyperscale-metadata.md) des Systems.
* [Serverparameter](reference-hyperscale-parameters.md) passen das Verhalten einiger Funktionen an.
