---
title: Installieren Ihrer benutzerdefinierten Apache Hadoop-Anwendungen in Azure HDInsight
description: Erfahren Sie, wie Sie HDInsight-Anwendungen für Apache Hadoop-Cluster in Azure HDInsight installieren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 11/29/2019
ms.openlocfilehash: d110f4ee0248b6ea069c6033dd69840eec1a18f3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007153"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Installieren von benutzerdefinierten Apache Hadoop-Anwendungen in Azure HDInsight

In diesem Artikel erfahren Sie, wie Sie eine [Apache Hadoop](https://hadoop.apache.org/)-Anwendung in Azure HDInsight installieren, die noch nicht im Azure-Portal veröffentlicht wurde. Die in diesem Artikel installierte Anwendung ist [Hue](https://gethue.com/).

Eine HDInsight-Anwendung kann von Benutzern in einem HDInsight-Cluster installiert werden.  Diese Anwendungen können von Microsoft oder von unabhängigen Softwareanbietern (Independent Software Vendors, ISVs) bezogen oder aber selbst entwickelt werden.  

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie HDInsight-Anwendungen in einem vorhandenen HDInsight-Cluster installieren möchten, benötigen Sie einen HDInsight-Cluster. Informationen zum Erstellen eines solchen Clusters finden Sie unter [Erstellen von Clustern](hadoop/apache-hadoop-linux-tutorial-get-started.md). Im Zuge der HDInsight-Clustererstellung können Sie auch HDInsight-Anwendungen installieren.

## <a name="install-hdinsight-applications"></a>Installieren von HDInsight-Anwendungen

HDInsight-Anwendungen können bei der Clustererstellung oder in einem vorhandenen HDInsight-Cluster installiert werden. Informationen zum Definieren von Azure Resource Manager-Vorlagen finden Sie unter [MSDN: Installieren einer HDInsight-Anwendung](/rest/api/hdinsight/hdinsight-application).

Zum Bereitstellen der Anwendung „Hue“ benötigen Sie folgende Dateien:

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): Die Resource Manager-Vorlage zum Installieren der HDInsight-Anwendung. Informationen zum Entwickeln Ihrer eigenen Resource Manager-Vorlage finden Sie unter [MSDN: Installieren einer HDInsight-Anwendung](/rest/api/hdinsight/hdinsight-application).
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): Die Skriptaktion, die von der Resource Manager-Vorlage zum Konfigurieren des Edgeknotens aufgerufen wird.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): Die von „hue-install_v0.sh“ aufgerufene Hue-Binärdatei.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): Die von „hue-install_v0.sh“ aufgerufene Hue-Binärdatei.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): Eine von „hue-install_v0.sh“ aufgerufene Beispielwebanwendung (Tomcat).

### <a name="to-install-hue-to-an-existing-hdinsight-cluster"></a>So installieren Sie Hue in einem bereits vorhandenen HDInsight-Cluster

1. Wählen Sie die folgende Abbildung aus, um sich bei Azure anzumelden, und öffnen Sie die Resource Manager-Vorlage im Azure-Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Die Resource Manager-Vorlage finden Sie unter [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue).  Informationen zum Erstellen dieser Resource Manager-Vorlage finden Sie unter [MSDN: Installieren einer HDInsight-Anwendung](/rest/api/hdinsight/hdinsight-application).

1. Wählen Sie in der Dropdownliste die **Ressourcengruppe** aus, die Ihren Cluster enthält. Es muss die gleiche Ressourcengruppe wie im Cluster verwendet werden.

1. Geben Sie den Namen des Clusters ein, in dem die Anwendung installiert werden soll. Dieser Cluster muss bereits vorhanden sein.

1. Aktivieren Sie das Kontrollkästchen **Ich stimme den oben genannten Geschäftsbedingungen zu**.

1. Wählen Sie die Option **Kaufen**.

Der Installationsstatus kann über die an das Portaldashboard angeheftete Kachel sowie anhand der Portalbenachrichtigung nachverfolgt werden.  (Klicken Sie oben im Portal auf das Glockensymbol.) Die Installation der Anwendung dauert etwa zehn Minuten.

### <a name="to-install-hue-while-creating-a-cluster"></a>So installieren Sie Hue bei der Clustererstellung

1. Wählen Sie die folgende Abbildung aus, um sich bei Azure anzumelden, und öffnen Sie die Resource Manager-Vorlage im Azure-Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Die Resource Manager-Vorlage finden Sie unter [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json).  Informationen zum Erstellen dieser Resource Manager-Vorlage finden Sie unter [MSDN: Installieren einer HDInsight-Anwendung](/rest/api/hdinsight/hdinsight-application).

2. Befolgen Sie die Anleitung, um den Cluster zu erstellen und Hue zu installieren. Weitere Informationen zum Erstellen von HDInsight-Clustern finden Sie unter [Erstellen von Linux-basierten Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="other-installation-methods"></a>Andere Installationsmethoden

Neben dem Azure-Portal können Sie Resource Manager-Vorlagen auch mithilfe von [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) sowie mithilfe der [Azure-Befehlszeilenschnittstelle](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli) aufrufen.

## <a name="validate-the-installation"></a>Überprüfen der Installation

Sie können den Status der Anwendung im Azure-Portal nachverfolgen und sich so vergewissern, dass die Anwendung installiert wurde. Darüber hinaus können Sie sich vergewissern, dass alle HTTP-Endpunkte wie erwartet verfügbar sind, und die Webseite überprüfen (sofern vorhanden).

Sie können für **Hue** die folgenden Schritte verwenden:

### <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie den Cluster aus, in dem Sie die Anwendung installiert haben.
1. Wählen Sie im Menü **Einstellungen** die Option **Anwendungen** aus.
1. Wählen Sie in der Liste **Hue** aus, um die Eigenschaften anzuzeigen.  
1. Wählen Sie den Webseitenlink aus, um die Website zu überprüfen.

### <a name="azure-cli"></a>Azure CLI

Ersetzen Sie `CLUSTERNAME` und `RESOURCEGROUP` durch die entsprechenden Werte, und geben Sie dann die folgenden Befehle ein:

* Listet alle Anwendungen für den HDInsight-Cluster auf.

    ```azurecli
    az hdinsight application list --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

* Ruft Eigenschaften der angegebenen Anwendung ab.

    ```azurecli
    az hdinsight application show --name hue --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

## <a name="troubleshoot-the-installation"></a>Behandeln von Installationsproblemen

Sie können den Status der Anwendungsinstallation über die Portalbenachrichtigung überprüfen. (Klicken Sie oben im Portal auf das Glockensymbol.)

Wenn eine Anwendungsinstallation nicht erfolgreich ist, können Sie Fehlermeldungen und Debuginformationen an drei Stellen anzeigen:

* HDInsight-Anwendungen: allgemeine Fehlerinformationen.

    Öffnen Sie den Cluster im Portal, und wählen Sie unter „Einstellungen“ die Option „Anwendungen“ aus:

    ![HDInsight-Anwendungen: Fehler bei Anwendungsinstallation](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png)

* HDInsight-Skriptaktion: Wenn mit der Fehlermeldung der HDInsight-Anwendung ein Skriptaktionsfehler angegeben wird, werden im Bereich mit den Skriptaktionen Details zum Skriptfehler angezeigt.

    Wählen Sie unter „Einstellungen“ die Option „Skriptaktion“ aus. Im Skriptaktionsverlauf werden die Fehlermeldungen angezeigt.

    ![HDInsight-Anwendungen: Fehler bei Skriptaktion](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png)

* Apache Ambari-Webbenutzeroberfläche: Wenn das Installationsskript der Grund für den Fehler war, können Sie die Ambari-Webbenutzeroberfläche verwenden, um die vollständigen Protokolle zu den Installationsskripts zu überprüfen.

    Weitere Informationen finden Sie unter [Behandeln von Problemen mit Skriptaktionen](./troubleshoot-script-action.md).

## <a name="remove-hdinsight-applications"></a>Entfernen von HDInsight-Anwendungen

### <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie den Cluster aus, in dem Sie die Anwendung installiert haben.
1. Wählen Sie im Menü **Einstellungen** die Option **Anwendungen** aus.
1. Klicken Sie mit der rechten Maustaste auf die zu entfernende Anwendung, und wählen Sie dann **Löschen** aus.
1. Klicken Sie auf **Ja**, um zu bestätigen.

### <a name="azure-cli"></a>Azure CLI

Ersetzen Sie `NAME`, `CLUSTERNAME` und `RESOURCEGROUP` durch die entsprechenden Werte, und geben Sie dann den folgenden Befehl ein:

```azurecli
az hdinsight application delete --name NAME --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
```

## <a name="next-steps"></a>Nächste Schritte

* [MSDN: Installieren einer HDInsight-Anwendung](/rest/api/hdinsight/hdinsight-application): Hier erfahren Sie, wie Sie Resource Manager-Vorlagen für die Bereitstellung von HDInsight-Anwendungen entwickeln.
* [Installieren von HDInsight-Anwendungen](hdinsight-apps-install-applications.md): Hier erfahren Sie, wie Sie eine HDInsight-Anwendung in Ihren Clustern installieren.
* [Veröffentlichen von HDInsight-Anwendungen](hdinsight-apps-publish-applications.md): Hier erfahren Sie, wie Sie benutzerdefinierte HDInsight-Anwendungen im Azure Marketplace veröffentlichen.
* [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md): Hier erfahren Sie, wie Sie mithilfe der Skriptaktion zusätzliche Anwendungen installieren.
* [Erstellen von Linux-basierten Apache Hadoop-Clustern in HDInsight mit Resource Manager-Vorlagen](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Hier erfahren Sie, wie Sie Resource Manager-Vorlagen für die Erstellung von HDInsight-Clustern aufrufen.
* [Verwenden leerer Edgeknoten in HDInsight](hdinsight-apps-use-edge-node.md): Erfahren Sie, wie Sie einen leeren Edgeknoten zum Zugreifen auf HDInsight-Cluster, Testen von HDInsight-Anwendungen und Hosten von HDInsight-Anwendungen verwenden.
