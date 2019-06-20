---
title: Überwachen der Clusterleistung – Azure HDInsight
description: Es wird beschrieben, wie Sie die Kapazität und Leistung eines HDInsight-Clusters überwachen.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 3fcd1e54a8993b2693b169a2c8b4c6e9bca57119
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393415"
---
# <a name="monitor-cluster-performance"></a>Überwachen der Clusterleistung

Die Überwachung der Integrität und Leistung eines HDInsight-Clusters ist sehr wichtig, um eine optimale Leistung und Ressourcenverwendung zu gewährleisten. Darüber hinaus kann die Überwachung dazu beitragen, Clusterkonfigurationsfehler und Benutzercodeprobleme zu erkennen und zu behandeln.

In den folgenden Abschnitten erfahren Sie, wie Sie die Last in Ihren Clustern und Apache Hadoop YARN-Warteschlangen überwachen und optimieren und wie Sie Speicherdrosselungsprobleme erkennen.

## <a name="monitor-cluster-load"></a>Überwachen der Clusterlast

Die Leistung von Hadoop-Clustern ist am besten, wenn die Last des Clusters gleichmäßig auf alle Knoten verteilt ist. Dadurch können Verarbeitungsaufgaben ausgeführt werden, ohne durch Arbeitsspeicher-, CPU- oder Datenträgerressourcen auf einzelnen Knoten eingeschränkt zu werden.

Melden Sie sich bei der [Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md) an, und wählen Sie dann die Registerkarte **Hosts** aus, um sich einen allgemeinen Überblick über die Knoten Ihres Clusters und deren Last zu verschaffen. Ihre Hosts werden anhand ihrer vollqualifizierten Domänennamen aufgelistet. Der Betriebsstatus jedes Hosts wird mit einer farbigen Integritätsanzeige angegeben:

| Farbe | BESCHREIBUNG |
| --- | --- |
| Rot | Mindestens eine Master-Komponente auf dem Host ist ausgefallen. Zeigen Sie mit der Maus darauf, um eine QuickInfo mit den betroffenen Komponenten anzuzeigen. |
| Orange | Mindestens eine sekundäre Komponente auf dem Host ist ausgefallen. Zeigen Sie mit der Maus darauf, um eine QuickInfo mit den betroffenen Komponenten anzuzeigen. |
| Gelb | Ambari Server hat seit mehr als drei Minuten keinen Heartbeat vom Host empfangen. |
| Grün | Normaler Ausführungszustand |

Außerdem werden Spalten angezeigt, in denen die Anzahl von Kernen und die RAM-Menge für jeden Host sowie die Datenträgerauslastung und die durchschnittliche Auslastung angezeigt werden.

![Registerkarte „Hosts“](./media/hdinsight-key-scenarios-to-monitor/hosts-tab.png)

Wählen Sie einen beliebigen Hostnamen aus, um eine ausführliche Übersicht über die Komponenten, die auf diesem Host ausgeführt werden, und die dazugehörigen Metriken zu erhalten. Die Metriken werden als auswählbare Zeitachse für CPU-Auslastung, Ladezustand, Datenträgerauslastung, Speicherauslastung, Netzwerkauslastung und Anzahl von Prozessen angezeigt.

![Hostdetails](./media/hdinsight-key-scenarios-to-monitor/host-details.png)

Unter [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md) finden Sie Details zum Festlegen von Warnungen und Anzeigen von Metriken.

## <a name="yarn-queue-configuration"></a>Konfiguration von YARN-Warteschlangen

Hadoop verfügt über verschiedene Dienste, die auf der dazugehörigen verteilten Plattform ausgeführt werden. YARN (Yet Another Resource Negotiator) koordiniert diese Dienste und ordnet Clusterressourcen zu, um sicherzustellen, dass die Last gleichmäßig auf den gesamten Cluster verteilt wird.

YARN teilt die beiden Aufgaben von JobTracker (Ressourcenverwaltung und Auftragsplanung/-überwachung) auf zwei Daemons auf: einen globalen Ressourcen-Manager und einen anwendungsspezifischen ApplicationMaster (AM).

Der Ressourcen-Manager ist ein *reiner Scheduler*, dessen einzige Aufgabe darin besteht, die verfügbaren Ressourcen zwischen allen konkurrierenden Anwendungen zu vermitteln. Der Ressourcen-Manager stellt sicher, dass immer alle Ressourcen genutzt werden, und optimiert die Nutzung anhand verschiedener Konstanten (beispielsweise SLAs und Kapazitätsgarantien). Der ApplicationMaster handelt Ressourcen mit dem Ressourcen-Manager aus und arbeitet mit den NodeManager-Komponenten zusammen, um die Container und ihre entsprechende Ressourcennutzung auszuführen und zu überwachen.

Wenn mehrere Mandanten einen großen Cluster gemeinsam nutzen, wird auch ein Konkurrenzkampf um die Ressourcen des Clusters ausgetragen. Der CapacityScheduler ist ein austauschbarer Scheduler, der den Ressourcenaustausch unterstützt, indem Anforderungen in Warteschlangen eingereiht werden. Der CapacityScheduler unterstützt auch *hierarchische Warteschlangen*, um sicherzustellen, dass Ressourcen zwischen den Unterwarteschlangen einer Organisation gemeinsam genutzt werden, bevor Warteschlangen anderer Anwendungen kostenlose Ressourcen verwenden dürfen.

Mithilfe von YARN können diesen Warteschlangen Ressourcen zugeordnet werden, und es wird angezeigt, ob Ihre gesamten verfügbaren Ressourcen zugewiesen wurden. Melden Sie sich zum Anzeigen von Informationen zu Ihren Warteschlangen bei der Ambari-Webbenutzeroberfläche an, und wählen Sie im oberen Menü die Option **YARN Queue Manager** aus.

![YARN Queue Manager](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager.png)

Auf der Seite „YARN Queue Manager“ wird links eine Liste mit Ihren Warteschlangen und der jeweils zugewiesene Kapazitätsprozentsatz angezeigt.

![Seite mit Details zum YARN Queue Manager](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Einen ausführlicheren Überblick über Ihre Warteschlangen erhalten Sie, indem Sie im Ambari-Dashboard in der Liste auf der linken Seite den Dienst **YARN** auswählen. Wählen Sie anschließend im Dropdownmenü **Quick Links** (Quicklinks) unter Ihrem aktiven Knoten die Option **Resource Manager UI** (Benutzeroberfläche des Ressourcen-Managers) aus.

![Menülink für die Benutzeroberfläche des Ressourcen-Managers](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

Wählen Sie auf der Benutzeroberfläche des Ressourcen-Managers im Menü auf der linken Seite die Option **Scheduler** aus. Unter *Application Queues* (Anwendungswarteschlangen) wird eine Liste mit Ihren Warteschlangen angezeigt. Hier sind die verwendete Kapazität für die einzelnen Warteschlangen, der Grad der Verteilung auf die Warteschlangen und etwaige Ressourceneinschränkungen für Aufträge angegeben.

![Menülink für die Benutzeroberfläche des Ressourcen-Managers](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui.png)

## <a name="storage-throttling"></a>Speicherdrosselung

Zu einem Leistungsengpass kann es für einen Cluster auf der Speicherebene kommen. Der Grund für diese Art von Engpass ist meistens eine *Blockierung* der E/A-Vorgänge. Dies passiert, wenn Ihre ausgeführten Aufgaben mehr E/A-Daten senden, als vom Speicherdienst bewältigt werden können. Diese Blockierung führt zu einer Warteschlange mit E/A-Anforderungen, die auf ihre Verarbeitung nach der Verarbeitung der aktuellen E/A-Daten warten. Zu den Blockierungen kommt es aufgrund von *Speicherdrosselung*. Es handelt sich hierbei nicht um ein physisches Limit, sondern um eine Beschränkung durch den Speicherdienst per Vereinbarung zum Servicelevel (SLA). Mit dieser Beschränkung wird sichergestellt, dass kein einzelner Client oder Mandant über ein Monopol in Bezug auf den Dienst verfügt. Durch die Vereinbarung zum Servicelevel ist die Anzahl von E/A-Vorgängen pro Sekunde (IOs per second, IOPS) für Azure Storage beschränkt. Ausführliche Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Wenn Sie Azure Storage nutzen und Informationen zur Überwachung von speicherbezogenen Problemen suchen, finden Sie diese unter [Microsoft Azure-Speicher: Überwachung, Diagnose und Problembehandlung](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Wenn Sie Azure Data Lake Storage (ADLS) als Sicherungsspeicher für Ihren Cluster verwenden, erfolgt die Drosselung bei Ihnen wahrscheinlich aufgrund von Einschränkungen der Bandbreite. Die Drosselung lässt sich in diesem Fall durch Beobachten der Drosselungsfehler in den Taskprotokollen ermitteln. Informationen zu ADLS finden Sie in den folgenden Artikeln jeweils im Abschnitt zur Drosselung für den entsprechenden Dienst:

* [Anleitung für die Leistungsoptimierung für Apache Hive in HDInsight und Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Anleitung für die Leistungsoptimierung für MapReduce in HDInsight und Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Anleitung für die Leistungsoptimierung für Apache Storm in HDInsight und Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung und Überwachung für Ihre Cluster finden Sie unter den folgenden Links:

* [Analysieren von HDInsight-Protokollen](hdinsight-debug-jobs.md)
* [Debuggen von Apps mit Apache Hadoop YARN-Protokollen](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Aktivieren von Heapdumps für Apache Hadoop-Dienste in Linux-basiertem HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
