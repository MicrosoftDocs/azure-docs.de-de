---
title: Verwalten von Apache Hadoop-Clustern in HDInsight mit PowerShell – Azure
description: Hier erfahren Sie, wie Sie administrative Aufgaben für Apache Hadoop-Cluster in HDInsight mit Azure PowerShell ausführen.
ms.reviewer: tyfox
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: tylerfox
ms.openlocfilehash: a7e129f43b957b271c77f451ab198a9068bb0797
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64718996"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Verwalten von Apache Hadoop-Clustern in HDInsight mit Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell kann zum Steuern und Automatisieren der Bereitstellung und Verwaltung Ihrer Workloads in Azure verwendet werden. In diesem Artikel erfahren Sie, wie Sie [Apache Hadoop](https://hadoop.apache.org/)-Cluster in Azure HDInsight mithilfe des Azure PowerShell-Az-Moduls verwalten. Eine Liste der HDInsight PowerShell-Cmdlets finden Sie unter [Az.HDInsight-Referenz](https://docs.microsoft.com/powershell/module/az.hdinsight).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Das PowerShell-[Az-Modul](https://docs.microsoft.com/powershell/azure/overview) ist installiert.

## <a name="create-clusters"></a>Erstellen von Clustern
Siehe [Erstellen von Linux-basierten Clustern in HDInsight mit Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Auflisten von Clustern
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
Mithilfe der Clusterskalierung können Sie die Anzahl der von einem in Azure HDInsight ausgeführten Cluster verwendeten Workerknoten ändern, ohne den Cluster neu erstellen zu müssen.

Auswirkungen der Änderung der Anzahl von Datenknoten für die von HDInsight unterstützten Clustertypen:

* Apache Hadoop

    Sie können die Anzahl der Workerknoten in einem aktiven Hadoop-Cluster problemlos ohne Auswirkungen auf ausstehende oder aktive Aufträge erhöhen. Neue Aufträge können auch während des Vorgangs gesendet werden. Fehler bei einer Skalierung werden ordnungsgemäß behandelt, sodass der Cluster immer in einem funktionsfähigen Zustand verbleibt.

    Wenn ein Hadoop-Cluster durch Verringern der Anzahl der Datenknoten zentral herunterskaliert wird, werden einige der Dienste im Cluster neu gestartet. Ein Neustart von Diensten führt beim Abschluss des Skalierungsvorgangs bei allen aktiven und ausstehenden Aufträgen zu einem Fehler. Sie können die Aufträge jedoch nach Abschluss des Vorgangs erneut senden.
* Apache HBase

    Sie können Knoten reibungslos Ihrem HBase-Cluster hinzufügen oder aus diesem entfernen, während er aktiv ist. Regionale Server werden innerhalb weniger Minuten nach Abschluss des Skalierungsvorgangs automatisch ausgeglichen. Allerdings können Sie die regionalen Server auch manuell ausgleichen, indem Sie sich am Hauptknoten des Clusters anmelden und in einem Eingabeaufforderungsfenster die folgenden Befehle ausführen:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

* Apache Storm

    Sie können Datenknoten übergangslos zum Storm-Cluster hinzufügen oder aus diesem entfernen, während er aktiv ist. Nach erfolgreichen Abschluss des Skalierungsvorgangs müssen Sie die Topologie neu ausgleichen.

    Es stehen zwei Methoden für den erneuten Ausgleich zur Verfügung:

  * Storm-Webbenutzeroberfläche
  * Befehlszeilenschnittstelle (CLI)

    Weitere Informationen finden Sie in der [Apache Storm-Dokumentation](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

    Die Storm-Webbenutzeroberfläche ist für den HDInsight-Cluster verfügbar:

    ![HDInsight Storm-Skalierung ausgleichen](./media/hdinsight-administer-use-powershell/hdinsight.portal.scale.cluster.png)

    Es folgt ein Beispiel, wie die Storm-Topologie mithilfe des CLI-Befehls neu ausgeglichen werden kann:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Führen Sie den folgenden Befehl auf einem Clientcomputer aus, um die Hadoop-Clustergröße mithilfe von Azure PowerShell zu ändern:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


## <a name="grantrevoke-access"></a>Gewähren/Entziehen des Zugriffs
In HDInsight-Clustern stehen die folgenden HTTP-Webdienste zur Verfügung (alle mit RESTful-Endpunkten):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Der Zugriff auf diese Dienste wird standardmäßig gewährt. Sie können den Zugriff widerrufen/gewähren. Zum Widerrufen:

```powershell
Revoke-AzHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

Zum Gewähren:

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = "<Enter the Password>"
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]  
> Durch Gewähren/Widerrufen des Zugriffs werden der Benutzername und das Kennwort des Clusterbenutzers zurückgesetzt.

Der Zugriff kann auch über das Portal gewährt bzw. widerrufen werden. Weitere Informationen finden Sie unter [Verwalten von Apache Hadoop-Clustern in HDInsight mit dem Azure-Portal](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Aktualisieren von HTTP-Anmeldeinformationen
Dabei handelt es sich um die gleiche Vorgehensweise wie beim Gewähren/Widerrufen des HTTP-Zugriffs. Wenn dem Cluster der HTTP-Zugriff gewährt wurde, müssen Sie ihn zunächst widerrufen.  Gewähren Sie anschließend den Zugriff mit den neuen HTTP-Anmeldeinformationen.

## <a name="find-the-default-storage-account"></a>Suchen des Standardspeicherkontos
Das folgende PowerShell-Skript veranschaulicht, wie der Name und zugehörigen Informationen des Standardspeicherkontos abgerufen werden:

> [!IMPORTANT]  
> Die Werte für `DefaultStorageAccount` und `DefaultStorageContainer` werden nicht von [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) zurückgegeben, wenn für das Speicherkonto die [sichere Übertragung](../storage/common/storage-require-secure-transfer.md) aktiviert ist.


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

**So übermitteln Sie Apache Pig-Aufträge**

Weitere Informationen finden Sie unter [Ausführen von Apache Pig-Aufträgen mit PowerShell](hadoop/apache-hadoop-use-pig-powershell.md).

**So übermitteln Sie Apache Sqoop-Aufträge**

Weitere Informationen finden Sie unter [Verwenden von Apache Hadoop mit HDInsight](hadoop/hdinsight-use-sqoop.md).

**So übermitteln Sie Apache Oozie-Aufträge**

Weitere Informationen finden Sie unter [Verwenden von Apache Oozie mit Apache Hadoop zum Definieren und Ausführen eines Workflows in HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Hochladen von Daten in Azure Blob Storage

Siehe [Hochladen von Daten in HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Siehe auch

* [Referenzdokumentation zum HDInsight-Cmdlet](https://msdn.microsoft.com/library/azure/dn479228.aspx)
* [Verwalten von Apache Hadoop-Clustern in HDInsight mit dem Azure-Portal](hdinsight-administer-use-portal-linux.md)
* [Verwalten von HDInsight über eine Befehlszeilenschnittstelle](hdinsight-administer-use-command-line.md)
* [Erstellen von Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Programmgesteuertes Übermitteln von Apache Hadoop-Aufträgen](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Erste Schritte mit Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
