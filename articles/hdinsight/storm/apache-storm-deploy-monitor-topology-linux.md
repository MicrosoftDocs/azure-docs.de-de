---
title: Bereitstellen und Verwalten von Apache Storm-Topologien in Azure HDInsight
description: Erfahren Sie, wie Sie Apache Storm-Topologien mithilfe des Storm-Dashboards in Linux-basiertem HDInsight bereitstellen, überwachen und verwalten. Hadoop-Tools für Visual Studio verwenden
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: c8b05886ae338bb73b0f4ddce4d02a1f1a926a45
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078244"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Bereitstellen und Verwalten von Apache Storm-Topologien in Azure HDInsight 

Dieses Dokument enthält die Grundlagen zur Verwaltung und Überwachung von [Apache Storm](https://storm.apache.org/)-Topologien, die unter Storm in HDInsight-Clustern ausgeführt werden.

## <a name="prerequisites"></a>Voraussetzungen

* **Storm unter Linux in einem HDInsight-Cluster**: Informationen zum Erstellen eines Clusters finden Sie unter [Erste Schritte mit Apache Storm in HDInsight](apache-storm-tutorial-get-started-linux.md) .

* (Optional) **Erfahrung mit SSH und SCP**: Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Optional) **Visual Studio**: Azure SDK 2.5.1 oder höher und die Data Lake-Tools für Visual Studio. Weitere Informationen finden Sie unter [Erste Schritte mit den Data Lake-Tools für Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    Eine der folgenden Versionen von Visual Studio:

  * Visual Studio 2012 mit Update 4

  * Visual Studio 2013 mit Update 4 oder [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (beliebige Edition)

  * Visual Studio 2017 (beliebige Edition). Data Lake-Tools für Visual Studio-2017 werden als Teil der Azure-Arbeitslast installiert.

## <a name="submit-a-topology-visual-studio"></a>Senden einer Topologie: Visual Studio

Mithilfe der HDInsight-Tools können C#- oder hybride Topologien an Ihre Storm-Cluster übermittelt werden. Für die folgenden Schritte wird eine Beispielanwendung verwendet. Informationen über das Erstellen mithilfe der HDInsight-Tools finden Sie unter [Entwickeln von C#-Topologien mithilfe der HDInsight-Tools für Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

1. Wenn Sie die neueste Version der Data Lake-Tools für Visual Studio noch nicht installiert haben, finden Sie Informationen dazu unter [Erste Schritte mit den Data Lake-Tools für Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Die Data Lake-Tools für Visual Studio wurden zuvor als HDInsight-Tools für Visual Studio bezeichnet.
    >
    > Data Lake-Tools für Visual Studio sind in der __Azure-Arbeitslast__ für Visual Studio 2017 enthalten.

2. Öffnen Sie Visual Studio, und wählen Sie **Datei** > **Neu** > **Projekt** aus.

3. Erweitern Sie im Dialogfeld **Neues Projekt** die Optionen **Installiert** > **Vorlagen**, und wählen Sie dann **HDInsight** aus. Wählen Sie in der Liste der Vorlagen die Option **Storm Sample**aus. Geben Sie unten im Dialogfeld einen Namen für die Anwendung ein.

    ![image](./media/apache-storm-deploy-monitor-topology-linux/sample.png)

4. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **An Storm in HDInsight übermitteln** aus.

   > [!NOTE]  
   > Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen für Ihr Azure-Abonnement ein. Wenn Sie über mehrere Abonnements verfügen, melden Sie sich bei dem Abonnement an, das Ihren Storm-Cluster in HDInsight enthält.

5. Wählen Sie in der Dropdownliste **Storm Cluster** Ihren Storm-Cluster in HDInsight und dann die Option **Übermitteln** aus. Sie können über das Fenster **Ausgabe** überwachen, ob die Übermittlung erfolgreich ausgeführt wurde.

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Senden einer Topologie: SSH und der Storm-Befehl

1. Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her. Ersetzen Sie **USERNAME** durch den Namen der SSH-Anmeldung. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres HDInsight-Clusters:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Weitere Informationen zur Verwendung von SSH für das Herstellen von Verbindungen mit dem HDInsight-Cluster finden Sie unter [Verwenden von SSH mit HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Verwenden Sie zum Starten einer Beispieltopologie den folgenden Befehl.

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Dieser Befehl startet die WordCount-Beispieltopologie auf dem Cluster. Diese Topologie generiert nach dem Zufallsprinzip Sätze und zählt dann die Instanzen jedes Worts in den Sätzen.

   > [!NOTE]  
   > Wenn die Topologie an den Cluster gesendet wird, müssen Sie zuerst die JAR-Datei mit dem Cluster kopieren, bevor Sie den Befehl `storm` verwenden. Um die Datei auf den Cluster zu kopieren, können Sie den `scp`-Befehl verwenden. Zum Beispiel, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > Das Beispiel "WordCount" und andere Storm-Starter-Beispiele sind unter `/usr/hdp/current/storm-client/contrib/storm-starter/`bereits auf dem Cluster enthalten.

## <a name="submit-a-topology-programmatically"></a>Senden einer Topologie: programmgesteuert

Sie können mit dem Nimbus-Dienst programmgesteuert eine Topologie bereitstellen. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) stellt eine Java-Beispielanwendung bereit, die zeigt, wie Sie eine Topologie über den Nimbus-Dienst bereitstellen und starten.

## <a name="monitor-and-manage-visual-studio"></a>Überwachen und Verwalten: Visual Studio

Wenn eine Topologie mithilfe von Visual Studio übermittelt wurde, wird die Ansicht **Storm-Topologien** angezeigt. Wählen Sie die Topologie aus der Liste aus, um Informationen zur aktiven Topologie anzuzeigen.

![Visual Studio Monitor](./media/apache-storm-deploy-monitor-topology-linux/vsmonitor.png)

> [!NOTE]  
> Sie können die **Storm-Topologien** auch im **Server-Explorer** anzeigen, indem Sie **Azure** > **HDInsight** erweitern und dann mit der rechten Maustaste auf einen Storm-Cluster in HDInsight klicken und **View Storm Topologies** auswählen.

Wählen Sie die Form für die Spouts oder Bolts aus, um Informationen über diese Komponenten anzuzeigen. Für jedes ausgewählte Element wird ein neues Fenster geöffnet.

### <a name="deactivate-and-reactivate"></a>Deaktivieren und erneutes Aktivieren

Durch das Deaktivieren wird eine Topologie unterbrochen, bis sie beendet oder erneut aktiviert wird. Um diese Vorgänge auszuführen, verwenden Sie die Schaltflächen __Deaktivieren__ und __Erneut aktivieren__  oben in der __Topologiezusammenfassung__.

### <a name="rebalance"></a>Ausgleichen

Durch das Ausgleichen einer Topologie kann das System die Parallelität der Topologie überarbeiten. Wenn Sie z.B. die Größe des Clusters geändert haben, um zusätzliche Notizen hinzuzufügen, ermöglicht ein Ausgleich einer Topologie, die neuen Knoten zu erkennen.

Verwenden Sie für den Ausgleich einer Topologie die Schaltfläche __Neu ausgleichen__ oben in der __Topologiezusammenfassung__.

> [!WARNING]  
> Beim Ausgleichen einer Topologie wird diese zunächst deaktiviert. Anschließend werden die Worker gleichmäßig auf den Cluster verteilt, und zum Schluss wird die Topologie wieder in den Zustand vor dem Ausgleich zurückgesetzt. Wenn die Topologie also z.B. aktiv war, wird sie wieder aktiviert. Wenn sie deaktiviert war, bleibt sie deaktiviert.

### <a name="kill-a-topology"></a>Beenden einer Topologie

Storm-Topologien werden weiterhin ausgeführt, bis sie beendet werden oder der Cluster gelöscht wird. Verwenden Sie zum Beenden einer Topologie die Schaltfläche __Beenden__ oben in der __Topologiezusammenfassung__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Überwachen und Verwalten: SSH und der Storm-Befehl

Mit dem Dienstprogramm `storm` können Sie an der Befehlszeile mit ausgeführten Topologien arbeiten. Eine vollständige Liste der Befehle erhalten Sie mit `storm -h` .

### <a name="list-topologies"></a>Auflisten der Topologien

Mit dem folgenden Befehl können Sie alle ausgeführten Topologien auflisten:

    storm list

Die Ausgabe dieses Befehls sieht in etwa wie folgt aus:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Deaktivieren und erneutes Aktivieren

Durch das Deaktivieren wird eine Topologie unterbrochen, bis sie beendet oder erneut aktiviert wird. Verwenden Sie zum Deaktivieren und erneuten Aktivieren den folgenden Befehl:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Beenden einer ausgeführten Topologie

Storm-Topologien werden, nachdem sie einmal gestartet wurden, so lange ausgeführt, bis sie beendet werden. Führen Sie zum Beenden einer Topologie den folgenden Befehl aus:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Ausgleichen

Durch das Ausgleichen einer Topologie kann das System die Parallelität der Topologie überarbeiten. Wenn Sie z.B. die Größe des Clusters geändert haben, um zusätzliche Notizen hinzuzufügen, ermöglicht ein Ausgleich einer Topologie, die neuen Knoten zu erkennen.

> [!WARNING]  
> Beim Ausgleichen einer Topologie wird diese zunächst deaktiviert. Anschließend werden die Worker gleichmäßig auf den Cluster verteilt, und zum Schluss wird die Topologie wieder in den Zustand vor dem Ausgleich zurückgesetzt. Wenn die Topologie also z.B. aktiv war, wird sie wieder aktiviert. Wenn sie deaktiviert war, bleibt sie deaktiviert.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Überwachen und Verwalten: Storm-Benutzeroberfläche

Die Storm-Benutzeroberfläche bietet eine Weboberfläche zum Arbeiten mit ausgeführten Topologien und befindet sich auf dem HDInsight-Cluster. Wenn Sie die Storm-Benutzeroberfläche anzeigen möchten, öffnen Sie **https://CLUSTERNAME.azurehdinsight.net/stormui** in einem Browser, wobei **CLUSTERNAME** der Name Ihres Clusters ist.

> [!NOTE]  
> Wenn Sie dazu aufgefordert werden, einen Benutzernamen und ein Kennwort anzugeben, geben Sie den Namen des Cluster-Administrators (Admin) und das entsprechende Kennwort ein, die Sie beim Erstellen des Clusters verwendet haben.

### <a name="main-page"></a>Hauptseite

Die Hauptseite der Storm-Benutzeroberfläche bietet die folgenden Informationen:

* **Clusterzusammenfassung**: Grundlegende Informationen zum Storm-Cluster.
* **Topologiezusammenfassung**: Eine Liste der aktiven Topologien. Verwenden Sie die Links in diesem Abschnitt, um weitere Informationen zu bestimmten Topologien anzuzeigen.
* **Supervisor-Zusammenfassung**: Informationen zum Storm-Supervisor.
* **Nimbus-Konfiguration**: Die Nimbus-Konfiguration für den Cluster.

### <a name="topology-summary"></a>Topologiezusammenfassung:

Wenn Sie einen Link aus dem Abschnitt **Topologiezusammenfassung** auswählen, werden die folgenden Informationen zur Topologie angezeigt:

* **Topologiezusammenfassung**: Grundlegende Informationen zur Topologie.
* **Topologieaktionen**: Verwaltungsaktionen, die für die Topologie ausgeführt werden können.

  * **Aktivieren**: Setzt die Verarbeitung einer deaktivierten Topologie fort.
  * **Deaktivieren**: Hält eine aktive Topologie an.
  * **Ausgleichen**: Passt die Parallelität der Topologie an. Sie sollten aktive Topologien ausgleichen, nachdem Sie die Anzahl der Knoten im Cluster geändert haben. Dieser Vorgang ermöglicht der Topologie, die Parallelität anzupassen, um die höhere oder geringere Anzahl der Knoten im Cluster zu kompensieren.

    Weitere Informationen finden Sie unter <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Grundlegendes zur Parallelität einer Apache Storm-Topologie</a>.
  * **Beenden**: Beendet eine Storm-Topologie nach dem angegebenen Zeitlimit.
* **Topologiestatistik**: Statistiken zur Topologie. Verwenden Sie die Links in der Spalte **Fenster**, um den Zeitrahmen für die verbleibenden Einträge auf der Seite festzulegen.
* **Spouts**: Die von der Topologie verwendeten Spouts. Verwenden Sie die Links in diesem Abschnitt, um weitere Informationen zu bestimmten Spouts anzuzeigen.
* **Bolts**: Die von der Topologie verwendeten Bolts. Verwenden Sie die Links in diesem Abschnitt, um weitere Informationen zu bestimmten Bolts anzuzeigen.
* **Topologiekonfiguration**: Die Konfiguration der ausgewählten Topologie.

### <a name="spout-and-bolt-summary"></a>Spout und Bolt: Zusammenfassung

Wenn Sie im Abschnitt **Spouts** oder **Bolts** einen Spout auswählen, werden die folgenden Informationen zum ausgewählten Element angezeigt:

* **Komponentenzusammenfassung**: Grundlegende Informationen zum Spout oder Bolt.
* **Statistik für Spout/Bolt**: Statistiken zum Spout oder Bolt. Verwenden Sie die Links in der Spalte **Fenster**, um den Zeitrahmen für die verbleibenden Einträge auf der Seite festzulegen.
* **Eingabestatistik** (nur Bolt): Informationen zu den Eingabedatenströmen, die vom Bolt verbraucht werden.
* **Ausgabestatistik**: Informationen zu den Datenströmen, die vom Spout oder Bolt ausgegeben werden.
* **Ausführer**: Informationen zu den Instanzen von Spout oder Bolt. Wählen Sie den Eintrag **Port** für einen bestimmten Ausführer aus, um ein Protokoll mit Diagnoseinformationen anzuzeigen, das für diese Instanz generiert wurde.
* **Fehler:** Fehlerinformationen für diesen Spout oder Bolt.

## <a name="monitor-and-manage-rest-api"></a>Überwachen und Verwalten: REST-API

Die Storm-Benutzeroberfläche baut auf der REST-API auf, sodass Sie mithilfe der REST-API ähnliche Verwaltungs- und Überwachungsfunktionen ausführen können. Mithilfe der REST-API können Sie benutzerdefinierte Tools zum Verwalten und Überwachen von Storm-Topologien erstellen.

Weitere Informationen finden Sie unter [REST-API der Apache Storm-Benutzeroberfläche](https://storm.apache.org/releases/current/STORM-UI-REST-API.html)(in englischer Sprache). Die folgenden Informationen gelten für die Verwendung der REST-API mit Apache Storm in HDInsight.

> [!IMPORTANT]  
> Die Storm-REST-API ist nicht öffentlich über das Internet verfügbar. Der Zugriff muss über einen SSH-Tunnel zum Hauptknoten des HDInsight-Clusters erfolgen. Weitere Informationen zum Erstellen und Verwenden eines SSH-Tunnels finden Sie unter [Verwenden von SSH-Tunneling zum Zugriff auf die Apache Ambari-Webbenutzeroberfläche, ResourceManager, JobHistory, NameNode, Apache Oozie und andere Webbenutzeroberflächen](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Basis-URI

Der Basis-URI für die REST-API auf Linux-basierten HDInsight-Clustern ist auf dem Hauptknoten unter **https:\//HEADNODEFQDN:8744/api/v1/** verfügbar. Der Domänenname des Hauptknotens wird während der Clustererstellung generiert und ist nicht statisch.

Sie können den vollqualifizierten Domänennamen (FQDN) für den Clusterhauptknoten auf unterschiedliche Arten ermitteln:

* **Über eine SSH-Sitzung**: Verwenden Sie den Befehl `headnode -f` für eine SSH-Sitzung mit dem Cluster.
* **Über Ambari Web**: Wählen Sie oben auf der Seite **Dienste** und dann **Storm**. Wählen Sie auf der Registerkarte **Zusammenfassung** die Option **Storm UI-Server**. Der vollqualifizierte Domänenname des Hosts, auf dem die Storm UI und die REST-API ausgeführt werden, wird oben auf der Seite angezeigt.
* **Über die Ambari-REST-API**: Verwenden Sie den Befehl `curl -u admin -G "https:\//CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"`, um Informationen zu dem Knoten abzurufen, auf dem die Storm UI und die REST-API ausgeführt werden. Ersetzen Sie **CLUSTERNAME** durch den Namen des Clusters. Geben Sie bei entsprechender Aufforderung das Kennwort des Anmeldekontos (Administrator) ein. In der Antwort enthält der Eintrag „host_name“ den vollqualifizierten Domänennamen des Knotens.

### <a name="authentication"></a>Authentication

Anforderungen an die REST-API müssen die **Standardauthentifizierung**und somit den Benutzernamen und das Kennwort des HDInsight-Clusteradministrators verwenden.

> [!NOTE]  
> Da die Standardauthentifizierung unverschlüsselt gesendet wird, sollten Sie **immer** HTTPS verwenden, um die Kommunikation mit dem Cluster zu schützen.

### <a name="return-values"></a>Rückgabewerte

Informationen, die von der REST-API zurückgegeben werden, sind möglicherweise nur innerhalb des Clusters verwendbar. Auf den für [Apache ZooKeeper](https://zookeeper.apache.org/)-Server zurückgegebenen vollqualifizierten Domänennamen (FQDN) kann zum Beispiel nicht über das Internet zugegriffen werden.

## <a name="next-steps"></a>Nächste Schritte

Lernen Sie mehr über das [Entwickeln Java-basierter Topologien mit Apache Maven](apache-storm-develop-java-topology.md).

Weitere Beispieltopologien finden Sie unter [Beispieltopologien für Apache Storm in HDInsight](apache-storm-example-topology.md).
