---
title: Leistungsdiagnose in Hyperscale
description: In diesem Artikel wird beschrieben, wie Hyperscale-Leistungsprobleme in Azure SQL-Datenbank behandelt werden.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019 sqldbrb=1
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: ed31ff5d77b258d141a77fc174c2d5452adf7d01
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791714"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>Diagnose zur Problembehandlung bei SQL-Hyperscale
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Zur Behandlung von Leistungsproblemen in einer Hyperscale-Datenbank sind [Methodiken der allgemeinen Leistungsoptimierung](monitor-tune-overview.md) auf dem Azure SQL-Datenbank-Computeknoten der Ausgangspunkt für eine Leistungsuntersuchung. Allerdings wurden angesichts der [verteilten Architektur](service-tier-hyperscale.md#distributed-functions-architecture) von Hyperscale zusätzliche Diagnosen zur Unterstützung hinzugefügt. In diesem Artikel werden Hyperscale-spezifische Diagnosedaten beschrieben.

## <a name="log-rate-throttling-waits"></a>Wartezeiten für Protokollratendrosselung

Bei jeder Azure SQL-Datenbank-Dienstebene gibt es Limits für die Protokollgenerierungsrate, die über die [Governance für die Protokollrate](resource-limits-logical-server.md#transaction-log-rate-governance) erzwungen werden. In Hyperscale ist das Limit für Protokollgenerierung – unabhängig von der Dienstebene – derzeit auf 100 MB/s festgelegt. Es gibt jedoch Zeiten, in denen die Protokollgenerierungsrate für das primäre Computereplikat gedrosselt werden muss, um die SLAs (Vereinbarungen zum Servicelevel) für Wiederherstellbarkeit einzuhalten. Diese Drosselung erfolgt, wenn ein [Seitenserver oder ein anderes Computereplikat](service-tier-hyperscale.md#distributed-functions-architecture) mit der Anwendung neuer Protokolldatensätze vom Protokolldienst erheblich im Rückstand ist.

Die folgenden Wartetypen (in [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)) beschreiben die Gründe, aus denen die Protokollrate beim primären Computereplikat nicht gedrosselt werden kann:

|Wartetyp    |BESCHREIBUNG                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Tritt auf, wenn eine Protokollgenerierungsrate für den primären Hyperscale-Datenbank-Computeknoten infolge einer verzögerten Protokollnutzung auf dem/den Seitenserver(n) gedrosselt wird.         |
|RBIO_RG_DESTAGE        | Tritt auf, wenn eine Protokollgenerierungsrate für einen Hyperscale-Datenbank-Computeknoten infolge einer verzögerten Protokollnutzung durch den langfristigen Protokollspeicher gedrosselt wird.         |
|RBIO_RG_REPLICA        | Tritt auf, wenn eine Protokollgenerierungsrate für einen Hyperscale-Datenbank-Serverknoten infolge einer verzögerten Protokollnutzung durch lesbare sekundären Replikate gedrosselt wird.         |
|RBIO_RG_LOCALDESTAGE   | Tritt auf, wenn eine Protokollgenerierungsrate für einen Hyperscale-Datenbank-Computeknoten infolge einer verzögerten Protokollnutzung durch den Protokolldienst gedrosselt wird.         |

## <a name="page-server-reads"></a>Seitenserver-Lesevorgänge

Die Computereplikate können eine vollständige Kopie der Datenbank nicht lokal zwischenspeichern. Die lokalen Daten für das Computereplikat werden im Pufferpool (im Arbeitsspeicher) und im lokalen RBPEX-Cache (Resilient Buffer Pool Extension, resiliente Pufferpoolerweiterung) gespeichert, bei dem es sich um einen partiellen (nicht abdeckenden) Cache von Datenseiten handelt. Dieser lokale RBPEX-Cache wird proportional zur Computegröße dimensioniert und ist drei Mal so groß wie der Arbeitsspeicher des Computetarifs. Der RBPEX-Cache ähnelt dem Pufferpool insofern, als er die Daten enthält, auf die am häufigsten zugegriffen wird. Andererseits verfügt jeder Seitenserver über einen abdeckenden RBPEX-Cache für den von ihm verwalteten Teil der Datenbank.

Wenn bei einem Lesevorgang in einem Computereplikat die Daten nicht im Pufferpool oder im lokalen RBPEX-Cache vorhanden sind, wird die Funktion getPage(pageId, LSN) aufgerufen und die Seite vom entsprechenden Seitenserver abgerufen. Lesevorgänge von Seitenservern aus sind Remotelesevorgänge und folglich langsamer als Lesevorgänge aus dem lokalen RBPEX-Cache. Bei der Behandlung von E/A-bezogenen Leistungsproblemen müssen wir ermitteln können, wie viele E/A-Vorgänge über relativ langsamere Remoteseitenserver-Lesevorgänge erfolgten.

Einige dynamische Verwaltungssichten (DMVs) und erweiterte Ereignisse enthalten Spalten und Felder, die die Anzahl von Remotelesevorgängen von einem Seitenserver angeben, die mit der Gesamtanzahl von Lesevorgängen verglichen werden kann. Der Abfragespeicher erfasst auch Remotelesevorgänge als Teil der Abfragelaufzeitstatistiken.

- Spalten zum Berichten über Seitenserver-Lesevorgänge stehen in Ausführungs-DMVs und Katalogsichten zur Verfügung, z. B.:

  - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
  - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
  - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
  - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
  - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- Seitenserver-Lesevorgänge werden den folgenden erweiterten Ereignissen hinzugefügt:
  - sql_statement_completed
  - sp_statement_completed
  - sql_batch_completed
  - rpc_completed
  - scan_stopped
  - query_store_begin_persist_runtime_stat
  - query-store_execution_runtime_info
- „ActualPageServerReads/ActualPageServerReadAheads“ werden dem Abfrageplan-XML-Code für tatsächliche Pläne hinzugefügt. Beispiel:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Wenn Sie diese Attribute im Fenster mit den Abfrageplaneigenschaften anzeigen möchten, ist SSMS 18.3 oder höher erforderlich.

## <a name="virtual-file-stats-and-io-accounting"></a>Virtuelle Dateistatistiken und E/A-Kontoführung

In Azure SQL-Datenbank ist die dynamische Verwaltungsfunktion [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) die primäre Methode zum Überwachen der E/A-Vorgänge von SQL-Datenbank. Die E/A-Merkmale in Hyperscale unterscheiden sich aufgrund der [verteilten Architektur](service-tier-hyperscale.md#distributed-functions-architecture). In diesem Abschnitt konzentrieren wir uns bei E/A-Vorgängen (Lese- und Schreibvorgänge) auf Datendateien, die in dieser DMF angezeigt werden. In Hyperscale entspricht jede in dieser DMF sichtbare Datendatei einem Remoteseitenserver. Der hier erwähnte RBPEX-Cache ist ein lokaler SSD-basierter Cache, bei dem es sich um einen nicht abdeckenden Cache auf dem Computereplikat handelt.

### <a name="local-rbpex-cache-usage"></a>Lokale RBPEX-Cachenutzung

Der lokale RBPEX-Cache befindet sich auf dem Computereplikat im lokalen SSD-Speicher. Folglich ist die E/A für diesen Cache schneller als die E/A auf Remoteseitenservern. Derzeit enthält [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) in einer Hyperscale-Datenbank eine spezielle Zeile mit einem Bericht über den E/A-Vorgang, der im lokalen RBEX-Cache für das Computereplikat erfolgt ist. Bei dieser Zeile lautet der Wert für die Spalten `database_id` und `file_id` gleich „0“. In der Abfrage unten beispielsweise wird die RBPEX-Nutzungsstatistik seit dem Datenbankstart zurückgegeben.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Das Verhältnis von Lesevorgängen im RBPEX-Cache zu aggregierten Lesevorgängen in allen anderen Datendateien liefert die RBPEX-Cachetrefferquote.

### <a name="data-reads"></a>Datenlesevorgänge

- Lesevorgänge von der SQL Server-Datenbank-Engine für ein Computereplikat werden möglicherweise entweder durch den lokalen RBPEX-Cache, durch Remoteseitenserver oder eine Kombination aus beidem verarbeitet, wenn mehrere Seiten gelesen werden.
- Wenn das Computereplikat einige Seiten aus einer bestimmten Datei liest, z.B. „file_id 1“, (falls sich diese Daten ausschließlich im lokalen RBPEX-Cache befinden) werden alle E/A-Vorgänge für diesen Lesevorgang auf „file_id 0 (RBPEX)“ angerechnet. Wenn sich ein Teil dieser Daten im lokalen RBPEX-Cache befindet und ein Teil auf einem Remoteseitenserver, wird der E/A-Vorgang für den vom RBPEX-Cache verarbeiteten Teil in „file_id 0“ berücksichtigt und der vom Remoteseitenserver verarbeitete Teil in „file_id 1“.
- Wenn ein Computereplikat eine Seite mit einer bestimmten [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) (Log Sequence Number, Protokollfolgenummer) von einem Seitenserver anfordert und der Seitenserver nicht auf die angeforderte LSN aktualisiert hat, wartet der Lesevorgang im Computereplikat, bis der Seitenserver aktualisiert hat. Erst dann wird die Seite an das Computereplikat zurückgegeben. Bei jedem Lesevorgang von einem Seitenserver in das Computereplikat wird der Wartetyp „PAGEIOLATCH_*“ angezeigt, wenn es auf diesen E/A-Vorgang wartet. Diese Wartezeit umfasst in Hyperscale sowohl die Zeit zum Aktualisieren der angeforderten Seite auf dem Seitenserver auf die erforderliche LSN als auch die Zeit, die zum Übertragen der Seite vom Seitenserver in das Computereplikat benötigt wird.
- Umfangreiche Lesevorgänge wie „Read-Ahead“ erfolgen oft mithilfe von [„Scatter-Gather“-Lesevorgängen](/sql/relational-databases/reading-pages/). Dies ermöglicht Lesevorgänge für Seiten mit einer Gesamtgröße von bis zu 4 MB gleichzeitig, was in der SQL Server-Datenbank-Engine als ein einzelner Lesevorgang gilt. Wenn sich die gelesenen Daten jedoch im RBPEX -Cache befinden, werden diese Lesevorgänge als mehrere einzelne 8-KB-Lesevorgänge angesehen, da der Pufferpool und der RBPEX-Cache immer 8-KB-Seiten verwenden. Infolgedessen ist die Anzahl der für RBPEX angezeigten E/A-Lesevorgänge möglicherweise größer als die tatsächliche Anzahl der von der Engine ausgeführten E/A-Vorgänge.

### <a name="data-writes"></a>Datenschreibvorgänge

- Das primäre Computereplikat schreibt nicht direkt auf Seitenserver. Stattdessen werden Protokolldatensätze des Protokolldiensts auf entsprechenden Seitenservern erneut wiedergegeben.
- Schreibvorgänge im Computereplikat werden vorwiegend in den lokalen RBPEX-Cache „(file_id 0)“ geschrieben. Bei Schreibvorgängen in logischen Dateien, die größer als 8 KB sind (d. h. dazu wurde [Gather-Write](/sql/relational-databases/writing-pages/) verwendet), wird jeder Vorgang in mehrere einzelne 8-KB-Schreibvorgänge in RBPEX übersetzt, da der Pufferpool und der RBPEX-Cache immer 8-KB-Seiten verwenden. Infolgedessen ist die Anzahl der für RBPEX angezeigten E/A-Schreibvorgänge möglicherweise größer als die tatsächliche Anzahl der von der Engine ausgeführten E/A-Vorgänge.
- Nicht-RBPEX-Dateien oder andere Datendateien als „file_id 0“, die Seitenservern entsprechen, zeigen ebenfalls Schreibvorgänge an. In der Dienstebene „Hyperscale“ werden diese Schreibvorgänge simuliert, weil die Computereplikate niemals direkt auf Seitenserver schreiben. Schreib-IOPS und Durchsatz werden für das Computereplikat berücksichtigt, aber die Latenz bei anderen Datendateien als „file_id 0“ spiegelt nicht die tatsächliche Latenz von Seitenserver-Schreibvorgängen wider.

### <a name="log-writes"></a>Protokollschreibvorgänge

- Bei der primären Compute-Instanz wird in „file_id 2“ von „sys.dm_io_virtual_file_stats“ ein Protokollschreibvorgang berücksichtigt. Ein Protokollschreibvorgang in der primären Computeressource ist ein Schreibvorgang in die Protokollzielzone.
- Protokolldatensätze werden auf dem sekundären Replikat bei einem Commit nicht festgeschrieben. Bei Hyperscale wird das Protokoll vom Protokolldienst asynchron auf die sekundären Replikate angewendet. Da auf sekundären Replikaten eigentlich keine Protokollschreibvorgänge erfolgen, dient die Erfassung der Protokoll-E/A-Vorgänge auf den sekundären Replikaten lediglich der Nachverfolgung.

## <a name="data-io-in-resource-utilization-statistics"></a>Daten-E/A in Statistiken zur Ressourcenauslastung

In einer Nicht-Hyperscale-Datenbank werden kombinierte Lese- und Schreib-IOPS für Datendateien relativ zum Daten-IOPS-Limit der [Ressourcenkontrolle](./resource-limits-logical-server.md#resource-governance) in den Sichten [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) und [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) in der Spalte `avg_data_io_percent` ausgegeben. Der gleiche Wert wird im Azure-Portal als _Daten-E/A in Prozent_ angezeigt.

In einer Hyperscale-Datenbank zeigt diese Spalte die Daten-IOPS-Auslastung in Bezug auf den Grenzwert für den lokalen Speicher nur für das Computereplikat an, insbesondere E/A für RBPEX und `tempdb`. Ein Wert von 100 % in dieser Spalte gibt an, dass die Ressourcenkontrolle lokale Speicher-IOPS einschränkt. Wenn dies mit einem Leistungsproblem korreliert, sollten Sie die Workload so optimieren, dass weniger E/A generiert wird, oder Sie erhöhen das Ziel des Datenbankdiensts, um das [Limit](resource-limits-vcore-single-databases.md) _Max. Data-IOPS_ der Ressourcenkontrolle zu erhöhen. Für die Ressourcenkontrolle für RBPEX-Lese- und -Schreibvorgänge zählt das System einzelne E/A-Vorgänge mit einer Größe von 8 KB statt größere E/A-Vorgänge, die möglicherweise von der SQL Server-Datenbank-Engine ausgegeben werden.

Daten-E/A für Remoteseitenserver wird nicht in Ressourcenverwendungssichten oder im Portal gemeldet, sondern in der [sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/)-DMF, wie bereits erwähnt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- Informationen zu V-Kern-Ressourcenlimits für eine Hyperscale-Einzeldatenbank finden Sie unter [Dienstebene „Hyperscale“ – V-Kern-Limits](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5).
- Informationen zur Leistungsoptimierung bei Azure SQL-Datenbank finden Sie unter [Abfrageleistung in Azure SQL-Datenbank](performance-guidance.md).
- Informationen zur Leistungsoptimierung mithilfe von Abfragespeicher finden Sie unter [Leistungsüberwachung mit dem Abfragespeicher](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/).
- Informationen zu DMV-Überwachungsskripts finden Sie unter [Überwachen der Leistung von Azure SQL-Datenbank mit dynamischen Verwaltungssichten](monitoring-with-dmvs.md).