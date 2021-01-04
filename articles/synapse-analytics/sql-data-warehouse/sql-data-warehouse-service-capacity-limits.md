---
title: Kapazitätsgrenzen für dedizierten SQL-Pool
description: Die maximalen Werte, die für verschiedene Komponenten des dedizierten SQL-Pools in Azure Synapse Analytics zulässig sind.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 2/19/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 7f0eff28533d8cf736d032aff61454a49bcf379e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449729"
---
# <a name="capacity-limits-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Kapazitätsgrenzen für dedizierten SQL-Pool in Azure Synapse Analytics

Die maximalen Werte, die für verschiedene Komponenten des dedizierten SQL-Pools in Azure Synapse Analytics zulässig sind.

## <a name="workload-management"></a>Verwalten von Arbeitsauslastungen

| Category | BESCHREIBUNG | Maximum |
|:--- |:--- |:--- |
| [Data Warehouse-Einheiten (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Max. DWUs für einen dedizierten SQL-Pool  | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Data Warehouse-Einheiten (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Standard-DTU pro Server |54.000<br></br>Standardmäßig verfügt jede SQL Server-Instanz (z.B. „myserver.database.windows.net“) über ein Kontingent von 54.000 DTUs, das bis zu DW5000c zulässt. Bei diesem Kontingentwert handelt es sich einfach um ein Sicherheitslimit. Sie können Ihr Kontingent erhöhen, indem Sie [ein Supportticket erstellen](sql-data-warehouse-get-started-create-support-ticket.md) und als Anfragetyp *Kontingent* auswählen.  Multiplizieren Sie zum Berechnen Ihrer DTU-Anforderungen die Anzahl der insgesamt benötigten DWUs mit 7,5, oder multiplizieren Sie die insgesamt erforderlichen cDWUs mit 9,5. Beispiel:<br></br>DW6000 x 7,5 = 45.000 DTUs<br></br>DW5000c x 9,5 = 47.500 DTUs<br></br>Sie können den aktuellen DTU-Verbrauch im Portal über die Option „SQL-Server“ anzeigen. Sowohl angehaltene als auch nicht angehaltene Datenbanken werden in das DTU-Kontingent eingerechnet. |
| Datenbankverbindung |Maximale Anzahl gleichzeitig geöffneter Sitzungen |1024<br/><br/>Die Anzahl der gleichzeitig geöffneten Sitzungen variiert je nach ausgewählter DWU. DWU600c und höher unterstützt maximal 1024 offene Sitzungen. DWU500c und darunter unterstützt maximal 512 gleichzeitig geöffnete Sitzungen. Beachten Sie, dass die Anzahl der Abfragen begrenzt ist, die gleichzeitig ausgeführt werden können. Wenn der Grenzwert überschritten wird, gelangt die Anforderung in eine interne Warteschlange, in der sie auf die Verarbeitung wartet. |
| Datenbankverbindung |Maximaler Arbeitsspeicher für vorbereitete Anweisungen |20 MB |
| [Verwaltung von Arbeitsauslastungen](resource-classes-for-workload-management.md) |Maximale Anzahl gleichzeitiger Abfragen |128<br/><br/>  Maximal werden 128 gleichzeitige Abfragen ausgeführt, und verbleibende Abfragen werden in die Warteschlange eingereiht.<br/><br/>Die Anzahl der gleichzeitigen Abfragen kann abnehmen, wenn Benutzern höhere Ressourcenklassen zugeordnet werden oder die [Data Warehouse-Einheit](memory-concurrency-limits.md)-Einstellung gesenkt wird. Ausführungen einiger Abfragen, z.B. DMV-Abfragen, sind immer zulässig und haben keine Auswirkungen auf das Limit für gleichzeitige Abfragen. Ausführlichere Informationen zur Ausführung gleichzeitiger Abfragen finden Sie im Artikel zu [Parallelitätshöchstwerten](memory-concurrency-limits.md). |
| [tempdb](sql-data-warehouse-tables-temporary.md) |Maximale GB |399 GB pro DW100c. „tempdb“ ist in DWU1000c 3,99 TB groß. |
||||

## <a name="database-objects"></a>Datenbankobjekte

| Category | BESCHREIBUNG | Maximum |
|:--- |:--- |:--- |
| Datenbank |Max. Größe | Gen1: 240TB komprimiert auf dem Datenträger. Dieser Speicherplatz ist unabhängig von „tempdb“ oder vom Protokollspeicherplatz und daher für permanente Tabellen reserviert.  Komprimierung von gruppiertem Columnstore wird auf 5X geschätzt.  Diese Komprimierung ermöglicht der Datenbank einen Zuwachs auf ungefähr 1PB, wenn alle Tabellen mit gruppiertem Columnstore konfiguriert sind (die Standardtabellentyp). <br/><br/> Gen2: Unbegrenzter Speicher für Columnstore-Tabellen.  Der Rowstore-Teil der Datenbank ist auf dem Datenträger weiterhin auf 240 TB (komprimiert auf dem Datenträger) begrenzt. |
| Tabelle |Max. Größe |Unbegrenzte Größe für Columnstore-Tabellen. <br>60 TB für Rowstore-Tabellen, die auf dem Datenträger komprimiert sind. |
| Tabelle |Tabellen pro Datenbank | 100.000 |
| Tabelle |Spaltenanzahl pro Tabelle |1024 Spalten |
| Tabelle |Bytes pro Spalte |Abhängig von der Spalte [Datentyp](sql-data-warehouse-tables-data-types.md). Grenzwert ist 8.000 für Char-Datentypen, 4.000 für Nvarchar oder 2GB für MAX-Datentypen. |
| Tabelle |Bytes pro Zeile, definierte Größe |8\.060 Bytes<br/><br/>Die Anzahl von Bytes pro Zeile wird auf die gleiche Weise wie bei SQL Server mit aktivierter Seitenkomprimierung berechnet. Wie bei SQL Server wird die Speicherung von Zeilenüberlaufsdaten unterstützt, sodass **Spalten variabler Länge** aus der Zeile verschoben werden können. Wenn Zeilen variabler Länge aus der Zeile verschoben werden, wird nur der 24-Byte-Stamm im Hauptdatensatz gespeichert. Weitere Informationen finden Sie unter [Zeilenüberlaufdaten mit über 8 KB](https://msdn.microsoft.com/library/ms186981.aspx). |
| Tabelle |Partitionen pro Tabelle |15.000<br/><br/>Um eine hohe Leistung zu erzielen, empfehlen wir, die Anzahl der Partitionen zu minimieren, die Sie zum Erfüllen Ihrer Geschäftsanforderungen benötigen. Mit einer steigenden Anzahl von Partitionen wächst der Verarbeitungsaufwand für Datendefinitionssprache (DDL)- und Datenbearbeitungssprache (DML)-Vorgänge, was zu Leistungseinbußen führt. |
| Tabelle |Zeichen pro Partitionsbegrenzungswert. |4000 |
| Index |Nicht gruppierte Indizes pro Tabelle. |50<br/><br/>Gilt nur für Rowstore-Tabellen |
| Index |Gruppierte Indizes pro Tabelle. |1<br><br/>Gilt sowohl für Rowstore- als auch für Columnstore-Tabellen |
| Index |Größe des Indexschlüssels. |900 Bytes<br/><br/>Gilt nur für Rowstore-Indizes.<br/><br/>Indizes für „varchar“-Spalten mit einer maximalen Größe von mehr als 900 Bytes können erstellt werden, wenn die vorhandenen Daten in den Spalten bei der Indexerstellung nicht größer als 900 Bytes sind. Anschließende auf die Spalten angewendete INSERT- oder UPDATE-Anweisungen, die bewirken, dass die Gesamtgröße 900 Bytes überschreitet, haben allerdings keinen Erfolg. |
| Index |Schlüsselspalten pro Index. |16<br/><br/>Gilt nur für Rowstore-Indizes. Gruppierte Columnstore-Indizes enthalten alle Spalten. |
| Statistik |Größe der kombinierten Spaltenwerte. |900 Bytes |
| Statistik |Spalten pro Statistikobjekt. |32 |
| Statistik |Für Spalten pro Tabelle erstellte Statistiken. |30.000 |
| Gespeicherte Prozeduren |Maximale Schachtelungsebenen. |8 |
| Sicht |Spalten pro Sicht |1\.024 |
||||

## <a name="loads"></a>Lädt

| Category | BESCHREIBUNG | Maximum |
|:--- |:--- |:--- |
| PolyBase-Auslastung |MB pro Zeile |1<br/><br/>PolyBase lädt Zeilen, die kleiner als 1 MB sind. Das Laden von LOB-Datentypen in Tabellen mit einem gruppierten Columnstore-Index (CCI) wird nicht unterstützt.<br/> |
|PolyBase-Auslastung|Gesamtanzahl de Dateien|1\.000.000<br/><br/>Polybase-Lasten dürfen ein Limit von 1 Mio. Dateien nicht überschreiten. Möglicherweise erhalten Sie die folgende Fehlermeldung: **Fehler beim Vorgang, weil die Teilungsanzahl die Obergrenze von 1.000.000 überschreitet.**|

## <a name="queries"></a>Abfragen

| Category | BESCHREIBUNG | Maximum |
|:--- |:--- |:--- |
| Abfrage |In Warteschlange gestellte Abfragen von Benutzertabellen. |1000 |
| Abfrage |Gleichzeitige Abfragen von Systemsichten. |100 |
| Abfrage |In Warteschlange gestellte Abfragen von Systemsichten |1000 |
| Abfrage |Maximale Parameter |2098 |
| Batch |Maximale Größe |65.536*4096 |
| SELECT-Ergebnisse |Spalten pro Zeile |4096<br/><br/>Das Ergebnis einer SELECT-Anweisung kann nie mehr als 4.096 Spalten pro Zeile enthalten. Es gibt keine Garantie, dass Sie stets über 4096 verfügen. Wenn der Abfrageplan eine temporäre Tabelle erfordert, gilt möglicherweise der Maximalwert von 1024 Spalten pro Tabelle. |
| SELECT |Geschachtelte Unterabfragen |32<br/><br/>In einer SELECT-Anweisung sind maximal 32 geschachtelte Unterabfragen zulässig. Es gibt keine Garantie, dass Sie stets über 32 verfügen. Ein JOIN-Befehl kann z. B. eine Unterabfrage in den Abfrageplan einführen. Die Anzahl der Unterabfragen kann auch durch den verfügbaren Speicher eingeschränkt werden. |
| SELECT |Spalten pro JOIN |1024 Spalten<br/><br/>Für einen JOIN sind maximal 1.024 Spalten zulässig. Es gibt keine Garantie, dass Sie stets über 1024 verfügen. Wenn der JOIN-Plan eine temporäre Tabelle mit mehr Spalten als das JOIN-Ergebnis erfordert, gilt die Grenze von 1024 für die temporäre Tabelle. |
| SELECT |Bytes pro GROUP BY-Spalten. |8060<br/><br/>Die Maximalgröße von Spalten in der GROUP BY-Klausel beträgt 8.060 Bytes. |
| SELECT |Bytes pro ORDER BY-Spalten |8\.060 Bytes<br/><br/>Die Maximalgröße von Spalten in der ORDER BY-Klausel beträgt 8.060 Bytes. |
| Bezeichner pro Anweisung |Anzahl referenzierter Bezeichner |65.535<br/><br/> Die Anzahl von Bezeichnern, die in einem einzelnen Ausdruck einer Abfrage enthalten sein können, ist beschränkt. Das Überschreiten dieses Werts führt zum SQL Server-Fehler 8632. Weitere Informationen finden Sie unter [Interner Fehler: Ein Ausdrucksdienstelimit wurde erreicht.](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a) |
| Zeichenfolgenliterale | Anzahl von Zeichenfolgenliteralen in einer Anweisung | 20.000 <br/><br/>Die Anzahl von Zeichenfolgenkonstanten in einem einzelnen Ausdruck einer Abfrage ist beschränkt. Das Überschreiten dieses Werts führt zum SQL Server-Fehler 8632.|
||||

## <a name="metadata"></a>Metadaten

| Systemsicht | Maximale Zeilenanzahl |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10.000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Gesamtanzahl der DMS-Worker für die letzten 1000 SQL-Anforderungen. |
| sys.dm_pdw_errors |10.000 |
| sys.dm_pdw_exec_requests |10.000 |
| sys.dm_pdw_exec_sessions |10.000 |
| sys.dm_pdw_request_steps |Die Gesamtzahl der Schritte für die letzten 1000 SQL-Anforderungen, die in „sys.dm_pdw_exec_requests“ gespeichert sind. |
| sys.dm_pdw_os_event_logs |10.000 |
| sys.dm_pdw_sql_requests |Die letzten 1000 SQL-Anforderungen, die in „sys.dm_pdw_exec_requests“ gespeichert sind. |
|||

## <a name="next-steps"></a>Nächste Schritte

Empfehlungen zur Verwendung von Azure Synapse finden Sie unter [Cheatsheet](cheat-sheet.md).
