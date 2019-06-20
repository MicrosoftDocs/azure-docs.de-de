---
title: Verwalten von Azure Data Lake Analytics mithilfe von Azure PowerShell
description: In diesem Artikel wird beschrieben, wie Sie Azure PowerShell zum Verwalten von Data Lake Analytics-Konten, -Datenquellen, -Benutzern und -Aufträgen verwenden.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.topic: conceptual
ms.date: 06/29/2018
ms.openlocfilehash: 4273828c9c2bdb75fcbc1de45da55c5a03dd615f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66156438"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Verwalten von Azure Data Lake Analytics mithilfe von Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

In diesem Artikel wird beschrieben, wie Sie Azure Data Lake Analytics-Konten, -Datenquellen, -Benutzer und -Aufträge mit Azure PowerShell verwalten.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sammeln Sie die folgenden Informationen, um PowerShell mit Data Lake Analytics zu verwenden: 

* **Abonnement-ID**: Die ID des Azure-Abonnements, das Ihr Data Lake Analytics-Konto enthält.
* **Ressourcengruppe**: Der Name der Azure-Ressourcengruppe, die Ihr Data Lake Analytics-Konto enthält.
* **Name des Data Lake Analytics-Kontos:** Der Name Ihres Data Lake Analytics-Kontos.
* **Name des Data Lake Store-Standardkontos:** Jedes Data Lake Analytics-Konto verfügt über ein Data Lake Store-Standardkonto.
* **Standort**: Der Standort des Data Lake Analytics-Kontos, z.B. „USA, Osten 2“, oder andere unterstützte Standorte.

In den PowerShell-Codeausschnitten dieses Tutorials werden die folgenden Variablen zum Speichern dieser Informationen verwendet:

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in-to-azure"></a>Anmelden an Azure

### <a name="log-in-using-interactive-user-authentication"></a>Anmelden per interaktiver Benutzerauthentifizierung

Anmelden per Abonnement-ID oder Abonnementname

```powershell
# Using subscription id
Connect-AzAccount -SubscriptionId $subId

# Using subscription name
Connect-AzAccount -SubscriptionName $subname 
```

## <a name="saving-authentication-context"></a>Speichern des Authentifizierungskontexts

Das Cmdlet `Connect-AzAccount` fordert immer zur Eingabe von Anmeldeinformationen auf. Damit diese Aufforderung nicht erfolgt, können Sie die folgenden Cmdlets verwenden:

```powershell
# Save login session information
Save-AzAccounts -Path D:\profile.json  

# Load login session information
Select-AzAccounts -Path D:\profile.json 
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>Anmelden per Service Principal Identity (SPI)

```powershell
$tenantid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"  
$spi_appname = "appname" 
$spi_appid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" 
$spi_secret = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" 

$pscredential = New-Object System.Management.Automation.PSCredential ($spi_appid, (ConvertTo-SecureString $spi_secret -AsPlainText -Force))
Login-AzAccount -ServicePrincipal -TenantId $tenantid -Credential $pscredential -Subscription $subid
```

## <a name="manage-accounts"></a>Konten verwalten


### <a name="list-accounts"></a>Auflisten von Konten

```powershell
# List Data Lake Analytics accounts within the current subscription.
Get-AdlAnalyticsAccount

# List Data Lake Analytics accounts within a specific resource group.
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

### <a name="create-an-account"></a>Erstellen eines Kontos

Für jedes Data Lake Analytics-Konto ist ein Data Lake Store-Standardkonto erforderlich, das zum Speichern von Protokollen verwendet wird. Sie können ein vorhandenes Konto wiederverwenden oder ein Konto erstellen. 

```powershell
# Create a data lake store if needed, or you can re-use an existing one
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-account-information"></a>Abrufen von Kontoinformationen

Rufen Sie Details zu einem Konto ab.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

### <a name="check-if-an-account-exists"></a>Überprüfen, ob ein Konto vorhanden ist

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

## <a name="manage-data-sources"></a>Verwalten von Datenquellen
Azure Data Lake Analytics unterstützt derzeit die folgenden Datenquellen:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage (in englischer Sprache)](../storage/common/storage-introduction.md)

Für jedes Data Lake Analytics-Konto ist ein Data Lake Store-Standardkonto vorhanden. Das Data Lake Store-Standardkonto dient zum Speichern von Auftragsmetadaten und -überwachungsprotokollen. 

### <a name="find-the-default-data-lake-store-account"></a>Ermitteln des Data Lake-Standardspeicherkontos

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

Sie können das Standardkonto für den Data Lake-Speicher finden, indem Sie die Liste der Datenquellen nach der `IsDefault`-Eigenschaft filtern:

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault } 
```

### <a name="add-a-data-source"></a>Hinzufügen einer Datenquelle

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>Auflisten von Datenquellen

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Account $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>Übermitteln von U-SQL-Aufträgen

### <a name="submit-a-string-as-a-u-sql-job"></a>Übermitteln einer Zeichenfolge als U-SQL-Auftrag

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```

### <a name="submit-a-file-as-a-u-sql-job"></a>Übermitteln einer Datei als U-SQL-Auftrag

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

### <a name="list-jobs"></a>Auflisten von Aufträgen

Die Ausgabe enthält die derzeit ausgeführten Aufträge sowie die Aufträge, die vor Kurzem abgeschlossen wurden.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>Auflisten der Top N Aufträge

Die Liste der Aufträge wird standardmäßig nach der Übermittlungszeit sortiert. Die zuletzt gesendeten Aufträge werden somit zuerst angezeigt. Im ADLA-Konto werden Aufträge standardmäßig 180 Tage lang beibehalten, mit dem Cmdlet „Get-AdlJob“ werden jedoch nur die ersten 500 Aufträge zurückgegeben. Verwenden Sie den Parameter „-Top“, um eine bestimmte Anzahl von Aufträgen aufzulisten.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>Auflisten von Aufträgen nach Auftragsstatus

Mithilfe des Parameters `-State`. Sie können jeden der folgenden Werte kombinieren:

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

### <a name="list-jobs-by-job-result"></a>Auflisten von Aufträgen nach Auftragsergebnis

Verwenden Sie den Parameter `-Result`, um zu erkennen, ob die beendeten Aufträge erfolgreich abgeschlossen wurden. Er enthält folgende Werte:

* Abgebrochen
* Fehler
* Keine
* Succeeded

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

### <a name="list-jobs-by-job-submitter"></a>Auflisten von Aufträgen nach dem Übermittler des Auftrags

Mithilfe des Parameters `-Submitter` können Sie erkennen, wer einen Auftrag übermittelt hat.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>Auflisten von Aufträgen nach der Übermittlungszeit

Der Parameter `-SubmittedAfter` ist nützlich, um nach einem Zeitbereich zu filtern.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="get-job-status"></a>Abrufen des Auftragsstatus

Rufen Sie den Status eines bestimmten Auftrags ab.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```


### <a name="cancel-a-job"></a>Abbrechen eines Auftrags

```powershell
Stop-AdlJob -Account $adla -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Warten auf den Abschluss eines Auftrags

Anstatt `Get-AdlAnalyticsJob` zu wiederholen, bis ein Auftrag abgeschlossen ist, können Sie das Cmdlet `Wait-AdlJob` verwenden, um zu warten, bis der Auftrag beendet wurde.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="analyzing-job-history"></a>Analysieren des Auftragsverlaufs

Mit Azure PowerShell zum Analysieren des Verlaufs von Aufträgen, die in Data Lake Analytics ausgeführt wurden, steht Ihnen eine wirkungsvolle Methode zur Verfügung. Dadurch können Sie Einblick in die Verwendung und die Kosten erhalten. Um mehr zu erfahren, sehen Sie sich das [Beispielrepository für die Analyse des Auftragsverlaufs](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis) an.  

## <a name="list-job-pipelines-and-recurrences"></a>Auflisten von Auftragspipelines und -wiederholungen

Verwenden Sie das Cmdlet `Get-AdlJobPipeline`, um die Pipelineinformationen für zuvor übermittelte Aufträge anzuzeigen.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Verwenden Sie das Cmdlet `Get-AdlJobRecurrence`, um die Wiederholungsinformationen für zuvor übermittelte Aufträge anzuzeigen.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```


## <a name="manage-compute-policies"></a>Verwalten von Computerichtlinien

### <a name="list-existing-compute-policies"></a>Auflisten vorhandener Computerichtlinien

Durch das Cmdlet `Get-AdlAnalyticsComputePolicy` werden Informationen über Computerichtlinien für ein Data Lake Analytics-Konto abgerufen.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Erstellen einer Computerichtlinie

Durch das Cmdlet `New-AdlAnalyticsComputePolicy` werden Informationen über Computerichtlinien für ein Data Lake Analytics-Konto abgerufen. In diesem Beispiel werden die maximalen AUs, die für den angegebenen Benutzer verfügbar sind, auf 50 und die minimale Auftragspriorität auf 250 festgelegt.

```powershell
$userObjectId = (Get-AzAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```
## <a name="manage-files"></a>Verwalten von Dateien

### <a name="check-for-the-existence-of-a-file"></a>Überprüfen Sie das Vorhandensein einer Datei.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="uploading-and-downloading"></a>Hoch- und Herunterladen

Laden Sie eine Datei hoch.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

Laden Sie einen gesamten Ordner rekursiv hoch.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Laden Sie eine Datei herunter.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Laden Sie einen gesamten Ordner rekursiv herunter.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Wenn der Upload- oder Downloadvorgang unterbrochen wird, können Sie versuchen, den Vorgang fortzusetzen, indem Sie das Cmdlet mit dem Flag ``-Resume`` erneut ausführen.

## <a name="manage-the-u-sql-catalog"></a>Verwalten des U-SQL-Katalogs

Der U-SQL-Katalog wird zum Strukturieren von Daten und Code verwendet, damit diese von U-SQL-Skripts gemeinsam genutzt werden können. Der Katalog ermöglicht die höchstmögliche Leistung mit Daten in Azure Data Lake. Weitere Informationen finden Sie unter [Verwenden des U-SQL-Katalogs](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-items-in-the-u-sql-catalog"></a>Auflisten von Elementen im U-SQL-Katalog

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>Auflisten aller Assemblys des U-SQL-Katalogs

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>Abrufen von Details zu einem Katalogelement

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="store-credentials-in-the-catalog"></a>Speichern von Anmeldeinformationen im Katalog

Erstellen Sie innerhalb einer U-SQL-Datenbank ein credential-Objekt für eine in Azure gehostete Datenbank. Derzeit können über PowerShell lediglich U-SQL-Anmeldeinformationen als Katalogelement erstellt werden.

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

## <a name="manage-firewall-rules"></a>Verwalten von Firewallregeln

### <a name="list-firewall-rules"></a>Auflisten von Firewallregeln

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

### <a name="add-a-firewall-rule"></a>Hinzufügen einer Firewallregel

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="modify-a-firewall-rule"></a>Ändern einer Firewallregel

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>Entfernen einer Firewallregel

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>Zulassen von Azure-IP-Adressen

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```


## <a name="working-with-azure"></a>Arbeiten mit Azure

### <a name="get-error-details"></a>Abrufen von Fehlerdetails

```powershell
Resolve-AzError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator-on-your-windows-machine"></a>Überprüfen, ob die Ausführung auf Ihrem Windows-Computer als Administrator erfolgt

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>Suchen einer TenantID

Über einen Abonnementnamen:

```powershell
function Get-TenantIdFromSubscriptionName( [string] $subname )
{
    $sub = (Get-AzSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubscriptionName "ADLTrainingMS"
```

Über eine Abonnement-ID:

```powershell
function Get-TenantIdFromSubscriptionId( [string] $subid )
{
    $sub = (Get-AzSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubscriptionId $subid
```

Über eine Domänenadresse wie z.B. „contoso.com“:

```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Auflisten Ihrer gesamten Abonnements und Mandanten-IDs

```powershell
$subs = Get-AzSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Erstellen eines Data Lake Analytics-Kontos mithilfe einer Vorlage

Sie können eine Azure-Ressourcengruppenvorlage auch mit dem folgenden Beispiel verwenden: [Erstellen eines Data Lake Analytics-Kontos mithilfe einer Vorlage](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)

## <a name="next-steps"></a>Nächste Schritte
* [Übersicht über Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* Erste Schritte mit Data Lake Analytics mithilfe [des Azure-Portals](data-lake-analytics-get-started-portal.md) | [von Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [der Azure CLI](data-lake-analytics-get-started-cli.md)
* Verwalten von Azure Data Lake Analytics mithilfe [des Azure-Portals](data-lake-analytics-manage-use-portal.md) | [von Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [der Befehlszeilenschnittstelle](data-lake-analytics-manage-use-cli.md) 
