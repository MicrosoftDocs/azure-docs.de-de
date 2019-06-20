---
title: Azure SQL-Datenbank – Versionshinweise | Microsoft-Dokumentation
description: Weitere Informationen zu den neuen Features und Verbesserungen in Azure SQL-Datenbank und in der zugehörigen Dokumentation
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: d527c4fed9c43e62d815078c049d4d8e6f8a46b7
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787711"
---
# <a name="sql-database-release-notes"></a>Versionshinweise zu SQL-Datenbank

In diesem Artikel erhalten Sie weitere Informationen zu den neuen Features und Verbesserungen in Azure SQL-Datenbank und in der zugehörigen Dokumentation. Die Verbesserungen an SQL-Datenbank finden Sie außerdem unter [Azure-Updates](https://azure.microsoft.com/updates/?product=sql-database). Informationen zu Verbesserungen bei anderen Azure-Diensten finden Sie unter [Dienstupdates](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Features in der öffentlichen Vorschau

| Feature | Details |
| ---| --- |
| Beschleunigte Datenbankwiederherstellung bei Singletons und Pools für elastische Datenbanken | Weitere Informationen finden Sie unter [Schnellere Datenbankwiederherstellung](sql-database-accelerated-database-recovery.md).|
|Geschätzte eindeutige Anzahl|Weitere Informationen finden Sie unter [Geschätzte Abfrageverarbeitung](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Batchmodus für Rowstore (unter Kompatibilitätsgrad 150)|Weitere Informationen finden Sie unter [Batchmodus bei Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Datenermittlung und -klassifizierung  |Weitere Informationen finden Sie unter [Azure SQL-Datenbank und SQL Data Warehouse: Datenermittlung und -klassifizierung](sql-database-data-discovery-and-classification.md).|
| Aufträge für die elastische Datenbank | Weitere Informationen finden Sie unter [Erstellen, Konfigurieren und Verwalten von Aufträgen für die elastische Datenbank](elastic-jobs-overview.md). |
| Elastische Abfragen | Weitere Informationen finden Sie unter [Übersicht über elastische Abfragen](sql-database-elastic-query-overview.md). |
| Elastische Transaktionen | [Verteilte Transaktionen über Clouddatenbanken](sql-database-elastic-transactions-overview.md). |
| Instanzensortierung bei verwalteten Instanzen |Weitere Informationen finden Sie unter [Verwenden von PowerShell mit einer Azure Resource Manager-Vorlage zum Erstellen einer verwalteten Instanz in Azure SQL-Datenbank](./scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md).|
|Feedback zur Speicherzuweisung (Zeilenmodus) (unter Kompatibilitätsgrad 150)|Weitere Informationen finden Sie unter [Feedback zur Speicherzuweisung im Zeilenmodus](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Abfrage-Editor im Azure-Portal |Weitere Informationen finden Sie unter [Verwenden des SQL-Abfrage-Editors im Azure-Portal zum Verbinden und Abfragen von Daten](sql-database-connect-query-portal.md).|
| R-Dienste/maschinelles Lernen mit Singletons und Pools für elastische Datenbanken |Weitere Informationen finden Sie unter [Machine Learning Services in Azure SQL Database (Machine Learning Services in Azure SQL-Datenbank)](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
| Erneutes Erstellen gelöschter Datenbanken mit verwalteten Instanzen |Weitere Informationen finden Sie unter [Re-create dropped databases in Azure SQL Managed Instance (Erneutes Erstellen gelöschter Datenbanken in verwalteten Azure SQL-Datenbank-Instanzen)](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| Replikation mit verwalteten Instanzen |Weitere Informationen finden Sie unter [Konfigurieren der Replikation in einer verwalteten Azure SQL-Datenbank-Instanzdatenbank](replication-with-sql-database-managed-instance.md).|
| Serverlose Computeebene | Weitere Informationen finden Sie unter [SQL-Datenbank – serverlos (Vorschau)](sql-database-serverless.md).|
|Azure SQL-Analyse|Weitere Informationen finden Sie unter [Azure SQL-Analyse](../azure-monitor/insights/azure-sql.md).|
|Verzögerte Kompilierung von Tabellenvariablen (unter Kompatibilitätsgrad 150)|Weitere Informationen finden Sie unter [Verzögerte Kompilierung von Tabellenvariablen](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| Bedrohungserkennung bei verwalteten Instanzen |Weitere Informationen finden Sie unter [Konfigurieren der Bedrohungserkennung (Vorschau) für eine verwaltete Azure SQL-Datenbank-Instanz](sql-database-managed-instance-threat-detection.md).|
| Unterstützung von Zeitzonen für verwaltete Instanzen|Weitere Informationen finden Sie unter [Zeitzone in einer verwalteten Azure SQL-Datenbank-Instanz (Vorschauversion)](sql-database-managed-instance-timezone.md).|
| Transparent Data Encryption (TDE) mit Bring Your Own Key-Szenarien (BYOK) bei verwalteten Instanzen |Weitere Informationen finden Sie unter [Azure SQL Transparent Data Encryption mithilfe von Schlüsseln, die vom Kunden in Azure Key Vault verwaltet werden: Bring Your Own Key-Unterstützung](transparent-data-encryption-byok-azure-sql.md).|
| &nbsp; |

## <a name="may-2019"></a>Mai 2019

### <a name="service-improvements"></a>Verbesserungen beim Dienst

| Verbesserungen beim Dienst | Details |
| --- | --- |
|Die Dienstebene „Hyperscale“ ist jetzt allgemein verfügbar.| Weitere Informationen finden Sie unter [Dienstebene „Hyperscale“ für bis zu 100TB](sql-database-service-tier-hyperscale.md) und [Skalieren Sie Ihre Azure-Datenbankworkloads durch Hyperscale mit Höchstleistung](https://azure.microsoft.com/blog/get-high-performance-scaling-for-your-azure-database-workloads-with-hyperscale/).|
|Die serverlose Computeebene befindet sich jetzt in der öffentlichen Vorschauphase. | Weitere Informationen finden Sie unter [SQL-Datenbank – serverlos (Vorschau)](sql-database-serverless.md).|
| Die Transaktionsprotokollraten und die Ziel-IOPS für Datenbanken wurden mithilfe des vCore-basierten Kaufmodells erhöht.| Weitere Informationen finden Sie unter [Limits des vCore-basierten Kaufmodells für eine Einzeldatenbank in Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases) und [Ressourcenlimits für Einzeldatenbanken, die das DTU-basierte Kaufmodell verwenden](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases).
| &nbsp; |

### <a name="documentation-improvements"></a>Verbesserungen bei der Dokumentation

| Verbesserungen bei der Dokumentation | Details |
| --- | --- |
| Die Dokumentation für die Dienstebene „Hyperscale“ wurde aktualisiert, damit sie dem Stand der allgemeinen Verfügbarkeit entspricht.| Weitere Informationen finden Sie unter [Dienstebene „Hyperscale“ (Vorschau) für bis zu 100TB](sql-database-service-tier-hyperscale.md).|
|Im Rahmen der öffentlichen Vorschauphase der serverlosen Computeebene wurde die entsprechende Dokumentation veröffentlicht.| Weitere Informationen finden Sie unter [SQL-Datenbank – serverlos (Vorschau)](sql-database-serverless.md).|
| &nbsp; |

## <a name="april-2019"></a>April 2019

### <a name="service-improvements"></a>Verbesserungen beim Dienst

| Verbesserungen beim Dienst | Details |
| --- | --- |
| Öffentliche Endpunkte für eine verwaltete Azure SQL-Datenbank-Instanz befinden sich jetzt in der öffentlichen Vorschauphase.| Weitere Informationen finden Sie unter [Sicheres Verwenden einer verwalteten Azure SQL-Datenbank-Instanz mit öffentlichen Endpunkten](sql-database-managed-instance-public-endpoint-securely.md).|
| Die Zeitzonenunterstützung für eine verwaltete Azure SQL-Datenbank-Instanz befindet sich jetzt in der öffentlichen Vorschauphase.| Weitere Informationen finden Sie unter [Zeitzone in einer verwalteten Azure SQL-Datenbank-Instanz (Vorschauversion)](sql-database-managed-instance-timezone.md).|
| Der Schutz von Azure SQL-Datenbank mithilfe von verwalteten Identitäten befindet sich jetzt in der zweiten öffentlichen Vorschauphase.| Weitere Informationen finden Sie unter [Securing Azure SQL Databases with managed identities just got easier (Noch einfacherer Schutz von Azure SQL-Datenbanken mithilfe von verwalteten Identitäten)](https://azure.microsoft.com/blog/securing-azure-sql-databases-with-managed-identities-just-got-easier/).|
| &nbsp; |

### <a name="documentation-improvements"></a>Verbesserungen bei der Dokumentation

| Verbesserungen bei der Dokumentation | Details |
| --- | --- |
| Öffentliche Endpunkte für eine verwaltete Azure SQL-Datenbank-Instanz befinden sich jetzt in der öffentlichen Vorschauphase.| Weitere Informationen finden Sie unter [Sicheres Verwenden einer verwalteten Azure SQL-Datenbank-Instanz mit öffentlichen Endpunkten](sql-database-managed-instance-public-endpoint-securely.md).|
| Die Zeitzonenunterstützung für eine verwaltete Azure SQL-Datenbank-Instanz befindet sich jetzt in der öffentlichen Vorschauphase.| Weitere Informationen finden Sie unter [Zeitzone in einer verwalteten Azure SQL-Datenbank-Instanz (Vorschauversion)](sql-database-managed-instance-timezone.md). |
| Ressourcengovernance in Azure SQL-Datenbank | Weitere Informationen finden Sie unter [Resource governance in Azure SQL Database (Ressourcengovernance in Azure SQL-Datenbank)](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). || &nbsp; |

## <a name="march-2019"></a>März 2019

### <a name="service-improvements"></a>Verbesserungen beim Dienst

| Verbesserungen beim Dienst | Details |
| --- | --- |
| Unterstützung für die horizontale Leseskalierung in Azure SQL-Datenbank ist nun allgemein verfügbar. | Weitere Informationen finden Sie unter [Verwenden von schreibgeschützten Replikaten für den Lastenausgleich schreibgeschützter Abfrageworkloads](sql-database-read-scale-out.md).|
| &nbsp; |

### <a name="documentation-improvements"></a>Verbesserungen bei der Dokumentation

| Verbesserungen bei der Dokumentation | Details |
| --- | --- |
| Hinzugefügt: Protokollbeschränkungen für Singletons|Weitere Informationen finden Sie unter [Limits des auf virtuellen Kernen basierenden Kaufmodells für eine Einzeldatenbank in Azure SQL-Datenbank](sql-database-vcore-resource-limits-single-databases.md).|
| Hinzugefügt: Protokollbeschränkungen für Pools für elastische Datenbanken und Pooldatenbanken|Weitere Informationen finden Sie unter [Ressourcenlimits für Pools für elastische Datenbanken, die das V-Kern-basierte Kaufmodell verwenden](sql-database-vcore-resource-limits-elastic-pools.md).|
| Hinzugefügt: Transaktionsprotokollratengovernance| Neuer Inhalt für die [Transaktionsprotokollratengovernance](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)|
| Aktualisiert: PowerShell-Beispiele für Singletons und Pools für elastische Datenbanken für die Verwendung des az.sql-Moduls | Weitere Informationen finden Sie in den [PowerShell-Beispielen für Singletons und Pools für elastische Datenbanken](sql-database-powershell-samples.md#single-database-and-elastic-pools).|
| &nbsp; |

## <a name="february-2019"></a>Februar 2019

### <a name="service-improvements"></a>Verbesserungen beim Dienst

| Verbesserungen beim Dienst | Details |
| --- | --- |
|Allgemein verfügbar: Erstellen eines fortsetzbaren Onlineindizes| Weitere Informationen finden Sie unter [CREATE INDEX (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql).|
|Verbessert: Unterstützung für verwaltete Instanzen für Routingtabellen| Weitere Informationen finden Sie unter [Netzwerkanforderungen](sql-database-managed-instance-connectivity-architecture.md#network-requirements).|
|Hinzugefügt: Unterstützung für das Umbenennen von Datenbank in verwalteten Instanzen | Weitere Informationen finden Sie in der Syntax zu [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) und [sp_rename](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql).|
|SQL-Datenbank als Quelle von Verweisdaten für Stream Analytics | Weitere Informationen finden Sie unter [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) und [Azure Stream Analytics now supports Azure SQL Database as reference data input (Azure Stream Analytics unterstützt nun Azure SQL-Datenbank als Eingabequelle von Verweisdaten)](https://azure.microsoft.com/blog/azure-stream-analytics-now-supports-azure-sql-database-as-reference-data-input/).|
|Hinzugefügt: Unterstützung für verwaltete Instanzen im Datenmigrations-Assistenten |Weitere Informationen finden Sie unter [What's new in DMA (Neuerungen im Datenmigrations-Assistenten)](https://docs.microsoft.com/sql/dma/dma-whatsnew).|
|Hinzugefügt: Unterstützung für Zielbereitschaftsüberprüfung für verwaltete Instanzen in SQL Server Migration Assistant | Weitere Informationen finden Sie unter [SQL Server Migration Assistant](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant).
|Hinzugefügt: Unterstützung der Migration von Amazon RDS zu verwalteten Instanzen im Datenmigrationsdienst | Weitere Informationen finden Sie unter [Tutorial: Migrieren von RDS SQL Server zu Azure SQL-Datenbank oder zu einer verwalteten Azure SQL-Datenbank-Instanz mithilfe von DMS (online)](../dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md).|
| &nbsp; |

### <a name="documentation-improvements"></a>Verbesserungen bei der Dokumentation

| Verbesserungen bei der Dokumentation | Details |
| --- | --- |
|Hinzugefügt: Erläuterungen zu den Bereitstellungsoptionen für verwaltete Instanzen|Viele Artikel wurden aktualisiert, um die Anwendbarkeit der Bereitstellungsoptionen auf Singletons, Pools für elastische Datenbanken und verwaltete Instanzen näher zu erläutern. |
|Aktualisiert: tempdb-Größen für das DTU-basierte Kaufmodell | Weitere Informationen finden Sie unter [Tempdb-Datenbank in SQL-Datenbank](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
|Aktualisiert: Unterstützung für das Importieren und Exportieren mit BACPAC-Datei für verwaltete Instanzen| Weitere Informationen finden Sie unter [Importieren einer BACPAC-Datei](sql-database-import.md) und [Exportieren einer Azure SQL-Datenbank in eine BACPAC-Datei](sql-database-export.md). |
| &nbsp; |

## <a name="january-2019"></a>Januar 2019

### <a name="service-improvements"></a>Verbesserungen beim Dienst

| Verbesserungen beim Dienst | Details |
| --- | --- |
| Zusätzliche Optionen für die Granularität von Computeressourcen | Die Computeoptionen für die Dienstebenen „Universell“ und „Unternehmenskritisch“ für [Singletons](sql-database-vcore-resource-limits-single-databases.md) und [Pools für elastische Datenbanken](sql-database-vcore-resource-limits-elastic-pools.md) präziser konfiguriert werden.|
| Anzeige von Überwachungsdatensätzen für verwaltete Instanzen im Azure-Portal | Die Anzeige von [Überwachungsdatensätzen für verwaltete Instanzen](sql-database-managed-instance-auditing.md) im Azure-Portal wird nun unterstützt.|
| Das Feature „Advanced Thread Detection“ wurde in „Advanced Data Security“ umbenannt. | Das Feature „Advanced Thread Detection“ wurde für Singletons, Pools für elastische Datenbanken und verwaltete Instanzen in [Advanced Data Security](sql-advanced-threat-protection.md) umbenannt. |
| &nbsp; |

### <a name="documentation-improvements"></a>Verbesserungen bei der Dokumentation

| Verbesserungen bei der Dokumentation | Details |
| --- | --- |
| Verwaltete Instanzen und Transaktionsreplikation | Ein Artikel über die Verwendung der [Transaktionsreplikation mit verwalteten Azure SQL-Datenbank-Instanzen](replication-with-sql-database-managed-instance.md) wurde hinzugefügt. |
| Hinzugefügt: Tutorial zu Azure AD mit verwalteten Instanzen | In diesem Tutorial zu [Azure AD mit verwalteten Instanzen](sql-database-managed-instance-aad-security-tutorial.md) wird veranschaulicht, wie Sie die Sicherheit von verwalteten Instanzen mithilfe von Azure AD-Anmeldungen konfigurieren und testen können. |
| Aktualisiert: Auftragsautomatisierung mit Transact-SQL-Skripts | Die Erläuterungen zur Verwendung der [Auftragsautomatisierung mithilfe von Transact-SQL-Skripts](sql-database-job-automation-overview.md) für Singletons, Pools für elastische Datenbanken und verwaltete Instanzen wurden aktualisiert und verbessert. |
| Aktualisiert: Sicherheit für verwaltete Instanzen | Die Erläuterungen zum [Sicherheitsmodell für verwaltete Instanzen](sql-database-security-overview.md) wurden aktualisiert und verbessert, und es wurden Vergleiche mit den Sicherheitsmodellen für Singletons und Pools für elastische Datenbanken hinzugefügt. |
| Aktualisiert: sämtliche Schnellstartanleitungen und Tutorials | Alle Schnellstarts und Tutorials in der [Dokumentation](https://docs.microsoft.com/azure/sql-database) wurden aktualisiert, damit sie den Änderungen im Azure-Portal entsprechen. |
| Hinzugefügt: Schnellstartanleitungen | Schnellstartanleitungen für [Singletons](sql-database-quickstart-guide.md) und [verwaltete Instanzen](sql-database-managed-instance-quickstart-guide.md) wurden hinzugefügt. |
| Hinzugefügt: SQL-Datenbank-Glossar | In diesem [Glossar](sql-database-glossary-terms.md) finden Sie eine Liste der festgelegten Begriffe für SQL-Datenbank und Links zu den Seiten, auf denen die Begriffe im entsprechenden Kontext erläutert werden. |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>Mitwirken an der Verbesserung des Inhalts

Bei der Dokumentation zu Azure SQL-Datenbank handelt es sich um eine Open Source-Dokumentation. Dadurch entstehen mehrere Vorteile:

- Durch die öffentliche Planung in Open Source-Repositorys erhalten wir Feedback zu benötigten Dokumentationen.
- Durch die öffentliche Prüfung in Open Source-Repositorys können wir mit dem ersten Release den bestmöglichen Inhalt veröffentlichen.
- Durch öffentliche Updates in Open Source-Repositorys können wir Inhalt kontinuierlich verbessern.

Weitere Informationen zum Mitwirken an der Dokumentation zu Azure SQL-Datenbank finden Sie unter [Leitfaden für Mitwirkende an der Microsoft-Dokumentation: Übersicht](https://docs.microsoft.com/contribute/). Die Benutzeroberfläche auf [docs.microsoft.com](https://docs.microsoft.com/) bezieht [GitHub-Workflows](https://github.com/) direkt ein, um den Prozess noch einfacher zu gestalten. Beginnen Sie mit dem [Bearbeiten des angezeigten Dokuments](https://docs.microsoft.com/contribute/#quick-edits-to-existing-documents). Alternativ können Sie [neue Artikel prüfen](https://docs.microsoft.com/contribute/#review-open-prs) oder [nützliche Tickets erstellen](https://docs.microsoft.com/contribute/#create-quality-issues).
