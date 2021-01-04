---
title: Wiederherstellen eines vorhandenen dedizierten SQL-Pools
description: Schrittanleitung für die Wiederherstellung eines bestehenden dedizierten SQL-Pools in Azure Synapse Analytics.
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
ms.openlocfilehash: d3c84f7d951db715d26042f524f385826197e076
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450004"
---
# <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Wiederherstellen eines vorhandenen dedizierten SQL-Pools (früher SQL DW)

In diesem Artikel erfahren Sie, wie Sie einen vorhandenen dedizierten SQL-Pool (früher SQL DW) über das Azure-Portal und mit PowerShell wiederherstellen können.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Überprüfen Sie Ihre DTU-Kapazität.** Jeder Pool wird von einer [logischen SQL Server-Instanz](../../azure-sql/database/logical-servers.md) gehostet (z. B. myserver.database.windows.net), die über ein Standard-DTU-Kontingent verfügt. Vergewissern Sie sich, dass der Server über ein ausreichendes DTU-Kontingent für die Datenbankwiederherstellung verfügt. Informationen zum Berechnen des DTU-Bedarfs bzw. zur Anforderung weiterer DTUs finden Sie unter [Anfordern einer DTU-Kontingentänderung](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Voraussetzungen

1. Bevor Sie beginnen, müssen Sie [Azure PowerShell installieren](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Sie benötigen einen vorhandenen Wiederherstellungspunkt. Wenn Sie eine neue Wiederherstellung erstellen möchten, lesen Sie [das Tutorial zum Erstellen eines neuen benutzerdefinierten Wiederherstellungspunkts](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-powershell"></a>Wiederherstellen eines vorhandenen dedizierten SQL-Pools (früher SQL DW) mit PowerShell

Mit dem PowerShell-Cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) können Sie einen vorhandenen dedizierten SQL-Pool (früher SQL DW) anhand eines Wiederherstellungspunkts wiederherstellen.

1. Öffnen Sie PowerShell.

2. Stellen Sie eine Verbindung mit Ihrem Azure-Konto her, und listen Sie alle Abonnements auf, die Ihrem Konto zugeordnet sind.

3. Wählen Sie das Abonnement aus, in dem die wiederherzustellende Datenbank enthalten ist.

4. Listen Sie die Wiederherstellungspunkte für den dedizierten SQL-Pool (früher SQL DW) auf.

5. Wählen Sie den gewünschten Wiederherstellungspunkt mit RestorePointCreationDate aus.

6. Stellen Sie den dedizierten SQL-Pool (früher SQL DW) anhand des gewünschten Wiederherstellungspunkts mit dem PowerShell-Cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) wieder her.

    1. Zum Wiederherstellen des dedizierten SQL-Pools (früher SQL DW) auf einem anderen Server müssen Sie den Namen des anderen Servers angeben.  Dieser Server kann sich auch in einer anderen Ressourcengruppe und Region befinden.
    2. Zum Wiederherstellen in einem anderen Abonnement verwenden Sie die Schaltfläche „Verschieben“, um den Server in ein anderes Abonnement zu verschieben.

7. Überprüfen Sie, ob der wiederhergestellte dedizierte SQL-Pool (früher SQL DW) online ist.

8. Nach Abschluss der Wiederherstellung können Sie Ihren wiederhergestellten dedizierten SQL-Pool (früher SQL DW) konfigurieren. Befolgen Sie hierzu die Anleitung [Konfigurieren der Datenbank nach der Wiederherstellung](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

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

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different server
#$TargetResourceGroupName = $Database.ResourceGroupName # for restoring to different server in same resourcegroup 
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-the-azure-portal"></a>Wiederherstellen eines vorhandenen dedizierten SQL-Pools (früher SQL DW) über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Navigieren Sie zum dedizierten SQL-Pool, mit dem Sie die Wiederherstellung durchführen möchten.
3. Wählen Sie oben im Blatt „Übersicht“ die Option **Wiederherstellen** aus.

    ![ Wiederherstellung – Übersicht](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Wählen Sie entweder die Option **Automatische Wiederherstellungspunkte** oder **Benutzerdefinierte Wiederherstellungspunkte** aus. Wenn der dedizierte SQL-Pool (früher SQL DW) keine automatischen Wiederherstellungspunkte enthält, warten Sie einige Stunden, oder erstellen Sie einen benutzerdefinierten Wiederherstellungspunkt, bevor Sie eine Wiederherstellung durchführen. Wählen Sie für „Benutzerdefinierte Wiederherstellungspunkte“ einen vorhandenen benutzerdefinierten Wiederherstellungspunkt aus, oder erstellen Sie einen neuen. Für **Server** können Sie einen Server in einer anderen Ressourcengruppe und Region auswählen oder einen neuen erstellen. Nachdem Sie alle Parameter angegeben haben, klicken Sie auf **Überprüfen + wiederherstellen**.

    ![Automatische Wiederherstellungspunkte](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Nächste Schritte

- [Wiederherstellen eines gelöschten dedizierten SQL-Pools (früher SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
- [Wiederherstellen eines dedizierten SQL-Pools (früher SQL DW) aus einer Geosicherung](sql-data-warehouse-restore-from-geo-backup.md)
