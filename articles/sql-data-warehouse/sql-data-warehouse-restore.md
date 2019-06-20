---
title: Wiederherstellen einer Azure SQL Data Warehouse-Instanz | Microsoft-Dokumentation
description: Anleitung zum Wiederherstellen einer Azure SQL Data Warehouse-Instanz.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: ebbcbcc3d0934800980b7d8e00895b11ff2747b7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60310424"
---
# <a name="restoring-azure-sql-data-warehouse"></a>Wiederherstellen von Azure SQL Data Warehouse 
In diesem Artikel erfahren Sie, wie Sie die folgenden Aufgaben im Azure-Portal und mithilfe von PowerShell ausführen:

- Erstellen eines Wiederherstellungspunkts
- Wiederherstellung anhand eines automatischen oder benutzerdefinierten Wiederherstellungspunkts
- Wiederherstellung anhand einer gelöschten Datenbank
- Wiederherstellung anhand einer Geosicherung
- Erstellen einer Kopie Ihrer Data Warehouse-Instanz mithilfe eines benutzerdefinierten Wiederherstellungspunkts

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Überprüfen Sie Ihre DTU-Kapazität.** Jedes SQL Data Warehouse wird von einer SQL Server-Instanz gehostet (z.B. myserver.database.windows.net), die über ein Standard-DTU-Kontingent verfügt.  Bevor Sie eine SQL Data Warehouse-Instanz wiederherstellen können, überprüfen Sie, ob Ihre SQL Server-Instanz über ein ausreichendes DTU-Kontingent für die Datenbankwiederherstellung verfügt. Informationen zum Berechnen des DTU-Bedarfs bzw. zur Anforderung weiterer DTUs finden Sie unter [Anfordern einer DTU-Kontingentänderung][Request a DTU quota change].

## <a name="restore-through-powershell"></a>Wiederherstellung über PowerShell

## <a name="install-powershell"></a>Installieren von PowerShell
Damit Sie Azure PowerShell mit SQL Data Warehouse verwenden können, müssen Sie Azure PowerShell installieren.  Sie können die Version überprüfen, indem Sie **Get-Module -ListAvailable -Name Az**ausführen. Weitere Informationen zum Installieren der neuesten Version finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][How to install and configure Azure PowerShell].

## <a name="restore-an-active-or-paused-database-using-powershell"></a>Wiederherstellen einer aktiven oder angehaltenen Datenbank mithilfe von PowerShell
Verwenden Sie das PowerShell-Cmdlet [Restore-AzSqlDatabase][Restore-AzSqlDatabase], um eine Datenbank auf der Grundlage eines Wiederherstellungspunkts wiederherzustellen.

1. Öffnen Sie Windows PowerShell.

2. Stellen Sie eine Verbindung mit Ihrem Azure-Konto her, und listen Sie alle Abonnements auf, die Ihrem Konto zugeordnet sind.

3. Wählen Sie das Abonnement aus, in dem die wiederherzustellende Datenbank enthalten ist.

4. Listen Sie die Wiederherstellungspunkte für die Datenbank auf.

5. Wählen Sie den gewünschten Wiederherstellungspunkt mit RestorePointCreationDate aus.

   > [!NOTE]
   > Bei der Wiederherstellung können Sie einen anderen ServiceObjectiveName (DWU) oder einen Server angeben, der in einer anderen Region vorhanden ist.

6. Stellen Sie die Datenbank mit dem gewünschten Wiederherstellungspunkt wieder her.

7. Überprüfen Sie, ob die wiederhergestellte Datenbank online ist.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> Nach Abschluss der Wiederherstellung können Sie Ihre wiederhergestellte Datenbank konfigurieren. Befolgen Sie hierzu die Anleitung [Konfigurieren der Datenbank nach der Wiederherstellung][Configure your database after recovery].
>

## <a name="copy-your-data-warehouse-with-user-defined-restore-points-using-powershell"></a>Kopieren Ihrer Data Warehouse-Datenbank mit benutzerdefinierten Wiederherstellungspunkten mithilfe von PowerShell
Verwenden Sie das PowerShell-Cmdlet [Restore-AzSqlDatabase][Restore-AzSqlDatabase], um eine Datenbank auf der Grundlage eines benutzerdefinierten Wiederherstellungspunkts wiederherzustellen.

1. Öffnen Sie Windows PowerShell.
2. Stellen Sie eine Verbindung mit Ihrem Azure-Konto her, und listen Sie alle Abonnements auf, die Ihrem Konto zugeordnet sind.
3. Wählen Sie das Abonnement aus, in dem die wiederherzustellende Datenbank enthalten ist.
4. Erstellen eines Wiederherstellungspunkts für eine sofortige Kopie Ihrer Datenbank
5. Benennen Sie Ihre Datenbank temporär um.
6. Rufen Sie über den angegebenen RestorePointLabel-Wert den letzten Wiederherstellungspunkt ab.
7. Rufen Sie die Ressourcen-ID der Datenbank ab, um die Wiederherstellung einzuleiten.
8. Stellen Sie die Datenbank mit dem gewünschten Wiederherstellungspunkt wieder her.
9. Überprüfen Sie, ob die wiederhergestellte Datenbank online ist.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$TempDatabaseName = "<YourTemporaryDatabaseName>"
$Label = "<YourRestorePointLabel"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

# Rename the database to a temporary name
Set-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -NewName $TempDatabaseName

# Get the most recent restore point with the specified label
$LabelledRestorePoint = Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName | where {$_.RestorePointLabel -eq $Label} | sort {$_.RestorePointCreationDate} | select -Last 1

# Get the resource id of the database
$ResourceId = (Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName).ResourceId

# Restore the database to its original name from the labelled restore point from the temporary database
$RestoredDatabase = Restore-AzSqlDatabase -FromPointInTimeBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ResourceId $ResourceId -PointInTime $LabelledRestorePoint.RestorePointCreationDate -TargetDatabaseName $DatabaseName

# Verify the status of restored database
$RestoredDatabase.status

# The original temporary database can be deleted at this point

```

## <a name="restore-a-deleted-database-using-powershell"></a>Wiederherstellen einer gelöschten Datenbank mithilfe von PowerShell
Verwenden Sie das Cmdlet [Restore-AzSqlDatabase][Restore-AzSqlDatabase], um eine gelöschte Datenbank wiederherzustellen.

1. Öffnen Sie Windows PowerShell.
2. Stellen Sie eine Verbindung mit Ihrem Azure-Konto her, und listen Sie alle Abonnements auf, die Ihrem Konto zugeordnet sind.
3. Wählen Sie das Abonnement aus, das die gelöschte Datenbank enthält, die wiederhergestellt werden soll.
4. Rufen Sie die spezifische gelöschte Datenbank ab.
5. Stellen Sie die gelöschte Datenbank wieder her.
6. Überprüfen Sie, ob die wiederhergestellte Datenbank online ist.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> Nach Abschluss der Wiederherstellung können Sie Ihre wiederhergestellte Datenbank konfigurieren. Befolgen Sie hierzu die Anleitung [Konfigurieren der Datenbank nach der Wiederherstellung][Configure your database after recovery].
>

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Wiederherstellen von einer geografischen Azure-Region aus mithilfe von PowerShell
Verwenden Sie das Cmdlet [Restore-AzSqlDatabase][Restore-AzSqlDatabase], um eine Datenbank wiederherzustellen.

> [!NOTE]
> Sie können eine Geowiederherstellung nach Gen2 durchführen! Geben Sie zu diesem Zweck als optionalen Parameter einen ServiceObjectiveName-Wert für Gen2 ein (z.B. DW1000**c**).
>

1. Öffnen Sie Windows PowerShell.
2. Stellen Sie eine Verbindung mit Ihrem Azure-Konto her, und listen Sie alle Abonnements auf, die Ihrem Konto zugeordnet sind.
3. Wählen Sie das Abonnement aus, in dem die wiederherzustellende Datenbank enthalten ist.
4. Rufen Sie die Datenbank ab, die Sie wiederherstellen möchten.
5. Erstellen Sie die Wiederherstellungsanforderung für die Datenbank.
6. Überprüfen Sie den Status der mittels Geowiederherstellung wiederhergestellten Datenbank.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Informationen zum Konfigurieren der Datenbank nach Abschluss der Wiederherstellung finden Sie unter [Konfigurieren der Datenbank nach der Wiederherstellung][Configure your database after recovery].
>

Für die wiederhergestellte Datenbank ist TDE aktiviert, wenn für die Quelldatenbank TDE aktiviert ist.

## <a name="restore-through-the-azure-portal"></a>Wiederherstellung über das Azure-Portal

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Erstellen eines benutzerdefinierten Wiederherstellungspunkts im Azure-Portal
1. Melden Sie sich beim [Azure-Portal][Azure portal] an.

2. Navigieren Sie zu der SQL Data Warehouse-Instanz, für die Sie einen Wiederherstellungspunkt erstellen möchten.

3. Wählen Sie oben im Blatt „Übersicht“ die Option **Neuer Wiederherstellungspunkt** aus.

    ![Neuer Wiederherstellungspunkt](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)

4. Geben Sie einen Namen für Ihren Wiederherstellungspunkt an.

    ![Name für den Wiederherstellungspunkt](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Wiederherstellen einer aktiven oder angehaltenen Datenbank im Azure-Portal
1. Melden Sie sich beim [Azure-Portal][Azure portal] an.
2. Navigieren Sie zu der SQL Data Warehouse-Instanz, mit der Sie die Wiederherstellung durchführen möchten.
3. Wählen Sie oben im Blatt „Übersicht“ die Option **Wiederherstellen** aus.

    ![ Wiederherstellung – Übersicht](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)

4. Wählen Sie entweder die Option **Automatische Wiederherstellungspunkte** oder **Benutzerdefinierte Wiederherstellungspunkte** aus.

    ![Automatische Wiederherstellungspunkte](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)

5. Wenn Sie sich für benutzerdefinierte Wiederherstellungspunkte entschieden haben, **wählen Sie einen Wiederherstellungspunkt aus**, oder **erstellen Sie einen neuen benutzerdefinierten Wiederherstellungspunkt**.

    ![Benutzerdefinierte Wiederherstellungspunkte](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Wiederherstellen einer gelöschten Datenbank im Azure-Portal
1. Melden Sie sich beim [Azure-Portal][Azure portal] an.
2. Navigieren Sie zu der SQL Server-Instanz, auf der Ihre gelöschte Datenbank gehostet wurde.
3. Wählen Sie im Inhaltsverzeichnis das Symbol für gelöschte Datenbanken aus.

    ![Gelöschte Datenbanken](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_0.png)

4. Wählen Sie die gelöschte Datenbank aus, die Sie wiederherstellen möchten.

    ![Auswahl von gelöschten Datenbanken](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_1.png)

5. Geben Sie einen neuen Datenbanknamen ein.

    ![Angeben eines Datenbanknamens](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_2.png)

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
