---
title: Wiederherstellen eines gelöschten dedizierten SQL-Pools (früher SQL Data Warehouse)
description: Anleitung für das Wiederherstellen eines gelöschten dedizierten SQL-Pools in Azure Synapse Analytics.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7264791654bf1b646338f0d429930b63f0cc3a06
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449924"
---
# <a name="restore-a-deleted-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Wiederherstellen eines gelöschten dedizierten SQL-Pools (früher SQL Data Warehouse) in Azure Synapse Analytics

In diesem Artikel erfahren Sie, wie Sie einen dedizierten SQL-Pool (früher SQL Data Warehouse) im Azure-Portal oder mithilfe von PowerShell wiederherstellen.

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Überprüfen Sie Ihre DTU-Kapazität.** Jeder dedizierte SQL-Pool (früher SQL Data Warehouse) wird von einem [logischen SQL-Server](../../azure-sql/database/logical-servers.md) gehostet (z. B. myserver.database.windows.net), der über ein DTU-Standardkontingent verfügt.  Vergewissern Sie sich, dass der Server über ein ausreichendes DTU-Kontingent für die Datenbankwiederherstellung verfügt. Informationen zum Berechnen des DTU-Bedarfs bzw. zur Anforderung weiterer DTUs finden Sie unter [Anfordern einer DTU-Kontingentänderung](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Wiederherstellen einer gelöschten Data Warehouse-Instanz mit PowerShell

Mit dem Cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) können Sie einen gelöschten dedizierten SQL-Pool (früher SQL Data Warehouse) wiederherstellen. Wenn der entsprechende Server ebenfalls gelöscht wurde, können Sie diese Data Warehouse-Instanz nicht wiederherstellen.

1. Bevor Sie beginnen, müssen Sie [Azure PowerShell installieren](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Öffnen Sie PowerShell.
3. Stellen Sie eine Verbindung mit Ihrem Azure-Konto her, und listen Sie alle Abonnements auf, die Ihrem Konto zugeordnet sind.
4. Wählen Sie das Abonnement aus, das den gelöschten dedizierten SQL-Pool (früher SQL Data Warehouse) enthält, der wiederhergestellt werden soll.
5. Rufen Sie die spezifische gelöschte Data Warehouse-Instanz ab.
6. Wiederherstellen eines gelöschten dedizierten SQL-Pools (früher SQL Data Warehouse)
    1. Zum Wiederherstellen des dedizierten SQL-Pools (früher SQL Data Warehouse) auf einem anderen Server müssen Sie den Namen des anderen Servers angeben.  Dieser Server kann sich auch in einer anderen Ressourcengruppe und Region befinden.
    1. Für die Wiederherstellung in einem anderen Abonnement verwenden Sie die Schaltfläche [Verschieben](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal), um den Server in ein anderes Abonnement zu verschieben.
7. Überprüfen Sie, ob die wiederhergestellte Data Warehouse-Instanz online ist.
8. Nach Abschluss der Wiederherstellung können Sie Ihre wiederhergestellte Data Warehouse-Instanz konfigurieren. Befolgen Sie hierzu die Anleitung [Konfigurieren der Datenbank nach der Wiederherstellung](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Wiederherstellen einer gelöschten Datenbank im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Navigieren Sie zu dem Server, auf dem Ihre gelöschte Data Warehouse-Instanz gehostet wurde.
3. Wählen Sie im Inhaltsverzeichnis das Symbol für **gelöschte Datenbanken** aus.

    ![Gelöschte Datenbanken](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Wählen Sie die gelöschte Azure Synapse Analytics-Datenbank aus, die Sie wiederherstellen möchten.

    ![Auswahl von gelöschten Datenbanken](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Geben Sie einen neuen **Datenbanknamen** an, und klicken Sie auf **OK**.

    ![Angeben eines Datenbanknamens](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Nächste Schritte

- [Wiederherstellen eines vorhandenen dedizierten SQL-Pools (früher SQL Data Warehouse)](sql-data-warehouse-restore-active-paused-dw.md)
- [Wiederherstellen eines dedizierten SQL-Pools (früher SQL Data Warehouse) aus einer Geosicherung](sql-data-warehouse-restore-from-geo-backup.md)
