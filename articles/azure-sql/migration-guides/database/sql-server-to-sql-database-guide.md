---
title: 'Migrationsleitfaden: SQL Server zu SQL-Datenbank'
description: Befolgen Sie diese Anleitung, um Ihre SQL Server-Datenbanken zu Azure SQL-Datenbank zu migrieren.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: ee4abaf3c9f6aa70ba14920711c8917994254649
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326980"
---
# <a name="migration-guide-sql-server-to-sql-database"></a>Migrationsleitfaden: SQL Server zu SQL-Datenbank
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Mithilfe dieses Leitfadens können Sie Ihre SQL Server-Instanzen zu Azure SQL-Datenbank migrieren. 

Sie können SQL Server-Instanzen migrieren, die lokal oder auf folgenden Plattformen ausgeführt werden: 

- SQL Server auf virtuellen Computern  
- Amazon Web Services (AWS) EC2 
- Amazon Relational Database Service (AWS RDS) 
- Compute Engine (Google Cloud Platform – GCP)  
- Cloud SQL für SQL Server (Google Cloud Platform – GCP) 

Weitere Informationen zur Migration finden Sie in [dieser Übersicht](sql-server-to-sql-database-overview.md). Informationen zu anderen Szenarios finden Sie im [Leitfaden zur Datenbankmigration](https://datamigration.microsoft.com/).

:::image type="content" source="media/sql-server-to-database-overview/migration-process-flow-small.png" alt-text="Ablauf des Migrationsprozesses":::

## <a name="prerequisites"></a>Voraussetzungen 

Stellen Sie sicher, dass Sie die folgenden Voraussetzungen erfüllen, um Ihre SQL Server-Instanzen zu Azure SQL-Datenbank zu migrieren: 

- Sie haben eine [Migrationsmethode](sql-server-to-sql-database-overview.md#compare-migration-options) ausgewählt und verfügen über entsprechende Tools. 
- Sie haben den [Datenmigrations-Assistenten (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) auf einem Computer installiert, der eine Verbindung mit Ihrer SQL Server-Quelldatenbank herstellen kann.
- Sie verfügen über eine [Azure SQL-Datenbank](../../database/single-database-create-quickstart.md)-Zielinstanz.


## <a name="pre-migration"></a>Vor der Migration

Sobald Sie sich davon überzeugt haben, dass Ihre Quellumgebung unterstützt wird, können Sie mit der Prämigrationsphase beginnen. Ermitteln Sie alle vorhandenen Datenquellen, bewerten Sie die Umsetzbarkeit der Migration, und identifizieren Sie Probleme, die der Migration möglicherweise im Weg stehen. 

### <a name="discover"></a>Entdecken

Scannen Sie das Netzwerk in der Ermittlungsphase nach SQL Server-Instanzen und -Features, die von Ihrer Organisation verwendet werden. 

Nutzen Sie [Azure Migrate](../../../migrate/migrate-services-overview.md), um die Umsetzbarkeit der Migration auf lokalen Servern zu bewerten, führen Sie eine leistungsbasierte Größenanpassung durch, und erstellen Sie Kostenschätzungen für die Ausführung in Azure. 

Alternativ können Sie das  [Microsoft Assessment and Planning Toolkit (MAP Toolkit)](https://www.microsoft.com/download/details.aspx?id=7826) zur Bewertung Ihrer aktuellen IT-Infrastruktur verwenden. Das Toolkit bietet leistungsfähige Optionen zur Bestandserfassung, Bewertung und Berichterstellung, die den Planungsprozess für die Migration erleichtern. 

Weitere Informationen zu den für die Ermittlungsphase verfügbaren Tools finden Sie unter [Dienste und Tools für Datenmigrationsszenarios](../../../dms/dms-tools-matrix.md). 

### <a name="assess"></a>Bewerten 

Nachdem die Datenquellen ermittelt wurden, sollten Sie alle lokalen SQL Server-Datenbanken bewerten, die zu Azure SQL-Datenbank migriert werden können, um Hindernisse oder Kompatibilitätsprobleme zu ermitteln. 

Wenn Sie Datenbanken mit dem Datenmigrations-Assistenten (Version 4.1 und höher) bewerten, erhalten Sie folgende Informationen: 

- [Azure-Zielempfehlungen](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Azure-SKU-Empfehlungen](/sql/dma/dma-sku-recommend-sql-db)

Befolgen Sie diese Schritte, um Ihre Umgebung mit dem Datenmigrations-Assistenten zu bewerten: 

1. Öffnen Sie den [Datenmigrations-Assistenten](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Klicken Sie auf **Datei** und dann auf **Neues Projekt**. 
1. Wählen Sie einen Projektnamen, SQL Server als Quellserver und Azure SQL-Datenbank als Zielserver aus. 
1. Legen Sie fest, welche Bewertungsberichte generiert werden sollen – zum Beispiel für die Datenbankkompatibilität oder die Featureparität. Die Berechtigungen für den SQL Server-Quellserver unterscheiden sich je nach Art der Bewertung möglicherweise.  DMA zeigt vor der Bewertung an, welche Berechtigungen für den ausgewählten Advisor benötigt werden.
    - Die Kategorie **Featureparität** enthält ausführliche Empfehlungen, in Azure verfügbare Alternativen und Schritte zur Risikominimierung, die bei der Planung Ihres Migrationsprojekts nützlich sind. Hierfür sind sysadmin-Berechtigungen erforderlich.
    - In der Kategorie **Kompatibilitätsprobleme** werden teilweise oder nicht unterstützte Features aufgeführt, die die Migration möglicherweise verhindern, sowie Empfehlungen, um diese zu beheben. Die Berechtigungen `CONNECT SQL`, `VIEW SERVER STATE` und `VIEW ANY DEFINITION` sind erforderlich.
1. Geben Sie die Quellverbindungsdetails für Ihre SQL Server-Instanz an, und stellen Sie eine Verbindung mit der Quelldatenbank her.
1. Klicken Sie auf **Bewertung starten**. 
1. Lesen Sie sich nach Abschluss des Vorgangs die Bewertungsberichte durch, um beispielsweise Probleme mit der Featureparität zu ermitteln, die die Migration verhindern könnten. Der Bewertungsbericht kann auch in eine Datei exportiert und für andere Teams und Mitarbeiter in Ihrer Organisation freigegeben werden. 
1. Bestimmen Sie den Datenbank-Kompatibilitätsgrad, mit dem der Aufwand vor der Migration am geringsten ist.  
1. Ermitteln Sie die am besten geeignete Azure SQL-Datenbank-SKU für Ihre lokale Workload. 

Weitere Informationen finden Sie unter [Durchführen einer SQL Server-Migrationsbewertung mit dem Datenmigrations-Assistenten](/sql/dma/dma-assesssqlonprem).

Wenn bei der Bewertung mehrere Blockaden gefunden werden, die bestätigen, dass Ihre Datenbank noch nicht für die Migration zu Azure SQL-Datenbank bereit ist, sollten Sie alternativ Folgendes erwägen:

- [Azure SQL Managed Instance](../managed-instance/sql-server-to-managed-instance-overview.md), wenn mehrere instanzbezogene Abhängigkeiten vorliegen
- [SQL Server auf Azure-VMs](../virtual-machines/sql-server-to-sql-on-azure-vm-migration-overview.md), wenn sowohl SQL-Datenbank als auch SQL Managed Instance ungeeignet ist 



#### <a name="scaled-assessments-and-analysis"></a>Skalierte Bewertungen und Analysen
Der Datenmigrations-Assistent unterstützt skalierte Bewertungen und die Konsolidierung der Bewertungsberichte, damit diese analysiert werden können. 

Wenn mehrere Server und Datenbanken bewertet und analysiert werden müssen, um einen umfassenderen Überblick über den Datenbestand zu erstellen, finden Sie unter den folgenden Links weitere Informationen:

- [Skalierte Bewertungen mithilfe von PowerShell](/sql/dma/dma-consolidatereports)
- [Analysieren von Bewertungsberichten mithilfe von Power BI](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
> Die Ausführung skalierter Bewertungen für mehrere Datenbanken kann (insbesondere für große Datenbanken) mithilfe des [Befehlszeilen-Hilfsprogramms von DMA](/sql/dma/dma-commandline) automatisiert und für weitere Analysen und zur Bewertung der Zielbereitschaft in [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) hochgeladen werden.

## <a name="migrate"></a>Migrieren

Nachdem Sie die Schritte der Prämigrationsphase abgeschlossen haben, können Sie sich der Schema- und Datenmigration widmen. 

Migrieren Sie Ihre Daten mithilfe der gewählten [Migrationsmethode](sql-server-to-sql-database-overview.md#compare-migration-options). 

In diesem Leitfaden werden die zwei beliebtesten Optionen beschrieben: der Datenmigrations-Assistent und Azure Database Migration Service. 

### <a name="data-migration-assistant-dma"></a>Datenmigrations-Assistent (DMA)

Führen Sie die folgenden Schritte aus, um eine Datenbank mithilfe von DMA von SQL Server zu Azure SQL-Datenbank zu migrieren: 

1. Laden Sie den [Datenmigrations-Assistenten](https://www.microsoft.com/download/details.aspx?id=53595) herunter, und installieren Sie ihn.
1. Erstellen Sie ein neues Projekt, und wählen Sie **Migration** als Projekttyp aus.
1. Legen Sie **SQL Server** als Quellservertyp und **Azure SQL-Datenbank** als Zielservertyp aus. Legen Sie außerdem den Migrationsumfang **Schema und Daten** fest, und klicken Sie dann auf **Erstellen**.
1. Geben Sie im Migrationsprojekt die Quellserverinformationen an. Dazu gehören der Servername, die Anmeldeinformationen zum Herstellen einer Verbindung mit dem Server und die zu migrierende Quelldatenbank.
1. Geben Sie unter den Zielserverinformationen den Azure SQL-Datenbank-Servernamen, die Anmeldeinformationen zum Herstellen einer Verbindung mit dem Server und die Zieldatenbank an, zu der migriert werden soll.
1. Wählen Sie die Schemaobjekte aus, und stellen Sie diese für das Azure SQL-Datenbank-Ziel bereit.
1. Klicken Sie schließlich auf **Datenmigration starten**, und überwachen Sie den Migrationsfortschritt.

Ein ausführliches Tutorial finden Sie unter [Migrieren von lokalen SQL Server-Instanzen oder SQL Server-Instanzen auf Azure-VMs zu Azure SQL-Datenbank mithilfe des Datenmigrations-Assistenten](/sql/dma/dma-migrateonpremsqltosqldb). 


> [!NOTE]
> - Skalieren Sie Ihre Datenbank während des Importvorgangs auf eine höhere Dienstebene und Computegröße, um die Importgeschwindigkeit zu maximieren, indem Sie mehr Ressourcen bereitstellen. Sie können dann nach erfolgreichem Import zentral herunterskalieren.</br>
> - Der Kompatibilitätsgrad der importierten Datenbank basiert auf dem Kompatibilitätsgrad Ihrer Quelldatenbank. 


### <a name="azure-database-migration-service-dms"></a>Azure Database Migration Service (DMS)

Führen Sie die folgenden Schritte aus, um eine Datenbank mithilfe von DMS von SQL Server zu Azure SQL-Datenbank zu migrieren:

1. Registrieren Sie den Ressourcenanbieter **Microsoft.DataMigration** in Ihrem Abonnement, wenn Sie dies noch nicht getan haben. 
1. Erstellen Sie eine Azure Database Migration Service-Instanz an einem gewünschten Standort Ihrer Wahl (vorzugsweise in derselben Region wie Ihr Azure SQL-Datenbank-Ziel). Wählen Sie ein vorhandenes virtuelles Netzwerk aus, oder erstellen Sie ein neues, in dem Ihre DMS-Instanz gehostet werden soll.
1. Nachdem Sie Ihre DMS-Instanz erstellt haben, erstellen Sie ein neues Migrationsprojekt, und geben Sie dabei **SQL Server** als Quellservertyp und **Azure SQL-Datenbank** als Zielservertyp an. Wählen Sie **Offlinedatenmigration** auf dem Blatt für die Migrationsprojekterstellung als Aktivitätstyp aus.
1. Geben Sie auf der Seite **Migrationsquelle** die Details zur SQL Server-Quellinstanz und auf der Seite **Migrationsziel** die Details zur Azure SQL-Datenbank-Zielinstanz ein.
1. Ordnen Sie die Quell- und Zieldatenbanken für die Migration zu, und wählen Sie dann die Tabellen aus, die Sie migrieren möchten.
1. Überprüfen Sie die Migrationszusammenfassung, und klicken Sie auf **Migration ausführen**. Anschließend können Sie die Migrationsaktivität überwachen und den Fortschritt der Datenbankmigration verfolgen.

Ein ausführliches Tutorial finden Sie unter [Migrieren von SQL Server zu Azure SQL-Datenbank mit DMS](../../../dms/tutorial-sql-server-to-azure-sql.md). 

## <a name="data-sync-and-cutover"></a>Datensynchronisierung und Übernahme

Wenn Migrationsoptionen angewendet werden, die Datenänderungen kontinuierlich von der Quelle zum Ziel replizieren und/oder synchronisieren, werden die Quelldaten und das Quellschema ggf. geändert und weichen vom Ziel ab. Sorgen Sie während der Datensynchronisierung dafür, dass alle Änderungen an der Quelle erfasst und während des Migrationsprozesses auf das Ziel angewendet werden. 

Nachdem Sie sich vergewissert haben, dass die Daten in der Quelle und im Ziel identisch sind, können Sie die Übernahme von der Quell- zur Zielumgebung durchführen. Sie sollten den Übernahmeprozess mit den Geschäfts- und Anwendungsteams planen, um Unterbrechungen so geringfügig wie möglich zu halten und die Geschäftskontinuität nicht zu beeinträchtigen. 

> [!IMPORTANT]
> Weitere Informationen zu den Schritten für eine Übernahmemigration mithilfe von DMS finden Sie unter [Durchführen der Migrationsübernahme](../../../dms/tutorial-sql-server-azure-sql-online.md#perform-migration-cutover).


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

Sie sollten unbedingt die erweiterten cloudbasierten Features von Azure SQL-Datenbank nutzen, z. B. die [integrierte Hochverfügbarkeit](../../database/high-availability-sla.md), die [Bedrohungserkennung](../../database/azure-defender-for-sql.md) und die [Überwachung und Optimierung Ihrer Workloads](../../database/monitor-tune-overview.md). 

Einige SQL Server-Features sind nur verfügbar, wenn der [Datenbank-Kompatibilitätsgrad](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) in den neuesten Kompatibilitätsgrad (150) geändert wurde. 

Weitere Informationen finden Sie unter [Verwalten von Azure SQL-Datenbank nach der Migration](../../database/manage-data-after-migrating-to-database.md).


## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Dienste und Tools von Microsoft und Drittanbietern, mit denen Sie verschiedene Datenbank- und Datenmigrationsszenarios und Spezialaufgaben ausführen können, finden Sie unter [Dienste und Tools für die Datenmigration](../../../dms/dms-tools-matrix.md).

- Weitere Informationen zu SQL-Datenbank finden Sie unter:
    - [Übersicht über Azure SQL-Datenbank](../../database/sql-database-paas-overview.md)
   - [Azure-Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) 


- Weitere Informationen zum Framework und zum Einführungszyklus von Cloudmigrationen finden Sie unter:
   -  [Cloud Adoption Framework für Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Bewährte Methoden für die Kostenermittlung und Größenanpassung von zu Azure migrierten Workloads](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Informationen zum Bewerten der Anwendungszugriffsebene finden Sie unter [Data Access Migration Toolkit (Vorschauversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Ausführliche Informationen zum Ausführen von A/B-Tests auf Datenzugriffsebene finden Sie unter [Assistent für Datenbankexperimente](/sql/dea/database-experimentation-assistant-overview).