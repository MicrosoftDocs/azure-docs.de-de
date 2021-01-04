---
title: Aktivieren von Heapdumps für Apache Hadoop-Dienste in HDInsight – Azure
description: Aktivieren Sie Heapdumps für Apache Hadoop-Dienste von Linux-basierten HDInsight-Clustern zum Debuggen und für Analysen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 1ef52d74f7ae6e7e0d8c58e3b1972a0a1227c6b5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001921"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Aktivieren von Heapdumps für Apache Hadoop-Dienste in Linux-basiertem HDInsight

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Heapdumps enthalten eine Momentaufnahme des Speichers der Anwendung, einschließlich der Werte von Variablen zum Zeitpunkt der Dumperstellung. Daher sind sie zum Diagnostizieren von Problemen nützlich, die bei der Ausführung auftreten.

## <a name="services"></a>Dienste

Sie können Heapdumps für die folgenden Dienste aktivieren:

* **Apache hcatalog**: tempelton
* **Apache hive**: hiveserver2, metastore, derbyserver
* **mapreduce** : jobhistoryserver
* **Apache yarn**: resourcemanager, nodemanager, timelineserver
* **Apache hdfs**: datanode, secondarynamenode, namenode

Sie können Heapdumps auch für die Mapper- und Reducer-Prozesse aktivieren, die von HDInsight ausgeführt wurden.

## <a name="understanding-heap-dump-configuration"></a>Grundlegendes zur Konfiguration von Heapdumps

Heapdumps werden aktiviert, indem Optionen (oder Parameter) an die JVM übergeben werden, wenn ein Dienst gestartet wird. Für die meisten [Apache Hadoop](https://hadoop.apache.org/)-Dienste können Sie das zum Starten des Diensts verwendete Shellskript ändern, um diese Optionen zu übergeben.

In jedem Skript ist ein Export für **\*\_OPTS** vorhanden, der die an die JVM übergebenen Optionen enthält. Beispiel: Im Skript **hadoop-env.sh** enthält die Zeile, die mit `export HADOOP_NAMENODE_OPTS=` beginnt, die Optionen für den NameNode-Dienst.

Mapper- und Reducer-Prozesse unterscheiden sich geringfügig, da diese untergeordnete Prozesse des MapReduce-Diensts sind. Jeder Mapper- oder Reducer-Prozess wird in einem untergeordneten Container ausgeführt, und es gibt zwei Einträge, die die JVM-Optionen enthalten. Beide sind in **mapred-site.xml** enthalten:

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]  
> Wir empfehlen die Verwendung von [Apache Ambari](https://ambari.apache.org/) zum Ändern der Skripts und der Einstellungen in „mapred-site.xml“, da Ambari das Replizieren von Änderungen auf Knoten im Cluster verarbeitet. Unter [Verwenden von Apache Ambari](#using-apache-ambari) finden Sie die detaillierten Schritte.

### <a name="enable-heap-dumps"></a>Aktivieren von Heapdumps

Die folgende Option aktiviert Heapdumps bei einem "OutOfMemoryError":

`-XX:+HeapDumpOnOutOfMemoryError`

Das **+** -Zeichen gibt an, dass diese Option aktiviert ist. Der Standardwert ist deaktiviert.

> [!WARNING]  
> Heapdumps sind für Hadoop-Dienste auf HDInsight-Clustern nicht standardmäßig aktiviert, da die Dumpdateien groß sein können. Wenn Sie sie zur Problembehandlung aktivieren, denken Sie daran, sie zu deaktivieren, sobald Sie das Problem reproduziert und die Dumpdateien gesammelt haben.

### <a name="dump-location"></a>Dumpspeicherort

Der Standardspeicherort für die Dumpdatei ist das aktuelle Arbeitsverzeichnis. Sie können steuern, wo die Datei gespeichert wird, indem Sie die folgende Option verwenden:

`-XX:HeapDumpPath=/path`

Beispiel: `-XX:HeapDumpPath=/tmp` bewirkt, dass die Dumps im Verzeichnis „/temp“ gespeichert werden.

### <a name="scripts"></a>Skripts

Sie können auch ein Skript auslösen, wenn ein **OutOfMemoryError** auftritt. So können Sie beispielsweise eine Benachrichtigung auslösen, damit Sie wissen, dass der Fehler aufgetreten ist. Verwenden Sie die folgende Option, um ein Skript auszulösen, wenn __OutOfMemoryError__ auftritt:

`-XX:OnOutOfMemoryError=/path/to/script`

> [!NOTE]  
> Da Apache Hadoop ein verteiltes System ist, muss jedes verwendete Skript auf allen Knoten im Cluster vorhanden sein, auf denen der Dienst ausgeführt wird.
> 
> Das Skript muss sich zudem an einem Speicherort befinden, auf den das Konto zugreifen kann, mit dem der Dienst ausgeführt wird, und Ausführungsberechtigungen müssen gewährt werden. Sie könnten z. B. Skripts in `/usr/local/bin` speichern und `chmod go+rx /usr/local/bin/filename.sh` zum Gewähren von Lese- und Ausführungsberechtigungen verwenden.

## <a name="using-apache-ambari"></a>Verwenden von Apache Ambari

Gehen Sie folgendermaßen vor, um die Konfiguration für einen Dienst zu ändern:

1. Navigieren Sie in einem Webbrowser zu `https://CLUSTERNAME.azurehdinsight.net`, wobei `CLUSTERNAME` der Name Ihres Clusters ist.

2. Wählen Sie mithilfe der Liste auf der linken Seite den Dienstbereich aus, den Sie ändern möchten. Beispielsweise **HDFS**. Wählen Sie im mittleren Bereich die Registerkarte **Configs** aus.

    ![Bild der Ambari-Webbenutzeroberfläche mit ausgewählter Registerkarte für HDFS-Konfigurationen](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-service-config-tab.png)

3. Geben Sie im Feld **Filter** den Text **opts** ein. Es werden nur Elemente mit diesem Text angezeigt.

    ![Gefilterte Liste der Apache Ambari-Konfigurationen](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdinsight-filter-list.png)

4. Suchen Sie nach dem Eintrag **\*\_OPTS** für den Dienst, für den Sie Heapdumps aktivieren möchten, und fügen die Optionen hinzu, die Sie aktivieren möchten. In der folgenden Abbildung wurde `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` dem Eintrag **HADOOP\_NAMENODE\_OPTS** hinzugefügt:

    ![Apache Ambari: hadoop-namenode-opts](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hadoop-namenode-opts.png)

   > [!NOTE]  
   > Beim Aktivieren von Heapdumps für den untergeordneten Mapper- oder Reducer-Prozess suchen Sie nach den Feldern mit der Bezeichnung **mapreduce.admin.map.child.java.opts** und **mapreduce.admin.reduce.child.java.opts**.

    Verwenden Sie die Schaltfläche **Save** , um die Änderungen zu speichern. Sie können einen kurzen Hinweis zur Beschreibung der Änderungen eingeben.

5. Nachdem die Änderungen angewendet wurden, wird das Symbol für **Neustart erforderlich** neben einem oder mehreren Diensten angezeigt.

    ![Symbol "Restart required" und Schaltfläche "Restart"](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/restart-required-icon.png)

6. Wählen Sie jeden Dienst aus, für den ein Neustart erforderlich ist, und verwenden Sie die Schaltfläche **Service Actions**, um **Turn On Maintenance Mode** auszuwählen. Der Wartungsmodus verhindert, dass Warnungen von dem Dienst generiert werden, wenn Sie ihn neu starten.

    ![Menü zum Aktivieren des HDI-Wartungsmodus](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-maintenance-mode.png)

7. Nachdem Sie den Wartungsmodus aktiviert haben, verwenden Sie die Schaltfläche **Restart** für den Dienst, um **Restart All Effected** auszuwählen.

    ![Apache Ambari: Eintrag zum Neustarten aller betroffenen Instanzen](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-restart-all-button.png)

   > [!NOTE]  
   > Die Einträge für die Schaltfläche **Restart** (Neu starten) können für andere Dienste anders lauten.

8. Verwenden Sie nach dem Neustart der Dienste die Schaltfläche **Service Actions**, um **Turn Off Maintenance Mode** auszuwählen. Dadurch setzt Ambari das Überwachen auf Warnungen für den Dienst fort.