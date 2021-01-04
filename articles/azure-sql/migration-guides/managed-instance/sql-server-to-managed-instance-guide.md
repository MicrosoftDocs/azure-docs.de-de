---
title: Leitfaden für die Migration von SQL Server zu SQL Managed Instance
description: Befolgen Sie diese Anleitung, um Ihre SQL Server-Datenbank-Instanzen zu Azure SQL Managed Instance zu migrieren.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 5d5404537ad107a54bd32110727e5a7d0f74ebea
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326895"
---
# <a name="migration-guide-sql-server-to-sql-managed-instance"></a>Migrationsleitfaden: SQL Server zu SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Mithilfe dieses Leitfadens können Sie Ihre SQL Server-Instanzen zu Azure SQL Managed Instance zu migrieren. 

Sie können SQL Server-Instanzen migrieren, die lokal oder auf folgenden Plattformen ausgeführt werden: 

- SQL Server auf virtuellen Computern  
- Amazon Web Services (AWS) EC2 
- Amazon Relational Database Service (AWS RDS) 
- Compute Engine (Google Cloud Platform – GCP)  
- Cloud SQL für SQL Server (Google Cloud Platform – GCP) 

Weitere Informationen zur Migration finden Sie in [dieser Übersicht](sql-server-to-managed-instance-overview.md). Informationen zu anderen Szenarios finden Sie im [Leitfaden zur Datenbankmigration](https://datamigration.microsoft.com/).

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-flow-small.png" alt-text="Ablauf des Migrationsprozesses":::

## <a name="prerequisites"></a>Voraussetzungen 

Sorgen Sie zunächst dafür, dass diese Voraussetzungen erfüllt sind, um Ihre SQL Server-Datenbank-Instanz zu Azure SQL Managed Instance zu migrieren: 

- Wählen Sie eine [Migrationsmethode](sql-server-to-managed-instance-overview.md#compare-migration-options) und die jeweils erforderlichen Tools aus.
- Installieren Sie den [Datenmigrations-Assistenten (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) auf einem Computer, der eine Verbindung mit Ihrer SQL Server-Quelldatenbank herstellen kann.


## <a name="pre-migration"></a>Vor der Migration

Sobald Sie sich davon überzeugt haben, dass Ihre Quellumgebung unterstützt wird, können Sie mit der Prämigrationsphase beginnen. Ermitteln Sie alle vorhandenen Datenquellen, bewerten Sie die Umsetzbarkeit der Migration, und identifizieren Sie Probleme, die der Migration möglicherweise im Weg stehen.  

### <a name="discover"></a>Entdecken

Scannen Sie das Netzwerk in der Ermittlungsphase nach SQL Server-Instanzen und -Features, die von Ihrer Organisation verwendet werden. 

Nutzen Sie [Azure Migrate](../../../migrate/migrate-services-overview.md), um die Umsetzbarkeit der Migration auf lokalen Servern zu bewerten, führen Sie eine leistungsbasierte Größenanpassung durch, und erstellen Sie eine Kostenschätzungen für die Ausführung in Azure. 

Alternativ können Sie das  [Microsoft Assessment and Planning Toolkit (MAP Toolkit)](https://www.microsoft.com/download/details.aspx?id=7826) zur Bewertung Ihrer aktuellen IT-Infrastruktur verwenden. Das Toolkit bietet leistungsfähige Optionen zur Bestandserfassung, Bewertung und Berichterstellung, die den Planungsprozess für die Migration erleichtern. 

Weitere Informationen zu den für die Ermittlungsphase verfügbaren Tools finden Sie unter [Dienste und Tools für Datenmigrationsszenarios](../../../dms/dms-tools-matrix.md). 

### <a name="assess"></a>Bewerten 

Nachdem die Datenquellen ermittelt wurden, sollten Sie alle lokalen SQL Server-Instanzen bewerten, die zu Azure SQL Managed Instance migriert werden können, um Hindernisse oder Kompatibilitätsprobleme zu identifizieren. 

Wenn Sie Datenbanken mit dem Datenmigrations-Assistenten (Version 4.1 und höher) bewerten, erhalten Sie folgende Informationen: 

- [Azure-Zielempfehlungen](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Azure-SKU-Empfehlungen](/sql/dma/dma-sku-recommend-sql-db)

Befolgen Sie diese Schritte, um Ihre Umgebung mit dem Datenmigrations-Assistenten zu bewerten: 

1. Öffnen Sie den [Datenmigrations-Assistenten](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Klicken Sie auf **Datei** und dann auf **Neues Projekt**. 
1. Wählen Sie einen Projektnamen, SQL Server als Quellserver und Azure SQL Managed Instance als Zielserver aus. 
1. Legen Sie fest, welche Bewertungsberichte generiert werden sollen – zum Beispiel für die Datenbankkompatibilität oder die Featureparität. Die Berechtigungen für den SQL Server-Quellserver unterscheiden sich je nach Art der Bewertung möglicherweise.  DMA zeigt vor der Bewertung an, welche Berechtigungen für den ausgewählten Advisor benötigt werden.
    - Die Kategorie **Featureparität** enthält ausführliche Empfehlungen, in Azure verfügbare Alternativen und Schritte zur Risikominimierung, die bei der Planung Ihres Migrationsprojekts nützlich sind. Hierfür sind sysadmin-Berechtigungen erforderlich.
    - In der Kategorie **Kompatibilitätsprobleme** werden teilweise oder nicht unterstützte Features aufgeführt, die die Migration möglicherweise verhindern, sowie Empfehlungen, um diese Probleme zu beheben. Die Berechtigungen `CONNECT SQL`, `VIEW SERVER STATE` und `VIEW ANY DEFINITION` sind erforderlich.
1. Geben Sie die Quellverbindungsdetails für Ihre SQL Server-Instanz an, und stellen Sie eine Verbindung mit der Quelldatenbank her.
1. Klicken Sie auf **Bewertung starten**. 
1. Lesen Sie sich nach Abschluss des Vorgangs die Bewertungsberichte durch, um beispielsweise Probleme mit der Featureparität zu ermitteln, die die Migration verhindern könnten. Der Bewertungsbericht kann auch in eine Datei exportiert und für andere Teams und Mitarbeiter in Ihrer Organisation freigegeben werden. 
1. Bestimmen Sie den Datenbank-Kompatibilitätsgrad, mit dem der Aufwand vor der Migration am geringsten ist.  
1. Ermitteln Sie die am besten geeignete Azure SQL Managed Instance-SKU für Ihre lokale Workload. 

Weitere Informationen finden Sie unter [Durchführen einer SQL Server-Migrationsbewertung mit dem Datenmigrations-Assistenten](/sql/dma/dma-assesssqlonprem).

Wenn SQL Managed Instance für Ihre Workload nicht als Ziel geeignet ist, kann SQL Server auf Azure-VMs für Ihr Unternehmen eine alternative Möglichkeit darstellen. 

#### <a name="scaled-assessments-and-analysis"></a>Skalierte Bewertungen und Analysen

Der Datenmigrations-Assistent unterstützt skalierte Bewertungen und die Konsolidierung der Bewertungsberichte, damit diese analysiert werden können. Wenn mehrere Server und Datenbanken bewertet und analysiert werden müssen, um einen umfassenderen Überblick über den Datenbestand zu erstellen, finden Sie unter den folgenden Links weitere Informationen.

- [Skalierte Bewertungen mithilfe von PowerShell](/sql/dma/dma-consolidatereports)
- [Analysieren von Bewertungsberichten mithilfe von Power BI](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
>Die Ausführung skalierter Bewertungen für mehrere Datenbanken kann mithilfe des [Befehlszeilen-Hilfsprogramms von DMA](/sql/dma/dma-commandline) automatisiert werden. Dadurch können die Ergebnisse auch in [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) hochgeladen werden, um weitere Analysen durchzuführen und die Bereitschaft der Zielumgebung zu bewerten.

### <a name="create-a-performance-baseline"></a>Erstellen einer Leistungsbaseline

Wenn Sie die Leistung Ihrer Workload in einer SQL Managed Instance-Instanz mit Ihrer ursprünglich in SQL Server ausgeführten Workload vergleichen möchten, können Sie zu diesem Zweck eine Leistungsbaseline erstellen. Weitere Informationen finden Sie im Artikel zur [Leistungsbaseline](sql-server-to-managed-instance-performance-baseline.md). 

### <a name="create-sql-managed-instance"></a>Erstellen einer verwalteten SQL-Instanz 

Erstellen Sie auf Grundlage der Informationen aus der Ermittlungs- und Bewertungsphase eine entsprechend große SQL Managed Instance-Zielinstanz. Hierfür können Sie das [Azure-Portal](../../managed-instance/instance-create-quickstart.md), [PowerShell](../../managed-instance/scripts/create-configure-managed-instance-powershell.md) oder eine [Azure Resource Manager-Vorlage (ARM-Vorlage)](../../managed-instance/create-template-quickstart.md) verwenden. 


## <a name="migrate"></a>Migrieren

Nachdem Sie die Schritte der Prämigrationsphase abgeschlossen haben, können Sie sich der Schema- und Datenmigration widmen. 

Migrieren Sie Ihre Daten mithilfe der gewählten [Migrationsmethode](sql-server-to-managed-instance-overview.md#compare-migration-options). 

In diesem Leitfaden werden die beiden beliebtesten Optionen behandelt: Azure Database Migration Service (DMS) und die native Sicherung und Wiederherstellung. 

### <a name="database-migration-service"></a>Database Migration Service

Befolgen Sie diese Schritte, um Migrationen mit DMS durchzuführen:

1. Registrieren Sie den Ressourcenanbieter **Microsoft.DataMigration** in Ihrem Abonnement, wenn Sie diesen Vorgang zum ersten Mal durchführen.
1. Erstellen Sie eine Azure Database Migration Service-Instanz an einem Standort Ihrer Wahl (vorzugsweise in der Region der Azure SQL Managed Instance-Zielinstanz). Wählen Sie dann ein vorhandenes virtuelles Netzwerk aus, oder erstellen Sie ein neues, um Ihre DMS-Instanz zu hosten.
1. Wenn Sie Ihre DMS-Instanz erstellt haben, können Sie ein neues Migrationsprojekt erstellen. Geben Sie **SQL Server** als Quellserver und **Azure SQL Managed Instance** als Zielserver an. Wählen Sie auf dem Blatt für die Projekterstellung eine Aktivität (Online- oder Offlinedatenmigration) aus. 
1.  Geben Sie auf der Seite **Migrationsquelle** die Details zur SQL Server-Quellinstanz und auf der Seite **Migrationsziel** die Details zur Azure SQL Managed Instance-Zielinstanz ein. Wählen Sie **Weiter** aus.
1. Wählen Sie die Datenbank aus, die Sie migrieren möchten. 
1. Geben Sie in den Konfigurationseinstellungen die **SMB-Netzwerkfreigabe** an, die die Sicherungsdateien für Ihre Datenbank enthält. Verwenden Sie in DMS die Windows-Benutzeranmeldeinformationen, die auf die Netzwerkfreigabe zugreifen können. Geben Sie die **Details zu Ihrem Azure Storage-Konto** an. 
1. Überprüfen Sie die Migrationszusammenfassung, und klicken Sie auf **Migration ausführen**. Anschließend können Sie die Migrationsaktivität überwachen und den Fortschritt der Datenbankmigration verfolgen.
1. Klicken Sie nach der Wiederherstellung der Datenbank auf **Übernahme starten**. Beim Migrationsprozess wird die Sicherung des Protokollfragments kopiert, sobald Sie es in der SMB-Netzwerkfreigabe verfügbar machen, und im Ziel wiederhergestellt. 
1. Halten Sie in der Quelldatenbank sämtlichen eingehenden Datenverkehr an, und aktualisieren Sie die Verbindungszeichenfolge auf die neue Azure SQL Managed Instance-Datenbank. 

Ein ausführliches Tutorial für diese Migrationsoption finden Sie unter [Tutorial: Onlinemigration von SQL Server zu Azure SQL Managed Instance mithilfe von DMS](../../../dms/tutorial-sql-server-managed-instance-online.md). 
   


### <a name="backup-and-restore"></a>Sichern und Wiederherstellen 

Eine der wichtigsten Funktionen von Azure SQL Managed Instance für eine schnelle und einfache Datenbankmigration besteht in der nativen Wiederherstellung von Datenbanksicherungsdateien (`.bak`), die in [Azure Storage](https://azure.microsoft.com/services/storage/) gespeichert sind. Die Sicherung und Wiederherstellung erfolgen asynchron, und die Dauer richtet sich nach der Größe Ihrer Datenbank. 

Das folgende Diagramm bietet einen allgemeinen Überblick über den Prozess:

![Im Diagramm weist ein Pfeil mit der Beschriftung „SICHERN/zu URL hochladen“ zu Azure Storage. Ein weiterer Pfeil mit der Beschriftung „RESTORE FROM URL“ weist von Azure Storage zu einer verwalteten SQL-Instanz.](./media/sql-server-to-managed-instance-overview/migration-restore.png)

> [!NOTE]
> Je nach Größe der Datenbank dauert es unterschiedlich lange, die Sicherung in Azure Storage hochzuladen und einen nativen Wiederherstellungsvorgang zu Azure SQL Managed Instance durchzuführen. Bei großen Datenbanken müssen Sie eine ausreichende Downtime einberechnen, um den Vorgang abzuschließen. 


Befolgen Sie diese Schritte, um die Migration mit dem Sicherungs- und Wiederherstellungsansatz durchzuführen: 

1. Sichern Sie Ihre Datenbank in Azure Blob Storage. Verwenden Sie beispielsweise die Option [Backup to URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) (Sicherung über URL) in [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Verwenden Sie das entsprechende [Microsoft Azure-Tool](https://go.microsoft.com/fwlink/?LinkID=324399), wenn Datenbanken unterstützt werden müssen, die älter als SQL Server 2012 SP1 CU2 sind. 
1. Stellen Sie über SQL Server Management Studio eine Verbindung mit Ihrer Azure SQL Managed Instance-Instanz her. 
1. Erstellen Sie mithilfe einer SAS (Shared Access Signature) die Anmeldeinformationen für den Zugriff auf das Azure Blob Storage-Konto, das Ihre Datenbanksicherungen enthält. Zum Beispiel:

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```
1. Stellen Sie die Sicherung aus dem Azure Blob Storage-Container wieder her. Zum Beispiel: 

    ```sql
   RESTORE DATABASE [TargetDatabaseName] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

1. Rufen Sie die Datenbank nach Abschluss der Wiederherstellung im **Objekt-Explorer** in SQL Server Management Studio auf. 

Weitere Informationen zu dieser Migrationsoption finden Sie unter [Schnellstart: Wiederherstellen einer Datenbank in Azure SQL Managed Instance mit SSMS](../../managed-instance/restore-sample-database-quickstart.md).

> [!NOTE]
> Der Vorgang der Datenbankwiederherstellung ist asynchron und wiederholbar. Möglicherweise erhalten Sie eine Fehlermeldung in SQL Server Management Studio, falls die Verbindung unterbrochen oder ein Timeout überschritten wird. Azure SQL-Datenbank versucht weiterhin, die Datenbank im Hintergrund wiederherzustellen, und Sie können den Wiederherstellungsfortschritt mithilfe der Ansichten [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) und [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) verfolgen.



## <a name="data-sync-and-cutover"></a>Datensynchronisierung und Übernahme

Wenn Migrationsoptionen angewendet werden, die Datenänderungen kontinuierlich von der Quelle zum Ziel replizieren und/oder synchronisieren, werden die Quelldaten und das Quellschema ggf. geändert und weichen vom Ziel ab. Sorgen Sie während der Datensynchronisierung dafür, dass alle Änderungen an der Quelle erfasst und während des Migrationsprozesses auf das Ziel angewendet werden. 

Nachdem Sie sich vergewissert haben, dass die Daten in der Quelle und im Ziel identisch sind, können Sie die Übernahme von der Quell- zur Zielumgebung durchführen. Sie sollten den Übernahmeprozess mit den Geschäfts- und Anwendungsteams planen, um Unterbrechungen so geringfügig wie möglich zu halten und die Geschäftskontinuität nicht zu beeinträchtigen. 

> [!IMPORTANT]
> Weitere Informationen zu den Schritten für eine Übernahmemigration mithilfe von DMS finden Sie unter [Durchführen der Migrationsübernahme](../../../dms/tutorial-sql-server-managed-instance-online.md#performing-migration-cutover).


## <a name="post-migration"></a>Nach der Migration

Nach dem erfolgreichen Abschluss der Migrationsphase sind einige weitere Schritte erforderlich, um sicherzustellen, dass alles so reibungslos und effizient wie möglich funktioniert. 

Die Postmigrationsphase ist unerlässlich, um Datengenauigkeitsprobleme abzustimmen, die Vollständigkeit zu bestätigen und Leistungsprobleme der Workload zu beheben. 

### <a name="remediate-applications"></a>Korrigieren von Anwendungen 

Wenn die Daten in die Zielumgebung migriert wurden, müssen alle Anwendungen, die zuvor die Quelle verwendet haben, beginnen das Ziel zu verwenden. Hierfür sind in einigen Fällen Änderungen an den Anwendungen erforderlich.

### <a name="perform-tests"></a>Durchführen von Tests

Das Testvorgehen für die Datenbankmigration umfasst die folgenden Aktivitäten:

1. **Entwickeln von Validierungstests**: Für das Testen der Datenbankmigration müssen Sie SQL-Abfragen verwenden. Sie müssen die Validierungsabfragen erstellen, die für die Quell- und die Zieldatenbank ausgeführt werden sollen. Ihre Validierungsabfragen sollten den von Ihnen definierten Bereich abdecken.
1. **Einrichten der Testumgebung**: Die Testumgebung sollte eine Kopie der Quelldatenbank und der Zieldatenbank enthalten. Stellen Sie sicher, dass Sie die Testumgebung isolieren.
1. **Ausführen der Validierungstests**: Führen Sie die Validierungstests für die Quelle und das Ziel aus, und analysieren Sie anschließend die Ergebnisse.
1. **Ausführen von Leistungstests**: Führen Sie Leistungstests für die Quelle und das Ziel aus, und analysieren und vergleichen Sie anschließend die Ergebnisse.

   > [!NOTE]
   > Für Unterstützung bei der Entwicklung und Ausführung von Validierungstests nach der Migration sollten Sie die Datenqualitätslösung des Partners [QuerySurge](https://www.querysurge.com/company/partners/microsoft) in Erwägung ziehen. 



## <a name="leverage-advanced-features"></a>Nutzen der erweiterten Features 

Sie sollten die erweiterten cloudbasierten Features von SQL Managed Instance nutzen, z. B. die [integrierte Hochverfügbarkeit](../../database/high-availability-sla.md), die [Bedrohungserkennung](../../database/azure-defender-for-sql.md) und die [Überwachung und Optimierung Ihrer Workloads](../../database/monitor-tune-overview.md). 

Die [Azure SQL-Analyse](../../../azure-monitor/insights/azure-sql.md) bietet eine zentralisierte Überwachung für große Mengen verwalteter Instanzen.

Einige SQL Server-Features sind nur verfügbar, wenn der [Datenbank-Kompatibilitätsgrad](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) in den neuesten Kompatibilitätsgrad (150) geändert wurde. 


## <a name="next-steps"></a>Nächste Schritte

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