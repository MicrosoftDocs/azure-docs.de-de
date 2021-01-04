---
title: 'Inhaltsreferenz: Konfigurieren und Verwalten'
titleSuffix: Azure SQL Managed Instance
description: Eine Referenzanleitung für Inhalte, in denen das Konfigurieren und Verwalten von Azure SQL Managed Instance erläutert wird.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 04/16/2019
ms.openlocfilehash: b7f2f060f32cf3bf92660a2e8b75a45b7ec76b82
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779763"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Azure SQL Managed Instance: Inhaltsreferenz
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dieser Artikel enthält eine Inhaltsreferenz für verschiedene Anleitungen, Skripts und Erläuterungen, die Sie beim Verwalten und Konfigurieren von Azure SQL Managed Instance unterstützen.

## <a name="load-data"></a>Laden der Daten

- [Migrieren zu Azure SQL Managed Instance](migrate-to-instance-from-sql-server.md): Hier finden Sie Informationen zum empfohlenen Migrationsprozess und zu den Tools für die Migration zu Azure SQL Managed Instance.
- [Migrieren des TDE-Zertifikats zu Azure SQL Managed Instance](tde-certificate-migrate.md): Wenn Ihre SQL Server-Datenbank mit transparenter Datenverschlüsselung (TDE) geschützt ist, müssen Sie ein Zertifikat migrieren, das SQL Managed Instance zum Entschlüsseln der Sicherung verwenden kann, die Sie in Azure wiederherstellen möchten.
- [Importieren einer Datenbank über eine BACPAC-Datei](../database/database-import.md)
- [Exportieren einer Datenbank in eine BACPAC-Datei](../database/database-export.md)
- [Laden von Daten mit BCP](../load-from-csv-with-bcp.md)
- [Laden von Daten mit Azure Data Factory](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>Netzwerkkonfiguration

- [Bestimmen der Subnetzgröße](vnet-subnet-determine-size.md): Da die Größe des Subnetzes nach der Bereitstellung von SQL Managed Instance nicht geändert werden kann, müssen Sie berechnen, welcher IP-Adressbereich für die Anzahl und Typen von verwalteten Instanzen erforderlich ist, die im Subnetz bereitgestellt werden sollen. 
- [Erstellen eines neuen VNETs und Subnetzes](virtual-network-subnet-create-arm-template.md): Konfigurieren Sie das virtuelle Netzwerk und das Subnetz gemäß den [Netzwerkanforderungen](connectivity-architecture-overview.md#network-requirements). 
- [Konfigurieren eines vorhandenen VNets und Subnetzes](vnet-existing-add-subnet.md): Überprüfen Sie die Netzwerkanforderungen, und konfigurieren Sie das vorhandene virtuelle Netzwerk und Subnetz für die Bereitstellung von SQL Managed Instance. 
- [Konfigurieren von benutzerdefiniertem DNS](custom-dns-configure.md): Konfigurieren Sie benutzerdefiniertes DNS, um externen Ressourcen von SQL Managed Instance aus den Zugriff auf benutzerdefinierte Domänen über einen Verbindungsserver von DB-E-Mal-Profilen zu ermöglichen. 
- [Synchronisieren der Netzwerkkonfiguration](azure-app-sync-network-configuration.md): Aktualisieren Sie den Netzwerkkonfigurationsplan, wenn Sie nach der [Integration ihrer App in ein virtuelles Azure-Netzwerk](../../app-service/web-sites-integrate-with-vnet.md) keine Verbindung herstellen können.
- [Ermitteln der IP-Adresse des Verwaltungsendpunkts](management-endpoint-find-ip-address.md): Bestimmen Sie den öffentlichen Endpunkt, den SQL Managed Instance für Verwaltungszwecke verwendet. 
- [Überprüfen des integrierten Firewallschutzes](management-endpoint-verify-built-in-firewall.md): Vergewissern Sie sich, dass SQL Managed Instance Datenverkehr nur über die erforderlichen Ports und andere integrierte Firewallregeln zulässt. 
- [Herstellen einer Verbindung mit Anwendungen](connect-application-instance.md): Lernen Sie verschiedene Möglichkeiten zum Verbinden von Anwendungen mit SQL Managed Instance kennen.

## <a name="feature-configuration"></a>Featurekonfiguration

- [Konfigurieren der Azure AD-Authentifizierung](../database/authentication-aad-configure.md)
- [Konfigurieren des bedingten Zugriffs](../database/conditional-access-configure.md)
- [Mehrstufige Azure AD-Authentifizierung](../database/authentication-mfa-ssms-overview.md)
- [Konfigurieren der mehrstufigen Authentifizierung](../database/authentication-mfa-ssms-configure.md)
- [Konfigurieren einer temporalen Aufbewahrungsrichtlinie](../database/temporal-tables-retention-policy.md)
- [Konfigurieren von TDE mit BYOK](../database/transparent-data-encryption-byok-configure.md)
- [Rotieren von TDE-BYOK-Schlüsseln](../database/transparent-data-encryption-byok-key-rotation.md)
- [Entfernen einer TDE-Schutzvorrichtung](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [Konfigurieren von In-Memory OLTP](../in-memory-oltp-configure.md)
- [Konfigurieren von Azure Automation](../database/automation-manage.md)
- [Transaktionsreplikation](replication-between-two-instances-configure-tutorial.md): Dies ermöglicht Ihnen das Replizieren Ihrer Daten zwischen verwalteten Instanzen oder von einem lokalen SQL Server zu SQL Managed Instance und umgekehrt.
- [Konfigurieren der Bedrohungserkennung](threat-detection-configure.md): Die [Bedrohungserkennung](../database/threat-detection-overview.md) ist ein integriertes Feature von Azure SQL Managed Instance, mit dem verschiedene potenzielle Angriffe erkannt werden, z. B. die Einschleusung von SQL-Befehlen oder ein Zugriff von verdächtigen Standorten. 
- [Das Erstellen von Benachrichtigungen](alerts-create.md) ermöglicht es Ihnen, Benachrichtigungen zu überwachten Metriken wie CPU-Auslastung, Speicherplatzbelegung, IOPS u. a. für SQL Managed Instance einzurichten. 

## <a name="monitoring-and-tuning"></a>Überwachung und Optimierung

- [Manuelles Optimieren](../database/performance-guidance.md)
- [Überwachen der Leistung mithilfe von DMVs](../database/monitoring-with-dmvs.md)
- [Überwachen der Leistung mit dem Abfragespeicher](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Behandeln von Leistungsproblemen mithilfe von Intelligent Insights](../database/intelligent-insights-troubleshoot-performance.md)
- [Verwenden des Intelligent Insights-Diagnoseprotokolls](../database/intelligent-insights-use-diagnostics-log.md)
- [Überwachen des In-Memory-OLTP-Speicherplatzes](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Erweiterte Ereignisse

- [Erweiterte Ereignisse](../database/xevent-db-diff-from-svr.md)
- [Speichern erweiterter Ereignisse in einer Ereignisdatei](../database/xevent-code-event-file.md)
- [Speichern erweiterter Ereignisse in einem Ringpuffer](../database/xevent-code-ring-buffer.md)

### <a name="alerting"></a>Warnungen

- [Erstellen von Warnungen für eine verwaltete Instanz](alerts-create.md)

## <a name="operations"></a>Operationen (Operations)

- [Vom Benutzer initiiertes manuelles Failover für SQL Managed Instance](user-initiated-failover.md)

## <a name="develop-applications"></a>Entwickeln von Anwendungen

- [Konnektivität](../database/connect-query-content-reference-guide.md#libraries)
- [Verwenden des Spark-Connectors](../../cosmos-db/spark-connector.md)
- [Authentifizieren einer App](../database/application-authentication-get-client-id-keys.md)
- [Verwenden der Batchverarbeitung für bessere Leistung](../performance-improve-use-batching.md)
- [Konnektivitätsleitfaden](../database/troubleshoot-common-connectivity-issues.md)
- [DNS-Aliase](../database/dns-alias-overview.md)
- [Einrichten eines DNS-Alias mithilfe von PowerShell](../database/dns-alias-powershell-create.md)
- [Ports – ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C und C ++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>Entwerfen von Anwendungen

- [Entwurf für die Notfallwiederherstellung](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [Entwurf für Pools für elastische Datenbanken](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Entwurf für App-Upgrades](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Entwurf von SaaS-Anwendungen mit mehreren Mandanten

- [SaaS-Entwurfsmuster](../database/saas-tenancy-app-design-patterns.md)
- [SaaS-Video Indexer](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS-App-Sicherheit](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Nächste Schritte

Beginnen Sie mit der [Bereitstellung von SQL Managed Instance](instance-create-quickstart.md).