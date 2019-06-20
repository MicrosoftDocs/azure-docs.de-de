---
title: Anpassen von Azure HDInsight-Clusterkonfigurationen mithilfe von Bootstrap
description: Erfahren Sie, wie Sie die HDInsight-Clusterkonfiguration mithilfe von .Net-, PowerShell- und Resource Manager-Vorlagen programmgesteuert anpassen können.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 7f9100686eaab8c4c75e3d862026b18b6c46ed09
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65203707"
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

* Bei der Verwendung von PowerShell benötigen Sie das [Az-Modul](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Mithilfe von Azure PowerShell

Der folgende PowerShell-Code passt eine [Apache Hive](https://hive.apache.org/)-Konfiguration an:

> [!IMPORTANT]  
> Möglicherweise muss der Parameter `Spark2Defaults` mit [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) verwendet werdet. Sie können leere Werte an den Parameter übergeben, wie im folgenden Codebeispiel gezeigt.


```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

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

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Klicken Sie im linken Menü auf **HDInsight-Cluster**. Klicken Sie zunächst auf **Alle Dienste**, falls die Option nicht angezeigt wird.
3. Klicken Sie auf den Cluster, den Sie gerade mit dem PowerShell-Skript erstellt haben.
4. Klicken Sie oben im Blatt auf **Dashboard** , um die Ambari-Benutzeroberfläche zu öffnen.
5. Klicken Sie im linken Menü auf **Hive** .
6. Klicken Sie unter **Summary** (Zusammenfassung) auf **HiveServer2**.
7. Klicken Sie auf die Registerkarte **Configs** .
8. Klicken Sie im linken Menü auf **Hive** .
9. Klicken Sie auf die Registerkarte **Advanced** .
10. Scrollen Sie nach unten, und erweitern Sie dann **Advanced hive-site**.
11. Suchen Sie im Abschnitt nach **hive.metastore.client.socket.timeout** .

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
Informationen hierzu finden Sie unter [Erstellen von Linux-basierten Clustern in HDInsight mit dem .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

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

![HDInsight Hadoop passt Cluster Bootstrap Azure Resource Manager-Vorlage an](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Weitere Informationen
* Unter [Erstellen von Apache Hadoop-Clustern in HDInsight][hdinsight-provision-cluster] finden Sie Anweisungen zum Erstellen eines HDInsight-Clusters mit anderen benutzerdefinierten Optionen.
* [Entwickeln von Skriptaktionsskripts für HDInsight][hdinsight-write-script]
* [Installieren und Verwenden von Apache Spark in HDInsight-Clustern][hdinsight-install-spark]
* [Installieren und Verwenden von Apache Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install.md)

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions-linux.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Phasen während der Clustererstellung"

## <a name="appendix-powershell-sample"></a>Anhang: PowerShell-Beispiel

Dieses PowerShell-Skript erstellt einen HDInsight-Cluster und passt eine Hive-Einstellung an. Achten Sie darauf, Werte für `$nameToken`, `$httpPassword` und `$sshPassword` einzugeben.

> [!IMPORTANT]  
> Die Werte für `DefaultStorageAccount` und `DefaultStorageContainer` werden nicht von [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) zurückgegeben, wenn für das Speicherkonto die [sichere Übertragung](../storage/common/storage-require-secure-transfer.md) aktiviert ist.

> [!WARNING]  
> Speicherkonten vom Typ `BlobStorage` können nicht für HDInsight-Cluster verwendet werden.


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
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

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
