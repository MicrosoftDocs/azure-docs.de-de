---
title: Apache Storm mit Python-Komponenten – Azure HDInsight
description: Erfahren Sie, wie Sie eine Apache Storm-Topologie erstellen, die Python-Komponenten in Azure HDInsight verwendet.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-python
ms.date: 12/16/2019
ms.openlocfilehash: e62e2583f8a4d3872a1342f0f702a1913f733e7f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545462"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Entwickeln von Apache Storm-Topologien mit Python in HDInsight

Erfahren Sie, wie Sie eine [Apache Storm](https://storm.apache.org/)-Topologie erstellen, die Python-Komponenten verwendet. Apache Storm unterstützt mehrere Sprachen und ermöglicht sogar das Kombinieren von Komponenten in verschiedenen Sprachen in einer Topologie. Mit dem (mit Storm 0.10.0 eingeführten) [Flux](https://storm.apache.org/releases/current/flux.html)-Framework können Sie problemlos Lösungen erstellen, die Python-Komponenten verwenden.

> [!IMPORTANT]  
> Die Informationen in diesem Dokument wurden mit Storm unter HDInsight 3.6 getestet.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Apache Storm-Cluster in HDInsight. Siehe [Erstellen von Apache Hadoop-Clustern im Azure-Portal](../hdinsight-hadoop-create-linux-clusters-portal.md), und wählen Sie **Storm** für **Clustertyp** aus.

* Eine lokale Storm-Entwicklungsumgebung (optional). Eine lokale Storm-Umgebung ist nur erforderlich, wenn die Topologie lokal ausgeführt werden soll. Weitere Informationen finden Sie unter [Setting up a development environment](https://storm.apache.org/releases/current/Setting-up-development-environment.html) (Einrichten einer Entwicklungsumgebung).

* [Python 2.7 oder höher](https://www.python.org/downloads/)

* [Java Developer Kit (JDK), Version 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* Ordnungsgemäße [Installation](https://maven.apache.org/install.html) von [Apache Maven](https://maven.apache.org/download.cgi) (gemäß Apache).  Maven ist ein Projekterstellungssystem für Java-Projekte.

## <a name="storm-multi-language-support"></a>Storm-Unterstützung für mehrere Sprachen

Apache Storm wurde für den Einsatz mit Komponenten entwickelt, die in einer beliebigen Programmiersprache geschrieben wurden. Die Komponenten müssen die Thrift-Definition für Storm verarbeiten können. Für Python wird ein Modul als Teil des Apache Storm-Projekts bereitgestellt, das Ihnen eine problemlose Verknüpfung mit Storm ermöglicht. Sie finden dieses Modul unter [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Storm ist ein Java-Prozess, der auf der Java Virtual Machine (JVM) ausgeführt wird. In anderen Sprachen geschriebene Komponenten werden als Unterprozesse ausgeführt. Storm kommuniziert mit diesen Unterprozessen über JSON-Meldungen, die über „stdin/stdout“ gesendet werden. Weitere Informationen zur Kommunikation zwischen Komponenten finden Sie in der Dokumentation zum [Multi-Lang Protocol](https://storm.apache.org/releases/current/Multilang-protocol.html) .

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

Wie bereits erwähnt, wird die Thrift-Definition für Storm mit der Datei `storm.py` implementiert. Das Flux-Framework bezieht `storm.py` automatisch ein, wenn das Projekt erstellt wird, Sie müssen sich also nicht um die Einbeziehung kümmern.

## <a name="build-the-project"></a>Erstellen des Projekts

1. Laden Sie das Projekt unter [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) herunter.

1. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie zum Stammverzeichnis des Projekts: `hdinsight-python-storm-wordcount-master`. Geben Sie den folgenden Befehl ein:

    ```cmd
    mvn clean compile package
    ```

    Dieser Befehl erstellt eine `target/WordCount-1.0-SNAPSHOT.jar`-Datei, die die kompilierte Topologie enthält.

## <a name="run-the-storm-topology-on-hdinsight"></a>Ausführen der Storm-Topologie unter HDInsight

1. Verwenden Sie einen [SSH-Befehl](../hdinsight-hadoop-linux-use-ssh-unix.md), um die Datei `WordCount-1.0-SNAPSHOT.jar` in den Storm-Cluster in HDInsight zu kopieren. Bearbeiten Sie den folgenden Befehl, indem Sie CLUSTERNAME durch den Namen Ihres Clusters ersetzen, und geben Sie den Befehl dann ein:

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Wenn die Datei hochgeladen ist, stellen Sie über SSH eine Verbindung mit dem Cluster her:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Verwenden Sie in der SSH-Sitzung den folgenden Befehl, um die Topologie im Cluster zu starten:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    Eine gestartete Storm-Topologie wird ausgeführt, bis sie beendet wird.

1. Auf der Storm-Benutzeroberfläche können Sie die Topologie im Cluster anzeigen. Die Storm-Benutzeroberfläche befindet sich unter `https://CLUSTERNAME.azurehdinsight.net/stormui`. Ersetzen Sie `CLUSTERNAME` durch den Namen Ihres Clusters.

1. Beenden Sie die Storm-Topologie. Verwenden Sie den folgenden Befehl, um die Topologie im Cluster zu beenden:

    ```bash
    storm kill wordcount
    ```

    Alternativ können Sie dazu auch die Storm-Benutzeroberfläche verwenden. Wählen Sie unter **Topology actions** (Topologieaktionen) für die Topologie die Option **Kill** (Beenden) aus.

## <a name="run-the-topology-locally"></a>Lokales Ausführen der Topologie

Verwenden Sie den folgenden Befehl, um die Topologie lokal auszuführen:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Dieser Befehl setzt eine lokale Storm-Entwicklungsumgebung voraus. Weitere Informationen finden Sie unter [Setting up a development environment](https://storm.apache.org/releases/current/Setting-up-development-environment.html) (Einrichten einer Entwicklungsumgebung).

Sobald die Topologie startet, gibt er Informationen über die lokale Konsole aus, die folgendem Text ähneln:

```output
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

Verwenden Sie zum Beenden der Topologie __STRG+C__ .

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Dokumenten werden weitere Möglichkeiten zur Verwendung von Python mit HDInsight beschrieben: [Verwenden von benutzerdefinierten Python-Funktionen (UDFs) in Apache Pig und Apache Hive](../hadoop/python-udf-hdinsight.md)