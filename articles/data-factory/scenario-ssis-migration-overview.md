---
title: Migrieren von lokalen SQL Server Integration Services (SSIS)-Workloads zu SSIS in Azure Data Factory (ADF)
description: Migrieren Sie lokale SSIS-Workloads zu SSIS in ADF.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: ef4b01e38a60d6770ba476988fab934ada0bc631
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635693"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Migrieren von lokalen SSIS-Workloads zu SSIS in ADF

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Übersicht

Wenn Sie Ihre Datenbankworkloads von SQL Server lokal zu Azure-Datenbankdiensten migrieren (und zwar Azure SQL-Datenbank oder Azure SQL Managed Instance), müssen Ihre ETL-Workloads in SSIS (SQL Server Integration Services) als einer der primären Dienste mit Mehrwert ebenfalls migriert werden.

Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF) unterstützt die Ausführung von SSIS-Paketen. Sobald Azure-SSIS IR bereitgestellt wurde, können Sie vertraute Tools wie SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) und Befehlszeilenprogramme wie „dtinstall/dtutil/dtexec“ verwenden, um Ihre Pakete in Azure bereitzustellen und auszuführen. Weitere Informationen finden Sie unter [Azure SSIS per Lift & Shift migrieren – Übersicht](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

In diesem Artikel wird der Migrationsprozess Ihrer ETL-Workloads von lokalen SSIS zu SSIS in ADF hervorgehoben. Der Migrationsprozess besteht aus zwei Phasen: **Bewertung** und **Migration** .

## <a name="assessment"></a>Bewertung

Um einen vollständigen Migrationsplan zu erstellen, hilft eine gründliche Bewertung, Probleme mit den SSIS-Quellpaketen zu identifizieren, die eine erfolgreiche Migration verhindern würden.

Der Datenmigrations-Assistent (DMA) ist ein zu diesem Zweck frei herunterladbares Tool, das lokal installiert und ausgeführt werden kann. Das DMA-Bewertungsprojekt vom Typ **Integrationsdienst** kann erstellt werden, um SSIS-Pakete in Batches zu bewerten und Kompatibilitätsprobleme zu identifizieren, die in den folgenden Kategorien dargestellt werden:

- Migrationsblocker: Dies sind Kompatibilitätsprobleme, die die Migration von Quellpaketen für die Ausführung in Azure-SSIS IR blockieren. DMA bietet Anleitungen, die Ihnen helfen, diese Probleme zu lösen.

- Informative Probleme: Dies sind teilweise unterstützte oder veraltete Features, die in Quellpaketen verwendet werden. DMA bietet zur Behebung eine umfassende Reihe von Empfehlungen, in Azure verfügbare alternative Ansätze, und Schritte zur Risikominimierung.

### <a name="four-storage-types-for-ssis-packages"></a>Vier Speichertypen für SSIS-Pakete

- SSIS-Katalog (SSISDB): Wurde eingeführt mit SQL Server 2012 und enthält eine Reihe von gespeicherten Prozeduren, Ansichten und Tabellenwertfunktionen, die für die Arbeit mit SSIS-Projekten/-Paketen verwendet werden.
- Dateisystem:
- SQL Server-Systemdatenbank (MSDB).
- SSIS-Paketspeicher: Eine Paketverwaltungsebene über zwei Untertypen:
  - MSDB, eine Systemdatenbank in SQL Server, die zum Speichern von SSIS-Paketen verwendet wird.
  - Verwaltetes Dateisystem, bei dem es sich um einen bestimmten Ordner im SQL Server-Installationspfad handelt, in dem SSIS-Pakete gespeichert werden.

DMA unterstützt derzeit die Batchbewertung von Paketen, die seit **DMA Version v5.0** in **Dateisystem** , **Paketspeicher** und **SSIS-Katalog** gespeichert werden.

Rufen Sie [DMA](/sql/dma/dma-overview) ab, und [führen Sie damit Ihre Paketbewertung durch](/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Migration

In Abhängigkeit von den [Speichertypen](#four-storage-types-for-ssis-packages) von SSIS-Quellpaketen und dem Migrationsziel der Datenbankworkloads können die Schritte zum Migrieren von **SSIS-Paketen** und **SQL Server-Agentaufträgen** variieren, die die Ausführung von SSIS-Paketen planen. Es gibt zwei Szenarien:

- [**Azure SQL Managed Instance** als Datenbankworkloadziel](#azure-sql-managed-instance-as-database-workload-destination)
- [**Azure SQL-Datenbank** als Datenbankworkloadziel](#azure-sql-database-as-database-workload-destination)

Es ist auch eine praktische Möglichkeit, mithilfe von [SSIS DevOps-Tools](/sql/integration-services/devops/ssis-devops-overview) die erneute Bereitstellung von Batch-Paketen im Migrationsziel durchzuführen.  

### <a name="azure-sql-managed-instance-as-database-workload-destination"></a>**Azure SQL Managed Instance** als Datenbankworkloadziel

| **Paketspeichertyp** |Batchmigration von SSIS-Paketen|Batchmigration von SSIS-Aufträgen|
|-|-|-|
|SSISDB|[Migrieren von **SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|<li>[Migrieren von SSIS-Aufträgen zu einem Azure SQL Managed Instance-Agent](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li>Konvertieren Sie sie mithilfe von Skripts/SSMS/ADF-Portal in ADF-Pipelines/-Aktivitäten/-Trigger. Weitere Informationen finden Sie unter [SSMS-Zeitplanungsfunktion](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Dateisystem|Stellen Sie sie in Dateifreigaben/Azure Files über „dtinstall/dtutil/manual copy“ erneut bereit, oder bewahren Sie sie in Dateisystemen für den Zugriff über VNet/Selbstgehostete IR. Weitere Informationen finden Sie unter [dtutil-Hilfsprogramm](/sql/integration-services/dtutil-utility).|<li>[Migrieren von SSIS-Aufträgen zu einem Azure SQL Managed Instance-Agent](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Migrieren mit dem [SSIS-Auftragsmigrations-Assistenten in SSMS](how-to-migrate-ssis-job-ssms.md) <li>Konvertieren Sie sie mithilfe von Skripts/SSMS/ADF-Portal in ADF-Pipelines/-Aktivitäten/-Trigger. Weitere Informationen finden Sie unter [SSMS-Zeitplanungsfunktion](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportieren Sie sie über SSMS/dtutil in Dateisysteme/Dateifreigaben/Azure Files. Weitere Informationen finden Sie unter [Exportieren von SSIS-Paketen](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages).|Konvertieren Sie sie mithilfe von Skripts/SSMS/ADF-Portal in ADF-Pipelines/-Aktivitäten/-Trigger. Weitere Informationen finden Sie unter [SSMS-Zeitplanungsfunktion](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Paketspeicher|Exportieren Sie sie über „SSMS/dtutil“ in den Paketspeicher, oder stellen Sie sie über „dtinstall/dtutil/manualcopy“ im Paketspeicher erneut bereit. Weitere Informationen hierzu finden Sie unter [Verwalten von Paketen mit dem Paketspeicher für Azure-SSIS Integration Runtime](azure-ssis-integration-runtime-package-store.md).|<li>[Migrieren von SSIS-Aufträgen zu einem Azure SQL Managed Instance-Agent](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Konvertieren Sie sie mithilfe von Skripts/SSMS/ADF-Portal in ADF-Pipelines/-Aktivitäten/-Trigger. Weitere Informationen finden Sie unter [SSMS-Zeitplanungsfunktion](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL-Datenbank** als Datenbankworkloadziel

| **Paketspeichertyp** |Batchmigration von SSIS-Paketen|Batchmigration von Aufträgen|
|-|-|-|
|SSISDB|Stellen Sie sie über SSDT/SSMS erneut in Azure-SSISDB bereit. Weitere Informationen finden Sie unter [Bereitstellen von SSIS-Paketen in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Konvertieren Sie sie mithilfe von Skripts/SSMS/ADF-Portal in ADF-Pipelines/-Aktivitäten/-Trigger. Weitere Informationen finden Sie unter [SSMS-Zeitplanungsfunktion](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Dateisystem|Stellen Sie sie in Dateifreigaben/Azure Files über „dtinstall/dtutil/manual copy“ erneut bereit, oder bewahren Sie sie in Dateisystemen für den Zugriff über VNet/Selbstgehostete IR. Weitere Informationen finden Sie unter [dtutil-Hilfsprogramm](/sql/integration-services/dtutil-utility).|<li> Migrieren mit dem [SSIS-Auftragsmigrations-Assistenten in SSMS](how-to-migrate-ssis-job-ssms.md) <li> Konvertieren Sie sie mithilfe von Skripts/SSMS/ADF-Portal in ADF-Pipelines/-Aktivitäten/-Trigger. Weitere Informationen finden Sie unter [SSMS-Zeitplanungsfunktion](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportieren Sie sie über SSMS/dtutil in Dateisysteme/Dateifreigaben/Azure Files. Weitere Informationen finden Sie unter [Exportieren von SSIS-Paketen](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages).|Konvertieren Sie sie mithilfe von Skripts/SSMS/ADF-Portal in ADF-Pipelines/-Aktivitäten/-Trigger. Weitere Informationen finden Sie unter [SSMS-Zeitplanungsfunktion](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Paketspeicher|Exportieren Sie sie über SSMS/dtutil in Dateisystme/Dateifreigaben/Azure Files, oder stellen Sie sie in Dateifreigaben/Azure Files über dtinstall/dtutil/manuelle Kopie erneut bereit, oder bewahren Sie sie in Dateisystemen für den Zugriff über VNet/Selbstgehostete IR. Weitere Informationen finden Sie unter „dtutil-Hilfsprogramm“. Weitere Informationen finden Sie unter [dtutil-Hilfsprogramm](/sql/integration-services/dtutil-utility).|Konvertieren Sie sie mithilfe von Skripts/SSMS/ADF-Portal in ADF-Pipelines/-Aktivitäten/-Trigger. Weitere Informationen finden Sie unter [SSMS-Zeitplanungsfunktion](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Azure Data Factory](./introduction.md)
- [Datenbankmigrations-Assistent](/sql/dma/dma-overview)
- [Migrieren von SSIS-Workloads per Lift & Shift in die Cloud](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [DevOps-Tools für SQL Server Integration Services (SSIS)](/sql/integration-services/devops/ssis-devops-overview)
- [Migrieren von SSIS-Paketen zu Azure SQL Managed Instance](../dms/how-to-migrate-ssis-packages-managed-instance.md)
- [Erneutes Bereitstellen von Paketen für Azure SQL-Datenbank](../dms/how-to-migrate-ssis-packages.md)

- [Lokaler Datenzugriff von Azure-SSIS Integration Runtime](https://techcommunity.microsoft.com/t5/sql-server-integration-services/vnet-or-no-vnet-secure-data-access-from-ssis-in-azure-data/ba-p/1062056)
- [Anpassen des Setups für eine Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)
- [Zugreifen auf Datenspeicher und Dateifreigaben mit Windows-Authentifizierung in SSIS-Paketen in Azure](ssis-azure-connect-with-windows-auth.md)
- [Verwenden von Authentifizierung der verwalteten Identität](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
- [Verwenden von Azure Key Vault](store-credentials-in-key-vault.md)
- [Konfigurieren der Azure-SSIS-Integration Runtime für hohe Leistung](configure-azure-ssis-integration-runtime-performance.md)
- [Starten und Beenden von Azure-SSIS Integration Runtimes nach einem Zeitplan](how-to-schedule-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen von in Azure bereitgestellten SSIS-Paketen](/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Ausführen von in Azure bereitgestellten SSIS-Paketen](/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Überwachen von Azure-SSIS Integration Runtime](./monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Planen der Ausführung von in Azure bereitgestellten SSIS-Paketen](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)