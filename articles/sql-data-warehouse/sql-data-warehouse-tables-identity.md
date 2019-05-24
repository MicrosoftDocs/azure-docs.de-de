---
title: Erstellen von Ersatzschlüsseln mit IDENTITY – Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Empfehlungen und Beispiele zum Erstellen von Ersatzschlüsseln für Tabellen in Azure SQL Data Warehouse mithilfe der IDENTITY-Eigenschaft
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 19a06d0fdff324dc3bee246ef7a5a7011c089872
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851593"
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Erstellen von Ersatzschlüsseln mit IDENTITY in Azure SQL Data Warehouse

Empfehlungen und Beispiele zum Erstellen von Ersatzschlüsseln für Tabellen in Azure SQL Data Warehouse mithilfe der IDENTITY-Eigenschaft

## <a name="what-is-a-surrogate-key"></a>Was ist ein Ersatzschlüssel?

Ein Ersatzschlüssel für eine Tabelle ist eine Spalte mit einem eindeutigen Bezeichner für jede Zeile. Der Schlüssel wird nicht aus den Tabellendaten generiert. Datenmodellierer erstellen häufig Ersatzschlüssel in ihren Tabellen, während sie Data Warehouse-Modelle erstellen. Mit der IDENTITY-Eigenschaft können Sie dies einfach und effektiv durchführen, ohne die Leistung beim Laden zu beeinträchtigen.  

## <a name="creating-a-table-with-an-identity-column"></a>Erstellen einer Tabelle mit einer IDENTITY-Spalte

Die IDENTITY-Eigenschaft wurde so entwickelt, dass sie über alle Verteilungen im Data Warehouse horizontal hochskaliert werden kann. Aus diesem Grund ist die Implementierung von IDENTITY auf das Erreichen dieser Ziele ausgerichtet.

Sie können beim Erstellen einer Tabelle definieren, dass diese die IDENTITY-Eigenschaft hat, indem Sie Syntax verwenden, die der folgenden Anweisung ähnelt:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Anschließend können Sie `INSERT..SELECT` zum Auffüllen der Tabelle verwenden.

Im restlichen Abschnitt werden die Feinheiten der Implementierung hervorgehoben, um Ihr Verständnis zu vertiefen.  

### <a name="allocation-of-values"></a>Zuordnung von Werten

Die IDENTITY-Eigenschaft garantiert nicht die Reihenfolge, in der die Ersatzwerte zugeordnet sind. Dies spiegelt das Verhalten von SQL Server und Azure SQL-Datenbank wieder. Allerdings ist in Azure SQL Data Warehouse die Abwesenheit einer Garantie ausgeprägter.

Dies wird in folgendem Beispiel veranschaulicht:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

Im vorherigen Beispiel wurden zwei Zeilen der Verteilung 1 zugeordnet. Die erste Zeile weist den Ersatzwert 1 in der Spalte `C1` auf, und die zweite Zeile den Ersatzwert 61. Beide Werte wurden durch die IDENTITY-Eigenschaft generiert. Die Zuordnung der Werte ist jedoch nicht fortlaufend. Dieses Verhalten ist beabsichtigt.

### <a name="skewed-data"></a>Verfälschte Daten

Der Wertebereich für den Datentyp ist gleichmäßig auf die Verteilungen verteilt. Wenn eine verteilte Tabelle verfälschte Daten aufweist, kann der Wertebereich, der für den Datentyp zu Verfügung steht, frühzeitig erschöpft sein. Wenn z.B. alle Daten in einer einzelnen Verteilung enden, hat die Tabelle tatsächlich nur Zugriff auf ein Sechzehntel des Datentyps. Aus diesem Grund ist die IDENTITY-Eigenschaft auf die Datentypen `INT` und `BIGINT` beschränkt.

### <a name="selectinto"></a>SELECT..INTO

Wenn eine vorhandene Identitätsspalte in einer neuer Tabelle ausgewählt ist, erbt die neue Spalte die IDENTITY-Eigenschaft, es sei denn, eine der folgenden Bedingungen trifft zu:

- Die SELECT-Anweisung enthält einen Join.
- Mehrere SELECT-Anweisungen werden mithilfe von UNION verknüpft.
- Die IDENTITY-Spalte ist mehr als einmal in der Auswahlliste aufgeführt.
- Die IDENTITY-Spalte ist Teil eines Ausdrucks.

Wenn eine dieser Bedingungen zutrifft, wird die Spalte als NOT NULL erstellt, statt die IDENTITY-Eigenschaft zu erben.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

CREATE TABLE AS SELECT (CTAS) folgt dem gleichen SQL Server-Verhalten wie SELECT..INTO. Sie können jedoch keine IDENTITY-Eigenschaft in der Spaltendefinition im `CREATE TABLE`-Teil der Anweisung angeben. Außerdem können Sie keine IDENTITY-Funktion im `SELECT`-Teil von CTAS verwenden. Um eine Tabelle zu aufzufüllen, müssen Sie `CREATE TABLE` verwenden, um die Tabelle zu definieren, und dann `INSERT..SELECT`, um sie aufzufüllen.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Explizites Einfügen von Werten in eine IDENTITY-Spalte

SQL Data Warehouse unterstützt `SET IDENTITY_INSERT <your table> ON|OFF`-Syntax. Mit dieser Syntax können Sie explizit Werte in die IDENTITY-Spalte einfügen.

Viele Datenmodellierer verwenden vordefinierte negative Werte für bestimmte Zeilen in ihren Dimensionen. Die Zeile „-1“ oder „unbekannter Member“ ist ein Beispiel.

Das nächste Skript zeigt, wie Sie diese Zeile explizit mithilfe von SET IDENTITY_INSERT hinzufügen können:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT  *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>Laden von Daten

Die IDENTITY-Eigenschaft wirkt sich auf Ihren Code zum Laden von Daten aus. In diesem Abschnitt werden einige grundlegende Muster zum Laden von Daten in Tabellen mit IDENTITY hervorgehoben.

Um Daten in eine Tabelle zu laden und einen Ersatzschlüssel mit IDENTITY zu generieren, erstellen Sie die Tabelle und verwenden Sie dann INSERT..SELECT oder INSERT..VALUES, um den Ladevorgang durchzuführen.

Im folgende Beispiel wird das grundlegende Muster aufgezeigt:

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> Aktuell ist es nicht möglich, `CREATE TABLE AS SELECT` zu verwenden, wenn Sie Daten mit einer IDENTITY-Spalte in eine Tabelle laden.
>

Weitere Informationen zum Laden von Daten finden Sie unter [Entwerfen von ELT-Prozessen für Azure SQL Data Warehouse](design-elt-data-loading.md) und [Bewährte Methoden zum Laden von Daten in Azure SQL Data Warehouse](guidance-for-loading-data.md).

## <a name="system-views"></a>Systemsichten

Sie können über die Katalogsicht [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) eine Spalte ermitteln, die die IDENTITY-Eigenschaft enthält.

Damit Sie das Datenbankschema besser verstehen, wird in diesem Beispiel gezeigt, wie Sie „sys.identity_column“ in andere Systemkatalogsichten integrieren können:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Einschränkungen

Die IDENTITY-Eigenschaft kann in folgenden Fällen nicht verwendet werden:

- Wenn der Spaltendatentyp weder INT noch BIGINT ist
- Wenn die Spalte auch der Verteilungsschlüssel ist
- Wenn die Tabelle eine externe Tabelle ist

Die folgenden verwandten Funktionen werden in SQL Data Warehouse nicht unterstützt:

- [IDENTITY()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)

## <a name="common-tasks"></a>Häufige Aufgaben

Dieser Abschnitt enthält Beispielcode, den Sie verwenden können, um bei der Arbeit mit der IDENTITY-Spalten allgemeine Aufgaben auszuführen.

Spalte C1 ist die IDENTITY in allen folgenden Aufgaben.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Suchen Sie den höchsten zugeordneten Wert für eine Tabelle

Verwenden Sie die Funktion `MAX()`, um den höchsten zugeordneten Wert einer verteilten Tabelle zu bestimmen:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Suchen Sie den Startwert und die Schrittweite für die IDENTITY-Eigenschaft

Sie können die Katalogansichten verwenden, um die Konfigurationswerte für die ID-Schrittweite und den Startwert für eine Tabelle mit der folgenden Abfrage zu ermitteln:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Nächste Schritte

- [Tabellenübersicht](/azure/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [CREATE TABLE (Transact-SQL) IDENTITY (Eigenschaft)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest)
- [DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)
