---
title: Benutzerdefinierte Wiederherstellungspunkte
description: Vorgehensweise zum Erstellen eines Wiederherstellungspunkts für den dedizierten SQL-Pool (früher SQL DW)
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 097a3132208eee98b3f95291e414263e637bc265
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96545586"
---
# <a name="user-defined-restore-points-for-a-dedicated-sql-pool-formerly-sql-dw"></a>Benutzerdefinierte Wiederherstellungspunkte für einen dedizierten SQL-Pool (früher SQL DW)

In diesem Artikel erfahren Sie, wie Sie mithilfe von PowerShell und über das Azure-Portal einen neuen benutzerdefinierten Wiederherstellungspunkt für einen dedizierten SQL-Pool (früher SQL DW) in Azure Synapse Analytics erstellen.

## <a name="create-user-defined-restore-points-through-powershell"></a>Erstellen benutzerdefinierter Wiederherstellungspunkte mit PowerShell

Verwenden Sie zum Erstellen eines benutzerdefinierten Wiederherstellungspunkts das PowerShell-Cmdlet [New-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

1. Bevor Sie beginnen, müssen Sie [Azure PowerShell installieren](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Öffnen Sie PowerShell.
3. Stellen Sie eine Verbindung mit Ihrem Azure-Konto her, und listen Sie alle Abonnements auf, die Ihrem Konto zugeordnet sind.
4. Wählen Sie das Abonnement aus, in dem die wiederherzustellende Datenbank enthalten ist.
5. Erstellen Sie einen Wiederherstellungspunkt für eine sofortige Kopie Ihrer Data Warehouse-Instanz.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. Sehen Sie sich die Liste aller vorhandenen Wiederherstellungspunkte an.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Erstellen benutzerdefinierter Wiederherstellungspunkte mit dem Azure-Portal

Benutzerdefinierte Wiederherstellungspunkte können auch mit dem Azure-Portal erstellt werden.

1. Melden Sie sich bei Ihrem [Azure-Portal](https://portal.azure.com/)-Konto an.

2. Navigieren Sie zum dedizierten SQL-Pool (früher SQL DW), für den Sie einen Wiederherstellungspunkt erstellen möchten.

3. Wählen Sie im linken Bereich **Übersicht** aus, und wählen Sie **+ Neuer Wiederherstellungspunkt** aus. Wenn die Schaltfläche „Neuer Wiederherstellungspunkt“ nicht aktiviert wurde, stellen Sie sicher, dass der dedizierte SQL-Pool (früher SQL DW) nicht angehalten wird.

    ![Neuer Wiederherstellungspunkt](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Geben Sie einen Namen für den benutzerdefinierten Wiederherstellungspunkt ein, und klicken Sie auf **Übernehmen**. Für benutzerdefinierte Wiederherstellungspunkte gilt eine standardmäßige Aufbewahrungsdauer von sieben Tagen.

    ![Name für den Wiederherstellungspunkt](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Nächste Schritte

- [Wiederherstellen eines vorhandenen dedizierten SQL-Pools (früher SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Wiederherstellen eines gelöschten dedizierten SQL-Pools (früher SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
- [Wiederherstellen eines dedizierten SQL-Pools (früher SQL DW) aus einer Geosicherung](sql-data-warehouse-restore-from-geo-backup.md)
