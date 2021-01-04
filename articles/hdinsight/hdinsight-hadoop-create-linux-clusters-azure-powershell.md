---
title: Erstellen von Apache Hadoop-Clustern mit PowerShell – Azure HDInsight
description: Erfahren Sie, wie Sie Azure PowerShell verwenden, um Apache Hadoop-, Apache HBase-, Apache Storm- und Apache Spark-Cluster unter Linux für HDInsight zu erstellen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 23ce0f83e4233837d56ba9045a9a356ebd1cc33f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92541810"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Erstellen von Linux-basierten Clustern in HDInsight mit Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Workloads in Microsoft Azure steuern und automatisieren können. Dieses Dokument enthält Informationen zum Erstellen eines Linux-basierten HDInsight-Clusters mit Azure PowerShell. Darüber hinaus ist ein Beispielskript enthalten.

Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/install-Az-ps)-Az-Modul

## <a name="create-cluster"></a>Cluster erstellen

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Für die Erstellung eines HDInsight-Clusters mithilfe von Azure PowerShell müssen die folgenden Prozeduren ausgeführt werden:

* Erstellen einer Azure-Ressourcengruppe
* Erstellen eines Azure-Speicherkontos
* Erstellen eines Azure-Blobcontainers
* Erstellen eines HDInsight-Clusters

> [!NOTE]
> Die Verwendung von PowerShell zum Erstellen eines HDInsight-Clusters mit Azure Data Lake Storage Gen2 wird derzeit nicht unterstützt.

Das folgende Skript veranschaulicht das Erstellen eines neuen Clusters:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Die Werte, die Sie für die Clusteranmeldung angeben, werden verwendet, um das Hadoop-Benutzerkonto für den Cluster zu erstellen. Verwenden Sie dieses Konto, um eine Verbindung mit Diensten herzustellen, die auf dem Cluster gehostet werden, z.B. Webbenutzeroberflächen oder REST-APIs.

Die Werte, die Sie für den SSH-Benutzer angeben, werden verwendet, um den SSH-Benutzer für den Cluster zu erstellen. Verwenden Sie dieses Konto zum Starten einer SSH-Remotesitzung mit dem Cluster und zum Ausführen von Aufträgen. Weitere Informationen finden Sie im Dokument [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]  
> Wenn Sie die Verwendung von mehr als 32 Workerknoten planen (entweder bei Erstellung des Clusters oder durch eine Skalierung des Clusters nach der Erstellung), müssen Sie auch eine Hauptknotengröße von mindestens 8 Kernen und 14 GB Arbeitsspeicher (RAM) angeben.
>
> Weitere Informationen zu Knotengrößen und den damit verbundenen Kosten finden Sie unter [HDInsight – Preise](https://azure.microsoft.com/pricing/details/hdinsight/).

Das Erstellen eines Clusters kann bis zu 20 Minuten dauern.

## <a name="create-cluster-configuration-object"></a>Erstellen eines Clusters: Konfigurationsobjekt

Sie können ein HDInsight-Konfigurationsobjekt auch mit dem Cmdlet [`New-AzHDInsightClusterConfig`](/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) erstellen. Anschließend können Sie dieses Konfigurationsobjekt ändern, um zusätzliche Konfigurationsoptionen für Ihren Cluster zu aktivieren. Verwenden Sie abschließend den Parameter `-Config` des Cmdlets [`New-AzHDInsightCluster`](/powershell/module/az.hdinsight/new-azhdinsightcluster), um die Konfiguration zu verwenden.

Das folgende Skript erstellt ein Konfigurationsobjekt zum Konfigurieren eines Clustertyps R-Server in HDInsight. Die Konfiguration aktiviert einen Edge-Knoten, RStudio, sowie ein zusätzliches Speicherkonto.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> Die Verwendung eines Speicherkontos an einem anderen Ort als dem HDInsight-Cluster wird nicht unterstützt. Wenn Sie dieses Beispiel verwenden möchten, erstellen Sie das zusätzliche Speicherkonto am gleichen Speicherort wie den Server.

## <a name="customize-clusters"></a>Anpassen von Clustern

* Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Löschen des Clusters

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Problembehandlung

Falls beim Erstellen von HDInsight-Clustern Probleme auftreten, sehen Sie sich die [Voraussetzungen für die Zugriffssteuerung](hdinsight-hadoop-create-linux-clusters-portal.md) an.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie einen HDInsight-Cluster erfolgreich erstellt haben, nutzen Sie die folgenden Ressourcen, um zu erfahren, wie Sie mit Ihrem Cluster arbeiten.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop-Cluster

* [Verwenden von Apache Hive mit HDInsight](hadoop/hdinsight-use-hive.md)
* [Verwenden von MapReduce mit HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase Cluster

* [Erste Schritte mit Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Entwickeln von Java-Anwendungen für Apache HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm-Cluster

* [Entwickeln von Java-Topologien für Storm in HDInsight](storm/apache-storm-develop-java-topology.md)
* [Verwenden von Python-Komponenten in Storm in HDInsight](storm/apache-storm-develop-python-topology.md)
* [Bereitstellen und Überwachen von Topologien mit Storm in HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark-Cluster

* [Erstellen einer eigenständigen Anwendung mit Scala](spark/apache-spark-create-standalone-application.md)
* [Ausführen von Remoteaufträgen in einem Apache Spark-Cluster mithilfe von Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark mit BI: Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](spark/apache-spark-use-bi-tools.md)
* [Apache Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](spark/apache-spark-machine-learning-mllib-ipython.md)