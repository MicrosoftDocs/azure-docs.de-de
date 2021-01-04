---
title: Migrieren zu Azure Resource Manager-Tools für HDInsight
description: 'Gewusst wie: Migrieren zu Azure Resource Manager-basierten Entwicklungstools für HDInsight-Cluster'
ms.reviewer: jasonh
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive, devx-track-azurecli
ms.topic: how-to
ms.date: 02/21/2018
ms.openlocfilehash: 57dec799cbda03e20717a402a88f1d818d9acd92
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629475"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrieren zu Azure Resource Manager-basierten Entwicklungstools für HDInsight-Cluster

Die Azure Service Manager (ASM)-basierten Tools für HDInsight sind veraltet und werden ersetzt. Wenn Sie bisher Azure PowerShell, die klassische Azure CLI oder das HDInsight .NET SDK für die Arbeit mit HDInsight-Clustern verwendet haben, wird Ihnen empfohlen, von nun an die Azure Resource Manager-Versionen von PowerShell, der CLI und des .NET SDK zu verwenden. Dieser Artikel bietet Ihnen hilfreiche Informationen für die Migration zum neuen Resource Manager-basierten Ansatz. Außerdem weist dieses Dokument auch auf eventuelle Unterschiede zwischen den ASM- und Resource Manager-basierten Ansätzen für HDInsight hin.

> [!IMPORTANT]  
> Die ASM-basierten Versionen von PowerShell, der CLI und des .NET SDK werden noch bis zum **1. Januar 2017** unterstützt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Migrieren der klassischen Azure CLI zu Azure Resource Manager

> [!IMPORTANT]  
> Azure CLI stellt keinen Support für die Arbeit mit HDInsight-Clustern zur Verfügung. Sie können die klassische Azure CLI weiterhin mit HDInsight verwenden, jedoch ist die klassische Azure CLI inzwischen veraltet.

Folgende sind grundlegende Befehle für die Arbeit mit HDInsight über die klassische Azure CLI:

* `azure hdinsight cluster create` – erstellt einen neuen HDInsight-Cluster
* `azure hdinsight cluster delete` – löscht einen vorhandenen HDInsight-Cluster
* `azure hdinsight cluster show` – zeigt Informationen zu einem vorhandenen Cluster an
* `azure hdinsight cluster list` – listet HDInsight-Cluster für Ihr Azure-Abonnement auf

Verwenden Sie den Switch `-h` , um die Parameter und Switches für jeden Befehl zu überprüfen.

### <a name="new-commands"></a>Neue Befehle
Neue mit Azure Resource Manager verfügbare Befehle sind:

* `azure hdinsight cluster resize` – ändert dynamisch die Anzahl von Workerknoten im Cluster
* `azure hdinsight cluster enable-http-access` – aktiviert HTTPs-Zugriff auf den Cluster (standardmäßig aktiviert)
* `azure hdinsight cluster disable-http-access` – deaktiviert den HTTPs-Zugriff auf den Cluster
* `azure hdinsight script-action` – enthält Befehle zum Erstellen/Verwalten von Skriptaktionen auf einem Cluster
* `azure hdinsight config` – enthält Befehle zum Erstellen einer Konfigurationsdatei, die mit dem Befehl `hdinsight cluster create` zum Bereitstellen von Konfigurationsinformationen genutzt werden kann

### <a name="deprecated-commands"></a>Veraltete Befehle
Die `azure hdinsight job`-Befehle, die Sie ggf. zum Übermitteln von Aufträgen an Ihren HDInsight-Cluster verwenden, stehen in den Resource Manager-Befehlen nicht zur Verfügung. Wenn Sie Aufträge programmgesteuert von Skripts an HDInsight übermitteln möchten, verwenden Sie die von HDInsight bereitgestellten REST-APIs. Weitere Informationen zum Übermitteln von Aufträgen mithilfe von REST-APIs finden Sie in den folgenden Dokumenten.

* [Ausführen von MapReduce-Aufträgen mit Hadoop in HDInsight mithilfe von Curl](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Ausführen von Apache Hive-Abfragen mit Apache Hadoop in HDInsight mit Curl](hadoop/apache-hadoop-use-hive-curl.md)


Informationen zu anderen Möglichkeiten zum interaktiven Ausführen von Apache Hadoop MapReduce, Apache Hive und Apache Pig finden Sie unter [Verwenden von MapReduce mit Apache Hadoop in HDInsight](hadoop/hdinsight-use-mapreduce.md), [Verwenden von Apache Hive mit Apache Hadoop auf HDInsight](hadoop/hdinsight-use-hive.md) und [Verwenden von Apache Pig mit Apache Hadoop auf HDInsight](./index.yml).

### <a name="examples"></a>Beispiele
**Erstellen eines Clusters**

* Alter Befehl (ASM) – `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Neuer Befehl – `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Löschen eines Clusters**

* Alter Befehl (ASM) – `azure hdinsight cluster delete myhdicluster`
* Neuer Befehl – `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Auflisten von Clustern**

* Alter Befehl (ASM) – `azure hdinsight cluster list`
* Neuer Befehl – `azure hdinsight cluster list`

> [!NOTE]  
> Wenn die Ressourcengruppe für den Listenbefehl mithilfe von `-g` festgelegt wird, werden nur die Cluster in der angegebenen Ressourcengruppe zurückgegeben.

**Anzeigen von Clusterinformationen**

* Alter Befehl (ASM) – `azure hdinsight cluster show myhdicluster`
* Neuer Befehl – `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrieren von Azure PowerShell zu Azure Resource Manager
Allgemeine Informationen zu Azure PowerShell im Azure Resource Manager-Modus finden Sie unter [Verwenden von Windows PowerShell mit dem Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

Azure PowerShell-Resource-Manager-Cmdlets können neben ASM-Cmdlets installiert werden. Die Cmdlets der beiden Modi können nach ihren Namen unterschieden werden.  Die Namen der Cmdlets enthalten im Resource Manager-Modus *AzHDInsight* , im älteren ASM-Modus hingegen *AzureHDInsight*.  Beispiel: *New-AzHDInsightCluster* und *New-AzureHDInsightCluster* Parameter und Switches haben unter Umständen neue Namen, und für die Verwendung von Resource Manager stehen viele neue Parameter zur Verfügung.  So erfordern beispielsweise verschiedene Cmdlets einen neuen Switch namens *-ResourceGroupName*.

Bevor Sie die HDInsight-Cmdlets verwenden können, müssen sich mit Ihrem Azure-Konto verbinden und eine neue Ressourcengruppe erstellen:

* [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)

### <a name="renamed-cmdlets"></a>Umbenannte Cmdlets
So listen Sie die HDInsight-ASM-Cmdlets in der Windows-PowerShell-Konsole auf:

```powershell
help *azurehdinsight*
```

Die folgende Tabelle enthält die ASM-Cmdlets und deren Namen im Resource Manager-Modus:

| ASM-Cmdlets | Resource Manager-Cmdlets |
| --- | --- |
| Add-AzureHDInsightConfigValue |[Add-AzHDInsightConfigValue](/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) |
| Add-AzureHDInsightMetastore |[Add-AzHDInsightMetastore](/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-AzHDInsightScriptAction](/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Add-AzHDInsightStorage](/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Get-AzureHDInsightProperty |[Get-AzHDInsightProperty](/powershell/module/az.hdinsight/get-azhdinsightproperty) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzHDInsightHiveJob](/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[New-AzHDInsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzHDInsightClusterConfig](/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[New-AzHDInsightHiveJobDefinition](/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzHDInsightMapReduceJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[New-AzHDInsightPigJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzHDInsightSqoopJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzHDInsightStreamingMapReduceJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Remove-AzHDInsightCluster](/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](/powershell/module/azurerm.hdinsight/revoke-azurermhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightClusterSize](/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stop-AzHDInsightJob](/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Use-AzHDInsightCluster](/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Wait-AzHDInsightJob](/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Neue Cmdlets
Die folgenden Cmdlets sind neu und nur im Resource Manager-Modus verfügbar. 

**Cmdlets zu Skriptaktionen:**

* **Get-AzHDInsightPersistedScriptAction** : Ruft persistente Skriptaktionen für einen Cluster ab und listet diese in chronologischer Reihenfolge auf oder ruft Details zu einer bestimmten persistenten Skriptaktion ab. 
* **Get-AzHDInsightScriptActionHistory** : Ruft den Skriptaktionsverlauf für einen Cluster ab und listet ihn in umgekehrter chronologischer Reihenfolge auf oder ruft Details zu zuvor ausgeführten Skritpaktionen ab. 
* **Remove-AzHDInsightPersistedScriptAction** : Entfernt eine persistente Skriptaktion aus einem HDInsight-Cluster.
* **Set-AzHDInsightPersistedScriptAction** : Legt eine zuvor ausgeführte Skriptaktion als persistente Skriptaktion fest.
* **Submit-AzHDInsightScriptAction** : Übermittelt eine neue Skriptaktion an einen Azure HDInsight-Cluster. 

Weitere Informationen zur Verwendung finden Sie unter [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

**Identitätsbezogene Cluster-Cmdlets:**

* **Add-AzHDInsightClusterIdentity** : Fügt einem Clusterkonfigurationsobjekt eine Clusteridentität hinzu, sodass der HDInsight-Cluster auf Azure Data Lake Storage zugreifen kann. Siehe hierzu auch [Erstellen eines HDInsight-Clusters mit Data Lake Storage mithilfe von Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Beispiele
**Cluster erstellen**

Alter Befehl (ASM): 

```azurepowershell
New-AzureHDInsightCluster `
    -Name $clusterName `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainerName $containerName `
    -ClusterSizeInNodes 2 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.2" `
    -Credential $httpCredential `
    -SshCredential $sshCredential
```

Neuer Befehl:

```azurepowershell
New-AzHDInsightCluster `
    -ClusterName $clusterName `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainer $containerName  `
    -ClusterSizeInNodes 2 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.2" `
    -HttpCredential $httpcredentials `
    -SshCredential $sshCredentials
```

**Löschen eines Clusters**

Alter Befehl (ASM):

```azurepowershell
Remove-AzureHDInsightCluster -name $clusterName 
```

Neuer Befehl:

```azurepowershell
Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
```

**Auflisten von Clusterdetails**

Alter Befehl (ASM):

```azurepowershell
Get-AzureHDInsightCluster
```

Neuer Befehl:

```azurepowershell
Get-AzHDInsightCluster
```

**Anzeigen von Clustern**

Alter Befehl (ASM):

```azurepowershell
Get-AzureHDInsightCluster -Name $clusterName
```

Neuer Befehl:

```azurepowershell
Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName
```

#### <a name="other-samples"></a>Weitere Beispiele
* [Erstellen von Hadoop-Clustern in HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Übermitteln von Apache Hive-Aufträgen](hadoop/apache-hadoop-use-hive-powershell.md)
* [Übermitteln von Apache Sqoop-Aufträgen](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrieren zum neuen HDInsight .NET SDK
Das [Azure Service Management (ASM)-basierte HDInsight .NET SDK](/previous-versions/azure/reference/mt416619(v=azure.100)) ist veraltet. Es wird empfohlen, das [Azure Resource Manager-basierte HDInsight .NET SDK](/dotnet/api/overview/azure/hdinsight) zu verwenden. Die folgenden ASM-basierten HDInsight-Pakete sind veraltet.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Dieser Abschnitt enthält weitere Informationen dazu, wie Sie bestimmte Aufgaben mithilfe des Resource Manager-basierten SDK ausführen können.

| Gewusst wie: Verwenden des Resource Manager-basierten HDInsight SDK | Links |
| --- | --- |
| Azure HDInsight SDK für .NET|Weitere Informationen finden Sie unter [Azure HDInsight SDK für .NET](/dotnet/api/overview/azure/hdinsight?view=azure-dotnet&preserve-view=true). |
| Authentifizieren von Clientanwendungen, die Azure Active Directory mit dem .NET SDK interaktiv nutzen |Weitere Informationen finden Sie unter [Ausführen von Apache Hive-Abfragen per HDInsight .NET-SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Der Codeausschnitt in diesem Artikel verwendet den interaktiven Authentifizierungsansatz. |
| Authentifizieren von Clientanwendungen, die Azure Active Directory mit dem .NET SDK nicht interaktiv nutzen |Siehe [Erstellen von HDInsight-Anwendungen für die nicht interaktive Authentifizierung](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Übermitteln eines Apache Hive-Auftrags mit dem .NET SDK |Weitere Informationen finden Sie unter [Übermitteln von Apache Hive-Aufträgen](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). |
| Übermitteln eines Apache Sqoop-Auftrags mit dem .NET SDK |Weitere Informationen finden Sie unter [Übermitteln von Apache Sqoop-Aufträgen](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md). |
| Auflisten von HDInsight-Clustern mit dem .NET SDK |Weitere Informationen finden Sie unter [Auflisten von HDInsight-Clustern](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Skalieren von HDInsight-Clustern mit dem .NET SDK |Weitere Informationen finden Sie unter [Skalieren von HDInsight-Clustern](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Gewähren/Widerrufen von Zugriff auf HDInsight-Cluster mit dem .NET SDK |Weitere Informationen finden Sie unter [Gewähren/Verweigern des Zugriffs auf HDInsight-Cluster](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Aktualisieren von HTTP-Anmeldeinformationen für HDInsight-Cluster mit dem .NET SDK |Weitere Informationen finden Sie unter [Aktualisieren von HTTP-Benutzeranmeldeinformationen für HDInsight-Cluster](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Suchen des standardmäßigen Speicherkonto für HDInsight-Cluster mit dem .NET SDK |Weitere Informationen finden Sie unter [Auffinden des Standardspeicherkontos für HDInsight-Cluster](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Löschen von HDInsight-Clustern mithilfe des .NET SDK |Weitere Informationen finden Sie unter [Löschen von HDInsight-Clustern](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Beispiele
Im Folgenden finden Sie einige Beispiele dazu, wie ein Vorgang mit dem ASM-basierten SDK durchgeführt wird und welcher der entsprechende Codeausschnitt für das Resource Manager-basierte SDK ist.

**Erstellen eines Cluster-CRUD-Clients**

* Alter Befehl (ASM)

  ```azurecli
  //Certificate auth
  //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
  const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
  var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
  var client = HDInsightClient.Connect(cred);
  ```
* Neuer Befehl (Dienstprinzipalautorisierung)

  ```azurecli
  //Service principal auth
  //This will log the application in as itself, rather than on behalf of a specific user.
  //For details, including how to set up the application, see:
  //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
  var authFactory = new AuthenticationFactory();
  
  var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
  var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
  var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
  var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
  _hdiManagementClient = new HDInsightManagementClient(creds);
  ```

* Neuer Befehl (Benutzerautorisierung)

  ```azurecli
  //User auth
  //This will log the application in on behalf of the user.
  //The end-user will see a login popup.
  
  var authFactory = new AuthenticationFactory();
  
  var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
  var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
  var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
  var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
  _hdiManagementClient = new HDInsightManagementClient(creds);
  ```

**Erstellen eines Clusters**

* Alter Befehl (ASM)

  ```azurecli
  var clusterInfo = new ClusterCreateParameters
              {
                  Name = dnsName,
                  DefaultStorageAccountKey = key,
                  DefaultStorageContainer = defaultStorageContainer,
                  DefaultStorageAccountName = storageAccountDnsName,
                  ClusterSizeInNodes = 1,
                  ClusterType = type,
                  Location = "West US",
                  UserName = "admin",
                  Password = "*******",
                  Version = version,
                  HeadNodeSize = NodeVMSize.Large,
              };
  clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
  client.CreateCluster(clusterInfo);
  ```

* Neuer Befehl

  ```azurecli
  var clusterCreateParameters = new ClusterCreateParameters
      {
          Location = "West US",
          ClusterType = "Hadoop",
          Version = "3.1",
          OSType = OSType.Linux,
          DefaultStorageAccountName = "mystorage.blob.core.windows.net",
          DefaultStorageAccountKey =
              "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
          UserName = "hadoopuser",
          Password = "*******",
          HeadNodeSize = "ExtraLarge",
          RdpUsername = "hdirp",
          RdpPassword = ""*******",
          RdpAccessExpiry = new DateTime(2025, 3, 1),
          ClusterSizeInNodes = 5
      };
  var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
  clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);
  ```

**Aktivieren von HTTP-Zugriff**

* Alter Befehl (ASM)

  ```azurecli
  client.EnableHttp(dnsName, "West US", "admin", "*******");
  ```

* Neuer Befehl
  
  ```azurecli
  var httpParams = new HttpSettingsParameters
  {
         HttpUserEnabled = true,
         HttpUsername = "admin",
         HttpPassword = "*******",
  };
  client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);
  ```

**Löschen eines Clusters**

* Alter Befehl (ASM)

  ```azurecli
  client.DeleteCluster(dnsName);
  ```

* Neuer Befehl

  ```azurecli
  client.Clusters.Delete(resourceGroup, dnsname);
  ```
