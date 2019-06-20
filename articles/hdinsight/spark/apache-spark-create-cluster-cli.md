---
title: Erstellen eines Apache Spark-Clusters in Azure HDInsight mit der Azure-Befehlszeilenschnittstelle
description: Dieser Schnellstart zeigt, wie Sie mit der Azure-Befehlszeilenschnittstelle einen Apache Spark-Cluster in Azure HDInsight erstellen.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 05/09/2019
ms.author: hrasheed
ms.openlocfilehash: b9478ca8e1b31c1761e063a6789e96043f9a2c68
ms.sourcegitcommit: 9e8dfa1169a55c3c8af93a6c5f4e0dace4de48b2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2019
ms.locfileid: "65556813"
---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight-with-azure-cli"></a>Erstellen eines Apache Spark-Clusters in Azure HDInsight mit der Azure-Befehlszeilenschnittstelle

In diesem Schnellstart erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle einen Apache Spark-Cluster in Azure HDInsight erstellen. Apache Spark ermöglicht schnelle Datenanalysen und Clustercomputing mit In-Memory-Verarbeitung. Die [Azure-Befehlszeilenschnittstelle (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) ist die plattformübergreifende Befehlszeilenumgebung von Microsoft zum Verwalten von Azure-Ressourcen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Azure-Befehlszeilenschnittstelle. Die Schritte zum Installieren der Azure-Befehlszeilenschnittstelle finden Sie bei Bedarf unter [Installieren der Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>Erstellen eines Apache Spark-Clusters

1. Melden Sie sich bei Ihrem Azure-Abonnement an. Wenn Sie Azure Cloud Shell verwenden möchten, wählen Sie einfach in der rechten oberen Ecke des Codeblocks die Option **Ausprobieren** aus. Geben Sie andernfalls den nachstehenden Befehl ein:

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
        --size $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-default-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nachdem Sie den Schnellstart abgeschlossen haben, können Sie den Cluster löschen. Mit HDInsight werden Ihre Daten im Azure-Speicher gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird. Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen.

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

In diesem Schnellstart haben Sie erfahren, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle einen Apache Spark-Cluster in Azure HDInsight erstellen.  Im nächsten Tutorial erfahren Sie, wie Sie mithilfe eines HDInsight Spark-Clusters interaktive Abfragen für Beispieldaten ausführen.

> [!div class="nextstepaction"]
> [Ausführen von interaktiven Abfragen für Apache Spark](./apache-spark-load-data-run-query.md)
