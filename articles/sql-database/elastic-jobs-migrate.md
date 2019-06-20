---
title: Migrieren zu den neuen Aufträgen für die elastische Datenbank | Microsoft-Dokumentation
description: Migrieren zu den neuen Aufträgen für die elastische Datenbank
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: johnpaulkee
ms.author: joke
ms.reviewer: sstein
manager: craigg
ms.date: 03/13/2019
ms.openlocfilehash: 627b29018afff2adb57b3b8b5c816bea9c421f68
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236856"
---
# <a name="migrate-to-the-new-elastic-database-jobs"></a>Migrieren zu den neuen Aufträgen für die elastische Datenbank

Eine Upgradeversion der [Aufträge für die elastische Datenbank](elastic-jobs-overview.md) ist verfügbar.

Wenn Sie über eine vorhandene vom Kunden gehostete Version der Aufträge für die elastische Datenbank verfügen, werden Migrations-Cmdlets und -Skripts für die einfache Migration zur neuesten Version bereitgestellt.


## <a name="prerequisites"></a>Voraussetzungen

Die Upgradeversion der Aufträge für die elastische Datenbank umfasst neue PowerShell-Cmdlets zur Verwendung während der Migration. Mit diesen neuen Cmdlets werden alle Ihre vorhandenen Auftragsanmeldeinformationen, Ziele (einschließlich Datenbanken, Servern, benutzerdefinierten Sammlungen), Auftragsauslöser, Auftragszeitpläne, Auftragsinhalte und Aufträge zu einem neuen Agent für elastische Aufträge übertragen.

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>Installieren der neuesten Cmdlets für elastische Aufträge

Wenn Sie nicht bereits ein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.

Installieren Sie das Modul **Az.Sql 1.1.1-preview**, um die aktuellen Cmdlets für elastische Aufträge zu erhalten. Führen Sie die folgenden Befehle in PowerShell mit Administratorzugriff aus.

```powershell
# Installs the latest PackageManagement powershell package which PowerShellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# Installs the latest PowerShellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Restart your powershell session with administrative access

# Places Az.Sql preview cmdlets side by side with existing Az.Sql version
Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease

# Import the Az.Sql module
Import-Module Az.Sql -RequiredVersion 1.1.1

# Confirm if module successfully imported - if the imported version is 1.1.1, then continue
Get-Module Az.Sql
```

### <a name="create-a-new-elastic-job-agent"></a>Erstellen eines neuen Agents für elastische Aufträge

Erstellen Sie nach dem Installieren der neuen Cmdlets einen neuen Agent für elastische Aufträge.

```powershell
# Register your subscription for the for the Elastic Jobs public preview feature
Register-AzProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql

# Get an existing database to use as the job database - or create a new one if necessary
$db = Get-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName>
# Create a new elastic job agent
$agent = $db | New-AzSqlElasticJobAgent -Name <agentName>
```

### <a name="install-the-old-elastic-database-jobs-cmdlets"></a>Installieren der alten Cmdlets für die Aufträge für die elastische Datenbank

Bei der Migration müssen einige der *alten* Cmdlets für elastische Aufträge verwendet werden. Führen Sie daher die folgenden Befehle aus, wenn diese Cmdlets noch nicht installiert sind.

```powershell
# Install the old elastic job cmdlets if necessary and initialize the old jobs cmdlets
.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease

# Install the old jobs cmdlets
cd Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools
Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
.\InstallElasticDatabaseJobsCmdlets.ps1

# Choose the subscription where your existing jobs are
Select-AzSubscription -SubscriptionId <subscriptionId>
Use-AzureSqlJobConnection -CurrentAzureSubscription -Credential (Get-Credential)
```



## <a name="migration"></a>Migration

Nachdem die alten und die neuen Cmdlets für elastische Aufträge initialisiert wurden, migrieren Sie Ihre Auftragsanmeldeinformationen, Ziele und Aufträge zu der neuen *Auftragsdatenbank*.

### <a name="setup"></a>Einrichtung

```powershell
$ErrorActionPreference = "Stop";

# Helper function to show starting write output
function Log-StartOutput ($output) {
  Write-Output ("`r--------------------- " + $output + " ---------------------")
}

# Helper function to show starting write output
function Log-ChildOutput ($output) {
  Write-Output ("  - " + $output)
}
```



### <a name="migrate-credentials"></a>Migrieren der Anmeldeinformationen

```powershell
function Migrate-Credentials ($agent) {
    Log-StartOutput "Migrating credentials"

    $oldCreds = Get-AzureSqlJobCredential
    $oldCreds | % {
        $oldCredName = $_.CredentialName
        $oldUserName = $_.UserName
        Write-Output ("Credential " + $oldCredName)
        $oldCredential = Get-Credential -UserName $oldUserName `
                         -Message ("Please enter in the password that was used for your credential " + $oldCredName)
        try
        {
            $cred = New-AzSqlElasticJobCredential -ParentObject $agent -Name $oldCredName -Credential $oldCredential
        }
        catch [System.Management.Automation.PSArgumentException]
        {
            $cred = Get-AzSqlElasticJobCredential -ParentObject $agent -Name $oldCredName
            $cred = Set-AzSqlElasticJobCredential -InputObject $cred -Credential $oldCredential
        }

        Log-ChildOutput ("Added user " + $oldUserName)
    }
}
```

Führen Sie zum Migrieren Ihrer Anmeldeinformationen den folgenden Befehl durch Übergeben des PowerShell-Objekts `$agent` von weiter oben aus.

```powershell
Migrate-Credentials $agent
```

Beispielausgabe

```powershell
# You should see similar output after executing the above
# --------------------- Migrating credentials ---------------------
# Credential cred1
#  - Added user user1
# Credential cred2
#  - Added user user2
# Credential cred3
#  - Added user user3
```

### <a name="migrate-targets"></a>Migrieren von Zielen

```powershell
function Migrate-TargetGroups ($agent) {
    Log-StartOutput "Migrating target groups"

    # Setup hash of target groups
    $targetGroups = [ordered]@{}

    # Fetch root job targets from old service
    $rootTargets = Get-AzureSqlJobTarget

    # Return if no root targets are found
    if ($rootTargets.Count -eq 0)
    {
        Write-Output "No targets found - no need for migration"
        return
    }

    # Create list of target groups to create
    # We format the target group name as such:
    # - If root target is server type, then target group name is "(serverName)"
    # - If root target is database type, then target group name is "(serverName,databaseName)"
    # - If root target is shard map type, then target group name is "(serverName,databaseName,shardMapName)"
    # - If root target is custom collection, then target group name is "customCollectionName"
    $rootTargets | % {
        $tgName = Format-OldTargetName -target $_
        $childTargets = Get-ChildTargets -target $_
        $targetGroups.Add($tgName, $childTargets)
    }

    # Flatten list
    for ($i=$targetGroups.Count - 1; $i -ge 0; $i--)
    {
        # Fetch target group's initial list of targets unexpanded
        $targets = $targetGroups[$i]

        # Expand custom collection targets
        $j = 0;
        while ($j -lt $targets.Count)
        {
            $target = $targets[$j]
            if ($target.TargetType -eq "CustomCollection")
            {
                $targets = [System.Collections.ArrayList] $targets
                $targets.Remove($target) # Remove this target from the list

                $expandedTargets = $targetGroups[$target.TargetDescription.CustomCollectionName]

                foreach ($expandedTarget in $expandedTargets)
                {
                    $targets.Add($expandedTarget) | Out-Null
                }

                # Set updated list of targets for tg
                $targetGroups[$i] = $targets
                # Note we don't increment here in case we need to expand further
            }
            else
            {
                # Skip if no custom collection target needs to be expanded
                $j++
            }
        }
    }

    # Add targets to target group
    foreach ($targetGroup in $targetGroups.Keys)
    {
        $tg = Setup-TargetGroup -tgName $targetGroup -agent $agent
        $targets = $targetGroups[$targetGroup]
        Migrate-Targets -targets $targets -tg $tg
        $targetsAdded = (Get-AzSqlElasticJobTargetGroup -ParentObject $agent -Name $tg.TargetGroupName).Targets
        foreach ($targetAdded in $targetsAdded)
        {
            Log-ChildOutput ("Added target " + (Format-NewTargetName $targetAdded))
        }
    }
}

## Target group helpers
# Migrate shard map target from old jobs to new job's target group
function Migrate-Targets ($targets, $tg) {
  Write-Output ("Target group " + $tg.TargetGroupName)
  foreach ($target in $targets) {
    if ($target.TargetType -eq "Server") {
      Add-ServerTarget -target $target -tg $tg
    }
    elseif ($target.TargetType -eq "Database") {
      Add-DatabaseTarget -target $target -tg $tg
    }
    elseif ($target.TargetType -eq "ShardMap") {
      Add-ShardMapTarget -target $target -tg $tg
    }
  }
}

# Migrate server target from old jobs to new job's target group
function Add-ServerTarget ($target, $tg) {
  $jobTarget = Get-AzureSqlJobTarget -TargetId $target.TargetId
  $serverName = $jobTarget.ServerName
  $credName = $jobTarget.MasterDatabaseCredentialName
  $t = Add-AzSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -RefreshCredentialName $credName
}

# Migrate database target from old jobs to new job's target group
function Add-DatabaseTarget ($target, $tg) {
  $jobTarget = Get-AzureSqlJobTarget -TargetId $target.TargetId
  $serverName = $jobTarget.ServerName
  $databaseName = $jobTarget.DatabaseName
  $exclude = $target.Membership

  if ($exclude -eq "Exclude") {
    $t = Add-AzSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -DatabaseName $databaseName -Exclude
  }
  else {
    $t = Add-AzSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -DatabaseName $databaseName
  }
}

# Migrate shard map target from old jobs to new job's target group
function Add-ShardMapTarget ($target, $tg) {
  $jobTarget = Get-AzureSqlJobTarget -TargetId $target.TargetId
  $smName = $jobTarget.ShardMapName
  $serverName = $jobTarget.ShardMapManagerServerName
  $databaseName = $jobTarget.ShardMapManagerDatabaseName
  $credName = $jobTarget.ShardMapManagerCredentialName
  $exclude = $target.Membership

  if ($exclude -eq "Exclude") {
    $t = Add-AzSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -ShardMapName $smName -DatabaseName $databasename -RefreshCredentialName $credName -Exclude
  }
  else {
    $t = Add-AzSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -ShardMapName $smName -DatabaseName $databasename -RefreshCredentialName $credName
  }
}

# Helper to format target old target names
function Format-OldTargetName ($target) {
  if ($target.TargetType -eq "Server") {
    $tgName = "(" + $target.ServerName + ")"
  }
  elseif ($target.TargetType -eq "Database") {
    $tgName = "(" + $target.ServerName + "," + $target.DatabaseName + ")"
  }
  elseif ($target.TargetType -eq "ShardMap") {
    $tgName = "(" + $target.ShardMapManagerServerName + "," +
    $target.ShardMapManagerDatabaseName + "," + `
      $target.ShardMapName + ")"
  }
  elseif ($target.TargetType -eq "CustomCollection") {
    $tgName = $target.CustomCollectionName
  }

  return $tgName
}

# Helper to format new target names
function Format-NewTargetName ($target) {
  if ($target.TargetType -eq "SqlServer") {
    $tgName = "(" + $target.TargetServerName + ")"
  }
  elseif ($target.TargetType -eq "SqlDatabase") {
    $tgName = "(" + $target.TargetServerName + "," + $target.TargetDatabaseName + ")"
  }
  elseif ($target.TargetType -eq "SqlShardMap") {
    $tgName = "(" + $target.TargetServerName + "," +
    $target.TargetDatabaseName + "," + `
      $target.TargetShardMapName + ")"
  }
  elseif ($target.TargetType -eq "SqlElasticPool") {
    $tgName = "(" + $target.TargetServerName + "," +
    $target.TargetDatabaseName + "," + `
      $target.TargetElasticPoolName + ")"
  }

  return $tgName
}

# Get child targets
function Get-ChildTargets($target) {
  if ($target.TargetType -eq "CustomCollection") {
    $children = Get-AzureSqlJobChildTarget -TargetId $target.TargetId
    if ($children.Count -eq 1)
    {
        $arr = New-Object System.Collections.ArrayList($null)
        $arr.Add($children)
        $children = $arr
    }
    return $children
  }
  else {
    return $target
  }
}

# Migrates target groups
function Setup-TargetGroup ($tgName, $agent) {
  try {
    $tg = New-AzSqlElasticJobTargetGroup -ParentObject $agent -Name $tgName
    return $tg
  }
  catch [System.Management.Automation.PSArgumentException] {
    $tg = Get-AzSqlElasticJobTargetGroup -ParentObject $agent -Name $tgName
    return $tg
  }
}
```

Um Ihre Ziele (Server, Datenbanken und benutzerdefinierten Sammlungen) zu der neuen Auftragsdatenbank zu migrieren, führen Sie das Cmdlet **Migrate-TargetGroups** aus, um Folgendes zu erreichen:

- Ziele auf Stammebene, die Server und Datenbanken sind, werden zu der neuen Zielgruppe „(\<Servername\>, \<Datenbankname\>)“ migriert, die nur das Ziel auf Stammebene enthält.
- Eine benutzerdefinierte Sammlung wird zu einer neuen Zielgruppe migriert, die alle untergeordneten Ziele enthält.

```powershell
Migrate-TargetGroups $agent
```

Beispielausgabe:

```powershell
# --------------------- Migrating target groups ---------------------
# Target group cc1
#   - Added target (s1)
#   - Added target (s1,db1)
# Target group cc2
#   - Added target (s1,db1)
# Target group cc3
#   - Added target (s1)
#   - Added target (s1,db1)
# Target group (s1,db1)
#   - Added target (s1,db1)
# Target group (s1,db2)
#   - Added target (s1,db2)
# Target group (s1)
#   - Added target (s1)
# Target group (s1,db1,sm1)
#   - Added target (s1,db1,sm1)
```



### <a name="migrate-jobs"></a>Migrieren von Aufträgen

```powershell
function Migrate-Jobs ($agent)
{
    Log-StartOutput "Migrating jobs and job steps"

    $oldJobs = Get-AzureSqlJob
    $newJobs = [System.Collections.ArrayList] @()

    foreach ($oldJob in $oldJobs)
    {
        # Ignore system jobs
        if ($oldJob.ContentName -eq $null)
        {
            continue
        }

        # Schedule
        $oldJobTriggers = Get-AzureSqlJobTrigger -JobName $oldJob.JobName

        if ($oldJobTriggers.Count -ge 1)
        {
            foreach ($trigger in $oldJobTriggers)
            {

                $schedule = Get-AzureSqlJobSchedule -ScheduleName $trigger.ScheduleName
                $newJob = [PSCustomObject] @{
                    JobName  = ($trigger.JobName + " (" + $trigger.ScheduleName + ")");
                    Description = $oldJob.ContentName
                    Schedule = $schedule
                    TargetGroupName = (Format-OldTargetName(Get-AzureSqlJobTarget -TargetId $oldJob.TargetId))
                    CredentialName = $oldJob.CredentialName
                    Output = $oldJob.ResultSetDestination
                }
                $newJobs.Add($newJob) | Out-Null
            }
        }
        else
        {
            $newJob = [PSCustomObject] @{
                JobName  = $oldJob.JobName
                Description = $oldJob.ContentName
                Schedule = $null
                TargetGroupName = (Format-OldTargetName(Get-AzureSqlJobTarget -TargetId $oldJob.TargetId))
                CredentialName = $oldJob.CredentialName
                Output = $oldJob.ResultSetDestination
            }
            $newJobs.Add($newJob) | Out-Null
        }
    }

    # At this point, we should have an organized list of jobs to create
    foreach ($newJob in $newJobs)
    {
        Write-Output ("Job " + $newJob.JobName)
        $job = Setup-Job $newJob $agent
        If ($job.Interval -ne $null)
        {
            Log-ChildOutput ("Schedule with start time " + $job.StartTime + " and end time at " + $job.EndTime)
            Log-ChildOutput ("Repeats every " + $job.Interval)
        }
        else {
            Log-ChildOutput ("Repeats once")
        }

        Setup-JobStep $newJob $job
    }
}

# Migrates jobs
function Setup-Job ($job, $agent) {
  $jobName = $newJob.JobName
  $jobDescription = $newJob.Description

  # Create or update a job has a recurring schedule
  if ($newJob.Schedule -ne $null) {
    $schedule = $newJob.Schedule
    $startTime = $schedule.StartTime.UtcTime
    $endTime = $schedule.EndTime.UtcTime
    $intervalType = $schedule.Interval.IntervalType.ToString()
    $intervalType = $intervalType.Substring(0, $intervalType.Length - 1) # Remove the last letter (s)
    $intervalCount = $schedule.Interval.Count

    try {
      $job = New-AzSqlElasticJob -ParentObject $agent -Name $jobName `
        -Description $jobDescription -IntervalType $intervalType -IntervalCount $intervalCount `
        -StartTime $startTime -EndTime $endTime
      return $job
    }
    catch [System.Management.Automation.PSArgumentException] {
      $job = Get-AzSqlElasticJob -ParentObject $agent -Name $jobName
      $job = $job | Set-AzSqlElasticJob -Description $jobDescription -IntervalType $intervalType -IntervalCount $intervalCount `
        -StartTime $startTime -EndTime $endTime
      return $job
    }
  }
  # Create or update a job that runs once
  else {
    try {
      $job = New-AzSqlElasticJob -ParentObject $agent -Name $jobName `
        -Description $jobDescription -RunOnce
      return $job
    }
    catch [System.Management.Automation.PSArgumentException] {
      $job = Get-AzSqlElasticJob -ParentObject $agent -Name $jobName
      $job = $job | Set-AzSqlElasticJob -Description $jobDescription -RunOnce
      return $job
    }
  }
}
# Migrates job steps
function Setup-JobStep ($newJob, $job) {
  $defaultJobStepName = 'JobStep'
  $contentName = $newJob.Description
  $commandText = (Get-AzureSqlJobContentDefinition -ContentName $contentName).CommandText
  $targetGroupName = $newJob.TargetGroupName
  $credentialName = $newJob.CredentialName

  $output = $newJob.Output

  if ($output -ne $null) {
    $outputServerName = $output.TargetDescription.ServerName
    $outputDatabaseName = $output.TargetDescription.DatabaseName
    $outputCredentialName = $output.CredentialName
    $outputSchemaName = $output.SchemaName
    $outputTableName = $output.TableName
    $outputDatabase = Get-AzSqlDatabase -ResourceGroupName $job.ResourceGroupName -ServerName $outputServerName -Databasename $outputDatabaseName

    try {
      $jobStep = $job | Add-AzSqlElasticJobStep -Name $defaultJobStepName `
        -TargetGroupName $targetGroupName -CredentialName $credentialName -CommandText $commandText `
        -OutputDatabaseObject $outputDatabase `
        -OutputSchemaName $outputSchemaName -OutputTableName $outputTableName `
        -OutputCredentialName $outputCredentialName
    }
    catch [System.Management.Automation.PSArgumentException] {
      $jobStep = $job | Get-AzSqlElasticJobStep -Name $defaultJobStepName
      $jobStep = $jobStep | Set-AzSqlElasticJobStep -TargetGroupName $targetGroupName `
        -CredentialName $credentialName -CommandText $commandText `
        -OutputDatabaseObject $outputDatabase `
        -OutputSchemaName $outputSchemaName -OutputTableName $outputTableName `
        -OutputCredentialName $outputCredentialName
    }
  }
  else {
    try {
      $jobStep = $job | Add-AzSqlElasticJobStep -Name $defaultJobStepName -TargetGroupName $targetGroupName -CredentialName $credentialName -CommandText $commandText
    }
    catch [System.Management.Automation.PSArgumentException] {
      $jobStep = $job | Get-AzSqlElasticJobStep -Name $defaultJobStepName
      $jobStep = $jobStep | Set-AzSqlElasticJobStep -TargetGroupName $targetGroupName -CredentialName $credentialName -CommandText $commandText
    }
  }
  Log-ChildOutput ("Added step " + $jobStep.StepName + " using target group " + $jobStep.TargetGroupName + " using credential " + $jobStep.CredentialName)
  Log-ChildOutput("Command text script taken from content name " + $contentName)

  if ($jobStep.Output -ne $null) {
    Log-ChildOutput ("With output target as (" + $jobStep.Output.ServerName + "," + $jobStep.Output.DatabaseName + "," + $jobStep.Output.SchemaName + "," + $jobStep.Output.TableName + ")")
  }
}
```

Um Ihre Aufträge, Auftragsinhalte, Auftragsauslöser und Auftragszeitpläne zu der Datenbank des neuen Agents für elastische Aufträge zu migrieren, führen Sie das Cmdlet **Migrate-Jobs** durch Übergeben in den Agent aus.

- Aufträge mit mehreren Triggern mit unterschiedlichen Zeitplänen werden in mehrere Aufträge mit dem folgenden Benennungsschema unterteilt: „\<Auftragsname\> (\<Zeitplanname\>)“.
- Auftragsinhalte werden durch Hinzufügen des Standardauftragsschritts „JobStep“ mit zugeordnetem Befehlstext zu einem Auftrag migriert.
- Aufträge werden standardmäßig deaktiviert, sodass Sie sie vor dem Aktivieren überprüfen können.

```powershell
Migrate-Jobs $agent
```

Beispielausgabe:
```powershell
--------------------- Migrating jobs and job steps ---------------------
Job job1
  - Repeats once
  - Added step JobStep using target group cc2 using credential cred1
  - Command text script taken from content name SampleContext
Job job2
  - Repeats once
  - Added step JobStep using target group (s1,db1) using credential cred1
  - Command text script taken from content name SampleContent
  - With output target as (s1,db1,dbo,sampleTable)
Job job3 (repeat every 10 min)
  - Schedule with start time 05/16/2018 22:05:28 and end time at 12/31/9999 11:59:59
  - Repeats every PT10M
  - Added step JobStep using target group cc1 using credential cred1
  - Command text script taken from content name SampleContent
Job job3 (repeat every 5 min)
  - Schedule with start time 05/16/2018 22:05:31 and end time at 12/31/9999 11:59:59
  - Repeats every PT5M
  - Added step JobStep using target group cc1 using credential cred1
  - Command text script taken from content name SampleContent
Job job4
  - Repeats once
  - Added step JobStep using target group (s1,db1) using credential cred1
  - Command text script taken from content name SampleContent
```



## <a name="migration-complete"></a>Nach Abschluss der Migration

Die *Auftragsdatenbank* sollte nun alle migrierten Auftragsanmeldeinformationen, Ziele, Auftragsauslöser, Auftragszeitpläne, Auftragsinhalte und Aufträge enthalten.

Um zu überprüfen, ob diese alle korrekt migriert wurden, verwenden Sie folgende Skripts:

```powershell
$creds = $agent | Get-AzSqlElasticJobCredential
$targetGroups = $agent | Get-AzSqlElasticJobTargetGroup
$jobs = $agent | Get-AzSqlElasticJob
$steps = $jobs | Get-AzSqlElasticJobStep
```

Um zu testen, ob alle Aufträge ordnungsgemäß ausgeführt werden, starten Sie sie:

```powershell
$jobs | Start-AzSqlElasticJob
```

Denken Sie daran, dass Sie alle Aufträge, die nach einem Zeitplan ausgeführt wurden, aktivieren müssen, damit sie im Hintergrund ausgeführt werden können:

```powershell
$jobs | Set-AzSqlElasticJob -Enable
```

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Verwalten von elastischen Aufträgen mithilfe von PowerShell](elastic-jobs-powershell.md)
- [Erstellen und Verwalten von Aufträgen für die elastische Datenbank mithilfe von Transact-SQL (T-SQL)](elastic-jobs-tsql.md)
