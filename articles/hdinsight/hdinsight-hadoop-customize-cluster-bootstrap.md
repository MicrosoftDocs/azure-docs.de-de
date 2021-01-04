---
title: Anpassen von Azure HDInsight-Clusterkonfigurationen mithilfe von Bootstrap
description: Erfahren Sie, wie Sie die HDInsight-Clusterkonfiguration mithilfe von .NET-, PowerShell- und Resource Manager-Vorlagen programmgesteuert anpassen können.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/01/2020
ms.openlocfilehash: 246c5600da3b554ba65872780f0719a58f3f4be2
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547485"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Anpassen von HDInsight-Clustern mithilfe von Bootstrap

Bootstrap-Skripts ermöglichen Ihnen das programmgesteuerte Installieren und Konfigurieren von Komponenten in Azure HDInsight.

Beim Erstellen Ihres HDInsight-Clusters gibt es drei Ansätze zum Festlegen der Einstellungen für die Konfigurationsdatei:

* Mithilfe von Azure PowerShell
* Verwenden von .NET SDK
* Verwenden von Azure Resource Manager-Vorlagen

Mit diesen programmgesteuerten Methoden können Sie z. B. Optionen in diesen Dateien konfigurieren:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* server.properties (Kafka-Brokerkonfiguration)

Informationen zum Installieren zusätzlicher Komponenten in HDInsight-Clustern während ihrer Erstellung finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Voraussetzungen

* Bei Verwendung von PowerShell benötigen Sie das [Az-Modul](/powershell/azure/).

## <a name="use-azure-powershell"></a>Mithilfe von Azure PowerShell

Der folgende PowerShell-Code passt eine [Apache Hive](https://hive.apache.org/)-Konfiguration an:

> [!IMPORTANT]  
> Möglicherweise muss der Parameter `Spark2Defaults` mit [Add-AzHDInsightConfigValue](/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) verwendet werdet. Sie können leere Werte an den Parameter übergeben, wie im folgenden Codebeispiel gezeigt.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90s" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

Ein vollständiges funktionierendes PowerShell-Skript finden Sie im [Anhang](#appendix-powershell-sample).

**So überprüfen Sie die Änderung:**

1. Navigieren Sie zu `https://CLUSTERNAME.azurehdinsight.net/`, wobei `CLUSTERNAME` der Name Ihres Clusters ist.
1. Navigieren Sie im Menü auf der linken Seite zu **Hive** > **Configs** > **Advanced** .
1. Erweitern Sie **Advanced hive-site** .
1. Suchen Sie **hive.metastore.client.socket.timeout** , und vergewissern Sie sich, dass der Wert **90s** lautet.

Weitere Beispiele zum Anpassen anderer Konfigurationsdateien:

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>Verwenden von .NET SDK

Weitere Informationen finden Sie unter [Azure HDInsight SDK für .NET](/dotnet/api/overview/azure/hdinsight?view=azure-dotnet&preserve-view=true).

## <a name="use-resource-manager-template"></a>Verwenden von Resource Manager-Vorlagen

Sie können Bootstrap in einer Resource Manager-Vorlage verwenden:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![Hadoop passt Azure Resource Manager-Vorlage für Cluster-Bootstrap an](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

Codeausschnitt aus der Resource Manager-Beispielvorlage zum Umschalten der Konfiguration in „spark2-defaults“ zum regelmäßigen Bereinigen von Ereignisprotokollen aus dem Speicher.  

```json
"configurations": {
    "spark2-defaults": {
        "spark.history.fs.cleaner.enabled": "true",
        "spark.history.fs.cleaner.interval": "7d",
        "spark.history.fs.cleaner.maxAge": "90d"
    }
}
```

## <a name="see-also"></a>Weitere Informationen

* Unter [Erstellen von Apache Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) finden Sie Anweisungen zum Erstellen eines HDInsight-Clusters mit anderen benutzerdefinierten Optionen.
* [Entwickeln von Script Action-Skripts für HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Installieren und Verwenden von Apache Spark in HDInsight-Clustern](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [Installieren und Verwenden von Apache Giraph in HDInsight-Clustern](./hdinsight-hadoop-hue-linux.md)

## <a name="appendix-powershell-sample"></a>Anhang: PowerShell-Beispiel

Dieses PowerShell-Skript erstellt einen HDInsight-Cluster und passt eine Hive-Einstellung an. Achten Sie darauf, Werte für `$nameToken`, `$httpPassword` und `$sshPassword` einzugeben.

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>"
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>'

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>'
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

# Note: Storage account kind BlobStorage cannot be used as primary storage.

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90s"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```