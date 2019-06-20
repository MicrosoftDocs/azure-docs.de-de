---
title: Kapazitätslimits – Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Die maximalen Werte, die für verschiedene Komponenten von Azure SQL Data Warehouse zulässig sind.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/14/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 62213ca1910ff26287bcd398d89fe7f8caf3cfac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514678"
---
# <a name="sql-data-warehouse-capacity-limits"></a>Kapazitätsgrenzen von SQL Data Warehouse
Die maximalen Werte, die für verschiedene Komponenten von Azure SQL Data Warehouse zulässig sind.

## <a name="workload-management"></a>Workloadverwaltung
| Category (Kategorie) | BESCHREIBUNG | Maximum |
|:--- |:--- |:--- |
| [Data Warehouse-Einheiten (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Max. DWUs für ein einzelnes SQL Data Warehouse | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Data Warehouse-Einheiten (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Standard-DTU pro Server |54.000<br></br>Standardmäßig verfügt jede SQL Server-Instanz (z.B. „myserver.database.windows.net“) über ein Kontingent von 54.000 DTUs, das bis zu DW6000c zulässt. Bei diesem Kontingentwert handelt es sich einfach um ein Sicherheitslimit. Sie können Ihr Kontingent erhöhen, indem Sie [ein Supportticket erstellen](sql-data-warehouse-get-started-create-support-ticket.md) und als Anfragetyp *Kontingent* auswählen.  Multiplizieren Sie zum Berechnen Ihrer DTU-Anforderungen die Anzahl der insgesamt benötigten DWUs mit 7,5, oder multiplizieren Sie die erforderlichen cDWUs mit 9,0. Beispiel:<br></br>DW6000 x 7,5 = 45.000 DTUs<br></br>DW6000c x 9.0 = 54.000 DTUs<br></br>Sie können den aktuellen DTU-Verbrauch im Portal über die Option „SQL-Server“ anzeigen. Sowohl angehaltene als auch nicht angehaltene Datenbanken werden in das DTU-Kontingent eingerechnet. |
| Datenbankverbindung |Maximale Anzahl gleichzeitig geöffneter Sitzungen |1024<br/><br/>Die Anzahl der gleichzeitig geöffneten Sitzungen variiert je nach ausgewählter DWU. DWU600c und höher unterstützt maximal 1024 offene Sitzungen. DWU500c und darunter unterstützt maximal 512 gleichzeitig geöffnete Sitzungen. Beachten Sie, dass die Anzahl der Abfragen begrenzt ist, die gleichzeitig ausgeführt werden können. Wenn der Grenzwert überschritten wird, gelangt die Anforderung in eine interne Warteschlange, in der sie auf die Verarbeitung wartet. |
| Datenbankverbindung |Maximaler Arbeitsspeicher für vorbereitete Anweisungen |20 MB |
| [Workloadverwaltung](resource-classes-for-workload-management.md) |Maximale Anzahl gleichzeitiger Abfragen |128<br/><br/> SQL Data Warehouse kann maximal 128 gleichzeitige Abfragen ausführen. Darüber hinausgehende Abfragen werden in eine Warteschlange eingereiht.<br/><br/>Die Anzahl der gleichzeitigen Abfragen kann abnehmen, wenn Benutzern höhere Ressourcenklassen zugeordnet werden oder wenn SQL Data Warehouse eine niedrigere Einstellung für [Data Warehouse-Einheit](memory-and-concurrency-limits.md) aufweist. Ausführungen einiger Abfragen, z.B. DMV-Abfragen, sind immer zulässig und haben keine Auswirkungen auf das Limit für gleichzeitige Abfragen. Ausführlichere Informationen zur Ausführung gleichzeitiger Abfragen finden Sie im Artikel zu [Parallelitätshöchstwerten](memory-and-concurrency-limits.md#concurrency-maximums). |
| [tempdb](sql-data-warehouse-tables-temporary.md) |Maximale GB |399GB pro DW100. Daher ist „tempdb“ in DWU1000 3,99 TB groß. |

## <a name="database-objects"></a>Datenbankobjekte
| Category (Kategorie) | BESCHREIBUNG | Maximum |
|:--- |:--- |:--- |
| Datenbank |Max. Größe | Gen1: 240TB komprimiert auf dem Datenträger. Dieser Speicherplatz ist unabhängig von „tempdb“ oder vom Protokollspeicherplatz und daher für permanente Tabellen reserviert.  Komprimierung von gruppiertem Columnstore wird auf 5X geschätzt.  Diese Komprimierung ermöglicht der Datenbank einen Zuwachs auf ungefähr 1PB, wenn alle Tabellen mit gruppiertem Columnstore konfiguriert sind (die Standardtabellentyp). <br/><br/> Gen2: 240TB für Rowstore- und unbegrenzter Speicher für Columnstore-Tabellen |
| Table |Max. Größe |60 TB komprimiert auf dem Datenträger |
| Table |Tabellen pro Datenbank | 100.000 |
| Table |Spalten pro Tabelle |1024 Spalten |
| Table |Bytes pro Spalte |Abhängig von der Spalte [Datentyp](sql-data-warehouse-tables-data-types.md). Grenzwert ist 8.000 für Char-Datentypen, 4.000 für Nvarchar oder 2GB für MAX-Datentypen. |
| Table |Bytes pro Zeile, definierte Größe |8\.060 Bytes<br/><br/>Die Anzahl von Bytes pro Zeile wird auf die gleiche Weise wie bei SQL Server mit aktivierter Seitenkomprimierung berechnet. Wie SQL Server unterstützt SQL Data Warehouse die Speicherung von Zeilenüberlaufsdaten, sodass **Spalten variabler Länge** aus der Zeile verschoben werden können. Wenn Zeilen variabler Länge aus der Zeile verschoben werden, wird nur der 24-Byte-Stamm im Hauptdatensatz gespeichert. Weitere Informationen finden Sie unter [Zeilenüberlaufdaten mit über 8 KB](https://msdn.microsoft.com/library/ms186981.aspx). |
| Table |Partitionen pro Tabelle |15.000<br/><br/>Um eine hohe Leistung zu erzielen, empfehlen wir, die Anzahl der Partitionen zu minimieren, die Sie zum Erfüllen Ihrer Geschäftsanforderungen benötigen. Mit einer steigenden Anzahl von Partitionen wächst der Verarbeitungsaufwand für Datendefinitionssprache (DDL)- und Datenbearbeitungssprache (DML)-Vorgänge, was zu Leistungseinbußen führt. |
| Table |Zeichen pro Partitionsbegrenzungswert. |4000 |
| Index |Nicht gruppierte Indizes pro Tabelle. |50<br/><br/>Gilt nur für Rowstore-Tabellen |
| Index |Gruppierte Indizes pro Tabelle. |1<br><br/>Gilt sowohl für Rowstore- als auch für Columnstore-Tabellen |
| Index |Größe des Indexschlüssels. |900 Bytes<br/><br/>Gilt nur für Rowstore-Indizes.<br/><br/>Indizes für „varchar“-Spalten mit einer maximalen Größe von mehr als 900 Bytes können erstellt werden, wenn die vorhandenen Daten in den Spalten bei der Indexerstellung nicht größer als 900 Bytes sind. Anschließende auf die Spalten angewendete INSERT- oder UPDATE-Anweisungen, die bewirken, dass die Gesamtgröße 900 Bytes überschreitet, haben allerdings keinen Erfolg. |
| Index |Schlüsselspalten pro Index. |16<br/><br/>Gilt nur für Rowstore-Indizes. Gruppierte Columnstore-Indizes enthalten alle Spalten. |
| Statistiken |Größe der kombinierten Spaltenwerte. |900 Bytes |
| Statistiken |Spalten pro Statistikobjekt. |32 |
| Statistiken |Für Spalten pro Tabelle erstellte Statistiken. |30.000 |
| Gespeicherte Prozeduren |Maximale Schachtelungsebenen. |8 |
| Sicht |Spalten pro Sicht |1024 |

## <a name="loads"></a>Lädt
| Category (Kategorie) | BESCHREIBUNG | Maximum |
|:--- |:--- |:--- |
| PolyBase-Auslastung |MB pro Zeile |1<br/><br/>PolyBase lädt Zeilen, die kleiner als 1 MB sind. Das Laden von LOB-Datentypen in Tabellen mit einem gruppierten Columnstore-Index (CCI) wird nicht unterstützt.<br/><br/> |

## <a name="queries"></a>Abfragen
| Category (Kategorie) | BESCHREIBUNG | Maximum |
|:--- |:--- |:--- |
| Abfragen |In Warteschlange gestellte Abfragen von Benutzertabellen. |1000 |
| Abfragen |Gleichzeitige Abfragen von Systemsichten. |100 |
| Abfragen |In Warteschlange gestellte Abfragen von Systemsichten |1000 |
| Abfragen |Maximale Parameter |2098 |
| Batch |Maximale Größe |65.536*4096 |
| SELECT-Ergebnisse |Spalten pro Zeile |4096<br/><br/>Das Ergebnis einer SELECT-Anweisung kann nie mehr als 4.096 Spalten pro Zeile enthalten. Es gibt keine Garantie, dass Sie stets über 4096 verfügen. Wenn der Abfrageplan eine temporäre Tabelle erfordert, gilt möglicherweise der Maximalwert von 1024 Spalten pro Tabelle. |
| SELECT |Geschachtelte Unterabfragen |32<br/><br/>In einer SELECT-Anweisung sind maximal 32 geschachtelte Unterabfragen zulässig. Es gibt keine Garantie, dass Sie stets über 32 verfügen. Ein JOIN-Befehl kann z. B. eine Unterabfrage in den Abfrageplan einführen. Die Anzahl der Unterabfragen kann auch durch den verfügbaren Speicher eingeschränkt werden. |
| SELECT |Spalten pro JOIN |1024 Spalten<br/><br/>Für einen JOIN sind maximal 1.024 Spalten zulässig. Es gibt keine Garantie, dass Sie stets über 1024 verfügen. Wenn der JOIN-Plan eine temporäre Tabelle mit mehr Spalten als das JOIN-Ergebnis erfordert, gilt die Grenze von 1024 für die temporäre Tabelle. |
| SELECT |Bytes pro GROUP BY-Spalten. |8\.060<br/><br/>Die Maximalgröße von Spalten in der GROUP BY-Klausel beträgt 8.060 Bytes. |
| SELECT |Bytes pro ORDER BY-Spalten |8\.060 Bytes<br/><br/>Die Maximalgröße von Spalten in der ORDER BY-Klausel beträgt 8.060 Bytes. |
| Bezeichner pro Anweisung |Anzahl referenzierter Bezeichner |65.535<br/><br/>SQL Data Warehouse beschränkt die Anzahl von Bezeichnern, die in einem einzelnen Ausdruck einer Abfrage enthalten sein können. Das Überschreiten dieses Werts führt zum SQL Server-Fehler 8632. Weitere Informationen finden Sie unter [Interner Fehler: Ein Ausdrucksdienstelimit wurde erreicht.](https://support.microsoft.com/en-us/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a) |
| Zeichenfolgenliterale | Anzahl von Zeichenfolgenliteralen in einer Anweisung | 20.000 <br/><br/>SQL Data Warehouse beschränkt die Anzahl von Zeichenfolgenkonstanten in einem einzelnen Ausdruck einer Abfrage. Das Überschreiten dieses Werts führt zum SQL Server-Fehler 8632.|

## <a name="metadata"></a>Metadaten
| Systemsicht | Maximale Anzahl von Zeilen |
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

## <a name="next-steps"></a>Nächste Schritte
Empfehlungen zur Verwendung von SQL Data Warehouse finden Sie unter [Cheatsheet](cheat-sheet.md).
