---
title: Leistungsoptimierung mit Zwischenspeichern von Resultsets
description: Übersicht über das Feature „Zwischenspeichern von Resultsets“ für dedizierte SQL-Pools in Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: 2b54277d0306244dc4ab6740fdd30e52668dd63c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460768"
---
# <a name="performance-tuning-with-result-set-caching"></a>Leistungsoptimierung mit Zwischenspeichern von Resultsets

Wenn das Zwischenspeichern von Resultsets aktiviert ist, werden die Abfrageergebnisse von dedizierten SQL-Pools in der Benutzerdatenbank automatisch zwischengespeichert, damit sie wiederholt verwendet werden können.  Dies ermöglicht es, dass nachfolgende Abfrageausführungen die Ergebnisse direkt aus dem permanenten Cache erhalten und so keine Neuberechnung erforderlich ist.   Durch das Zwischenspeichern von Resultsets wird die Abfrageleistung verbessert und die Nutzung von Computeressourcen verringert.  Außerdem belegen Abfragen, die das zwischengespeicherte Resultset verwenden, keine Parallelitätsslots und werden daher auf vorhandene Parallelitätslimits nicht angerechnet. Aus Sicherheitsgründen können Benutzer nur dann auf die zwischengespeicherten Ergebnisse zugreifen, wenn sie über dieselben Datenzugriffsberechtigungen wie die Benutzer verfügen, die die zwischengespeicherten Ergebnisse erstellen.  

## <a name="key-commands"></a>Schlüsselbefehle

[Aktivieren/Deaktivieren des Zwischenspeicherns von Resultsets für eine Benutzerdatenbank](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Aktivieren/Deaktivieren des Zwischenspeicherns von Resultsets für eine Sitzung](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Überprüfen der Größe des zwischengespeicherten Resultsets](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

[Bereinigen des Caches](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Nicht zwischengespeicherte Elemente  

Sobald das Zwischenspeichern von Resultsets für eine Datenbank aktiviert wurde, werden die Ergebnisse für alle Abfragen so lange zwischengespeichert, bis der Cache voll ist – mit Ausnahme der folgenden Abfragen:

- Abfragen mit integrierten Funktionen oder Laufzeitausdrücken, die nicht deterministisch sind, auch wenn sich die Daten oder die Abfrage der Basistabellen nicht geändert haben bzw. hat. Beispiele wären etwa „DateTime.Now()“ und „GetDate()“.
- Abfragen, die benutzerdefinierte Funktionen verwenden
- Abfragen, die Tabellen mit aktivierter Sicherheit auf Zeilenebene oder Spaltenebene verwenden
- Abfragen, die Daten mit einer Zeilengröße von mehr als 64 KB zurückgeben
- Abfragen, die Daten in großem Umfang zurückgeben (>10 GB) 
>[!NOTE]
> - Einige nicht deterministische Funktionen und Laufzeitausdrücke können bei wiederholten Abfragen für die gleichen Daten deterministisch sein. Ein Beispiel wäre etwa „ROW_NUMBER()“.  
> - Verwenden Sie „ORDER BY“ in Ihrer Abfrage, wenn die Zeilenreihenfolge/-sequenz im Abfrageresultset für Ihre Anwendungslogik wichtig ist.
> - Wenn die Daten in den ORDER BY-Spalten nicht eindeutig sind, gibt es für Zeilen mit identischen Werten in den ORDER BY-Spalten keine garantierte Zeilenreihenfolge. Dabei spielt es keine Rolle, ob die Zwischenspeicherung von Resultsets aktiviert ist.

> [!IMPORTANT]
> Die Vorgänge zum Erstellen des Resultsetcaches und zum Abrufen von Daten aus dem Cache erfolgen im Steuerknoten einer Instanz des dedizierten SQL-Pools.
> Wenn das Zwischenspeichern von Resultsets aktiviert ist (ON), können ausgeführte Abfragen, die ein großes Resultset zurückgeben (z. B. >1 GB), eine starke Drosselung in dem Kontrollknoten verursachen und die gesamte Abfrageantwort auf der Instanz verlangsamen.  Diese Abfragen werden häufig beim Durchsuchen von Daten oder ETL-Vorgängen verwendet. Benutzer sollten das Zwischenspeichern von Resultsets auf der Datenbank deaktivieren, bevor sie diese Abfragetypen ausführen, um den Kontrollknoten nicht zu belasten und Leistungsprobleme zu verursachen.  

Führen Sie diese Abfrage für die Zeit aus, die zum Zwischenspeichern von Resultsets für eine Abfrage benötigt wird:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

Hier sehen Sie eine Beispielausgabe für eine Abfrage, die mit deaktiviertem Zwischenspeichern von Resultsets ausgeführt wurde.

![Screenshot, der Abfrageergebnisse, einschließlich Standorttyp und Befehl, zeigt.](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Hier sehen Sie eine Beispielausgabe für eine Abfrage, die mit aktiviertem Zwischenspeichern von Resultsets ausgeführt wurde.

![Screenshot, der Abfrageergebnisse mit dem ausgewählten Befehl * aus „[D W ResultCache D b] dot d b o“ aufgerufen zeigt.](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Wann zwischengespeicherte Ergebnisse verwendet werden

Das zwischengespeicherte Resultset wird für eine Abfrage erneut verwendet, wenn alle folgenden Anforderungen erfüllt sind:

- Der Benutzer, der die Abfrage ausführt, kann auf alle Tabellen zugreifen, auf die in der Abfrage verwiesen wird.
- Es gibt eine exakte Übereinstimmung zwischen der neuen Abfrage und der vorherigen Abfrage, die den Resultsetcache generiert hat.
- In den Tabellen, aus denen das zwischengespeicherte Resultset generiert wurde, gab es keine Daten- oder Schemaänderungen.

Führen Sie diesen Befehl aus, um zu überprüfen, ob eine Abfrage mit einem Ergebniscachetreffer oder -fehler ausgeführt wurde. Die Spalte „result_cache_hit“ gibt folgende Werte zurück: 1 für Cachetreffer, 0 für Cachefehler und negative Werte für Gründe, warum das Zwischenspeichern von Resultsets nicht verwendet wurde. Weitere Informationen hierzu finden Sie unter [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Verwalten von zwischengespeicherten Ergebnissen

Die maximale Größe des Resultsetcaches ist 1 TB pro Datenbank.  Wenn sich die zugrundeliegenden Abfragedaten ändern, werden die zwischengespeicherten Ergebnisse automatisch ungültig gemacht.  

Die Cacheentfernung wird von dedizierten SQL-Pools nach diesem Zeitplan automatisch verwaltet:

- Alle 48 Stunden, wenn das Resultset nicht verwendet oder ungültig gemacht wurde.
- Wenn sich der Resultsetcache der maximalen Größe nähert.

Benutzer können den gesamten Resultsetcache mit einer der folgenden Optionen manuell leeren:

- Deaktivieren des Resultsetcachefeatures für die Datenbank
- Ausführen des Datenbankkonsolenbefehls (DBCC) DROPRESULTSETCACHE, während eine Verbindung zur Datenbank besteht

Durch das Anhalten einer Datenbank wird das zwischengespeicherte Resultset nicht geleert.  

## <a name="next-steps"></a>Nächste Schritte

Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht](sql-data-warehouse-overview-develop.md).
