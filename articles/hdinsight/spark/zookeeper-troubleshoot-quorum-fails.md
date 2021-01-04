---
title: Fehler beim Bilden eines Quorums durch den Apache ZooKeeper-Server in Azure HDInsight
description: Fehler beim Bilden eines Quorums durch den Apache ZooKeeper-Server in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 05/20/2020
ms.openlocfilehash: 680ba2d0840b0f447f9a3cb99707b8357c68f14e
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545513"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Fehler beim Bilden eines Quorums durch den Apache ZooKeeper-Server in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme mit ZooKeeper-Instanzen in Azure HDInsight-Clustern beschrieben.

## <a name="symptoms"></a>Symptome

* Beide Ressourcen-Manager wechseln in den Standbymodus.
* Beide NameNodes befinden sich im Standbymodus.
* Aufgrund von ZooKeeper-Verbindungsfehlern treten bei Spark-, Hive- und Yarn-Aufträgen oder Hive-Abfragen Fehler auf.
* LLAP-Daemons können nicht in sicheren Spark- oder sicheren, interaktiven Hive-Clustern gestartet werden.

## <a name="sample-log"></a>Beispielprotokoll

Möglicherweise wird eine Fehlermeldung ähnlich der folgenden in Yarn-Protokollen („/var/log/hadoop-yarn/yarn/yarn-yarn*.log“ auf den Hauptknoten) angezeigt:

```output
2020-05-05 03:17:18.3916720|Lost contact with Zookeeper. Transitioning to standby in 10000 ms if connection is not reestablished.
Message
2020-05-05 03:17:07.7924490|Received RMFatalEvent of type STATE_STORE_FENCED, caused by org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth
...
2020-05-05 03:17:08.3890350|State store operation failed 
2020-05-05 03:17:08.3890350|Transitioning to standby state
```

## <a name="related-issues"></a>Verwandte Probleme

* Hochverfügbarkeitsdienste wie Yarn, NameNode und Livy können aus vielen Gründen ausfallen.
* Bestätigen Sie mithilfe der Protokolle, dass das Problem mit ZooKeeper-Verbindungen zusammenhängt.
* Stellen Sie sicher, dass das Problem wiederholt auftritt (verwenden Sie diese Lösungen nicht für Einzelfälle).
* Bei Aufträgen können aufgrund von ZooKeeper-Verbindungsproblemen vorübergehend Fehler auftreten.

## <a name="common-causes-for-zookeeper-failure"></a>Häufige Gründe für ZooKeeper-Fehler

* Hohe CPU-Auslastung auf den ZooKeeper-Servern
  * Wenn in der Ambari-Benutzeroberfläche eine anhaltende CPU-Auslastung von beinahe 100 % auf den ZooKeeper-Servern angezeigt wird, können die zu diesem Zeitpunkt offenen ZooKeeper-Sitzungen ablaufen und ein Timeout auftreten.
* ZooKeeper-Clients melden häufige Timeouts.
  * In den Protokollen für Resource Manager, NameNode u. a. werden Ihnen häufige Timeouts bei Clientverbindungen angezeigt.
  * Dies könnte zu einem Quorumverlust, häufigen Failovern und anderen Problemen führen.

## <a name="check-for-zookeeper-status"></a>Überprüfen des ZooKeeper-Status

* Suchen Sie die ZooKeeper-Server über die Datei „/etc/hosts“ oder die Ambari-Benutzeroberfläche.
* Führen Sie den folgenden Befehl aus.
  * `echo stat | nc <ZOOKEEPER_HOST_IP> 2181` (oder 2182)  
  * Port 2181 ist die Apache ZooKeeper-Instanz.
  * Port 2182 wird von der HDInsight-ZooKeeper-Instanz verwendet (um Hochverfügbarkeit für Dienste bereitzustellen, die standardmäßig nicht hochverfügbar sind).
  * Wenn der Befehl keine Ausgabe anzeigt, bedeutet dies, dass die ZooKeeper-Server nicht ausgeführt werden.
  * Wenn die Server ausgeführt werden, enthält das Ergebnis Statistiken zu Clientverbindungen und andere Statistiken.

```output
Zookeeper version: 3.4.6-8--1, built on 12/05/2019 12:55 GMT
Clients:
 /10.2.0.57:50988[1](queued=0,recved=715,sent=715)
 /10.2.0.57:46632[1](queued=0,recved=138340,sent=138347)
 /10.2.0.34:14688[1](queued=0,recved=264653,sent=353420)
 /10.2.0.52:49680[1](queued=0,recved=134812,sent=134814)
 /10.2.0.57:50614[1](queued=0,recved=19812,sent=19812)
 /10.2.0.56:35034[1](queued=0,recved=2586,sent=2586)
 /10.2.0.52:63982[1](queued=0,recved=72215,sent=72217)
 /10.2.0.57:53024[1](queued=0,recved=19805,sent=19805)
 /10.2.0.57:45126[1](queued=0,recved=19621,sent=19621)
 /10.2.0.56:41270[1](queued=0,recved=1348743,sent=1348788)
 /10.2.0.53:59097[1](queued=0,recved=72215,sent=72217)
 /10.2.0.56:41088[1](queued=0,recved=788,sent=802)
 /10.2.0.34:10246[1](queued=0,recved=19575,sent=19575)
 /10.2.0.56:40944[1](queued=0,recved=717,sent=717)
 /10.2.0.57:45466[1](queued=0,recved=19861,sent=19861)
 /10.2.0.57:59634[0](queued=0,recved=1,sent=0)
 /10.2.0.34:14704[1](queued=0,recved=264622,sent=353355)
 /10.2.0.57:42244[1](queued=0,recved=49245,sent=49248)

Latency min/avg/max: 0/3/14865
Received: 238606078
Sent: 239139381
Connections: 18
Outstanding: 0
Zxid: 0x1004f99be
Mode: follower
Node count: 133212
```

## <a name="cpu-load-peaks-up-every-hour"></a>CPU-Auslastung steigt stündlich auf Spitzenwerte

* Melden Sie sich beim ZooKeeper-Server an, und überprüfen Sie „/etc/crontab“.
* Wenn zu diesem Zeitpunkt stündliche Aufträge ausgeführt werden, können Sie die Startzeit auf verschiedenen ZooKeeper-Servern zufällig festlegen.
  
## <a name="purging-old-snapshots"></a>Löschen von alten Momentaufnahmen

* ZooKeeper-Instanzen sind so konfiguriert, dass alte Momentaufnahmen automatisch gelöscht werden.
* Standardmäßig werden die letzten 30 Momentaufnahmen beibehalten.
* Die Anzahl der beibehaltenen Momentaufnahmen wird durch den Konfigurationsschlüssel `autopurge.snapRetainCount` gesteuert. Diese Eigenschaft befindet sich in den folgenden Dateien:
  * `/etc/zookeeper/conf/zoo.cfg` für Hadoop-ZooKeeper
  * `/etc/hdinsight-zookeeper/conf/zoo.cfg` für HDInsight-ZooKeeper
* Legen Sie `autopurge.snapRetainCount` auf den Wert 3 fest, und starten Sie die ZooKeeper-Server neu.
  * Die Konfiguration von Hadoop-ZooKeeper kann aktualisiert und der Dienst über Ambari neu gestartet werden.
  * Manuelles Anhalten und Neustarten von HDInsight-ZooKeeper
    * `sudo lsof -i :2182` gibt Ihnen die Prozess-ID, die Sie beenden sollen.
    * `sudo python /opt/startup_scripts/startup_hdinsight_zookeeper.py`
* Löschen Sie Momentaufnahmen nicht manuell – dies könnte zu einem Datenverlust führen.

## <a name="cancelledkeyexception-in-the-zookeeper-server-log-doesnt-require-snapshot-cleanup"></a>CancelledKeyException im ZooKeeper-Serverprotokoll erfordert keine Momentaufnahmenbereinigung

* Diese Ausnahme wird auf den ZooKeeper-Servern angezeigt (/var/log/zookeeper/zookeeper-zookeeper-* oder /var/log/hdinsight-zookeeper/zookeeper*-Dateien)
* Diese Ausnahme bedeutet in der Regel, dass der Client nicht mehr aktiv ist und der Server keine Nachricht senden kann.
* Diese Ausnahme weist zudem darauf hin, dass der ZooKeeper-Client Sitzungen vorzeitig beendet.
* Suchen Sie nach den anderen in diesem Dokument beschriebenen Symptomen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

- Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.
- Herstellen einer Verbindung mit [@AzureSupport](https://twitter.com/azuresupport), dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.
- Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie dazu auf der Menüleiste die Option **Support** aus, oder öffnen Sie den Hub **Hilfe und Support** . Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). Zugang zu Abonnementverwaltung und Abrechnungssupport ist in Ihrem Microsoft Azure-Abonnement enthalten. Technischer Support wird über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) bereitgestellt.