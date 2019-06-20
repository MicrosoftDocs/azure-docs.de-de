---
title: Konfigurieren von Azure SQL-Datenbank – Singleton | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Singleton in Azure SQL-Datenbank konfigurieren und verwalten.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 4c6799372f203f021a07ae52a1d7f591aae5afad
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60339005"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Verwenden eines Singleton in Azure SQL-Datenbank

In diesem Abschnitt sind verschiedene Anleitungen, Skripts und Erläuterungen aufgeführt, die beim Verwalten und Konfigurieren eines Singleton in Azure SQL-Datenbank hilfreich sein können.

## <a name="migrate"></a>Migrieren

- [Migrieren zu SQL-Datenbank:](sql-database-single-database-migrate.md) Erfahren Sie etwas über den empfohlenen Migrationsprozess und die Tools für die Migration zu einer verwalteten Instanz.
- Informationen zum [Verwalten von SQL-Datenbank nach der Migration](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Konfigurieren von Features

- [Konfigurieren der Transaktionsreplikation](replication-to-sql-database.md) zum Replizieren Ihrer Daten zwischen Datenbanken.
- [Konfigurieren der Bedrohungserkennung](sql-database-threat-detection.md), um Azure SQL-Datenbank zur Erkennung verdächtiger Aktivitäten, z.B. der Einschleusung von SQL-Befehlen oder dem Zugriff von verdächtigen Standorten, zu verwenden.
- [Konfigurieren der dynamischen Datenmaskierung](sql-database-dynamic-data-masking-get-started-portal.md) zum Schutz Ihrer sensiblen Daten.
- [Konfigurieren der Sicherungsaufbewahrung](sql-database-long-term-backup-retention-configure.md) zum Speichern Ihrer Sicherungen in Azure Blob Storage. Alternativ kann auch das [Konfigurieren der Sicherungsaufbewahrung mit einem Azure-Tresor (veraltet)](sql-database-long-term-backup-retention-configure-vault.md) verwendet werden.
- [Konfigurieren der Georeplikation](sql-database-geo-replication-portal.md), um ein Replikat Ihrer Datenbank in einer anderen Region aufzubewahren.
- [Konfigurieren der Sicherheit für geografische Replikate](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Überwachen und Optimieren Ihrer Datenbank

- [Aktivieren der automatischen Optimierung](sql-database-automatic-tuning-enable.md), damit Azure SQL-Datenbank die Leistung Ihrer Workload optimiert.
- [Aktivieren von E-Mail-Benachrichtigungen zur automatischen Optimierung](sql-database-automatic-tuning-email-notifications.md) zum Abrufen von Informationen zu Optimierungsempfehlungen.
- [Anwendung von Empfehlungen zur Leistung](sql-database-advisor-portal.md) und Optimieren der Datenbank.
- [Erstellen von Warnungen](sql-database-insights-alerts-portal.md), um Benachrichtigungen von Azure SQL-Datenbank zu erhalten.
- [Behandeln von Konnektivitätsproblemen](sql-database-troubleshoot-common-connection-issues.md), wenn Sie Verbindungsprobleme zwischen den Anwendungen und der Datenbank feststellen. Sie können auch [Resource Health bei Konnektivitätsproblemen verwenden](sql-database-resource-health.md).
- [Verwalten von Dateispeicherplatz](sql-database-file-space-management.md) zum Überwachen der Speichernutzung in der Datenbank.

## <a name="query-distributed-data"></a>Abfragen verteilter Daten

- [Abfragen vertikal partitionierter Daten](sql-database-elastic-query-getting-started-vertical.md) in mehreren Datenbanken.
- [Erstellen übergreifender Berichte für die horizontal hochskalierte Datenschicht](sql-database-elastic-query-horizontal-partitioning.md).
- [Durchführen tabellenübergreifender Abfragen mit unterschiedlichen Schemas](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Aufträge für die elastische Datenbank

- [Erstellen und Verwalten](elastic-jobs-powershell.md) von Aufträgen für die elastische Datenbank mithilfe von PowerShell.
- [Erstellen und Verwalten](elastic-jobs-tsql.md) von Aufträgen für die elastische Datenbank mithilfe von Transact-SQL.
- [Migrieren aus alten elastischen Aufträgen](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Datenbank-Sharding

- [Upgraden der Clientbibliothek für elastische Datenbanken](sql-database-elastic-scale-upgrade-client-library.md).
- [Erstellen einer App mit Sharding](sql-database-elastic-scale-get-started.md).
- [Abfragen horizontal partitionierter Daten](sql-database-elastic-query-getting-started.md).
- Ausführen von [Multishardabfragen](sql-database-elastic-scale-multishard-querying.md).
- [Verschieben von Sharddaten](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Konfigurieren der Sicherheit](sql-database-elastic-scale-split-merge-security-configuration.md) in Datenbank-Shards.
- [Hinzufügen eines Shards](sql-database-elastic-scale-add-a-shard.md) zur aktuellen Gruppe von Datenbank-Shards.
- [Beheben von Problemen bei der Shardzuordnung](sql-database-elastic-database-recovery-manager.md).
- [Migrieren von Sharddatenbanken](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Erstellen von Leistungsindikatoren](sql-database-elastic-database-perf-counters.md).
- [Verwenden von Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) zum Abfragen partitionierter Daten.
- [Verwenden des Dapper-Frameworks](sql-database-elastic-scale-working-with-dapper.md) zum Abfragen partitionierter Daten.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [Anleitungen zur verwalteten Instanz](sql-database-howto-managed-instance.md).
