---
title: 'Schnellstart: Apache Spark-Cluster mit Azure CLI: Azure HDInsight'
description: Dieser Schnellstart zeigt, wie Sie mit der Azure-Befehlszeilenschnittstelle einen Apache Spark-Cluster in Azure HDInsight erstellen.
ms.service: hdinsight
ms.topic: quickstart
ms.date: 02/03/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: db7e6b8fb6e70d6a64e84da29bbb0a299ed8da2a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940550"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>Schnellstart: Erstellen eines Apache Spark-Clusters in Azure HDInsight mithilfe der Azure CLI

In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle (Command-Line Interface, CLI) einen Apache Spark-Cluster in Azure HDInsight erstellen. Azure HDInsight ist ein umfassender, verwalteter Open-Source-Analysedienst für Unternehmen. Das Apache Spark-Framework für HDInsight ermöglicht schnelle Datenanalysen und Clustercomputing mit In-Memory-Verarbeitung. Die Azure CLI ist die plattformübergreifende Befehlszeilenumgebung von Microsoft zum Verwalten von Azure-Ressourcen.

Bei gemeinsamer Verwendung mehrerer Cluster empfiehlt sich die Erstellung eines virtuellen Netzwerks. Bei Verwendung eines Spark-Clusters sollten Sie den Hive Warehouse Connector verwenden. Weitere Informationen finden Sie unter [Planen eines virtuellen Netzwerks für Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) sowie unter [Integrieren von Apache Spark und Apache Hive per Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-apache-spark-cluster"></a>Erstellen eines Apache Spark-Clusters

1. Melden Sie sich bei Ihrem Azure-Abonnement an. Wenn Sie Azure Cloud Shell verwenden möchten, wählen Sie in der rechten oberen Ecke des folgenden Codeblocks die Option **Jetzt testen** aus. Geben Sie andernfalls den folgenden Befehl ein:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Legen Sie Umgebungsvariablen fest. Die Verwendung von Variablen in diesem Schnellstart basiert auf Bash. Für andere Umgebungen sind geringfügige Variationen erforderlich. Ersetzen Sie im Codeausschnitt unten „RESOURCEGROUPNAME“, „LOCATION“, „CLUSTERNAME“, „STORAGEACCOUNTNAME“ und „PASSWORD“ durch die gewünschten Werte. Geben Sie dann die CLI-Befehle zum Festlegen der Umgebungsvariablen ein.

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
    export clusterType=spark
    export componentVersion=Spark=2.3
    ```

3. Erstellen Sie die Ressourcengruppe mithilfe des folgenden Befehls:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. Erstellen Sie ein Azure Storage-Konto mithilfe des folgenden Befehls:

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. Extrahieren Sie den primären Schlüssel aus dem Azure Storage-Konto, und speichern Sie ihn in einer Variablen, indem Sie den folgenden Befehl eingeben:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Erstellen Sie einen Azure Storage-Container mithilfe des folgenden Befehls:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. Erstellen Sie den Apache Spark-Cluster mithilfe des folgenden Befehls:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nachdem Sie den Schnellstart abgeschlossen haben, können Sie den Cluster löschen. Mit HDInsight werden Ihre Daten in Azure Storage gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird. Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen.

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

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie erfahren, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle einen Apache Spark-Cluster in Azure HDInsight erstellen.  Im nächsten Tutorial erfahren Sie, wie Sie mithilfe eines HDInsight-Clusters interaktive Abfragen für Beispieldaten ausführen.

> [!div class="nextstepaction"]
> [Ausführen von interaktiven Abfragen für Apache Spark](./apache-spark-load-data-run-query.md)
