---
title: Fehler beim Starten von Apache HBase Master in Azure HDInsight
description: Fehler beim Starten von Apache HBase Master (HMaster) in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 032c25969bf477e1163b8db2aca631044c457939
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539971"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Fehler beim Starten von Apache HBase Master (HMaster) in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="scenario-atomic-renaming-failure"></a>Szenario: Fehler bei der atomischen Umbenennung

### <a name="issue"></a>Problem

Beim Start wurden unerwartete Dateien erkannt.

### <a name="cause"></a>Ursache

Im Rahmen des Startvorgangs führt HMaster verschiedene Initialisierungsschritte aus. Dabei werden unter anderem Daten aus dem Ablageordner (.tmp) in den Datenordner verschoben. HMaster untersucht zudem den Ordner mit write-ahead-Protokollen (WALs), um festzustellen, ob nicht reagierende Regionsserver vorhanden sind.

HMaster führt einen einfachen list-Befehl für die WAL-Ordner aus. Findet HMaster zu einem beliebigen Zeitpunkt eine unerwartete Datei in einem dieser Ordner, wird eine Ausnahme ausgelöst und kein Start ausgeführt.

### <a name="resolution"></a>Lösung

Überprüfen Sie die Aufrufliste und versuchen Sie festzustellen, welcher Ordner das Problem verursachen könnte (dies können beispielsweise der WAL-Ordner oder der TMP-Ordner sein). Versuchen Sie anschließend im Cloud-Explorer oder mithilfe von HDFS-Befehlen die problematische Datei zu lokalisieren. Im Allgemeinen handelt es sich dabei um eine `*-renamePending.json`-Datei. (Die `*-renamePending.json`-Datei ist eine Journaldatei, mit welcher der Vorgang der atomischen Umbenennung im WASB-Treiber implementiert wird. Aufgrund von Fehlern in dieser Implementierung können derartige Dateien nach Prozessabstürzen usw. weiterhin vorhanden sein.) Erzwingen Sie die Löschung dieser Datei im Cloud-Explorer oder durch Ausführen von HDFS-Befehlen.

Gelegentlich kann an diesem Speicherort auch eine temporäre Datei mit einem Namen wie `$$$.$$$` vorhanden sein. Sie müssen den HDFS-Befehl `ls` verwenden, um diese Datei anzuzeigen; im Cloud-Explorer wird die Datei nicht angezeigt. Führen Sie zum Löschen dieser Datei den HDFS-Befehl `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$` aus.

Nach dem Ausführen dieser Befehle sollte HMaster sofort gestartet werden.

---

## <a name="scenario-no-server-address-listed"></a>Szenario: Keine Serveradresse aufgelistet

### <a name="issue"></a>Problem

Möglicherweise wird eine Meldung angezeigt, die besagt, dass die `hbase: meta`-Tabelle nicht online ist. Bei Ausführung von `hbck` wird u. U. Folgendes gemeldet: `hbase: meta table replicaId 0 is not found on any region.` In den HMaster-Protokollen könnte diese Meldung angezeigt werden: `No server address listed in hbase: meta for region hbase: backup <region name>`.  

### <a name="cause"></a>Ursache

HMaster konnte nach einem HBase-Neustart nicht initialisiert werden.

### <a name="resolution"></a>Lösung

1. Geben Sie in der HBase-Shell die folgenden Befehle ein (ändern Sie ggf. die Istwerte):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Löschen Sie den Eintrag `hbase: namespace`. Bei diesem Eintrag kann es sich um den gleichen Fehler handeln, der auch beim Überprüfen der Tabelle `hbase: namespace` gemeldet wurde.

1. Starten Sie den aktiven HMaster von der Ambari-Benutzeroberfläche aus neu, um HBase hochzufahren.

1. Führen Sie in der HBase-Shell den folgenden Befehl aus, um alle Tabellen hochzufahren, die offline sind:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Szenario: java.io.IOException: Timedout

### <a name="issue"></a>Problem

Für HMaster tritt ein Timeout mit einer schwerwiegenden Ausnahme wie `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned` auf.

### <a name="cause"></a>Ursache

Dieses Problem kann auftreten, wenn viele Tabellen und Regionen vorhanden sind, die beim Neustart der HMaster-Dienste nicht geleert wurden. Das Timeout ist ein bekannter HMaster-Fehler. Allgemeine Cluster-Startaufgaben können sehr lange dauern. HMaster wird heruntergefahren, wenn die Namespacetabelle noch nicht zugewiesen ist. Die langwierigen Starttasks treten auf, wenn eine große Menge an nicht geleerten Daten vorhanden und ein Zeitlimit von fünf Minuten nicht ausreichend ist.

### <a name="resolution"></a>Lösung

1. Wechseln Sie auf der Apache Ambari-Benutzeroberfläche zu **HBase** > **Configs** . Fügen Sie in der benutzerdefinierten Datei `hbase-site.xml` die folgende Einstellung hinzu:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Starten Sie die erforderlichen Dienste neu (HMaster und möglicherweise andere HBase-Dienste).

---

## <a name="scenario-frequent-region-server-restarts"></a>Szenario: Häufige Neustarts des Regionsservers

### <a name="issue"></a>Problem

Knoten werden regelmäßig neu gestartet. Die Regionsserverprotokolle enthalten Einträge wie:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Ursache

Lange Unterbrechung der JVM-GC von `regionserver`. Die Pause führt dazu, dass `regionserver` nicht mehr reagiert und innerhalb des ZooKeeper-Sitzungstimeouts von 40 Sekunden keinen Heartbeat an HMaster senden kann. HMaster stuft `regionserver` als inaktiv ein, bricht `regionserver` ab, und führt einen Neustart aus.

### <a name="resolution"></a>Lösung

Ändern Sie das ZooKeeper-Sitzungstimeout. Hierzu muss nicht nur die `hbase-site`-Einstellung `zookeeper.session.timeout`, sondern auch die `zoo.cfg`-Einstellung `maxSessionTimeout` von ZooKeeper geändert werden.

1. Navigieren Sie auf der Ambari-Benutzeroberfläche zu **HBase > Configs > Settings** („HBase“ > „Konfigurationen“ > „Einstellungen“), und ändern Sie im Timeoutabschnitt den Wert des ZooKeeper-Sitzungstimeouts.

1. Navigieren Sie in der Ambari-Benutzeroberfläche zu **Zookeeper -> Configs -> Custom** `zoo.cfg` (Zookeeper -> Konfigurationen -> Benutzerdefiniert), und fügen Sie die folgende Einstellung hinzu, bzw. ändern Sie sie. Achten Sie darauf, dass der Wert mit dem HBase-Wert für `zookeeper.session.timeout` übereinstimmt.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Starten Sie die erforderlichen Dienste neu.

---

## <a name="scenario-log-splitting-failure"></a>Szenario: Fehler bei der Protokollaufteilung

### <a name="issue"></a>Problem

Fehler beim Starten von HMaster in einem HBase-Cluster.

### <a name="cause"></a>Ursache

Falsch konfigurierte HDFS- und HBase-Einstellungen für ein sekundäres Speicherkonto.

### <a name="resolution"></a>Lösung

Legen Sie „hbase.rootdir: wasb://@.blob.core.windows.net/hbase“ fest, und starten Sie die Dienste über Ambari neu.

---

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.

* Herstellen einer Verbindung mit [@AzureSupport](https://twitter.com/azuresupport), dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.

* Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie dazu auf der Menüleiste die Option **Support** aus, oder öffnen Sie den Hub **Hilfe und Support** . Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). Zugang zu Abonnementverwaltung und Abrechnungssupport ist in Ihrem Microsoft Azure-Abonnement enthalten. Technischer Support wird über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) bereitgestellt.