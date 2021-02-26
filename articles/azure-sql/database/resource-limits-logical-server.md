---
title: Ressourcenlimits für logische Server in Azure
description: Dieser Artikel bietet eine Übersicht über die Ressourcenlimits für den logischen Server in Azure, der von Azure SQL-Datenbank und Azure Synapse Analytics verwendet wird. Darüber hinaus bietet er Informationen darüber, was geschieht, wenn diese Ressourcenlimits erreicht oder überschritten werden.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 02/02/2021
ms.openlocfilehash: e8f18f56c746f0d12f43cc2fb6ce9088a9b82b45
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492381"
---
# <a name="resource-limits-for-azure-sql-database-and-azure-synapse-analytics-servers"></a>Ressourcenlimits für Azure SQL-Datenbank und Azure Synapse Analytics-Server.
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Dieser Artikel bietet eine Übersicht über die Ressourcenlimits für den logischen Server, der von Azure SQL-Datenbank und Azure Synapse Analytics verwendet wird. Hier erfahren Sie, was passiert, wenn diese Ressourcenlimits erreicht oder überschritten werden. Außerdem werden die zum Erzwingen dieser Grenzwerte verwendeten Ressourcenkontrollmechanismen beschrieben.

> [!NOTE]
> Informationen zu den Grenzwerten für Azure SQL Managed Instance finden Sie unter [Ressourcenlimits bei SQL-Datenbank für verwaltete Instanzen](../managed-instance/resource-limits.md).

## <a name="maximum-resource-limits"></a>Maximale Ressourcenlimits

| Resource | Begrenzung |
| :--- | :--- |
| Datenbanken pro Server | 5\.000 |
| Standardanzahl von Servern pro Abonnement in beliebiger Region | 20 |
| Maximale Anzahl von Servern pro Abonnement in beliebiger Region | 200 |  
| DTU/eDTU-Kontingent pro Server | 54.000 |  
| Virtuelle Kerne pro Server/Instanz | 540 |
| Maximale Anzahl von Pools pro Server | Begrenzt durch die Anzahl von DTUs oder virtuellen Kernen. Beispiel: Wenn jeder Pool 1.000 DTUs umfasst, kann ein Server 54 Pools unterstützen.|
|||

> [!IMPORTANT]
> Wenn sich die Anzahl der Datenbanken dem Grenzwert pro Server nähert, kann Folgendes geschehen:
>
> - Höhere Latenz bei der Ausführung von Abfragen, die die Masterdatenbank betreffen.  Dies bezieht sich auch auf die Ansichten der Ressourcennutzungsstatistiken (z. B. sys.resource_stats) ein.
> - Höhere Latenz bei Verwaltungsvorgängen und dem Rendern von Portalblickpunkten. Dazu gehört auch das Aufzählen von Datenbanken auf dem Server.

> [!NOTE]
> Wenn Sie ein höheres DTU-/eDTU-Kontingent oder eine höhere Anzahl von virtuellen Kernen oder Servern als die Standardanzahl erhalten möchten, übermitteln Sie im Azure-Portal eine neue Supportanfrage. Weitere Informationen finden Sie unter [Anfordern von Kontingenterhöhungen für Azure SQL-Datenbank](quota-increase-request.md).

### <a name="storage-size"></a>Speichergröße

Informationen zu den Grenzwerten bei der Speichergröße pro Tarif für Ressourcenspeichergrößen von Einzeldatenbanken finden Sie unter [DTU-Ressourcenlimits](resource-limits-dtu-single-databases.md) und [V-Kern-Ressourcenlimits](resource-limits-vcore-single-databases.md).

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Was geschieht, wenn die Datenbankressourcen erreicht werden?

### <a name="compute-cpu"></a>Compute (CPU)

Bei einer hohen Computenutzung (CPU-Auslastung) der Datenbank erhöht sich die Abfragelatenz – bis hin zu möglichen Abfragetimeouts. Unter diesen Bedingungen können Abfragen vom Dienst in die Warteschlange eingereiht werden, und dann werden Ressourcen für die Ausführung bereitgestellt, wenn Ressourcen frei werden.
Wenn eine hohe Computenutzung festgestellt wird, stehen folgende Optionen als Gegenmaßnahmen zur Verfügung:

- Erhöhen der Computegröße der Datenbank oder des Pools für elastische Datenbanken, um mehr Computeressourcen für die Datenbank bereitzustellen. Siehe [Skalieren der Ressourcen für einzelne Datenbanken](single-database-scale.md) und [Skalieren der Ressourcen für elastische Pools in Azure SQL-Datenbank](elastic-pool-scale.md).
- Optimieren von Abfragen, um die CPU-Ressourcennutzung der einzelnen Abfragen zu verringern. Weitere Informationen finden Sie unter [Abfrageoptimierung/Abfragehinweise](performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

Wenn der verwendete Datenbankspeicherplatz die maximale Größe erreicht, treten bei Einfügungen in die Datenbank und Updates, die die Datenmenge erhöhen, Fehler auf, sodass Clients eine [Fehlermeldung](troubleshoot-common-errors-issues.md) erhalten. SELECT- und DELETE-Anweisungen werden weiterhin erfolgreich ausgeführt.

Wenn eine hohe Speicherplatznutzung festgestellt wird, stehen folgende Optionen als Gegenmaßnahmen zur Verfügung:

- Erhöhen der maximalen Größe der Datenbank oder des Pools für elastische Datenbanken oder Hinzufügen von zusätzlichem Speicher. Siehe [Skalieren der Ressourcen für einzelne Datenbanken](single-database-scale.md) und [Skalieren der Ressourcen für elastische Pools in Azure SQL-Datenbank](elastic-pool-scale.md).
- Wenn sich die Datenbank in einem Pool für elastische Datenbanken befindet, kann sie auch aus dem Pool heraus verschoben werden, damit ihr Speicherplatz nicht mit anderen Datenbanken geteilt wird.
- Verkleinern Sie eine Datenbank, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](file-space-manage.md).
- Überprüfen Sie, ob der Grund für die hohe Speicherplatznutzung eine Spitze bei der Größe des permanenten Versionsspeichers (PVS) ist. PVS ist ein Teil jeder Datenbank und wird zum Implementieren der [beschleunigten Datenbankwiederherstellung](../accelerated-database-recovery.md) verwendet. Informationen zum Ermitteln der aktuellen PVS-Größe finden Sie unter [PVS-Problembehandlung](https://docs.microsoft.com/sql/relational-databases/accelerated-database-recovery-management#troubleshooting). Ein häufiger Grund für eine große PVS-Größe ist eine Transaktion, die während eines langen Zeitraums (Stunden) geöffnet ist und so die Bereinigung älterer Versionen in PVS verhindert.

### <a name="sessions-and-workers-requests"></a>Sitzungen und Worker (Anforderungen)

Die maximale Anzahl von Sitzungen und Workern wird durch die Dienstebene und Computegröße (DTUs/eDTUs oder V-Kerne) bestimmt. Neue Anforderungen werden abgelehnt, wenn Sitzung oder Worker Grenzwerte erreicht haben, und Clients erhalten eine Fehlermeldung. Während die Anzahl der verfügbaren Verbindungen von der Anwendung gesteuert werden kann, ist die Anzahl paralleler Worker oft schwieriger zu schätzen und zu steuern. Dies gilt insbesondere während der Spitzenlastzeiten, wenn Datenbankressourcenlimits erreicht werden und sich Worker aufgrund längerer Ausführungszeiten von Abfragen, langer Blockierungsketten oder einer übermäßigen Abfrageparallelität anhäufen.

Wenn eine hohe Sitzungs- oder Workernutzung festgestellt wird, stehen folgende Optionen als Gegenmaßnahmen zur Verfügung:

- Erhöhen der Dienstebene oder Computegröße der Datenbank oder des Pools für elastische Datenbanken. Siehe [Skalieren der Ressourcen für einzelne Datenbanken](single-database-scale.md) und [Skalieren der Ressourcen für elastische Pools in Azure SQL-Datenbank](elastic-pool-scale.md).
- Optimieren von Abfragen, um die Ressourcenverwendung durch die einzelnen Abfragen zu verringern, wenn die Ursache der zunehmenden Auslastung durch Worker Konflikte bezüglich der Computeressourcen sind. Weitere Informationen finden Sie unter [Abfrageoptimierung/Abfragehinweise](performance-guidance.md#query-tuning-and-hinting).
- Verringern der Einstellung [MAXDOP](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (maximaler Grad an Parallelität).
- Optimieren der Abfragearbeitsauslastung, um die Anzahl von Vorkommen und die Dauer der Abfrageblockierung zu verringern. Weitere Informationen finden Sie unter [Verstehen und Beheben von Problemen durch Blockierungen in Azure SQL](understand-resolve-blocking.md).

### <a name="memory"></a>Arbeitsspeicher

Im Gegensatz zu anderen Ressourcen (CPU, Worker, Speicher) wirkt sich das Erreichen des Arbeitsspeicherlimits nicht negativ auf die Abfrageleistung aus und führt nicht zu Fehlern und Ausfällen. Wie im [Handbuch zur Architektur der Speicherverwaltung](/sql/relational-databases/memory-management-architecture-guide) ausführlich beschrieben, wird von der SQL Server-Datenbank-Engine häufig der gesamte verfügbare Arbeitsspeicher genutzt. Dies ist so beabsichtigt. Arbeitsspeicher wird hauptsächlich zum Zwischenspeichern von Daten genutzt, um teurere Speicherzugriffe zu vermeiden. Daher führt eine höhere Arbeitsspeicherauslastung dank schnellerer Lesevorgänge aus dem Arbeitsspeicher (im Gegensatz zu langsameren Lesevorgängen aus dem Speicher) in der Regel zu einer besseren Abfrageleistung.

Wenn die Workload nach dem Start der Datenbank-Engine damit beginnt, Daten aus dem Speicher zu lesen, werden Daten aggressiv im Arbeitsspeicher zwischengespeichert. Nach dieser anfänglichen Anlaufphase nähern sich in [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) die Werte in den Spalten `avg_memory_usage_percent` und `avg_instance_memory_percent` häufig der 100-Prozent-Marke an oder erreichen diese sogar. Dies gilt insbesondere bei Datenbanken, die sich nicht im Leerlauf befinden und nicht vollständig in den Arbeitsspeicher passen.

Arbeitsspeicher wird aber nicht nur als Datencache, sondern auch in anderen Komponenten der Datenbank-Engine verwendet. Wenn Arbeitsspeicher benötigt wird und der gesamte verfügbare Arbeitsspeicher durch den Datencache beansprucht wurde, wird der Datencache durch die Datenbank-Engine dynamisch verkleinert, um Arbeitsspeicher für andere Komponenten verfügbar zu machen, und dynamisch vergrößert, wenn Arbeitsspeicher von anderen Komponenten freigegeben wird.

In seltenen Fällen kann eine besonders anspruchsvolle Workload dazu führen, dass nicht genügend Arbeitsspeicher zur Verfügung steht, was entsprechende Fehler zur Folge hat. Dies kann bei jeder beliebigen Arbeitsspeicherauslastung zwischen null und 100 Prozent passieren. Eine solche Situation entsteht tendenziell eher bei kleineren Computegrößen mit entsprechend niedrigerem Arbeitsspeicherlimit bzw. bei Workloads, bei denen mehr Arbeitsspeicher für die Abfrageverarbeitung beansprucht wird (beispielsweise in [umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md)).

Im Falle von Fehlern aufgrund von unzureichendem Arbeitsspeicher haben Sie unter anderem folgende Möglichkeiten:
- Erhöhen der Dienstebene oder Computegröße der Datenbank oder des Pools für elastische Datenbanken. Siehe [Skalieren der Ressourcen für einzelne Datenbanken](single-database-scale.md) und [Skalieren der Ressourcen für elastische Pools in Azure SQL-Datenbank](elastic-pool-scale.md).
- Optimieren der Abfragen und der Konfiguration, um die Arbeitsspeicherauslastung zu verringern. Gängige Lösungen sind in der folgenden Tabelle beschrieben:

|Lösung|Beschreibung|
| :----- | :----- |
|Verkleinern von Speicherzuweisungen|Weitere Informationen zu Speicherzuweisungen finden Sie im Blogbeitrag [Grundlegendes zu SQL Server-Speicherzuweisungen](https://techcommunity.microsoft.com/t5/sql-server/understanding-sql-server-memory-grant/ba-p/383595). Eine gängige Lösung zur Vermeidung übermäßig großer Speicherzuweisungen besteht darin, die [Statistik](/sql/relational-databases/statistics/statistics) auf dem neuesten Stand zu halten. Dies ermöglicht eine präzisere Schätzung der Arbeitsspeicherauslastung durch die Abfrage-Engine und die Vermeidung überdimensionierter Speicherzuweisungen.</br></br>In Datenbanken ab dem Kompatibilitätsgrad 140 kann die Speicherzuweisungsgröße durch die Datenbank-Engine unter Verwendung von [Feedback zur Speicherzuweisung im Batchmodus](/sql/relational-databases/performance/intelligent-query-processing#batch-mode-memory-grant-feedback) automatisch angepasst werden. In Datenbanken ab dem Kompatibilitätsgrad 150 wird von der Datenbank-Engine auf ähnliche Weise [Feedback zur Speicherzuweisung im Zeilenmodus](/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback) für gängigere Abfragen im Zeilenmodus verwendet. Diese integrierte Funktion trägt dazu bei, durch unzureichenden Arbeitsspeicher bedingte Fehler zu vermeiden, die auf überdimensionierte Speicherzuweisungen zurückzuführen sind.|
|Verkleinern des Abfrageplancaches|Von der Datenbank-Engine werden Abfragepläne im Arbeitsspeicher zwischengespeichert, um zu vermeiden, dass für jede Abfrageausführung ein Abfrageplan kompiliert wird. Aktivieren Sie die [datenbankweit gültige Konfiguration](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) „OPTIMIZE_FOR_AD_HOC_WORKLOADS“, um eine Überfrachtung des Abfrageplancaches durch die Zwischenspeicherung von Plänen zu vermeiden, die lediglich einmal verwendet werden.|
|Verkleinern des für Sperren beanspruchten Arbeitsspeichers|Von der Datenbank-Engine wird Arbeitsspeicher für [Sperren](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#Lock_Engine) verwendet. Vermeiden Sie nach Möglichkeit umfangreiche Transaktionen mit zahlreichen Sperren, die eine hohe Auslastung des für Sperren beanspruchten Arbeitsspeichers zur Folge haben.|


## <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>Ressourcenauslastung durch Benutzerarbeitsauslastungen und interne Prozesse

Die CPU- und Arbeitsspeicherauslastung durch Benutzerarbeitsauslastungen in den einzelnen Datenbanken wird in den Spalten `avg_cpu_percent` und `avg_memory_usage_percent` der Sichten [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) und [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) gemeldet. Bei Pools für elastische Datenbanken wird die Ressourcenauslastung auf Poolebene in der Sicht [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) gemeldet. Die CPU-Auslastung durch Benutzerarbeitsauslastungen wird auch über die Azure Monitor-Metrik `cpu_percent` gemeldet, und zwar für [Einzeldatenbanken](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserversdatabases) und für [Pools für elastische Datenbanken](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) auf Poolebene.

Azure SQL-Datenbank benötigt Computeressourcen zur Implementierung zentraler Dienstfeatures wie Hochverfügbarkeit und Notfallwiederherstellung, Datenbanksicherung und -wiederherstellung, Überwachung, Abfragespeicher, automatische Optimierung und Ähnliches. Ein gewisser Teil der Gesamtressourcen wird mithilfe von Mechanismen der [Ressourcengovernance](#resource-governance) für diese internen Prozesse reserviert. Die übrigen Ressourcen stehen für Benutzerarbeitsauslastungen zur Verfügung. Wenn von internen Prozessen keine Computeressourcen beansprucht werden, werden die Ressourcen für Benutzerworkloads bereitgestellt.

Die Gesamtauslastung für CPU und Arbeitsspeicher durch Benutzerarbeitsauslastungen und interne Prozesse wird in den Spalten `avg_instance_cpu_percent` und `avg_instance_memory_percent` der Sichten [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) und [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) gemeldet. Diese Daten werden auch über die Azure Monitor-Metriken `sqlserver_process_core_percent` und `sqlserver_process_memory_percent` gemeldet, und zwar für [Einzeldatenbanken](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserversdatabases) und für [Pools für elastische Datenbanken](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) auf Poolebene.

Eine detailliertere Aufschlüsselung der aktuellen Ressourcenauslastung durch Benutzerarbeitsauslastungen und interne Prozesse finden Sie in den Sichten [sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) und [sys.dm_resource_governor_workload_groups_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database). Ausführliche Informationen zu Ressourcenpools und Arbeitsauslastungsgruppen, auf die in diesen Sichten verwiesen wird, finden Sie unter [Ressourcengovernance](#resource-governance). In diesen Sichten wird die Ressourcenverwendung durch Benutzerarbeitsauslastungen und bestimmte interne Prozesse in den zugeordneten Ressourcenpools und Arbeitsauslastungsgruppen gemeldet.

Bei der Leistungsüberwachung und Problembehandlung muss sowohl die **benutzerspezifische CPU-Auslastung** (`avg_cpu_percent`, `cpu_percent`) als auch die **CPU-Gesamtauslastung** für Benutzerworkloads und interne Prozesse (`avg_instance_cpu_percent`, `sqlserver_process_core_percent`) berücksichtigt werden.

Die **benutzerspezifische CPU-Auslastung** wird als Prozentsatz der Grenzwerte für Benutzerarbeitsauslastungen in den einzelnen Dienstzielen berechnet. Eine **benutzerspezifische CPU-Auslastung** von 100 Prozent gibt an, dass für die Benutzerarbeitsauslastung der Grenzwert des Dienstziels erreicht wurde. Wenn sich jedoch die **CPU-Gesamtauslastung** zwischen 70 und 100 Prozent bewegt, kann es zu einer Abflachung des Durchsatzes für Benutzerworkloads sowie zu einer Erhöhung der Abfragelatenz kommen, auch wenn die gemeldete **benutzerspezifische CPU-Auslastung** deutlich unter 100 Prozent liegt. Dieses Verhalten wird durch die Verwendung kleinerer Dienstziele mit einer moderaten Zuordnung von Computeressourcen, aber relativ intensiven Benutzerarbeitsauslastungen (etwa in [umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md)) begünstigt. Es kann aber auch bei kleineren Dienstzielen auftreten, wenn interne Prozesse vorübergehend zusätzliche Ressourcen benötigen (etwa beim Erstellen eines neuen Replikats der Datenbank).

Bei einer hohen **CPU-Gesamtauslastung** stehen die gleichen Abhilfeoptionen zur Verfügung, die weiter oben bereits erwähnt wurden. Hierzu zählen unter anderem die Erhöhung des Dienstziels und/oder die Optimierung der Benutzerworkload.

## <a name="resource-governance"></a>Ressourcengovernance

Zur Erzwingung von Ressourcenlimits verwendet Azure SQL-Datenbank eine auf SQL Server [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) basierende Ressourcengovernanceimplementierung, die geändert und erweitert wurde, um in Azure SQL-Datenbank ausgeführt zu werden. In SQL-Datenbank stellen mehrere [Ressourcenpools](/sql/relational-databases/resource-governor/resource-governor-resource-pool) und [Arbeitsauslastungsgruppen](/sql/relational-databases/resource-governor/resource-governor-workload-group) mit Ressourcenlimits, die auf Pool- und Gruppenebene festgelegt werden, eine [ausgeglichene Database-as-a-Service-Lösung](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/) bereit. Die Benutzerarbeitsauslastung und interne Workloads werden in separate Ressourcenpools und Arbeitsauslastungsgruppen eingeteilt. Die Benutzerarbeitsauslastung auf den primären und lesbaren sekundären Replikaten, einschließlich Georeplikaten, wird als Ressourcenpool `SloSharedPool1` und Arbeitsauslastungsgruppe `UserPrimaryGroup.DBId[N]` klassifiziert, wobei `N` für den Datenbank-ID-Wert steht. Außerdem gibt es mehrere Ressourcenpools und Arbeitsauslastungsgruppen für verschiedene interne Workloads.

Zusätzlich zur Verwendung von Resource Governor zum Steuern von Ressourcen innerhalb des SQL-Prozesses verwendet Azure SQL-Datenbank auch Windows-[Auftragsobjekte](/windows/win32/procthread/job-objects) für die Ressourcenkontrolle auf Prozessebene und den [Ressourcen-Manager für Dateiserver (File Server Resource Manager, FSRM)](/windows-server/storage/fsrm/fsrm-overview) von Windows für die Verwaltung von Speicherkontingenten.

Die Azure SQL-Datenbank-Ressourcenkontrolle ist hierarchisch strukturiert. Grenzwerte werden von oben nach unten auf Betriebssystemebene und auf Speichervolumeebene mithilfe von Betriebssystem-Ressourcenkontrollmechanismen und Resource Governor, dann auf Ressourcenpoolebene mithilfe von Resource Governor und dann auf Arbeitsauslastungsgruppenebene mithilfe von Resource Governor erzwungen. Die für die aktuelle Datenbank oder den Pool für elastische Datenbanken geltenden Grenzwerte der Ressourcenkontrolle sind in der Ansicht [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) angegeben.

### <a name="data-io-governance"></a>Daten-E/A-Governance

In Azure SQL-Datenbank ist die Daten-E/A-Governance ein Prozess zum Begrenzen der physischen E/A-Lese- und -Schreibvorgänge für Datendateien einer Datenbank. IOPS-Grenzwerte werden für jeden Servicelevel festgelegt, um den „Noisy Neighbor“-Effekt zu minimieren, eine gerechte Ressourcenzuordnung im mehrinstanzenfähigen Dienst zu gewährleisten und die Funktionen der zugrunde liegenden Hardware und Speicher zu unterstützen.

Bei Einzeldatenbanken gelten die Grenzwerte für Arbeitsauslastungsgruppen für alle Speicher-E/A-Vorgänge für die Datenbank, während die Grenzwerte für Ressourcenpools für alle Speicher-E/A-Vorgänge für alle Datenbanken im gleichen dedizierten SQL-Pool (einschließlich der Datenbank `tempdb`) gelten. Bei Pools für elastische Datenbanken gelten die Grenzwerte für Arbeitsauslastungsgruppen für jede Datenbank im Pool, während die Grenzwerte für Ressourcenpools für den gesamten Pool für elastische Datenbanken gelten, einschließlich der Datenbank `tempdb`, die von allen Datenbanken im Pool gemeinsam genutzt wird. Im Allgemeinen können die Grenzwerte für Ressourcenpools nicht von der Workload einer Datenbank (entweder einzeln oder in einem Pool) erreicht werden, weil die Grenzwerte für Arbeitsauslastungsgruppen niedriger sind als die Grenzwerte für Ressourcenpools und IOPS/den Durchsatz früher begrenzen. Poolgrenzwerte können jedoch von der kombinierten Workload mehrerer Datenbanken für denselben Pool erreicht werden.

Beispiel: Wenn eine Abfrage 1.000 IOPS ohne E/A-Ressourcenkontrolle generiert, der maximale IOPS-Grenzwert für die Workloadgruppe aber auf 900 IOPS festgelegt ist, kann die Abfrage maximal 900 IOPS generieren. Wenn der maximale IOPS-Grenzwert für den Ressourcenpool jedoch auf 1.500 IOPS festgelegt ist und die Gesamtanzahl der E/A-Vorgänge aller dem Ressourcenpool zugeordneten Arbeitsauslastungsgruppen 1.500 IOPS überschreitet, kann die Anzahl der E/A-Vorgänge der gleichen Abfrage so reduziert werden, dass sie unter dem Grenzwert von 900 IOPS liegt.

Die von der Ansicht [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) zurückgegebenen Mindest- und Höchstwerte für IOPS und Durchsatz gelten als Grenzwerte/Obergrenzen, nicht als Garantien. Darüber hinaus garantiert die Ressourcenkontrolle keine bestimmte Speicherlatenz. Die bestmöglichen erreichbaren Latenzen, IOPS und Durchsatzwerte für eine bestimmte Benutzerarbeitsauslastung hängen nicht nur von den Grenzwerten der E/A-Ressourcenkontrolle, sondern auch von der Mischung der verwendeten E/A-Größen und den Funktionen des zugrunde liegenden Speichers ab. Von SQL-Datenbank werden E/A-Vorgänge in Größenordnungen zwischen 512 KB und 4 MB verwendet. Zur Erzwingung der IOPS-Grenzwerte wird jede E/A unabhängig von der Größe berücksichtigt, mit Ausnahme von Datenbanken mit Datendateien in Azure Storage. In diesem Fall werden E/A-Vorgänge, die größer als 256 KB sind, als mehrere E/A-Vorgänge mit 256 KB berücksichtigt, um sie mit der E/A-Berücksichtigung von Azure Storage abzustimmen.

Bei Datenbanken vom Typ „Basic“, „Standard“ und „Universell“, die Datendateien in Azure Storage verwenden, kann der Wert `primary_group_max_io` möglicherweise nicht erreicht werden, wenn eine Datenbank nicht über genügend Datendateien verfügt, um diese Anzahl von IOPS kumulativ bereitzustellen, die Daten nicht gleichmäßig auf die Dateien verteilt sind oder die Leistungsstufe der zugrunde liegenden Blobs die IOPS bzw. den Durchsatz auf einen Wert begrenzt, der unter den Grenzwerten der Ressourcenkontrolle liegt. Ebenso kann der Wert `primary_max_log_rate` bei kleinen Protokoll-E/As, die durch häufige Transaktionscommits generiert werden, aufgrund des IOPS-Grenzwerts des zugrunde liegenden Azure Storage-Blobs möglicherweise nicht von einer Workload erreicht werden. Für Datenbanken, die Azure Storage Premium verwenden, verwendet Azure SQL-Datenbank ausreichend große Speicherblobs, um die benötigten IOPS bzw. den Durchsatz unabhängig von der Datenbankgröße zu erhalten. Bei größeren Datenbanken werden mehrere Datendateien erstellt, um die Kapazität für die gesamten IOPS bzw. den Durchsatz zu erhöhen.

In den Ansichten [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) und [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) erfasste Ressourcennutzungswerte wie `avg_data_io_percent` und `avg_log_write_percent` werden als Prozentsätze der maximalen Grenzwerte der Ressourcenkontrolle berechnet. Wenn andere Faktoren als die Ressourcenkontrolle die IOPS/den Durchsatz einschränken, kann es daher bei zunehmender Arbeitsauslastung zu einer Abflachung der IOPS/des Durchsatzes sowie zu höheren Latenzen kommen, auch wenn die gemeldete Ressourcenauslastung unter 100 Prozent bleibt.

Wenn Sie Werte für Lese- und Schreib-IOPS, Durchsatz und Latenz pro Datenbankdatei anzeigen möchten, verwenden Sie die Funktion [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql). Diese Funktion zeigt alle E/A-Vorgänge für die Datenbank – einschließlich E/A-Vorgänge im Hintergrund, die nicht für `avg_data_io_percent` berücksichtigt werden, aber IOPS und Durchsatz des zugrunde liegenden Speichers beanspruchen und sich auf die Speicherlatenz auswirken können. Die Funktion zeigt zusätzliche Wartezeit, die möglicherweise von der E/A-Ressourcenkontrolle für Lese- und Schreibvorgänge in den Spalten `io_stall_queued_read_ms` und `io_stall_queued_write_ms` eingeführt wird.

### <a name="transaction-log-rate-governance"></a>Transaktionsprotokollratengovernance

Als Transaktionsprotokollratengovernance wird der Prozess in Azure SQL-Datenbank bezeichnet, der verwendet wird, um hohe Datenerfassungsraten für Workloads zu begrenzen, z. B. für Masseneinfügung (BULK INSERT), SELECT INTO und für die Indizierung. Diese Grenzwerte werden für die Rate, in der Protokolleinträge generiert werden, in Sekundenbruchteilen verfolgt und erzwungen. Dadurch wird der Durchsatz unabhängig von den E/A-Größen der Datendateien eingeschränkt.  Die Generierungsrate von Transaktionsprotokollen wird momentan bis zu einem hardwareabhängigen Punkt linear skaliert. Die maximal zulässige Protokollrate beträgt dabei für das vCore-basierte Kaufmodell 96 MB/s.

> [!NOTE]
> Die tatsächlichen physischen E/A-Größen für Transaktionsprotokolldateien sind nicht gesteuert oder beschränkt.

Protokollraten werden so festgelegt, dass sie in einer Vielzahl von Szenarios erreicht und erhalten werden können, während das gesamte System seine Funktionalität mit minimalen Einbußen bei der Benutzerauslastung beibehalten kann. Die Protokollratengovernance sorgt dafür, dass sich Transaktionsprotokollsicherungen innerhalb veröffentlichter Vereinbarungen zum Servicelevel für die Wiederherstellbarkeit bewegen.  Diese Governance verhindert außerdem ein exzessives Backlog bei sekundären Replikaten.

Während die Protokolleinträge generiert werden, wird jeder Vorgang ausgewertet und es wird eingeschätzt, ob er verzögert werden soll, um eine maximal erwünschte Protokollrate aufrechtzuerhalten (MB/s). Die Verzögerungen werden nicht hinzugefügt, wenn die Protokolleinträge in den Speicher geleert werden. Vielmehr wird die Protokollratengovernance während der Generierung der Protokollrate selbst angewendet.

Die tatsächlichen Protokollgenerierungsraten, die während der Laufzeit durchgesetzt werden, werden möglicherweise auch von Feedbackmechanismen beeinflusst. Dadurch wird die zulässige Protokollrate zeitweise reduziert, damit sich das System stabilisieren kann. Die Speicherplatzverwaltung für die Protokolldatei, mithilfe derer also vermieden wird, dass nicht mehr genug Protokollspeicherplatz sowie Verfügbarkeitsgruppenreplikationsmechanismen vorhanden sind, kann zeitweise zu einer Verringerung der gesamten Begrenzungen für das System führen.

Die Datenverkehrsmodellierung der Protokollratenbegrenzung erfolgt über die folgenden Wartetypen (über die Sichten [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) und [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) bereitgestellt):

| Wartetyp | Notizen |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Datenbankbegrenzung |
| POOL_LOG_RATE_GOVERNOR | Poolbegrenzung |
| INSTANCE_LOG_RATE_GOVERNOR | Instanzebenenbegrenzung |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Feedbackkontrolle; physische Replikation von Verfügbarkeitsgruppen in den Tarifen „Premium“/„Unternehmenskritisch“ zu langsam |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Feedbacksteuerung; Raten werden beschränkt, um eine Situation zu vermeiden, in der der Speicherplatz für Protokolle ausgeht |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | Feedbacksteuerung für Georeplikation, Begrenzen der Protokollrate, um hohe Datenlatenz und Nichtverfügbarkeit von sekundären Georeplikaten zu vermeiden.|
|||

Wenn es zu einer Begrenzung der Protokollrate zu kommen droht, die die gewünschte Skalierbarkeit beeinträchtigt, können Sie die folgenden Optionen in Erwägung ziehen:

- Skalieren Sie zentral auf einen höheren Servicelevel hoch, um die maximale Protokollrate von 96 MB/s zu erhalten, oder wechseln Sie zu einer anderen Dienstebene. Die Dienstebene [Hyperscale](service-tier-hyperscale.md) bietet unabhängig vom gewählten Servicelevel eine Protokollrate von 100 MB/s.
- Wenn die Daten, die geladen werden, kurzlebig sind, wie z. B. Stagingdaten in einem ETL-Prozess, können sie in eine tmpdb geladen werden (für die nur die minimal notwendigen Protokolle erstellt werden).
- In Analyseszenarios kann das Laden in eine gruppierte von einem Columnstore abgedeckte Tabelle erfolgen. Dadurch wird die erforderliche Protokollrate über Komprimierung reduziert. Dieses Vorgehen erhöht jedoch die CPU-Auslastung und eignet sich nur für Datasets, die von gruppierten Columnstore-Indizes profitieren.

### <a name="storage-space-governance"></a>Speicherplatzgovernance

Auf den Dienstebenen „Premium“ und „Unternehmenskritisch“ werden Daten und Transaktionsprotokolldateien auf dem lokalen SSD-Volume des Computers gespeichert, der die Datenbank oder den elastischen Pool hostet. Dies bietet hohe IOPS und Durchsätze sowie eine niedrige E/A-Wartezeit. Die Größe dieses lokalen Volumes hängt von den Hardwarefunktionen ab und ist begrenzt. Auf einem bestimmten Computer wird der lokale Volumespeicherplatz von Kundendatenbanken einschließlich `tempdb`, des Betriebssystems, der Verwaltungssoftware, Überwachungsdaten, Protokollen und weiteren Komponenten genutzt. Wenn Datenbanken erstellt oder gelöscht werden und ihre Speicherplatznutzung erhöht bzw. verringert wird, schwankt die lokale Speicherplatzbelegung auf einem Computer im Laufe der Zeit. 

Wenn das System erkennt, dass der verfügbare freie Speicherplatz auf einem Computer gering ist und eine Datenbank oder ein Pool für elastische Datenbanken nicht mehr über genügend Speicherplatz verfügt, wird die Datenbank oder der Pool für elastische Datenbanken auf einen anderen Computer mit ausreichend freiem Speicherplatz verschoben, sodass eine Nutzung bis zur maximalen Größenbeschränkung des konfigurierten Dienstziels möglich ist. Wie bei einem Datenbankskalierungsvorgang erfolgt diese Verschiebung online und hat eine ähnliche [Auswirkung](single-database-scale.md#impact) (kurzes Failover von mehreren Sekunden am Ende des Vorgangs). Dieses Failover beendet offene Verbindungen und führt ein Rollback für Transaktionen durch, die sich potenziell auf Anwendungen auswirken, die zu diesem Zeitpunkt die Datenbank verwenden.

Da Daten physisch auf einen anderen Computer kopiert werden, kann es möglicherweise lange dauern, größere Datenbanken zu verschieben. Wenn der lokale Speicherplatz während dieser Zeit von einer großen Benutzerdatenbank oder einem Pool für elastische Datenbanken beansprucht wird oder die `tempdb`-Datenbank schnell wächst, steigt das Risiko, dass der gesamte Speicherplatz aufgebraucht ist. Das System initiiert die Datenbankverschiebung auf eine ausgeglichene Weise, um Fehler aufgrund von nicht genügend Speicherplatz und unnötige Failover zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu allgemeinen Azure-Einschränkungen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Informationen zu DTUs und eDTUs finden Sie unter [Datenbanktransaktionseinheiten (DTUs) und elastische Datenbanktransaktionseinheiten (eDTUs)](purchasing-models.md#dtu-based-purchasing-model).
- Informationen zu Größenbeschränkungen für tempdb finden Sie unter [Tempdb-Datenbank in SQL-Datenbank](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
