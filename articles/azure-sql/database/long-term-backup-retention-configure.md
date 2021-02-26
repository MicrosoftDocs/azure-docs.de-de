---
title: 'Azure SQL-Datenbank: Verwalten der langfristigen Aufbewahrung von Sicherungen'
description: Erfahren Sie, wie Sie mit dem Azure-Portal und PowerShell automatisierte Sicherungen für Azure SQL-Datenbank bis zu 10 Jahre lang in Azure Storage speichern und wiederherstellen.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/16/2020
ms.openlocfilehash: a0653f24eeb0a96c28714d00f1d943dfc7d336db
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979690"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Verwalten der langfristigen Aufbewahrung von Sicherungen in Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

In Azure SQL-Datenbank können Sie eine Datenbank mit einer Richtlinie für die [Langzeitaufbewahrung](long-term-retention-overview.md) (Long-Term Retention, LTR) konfigurieren, um die Datenbanksicherungen automatisch in separaten Azure Blob Storage-Containern bis zu 10 Jahre lang aufzubewahren. Sie können dann mit dem Azure-Portal oder mit PowerShell eine Datenbank anhand dieser Sicherungen wiederherstellen. Sie können die Langzeitaufbewahrung auch für [Azure SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md) konfigurieren. Dieses Feature befindet sich jedoch derzeit in der eingeschränkten öffentlichen Vorschau.

## <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

In den folgenden Abschnitten wird erläutert, wie Sie mithilfe des Azure-Portals die langfristige Aufbewahrung konfigurieren, Sicherungen in der langfristigen Aufbewahrung anzeigen und Sicherungen aus der langfristigen Aufbewahrung wiederherstellen.

### <a name="configure-long-term-retention-policies"></a>Konfigurieren von Richtlinien für die langfristige Aufbewahrung

Sie können SQL-Datenbank [zur Aufbewahrung von automatisierten Sicherungen](long-term-retention-overview.md) für einen längeren Zeitraum konfigurieren, als gemäß der Aufbewahrungsdauer für Ihre Dienstebene vorgesehen ist.

1. Wählen Sie im Azure-Portal Ihre SQL Server-Instanz aus, und klicken Sie dann auf **Sicherungen verwalten**. Aktivieren Sie auf der Registerkarte **Richtlinien konfigurieren** das Kontrollkästchen fürdie Datenbank, für die Sie Richtlinien für die langfristige Sicherungsaufbewahrung festlegen oder ändern möchten. Wenn das Kontrollkästchen neben der Datenbank nicht aktiviert ist, gelten die Änderungen für die Richtlinie nicht für diese Datenbank.  

   ![Link zum Verwalten von Sicherungen](./media/long-term-backup-retention-configure/ltr-configure-ltr.png)

2. Wählen Sie im Bereich **Configure policies** (Richtlinien konfigurieren) aus, ob Sie wöchentliche, monatliche oder jährliche Sicherungen aufbewahren möchten, und geben Sie den jeweiligen Aufbewahrungszeitraum an.

   ![Konfigurieren von Richtlinien](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

3. Klicken Sie auf **Anwenden**, wenn Sie fertig sind.

> [!IMPORTANT]
> Wenn Sie eine Richtlinie für die Langzeitaufbewahrung von Sicherungen aktivieren, kann es bis zu 7 Tage dauern, bevor die erste Sicherung angezeigt wird und wiederhergestellt werden kann. Weitere Informationen dazu, in welchen Intervallen Sicherungskopien für die Langzeitaufbewahrung erstellt werden, finden Sie unter [Langzeitaufbewahrung von Sicherungen](long-term-retention-overview.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Anzeigen von Sicherungen und Wiederherstellen einer Sicherung

Zeigen Sie die Sicherungen an, die für eine bestimmte Datenbank mit einer LTR-Richtlinie aufbewahrt werden, und führen Sie die Wiederherstellung aus diesen Sicherungen aus.

1. Wählen Sie Ihren Server im Azure-Portal aus, und klicken Sie dann auf **Sicherungen verwalten**. Wählen Sie auf der Registerkarte **Verfügbare Sicherungen** die Datenbank aus, für die Sie verfügbare Sicherungen anzeigen möchten.

   ![Auswählen der Datenbank](./media/long-term-backup-retention-configure/ltr-available-backups-select-database.png)

1. Überprüfen Sie im Bereich **Verfügbare Sicherungen** die verfügbaren Sicherungen.

   ![Anzeigen von Sicherungen](./media/long-term-backup-retention-configure/ltr-available-backups.png)

1. Wählen Sie die Sicherung aus, die Sie wiederherstellen möchten, und geben Sie dann den neuen Datenbanknamen an.

   ![Wiederherstellen](./media/long-term-backup-retention-configure/ltr-restore.png)

1. Klicken Sie auf **OK**, um Ihre Datenbank aus der Sicherung in Azure Storage in der neuen Datenbank wiederherzustellen.

1. Klicken Sie auf der Symbolleiste auf das Benachrichtigungssymbol, um den Status des Wiederherstellungsauftrags anzuzeigen.

   ![Status des Wiederherstellungsauftrags](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. Öffnen Sie nach Abschluss des Wiederherstellungsauftrags die Seite **SQL-Datenbanken**, um die neu wiederhergestellte Datenbank anzuzeigen.

> [!NOTE]
> Auf diesem Blatt können Sie mithilfe von SQL Server Management Studio eine Verbindung mit der wiederhergestellten Datenbank herstellen, um erforderliche Aufgaben durchzuführen. Sie können beispielsweise [einen Teil der Daten aus der wiederhergestellten Datenbank extrahieren und in die vorhandene Datenbank kopieren oder die vorhandene Datenbank löschen und die wiederhergestellte Datenbank in den vorhandenen Datenbanknamen umbenennen](recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

In den folgenden Abschnitten wird erläutert, wie Sie mithilfe von PowerShell die langfristige Sicherungsaufbewahrung konfigurieren, Sicherungen in Azure Storage anzeigen und eine Wiederherstellung aus einer Sicherung in Azure Storage ausführen.

### <a name="azure-roles-to-manage-long-term-retention"></a>Azure-Rollen zum Verwalten der langfristigen Aufbewahrung

Für **Get-AzSqlDatabaseLongTermRetentionBackup** und **Restore-AzSqlDatabase** müssen Sie über eine der folgenden Rollen verfügen:

- Rolle „Besitzer des Abonnements“ oder
- Rolle „Mitwirkender von SQL Server“ oder
- Benutzerdefinierte Rolle mit den folgenden Berechtigungen:

   Microsoft.Sql/locations/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read

Für **Remove-AzSqlDatabaseLongTermRetentionBackup** müssen Sie über eine der folgenden Rollen verfügen:

- Rolle „Besitzer des Abonnements“ oder
- Benutzerdefinierte Rolle mit der folgenden Berechtigung:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> Die Rolle „Mitwirkender von SQL Server“ besitzt keine Berechtigung zum Löschen von LTR-Sicherungen.

Azure RBAC-Berechtigungen können im Bereich *Abonnement* oder *Ressourcengruppe* erteilt werden. Für den Zugriff auf LTR-Sicherungen, die zu einem gelöschten Server gehören, muss die Berechtigung jedoch im Bereich *Abonnement* dieses Servers erteilt werden.

- Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

### <a name="create-an-ltr-policy"></a>Erstellen einer LTR-Richtlinie

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Anzeigen von LTR-Richtlinien

In diesem Beispiel wird gezeigt, wie Sie die LTR-Richtlinien auf einem Server auflisten.

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $serverName | `
    Get-AzSqlDatabaseLongTermRetentionPolicy

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup
```

### <a name="clear-an-ltr-policy"></a>Löschen einer LTR-Richtlinie

In diesem Beispiel wird gezeigt, wie Sie eine LTR-Richtlinie aus einer Datenbank löschen.

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Anzeigen von LTR-Sicherungen

Dieses Beispiel zeigt, wie Sie die LTR-Sicherungen auf einem Server auflisten.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Löschen von LTR-Sicherungen

Dieses Beispiel zeigt, wie Sie eine LTR-Sicherung aus der Liste der Sicherungen löschen.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Das Löschen der LTR-Sicherung kann nicht rückgängig gemacht werden. Wenn Sie eine LTR-Sicherung löschen möchten, nachdem der Server oder die Ressourcengruppe gelöscht wurde, müssen Sie über die Berechtigung im Bereich „Abonnement“ verfügen. Sie können Benachrichtigungen zu jedem Löschvorgang in Azure Monitor einrichten, indem Sie nach dem Vorgang „Löscht eine Sicherung zur langfristigen Aufbewahrung“ filtern. Das Aktivitätsprotokoll enthält Informationen dazu, wer die Anforderung vorgenommen hat und wann sie erfolgt ist. Ausführliche Anweisungen finden Sie unter [Erstellen von Aktivitätsprotokollwarnungen](../../azure-monitor/platform/alerts-activity-log.md).

### <a name="restore-from-ltr-backups"></a>Wiederherstellen von LTR-Sicherungen

Dieses Beispiel zeigt, wie Sie eine LTR-Sicherung wiederherstellen. Beachten Sie, dass sich diese Schnittstelle nicht geändert hat, der Parameter für die Ressourcen-ID jetzt jedoch die Ressourcen-ID der LTR-Sicherung erfordert.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Sie müssen über Berechtigungen im Bereich des Abonnements des Servers verfügen, und dieses Abonnement muss aktiv sein, um die Wiederherstellung aus einer LTR-Sicherung nach dem Löschen des Servers oder der Ressourcengruppe durchführen zu können. Sie müssen außerdem den optionalen Parameter -ResourceGroupName auslassen.

> [!NOTE]
> Auf diesem Blatt können Sie mithilfe von SQL Server Management Studio eine Verbindung mit der wiederhergestellten Datenbank herstellen, um erforderliche Aufgaben durchzuführen. Sie können beispielsweise einen Teil der Daten aus der wiederhergestellten Datenbank extrahieren und in die vorhandene Datenbank kopieren oder die vorhandene Datenbank löschen und die wiederhergestellte Datenbank in den vorhandenen Datenbanknamen umbenennen. Siehe [Point-in-Time-Wiederherstellung](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations"></a>Einschränkungen
- Bei der Wiederherstellung mithilfe einer LTR-Sicherung wird die Eigenschaft für die Leseskalierung deaktiviert. Aktualisieren Sie die Datenbank nach ihrer Erstellung, um die Leseskalierung für die wiederhergestellte Datenbank zu aktivieren.
- Neim Wiederherstellen aus einer LTR-Sicherung, die erstellt wurde, als die Datenbank zu einem Pool für elastische Datenbanken gehörte, müssen Sie das Servicelevelziel angeben. 

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu vom Dienst generierten automatischen Sicherungen finden Sie im Artikel zu [automatischen Sicherungen](automated-backups-overview.md).
- Weitere Informationen zur langfristigen Beibehaltung von Sicherungen finden Sie im Artikel zur [langfristigen Beibehaltung von Sicherungen](long-term-retention-overview.md).
