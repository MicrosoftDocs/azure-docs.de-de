---
title: Leistungsoptimierung mit einem sortierten gruppierten Columnstore-Index
description: Empfehlungen und Überlegungen, die Sie kennen sollten, wenn Sie einen sortierten gruppierten Columnstore-Index zur Verbesserung Ihrer Abfrageleistung in dedizierten SQL-Pools verwenden
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: afb6efcee2ad4f5cf25a411eed353ff2fc27d75c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460792"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Leistungsoptimierung mit einem sortierten gruppierten Columnstore-Index  

Wenn Benutzer eine Columnstore-Tabelle in dedizierten SQL-Pools abfragen, überprüft der Optimierer die minimalen und maximalen Werte, die in den einzelnen Segmenten gespeichert sind.  Segmente, die sich außerhalb der Grenzen des Abfrageprädikats befinden, werden nicht vom Datenträger in den Arbeitsspeicher gelesen.  Die Leistung einer Abfrage kann gesteigert werden, wenn die Anzahl der zu lesenden Segmente und deren Gesamtgröße gering ist.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Vergleich sortierter und nicht sortierter gruppierter Columnstore-Indizes

Standardmäßig erstellt eine interne Komponente (Index-Generator) für jede Tabelle, die ohne Indexoption erstellt wurde, einen nicht geordneten gruppierten Columnstore-Index (CCI).  Die Daten den einzelnen Spalten werden in einem separaten CCI-Zeilengruppensegment komprimiert.  Für den Wertebereich jedes Segments gibt es Metadaten, sodass Segmente, die sich außerhalb der Grenzen des Abfrageprädikats befinden, während der Abfrageausführung nicht von der Festplatte gelesen werden.  CCI bietet den höchsten Grad an Datenkomprimierung und verringert die Größe der zu lesenden Segmente, damit Abfragen schneller ausgeführt werden. Da der Index-Generator die Daten jedoch nicht sortiert, bevor er sie in Segmente komprimiert, können Segmente mit überlappenden Wertebereichen auftreten, was dazu führt, dass Abfragen mehr Segmente vom Datenträger lesen muss und die Fertigstellung länger dauert.  

Beim Erstellen einer geordneten CCI-Tabelle sortiert die Engine des dedizierten SQL-Pools die vorhandenen Daten im Arbeitsspeicher nach dem/den Sortierschlüssel(n), bevor sie der Index-Generator in Indexsegmente komprimiert.  Bei sortierten Daten wird die Überlappung von Segmenten verringert, sodass Abfragen Segmente effizienter entfernen können und somit eine schnellere Leistung aufweisen, da die Anzahl der Segmente, die vom Datenträger gelesen werden sollen, kleiner ist.  Die Überlappung von Segmenten lässt sich vermeiden, wenn alle Daten im Arbeitsspeicher gleichzeitig sortiert werden.  Aufgrund großer Tabellen in Data Warehouses kommt dieses Szenario nicht häufig vor.  

Wenn Sie die Segmentbereiche für eine Spalte überprüfen möchten, führen Sie den folgenden Befehl unter Angabe Ihres Tabellen- und Spaltennamens aus:

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id 


```

> [!NOTE] 
> In einer sortierten CCI-Tabelle werden die neuen Daten aus dem gleichen Batch von DML- oder Datenladevorgängen innerhalb dieses Batches sortiert, es findet jedoch keine globale Sortierung aller Daten in der Tabelle statt.  Benutzer können die geordnete CCI-Tabelle neu erstellen (REBUILD), um alle Daten in der Tabelle zu sortieren.  Im dedizierten SQL-Pool ist die Neuerstellung des Columnstore-Indexes ein Offlinevorgang.  Bei einer partitionierten Tabelle erfolgt die Neuerstellung (REBUILD) der Partitionen nacheinander.  Die Daten in der Partition, die neu erstellt wird, sind „offline“ und nicht verfügbar, bis die Neuerstellung (REBUILD) für diese Partition beendet ist. 

## <a name="query-performance"></a>Abfrageleistung

Der Leistungsgewinn einer Abfrage aus einer geordneten CCI-Tabelle hängt von den Abfragemustern, der Größe der Daten, der Art der Sortierung der Daten, der physischen Struktur von Segmenten und der DWU und Ressourcenklasse ab, die für die Ausführung der Abfrage ausgewählt wurden.  Benutzer sollten alle diese Faktoren überprüfen, bevor Sie die Ordnungsspalten beim Entwerfen einer geordneten CCI-Tabelle auswählen.

Abfragen mit all diesen Mustern werden normalerweise schneller mit einer geordneten CCI-Tabelle ausgeführt.  
1. Die Abfragen verfügen über Gleichheits-, Ungleichheits- oder Bereichsprädikate.
1. Die Prädikatspalten und die geordneten CCI-Spalten sind identisch.  
1. Die Prädikatspalten werden in derselben Reihenfolge wie die Spaltenordnungszahl geordneter CCI-Spalten verwendet.  
 
In diesem Beispiel verfügt die Tabelle T1 über einen gruppierten Columnstore-Index, der in der Reihenfolge Col_C, Col_B und Col_A sortiert ist.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

Die Leistung von Abfrage 1 kann mehr von geordneten CCI-Tabellen als die anderen drei Abfragen profitieren. 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>Leistung beim Laden von Daten

Die Leistung beim Laden von Daten in eine sortierte CCI-Tabelle ähnelt dem Laden einer partitionierten Tabelle.  Das Laden von Daten in eine geordnete CCI-Tabelle kann aufgrund des Datensortiervorgangs länger als das Laden in eine nicht geordnete CCI-Tabelle dauern. Abfragen können jedoch später mit einer geordneten CCI-Tabelle schneller ausgeführt werden.  

Im Folgenden finden Sie ein Beispiel für einen Leistungsvergleich beim Laden von Daten in Tabellen mit unterschiedlichen Schemas.

![Balkendiagramm, das den Leistungsvergleich beim Laden von Daten in Tabellen mit unterschiedlichen Schemas zeigt.](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Im Folgenden finden Sie ein Beispiel für einen Abfrageleistungsvergleich zwischen einer CCI-Tabelle und einer geordneten CCI-Tabelle.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Verringern der Überlappung von Segmenten

Die Anzahl der sich überlappenden Segmente hängt von der Größe der zu sortierenden Daten, dem verfügbaren Arbeitsspeicher und der Einstellung des maximalen Parallelitätsgrads (MAXDOP) bei der Erstellung einer geordneten CCI-Tabelle ab. Im Folgenden finden Sie Optionen zum Reduzieren der Segmentüberlappung bei der Erstellung einer geordneten CCI-Tabelle.

- Verwenden Sie die xlargerc-Ressourcenklasse für eine höhere DWU, um mehr Arbeitsspeicher für die Datensortierung zu ermöglichen, bevor der Index-Generator die Daten in Segmente komprimiert.  In einem Indexsegment kann der physische Speicherort der Daten nicht mehr geändert werden.  Innerhalb eines Segments oder segmentübergreifend gibt es keine Datensortierung.  

- Erstellen Sie eine geordnete CCI-Tabelle mit MAXDOP = 1.  Jeder Thread, der für die Erstellung eines sortierten CCI verwendet wird, verarbeitet eine Teilmenge an Daten und sortiert diese lokal.  Für Daten, die von unterschiedlichen Threads sortiert wurden, gibt es keine globale Sortierung.  Mithilfe paralleler Threads kann die Zeit zum Erstellen eines sortierten CCI verkürzt werden. Dabei werden jedoch mehr überlappende Segmente generiert als bei der Verwendung eines einzelnen Threads.  Derzeit wird die MAXDOP-Option nur beim Erstellen einer geordneten CCI-Tabelle mit dem Befehl CREATE TABLE AS SELECT unterstützt.  Das Erstellen einer geordneten CCI-Tabelle über die Befehle CREATE INDEX oder CREATE TABLE unterstützt die MAXDOP-Option nicht. Beispiel:

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- Sortieren Sie die Daten vorab nach den Sortierschlüsseln, bevor Sie sie in Tabellen laden.

Im Folgenden finden Sie ein Beispiel für eine Verteilung einer geordneten CCI-Tabelle ohne Segmentüberlappungen, bei dem die oben genannten Empfehlungen beachtet wurden. Die geordnete CCI-Tabelle wird in einer DWU1000c-Datenbank über CTAS aus einer Heaptabelle mit 20 GB mithilfe von MAXDOP 1 und xlargerc erstellt.  Die CCI-Tabelle wird in einer BIGINT-Spalte ohne Duplikate sortiert.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Erstellen eines sortierten CCI für große Tabellen

Das Erstellen eines sortierten CCI ist ein Offlinevorgang.  Bei Tabellen ohne Partitionen sind die Daten für Benutzer erst verfügbar, wenn der Erstellungsprozess des sortierten CCI abgeschlossen ist.   Da die Engine sortierte CCI-Partitionen für partitionierte Tabellen nach Partition erstellt, können die Benutzer auf die Daten in den Partitionen weiterhin zugreifen, für die die Erstellung des sortierten CCI nicht gerade erfolgt.   Mit dieser Option können Sie die Ausfallzeiten beim Erstellen eines sortierten CCI für große Tabellen minimieren: 

1.    Erstellen Sie Partitionen für die große Zieltabelle (Table_A).
2.    Erstellen Sie eine leere sortierte CCI-Tabelle (Table_B), die dieselben Tabellen- und Partitionsschemas wie Tabelle A aufweist.
3.    Fügen Sie eine Partition von Tabelle A in Tabelle B ein.
4.    Führen Sie „ALTER INDEX <Ordered_CCI_Index> ON <Table_B> REBUILD PARTITION = <Partition_ID>“ für Tabelle B aus, um die eingefügte Partition neu zu erstellen.  
5.    Wiederholen Sie die Schritte 3 und 4 für jede Partition in „Table_A“.
6.    Nachdem alle Partitionen von „Table_A“ in „Table_B“ eingefügt und neu erstellt wurden, löschen Sie „Table_A“ und benennen „Table_B“ in „Table_A“ um. 

## <a name="examples"></a>Beispiele

**A. So suchen Sie sortierte Spalten und die Ordnungszahl der Sortierung:**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Fügen Sie Spalten aus der Sortierliste hinzu, oder entfernen Sie sie, wenn Sie die Ordnungszahl der Spalte ändern möchten oder von einem CCI zu einem sortierten CCI wechseln möchten:**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht](sql-data-warehouse-overview-develop.md).
