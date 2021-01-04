---
title: Überwachen der Clusterleistung – Azure HDInsight
description: Überwachen der Integrität und Leistung von Apache Hadoop Clustern in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 03/09/2020
ms.openlocfilehash: 1da86e36cf20dc15152aea74be6c43a4cb43d3b4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014243"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>Überwachen der Clusterleistung in Azure HDInsight

Die Überwachung der Integrität und Leistung eines HDInsight-Clusters ist sehr wichtig, um eine optimale Leistung und Ressourcenverwendung zu gewährleisten. Darüber hinaus kann die Überwachung dazu beitragen, Clusterkonfigurationsfehler und Benutzercodeprobleme zu erkennen und zu behandeln.

In den folgenden Abschnitten erfahren Sie, wie Sie die Last in Ihren Clustern und Apache Hadoop YARN-Warteschlangen überwachen und optimieren und wie Sie Speicherdrosselungsprobleme erkennen.

## <a name="monitor-cluster-load"></a>Überwachen der Clusterlast

Die Leistung von Hadoop-Clustern ist am besten, wenn die Last des Clusters gleichmäßig auf alle Knoten verteilt ist. Dadurch können Verarbeitungsaufgaben ausgeführt werden, ohne durch Arbeitsspeicher-, CPU- oder Datenträgerressourcen auf einzelnen Knoten eingeschränkt zu werden.

Melden Sie sich bei der [Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md) an, und wählen Sie dann die Registerkarte **Hosts** aus, um sich einen allgemeinen Überblick über die Knoten Ihres Clusters und deren Last zu verschaffen. Ihre Hosts werden anhand ihrer vollqualifizierten Domänennamen aufgelistet. Der Betriebsstatus jedes Hosts wird mit einer farbigen Integritätsanzeige angegeben:

| Color | BESCHREIBUNG |
| --- | --- |
| Red | Mindestens eine Master-Komponente auf dem Host ist ausgefallen. Zeigen Sie mit der Maus darauf, um eine QuickInfo mit den betroffenen Komponenten anzuzeigen. |
| Orange | Mindestens eine sekundäre Komponente auf dem Host ist ausgefallen. Zeigen Sie mit der Maus darauf, um eine QuickInfo mit den betroffenen Komponenten anzuzeigen. |
| Gelb | Ambari Server hat seit mehr als drei Minuten keinen Heartbeat mehr vom Host empfangen. |
| Grün | Normaler Ausführungszustand |

Außerdem werden Spalten angezeigt, in denen die Anzahl von Kernen und die RAM-Menge für jeden Host sowie die Datenträgerauslastung und die durchschnittliche Auslastung angezeigt werden.

![Apache Ambari: Übersicht über die Hostregisterkarte](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

Wählen Sie einen beliebigen Hostnamen aus, um eine ausführliche Übersicht über die Komponenten, die auf diesem Host ausgeführt werden, und die dazugehörigen Metriken zu erhalten. Die Metriken werden als auswählbare Zeitachse für CPU-Auslastung, Ladezustand, Datenträgerauslastung, Speicherauslastung, Netzwerkauslastung und Anzahl von Prozessen angezeigt.

![Apache Ambari: Übersicht über die Hostdetails](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

Unter [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md) finden Sie Details zum Festlegen von Warnungen und Anzeigen von Metriken.

## <a name="yarn-queue-configuration"></a>Konfiguration von YARN-Warteschlangen

Hadoop verfügt über verschiedene Dienste, die auf der dazugehörigen verteilten Plattform ausgeführt werden. YARN (Yet Another Resource Negotiator) koordiniert diese Dienste und ordnet Clusterressourcen zu, um sicherzustellen, dass die Last gleichmäßig auf den gesamten Cluster verteilt wird.

YARN teilt die beiden Aufgaben von JobTracker (Ressourcenverwaltung und Auftragsplanung/-überwachung) auf zwei Daemons auf: einen globalen Ressourcen-Manager und einen anwendungsspezifischen ApplicationMaster (AM).

Der Ressourcen-Manager ist ein *reiner Scheduler*, dessen einzige Aufgabe darin besteht, die verfügbaren Ressourcen zwischen allen konkurrierenden Anwendungen zu vermitteln. Der Ressourcen-Manager stellt sicher, dass immer alle Ressourcen genutzt werden, und optimiert die Nutzung anhand verschiedener Konstanten (beispielsweise SLAs und Kapazitätsgarantien). Der ApplicationMaster handelt Ressourcen mit dem Ressourcen-Manager aus und arbeitet mit den NodeManager-Komponenten zusammen, um die Container und ihre entsprechende Ressourcennutzung auszuführen und zu überwachen.

Wenn mehrere Mandanten einen großen Cluster gemeinsam nutzen, wird auch ein Konkurrenzkampf um die Ressourcen des Clusters ausgetragen. Der CapacityScheduler ist ein austauschbarer Scheduler, der den Ressourcenaustausch unterstützt, indem Anforderungen in Warteschlangen eingereiht werden. Der CapacityScheduler unterstützt auch *hierarchische Warteschlangen*, um sicherzustellen, dass Ressourcen zwischen den Unterwarteschlangen einer Organisation gemeinsam genutzt werden, bevor Warteschlangen anderer Anwendungen kostenlose Ressourcen verwenden dürfen.

Mithilfe von YARN können diesen Warteschlangen Ressourcen zugeordnet werden, und es wird angezeigt, ob Ihre gesamten verfügbaren Ressourcen zugewiesen wurden. Melden Sie sich zum Anzeigen von Informationen zu Ihren Warteschlangen bei der Ambari-Webbenutzeroberfläche an, und wählen Sie im oberen Menü die Option **YARN Queue Manager** aus.

![Apache Ambari: YARN Queue Manager](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

Auf der Seite „YARN Queue Manager“ wird links eine Liste mit Ihren Warteschlangen und der jeweils zugewiesene Kapazitätsprozentsatz angezeigt.

![Seite mit Details zum YARN Queue Manager](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Einen ausführlicheren Überblick über Ihre Warteschlangen erhalten Sie, indem Sie im Ambari-Dashboard in der Liste auf der linken Seite den Dienst **YARN** auswählen. Wählen Sie anschließend im Dropdownmenü **Quick Links** (Quicklinks) unter Ihrem aktiven Knoten die Option **Resource Manager UI** (Benutzeroberfläche des Ressourcen-Managers) aus.

![Menülinks der Benutzeroberfläche des Ressourcen-Managers](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

Wählen Sie auf der Benutzeroberfläche des Ressourcen-Managers im Menü auf der linken Seite die Option **Scheduler** aus. Unter *Application Queues* (Anwendungswarteschlangen) wird eine Liste mit Ihren Warteschlangen angezeigt. Hier sind die verwendete Kapazität für die einzelnen Warteschlangen, der Grad der Verteilung auf die Warteschlangen und etwaige Ressourceneinschränkungen für Aufträge angegeben.

![Apache Hadoop: Menü der Benutzeroberfläche des Ressourcen-Managers](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>Speicherdrosselung

Zu einem Leistungsengpass kann es für einen Cluster auf der Speicherebene kommen. Der Grund für diese Art von Engpass ist meistens eine *Blockierung* der E/A-Vorgänge. Dies geschieht, wenn Ihre ausgeführten Aufgaben mehr E/A-Daten senden, als vom Speicherdienst bewältigt werden können. Diese Blockierung führt zu einer Warteschlange mit E/A-Anforderungen, die auf ihre Verarbeitung nach der Verarbeitung der aktuellen E/A-Daten warten. Zu den Blockierungen kommt es aufgrund von *Speicherdrosselung*. Es handelt sich hierbei nicht um ein physisches Limit, sondern um eine Beschränkung durch den Speicherdienst per Vereinbarung zum Servicelevel (SLA). Mit dieser Beschränkung wird sichergestellt, dass kein einzelner Client oder Mandant über ein Monopol in Bezug auf den Dienst verfügt. Durch die Vereinbarung zum Servicelevel ist die Anzahl von E/A-Vorgängen pro Sekunde (IOs per second, IOPS) für Azure Storage beschränkt. Ausführliche Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Standardspeicherkonten](../storage/common/scalability-targets-standard-account.md).

Wenn Sie Azure Storage nutzen, finden Sie Informationen zur Überwachung von speicherbezogenen Problemen unter [Microsoft Azure Storage: Überwachung, Diagnose und Problembehandlung](../storage/common/storage-monitoring-diagnosing-troubleshooting.md).

Wenn Sie Azure Data Lake Storage (ADLS) als Sicherungsspeicher für Ihren Cluster verwenden, erfolgt die Drosselung bei Ihnen wahrscheinlich aufgrund von Einschränkungen der Bandbreite. Die Drosselung lässt sich in diesem Fall durch Beobachten der Drosselungsfehler in den Taskprotokollen ermitteln. Informationen zu ADLS finden Sie in den folgenden Artikeln jeweils im Abschnitt zur Drosselung für den entsprechenden Dienst:

* [Anleitung für die Leistungsoptimierung für Apache Hive in HDInsight und Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Anleitung für die Leistungsoptimierung für MapReduce in HDInsight und Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Anleitung für die Leistungsoptimierung für Apache Storm in HDInsight und Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="troubleshoot-sluggish-node-performance"></a>Beheben von Problemen bei der Leistung langsamer Knoten

Trägheit kann unter anderem auf wenig Speicherplatz im Cluster zurückzuführen sein. Führen Sie eine Untersuchung anhand der folgenden Schritte durch:

1. Verwenden Sie den [SSH-Befehl](./hdinsight-hadoop-linux-use-ssh-unix.md), um Verbindungen mit den einzelnen Knoten herzustellen.

1. Überprüfen Sie die Datenträgerauslastung, indem Sie einen der folgenden Befehle ausführen:

    ```bash
    df -h
    du -h --max-depth=1 / | sort -h
    ```

1. Überprüfen Sie anhand der Ausgabe, ob im Ordner `mnt` oder in anderen Ordnern große Dateien vorhanden sind. Die Ordner `usercache` und `appcache` („mnt/resource/hadoop/yarn/local/usercache/hive/appcache/“) enthalten in der Regel große Dateien.

1. Sollten große Dateien vorhanden sein, wurde die Dateivergrößerung entweder durch einen aktuellen Auftrag verursacht, oder ein fehlerhafter vorheriger Auftrag hat zu diesem Problem beigetragen. Überprüfen Sie mithilfe des folgenden Befehls, ob dieses Verhalten durch einen aktuellen Auftrag verursacht wird:

    ```bash
    sudo du -h --max-depth=1 /mnt/resource/hadoop/yarn/local/usercache/hive/appcache/
    ```

1. Sollte von diesem Befehl ein bestimmter Auftrag angegeben werden, können Sie diesen mithilfe eines Befehls wie dem folgenden beenden:

    ```bash
    yarn application -kill -applicationId <application_id>
    ```

    Ersetzen Sie `application_id` durch die Anwendungs-ID. Sollten keine bestimmten Aufträge angegeben werden, fahren Sie mit dem nächsten Schritt fort.

1. Falls durch den oben genannten Befehl keine spezifischen Aufträge angegeben wurden, löschen Sie die gefundenen großen Dateien mithilfe eines Befehls wie dem folgenden:

    ```bash
    rm -rf filecache usercache
    ```

Weitere Informationen zu Speicherplatzproblemen finden Sie unter [Nicht genügend Speicherplatz](./hadoop/hdinsight-troubleshoot-out-disk-space.md).

> [!NOTE]  
> Wenn Sie über große Dateien verfügen, die zu dem Speicherplatzproblem beitragen, aber erhalten bleiben sollen, müssen Sie Ihren HDInsight-Cluster zentral hochskalieren und die Dienste neu starten. Warten Sie nach Abschluss dieser Prozedur einige Minuten. Danach steht wieder freier Speicherplatz zur Verfügung, und der Knoten funktioniert wie gewohnt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung und Überwachung für Ihre Cluster finden Sie unter den folgenden Links:

* [Analysieren von HDInsight-Protokollen](./hdinsight-troubleshoot-guide.md)
* [Debuggen von Apps mit Apache Hadoop YARN-Protokollen](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Aktivieren von Heapdumps für Apache Hadoop-Dienste in Linux-basiertem HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)