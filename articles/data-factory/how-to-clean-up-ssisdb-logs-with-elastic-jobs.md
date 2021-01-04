---
title: Bereinigen von SSISDB-Protokollen mit Aufträgen für die elastische Azure-Datenbank
description: In diesem Artikel wird beschrieben, wie Sie SSISDB-Protokolle über Aufträge für die elastische Azure-Datenbank bereinigen, um die gespeicherte Prozedur auszulösen, die für diesen Zweck vorhanden ist.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/09/2020
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3539b867d8f03d11e7799498d0207a65ac9db7d8
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636628"
---
# <a name="clean-up-ssisdb-logs-with-azure-elastic-database-jobs"></a>Bereinigen von SSISDB-Protokollen mit Aufträgen für die elastische Azure-Datenbank

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel wird beschrieben, wie Sie Aufträge für elastische Azure-Datenbanken verwenden, um die gespeicherte Prozedur auszulösen, mit der Protokolle für die SQL Server Integration Services-Katalogdatenbank (`SSISDB`) bereinigt werden.

„Aufträge für die elastische Datenbank“ ist ein Azure-Dienst, der Ihnen das Automatisieren und Ausführen von Aufträgen für eine Datenbank oder eine Gruppe von Datenbanken vereinfacht. Sie können diese Aufträge planen, ausführen und überwachen, indem Sie das Azure-Portal, Transact-SQL, PowerShell oder REST-APIs verwenden. Verwenden Sie einen Auftrag für die elastische Datenbank, um die gespeicherte Prozedur für die einmalige oder auf einem Zeitplan basierende Bereinigung von Protokollen auszulösen. Sie können das Zeitplanintervall basierend auf der SSISDB-Ressourcenverwendung auswählen, um eine hohe Datenbanklast zu vermeiden.

Weitere Informationen finden Sie unter [Verwalten von Datenbankgruppen mithilfe von Aufträgen für die elastische Datenbank](../azure-sql/database/elastic-jobs-overview.md).

In den folgenden Abschnitten wird beschrieben, wie Sie die gespeicherte Prozedur `[internal].[cleanup_server_retention_window_exclusive]` auslösen. Hiermit werden SSISDB-Protokolle entfernt, die außerhalb des vom Administrator festgelegten Aufbewahrungsfensters liegen.

## <a name="clean-up-logs-with-power-shell"></a>Bereinigen von Protokollen mit PowerShell

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Mit den folgenden PowerShell-Beispielskripts wird ein neuer elastischer Auftrag erstellt, um die gespeicherte Prozedur für die SSISDB-Protokollbereinigung auszulösen. Weitere Informationen finden Sie unter [Erstellen eines Agents für elastische Aufträge mithilfe von PowerShell](../azure-sql/database/elastic-jobs-powershell-create.md).

### <a name="create-parameters"></a>Erstellen von Parametern

``` powershell
# Parameters needed to create the Job Database
param(
$ResourceGroupName = $(Read-Host "Please enter an existing resource group name"),
$AgentServerName = $(Read-Host "Please enter the name of an existing logical SQL server(for example, yhxserver) to hold the SSISDBLogCleanup job database"),
$SSISDBLogCleanupJobDB = $(Read-Host "Please enter a name for the Job Database to be created in the given SQL Server"),
# The Job Database should be a clean,empty,S0 or higher service tier. We set S0 as default.
$PricingTier = "S0",

# Parameters needed to create the Elastic Job agent
$SSISDBLogCleanupAgentName = $(Read-Host "Please enter a name for your new Elastic Job agent"),

# Parameters needed to create the job credential in the Job Database to connect to SSISDB
$PasswordForSSISDBCleanupUser = $(Read-Host "Please provide a new password for SSISDBLogCleanup job user to connect to SSISDB database for log cleanup"),
# Parameters needed to create a login and a user in the SSISDB of the target server
$SSISDBServerEndpoint = $(Read-Host "Please enter the name of the target logical SQL server which contains SSISDB you need to cleanup, for example, myserver") + '.database.windows.net',
$SSISDBServerAdminUserName = $(Read-Host "Please enter the target server admin username for SQL authentication"),
$SSISDBServerAdminPassword = $(Read-Host "Please enter the target server admin password for SQL authentication"),
$SSISDBName = "SSISDB",

# Parameters needed to set job scheduling to trigger execution of cleanup stored procedure
$RunJobOrNot = $(Read-Host "Please indicate whether you want to run the job to cleanup SSISDB logs outside the log retention window immediately(Y/N). Make sure the retention window is set appropriately before running the following powershell scripts. Those removed SSISDB logs cannot be recoverd"),
$IntervalType = $(Read-Host "Please enter the interval type for the execution schedule of SSISDB log cleanup stored procedure. For the interval type, Year, Month, Day, Hour, Minute, Second can be supported."),
$IntervalCount = $(Read-Host "Please enter the detailed interval value in the given interval type for the execution schedule of SSISDB log cleanup stored procedure"),
# StartTime of the execution schedule is set as the current time as default. 
$StartTime = (Get-Date)
```

### <a name="trigger-the-cleanup-stored-procedure"></a>Auslösen der gespeicherten Prozedur für die Bereinigung

```powershell
# Install the latest PackageManagement powershell package which PowershellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force
# You may need to restart the powershell session
# Install the latest PowershellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Place AzureRM.Sql preview cmdlets side by side with existing AzureRM.Sql version
Install-Module -Name AzureRM.Sql -AllowPrerelease -Force

# Sign in to your Azure account
Connect-AzureRmAccount

# Create a Job Database which is used for defining jobs of triggering SSISDB log cleanup stored procedure and tracking cleanup history of jobs
Write-Output "Creating a blank SQL database to be used as the SSISDBLogCleanup  Job Database ..."
$JobDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $SSISDBLogCleanupJobDB -RequestedServiceObjectiveName $PricingTier
$JobDatabase

# Enable the Elastic Jobs preview in your Azure subscription
Register-AzureRmProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql

# Create the Elastic Job agent
Write-Output "Creating the Elastic Job agent..."
$JobAgent = $JobDatabase | New-AzureRmSqlElasticJobAgent -Name $SSISDBLogCleanupAgentName
$JobAgent

# Create the job credential in the Job Database to connect to SSISDB database in the target server for log cleanup
Write-Output "Creating job credential to connect to SSISDB database..."
$JobCredSecure = ConvertTo-SecureString -String $PasswordForSSISDBCleanupUser -AsPlainText -Force
$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "SSISDBLogCleanupUser", $JobCredSecure
$JobCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "SSISDBLogCleanupUser" -Credential $JobCred

# In the master database of the target SQL server which contains SSISDB to cleanup 
# - Create the job user login
Write-Output "Grant permissions on the master database of the target server..."
$Params = @{
  'Database' = 'master'
  'ServerInstance' = $SSISDBServerEndpoint
  'Username' = $SSISDBServerAdminUserName
  'Password' = $SSISDBServerAdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '" + $PasswordForSSISDBCleanupUser + "'"
}
Invoke-SqlCmd @Params

# For SSISDB database of the target SQL server
# - Create the SSISDBLogCleanup user from the SSISDBlogCleanup user login
# - Grant permissions for the execution of SSISDB log cleanup stored procedure 
Write-Output "Grant appropriate permissions on SSISDB database..."
$TargetDatabase = $SSISDBName
$CreateJobUser = "CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser"
$GrantStoredProcedureExecution = "GRANT EXECUTE ON internal.cleanup_server_retention_window_exclusive TO SSISDBLogCleanupUser"

$TargetDatabase | % {
  $Params.Database = $_
  $Params.Query = $CreateJobUser
  Invoke-SqlCmd @Params
  $Params.Query = $GrantStoredProcedureExecution
  Invoke-SqlCmd @Params
}

# Create a target group which includes SSISDB database needed to cleanup
Write-Output "Creating the target group including only SSISDB database needed to cleanup ..."
$SSISDBTargetGroup = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name "SSISDBTargetGroup"
$SSISDBTargetGroup | Add-AzureRmSqlElasticJobTarget -ServerName $SSISDBServerEndpoint -Database $SSISDBName 

# Create the job to trigger execution of SSISDB log cleanup stored procedure
Write-Output "Creating a new job to trigger execution of the stored procedure for SSISDB log cleanup"
$JobName = "CleanupSSISDBLog"
$Job = $JobAgent | New-AzureRmSqlElasticJob -Name $JobName -RunOnce
$Job

# Add the job step to execute internal.cleanup_server_retention_window_exclusive
Write-Output "Adding the job step for the cleanup stored procedure execution"
$SqlText = "EXEC internal.cleanup_server_retention_window_exclusive"
$Job | Add-AzureRmSqlElasticJobStep -Name "step to execute cleanup stored procedure" -TargetGroupName $SSISDBTargetGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText

# Run the job to immediately start cleanup stored procedure execution for once
IF(($RunJobOrNot = "Y") -Or ($RunJobOrNot = "y"))
{
Write-Output "Start a new execution of the stored procedure for SSISDB log cleanup immediately..."
$JobExecution = $Job | Start-AzureRmSqlElasticJob
$JobExecution
}

# Schedule the job running to trigger stored procedure execution on schedule for removing SSISDB logs outside the retention window
Write-Output "Start the execution schedule of the stored procedure for SSISDB log cleanup..."
$Job | Set-AzureRmSqlElasticJob -IntervalType $IntervalType -IntervalCount $IntervalCount -StartTime $StartTime -Enable
```

## <a name="clean-up-logs-with-transact-sql"></a>Bereinigen von Protokollen mit Transact-SQL

Mit den folgenden Transact-SQL-Beispielskripts wird ein neuer elastischer Auftrag erstellt, um die gespeicherte Prozedur für die SSISDB-Protokollbereinigung auszulösen. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Aufträgen für die elastische Datenbank mit Transact-SQL (T-SQL)](../azure-sql/database/elastic-jobs-tsql-create-manage.md).

1. Erstellen bzw. ermitteln Sie eine leere Azure SQL-Datenbank (S0 oder höher) als SSISDBCleanup-Auftragsdatenbank. Erstellen Sie anschließend im [Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.SQLElasticJobAgent) einen Agent für elastische Aufträge.

2. Erstellen Sie in der Auftragsdatenbank Anmeldeinformationen für den Auftrag zur Bereinigung von SSISDB-Protokollen. Diese Anmeldeinformationen werden verwendet, um eine Verbindung mit Ihrer SSISDB-Datenbank zur Bereinigung der Protokolle herzustellen.

    ```sql
    -- Connect to the job database specified when creating the job agent
    -- Create a database master key if one does not already exist, using your own password.  
    CREATE MASTER KEY ENCRYPTION BY PASSWORD= '<EnterStrongPasswordHere>';  

    -- Create a credential for SSISDB log cleanup.  
    CREATE DATABASE SCOPED CREDENTIAL SSISDBLogCleanupCred WITH IDENTITY = 'SSISDBLogCleanupUser', SECRET = '<EnterStrongPasswordHere>'; 
    ```

3. Definieren Sie die Zielgruppe, die die SSISDB-Datenbank enthält, für die Sie die gespeicherte Prozedur für die Bereinigung ausführen möchten.

    ```sql
    -- Connect to the job database 
    -- Add a target group 
    EXEC jobs.sp_add_target_group 'SSISDBTargetGroup'

    -- Add SSISDB database into the target group
    EXEC jobs.sp_add_target_group_member 'SSISDBTargetGroup',
    @target_type = 'SqlDatabase',
    @server_name = '<EnterSSISDBTargetServerName>',
    @database_name = '<EnterSSISDBName>'

    --View the recently created target group and target group members
    SELECT * FROM jobs.target_groups WHERE target_group_name = 'SSISDBTargetGroup';
    SELECT * FROM jobs.target_group_members WHERE target_group_name = 'SSISDBTargetGroup';
    ```
4. Erstellen Sie entsprechende Berechtigungen für die SSISDB-Datenbank. Der SSISDB-Katalog muss über geeignete Berechtigungen für die gespeicherte Prozedur verfügen, damit die Bereinigung der SSISDB-Protokolle erfolgreich ist. Eine ausführliche Anleitung finden Sie unter [Verwalten von Anmeldungen](../azure-sql/database/logins-create-manage.md).

    ```sql
    -- Connect to the master database in the target server including SSISDB 
    CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '<strong_password>';

    -- Connect to SSISDB database in the target server to cleanup logs
    CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser;
    GRANT EXECUTE ON internal.cleanup_server_retention_window_exclusive TO SSISDBLogCleanupUser
    ```
5. Erstellen Sie den Auftrag, und fügen Sie einen Auftragsschritt hinzu, um die Ausführung der gespeicherten Prozedur für die Bereinigung von SSISDB-Protokollen auszulösen.

    ```sql
    --Connect to the job database 
    --Add the job for the execution of SSISDB log cleanup stored procedure.
    EXEC jobs.sp_add_job @job_name='CleanupSSISDBLog', @description='Remove SSISDB logs which are outside the retention window'

    --Add a job step to execute internal.cleanup_server_retention_window_exclusive
    EXEC jobs.sp_add_jobstep @job_name='CleanupSSISDBLog',
    @command=N'EXEC internal.cleanup_server_retention_window_exclusive',
    @credential_name='SSISDBLogCleanupCred',
    @target_group_name='SSISDBTargetGroup'
    ```
6. Stellen Sie vor dem Fortfahren sicher, dass das Aufbewahrungszeitfenster richtig festgelegt wurde. SSISDB-Protokolle, die außerhalb des Zeitfensters liegen, werden gelöscht und können nicht wiederhergestellt werden.

   Anschließend können Sie den Auftrag sofort ausführen, um mit dem Bereinigen der SSISDB-Protokolle zu beginnen.

    ```sql
    --Connect to the job database 
    --Run the job immediately to execute the stored procedure for SSISDB log cleanup
    declare @je uniqueidentifier
    exec jobs.sp_start_job 'CleanupSSISDBLog', @job_execution_id = @je output

    --Watch the execution results for SSISDB log cleanup 
    select @je
    select * from jobs.job_executions where job_execution_id = @je
    ```
7. Optional können Sie Auftragsausführungen so planen, dass außerhalb des Aufbewahrungszeitfensters liegende SSISDB-Protokolle nach einem Zeitplan entfernt werden. Verwenden Sie eine ähnliche Anweisung, um die Auftragsparameter zu aktualisieren.

    ```sql
    --Connect to the job database 
    EXEC jobs.sp_update_job
    @job_name='CleanupSSISDBLog',
    @enabled=1,
    @schedule_interval_type='<EnterIntervalType(Month,Day,Hour,Minute,Second)>',
    @schedule_interval_count='<EnterDetailedIntervalValue>',
    @schedule_start_time='<EnterProperStartTimeForSchedule>',
    @schedule_end_time='<EnterProperEndTimeForSchedule>'
    ```

## <a name="monitor-the-cleanup-job-in-the-azure-portal"></a>Überwachen des Bereinigungsauftrags im Azure-Portal

Sie können die Ausführung des Bereinigungsauftrags im Azure-Portal überwachen. Für jede Ausführung werden der Status, die Startzeit und die Endzeit des Auftrags angezeigt.

![Überwachen des Bereinigungsauftrags im Azure-Portal](media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/monitor-cleanup-job-portal.png)

## <a name="monitor-the-cleanup-job-with-transact-sql"></a>Überwachen des Bereinigungsauftrags mit Transact-SQL

Sie können Transact-SQL auch verwenden, um den Ausführungsverlauf des Bereinigungsauftrags anzuzeigen.

```sql
--Connect to the job database 
--View all execution statuses for the job to cleanup SSISDB logs
SELECT * FROM jobs.job_executions WHERE job_name = 'CleanupSSISDBLog' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions WHERE is_active = 1
ORDER BY start_time DESC
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zu Verwaltungs- und Überwachungsaufgaben in Bezug auf die Azure-SSIS Integration Runtime finden Sie in den folgenden Artikeln. Die Azure-SSIS IR ist die Laufzeit-Engine für SSIS-Pakete, die in Azure SQL-Datenbank in der SSISDB gespeichert sind.

-   [Neukonfigurieren der Azure-SSIS-Integration Runtime](manage-azure-ssis-integration-runtime.md)

-   [Überwachen einer Integrationslaufzeit in Azure Data Factory](monitor-integration-runtime.md#azure-ssis-integration-runtime).