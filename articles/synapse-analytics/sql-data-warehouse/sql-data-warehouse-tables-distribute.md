---
title: Entwurfsleitfaden für verteilte Tabellen
description: Empfehlungen für das Entwerfen von Tabellen mit Hashverteilung und verteilten Roundrobintabellen mithilfe eines dedizierten SQL-Pools in Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: c452d51018ef3f204cd7281971c07fb6337d39bf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449707"
---
# <a name="guidance-for-designing-distributed-tables-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Leitfaden zum Entwerfen von verteilten Tabellen mithilfe eines dedizierten SQL-Pools in Azure Synapse Analytics

Empfehlungen für das Entwerfen von Tabellen mit Hashverteilung und verteilten Roundrobintabellen in dedizierten SQL-Pools

In diesem Artikel wird davon ausgegangen, dass Sie mit den Konzepten der Datenverteilung und -verschiebung im dedizierten SQL-Pool vertraut sind.  Weitere Informationen finden Sie unter [Azure Synapse Analytics-Architektur](massively-parallel-processing-mpp-architecture.md).

## <a name="what-is-a-distributed-table"></a>Was ist eine verteilte Tabelle?

Eine verteilte Tabelle wird zwar als einzelne Tabelle dargestellt, tatsächlich sind die Zeilen jedoch in 60 Verteilungen gespeichert. Die Zeilen werden mit einem Hash- oder Roundrobinalgorithmus verteilt.  

**Tabellen mit Hashverteilung** verbessern die Abfrageleistung bei umfangreichen Faktentabellen und bilden den Schwerpunkt dieses Artikels. **Roundrobintabellen** ermöglichen die Verbesserung der Ladegeschwindigkeit. Durch diese Entwurfsentscheidungen lassen sich Abfrageleistung und Ladegeschwindigkeit erheblich verbessern.

Eine andere Tabellenspeicheroption besteht darin, eine kleine Tabelle auf allen Computeknoten zu replizieren. Weitere Informationen finden Sie unter [Entwurfsleitfaden für replizierte Tabellen](design-guidance-for-replicated-tables.md). Eine praktische Entscheidungshilfe für die Wahl einer der drei Optionen finden Sie in der [Tabellenübersicht](sql-data-warehouse-tables-overview.md) unter „Verteilte Tabellen“.

Für den Tabellenentwurf sollten Sie möglichst umfassende Kenntnisse zu Ihren Daten und über das Abfragen der Daten besitzen.    Stellen Sie sich beispielsweise die folgenden Fragen:

- Wie groß ist die Tabelle?
- Wie oft wird die Tabelle aktualisiert?
- Verfüge ich in einem dedizierten SQL-Pool über Fakten- und Dimensionstabellen?

### <a name="hash-distributed"></a>Hashverteilung

Bei einer Tabelle mit Hashverteilung werden Tabellenzeilen mithilfe einer deterministischen Hashfunktion, die jede Zeile einer [Verteilung](massively-parallel-processing-mpp-architecture.md#distributions) zuweist, auf die Computeknoten verteilt.

![Verteilte Tabelle](./media/sql-data-warehouse-tables-distribute/hash-distributed-table.png "Verteilte Tabelle")  

Da identische Werte per Hash immer der gleichen Verteilung zugewiesen werden, sind die Zeilenpositionen in SQL Analytics bekannt. Der dedizierte SQL-Pool nutzt dieses Wissen, um Datenverschiebungen während Abfragen zu minimieren, was die Abfrageleistung verbessert.

Tabellen mit Hashverteilung eignen sich gut für umfangreiche Faktentabellen in einem Sternschema. Sie können sehr viele Zeilen umfassen und trotzdem eine hohe Leistung bieten. Es gibt natürlich einige Entwurfsaspekte, die dazu beitragen, die Leistung zu erreichen, für die das verteilte System konzipiert ist. Einer dieser Aspekte, der auch in diesem Artikel beschrieben wird, ist die Wahl einer geeigneten Verteilungsspalte.

Geeignete Szenarien für die Verwendung einer Tabelle mit Hashverteilung:

- Die Tabellengröße auf dem Datenträger beträgt mehr als 2 GB.
- In der Tabelle erfolgen häufig Einfüge-, Aktualisierungs- und Löschvorgänge.

### <a name="round-robin-distributed"></a>Roundrobinverteilung

Bei einer Tabelle mit Roundrobinverteilung werden die Tabellenzeilen gleichmäßig auf alle Verteilungen aufgeteilt. Die Zeilen werden den Verteilungen nach dem Zufallsprinzip zugewiesen. Anders als bei Tabellen mit Hashverteilung werden Zeilen mit identischen Werten nicht unbedingt der gleichen Verteilung zugewiesen.

Aus diesem Grund muss das System in einigen Fällen zunächst einen Datenverschiebungsvorgang auslösen, um die Daten besser zu organisieren, bevor die Aufgabenstellung einer Abfrage gelöst werden kann.  Aufgrund dieses zusätzlichen Schritts können die Abfragen verlangsamt werden. Zur Verknüpfung einer Roundrobintabelle ist in der Regel eine Umstrukturierung der Zeilen erforderlich, was sich negativ auf die Leistung auswirkt.

Geeignete Szenarien für die Verwendung der Roundrobinverteilung in einer Tabelle:

- Als einfacher Ausgangspunkt, da es sich hierbei um die Standardkonfiguration handelt
- Wenn kein offensichtlicher Joinschlüssel vorhanden ist
- Wenn keine gute Kandidatenspalte für die Hashverteilung der Tabelle vorhanden ist
- Wenn die Tabelle nicht über einen gemeinsamen Joinschlüssel mit anderen Tabellen verfügt
- Wenn das Join weniger signifikant als andere Joins in der Abfrage ist
- Wenn die Tabelle eine temporäre Stagingtabelle ist

Im Tutorial [Laden von Daten zu New Yorker Taxis](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse) wird anhand eines Beispiels das Laden von Daten in eine Roundrobin-Stagingtabelle gezeigt.

## <a name="choosing-a-distribution-column"></a>Wählen einer Verteilungsspalte

Eine Tabelle mit Hashverteilung besitzt eine Verteilungsspalte. Dabei handelt es sich um den Hashschlüssel. Der folgende Code erstellt beispielsweise eine Tabelle mit Hashverteilung, bei der die Spalte „ProductKey“ als Verteilungsspalte fungiert.

```sql
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

Die in der Verteilungsspalte gespeicherten Daten können aktualisiert werden. Aktualisierungen von Daten in der Verteilungsspalte können zu einer Zufallswiedergabe von Daten führen.

Die Wahl einer Verteilungsspalte ist eine wichtige Entwurfsentscheidung, da die Zeilenverteilung auf den Werten in dieser Spalte basiert. Die beste Wahl hängt von mehreren Faktoren ab und erfordert häufig Kompromisse. Nach dem Auswählen einer Verteilungsspalte kann diese nicht mehr geändert werden.  

Sollten Sie sich beim ersten Mal nicht für die optimale Spalte entschieden haben, können Sie die Tabelle mithilfe von [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) mit einer anderen Verteilungsspalte neu erstellen.

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Wählen Sie eine Verteilungsspalte mit Daten, die sich gleichmäßig verteilen lassen.

Aus Leistungsgründen sollten alle Verteilungen ungefähr die gleiche Anzahl von Zeilen besitzen. Bei einem Missverhältnis der Zeilenanzahl schließen einige Verteilungen ihren Teil einer parallelen Abfrage schneller ab als andere. Da die Abfrage erst abgeschlossen werden kann, wenn die Verarbeitung aller Verteilungen abgeschlossen ist, ist die Abfrage nur so schnell wie die langsamste Verteilung.

- Datenschiefe bedeutet, dass die Daten nicht gleichmäßig auf die Verteilungen aufgeteilt sind.
- Verarbeitungsschiefe bedeutet, dass bei parallelen Abfragen einige Verteilungen mehr Zeit benötigen als andere. Dieser Fall kann eintreten, wenn eine Datenschiefe vorliegt.
  
Wählen Sie zur Erzielung einer ausgeglichenen parallelen Verarbeitung eine Verteilungsspalte, die folgende Kriterien erfüllt:

- **Viele eindeutige Werte:** Die Spalte kann einige Duplikatwerte enthalten. Zeilen mit gleichem Wert werden jedoch alle der gleichen Verteilung zugewiesen. Da 60 Verteilungen vorhanden sind, sollte die Spalte mindestens 60 eindeutige Werte enthalten.  Die Anzahl eindeutiger Werte ist in der Regel deutlich höher.
- **Keine oder nur wenige NULL-Werte:** Stellen Sie sich als Extrembeispiel vor, alle Werte in der Spalte wären NULL-Werte. In diesem Fall würden alle Zeilen der gleichen Verteilung zugewiesen. Dadurch würde die gesamte Abfrageverarbeitung in einer einzelnen Verteilung stattfinden und nicht von der parallelen Verarbeitung profitieren.
- **Keine Datumsspalte:** Alle Daten für das gleiche Datum werden der gleichen Verteilung zugewiesen. Wenn mehrere Benutzer nach dem gleichen Datum filtern, findet die Verarbeitung nur in einer der 60 Verteilungen statt.

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Wählen Sie eine Verteilungsspalte, die Datenverschiebungen minimiert.

Zur Ermittlung des korrekten Abfrageergebnisses werden Daten ggf. zwischen Computeknoten verschoben. Zu Datenverschiebungen kommt es in der Regel, wenn Abfragen über Joins und Aggregationen für verteilte Tabellen verfügen. Die Wahl einer Verteilungsspalte, die zur Minimierung von Datenverschiebungen beiträgt, ist eine der wichtigsten Strategien zum Optimieren der Leistung Ihres dedizierten SQL-Pools.

Zur Minimierung von Datenverschiebungen muss die Verteilungsspalte folgende Kriterien erfüllen:

- Sie wird in Klauseln vom Typ `JOIN`, `GROUP BY`, `DISTINCT`, `OVER` und `HAVING` verwendet. Wenn zwei umfangreiche Faktentabellen über häufige Joins verfügen, verbessert sich die Abfrageleistung, wenn die Verteilung der beiden Tabellen auf einer der Verknüpfungsspalten basiert.  Wenn eine Tabelle nicht in Joins verwendet wird, können Sie sie ggf. auf der Grundlage einer Tabelle verteilen, die in der `GROUP BY`-Klausel verwendet wird.
- Sie wird *nicht* in `WHERE`-Klauseln verwendet. Dies kann die Abfrage eingrenzen und dazu führen, dass sie nicht für alle Verteilungen ausgeführt wird.
- Sie ist *keine* Datumsspalte. WHERE-Klauseln werden häufig zum Filtern nach Datum verwendet.  In diesem Fall findet die gesamte Verarbeitung unter Umständen nur in einigen wenigen Verteilungen statt.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Was, wenn keine der Spalten als Verteilungsspalte geeignet ist?

Wenn keine der Spalten genügend unterschiedliche Werte für eine Verteilungsspalte aufweist, können Sie eine neue Spalte aus einem Wert oder mehreren Werten zusammensetzen. Um Datenverschiebungen beim Ausführen von Abfragen zu vermeiden, verwenden Sie die zusammengesetzte Verteilungsspalte als Verknüpfungsspalte in Abfragen.

Nach dem Entwerfen einer Tabelle mit Hashverteilung müssen Daten in die Tabelle geladen werden.  Eine entsprechende Anleitung finden Sie in der [Übersicht über das Laden](design-elt-data-loading.md).

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Wie kann ich feststellen, ob ich eine geeignete Verteilungsspalte gewählt habe?

Überprüfen Sie, wie gleichmäßig die Zeilen auf die 60 Verteilungen aufgeteilt sind, nachdem Sie Daten in eine Tabelle mit Hashverteilung geladen haben. Die Zeilen pro Verteilung können um bis zu zehn Prozent variieren, ohne dass dies merkliche Auswirkungen auf die Leistung hat.

### <a name="determine-if-the-table-has-data-skew"></a>Ermitteln, ob die Tabelle von Datenschiefe betroffen ist

Mit [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) können Sie schnell feststellen, ob eine Datenschiefe vorliegt. Der folgende SQL-Code gibt die Anzahl von Tabellenzeilen zurück, die in jeder der 60 Verteilungen gespeichert sind. Um eine ausgeglichene Leistung zu erzielen, müssen die Zeilen in Ihrer verteilten Tabelle gleichmäßig auf alle Verteilungen aufgeteilt sein.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

So ermitteln Sie Tabellen mit einer Datenschiefe von mehr als zehn Prozent:

1. Erstellen Sie die Ansicht „dbo.vTableSizes“ aus dem Artikel mit der [Tabellenübersicht](sql-data-warehouse-tables-overview.md#table-size-queries).  
2. Führen Sie die folgende Abfrage aus:

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having (max(row_count * 1.000) - min(row_count * 1.000))/max(row_count * 1.000) >= .10
    )
order by two_part_name, row_count
;
```

### <a name="check-query-plans-for-data-movement"></a>Überprüfen von Abfrageplänen auf Datenverschiebungen

Eine gute Verteilungsspalte ermöglicht Joins und Aggregationen mit geringer Datenverschiebung. Aus diesem Grund müssen Joins auf eine bestimmte Weise erstellt werden. Um bei einem Join zweier Tabellen mit Hashverteilung eine möglichst geringe Datenverschiebung zu erzielen, muss eine der Verknüpfungsspalten als die Verteilungsspalte fungieren.  Wenn zwei Tabellen mit Hashverteilung auf der Grundlage einer Verteilungsspalte gleichen Datentyps verknüpft sind, sind für das Join keine Datenverschiebungen erforderlich. Joins können zusätzliche Spalten verwenden, ohne Datenverschiebungen zu verursachen.

So vermeiden Sie Datenverschiebungen während eines Joins:

- Die an dem Join beteiligten Tabellen müssen per Hashverteilung auf der Grundlage **einer** der an dem Join beteiligten Spalten verteilt werden.
- Die Datentypen der Verknüpfungsspalten müssen in beiden Tabellen übereinstimmen.
- Die Spalten müssen mit dem Operator „Equals“ (Gleich) verknüpft werden.
- Der Jointyp darf nicht `CROSS JOIN` sein.

Ob es bei Abfragen zu Datenverschiebungen kommt, können Sie anhand des Abfrageplans ermitteln.  

## <a name="resolve-a-distribution-column-problem"></a>Lösen eines Problems mit der Verteilungsspalte

Nicht alle Fälle von Datenschiefe müssen behoben werden. Verteilen von Daten bedeutet, das richtige Gleichgewicht zwischen der Minimierung der Datenschiefe und der Minimierung von Datenverschiebungen zu finden. Es ist nicht immer möglich, beides zu minimieren. Manchmal überwiegen die Vorteile einer Minimierung von Datenverschiebungen unter Umständen die Nachteile der Datenschiefe.

Um zu entscheiden, ob die Datenschiefe in einer Tabelle beseitigt werden sollte, müssen Sie die Datenmenge und die Abfragen in Ihrer Workload möglichst umfassend verstehen. Wie Sie die Auswirkungen der Datenschiefe auf die Abfrageleistung überwachen, erfahren Sie im Artikel zur [Abfrageüberwachung](sql-data-warehouse-manage-monitor.md). Achten Sie insbesondere darauf, wie lange die Ausführung großer Abfragen für die einzelnen Verteilungen dauert.

Da die Verteilungsspalte für eine vorhandene Tabelle nicht geändert werden kann, muss die Tabelle zur Behebung der Datenschiefe in der Regel mit einer anderen Verteilungsspalte neu erstellt werden.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Neuerstellen der Tabelle mit einer neuen Verteilungsspalte

In diesem Beispiel wird [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) verwendet, um eine Tabelle mit einer anderen Hashverteilungsspalte neu zu erstellen.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie zum Erstellen einer verteilten Tabelle eine der folgenden Anweisungen:

- [CREATE TABLE (dedizierter SQL-Pool)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT (dedizierter SQL-Pool)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
