---
title: Cheat Sheet für den Vergleich zwischen SQL und Azure Monitor-Protokollabfragen | Microsoft-Dokumentation
description: Unterstützung für Benutzer, die mit SQL vertraut sind, beim Schreiben von Protokollabfragen in Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: b756b9484273c098dbeb6685430f70626b3af787
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65789229"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>Cheat Sheet für den Vergleich zwischen SQL und Azure Monitor-Protokollabfragen 

Die folgende Tabelle dient als Unterstützung für Benutzer, die mit SQL vertraut sind, beim Erlernen der Abfragesprache Kusto zum Schreiben von Protokollabfragen in Azure Monitor. Sehen Sie sich den T-SQL-Befehl für das Lösen von gängigen Szenarien und die Entsprechung in einer Azure Monitor-Protokollabfrage an.

## <a name="sql-to-azure-monitor"></a>Vergleich zwischen SQL und Azure Monitor

BESCHREIBUNG                             |SQL-Abfrage                                                                                          |Azure Monitor-Protokollabfrage
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Alle Daten in einer Tabelle auswählen            |`SELECT * FROM dependencies`                                                                       |<code>dependencies</code>
Bestimmte Spalten in einer Tabelle auswählen    |`SELECT name, resultCode FROM dependencies`                                                        |<code>dependencies <br>&#124; project name, resultCode</code>
100 Datensätze in einer Tabelle auswählen         |`SELECT TOP 100 * FROM dependencies`                                                               |<code>dependencies <br>&#124; take 100</code>
Null-Auswertung                         |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL`                                          |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
Zeichenfolgenvergleich: Gleichheit             |`SELECT * FROM dependencies WHERE name = "abcde"`                                                  |<code>dependencies <br>&#124; where name == "abcde"</code>
Zeichenfolgenvergleich: Untergeordnete Zeichenfolge            |`SELECT * FROM dependencies WHERE name like "%bcd%"`                                                   |<code>dependencies <br>&#124; where name contains "bcd"</code>
Zeichenfolgenvergleich: Platzhalter             |`SELECT * FROM dependencies WHERE name like "abc%"`                                                |<code>dependencies <br>&#124; where name startswith "abc"</code>
Datumsvergleich: Letzter Tag             |`SELECT * FROM dependencies WHERE timestamp > getdate()-1`                                         |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Datumsvergleich: Datumsbereich             |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'`                 |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Boolescher Vergleich                      |`SELECT * FROM dependencies WHERE !(success)`                                                      |<code>dependencies <br>&#124; where success == "False"</code>
Sortieren                                    |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc`                                  |<code>dependencies <br>&#124; order by timestamp asc</code>
Unterscheiden                                |`SELECT DISTINCT name, type  FROM dependencies`                                                    |<code>dependencies <br>&#124; summarize by name, type</code>
Gruppierung, Aggregation                   |`SELECT name, AVG(duration) FROM dependencies GROUP BY name`                                       |<code>dependencies <br>&#124; summarize avg(duration) by name</code>
Spaltenaliase, Erweitern                  |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name`             |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Top n-Datensätze nach Kennzahl                |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc`        |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
Union                                   |`SELECT * FROM dependencies UNION SELECT * FROM exceptions`                                        |<code>union dependencies, exceptions</code>
Union: mit Bedingungen                  |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions WHERE value < 5`                |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Join                                    |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Nächste Schritte

- Arbeiten Sie die Lektionen zum [Schreiben von Protokollabfragen in Azure Monitor](get-started-queries.md) durch.
