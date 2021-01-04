---
title: 'Azure HDInsight: Azure CLI-Beispiele'
description: Azure CLI-Beispiele für allgemeine Aufgaben in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: sample
ms.date: 09/23/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: a289a0d963c8e20a3b861ff9964e8afd6665de22
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542351"
---
# <a name="azure-hdinsight-azure-cli-samples"></a>Azure HDInsight: Azure CLI-Beispiele

> [!div class="op_single_selector"]
> [.NET-Beispiele](hdinsight-sdk-dotnet-samples.md)
> [Python-Beispiele](hdinsight-sdk-python-samples.md)
> [Java-Beispiele](hdinsight-sdk-java-samples.md)

Dieser Artikel enthält Beispielskripts für allgemeine Aufgaben. Aktualisieren Sie für jedes Beispiel die Variablen mit den entsprechenden Werten, und führen Sie dann den Befehl aus.

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Befehlszeilenschnittstelle. Informationen zu den erforderlichen Schritten finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

* Optional: Bash. In den Beispielen dieses Artikels wird die Bash-Shell unter Windows 10 verwendet. Die Installationsschritte finden Sie unter [Windows Subsystem for Linux Installation Guide for Windows 10](/windows/wsl/install-win10) (Windows-Subsystem für Linux: Installationshandbuch für Windows 10).  Die Beispiele können mit einigen geringfügigen Änderungen auch an einer Windows-Eingabeaufforderung verwendet werden.

## <a name="az-login"></a>az login

[Anmelden bei Azure](/cli/azure/reference-index#az-login)

```azurecli
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="az-hdinsight-create"></a>az hdinsight create

[Erstellen eines neuen Clusters](/cli/azure/hdinsight#az-hdinsight-create)

### <a name="create-a-cluster-with-an-existing-storage-account"></a>Erstellen eines Clusters mit einem vorhandenen Speicherkonto

```azurecli
# set variables
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=hadoop
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT
```

### <a name="create-a-cluster-with-the-enterprise-security-package-esp"></a>Erstellen eines Clusters mit dem Enterprise-Sicherheitspaket (ESP)

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=spark
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
export subnet="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyRG/providers/Microsoft.Network/virtualNetworks/MyVnet/subnets/subnet1"
export domain="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyRG/providers/Microsoft.AAD/domainServices/MyDomain.onmicrosoft.com"
export userAssignedIdentity="/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/MyMsiRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/MyMSI"
export domainAccount=MyAdminAccount@MyDomain.onmicrosoft.com
export groupDNS=MyGroup

az hdinsight create \
    --esp \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT \
    --subnet $subnet \
    --domain $domain \
    --assign-identity $userAssignedIdentity \
    --cluster-admin-account $domainAccount \
    --cluster-users-group-dns $groupDNS
```

### <a name="create-a-kafka-cluster-with-disk-encryption"></a>Erstellen eines Kafka-Clusters mit [Datenträgerverschlüsselung](./disk-encryption.md)

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=kafka
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
export encryptionKeyName=kafkaClusterKey
export encryptionKeyVersion=00000000000000000000000000000000
export encryptionVaultUri=https://MyKeyVault.vault.azure.net
export userAssignedIdentity="/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/MyMsiRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/MyMSI"

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT \
    --workernode-data-disks-per-node 2 \
    --encryption-key-name $encryptionKeyName \
    --encryption-key-version $encryptionKeyVersion \
    --encryption-vault-uri $encryptionVaultUri \
    --assign-identity $userAssignedIdentity
```

### <a name="create-a-cluster-with-azure-data-lake-storage-gen2"></a>Erstellen eines Clusters mit Azure Data Lake Storage Gen2

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=spark
export httpCredential='PASSWORD'
export adlgen2=MyStorageAccount
export sami=MyMSI

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $adlgen2 \
    --storage-account-managed-identity $sami
```

### <a name="create-a-cluster-with-configuration-from-json-string"></a>Erstellen eines Clusters mit der Konfiguration aus einer JSON-Zeichenfolge

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=spark
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
export clusterConfiguration="{'gateway':{'restAuthCredential.username':'admin'}}"

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT \
    --cluster-configuration $clusterConfiguration
```

### <a name="create-a-cluster-with-configuration-from-a-local-file"></a>Erstellen eines Clusters mit der Konfiguration aus einer lokalen Datei

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=spark
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
export clusterConfiguration=@/mnt/c/HDI/config.json

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT \
    --cluster-configuration $clusterConfiguration
```

## <a name="az-hdinsight-application-create"></a>az hdinsight application create

[Erstellen einer Anwendung für einen HDInsight-Cluster](/cli/azure/hdinsight/application#az-hdinsight-application-create)

### <a name="create-an-application-with-a-script-uri"></a>Erstellen einer Anwendung mit einem Skript-URI

```azurecli
export resourceGroupName=RESOURCEGROUPNAME
export applicationName=MyApplication
export clusterName=CLUSTERNAME
export scriptURI=https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
export scriptActionName=MyScriptAction
export scriptParameters='"-version latest -port 20000"'

az hdinsight application create \
    --resource-group $resourceGroupName \
    --name $applicationName \
    --cluster-name $clusterName \
    --script-uri $scriptURI  \
    --script-action-name $scriptActionName \
    --script-parameters "$scriptParameters"
```

### <a name="create-an-application-with-a-script-uri-and-specified-edge-node-size"></a>Erstellen einer Anwendung mit einem Skript-URI und angegebener Edgeknotengröße

```azurecli
export resourceGroupName=RESOURCEGROUPNAME
export applicationName=MyApplication
export clusterName=CLUSTERNAME
export scriptURI=https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
export scriptActionName=MyScriptAction
export scriptParameters='"-version latest -port 20000"'
export edgenodeSize=Standard_D4_v2

az hdinsight application create \
    --resource-group $resourceGroupName \
    --name $applicationName \
    --cluster-name $clusterName \
    --script-uri $scriptURI  \
    --script-action-name $scriptActionName \
    --script-parameters "$scriptParameters" \
    --edgenode-size $edgenodeSize
```

### <a name="create-an-application-with-https-endpoint"></a>Erstellen einer Anwendung mit HTTPS-Endpunkt

```azurecli
export resourceGroupName=RESOURCEGROUPNAME
export applicationName=MyApplication
export clusterName=CLUSTERNAME
export scriptURI=https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
export scriptActionName=MyScriptAction
export scriptParameters='"-version latest -port 20000"'
export destinationPort=8888
export subDomainSuffix=was

az hdinsight application create \
    --resource-group $resourceGroupName \
    --name $applicationName \
    --cluster-name $clusterName \
    --script-uri $scriptURI  \
    --script-action-name $scriptActionName \
    --script-parameters "$scriptParameters" \
    --destination-port $destinationPort \
    --sub-domain-suffix $subDomainSuffix
```

## <a name="az-hdinsight-script-action-execute"></a>az hdinsight script-action execute

[Ausführen von Skriptaktionen für den angegebenen HDInsight-Cluster](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-execute)

### <a name="execute-a-script-action-and-persist-on-success"></a>Ausführen einer Skriptaktion und Beibehalten bei Erfolg

```azurecli
export resourceGroupName=RESOURCEGROUPNAME
export clusterName=CLUSTERNAME
export scriptActionName=MyScriptAction
export scriptURI=https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
export roles="headnode workernode"

az hdinsight script-action execute \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --name $scriptActionName \
    --script-uri $scriptURI  \
    --roles $roles \
    --persist-on-success
```