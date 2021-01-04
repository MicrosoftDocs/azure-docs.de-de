---
title: Best Practices für dedizierte SQL-Pools (ehemals SQL DW)
description: Empfehlungen und bewährte Methoden für die Entwicklung von Lösungen für dedizierte SQL-Pools (ehemals SQL DW) in Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9802e6553d553aae4f13194dc9951d1a17af6f66
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462876"
---
# <a name="best-practices-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Best Practices für dedizierte SQL-Pools (ehemals SQL DW) in Azure Synapse Analytics

Dieser Artikel enthält eine Sammlung von bewährten Methoden, mit denen Sie bei Ihrer Bereitstellung eines [dedizierten SQL-Pools (ehemals SQL DW)](sql-data-warehouse-overview-what-is.md) eine optimale Leistung erzielen können.  Der Zweck dieses Artikels besteht darin, Ihnen einige grundlegende Anleitungen zu geben und wichtige Schwerpunktbereiche hervorzuheben.  

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzieren von Kosten durch das Pausieren und Skalieren

Weitere Informationen zur Kostensenkung durch Anhalten und Skalieren finden Sie im [Artikel zur Computeverwaltung](sql-data-warehouse-manage-compute-overview.md).

## <a name="maintain-statistics"></a>Verwalten von Statistiken

Dedizierte SQL-Pools (ehemals SQL DW) können so konfiguriert werden, dass Statistiken für Spalten automatisch erkannt und erstellt werden.  Die vom Optimierer erstellten Abfragepläne sind nur so gut wie die verfügbaren Statistiken.  

Wir empfehlen, dass Sie AUTO_CREATE_STATISTICS für Ihre Datenbanken aktivieren und die Statistiken täglich oder nach jedem Ladevorgang aktualisieren, um sicherzustellen, dass Statistiken für in Ihren Abfragen verwendete Spalten immer auf dem neuesten Stand sind.

Falls die Aktualisierung aller Ihrer Statistiken zu lange dauert, können Sie versuchen, selektiver auszuwählen, in welchen Spalten Statistiken häufig aktualisiert werden müssen. Beispielsweise können Sie Datumsspalten aktualisieren, wenn täglich neue Werte hinzugefügt werden.

> [!TIP]
> Die größten Vorteile ergeben sich, wenn Sie aktualisierte Statistiken für Spalten in Verknüpfungen, in der WHERE-Klausel verwendete Spalten und Spalten in GROUP BY nutzen.

Siehe auch [Verwalten von Tabellenstatistiken](sql-data-warehouse-tables-statistics.md), [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) und [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Verwenden von DMVs zum Überwachen und Optimieren von Abfragen

Dedizierte SQL-Pools (ehemals SQL DW) verfügen über mehrere DMVs, mit denen die Abfrageausführung überwacht werden kann.  Im Artikel [Überwachen der Workload mit DMVs](sql-data-warehouse-manage-monitor.md) finden Sie ausführliche Anweisungen zum Anzeigen von Informationen zu einer ausgeführten Abfrage.  

Die Verwendung der Option LABEL für Abfragen kann hilfreich sein, um Abfragen für diese DMVs schnell finden zu können.

Siehe auch: [Überwachen Ihrer Workload mit dynamischen Verwaltungssichten](sql-data-warehouse-manage-monitor.md), [LABEL](sql-data-warehouse-develop-label.md), [OPTION](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) und [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="tune-query-performance-with-new-product-enhancements"></a>Optimieren der Abfrageleistung mit neuen Produkterweiterungen

- [Leistungsoptimierung mit materialisierten Sichten](performance-tuning-materialized-views.md)
- [Leistungsoptimierung mit einem sortierten gruppierten Columnstore-Index](performance-tuning-ordered-cci.md)
- [Leistungsoptimierung mit Zwischenspeichern von Resultsets](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>Gruppieren von INSERT-Anweisungen in Batches

Ein einmaliger Ladevorgang für eine kleine Tabelle mit einer INSERT-Anweisung oder sogar das regelmäßige erneute Laden eines Suchvorgang kann für Ihre Zwecke eine gute Leistung bieten, wenn Sie eine Anweisung wie `INSERT INTO MyLookup VALUES (1, 'Type 1')` verwenden.  

Falls Sie im Laufe eines Tages aber Tausende oder Millionen von Zeilen laden müssen, werden Sie merken, dass einzelne INSERTS nicht mehr ausreichen.  Entwickeln Sie Ihre Prozesse stattdessen so, dass die Daten in eine Datei geschrieben werden und diese Datei von einem anderen Prozess regelmäßig geladen wird.

Siehe auch: [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Verwenden von PolyBase zum schnellen Laden und Exportieren von Daten

Dedizierte SQL-Pools (ehemals SQL DW) unterstützen das Laden und Exportieren von Daten mit mehreren Tools, z. B. Azure Data Factory, PolyBase und BCP.  Für kleinere Datenmengen, bei denen die Leistung nicht entscheidend ist, können auch beliebige andere Tools für Ihre Zwecke ausreichen.  PolyBase ist aber die beste Wahl, wenn Sie große Datenvolumen laden oder exportieren oder eine schnelle Leistung benötigt wird.  

PolyBase ist so konzipiert, dass es die verteilte Natur des Systems nutzt und Datenmengen schneller als jedes andere Tool laden und exportieren kann.  PolyBase-Ladevorgänge können per CTAS oder INSERT INTO ausgeführt werden.   

> [!TIP]
> Durch die Verwendung von CTAS wird die Transaktionsprotokollierung reduziert. Dies ist der schnellste Weg zum Laden Ihrer Daten.

Azure Data Factory unterstützt auch PolyBase-Ladevorgänge und kann eine ähnliche Leistung wie CTAS erreichen.  PolyBase unterstützt eine Vielzahl von Dateiformaten, z.B. Gzip-Dateien.  
  
> [!NOTE]
> Wenn Sie bei der Verwendung von Gzip-Textdateien den Durchsatz maximieren möchten, teilen Sie sie in 60 oder mehr Dateien auf, um die Parallelität Ihres Ladevorgangs zu maximieren.  Erwägen Sie das gleichzeitige Laden von Daten, um einen besseren Gesamtdurchsatz zu erzielen.

Lesen Sie dazu auch: [Laden von Daten](design-elt-data-loading.md), [Anleitung für die Verwendung von PolyBase](guidance-for-loading-data.md), [Dedizierter SQL-Pool – Lademuster und -strategien](https://blogs.msdn.microsoft.com/sqlcat/20../../), [Laden von Daten mit Azure Data Factory]( ../../data-factory/load-azure-sql-data-warehouse.md), [Verschieben von Daten mit Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md), [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) und [Create Table As Select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="load-then-query-external-tables"></a>Laden und Abfragen von externen Tabellen

Polybase, auch bekannt als externe Tabellen, kann zwar die schnellste Möglichkeit zum Laden von Daten sein, ist aber nicht optimal für Abfragen. PolyBase-Tabellen unterstützen derzeit nur Azure-Blobdateien und Azure Data Lake Storage. Diese Dateien werden nicht durch Computeressourcen gestützt.  

Infolgedessen kann der dedizierte SQL-Pool diese Arbeit nicht auslagern und muss deshalb die gesamte Datei in „tempdb“ laden, um die Daten lesen zu können.  Wenn mehrere auf diese Daten gerichtete Abfragen vorliegen, sollten Sie also besser diese Daten einmal laden, und die Abfragen sollten in der lokalen Tabelle durchgeführt werden.

Siehe auch: [Leitfaden für die Verwendung von PolyBase](guidance-for-loading-data.md).

## <a name="hash-distribute-large-tables"></a>Verwenden der Hashverteilung für große Tabellen

Für Tabellen wird standardmäßig die Roundrobin-Verteilung eingesetzt.  Dies erleichtert Benutzern die ersten Schritte beim Erstellen von Tabellen, ohne entscheiden zu müssen, wie die Tabellen verteilt werden sollen.  Roundrobin-Tabellen ermöglichen für einige Workloads eine ausreichende Leistung, aber in den meisten Fällen führt die Auswahl einer Verteilungsspalte zu einer besseren Leistung.  

Das häufigste Beispiel für den Fall, in dem eine von einer Spalte verteilte Tabelle eine deutlich höhere Leistung als eine Roundrobin-Tabelle aufweist, ist die Verknüpfung von zwei großen Faktentabellen.  

Wenn Sie beispielsweise eine Tabelle mit Bestellungen verwenden, die nach „order_id“ verteilt wird, und eine Tabelle mit Transaktionen, die ebenfalls nach „order_id“ verteilt wird, gilt Folgendes: Wenn Sie die Tabelle mit den Bestellungen per „order_id“ mit der Tabelle mit den Transaktionen verknüpfen, wird diese Abfrage zu einer Pass-Through-Abfrage. Dies bedeutet, dass wir Datenverschiebungsvorgänge beseitigen.  Weniger Schritte führen zu einer schnelleren Abfrage.  Außerdem führen weniger Datenverschiebungen zu schnelleren Abfragen.  

> [!TIP]
> Achten Sie beim Laden einer verteilten Tabelle darauf, dass Ihre eingehenden Daten nicht nach dem Verteilungsschlüssel sortiert sind, da dies mit einer Verlangsamung Ihrer Ladevorgänge verbunden ist.  

Unter den folgenden Links finden Sie weitere Details dazu, wie die Auswahl einer Verteilungsspalte zu einer Leistungssteigerung führen kann und wie Sie eine verteilte Tabelle in der WITH-Klausel Ihrer CREATE TABLE-Anweisung definieren können.

Siehe auch: [Übersicht über Tabellen](sql-data-warehouse-tables-overview.md), [Tabellenverteilung](sql-data-warehouse-tables-distribute.md), [Auswählen der Tabellenverteilung](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="do-not-over-partition"></a>Vermeiden von übermäßiger Partitionierung

Das Partitionieren von Daten kann für die Verwaltung Ihrer Daten durch Partitionswechsel oder die Optimierung von Scans durch eine Partitionsbeseitigung zwar effektiv sein, aber eine zu hohe Zahl von Partitionen kann Ihre Abfragen verlangsamen.  Oft funktioniert eine Partitionierungsstrategie mit hoher Granularität, die für SQL Server gut geeignet ist, in einem dedizierten SQL-Pool (ehemals SQL DW) möglicherweise nicht gut.  

Die Verwendung von zu vielen Partitionen kann auch die Effektivität von gruppierten Columnstore-Indizes reduzieren, wenn jede Partition weniger als 1 Million Zeilen enthält.  Beachten Sie, dass ein dedizierter SQL-Pool Ihre Daten im Hintergrund in 60 Datenbanken partitioniert. Wenn Sie also eine Tabelle mit 100 Partitionen erstellen, ergibt dies tatsächlich insgesamt 6.000 Partitionen.  

Jede Workload ist anders, sodass es ratsam ist, mit der Partitionierung zu experimentieren. So können Sie ermitteln, was für Ihre Workload am besten funktioniert.  Erwägen Sie die Verwendung einer geringeren Granularität als für SQL Server.  Versuchen Sie es beispielsweise anstelle von täglichen Partitionen mit wöchentlichen oder monatlichen Partitionen.

Siehe auch: [Tabellenpartitionierung](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Verringern von Transaktionsgrößen

INSERT-, UPDATE- und DELETE-Anweisungen werden in einer Transaktion ausgeführt, und wenn hierbei Fehler auftreten, muss ein Rollback durchgeführt werden.  Es ist ratsam, die Transaktionsgrößen nach Möglichkeit zu verringern, um das Potenzial für einen langen Rollbackvorgang zu reduzieren.  Hierfür können Sie INSERT-, UPDATE- und DELETE-Anweisungen in Teile unterteilen.  

Wenn Sie beispielsweise einen INSERT-Vorgang verwenden, der voraussichtlich eine Stunde dauert, können Sie ihn – falls möglich – in vier Teile unterteilen, die jeweils 15 Minuten dauern.  Nutzen Sie spezielle Fälle mit minimaler Protokollierung, z. B. CTAS, TRUNCATE, DROP TABLE oder INSERT zum Leeren von Tabellen, um das Rollbackrisiko zu verringern.  

Eine weitere Möglichkeit zum Beseitigen von Rollbacks ist die Verwendung von Vorgängen vom Typ „Nur Metadaten“, z.B. ein Partitionswechsel für die Datenverwaltung.  Anstatt z. B. eine DELETE-Anweisung zum Löschen aller Zeilen in einer Tabelle auszuführen, für die das Bestelldatum (order_date) im Oktober 2001 liegt, können Sie Ihre Daten monatlich partitionieren und die Partition dann gegen Daten für eine leere Partition aus einer anderen Tabelle auswechseln (siehe Beispiele zu [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)).  

Erwägen Sie bei nicht partitionierten Tabellen die Verwendung eines CTAS-Vorgangs, um die beizubehaltenden Daten in eine Tabelle zu schreiben, statt DELETE zu verwenden.  Wenn für einen CTAS-Vorgang die gleiche Zeit benötigt wird, ist dies ein viel sichererer Vorgang, da er mit minimaler Transaktionsprotokollierung verbunden ist und bei Bedarf schnell abgebrochen werden kann.

Weitere Informationen finden Sie unter [Verwenden von Transaktionen in SQL Data Warehouse](sql-data-warehouse-develop-transactions.md), [Optimieren von Transaktionen in SQL Analytics](sql-data-warehouse-develop-best-practices-transactions.md), [Tabellenpartitionierung](sql-data-warehouse-tables-partition.md), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) und [CREATE TABLE AS SELECT (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="reduce-query-result-sizes"></a>Reduzieren von Abfrageergebnisgrößen

Dieser Schritt hilft Ihnen, clientseitige Probleme zu vermeiden, die durch ein großes Abfrageergebnis verursacht werden.  Sie können Ihre Abfrage bearbeiten, um die Anzahl von zurückgegebenen Zeilen zu reduzieren. Einige Tools zur Abfragegenerierung ermöglichen es Ihnen, jeder Abfrage „Top N“-Syntax hinzuzufügen.  Sie können das Abfrageergebnis auch mit CETAS in eine temporäre Tabelle exportieren und dann den PolyBase-Export für die kompatible Verarbeitung verwenden.

## <a name="use-the-smallest-possible-column-size"></a>Verwenden der kleinstmöglichen Spaltengröße

Beim Definieren Ihrer DDL verbessert sich die Abfrageleistung, wenn Sie den kleinsten Datentyp verwenden, der für Ihre Daten unterstützt wird.  Diese Vorgehensweise ist besonders wichtig für CHAR- und VARCHAR-Spalten.  

Wenn der längste Wert in einer Spalte 25 Zeichen lang ist, sollten Sie die Spalte VARCHAR(25) definieren.  Vermeiden Sie es, für alle Zeichenspalten eine hohe Standardlänge zu definieren.  Definieren Sie darüber hinaus Spalten als VARCHAR, sofern dies ausreicht, anstatt NVARCHAR zu verwenden.

Siehe auch: [Übersicht über Tabellen](sql-data-warehouse-tables-overview.md), [Tabellendatentypen](sql-data-warehouse-tables-data-types.md), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-temporary-heap-tables-for-transient-data"></a>Verwenden temporärer Heaptabellen für vorübergehende Daten

Wenn Sie Daten vorübergehend verschieben, werden Sie möglicherweise feststellen, dass der Gesamtprozess durch die Nutzung einer Heaptabelle beschleunigt wird.  Wenn Sie Daten nur laden, um sie vor der Ausführung von weiteren Transformationen bereitzustellen, wird der Ladevorgang der Tabelle auf die Heaptabelle schneller sein, als wenn Sie Daten in eine geclusterte Columnstore-Tabelle laden.  

Beim Laden von Daten in eine temporäre Tabelle wird der Ladevorgang außerdem viel schneller als beim Laden einer Tabelle in einen dauerhaften Speicher durchgeführt.  Temporäre Tabellen, die mit dem Zeichen „#“ beginnen und auf die nur über die Sitzung, in der sie erstellt wurden, zugegriffen werden kann, funktionieren möglicherweise nur in begrenzten Szenarien.

Heaptabellen werden in der WITH-Klausel von CREATE TABLE definiert.  Denken Sie beim Verwenden einer temporären Tabelle daran, auch für die temporäre Tabelle Statistiken zu erstellen.

Siehe auch: [Temporäre Tabellen](sql-data-warehouse-tables-temporary.md), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="optimize-clustered-columnstore-tables"></a>Optimieren von gruppierten Columnstore-Tabellen

Gruppierte Columnstore-Indizes sind eine der effizientesten Möglichkeiten zum Speichern Ihrer Daten in einem dedizierten SQL-Pool.  Tabellen werden in einem dedizierten SQL-Pool standardmäßig als gruppierter Columnstore erstellt.  Die Verwendung einer guten Segmentqualität ist wichtig, um für Abfragen in Columnstore-Tabellen die beste Leistung zu erzielen.  

Wenn Zeilen bei hohem Arbeitsspeicherdruck in Columnstore-Tabellen geschrieben werden, kann die Qualität von Columnstore-Segmenten leiden.  Die Segmentqualität kann anhand der Anzahl an Zeilen in einer komprimierten Zeilengruppe gemessen werden.  Eine ausführliche Anleitung zur Erkennung und Verbesserung der Segmentqualität für gruppierte Columnstore-Tabellen finden Sie im Artikel [Tabellenindizes](sql-data-warehouse-tables-index.md) unter [Ursachen für eine schlechte Qualität des Columnstore-Index](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  

Weil qualitativ hochwertige Columnstore-Segmente wichtig sind, ist es eine gute Idee, Benutzer-IDs, die zur mittleren oder großen Ressourcenklasse gehören, zum Laden von Daten zu verwenden. Wenn Sie [Data Warehouse-Einheiten](what-is-a-data-warehouse-unit-dwu-cdwu.md) auf niedrigerer Ebene verwenden, sollten Sie dem ladenden Benutzer eine größere Ressourcenklasse zuweisen.

Da Columnstore-Tabellen Daten im Allgemeinen erst dann in ein komprimiertes Columnstore-Segment übertragen, wenn eine Tabelle mehr als eine Million Zeilen hat und jede Tabelle im dedizierten SQL-Pool in 60 Tabellen partitioniert ist, gilt folgende Faustregel: Columnstore-Tabellen stellen für eine Abfrage nur dann einen Vorteil dar, wenn die Tabelle mehr als 60 Millionen Zeilen hat.  Für Tabellen mit weniger als 60 Millionen Zeilen ist es unter Umständen nicht sinnvoll, einen Columnstore-Index zu nutzen.  Es muss aber nicht unbedingt einen Nachteil bedeuten.  

Wenn Sie Ihre Daten partitionieren, können Sie auch darauf achten, dass jede Partition 1 Million Zeilen aufweisen muss, um von einem gruppierten Columnstore-Index zu profitieren.  Falls eine Tabelle 100 Partitionen hat, muss sie mindestens 6 Milliarden Zeilen enthalten, um von einem gruppierten Columnstore zu profitieren (60 Verteilungen *100 Partitionen* 1 Million Zeilen).  

Falls Ihre Tabelle für dieses Beispiel nicht 6 Milliarden Zeilen enthält, sollten Sie entweder die Anzahl der Partitionen reduzieren oder stattdessen eine Heaptabelle verwenden.  Außerdem kann sich folgendes Experiment lohnen: Ermitteln Sie, ob eine bessere Leistung erzielt werden kann, indem eine Heaptabelle mit sekundären Indizes anstelle einer Columnstore-Tabelle verwendet wird.

> [!TIP]
> Beim Abfragen einer Columnstore-Tabelle werden die Abfragen schneller ausgeführt, wenn Sie nur die benötigten Spalten auswählen.  

Siehe auch [Tabellenindizes](sql-data-warehouse-tables-index.md), [Beschreibung von Columnstore-Indizes](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) und [Neuerstellen von Columnstore-Indizes](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Verwenden einer größeren Ressourcenklasse zum Verbessern der Abfrageleistung

Dedizierte SQL-Pools nutzen Ressourcengruppen zum Zuordnen von Arbeitsspeicher zu Abfragen.  Standardmäßig werden alle Benutzer der kleinen Ressourcenklasse zugewiesen, bei der pro Verteilung 100 MB Arbeitsspeicher gewährt werden.  Da immer 60 Verteilungen vorhanden sind, und jede Verteilung mindestens 100MB erhält, beträgt die gesamte systemweite Arbeitsspeicherzuordnung 6.000MB bzw. knapp 6GB.  

Bestimmte Abfragen, z.B. große Verknüpfungen oder Ladevorgänge für gruppierte Columnstore-Tabellen, profitieren von größeren Arbeitsspeicherzuordnungen.  Für einige Abfragen, z. B. reine Scans, ergibt sich kein Vorteil.  Andererseits reduziert die Nutzung größerer Ressourcenklassen die Parallelität. Diese Auswirkung sollten Sie berücksichtigen, bevor Sie alle Ihre Benutzer in eine große Ressourcenklasse verschieben.

Siehe auch: [Ressourcenklassen für die Workloadverwaltung](resource-classes-for-workload-management.md).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Verwenden einer kleineren Ressourcenklasse zur Steigerung der Parallelität

Wenn Sie bemerken, dass Benutzerabfragen mit einer langen Verzögerung verbunden sind, werden Ihre Benutzer möglicherweise in größeren Ressourcenklassen ausgeführt und verbrauchen viele Parallelitätsslots, sodass für andere Abfragen eine Warteschlange entsteht.  Führen Sie `SELECT * FROM sys.dm_pdw_waits` aus, um zu ermitteln, ob Zeilen zurückgegeben werden. So können Sie feststellen, ob Benutzeranfragen in eine Warteschlange eingereiht werden.

Siehe auch: [Ressourcenklassen für die Workloadverwaltung](resource-classes-for-workload-management.md), [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="other-resources"></a>Weitere Ressourcen

Informationen zu häufigen Problemen und den entsprechenden Lösungen finden Sie im Artikel [Problembehandlung](sql-data-warehouse-troubleshoot.md) .

Wenn Sie die benötigten Informationen in diesem Artikel nicht gefunden haben, können Sie links auf dieser Seite über die Option „Nach Dokumenten suchen“ alle Dokumente zu Azure Synapse durchsuchen.  Auf der [Frageseite von Microsoft Q&A (Fragen und Antworten) für Azure Synapse](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html) können Sie anderen Benutzern und der Azure Synapse-Produktgruppe Fragen stellen. Wir überwachen dieses Forum aktiv, um sicherzustellen, dass Ihre Frage entweder von einem anderen Benutzer oder einem Mitarbeiter beantwortet wird.  

Falls Sie Ihre Fragen lieber über Stack Overflow stellen möchten, können Sie dazu auch das [Stack Overflow-Forum für Azure Synapse](https://stackoverflow.com/questions/tagged/azure-sqldw) nutzen.

Verwenden Sie die Seite [Azure Synapse-Feedback](https://feedback.azure.com/forums/307516-sql-data-warehouse), um Features anzufordern.  Indem Sie Ihre Anfragen hinzufügen oder für andere Anfragen stimmen, leisten Sie wertvolle Unterstützung beim Priorisieren von Funktionen.
