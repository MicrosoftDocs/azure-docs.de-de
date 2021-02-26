---
title: Zugreifen auf Apache Hadoop YARN-Anwendungsprotokolle – Azure HDInsight
description: Erfahren Sie, wie Sie auf einem Linux-basierten HDInsight-Cluster (Apache Hadoop) mithilfe der Befehlszeile und eines Webbrowsers auf YARN-Anwendungsprotokolle zugreifen.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 882384b5e57db27cff981f80e790dfd41b624c93
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980693"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Zugreifen auf Apache Hadoop YARN-Anwendungsprotokolle unter Linux-basiertem HDInsight

Informieren Sie sich, wie Sie auf Protokolle für [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)-Anwendungen (Yet Another Resource Negotiator) in einem Apache Hadoop-Cluster in Azure HDInsight zugreifen.

## <a name="what-is-apache-yarn"></a>Was ist Apache YARN?

YARN unterstützt mehrere Programmierungsmodelle (u. a. Apache Hadoop MapReduce), indem die Ressourcenverwaltung von der Zeitplanung/Überwachung von Anwendungen getrennt wird. YARN verwendet einen globalen *`ResourceManager`* (RM), workerknotenbezogene *NodeManager* (NMs) und anwendungsbezogene *ApplicationMaster* (AMs). Der anwendungsbezogene AM handelt Ressourcen (CPU, Arbeitsspeicher, Datenträger, Netzwerk) für die Ausführung Ihrer Anwendung mit dem RM aus. Der RM arbeitet mit NMs zusammen, um diese Ressourcen zu gewähren, die als *Container* zugewiesen werden. Der AM ist zuständig für die Nachverfolgung des Status der Container, die ihm vom RM zugewiesen wurden. Je nach Art der Anwendung kann diese viele Container benötigen.

Jede Anwendung kann aus mehreren *Anwendungsversuchen* bestehen. Tritt bei einer Anwendung ein Fehler auf, kann ein neuer Versuch unternommen werden. Jeder Versuch wird in einem Container ausgeführt. In gewisser Weise stellt ein Container den Kontext für die Standardeinheit für Aufgaben bereit, die von einer YARN-Anwendung ausgeführt werden. Alle Aufgaben, die im Kontext eines Containers erledigt werden, erfolgen auf dem einzelnen Workerknoten, auf dem der Container angegeben wurde. Weitere Informationen finden Sie unter [Hadoop: Schreiben von YARN-Anwendungen](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) oder [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html).

Zur Skalierung Ihres Clusters für einen höheren Verarbeitungsdurchsatz können Sie die [Autoskalierung](hdinsight-autoscale-clusters.md) oder das [manuelle Skalieren Ihrer Cluster mit einigen verschiedenen Sprachen](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters) verwenden.

## <a name="yarn-timeline-server"></a>YARN Timeline Server

Der [Apache Hadoop YARN Timeline Server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) bietet allgemeine Informationen zu abgeschlossenen Anwendungen.

YARN Timeline Server umfasst die folgenden Arten von Daten:

* Die Anwendungs-ID, ein eindeutiger Bezeichner einer Anwendung
* Der Benutzer, der die Anwendung gestartet hat
* Informationen zu den erfolgten Versuchen, die Anwendung abzuschließen
* Die bei Anwendungsversuchen verwendeten Container

## <a name="yarn-applications-and-logs"></a>YARN-Anwendungen und -Protokolle

Anwendungsprotokolle (und dazugehörige Containerprotokolle) sind für das Beheben von Problemen bei Hadoop-Anwendungen besonders wichtig. YARN bietet mit der Protokollaggregation ein nützliches Framework für das Sammeln, Zusammenführen und Speichern von Anwendungsprotokollen.

Durch die Protokollaggregationsfunktion wird der Zugriff auf Anwendungsprotokolle deterministischer. Sie aggregiert Protokolle in allen Containern auf einem Workerknoten und speichert sie als eine aggregierte Protokolldatei pro Workerknoten. Das Protokoll wird, nachdem eine Anwendung beendet wurde, im Standarddateisystem gespeichert. Ihre Anwendung mag Hunderte oder Tausende von Containern verwenden, doch Protokolle für alle auf einem einzelnen Workerknoten vorhandenen Container werden immer zu einer zentralen Datei zusammengeführt. Daher wird nur ein Protokoll pro Workerknoten von Ihrer Anwendung genutzt. Die Protokollaggregation ist für HDInsight-Cluster ab Version 3.0 standardmäßig aktiviert. Aggregierte Protokolle befinden sich im Standardspeicher für den Cluster. Der folgende Pfad ist der HDFS-Pfad für die Protokolle:

```
/app-logs/<user>/logs/<applicationId>
```

`user` steht hier für den Namen des Benutzers, der die Anwendung gestartet hat. `applicationId` ist der eindeutige Bezeichner, der einer Anwendung durch den YARN-RM zugewiesen wird.

Die zusammengeführten Protokolle sind nicht unmittelbar lesbar, da sie in einem TFile-Binärformat mit Indizierung nach Container geschrieben werden. Verwenden Sie die YARN-`ResourceManager`-Protokolle oder CLI-Tools, um diese Protokolle für relevante Anwendungen oder Container im Nur-Text-Format anzuzeigen.

## <a name="yarn-logs-in-an-esp-cluster"></a>Yarn-Protokolle in einem ESP-Cluster

Der benutzerdefinierten `mapred-site` in Ambari müssen zwei Konfigurationen hinzugefügt werden.

1. Navigieren Sie in einem Webbrowser zu `https://CLUSTERNAME.azurehdinsight.net`, wobei `CLUSTERNAME` der Name Ihres Clusters ist.

1. Navigieren Sie über die Ambari-Benutzeroberfläche zu **MapReduce2** > **Configs** > **Advanced** > **Custom mapred-site** (MapReduce2 > Konfigurationen > Erweitert > Benutzerdefinierte mapred-Website).

1. Fügen Sie *eine* der folgenden Gruppen von Eigenschaften hinzu:

    **Gruppe 1**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **Gruppe 2**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Speichern Sie die Änderungen, und starten Sie alle betroffenen Dienste neu.

## <a name="yarn-cli-tools"></a>YARN-CLI-Tools

1. Verwenden Sie einen [ssh-Befehl](./hdinsight-hadoop-linux-use-ssh-unix.md) zum Herstellen der Verbindung mit dem Cluster. Bearbeiten Sie den folgenden Befehl, indem Sie CLUSTERNAME durch den Namen Ihres Clusters ersetzen, und geben Sie den Befehl dann ein:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Listen Sie alle Anwendungs-IDs der aktuell ausgeführten Yarn-Anwendungen mit dem folgenden Befehl auf:

    ```bash
    yarn top
    ```

    Notieren Sie sich in der Spalte `APPLICATIONID` die ID der Anwendung, deren Protokolle heruntergeladen werden sollen.

    ```output
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved
    
                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Sie können diese Protokolle im Nur-Text-Format anzeigen, indem Sie einen der folgenden Befehle ausführen:

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    Geben Sie beim Ausführen dieser Befehle die &lt;AnwendungsID>, den &lt;Benutzer,-der-die-Anwendung-gestartet-hat>, die &lt;ContainerID> und die &lt;Workerknotenadresse> an.

### <a name="other-sample-commands"></a>Weitere Beispielbefehle

1. Laden Sie Yarn-Containerprotokolle für alle Anwendungsmaster mit dem nachstehenden Befehl herunter. Durch diesen Schritt wird die Protokolldatei mit dem Namen `amlogs.txt` im Textformat erstellt.

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. Laden Sie Yarn-Containerprotokolle nur für den neuesten Anwendungsmaster mit folgendem Befehl herunter:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Laden Sie YARN-Containerprotokolle für die ersten beiden Anwendungsmaster mit folgendem Befehl herunter:

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. Laden Sie alle Yarn-Containerprotokolle mit dem folgenden Befehl herunter:

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. Laden Sie ein Yarn-Containerprotokoll für einen bestimmten Container mit folgendem Befehl herunter:

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>YARN-`ResourceManager`-Benutzeroberfläche

Die YARN-`ResourceManager`-Benutzeroberfläche wird auf dem Clusterhauptknoten ausgeführt. Der Zugriff erfolgt über die Ambari-Webbenutzeroberfläche. Führen Sie die folgenden Schritte aus, um die YARN-Protokolle anzeigen:

1. Navigieren Sie in Ihrem Webbrowser zu `https://CLUSTERNAME.azurehdinsight.net`. Ersetzen Sie CLUSTERNAME durch den Namen Ihres HDInsight-Clusters.

2. Wählen Sie aus der Liste der Dienste auf der linken Seite den Dienst **YARN** aus.

    ![Ausgewählter Apache Ambari Yarn-Dienst](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. Wählen Sie aus der Dropdownliste **QuickLinks** einen der Clusterhauptknoten und dann **`ResourceManager Log`** aus.

    ![Quicklinks für Apache Ambari Yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Eine Liste mit Links zu YARN-Protokollen wird angezeigt.

## <a name="next-steps"></a>Nächste Schritte

* [Apache Hadoop-Architektur in HDInsight](hdinsight-hadoop-architecture.md)
* [Problembehandlung für Apache Hadoop YARN mit Azure HDInsight](hdinsight-troubleshoot-yarn.md)
