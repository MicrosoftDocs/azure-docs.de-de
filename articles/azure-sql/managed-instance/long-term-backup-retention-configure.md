---
title: 'Azure SQL Managed Instance: Langfristige Sicherungsaufbewahrung (PowerShell)'
description: Erfahren Sie, wie Sie mithilfe von PowerShell automatisierte Sicherungen in separaten Azure Blob Storage-Containern für Azure SQL Managed Instance speichern und wiederherstellen.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 04/29/2020
ms.openlocfilehash: a5a2ff85395a55bcd4e8405e2eb60c6a4645818c
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833438"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Verwalten der langfristigen Sicherungsaufbewahrung für Azure SQL Managed Instance (PowerShell)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In Azure SQL Managed Instance können Sie eine Richtlinie für die [langfristige Sicherungsaufbewahrung](../database/long-term-retention-overview.md#sql-managed-instance-support) (Long-Term Retention, LTR) als Previewfunktion der eingeschränkten Public Preview konfigurieren. Dies ermöglicht es Ihnen, Datenbanksicherungen automatisch bis zu zehn Jahre in separaten Azure Blob Storage-Containern beizubehalten. Sie können dann mit PowerShell eine Datenbank anhand dieser Sicherungen wiederherstellen.

   > [!IMPORTANT]
   > Die Langzeitaufbewahrung für verwaltete Instanzen befindet sich derzeit in der eingeschränkten Vorschauphase und ist fallbasiert für EA- und CSP-Abonnements verfügbar. Wenn Sie eine Registrierung anfordern möchten, erstellen Sie ein [Azure-Supportticket](https://azure.microsoft.com/support/create-ticket/). Wählen Sie bei „Issuetyp“ die Option „Technisches Problem“, bei „Dienst“ die Option „Verwaltete SQL-Datenbank-Instanz“ und bei „Problemtyp“ die Option **Sicherung, Wiederherstellung und Geschäftskontinuität/Langfristige Sicherungsaufbewahrung** aus. Geben Sie in Ihrer Anforderung an, dass Sie für die eingeschränkte Public Preview der LTR für eine verwaltete Instanz registriert werden möchten.

In den folgenden Abschnitten wird erläutert, wie Sie mithilfe von PowerShell die langfristige Sicherungsaufbewahrung konfigurieren, Sicherungen in Azure SQL-Speicher anzeigen und eine Sicherung in Azure SQL-Speicher wiederherstellen.

## <a name="azure-roles-to-manage-long-term-retention"></a>Azure-Rollen zum Verwalten der langfristigen Aufbewahrung

Für **Get-AzSqlInstanceDatabaseLongTermRetentionBackup** und **Restore-AzSqlInstanceDatabase** müssen Sie über eine der folgenden Rollen verfügen:

- Rolle „Besitzer des Abonnements“ oder
- Rolle „Mitwirkender“ der verwalteten Instanz oder
- Benutzerdefinierte Rolle mit den folgenden Berechtigungen:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

Für **Remove-AzSqlInstanceDatabaseLongTermRetentionBackup** müssen Sie über eine der folgenden Rollen verfügen:

- Rolle „Besitzer des Abonnements“ oder
- Benutzerdefinierte Rolle mit der folgenden Berechtigung:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> Die Rolle „Mitwirkender“ für die verwaltete Instanz besitzt keine Berechtigung zum Löschen von LTR-Sicherungen.

Azure RBAC-Berechtigungen können im Bereich *Abonnement* oder *Ressourcengruppe* erteilt werden. Für den Zugriff auf LTR-Sicherungen, die zu einer gelöschten Instanz gehören, muss die Berechtigung jedoch im Bereich *Abonnement* dieser Instanz erteilt werden.

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

## <a name="create-an-ltr-policy"></a>Erstellen einer LTR-Richtlinie

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount

Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W' 
    YearlyRetention = 'P5Y' 
    WeekOfYear = '16'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

## <a name="view-ltr-policies"></a>Anzeigen von LTR-Richtlinien

In diesem Beispiel wird gezeigt, wie Sie die LTR-Richtlinien in einer Instanz für eine Einzeldatenbank auflisten.

```powershell
# gets the current version of LTR policy for a database
$LTRPolicies = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup
}
Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy 
```

In diesem Beispiel wird gezeigt, wie Sie die LTR-Richtlinien in einer Instanz für alle Datenbanken auflisten.

```powershell
# gets the current version of LTR policy for all of the databases on an instance

$Databases = Get-AzSqlInstanceDatabase -ResourceGroupName $resourceGroup -InstanceName $instanceName

$LTRParams = @{
    InstanceName = $instanceName
    ResourceGroupName = $resourceGroup
}

foreach($database in $Databases.Name){
    Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRParams  -DatabaseName $database
 }
```

## <a name="clear-an-ltr-policy"></a>Löschen einer LTR-Richtlinie

In diesem Beispiel wird gezeigt, wie Sie eine LTR-Richtlinie aus einer Datenbank löschen.

```powershell
# remove the LTR policy from a database
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    RemovePolicy = $true
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

## <a name="view-ltr-backups"></a>Anzeigen von LTR-Sicherungen

In diesem Beispiel wird gezeigt, wie Sie die LTR-Sicherungen in einer Instanz auflisten.

```powershell

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$LTRBackupParam = @{
    Location = $instance.Location 
    DatabaseState = 'Live'
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# only list the latest LTR backup for each database
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    OnlyLatestPerDatabase = $true
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 
```

## <a name="delete-ltr-backups"></a>Löschen von LTR-Sicherungen

Dieses Beispiel zeigt, wie Sie eine LTR-Sicherung aus der Liste der Sicherungen löschen.

```powershell
# remove the earliest backup
# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Das Löschen der LTR-Sicherung kann nicht rückgängig gemacht werden. Wenn Sie eine LTR-Sicherung löschen möchten, nachdem die Instanz gelöscht wurde, müssen Sie über die Berechtigung im Bereich „Abonnement“ verfügen. Sie können Benachrichtigungen zu jedem Löschvorgang in Azure Monitor einrichten, indem Sie nach dem Vorgang „Löscht eine Sicherung zur langfristigen Aufbewahrung“ filtern. Das Aktivitätsprotokoll enthält Informationen dazu, wer die Anforderung vorgenommen hat und wann sie erfolgt ist. Ausführliche Anweisungen finden Sie unter [Erstellen von Aktivitätsprotokollwarnungen](../../azure-monitor/platform/alerts-activity-log.md).

## <a name="restore-from-ltr-backups"></a>Wiederherstellen von LTR-Sicherungen

Dieses Beispiel zeigt, wie Sie eine LTR-Sicherung wiederherstellen. Beachten Sie, dass sich diese Schnittstelle nicht geändert hat, der Parameter für die Ressourcen-ID jetzt jedoch die Ressourcen-ID der LTR-Sicherung erfordert.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbname
    OnlyLatestPerDatabase = $true
}
$ltrBackup = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 

$RestoreLTRParam = @{
    TargetInstanceName          = $instanceName 
    TargetResourceGroupName     = $resourceGroup 
    TargetInstanceDatabaseName  = $dbName
    FromLongTermRetentionBackup = $true
    ResourceId                  = $ltrBackup.ResourceId 
}
Restore-AzSqlInstanceDatabase @RestoreLTRParam
```

> [!IMPORTANT]
> Um die Wiederherstellung aus einer LTR-Sicherung nach dem Löschen der Instanz durchführen zu können, müssen Sie über Berechtigungen im Bereich des Abonnements der Instanz verfügen, und dieses Abonnement muss aktiv sein. Sie müssen außerdem den optionalen Parameter -ResourceGroupName auslassen.

> [!NOTE]
> Auf diesem Blatt können Sie mithilfe von SQL Server Management Studio eine Verbindung mit der wiederhergestellten Datenbank herstellen, um erforderliche Aufgaben durchzuführen. Sie können beispielsweise einen Teil der Daten aus der wiederhergestellten Datenbank extrahieren und in die vorhandene Datenbank kopieren oder die vorhandene Datenbank löschen und die wiederhergestellte Datenbank in den vorhandenen Datenbanknamen umbenennen. Siehe [Point-in-Time-Wiederherstellung](../database/recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu vom Dienst generierten automatischen Sicherungen finden Sie im Artikel zu [automatischen Sicherungen](../database/automated-backups-overview.md).
- Weitere Informationen zur langfristigen Beibehaltung von Sicherungen finden Sie im Artikel zur [langfristigen Beibehaltung von Sicherungen](../database/long-term-retention-overview.md).
