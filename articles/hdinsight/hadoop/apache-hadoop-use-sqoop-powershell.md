---
title: Ausführen von Apache Sqoop-Aufträgen mit PowerShell und Azure HDInsight
description: Erfahren Sie, wie Sie Azure PowerShell auf einer Arbeitsstation verwenden können, um Apache Sqoop-Importe und -Exporte zwischen einem Apache Hadoop-Cluster und einer Azure SQL-Datenbank auszuführen.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: hrasheed
ms.openlocfilehash: 49375b00607f1f23a99855f8b80f5f5c3c4dac22
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64686671"
---
# <a name="run-apache-sqoop-jobs-by-using-azure-powershell-for-apache-hadoop-in-hdinsight"></a>Ausführen von Apache Sqoop-Aufträgen mit Azure PowerShell für Apache Hadoop in HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Erfahren Sie, wie Azure PowerShell zum Ausführen von Apache Sqoop-Aufträgen in Azure HDInsight für das Importieren und Exportieren von Daten zwischen einem HDInsight-Cluster und einer Azure SQL-Datenbank oder SQL Server-Datenbank verwendet werden kann. In diesem Beispiel werden Daten aus `/tutorials/usesqoop/data/sample.log` aus dem Standardspeicherkonto exportiert und dann in eine Tabelle namens `log4jlogs` in einer SQL Server-Datenbank importiert. Dieser Artikel ist eine Fortsetzung von [Verwenden von Apache Sqoop mit Hadoop in HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, benötigen Sie Folgendes:

* Eine Arbeitsstation, auf der das Azure PowerShell [AZ-Modul](https://docs.microsoft.com/powershell/azure/overview) installiert ist.

* Das [Einrichten der Testumgebung](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) unter [Verwenden von Apache Sqoop mit Hadoop in HDInsight](./hdinsight-use-sqoop.md) muss abgeschlossen sein.


## <a name="run-apache-sqoop-by-using-powershell"></a>Ausführen von Apache Sqoop mit PowerShell
Das folgende PowerShell-Skript verarbeitet vorab die Quelldatei und exportiert sie dann in eine Azure SQL-Datenbank in Tabelle `log4jlogs`. Ersetzen Sie `CLUSTERNAME`, `CLUSTERPASSWORD` und `SQLPASSWORD` durch die Werte, die Sie unter den Voraussetzungen verwendet haben.

```powershell 
<#------ BEGIN USER INPUT ------#>
$hdinsightClusterName = "CLUSTERNAME"
$httpUserName = "admin"  #default is admin, update as needed
$httpPassword = 'CLUSTERPASSWORD'
$sqlDatabasePassword = 'SQLPASSWORD'
<#------- END USER INPUT -------#>

# Other fixed variable that should be used as is
$sqlServerName = $hdinsightClusterName + "dbserver"
$sqlDatabaseName = $hdinsightClusterName + "db"
$tableName_log4j = "log4jlogs"
$exportDir_log4j = "/tutorials/usesqoop/data"
$sourceBlobName = "example/data/sample.log"
$destBlobName = "tutorials/usesqoop/data/sample.log"
$sqljdbcdriver = "/user/oozie/share/lib/sqoop/mssql-jdbc-7.0.0.jre8.jar"

$cluster = Get-AzHDInsightCluster -ClusterName $hdinsightClusterName
$defaultStorageAccountName = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
$defaultStorageContainer = $cluster.DefaultStorageContainer
$resourceGroup = $cluster.ResourceGroup

$sqlServer = Get-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $sqlServerName
$sqlServerLogin = $sqlServer.SqlAdministratorLogin
$sqlServerFQDN = $sqlServer.FullyQualifiedDomainName

#Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzContext}
catch{Connect-AzAccount}

#pre-process the source file
Write-Host "`nPreprocessing the source file ..." -ForegroundColor Green

# This procedure creates a new file with $destBlobName
# Define the connection string
$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroup `
                                -Name $defaultStorageAccountName)[0].Value

# Create block blob objects referencing the source and destination blob.
$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroup `
    -Name $defaultStorageAccountName

$storageContainer = ($storageAccount |Get-AzStorageContainer -Name $defaultStorageContainer).CloudBlobContainer

$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

# Define a MemoryStream and a StreamReader for reading from the source file
$stream = New-Object System.IO.MemoryStream
$stream = $sourceBlob.OpenRead()
$sReader = New-Object System.IO.StreamReader($stream)

# Define a MemoryStream and a StreamWriter for writing into the destination file
$memStream = New-Object System.IO.MemoryStream
$writeStream = New-Object System.IO.StreamWriter $memStream

# Pre-process the source blob
$exString = "java.lang.Exception:"
while(-Not $sReader.EndOfStream){
    $line = $sReader.ReadLine()
    $split = $line.Split(" ")

    # remove the "java.lang.Exception" from the first element of the array
    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
    if ($split[0] -eq $exString){
        #create a new ArrayList to remove $split[0]
        $newArray = [System.Collections.ArrayList] $split
        $newArray.Remove($exString)

        # update $split and $line
        $split = $newArray
        $line = $newArray -join(" ")
    }

    # remove the lines that has less than 7 elements
    if ($split.count -ge 7){
        write-host $line
        $writeStream.WriteLine($line)
    }
}

# Write to the destination blob
$writeStream.Flush()
$memStream.Seek(0, "Begin")
$destBlob.UploadFromStream($memStream)

#export the log file from the cluster to the SQL database
Write-Host "Exporting the log file ..." -ForegroundColor Green

$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

# Connection string
$connectionString = "jdbc:sqlserver://$sqlServerFQDN;user=$sqlServerLogin@$sqlServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

# Submit a Sqoop job
$sqoopDef = New-AzHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1" `
    -Files $sqljdbcdriver

$sqoopJob = Start-AzHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef

Wait-AzHDInsightJob `
    -ResourceGroupName $resourceGroup `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroup `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultStorageContainer `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultStorageContainer `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput
```

## <a name="limitations"></a>Einschränkungen
Für Linux-basiertes HDInsight gelten die folgenden Einschränkungen:

* Massenexport: Derzeit unterstützt der zum Exportieren von Daten nach Microsoft SQL Server oder Azure SQL-Datenbank verwendete Sqoop-Connector keine Masseneinfügungen.

* Batchverarbeitung: Wenn Sie beim Ausführen von Einfügevorgängen den Schalter `-batch` verwenden, führt Sqoop mehrere Einfügevorgänge aus, anstatt diese zu einem Batch zusammenzufassen. 

## <a name="next-steps"></a>Nächste Schritte
Nun wissen Sie, wie Sqoop verwendet haben. Weitere Informationen finden Sie unter:

* [Verwenden von Apache Oozie mit HDInsight](../hdinsight-use-oozie-linux-mac.md): Verwenden der Sqoop-Aktion in einem Oozie-Workflow.
* [Hochladen von Daten in HDInsight](../hdinsight-upload-data.md): Weitere Methoden zum Hochladen von Daten in HDInsight oder Azure Blob Storage.

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
