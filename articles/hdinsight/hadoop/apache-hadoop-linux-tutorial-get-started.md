---
title: 'Schnellstart: Erstellen eines Apache Hadoop-Clusters in Azure HDInsight mit einer Resource Manager-Vorlage'
description: In diesem Schnellstart erstellen Sie einen Apache Hadoop-Cluster in Azure HDInsight mit einer Resource Manager-Vorlage.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: fe1a76406c1a8a55996b4c099e04f0925daaa74c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946527"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-arm-template"></a>Schnellstart: Erstellen eines Apache Hadoop-Clusters in Azure HDInsight mithilfe einer ARM-Vorlage

In dieser Schnellstartanleitung verwenden Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage), um einen [Apache Hadoop](./apache-hadoop-introduction.md)-Cluster in Azure HDInsight zu erstellen. Hadoop war ursprünglich ein Open-Source-Framework für die verteilte Verarbeitung und Analyse umfangreicher Datasets in Clustern. Das Hadoop-Ökosystem umfasst verwandte Software und Hilfsprogramme, einschließlich Apache Hive, Apache HBase, Spark, Kafka und viele andere.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]
  
Zurzeit stehen in HDInsight [sieben verschiedene Clustertypen](../hdinsight-overview.md#cluster-types-in-hdinsight) zur Verfügung. Jeder Clustertyp unterstützt eine andere Gruppe von Komponenten. Alle Clustertypen unterstützen Hive. Eine Liste mit den unterstützten Komponenten in HDInsight finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen](../hdinsight-component-versioning.md)  

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/).

:::code language="json" source="~/quickstart-templates/101-hdinsight-linux-ssh-password/azuredeploy.json":::

Zwei Azure-Ressourcen sind in der Vorlage definiert:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): Erstellen eines Azure Storage-Kontos
* [Microsoft.HDInsight/cluster](/azure/templates/microsoft.hdinsight/clusters): Erstellen eines HDInsight-Clusters

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Wählen Sie unten die Schaltfläche **In Azure bereitstellen** aus, um sich bei Azure anzumelden und die ARM-Vorlage zu öffnen.

    [![In Azure bereitstellen](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json)

1. Geben Sie folgende Werte ein bzw. wählen diese aus:

    |Eigenschaft  |BESCHREIBUNG  |
    |---------|---------|
    |Subscription|Wählen Sie in der Dropdownliste das Azure-Abonnement aus, das für den Cluster verwendet wird.|
    |Resource group|Wählen Sie in der Dropdownliste Ihre vorhandene Ressourcengruppe oder die Option **Neu erstellen** aus.|
    |Standort|Als Wert wird automatisch der Speicherort für die Ressourcengruppe eingefügt.|
    |Clustername|Geben Sie einen global eindeutigen Namen ein. Verwenden Sie für diese Vorlage nur Kleinbuchstaben und Zahlen.|
    |Clustertyp | Wählen Sie **hadoop** aus. |
    |Benutzername für Clusteranmeldung|Geben Sie den Benutzernamen an, der Standardwert ist **admin**.|
    |Kennwort für Clusteranmeldung|Geben Sie ein Kennwort an. Das Kennwort muss mindestens zehn Zeichen lang sein und mindestens eine Ziffer, einen Groß- und einen Kleinbuchstaben sowie ein nicht alphanumerisches Zeichen enthalten (mit Ausnahme folgender Zeichen: ' " `). |
    |SSH-Benutzername|Geben Sie den Benutzernamen an, der Standardwert ist **sshuser**.|
    |SSH-Kennwort|Geben Sie das Kennwort an.|

    Einige Eigenschaften wurden in der Vorlage hartcodiert.  Diese Werte können über die Vorlage konfiguriert werden. Eine ausführlichere Erläuterung dieser Eigenschaften finden Sie unter [Erstellen von Linux-basierten Apache Hadoop-Clustern in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > Die Werte, die Sie angeben, müssen eindeutig sein und sollten den Benennungsrichtlinien folgen. Die Vorlage führt keine Überprüfungen durch. Wenn die Werte, die Sie angeben, bereits verwendet werden oder nicht den Richtlinien entsprechen, erhalten Sie eine Fehlermeldung, nachdem Sie die Vorlage übermittelt haben.  

    ![HDInsight Linux: erste Schritte mit der Resource Manager-Vorlage im Portal](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Bereitstellen eines Hadoop-Clusters in HDInsight mithilfe des Azure-Portals und einer Resource Manager-Vorlage für Ressourcengruppen")

1. Sehen Sie sich die **GESCHÄFTSBEDINGUNGEN** an. Wählen Sie anschließend **Ich stimme den oben genannten Geschäftsbedingungen zu** und dann **Kaufen** aus. Sie erhalten eine Benachrichtigung, dass die Bereitstellung derzeit durchgeführt wird. Das Erstellen eines Clusters dauert ca. 20 Minuten.

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Nachdem der Cluster erstellt wurde, erhalten Sie die Benachrichtigung **Bereitstellung erfolgreich** mit dem Link **Zu Ressource wechseln**. Auf der Seite „Ressourcengruppe“ sind der neue HDInsight-Cluster und der mit dem Cluster verbundene Standardspeicher aufgeführt. Jeder Cluster verfügt über eine Abhängigkeit von einem [Azure Blob Storage](../hdinsight-hadoop-use-blob-storage.md)-Konto, einer [Azure Data Lake Storage Gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md)- oder einer [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md)-Instanz. Dieses wird als Standardspeicherkonto bezeichnet. Der HDInsight-Cluster und das zugehörige Speicherkonto müssen sich in derselben Azure-Region befinden. Beim Löschen von Clustern wird das Speicherkonto nicht gelöscht.

> [!NOTE]  
> Andere Methoden zur Erstellung von Clustern und Informationen zu den in diesem Schnellstart verwendeten Eigenschaften finden Sie unter [Erstellen von HDInsight-Clustern](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nachdem Sie den Schnellstart abgeschlossen haben, können Sie den Cluster löschen. Mit HDInsight werden Ihre Daten in Azure Storage gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird. Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen.

> [!NOTE]  
> Wenn Sie *sofort* mit dem nächsten Tutorial fortfahren, um zu erfahren, wie Sie ETL-Vorgänge mithilfe von Hadoop in HDInsight ausführen, können Sie den Cluster weiterhin ausführen. Ansonsten müssen Sie in diesem Tutorial erneut einen Hadoop-Cluster erstellen. Wenn Sie jedoch nicht direkt mit dem nächsten Tutorial fortfahren, sollten Sie den Cluster jetzt löschen.

Navigieren Sie im Azure-Portal zu Ihrem Cluster, und wählen Sie **Löschen** aus.

![HDInsight: Löschen eines Clusters im Portal](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "HDInsight: Löschen eines Clusters im Portal")

Sie können auch den Namen der Ressourcengruppe auswählen, um die Seite für die Ressourcengruppe zu öffnen, und dann **Ressourcengruppe löschen** auswählen. Indem Sie die Ressourcengruppe löschen, löschen Sie sowohl den HDInsight-Cluster als auch das Standardspeicherkonto.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie erfahren, wie Sie einen Apache Hadoop-Cluster in HDInsight mit einer ARM-Vorlage erstellen. Im nächsten Artikel erfahren Sie, wie Sie mithilfe von Hadoop in HDInsight einen ETL-Vorgang zum Extrahieren, Transformieren und Laden von Daten ausführen.

> [!div class="nextstepaction"]
> [Extrahieren, Transformieren und Laden von Daten mithilfe von Interactive Query in HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
