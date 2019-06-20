---
title: Überwachen und Optimieren der Datenbankleistung in Azure SQL-Datenbank | Microsoft-Dokumentation
description: Tipps für die Leistungsoptimierung in Azure SQL-Datenbank durch Auswertung und Verbesserung.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 2fa43fcd48736a3d044deb07ed690af580c3b987
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416279"
---
# <a name="monitoring-and-performance-tuning"></a>Überwachen und Optimieren der Datenbankleistung

Mit Azure SQL-Datenbank können Sie problemlos die Nutzung überwachen, Ressourcen (CPU, Arbeitsspeicher, E/A) hinzufügen oder entfernen, potenzielle Probleme behandeln und Empfehlungen suchen, mit denen sich die Leistung Ihrer Datenbank verbessern lässt. Azure SQL-Datenbank bietet eine Vielzahl von Features, mit denen sich Probleme in Ihrer Datenbank automatisch beheben lassen, wenn Sie es der Datenbank überlassen möchten, sich an Ihre Workload anzupassen und die Leistung automatisch zu optimieren. Es gibt allerdings einige spezifische Probleme, die Sie möglicherweise beheben müssen. Dieser Artikel beschreibt einige Best Practices und Tools, die Sie zum Beheben von Leistungsproblemen verwenden können.

Es gibt zwei wichtige Aktivitäten, die Sie ausführen müssen, um sicherzustellen, dass Ihre Datenbank ohne Probleme ausgeführt wird:
- [Überwachen der Datenbankleistung](#monitoring-database-performance), um sicherzustellen, dass die Ressourcen, die Ihrer Datenbank zugewiesen wurden, die Workload verarbeiten können. Wenn Sie feststellen, dass Ihre Ressourcen sich ihren Limits nähern, müssen Sie die Abfragen identifizieren, die die meisten Ressourcen verbrauchen, und diese optimieren. Alternativ dazu können Sie auch durch ein Upgrade der Dienstebene weitere Ressourcen hinzufügen.
- [Beheben von Leistungsproblemen](#troubleshoot-performance-issues). um herauszufinden, warum ein Problem aufgetreten ist, und um die zugrunde liegende Ursache des Problems sowie die Aktion zu ermitteln, mit der sich das Problem beheben lässt.

## <a name="monitoring-database-performance"></a>Überwachen der Datenbankleistung

Die Überwachung der Leistung einer SQL-Datenbank in Azure beginnt mit der Überwachung der Ressourcennutzung relativ zur gewählten Datenbankleistung. Sie müssen die folgenden Ressourcen überwachen:
 - **CPU-Auslastung**: Sie müssen überprüfen, ob die CPU-Auslastung über einen längeren Zeitraum 100 % erreicht. Dies könnte ein Hinweis darauf sein, dass Sie ein Upgrade der Datenbank oder Instanz ausführen müssen oder dass Sie die Abfragen identifizieren und optimieren müssen, die den größten Teil der Computeleistung nutzen.
 - **Wartestatistik**: Sie müssen überprüfen, warum Abfragen auf einige Ressourcen warten. Abfragen warten möglicherweise darauf, dass Daten abgerufen oder in Datenbankdateien gespeichert werden, oder sie warten, weil ein Ressourcenlimit erreicht wurde, usw.
 - **E/A-Auslastung**: Sie müssen überprüfen, ob sich die E/A-Auslastung des zugrunde liegenden Speichers ihrem Limit nähert.
 - **Arbeitsspeicherauslastung**: Die Menge an Arbeitsspeicher, der für Ihre Datenbank oder Instanz verfügbar ist, ist proportional zur Anzahl von virtuellen Kernen, und Sie müssen überprüfen, ob der Arbeitsspeicher für Ihre Workload ausreichend ist. Die Seitenlebenserwartung ist einer der Parameter, die darauf hinweisen können, ob Ihre Seiten schnell aus dem Arbeitsspeicher entfernt werden.

Azure SQL-Datenbank **stellt Empfehlungen bereit, die Sie bei der Fehlerbehandlung und beim Beheben potenzieller Leistungsprobleme unterstützen können**. Sie können Möglichkeiten zur Verbesserung und Optimierung der Abfrageleistung identifizieren, ohne dass Sie Ressourcen anhand von [Empfehlungen zur Leistung für SQL-Datenbank](sql-database-advisor.md) ändern müssen. Fehlende Indizes und falsch optimierte Abfragen sind häufige Ursachen für eine schlechte Datenbankleistung. Sie können diese Empfehlungen zur Optimierung anwenden, um die Leistung Ihrer Workload zu verbessern. Sie können die Leistung Ihrer Abfragen auch automatisch durch Azure SQL-Datenbank optimieren lassen, indem Sie alle erkannten Empfehlungen anwenden und bestätigen, dass diese die Datenbankleistung verbessern (weitere Informationen finden Sie unter [automatically optimize performance of your queries (Automatisches Optimieren der Leistung Ihrer Abfragen)](sql-database-automatic-tuning.md)).

Es gibt folgende Optionen für die Überwachung und Problembehandlung der Datenbankleistung:

- Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **SQL-Datenbanken**, wählen Sie die Datenbank aus, und suchen Sie dann mithilfe des Diagramms „Überwachung“ nach Ressourcen, die sich ihrer maximalen Leistung annähern. „DTU-Verbrauch“ wird standardmäßig angezeigt. Klicken Sie auf **Bearbeiten** , um den Zeitraum und die angezeigten Werte zu ändern.
- Tools wie SQL Server Management Studio bieten viele nützliche Berichte, z.B. ein [Leistungsdashboard](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017), in dem Sie die Ressourcennutzung überwachen und die Abfragen mit dem größten Ressourcenverbrauch ermitteln können, oder einen [Abfragespeicher](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed), in dem Sie die Abfragen mit rückläufiger Leistung identifizieren können.
- Verwenden Sie [Query Performance Insight](sql-database-query-performance.md) im [Azure-Portal](https://portal.azure.com), um diejenigen Abfragen zu identifizieren, die die meisten Ressourcen verbrauchen. Dieses Feature ist nur in Einzeldatenbanken und Pools für elastische Datenbanken verfügbar.
- Verwenden Sie [SQL Database Advisor](sql-database-advisor-portal.md), um Empfehlungen zum Erstellen und Löschen von Indizes, zum Parametrisieren von Abfragen und zum Beheben von Schemaproblemen anzuzeigen. Dieses Feature ist nur in Einzeldatenbanken und Pools für elastische Datenbanken verfügbar.
- Mit [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) können Sie die Leistung Ihrer Datenbank automatisch überwachen. Bei Erkennung eines Leistungsproblems wird ein Diagnoseprotokoll mit Details und einer Fehlerursachenanalyse des Problems generiert. Empfehlungen zur Verbesserung der Leistung werden nach Möglichkeit bereitgestellt.
- [Aktivieren Sie die automatische Optimierung](sql-database-automatic-tuning-enable.md), und lassen Sie erkannte Leistungsprobleme automatisch durch Azure SQL-Datenbank verbessern.
- Verwenden Sie [dynamische Verwaltungsansichten (DMVs)](sql-database-monitoring-with-dmvs.md), [erweiterte Ereignisse](sql-database-xevent-db-diff-from-svr.md) und den [Abfragespeicher](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store), um detaillierte Informationen zur Behebung von Leistungsproblemen zu erhalten.

> [!TIP]
> Im [Leistungsleitfaden](sql-database-performance-guidance.md) finden Sie Methoden, mit denen Sie die Leistung von Azure SQL-Datenbank verbessern können, nachdem Sie das Leistungsproblem mithilfe einer oder mehrerer der oben genannten Methoden ermittelt haben.

## <a name="troubleshoot-performance-issues"></a>Behandeln von Leistungsproblemen

Beim Diagnostizieren und Beheben von Leistungsproblemen besteht der erste Schritt darin, den Status jeder aktiven Abfrage und die Bedingungen zu ermitteln, die zu Leistungsproblemen in Bezug auf den jeweiligen Workloadstatus führen. Um die Leistung der Azure SQL-Datenbank zu verbessern, sollten Sie sich darüber im Klaren sein, dass sich jede aktive Abfrageanforderung Ihrer Anwendung entweder in einem laufenden oder einem wartenden Zustand befindet. Wenn Sie die Informationen in diesem Artikel verwenden, um Leistungsprobleme in Azure SQL-Datenbank zu diagnostizieren und zu beheben, behalten Sie das folgende Diagramm im Kopf.

![Workload-Status](./media/sql-database-monitor-tune-overview/workload-states.png)

Bei einem Workload mit Leistungsproblemen kann das Leistungsproblem auf CPU-Konflikte zurückzuführen sein (eine **running-related**-Bedingung) oder einzelne Abfragen warten auf etwas (eine **waitingrelated**-Bedingung).

Für Probleme **in Zusammenhang mit der Ausführung** können folgende Ursachen vorliegen:
- **Kompilierungsprobleme**: Der SQL-Abfrageoptimierer generiert möglicherweise aufgrund veralteter Statistiken, einer falschen Schätzung der zu verarbeitenden Zeilenanzahl oder einer falschen Schätzung des erforderlichen Arbeitsspeichers einen Plan, der nicht optimal ist. Wenn Sie wissen, dass die Abfrage in der Vergangenheit oder auf einer anderen Instanz (einer verwalteten Instanz oder einer SQL Server-Instanz) schneller ausgeführt wurde, vergleichen Sie die tatsächlichen Ausführungspläne, um mögliche Unterschiede festzustellen. Versuchen Sie, Abfragehinweise einzufügen oder Statistiken oder Indizes neu zu erstellen, um einen besseren Plan zu erzielen. Aktivieren Sie die automatische Plankorrektur in Azure SQL-Datenbank, um solche Probleme automatisch minimieren zu lassen.
- **Ausführungsprobleme**: Wenn der Abfrageplan optimal ist, sind möglicherweise Ressourcenlimits in der Datenbank erreicht, beispielsweise beim Schreibdurchsatz von Protokollen. Möglicherweise werden auch defragmentierte Indizes verwendet, die neu erstellt werden sollten. Eine große Anzahl von gleichzeitigen Abfragen, die viele Ressourcen verbrauchen, kann ebenfalls Probleme mit der Ausführung verursachen. Probleme aufgrund von **Wartevorgängen** stehen in den meisten Fällen in Zusammenhang mit Ausführungsproblemen, weil Abfragen, die nicht effizient ausgeführt werden, möglicherweise auf Ressourcen warten.

Für Probleme **in Zusammenhang mit Wartevorgängen** können folgende Ursachen vorliegen:
- **Blockierung**: Möglicherweise hält eine Abfrage eine Sperre für einige Objekte in der Datenbank, während andere Abfragen versuchen, auf diese Objekte zuzugreifen. Mithilfe von DMVs oder Überwachungstools können Sie blockierende Abfragen einfach ermitteln.
- **E/A-Probleme**: Möglicherweise warten Abfragen darauf, dass Seiten in die Daten- oder Protokolldateien geschrieben werden. In diesem Fall sehen Sie in der DMV die Wartestatistiken `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG` oder `PAGEIOLATCH_*`.
- **TempDB-Probleme**: Wenn Sie viele temporäre Tabellen verwenden oder viele TempDB-Überläufe in Ihren Plänen feststellen, liegt möglicherweise ein Problem mit dem TempDB-Durchsatz vor. 
- **Arbeitsspeicherbezogene Probleme**: Möglicherweise steht für Ihre Workload nicht genügend Arbeitsspeicher zur Verfügung, sodass die Seitenlebenserwartung sinkt oder Ihre Abfragen eine geringere Speicherzuweisung erhalten als erforderlich. In einigen Fällen lassen sich diese Probleme mit den integrierten Intelligencefunktionen des Abfrageoptimierers beheben.
 
In den folgenden Abschnitten wird erläutert, wie sich einige dieser Probleme identifizieren und beheben lassen.

## <a name="running-related-performance-issues"></a>Leistungsprobleme in Zusammenhang mit der Ausführung

Als allgemeine Richtlinie gilt: Wenn Ihre CPU-Auslastung konstant bei oder über 80 % liegt, haben Sie ein ausführungsbedingtes Leistungsproblem. Wenn ein Problem in Zusammenhang mit der Ausführung vorliegt, kann dies durch unzureichende CPU-Ressourcen oder durch eine der folgenden Bedingungen verursacht werden:

- Zu viele ausgeführte Abfragen
- Zu viele kompilierte Abfragen
- Eine oder mehrere ausgeführte Abfragen verwenden einen suboptimalen Abfrageplan.

Wenn Sie feststellen, dass ein Problem in Zusammenhang mit der Ausführung vorliegt, besteht Ihr Ziel darin, mithilfe einer oder mehrerer Methoden die genaue Ursache herauszufinden. Die gängigsten Methoden zum Ermitteln von Probleme in Zusammenhang mit der Ausführung sind die folgenden:

- Verwenden Sie das [Azure-Portal](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal), um die prozentuale CPU-Auslastung zu überwachen.
- Verwenden Sie die folgenden [dynamische Verwaltungssichten](sql-database-monitoring-with-dmvs.md):

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) gibt die CPU-, E/A- und Arbeitsspeichernutzung für eine Azure SQL-Datenbank zurück. Für alle 15 Sekunden ist eine Zeile vorhanden, selbst wenn keine Aktivität in der Datenbank erfolgt ist. Historische Daten werden eine Stunde lang aufbewahrt.
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) gibt CPU-Nutzungs- und Speicherdaten für eine Azure SQL-Datenbank zurück. Die Daten werden in Intervallen von fünf Minuten gesammelt und aggregiert.

> [!IMPORTANT]
> Unter [Ermitteln von Problemen mit der CPU-Auslastung](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues) finden Sie eine Reihe von T-SQL-Abfragen, bei denen diese DMVs zum Beheben von Problemen mit der CPU-Auslastung verwendet werden.

### <a name="ParamSniffing"></a> Behandeln von Problemen bei Abfragen mit parameterempfindlichem Ausführungsplan

Das Problem eines parameterempfindlichen Plans bezieht sich auf ein Szenario, in dem der Abfrageoptimierer einen Abfrageausführungsplan generiert, der nur für einen bestimmten Parameterwert (oder eine Gruppe von Werten) optimal ist, und der zwischengespeicherte Plan dann nicht optimal für die in aufeinanderfolgenden Ausführungen verwendeten Parameterwerte ist. Nicht optimale Pläne können dann zu Problemen der Abfrageleistung und einer allgemeinen Verschlechterung des Workloaddurchsatzes führen. Weitere Informationen zur Parameterermittlung und Abfrageverarbeitung finden Sie im [Handbuch zur Architektur der Abfrageverarbeitung](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Es gibt mehrere Lösungen zur Umgehung von Problemen, die jeweils ihre Schwächen und Nachteile haben:

- Verwenden des Abfragehinweises [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) bei jeder Abfrageausführung. Bei dieser Problemumgehung werden Kompilierungszeit und erhöhte CPU-Leistung für eine bessere Qualität des Abfrageplans eingebüßt. Die Verwendung der Option `RECOMPILE` ist häufig bei Workloads, die einen hohen Durchsatz erfordern, nicht möglich.
- Verwenden des Abfragehinweises [OPTION (OPTIMIZE FOR...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query), um den tatsächlichen Parameterwert mit einem typischen Parameterwert zu überschreiben, mit dem ein ausreichender Plan für die meisten möglichen Parameterwerte erzeugt wird.   Diese Option erfordert ein gutes Verständnis der optimalen Parameterwerte und zugehörigen Planmerkmale.
- Verwenden des Abfragehinweises [OPTION (OPTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query), um den tatsächlichen Parameterwert zu überschreiben und im Austausch den Durchschnittswert des Dichtevektors zu verwenden. Dies ist auch möglich, indem die eingehenden Parameterwerte in lokalen Variablen erfasst werden und dann die lokalen Variablen in den Prädikaten anstelle der Parameter selbst verwendet werden. Für diese spezielle Fehlerbehebung muss eine *ausreichend gute* durchschnittliche Dichte vorhanden sein.
- Vollständiges Deaktivieren der Parameterermittlung mit dem Abfragehinweis [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query).
- Verwenden des Abfragehinweises [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query), um erneute Kompilierungen im Cache zu verhindern. Bei dieser Lösung wird davon ausgegangen, dass der *ausreichend gute* allgemeine Plan derjenige ist, der sich bereits im Cache befindet. Sie können auch automatische Updates für Statistiken deaktivieren, um die Wahrscheinlichkeit zu verringern, dass der gute Plan entfernt und ein neuer schlechter Plan kompiliert wird.
- Erzwingen des Plans durch explizites Verwenden des Abfragehinweises [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) (explizites Angeben, Festlegen eines bestimmten Plans mit dem Abfragespeicher oder Aktivieren von [Automatische Optimierung](sql-database-automatic-tuning.md)).
- Ersetzen der Einzelprozedur durch eine geschachtelte Gruppe von Prozeduren, die jeweils basierend auf bedingter Logik und zugehörigen Parameterwerten verwendet werden können.
- Erstellen von Alternativen für die dynamische Zeichenfolgenausführung zur Definition einer statischen Prozedur.

Weitere Informationen zur Behebung dieser Arten von Problemen finden in folgenden Blogbeiträgen:

- Blogbeitrag zum [Ermitteln eines Parameters](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
- Blogbeitrag zu [dynamischem SQL im Vergleich zur Qualität des Abfrageplans für parametrisierte Abfragen](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- Blogbeitrag zu [Techniken zur SQL-Abfrageoptimierung in SQL Server: Parameterermittlung](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Behandeln von Problemen der Kompilierungsaktivität aufgrund falscher Parametrisierung

Wenn eine Abfrage Literale enthält, wählt entweder die Datenbank-Engine eine automatische Parametrisierung der Anweisung aus, oder ein Benutzer kann die Anweisung explizit parametrisieren, um die Anzahl von Kompilierungen zu verringern. Eine große Anzahl von Kompilierungen einer Abfrage anhand des gleichen Musters, jedoch mit unterschiedlichen Literalwerten, kann zu einer hohen CPU-Auslastung führen. Auch wenn Sie eine Abfrage nur teilweise parametrisieren und diese dann weiterhin Literale enthält, wird sie von der Datenbank-Engine nicht weiter parametrisiert.  Nachfolgend sehen Sie ein Beispiel einer teilweise parametrisierten Abfrage:

```sql
SELECT * FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

Im vorherigen Beispiel nimmt `t1.c1` die Form `@p1` an, aber `t2.c2` verwendet weiterhin die GUID als Literal. Wenn Sie in diesem Fall den Wert für `c2` ändern, wird die Abfrage als eine andere Abfrage behandelt, und eine neue Kompilierung findet statt. Damit im vorherigen Beispiel die Anzahl von Kompilierungen reduziert wird, muss auch die GUID parametrisiert werden.

Die folgende Abfrage zeigt die Anzahl von Abfragen nach Abfragehash, um festzustellen, ob eine Abfrage richtig parametrisiert ist:

```sql
SELECT  TOP 10  
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p 
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs 
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC
```
### <a name="factors-influencing-query-plan-changes"></a>Faktoren mit Einfluss auf Abfrageplanänderungen

Die Neukompilierung eines Abfrageausführungsplans führt möglicherweise zu einem generierten Abfrageplan, der sich von dem ursprünglich zwischengespeicherten Plan unterscheidet. Es gibt verschiedene Gründe dafür, warum ein vorhandener ursprünglicher Plan automatisch neu kompiliert wird:
- Änderungen an dem Schema, auf das von der Abfrage verwiesen wird
- Datenänderungen an den Tabellen, auf die von der Abfrage verwiesen wird 
- Änderungen an Abfragekontextoptionen 

Ein kompilierter Plan kann aus zahlreichen Gründen aus dem Cache geworfen werden, darunter Neustarts von Instanzen, Änderungen an der Konfiguration im Datenbankbereich, Speicherauslastung und explizite Anforderungen zum Löschen des Caches. Darüber hinaus führt ein RECOMPILE-Hinweis dazu, dass ein Plan nicht zwischengespeichert wird.

Eine Neukompilierung (oder eine erneute Kompilierung nach dem Entfernen aus dem Cache) kann dennoch zur Generierung eines Abfrageausführungsplans führen, der mit dem ursprünglichen Plan identisch ist.  Wenn jedoch im Vergleich zum vorherigen oder ursprünglichen Plan Änderungen vorliegen, finden Sie nachfolgend die am häufigsten zutreffenden Erklärungen für die Änderung eines Abfrageausführungsplans:

- **Geändertes physisches Design**. Beispielsweise können bei einer neuen Kompilierung neu erstellte Indizes verwendet werden, die die Anforderungen einer Abfrage effektiver abdecken, falls der Abfrageoptimierer entscheidet, dass dieser neue Index besser geeignet ist als die ursprünglich für die erste Version der Abfrageausführung ausgewählte Datenstruktur.  Alle physischen Änderungen an den referenzierten Objekten führen zum Zeitpunkt der Kompilierung möglicherweise zu einer neuen Planauswahl.

- **Unterschiede zwischen Serverressourcen**. In einem Szenario, in dem sich ein Plan auf „System A“ von dem auf „System B“ unterscheidet, kann die Verfügbarkeit der Ressourcen (z.B. die Anzahl verfügbarer Prozessoren) beeinflussen, welcher Plan generiert wird.  Das heißt, wenn ein System eine höhere Anzahl von Prozessoren aufweist, wird möglicherweise ein paralleler Plan ausgewählt. 

- **Andere Statistiken**. Die den referenzierten Objekten zugeordneten statistischen Informationen haben sich geändert oder unterscheiden sich wesentlich von den Statistiken des ursprünglichen Systems.  Wenn sich die Statistiken ändern und eine Neukompilierung durchgeführt wird, verwendet der Abfrageoptimierer Statistiken ab dem jeweiligen Zeitpunkt. Die überarbeiteten Statistiken enthalten möglicherweise erheblich abweichende Datenverteilungen und Häufigkeiten, die in der ursprünglichen Kompilierung nicht vorlagen.  Diese Änderungen werden zur Erstellung von Kardinalitätsschätzungen verwendet (Anzahl der Zeilen, die voraussichtlich die logische Abfragestruktur durchlaufen).  Änderungen an Kardinalitätsschätzungen können dazu führen, dass andere physische Operatoren ausgewählt und Vorgänge in anderen Reihenfolgen durchgeführt werden.  Selbst geringfügige Änderungen an Statistiken können zu einem anderen Abfrageausführungsplan führen.

- **Geänderter Datenbank-Kompatibilitätsgrad oder geänderte Version der Kardinalitätsschätzung**.  Bei Änderungen am Datenbank-Kompatibilitätsgrad können neue Strategien und Funktionen aktiviert werden, die möglicherweise zu einem anderen Abfrageausführungsplan führen.  Neben dem Datenbank-Kompatibilitätsgrad kann die Auswahl des Abfrageausführungsplans zur Kompilierzeit auch durch das Deaktivieren oder Aktivieren des Ablaufverfolgungsflags 4199 oder das Ändern des Status der datenbankbezogenen Konfiguration QUERY_OPTIMIZER_HOTFIXES beeinflusst werden.  Auch die Ablaufverfolgungsflags 9481 (Legacy-CE erzwingen) und 2312 (Standard-CE erzwingen) besitzen Auswirkungen auf die Planauswahl. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Lösen von Abfrageproblemen oder Bereitstellen weiterer Ressourcen

Sobald Sie das Problem identifiziert haben, können Sie die problematischen Abfragen optimieren oder die Computegröße oder Dienstebene erhöhen, um die Kapazität Ihrer Azure SQL-Datenbank zu steigern, damit die CPU-Anforderungen erfüllt werden können. Informationen zur Skalierung der Ressourcen für einzelne Datenbanken finden Sie unter [Skalieren einzelner Datenbankressourcen in Azure SQL-Datenbank](sql-database-single-database-scale.md). Um die Ressourcen für Pools für elastische Datenbanken zu skalieren, lesen Sie [Skalieren von Ressourcen für Pools für elastische Datenbanken in Azure SQL-Datenbank](sql-database-elastic-pool-scale.md). Informationen zum Skalieren einer verwalteten Instanz finden Sie unter [Ressourcenlimits auf Instanzebene](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>Feststellen, ob Ausführungsprobleme aufgrund eines höheren Workloadvolumens auftreten

Durch eine Zunahme des Anwendungsdatenverkehrs und Workloadvolumens kann sich eine höhere CPU-Auslastung ergeben, doch muss dieses Problem sorgfältig diagnostiziert werden. Beantworten Sie in einem Szenario mit hoher CPU-Auslastung die folgenden Fragen, um zu bestimmen, ob tatsächlich eine höhere CPU-Auslastung aufgrund von Änderungen des Workloadvolumens vorliegt:

1. Sind die Abfragen von der Anwendung die Ursache für das Problem einer hohen CPU-Auslastung?
2. Für Abfragen mit der höchsten CPU-Auslastung (die identifiziert werden können):

   - Stellen Sie fest, ob der gleichen Abfrage mehrere Ausführungspläne zugeordnet waren. Wenn das der Fall ist, ermitteln Sie den Grund dafür.
   - Stellen Sie bei Abfragen mit demselben Ausführungsplan fest, ob die Ausführungszeiten konsistent waren und ob die Anzahl von Ausführungen zunahm. Wenn das der Fall ist, liegen wahrscheinlich Leistungsprobleme aufgrund einer gestiegenen Workload vor.

Zusammenfassend lässt sich sagen: Wenn der Abfrageausführungsplan nicht anders ausgeführt wurde, aber die CPU-Auslastung gemeinsam mit der Anzahl von Ausführungen zugenommen hat, liegt wahrscheinlich ein Leistungsproblem im Zusammenhang mit einer gestiegenen Workload vor.

Es ist nicht immer leicht festzustellen, ob eine Änderung des Workloadvolumens vorliegt, die ein CPU-Problem bewirkt.   Zu beachtende Faktoren: 

- **Geänderte Ressourcennutzung**

  Beispielsweise bedeutet in einem Szenario, in dem die CPU-Auslastung über einen längeren Zeitraum auf 80 % gestiegen ist,  diese Auslastung alleine nicht, dass sich das Workloadvolumen geändert hat.  Auch Regressionen des Abfrageausführungsplans und eine geänderte Datenverteilung können zu einer höheren Ressourcenauslastung beitragen, selbst wenn die Anwendung exakt dieselbe Workload ausführt.

- **Neue Abfrage**

   Eine Anwendung kann zu unterschiedlichen Zeiten eine neue Gruppe von Abfragen bewirken.

- **Anzahl der Anforderungen hat sich erhöht oder verringert**

   Dieses Szenario ist der offensichtlichste Maßstab für die Workload. Die Anzahl der Abfragen entspricht nicht immer einer gesteigerten Ressourcennutzung. Diese Metrik ist dennoch ein wesentliches Anzeichen, falls andere Faktoren keine Änderungen aufweisen.

## <a name="waiting-related-performance-issues"></a>Leistungsprobleme in Zusammenhang mit Wartevorgängen

Wenn Sie sicher sind, dass kein Problem in Zusammenhang mit der Ausführung und CPU-Auslastung vorliegt, wird das Problem durch Wartevorgänge verursacht. Kurz gesagt: Ihre CPU-Ressourcen werden nicht effizient genutzt, weil die CPU auf eine andere Ressource wartet. In diesem Fall müssen Sie herausfinden, worauf Ihre CPU-Ressourcen warten. Hier finden Sie die gängigsten Methoden zur Anzeige der wichtigsten Wartetypkategorien:

- Der [Abfragedatenspeicher](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) bietet eine Wartestatistik pro Abfrage im Zeitverlauf. Im Abfragedatenspeicher sind Wartetypen mit Wartekategorien kombiniert. Die Zuordnung von Wartekategorien zu Wartetypen finden Sie in [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) gibt Informationen über alle Wartezeiten zurück, die von Threads auftreten, die während des Betriebs ausgeführt werden. Sie können diese aggregierte Ansicht verwenden, um Leistungsprobleme mit der Azure SQL-Datenbank und auch mit bestimmten Abfragen und Batches zu diagnostizieren.
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) gibt Informationen zur Warteschlange von Aufgaben, die bei einigen Ressource warten.

In Szenarien mit hoher CPU-Auslastung spiegeln Abfragespeicher und Wartestatistik nicht immer die CPU-Auslastung wieder. Dafür gibt es zwei Gründe:

- Abfragen mit hoher CPU-Auslastung werden möglicherweise immer noch ausgeführt und sind nicht beendet.
- Die Abfragen mit hoher CPU-Auslastung wurden ausgeführt, als ein Failover auftrat.

Dynamische Verwaltungssichten, in denen Abfragespeicher und Wartestatistik nachgeführt werden, zeigen nur Ergebnisse für erfolgreich abgeschlossene Abfragen und Abfragen mit Zeitüberschreitung, aber keine Daten für derzeit ausgeführte Anweisungen (bis zu deren Abschluss). Die dynamische Verwaltungssicht [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) ermöglicht Ihnen das Nachverfolgen derzeit ausgeführter Abfragen und der Zeit des zugeordneten Workers.

Wie im vorherigen Diagramm dargestellt, sind die am häufigsten Wartetypen:

- Sperren (Blockieren)
- E/A
- Konflikt in Zusammenhang mit `tempdb`
- Wartetyp „Speicherzuweisung“

> [!IMPORTANT]
> In den folgenden Artikeln finden Sie eine Reihe von T-SQL-Abfragen, bei denen diese DMVs verwendet werden, um Probleme aufgrund von Wartevorgängen zu beheben:
>
> - [Identifizieren von Problemen mit der E/A-Leistung](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identifizieren von Leistungsproblemen mit ](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)`tempdb`
> - [Identifizieren von Problemen mit Wartevorgängen aufgrund von Speicherzuweisungen](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox - Waits and Latches](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches) (TigerToolbox: Wartevorgänge und Latches)
> - [TigerToolbox - usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improving-database-performance-with-more-resources"></a>Verbesserung der Datenbankleistung mit mehr Ressourcen

Schließlich können Sie noch die Anzahl der in Azure SQL-Datenbank verfügbaren Ressourcen ändern, wenn es keine umsetzbaren Elemente gibt, die die Leistung Ihrer Datenbank verbessern können. Sie können mehr Ressourcen zuweisen, indem Sie die [DTU-Dienstebene](sql-database-service-tiers-dtu.md) einer Einzeldatenbank verändern oder die eDTUs eines Pools für elastische Datenbanken zu einem beliebigen Zeitpunkt erhöhen. Alternativ können Sie bei Verwendung des [vCore-basierten Kaufmodells](sql-database-service-tiers-vcore.md) entweder die Dienstebene ändern oder die Ressourcen heraufsetzen, die der Datenbank zugeordnet werden.

1. Bei Einzeldatenbanken können Sie bedarfsgesteuert die [Dienstebenen](sql-database-single-database-scale.md) oder [Computeressourcen](sql-database-single-database-scale.md) ändern, um die Datenbankleistung zu steigern.
2. Ziehen Sie bei mehreren Datenbanken [Pools für elastische Datenbanken](sql-database-elastic-pool-guidance.md) in Betracht, um Ressourcen automatisch zu skalieren.

## <a name="tune-and-refactor-application-or-database-code"></a>Optimieren und Ändern von Anwendungs- oder Datenbankcode

Sie können Anwendungscode ändern, um effektiver die Datenbank zu nutzen, Indizes zu verändern, Pläne zu erzwingen oder Hinweise zu nutzen, um die Datenbank manuell an Ihre Workload anzupassen. Im [Leistungsleitfaden](sql-database-performance-guidance.md) finden Sie hilfreiche Informationen und Tipps zur Überwachung und Optimierung.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Aktivierung der automatischen Optimierung in Azure SQL-Datenbank und zur Verwendung für die vollständige Verwaltung Ihrer Workload finden Sie unter [Aktivieren der automatischen Optimierung](sql-database-automatic-tuning-enable.md).
- Bei der Verwendung der manuellen Optimierung helfen Ihnen die Informationen unter [Azure SQL Database Advisor im Azure-Portal](sql-database-advisor-portal.md) weiter. Außerdem können Sie manuell die Empfehlungen anwenden, die zu einer Verbesserung der Leistung Ihrer Abfragen führen.
- Ändern Sie in der Datenbank verfügbare Ressourcen, indem Sie [Dienst- und Leistungsebenen für Azure SQL-Datenbanken](sql-database-performance-guidance.md) ändern.
