---
title: Bereitstellen und Verwalten von Apache Storm-Topologien in Azure HDInsight
description: Erfahren Sie, wie Sie Apache Storm-Topologien im Storm-Dashboard in Linux-basiertem HDInsight bereitstellen, überwachen und verwalten. Hadoop-Tools für Visual Studio verwenden
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 9b190b5847c7412344e2bb09fd4000985816219b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995589"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Bereitstellen und Verwalten von Apache Storm-Topologien in Azure HDInsight

Dieses Dokument enthält die Grundlagen zur Verwaltung und Überwachung von [Apache Storm](https://storm.apache.org/)-Topologien, die unter Storm in HDInsight-Clustern ausgeführt werden.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Apache Storm-Cluster in HDInsight. Siehe [Erstellen von Apache Hadoop-Clustern im Azure-Portal](../hdinsight-hadoop-create-linux-clusters-portal.md), und wählen Sie **Storm** für **Clustertyp** aus.

* (Optional:) Vertrautheit mit Secure Shell (SSH) und Secure Copy (SCP). Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Optional:) Visual Studio, Azure SDK 2.5.1 oder höher und Data Lake Tools für Visual Studio. Weitere Informationen finden Sie unter [Apache Hadoop: Data Lake Tools für Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Übermitteln einer Topologie mithilfe von Visual Studio

Mit den Data Lake-Tools für Visual Studio können Sie C#-Code oder Hybridtopologien an den Storm-Cluster übermitteln. Für die folgenden Schritte wird eine Beispielanwendung verwendet. Informationen zur Topologieerstellung mithilfe von Data Lake Tools finden Sie unter [Apache Storm-Topologien mit Visual Studio und C#](apache-storm-develop-csharp-visual-studio-topology.md).

1. Wenn Sie die neueste Version von Data Lake Tools für Visual Studio noch nicht installiert haben, finden Sie Informationen dazu unter [Verwenden von Data Lake Tools für Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Azure Data Lake- und Stream Analytics-Tools für Visual Studio wurden früher als HDInsight Tools for Visual Studio bezeichnet.
    >
    > Azure Data Lake- und Stream Analytics-Tools für Visual Studio sind in der Workload **Azure-Entwicklung** für Visual Studio 2019 enthalten.

1. Starten Sie Visual Studio.

1. Wählen Sie im Fenster **Start** die Option **Neues Projekt erstellen** aus.

1. Wählen Sie im Fenster **Neues Projekt erstellen** das Suchfeld aus, und geben Sie `Storm` ein. Wählen Sie dann **Storm Sample** (Storm-Beispiel) in der Ergebnisliste und anschließend **Weiter** aus.

1. Geben Sie im Fenster **Neues Projekt konfigurieren** einen **Projektnamen** ein, und navigieren Sie zum **Speicherort** für das neue Projekt, oder erstellen Sie einen. Klicken Sie anschließend auf **Erstellen**.

    ![Fenster „Neues Projekt konfigurieren“, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

1. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf **Azure**, wählen Sie **Verbindung mit Microsoft Azure-Abonnement herstellen** aus, und melden Sie sich an.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **An Storm auf HDInsight übermitteln** aus.

    > [!NOTE]  
    > Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen für Ihr Azure-Abonnement ein. Wenn Sie über mehrere Abonnements verfügen, melden Sie sich bei dem Abonnement an, das Ihren Storm-Cluster in HDInsight enthält.

1. Wählen Sie im Dialogfeld **Topologie übermitteln** in der Dropdownliste **Storm-Cluster** ihren Storm in HDInsight-Cluster und dann **Übermitteln** aus. Sie können im Bereich **Ausgabe** überwachen, ob die Übermittlung erfolgreich ausgeführt wurde.

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Übermitteln einer Topologie mithilfe von SSH und des Storm-Befehls

1. Verwenden Sie einen [ssh-Befehl](../hdinsight-hadoop-linux-use-ssh-unix.md) zum Herstellen der Verbindung mit dem Cluster. Bearbeiten Sie den folgenden Befehl, indem Sie CLUSTERNAME durch den Namen Ihres Clusters ersetzen, und geben Sie den Befehl dann ein:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Verwenden Sie in der SSH-Sitzung den folgenden Befehl, um die **WordCount**-Beispieltopologie zu starten:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    Dieser Befehl startet die WordCount-Beispieltopologie auf dem Cluster. Diese Topologie generiert nach dem Zufallsprinzip Sätze und zählt dann die Instanzen jedes Worts in den Sätzen.

    > [!NOTE]  
    > Beim Übermitteln der Topologie an den Cluster müssen Sie zunächst die JAR-Datei mit dem Cluster kopieren, bevor Sie den Befehl `storm` verwenden. Um die Datei auf den Cluster zu kopieren, können Sie den `scp`-Befehl verwenden. Geben Sie z. B. `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar` ein
    >
    > Das Beispiel *WordCount* und andere Storm Starter-Beispiele sind unter `/usr/hdp/current/storm-client/contrib/storm-starter/` bereits im Cluster enthalten.

## <a name="submit-a-topology-programmatically"></a>Programmgesteuertes Übermitteln einer Topologie

Sie können mit dem Nimbus-Dienst programmgesteuert eine Topologie bereitstellen. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) stellt eine Java-Beispielanwendung bereit, die zeigt, wie Sie eine Topologie über den Nimbus-Dienst bereitstellen und starten.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Überwachen und Verwalten einer Topologie in Visual Studio

Wenn Sie eine Topologie in Visual Studio übermitteln, wird das Fenster **Storm-Topologien anzeigen** angezeigt. Wählen Sie die Topologie aus der Liste aus, um Informationen zur aktiven Topologie anzuzeigen.

![Überwachen der Topologie, Fenster „Storm-Topologien anzeigen“, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> Sie können sich **Storm-Topologien** auch über den **Server-Explorer** anzeigen lassen. Erweitern Sie **Azure** > **HDInsight**, klicken Sie mit der rechten Maustaste auf einen Storm im HDInsight-Cluster, und wählen Sie anschließend **Storm-Topologien anzeigen** aus.

Wählen Sie die Form für die Spouts oder Bolts aus, um Informationen über diese Komponenten anzuzeigen. Für das ausgewählte Element wird eine QuickInfo mit Komponenteninformationen angezeigt.

### <a name="deactivate-and-reactivate-a-topology"></a>Deaktivieren und erneutes Aktivieren einer Topologie

Durch Deaktivieren einer Topologie wird die Topologie unterbrochen, bis sie beendet oder wieder aktiviert wird. Verwenden Sie zum Durchzuführen dieser Vorgänge die Schaltflächen **Deaktivieren** und **Erneut aktivieren** im Bereich **Aktionen** oben im Fenster **Storm-Topologien anzeigen**.

### <a name="rebalance-a-topology"></a>Ausgleichen einer Topologie

Durch das Ausgleichen einer Topologie kann das System die Parallelität der Topologie überarbeiten. Wenn Sie z. B. die Größe des Clusters geändert haben, um zusätzliche Notizen hinzuzufügen, können in der Topologie durch einen Ausgleich die neuen Knoten erkannt werden.

Verwenden Sie zum Ausgleichen einer Topologie die Schaltfläche **Ausgleichen** im Bereich **Aktionen** des Fensters **Storm-Topologien anzeigen**.

> [!WARNING]  
> Beim Ausgleichen einer Topologie wird diese deaktiviert, die Worker werden gleichmäßig neu auf den Cluster verteilt, und anschließend wird die Topologie wieder in den Zustand vor dem Ausgleich zurückgesetzt. Wenn die Topologie z. B. aktiv war, wird sie wieder aktiviert. Wenn die Topologie deaktiviert war, bleibt sie deaktiviert.

### <a name="kill-a-running-topology"></a>Beenden einer ausgeführten Topologie

Storm-Topologien werden weiterhin ausgeführt, bis sie beendet werden oder der Cluster gelöscht wird. Verwenden Sie zum Beenden einer Topologie die Schaltfläche **Beenden** im Bereich **Aktionen**.

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Überwachen und Verwalten einer Topologie mithilfe von SSH und des Storm-Befehls

Mit dem Dienstprogramm `storm` können Sie an der Befehlszeile mit ausgeführten Topologien arbeiten. Eine vollständige Liste der Befehle erhalten Sie mit `storm -h` .

### <a name="list-topologies"></a>Auflisten der Topologien

Mit dem folgenden Befehl können Sie alle ausgeführten Topologien auflisten:

```shell
storm list
```

Die Ausgabe dieses Befehls sieht in etwa wie folgt aus:

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Deaktivieren und erneutes Aktivieren einer Topologie

Durch Deaktivieren einer Topologie wird die Topologie unterbrochen, bis sie beendet oder wieder aktiviert wird. Verwenden Sie zum Deaktivieren oder erneuten Aktivieren den folgenden Befehl:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Beenden einer ausgeführten Topologie

Storm-Topologien werden, nachdem sie einmal gestartet wurden, so lange ausgeführt, bis sie beendet werden. Führen Sie zum Beenden einer Topologie den folgenden Befehl aus:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Ausgleichen einer Topologie

Durch das Ausgleichen einer Topologie kann das System die Parallelität der Topologie überarbeiten. Wenn Sie z. B. die Größe des Clusters geändert haben, um zusätzliche Notizen hinzuzufügen, können in der Topologie durch einen Ausgleich die neuen Knoten erkannt werden.

> [!WARNING]  
> Beim Ausgleichen einer Topologie wird diese deaktiviert, die Worker werden gleichmäßig neu auf den Cluster verteilt, und anschließend wird die Topologie wieder in den Zustand vor dem Ausgleich zurückgesetzt. Wenn die Topologie z. B. aktiv war, wird sie wieder aktiviert. Wenn sie deaktiviert war, bleibt sie deaktiviert.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Überwachen und Verwalten einer Topologie in der Storm-Benutzeroberfläche

Die Storm-Benutzeroberfläche ist eine Weboberfläche zum Arbeiten mit ausgeführten Topologien und befindet sich im HDInsight-Cluster. Zum Anzeigen der Storm-Benutzeroberfläche öffnen Sie `https://CLUSTERNAME.azurehdinsight.net/stormui` in einem Webbrowser, wobei *CLUSTERNAME* der Name Ihres Clusters ist.

> [!NOTE]  
> Wenn Sie aufgefordert werden, einen Benutzernamen und ein Kennwort anzugeben, geben Sie den Benutzernamen des Clusteradministrators und das entsprechende Kennwort ein, die Sie beim Erstellen des Clusters verwendet haben.

### <a name="storm-ui-main-page"></a>Hauptseite der Storm-Benutzeroberfläche

Die Hauptseite der Storm-Benutzeroberfläche bietet die folgenden Informationen:

| `Section` | BESCHREIBUNG |
| --- | --- |
| Clusterzusammenfassung| Grundlegende Informationen zum Storm-Cluster. |
| Nimbus summary (Nimbus-Zusammenfassung) | Eine Liste allgemeiner Nimbus-Informationen |
| Topologiezusammenfassung: | Eine Liste der aktiven Topologien. Wenn Sie weitere Informationen zu einer bestimmten Topologie anzeigen möchten, wählen Sie den zugehörigen Link in der Spalte **Name** aus. |
| Supervisor summary (Supervisorzusammenfassung) | Informationen zum Storm-Supervisor. Um die einem bestimmten Supervisor zugeordneten Arbeitsressourcen anzuzeigen, wählen Sie den zugehörigen Link in der Spalte **Host** oder **ID** aus. |
| Nimbus configuration (Nimbus-Konfiguration) | Die Nimbus-Konfiguration für den Cluster. |

Die Hauptseite der Storm-Benutzeroberfläche ähnelt der folgenden Webseite:

![Hauptseite, Storm-Benutzeroberfläche, Apache Storm-Topologien, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>Topologiezusammenfassung:

Wenn Sie einen Link aus dem Abschnitt **Topologiezusammenfassung** auswählen, werden die folgenden Informationen zur Topologie angezeigt:

| `Section` | BESCHREIBUNG |
| --- | --- |
| Topologiezusammenfassung: | Grundlegende Informationen zur Topologie. |
| Topology actions (Topologieaktionen)| Verwaltungsaktionen, die für die Topologie ausgeführt werden können. Die verfügbaren Aktionen werden weiter unten in diesem Abschnitt beschrieben. |
| Topologiestatistik | Statistiken zur Topologie. Wenn Sie den Zeitrahmen für einen Eintrag in diesem Abschnitt festlegen möchten, wählen Sie den entsprechenden Link in der Spalte **Fenster** aus. |
| Spouts *(Zeitrahmen)* | Die von der Topologie verwendeten Spouts. Wenn Sie weitere Informationen zu einem bestimmten Spout anzeigen möchten, wählen Sie den entsprechenden Link in der Spalte **ID** aus. |
| Bolts *(Zeitrahmen)* | Die von der Topologie verwendeten Bolts. Wenn Sie weitere Informationen zu einem bestimmten Bolt anzeigen möchten, wählen Sie den entsprechenden Link in der Spalte **ID** aus. |
| Worker resources (Arbeitsressourcen) | Eine Liste von Arbeitsressourcen. Wenn Sie weitere Informationen zu einer bestimmten Arbeitsressource anzeigen möchten, wählen Sie den entsprechenden Link in der Spalte **Host** aus. |
| Topology visualization (Topologievisualisierung) | Die Schaltfläche **Show Visualization** (Visualisierung anzeigen), über die eine Visualisierung der Topologie angezeigt wird. |
| Topologiekonfiguration | Die Konfiguration der ausgewählten Topologie. |

Die Seite „Topologiezusammenfassung“ in Storm ähnelt der folgenden Webseite:

![Seite „Topology summary“ (Topologiezusammenfassung), Storm-Benutzeroberfläche, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

Im Abschnitt **Topologieaktionen** können Sie die folgenden Schaltflächen auswählen, um eine Aktion auszuführen:

| Taste | BESCHREIBUNG |
| --- | --- |
| Aktivieren | Setzt die Verarbeitung einer deaktivierten Topologie fort. |
| Deaktivieren | Hält eine aktive Topologie an. |
| Ausgleichen | Passt die Parallelität der Topologie an. Sie sollten ausgeführte Topologien ausgleichen, nachdem Sie die Anzahl der Knoten im Cluster geändert haben. Durch diesen Vorgang kann die Parallelität in der Topologie angepasst werden, um die zusätzliche oder geringere Anzahl der Knoten im Cluster zu kompensieren.<br/><br/>Weitere Informationen finden Sie unter <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Grundlegendes zur Parallelität einer Apache Storm-Topologie</a>.
| Beenden | Beendet eine Storm-Topologie nach dem angegebenen Zeitlimit. |
| Debuggen | Startet eine Debugsitzung für die ausgeführte Topologie. |
| Stop Debug (Debuggen beenden) | Beendet die Debugsitzung für die ausgeführte Topologie. |
| Change Log Level (Protokollierungsebene ändern) | Ändert die Debugprotokollierungsebene. |

##### <a name="spout-and-bolt-summary"></a>Zusammenfassung für Spouts und Bolts

Wenn Sie im Abschnitt **Spouts** oder **Bolts** einen Spout auswählen, werden die folgenden Informationen zum ausgewählten Element angezeigt:

| `Section` | BESCHREIBUNG |
| --- | --- |
| Komponentenzusammenfassung | Grundlegende Informationen zum Spout oder Bolt. |
| Component actions (Komponentenaktionen) | Schaltflächen **Debuggen** und **Stop Debug** (Debuggen beenden). |
| Spout stats (Spout-Statistik) oder Bolt stats (Bolt-Statistik) | Statistiken zum Spout oder Bolt. Wenn Sie den Zeitrahmen für einen Eintrag in diesem Abschnitt festlegen möchten, wählen Sie den entsprechenden Link in der Spalte **Fenster** aus. |
| (Nur Bolt)<br/>Input stats (Eingabestatistik) *(Zeitrahmen)* | Informationen zu den Eingabedatenströmen, die vom Bolt verbraucht werden. |
| Output stats (Ausgabestatistik) *(Zeitrahmen)* | Informationen zu den Datenströmen, die vom Spout oder Bolt ausgegeben werden. |
| Profiling and debugging (Profilerstellung und Debuggen) | Steuerelemente zur Profilerstellung und zum Debuggen der Komponenten auf dieser Seite. Sie können den Wert für **Status / Timeout (Minutes)** (Status/Timeout (Minuten)) festlegen und die Schaltflächen **JStack**, **Restart Worker** (Worker neu starten) und **Heap** auswählen. |
| Executors *(Zeitrahmen)* | Informationen zu den Instanzen von Spout oder Bolt. Um ein Protokoll mit Diagnoseinformationen für eine Instanz anzuzeigen, wählen Sie den Eintrag **Port** für einen bestimmten Executor aus. Sie können auch die einem bestimmten Executor zugeordneten Arbeitsressourcen anzeigen, indem Sie den entsprechenden Link in der Spalte **Host** auswählen. |
| Errors | Fehlerinformationen für diesen Spout oder Bolt. |

Die Zusammenfassungsseite für einen Bolt in Storm ähnelt der folgenden Webseite:

![Zusammenfassungsseite für einen Bolt, Storm-Benutzeroberfläche, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>Überwachen und Verwalten der Topologie mithilfe der REST-API

Die Storm-Benutzeroberfläche baut auf der REST-API auf, sodass Sie mithilfe der REST-API ähnliche Verwaltungs- und Überwachungsaufgaben ausführen können. Mithilfe der REST-API können Sie benutzerdefinierte Tools zum Verwalten und Überwachen von Storm-Topologien erstellen.

Weitere Informationen finden Sie unter [REST-API der Apache Storm-Benutzeroberfläche](https://storm.apache.org/releases/current/STORM-UI-REST-API.html)(in englischer Sprache). Die folgenden Informationen gelten für die Verwendung der REST-API mit Apache Storm in HDInsight.

> [!IMPORTANT]  
> Die Storm-REST-API ist nicht öffentlich über das Internet verfügbar. Der Zugriff muss über einen SSH-Tunnel zum Hauptknoten des HDInsight-Clusters erfolgen. Informationen zum Erstellen und Verwenden eines SSH-Tunnels finden Sie unter [Verwenden von SSH-Tunneln für den Zugriff auf Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Basis-URI

Der Basis-URI für die REST-API in Linux-basierten HDInsight-Clustern ist unter der URL-Adresse `https://HEADNODEFQDN:8744/api/v1/` verfügbar, wobei *HEADNODEFQDN* durch den Hauptknoten ersetzt wird. Der Domänenname des Hauptknotens wird bei der Clustererstellung generiert und ist nicht statisch.

Sie können den vollqualifizierten Domänennamen (FQDN) für den Clusterhauptknoten auf unterschiedliche Weise ermitteln:

| Ermittlungsmethode für den FQDN | BESCHREIBUNG |
| --- | --- |
| SSH-Sitzung | Verwenden Sie den Befehl `headnode -f` für eine SSH-Sitzung mit dem Cluster. |
| Ambari-Web | Wählen Sie auf der Webseite des Ambari-Clusters (`https://CLUSTERNAME.azurehdinsight.net`) oben auf der Seite die Option **Dienste** und dann **Storm** aus. Wählen Sie auf der Registerkarte **Zusammenfassung** die Option **Storm UI-Server**. Der vollqualifizierte Domänenname des Hosts, auf dem die Storm UI und die REST-API ausgeführt werden, wird oben auf der Seite angezeigt. |
| Ambari-REST-API | Verwenden Sie den Befehl `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"`, um Informationen zu dem Knoten abzurufen, auf dem die Storm UI und die REST-API ausgeführt werden. Ersetzen Sie die beiden Instanzen von *CLUSTERNAME* durch den Namen des Clusters. Geben Sie bei entsprechender Aufforderung das Kennwort für das Benutzerkonto (Administrator) ein. In der Antwort enthält der Eintrag „host_name“ der JSON-Ausgabe den vollqualifizierten Domänennamen des Knotens. |

### <a name="authentication"></a>Authentication

In Anforderungen an die REST-API muss die *Standardauthentifizierung* verwendet werden: Sie müssen also den Benutzernamen und das Kennwort des Administrators für den HDInsight-Cluster verwenden.

> [!NOTE]  
> Da die Standardauthentifizierung unverschlüsselt gesendet wird, sollten Sie *immer* HTTPS verwenden, um die Kommunikation mit dem Cluster zu schützen.

### <a name="return-values"></a>Rückgabewerte

Informationen, die von der REST-API zurückgegeben werden, sind möglicherweise nur innerhalb des Clusters verwendbar. Auf den für [Apache ZooKeeper](https://zookeeper.apache.org/)-Server zurückgegebenen vollqualifizierten Domänennamen (FQDN) kann z. B. nicht über das Internet zugegriffen werden.

## <a name="next-steps"></a>Nächste Schritte

Lernen Sie mehr über das [Entwickeln Java-basierter Topologien mit Apache Maven](apache-storm-develop-java-topology.md).

Weitere Beispieltopologien finden Sie unter [Apache Storm-Beispieltopologien in Azure HDInsight](apache-storm-example-topology.md).
