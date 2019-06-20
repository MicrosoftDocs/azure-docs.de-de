---
title: Verwalten von Apache Hadoop-Clustern in HDInsight mit dem Azure-Portal
description: Hier erhalten Sie Informationen zum Erstellen und Verwalten von HDInsight-Clustern mithilfe des Azure-Portals.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 528d2fd5b26e06478078711405bd5b9ca51feeda
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65860000"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Verwalten von Apache Hadoop-Clustern in HDInsight mit dem Azure-Portal

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Mit dem [Azure-Portal][azure-portal] können Sie [Apache Hadoop](https://hadoop.apache.org/)-Cluster in Azure HDInsight verwalten. Rufen Sie über die Registerkartenauswahl oben Informationen zum Verwalten von Hadoop-Clustern in HDInsight mit anderen Tools ab.

## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Ein vorhandener Apache Hadoop-Cluster in HDInsight.  Weitere Informationen finden Sie unter [Erstellen von Linux-basierten Clustern in HDInsight mithilfe des Azure-Portals](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Erste Schritte
Melden Sie sich bei [https://portal.azure.com](https://portal.azure.com) an.

## <a name="showClusters"></a>Auflisten und Anzeigen von Clustern
Auf der Seite **HDInsight-Cluster** werden Ihre vorhandenen Cluster aufgeführt.  Im Portal:
1. Wählen Sie im Menü links **Alle Dienste** aus.
2. Wählen Sie unter **ANALYSEN** die Option **HDInsight-Cluster** aus.

## <a name="homePage"></a>Cluster-Homepage 
Wählen Sie auf der Seite [**HDInsight-Cluster**](#showClusters) den Namen Ihres Clusters aus.  Dadurch wird die Ansicht **Übersicht** geöffnet, die dem folgenden Screenshot ähnelt:

![Azure-Portal – HDInsight-Cluster-Zusammenfassung](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Obere Menüleiste:**  

| Item| BESCHREIBUNG |
|---|---|
|Move|Verschiebt den Cluster in eine andere Ressourcengruppe oder ein anderes Abonnement.|
|Löschen|Löscht den Cluster. |
|Aktualisieren|Aktualisiert die Ansicht.|

**Menü auf der linken Seite:**  
  - **Obere linke Menüleiste:**

    | Item| BESCHREIBUNG |
    |---|---|
    |Übersicht|Gibt allgemeine Informationen zu Ihrem Cluster an.|
    |Aktivitätsprotokoll|Dient zum Anzeigen und Abfragen von Aktivitätsprotokollen.|
    |Zugriffssteuerung (IAM)|Verwenden Sie Rollenzuweisungen.  Weitere Informationen finden Sie unter [Verwenden von Rollenzuweisungen zum Verwalten Ihrer Azure-Abonnementressourcen](../role-based-access-control/role-assignments-portal.md).|
    |`Tags`|Dient zum Festlegen von Schlüssel-Wert-Paaren, um eine benutzerdefinierte Taxonomie für Ihre Clouddienste zu definieren. Sie können z. B. einen Schlüssel mit dem Namen **Projekt** erstellen und dann einen gemeinsamen Wert für alle mit einem bestimmten Projekt verknüpften Dienste verwenden.|
    |Diagnose und Problembehandlung|Dient zum Anzeigen von Informationen zur Problembehandlung.|
    |Schnellstart|Zeigt hilfreiche Informationen für die ersten Schritte mit HDInsight an.|
    |Tools|Hilfeinformationen für HDInsight-bezogene Tools.|

  - **Menü „Einstellungen“**  

    | Item| BESCHREIBUNG |
    |---|---|
    |Clustergröße|Dient zum Überprüfen, Erhöhen und Verringern der Anzahl von Workerknoten im Cluster. Weitere Informationen finden Sie unter [Skalieren von Clustern](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Kontingentgrenzen|Dient zum Anzeigen der verwendeten und verfügbaren Kernspeicher für Ihr Abonnement.|
    |SSH + Clusteranmeldung|Zeigt die Anweisungen für die Verbindung mit dem Cluster über eine Secure Shell-Verbindung (SSH) an. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Data Lake Storage Gen1|Dient zum Konfigurieren des Data Lake Storage Gen1-Zugriffs.  Weitere Informationen finden Sie unter [Schnellstart: Einrichten von Clustern in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).|
    |Speicherkonten|Dient zum Anzeigen der Speicherkonten und Schlüssel. Die Speicherkonten werden während der Clustererstellung konfiguriert.|
    |ANWENDUNGEN|Dient zum Hinzufügen und Entfernen von HDInsight-Anwendungen.  Weitere Informationen finden Sie unter [Installieren benutzerdefinierter HDInsight-Anwendungen](hdinsight-apps-install-custom-applications.md).|
    |Skriptaktionen|Dienst zum Anwenden von Bashskripts auf den Cluster. Weitere Informationen finden Sie unter [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).|
    |Externe Metastores|Anzeigen der Metastores für [Apache Hive](https://hive.apache.org/) und [Apache Oozie](https://oozie.apache.org/). Die Metastores können nur während der Clustererstellung konfiguriert werden.|
    |HDInsight-Partner|Dient zum Hinzufügen/Entfernen des aktuellen HDInsight-Partners.|
    |Eigenschaften|Dient zum Anzeigen der [Clustereigenschaften](#properties).|
    |Sperren|Dient zum Hinzufügen einer Sperre, um zu verhindern, dass der Cluster geändert oder gelöscht wird.|
    |Automatisierungsskript|Dient zum Anzeigen und Exportieren der Azure Resource Manager-Vorlage für den Cluster. Derzeit können Sie nur das abhängige Azure-Speicherkonto exportieren. Weitere Informationen finden Sie unter [Erstellen Linux-basierter Apache Hadoop-Cluster in HDInsight mithilfe von Azure Resource Manager-Vorlagen](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Menü „Überwachung“**

    | Item| BESCHREIBUNG |
    |---|---|
    |Alerts|Dient zum Verwalten der Warnungen und Aktionen.|
    |Metriken|Überwacht die Clustermetriken in Azure Monitor-Protokollen.|
    |Diagnoseeinstellungen|Einstellungen zum Speicherort der Diagnosemetriken.|
    |Operations Management Suite|Überwacht den Cluster in Azure Operations Management Suite (OMS) und Azure Monitor-Protokollen.|

  - **Menü „Support und Problembehandlung“**

    | Item| BESCHREIBUNG |
    |---|---|
    |Ressourcenintegrität|Weitere Informationen finden Sie unter [Übersicht über Azure Resource Health](../service-health/resource-health-overview.md).|
    |Neue Supportanfrage|Ermöglicht die Erstellung eines Supporttickets für den Microsoft-Support.|

## <a name="properties"></a>Clustereigenschaften

Wählen Sie auf der [Cluster-Homepage](#homePage) unter **Einstellungen** die Option **Eigenschaften** aus.

|Item | BESCHREIBUNG |
|---|---|
|Hostname|Clustername|
|Cluster-URL|Dies ist die URL für die Ambari-Weboberfläche.|
|Secure Shell (SSH)|Der Benutzername und der Hostname für den Zugriff auf den Cluster über SSH.|
|Status|Eine der folgenden Optionen: Aborted, Accepted, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operational, Running, Error, Deleting, Deleted, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization.|
|Region|Azure-Standort. Eine Liste der unterstützten Azure-Standorte finden Sie im Dropdownlistenfeld **Region** unter [HDInsight-Preise](https://azure.microsoft.com/pricing/details/hdinsight/).|
|Erstellungsdatum|Das Datum der Clusterbereitstellung.|
|Betriebssystem|Entweder **Windows** oder **Linux**.|
|Type|Hadoop, HBase, Storm, Spark.|
|Version|Siehe [HDInsight-Versionen](hdinsight-component-versioning.md).|
|Abonnement|Abonnementname.|
|Standarddatenquelle|Das Standardcluster-Dateisystem.|
|Workerknotengröße|Die ausgewählte VM-Größe des Workerknotens.|
|Hauptknotengröße|Die ausgewählte VM-Größe der Hauptknoten.|
|Virtuelles Netzwerk|Der Name des virtuellen Netzwerks, für das der Cluster bereitgestellt wird, wenn eines zum Zeitpunkt der Bereitstellung ausgewählt wurde.|

## <a name="move-clusters"></a>Verschieben von Clustern

Sie können einen HDInsight-Cluster in eine andere Azure-Ressourcengruppe oder ein anderes Abonnement verschieben.

Auf der [Startseite des Clusters](#homePage):

1. Wählen Sie auf der oberen Menüleiste **Verschieben** aus.
2. Wählen Sie **In eine andere Ressourcengruppe verschieben** oder **In ein anderes Abonnement verschieben** aus.
3. Befolgen Sie die Anweisungen auf der neuen Seite.

## <a name="delete-clusters"></a>Löschen von Clustern
Wenn Sie einen Cluster löschen, werden weder das Standardspeicherkonto noch die verknüpften Speicherkonten dadurch gelöscht. Sie können den Cluster mit den gleichen Speicherkonten und den gleichen Metastores neu erstellen. Sie sollten einen neuen Standardblobcontainer verwenden, wenn Sie den Cluster neu erstellen.

Auf der [Startseite des Clusters](#homePage):

1. Klicken Sie auf der oberen Menüleiste auf **Löschen**.
2. Befolgen Sie die Anweisungen auf der neuen Seite.

Siehe auch [Anhalten/Herunterfahren von Clustern](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Hinzufügen weiterer Speicherkonten

Nachdem ein Cluster erstellt wurde, können Sie weitere Azure-Speicherkonten und Azure Data Lake Storage-Konten hinzufügen. Weitere Informationen finden Sie unter [Hinzufügen zusätzlicher Speicherkonten zu HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Skalieren von Clustern
Mithilfe der Clusterskalierung können Sie die Anzahl der von einem Azure HDInsight-Cluster verwendeten Workerknoten ändern, ohne den Cluster neu erstellen zu müssen.

> [!NOTE]  
> Es werden nur Cluster mit HDInsight-Versionen ab 3.1.3 unterstützt. Überprüfen Sie ggf. auf der Seite „Eigenschaften“ die Version Ihres Clusters.  Informationen hierzu finden Sie unter „Auflisten und Anzeigen von Clustern“.

Auf der [Startseite des Clusters](#homePage):

1. Wählen Sie unter **Einstellungen** die Option **Clustergröße** aus.
2. Geben Sie die **Anzahl der Workerknoten** in das numerische Textfeld ein. Die maximale Anzahl von Clusterknoten variiert zwischen Azure-Abonnements. Sie können sich an den Azure-Abrechnungssupport wenden, falls Sie diese Begrenzung erhöhen möchten.  Die Änderungen, die Sie an der Anzahl von Knoten vorgenommen haben, werden in den Kosteninformationen widergespiegelt.
3. Wählen Sie **Speichern** aus.

    ![HDInsight Hadoop HBase Storm Spark skalieren](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster2.png)

Auswirkungen der Änderung der Anzahl von Datenknoten variieren für die von HDInsight unterstützten Clustertypen:

* Apache Hadoop

    Sie können die Anzahl der Workerknoten in einem aktiven Hadoop-Cluster problemlos ohne Auswirkungen auf ausstehende oder aktive Aufträge erhöhen. Neue Aufträge können auch während des Vorgangs gesendet werden. Fehler bei einer Skalierung werden ordnungsgemäß behandelt, sodass der Cluster immer in einem funktionsfähigen Zustand verbleibt.

    Wenn ein Hadoop-Cluster durch Verringern der Anzahl der Datenknoten zentral herunterskaliert wird, werden einige der Dienste im Cluster neu gestartet. Dieses Verhalten führt beim Abschluss des Skalierungsvorgangs bei allen aktiven und ausstehenden Aufträgen zu einem Fehler. Sie können die Aufträge jedoch nach Abschluss des Vorgangs erneut senden.
* Apache HBase

    Sie können Knoten reibungslos Ihrem HBase-Cluster hinzufügen oder aus diesem entfernen, während er aktiv ist. Regionale Server werden innerhalb weniger Minuten nach Abschluss des Skalierungsvorgangs automatisch ausgeglichen. Allerdings können Sie die regionalen Server auch manuell ausgleichen, indem Sie sich am Hauptknoten des Clusters anmelden und in einem Eingabeaufforderungsfenster die folgenden Befehle ausführen:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Weitere Informationen zur Verwendung von HBase-Shell finden Sie unter [Erste Schritte mit einem Apache HBase-Beispiel in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Sie können Datenknoten übergangslos zum Storm-Cluster hinzufügen oder aus diesem entfernen, während er aktiv ist. Nach erfolgreichem Abschluss des Skalierungsvorgangs müssen Sie jedoch die Topologie neu ausgleichen.

    Es stehen zwei Methoden für den erneuten Ausgleich zur Verfügung:

  * Storm-Webbenutzeroberfläche
  * Befehlszeilenschnittstelle (CLI)

    Weitere Informationen finden Sie in der [Apache Storm-Dokumentation](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

    Die Storm-Webbenutzeroberfläche ist für den HDInsight-Cluster verfügbar:

    ![Ausgleichen der HDInsight Storm-Skalierung](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Es folgt ein Beispiel-CLI-Befehl für den Neuausgleich der Storm-Topologie:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```


## <a name="pauseshut-down-clusters"></a>Anhalten/Herunterfahren von Clustern

Die meisten Hadoop-Aufträge sind Batchaufträge, die nur gelegentlich ausgeführt werden. Bei den meisten Hadoop-Clustern wird der Cluster immer wieder für einen längeren Zeitraum nicht zur Verarbeitung verwendet. Mit HDInsight werden Ihre Daten im Azure-Speicher gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird.
Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen.

Es gibt viele Methoden, mit denen Sie den Prozess programmieren können:

* Verwenden Sie Azure Data Factory. Informationen zum Erstellen bedarfsgesteuerter verknüpfter HDInsight-Dienste finden Sie unter [Erstellen von bedarfsgesteuerten Linux-basierten Apache Hadoop-Clustern in HDInsight mit Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md).
* Verwenden Sie Azure PowerShell.  Weitere Informationen hierzu finden Sie unter [Analysieren von Flugverspätungsdaten mit Hive in HDInsight](hdinsight-analyze-flight-delay-data-linux.md).
* Verwenden Sie die Azure-Befehlszeilenschnittstelle. Weitere Informationen finden Sie unter [Verwalten von Azure HDInsight-Clustern mit der Azure-Befehlszeilenschnittstelle](hdinsight-administer-use-command-line.md).
* Verwenden das Sie HDInsight .NET-SDK. Weitere Informationen finden Sie unter [Übermitteln von Apache Hadoop-Aufträgen](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Die Preisinformationen finden Sie unter [HDInsight Preise](https://azure.microsoft.com/pricing/details/hdinsight/). Informationen zum Löschen eines Clusters aus dem Portal finden Sie unter [Löschen von Clustern](#delete-clusters)



## <a name="upgrade-clusters"></a>Aktualisieren von Clustern

Informationen finden Sie unter [Aktualisieren eines HDInsight-Clusters auf eine neuere Version](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Öffnen der Apache Ambari-Webbenutzeroberfläche

Ambari bietet eine intuitive, einfach zu bedienende Webbenutzeroberfläche für die Hadoop-Verwaltung, gesichert durch die RESTful-APIs. Mit Ambari können Systemadministratoren Hadoop-Cluster verwalten und überwachen.

Auf der [Startseite des Clusters](#homePage):

1. Wählen Sie **Clusterdashboards** aus.

    ![Menü für Hadoop-Cluster in HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Wählen Sie auf der neuen Seite die **Ambari-Homepage** aus.
2. Geben Sie den Benutzernamen und das Kennwort für den Cluster ein.  Der Standardbenutzername für den Cluster lautet _admin_. Die Ambari-Webbenutzeroberfläche sieht wie folgt aus:

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

### <a name="change-the-ssh-user-password"></a>Ändern des SSH-Benutzerkennworts
1. Speichern Sie den folgenden Text mit einem Texteditor als Datei mit dem Namen **changepassword.sh**.

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
4. Wählen Sie auf dem Blatt **Skriptaktionen** die Option **Neue übermitteln** aus. 
5. Geben Sie auf dem Blatt **Skriptaktion übermitteln** die folgenden Informationen ein:

   | Feld | Wert |
   | --- | --- |
   | Skripttyp | Wählen Sie in der Dropdownliste **– Benutzerdefiniert** aus.|
   | NAME |„Change ssh password“ (SSH-Kennwort ändern) |
   | Bash-Skript-URI |Der URI zur Datei „changepassword.sh“ |
   | Knotentyp(en): (Hauptknoten, Worker, Nimbus, Supervisor, Zookeeper usw.) |✓ für alle aufgeführten Knotentypen |
   | Parameter |Geben Sie den SSH-Benutzernamen und dann das neue Kennwort ein. Es muss ein Leerzeichen zwischen den Benutzernamen und das Kennwort eingefügt werden. |
   | Speichern Sie diese Skriptaktion ... |Lassen Sie dieses Feld deaktiviert. |

6. Wählen Sie **Erstellen** aus, um das Skript anzuwenden. Nachdem das Skript ausgeführt wurde, können Sie per SSH mit dem neuen Kennwort eine Verbindung mit dem Cluster herstellen.

## <a name="grantrevoke-access"></a>Gewähren/Entziehen des Zugriffs
In HDInsight-Clustern stehen die folgenden HTTP-Webdienste zur Verfügung (alle mit RESTful-Endpunkten):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Der Zugriff auf diese Dienste wird standardmäßig gewährt. Sie können den Zugriff mit [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access) widerrufen/gewähren.

## <a name="find-the-subscription-id"></a>Ermitteln der Abonnement-ID
Jeder Cluster ist mit einem Azure-Abonnement verknüpft.  Sie können die Azure-Abonnement-ID auf der [Cluster-Homepage](#homePage) einsehen.

## <a name="find-the-resource-group"></a>Suchen der Ressourcengruppe
Im Azure Resource Manager-Modus wird jeder HDInsight-Cluster mit einer Azure Resource Manager-Gruppe erstellt. Sie können die Gruppe „Resource Manager“ auf der [Cluster-Homepage](#homePage) einsehen.

## <a name="find-the-storage-accounts"></a>Suchen der Speicherkonten
HDInsight-Cluster verwenden zum Speichern von Daten entweder ein Azure-Speicherkonto oder Azure Data Lake Storage. Jeder HDInsight-Cluster kann ein Standardspeicherkonto und eine Reihe von verknüpften Speicherkonten enthalten. Wählen Sie zum Auflisten der Speicherkonten auf der [Startseite des Clusters](#homePage) unter **Einstellungen** die Option **Speicherkonten** aus.


## <a name="monitor-jobs"></a>Überwachen von Aufträgen
Weitere Informationen finden Sie unter [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md#monitoring).


## <a name="monitor-cluster-usage"></a>Überwachen der Clusternutzung
Der Abschnitt **Nutzung** auf dem Blatt für den HDInsight-Cluster enthält Informationen zur Anzahl von Kernen, die für Ihr Abonnement zur Verwendung mit HDInsight verfügbar sind. Außerdem ist hier die Anzahl von Kernen angegeben, die diesem Cluster zugeordnet sind. Weiterhin wird angezeigt, wie die Kerne für die Knoten innerhalb dieses Clusters zugeordnet sind. Informationen hierzu finden Sie unter „Auflisten und Anzeigen von Clustern“.

> [!IMPORTANT]  
> Um die vom HDInsight-Cluster bereitgestellten Dienste zu überwachen, müssen Sie Ambari Web oder die Ambari-REST-API verwenden. Weitere Informationen zur Verwendung von Ambari finden Sie unter [Verwalten von HDInsight-Clustern mit Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="connect-to-a-cluster"></a>Herstellen der Verbindung zu einem Cluster

* [Verwenden von Apache Hive mit HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Verwenden von SSH mit HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einige grundlegende administrative Funktionen kennengelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Verwalten von HDInsight mit Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Verwalten von HDInsight mit der Azure-CLI](hdinsight-administer-use-command-line.md)
* [Erstellen von Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md)
* [Verwenden der Apache Ambari-REST-API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Verwenden von Apache Hive in HDInsight](hadoop/hdinsight-use-hive.md)
* [Verwenden von Apache Pig in HDInsight](hadoop/hdinsight-use-pig.md)
* [Verwenden von Apache Sqoop in HDInsight](hadoop/hdinsight-use-sqoop.md)
* [Erste Schritte mit Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Welche Apache Hadoop-Version wird in Azure HDInsight verwendet?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Hadoop-Befehlszeile"
