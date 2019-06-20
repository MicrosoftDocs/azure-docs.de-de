---
title: Erstellen von Apache Hadoop-Clustern mithilfe der Azure CLI – Azure HDInsight
description: Erfahren Sie, wie Sie HDInsight-Cluster mit der plattformübergreifenden Azure CLI erstellen.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: hrasheed
ms.openlocfilehash: 0a278cd98b0dd6c6d8f0fe9bfee81e5bafd4f543
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65597685"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Erstellen von HDInsight-Clustern mit der Azure-Befehlszeilenschnittstelle

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Anhand der Schritte in diesem Dokument werden Sie durch die Erstellung eines HDInsight 3.6-Clusters mithilfe der Azure CLI geführt.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Azure-Befehlszeilenschnittstelle. Die Schritte zum Installieren der Azure-Befehlszeilenschnittstelle finden Sie bei Bedarf unter [Installieren der Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Erstellen eines Clusters

1. Melden Sie sich bei Ihrem Azure-Abonnement an. Wenn Sie Azure Cloud Shell verwenden möchten, wählen Sie einfach in der rechten oberen Ecke des Codeblocks die Option **Ausprobieren** aus. Geben Sie andernfalls den nachstehenden Befehl ein:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Legen Sie Umgebungsvariablen fest. Die Verwendung von Variablen in diesem Artikel basiert auf Bash. Für andere Umgebungen sind geringfügige Variationen erforderlich. Eine vollständige Liste der möglichen Parameter für die Erstellung von Clustern finden Sie unter [az-hdinsight-create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create).

    |Parameter | BESCHREIBUNG |
    |---|---|
    |`--size`| Die Anzahl an Workerknoten im Cluster. In diesem Artikel wird die Variable `clusterSizeInNodes` als Wert verwendet, der an `--size` übergeben wird. |
    |`--version`| Die HDInsight-Clusterversion. In diesem Artikel wird die Variable `clusterVersion` als Wert verwendet, der an `--version` übergeben wird. Weitere Informationen: [Unterstützte HDInsight-Versionen](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Die Art von HDInsight-Cluster, z. B. hadoop, interactivehive, hbase, kafka, storm, spark, rserver, mlservices.  In diesem Artikel wird die Variable `clusterType` als Wert verwendet, der an `--type` übergeben wird. Weitere Informationen: [Clustertypen und Konfiguration](./hdinsight-hadoop-provision-linux-clusters.md#cluster-types).|
    |`--component-version`|Die Versionen verschiedener Hadoop-Komponenten durch Leerzeichen getrennt im Format „Komponente=Version“. In diesem Artikel wird die Variable `componentVersion` als Wert verwendet, der an `--component-version` übergeben wird. Weitere Informationen: [Verfügbare Apache Hadoop-Komponenten in verschiedenen Versionen von HDInsight](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    Ersetzen Sie `RESOURCEGROUPNAME`, `LOCATION`, `CLUSTERNAME`, `STORAGEACCOUNTNAME` und `PASSWORD` durch die gewünschten Werte. Ändern Sie die Werte der anderen Variablen nach Bedarf. Geben Sie dann die CLI-Befehle ein.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'
    
    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. [Erstellen Sie die Ressourcengruppe](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) mithilfe des folgenden Befehls:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Eine Liste der gültigen Orte können Sie mithilfe des Befehls `az account list-locations` erzeugen. Verwenden Sie anschließend einen der Orte aus dem Wert `name`.

4. [Erstellen Sie ein Azure-Speicherkonto](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) mithilfe des folgenden Befehls:

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Extrahieren Sie den Primärschlüssel aus dem Azure-Speicherkonto](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list), und speichern Sie ihn in einer Variablen, indem Sie den folgenden Befehl eingeben:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Erstellen Sie einen Azure Storage-Container](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) mithilfe des folgenden Befehls:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Erstellen Sie den HDInsight-Cluster](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) mithilfe des folgenden Befehls:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --size $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-default-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > HDInsight-Cluster gibt es in verschiedenen Typen, die der Workload oder Technologie entsprechen, für die der Cluster optimiert ist. Es ist keine unterstützte Methode zum Erstellen eines Clusters vorhanden, bei der mehrere Typen kombiniert werden, z.B. Storm und HBase in einem Cluster.

    Die Fertigstellung der Clustererstellung kann möglicherweise einige Minuten dauern. In der Regel dauert es etwa 15 Minuten.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nach Abschluss des Artikels kann es ratsam sein, den Cluster zu löschen. Mit HDInsight werden Ihre Daten im Azure-Speicher gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird. Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen.

Geben Sie die folgenden Befehle oder einige von ihnen ein, um Ressourcen zu entfernen:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>Problembehandlung

Falls beim Erstellen von HDInsight-Clustern Probleme auftreten, sehen Sie sich die [Voraussetzungen für die Zugriffssteuerung](./hdinsight-hadoop-customize-cluster-linux.md#access-control) an.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie einen HDInsight-Cluster erfolgreich mithilfe der Azure-Befehlszeilenschnittstelle erstellt haben, nutzen Sie die folgenden Informationen, um mehr über die Arbeit mit Ihrem Cluster zu lernen:

### <a name="apache-hadoop-clusters"></a>Apache Hadoop-Cluster

* [Verwenden von Apache Hive mit HDInsight](hadoop/hdinsight-use-hive.md)
* [Verwenden von Apache Pig mit HDInsight](hadoop/hdinsight-use-pig.md)
* [Verwenden von MapReduce mit HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase Cluster

* [Erste Schritte mit Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Entwickeln von Java-Anwendungen für Apache HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm-Cluster

* [Entwickeln von Java-Topologien für Apache Storm in HDInsight](storm/apache-storm-develop-java-topology.md)
* [Verwenden von Python-Komponenten in Apache Storm in HDInsight](storm/apache-storm-develop-python-topology.md)
* [Bereitstellen und Überwachen von Topologien mit Apache Storm in HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
