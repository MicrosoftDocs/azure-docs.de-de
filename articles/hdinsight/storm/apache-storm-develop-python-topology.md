---
title: Apache Storm mit Python-Komponenten – Azure HDInsight
description: Erfahren Sie, wie Sie eine Apache Storm-Topologie erstellen, die Python-Komponenten verwendet.
author: hrasheed-msft
ms.reviewer: jasonh
keywords: Apache Storm Python
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: hrasheed
ms.openlocfilehash: 3b808a12c7a669a272bf6a1bbb253ed6b4625288
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078214"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Entwickeln von Apache Storm-Topologien mit Python in HDInsight

Erfahren Sie, wie Sie eine [Apache Storm](https://storm.apache.org/)-Topologie erstellen, die Python-Komponenten verwendet. Apache Storm unterstützt mehrere Sprachen und ermöglicht sogar das Kombinieren von Komponenten in verschiedenen Sprachen in einer Topologie. Mit dem (mit Storm 0.10.0 eingeführten) [Flux](https://storm.apache.org/releases/current/flux.html)-Framework können Sie problemlos Lösungen erstellen, die Python-Komponenten verwenden.

> [!IMPORTANT]  
> Die Informationen in diesem Dokument wurden mit Storm unter HDInsight 3.6 getestet. 

Der Code für dieses Projekt ist verfügbar unter [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>Voraussetzungen

* Python 2.7 oder höher

* Java JDK 1.8 oder höher

* [Apache Maven 3](https://maven.apache.org/download.cgi)

* (Optional) Eine lokale Storm-Entwicklungsumgebung. Eine lokale Storm-Umgebung ist nur erforderlich, wenn die Topologie lokal ausgeführt werden soll. Weitere Informationen finden Sie unter [Setting up a development environment](https://storm.apache.org/releases/1.1.2/Setting-up-development-environment.html) (Einrichten einer Entwicklungsumgebung).

## <a name="storm-multi-language-support"></a>Storm-Unterstützung für mehrere Sprachen

Apache Storm wurde für den Einsatz mit Komponenten entwickelt, die in einer beliebigen Programmiersprache geschrieben wurden. Die Komponenten müssen mit der [Thrift-Definition für Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift) umgehen können. Für Python wird ein Modul als Teil des Apache Storm-Projekts bereitgestellt, das Ihnen eine problemlose Verknüpfung mit Storm ermöglicht. Sie finden dieses Modul unter [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Storm ist ein Java-Prozess, der auf der Java Virtual Machine (JVM) ausgeführt wird. In anderen Sprachen geschriebene Komponenten werden als Unterprozesse ausgeführt. Storm kommuniziert mit diesen Unterprozessen über JSON-Meldungen, die über „stdin/stdout“ gesendet werden. Weitere Informationen zur Kommunikation zwischen Komponenten finden Sie in der Dokumentation zum [Multi-Lang Protocol](https://storm.apache.org/documentation/Multilang-protocol.html) .

## <a name="python-with-the-flux-framework"></a>Python mit dem Flux-Framework

Mit dem Flux-Framework können Sie Storm-Topologien von den Komponenten getrennt definieren. Das Flux-Framework verwendet YAML, um die Storm-Topologie zu definieren. Der folgende Text zeigt, wie im YAML-Dokument auf eine Python-Komponente verwiesen wird:

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

Die Klasse `FluxShellSpout` dient zum Starten des `sentencespout.py`-Skripts, das den Spout implementiert.

Flux erwartet, dass sich die Python-Skripts im `/resources`-Verzeichnis der JAR-Datei befinden, die die Topologie enthält. Also sind die Python-Skripts in diesem Beispiel im `/multilang/resources`-Verzeichnis gespeichert. Die `pom.xml` bezieht diese Datei mit folgendem XML-Code ein:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Wie bereits erwähnt, gibt es eine `storm.py`-Datei, die die Thrift-Definition für Storm implementiert. Das Flux-Framework bezieht `storm.py` automatisch ein, wenn das Projekt erstellt wird, Sie müssen sich also nicht um die Einbeziehung kümmern.

## <a name="build-the-project"></a>Erstellen des Projekts

Führen Sie im Stammverzeichnis des Projekts folgenden Befehl aus:

```bash
mvn clean compile package
```

Dieser Befehl erstellt eine `target/WordCount-1.0-SNAPSHOT.jar`-Datei, die die kompilierte Topologie enthält.

## <a name="run-the-topology-locally"></a>Lokales Ausführen der Topologie

Verwenden Sie den folgenden Befehl, um die Topologie lokal auszuführen:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Dieser Befehl setzt eine lokale Storm-Entwicklungsumgebung voraus. Weitere Informationen finden Sie unter [Setting up a development environment](https://storm.apache.org/releases/current/Setting-up-development-environment.html) (Einrichten einer Entwicklungsumgebung).

Sobald die Topologie startet, gibt er Informationen über die lokale Konsole aus, die folgendem Text ähneln:


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


Verwenden Sie zum Beenden der Topologie __STRG+C__.

## <a name="run-the-storm-topology-on-hdinsight"></a>Ausführen der Storm-Topologie unter HDInsight

1. Verwenden Sie den folgenden Befehl, um die `WordCount-1.0-SNAPSHOT.jar`-Datei in Ihren Cluster vom Typ „Storm in HDInsight“ zu kopieren.

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Ersetzen Sie `sshuser` durch den SSH-Benutzer Ihres Clusters. Ersetzen Sie `mycluster` durch den Namen des Clusters. Möglicherweise werden Sie zur Eingabe des Kennworts für den SSH-Benutzer aufgefordert.

    Weitere Informationen zur Verwendung von SSH und SCP finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Wenn die Datei hochgeladen ist, stellen Sie über SSH eine Verbindung mit dem Cluster her:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Verwenden Sie in der SSH-Sitzung den folgenden Befehl, um die Topologie im Cluster zu starten:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. Sie können in der Storm-Benutzeroberfläche die Topologie im Cluster anzeigen. Die Storm-Benutzeroberfläche befindet sich unter https://mycluster.azurehdinsight.net/stormui. Ersetzen Sie `mycluster` durch den Namen Ihres Clusters.

> [!NOTE]  
> Eine gestartete Storm-Topologie wird ausgeführt, bis sie beendet wird. Sie können die Topologie mithilfe einer der folgenden Methoden beenden:
>
> * Der Befehl `storm kill TOPOLOGYNAME` über die Befehlszeile
> * Mit der **Kill**-Schaltfläche in der Storm-Benutzeroberfläche.


## <a name="next-steps"></a>Nächste Schritte

In den folgenden Dokumenten werden weitere Möglichkeiten zur Verwendung von Python mit HDInsight beschrieben:

* [Verwenden von benutzerdefinierten Python-Funktionen (UDFs) in Apache Pig und Apache Hive](../hadoop/python-udf-hdinsight.md)
