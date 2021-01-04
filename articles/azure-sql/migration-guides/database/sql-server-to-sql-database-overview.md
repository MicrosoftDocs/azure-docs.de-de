---
title: 'SQL Server zu SQL-Datenbank: Übersicht über die Migration'
description: In diesem Artikel lernen Sie die verschiedenen Tools und Optionen kennen, die zum Migrieren Ihrer SQL Server-Datenbanken zu Azure SQL-Datenbank verfügbar sind.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 46216fe06e3d3425d5b237cdbb7326eed596945a
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326929"
---
# <a name="migration-overview-sql-server-to-sql-database"></a>Migrationsübersicht: SQL Server zu SQL-Datenbank
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

In diesem Artikel werden verschiedene Migrationsoptionen und -aspekte für die Migration Ihrer SQL Server-Instanz zu Azure SQL-Datenbank beschrieben. 

Sie können SQL Server-Instanzen migrieren, die lokal oder auf folgenden Plattformen ausgeführt werden: 

- SQL Server auf virtuellen Computern  
- Amazon Web Services (AWS) EC2 
- Amazon Relational Database Service (AWS RDS) 
- Compute Engine (Google Cloud Platform – GCP)  
- Cloud SQL für SQL Server (Google Cloud Platform – GCP) 

Informationen zu anderen Szenarios finden Sie im [Leitfaden zur Datenbankmigration](https://datamigration.microsoft.com/). 

## <a name="overview"></a>Übersicht

[Azure SQL-Datenbank](../../database/sql-database-paas-overview.md) ist eine empfohlene Zieloption für SQL Server-Arbeitsauslastungen, die eine vollständig verwaltete Platform-as-a-Service-Lösung (PaaS) erfordern. SQL-Datenbank bietet die meisten Funktionen für die Datenbankverwaltung sowie Hochverfügbarkeit, intelligente Abfrageverarbeitung, Skalierbarkeit und Leistungsfunktionen, die sich für viele verschiedene Anwendungstypen eignen. 

In SQL-Datenbank sind zudem mehrere [Bereitstellungsmodelle](../../database/sql-database-paas-overview.md#deployment-models) und [Dienstebenen](../../database/service-tiers-vcore.md#service-tiers) verfügbar, die für Flexibilität sorgen und für unterschiedliche Typen von Anwendungen oder Arbeitsauslastungen geeignet sind.

Einer der Hauptvorteile einer Migration zu SQL-Datenbank besteht darin, dass Sie Ihre Anwendung mithilfe der PaaS-Funktionen modernisieren und jegliche Abhängigkeiten von technischen Komponenten eliminieren können, die auf Instanzebene bestehen, z. B. SQL-Agent-Aufträge.

Sie können auch Kosten sparen, indem Sie Ihre lokalen SQL Server-Lizenzen mithilfe des [Azure-Hybridvorteils](https://azure.microsoft.com/pricing/hybrid-benefit/) für SQL Server zu Azure SQL-Datenbank migrieren, wenn Sie sich für das [vCore-basierte Kaufmodell](../../database/service-tiers-vcore.md) entscheiden.

In diesem Leitfaden sollen Migrationsoptionen und -aspekte erläutert werden, die für die Vorbereitung der Migration Ihrer SQL Server-Datenbanken zu Azure SQL-Datenbank relevant sind.  

## <a name="considerations"></a>Überlegungen 

Die wichtigsten Faktoren, die beim Auswerten von Migrationsoptionen berücksichtigt werden sollten, hängen von Folgendem ab: 

- Anzahl der Server und Datenbanken
- Größe der Datenbanken
- Akzeptable Downtime für das Geschäft während des Migrationsprozesses 

Die in diesem Leitfaden aufgeführten Migrationsoptionen berücksichtigen diese Faktoren. Bei der logischen Datenmigration zu Azure SQL-Datenbank kann die Migrationsdauer sowohl von der Anzahl der Objekte in einer Datenbank als auch von der Größe der Datenbank abhängen. 

Für verschiedene Arbeitsauslastungen und Benutzereinstellungen stehen verschiedene Tools zur Verfügung. Mit einigen UI-basierten Tools können Singletons schnell migriert werden, während andere Tools die Automatisierung unterstützen und Datenbanken im großen Stil migrieren können. 

## <a name="choose-appropriate-target"></a>Auswählen des richtigen Ziels

Bei der Auswahl des richtigen Bereitstellungsmodells und der richtigen Dienstebene für Azure SQL-Datenbank sollten Sie einige allgemeine Richtlinien berücksichtigen. Während der Bereitstellung können Sie Compute- und Speicherressourcen auswählen und anschließend über das [Azure-Portal](../../database/scale-resources.md) ändern, ohne dass es zu Downtime Ihrer Anwendung kommt.


**Bereitstellungsmodelle:** Sie sollten die Workload und das Nutzungsmuster Ihrer Anwendung verstehen, um die Wahl zwischen einem Singleton oder einem Pool für elastische Datenbanken zu treffen 

- Bei einem [Singleton](../../database/single-database-overview.md) handelt es sich um eine vollständig verwaltete Datenbank, die für die meisten modernen Cloudanwendungen und Microservices geeignet ist.
- Ein [Pool für elastische Datenbanken](../../database/elastic-pool-overview.md) besteht aus einer Gruppe von Singletons, die sich bestimmte Ressourcen wie die CPU oder den Arbeitsspeicher teilen. Dieses Modell eignet sich bei vorhersagbaren Nutzungsmustern, bei denen die Pooldatenbanken effektiv die gleichen Ressourcen verwenden können.

**Kaufmodelle:** Sie haben die Wahl zwischen dem vCore-basierten, dem DTU-basierten oder dem serverlosen Kaufmodell. 

- Mit dem [vCore-basierten Modell](../../database/service-tiers-vcore.md) können Sie die Anzahl von virtuellen Kernen für Ihre Instanz von Azure SQL-Datenbank selbst festlegen. Dies ist bei der Migration von einer lokalen SQL Server-Instanz die einfachste Option. Es ist außerdem die einzige Option, die Einsparungen bei den Lizenzkosten mit dem [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) unterstützt. 
- Beim [DTU-basierten Modell](../../database/service-tiers-dtu.md) werden die zugrunde liegenden Compute-, Arbeitsspeicher- und Eingabe/Ausgaberessourcen für eine gemischte DTU abstrahiert. 
- Das [serverlose Modell](../../database/serverless-tier-overview.md) ist für Arbeitsauslastungen vorgesehen, die eine automatische bedarfsgesteuerte Skalierung mit Computeressourcen erfordern, die pro Nutzungssekunde abgerechnet werden. Im Tarif „Serverloses Computing“ werden Datenbanken während inaktiver Phasen (in denen nur Speicher in Rechnung gestellt wird) automatisch angehalten und bei Fortsetzung der Aktivität automatisch wieder in Betrieb genommen. 

**Dienstebenen:** Sie haben die Wahl zwischen drei Dienstebenen, die für verschiedene Anwendungstypen entwickelt wurden.

- Die [Dienstebene „Universell“/„Standard“](../../database/service-tier-general-purpose.md) ist eine ausgeglichene budgetorientierte Tarifoption mit Compute- und Speicherkapazitäten, die für die Bereitstellung von Anwendungen der mittleren unteren Ebene geeignet ist und Redundanz auf Speicherebene für die Wiederherstellung nach Fehlern bereitstellt. Sie eignet sich für die meisten Datenbankworkloads. 
- Die [Dienstebene „Unternehmenskritisch“/„Premium“](../../database/service-tier-business-critical.md) ist für Anwendungen der obersten Ebene vorgesehen, die hohe Transaktionsraten, E/A-Vorgänge mit niedriger Latenz und ein hohes Maß an Resilienz erfordern, wobei sekundäre Replikate für Failover und das Auslagern von Leseworkloads verfügbar sind.
- Die [Dienstebene „Hyperscale“](../../database/service-tier-hyperscale.md) wurde für Datenbanken mit wachsenden Datenvolumen konzipiert, die automatisch auf bis zu 100 TB skaliert werden müssen. Diese Option eignet sich für sehr große Datenbanken. 

> [!IMPORTANT]
> [Die Transaktionsprotokollrate wird in Azure SQL-Datenbank geregelt](../../database/resource-limits-logical-server.md#transaction-log-rate-governance), um hohe Erfassungsraten zu drosseln. Daher kann es während der Migration notwendig sein, Zieldatenbankressourcen (vCores/DTUs) zu skalieren, um die Last auf die CPU oder den Durchsatz zu verringern. Wählen Sie die Zieldatenbank mit der passenden Größe aus, aber planen Sie ein, die Ressourcen für die Migration bei Bedarf zu skalieren. 


### <a name="sql-server-on-azure-vm-alternative"></a>Alternative: SQL Server auf Azure-VMs

Ihr Unternehmen weist möglicherweise Anforderungen auf, die [Azure Virtual Machines zu einem besseren Ziel für SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) als Azure SQL-Datenbank machen. 

Wenn Folgendes für Ihr Unternehmen gilt, sollten Sie stattdessen eine Migration zu einer SQL Server-VM in Azure in Betracht ziehen: 

- Sie benötigen direkten Zugriff auf das Betriebs- oder Dateisystem, z. B. um Drittanbieter- oder benutzerdefinierte Agents auf der gleichen VM wie SQL Server zu installieren. 
- Wenn Sie unbedingt von Features abhängig sind, die noch nicht unterstützt werden, wie z. B. FileStream/FileTable, PolyBase und instanzübergreifende Transaktionen. 
- Wenn Sie unbedingt bei einer bestimmten Version von SQL Server (z. B. 2012) bleiben müssen. 
- Wenn Ihre Computeanforderungen wesentlich niedriger sind als bei einer verwalteten Instanz (z. B. ein virtueller Kern) und eine Datenbankkonsolidierung nicht in Frage kommt. 


## <a name="migration-tools"></a>Migrationstools 

Für die Migration werden die Tools „Datenmigrations-Assistent“ (DMA) und „Azure Database Migration Service“ empfohlen. Es sind auch andere alternative Migrationsoptionen verfügbar. 

### <a name="recommended-tools"></a>Empfohlene Tools

In der folgenden Tabelle werden die empfohlenen Migrationstools aufgeführt: 

|Technologie | BESCHREIBUNG|
|---------|---------|
|[Datenmigrations-Assistent (DMA)](/sql/dma/dma-migrateonpremsqltosqldb)|Der Datenmigrations-Assistent ist ein Desktoptool, das nahtlose SQL Server-Bewertungen und Migrationen zu Azure SQL-Datenbank (sowohl Schema als auch Daten) bereitstellt. Das Tool kann lokal auf einem Server oder auf Ihrem lokalen Computer installiert werden, der mit ihren Quelldatenbanken verbunden ist. Bei der Migration handelt es sich um eine logische Datenverschiebung zwischen Objekten in der Quell- und der Zieldatenbank. </br> – Migrieren von Singletons (Schema und Daten)|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)|Ein Erstanbieter-Azure-Dienst, mit dem Ihre SQL Server-Datenbanken über das Azure-Portal oder mit PowerShell zu Azure SQL-Datenbank migriert werden können. In Azure DMS müssen Sie bei der Bereitstellung ein bevorzugtes virtuelles Azure-Netzwerk (VNET) auswählen, um eine Verbindung mit Ihren SQL Server-Quelldatenbanken zu gewährleisten. </br> – Migrieren von Singletons oder im großen Stil. |
| | |


### <a name="alternative-tools"></a>Alternative Tools

In der folgenden Tabelle werden alternative Migrationstools aufgeführt: 

|Technologie |BESCHREIBUNG  |
|---------|---------|
|[Transaktionsreplikation](../../database/replication-to-sql-database.md)|Mit diesem Tool können Daten aus Tabellen der SQL Server-Quelldatenbank in SQL-Datenbank repliziert werden, indem eine Migrationsoption vom Typ „Verleger/Abonnent“ angegeben und die Transaktionskonsistenz beibehalten wird. Inkrementelle Datenänderungen werden an Abonnenten weitergegeben, sobald sie auf den Verlegern auftreten.|
|[Import/Export-Dienst/BACPAC-Datei:](../../database/database-import.md)|Eine [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac)-Datei ist eine Windows-Datei mit der Erweiterung `.bacpac`, die das Schema und die Daten einer Datenbank kapselt. Mit einer BACPAC-Datei können Daten sowohl aus einer SQL Server-Quelldatenbank exportiert als auch in Azure SQL-Datenbank importiert werden. Die BACPAC-Datei kann über das Azure-Portal in eine neue Azure SQL-Datenbank-Instanz importiert werden. </br></br> Für Skalierbarkeit und Leistung in großen Datenbanken oder bei einer großen Anzahl von Datenbanken sollten Sie das Befehlszeilen-Hilfsprogramm [SqlPackage](../../database/database-import.md#using-sqlpackage) in Betracht ziehen, um Datenbanken zu exportieren und zu importieren.|
|[Massenkopieren](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|Mit dem [Hilfsprogramm zum Massenkopieren](/sql/tools/bcp-utility) werden Daten aus einer SQL Server-Instanz in eine Datendatei kopiert. Verwenden Sie das Hilfsprogramm zum Massenkopieren, um die Daten aus Ihrer Quelle zu exportieren und die Datendatei in die Zielinstanz von SQL-Datenbank zu importieren. </br></br> Für Massenkopiervorgänge mit hoher Geschwindigkeit zum Verschieben von Daten zu Azure SQL-Datenbank kann dass [intelligente Tool zum Massenkopieren](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) verwendet werden, um die Übertragungsgeschwindigkeit mithilfe paralleler Kopiertasks zu maximieren.|
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-database.md)|Die [Kopieraktivität](../../../data-factory/copy-activity-overview.md) in Azure Data Factory migriert Daten aus SQL Server-Quelldatenbanken mithilfe integrierter Connectors und einer [Integration Runtime](../../../data-factory/concepts-integration-runtime.md) zu SQL-Datenbank.</br> </br> ADF unterstützt ein breites Spektrum von [Connectors](../../../data-factory/connector-overview.md), um Daten aus SQL Server-Quellen in SQL-Datenbank zu verschieben.|
|[SQL-Datensynchronisierung](../../database/sql-data-sync-data-sql-server-sql-database.md)|SQL-Datensynchronisierung ist ein Dienst, der auf Azure SQL-Datenbank basiert und mit dem Sie die ausgewählten Daten bidirektional über mehrere Datenbanken hinweg synchronisieren können, sowohl lokal als auch in der Cloud.</br>Die Datensynchronisierung ist nützlich, wenn Daten über mehrere Datenbanken in Azure SQL-Datenbank oder SQL Server hinweg auf dem neuesten Stand gehalten werden müssen.|
| | |

## <a name="compare-migration-options"></a>Vergleich der Migrationsoptionen

Im Folgenden können Sie die Migrationsoptionen vergleichen, um die richtige Vorgehensweise für Ihre Geschäftsanforderungen zu wählen. 

### <a name="recommended-options"></a>Empfohlene Optionen

In der folgenden Tabelle werden die empfohlenen Migrationsoptionen verglichen: 

|Migrationsoption  |Verwendung  |Überlegungen  |
|---------|---------|---------|
|[Datenmigrations-Assistent (DMA)](/sql/dma/dma-migrateonpremsqltosqldb) | – Migrieren von Singletons (Schema und Daten)  </br> – Während der Datenmigration ist Downtime möglich. </br> </br> Unterstützte Quellen: </br> – SQL Server (2005 – 2019) lokal oder Azure-VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP Compute mit SQL Server-VM | – Bei der Migration werden Daten zwischen Datenbankobjekten (von der Quelle zum Ziel) verschoben. Daher wird empfohlen, diese außerhalb der Spitzenzeiten auszuführen. </br> – DMA meldet den Status der Migration pro Datenbankobjekt einschließlich der Anzahl der migrierten Zeilen.  </br> – Bei großen Migrationen (Anzahl der Datenbanken/Größe der Datenbank) sollten Sie den unten aufgeführten Dienst „Azure Database Migration Service“ verwenden.|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)| – Migrieren von Singletons oder im großen Stil. </br> – Während des Migrationsprozesses ist Downtime möglich. </br> </br> Unterstützte Quellen: </br> – SQL Server (2005 – 2019) lokal oder Azure-VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP Compute mit SQL Server-VM | – Migrationen im großen Stil können mit [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md) automatisiert werden. </br> – Die Zeit bis zum Abschluss der Migration hängt von der Datenbankgröße und der Anzahl der Datenbankobjekte ab. </br> – Die Quelldatenbank muss schreibgeschützt sein. |
| | | |

### <a name="alternative-options"></a>Alternative Optionen

In der folgenden Tabelle werden die alternativen Migrationsoptionen verglichen: 

|Methode/Technologie |Verwendung    |Überlegungen  |
|---------|---------|---------|
|[Transaktionsreplikation](../../database/replication-to-sql-database.md)| – Migrieren durch kontinuierliches Veröffentlichen von Änderungen der Quelldatenbanktabellen an die Zieltabellen in SQL-Datenbank </br> – Vollständige oder teilweise Datenbankmigrationen ausgewählter Tabellen (Teilmenge der Datenbank)  </br> </br> Unterstützte Quellen: </br> - [SQL Server (2016–2019) mit einigen Einschränkungen](/sql/relational-databases/replication/replication-backward-compatibility) </br> – AWS EC2  </br> – GCP Compute mit SQL Server-VM  | – Das Setup ist im Vergleich zu anderen Migrationsoptionen relativ komplex.   </br> – Bietet eine kontinuierliche Replikationsoption zum Migrieren von Daten (ohne die Datenbanken offline zu schalten)  </br> – Es gibt einige Einschränkungen bei der Transaktionsreplikation, die beim Einrichten des Verlegers für die SQL Server-Quellinstanz berücksichtigt werden müssen. Weitere Informationen finden Sie unter [Einschränkungen für das Veröffentlichen von Objekten](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects). </br>– Es ist möglich, [die Replikationsaktivität zu überwachen](/sql/relational-databases/replication/monitor/monitoring-replication).    |
|[Import/Export-Dienst/BACPAC-Datei:](../../database/database-import.md)| – Migrieren individueller Branchenanwendungsdatenbanken </br>– Für kleinere Datenbanken geeignet  </br>  – Erfordert keinen separaten Migrationsdienst oder ein Tool </br> </br> Unterstützte Quellen: </br> – SQL Server (2005 – 2019) lokal oder Azure-VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP Compute mit SQL Server-VM  |  – Erfordert Downtime, da Daten aus der Quelle exportiert und im Ziel importiert werden müssen   </br> – Die für den Export/Import verwendeten Dateiformate und Datentypen müssen mit den Tabellenschemas übereinstimmen, um Kürzungen oder Fehler durch nicht übereinstimmende Datentypen zu vermeiden.  </br> – Die Zeit zum Exportieren einer Datenbank mit einer großen Anzahl von Objekten kann deutlich höher sein.       |
|[Massenkopieren](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| – Vollständige oder teilweise Datenmigrationen </br> – Downtime ist möglich </br> </br> Unterstützte Quellen: </br> – SQL Server (2005 – 2019) lokal oder Azure-VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP Compute mit SQL Server-VM   | – Erfordert Downtime für das Exportieren von Daten aus der Quelle und das Importieren in das Ziel </br> – Die für den Export/Import verwendeten Dateiformate und Datentypen müssen mit den Tabellenschemas übereinstimmen. |
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-database.md)| – Migrieren und/oder Transformieren von Daten aus SQL Server-Quelldatenbanken </br> – Zusammenführen von Daten aus mehreren Datenquellen in Azure SQL-Datenbank (wird in der Regel für Business Intelligence-Workloads verwendet)  |  – Erfordert das Erstellen von Datenverschiebungspipelines in ADF zum Verschieben von Daten aus der Quelle zum Ziel   </br> - Die [Kosten](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) sind ein wichtiger Aspekt, der auf den Pipelinetriggern, den Aktivitätsausführungen, der Dauer der Datenverschiebung und mehr basiert. |
|[SQL-Datensynchronisierung](../../database/sql-data-sync-data-sql-server-sql-database.md)| – Synchronisieren von Daten zwischen Quell- und Zieldatenbanken</br> – Eignet sich für eine fortlaufende Synchronisierung zwischen Azure SQL-Datenbank und der lokalen SQL Server-Instanz in einem bidirektionalen Flow | – Die Instanz von Azure SQL-Datenbank muss als Hub-Datenbank für die Synchronisierung mit der lokalen SQL Server-Datenbank (Mitgliedsdatenbank) konfiguriert werden.</br> – Im Gegensatz zur Transaktionsreplikation unterstützt die SQL-Datensynchronisierung die bidirektionale Datensynchronisierung zwischen der lokalen Datenbank und der Instanz von Azure SQL-Datenbank. </br> – Kann je nach Arbeitsauslastung höhere Auswirkungen auf die Leistung haben|
| | | |

## <a name="feature-interoperability"></a>Featureinteroperabilität 

Beim Migrieren von Workloads, die andere SQL Server-Features nutzen, gibt es weitere Aspekte, die berücksichtigt werden müssen.

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services
Migrieren Sie SQL Server Integration Services-Pakete (SSIS) zu Azure, indem Sie die Pakete neu in der Azure-SSIS-Runtime in [Azure Data Factory](../../../data-factory/introduction.md) bereitstellen. Azure Data Factory [unterstützt die Migration von SSIS-Paketen](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) durch eine Runtime, in der SSIS-Pakete in Azure ausgeführt werden können. Alternativ können Sie die SSIS-ETL-Logik auch nativ in ADF mithilfe von [Dataflows](../../../data-factory/concepts-data-flow-overview.md) neu schreiben.


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
SQL Server Reporting Services-Berichte (SSRS) können zu paginierten Berichten in Power BI migriert werden. Verwenden Sie das  [RDL-Migrationstool](https://github.com/microsoft/RdlMigration), um die Vorbereitung und Migration Ihrer Berichte zu unterstützen. Dieses Tool wurde von Microsoft entwickelt, um Kunden bei der Migration von RDL-Berichten von ihren SSRS-Servern zu Power BI zu unterstützen. Es ist auf GitHub verfügbar. Dort finden Sie auch eine dokumentierte, vollständige exemplarische Vorgehensweise für das Migrationsszenario. 

#### <a name="high-availability"></a>Hochverfügbarkeit
Das manuelle Setup der Hochverfügbarkeitsfeatures von SQL Server wie Always On-Failoverclusterinstanzen und Always On-Verfügbarkeitsgruppen sind für die Zielinstanz von Azure SQL-Datenbank überflüssig, da die Hochverfügbarkeitsarchitektur bereits in die Dienstebenen [Universell (Standardverfügbarkeitsmodell)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) und [Unternehmenskritisch (Premium-Verfügbarkeitsmodell)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) von SQL-Datenbank integriert ist. Die Dienstebene „Unternehmenskritisch“/„Premium“ stellt auch die horizontale Leseskalierung bereit, die das Herstellen einer Verbindung mit einem der sekundären Knoten für schreibgeschützte Zwecke ermöglicht. 

Neben der in SQL-Datenbank enthaltenen Hochverfügbarkeitsarchitektur gibt es auch das Feature für [Autofailover-Gruppen](../../database/auto-failover-group-overview.md), mit dem Sie die Replikation und das Failover von Datenbanken in einer verwalteten Instanz auf eine andere Region verwalten können. 

#### <a name="sql-agent-jobs"></a>SQL-Agent-Aufträge
SQL-Agent-Aufträge werden in Azure SQL-Datenbank nicht direkt unterstützt und müssen als [Aufträge für elastische Datenbanken (Vorschauversion)](../../database/job-automation-overview.md#elastic-database-jobs-preview) bereitgestellt werden.

#### <a name="logins-and-groups"></a>Anmeldungen und Gruppen
Verschieben Sie SQL-Anmeldungen aus der SQL Server-Quelldatenbank mithilfe von Database Migration Service (DMS) im Offlinemodus zu Azure SQL-Datenbank.  Verwenden Sie das Blatt **Selected logins** (Ausgewählte Anmeldungen) im **Migrations-Assistenten**, um Anmeldungen in Ihre Zielinstanz von SQL-Datenbank zu migrieren. 

Auch Windows-Benutzer und -Gruppen können mithilfe von DMS migriert werden, indem Sie die entsprechende Umschaltfläche auf der Seite „DMS Configuration“ (DMS-Konfiguration) aktivieren. 

Alternativ können Sie das [PowerShell-Hilfstool](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) verwenden, das speziell von den Datenmigrationsarchitekten von Microsoft entworfen wurde. Das Hilfsprogramm erstellt mithilfe von PowerShell ein Transact-SQL-Skript (T-SQL) zum Neuerstellen von Anmeldungen und ausgewählten Datenbankbenutzern aus der Quelle in der Zielinstanz. Das Tool weist automatisch Windows Server AD-Konten zu Azure AD-Konten zu und kann eine UPN-Suche für jede Anmeldung in der Active Directory-Quellinstanz durchführen. Das Tool erstellt benutzerdefinierte Server- und Datenbankrollen sowie Rollenmitgliedschaften, Datenbankrollen und Benutzerberechtigungen. Eigenständige Datenbanken werden noch nicht unterstützt, und nur eine Teilmenge der möglichen SQL Server-Berechtigungen werden erstellt. 


#### <a name="system-databases"></a>Systemdatenbanken
Die einzigen in Azure SQL-Datenbank anwendbaren Systemdatenbanken sind die [Masterdatenbank](/sql/relational-databases/databases/master-database) und tempdb. Weitere Informationen finden Sie unter [tempdb in Azure SQL-Datenbank](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="leverage-advanced-features"></a>Nutzen der erweiterten Features 

Sie sollten unbedingt die Vorteile der erweiterten cloudbasierten Features nutzen, die von SQL-Datenbank angeboten werden. Beispielsweise müssen Sie sich nicht mehr um die Verwaltung der Sicherungen sorgen, da der Dienst sich darum kümmert. Sie können den Datenbankzustand [zu einem beliebigen Zeitpunkt innerhalb des Aufbewahrungszeitraums](../../database/recovery-using-backups.md#point-in-time-restore) wiederherstellen. 

Sie sollten außerdem die  [Azure Active Directory-Authentifizierung](../../database/authentication-aad-overview.md), die [Überwachung](../../database/auditing-overview.md), die  [Bedrohungserkennung](../../database/azure-defender-for-sql.md), die  [Sicherheit auf Zeilenebene](/sql/relational-databases/security/row-level-security) und die  [dynamische Datenmaskierung](/sql/relational-databases/security/dynamic-data-masking) verwenden, um die Sicherheit zu erhöhen.

Zusätzlich zu den erweiterten Verwaltungs- und Sicherheitsfeatures stellt SQL-Datenbank eine Reihe von erweiterten Tools bereit, die Sie beim [Überwachen und Optimieren Ihrer Arbeitsauslastung](../../database/monitor-tune-overview.md) unterstützen. Die [Azure SQL-Analyse (Vorschauversion)](../../../azure-monitor/insights/azure-sql.md) ist eine erweiterte Cloudüberwachungslösung, mit der Sie die Leistung aller Ihrer Datenbanken in Azure SQL-Datenbank im großen Stil und abonnementübergreifend in einer zentralen Ansicht überwachen können. Azure SQL-Analyse erfasst und visualisiert wichtige Leistungsmetriken und umfasst integrierte Logik für die Problembehandlung in Bezug auf die Leistung.

Mithilfe der [automatischen Optimierung](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) kann die Leistung Ihrer SQL-Ausführungsplanstatistiken kontinuierlich überwacht werden. Erkannte Leistungsprobleme werden durch das Feature automatisch korrigiert. 


## <a name="migration-assets"></a>Migrationsressourcen 

Weitere Unterstützung finden Sie in den folgenden Ressourcen, die für reale Migrationsprojekte entwickelt wurden.

|Asset  |BESCHREIBUNG  |
|---------|---------|
|[Data Workload Assessment Model and Tool (Datenarbeitsauslastungs-Bewertungsmodell und -tool)](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Dieses Tool stellt für eine angegebene Arbeitsauslastung Informationen zu empfohlenen optimalen Zielplattformen, zur Cloudbereitschaft und zum Korrekturbedarf für Anwendungen/Datenbanken bereit. Es bietet eine einfache Berechnung und Berichterstellung mit nur einem Klick, die Ihnen durch einen automatisierten und einheitlichen Entscheidungsprozess für die Zielplattform dabei helfen, Bewertungen von umfangreichen Datenbeständen zu beschleunigen.|
|[DBLoader-Hilfsprogramm](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|DBLoader kann zum Laden von Daten aus durch Trennzeichen getrennten Textdateien in SQL Server verwendet werden. Dieses Windows-Konsolenhilfsprogramm verwendet die native Clientbenutzeroberfläche für Massenladevorgänge von SQL Server, die in allen Versionen von SQL Server funktioniert (einschließlich Azure SQL-Datenbank).|
|[Massenerstellung von Datenbanken mit PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|Diese Ressource enthält drei PowerShell-Skripts, mit denen eine Ressourcengruppe (create_rg.ps1), der [logische Server in Azure](../../database/logical-servers.md) (create_sqlserver.ps1) und die Instanz von Azure SQL-Datenbank (create_sqldb.ps1) erstellt werden. Die Skripts enthalten Schleifenfunktionen, sodass Sie Iterationen durchführen und beliebig viele Server und Datenbanken erstellen können.|
|[Massenbereitstellung von Schemas mit MSSQL-Scripter & PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|Mit dieser Ressource wird eine Ressourcengruppe sowie mindestens ein [logischer Server in Azure](../../database/logical-servers.md) zum Hosten der Azure SQL-Datenbank-Instanz erstellt. Außerdem exportiert sie jedes Schema aus einer lokalen SQL Server-Instanz (oder mehreren SQL Server-Instanzen (2005 und höher)) und importiert diese in Azure SQL-Datenbank.|
|[Konvertieren von SQL Server-Agent-Aufträgen in Aufträge für elastische Datenbanken](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|Dieses Skript migriert die SQL Server-Agent-Aufträge Ihrer Quelldatenbank zu Aufträgen für elastische Datenbanken.|
|[Senden von E-Mails aus Azure SQL-Datenbank](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|Dies ist eine alternative Lösung zur Funktion „SendMail“, die in der lokalen Version von SQL Server verfügbar ist. Diese Lösung sendet E-Mails aus Azure SQL-Datenbank mithilfe von Azure Functions und dem Azure-SendGrid-Dienst.|
|[Hilfsprogramm zum Verschieben lokaler SQL Server-Anmeldungen zu Azure SQL-Datenbank](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Hierbei handelt es sich um ein PowerShell-Skript, das ein T-SQL-Befehlsskript erstellt, um Anmeldungen und ausgewählte Datenbankbenutzer aus der lokalen SQL Server-Instanz in Azure SQL-Datenbank neu zu erstellen. Das Tool ermöglicht die automatische Zuordnung von Windows Server AD-Konten zu Azure AD-Konten sowie die optionale Migration von nativen SQL Server-Anmeldungen.|
|[Automatisierung der PerfMon-Datensammlung mithilfe von Logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Dieses Tool sammelt PerfMon-Daten, um Einblicke in die Baselineleistung zu gewinnen und die Empfehlungen für Migrationsziele beizusteuern. Das Tool verwendet die ausführbare Datei „logman.exe“, um den Befehl zu erstellen, der die Leistungsindikatoren für eine SQL Server-Remoteinstanz erstellt, startet, beendet und löscht.|
|[Whitepaper zur Datenbankmigration zu Azure SQL-Datenbank mithilfe von BACPAC-Dateien](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|Dieses Whitepaper enthält Anleitungen und Schritte, die Ihnen helfen, die Migration von SQL Server zu Azure SQL-Datenbank mithilfe von BACPAC-Dateien zu beschleunigen.|

Diese Ressourcen wurden im Rahmen des Data SQL Ninja-Programms entwickelt, das vom Azure Data Group-Entwicklungsteam gesponsert wird. Der Hauptzweck des Data SQL Ninja-Programms besteht darin, Hindernisse für komplexe Modernisierung zu beseitigen und Letztere zu beschleunigen sowie den Wettbewerb in Bezug auf Möglichkeiten zur Migration von Datenplattformen zur Azure-Datenplattform von Microsoft zu fördern. Wenn Sie denken, dass Ihre Organisation an einer Teilnahme am Data SQL Ninja-Programm interessiert wäre, wenden Sie sich an Ihr Kundenteam, und bitten Sie es, eine Nominierung einzureichen.


## <a name="next-steps"></a>Nächste Schritte

Informationen zum Beginn der Migration Ihrer SQL Server-Instanz zu Azure SQL-Datenbank finden Sie im [Leitfaden für die Migration von SQL Server zu SQL-Datenbank](sql-server-to-sql-database-guide.md).

- Eine Übersicht über die Dienste und Tools von Microsoft und Drittanbietern, mit denen Sie verschiedene Datenbank- und Datenmigrationsszenarios und Spezialaufgaben ausführen können, finden Sie unter [Dienste und Tools für die Datenmigration](../../../dms/dms-tools-matrix.md).

- Weitere Informationen zu SQL-Datenbank finden Sie unter:
   - [Übersicht über Azure SQL-Datenbank](../../database/sql-database-paas-overview.md)
   - [Azure-Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) 

- Weitere Informationen zum Framework und zum Einführungszyklus von Cloudmigrationen finden Sie unter:
   -  [Cloud Adoption Framework für Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Bewährte Methoden für die Kostenermittlung und Größenanpassung von zu Azure migrierten Workloads](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Informationen zum Bewerten der Anwendungszugriffsebene finden Sie unter [Data Access Migration Toolkit (Vorschauversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Ausführliche Informationen zum Ausführen von A/B-Tests auf Datenzugriffsebene finden Sie unter [Assistent für Datenbankexperimente](/sql/dea/database-experimentation-assistant-overview).