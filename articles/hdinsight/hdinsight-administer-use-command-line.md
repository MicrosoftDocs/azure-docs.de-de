---
title: Verwalten von Azure HDInsight-Clustern mit der Azure-Befehlszeilenschnittstelle
description: Erfahren Sie, wie Sie mit der Azure-Befehlszeilenschnittstelle Azure HDInsight-Cluster verwalten. Zu den Clustertypen gehören Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive Query und ML Services.
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tyfox
ms.openlocfilehash: 5ae97b17d06fa0a9934a58ac662ef12116cce4f6
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137401"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Verwalten von Azure HDInsight-Clustern mit der Azure-Befehlszeilenschnittstelle

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Erfahren Sie, wie Sie mit der [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) Azure HDInsight-Cluster verwalten. Die Azure-Befehlszeilenschnittstelle (CLI) ist die plattformübergreifende Befehlszeilenumgebung von Microsoft zum Verwalten von Azure-Ressourcen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Befehlszeilenschnittstelle. Die Schritte zum Installieren der Azure-Befehlszeilenschnittstelle finden Sie bei Bedarf unter [Installieren der Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Ein Apache Hadoop-Cluster in HDInsight. Weitere Informationen finden Sie unter [Erste Schritte mit HDInsight unter Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Herstellen einer Verbindung mit Azure

Melden Sie sich bei Ihrem Azure-Abonnement an. Wenn Sie Azure Cloud Shell verwenden möchten, wählen Sie einfach in der rechten oberen Ecke des Codeblocks die Option **Ausprobieren** aus. Geben Sie andernfalls den nachstehenden Befehl ein:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Auflisten von Clustern

Verwenden Sie [az hdinsight list](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list), um Cluster aufzulisten. Bearbeiten Sie die folgenden Befehle, indem Sie `RESOURCE_GROUP_NAME` durch den Namen Ihrer Ressourcengruppe ersetzen und dann die folgenden Befehle eingeben:

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>Anzeigen von Clustern

Verwenden Sie [az hdinsight show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show), um Informationen für einen bestimmten Cluster anzuzeigen. Bearbeiten Sie den folgenden Befehl, indem Sie `RESOURCE_GROUP_NAME` und `CLUSTER_NAME` durch die entsprechenden Informationen ersetzen und dann den folgenden Befehl eingeben:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Löschen von Clustern

Verwenden Sie [az hdinsight delete](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete), um einen bestimmten Cluster zu löschen. Bearbeiten Sie den folgenden Befehl, indem Sie `RESOURCE_GROUP_NAME` und `CLUSTER_NAME` durch die entsprechenden Informationen ersetzen und dann den folgenden Befehl eingeben:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Sie können einen Cluster auch löschen, indem Sie die Ressourcengruppe löschen, die den Cluster enthält. Beachten Sie, dass damit alle Ressourcen in der Gruppe, einschließlich des Standardspeicherkontos, gelöscht werden.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Skalieren von Clustern

Verwenden Sie [az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize), um die Größe des angegebenen HDInsight-Clusters auf die angegebene Größe anzupassen. Bearbeiten Sie den folgenden Befehl, indem Sie `RESOURCE_GROUP_NAME` und `CLUSTER_NAME` durch die entsprechenden Informationen ersetzen. Ersetzen Sie `TARGET_INSTANCE_COUNT` durch die gewünschte Anzahl von Workerknoten für Ihren Cluster. Weitere Informationen zum Skalieren von Clustern finden Sie unter [Skalieren von HDInsight-Clustern](./hdinsight-scaling-best-practices.md). Geben Sie den folgenden Befehl ein:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --target-instance-count TARGET_INSTANCE_COUNT
```

## <a name="next-steps"></a>Nächste Schritte

Sie sind nun in der Lage, verschiedene Verwaltungsaufgaben für HDInsight-Cluster auszuführen. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Verwalten von Apache Hadoop-Clustern in HDInsight mit dem Azure-Portal](hdinsight-administer-use-portal-linux.md)
* [Verwalten von HDInsight mit Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Erste Schritte mit Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Erste Schritte mit der Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
