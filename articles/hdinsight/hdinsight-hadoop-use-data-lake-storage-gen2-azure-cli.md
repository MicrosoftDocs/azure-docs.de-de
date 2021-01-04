---
title: Erstellen eines Azure HDInsight-Clusters – Azure Data Lake Storage Gen2 – Azure CLI
description: Hier erfahren Sie, wie Sie über die Azure CLI Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern verwenden können.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 09/17/2020
ms.openlocfilehash: f88c242ab6f7ddabe17c9363aa1e6bb7f617c800
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94845106"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-azure-cli"></a>Erstellen eines Clusters mit Data Lake Storage Gen2 über die Azure CLI

Gehen Sie wie nachfolgend beschrieben vor, um einen HDInsight-Cluster zu erstellen, der Data Lake Storage Gen2 als Speicher verwendet.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht mit Azure Data Lake Storage Gen2 vertraut sind, finden Sie weitere Informationen im Abschnitt [Übersicht](hdinsight-hadoop-use-data-lake-storage-gen2.md). 
- Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.
- Um die CLI-Skriptbeispiele auszuführen, haben Sie drei Möglichkeiten:
    - Verwenden Sie [Azure Cloud Shell](../cloud-shell/overview.md) aus dem Azure-Portal (siehe nächster Abschnitt).
    - Verwenden Sie die eingebettete Azure Cloud Shell, indem Sie die Schaltfläche „Ausprobieren“ in der oberen rechten Ecke jedes Codeblocks verwenden.
    - [Installieren Sie die neueste Version der Azure CLI](/cli/azure/install-azure-cli) (2.0.13 oder höher), wenn Sie lieber eine lokale CLI-Konsole verwenden möchten. Melden Sie sich über `az login` mit einem Konto bei Azure an, das mit dem Azure-Abonnement verknüpft ist, unter dem Sie die vom Benutzer zugewiesene verwaltete Identität bereitstellen möchten. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Sie können [hier eine Beispielvorlagendatei](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) und [hier eine Beispieldatei für Parameter](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json) herunterladen. Ersetzen Sie vor der Verwendung der Vorlage und des Azure CLI-Codeausschnitts unten die folgenden Platzhalter durch die korrekten Werte:

| Platzhalter | BESCHREIBUNG |
|---|---|
| `<SUBSCRIPTION_ID>` | Die ID Ihres Azure-Abonnements |
| `<RESOURCEGROUPNAME>` | Die Ressourcengruppe, in der der neue Cluster und das Speicherkonto erstellt werden sollen |
| `<MANAGEDIDENTITYNAME>` | Der Name der verwalteten Identität, der die Berechtigungen für Ihr Azure Data Lake Storage Gen2-Speicherkonto gewährt wird |
| `<STORAGEACCOUNTNAME>` | Das neue Speicherkonto mit Azure Data Lake Storage Gen2, das erstellt wird |
| `<FILESYSTEMNAME>`  | Der Name des Dateisystems, das dieser Cluster im Storage-Konto verwenden soll. |
| `<CLUSTERNAME>` | Der Name des HDInsight-Clusters. |
| `<PASSWORD>` | Das gewählte Kennwort für die Anmeldung im Cluster über SSH und das Ambari-Dashboard |

Der Codeausschnitt führt die folgenden ersten Schritte durch:

1. Anmeldung bei Ihrem Azure-Konto
1. Festlegen des aktiven Abonnements, in dem die Erstellung durchgeführt wird
1. Erstellen einer neuen Ressourcengruppe für die neuen Bereitstellungsaktivitäten
1. Erstellen einer benutzerseitig zugewiesenen verwalteten Identität
1. Hinzufügen einer Erweiterung zur Azure CLI, um Features für Data Lake Storage Gen2 zu nutzen
1. Erstellen eines neuen Speicherkontos mit Data Lake Storage Gen2 mithilfe des Flags `--hierarchical-namespace true`

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Melden Sie sich als Nächstes beim Portal an. Fügen Sie die neue benutzerseitig zugewiesene verwaltete Identität zu der Rolle **Mitwirkender an Storage-Blobdaten** im Speicherkonto zu. Dieser Schritt wird in Schritt 3 unter [Verwenden des Azure-Portals](hdinsight-hadoop-use-data-lake-storage-gen2.md) beschrieben.

 > [!IMPORTANT]
 > Stellen Sie sicher, dass Ihr Storage-Konto über die vom Benutzer zugewiesene Identität mit den Rollenberechtigungen für **Mitwirkende an Storage-Blobdaten** verfügt. Wenn dies nicht der Fall ist, schlägt die Clustererstellung fehl.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nach Abschluss des Artikels kann es ratsam sein, den Cluster zu löschen. Mit HDInsight werden Ihre Daten in Azure Storage gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird. Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen.

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

Sie haben die Erstellung eines HDInsight-Clusters erfolgreich abgeschlossen. Als Nächstes wird beschrieben, wie Sie mit Ihrem Cluster arbeiten.

### <a name="apache-spark-clusters"></a>Apache Spark-Cluster

* [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md)
* [Erstellen einer eigenständigen Anwendung mit Scala](spark/apache-spark-create-standalone-application.md)
* [Ausführen von Remoteaufträgen in einem Apache Spark-Cluster mithilfe von Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark mit BI: Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](spark/apache-spark-use-bi-tools.md)
* [Apache Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Apache Hadoop-Cluster

* [Verwenden von Apache Hive mit HDInsight](hadoop/hdinsight-use-hive.md)
* [Verwenden von MapReduce mit HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase Cluster

* [Erste Schritte mit Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Entwickeln von Java-Anwendungen für Apache HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)
