---
title: Installieren von eigenen benutzerdefinierten Apache Hadoop-Anwendungen in Azure HDInsight
description: Hier erfahren Sie, wie Sie Hadoop-Anwendungen in HDInsight installieren.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 0acac29ee49bc94c195d0e13e55fff3a735ad36b
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65859809"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Installieren von benutzerdefinierten Apache Hadoop-Anwendungen in Azure HDInsight

In diesem Artikel erfahren Sie, wie Sie eine [Apache Hadoop](https://hadoop.apache.org/)-Anwendung in Azure HDInsight installieren, die noch nicht im Azure-Portal veröffentlicht wurde. Die Anwendung, die Sie in diesem Artikel installieren, hat den Namen [Hue](https://gethue.com/).

Eine HDInsight-Anwendung kann von Benutzern in einem Linux-basierten HDInsight-Cluster installiert werden.  Diese Anwendungen können von Microsoft oder von unabhängigen Softwareanbietern (Independent Software Vendors, ISVs) bezogen oder aber selbst entwickelt werden.  

Weitere verwandte Artikel:

* [Installieren von HDInsight-Anwendungen](hdinsight-apps-install-applications.md): Hier erfahren Sie, wie Sie eine HDInsight-Anwendung in Ihren Clustern installieren.
* [Veröffentlichen von HDInsight-Anwendungen](hdinsight-apps-publish-applications.md): Hier erfahren Sie, wie Sie benutzerdefinierte HDInsight-Anwendungen im Azure Marketplace veröffentlichen.
* [MSDN: Installieren einer HDInsight-Anwendung](https://msdn.microsoft.com/library/mt706515.aspx): Hier erfahren Sie, wie Sie HDInsight-Anwendungen definieren.

## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie HDInsight-Anwendungen in einem vorhandenen HDInsight-Cluster installieren möchten, benötigen Sie einen HDInsight-Cluster. Informationen zum Erstellen eines solchen Clusters finden Sie unter [Erstellen von Clustern](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Im Zuge der HDInsight-Clustererstellung können Sie auch HDInsight-Anwendungen installieren.

## <a name="install-hdinsight-applications"></a>Installieren von HDInsight-Anwendungen
HDInsight-Anwendungen können bei der Clustererstellung oder in einem vorhandenen HDInsight-Cluster installiert werden. Informationen zum Definieren von Azure Resource Manager-Vorlagen finden Sie unter [MSDN: Installieren einer HDInsight-Anwendung](https://msdn.microsoft.com/library/mt706515.aspx).

Zum Bereitstellen der Anwendung „Hue“ benötigen Sie folgende Dateien:

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): Die Resource Manager-Vorlage zum Installieren der HDInsight-Anwendung. Informationen zum Entwickeln Ihrer eigenen Resource Manager-Vorlage finden Sie unter [MSDN: Installieren einer HDInsight-Anwendung](https://msdn.microsoft.com/library/mt706515.aspx).
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): Die Skriptaktion, die von der Resource Manager-Vorlage zum Konfigurieren des Edgeknotens aufgerufen wird.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): Die von „hue-install_v0.sh“ aufgerufene Hue-Binärdatei.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): Die von „hue-install_v0.sh“ aufgerufene Hue-Binärdatei.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): Eine von „hue-install_v0.sh“ aufgerufene Beispielwebanwendung (Tomcat).

**So installieren Sie Hue in einem bereits vorhandenen HDInsight-Cluster**

1. Klicken Sie auf die folgende Abbildung, um sich bei Azure anzumelden, und öffnen Sie die Resource Manager-Vorlage im Azure-Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Mit dieser Schaltfläche öffnen Sie eine Resource Manager-Vorlage im Azure-Portal.  Die Resource Manager-Vorlage finden Sie unter [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue).  Informationen zum Erstellen dieser Resource Manager-Vorlage finden Sie unter [MSDN: Installieren einer HDInsight-Anwendung](https://msdn.microsoft.com/library/mt706515.aspx).
2. Geben Sie auf dem Blatt **Parameter** Folgendes ein:

   * **ClusterName**: Geben Sie den Namen des Clusters ein, in dem die Anwendung installiert werden soll. Dieser Cluster muss bereits vorhanden sein.
3. Klicken Sie auf **OK** , um die Parameter zu speichern.
4. Wechseln Sie vom Blatt **Benutzerdefinierte Bereitstellung** zu **Ressourcengruppe**.  Die Ressourcengruppe ist ein Container, in dem der Cluster, das abhängige Speicherkonto und andere Ressourcen gruppiert werden. Es wird benötigt, um die Ressourcengruppe als Cluster zu verwenden.
5. Klicken Sie auf **Rechtliche Bedingungen** und dann auf **Erstellen**.
6. Vergewissern Sie sich, dass das Kontrollkästchen **An Dashboard anheften** aktiviert ist, und klicken Sie auf **Erstellen**. Der Installationsstatus kann über die an das Portaldashboard angeheftete Kachel sowie anhand der Portalbenachrichtigung nachverfolgt werden.  (Klicken Sie oben im Portal auf das Glockensymbol.) Die Installation der Anwendung dauert etwa zehn Minuten.

**So installieren Sie Hue im Zuge der Clustererstellung**

1. Klicken Sie auf die folgende Abbildung, um sich bei Azure anzumelden, und öffnen Sie die Resource Manager-Vorlage im Azure-Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Mit dieser Schaltfläche öffnen Sie eine Resource Manager-Vorlage im Azure-Portal.  Die Resource Manager-Vorlage finden Sie unter [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json).  Informationen zum Erstellen dieser Resource Manager-Vorlage finden Sie unter [MSDN: Installieren einer HDInsight-Anwendung](https://msdn.microsoft.com/library/mt706515.aspx).
2. Befolgen Sie die Anleitung, um den Cluster zu erstellen und Hue zu installieren. Weitere Informationen zum Erstellen von HDInsight-Clustern finden Sie unter [Erstellen von Linux-basierten Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Neben dem Azure-Portal können Sie Resource Manager-Vorlagen auch mithilfe von [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) sowie mithilfe der [klassischen Azure CLI](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli) aufrufen.

## <a name="validate-the-installation"></a>Überprüfen der Installation
Sie können den Status der Anwendung im Azure-Portal nachverfolgen und sich so vergewissern, dass die Anwendung installiert wurde. Darüber hinaus können Sie sich vergewissern, dass alle HTTP-Endpunkte wie erwartet verfügbar sind, und die Webseite überprüfen (sofern vorhanden):

**So öffnen Sie das Hue-Portal**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie im linken Menü auf **HDInsight-Cluster** .  Sollte diese Option nicht angezeigt werden, klicken Sie auf **Durchsuchen** und anschließend auf **HDInsight-Cluster**.
3. Klicken Sie auf den Cluster, in dem Sie die Anwendung installiert haben.
4. Klicken Sie auf dem Blatt **Einstellungen** unter der Kategorie **Allgemein** auf **Anwendungen**. Die Anwendung **Hue** wird in der Liste auf dem Blatt **Installierte Apps** angezeigt.
5. Klicken Sie in der Liste auf **Hue** , um die Eigenschaften anzuzeigen.  
6. Klicken Sie auf den Webseitenlink, um die Website zu überprüfen. Öffnen Sie den HTTP-Endpunkt in einem Browser, um die Weboberfläche von Hue zu überprüfen, und öffnen Sie den SSH-Endpunkt per SSH. Informationen hierzu finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="troubleshoot-the-installation"></a>Behandeln von Installationsproblemen
Sie können den Status der Anwendungsinstallation über die Portalbenachrichtigung überprüfen. (Klicken Sie oben im Portal auf das Glockensymbol.)

Wenn eine Anwendungsinstallation nicht erfolgreich ist, können Sie Fehlermeldungen und Debuginformationen an drei Stellen anzeigen:

* HDInsight-Anwendungen: allgemeine Fehlerinformationen.

    Öffnen Sie den Cluster im Portal, und klicken Sie im Blatt „Einstellungen“ auf „Anwendungen“:

    ![HDInsight-Anwendungen: Fehler bei Anwendungsinstallation](./media/hdinsight-apps-install-applications/hdinsight-apps-error.png)
* HDInsight-Skriptaktion: Wenn mit der Fehlermeldung der HDInsight-Anwendung ein Skriptaktionsfehler angegeben wird, werden im Bereich mit den Skriptaktionen Details zum Skriptfehler angezeigt.

    Klicken Sie im Blatt „Einstellungen“ auf „Skriptaktion“. Im Skriptaktionsverlauf werden die Fehlermeldungen angezeigt.

    ![HDInsight-Anwendungen: Fehler bei Skriptaktion](./media/hdinsight-apps-install-applications/hdinsight-apps-script-action-error.png)
* Ambari-Webbenutzeroberfläche: Wenn das Installationsskript der Grund für den Fehler war, können Sie die Ambari-Webbenutzeroberfläche verwenden, um die vollständigen Protokolle zu den Installationsskripts zu überprüfen.

    Weitere Informationen finden Sie unter [Problembehandlung](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting).

## <a name="remove-hdinsight-applications"></a>Entfernen von HDInsight-Anwendungen
HDInsight-Anwendungen können auf unterschiedliche Weise gelöscht werden.

### <a name="use-portal"></a>Mithilfe des Portals
**So entfernen Sie eine Anwendung mithilfe des Portals**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie im linken Menü auf **HDInsight-Cluster** .  Sollte diese Option nicht angezeigt werden, klicken Sie auf **Durchsuchen** und anschließend auf **HDInsight-Cluster**.
3. Klicken Sie auf den Cluster, in dem Sie die Anwendung installiert haben.
4. Klicken Sie auf dem Blatt **Einstellungen** unter der Kategorie **Allgemein** auf **Anwendungen**. Eine Liste mit installierten Anwendungen wird angezeigt. Im Rahmen dieses Tutorials wird **Hue** auf dem Blatt **Installierte Apps** angezeigt.
5. Klicken Sie mit der rechten Maustaste auf die zu entfernende Anwendung, und klicken Sie anschließend auf **Löschen**.
6. Klicken Sie auf **Ja**, um zu bestätigen.

Über das Portal können Sie auch den Cluster oder die Ressourcengruppe löschen, die die Anwendung enthält.

### <a name="use-azure-powershell"></a>Mithilfe von Azure PowerShell
Mit Azure PowerShell können Sie den Cluster oder die Ressourcengruppe löschen. Weitere Informationen finden Sie unter [Löschen von Clustern mithilfe von Azure PowerShell](hdinsight-administer-use-powershell.md#delete-clusters).

### <a name="use-azure-cli"></a>Mithilfe der Azure-Befehlszeilenschnittstelle
Mit der Azure-Befehlszeilenschnittstelle können Sie den Cluster oder die Ressourcengruppe löschen. Weitere Informationen finden Sie unter [Löschen von Clustern mithilfe der Azure-Befehlszeilenschnittstelle](hdinsight-administer-use-command-line.md#delete-clusters).

## <a name="next-steps"></a>Nächste Schritte
* [MSDN: Installieren einer HDInsight-Anwendung](https://msdn.microsoft.com/library/mt706515.aspx): Hier erfahren Sie, wie Sie Resource Manager-Vorlagen für die Bereitstellung von HDInsight-Anwendungen entwickeln.
* [Installieren von HDInsight-Anwendungen](hdinsight-apps-install-applications.md): Hier erfahren Sie, wie Sie eine HDInsight-Anwendung in Ihren Clustern installieren.
* [Veröffentlichen von HDInsight-Anwendungen](hdinsight-apps-publish-applications.md): Hier erfahren Sie, wie Sie benutzerdefinierte HDInsight-Anwendungen im Azure Marketplace veröffentlichen.
* [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md): Hier erfahren Sie, wie Sie mithilfe der Skriptaktion zusätzliche Anwendungen installieren.
* [Erstellen von Linux-basierten Apache Hadoop-Clustern in HDInsight mit Resource Manager-Vorlagen](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Hier erfahren Sie, wie Sie Resource Manager-Vorlagen für die Erstellung von HDInsight-Clustern aufrufen.
* [Verwenden leerer Edgeknoten in HDInsight](hdinsight-apps-use-edge-node.md): Erfahren Sie, wie Sie einen leeren Edgeknoten zum Zugreifen auf HDInsight-Cluster, Testen von HDInsight-Anwendungen und Hosten von HDInsight-Anwendungen verwenden.
