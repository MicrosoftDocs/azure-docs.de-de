---
title: Ändern verteilter Tabellen – Hyperscale (Citus) – Azure Database for PostgreSQL
description: SQL-Befehle zum Erstellen und Ändern von verteilten Tabellen – Hyperscale (Citus) – über das Azure-Portal
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: cf9f9ca5b8690a38c6e5aa6f519378c0a2e3a4f2
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026436"
---
# <a name="distribute-and-modify-tables"></a>Verteilen und Ändern von Tabellen

## <a name="distributing-tables"></a>Verteilen von Tabellen

Zum Erstellen einer verteilten Tabelle müssen Sie zunächst das Tabellenschema definieren. Dazu können Sie eine Tabelle mithilfe der Anweisung [CREATE TABLE](http://www.postgresql.org/docs/current/static/sql-createtable.html) über dieselben Schritte erstellen, die Sie bei einer regulären PostgreSQL-Tabelle ausführen.

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    actor jsonb,
    org jsonb,
    created_at timestamp
);
```

Anschließend können Sie mithilfe der Funktion „create\_distributed\_table()“ die Tabellenverteilungsspalte angeben und die Workershards erstellen.

```sql
SELECT create_distributed_table('github_events', 'repo_id');
```

Durch diesen Funktionsaufruf wird Hyperscale (Citus) darüber informiert, dass die Tabelle „github\_events“ auf die Spalte „repo\_id“ verteilt werden soll (durch ein Hashing des Spaltenwerts). Außerdem erstellt die Funktion unter Verwendung der Konfigurationswerte „citus.shard\_count“ und „citus.shard\_replication\_factor“ Shards für die Workerknoten.

Entsprechend des „citus.shard\_count“-Werts wird eine bestimmte Anzahl von Shards erstellt. Jeder Shard umfasst dabei einen Teil des Hashraums und wird basierend auf dem Konfigurationswert „citus.shard\_replication\_factor“ repliziert. Die auf dem Worker erstellten Shardreplikate weisen dieselben Tabellenschema-, Index- und Constraintdefinitionen auf wie die Tabelle des Koordinators. Sobald die Replikate erstellt wurden, speichert die Funktion alle verteilten Metadaten auf dem Koordinator.

Jedem erstellten Shard wird eine eindeutige Shard-ID zugewiesen, und alle zugehörigen Replikate weisen dieselbe Shard-ID auf. Shards werden auf dem Workerknoten als reguläre PostgreSQL-Tabellen mit dem Namen „\'tabellenname\_shardid\'“ dargestellt. Dabei ist „tabellenname“ der Name der verteilten Tabelle, „shardid“ ist die zugewiesene eindeutige ID. Um Befehle für einzelne Shards anzuzeigen oder auszuführen, kann eine Verbindung mit den Postgres-Workerinstanzen hergestellt werden.

Sie können nun Daten in die verteilte Tabelle einfügen und Abfragen für die Inhalte ausführen. Weitere Informationen zu der in diesem Abschnitt verwendeten UDF finden Sie in der Referenz zur [Tabellen- und Shard-DDL](reference-hyperscale-functions.md#table-and-shard-ddl).

### <a name="reference-tables"></a>Verweistabellen

Mit der obigen Methode werden Tabellen auf mehrere horizontale Datenbankpartitionen verteilt.  Eine weitere Möglichkeit ist, Tabellen in einen einzelnen Shard zu verteilen und den Shard auf jedem Workerknoten zu replizieren. Tabellen, die mit dieser Methode verteilt werden, werden als *Verweistabellen* bezeichnet. Sie werden zum Speichern von Daten verwendet, auf die häufig von mehreren Knoten in einem Cluster zugegriffen werden muss.

Nachfolgend sind gängige Szenarien für Verweistabellen aufgeführt:

-   Kleinere Tabellen, die mit größeren verteilten Tabellen verknüpft werden müssen.
-   Tabellen in mehrinstanzenfähigen Apps, die nicht über eine Mandanten-ID-Spalte verfügen oder keinem Mandanten zugeordnet sind. (Während der Migration gilt dies sogar bei einigen Tabellen, die einem Mandanten zugeordnet sind.)
-   Tabellen, die individuelle Constraints für mehrere Spalten benötigen und klein genug sind.

Beispiel: Für eine mehrinstanzenfähige E-Commerce-Website muss die Mehrwertsteuer für Transaktionen in allen zugehörigen Stores berechnet werden. Steuerinformationen sind nicht mandantenspezifisch. Daher ist es sinnvoll, diese Informationen in einer verteilten Tabelle zu speichern. Eine US-spezifische Verweistabelle könnte wie folgt aussehen:

```postgresql
-- a reference table

CREATE TABLE states (
  code char(2) PRIMARY KEY,
  full_name text NOT NULL,
  general_sales_tax numeric(4,3)
);

-- distribute it to all workers

SELECT create_reference_table('states');
```

Abfragen (z. B. zum Berechnen der Mehrwertsteuer für den Inhalt eines Einkaufswagens) können jetzt für die `states`-Tabelle verknüpft werden, ohne dass dadurch die Netzwerkbelastung steigt. Außerdem kann für eine verbesserte Validierung ein Fremdschlüssel zum Statuscode hinzugefügt werden.

Die UDF `create_reference_table` verteilt eine Tabelle nicht nur als einzelnen replizierten Shard, sondern markiert die Tabelle zusätzlich in den Metadatentabellen von Hyperscale (Citus) als Verweistabelle. Hyperscale (Citus) führt bei Änderungen an Tabellen, die auf diese Weise markiert sind, automatisch Zweiphasencommits ([2PC](https://en.wikipedia.org/wiki/Two-phase_commit_protocol)) aus. Dadurch wird eine starke Konsistenz garantiert.

Wenn Sie über eine verteilte Tabelle mit einem einzelnen Shard verfügen, können Sie die Tabelle wie folgt aktualisieren, damit sie als Verweistabelle erkannt wird:

```postgresql
SELECT upgrade_to_reference_table('table_name');
```

Ein weiteres Beispiel zum Verwenden von Verweistabellen finden Sie im [Tutorial zu mehrinstanzenfähigen Datenbanken](tutorial-design-database-hyperscale-multi-tenant.md).

### <a name="distributing-coordinator-data"></a>Verteilen von Koordinatordaten

Wenn eine vorhandene PostgreSQL-Datenbank in einen Koordinatorknoten für einen Hyperscale (Citus)-Cluster umgewandelt wird, können die Daten in den zugehörigen Tabellen effizient und mit minimaler Unterbrechung für eine Anwendung verteilt werden.

Die oben beschriebene Funktion `create_distributed_table` kann sowohl für leere Tabellen als auch für Tabellen mit Inhalten verwendet werden. Bei gefüllten Tabellen werden die Tabellenzeilen automatisch innerhalb des Clusters verteilt. Die Meldung \"HINWEIS: Daten werden aus lokaler Tabelle kopiert...\. gibt Auskunft darüber, ob Daten kopiert werden.\" Beispiel:

```postgresql
CREATE TABLE series AS SELECT i FROM generate_series(1,1000000) i;
SELECT create_distributed_table('series', 'i');
NOTICE:  Copying data from local table...
 create_distributed_table
 --------------------------

 (1 row)
```

Schreibvorgänge für die Tabelle werden bei der Migration von Daten blockiert. Ausstehende Schreibvorgänge werden wie verteilte Abfragen behandelt, sobald die Funktion ein Commit ausführt. (Wenn bei der Funktion ein Fehler auftritt, werden die Abfragen erneut als lokale Abfragen behandelt.) Lesevorgänge können weiter normal ausgeführt werden und werden zu verteilten Abfragen, sobald die Funktion ein Commit ausführt.

Wenn Sie die Tabellen A und B verteilen und Tabelle A einen Fremdschlüssel zu Tabelle B aufweist, verteilen Sie zunächst die als Schlüsselziel definierte Tabelle B. Wenn Sie den Vorgang in der falschen Reihenfolge durchführen, erhalten Sie einen Fehler:

```
ERROR:  cannot create foreign key constraint
DETAIL:  Referenced table must be a distributed table or a reference table.
```

Sollte es nicht möglich sein, die Tabellen in der richtigen Reihenfolge zu verteilen, verwerfen Sie die Fremdschlüssel, verteilen Sie die Tabellen, und erstellen Sie dann die Fremdschlüssel neu.

Bei der Migration von Daten aus einer externen Datenbank – z. B. von Amazon RDS in eine Hyperscale (Citus)-Cloud –, erstellen Sie zunächst die verteilten Hyperscale (Citus)-Tabellen mithilfe von `create_distributed_table` und kopieren dann die Daten in die Tabelle.
Indem Sie die Daten in verteilte Tabellen kopieren, werden Speicherplatzengpässe auf dem Koordinatorknoten verhindert.

## <a name="colocating-tables"></a>Zusammenstellen von Tabellen

Zusammenstellung bedeutet, dass verwandte Informationen auf denselben Computern gespeichert werden. Dadurch werden effiziente Abfragen ermöglicht. Gleichzeitig profitieren Sie für das gesamte Dataset von den Vorteilen einer horizontalen Skalierbarkeit. Weitere Informationen finden Sie unter [Zusammenstellung](concepts-hyperscale-colocation.md).

Tabellen werden in Gruppen zusammengestellt. Um die Zuweisung zu einer Zusammenstellungsgruppe für eine Tabelle manuell zu steuern, verwenden Sie den optionalen Parameter `colocate_with` von `create_distributed_table`. Wenn Sie bei der Tabellenzusammenstellung keine Präferenz haben, lassen Sie diesen Parameter weg. Der Standardwert lautet `'default'`. Mit diesem Wert wird die Tabelle mit einer beliebigen anderen standardmäßigen Zusammenstellungstabelle gruppiert, die denselben Verteilungsspaltentyp, dieselbe Shardanzahl und denselben Replikationsfaktor aufweist. Wenn Sie diese implizite Zusammenstellung außer Kraft setzen möchten, können Sie `update_distributed_table_colocation()` verwenden.

```postgresql
-- these tables are implicitly co-located by using the same
-- distribution column type and shard count with the default
-- co-location group

SELECT create_distributed_table('A', 'some_int_col');
SELECT create_distributed_table('B', 'other_int_col');
```

Ist eine neue Tabelle nicht mit anderen Tabellen in ihrer impliziten Zusammenstellungsgruppe verwandt, geben Sie `colocated_with => 'none'` an.

```postgresql
-- not co-located with other tables

SELECT create_distributed_table('A', 'foo', colocate_with => 'none');
```

Indem Sie Tabellen, die nicht verwandt sind, in eigene Zusammenstellungsgruppen unterteilen, wird die Leistung beim [erneuten Ausgleich von Shards](howto-hyperscale-scale-rebalance.md) verbessert, da Shards innerhalb einer Gruppe gemeinsam verschoben werden müssen.

Wenn Tabellen jedoch verwandt sind (wenn sie z. B. verknüpft werden), kann es sinnvoll sein, diese Tabellen explizit zusammenzustellen. Die Vorteile einer sinnvollen Zusammenstellung sind wichtiger als ein möglicher Overhead beim erneuten Ausgleich.

Für eine explizite Zusammenstellung mehrerer Tabellen verteilen Sie zunächst eine der Tabellen, und platzieren Sie dann die übrigen Tabellen in der entsprechenden Zusammenstellungsgruppe. Beispiel:

```postgresql
-- distribute stores
SELECT create_distributed_table('stores', 'store_id');

-- add to the same group as stores
SELECT create_distributed_table('orders', 'store_id', colocate_with => 'stores');
SELECT create_distributed_table('products', 'store_id', colocate_with => 'stores');
```

Informationen zu Zusammenstellungsgruppen werden in der Tabelle [pg_dist_colocation](reference-hyperscale-metadata.md#colocation-group-table) gespeichert. [pg_dist_partition](reference-hyperscale-metadata.md#partition-table) gibt Auskunft darüber, welche Tabellen welchen Gruppen zugewiesen sind.

## <a name="dropping-tables"></a>Verwerfen von Tabellen

Sie können den PostgreSQL-Standardbefehl „DROP TABLE“ verwenden, um Ihre verteilten Tabellen zu entfernen. Wie bei regulären Tabellen werden mit „DROP TABLE“ alle Indizes, Regeln, Trigger und Constraints entfernt, die für die Zieltabelle vorhanden sind. Darüber hinaus werden die Shards auf den Workerknoten entfernt und die zugehörigen Metadaten bereinigt.

```sql
DROP TABLE github_events;
```

## <a name="modifying-tables"></a>Ändern von Tabellen

Hyperscale (Citus) überträgt automatisch eine Vielzahl von DDL-Anweisungen.
Wenn eine verteilte Tabelle auf dem Koordinatorknoten geändert wird, werden auch die Shards auf den Workern aktualisiert. Bei einigen DDL-Anweisungen ist eine manuelle Übertragung erforderlich, und bestimmte Anweisungen sind nicht zulässig (z. B. Anweisungen zum Ändern einer Verteilungsspalte).
Beim Versuch, eine DDL-Anweisung auszuführen, die nicht automatisch übertragen werden kann, wird ein Fehler ausgegeben. Die Tabellen auf dem Koordinatorknoten werden nicht geändert.

Hier finden Sie eine Referenz zu den Kategorien von DDL-Anweisungen, die übertragen werden.
Die automatische Übertragung kann mit einem [Konfigurationsparameter](reference-hyperscale-parameters.md#citusenable_ddl_propagation-boolean) aktiviert oder deaktiviert werden.

### <a name="addingmodifying-columns"></a>Hinzufügen/Ändern von Spalten

Hyperscale (Citus) überträgt die meisten [ALTER TABLE](https://www.postgresql.org/docs/current/static/ddl-alter.html)-Befehle automatisch. Das Hinzufügen von Spalten oder Ändern ihrer Standardwerte kann wie bei einer PostgreSQL-Datenbank mit einem einzelnen Computer durchgeführt werden:

```postgresql
-- Adding a column

ALTER TABLE products ADD COLUMN description text;

-- Changing default value

ALTER TABLE products ALTER COLUMN price SET DEFAULT 7.77;
```

Auch wesentliche Änderungen an einer vorhandenen Spalte (z. B. das Umbenennen oder das Ändern des Datentyps) sind möglich. Der Datentyp der [Verteilungsspalte](concepts-hyperscale-nodes.md#distribution-column) kann jedoch nicht geändert werden.
Über diese Spalte wird festgelegt, wie Tabellendaten innerhalb des Hyperscale (Citus)-Clusters verteilt werden. Würde der Datentyp dieser Spalte geändert, müssten die Daten verschoben werden.

Ein entsprechender Versuch führt zu einem Fehler:

```postgres
-- assumining store_id is the distribution column
-- for products, and that it has type integer

ALTER TABLE products
ALTER COLUMN store_id TYPE text;

/*
ERROR:  XX000: cannot execute ALTER TABLE command involving partition column
LOCATION:  ErrorIfUnsupportedAlterTableStmt, multi_utility.c:2150
*/
```

### <a name="addingremoving-constraints"></a>Hinzufügen/Entfernen von Constraints

Bei Verwendung von Hyperscale (Citus) profitieren Sie weiterhin von der Sicherheit einer relationalen Datenbank, einschließlich Datenbankconstraints (weitere Informationen finden Sie in der [Dokumentation](https://www.postgresql.org/docs/current/static/ddl-constraints.html) zu PostgreSQL).
Aufgrund des Aufbaus verteilter Systeme erstellt Hyperscale (Citus) keine Querverweise für Eindeutigkeitseinschränkungen oder referentielle Integrität zwischen Workerknoten.

Um einen Fremdschlüssel für zusammengestellte verteilte Tabellen einzurichten, muss der Schlüssel immer die Verteilungsspalte enthalten. Dazu muss der Schlüssel möglicherweise als zusammengesetzter Schlüssel definiert werden.

Fremdschlüssel können in folgenden Situationen erstellt werden:

-   zwischen zwei lokalen (nicht verteilten) Tabellen,
-   zwischen zwei Verweistabellen,
-   zwischen zwei [zusammengestellten](concepts-hyperscale-colocation.md) verteilten Tabellen, wenn der Schlüssel die Verteilungsspalte umfasst, oder
-   als verteilte Tabelle, die auf eine [Verweistabelle](concepts-hyperscale-nodes.md#type-2-reference-tables) verweist

Fremdschlüssel aus Verweistabellen, die auf verteilte Tabellen zeigen, werden nicht unterstützt.

> [!NOTE]
>
> Primärschlüssel und Eindeutigkeitseinschränkungen müssen die Verteilungsspalte umfassen. Wenn Sie zu einer anderen Spalte als der Verteilungsspalte hinzugefügt werden, wird ein Fehler ausgegeben

Dieses Beispiel zeigt, wie Primärschlüssel und Fremdschlüssel für verteilte Tabellen erstellt werden:

```postgresql
--
-- Adding a primary key
-- --------------------

-- We'll distribute these tables on the account_id. The ads and clicks
-- tables must use compound keys that include account_id.

ALTER TABLE accounts ADD PRIMARY KEY (id);
ALTER TABLE ads ADD PRIMARY KEY (account_id, id);
ALTER TABLE clicks ADD PRIMARY KEY (account_id, id);

-- Next distribute the tables

SELECT create_distributed_table('accounts', 'id');
SELECT create_distributed_table('ads',      'account_id');
SELECT create_distributed_table('clicks',   'account_id');

--
-- Adding foreign keys
-- -------------------

-- Note that this can happen before or after distribution, as long as
-- there exists a uniqueness constraint on the target column(s) which
-- can only be enforced before distribution.

ALTER TABLE ads ADD CONSTRAINT ads_account_fk
  FOREIGN KEY (account_id) REFERENCES accounts (id);
ALTER TABLE clicks ADD CONSTRAINT clicks_ad_fk
  FOREIGN KEY (account_id, ad_id) REFERENCES ads (account_id, id);
```

Auf ähnliche Weise wird die Verteilungsspalte auch zu Eindeutigkeitseinschränkungen hinzugefügt:

```postgresql
-- Suppose we want every ad to use a unique image. Notice we can
-- enforce it only per account when we distribute by account id.

ALTER TABLE ads ADD CONSTRAINT ads_unique_image
  UNIQUE (account_id, image_url);
```

Einschränkungen, die ungleich NULL sind, können auf beliebige Spalten (Verteilungsspalten und andere Spalten) angewendet werden, da keine Lookups zwischen Workern erforderlich sind.

```postgresql
ALTER TABLE ads ALTER COLUMN image_url SET NOT NULL;
```

### <a name="using-not-valid-constraints"></a>Verwenden von NOT VALID-Constraints

In einigen Situationen kann es sinnvoll sein, Constraints für neue Zeilen zu erzwingen, während vorhandene nicht konforme Zeilen unverändert bleiben. Hyperscale (Citus) unterstützt diese Funktion für CHECK-Constraints und Fremdschlüssel, indem der PostgreSQL-Constraint \"NOT VALID\" verwendet wird.

Beispiel: Eine Anwendung speichert Benutzerprofile in einer [Verweistabelle](concepts-hyperscale-nodes.md#type-2-reference-tables).

```postgres
-- we're using the "text" column type here, but a real application
-- might use "citext" which is available in a postgres contrib module

CREATE TABLE users ( email text PRIMARY KEY );
SELECT create_reference_table('users');
```

Nach einiger Zeit enthält die Tabelle auch Inhalte, bei denen es sich nicht um gültige E-Mail-Adressen handelt.

```postgres
INSERT INTO users VALUES
   ('foo@example.com'), ('hacker12@aol.com'), ('lol');
```

Diese Adressen sollen nun überprüft werden. PostgreSQL ermöglicht es jedoch nicht, einen CHECK-Constraint hinzuzufügen, bei dem für vorhandene Zeilen ein Fehler auftritt. Ein Constraint mit der Markierung NOT VALID ist jedoch zulässig:

```postgres
ALTER TABLE users
ADD CONSTRAINT syntactic_email
CHECK (email ~
   '^[a-zA-Z0-9.!#$%&''*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$'
) NOT VALID;
```

Neue Zeilen sind jetzt geschützt.

```postgres
INSERT INTO users VALUES ('fake');

/*
ERROR:  new row for relation "users_102010" violates
        check constraint "syntactic_email_102010"
DETAIL:  Failing row contains (fake).
*/
```

Zu einem späteren Zeitpunkt außerhalb der Spitzenzeiten kann ein Datenbankadministrator versuchen, die ungültigen Zeilen zu korrigieren und den Constraint erneut zu überprüfen.

```postgres
-- later, attempt to validate all rows
ALTER TABLE users
VALIDATE CONSTRAINT syntactic_email;
```

Weitere Informationen zu NOT VALID und VALIDATE CONSTRAINT finden Sie in der PostgreSQL-Dokumentation im Abschnitt zu [ALTER TABLE](https://www.postgresql.org/docs/current/sql-altertable.html).

### <a name="addingremoving-indices"></a>Hinzufügen/Entfernen von Indizes

Hyperscale (Citus) unterstützt das Hinzufügen und Entfernen von [Indizes](https://www.postgresql.org/docs/current/static/sql-createindex.html):

```postgresql
-- Adding an index

CREATE INDEX clicked_at_idx ON clicks USING BRIN (clicked_at);

-- Removing an index

DROP INDEX clicked_at_idx;
```

Beim Hinzufügen eines Index wird eine Schreibsperre verwendet. Dies kann in einem mehrinstanzenfähigen \"bevorzugten System\" nicht wünschenswert sein. Um Downtime zu minimieren, erstellen Sie den Index stattdessen [gleichzeitig](https://www.postgresql.org/docs/current/static/sql-createindex.html#SQL-CREATEINDEX-CONCURRENTLY). Diese Methode ist arbeits- und zeitaufwendiger als der standardmäßige Vorgang zum Erstellen eines Index. Da die regulären Vorgänge während der Indexerstellung jedoch weiter ausgeführt werden können, ist diese Methode sinnvoll, wenn in einer Produktionsumgebung neue Indizes hinzugefügt werden sollen.

```postgresql
-- Adding an index without locking table writes

CREATE INDEX CONCURRENTLY clicked_at_idx ON clicks USING BRIN (clicked_at);
```
