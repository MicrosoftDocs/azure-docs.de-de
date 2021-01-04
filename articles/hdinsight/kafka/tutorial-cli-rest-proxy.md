---
title: 'Tutorial: Erstellen eines für Apache Kafka-REST-Proxys geeigneten Clusters in HDInsight per Azure CLI'
description: Erfahren Sie, wie Sie Apache Kafka-Vorgänge mithilfe eines Kafka-REST-Proxys in Azure HDInsight ausführen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 68dddcbc5771ef1a8b5d6ea423674a1c6845a5e6
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539478"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Tutorial: Erstellen eines für Apache Kafka-REST-Proxys geeigneten Clusters in HDInsight per Azure CLI

In diesem Tutorial wird beschrieben, wie Sie einen für Apache Kafka-[REST-Proxys geeigneten](./rest-proxy.md) Cluster in Azure HDInsight erstellen, indem Sie die Azure-Befehlszeilenschnittstelle (Command-Line Interface, CLI) verwenden. Azure HDInsight ist ein umfassender, verwalteter Open-Source-Analysedienst für Unternehmen. Apache Kafka ist eine verteilte Open Source-Streamingplattform. Sie wird häufig als Nachrichtenbroker eingesetzt, da sie eine ähnliche Funktionalität wie eine Veröffentlichen-Abonnieren-Nachrichtenwarteschlange aufweist. Mithilfe des Kafka-REST-Proxys können Sie über eine [REST-API](/rest/api/hdinsight-kafka-rest-proxy/) per HTTP mit Ihrem Kafka-Cluster interagieren. Die Azure CLI ist die plattformübergreifende Befehlszeilenumgebung von Microsoft zum Verwalten von Azure-Ressourcen.

Auf die Apache Kafka-API kann nur von Ressourcen im gleichen virtuellen Netzwerk zugegriffen werden. Sie können per SSH direkt auf den Cluster zugreifen. Wenn Sie eine Verbindung zwischen anderen Diensten, Netzwerken und virtuellen Computern und Apache Kafka herstellen möchten, müssen Sie zunächst ein virtuelles Netzwerk und anschließend die Ressourcen in diesem Netzwerk erstellen. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Apache Kafka in HDInsight über ein virtuelles Azure-Netzwerk](./apache-kafka-connect-vpn-gateway.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Voraussetzungen für den Kafka-REST-Proxy
> * Erstellen eines Apache Kafka-Clusters per Azure CLI

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Eine Anwendung, die bei Azure AD registriert ist. Die Clientanwendungen, die Sie für die Interaktion mit dem Kafka-REST-Proxy schreiben, verwenden die ID und den geheimen Schlüssel dieser Anwendung zur Authentifizierung bei Azure. Weitere Informationen finden Sie unter [Registrieren einer Anwendung bei Microsoft Identity Platform](../../active-directory/develop/quickstart-register-app.md).

* Eine Azure AD-Sicherheitsgruppe, in der Ihre registrierte Anwendung Mitglied ist. Mit dieser Sicherheitsgruppe wird gesteuert, welche Anwendungen mit dem REST-Proxy interagieren dürfen. Weitere Informationen zum Erstellen von Azure AD-Gruppen finden Sie unter [Erstellen einer einfachen Gruppe und Hinzufügen von Mitgliedern mithilfe von Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* Azure-Befehlszeilenschnittstelle. Stellen Sie sicher, dass Sie mindestens Version 2.0.79 verwenden. Weitere Informationen finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-apache-kafka-cluster"></a>Erstellen eines Apache Kafka-Clusters

1. Melden Sie sich bei Ihrem Azure-Abonnement an.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Legen Sie Umgebungsvariablen fest. Die Verwendung von Variablen in diesem Tutorial basiert auf Bash. Für andere Umgebungen sind geringfügige Variationen erforderlich.

    |Variable | BESCHREIBUNG |
    |---|---|
    |resourceGroupName|Ersetzen Sie RESOURCEGROUPNAME durch den Namen Ihrer neuen Ressourcengruppe.|
    |location|Ersetzen Sie LOCATION durch eine Region, in der der Cluster erstellt werden soll. Verwenden Sie den Befehl `az account list-locations`, um eine Liste mit gültigen Standorten zu erhalten.|
    |clusterName|Ersetzen Sie CLUSTERNAME durch einen global eindeutigen Namen für Ihren neuen Cluster.|
    |storageAccount|Ersetzen Sie STORAGEACCOUNTNAME durch einen Namen für Ihr neues Speicherkonto.|
    |httpPassword|Ersetzen Sie PASSWORD durch ein Kennwort für die Clusteranmeldung per **admin** .|
    |sshPassword|Ersetzen Sie PASSWORD durch ein Kennwort für den Secure Shell-Benutzernamen **sshuser** .|
    |securityGroupName|Ersetzen Sie SECURITYGROUPNAME durch den Namen der AAD-Clientsicherheitsgruppe für den Kafka-REST-Proxy. Die Variable wird an den Parameter `--kafka-client-group-name` für `az-hdinsight-create` übergeben.|
    |securityGroupID|Ersetzen Sie SECURITYGROUPID durch die ID der AAD-Clientsicherheitsgruppe für den Kafka-REST-Proxy. Die Variable wird an den Parameter `--kafka-client-group-id` für `az-hdinsight-create` übergeben.|
    |storageContainer|Der Speichercontainer, der vom Cluster verwendet wird. Lassen Sie die Einstellung für dieses Tutorial unverändert. Diese Variable wird anhand des Namens des Clusters festgelegt.|
    |workernodeCount|Die Anzahl von Workerknoten im Cluster. Lassen Sie diese Einstellung für dieses Tutorial unverändert. Um Hochverfügbarkeit zu garantieren, benötigt Kafka mindestens drei Workerknoten.|
    |clusterType|Der Typ des HDInsight-Clusters. Lassen Sie diese Einstellung für dieses Tutorial unverändert.|
    |clusterVersion|Die Version des HDInsight-Clusters. Lassen Sie diese Einstellung für dieses Tutorial unverändert. Für den Kafka-REST-Proxy ist mindestens Clusterversion 4.0 erforderlich.|
    |componentVersion|Kafka-Version. Lassen Sie diese Einstellung für dieses Tutorial unverändert. Für den Kafka-REST-Proxy ist mindestens Komponentenversion 2.1 erforderlich.|

    Aktualisieren Sie die Variablen mit den gewünschten Werten. Geben Sie dann die CLI-Befehle zum Festlegen der Umgebungsvariablen ein.

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. [Erstellen Sie die Ressourcengruppe](/cli/azure/group#az-group-create) mithilfe des folgenden Befehls:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Erstellen Sie ein Azure Storage-Konto](/cli/azure/storage/account#az-storage-account-create) mithilfe des folgenden Befehls:

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. [Extrahieren Sie den Primärschlüssel aus dem Azure Storage-Konto](/cli/azure/storage/account/keys#az-storage-account-keys-list), und speichern Sie ihn in einer Variablen, indem Sie den folgenden Befehl eingeben:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Erstellen Sie einen Azure Storage-Container](/cli/azure/storage/container#az-storage-container-create) mithilfe des folgenden Befehls:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Erstellen des HDInsight-Clusters](/cli/azure/hdinsight#az-hdinsight-create) Beachten Sie vor dem Eingeben des Befehls die folgenden Parameter:

    1. Erforderliche Parameter für Kafka-Cluster:

        |Parameter | BESCHREIBUNG|
        |---|---|
        |--type|Der Wert muss **Kafka** lauten.|
        |--workernode-data-disks-per-node|Die Anzahl von Datenträgern, die pro Workerknoten verwendet werden sollen. HDInsight Kafka wird nur mit Datenträgern unterstützt. In diesem Tutorial wird der Wert **2** verwendet.|

    1. Erforderliche Parameter für Kafka-REST-Proxy:

        |Parameter | BESCHREIBUNG|
        |---|---|
        |--kafka-management-node-size|Die Größe des Knotens. In diesem Tutorial wird der Wert **Standard_D4_v2** verwendet.|
        |--kafka-client-group-id|Die ID der AAD-Clientsicherheitsgruppe für den Kafka-REST-Proxy. Der Wert wird über die Variable **$securityGroupID** übergeben.|
        |--kafka-client-group-name|Der Name der AAD-Clientsicherheitsgruppe für den Kafka-REST-Proxy. Der Wert wird über die Variable **$securityGroupName** übergeben.|
        |--version|Es muss mindestens Version 4.0 des HDInsight-Clusters verwendet werden. Der Wert wird über die Variable **$clusterVersion** übergeben.|
        |--component-version|Es muss mindestens Kafka-Version 2.1 verwendet werden. Der Wert wird über die Variable **$componentVersion** übergeben.|
    
        Wenn Sie den Cluster ohne REST-Proxy erstellen möchten, müssen Sie `--kafka-management-node-size`, `--kafka-client-group-id` und `--kafka-client-group-name` aus dem Befehl `az hdinsight create` entfernen.

    1. Fügen Sie die Parameter `--vnet-name` und `--subnet` und die zugehörigen Werte hinzu, wenn Sie über ein virtuelles Netzwerk verfügen.

    Geben Sie den folgenden Befehl zum Erstellen des Clusters ein:

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    Es kann möglicherweise einige Minuten dauern, bis die Clustererstellung abgeschlossen ist. In der Regel dauert es etwa 15 Minuten.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nach Abschluss des Artikels kann es ratsam sein, den Cluster zu löschen. Mit HDInsight werden Ihre Daten in Azure Storage gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird. Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen.

Geben Sie die folgenden Befehle oder einige von ihnen ein, um Ressourcen zu entfernen:

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie in Azure HDInsight nun erfolgreich einen für Apache Kafka-REST-Proxys geeigneten Cluster per Azure CLI erstellt haben, können Sie Python-Code verwenden, um mit dem REST-Proxy zu interagieren:

> [!div class="nextstepaction"]
> [Erstellen der Beispielanwendung](./rest-proxy.md#client-application-sample)