---
title: 'Tutorial: Apache Kafka Producer- und Consumer-APIs – Azure HDInsight'
description: Erfahren Sie, wie Sie die Apache Kafka Producer- und Consumer-APIs mit Kafka in HDInsight verwenden. In diesem Tutorial erfahren Sie, wie Sie diese APIs mit Kafka auf HDInsight aus einer Java-Anwendung verwenden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: b942fb321d2bceef64930bea0c660f66747508b6
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629305"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Tutorial: Verwenden der Apache Kafka Producer- und Consumer-APIs

Erfahren Sie, wie Sie die Apache Kafka Producer- und Consumer-APIs mit Kafka in HDInsight verwenden.

Mit der Kafka Producer-API können Anwendungen Datenströme an den Kafka-Cluster senden. Mit der Kafka Consumer-API können Anwendungen Datenströme aus dem Cluster lesen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Voraussetzungen
> * Grundlegendes zum Code
> * Erstellen und Bereitstellen der Anwendung
> * Ausführen der Anwendung im Cluster

Weitere Informationen zu den APIs finden Sie in der Apache-Dokumentation unter [Producer API](https://kafka.apache.org/documentation/#producerapi) und [Consumer API](https://kafka.apache.org/documentation/#consumerapi).

## <a name="prerequisites"></a>Voraussetzungen

* Apache Kafka in einem HDInsight-Cluster. Informationen zum Erstellen des Clusters finden Sie unter [Schnellstart: Erstellen eines Apache Kafka-Clusters in Azure HDInsight im Azure-Portal](apache-kafka-get-started.md).
* [Version 8 des Java Developer Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-long-term-support) oder eine vergleichbare Lösung (etwa OpenJDK).
* Ordnungsgemäße [Installation](https://maven.apache.org/install.html) von [Apache Maven](https://maven.apache.org/download.cgi) (gemäß Apache).  Maven ist ein Projekterstellungssystem für Java-Projekte.
* Ein SSH-Client wie Putty. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Grundlegendes zum Code

Die exemplarische Anwendung befindet sich unter [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) im `Producer-Consumer` -Unterverzeichnis. Wenn Sie einen Kafka-Cluster mit aktiviertem **Enterprise-Sicherheitspaket (ESP)** verwenden, sollten Sie die Anwendungsversion im Unterverzeichnis `DomainJoined-Producer-Consumer` verwenden.

Die Anwendung besteht im Wesentlichen aus vier Dateien:
* `pom.xml`: Diese Datei definiert die Projektabhängigkeiten, Java-Version und Verpackungsmethoden.
* `Producer.java`: Diese Datei sendet mithilfe der Producer-API willkürliche Sätze an Kafka.
* `Consumer.java`: Diese Datei verwendet die Consumer-API zum Lesen von Daten aus Kafka und Ausgeben an STDOUT.
* `AdminClientWrapper.java`: Diese Datei verwendet die Admin-API, um Kafka-Themen zu erstellen, zu beschreiben und zu löschen.
* `Run.java`: Die Befehlszeilenschnittstelle zum Ausführen des Producer- und Consumer-Codes.

### <a name="pomxml"></a>Pom.Xml

Wichtige Informationen zur `pom.xml`-Datei:

* Abhängigkeiten: Dieses Projekt benötigt die Kafka Producer- und Consumer-APIs, die durch das `kafka-clients`-Paket bereitgestellt werden. Der folgende XML-Code definiert diese Abhängigkeit:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>${kafka.version}</version>
    </dependency>
    ```

    Der `${kafka.version}`-Eintrag wird im `<properties>..</properties>`-Abschnitt von `pom.xml` deklariert und ist für die Kafka-Version des HDInsight-Clusters konfiguriert.

* Plug-Ins: Maven-Plug-Ins bieten verschiedene Funktionen. In diesem Projekt werden die folgenden Plug-Ins verwendet:

    * `maven-compiler-plugin`: Wird verwendet, um die vom Projekt verwendete Java-Version auf 8 festzulegen. Dies ist die von HDInsight 3.6 verwendete Java-Version.
    * `maven-shade-plugin`: Wird zum Generieren einer Uber-JAR-Datei verwendet, die diese Anwendung sowie alle Abhängigkeiten enthält. Es wird auch zum Festlegen des Einstiegspunkts der Anwendung verwendet, damit Sie die JAR-Datei direkt ausführen können, ohne die Hauptklasse angeben zu müssen.

### <a name="producerjava"></a>Producer.java

Der Producer kommuniziert mit den Kafka-Brokerhosts (Workerknoten) und sendet Daten an ein Kafka-Thema. Der folgende Codeausschnitt stammt aus der Datei [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) aus dem [GitHub-Repository](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) und zeigt, wie die Producereigenschaften festgelegt werden. Für Cluster mit aktivierter Enterprise-Sicherheit muss eine zusätzliche Eigenschaft hinzugefügt werden: „properties.setProperty(CommonClientConfigs.SECURITY_PROTOCOL_CONFIG, "SASL_PLAINTEXT");“

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

Der Consumer kommuniziert mit den Kafka-Brokerhosts (Workerknoten) und liest Datensätze in eine Schleife ein. Der folgende Codeausschnitt aus der Datei [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) legt die Consumereigenschaften fest. Für Cluster mit aktivierter Enterprise-Sicherheit muss eine zusätzliche Eigenschaft hinzugefügt werden: „properties.setProperty(CommonClientConfigs.SECURITY_PROTOCOL_CONFIG, "SASL_PLAINTEXT");“

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

In diesem Code ist der Consumer so konfiguriert, dass er vom Beginn des Themas an liest (`auto.offset.reset` ist auf `earliest` festgelegt.)

### <a name="runjava"></a>Run.java

Die Datei [Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) bietet eine Befehlszeilenschnittstelle, die entweder den Producer- oder den Consumer-Code ausführt. Sie müssen die Kafka-Brokerhostinformationen als Parameter angeben. Sie können optional einen Gruppen-ID-Wert einschließen, der vom Consumer-Prozess verwendet wird. Wenn Sie mehrere Consumer-Instanzen mit der gleichen Gruppen-ID erstellen, wird beim Lesen aus dem Thema ein Lastenausgleich durchgeführt.

## <a name="build-and-deploy-the-example"></a>Erstellen und Bereitstellen des Beispiels

### <a name="use-pre-built-jar-files"></a>Verwenden von vordefinierten JAR-Dateien

Laden Sie die JAR-Datei aus dem [Azure-Beispiel zu den ersten Schritten mit Kafka](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/Prebuilt-Jars) herunter. Wenn für Ihren Cluster das **Enterprise-Sicherheitspaket (ESP)** aktiviert ist, verwenden Sie „kafka-producer-consumer-esp.jar“. Verwenden Sie den folgenden Befehl, um die JAR-Dateien in Ihren Cluster zu kopieren.

```cmd
scp kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
```

### <a name="build-the-jar-files-from-code"></a>Erstellen der JAR-Dateien aus Code

Wenn Sie diesen Schritt überspringen möchten, können Sie vorgefertigte JAR-Dateien aus dem Unterverzeichnis `Prebuilt-Jars` herunterladen. Laden Sie die Datei „kafka-producer-consumer.jar“ herunter. Wenn für Ihren Cluster das **Enterprise-Sicherheitspaket (ESP)** aktiviert ist, verwenden Sie „kafka-producer-consumer-esp.jar“. Führen Sie Schritt 3 aus, um die JAR-Datei in Ihren HDInsight-Cluster zu kopieren.

1. Laden Sie die Beispiele aus [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) herunter, und extrahieren Sie sie.

2. Legen Sie Ihr aktuelles Verzeichnis auf den Speicherort des Verzeichnisses `hdinsight-kafka-java-get-started\Producer-Consumer` fest. Wenn Sie einen Kafka-Cluster mit aktiviertem **Enterprise-Sicherheitspaket (ESP)** verwenden, sollten Sie den Speicherort auf das Unterverzeichnis `DomainJoined-Producer-Consumer` festlegen. Führen Sie den folgenden Befehl aus, um die Anwendung zu erstellen:

    ```cmd
    mvn clean package
    ```

    Mit diesem Befehl wird ein Verzeichnis mit dem Namen `target` erstellt, das eine Datei namens `kafka-producer-consumer-1.0-SNAPSHOT.jar` enthält. Für ESP-Cluster ist die Datei `kafka-producer-consumer-esp-1.0-SNAPSHOT.jar`

3. Ersetzen Sie `sshuser` durch den SSH-Benutzer für Ihren Cluster und `CLUSTERNAME` durch den Namen Ihres Clusters. Geben Sie den folgenden Befehl ein, um die Datei `kafka-producer-consumer-1.0-SNAPSHOT.jar` in Ihren HDInsight-Cluster zu kopieren. Geben Sie nach Aufforderung das Kennwort für den SSH-Benutzer ein.

    ```cmd
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a name="run-the-example"></a><a id="run"></a>Ausführen des Beispiels

1. Ersetzen Sie `sshuser` durch den SSH-Benutzer für Ihren Cluster und `CLUSTERNAME` durch den Namen Ihres Clusters. Geben Sie den folgenden Befehl ein, um eine SSH-Verbindung mit dem Cluster zu öffnen. Geben Sie bei entsprechender Aufforderung das Kennwort für das SSH-Benutzerkonto ein.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Ersetzen Sie zum Abrufen der Kafka-Brokerhosts die Werte für `<clustername>` und `<password>` im folgenden Befehl, und führen Sie ihn aus. Verwenden Sie die gleiche Groß-/Kleinschreibung für `<clustername>` wie im Azure-Portal gezeigt. Ersetzen Sie `<password>` durch das Kennwort für die Clusteranmeldung, und führen Sie dann den Befehl aus:

    ```bash
    sudo apt -y install jq
    export clusterName='<clustername>'
    export password='<password>'
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Für diesen Befehl ist Zugriff auf Ambari erforderlich. Wird Ihr Cluster durch eine NSG geschützt, führen Sie diesen Befehl auf einem Computer aus, über den auf Ambari zugegriffen werden kann.

1. Geben Sie den folgenden Befehl ein, um ein Kafka-Thema namens `myTest` zu erstellen:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

1. Um den Producer auszuführen und Daten in das Thema zu schreiben, führen Sie folgenden Befehl aus:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

1. Wenn der Producer abgeschlossen ist, führen Sie folgenden Befehl aus, um Daten aus dem Thema zu lesen:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

    Die gelesenen Datensätze und die Anzahl von Datensätzen wird angezeigt.

1. Drücken Sie __STRG+C__ , um den Consumer zu beenden.

### <a name="multiple-consumers"></a>Mehrere Consumer

Kafka-Consumer verwenden beim Lesen von Datensätzen eine Consumergruppe. Das Verwenden derselben Gruppe mit mehreren Consumern führt zu Lesevorgängen mit Lastenausgleich aus einem Thema. Jeder Consumer in der Gruppe erhält einen Teil der Datensätze.

Die Consumer-Anwendung akzeptiert einen Parameter, der als Gruppen-ID verwendet wird. Der folgende Befehl beispielsweise startet einen Consumer mit der Gruppen-ID `myGroup`:

```bash
java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup
```

Drücken Sie __STRG+C__ , um den Consumer zu beenden.

Führen Sie den folgenden Befehl aus, um diesen Vorgang in Aktion zu sehen:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; split-window -h 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; attach
```

Dieser Befehl teilt das Terminal mit `tmux` in zwei Spalten auf. In jeder Spalte wird ein Consumer mit dem gleichen Gruppen-ID-Wert gestartet. Beachten Sie nach Abschluss des Lesens durch die Consumer, dass jeder nur einen Teil der Datensätze gelesen hat. Drücken Sie zweimal __STRG+C__ , um `tmux` zu beenden.

Der Verbrauch durch die Clients in derselben Gruppe wird über die Partitionen für das Thema gesteuert. In diesem Codebeispiel besitzt das zuvor erstellte Thema `test` acht Partitionen. Wenn Sie acht Consumer starten, liest jeder Consumer Datensätze aus einer einzelnen Partition für das Thema.

> [!IMPORTANT]  
> Eine Consumergruppe kann nicht mehr Consumerinstanzen als Partitionen enthalten. In diesem Beispiel kann eine Consumergruppe bis zu acht Consumer enthalten, da dies die Anzahl von Partitionen im Thema ist. Sie können auch mehrere Consumergruppen verwenden, die jeweils nicht mehr als acht Consumer enthalten.

Datensätze werden in Kafka in der Reihenfolge gespeichert, in der sie in einer Partition empfangen werden. Erstellen Sie eine Consumergruppe, bei der die Anzahl von Consumerinstanzen mit der Anzahl von Partitionen übereinstimmt, um für Datensätze *in einer Partition* eine geordnete Bereitstellung zu erzielen. Erstellen Sie eine Consumergruppe mit nur einer Consumerinstanz, um für Datensätze *im Thema* eine geordnete Bereitstellung zu erzielen.

## <a name="common-issues-faced"></a>Häufige Probleme

1. **Fehler beim Erstellen von Themen** Wenn für Ihren Cluster das Enterprise-Sicherheitspaket aktiviert ist, verwenden Sie die [vordefinierten JAR-Dateien für Producer und Consumer](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Prebuilt-Jars/kafka-producer-consumer-esp.jar). Die ESP-JAR-Datei kann aus dem Code im [Unterverzeichnis `DomainJoined-Producer-Consumer`](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer) erstellt werden. Die Producer- und Consumer-Eigenschaften weisen eine zusätzliche `CommonClientConfigs.SECURITY_PROTOCOL_CONFIG`-Eigenschaft für ESP-aktivierte Cluster auf.

2. **Fehler bei ESP-aktivierten Clustern:** Wenn bei den Produce- und Consume-Vorgängen Fehler auftreten und Sie einen ESP-aktivierten Cluster verwenden, prüfen Sie, ob der Benutzer `kafka` in allen Ranger-Richtlinien vorhanden ist. Wenn er nicht vorhanden ist, fügen Sie ihn allen Ranger-Richtlinien hinzu.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Zum Bereinigen der im Rahmen dieses Tutorials erstellten Ressourcen können Sie die Ressourcengruppe löschen. Dadurch werden auch der zugeordnete HDInsight-Cluster sowie alle anderen Ressourcen gelöscht, die der Ressourcengruppe zugeordnet sind.

So entfernen Sie die Ressourcengruppe über das Azure-Portal:

1. Erweitern Sie im Azure-Portal das Menü auf der linken Seite, um das Menü mit den Diensten zu öffnen, und klicken Sie auf __Ressourcengruppen__ , um die Liste mit Ihren Ressourcengruppen anzuzeigen.
2. Suchen Sie die zu löschende Ressourcengruppe, und klicken Sie mit der rechten Maustaste rechts neben dem Eintrag auf die Schaltfläche __Mehr__ (...).
3. Klicken Sie auf __Ressourcengruppe löschen__ , und bestätigen Sie den Vorgang.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie die Apache Kafka Producer- und Consumer-APIs mit Kafka in HDInsight verwenden. Verwenden Sie Folgendes, um weitere Informationen zur Verwendung von Kafka zu erhalten:

* [Interagieren mit Apache Kafka-Clustern in Azure HDInsight mithilfe eines REST-Proxys](rest-proxy.md)
* [Analysieren von Apache Kafka-Protokollen](apache-kafka-log-analytics-operations-management.md)