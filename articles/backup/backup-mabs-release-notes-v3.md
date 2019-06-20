---
title: Anmerkungen zu dieser Version für Microsoft Azure Backup Server v3
description: Dieser Artikel enthält Informationen über die bekannten Probleme und Problemumgehungen für MABS v3.
services: backup
author: JYOTHIRMAISURI
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 11/22/2018
ms.author: v-jysur
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: d37245d7eed39ee9d219578db9e0a50d758ba9a2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60499697"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Anmerkungen zu dieser Version für Microsoft Azure Backup Server
Dieser Artikel enthält Informationen über die bekannten Probleme und Problemumgehungen für Microsoft Azure Backup Server (MABS) V3.

##  <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Sicherung und Wiederherstellung ist für gruppierte Workloads nicht möglich

**Beschreibung:** Beim Sichern/Wiederherstellen gruppierter Datenquellen wie z.B. Hyper-V-Cluster oder SQL-Cluster (SQL AlwaysOn) oder Exchange in einer Datenbankverfügbarkeitsgruppe (Database Availability Group, DAG) tritt nach dem Upgrade von MABS V2 auf MABS V3 ein Fehler auf.

**Problemumgehung:** Um dies zu verhindern, öffnen Sie SQL Server Management Studio (SSMS), und führen Sie das folgende SQL-Skript für die DPM-Datenbank aus:


    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO


##  <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Beim Upgrade auf MABS V3 tritt im russischen Gebietsschema ein Fehler auf

**Beschreibung:** Beim Upgrade von MABS V2 auf MABS V3 tritt im russischen Gebietsschema ein Fehler mit dem Fehlercode **4387** auf.

**Problemumgehung:** Führen Sie zum Durchführen des Upgrades auf MABS V3 mithilfe des russischen Installationspakets die folgenden Schritte aus:

1.  [Sichern](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) Sie Ihre SQL-Datenbank, und deinstallieren Sie MABS V2 (wählen Sie das Beibehalten der geschützten Daten während der Deinstallation aus).
2.  Führen Sie ein Upgrade auf SQL 2017 (Enterprise) durch, und deinstallieren Sie die Berichterstellung als Teil des Upgrades.
3. [Installieren](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) Sie SQL Server Reporting Services (SSRS).
4.  [Installieren](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017#ssms-installation-tips-and-issues-ssms-1791) Sie SQL Server Management Studio (SSMS).
5.  Konfigurieren Sie die Berichterstellung mit den Parametern, wie in [SSRS-Konfiguration mit SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs) dokumentiert.
6.  [Installieren](backup-azure-microsoft-azure-backup.md) Sie MABS V3.
7. Führen Sie das [Wiederherstellen](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) von SQL mithilfe von SSMS durch, und führen Sie anschließend das DPM-Synchronisierungstool wie [hier](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10)) beschrieben aus.
8.  Aktualisieren Sie die Eigenschaft „DataBaseVersion“ in der dbo.tbl_DLS_GlobalSetting-Tabelle mit folgendem Befehl:

        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'


9.  Starten Sie den MSDPM-Dienst.


## <a name="next-steps"></a>Nächste Schritte

[Neuerungen in MABS V3](backup-mabs-whats-new-mabs.md)
