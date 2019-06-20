---
title: Problembehandlung bei HBase mit Azure HDInsight
description: Hier erhalten Sie Antworten auf häufig gestellte Fragen zum Arbeiten mit HBase und Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 6ba17a3839390ed5fe503a6fe57b63d8fb119138
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64713499"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Problembehandlung bei Apache HBase mit Azure HDInsight

Lernen Sie die wichtigsten Probleme und ihre Lösungen bei der Arbeit mit Apache HBase-Nutzlasten in Apache Ambari kennen.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Wie führe ich hbck-Befehle mit mehreren nicht zugewiesenen Regionen aus?

Eine Fehlermeldung, die beim Ausführen des Befehls `hbase hbck` häufig ausgegeben wird, lautet „multiple regions being unassigned or holes in the chain of regions“ (mehrere Regionen sind nicht zugewiesen, oder die Kette der Regionen weist Lücken auf).

In der Benutzeroberfläche von HBase-Master wird die Anzahl der Regionen angezeigt, die über alle Regionsserver hinweg nicht ausgeglichen sind. Sie können dann den Befehl `hbase hbck` ausführen, um Lücken in der Kette der Regionen anzuzeigen.

Lücken können durch Regionen im Offlinezustand bewirkt werden; korrigieren Sie daher zuerst die Zuweisungen. 

Führen Sie die folgenden Schritte aus, um die nicht zugewiesenen Regionen wieder in einen normalen Zustand zu versetzen:

1. Melden Sie sich mithilfe von SSH beim HDInsight-HBase-Cluster an.
2. Führen Sie den Befehl `hbase zkcli` aus, um eine Verbindung mit der Apache ZooKeeper-Shell herzustellen.
3. Führen Sie den Befehl `rmr /hbase/regions-in-transition` oder den Befehl `rmr /hbase-unsecure/regions-in-transition` aus.
4. Verwenden Sie zum Schließen der `hbase zkcli`-Shell den Befehl `exit`.
5. Öffnen Sie die Apache Ambari-Benutzeroberfläche, und starten Sie anschließend den Active HBase-Master-Dienst neu.
6. Führen Sie den Befehl `hbase hbck` erneut aus (ohne Optionen). Überprüfen Sie die Ausgabe des Befehls, um sich zu vergewissern, dass alle Regionen zugewiesen sind.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Wie behebe ich Timeoutprobleme mit hbck-Befehlen für Regionszuweisungen?

### <a name="issue"></a>Problem

Eine mögliche Ursache für Timeoutprobleme beim Verwenden des Befehls `hbck` kann darin bestehen, dass sich verschiedene Regionen über einen langen Zeitraum im „Übergangszustand“ befinden. Diese Regionen werden in der HBase-Master-Benutzeroberfläche als offline angezeigt. Aufgrund der großen Anzahl von Regionen, die den Übergang versuchen, könnte ein HBase Master-Timeout aufgetreten sein, sodass HBase Master diese Regionen nicht wieder in den Onlinezustand versetzen kann.

### <a name="resolution-steps"></a>Lösungsschritte

1. Melden Sie sich mithilfe von SSH beim HDInsight-HBase-Cluster an.
2. Führen Sie den Befehl `hbase zkcli` aus, um eine Verbindung mit der Apache ZooKeeper-Shell herzustellen.
3. Führen Sie den Befehl `rmr /hbase/regions-in-transition` oder den Befehl `rmr /hbase-unsecure/regions-in-transition` aus.
4. Verwenden Sie zum Schließen der `hbase zkcli`-Shell den Befehl `exit`.
5. Starten Sie in der Ambari-Benutzeroberfläche den Active HBase-Master-Dienst neu.
6. Führen Sie den Befehl `hbase hbck -fixAssignments` erneut aus.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Wie erzwinge ich das Deaktivieren des abgesicherten HDFS-Modus in einem Cluster?

### <a name="issue"></a>Problem

Das lokale Apache HDFS (Hadoop Distributed File System) ist im abgesicherten Modus auf dem HDInsight-Cluster hängen geblieben.

### <a name="detailed-description"></a>Detaillierte Beschreibung

Dieser Fehler kann durch einen Ausfall beim Ausführen des folgenden HDFS-Befehls verursacht werden:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

Der beim Ausführen des Befehls angezeigte Fehler kann wie folgt aussehen:

```apache
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:4010)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1102)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:630)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:640)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2313)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2309)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1724)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2307)
        at org.apache.hadoop.ipc.Client.getRpcResponse(Client.java:1552)
        at org.apache.hadoop.ipc.Client.call(Client.java:1496)
        at org.apache.hadoop.ipc.Client.call(Client.java:1396)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:233)
        at com.sun.proxy.$Proxy10.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.mkdirs(ClientNamenodeProtocolTranslatorPB.java:603)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:278)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:194)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:176)
        at com.sun.proxy.$Proxy11.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.DFSClient.primitiveMkdir(DFSClient.java:3061)
        at org.apache.hadoop.hdfs.DFSClient.mkdirs(DFSClient.java:3031)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1162)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1158)
        at org.apache.hadoop.fs.FileSystemLinkResolver.resolve(FileSystemLinkResolver.java:81)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirsInternal(DistributedFileSystem.java:1158)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirs(DistributedFileSystem.java:1150)
        at org.apache.hadoop.fs.FileSystem.mkdirs(FileSystem.java:1898)
        at org.apache.hadoop.fs.shell.Mkdir.processNonexistentPath(Mkdir.java:76)
        at org.apache.hadoop.fs.shell.Command.processArgument(Command.java:273)
        at org.apache.hadoop.fs.shell.Command.processArguments(Command.java:255)
        at org.apache.hadoop.fs.shell.FsCommand.processRawArguments(FsCommand.java:119)
        at org.apache.hadoop.fs.shell.Command.run(Command.java:165)
        at org.apache.hadoop.fs.FsShell.run(FsShell.java:297)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:90)
        at org.apache.hadoop.fs.FsShell.main(FsShell.java:350)
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

### <a name="probable-cause"></a>Wahrscheinliche Ursache

Der HDInsight-Cluster wurde zentral auf sehr wenige Knoten herunterskaliert. Die Anzahl der Knoten ist kleiner als der oder nahezu gleich dem HDFS-Replikationsfaktor.

### <a name="resolution-steps"></a>Lösungsschritte 

1. Rufen Sie den HDFS-Status auf dem HDInsight-Cluster durch Ausführen der folgenden Befehle ab:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   ```

   ```apache
   hdiuser@hn0-spark2:~$ hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   Safe mode is ON
   Configured Capacity: 3372381241344 (3.07 TB)
   Present Capacity: 3138625077248 (2.85 TB)
   DFS Remaining: 3102710317056 (2.82 TB)
   DFS Used: 35914760192 (33.45 GB)
   DFS Used%: 1.14%
   Under replicated blocks: 0
   Blocks with corrupt replicas: 0
   Missing blocks: 0
   Missing blocks (with replication factor 1): 0

   -------------------------------------------------
   Live datanodes (8):

   Name: 10.0.0.17:30010 (10.0.0.17)
   Hostname: 10.0.0.17
   Decommission Status : Normal
   Configured Capacity: 421547655168 (392.60 GB)
   DFS Used: 5288128512 (4.92 GB)
   Non DFS Used: 29087272960 (27.09 GB)
   DFS Remaining: 387172253696 (360.58 GB)
   DFS Used%: 1.25%
   DFS Remaining%: 91.85%
   Configured Cache Capacity: 0 (0 B)
   Cache Used: 0 (0 B)
   Cache Remaining: 0 (0 B)
   Cache Used%: 100.00%
   Cache Remaining%: 0.00%
   Xceivers: 2
   Last contact: Wed Apr 05 16:22:00 UTC 2017
   ...

   ```
2. Sie können auch die HDFS-Integrität auf dem HDInsight-Cluster mithilfe der folgenden Befehle überprüfen:

   ```apache
   hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
   ```

   ```apache
   Connecting to namenode via http://hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net:30070/fsck?ugi=hdiuser&path=%2F
   FSCK started by hdiuser (auth:SIMPLE) from /10.0.0.22 for path / at Wed Apr 05 16:40:28 UTC 2017
   ....................................................................................................

   ....................................................................................................
   ..................Status: HEALTHY
   Total size:    9330539472 B
   Total dirs:    37
   Total files:   2618
   Total symlinks:                0 (Files currently being written: 2)
   Total blocks (validated):      2535 (avg. block size 3680686 B)
   Minimally replicated blocks:   2535 (100.0 %)
   Over-replicated blocks:        0 (0.0 %)
   Under-replicated blocks:       0 (0.0 %)
   Mis-replicated blocks:         0 (0.0 %)
   Default replication factor:    3
   Average block replication:     3.0
   Corrupt blocks:                0
   Missing replicas:              0 (0.0 %)
   Number of data-nodes:          8
   Number of racks:               1
   FSCK ended at Wed Apr 05 16:40:28 UTC 2017 in 187 milliseconds

   The filesystem under path '/' is HEALTHY
   ```

3. Wenn festgestellt wird, dass keine Blöcke fehlen, beschädigt oder unterrepliziert sind, bzw. dass diese Blöcke ignoriert werden können, führen Sie den folgenden Befehl aus, um den Namensknoten aus dem abgesicherten Modus herauszunehmen:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Wie behebe ich JDBC- oder SQLLine-Konnektivitätsprobleme mit Apache Phoenix?

### <a name="resolution-steps"></a>Lösungsschritte

Um eine Verbindung mit Apache Phoenix herzustellen, müssen Sie die IP-Adresse eines aktiven Apache ZooKeeper-Knotens angeben. Stellen Sie sicher, dass der ZooKeeper-Dienst, mit dem „sqlline.py“ eine Verbindung herstellen soll, ausgeführt wird.
1. Melden Sie sich mithilfe von SSH beim HDInsight-Cluster an.
2. Geben Sie den folgenden Befehl ein:
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > Sie können die IP-Adresse des aktiven ZooKeeper-Knotens aus der Ambari-Benutzeroberfläche abrufen. Wechseln Sie zu **HBase** > **Quick Links** > **ZK\* (Active)**  > **ZooKeeper Info**. 

3. Wenn „sqlline.py“ versucht, eine Verbindung mit Phoenix herzustellen und kein Timeout auftritt, führen Sie den folgenden Befehl aus, um die Verfügbarkeit und Integrität von Phoenix zu überprüfen:

   ```apache
           !tables
           !quit
   ```      
4. Wenn dieser Befehl erfolgreich ausgeführt wird, liegt kein Problem vor. Die vom Benutzer angegebene IP-Adresse ist möglicherweise falsch. Wenn der Befehl jedoch über einen längeren Zeitraum pausiert und anschließend der folgende Fehler ausgegeben wird, fahren Sie mit Schritt 5 fort.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Führen Sie die folgenden Befehle vom Hauptknoten (hn0) aus, um den Zustand der Phoenix-Tabelle SYSTEM.CATALOG zu diagnostizieren:

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   Der Befehl sollte eine Fehlermeldung ähnlich der Folgenden zurückgeben: 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. Führen Sie auf der Apache Ambari-Benutzeroberfläche die folgenden Schritte aus, um den HMaster-Dienst auf allen ZooKeeper-Knoten neu zu starten:

    1. Wechseln Sie im Abschnitt **Summary** von HBase zu **HBase** > **Active HBase Master**. 
    2. Starten Sie im Abschnitt **Components** den HBase-Master-Dienst neu.
    3. Wiederholen Sie diese Schritte für die verbleibenden **Standby HBase Master**-Dienste. 

Es kann bis zu fünf Minuten dauern, bis der HBase-Master-Dienst stabilisiert und der Wiederherstellungsvorgang abgeschlossen ist. Warten Sie einige Minuten, und führen Sie die sqlline.py-Befehle erneut aus, um sich zu vergewissern, dass die Tabelle SYSTEM.CATALOG aufgerufen wurde und abgefragt werden kann. 

Sobald sich die Tabelle „SYSTEM.CATALOG“ wieder im normalen Zustand befindet, sollte das Problem der Konnektivität mit Phoenix automatisch gelöst werden.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Was ist die Ursache dafür, dass ein Masterserver nicht startet?

### <a name="error"></a>Error 

Ein Fehler bei der atomischen Umbenennung tritt auf.

### <a name="detailed-description"></a>Detaillierte Beschreibung

Während des Startvorgangs schließt HMaster eine Vielzahl von Initialisierungsschritten ab. Hierzu zählt das Verschieben von Daten aus dem Scratch-Ordner (.tmp) in den Datenordner. HMaster untersucht zudem den Ordner mit write-ahead-Protokollen (WALs), um festzustellen, ob nicht reagierende Regionsserver vorhanden sind usw. 

Während des Starts führt HMaster einen grundlegenden `list`-Befehl für diese Ordner aus. Findet HMaster zu einem beliebigen Zeitpunkt eine unerwartete Datei in einem dieser Ordner, wird eine Ausnahme ausgelöst und kein Start ausgeführt.  

### <a name="probable-cause"></a>Wahrscheinliche Ursache

Versuchen Sie, in den Regionsserverprotokollen die Zeitachse der Dateierstellung zu ermitteln, und bestimmen Sie dann, ob ungefähr zum Zeitpunkt der Erstellung der betreffenden Datei ein Prozessabsturz aufgetreten ist. (Wenden Sie sich an den HBase-Support, um Unterstützung dabei zu erhalten.) So können wir robustere Mechanismen bereitstellen, um diesen Fehler künftig zu vermeiden und das ordnungsgemäße Herunterfahren von Prozessen sicherzustellen.

### <a name="resolution-steps"></a>Lösungsschritte

Überprüfen Sie die Aufrufliste und versuchen Sie festzustellen, welcher Ordner das Problem verursachen könnte (dies können beispielsweise der Ordner mit WALs oder der TMP-Ordner sein). Versuchen Sie anschließend im Cloud-Explorer oder mithilfe von HDFS-Befehlen die problematische Datei zu lokalisieren. Im Allgemeinen handelt es sich um eine Datei „\*-renamePending.json“ . (Die Datei „\*-renamePending.json“ ist eine Journaldatei, mit welcher der Vorgang der atomischen Umbenennung im WASB-Treiber implementiert wird. Aufgrund von Fehlern in dieser Implementierung können derartige Dateien nach Prozessabstürzen usw. weiterhin vorhanden sein.) Erzwingen Sie die Löschung dieser Datei im Cloud-Explorer oder durch Ausführen von HDFS-Befehlen. 

Gelegentlich kann auch eine temporäre Datei mit einem Namen wie *$$$.$$$* an diesem Speicherort vorhanden sein. Sie müssen den HDFS-Befehl `ls` verwenden, um diese Datei anzuzeigen; im Cloud-Explorer wird die Datei nicht angezeigt. Führen Sie zum Löschen dieser Datei den HDFS-Befehl `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$` aus.  

Nach dem Ausführen dieser Befehle sollte HMaster sofort gestartet werden. 

### <a name="error"></a>Error

In *hbase: meta* ist für Region xxx keine Serveradresse aufgelistet.

### <a name="detailed-description"></a>Detaillierte Beschreibung

Möglicherweise wird eine Meldung im Linux-Cluster angezeigt, in der darauf hingewiesen wird, dass die Tabelle *hbase: meta* nicht online ist. Beim Ausführen von `hbck` kann folgende Meldung angezeigt werden: „hbase: meta table replicaId 0 is not found on any region“ (hbase: Metatabelle mit replicaId 0 wurde für keine Region gefunden). Das Problem besteht möglicherweise darin, dass HMaster nach dem Neustarten von HBase nicht initialisiert werden konnte. In den HMaster-Protokollen könnte diese Meldung angezeigt werden: „Keine Serveradresse in ‚hbase:meta‘ aufgelistet für Region ‚hbase: backup \<Regionsname\>‘“.  

### <a name="resolution-steps"></a>Lösungsschritte

1. Geben Sie in der HBase-Shell die folgenden Befehle ein (ändern Sie ggf. die Istwerte):  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Löschen Sie den Eintrag *hbase: namespace*. Bei diesem Eintrag kann es sich um den gleichen Fehler handeln, der auch beim Überprüfen der Tabelle *hbase: namespace* gemeldet wurde.

3. Starten Sie in der Ambari-Benutzeroberfläche den Active HMaster-Dienst neu, um HBase hochzufahren.  

4. Führen Sie in der HBase-Shell den folgenden Befehl aus, um alle Tabellen hochzufahren, die offline sind:

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>Zusätzliche Lektüre

[HBase-Tabelle kann nicht verarbeitet werden](https://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Error

HMaster Timeout mit schwerwiegendem Ausnahmefehler ähnlich „java.io.IOException: Timeout 300.000ms beim Warten auf Zuweisung der Namespacetabelle“.

### <a name="detailed-description"></a>Detaillierte Beschreibung

Dieses Problem kann auftreten, wenn viele Tabellen und Regionen vorhanden sind, die beim Neustart der HMaster-Dienste nicht geleert wurden. Der Neustart kann fehlschlagen, und die vorherige Fehlermeldung wird angezeigt.  

### <a name="probable-cause"></a>Wahrscheinliche Ursache

Dies ist ein bekanntes Problem mit dem HMaster-Dienst. Allgemeine Cluster-Startaufgaben können sehr lange dauern. HMaster wird heruntergefahren, da die Namespace-Tabelle noch nicht zugewiesen ist. Dies ist nur in Szenarien der Fall, in denen eine große Menge an nicht geleerten Daten vorhanden ist, wobei ein Zeitlimit von fünf Minuten nicht ausreichend ist.
  
### <a name="resolution-steps"></a>Lösungsschritte

1. Wechseln Sie auf der Apache Ambari-Benutzeroberfläche zu **HBase** > **Configs**. Fügen Sie in der benutzerdefinierten Datei „hbase-site.xml“ die folgende Einstellung hinzu: 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Starten Sie die erforderlichen Dienste neu (HMaster und möglicherweise andere HBase-Dienste).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Was verursacht einen Neustartfehler auf einem Regionsserver?

### <a name="issue"></a>Problem

Ein Neustartfehler auf einem Regionsserver kann durch Befolgen der folgenden bewährten Methoden vermieden werden. Es wird empfohlen, hohe Workload-Aktivitäten zu unterbrechen, wenn Sie einen Neustart von HBase-Regionsservern planen. Wenn die Anwendung während des Herunterfahrens weiterhin versucht, Verbindungen mit Regionsservern herzustellen, wird der Regionsserver-Neustart um einige Minuten verlangsamt. Zudem empfiehlt es sich, zuvor alle Tabellen zu leeren. Eine Referenz zum Leeren von Tabellen finden Sie unter [HDInsight HBase: Verbessern der Neustartzeit für den Apache HBase-Cluster durch Leeren der Tabellen](https://web.archive.org/web/20190112153155/ https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Wenn Sie den Neustartvorgang auf HBase-Regionsservern über die Apache Ambari-Benutzeroberfläche initiieren, stellen Sie sofort fest, dass die Regionsserver heruntergefahren wurden, aber nicht sofort neu gestartet werden. 

Die folgenden Vorgänge laufen im Hintergrund ab: 

1. Der Ambari-Agent sendet eine Beendigungsanforderung an den Regionsserver.
2. Der Ambari-Agent wartet 30 Sekunden auf das ordnungsgemäße Herunterfahren des Regionsservers. 
3. Wenn Ihre Anwendung weiterhin eine Verbindung mit dem Regionsserver unterhält, wird der Server nicht sofort heruntergefahren. Das Zeitlimit von 30 Sekunden läuft ab, bevor der Server heruntergefahren wird. 
4. Nach 30 Sekunden sendet der Ambari-Agent einen Befehl zum Erzwingen des Beendens (`kill -9`) an den Regionsserver. Dies wird im Ambari-Agent-Protokoll angezeigt (im Verzeichnis „/var/log/“ des jeweiligen Workerknotens):

   ```apache
           2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
           est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
           f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
           -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
           2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
           /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
           PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
           nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
           2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
           2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
   ```
   Aufgrund dieses abrupten Herunterfahrens wird der Port, der dem Prozess zugeordnet ist, möglicherweise nicht freigegeben, obwohl der Regionsserverprozess beendet wird. Dieser Umstand kann beim Starten des Servers zu einer AddressBindException führen, wie in den folgenden Protokollen ersichtlich ist. Dies können Sie in „region-server.log“ im Verzeichnis „/var/log/hbase“ der Workerknoten überprüfen, auf denen beim Starten des Regionsservers ein Fehler auftritt. 

   ```apache

   2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
   java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
   at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
   at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
        
   Caused by: java.lang.reflect.InvocationTargetException
   at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
   at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
   at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
   at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
   ... 5 more
        
   Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
   at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
   at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
   at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
   ... 10 more
        
   Caused by: java.net.BindException: Address already in use
   at sun.nio.ch.Net.bind0(Native Method)
   at sun.nio.ch.Net.bind(Net.java:463)
   at sun.nio.ch.Net.bind(Net.java:455)
   at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
   at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
   ... 15 more
   ```

### <a name="resolution-steps"></a>Lösungsschritte

1. Versuchen Sie, die Last auf den HBase-Regionsservern zu reduzieren, bevor Sie einen Neustart initiieren. 
2. Sie können auch versuchen (wenn Schritt 1 nicht erfolgreich war), die Regionsserver auf den Workerknoten mithilfe der folgenden Befehle manuell neu zu starten:

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

### <a name="see-also"></a>Siehe auch
[Beheben von Problemen mit Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
