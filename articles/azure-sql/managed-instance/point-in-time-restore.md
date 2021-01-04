---
title: Point-in-Time-Wiederherstellung
titleSuffix: Azure SQL Managed Instance
description: Wiederherstellen einer Datenbank in einer verwalteten Azure SQL-Instanz auf den Zustand zu einem früheren Zeitpunkt.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 31be497d017cb60de6f46d7657889c9c1fabef4a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92788348"
---
# <a name="restore-a-database-in-azure-sql-managed-instance-to-a-previous-point-in-time"></a>Wiederherstellen einer Datenbank in einer verwalteten Azure SQL-Instanz auf den Zustand zu einem früheren Zeitpunkt
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Verwenden Sie die Point-in-Time-Wiederherstellung (Point-In-Time Restore, PITR), um eine Datenbank als Kopie einer anderen Datenbank in einem früheren Zustand zu erstellen. In diesem Artikel erfahren Sie, wie Sie eine Point-in-Time-Wiederherstellung einer Datenbank in einer verwalteten Azure SQL-Instanz durchführen.

Die Point-in-Time-Wiederherstellung ist in Wiederherstellungsszenarien hilfreich, die durch Fehler, falsch geladene Daten oder das Löschen wichtiger Daten verursacht wurden. Sie können sie aber auch einfach für Tests oder für die Überwachung verwenden. Sicherungsdateien werden je nach Datenbankeinstellungen für einen Zeitraum zwischen sieben und 35 Tagen aufbewahrt.

Mit der Point-in-Time-Wiederherstellung kann eine Datenbank wiederhergestellt werden:

- aus einer vorhandenen Datenbank.
- aus einer gelöschten Datenbank.
- in derselben verwalteten SQL-Instanz oder in einer anderen verwalteten SQL-Instanz. 

## <a name="limitations"></a>Einschränkungen

Die Point-in-Time-Wiederherstellung in einer verwalteten SQL-Instanz ist mit folgenden Einschränkungen verbunden:

- Bei der Wiederherstellung von einer Instanz der verwalteten SQL-Instanz in eine andere müssen sich beide Instanzen im gleichen Abonnement und in der gleichen Region befinden. Die regions- und abonnementübergreifende Wiederherstellung wird derzeit nicht unterstützt.
- Eine Point-in-Time-Wiederherstellung einer gesamten verwalteten SQL-Instanz ist nicht möglich. Dieser Artikel beschreibt nur die Möglichkeiten: die Point-in-Time-Wiederherstellung einer Datenbank, die in einer verwalteten SQL-Instanz gehostet wird.

> [!WARNING]
> Berücksichtigen Sie die Speichergröße Ihrer verwalteten SQL-Instanz. Abhängig von der Größe der wiederherzustellenden Daten kann es vorkommen, dass der Instanzspeicher nicht ausreicht. Wenn nicht ausreichend Speicherplatz für die wiederhergestellten Daten verfügbar ist, müssen Sie anders vorgehen.

Die folgende Tabelle enthält Point-in-Time-Wiederherstellungsszenarien für verwaltete SQL-Instanzen:

|           |Wiederherstellen vorhandener Datenbanken in derselben Instanz der verwalteten SQL-Instanz| Wiederherstellen vorhandener Datenbanken in einer anderen verwalteten SQL-Instanz|Wiederherstellen gelöschter Datenbanken in der gleichen verwalteten SQL-Instanz|Wiederherstellen gelöschter Datenbanken in einer anderen verwalteten SQL-Instanz|
|:----------|:----------|:----------|:----------|:----------|
|**Azure portal**| Ja|Nein |Ja|Nein|
|**Azure-Befehlszeilenschnittstelle**|Ja |Ja |Nein|Nein|
|**PowerShell**| Ja|Ja |Ja|Ja|

## <a name="restore-an-existing-database"></a>Wiederherstellen einer vorhandenen Datenbank

Eine vorhandene Datenbank kann über das Azure-Portal, mithilfe von PowerShell oder über die Azure CLI in der gleichen verwalteten SQL-Instanz wiederhergestellt werden. Wenn Sie eine Datenbank in einer anderen verwalteten SQL-Instanz wiederherstellen möchten, verwenden Sie PowerShell oder die Azure CLI, damit Sie die Eigenschaften für die verwaltete SQL-Zielinstanz und die Ressourcengruppe festlegen können. Wenn Sie diese Parameter nicht festlegen, wird die Datenbank standardmäßig in der vorhandenen verwalteten SQL-Instanz wiederhergestellt. Das Azure-Portal unterstützt derzeit keine Wiederherstellung in einer anderen verwalteten SQL-Instanz.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
2. Navigieren Sie zu Ihrer verwalteten SQL-Instanz, und wählen Sie die wiederherzustellende Datenbank aus.
3. Wählen Sie auf der Datenbankseite die Option **Wiederherstellen** aus:

    ![Wiederherstellen einer Datenbank mithilfe des Azure-Portals](./media/point-in-time-restore/restore-database-to-mi.png)

4. Wählen Sie auf der Seite **Wiederherstellen** den Zeitpunkt (Datum und Uhrzeit) für die Datenbankwiederherstellung aus.
5. Wählen Sie **Bestätigen** aus, um Ihre Datenbank wiederherzustellen. Daraufhin wird der Wiederherstellungsprozess gestartet. Hierbei wird eine neue Datenbank erstellt und mit Daten aus der ursprünglichen Datenbank zum angegebenen Zeitpunkt aufgefüllt. Weitere Informationen zum Wiederherstellungsprozess finden Sie unter [Wiederherstellungszeit](../database/recovery-using-backups.md#recovery-time).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sollte Azure PowerShell noch nicht installiert sein, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) weitere Informationen.

Wenn Sie die Datenbank unter Verwendung von PowerShell wiederherstellen möchten, legen Sie die Werte für die Parameter im folgenden Befehl fest. Führen Sie anschließend den folgenden Befehl aus:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

Wenn Sie die Datenbank in einer anderen verwalteten SQL-Instanz wiederherstellen möchten, geben Sie auch die Namen der Zielressourcengruppe und der verwalteten SQL-Zielinstanz an:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Ausführliche Informationen finden Sie unter [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Sollte die Azure CLI noch nicht installiert sein, lesen Sie [Installieren der Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Wenn Sie die Datenbank unter Verwendung der Azure CLI wiederherstellen möchten, legen Sie die Werte für die Parameter im folgenden Befehl fest. Führen Sie anschließend den folgenden Befehl aus:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Wenn Sie die Datenbank in einer anderen verwalteten SQL-Instanz wiederherstellen möchten, geben Sie auch die Namen der Zielressourcengruppe und der verwalteten SQL-Zielinstanz an:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Eine ausführliche Erläuterung der verfügbaren Parameter finden Sie in der [CLI-Dokumentation zum Wiederherstellen einer Datenbank in einer verwalteten SQL-Instanz](/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore).

---

## <a name="restore-a-deleted-database"></a>Wiederherstellung einer gelöschten Datenbank

Eine gelöschte Datenbank kann nur über PowerShell oder das Azure-Portal wiederhergestellt werden. Zum Wiederherstellen einer gelöschten Datenbank in derselben Instanz verwenden Sie entweder das Azure-Portal oder PowerShell. Zum Wiederherstellen einer gelöschten Datenbank in einer anderen Instanz verwenden Sie PowerShell. 

### <a name="portal"></a>Portal 


Öffnen Sie zum Wiederherstellen einer verwalteten Datenbank über das Azure-Portal die Übersichtsseite der verwalteten SQL-Instanzen, und wählen Sie **Gelöschte Datenbanken** aus. Wählen Sie eine gelöschte Datenbank aus, die Sie wiederherstellen möchten, und geben Sie den Namen für die neue Datenbank ein, die mit den aus der Sicherung wiederhergestellten Daten erstellt wird.

  ![Screenshot: Wiederherstellen einer gelöschten Azure SQL-Datenbank-Instanz](./media/point-in-time-restore/restore-deleted-sql-managed-instance-annotated.png)

.. /.. /sql-database

### <a name="powershell"></a>PowerShell

Wenn Sie eine Datenbank in derselben Instanz wiederherstellen möchten, aktualisieren Sie die Parameterwerte, und führen Sie dann den folgenden PowerShell-Befehl aus: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

Wenn Sie die Datenbank in einer anderen verwalteten SQL-Instanz wiederherstellen möchten, geben Sie auch die Namen der Zielressourcengruppe und der verwalteten SQL-Zielinstanz an:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>Überschreiben einer vorhandenen Datenbank

Wenn Sie eine vorhandene Datenbank überschreiben möchten, ist außerdem Folgendes erforderlich:

1. Löschen Sie die vorhandene Datenbank, die Sie überschreiben möchten.
2. Ändern Sie den Namen der mittels Point-in-Time-Wiederherstellung wiederhergestellten Datenbank in den Namen der gelöschten Datenbank.

### <a name="drop-the-original-database"></a>Löschen der ursprünglichen Datenbank

Sie können die Datenbank über das Azure-Portal, über PowerShell oder über T-SQL löschen.

Sie können die Datenbank auch löschen, indem Sie eine direkte Verbindung mit der verwalteten SQL-Instanz herstellen, SQL Server Management Studio (SSMS) starten und anschließend den folgenden Transact-SQL-Befehl (T-SQL) ausführen:

```sql
DROP DATABASE WorldWideImporters;
```

Verwenden Sie eine der folgenden Methoden, um eine Verbindung mit der Datenbank in der verwalteten SQL-Instanz herzustellen:

- [SSMS/Azure Data Studio über einen virtuellen Azure-Computer](./connect-vm-instance-configure.md)
- [Point-to-Site](./point-to-site-p2s-configure.md)
- [Öffentlicher Endpunkt](./public-endpoint-configure.md)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie im Azure-Portal die Datenbank der verwalteten SQL-Instanz und anschließend die Option **Löschen** aus.

   ![Löschen einer Datenbank über das Azure-Portal](./media/point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie den folgenden PowerShell-Befehl, um eine vorhandene Datenbank aus einer verwalteten SQL-Instanz zu löschen:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den folgenden Azure CLI-Befehl, um eine vorhandene Datenbank aus einer verwalteten SQL-Instanz zu löschen:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Ändern des Namens der neuen Datenbank, damit sie mit dem ursprünglichen Datenbanknamen übereinstimmt

Stellen Sie eine direkte Verbindung mit der verwalteten SQL-Instanz her, und starten Sie SQL Server Management Studio. Führen Sie anschließend die folgende T-SQL-Abfrage (Transact-SQL) aus. Die Abfrage ändert den Namen der wiederhergestellten Datenbank in den Namen der gelöschten Datenbank, die Sie überschreiben möchten.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Verwenden Sie eine der folgenden Methoden, um eine Verbindung mit der Datenbank in der verwalteten SQL-Instanz herzustellen:

- [Virtueller Azure-Computer](./connect-vm-instance-configure.md)
- [Point-to-Site](./point-to-site-p2s-configure.md)
- [Öffentlicher Endpunkt](./public-endpoint-configure.md)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [Automatisierte Sicherungen](../database/automated-backups-overview.md).