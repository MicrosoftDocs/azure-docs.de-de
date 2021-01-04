---
title: Leitfaden für die Migration von SQL Server zu SQL Server auf Azure-VMs
description: Befolgen Sie diese Anleitung, um einzelne SQL Server-Datenbank-Instanzen zu SQL Server auf Azure-VMs zu migrieren.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: ''
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 3b0fdccd3eaf6e6bd94b595107022f738bdd8382
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325915"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-vms"></a>Migrationsleitfaden: SQL Server zu SQL Server auf Azure-VMs 
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

In diesem Migrationsleitfaden wird behandelt, wie Sie Ihre Benutzerdatenbanken **ermitteln**, **bewerten** und von SQL Server zu einer SQL Server-Instanz auf einer Azure-VM **migrieren**, indem Sie die Sicherungs-, Wiederherstellungs- und Protokollversandoptionen nutzen. Die Bewertung erfolgt mit dem [Datenmigrations-Assistenten (DMA)](/sql/dma/dma-overview). 

Sie können SQL Server-Instanzen migrieren, die lokal oder auf folgenden Plattformen ausgeführt werden:

- SQL Server auf virtuellen Computern  
- Amazon Web Services (AWS) EC2 
- Amazon Relational Database Service (AWS RDS) 
- Compute Engine (Google Cloud Platform – GCP)

Informationen zu anderen Migrationsstrategien finden Sie in der [Übersicht zur Migration von SQL Server-VMs](sql-server-to-sql-on-azure-vm-migration-overview.md).

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="Ablauf des Migrationsprozesses":::

## <a name="prerequisites"></a>Voraussetzungen

Für die Migration zu SQL Server auf Azure-VMs benötigen Sie Folgendes: 

- den [Datenmigrations-Assistenten (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)
- ein [Azure Migrate-Projekt](../../../migrate/create-manage-projects.md)
- eine vorbereitete Zielinstanz für [SQL Server auf Azure-VMs](../../virtual-machines/windows/create-sql-vm-portal.md), deren Version identisch mit oder höher als die der SQL Server-Quellinstanz ist
- [Konnektivität zwischen Azure und einer lokalen Umgebung](/azure/architecture/reference-architectures/hybrid-networking)
- eine [geeignete Migrationsstrategie](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)

## <a name="pre-migration"></a>Vor der Migration

Bevor Sie mit der Migration beginnen, müssen Sie die Topologie Ihrer SQL-Umgebung ermitteln und die Umsetzbarkeit der gewünschten Migrationsstrategie bewerten. 

### <a name="discover"></a>Entdecken

Azure Migrate bewertet die Umsetzbarkeit der Migration auf lokalen Computern, führt eine leistungsbasierte Größenanpassung durch und erstellt eine Kostenschätzungen für die Ausführung in lokalen Umgebungen. Zur Planung der Migration können Sie Azure Migrate einsetzen, um [vorhandene Datenquellen und Details zu den Features](../../../migrate/concepts-assessment-calculation.md) zu ermitteln, die Ihre SQL Server-Instanzen verwenden. Bei diesem Prozess wird das Netzwerk gescannt, um alle SQL Server-Instanzen Ihrer Organisation sowie die verwendeten Versionen und Features zu ermitteln. 

> [!IMPORTANT]
> Berücksichtigen Sie die [Leistungsrichtlinien für SQL Server auf Azure-VMs](../../virtual-machines/windows/performance-guidelines-best-practices.md), wenn Sie eine Azure-Ziel-VM für Ihre SQL Server-Instanz auswählen.

Weitere Informationen Ermittlungstools finden Sie unter [Dienste und Tools für Datenmigrationsszenarios](../../../dms/dms-tools-matrix.md#business-justification-phase).


### <a name="assess"></a>Bewerten

Nachdem Sie alle Datenquellen ermittelt haben, können Sie den [Datenmigrations-Assistenten (DMA)](/sql/dma/dma-overview) verwenden, um lokale SQL Server-Instanzen zu bewerten, die zu einer SQL Server-Instanz auf einer Azure-VM migriert werden, und so Diskrepanzen zwischen Quell- und Zielinstanzen festzustellen. 


> [!NOTE]
> Wenn Sie Ihre SQL Server-Version _nicht_ upgraden, können Sie diesen Schritt überspringen und mit dem Abschnitt [Migrieren](#migrate) fortfahren. 


#### <a name="assess-user-databases"></a>Bewerten von Benutzerdatenbanken 

Der Datenmigrations-Assistent (DMA) unterstützt Sie bei der Migration zu einer modernen Datenplattform, indem er Kompatibilitätsprobleme erkennt, die die Datenbankfunktionalität auf der neuen Version von SQL Server beeinträchtigen können. DMA empfiehlt Leistungs- und Zuverlässigkeitsverbesserungen für Ihre Zielumgebung und ermöglicht es Ihnen zudem, Ihr Schema, Ihre Daten und Anmeldeobjekte vom Quellserver auf den Zielserver zu verschieben.

Weitere Informationen finden Sie unter [Bewertung](/sql/dma/dma-migrateonpremsql). 


> [!IMPORTANT]
>Die Berechtigungen für den SQL Server-Quellserver unterscheiden sich je nach Art der Bewertung möglicherweise. 
   > - Für den Advisor zur **Featureparität** müssen die Anmeldeinformationen für die Verbindungsherstellung mit der SQL Server-Quelldatenbank ein Mitglied der Serverrolle *sysadmin* sein.
   > - Für den Advisor zu Kompatibilitätsproblemen müssen die Anmeldeinformationen mindestens die Berechtigungen `CONNECT SQL`, `VIEW SERVER STATE` und `VIEW ANY DEFINITION` besitzen.
   > - DMA zeigt vor der Bewertung an, welche Berechtigungen für den ausgewählten Advisor benötigt werden.


#### <a name="assess-applications"></a>Bewerten von Anwendungen

In der Regel greift eine Anwendungsschicht auf Benutzerdatenbanken zu, um Daten zu speichern und zu ändern.  DMA kann die Datenzugriffsschicht einer Anwendung auf zwei Arten bewerten: 

- Mithilfe von erfassten [erweiterten Ereignissen](/sql/relational-databases/extended-events/extended-events) oder [SQL Server Profiler-Ablaufverfolgungen](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) Ihrer Benutzerdatenbanken. Sie können auch mithilfe des [Assistenten für Datenbankexperimente](/sql/dea/database-experimentation-assistant-capture-trace) ein Ablaufverfolgungsprotokoll erstellen, das auch für A/B-Tests verwendet werden kann.

- Mithilfe des [Data Access Migration Toolkit (DAMT, Vorschauversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit), das SQL-Abfragen im Code ermittelt und bewertet und zudem für die Migration von Anwendungsquellcode von einer Datenbankplattform zu einer anderen verwendet wird. Dieses Tool unterstützt verschiedene beliebte Dateitypen wie C#, Java, XML und Textformate. Eine Anleitung für die DAMT-Bewertung finden Sie auf dem [DAMT-Blog](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430).

Verwenden Sie DMA, um erfasste Ablaufverfolgungsdateien oder DAMT-Dateien während der Bewertung von Benutzerdatenbanken zu [importieren](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess). 


#### <a name="scale-assessments"></a>Skalieren von Bewertungen

Wenn für mehrere Server eine DMA-Bewertung durchgeführt werden muss, können Sie den Prozess über die [Befehlszeilenschnittstelle](/sql/dma/dma-commandline) automatisieren. Mithilfe der Schnittstelle können Sie Bewertungsbefehle für alle SQL Server-Instanzen vorbereiten, die migriert werden sollen. 

Wenn Sie zusammenfassende Berichte für große Bestände benötigen, können DMA-Bewertungen nun auch [in Azure Migrate konsolidiert](/sql/dma/dma-assess-sql-data-estate-to-sqldb) werden.

#### <a name="refactor-databases-with-dma"></a>Umgestalten von Datenbanken mit DMA

Auf Grundlage der DMA-Bewertungsergebnisse werden einige Empfehlungen angezeigt, um sicherzustellen, dass Ihre Benutzerdatenbanken nach der Migration ordnungsgemäß funktionieren. DMA liefert Details zu den betroffenen Objekten und Ressourcen zur Behebung der einzelnen Probleme. Es wird empfohlen, alle Breaking Changes und Behavior Changes zu beheben, bevor die Produktionsmigration durchgeführt wird.

Hinsichtlich veralteter Features können Sie sich dazu entscheiden, Ihre Benutzerdatenbanken im ursprünglichen [Kompatibilitätsmodus](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) auszuführen, um diese Änderungen zu vermeiden und die Migration zu beschleunigen. Sie können die [Datenbankkompatibilität jedoch nicht upgraden](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades), bis die Probleme mit veralteten Elementen behoben wurden.

Es wird dringend empfohlen, alle DMA-Korrekturen während der [Postmigrationsphase](#post-migration) als Skript auf die SQL Server-Zieldatenbank anzuwenden.

> [!CAUTION]
> Nicht alle SQL Server-Versionen unterstützen sämtliche Kompatibilitätsmodi. Vergewissern Sie sich, dass die [SQL Server-Zielversion](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) den ausgewählten Datenbank-Kompatibilitätsgrad unterstützt. SQL Server 2019 unterstützt beispielsweise keine Datenbanken mit dem Kompatibilitätsgrad 90 (entspricht SQL Server 2005). Für diese Datenbanken müsste mindestens ein Upgrade auf Kompatibilitätsgrad 100 durchgeführt werden.
>

## <a name="migrate"></a>Migrieren

Nachdem Sie die Schritte der Prämigrationsphase abgeschlossen haben, sind Sie bereit für die Migration der Benutzerdatenbanken und Komponenten. Migrieren Sie Ihre Datenbanken mithilfe Ihrer bevorzugten [Migrationsmethode](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).  

Im Folgenden werden die Schritte für eine Migration mit Sicherung und Wiederherstellung oder eine Migration mit minimaler Downtime erläutert, bei der die Sicherung und Wiederherstellung in Kombination mit dem Protokollversand verwendet werden. 

### <a name="backup-and-restore"></a>Sichern und Wiederherstellen

Befolgen Sie diese Schritte, wenn Sie eine gewöhnliche Migration mit Sicherung und Wiederherstellung durchführen möchten: 

1. Richten Sie die Konnektivität zur SQL Server-Zielinstanz auf einer Azure-VM Ihren Anforderungen entsprechend ein. Weitere Informationen finden Sie unter [Verbinden mit SQL Server-Instanzen auf virtuellen Azure-Maschinen (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Halten Sie alle Anwendungen an, die für die Migration vorgesehene Datenbanken verwenden. 
1. Sorgen Sie mit dem [Einzelbenutzermodus](/sql/relational-databases/databases/set-a-database-to-single-user-mode) dafür, dass Benutzerdatenbanken inaktiv sind. 
1. Führen Sie eine vollständige Datenbanksicherung an einem lokalen Speicherort durch.
1. Kopieren Sie lokale Sicherungsdateien auf Ihre VM, indem Sie einen Remotedesktop, [Azure Data Explorer](/azure/data-explorer/data-explorer-overview) oder das [Befehlszeilentool AzCopy](../../../storage/common/storage-use-azcopy-v10.md) (bei Sicherungen über 2 TB empfohlen) verwenden.
1. Stellen Sie die vollständigen Datenbanksicherungen in SQL Server auf der Azure-VM wieder her.

### <a name="log-shipping--minimize-downtime"></a>Protokollversand (Minimieren der Downtime)

Befolgen Sie diese Schritte, um eine Migration mit minimaler Downtime mithilfe von Sicherung, Wiederherstellung und Protokollversand durchzuführen: 

1. Richten Sie die Konnektivität zur SQL Server-Zielinstanz auf einer Azure-VM Ihren Anforderungen entsprechend ein. Weitere Informationen finden Sie unter [Verbinden mit SQL Server-Instanzen auf virtuellen Azure-Maschinen (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Stellen Sie sicher, dass die zu migrierenden Benutzerdatenbanken das vollständige oder massenprotokollierte Wiederherstellungsmodell verwenden.
1. Führen Sie eine vollständige Datenbanksicherung auf einen lokalen Speicherort durch, und ändern Sie vorhandene Datenbanksicherungsaufträge so, dass das Schlüsselwort [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) verwendet wird, um die Protokollkette aufrechtzuerhalten.
1. Kopieren Sie lokale Sicherungsdateien auf Ihre VM, indem Sie einen Remotedesktop, [Azure Data Explorer](/azure/data-explorer/data-explorer-overview) oder das [Befehlszeilentool AzCopy](../../../storage/common/storage-use-azcopy-v10.md) (bei Sicherungen über 1 TB empfohlen) verwenden.
1. Stellen Sie die vollständigen Datenbanksicherungen in SQL Server auf der Azure-VM wieder her.
1. Richten Sie den [Protokollversand](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) zwischen der lokalen Datenbank und der SQL Server-Zieldatenbank auf der Azure-VM ein. Initialisieren Sie Ihre Datenbanken nicht erneut, da dieser Vorgang bereits in einem vorherigen Schritt erfolgt ist.
1. Führen Sie eine **Übernahme** auf den Zielserver durch. 
   1. Halten Sie Anwendungen an, die die zu migrierenden Datenbanken verwenden. 
   1. Sorgen Sie mit dem [Einzelbenutzermodus](/sql/relational-databases/databases/set-a-database-to-single-user-mode) dafür, dass Benutzerdatenbanken inaktiv sind. 
   1. Wenn alles bereit ist, führen Sie ein [vom Protokollversand gesteuertes Failover](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) der lokalen Datenbanken auf die SQL Server-Zieldatenbanken auf der Azure-VM durch.



### <a name="migrating-objects-outside-user-databases"></a>Migrieren von Objekten außerhalb von Benutzerdatenbanken

Möglicherweise werden zusätzliche SQL Server-Objekte für den reibungslosen Betrieb Ihrer Benutzerdatenbanken nach der Migration benötigt. 

Die folgende Tabelle enthält Komponenten und empfohlene Migrationsmethoden, die vor oder nach der Migration Ihrer Benutzerdatenbanken anwendbar sind: 


| **Feature** | **Komponente** | **Migrationsmethode(n)** |
| --- | --- | --- |
| **Datenbanken** | Modell  | Skript mit SQL Server Management Studio |
|| TempDB | Verschieben Sie tempdb auf einen [temporären SSD-Datenträger einer Azure-VM](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk), um die bestmögliche Leistung zu erzielen. Wählen Sie eine VM-Größe mit ausreichend lokalem SSD-Speicher für tempdb. |
|| Benutzerdatenbanken mit Filestream |  Wenden Sie die [Sicherungs- und Wiederherstellungsmethode](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) für die Migration an. DMA unterstützt keine Datenbanken mit Filestream. |
| **Security** | SQL Server- und Windows-Anmeldungen | Verwenden Sie DMA zum [Migrieren von Benutzeranmeldungen](/sql/dma/dma-migrateserverlogins). |
|| SQL Server-Rollen | Skript mit SQL Server Management Studio |
|| Kryptografieanbieter | Eine [Konvertierung](../../virtual-machines/windows/azure-key-vault-integration-configure.md) wird empfohlen, damit Sie Azure Key Vault verwenden können. Hierfür wird der Ressourcenanbieter [Microsoft.SqlVirtualMachine](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) verwendet. |
| **Serverobjekte** | Sicherungsmedien | Ersetzen Sie die Datenbanksicherung mit [Azure Backup](../../../backup/backup-sql-server-database-azure-vms.md), oder schreiben Sie Sicherungen in [Azure Storage](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) (SQL Server 2012 SP1 CU2 und höher). Hierfür wird der Ressourcenanbieter [Microsoft.SqlVirtualMachine](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) verwendet.|
|| Verbindungsserver | Skript mit SQL Server Management Studio |
|| Servertrigger | Skript mit SQL Server Management Studio |
| **Replikation** | Lokale Veröffentlichungen | Skript mit SQL Server Management Studio |
|| Lokale Abonnenten | Skript mit SQL Server Management Studio |
| **Polybase** | PolyBase | Skript mit SQL Server Management Studio |
| **Verwaltung** | Datenbank-E-Mail | Skript mit SQL Server Management Studio |
| **SQL Server-Agent** | Aufträge | Skript mit SQL Server Management Studio |
|| Alerts | Skript mit SQL Server Management Studio |
|| Operatoren | Skript mit SQL Server Management Studio |
|| Proxys | Skript mit SQL Server Management Studio |
| **Betriebssystem** | Dateien, Dateifreigaben | Notieren Sie alle zusätzlichen Dateien oder Dateifreigaben, die von Ihren SQL Server-Instanzen verwendet werden, und replizieren Sie diese auf der Azure-Ziel-VM. |


## <a name="post-migration"></a>Nach der Migration

Nach dem erfolgreichen Abschluss der Migrationsphase sind einige weitere Schritte erforderlich, um sicherzustellen, dass alles so reibungslos und effizient wie möglich funktioniert.

### <a name="remediate-applications"></a>Korrigieren von Anwendungen

Wenn die Daten in die Zielumgebung migriert wurden, müssen alle Anwendungen, die zuvor die Quelle verwendet haben, beginnen das Ziel zu verwenden. Hierfür sind in einigen Fällen Änderungen an den Anwendungen erforderlich.

Wenden Sie alle vom Datenmigrations-Assistenten empfohlenen Korrekturen auf die Benutzerdatenbanken an. Hierfür sollten Sie Skripts verwenden, um den Vorgang zu automatisieren und Konsistenz zu gewährleisten.

### <a name="perform-tests"></a>Durchführen von Tests

Das Testvorgehen für die Datenbankmigration umfasst die folgenden Aktivitäten:

1. **Entwickeln der Validierungstests:**  Verwenden Sie SQL-Abfragen, um Datenbankmigrationen zu testen. Erstellen Sie Validierungsabfragen, die für die Quell- und die Zieldatenbanken ausgeführt werden sollen. Ihre Validierungsabfragen sollten den von Ihnen definierten Bereich abdecken.
2. **Einrichten der Testumgebung:**  Die Testumgebung sollte eine Kopie der Quelldatenbank und der Zieldatenbank enthalten. Stellen Sie sicher, dass Sie die Testumgebung isolieren.
3. **Ausführen der Validierungstests:**  Führen Sie die Validierungstests für die Quelle und das Ziel aus, und analysieren Sie anschließend die Ergebnisse.
4. **Ausführen der Leistungstests:**  Führen Sie Leistungstests für die Quelle und das Ziel aus, und analysieren und vergleichen Sie anschließend die Ergebnisse.

> [!TIP]
> Verwenden Sie den [Assistenten für Datenbankexperimente (DEA)](/sql/dea/database-experimentation-assistant-overview), um die Leistung der SQL Server-Zielinstanz zu bewerten.
>

### <a name="optimize"></a>Optimieren

Die Postmigrationsphase ist unerlässlich, um Probleme mit der Datengenauigkeit und -vollständigkeit abzustimmen und potenzielle Leistungsprobleme der Workload zu beheben.

Die folgenden Ressourcen liefern weitere Informationen zu diesen Problemen und den Behebungsschritten:

- [Leitfaden für die Überprüfung und Optimierung nach der Migration](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Optimieren der Leistung bei SQL Server auf Azure-VMs](../../virtual-machines/windows/performance-guidelines-best-practices.md)
- [Kostenoptimierung in Azure](https://azure.microsoft.com/overview/cost-optimization/)

## <a name="next-steps"></a>Nächste Schritte

- Die Verfügbarkeit der mit SQL Server kompatiblen Dienste finden Sie auf der Seite für die [globale Azure-Infrastruktur](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database).

- Eine Übersicht über die Dienste und Tools von Microsoft und Drittanbietern, mit denen Sie verschiedene Datenbank- und Datenmigrationsszenarios und Spezialaufgaben ausführen können, finden Sie im Artikel [Dienste und Tools für die Datenmigration](../../../dms/dms-tools-matrix.md).

- Hier finden Sie weitere Informationen zu Azure SQL:
   - [Bereitstellungsoptionen](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server auf Azure-VMs](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure-Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) 


- Weitere Informationen zum Framework und zum Einführungszyklus von Cloudmigrationen finden Sie unter:
   -  [Cloud Adoption Framework für Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Bewährte Methoden für die Kostenermittlung und Größenanpassung von zu Azure migrierten Workloads](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Hier finden Sie weitere Informationen zur Lizenzierung:
   - [Bring-Your-Own-License mit dem Azure-Hybridvorteil](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Erweiterter Support für SQL Server 2008 und SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Informationen zum Bewerten der Anwendungszugriffsebene finden Sie unter [Data Access Migration Toolkit (Vorschauversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Ausführliche Informationen zum Ausführen von A/B-Tests auf Datenzugriffsebene finden Sie unter [Assistent für Datenbankexperimente](/sql/dea/database-experimentation-assistant-overview).