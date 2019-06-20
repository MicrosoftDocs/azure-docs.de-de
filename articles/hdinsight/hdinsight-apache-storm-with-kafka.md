---
title: 'Tutorial: Verwenden von Apache Storm zum Lesen und Schreiben von Daten mit Apache Kafka – Azure HDInsight'
description: Erfahren Sie, wie Sie eine Streamingpipeline mit Apache Storm und Apache Kafka in HDInsight erstellen. In diesem Tutorial verwenden Sie die KafkaBolt- und KafkaSpout-Komponenten zum Streamen von Daten aus Kafka.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/06/2018
ms.openlocfilehash: c89567115079887295704e216cd4046fae99c9d1
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64873015"
---
# <a name="tutorial-use-apache-storm-with-apache-kafka-on-hdinsight"></a>Tutorial: Verwenden von Apache Storm mit Apache Kafka in HDInsight

Dieses Tutorial veranschaulicht die Verwendung einer [Apache Storm](https://storm.apache.org/)-Topologie zum Lesen und Schreiben von Daten mit [Apache Kafka](https://kafka.apache.org/) in HDInsight. In diesem Tutorial wird außerdem gezeigt, wie Sie Daten im Storm-Cluster in dem Speicher dauerhaft speichern, der mit dem [HDFS von Apache Hadoop](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) kompatibel ist.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Storm und Kafka
> * Grundlegendes zum Code
> * Erstellen von Kafka- und Storm-Clustern
> * Erstellen der Topologie
> * Konfigurieren der Topologie
> * Erstellen des Kafka-Themas
> * Starten der Topologien
> * Beenden der Topologien
> * Bereinigen von Ressourcen

## <a name="prerequisites"></a>Voraussetzungen

* Kenntnisse im Erstellen von Kafka-Themen. Weitere Informationen finden Sie im Dokument [Schnellstart für Kafka in HDInsight](./kafka/apache-kafka-get-started.md).

* Kenntnisse im Erstellen und Bereitstellen von Storm-Lösungen (Topologien). Dies betrifft insbesondere Topologien, die das [Apache Storm Flux](https://storm.apache.org/releases/current/flux.html)-Framework verwenden. Weitere Informationen finden Sie im Dokument zum [Erstellen einer Apache Storm-Topologie in Java](./storm/apache-storm-develop-java-topology.md).

* [Java JDK 1.8](https://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) oder höher HDInsight 3.5 oder höher erfordert Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Einen SSH-Client (Sie benötigen die Befehle `ssh` und `scp`) – weitere Informationen finden Sie unter [Verwenden von SSH mit HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Bei der Installation von Java und dem JDK auf Ihrer Entwicklungsworkstation können die folgenden Umgebungsvariablen festgelegt werden. Sie sollten dennoch prüfen, ob die Variablen vorhanden sind und korrekte Werte für Ihr System enthalten.

* `JAVA_HOME` – sollte auf das Verzeichnis verweisen, in dem das JDK installiert ist.
* `PATH` – sollte die folgenden Pfade enthalten:
  
    * `JAVA_HOME` (oder entsprechender Pfad)
    * `JAVA_HOME\bin` (oder entsprechender Pfad)
    * Das Verzeichnis, in dem Maven installiert ist

> [!IMPORTANT]  
> Für die in diesem Dokument beschriebenen Schritte wird eine Azure-Ressourcengruppe benötigt, die sowohl ein Cluster vom Typ „Storm in HDInsight“ als auch vom Typ „Kafka in HDInsight“ enthält. Diese Cluster befinden sich beide in einem virtuellen Azure-Netzwerk, damit der Storm-Cluster direkt mit dem Kafka-Cluster kommunizieren kann.
> 
> Der Einfachheit halber enthält dieses Dokument Links zu einer Vorlage, mit der die erforderlichen Azure-Ressourcen erstellt werden können. 
>
> Weitere Informationen zur Verwendung von HDInsight in einem virtuellen Netzwerk finden Sie im Dokument [Erweitern von HDInsight mit einem virtuellen Netzwerk](hdinsight-extend-hadoop-virtual-network.md).

## <a name="storm-and-kafka"></a>Storm und Kafka

Apache Storm stellt mehrere Komponenten für die Arbeit mit Apache Kafka bereit. In diesem Tutorial werden die folgenden Komponenten verwendet:

* `org.apache.storm.kafka.KafkaSpout`: Diese Komponente liest Daten aus Kafka. Diese Komponente benötigt die folgenden Komponenten:

    * `org.apache.storm.kafka.SpoutConfig`: Stellt die Konfiguration für die Spout-Komponente bereit.

    * `org.apache.storm.spout.SchemeAsMultiScheme` und `org.apache.storm.kafka.StringScheme`: Legen fest, wie die Daten aus Kafka in ein Storm-Tupel transformiert werden.

* `org.apache.storm.kafka.bolt.KafkaBolt`: Diese Komponente schreibt Daten in Kafka. Diese Komponente benötigt die folgenden Komponenten:

    * `org.apache.storm.kafka.bolt.selector.DefaultTopicSelector`: Beschreibt das Thema, in das geschrieben wird.

    * `org.apache.kafka.common.serialization.StringSerializer`: Konfiguriert den Bolt zum Serialisieren von Daten als Zeichenfolge.

    * `org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper`: Ordnet die Tupel-Datenstruktur, die in der Storm-Topologie verwendet wird, den in Kafka gespeicherten Feldern zu.

Diese Komponenten sind im Paket `org.apache.storm : storm-kafka` enthalten. Verwenden Sie die Paketversion, die Ihrer Storm-Version entspricht. Für HDInsight 3.6 lautet die Storm-Version 1.1.0.
Sie benötigen auch das Paket `org.apache.kafka : kafka_2.10`, das zusätzliche Kafka-Komponenten enthält. Verwenden Sie die Paketversion, die Ihrer Kafka-Version entspricht. Für HDInsight 3.6 lautet die Kafka-Version 1.1.1.

Der folgende XML-Code stellt die Abhängigkeitsdeklaration in `pom.xml` für ein [Apache Maven](https://maven.apache.org/)-Projekt dar:

```xml
<!-- Storm components for talking to Kafka -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-kafka</artifactId>
    <version>1.1.0</version>
</dependency>
<!-- needs to be the same Kafka version as used on your cluster -->
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka_2.10</artifactId>
    <version>1.1.1</version>
    <!-- Exclude components that are loaded from the Storm cluster at runtime -->
    <exclusions>
        <exclusion>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </exclusion>
        <exclusion>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## <a name="understanding-the-code"></a>Grundlegendes zum Code

Den in diesem Dokument verwendeten Code finden Sie unter [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

In diesem Tutorial werden zwei Topologien bereitgestellt:

* Kafka-writer: Generiert zufällige Sätze und speichert sie in Kafka.

* Kafka-reader: Liest Daten aus Kafka und speichert sie im HDFS-kompatiblen Dateispeicher für den Storm-Cluster.

    > [!WARNING]  
    > Damit Storm mit dem von HDInsight verwendeten HDFS-kompatiblen Speicher arbeiten kann, ist eine Skriptaktion erforderlich. Das Skript installiert mehrere JAR-Dateien im Pfad `extlib` für Storm. Die Vorlage in diesem Tutorial verwendet dieses Skript während der Clustererstellung automatisch.
    >
    > Wenn Sie nicht die Vorlage in diesem Dokument verwenden, um den Storm-Cluster zu erstellen, müssen Sie die Skriptaktion manuell in Ihrem Cluster anwenden.
    >
    > Die Skriptaktion befindet sich unter [https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh](https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh) und wird auf die übergeordneten und Nimbus-Knoten des Storm-Clusters angewandt. Weitere Informationen zur Verwendung von Skriptaktionen finden Sie im Dokument [Anpassen von HDInsight für die Verwendung von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

Die Topologien werden mit [Flux](https://storm.apache.org/releases/1.1.2/flux.html) definiert. Flux wurde mit Storm 0.10.x eingeführt und ermöglicht Ihnen die Trennung der Topologiekonfiguration vom Code. Für Topologien, die das Flux-Framework nutzen, wird die Topologie in einer YAML-Datei definiert. Die YAML-Datei kann als Teil der Topologie aufgenommen werden. Sie kann auch eine eigenständige Datei sein, die beim Übermitteln der Topologie verwendet wird. Außerdem unterstützt Flux die Variablenersetzung zur Laufzeit, die in diesem Beispiel verwendet wird.

Zur Laufzeit werden die folgenden Parameter für diese Topologien festgelegt:

* `${kafka.topic}`: Der Name des Kafka-Themas, für das die Topologien die Lese-/Schreibvorgänge durchführen.

* `${kafka.broker.hosts}`: Die Hosts, auf denen die Kafka-Broker ausgeführt werden. Die Brokerinformationen werden von der KafkaBolt-Komponente verwendet, wenn auf Kafka geschrieben wird.

* `${kafka.zookeeper.hosts}`: Die Hosts, auf denen Zookeeper im Kafka-Cluster ausgeführt wird.

* `${hdfs.url}`: Die Dateisystem-URL für die HDFSBolt-Komponente. Sie gibt an, ob die Daten in ein Azure Storage-Konto oder in Azure Data Lake Storage geschrieben werden.

* `${hdfs.write.dir}`: Das Verzeichnis, in das die Daten geschrieben werden.

Weitere Informationen zu Flux-Topologien finden Sie unter [https://storm.apache.org/releases/1.1.2/flux.html](https://storm.apache.org/releases/1.1.2/flux.html).

### <a name="kafka-writer"></a>Kafka-writer

In der Topologie von Kafka-writer akzeptiert die Kafka-Bolt-Komponente zwei Zeichenfolgenwerte als Parameter. Diese Parameter geben an, welche Tupelfelder der Bolt an Kafka als die Werte __key__ und __message__ sendet. Der Schlüssel („key“) wird zum Partitionieren von Daten in Kafka verwendet. Die Nachricht („message“) enthält die zu speichernden Daten.

In diesem Beispiel gibt die `com.microsoft.example.SentenceSpout`-Komponente ein Tupel aus, das die beiden Felder `key` und `message` enthält. Der Kafka-Bolt extrahiert diese Felder und sendet die darin enthaltenen Daten an Kafka.

Die Felder müssen nicht die Namen `key` und `message` verwenden. Diese Namen werden in diesem Projekt verwendet, um die Zuordnung einfacher verständlich zu machen.

Der folgende YAML-Code stellt die Definition für die Kafka-writer-Komponente dar:

```yaml
# kafka-writer
---

# topology definition
# name to be used when submitting
name: "kafka-writer"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Topic selector for KafkaBolt
  - id: "topicSelector"
    className: "org.apache.storm.kafka.bolt.selector.DefaultTopicSelector"
    constructorArgs:
      - "${kafka.topic}"

  # Mapper for KafkaBolt
  - id: "kafkaMapper"
    className: "org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper"
    constructorArgs:
      - "key"
      - "message"

  # Producer properties for KafkaBolt
  - id: "producerProperties"
    className: "java.util.Properties"
    configMethods:
      - name: "put"
        args:
          - "bootstrap.servers"
          - "${kafka.broker.hosts}"
      - name: "put"
        args:
          - "acks"
          - "1"
      - name: "put"
        args:
          - "key.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
      - name: "put"
        args:
          - "value.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
 

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "com.microsoft.example.SentenceSpout"
    parallelism: 8

# Bolt definitions
bolts:
  - id: "kafka-bolt"
    className: "org.apache.storm.kafka.bolt.KafkaBolt"
    parallelism: 8
    configMethods:
    - name: "withProducerProperties"
      args: [ref: "producerProperties"]
    - name: "withTopicSelector"
      args: [ref: "topicSelector"]
    - name: "withTupleToKafkaMapper"
      args: [ref: "kafkaMapper"]

# Stream definitions

streams:
  - name: "spout --> kafka" # Streams data from the sentence spout to the Kafka bolt
    from: "sentence-spout"
    to: "kafka-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="kafka-reader"></a>Kafka-reader

In der Topologie von Kafka-reader liest die Spout-Komponente die Daten aus Kafka als Zeichenfolgenwerte. Die Daten werden dann von der Protokollierungskomponente in das Storm-Protokoll und von der HDFS-Bolt-Komponente in das HDFS-kompatible Dateisystem für den Storm-Cluster geschrieben.

```yaml
# kafka-reader
---

# topology definition
# name to be used when submitting
name: "kafka-reader"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Convert data from Kafka into string tuples in storm
  - id: "stringScheme"
    className: "org.apache.storm.kafka.StringScheme"
  - id: "stringMultiScheme"
    className: "org.apache.storm.spout.SchemeAsMultiScheme"
    constructorArgs:
      - ref: "stringScheme"

  - id: "zkHosts"
    className: "org.apache.storm.kafka.ZkHosts"
    constructorArgs:
      - "${kafka.zookeeper.hosts}"

  # Spout configuration
  - id: "spoutConfig"
    className: "org.apache.storm.kafka.SpoutConfig"
    constructorArgs:
      # brokerHosts
      - ref: "zkHosts"
      # topic
      - "${kafka.topic}"
      # zkRoot
      - ""
      # id
      - "readerid"
    properties:
      - name: "scheme"
        ref: "stringMultiScheme"

    # How often to sync files to HDFS; every 1000 tuples.
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "KB"

  # File format; read the directory from filters at run time, and use a .txt extension when writing.
  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  # Internal file format; fields delimited by `|`.
  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "kafka-spout"
    className: "org.apache.storm.kafka.KafkaSpout"
    constructorArgs:
      - ref: "spoutConfig"
    # Set to the number of partitions for the topic
    parallelism: 8

# Bolt definitions
bolts:
  - id: "logger-bolt"
    className: "com.microsoft.example.LoggerBolt"
    parallelism: 1
  
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
    parallelism: 1

# Stream definitions

streams:
  # Stream data to log
  - name: "kafka --> log" # name isn't used (placeholder for logging, UI, etc.)
    from: "kafka-spout"
    to: "logger-bolt"
    grouping:
      type: SHUFFLE
  
  # stream data to file
  - name: "kafka --> hdfs"
    from: "kafka-spout"
    to: "hdfs-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="property-substitutions"></a>Eigenschaftenersetzungen

Das Projekt enthält eine Datei namens `dev.properties`, die zum Übergeben der durch die Topologien verwendeten Parameter dient. In ihr werden die folgenden Eigenschaften definiert:

| Datei „dev.properties“ | BESCHREIBUNG |
| --- | --- |
| `kafka.zookeeper.hosts` | Die [Apache ZooKeeper](https://zookeeper.apache.org/)-Hosts für den Kafka-Cluster |
| `kafka.broker.hosts` | Die Kafka-Brokerhosts (Workerknoten) |
| `kafka.topic` | Das von den Topologien verwendete Kafka-Thema |
| `hdfs.write.dir` | Das Verzeichnis, in das die Kafka-reader-Topologie schreibt |
| `hdfs.url` | Das vom Storm-Cluster verwendete Dateisystem Verwenden Sie für Azure Storage-Konten den Wert `wasb:///`. Verwenden Sie für Azure Data Lake Storage Gen2 den Wert `abfs:///`. Verwenden Sie für Azure Data Lake Storage Gen1 den Wert `adl:///`. |

## <a name="create-the-clusters"></a>Erstellen von Clustern

Apache Kafka in HDInsight ermöglicht keinen Zugriff auf die Kafka-Broker über das öffentliche Internet. Alle von Kafka verwendeten Elemente müssen sich im gleichen virtuellen Azure-Netzwerk befinden. In diesem Tutorial befinden sich der Kafka- und der Storm-Cluster im selben virtuellen Azure-Netzwerk. 

Das folgende Diagramm zeigt den Kommunikationsfluss zwischen Storm und Kafka:

![Diagramm mit Storm- und Kafka-Clustern in einem virtuellen Azure-Netzwerk](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]  
> Auf andere Dienste im Cluster, beispielsweise SSH und [Apache Ambari](https://ambari.apache.org/), kann über das Internet zugegriffen werden. Weitere Informationen zu den öffentlichen Ports, die für HDInsight verfügbar sind, finden Sie unter [Von HDInsight verwendete Ports und URIs](hdinsight-hadoop-port-settings-for-services.md).

Führen Sie zum Erstellen eines virtuellen Azure-Netzwerks und zum anschließenden Erstellen der Kafka- und Storm-Cluster in diesem die folgenden Schritte aus:

1. Verwenden Sie die folgende Schaltfläche, um sich bei Azure anzumelden, und öffnen Sie die Vorlage im Azure-Portal.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-storm-java-kafka%2Fmaster%2Fcreate-kafka-storm-clusters-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Die Azure Resource Manager-Vorlage finden Sie unter **https://github.com/Azure-Samples/hdinsight-storm-java-kafka/blob/master/create-kafka-storm-clusters-in-vnet.json** . Er erstellt die folgenden Ressourcen:
    
    * Azure-Ressourcengruppe
    * Virtuelles Azure-Netzwerk
    * Azure-Speicherkonto
    * Kafka in HDInsight Version 3.6 (drei Workerknoten)
    * Storm in HDInsight Version 3.6 (drei Workerknoten)

   > [!WARNING]  
   > Um die Verfügbarkeit von Kafka in HDInsight zu gewährleisten, muss der Cluster mindestens drei Workerknoten enthalten. Diese Vorlage erstellt einen Kafka-Cluster, der drei Workerknoten enthält.

2. Befolgen Sie die nachstehende Anleitung, um die Einträge im Abschnitt **Benutzerdefinierte Bereitstellung** aufzufüllen:

   1. Geben Sie im Abschnitt **Benutzerdefinierte Vorlage** folgende Informationen an:

      | Einstellung | Wert |
      | --- | --- |
      | Abonnement | Ihr Azure-Abonnement |
      | Ressourcengruppe | Die Ressourcengruppe mit den Ressourcen. |
      | Location | Die Azure-Region, in der die Ressourcen erstellt werden. |
      | Kafka Cluster Name (Kafka-Clustername) | Der Name des Kafka-Clusters. |
      | Storm Cluster Name (Storm-Clustername) | Der Name des Storm-Clusters. |
      | Benutzername für Clusteranmeldung | Der Administratorbenutzername für die Cluster. |
      | Kennwort für Clusteranmeldung | Das Administratorbenutzerkennwort für die Cluster. |
      | SSH-Benutzername | Der SSH-Benutzer, der für die Cluster erstellt werden soll. |
      | SSH-Kennwort | Das Kennwort für den SSH-Benutzer. |
   
      ![Darstellung der Vorlagenparameter](./media/hdinsight-apache-storm-with-kafka/storm-kafka-template.png)

3. Lesen Sie die **Geschäftsbedingungen**, und wählen Sie anschließend die Option **Ich stimme den oben genannten Geschäftsbedingungen zu**.

4. Aktivieren Sie zum Schluss **An Dashboard anheften**, und wählen Sie dann **Kaufen** aus.

> [!NOTE]  
> Die Clustererstellung kann bis zu 20 Minuten dauern.

## <a name="build-the-topology"></a>Erstellen der Topologie

1. Laden Sie das Projekt in Ihrer Entwicklungsumgebung unter [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) herunter, öffnen Sie eine Befehlszeile, und ändern Sie die Verzeichnisse am Speicherort, in den Sie das Projekt heruntergeladen haben.

2. Verwenden Sie im Verzeichnis **hdinsight-storm-java-kafka** den folgenden Befehl, um das Projekt zu kompilieren und ein Paket für die Bereitstellung zu erstellen:

   ```bash
   mvn clean package
   ```

    Mit dem Paketprozess wird eine Datei mit dem Namen `KafkaTopology-1.0-SNAPSHOT.jar` im Verzeichnis `target` erstellt.

3. Verwenden Sie die folgenden Befehle, um das Paket in Ihren Cluster vom Typ „Storm in HDInsight“ zu kopieren. Ersetzen Sie `sshuser` durch den Namen des SSH-Benutzers für den Cluster. Ersetzen Sie `stormclustername` durch den Namen des __Storm__-Clusters.

   ```bash
   scp ./target/KafkaTopology-1.0-SNAPSHOT.jar sshuser@stormclustername-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
   ```

    Geben Sie nach der entsprechenden Aufforderung das Kennwort ein, das Sie beim Erstellen der Cluster verwendet haben.

## <a name="configure-the-topology"></a>Konfigurieren der Topologie

1. Verwenden Sie eine der folgenden Methoden, um die Kafka-Brokerhosts für den **Kafka**-Cluster in HDInsight zu ermitteln:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!IMPORTANT]  
    > Im folgenden Bash-Beispiel wird angenommen, dass `$CLUSTERNAME` den Namen des __Kafka__-Clusters enthält. Außerdem wird angenommen, dass mindestens Version 1.5 von [jq](https://stedolan.github.io/jq/) installiert ist. Geben Sie bei entsprechender Aufforderung das Kennwort des Anmeldekontos für den Cluster ein.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    Der zurückgegebene Wert ähnelt dem folgenden Text:

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]  
    > Obwohl möglicherweise mehr als zwei Brokerhosts für den Cluster vorhanden sind, müssen Sie keine vollständige Liste aller Hosts für Clients angeben. Ein oder zwei genügen.

2. Verwenden Sie eine der folgenden Methoden, um die Zookeeper-Brokerhosts für den __Kafka__-Cluster in HDInsight zu ermitteln:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    > [!IMPORTANT]  
    > Im folgenden Bash-Beispiel wird angenommen, dass `$CLUSTERNAME` den Namen des __Kafka__-Clusters enthält. Außerdem wird angenommen, das [jq](https://stedolan.github.io/jq/) installiert ist. Geben Sie bei entsprechender Aufforderung das Kennwort des Anmeldekontos für den Cluster ein.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    Der zurückgegebene Wert ähnelt dem folgenden Text:

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]  
    > Obwohl mehr als zwei Zookeeper-Knoten vorhanden sind, müssen Sie keine vollständige Liste aller Hosts für Clients angeben. Ein oder zwei genügen.

    Speichern Sie diesen Wert, da er später wieder verwendet wird.

3. Bearbeiten Sie die Datei `dev.properties` im Stammverzeichnis des Projekts. Fügen Sie die Broker- und Zookeeper-Hostinformationen für den __Kafka__-Cluster in die entsprechenden Zeilen in dieser Datei ein. Das folgende Beispiel wird mithilfe der Beispielwerte aus den vorherigen Schritten konfiguriert:

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

    > [!IMPORTANT]  
    > Der `hdfs.url`-Eintrag wurde für einen Cluster konfiguriert, der ein Azure Storage-Konto verwendet. Um diese Topologie mit einem Storm-Cluster zu verwenden, der Data Lake Storage verwendet, ändern Sie diesen Wert von `wasb` in `adl`.

4. Speichern Sie die Datei `dev.properties`, und laden Sie sie anschließend mit dem folgenden Befehl in den **Storm**-Cluster hoch:

     ```bash
    scp dev.properties USERNAME@BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    Ersetzen Sie **USERNAME** durch den SSH-Benutzernamen für den Cluster. Ersetzen Sie **BASENAME** durch den Basisnamen, den Sie beim Erstellen des Clusters verwendet haben.

## <a name="create-the-kafka-topic"></a>Erstellen des Kafka-Themas

Kafka speichert die Daten in einem _Thema_. Sie müssen das Thema erstellen, bevor Sie die Storm-Topologien starten. Führen Sie die folgenden Schritte aus, um die Topologie zu erstellen:

1. Stellen Sie per SSH mithilfe des folgenden Befehls eine Verbindung mit dem __Kafka__-Cluster her. Ersetzen Sie `sshuser` durch den SSH-Benutzernamen, der beim Erstellen des Clusters verwendet wurde. Ersetzen Sie `kafkaclustername` durch den Namen des Kafka-Clusters:

    ```bash
    ssh sshuser@kafkaclustername-ssh.azurehdinsight.net
    ```

    Geben Sie nach der entsprechenden Aufforderung das Kennwort ein, das Sie beim Erstellen der Cluster verwendet haben.
   
    Informationen hierzu finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Um ein Kafka-Thema zu erstellen, verwenden Sie den folgenden Befehl. Ersetzen Sie `$KAFKAZKHOSTS` durch die Informationen des ZooKeeper-Hosts, den Sie beim Konfigurieren der Topologie verwendet haben:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    Dieser Befehl verbindet ZooKeeper für den Cluster Kafka und erstellt das neue Thema `stormtopic`. Dieses Thema wird von den Storm-Topologien verwendet.

## <a name="start-the-writer"></a>Starten der Writer-Topologie

1. Verwenden Sie Folgendes, um per SSH eine Verbindung mit dem **Storm**-Cluster herzustellen. Ersetzen Sie `sshuser` durch den SSH-Benutzernamen, der beim Erstellen des Clusters verwendet wurde. Ersetzen Sie `stormclustername` durch den Namen des Storm-Clusters:

    ```bash
    ssh sshuser@stormclustername-ssh.azurehdinsight.net
    ```

    Geben Sie nach der entsprechenden Aufforderung das Kennwort ein, das Sie beim Erstellen der Cluster verwendet haben.
   
    Informationen hierzu finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Verwenden Sie für die SSH-Verbindung mit dem Storm-Cluster den folgenden Befehl, um die Writer-Topologie zu starten:

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    Für diesen Befehl werden die folgenden Parameter verwendet:

    * `org.apache.storm.flux.Flux`: Flux wird verwendet, um diese Topologie zu konfigurieren und auszuführen.

    * `--remote`: Die Topologie wird an Nimbus übermittelt. Die Topologie wird auf die Workerknoten im Cluster verteilt.

    * `-R /writer.yaml`: Die Datei `writer.yaml` wird verwendet, um die Topologie zu konfigurieren. `-R` gibt an, dass diese Ressource in der JAR-Datei enthalten ist. Da sie im Stamm der JAR-Datei enthalten ist, lautet der Pfad `/writer.yaml`.

    * `--filter`: Einträge in der Topologie `writer.yaml` werden mit Werten in der Datei `dev.properties` aufgefüllt. Der Wert des Eintrags `kafka.topic` in der Datei wird beispielsweise verwendet, um den Eintrag `${kafka.topic}` in der Topologiedefinition zu ersetzen.

## <a name="start-the-reader"></a>Starten der Reader-Topologie

1. Verwenden Sie für die SSH-Sitzung mit dem Storm-Cluster den folgenden Befehl, um die Reader-Topologie zu starten:

   ```bash
   storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
   ```

2. Warten Sie eine Minute, und zeigen Sie dann mit dem folgenden Befehl die von der reader-Topologie erstellten Dateien an:

    ```bash
    hdfs dfs -ls /stormdata
    ```

    Die Ausgabe sieht in etwa wie folgender Text aus:

        Found 173 items
        -rw-r--r--   1 storm supergroup       5137 2018-04-09 19:00 /stormdata/hdfs-bolt-4-0-1523300453088.txt
        -rw-r--r--   1 storm supergroup       5128 2018-04-09 19:00 /stormdata/hdfs-bolt-4-1-1523300453624.txt
        -rw-r--r--   1 storm supergroup       5131 2018-04-09 19:00 /stormdata/hdfs-bolt-4-10-1523300455170.txt
        ...

3. Sie können den Inhalt der Datei mit dem folgenden Befehl anzeigen. Ersetzen Sie `filename.txt` durch den Namen einer Datei:

    ```bash
    hdfs dfs -cat /stormdata/filename.txt
    ```

    Der folgende Text ist ein Beispiel für den Dateiinhalt:

        four score and seven years ago
        snow white and the seven dwarfs
        i am at two with nature
        snow white and the seven dwarfs
        i am at two with nature
        four score and seven years ago
        an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>Beenden der Topologien

Verwenden Sie in einer SSH-Sitzung für den Storm-Cluster die folgenden Befehle, um die Storm-Topologien zu beenden:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Zum Bereinigen der im Rahmen dieses Tutorials erstellten Ressourcen können Sie die Ressourcengruppe löschen. Dadurch werden auch der zugeordnete HDInsight-Cluster sowie alle anderen Ressourcen gelöscht, die der Ressourcengruppe zugeordnet sind.

So entfernen Sie die Ressourcengruppe über das Azure-Portal:

1. Erweitern Sie im Azure-Portal das Menü auf der linken Seite, um das Menü mit den Diensten zu öffnen, und klicken Sie auf __Ressourcengruppen__, um die Liste mit Ihren Ressourcengruppen anzuzeigen.
2. Suchen Sie die zu löschende Ressourcengruppe, und klicken Sie mit der rechten Maustaste rechts neben dem Eintrag auf die Schaltfläche __Mehr__ (...).
3. Klicken Sie auf __Ressourcengruppe löschen__, und bestätigen Sie den Vorgang.

> [!WARNING]  
> Die Abrechnung für einen HDInsight-Cluster beginnt, sobald der Cluster erstellt wurde, und endet mit dem Löschen des Clusters. Die Gebühren werden anteilig nach Minuten erhoben. Daher sollten Sie Ihren Cluster immer löschen, wenn Sie ihn nicht mehr verwenden.
> 
> Wenn Sie einen Kafka-Cluster in HDInsight löschen, werden auch alle in Kafka gespeicherten Daten gelöscht.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie eine [Apache Storm](https://storm.apache.org/)-Topologie zum Schreiben und Lesen aus [Apache Kafka](https://kafka.apache.org/) in HDInsight verwenden. Außerdem haben Sie gelernt, Daten in dem von HDInsight verwendeten Speicher zu speichern, der mit dem [HDFS von Apache Hadoop](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) kompatibel ist.

Weitere Informationen zum Verwenden von Kafka in HDInsight finden Sie unter [Tutorial: Verwenden der Apache Kafka Producer- und Consumer-APIs](kafka/apache-kafka-producer-consumer-api.md).

Informationen zur Bereitstellung und Überwachung von Topologien in Linux-basiertem HDInsight finden Sie unter [Bereitstellen und Verwalten von Apache Storm-Topologien in Linux-basiertem HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md).
