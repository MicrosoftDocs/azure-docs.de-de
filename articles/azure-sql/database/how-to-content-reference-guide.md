---
title: 'Inhaltsreferenz: Konfigurieren und Verwalten'
description: Hier finden Sie eine Inhaltsreferenz, in der das Konfigurieren und Verwalten von Azure SQL-Datenbank erläutert wird.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 01/14/2020
ms.openlocfilehash: 19cec1deeef344ea5897061db0fec88066b90b05
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791765"
---
# <a name="configure-and-manage-content-reference---azure-sql-database"></a>Inhaltsreferenz: Konfigurieren und Verwalten – Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dieser Artikel enthält eine Inhaltsreferenz für verschiedene Anleitungen, Skripts und Erläuterungen, die beim Verwalten und Konfigurieren einer Azure SQL-Datenbank-Instanz hilfreich sein können. 

## <a name="load-data"></a>Laden der Daten

- [Migrieren zu SQL-Datenbank](migrate-to-database-from-sql-server.md)
- Erfahren Sie, wie Sie [SQL-Datenbank nach der Migration verwalten](manage-data-after-migrating-to-database.md).
- [Kopieren einer Datenbank](database-copy.md)
- [Importieren einer Datenbank über eine BACPAC-Datei](database-import.md)
- [Exportieren einer Datenbank in eine BACPAC-Datei](database-export.md)
- [Laden von Daten mit BCP](../load-from-csv-with-bcp.md)
- [Laden von Daten mit ADF](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="configure-features"></a>Konfigurieren von Features

- [Konfigurieren der Azure Active Directory-Authentifizierung (Azure AD-Authentifizierung)](authentication-aad-configure.md)
- [Konfigurieren des bedingten Zugriffs](conditional-access-configure.md)
- [Mehrstufige Azure AD-Authentifizierung](authentication-mfa-ssms-overview.md)
- [Konfigurieren der mehrstufigen Authentifizierung](authentication-mfa-ssms-configure.md)
- [Konfigurieren einer temporalen Aufbewahrungsrichtlinie](temporal-tables-retention-policy.md)
- [Konfigurieren von TDE mit BYOK](transparent-data-encryption-byok-configure.md)
- [Rotieren von TDE-BYOK-Schlüsseln](transparent-data-encryption-byok-key-rotation.md)
- [Entfernen einer TDE-Schutzvorrichtung](transparent-data-encryption-byok-remove-tde-protector.md)
- [Konfigurieren von In-Memory OLTP](../in-memory-oltp-configure.md)
- [Konfigurieren von Azure Automation](automation-manage.md)
- [Konfigurieren der Transaktionsreplikation](replication-to-sql-database.md) zum Replizieren Ihrer Daten zwischen Datenbanken.
- [Konfigurieren der Bedrohungserkennung](threat-detection-configure.md), um Azure SQL-Datenbank zur Erkennung verdächtiger Aktivitäten, z.B. der Einschleusung von SQL-Befehlen oder dem Zugriff von verdächtigen Standorten, zu verwenden.
- [Konfigurieren der dynamischen Datenmaskierung](dynamic-data-masking-configure-portal.md) zum Schutz Ihrer sensiblen Daten.
- [Konfigurieren der Sicherungsaufbewahrung](long-term-backup-retention-configure.md) zum Speichern Ihrer Sicherungen in Azure Blob Storage. 
- [Konfigurieren der Georeplikation](active-geo-replication-overview.md), um ein Replikat Ihrer Datenbank in einer anderen Region aufzubewahren.
- [Konfigurieren der Sicherheit für geografische Replikate](active-geo-replication-security-configure.md).

## <a name="monitor-and-tune-your-database"></a>Überwachen und Optimieren Ihrer Datenbank

- [Manuelles Optimieren](performance-guidance.md)
- [Überwachen der Leistung mithilfe von DMVs](monitoring-with-dmvs.md)
- [Überwachen der Leistung mit dem Abfragespeicher](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Aktivieren der automatischen Optimierung](automatic-tuning-enable.md), damit Azure SQL-Datenbank die Leistung Ihrer Workload optimiert.
- [Aktivieren von E-Mail-Benachrichtigungen zur automatischen Optimierung](automatic-tuning-email-notifications-configure.md) zum Abrufen von Informationen zu Optimierungsempfehlungen.
- [Anwendung von Empfehlungen zur Leistung](database-advisor-find-recommendations-portal.md) und Optimieren der Datenbank.
- [Erstellen von Warnungen](alerts-insights-configure-portal.md), um Benachrichtigungen von Azure SQL-Datenbank zu erhalten.
- [Behandeln von Konnektivitätsproblemen](troubleshoot-common-errors-issues.md), wenn Sie Verbindungsprobleme zwischen den Anwendungen und der Datenbank feststellen. Sie können auch [Resource Health bei Konnektivitätsproblemen verwenden](resource-health-to-troubleshoot-connectivity.md).
- [Behandeln von Leistungsproblemen mithilfe von Intelligent Insights](intelligent-insights-troubleshoot-performance.md)
- [Verwalten von Dateispeicherplatz](file-space-manage.md) zum Überwachen der Speichernutzung in der Datenbank.
- [Verwenden des Intelligent Insights-Diagnoseprotokolls](intelligent-insights-use-diagnostics-log.md)
- [Überwachen des In-Memory-OLTP-Speicherplatzes](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Erweiterte Ereignisse

- [Erweiterte Ereignisse](xevent-db-diff-from-svr.md)
- [Speichern erweiterter Ereignisse in der Ereignisdatei](xevent-code-event-file.md)
- [Speichern erweiterter Ereignisse im Ringpuffer](xevent-code-ring-buffer.md)

## <a name="query-distributed-data"></a>Abfragen verteilter Daten

- [Abfragen vertikal partitionierter Daten](elastic-query-getting-started-vertical.md) in mehreren Datenbanken.
- [Erstellen übergreifender Berichte für die horizontal hochskalierte Datenschicht](elastic-query-horizontal-partitioning.md).
- [Durchführen tabellenübergreifender Abfragen mit unterschiedlichen Schemas](elastic-query-vertical-partitioning.md).

### <a name="data-sync"></a>Datensynchronisierung

- [SQL-Datensynchronisierung](sql-data-sync-data-sql-server-sql-database.md)
- [Datensynchronisierungs-Agent](sql-data-sync-agent-overview.md)
- [Replizieren von Schemaänderungen](sql-data-sync-update-sync-schema.md)
- [Überwachen mit OMS](./monitor-tune-overview.md)
- [Bewährte Methoden für die Datensynchronisierung](sql-data-sync-best-practices.md)
- [Problembehandlung für die Datensynchronisierung](sql-data-sync-troubleshoot.md)

## <a name="elastic-database-jobs"></a>Aufträge für die elastische Datenbank

- [Erstellen und Verwalten](elastic-jobs-powershell-create.md) von Aufträgen für die elastische Datenbank mithilfe von PowerShell.
- [Erstellen und Verwalten](elastic-jobs-tsql-create-manage.md) von Aufträgen für die elastische Datenbank mithilfe von Transact-SQL.
- [Migrieren aus alten elastischen Aufträgen](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Datenbank-Sharding

- [Upgraden der Clientbibliothek für elastische Datenbanken](elastic-scale-upgrade-client-library.md).
- [Erstellen einer App mit Sharding](elastic-scale-get-started.md).
- [Abfragen horizontal partitionierter Daten](elastic-query-getting-started.md).
- Ausführen von [Multishardabfragen](elastic-scale-multishard-querying.md).
- [Verschieben von Sharddaten](elastic-scale-configure-deploy-split-and-merge.md).
- [Konfigurieren der Sicherheit](elastic-scale-split-merge-security-configuration.md) in Datenbank-Shards.
- [Hinzufügen eines Shards](elastic-scale-add-a-shard.md) zur aktuellen Gruppe von Datenbank-Shards.
- [Beheben von Problemen bei der Shardzuordnung](elastic-database-recovery-manager.md).
- [Migrieren von Sharddatenbanken](elastic-convert-to-use-elastic-tools.md).
- [Erstellen von Leistungsindikatoren](elastic-database-perf-counters.md).
- [Verwenden von Entity Framework](elastic-scale-use-entity-framework-applications-visual-studio.md) zum Abfragen partitionierter Daten.
- [Verwenden des Dapper-Frameworks](elastic-scale-working-with-dapper.md) zum Abfragen partitionierter Daten.

## <a name="develop-applications"></a>Entwickeln von Anwendungen

- [Konnektivität](connect-query-content-reference-guide.md#libraries)
- [Verwenden des Spark-Connectors](spark-connector.md)
- [Authentifizieren der App](application-authentication-get-client-id-keys.md)
- [Verwenden der Batchverarbeitung für bessere Leistung](../performance-improve-use-batching.md)
- [Konnektivitätsleitfaden](troubleshoot-common-connectivity-issues.md)
- [DNS-Aliase](dns-alias-overview.md)
- [Einrichten eines DNS-Alias über PowerShell](dns-alias-powershell-create.md)
- [Ports – ADO.NET](adonet-v12-develop-direct-route-ports.md)
- [C und C ++](develop-cplusplus-simple.md)
- [Excel](connect-excel.md)

## <a name="design-applications"></a>Entwerfen von Anwendungen

- [Entwurf für die Notfallwiederherstellung](designing-cloud-solutions-for-disaster-recovery.md)
- [Entwurf für Pools für elastische Datenbanken](disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Entwurf für App-Upgrades](manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-software-as-a-service-saas-applications"></a>Entwerfen von mehrinstanzenfähigen Software-as-a-Service-Anwendungen (SaaS-Anwendungen)

- [SaaS-Entwurfsmuster](saas-tenancy-app-design-patterns.md)
- [SaaS-Video Indexer](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS-App-Sicherheit](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Anleitungen zu Azure SQL Managed Instance](../managed-instance/how-to-content-reference-guide.md).