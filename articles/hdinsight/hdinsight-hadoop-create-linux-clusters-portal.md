---
title: Erstellen von Apache Hadoop-Clustern mit einem Webbrowser – Azure HDInsight
description: Erfahren Sie, wie Sie Apache Hadoop-, Apache HBase-, Apache Storm- und Apache Spark-Cluster in HDInsight erstellen. Verwenden Sie einen Webbrowser und das Azure-Portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 08/06/2020
ms.openlocfilehash: ebfd5c456d3658c6339e5174c9c4ab33f6c52d4d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92541705"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Erstellen von Linux-basierten Clustern in HDInsight mit dem Azure-Portal

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Das Azure-Portal ist ein webbasiertes Verwaltungstool für Dienste und Ressourcen, die in der Microsoft Azure-Cloud gehostet werden. In diesem Artikel wird beschrieben, wie Sie mit dem Portal Linux-basierte Azure HDInsight-Cluster erstellen. Weitere Informationen finden Sie unter [Einrichten von Clustern in HDInsight mit Apache Hadoop, Apache Spark, Apache Kafka usw](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Das Azure-Portal macht die meisten Clustereigenschaften verfügbar. Mithilfe von Azure Resource Manager-Vorlagen können Sie viele Details ausblenden. Weitere Informationen finden Sie unter [Erstellen von Apache Hadoop-Clustern in HDInsight mit Resource Manager-Vorlagen](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="create-clusters"></a>Erstellen von Clustern

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Klicken Sie im oberen Menü auf **+ Ressource erstellen** .

    ![Erstellen eines neuen Clusters im Azure-Portal](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Erstellen eines neuen Clusters im Azure-Portal")

1. Wählen Sie **Analytics** > **Azure HDInsight** aus, um die Seite **HDInsight-Cluster erstellen** zu öffnen.

## <a name="basics"></a>Grundlagen

![HDInsight: Erstellen von Clustergrundlagen](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Erstellen eines neuen Clusters im Azure-Portal")

Geben Sie auf der Registerkarte **Grundlagen** die folgenden Informationen an:

|Eigenschaft |BESCHREIBUNG |
|---|---|
|Subscription|Wählen Sie in der Dropdownliste das Azure-Abonnement aus, das für den Cluster verwendet wird.|
|Resource group|Wählen Sie in der Dropdownliste Ihre vorhandene Ressourcengruppe oder die Option **Neu erstellen** aus.|
|Clustername|Geben Sie einen global eindeutigen Namen ein.|
|Region|Wählen Sie in der Dropdownliste eine Region für die Erstellung des Clusters aus.|
|Clustertyp|Klicken Sie auf **Clustertyp auswählen** , um eine Liste zu öffnen. Wählen Sie in der Liste den gewünschten Clustertyp aus. Es gibt unterschiedliche Typen von HDInsight-Clustern. Diese entsprechen der Workload oder Technologie, für die der Cluster optimiert wurde. Es gibt keine unterstützte Methode zum Erstellen eines Clusters, in dem mehrere Typen kombiniert sind.|
|Version|Wählen Sie in der Dropdownliste eine **Version** aus. Verwenden Sie die Standardversion, wenn Sie nicht wissen, was Sie auswählen sollen. Weitere Informationen finden Sie unter [HDInsight-Clusterversionen](hdinsight-component-versioning.md).|
|Benutzername für Clusteranmeldung|Geben Sie den Benutzernamen an, der Standardwert ist **admin** .|
|Kennwort für Clusteranmeldung|Geben Sie das Kennwort an.|
|Bestätigen Sie das Clusteranmeldekennwort.|Geben Sie das Kennwort erneut ein.|
|SSH-Benutzername (Secure Shell)|Geben Sie den Benutzernamen an, der Standardwert ist **sshuser** .|
|Verwenden Sie ein Clusteranmeldekennwort für SSH|Aktivieren Sie das Kontrollkästchen **Clusteranmeldekennwort für SSH verwenden** , wenn Sie möchten, dass das SSH-Kennwort mit dem zuvor angegebenen Administratorkennwort identisch ist. Falls nicht, müssen Sie entweder ein **KENNWORT** oder einen **ÖFFENTLICHEN SCHLÜSSEL** zum Authentifizieren des SSH-Benutzers eingeben. Ein öffentlicher Schlüssel ist der von uns empfohlene Ansatz. Wählen Sie unten die Option **Auswählen** , um die Konfiguration der Anmeldeinformationen zu speichern.  Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|

Klicken Sie auf **Weiter: Speicher >>** , um zur nächsten Registerkarte zu gelangen.

## <a name="storage"></a>Storage

> [!WARNING] 
> Ab dem 15. Juni 2020 können Kunden keinen neuen Dienstprinzipal mit HDInsight mehr erstellen. Informationen zur Verwendung von Azure Active Directory finden Sie unter [Erstellen von Dienstprinzipalen und Zertifikaten](../active-directory/develop/howto-create-service-principal-portal.md).

![HDInsight: Erstellen von Clusterspeicher](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Erstellen eines neuen Clusters im Azure-Portal – Speicher")

### <a name="primary-storage"></a>Primärer Speicher

Wählen Sie in der Dropdownliste für **Primärer Speichertyp** Ihren Standardspeichertyp aus. Die später auszufüllenden Felder fallen je nach Ihrer Auswahl unterschiedlich aus. Für **Azure Storage** :

1. Wählen Sie für **Auswahlmethode** entweder **Aus Liste auswählen** oder **Zugriffsschlüssel verwenden** aus.
    * Wählen Sie **Aus Liste auswählen** und dann Ihr **Primäres Speicherkonto** in der Dropdownliste aus, oder wählen Sie **Neu erstellen** aus.
    * Geben Sie für **Zugriffsschlüssel verwenden** Ihren **Speicherkontonamen** ein. Geben Sie dann den **Zugriffsschlüssel** an.

1. Übernehmen Sie für **Container** den Standardwert, oder geben Sie einen neuen ein.

### <a name="additional-azure-storage"></a>Zusätzliche Azure Storage-Instanzen

Optional: Wählen Sie **Azure Storage hinzufügen** für zusätzlichen Clusterspeicher aus. Die Verwendung eines zusätzlichen Speicherkontos in einer anderen Region als im HDInsight-Cluster wird nicht unterstützt.

### <a name="metastore-settings"></a>Metastore-Einstellungen

Optional: Geben Sie eine vorhandene SQL-Datenbank-Instanz an, um Apache Hive-, Apache Oozie- oder Apache Ambari-Metadaten außerhalb des Clusters zu speichern. Die für den Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten, z. B. Azure HDInsight, konfiguriert sein. Verwenden Sie beim Erstellen eines Metastore für die Namen einer Datenbank keine Binde- oder Gedankenstriche. Diese Zeichen können dazu führen, dass der Prozess der Clustererstellung fehlschlägt.

> [!IMPORTANT]
> Der Standardmetastore stellt für Clusterformen, die Metastores unterstützen, eine Azure SQL-Datenbank-Instanz mit einem **Basic-Tarif mit einer Begrenzung auf fünf DTUs (nicht aktualisierbar)** bereit. Er eignet sich für grundlegende Testzwecke. Bei großen Workloads bzw. Produktionsworkloads empfiehlt sich die Migration zu einem externen Metastore.

Klicken Sie auf **Weiter: Sicherheit + Netzwerkbetrieb >>** , um zur nächsten Registerkarte zu gelangen.

## <a name="security--networking"></a>Sicherheit + Netzwerkbetrieb

![HDinsight-Clustererstellung: Sicherheit + Netzwerkbetrieb](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDinsight-Clustererstellung: Sicherheit + Netzwerkbetrieb")

Geben Sie auf der Registerkarte **Sicherheit + Netzwerkbetrieb** die folgenden Informationen an:

|Eigenschaft |BESCHREIBUNG |
|---|---|
|Sicherheitspaket für Unternehmen|Optional: Aktivieren Sie das Kontrollkästchen, um das **Enterprise-Sicherheitspaket** zu verwenden. Weitere Informationen finden Sie unter [Konfigurieren eines HDInsight-Clusters mit Enterprise-Sicherheitspaket (Enterprise Security Package, ESP) mithilfe von Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).|
|TLS|Optional: Wählen Sie in der Dropdownliste eine TLS-Version aus. Weitere Informationen finden Sie unter [Transport Layer Security](./transport-layer-security.md).|
|Virtuelles Netzwerk|Optional: Wählen Sie in der Dropdownliste ein vorhandenes virtuelles Netzwerk und Subnetz aus. Weitere Informationen finden Sie unter [Planen eines virtuellen Netzwerks für Azure HDInsight](hdinsight-plan-virtual-network-deployment.md). Der Artikel enthält die spezifischen Konfigurationsanforderungen für das virtuelle Netzwerk.|
|Datenträgerverschlüsselungseinstellungen|Optional: Aktivieren Sie das Kontrollkästchen, um die Verschlüsselung zu verwenden. Weitere Informationen finden Sie unter [Datenträgerverschlüsselung mit kundenseitig verwalteten Schlüsseln](./disk-encryption.md).|
|Kafka-REST-Proxy|Diese Einstellung ist nur für den Clustertyp Kafka verfügbar. Weitere Informationen finden Sie unter [Interagieren mit Apache Kafka-Clustern in Azure HDInsight mithilfe eines REST-Proxys](./kafka/rest-proxy.md).|
|Identity|Optional: Wählen Sie in der Dropdownliste eine vorhandene, vom Benutzer zugewiesene Dienstidentität aus. Weitere Informationen finden Sie unter [Verwaltete Identitäten in Azure HDInsight](./hdinsight-managed-identities.md).|

Klicken Sie auf **Weiter: Konfiguration + Preise >>** , um zur nächsten Registerkarte zu gelangen.

## <a name="configuration--pricing"></a>Konfiguration + Preise

![Erstellen der HDInsight-Clusterkonfiguration](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "Registerkarte „Konfiguration und Preise“")

Geben Sie auf der Registerkarte **Konfiguration + Preise** die folgenden Informationen an:

|Eigenschaft |BESCHREIBUNG |
|---|---|
|+ Anwendung hinzufügen|Optional: Wählen Sie beliebige Anwendungen aus. Diese Anwendungen können von Microsoft, unabhängigen Softwareherstellern (Independent Software Vendors, ISVs) oder Ihnen selbst entwickelt werden. Weitere Informationen finden Sie unter [Installieren von Anwendungen während der Clustererstellung](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).|
|Knotengröße|Optional: Wählen Sie einen Knoten anderer Größe aus.|
|Anzahl von Knoten|Optional: Geben Sie die Anzahl der Knoten für den angegebenen Knotentyp ein. Wenn Sie mehr als 32 Workerknoten planen, sollten Sie eine Hauptknotengröße mit mindestens 8 Kernen und 14 GB Arbeitsspeicher (RAM) auswählen. Planen Sie die Knoten entweder während der Clustererstellung oder durch das Skalieren des Clusters nach der Erstellung.|
|Aktivieren der automatischen Skalierung|Optional: Aktivieren Sie das Kontrollkästchen des Features. Weitere Informationen finden Sie unter [Automatische Skalierung von Azure HDInsight-Clustern](./hdinsight-autoscale-clusters.md).|
|+ Skriptaktion hinzufügen|Optional: Diese Option ist geeignet, wenn Sie einen Cluster bei seiner Erstellung mit einem benutzerdefinierten Skript anpassen möchten. Weitere Informationen zu Skriptaktionen finden Sie unter [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).|

Wählen Sie **Bewerten + erstellen >>** aus, um die Clusterkonfiguration zu überprüfen und zur letzten Registerkarte zu wechseln.

## <a name="review--create"></a>Bewerten + erstellen

![HDInsight: Zusammenfassung der Clustererstellung](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "Angeben der Anzahl der Clusterknoten")

Überprüfen Sie die Einstellungen. Wählen Sie **Erstellen** , um den Cluster zu erstellen.

Die Erstellung des Clusters dauert in der Regel ca. 20 Minuten. Unter **Benachrichtigungen** können Sie den Bereitstellungsprozess überprüfen.

## <a name="post-creation"></a>Nach der Erstellung

Wählen Sie nach Abschluss des Erstellungsprozesses in der Benachrichtigung **Bereitstellung erfolgreich** die Option **Zu Ressource wechseln** . Im Clusterfenster werden die folgenden Informationen angezeigt.

![HDI Azure-Portal: Clusterübersicht](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Clustereigenschaften")

Einige der Symbole im Fenster stehen für Folgendes:

|Eigenschaft | BESCHREIBUNG |
|---|---|
|Übersicht|Enthält alle grundlegenden Informationen zum Cluster. Beispiele hierfür sind der Name, die zugehörige Ressourcengruppe, der Standort, das Betriebssystem und die URL für das Cluster-Dashboard.|
|Cluster-Dashboards|Führt Sie zu dem mit dem Cluster verbundenen Ambari-Portal.|
|SSH + Clusteranmeldung|Enthält die Informationen zum Zugreifen auf den Cluster per SSH.|
|Löschen|Löscht den HDInsight-Cluster.|

## <a name="delete-the-cluster"></a>Löschen des Clusters

Weitere Informationen finden Sie unter [Löschen eines HDInsight-Clusters mithilfe von Browser, PowerShell oder Azure-Befehlszeilenschnittstelle](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Problembehandlung

Falls beim Erstellen von HDInsight-Clustern Probleme auftreten, sehen Sie sich die [Voraussetzungen für die Zugriffssteuerung](./hdinsight-hadoop-customize-cluster-linux.md#access-control) an.

## <a name="next-steps"></a>Nächste Schritte

Sie haben die Erstellung eines HDInsight-Clusters erfolgreich abgeschlossen. Als Nächstes wird beschrieben, wie Sie mit Ihrem Cluster arbeiten.

* [Verwenden von Apache Hive mit HDInsight](hadoop/hdinsight-use-hive.md)
* [Erste Schritte mit Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md)