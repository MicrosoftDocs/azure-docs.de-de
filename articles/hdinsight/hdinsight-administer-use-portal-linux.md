---
title: Verwalten von Apache Hadoop-Clustern in HDInsight mit dem Azure-Portal
description: Hier erhalten Sie Informationen zum Erstellen und Verwalten von Azure HDInsight-Clustern mithilfe des Azure-Portals.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/24/2020
ms.openlocfilehash: 7c644583e994e51f11bdf7991755a6ea94b01e76
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012805"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Verwalten von Apache Hadoop-Clustern in HDInsight mit dem Azure-Portal

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Mit dem [Azure-Portal](https://portal.azure.com) können Sie [Apache Hadoop](https://hadoop.apache.org/)-Cluster in Azure HDInsight verwalten. Rufen Sie über die Registerkartenauswahl oben Informationen zum Verwalten von Hadoop-Clustern in HDInsight mit anderen Tools ab.

## <a name="prerequisites"></a>Voraussetzungen

Ein vorhandener Apache Hadoop-Cluster in HDInsight.  Weitere Informationen finden Sie unter [Erstellen von Linux-basierten Clustern in HDInsight mithilfe des Azure-Portals](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Erste Schritte

Melden Sie sich bei [https://portal.azure.com](https://portal.azure.com) an.

## <a name="list-and-show-clusters"></a><a name="showClusters"></a>Auflisten und Anzeigen von Clustern

Auf der Seite **HDInsight-Cluster** werden Ihre vorhandenen Cluster aufgeführt.  Im Portal:
1. Wählen Sie im Menü links **Alle Dienste** aus.
2. Wählen Sie unter **ANALYSEN** die Option **HDInsight-Cluster** aus.

## <a name="cluster-home-page"></a><a name="homePage"></a>Cluster-Homepage

Wählen Sie auf der Seite [**HDInsight-Cluster**](#showClusters) den Namen Ihres Clusters aus.  Dadurch wird die Ansicht **Übersicht** geöffnet, die dem folgenden Screenshot ähnelt:

![Azure-Portal – HDInsight-Cluster-Zusammenfassung](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Obere Menüleiste:**  

| Element| BESCHREIBUNG |
|---|---|
|Move|Verschiebt den Cluster in eine andere Ressourcengruppe oder ein anderes Abonnement.|
|Löschen|Löscht den Cluster. |
|Aktualisieren|Aktualisiert die Sicht.|

**Menü auf der linken Seite:**  

  - **Obere linke Menüleiste:**

    | Element| BESCHREIBUNG |
    |---|---|
    |Übersicht|Gibt allgemeine Informationen zu Ihrem Cluster an.|
    |Aktivitätsprotokoll|Dient zum Anzeigen und Abfragen von Aktivitätsprotokollen.|
    |Zugriffssteuerung (IAM)|Verwenden Sie Rollenzuweisungen.  Weitere Informationen finden Sie unter [Verwenden von Rollenzuweisungen zum Verwalten Ihrer Azure-Abonnementressourcen](../role-based-access-control/role-assignments-portal.md).|
    |`Tags`|Dient zum Festlegen von Schlüssel-Wert-Paaren, um eine benutzerdefinierte Taxonomie für Ihre Clouddienste zu definieren. Sie können z. B. einen Schlüssel mit dem Namen **Projekt** erstellen und dann einen gemeinsamen Wert für alle mit einem bestimmten Projekt verknüpften Dienste verwenden.|
    |Diagnose und Problembehandlung|Dient zum Anzeigen von Informationen zur Problembehandlung.|
    |Schnellstart|Zeigt hilfreiche Informationen für die ersten Schritte mit HDInsight an.|
    |Tools|Hilfeinformationen für HDInsight-bezogene Tools.|

  - **Menü „Einstellungen“**  

    | Element| BESCHREIBUNG |
    |---|---|
    |Clustergröße|Dient zum Überprüfen, Erhöhen und Verringern der Anzahl von Workerknoten im Cluster. Weitere Informationen finden Sie unter [Skalieren von Clustern](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Kontingentgrenzen|Dient zum Anzeigen der verwendeten und verfügbaren Kernspeicher für Ihr Abonnement.|
    |SSH + Clusteranmeldung|Zeigt die Anweisungen für die Verbindung mit dem Cluster über eine Secure Shell-Verbindung (SSH) an. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Data Lake Storage Gen1|Dient zum Konfigurieren des Data Lake Storage Gen1-Zugriffs.  Weitere Informationen finden Sie unter [Schnellstart: Einrichten von Clustern in HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).|
    |Speicherkonten|Dient zum Anzeigen der Speicherkonten und Schlüssel. Die Speicherkonten werden während der Clustererstellung konfiguriert.|
    |Anwendungen|Dient zum Hinzufügen und Entfernen von HDInsight-Anwendungen.  Weitere Informationen finden Sie unter [Installieren benutzerdefinierter HDInsight-Anwendungen](hdinsight-apps-install-custom-applications.md).|
    |Skriptaktionen|Dienst zum Anwenden von Bashskripts auf den Cluster. Weitere Informationen finden Sie unter [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).|
    |Externe Metastores|Anzeigen der Metastores für [Apache Hive](https://hive.apache.org/) und [Apache Oozie](https://oozie.apache.org/). Die Metastores können nur während der Clustererstellung konfiguriert werden.|
    |HDInsight-Partner|Dient zum Hinzufügen/Entfernen des aktuellen HDInsight-Partners.|
    |Eigenschaften|Dient zum Anzeigen der [Clustereigenschaften](#properties).|
    |Locks|Dient zum Hinzufügen einer Sperre, um zu verhindern, dass der Cluster geändert oder gelöscht wird.|
    |Exportieren der Vorlage|Dient zum Anzeigen und Exportieren der Azure Resource Manager-Vorlage für den Cluster. Derzeit können Sie nur das abhängige Azure-Speicherkonto exportieren. Weitere Informationen finden Sie unter [Erstellen Linux-basierter Apache Hadoop-Cluster in HDInsight mithilfe von Azure Resource Manager-Vorlagen](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Menü „Überwachung“**

    | Element| BESCHREIBUNG |
    |---|---|
    |Alerts|Dient zum Verwalten der Warnungen und Aktionen.|
    |Metriken|Überwacht die Clustermetriken in Azure Monitor-Protokollen.|
    |Diagnoseeinstellungen|Einstellungen zum Speicherort der Diagnosemetriken.|
    |Azure Monitor|Überwacht Ihre Cluster in Azure Monitor.|

  - **Menü „Support und Problembehandlung“**

    | Element| BESCHREIBUNG |
    |---|---|
    |Ressourcenintegrität|Weitere Informationen finden Sie unter [Übersicht über Azure Resource Health](../service-health/resource-health-overview.md).|
    |Neue Supportanfrage|Ermöglicht die Erstellung eines Supporttickets für den Microsoft-Support.|

## <a name="cluster-properties"></a><a name="properties"></a>Clustereigenschaften

Wählen Sie auf der [Cluster-Homepage](#homePage) unter **Einstellungen** die Option **Eigenschaften** aus.

|Element | BESCHREIBUNG |
|---|---|
|HOSTNAME|Clustername.|
|CLUSTER URL|Dies ist die URL für die Ambari-Weboberfläche.|
|Privater Endpunkt|Der private Endpunkt für den Cluster.|
|Secure Shell (SSH)|Der Benutzername und der Hostname für den Zugriff auf den Cluster über SSH.|
|STATUS|Enthält einen der folgenden Werte: Aborted, Accepted, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operational, Running, Error, Deleting, Deleted, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization.|
|REGION|Azure-Standort. Eine Liste der unterstützten Azure-Standorte finden Sie im Dropdownlistenfeld **Region** unter [HDInsight-Preise](https://azure.microsoft.com/pricing/details/hdinsight/).|
|DATE CREATED|Das Datum der Clusterbereitstellung.|
|OPERATING SYSTEM|Entweder **Windows** oder **Linux**.|
|TYPE|Hadoop, HBase, Storm, Spark.|
|Version|Siehe [HDInsight-Versionen](hdinsight-component-versioning.md).|
|TLS-Mindestversion|Die TLS-Version.|
|SUBSCRIPTION|Abonnementname.|
|DEFAULT DATA SOURCE|Das Standardcluster-Dateisystem.|
|Workerknotengrößen|Die ausgewählte VM-Größe des Workerknotens.|
|Hauptknotengröße|Die ausgewählte VM-Größe der Hauptknoten.|
|Virtuelles Netzwerk|Der Name des virtuellen Netzwerks, für das der Cluster bereitgestellt wird, wenn eines zum Zeitpunkt der Bereitstellung ausgewählt wurde.|

## <a name="move-clusters"></a>Verschieben von Clustern

Sie können einen HDInsight-Cluster in eine andere Azure-Ressourcengruppe oder ein anderes Abonnement verschieben.

Auf der [Startseite des Clusters](#homePage):

1. Wählen Sie auf der oberen Menüleiste **Verschieben** aus.
2. Wählen Sie **In eine andere Ressourcengruppe verschieben** oder **In ein anderes Abonnement verschieben** aus.
3. Befolgen Sie die Anweisungen auf der neuen Seite.

## <a name="delete-clusters"></a>Löschen von Clustern

Wenn Sie einen Cluster löschen, werden dadurch weder das Standardspeicherkonto noch die verknüpften Speicherkonten gelöscht. Sie können den Cluster mit den gleichen Speicherkonten und den gleichen Metastores neu erstellen. Sie sollten einen neuen Standardblobcontainer verwenden, wenn Sie den Cluster neu erstellen.

Auf der [Startseite des Clusters](#homePage):

1. Klicken Sie auf der oberen Menüleiste auf **Löschen**.
2. Befolgen Sie die Anweisungen auf der neuen Seite.

Siehe auch [Anhalten/Herunterfahren von Clustern](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Hinzufügen weiterer Speicherkonten

Nachdem ein Cluster erstellt wurde, können Sie weitere Azure-Speicherkonten und Azure Data Lake Storage-Konten hinzufügen. Weitere Informationen finden Sie unter [Hinzufügen zusätzlicher Speicherkonten zu HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Skalieren von Clustern

Mithilfe der Clusterskalierung können Sie die Anzahl der von einem Azure HDInsight-Cluster verwendeten Workerknoten ändern, ohne den Cluster neu erstellen zu müssen.

Weitere Informationen finden Sie unter [Skalieren von HDInsight-Clustern](./hdinsight-scaling-best-practices.md).

## <a name="pauseshut-down-clusters"></a>Anhalten/Herunterfahren von Clustern

Die meisten Hadoop-Aufträge sind Batchaufträge, die nur gelegentlich ausgeführt werden. Bei den meisten Hadoop-Clustern gibt es immer wieder längere Phasen, in denen der Cluster nicht für die Verarbeitung genutzt wird. Mit HDInsight werden Ihre Daten in Azure Storage gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird.
Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen.

Es gibt viele Methoden, mit denen Sie den Prozess programmieren können:

- Verwenden Sie Azure Data Factory. Informationen zum Erstellen bedarfsgesteuerter verknüpfter HDInsight-Dienste finden Sie unter [Erstellen von bedarfsgesteuerten Linux-basierten Apache Hadoop-Clustern in HDInsight mit Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md).
- Verwenden Sie Azure PowerShell.  Weitere Informationen hierzu finden Sie unter [Analysieren von Flugverspätungsdaten mit Hive in HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md).
- Verwenden Sie die Azure-Befehlszeilenschnittstelle. Weitere Informationen finden Sie unter [Verwalten von Azure HDInsight-Clustern mit der Azure-Befehlszeilenschnittstelle](hdinsight-administer-use-command-line.md).
- Verwenden das Sie HDInsight .NET-SDK. Weitere Informationen finden Sie unter [Übermitteln von Apache Hadoop-Aufträgen](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Die Preisinformationen finden Sie unter [HDInsight Preise](https://azure.microsoft.com/pricing/details/hdinsight/). Informationen zum Löschen eines Clusters aus dem Portal finden Sie unter [Löschen von Clustern](#delete-clusters)

## <a name="upgrade-clusters"></a>Aktualisieren von Clustern

Informationen finden Sie unter [Aktualisieren eines HDInsight-Clusters auf eine neuere Version](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Öffnen der Apache Ambari-Webbenutzeroberfläche

Ambari bietet eine intuitive, einfach zu bedienende Webbenutzeroberfläche für die Hadoop-Verwaltung, gesichert durch die RESTful-APIs. Mit Ambari können Systemadministratoren Hadoop-Cluster verwalten und überwachen.

Auf der [Startseite des Clusters](#homePage):

1. Wählen Sie **Clusterdashboards** aus.

    ![Apache Hadoop-Clustermenü in HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Wählen Sie auf der neuen Seite die **Ambari-Homepage** aus.
1. Geben Sie den Benutzernamen und das Kennwort für den Cluster ein.  Der Standardbenutzername für den Cluster lautet _admin_.

Weitere Informationen finden Sie unter [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Ändern von Kennwörtern

Ein HDInsight-Cluster kann über zwei Benutzerkonten verfügen. Das HDInsight-Clusterbenutzerkonto (auch HTTP-Benutzerkonto genannt) und das SSH-Benutzerkonto werden während des Erstellungsprozesses erstellt. Sie können das Portal verwenden, um das Kennwort für das Clusterbenutzerkonto zu ändern, und Skriptaktionen, um das SSH-Benutzerkonto zu ändern.

### <a name="change-the-cluster-user-password"></a>Ändern des Clusterbenutzerkennworts

> [!NOTE]  
> Wenn Sie das Kennwort für den Clusterbenutzer (Admin) ändern, können Skriptaktionen, die für diesen Cluster ausgeführt werden, möglicherweise fehlschlagen. Wenn Sie Skriptaktionen beibehalten möchten, deren Ziel Workerknoten sind, können diese Skripts fehlschlagen, sobald Sie dem Cluster über eine Größenänderung Knoten hinzufügen. Weitere Informationen zu Skriptaktionen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

Auf der [Startseite des Clusters](#homePage):
1. Wählen Sie unter **Einstellungen** die Option **SSH + Clusteranmeldung** aus.
2. Wählen Sie **Anmeldeinformation zurücksetzen** aus.
3. Geben Sie in die Textfelder das neue Kennwort ein, und bestätigen Sie dieses.
4. Klicken Sie auf **OK**.

Das Kennwort wird auf allen Knoten im Cluster geändert.

### <a name="change-the-ssh-user-password-or-public-key"></a>Ändern des SSH-Benutzerkennworts oder des öffentlichen Schlüssels

1. Speichern Sie den folgenden Text mit einem Text-Editor als Datei namens **changecredentials.sh**.

    > [!IMPORTANT]  
    > Sie müssen einen Editor verwenden, der „LF“ als Zeilenende verwendet. Wenn der Editor „CRLF“ verwendet, funktioniert das Skript nicht.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Laden Sie die Datei an einen Speicherort hoch, auf den von HDInsight über eine HTTP- oder HTTPS-Adresse zugegriffen werden kann. Verwenden Sie z.B. einen öffentlichen Dateispeicher wie OneDrive oder Azure Blob Storage. Speichern Sie den URI (HTTP- oder HTTPS-Adresse) in der Datei, da dieser URI im nächsten Schritt benötigt wird.
3. Wählen Sie auf der [Startseite des Clusters](#homePage) unter **Einstellungen** die Option **Skriptaktionen** aus.
4. Wählen Sie auf der Seite **Skriptaktionen** die Option **+ Neue übermitteln** aus.
5. Geben Sie auf der Seite **Skriptaktion übermitteln** die folgenden Informationen ein:

> [!NOTE]
> Folgende Zeichen dürfen in den SSH-Kennwörtern nicht verwendet werden:
> ```
> " ' ` / \ < % ~ | $ & ! 
> ```

   | Feld | Wert |
   | --- | --- |
   | Skripttyp | Wählen Sie in der Dropdownliste **– Benutzerdefiniert** aus.|
   | Name |"Change ssh credentials" |
   | Bash-Skript-URI |Der URI der Datei „changecredentials.sh“ |
   | Knotentyp(en): (Hauptknoten, Worker, Nimbus, Supervisor oder Zookeeper.) |✓ für alle aufgeführten Knotentypen |
   | Parameter |Geben Sie den SSH-Benutzernamen und dann das neue Kennwort ein. Es muss ein Leerzeichen zwischen den Benutzernamen und das Kennwort eingefügt werden. |
   | Speichern Sie diese Skriptaktion ... |Lassen Sie dieses Feld deaktiviert. |

6. Wählen Sie **Erstellen** aus, um das Skript anzuwenden. Nachdem das Skript ausgeführt wurde, können Sie per SSH mit den neuen Anmeldeinformationen eine Verbindung mit dem Cluster herstellen.

## <a name="find-the-subscription-id"></a>Ermitteln der Abonnement-ID

Jeder Cluster ist mit einem Azure-Abonnement verknüpft.  Sie können die Azure-Abonnement-ID auf der [Cluster-Homepage](#homePage) einsehen.

## <a name="find-the-resource-group"></a>Suchen der Ressourcengruppe

Im Azure Resource Manager-Modus wird jeder HDInsight-Cluster mit einer Azure Resource Manager-Gruppe erstellt. Sie können die Gruppe „Resource Manager“ auf der [Cluster-Homepage](#homePage) einsehen.

## <a name="find-the-storage-accounts"></a>Suchen der Speicherkonten

HDInsight-Cluster verwenden zum Speichern von Daten entweder ein Azure-Speicherkonto oder Azure Data Lake Storage. Jeder HDInsight-Cluster kann ein Standardspeicherkonto und eine Reihe von verknüpften Speicherkonten enthalten. Wählen Sie zum Auflisten der Speicherkonten auf der [Startseite des Clusters](#homePage) unter **Einstellungen** die Option **Speicherkonten** aus.

## <a name="monitor-jobs"></a>Überwachen von Aufträgen

Weitere Informationen finden Sie unter [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="cluster-size"></a>Clustergröße

Die Kachel **Clustergröße** auf der [Startseite des Clusters](#homePage) zeigt die Anzahl von Kernen, die diesem Cluster zugewiesen sind, sowie die Zuordnung für die Knoten innerhalb des Clusters an.

> [!IMPORTANT]  
> Um die vom HDInsight-Cluster bereitgestellten Dienste zu überwachen, müssen Sie Ambari Web oder die Ambari-REST-API verwenden. Weitere Informationen zur Verwendung von Ambari finden Sie unter [Verwalten von HDInsight-Clustern mit Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="connect-to-a-cluster"></a>Herstellen der Verbindung zu einem Cluster

- [Verwenden von Apache Hive mit HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
- [Verwenden von SSH mit HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einige grundlegende administrative Funktionen kennengelernt. Weitere Informationen erhalten Sie in den folgenden Artikeln:

- [Verwalten von HDInsight mit Azure PowerShell](hdinsight-administer-use-powershell.md)
- [Verwalten von HDInsight mit der Azure-CLI](hdinsight-administer-use-command-line.md)
- [Erstellen von Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Verwenden der Apache Ambari-REST-API](hdinsight-hadoop-manage-ambari-rest-api.md)
- [Verwenden von Apache Hive in HDInsight](hadoop/hdinsight-use-hive.md)
- [Verwenden von Apache Sqoop in HDInsight](hadoop/hdinsight-use-sqoop.md)
- [Verwenden benutzerdefinierter Python-Funktionen mit Apache Hive und Apache Pig in HDInsight](hadoop/python-udf-hdinsight.md)
- [Welche Apache Hadoop-Version wird in Azure HDInsight verwendet?](hdinsight-component-versioning.md)