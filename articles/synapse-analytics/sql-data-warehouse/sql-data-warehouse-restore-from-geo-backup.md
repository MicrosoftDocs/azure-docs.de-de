---
title: Wiederherstellen eines dedizierten SQL-Pools aus einer Geosicherung
description: Schrittanleitung für die Geowiederherstellung eines dedizierten SQL-Pools in Azure Synapse Analytics
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 4683bd84873506483209f4a0eb3751a1b163ed48
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449874"
---
# <a name="geo-restore-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Geowiederherstellung eines dedizierten SQL-Pools in Azure Synapse Analytics

In diesem Artikel erfahren Sie, wie Sie Ihren dedizierten SQL-Pool (früher SQL Data Warehouse) aus einer Geosicherung über das Azure-Portal und PowerShell wiederherstellen.

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Überprüfen Sie Ihre DTU-Kapazität.** Jeder dedizierte SQL-Pool (früher SQL Data Warehouse) wird von einem [logischen SQL-Server](../../azure-sql/database/logical-servers.md) gehostet (z. B. myserver.database.windows.net), der über ein DTU-Standardkontingent verfügt. Vergewissern Sie sich, dass die SQL Server-Instanz über ein ausreichendes DTU-Kontingent für die Datenbankwiederherstellung verfügt. Informationen zum Berechnen des DTU-Bedarfs bzw. zur Anforderung weiterer DTUs finden Sie unter [Anfordern einer DTU-Kontingentänderung](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Wiederherstellen von einer geografischen Azure-Region mithilfe von PowerShell

Verwenden Sie für die Wiederherstellung aus einer Geosicherung die Cmdlets [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) und [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

> [!NOTE]
> Sie können eine Geowiederherstellung nach Gen2 durchführen! Geben Sie zu diesem Zweck als optionalen Parameter einen ServiceObjectiveName-Wert für Gen2 ein (z.B. DW1000 **c**).
>

1. Bevor Sie beginnen, müssen Sie [Azure PowerShell installieren](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Öffnen Sie PowerShell.
3. Stellen Sie eine Verbindung mit Ihrem Azure-Konto her, und listen Sie alle Abonnements auf, die Ihrem Konto zugeordnet sind.
4. Wählen Sie das Abonnement aus, in dem das wiederherzustellende Data Warehouse enthalten ist.
5. Rufen Sie das Data Warehouse ab, das Sie wiederherstellen möchten.
6. Erstellen Sie die Wiederherstellungsanforderung für das Data Warehouse.
7. Überprüfen Sie den Status des mittels Geowiederherstellung wiederhergestellten das Data Warehouse.
8. Informationen zum Konfigurieren des Data Warehouse nach Abschluss der Wiederherstellung finden Sie unter [Konfigurieren der Datenbank nach der Wiederherstellung]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

Für die wiederhergestellte Datenbank ist TDE aktiviert, wenn für die Quelldatenbank TDE aktiviert ist.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Wiederherstellen von einer geografischen Azure-Region über das Azure-Portal

Führen Sie die folgenden Schritte aus, um einen dedizierten SQL-Pool (früher SQL Data Warehouse) aus einer Geosicherung wiederherzustellen:

1. Melden Sie sich bei Ihrem [Azure-Portal](https://portal.azure.com/)-Konto an.
1. Suchen Sie nach **Dedizierte SQL-Pools (früher SQL Data Warehouse)** .

   ![Neues DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

1. Klicken Sie auf „Hinzufügen“. Geben Sie auf der Registerkarte **Grundlagen** die angeforderten Informationen ein, und klicken Sie auf **Weiter: Zusätzliche Einstellungen**.

   ![Grundlagen](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

1. Wählen Sie für den Parameter **Vorhandenen Daten verwenden** die Option **Sicherung** aus, und wählen Sie dann die entsprechende Sicherung aus den Scrolldownoptionen aus. Klicken Sie auf **Überprüfen und erstellen**.

   ![Sicherung](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

1. Nachdem das Data Warehouse wieder hergestellt wurde, überprüfen Sie, ob der **Status** „Online“ lautet.

## <a name="next-steps"></a>Nächste Schritte

- [Wiederherstellen eines vorhandenen dedizierten SQL-Pools (früher SQL Data Warehouse)](sql-data-warehouse-restore-active-paused-dw.md)
- [Wiederherstellen eines gelöschten dedizierten SQL-Pools (früher SQL Data Warehouse)](sql-data-warehouse-restore-deleted-dw.md)
