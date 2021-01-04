---
title: Verwalten von Apache Hadoop-Clustern mit PowerShell – Azure HDInsight
description: Hier erfahren Sie, wie Sie administrative Aufgaben für Apache Hadoop-Cluster in HDInsight mit Azure PowerShell ausführen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/13/2020
ms.openlocfilehash: 0b4a31b88c9a6fa8c2c6170fce38760c1a361f2e
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533426"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Verwalten von Apache Hadoop-Clustern in HDInsight mit Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell kann zum Steuern und Automatisieren der Bereitstellung und Verwaltung Ihrer Workloads in Azure verwendet werden. In diesem Artikel erfahren Sie, wie Sie [Apache Hadoop](https://hadoop.apache.org/)-Cluster in Azure HDInsight mithilfe des Azure PowerShell-Az-Moduls verwalten. Eine Liste der HDInsight PowerShell-Cmdlets finden Sie unter [Az.HDInsight-Referenz](/powershell/module/az.hdinsight).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Das PowerShell-[Az-Modul](/powershell/azure/) ist installiert.

## <a name="create-clusters"></a>Erstellen von Clustern

Siehe [Erstellen von Linux-basierten Clustern in HDInsight mit Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Auflisten der Cluster

Verwenden Sie den folgenden Befehl, um alle Cluster des aktuellen Abonnements aufzulisten:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Anzeigen von Clustern

Verwenden Sie den folgenden Befehl, um Details zu einem bestimmten Cluster des aktuellen Abonnements anzuzeigen:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Löschen von Clustern

Mit dem folgenden Befehl können Sie ein Cluster löschen:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

Sie können einen Cluster auch löschen, indem Sie die Ressourcengruppe entfernen, die den Cluster enthält. Beim Löschen einer Ressourcengruppe werden alle Ressourcen in der Gruppe, einschließlich des Standardspeicherkontos, gelöscht.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Skalieren von Clustern

Mithilfe der Clusterskalierung können Sie die Anzahl der von einem in Azure HDInsight ausgeführten Cluster verwendeten Workerknoten ändern, ohne den Cluster neu erstellen zu müssen. Führen Sie den folgenden Befehl auf einem Clientcomputer aus, um die Hadoop-Clustergröße mithilfe von Azure PowerShell zu ändern:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 Weitere Informationen zum Skalieren von Clustern finden Sie unter [Skalieren von HDInsight-Clustern](./hdinsight-scaling-best-practices.md).

## <a name="update-http-user-credentials"></a>Aktualisieren von HTTP-Anmeldeinformationen

[Set-AzHDInsightGatewayCredential](/powershell/module/az.hdinsight/set-azhdinsightgatewaycredential) legt die Gateway-HTTP-Anmeldeinformationen eines Azure HDInsight-Clusters fest.

```powershell
$clusterName = "CLUSTERNAME"
$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Set-AzHDInsightGatewayCredential -ClusterName $clusterName -HttpCredential $credential
```

## <a name="find-the-default-storage-account"></a>Suchen des Standardspeicherkontos

Das folgende PowerShell-Skript veranschaulicht, wie der Name und zugehörigen Informationen des Standardspeicherkontos abgerufen werden:

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```

## <a name="find-the-resource-group"></a>Suchen der Ressourcengruppe

Im Resource Manager-Modus gehört jeder HDInsight-Cluster einer Azure-Ressourcengruppe an.  So finden Sie die Ressourcengruppe:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>Übermitteln von Aufträgen

**So übermitteln Sie MapReduce-Aufträge**

Weitere Informationen finden Sie unter [Ausführen von MapReduce-Beispielen in HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**So übermitteln Sie Apache Hive-Aufträge**

Weitere Informationen finden Sie unter [Ausführen von Apache Hive-Abfragen mit PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**So übermitteln Sie Apache Sqoop-Aufträge**

Weitere Informationen finden Sie unter [Verwenden von Apache Hadoop mit HDInsight](hadoop/hdinsight-use-sqoop.md).

**So übermitteln Sie Apache Oozie-Aufträge**

Weitere Informationen finden Sie unter [Verwenden von Apache Oozie mit Apache Hadoop zum Definieren und Ausführen eines Workflows in HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Hochladen von Daten in Azure Blob Storage

Siehe [Hochladen von Daten in HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Weitere Informationen

* [Az.HDInsight-Cmdlets](/powershell/module/az.hdinsight/#hdinsight)
* [Verwalten von Apache Hadoop-Clustern in HDInsight mit dem Azure-Portal](hdinsight-administer-use-portal-linux.md)
* [Verwalten von HDInsight über eine Befehlszeilenschnittstelle](hdinsight-administer-use-command-line.md)
* [Erstellen von Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Programmgesteuertes Übermitteln von Apache Hadoop-Aufträgen](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Erste Schritte mit Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)