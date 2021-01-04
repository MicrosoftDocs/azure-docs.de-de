---
title: 'Migrationsübersicht: SQL Server zu SQL Managed Instance'
description: In diesem Artikel lernen Sie die verschiedenen Tools und Optionen kennen, die zum Migrieren Ihrer SQL Server-Datenbanken zu Azure SQL Managed Instance verfügbar sind.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 878d59445dde839ca9e702ac0c49af676e48a42f
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531189"
---
# <a name="migration-overview-sql-server-to-sql-managed-instance"></a>Migrationsübersicht: SQL Server zu SQL Managed Instance
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

In diesem Artikel werden verschiedene Migrationsoptionen und Aspekte für die Migration Ihrer SQL Server-Instanz zu Azure SQL Managed Instance beschrieben. 

Sie können SQL Server-Instanzen migrieren, die lokal oder auf folgenden Plattformen ausgeführt werden: 

- SQL Server auf virtuellen Computern  
- Amazon Web Services (AWS) EC2 
- Amazon Relational Database Service (AWS RDS) 
- Compute Engine (Google Cloud Platform – GCP)  
- Cloud SQL für SQL Server (Google Cloud Platform – GCP) 

Informationen zu anderen Szenarios finden Sie im [Leitfaden zur Datenbankmigration](https://datamigration.microsoft.com/). 

## <a name="overview"></a>Übersicht

[Azure SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md) ist eine empfohlene Zieloption für SQL Server-Workloads, die einen vollständig verwalteten Dienst erfordern, ohne dass Sie VMs (virtuelle Computer) oder ihre Betriebssysteme verwalten müssen. SQL Managed Instance ermöglicht die Migration per Lift & Shift Ihrer lokalen Anwendungen zu Azure mit minimalen Anwendungs- oder Datenbankänderungen, während die vollständige Isolation Ihrer Instanzen mit nativer Unterstützung von virtuellen Netzwerken gewährleistet wird. 

## <a name="considerations"></a>Überlegungen 

Die wichtigsten Faktoren, die beim Auswerten von Migrationsoptionen berücksichtigt werden sollten, hängen von Folgendem ab: 
- Anzahl der Server und Datenbanken
- Größe der Datenbanken
- Akzeptable Downtime für das Geschäft während des Migrationsprozesses 

Einer der Hauptvorteile der Migration Ihrer SQL Server-Instanzen zu SQL Managed Instance besteht darin, dass Sie die gesamte Instanz oder nur eine Teilmenge einzelner Datenbanken migrieren können. Planen Sie sorgfältig, Folgendes in den Migrationsprozess miteinzubeziehen: 
- Alle Datenbanken, die in derselben Instanz zusammengestellt werden müssen 
- Objekte auf Instanzebene, die für Ihre Anwendung erforderlich sind, einschließlich Anmeldungen, Anmeldeinformationen, SQL-Agent-Aufträge und -Operatoren sowie Trigger auf Serverebene. 

> [!NOTE]
> Azure SQL Managed Instance garantiert eine Verfügbarkeit von 99,99 %, und zwar auch in kritischen Szenarios, sodass der durch einige Features in Azure SQL Managed Instance verursachte Mehraufwand nicht verhindert werden kann. Weitere Informationen finden Sie im Blogbeitrag über die [Hauptursachen, die unterschiedliche Leistungen in SQL Server und Azure SQL Managed Instance verursachen können](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/). 


## <a name="choose-appropriate-target"></a>Auswählen des richtigen Ziels

Im Folgenden finden Sie einige allgemeine Richtlinien, die Ihnen bei der Auswahl der richtigen Dienstebene und Merkmale von SQL Managed Instance helfen, die Ihrer [Leistungsbaseline](sql-server-to-managed-instance-performance-baseline.md) entsprechen: 

- Verwenden Sie die Baseline für die CPU-Auslastung, um eine verwaltete Instanz bereitzustellen, die der Anzahl der Kerne entspricht, die Ihre Instanz von SQL Server verwendet. Es kann erforderlich sein, dass Ressourcen entsprechend der [Merkmale der Hardwaregeneration](../../managed-instance/resource-limits.md#hardware-generation-characteristics) skaliert werden müssen. 
- Verwenden Sie die Baseline für die Arbeitsspeicherauslastung, um eine [Option für die virtuellen Kerne](../../managed-instance/resource-limits.md#service-tier-characteristics) auszuwählen, die Ihrer Arbeitsspeicherbelegung entspricht. 
- Verwenden Sie die Baseline für die E/A-Latenz des Dateisubsystems, um zwischen den Dienstebenen „Universell“ (Latenz höher als 5 ms) und „Unternehmenskritisch“ (Latenz geringer als 3 ms) auszuwählen. 
- Verwenden Sie die Baseline für den Durchsatz, um die Größe der Daten und Protokolldateien im Voraus zuzuweisen, um die erwartete E/A-Leistung zu erzielen. 

Während der Bereitstellung können Sie Compute- und Speicherressourcen auswählen und anschließend mithilfe des [Azure-Portals](../../database/scale-resources.md) ändern, ohne dass es zu Downtime Ihrer Anwendung kommt. 

> [!IMPORTANT]
> Jede Abweichung in den [Anforderungen an virtuelle Netzwerke für verwaltete Instanzen](../../managed-instance/connectivity-architecture-overview.md#network-requirements) kann Sie am Erstellen neuer Instanzen oder Verwenden vorhandener Instanzen hindern. Erfahren Sie mehr über das  [Erstellen neuer](../../managed-instance/virtual-network-subnet-create-arm-template.md) und [Konfigurieren vorhandener](../../managed-instance/vnet-existing-add-subnet.md?branch=release-ignite-arc-data) Netzwerke. 

### <a name="sql-server-vm-alternative"></a>Alternative für SQL Server-VMs

Ihr Unternehmen weist möglicherweise Anforderungen auf, durch die SQL Server auf Azure-VMs zu einem besseren Ziel als Azure SQL Managed Instance wird. 

Wenn Folgendes für Ihr Unternehmen gilt, sollten Sie stattdessen eine Migration zu einer SQL Server-VM in Betracht ziehen: 

- Wenn Sie direkten Zugriff auf das Betriebssystem oder das Dateisystem benötigen, z. B. um Drittanbieter- oder benutzerdefinierte Agents auf demselben virtuellen SQL Server-Computer zu installieren. 
- Wenn Sie unbedingt von Features abhängig sind, die noch nicht unterstützt werden, wie z. B. FileStream/FileTable, PolyBase und instanzübergreifende Transaktionen. 
- Wenn Sie unbedingt bei einer bestimmten Version von SQL Server (z. B. 2012) bleiben müssen. 
- Wenn Ihre Computeanforderungen wesentlich niedriger sind als bei einer verwalteten Instanz (z. B. ein virtueller Kern) und eine Datenbankkonsolidierung nicht in Frage kommt. 


## <a name="migration-tools"></a>Migrationstools


Für die Migration werden die Tools „Datenmigrations-Assistent“ (DMA) und „Azure Database Migration Service“ empfohlen. Es sind auch andere alternative Migrationsoptionen verfügbar. 

### <a name="recommended-tools"></a>Empfohlene Tools

In der folgenden Tabelle werden die empfohlenen Migrationstools aufgeführt: 

|Technologie | BESCHREIBUNG|
|---------|---------|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md)  | Hierbei handelt es sich um einen Erstanbieterdienst von Azure, der die Migration im Offlinemodus für Anwendungen unterstützt, die sich Downtime während des Migrationsprozesses leisten können. Im Gegensatz zur kontinuierlichen Migration im Onlinemodus führt die Migration im Offlinemodus eine einmalige Wiederherstellung einer vollständigen Datenbanksicherung von der Quelle zum Ziel aus. | 
|[Native Sicherung und Wiederherstellung](../../managed-instance/restore-sample-database-quickstart.md) | SQL Managed Instance unterstützt die Wiederherstellung nativer SQL Server-Datenbanksicherungen (BAK-Dateien), weshalb es sich hierbei um die einfachste Migrationsoption für Kunden handelt, die vollständige Datenbanksicherungen in Azure Storage bereitstellen können. Vollständige und differenzielle Sicherungen werden ebenfalls unterstützt und später in diesem Artikel im Abschnitt [Migrationsressourcen](#migration-assets) dokumentiert.| 
| | |

### <a name="alternative-tools"></a>Alternative Tools

In der folgenden Tabelle werden alternative Migrationstools aufgeführt: 

|Technologie |BESCHREIBUNG  |
|---------|---------|
|[Transaktionsreplikation](../../managed-instance/replication-transactional-overview.md) | Mit diesem Tool können Daten aus Datenbanktabellen der SQL Server-Quelle in SQL Managed Instance repliziert werden, indem eine Migrationsoption vom Typ „Verleger/Abonnent“ angegeben und die Transaktionskonsistenz beibehalten wird. |  |
|[Massenkopieren](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| Mit dem [Hilfsprogramm zum Massenkopieren](/sql/tools/bcp-utility) werden Daten aus einer SQL Server-Instanz in eine Datendatei kopiert. Verwenden Sie das Hilfsprogramm zum Massenkopieren, um die Daten aus Ihrer Quelle zu exportieren und die Datendatei in das SQL Managed Instance-Ziel zu importieren.</br></br> Für Massenkopiervorgänge mit hoher Geschwindigkeit zum Verschieben von Daten in Azure SQL-Datenbank kann dass [intelligente Tool zum Massenkopieren](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) verwendet werden, um die Übertragungsgeschwindigkeit mithilfe paralleler Kopieraufgaben zu maximieren. | 
|[Assistent zum Importieren/Exportieren von BACPAC-Dateien](../../database/database-import.md?tabs=azure-powershell)| Eine [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac)-Datei ist eine Windows-Datei mit der Erweiterung `.bacpac`, die das Schema und die Daten einer Datenbank kapselt. BACPAC-Dateien können zum Exportieren von Daten aus einer SQL Server-Quelle und zum Importieren der Datei in Azure SQL Managed Instance verwendet werden.  |  
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| Die [Kopieraktivität](../../../data-factory/copy-activity-overview.md) in Azure Data Factory migriert Daten aus SQL Server-Datenbank-Quellinstanzen in SQL Managed Instance mithilfe integrierter Connectors und einer [Integration Runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> ADF unterstützt ein breites Spektrum von [Connectors](../../../data-factory/connector-overview.md), um Daten von SQL Server-Quellen in SQL Managed Instance zu verschieben. |
| | |

## <a name="compare-migration-options"></a>Vergleich der Migrationsoptionen

Im Folgenden können Sie die Migrationsoptionen vergleichen, um die richtige Vorgehensweise für Ihre Geschäftsanforderungen zu wählen. 

### <a name="recommended-options"></a>Empfohlene Optionen

In der folgenden Tabelle werden die empfohlenen Migrationsoptionen verglichen: 

|Migrationsoption  |Verwendung  |Überlegungen  |
|---------|---------|---------|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md) | – Migrieren einzelner oder mehrerer Datenbanken nach Maß </br> – Während des Migrationsprozesses ist Downtime möglich </br> </br> Unterstützte Quellen: </br> – SQL Server (2005 – 2019) lokal oder Azure-VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP Compute mit SQL Server-VM |  – Migrationen im großen Stil können mit [PowerShell](../../../dms/howto-sql-server-to-azure-sql-mi-powershell.md) automatisiert werden. </br> – Die Zeit bis zum Abschluss der Migration ist von der Datenbankgröße abhängig und wird von der Sicherungs- und Wiederherstellungszeit beeinflusst. </br> – Möglicherweise ist eine gewisse Downtime erforderlich. |
|[Native Sicherung und Wiederherstellung](../../managed-instance/restore-sample-database-quickstart.md) | – Migrieren individueller Branchenanwendungsdatenbanken  </br> – Schnelle und einfache Migration ohne separaten Migrationsdienst oder ein Tool  </br> </br> Unterstützte Quellen: </br> – SQL Server (2005 – 2019) lokal oder Azure-VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP Compute mit SQL Server-VM | – Die Datenbanksicherung verwendet mehrere Threads zum Optimieren der Datenübertragung an Azure Blob Storage, jedoch kann sich die ISV-Bandbreite und -Datenbankgröße auf die Übertragungsrate auswirken. </br> – Die Downtime sollte den Zeitaufwand zum Durchführen einer vollständigen Sicherung und Wiederherstellung berücksichtigen (dieser Vorgang ist von der Datengröße abhängig).| 
| | | |

### <a name="alternative-options"></a>Alternative Optionen

In der folgenden Tabelle werden die alternativen Migrationsoptionen verglichen: 

|Methode/Technologie |Verwendung |Überlegungen  |
|---------|---------|---------|
|[Transaktionsreplikation](../../managed-instance/replication-transactional-overview.md) | – Migrieren durch kontinuierliches Veröffentlichen von Änderungen an Quelldatenbanktabellen an die Zieldatenbanktabellen von SQL Managed Instance </br> – Vollständige oder teilweise Datenbankmigrationen ausgewählter Tabellen (Teilmenge der Datenbank)  </br> </br> Unterstützte Quellen: </br> – SQL Server (2012 – 2019) mit einigen Einschränkungen </br> – AWS EC2  </br> – GCP Compute mit SQL Server-VM | </br> – Das Setup ist im Vergleich zu anderen Migrationsoptionen relativ komplex.   </br> – Bietet eine kontinuierliche Replikationsoption zum Migrieren von Daten (ohne die Datenbanken offline zu schalten)</br> – Es gibt einige Einschränkungen bei der Transaktionsreplikation, die beim Einrichten des Verlegers für die SQL Server-Quellinstanz berücksichtigt werden müssen. Weitere Informationen finden Sie unter [Einschränkungen für das Veröffentlichen von Objekten](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects).  </br> – Eine Funktion zum [Überwachen der Replikationsaktivität](/sql/relational-databases/replication/monitor/monitoring-replication) ist verfügbar.    |
|[Massenkopieren](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| – Vollständige oder teilweise Datenmigrationen </br> – Downtime ist möglich </br> </br> Unterstützte Quellen: </br> – SQL Server (2005 – 2019) lokal oder Azure-VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP Compute mit SQL Server-VM   | – Erfordert Downtime für das Exportieren von Daten aus der Quelle und das Importieren in das Ziel </br> – Die für Exports und Imports verwendeten Dateiformate und Datentypen müssen mit den Tabellenschemas übereinstimmen. |
|[Assistent zum Importieren/Exportieren von BACPAC-Dateien](../../database/database-import.md)| – Migrieren individueller Branchenanwendungsdatenbanken </br>– Für kleinere Datenbanken geeignet  </br>  – Erfordert keinen separaten Migrationsdienst oder ein Tool </br> </br> Unterstützte Quellen: </br> – SQL Server (2005 – 2019) lokal oder Azure-VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP Compute mit SQL Server-VM  |   </br> – Erfordert Downtime, da Daten aus der Quelle exportiert und im Ziel importiert werden müssen   </br> – Die für den Export/Import verwendeten Dateiformate und Datentypen müssen mit den Tabellenschemas übereinstimmen, um Kürzungen oder Fehler durch nicht übereinstimmende Datentypen zu vermeiden. </br> – Die Zeit zum Exportieren einer Datenbank mit einer großen Anzahl von Objekten kann deutlich höher sein. |
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| – Migrieren und/oder Transformieren von Daten aus SQL Server-Datenbank-Quellinstanzen</br> – Zusammenführen von Daten aus mehreren Datenquellen in Azure SQL Managed Instance (wird in der Regel für Business Intelligence-Workloads verwendet)   </br> – Erfordert das Erstellen von Datenverschiebungspipelines in ADF zum Verschieben von Daten aus der Quelle zum Ziel   </br> - Die [Kosten](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) sind ein wichtiger Aspekt, der auf den Pipelinetriggern, Aktivitätsausführungen, die Dauer der Datenverschiebung und mehr basiert. |
| | | |

## <a name="feature-interoperability"></a>Featureinteroperabilität 

Beim Migrieren von Workloads, die andere SQL Server-Features nutzen, gibt es weitere Aspekte, die berücksichtigt werden müssen. 

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services

Migrieren Sie SSIS-Pakete (SQL Server Integration Services) und -Projekte in SSISDB mithilfe von [Azure Database Migration Service (DMS)](../../../dms/how-to-migrate-ssis-packages-managed-instance.md) zu Azure SQL Managed Instance. 

Nur SSIS-Pakete in SSISDB ab SQL Server 2012 werden für die Migration unterstützt. Konvertieren Sie ältere SSIS-Pakete vor der Migration. Weitere Informationen finden Sie unter [Tutorial für die Konvertierung von Projekten](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model). 


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

SSRS-Berichte (SQL Server Reporting Services) können zu paginierten Berichten in Power BI migriert werden. Verwenden Sie das  [RDL-Migrationstool](https://github.com/microsoft/RdlMigration), um die Vorbereitung und Migration Ihrer Berichte zu unterstützen. Dieses Tool wurde von Microsoft entwickelt, um Kunden bei der Migration von RDL-Berichten von ihren SSRS-Servern zu Power BI zu unterstützen. Es ist auf GitHub verfügbar. Dort finden Sie auch eine dokumentierte, vollständige exemplarische Vorgehensweise für das Migrationsszenario. 

#### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

Tabellarische SQL Server Analysis Services-Modelle von SQL Server 2012 und höher können zu Azure Analysis Services migriert werden. Dabei handelt es sich um ein PaaS-Bereitstellungsmodell für das tabellarische Analysis Services-Modell in Azure. Weitere Informationen über die Migration lokaler Modelle zu Azure Analysis Services finden Sie in diesem [Videotutorial](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/).

Alternativ können Sie auch die Migration Ihrer lokalen tabellarischen Analysis Services-Modelle zu [Power BI Premium mithilfe der neuen XMLA-Endpunkte für Lese- und Schreibvorgänge](/power-bi/admin/service-premium-connect-tools) in Betracht ziehen. 
> [!NOTE]
> Die Power BI-Funktionalität für XMLA-Endpunkte für Lese- und Schreibvorgänge befindet sich derzeit in der öffentlichen Vorschauphase und sollte nicht für Produktionsworkloads verwendet werden, bis die Funktionalität in die allgemeine Verfügbarkeit übergeht.

#### <a name="high-availability"></a>Hochverfügbarkeit

Die Hochverfügbarkeitsfeatures für Always On-Failoverclusterinstanzen und Always On-Verfügbarkeitsgruppen von SQL Server sind für das Azure SQL Managed Instance-Ziel überflüssig, da die Hochverfügbarkeitsarchitektur bereits in die Dienstebenen [Universell (Standardverfügbarkeitsmodell)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) und [Unternehmenskritisch (Premium-Verfügbarkeitsmodell)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) von SQL Managed Instance integriert ist. Das Premium-Verfügbarkeitsmodell stellt auch die horizontale Leseskalierung bereit, die das Herstellen einer Verbindung mit einem der sekundären Knoten für schreibgeschützte Zwecke ermöglicht.     

Neben der in SQL Managed Instance enthaltenen Hochverfügbarkeitsarchitektur gibt es auch das Feature für [Autofailover-Gruppen](../../database/auto-failover-group-overview.md), mit dem Sie die Replikation und das Failover von Datenbanken in einer verwalteten Instanz auf eine andere Region verwalten können. 

#### <a name="sql-agent-jobs"></a>SQL-Agent-Aufträge

Verwenden Sie die Azure Database Migration Service-Offlineoption zum Migrieren von [SQL-Agent-Aufträgen](../../../dms/howto-sql-server-to-azure-sql-mi-powershell.md#offline-migrations). Andernfalls müssen Sie die Aufträge mithilfe von SSMS (SQL Server Management Studio) in Transact-SQL (T-SQL) schreiben und dann im SQL Managed Instance-Ziel manuell neu erstellen. 

> [!IMPORTANT]
> Derzeit unterstützt Azure Database Migration Service nur Aufträge mit T-SQL-Subsystemschritten. Aufträge mit SSIS-Paketschritten müssen manuell migriert werden. 

#### <a name="logins-and-groups"></a>Anmeldungen und Gruppen

SQL-Anmeldungen der SQL Server-Quelle können mithilfe von Azure Database Migration Service im Offlinemodus zu Azure SQL Managed Instance migriert werden.  Verwenden Sie das Blatt **[Anmeldungen auswählen](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins)** im **Migrations-Assistent**, um Anmeldungen in Ihr SQL Managed Instance-Ziel zu migrieren. 

Standardmäßig unterstützt Azure Database Migration Service nur das Migrieren von SQL-Anmeldungen. Sie können allerdings die Möglichkeit zum Migrieren von Windows-Anmeldungen wie folgt aktivieren:

Stellen Sie sicher, dass die verwaltete SQL-Zielinstanz über Azure AD-Lesezugriff verfügt. Dieser kann über das Azure-Portal von einem Benutzer mit der Rolle **Unternehmensadministrator** oder **Globaler Administrator** konfiguriert werden.
Konfigurieren Sie die Azure Database Migration Service-Instanz, um die Migration von Windows-Anmeldungen von Benutzern/Gruppen zu aktivieren. Diese Konfiguration erfolgt über das Azure-Portal auf der Seite „Konfiguration“. Nachdem Sie diese Einstellung aktiviert haben, starten Sie den Dienst neu, damit die Änderungen übernommen werden.

Nach dem Neustart des Diensts werden die Windows-Anmeldungen der Benutzer/Gruppen in der Liste der für die Migration verfügbaren Anmeldungen angezeigt. Für alle Windows-Anmeldungen von Benutzern/Gruppen, die Sie migrieren, werden Sie aufgefordert, den zugeordneten Domänennamen anzugeben. Dienstbenutzerkonten (Konten mit dem Domänennamen „NT AUTHORITY“) und virtuelle Benutzerkonten (Konten mit dem Domänennamen „NT SERVICE“) werden nicht unterstützt.

Weitere Informationen finden Sie unter [Migrieren von Windows-Benutzern und -Gruppen in einer SQL Server-Instanz zu Azure SQL Managed Instance mithilfe von T-SQL](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

Alternativ können Sie das [PowerShell-Hilfstool](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) verwenden, das speziell von den Datenmigrationsarchitekten von Microsoft entworfen wurde. Das Hilfsprogramm verwendet PowerShell, um ein T-SQL-Skript zum Neuerstellen von Anmeldungen und ausgewählten Datenbankbenutzern aus der Quelle im Ziel zu erstellen. Das Tool weist automatisch Windows Server AD-Konten zu Azure AD-Konten zu und kann eine UPN-Suche für jede Anmeldung in der Active Directory-Quellinstanz durchführen. Das Tool erstellt benutzerdefinierte Server- und Datenbankrollen sowie Rollenmitgliedschaften, Datenbankrollen und Benutzerberechtigungen. Eigenständige Datenbanken werden derzeit nicht unterstützt und nur eine Teilmenge der möglichen SQL Server-Berechtigung werden erstellt. 

#### <a name="encryption"></a>Verschlüsselung

Beim Migrieren von Datenbanken, die mit  [Transparent Data Encryption](../../database/transparent-data-encryption-tde-overview.md) geschützt werden, zu einer verwalteten Instanz mithilfe der nativen Wiederherstellungsoption [müssen Sie das entsprechende Zertifikat aus der SQL Server-Quelle zum SQL Managed Instance-Ziel migrieren](../../managed-instance/tde-certificate-migrate.md), *bevor* die Datenbankwiederherstellung durchgeführt wird. 

#### <a name="system-databases"></a>Systemdatenbanken

Das Wiederherstellen von Systemdatenbanken wird nicht unterstützt. Zum Migrieren von Objekten auf Instanzebene (die in der Master- oder msdb-Datenbank gespeichert werden) müssen Sie diese mit T-SQL schreiben und dann in der verwalteten Zielinstanz neu erstellen. 

## <a name="leverage-advanced-features"></a>Nutzen der erweiterten Features 

Stellen Sie sicher, dass Sie die Vorteile der erweiterten cloudbasierten Features nutzen, die von SQL Managed Instance angeboten werden. Beispielsweise müssen Sie sich nicht mehr um die Verwaltung der Sicherungen sorgen, da der Dienst sich darum kümmert. Sie können [einen beliebigen Zeitpunkt im Aufbewahrungszeitraum](../../database/recovery-using-backups.md#point-in-time-restore) wiederherstellen. Darüber hinaus müssen Sie sich keine Gedanken über die Einrichtung von  [Hochverfügbarkeit](../../database/high-availability-sla.md) machen, da diese integriert ist. 

Sie sollten die [Azure Active Directory-Authentifizierung](../../database/authentication-aad-overview.md), [Überwachung](../../managed-instance/auditing-configure.md), [Bedrohungserkennung](../../database/azure-defender-for-sql.md), [Sicherheit auf Zeilenebene](/sql/relational-databases/security/row-level-security) und [dynamische Datenmaskierung](/sql/relational-databases/security/dynamic-data-masking) verwenden, um die Sicherheit zu erhöhen.

Zusätzlich zu erweiterten Verwaltungs- und Sicherheitsfeatures stellt SQL Managed Instance eine Reihe von erweiterten Tools bereit, die Sie beim [Überwachen und Optimieren Ihrer Workload](../../database/monitor-tune-overview.md) unterstützen. Mit der [Azure SQL-Analyse](../../../azure-monitor/insights/azure-sql.md) können Sie eine große Gruppe verwalteter Instanzen zentral überwachen. Die [automatische Optimierung](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) in verwalteten Instanzen überwacht kontinuierlich die Leistung der Ausführungsstatistiken Ihres SQL-Tarifs und behebt identifizierte Leistungsprobleme automatisch. 

Einige Features sind nur verfügbar, wenn der [Datenbank-Kompatibilitätsgrad](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) in den neuesten Kompatibilitätsgrad (150) geändert wurde. 

## <a name="migration-assets"></a>Migrationsressourcen 

Weitere Unterstützung finden Sie in den folgenden Ressourcen, die für reale Migrationsprojekte entwickelt wurden.

|Asset  |BESCHREIBUNG  |
|---------|---------|
|[Data Workload Assessment Model and Tool (Datenarbeitsauslastungs-Bewertungsmodell und -tool)](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Dieses Tool stellt für eine angegebene Arbeitsauslastung Informationen zu empfohlenen optimalen Zielplattformen, zur Cloudbereitschaft und zum Korrekturbedarf für Anwendungen/Datenbanken bereit. Es bietet eine einfache Berechnung und Berichterstellung mit nur einem Klick, die Ihnen durch Bereitstellung eines automatisierten und einheitlichen Zielplattform-Entscheidungsprozesses dabei helfen, umfangreiche Bewertungen zu beschleunigen.|
|[DBLoader-Hilfsprogramm](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|DBLoader kann zum Laden von Daten aus durch Trennzeichen getrennten Textdateien in SQL Server verwendet werden. Dieses Windows-Konsolenhilfsprogramm verwendet die native Clientbenutzeroberfläche für Massenladevorgänge von SQL Server, die für alle Versionen von SQL Server funktioniert (einschließlich Azure SQL Managed Instance).|
|[Hilfsprogramm zum Verschieben lokaler SQL Server-Anmeldungen zu Azure SQL Managed Instance](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Hierbei handelt es sich um ein PowerShell-Skript, das ein T-SQL-Befehlsskript erstellt, um Anmeldungen und ausgewählte Datenbankbenutzer aus der lokalen SQL Server-Instanz in Azure SQL Managed Instance neu zu erstellen. Das Tool ermöglicht die automatische Zuordnung von Windows Server AD-Konten zu Azure AD-Konten sowie die optionale Migration von nativen SQL Server-Anmeldungen.|
|[Automatisierung der Perfmon-Datensammlung mithilfe von Logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Hierbei handelt es sich um ein Tool, das Perfmon-Daten erfasst, um die Baselineleistung zu verstehen, die die Empfehlung für das Migrationsziel unterstützt. Dieses Tool verwendet die ausführbare Datei „logman.exe“, um den Befehl zu erstellen, der die Leistungsindikatoren für eine SQL Server-Remoteinstanz erstellt, startet, beendet und löscht.|
|[Whitepaper: Datenbankmigration zu Azure SQL Managed Instance durch Wiederherstellung vollständiger und differenzieller Sicherungen](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|In diesem Whitepaper finden Sie Anleitungen und Schritte zum Beschleunigen von Migrationen von SQL Server zu Azure SQL Managed Instance, wenn Sie nur über vollständige und differenzielle Sicherungen (und keine Protokollsicherungsfunktion) verfügen.|

Diese Ressourcen wurden im Rahmen des Data SQL Ninja-Programms entwickelt, das vom Azure Data Group-Entwicklungsteam gesponsert wird. Der Hauptzweck des Data SQL Ninja-Programms besteht darin, Hindernisse für komplexe Modernisierung zu beseitigen und Letztere zu beschleunigen sowie den Wettbewerb in Bezug auf Möglichkeiten zur Migration von Datenplattformen zur Azure-Datenplattform von Microsoft zu fördern. Wenn Sie denken, dass Ihre Organisation an einer Teilnahme am Data SQL Ninja-Programm interessiert wäre, wenden Sie sich an Ihr Kundenteam, und bitten Sie es, eine Nominierung einzureichen.


## <a name="next-steps"></a>Nächste Schritte

Informationen zum Beginn der Migration Ihrer SQL Server-Instanz zu Azure SQL Managed Instance finden Sie im [Leitfaden für die Migration von SQL Server zu SQL Managed Instance](sql-server-to-managed-instance-guide.md).

- Eine Übersicht über die Dienste und Tools von Microsoft und Drittanbietern, mit denen Sie verschiedene Datenbank- und Datenmigrationsszenarios und Spezialaufgaben ausführen können, finden Sie unter [Dienste und Tools für die Datenmigration](../../../dms/dms-tools-matrix.md).

- Weitere Informationen zu Azure SQL Managed Instance finden Sie unter:
   - [Dienstebenen für Azure SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Unterschiede zwischen SQL Server und Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure-Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) 


- Weitere Informationen zum Framework und zum Einführungszyklus von Cloudmigrationen finden Sie unter:
   -  [Cloud Adoption Framework für Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Bewährte Methoden für die Kostenermittlung und Größenanpassung von zu Azure migrierten Workloads](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Informationen zum Bewerten der Anwendungszugriffsebene finden Sie unter [Data Access Migration Toolkit (Vorschauversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Ausführliche Informationen zum Ausführen von A/B-Tests auf Datenzugriffsebene finden Sie unter [Assistent für Datenbankexperimente](/sql/dea/database-experimentation-assistant-overview).