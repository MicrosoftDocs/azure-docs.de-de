---
title: Verwendung von Apache Kafka in HDInsight mit Azure IoT Hub
description: In diesem Artikel erfahren Sie, wie Apache Kafka in HDInsight mit Azure IoT Hub verwendet wird. Das Projekt Kafka Connect für Azure IoT Hub stellt einen Quell- und einen Senkenconnector für Kafka bereit. Der Quellconnector kann Daten von IoT Hub lesen, während der Senkenconnector Daten in IoT Hub schreibt.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 420800e718c8f98bfd3d5d7383829d5aa5472828
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64698158"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Verwendung von Apache Kafka in HDInsight mit Azure IoT Hub

Hier erfahren Sie, wie Sie mithilfe des [Apache Kafka Connect-Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub)-Connectors Daten zwischen Apache Kafka in HDInsight und Azure IoT Hub verschieben. In diesem Dokument erfahren Sie, wie der IoT Hub-Connector über einen Edgeknoten im Cluster ausgeführt wird.

Mit der Kafka Connect-API können Sie Connector implementieren, die kontinuierlich Daten in Kafka abrufen oder Daten über Kafka mithilfe von Push an ein anderes System übertragen. [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) ist ein Connector, der Daten aus Azure IoT Hub in Kafka pullt. Dieser kann Daten auch über Kafka mithilfe von Push an IoT Hub übertragen. 

Für den Abruf von Daten aus IoT Hub verwenden Sie einen __Quellconnector__. Für die Übertragung von Daten mithilfe von Push an IoT Hub verwenden Sie einen __Senkenconnector__. Der IoT Hub-Connector stellt sowohl Quell- als auch Senkenconnector bereit.

In der folgenden Abbildung wird der Datenfluss zwischen Azure IoT Hub und Kafka in HDInsight unter Verwendung des Connectors gezeigt.

![Abbildung zum Datenfluss zwischen IoT Hub und Kafka über den Connector](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Weitere Informationen zur Connect-API finden Sie unter [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* Ein Kafka-Cluster in HDInsight. Weitere Informationen finden Sie im Dokument [Schnellstart für Kafka in HDInsight](apache-kafka-get-started.md).

* Ein Edgeknoten im Kafka-Cluster. Weitere Informationen finden Sie im Dokument [Verwenden leerer Edgeknoten in Hadoop-Clustern in HDInsight](../hdinsight-apps-use-edge-node.md).

* Einen Azure IoT Hub. Im Rahmen dieses Tutorials wird das Dokument [Verbinden des Raspberry Pi-Onlinesimulators mit Azure IoT Hub (Node.js)](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started) empfohlen.

* Einen SSH-Client. Bei den Schritten in diesem Dokument wird SSH für die Verbindungsherstellung mit dem Cluster verwendet. Weitere Informationen finden Sie im Dokument [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="install-the-connector"></a>Installieren des Connectors

1. Laden Sie das neueste Release von Kafka Connect für Azure IoT Hub unter [https://github.com/Azure/toketi-kafka-connect-iothub/releases/](https://github.com/Azure/toketi-kafka-connect-iothub/releases) herunter.

2. Verwenden Sie zum Hochladen der JAR-Datei in den Edgeknoten Ihres Kafka in HDInsight-Clusters den folgenden Befehl:

    > [!NOTE]  
    > Ersetzen Sie `sshuser` durch das SSH-Benutzerkonto für Ihren HDInsight-Cluster. Ersetzen Sie `new-edgenode` durch den Namen des Edgeknotens. Ersetzen Sie `clustername` durch den Namen des Clusters. Weitere Informationen zum SSH-Endpunkt für den Edgeknoten finden Sie im Dokument [Verwenden leerer Edgeknoten in Hadoop-Clustern in HDInsight](../hdinsight-apps-use-edge-node.md#access-an-edge-node).

    ```bash
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.clustername-ssh.azurehdinsight.net:
    ```

3. Stellen Sie nach dem Dateikopiervorgang über SSH eine Verbindung mit dem Edgeknoten her:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```

    Weitere Informationen finden Sie im Dokument [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Verwenden Sie für die Installation des Connectors im Kafka-Verzeichnis `libs` den folgenden Befehl:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

> [!TIP]  
> Falls bei der Verwendung einer vorgefertigten JAR-Datei in den nachfolgenden Schritten in diesem Dokument Probleme auftreten sollten, versuchen Sie, das Paket über die Quelle zu erstellen.
>
> Um den Connector zu erstellen, benötigen Sie eine Scala-Entwicklungsumgebung mit dem [Buildtool Scala](https://www.scala-sbt.org/).
>
> 1. Laden Sie die Quelle für den Connector unter [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) in Ihre Entwicklungsumgebung herunter.
>
> 2. Verwenden Sie über eine Eingabeaufforderung im Projektverzeichnis den folgenden Befehl zum Erstellen und Packen des Projekts:
>
>    ```bash
>    sbt assembly
>    ```
>
>    Mit diesem Befehl wird die Datei `kafka-connect-iothub-assembly_2.11-0.6.jar` im Zielverzeichnis `target/scala-2.11` für das Projekt erstellt.

## <a name="configure-apache-kafka"></a>Konfigurieren von Apache Kafka

Führen Sie über eine SSH-Verbindung mit dem Edgeknoten die folgenden Schritte durch, um Kafka für die Ausführung des Connectors im eigenständigen Modus zu konfigurieren:

1. Speichern Sie den Clusternamen in einer Variablen. Die Verwendung einer Variablen ermöglicht ein einfacheres Kopieren und Einfügen der anderen Befehle in diesem Abschnitt:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

2. Installieren Sie das Hilfsprogramm `jq`. Durch dieses Hilfsprogramm können die von Ambari-Abfragen zurückgegebenen JSON-Dokumente einfacher verarbeitet werden:

    ```bash
    sudo apt -y install jq
    ```

3. Rufen Sie die Adresse der Kafka-Broker ab. Auch wenn in Ihrem Cluster möglicherweise viele Broker vorhanden sind, müssen Sie nur auf einen oder zwei verweisen. Um die Adresse der zwei Brokerhosts zu ermitteln, verwenden Sie den folgenden Befehl:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Geben Sie bei der entsprechenden Aufforderung das Kennwort des Anmeldekontos (Administrator) für den Cluster ein. Der zurückgegebene Wert ähnelt dem folgenden Text:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

4. Rufen Sie die Adresse der Apache ZooKeeper-Knoten ab. Auch wenn in Ihrem Cluster möglicherweise mehrere ZooKeeper-Knoten vorhanden sind, müssen Sie nur auf einen oder zwei verweisen. Um die Adresse der zwei ZooKeeper-Knoten zu ermitteln, verwenden Sie den folgenden Befehl:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

5. Bei der Ausführung des Connectors im eigenständigen Modus wird für die Kommunikation mit den Kafka-Brokern die Datei `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` verwendet. Verwenden Sie zum Bearbeiten der Datei `connect-standalone.properties` den folgenden Befehl:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

    * Um den eigenständigen Modus für den Edgeknoten zum Suchen der Kafka-Broker zu konfigurieren, suchen Sie nach der Zeile, die mit `bootstrap.servers=` beginnt. Ersetzen Sie den Wert `localhost:9092` durch die Brokerhosts aus dem vorherigen Schritt.

    * Fügen Sie in die Zeilen `key.converter=` und `value.converter=` die folgenden Werte ein:

        ```ini
        key.converter=org.apache.kafka.connect.storage.StringConverter
        value.converter=org.apache.kafka.connect.storage.StringConverter
        ```

        > [!IMPORTANT]  
        > Durch diese Änderung können Sie Tests mit dem Konsolenproducer, der bei Kafka enthalten ist, durchführen. Eventuell benötigen Sie für andere Producer und Consumer unterschiedliche Konverter. Informationen zur Verwendung anderer Konverterwerte finden Sie unter [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    * Fügen Sie am Ende der Datei eine Zeile mit dem folgenden Text hinzu:

        ```text
        consumer.max.poll.records=10
        ```

        > [!TIP]  
        > Durch diese Änderung soll Timeouts im Senkenconnector verhindert werden, indem dieser auf jeweils 10 Datensätze begrenzt wird. Weitere Informationen finden Sie unter [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

6. Drücken Sie zum Speichern der Datei __STRG+X__, __Y__ und dann die __EINGABETASTE__.

7. Um die vom Connector verwendeten Themen zu erstellen, verwenden Sie die folgenden Befehle:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Um zu bestätigen, dass die Themen `iotin` und `iotout` erstellt wurden, verwenden Sie den folgenden Befehl:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Das Thema `iotin` dient zum Empfangen von Nachrichten von IoT Hub. Das Thema `iotout` dient zum Senden von Nachrichten an IoT Hub.

## <a name="get-iot-hub-connection-information"></a>Abrufen der IoT Hub-Verbindungsinformationen

Führen Sie zum Abrufen von IoT Hub-Informationen, die vom Connector verwendet werden, die folgenden Schritte durch:

1. Rufen Sie den Event Hub-kompatiblen Endpunkt und den zugehörigen Endpunktnamen für Ihren IoT Hub ab. Sie können diese Informationen mit einer der folgenden Methoden ermitteln:

   * __Führen Sie die folgenden Schritte über das [Azure-Portal](https://portal.azure.com/)__ aus:

     1. Navigieren Sie zu Ihrer IoT Hub-Instanz, und klicken Sie auf __Endpunkte__.
     2. Wählen Sie unter __Integrierte Endpunkte__ die Option __Ereignisse__ aus.
     3. Kopieren Sie unter __Eigenschaften__ den Wert der folgenden Felder:

         * __Event Hub-kompatibler Name__
         * __Event Hub-kompatibler Endpunkt__
         * __Partitionen__

        > [!IMPORTANT]  
        > Der Endpunktwert aus dem Portal enthält möglicherweise zusätzlichen Text, der in diesem Beispiel nicht benötigt wird. Extrahieren Sie den Text, der dem Muster `sb://<randomnamespace>.servicebus.windows.net/` entspricht.

   * __Geben Sie über die [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ den folgenden Befehl ein:

       ```azure-cli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Ersetzen Sie `myhubname` durch den Namen Ihrer IoT Hub-Instanz. Die Antwort ähnelt dem folgenden Text:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Rufen Sie die __SAS-Richtlinie__ und den __Schlüssel__ ab. In diesem Beispiel wird der __Dienstschlüssel__ verwendet. Sie können diese Informationen mit einer der folgenden Methoden ermitteln:

    * __Führen Sie die folgenden Schritte über das [Azure-Portal](https://portal.azure.com/)__ aus:

        1. Wählen Sie __SAS-Richtlinien__ aus, und klicken Sie dann auf __Dienst__.
        2. Kopieren Sie den Wert unter __Primärschlüssel__.
        3. Kopieren Sie den Wert von __Verbindungszeichenfolge – Primärschlüssel__.

    * __Geben Sie über die [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ den folgenden Befehl ein:

        1. Um den Wert des Primärschlüssels zu ermitteln, verwenden Sie den folgenden Befehl:

            ```azure-cli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Ersetzen Sie `myhubname` durch den Namen Ihrer IoT Hub-Instanz. Die Antwort ist der Primärschlüssel für die Richtlinie `service` für diesen Hub.

        2. Verwenden Sie zum Abrufen der Verbindungszeichenfolge für die Richtlinie `service` den folgenden Befehl:

            ```azure-cli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Ersetzen Sie `myhubname` durch den Namen Ihrer IoT Hub-Instanz. Die Antwort ist die Verbindungszeichenfolge für die Richtlinie `service`.

## <a name="configure-the-source-connection"></a>Konfigurieren der Quellverbindung

Um die Quelle für die Arbeit mit Ihrer IoT Hub-Instanz zu konfigurieren, führen Sie die folgenden Aktionen über eine SSH-Verbindung mit dem Edgeknoten aus:

1. Erstellen Sie eine Kopie von der Datei `connect-iot-source.properties` im Verzeichnis `/usr/hdp/current/kafka-broker/config/`. Um die Datei aus dem Projekt „toketi-kafka-connect-iothub“ herunterzuladen, verwenden Sie den folgenden Befehl:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

2. Verwenden Sie zum Bearbeiten der Datei `connect-iot-source.properties` und Hinzufügen der IoT Hub-Informationen den folgenden Befehl:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    Suchen Sie im Editor nach den folgenden Einträgen, und ändern Sie sie:

   * `Kafka.Topic=PLACEHOLDER`: Ersetzen Sie  durch `iotin`. Die vom IoT Hub empfangenen Nachrichten werden in das Thema `iotin` platziert.
   * `IotHub.EventHubCompatibleName=PLACEHOLDER`: Ersetzen Sie `PLACEHOLDER` durch den Event Hub-kompatiblen Namen.
   * `IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`: Ersetzen Sie `PLACEHOLDER` durch den Event Hub-kompatiblen Endpunkt.
   * `IotHub.Partitions=PLACEHOLDER`: Ersetzen Sie `PLACEHOLDER` durch die Anzahl von Partitionen aus den vorherigen Schritten.
   * `IotHub.AccessKeyName=PLACEHOLDER`: Ersetzen Sie  durch `service`.
   * `IotHub.AccessKeyValue=PLACEHOLDER`: Ersetzen Sie `PLACEHOLDER` durch den Primärschlüssel der Richtlinie `service`.
   * `IotHub.StartType=PLACEHOLDER`: Ersetzen Sie `PLACEHOLDER` durch ein Datum im UTC-Format. Dieses Datum bezieht sich auf den Zeitpunkt, an dem der Connector mit der Prüfung auf Nachrichten begonnen hat. Das Datumsformat ist `yyyy-mm-ddThh:mm:ssZ`.
   * `BatchSize=100`: Ersetzen Sie  durch `5`. Diese Änderung bewirkt, dass der Connector Nachrichten in Kafka liest, sobald fünf neue Nachrichten in IoT Hub vorhanden sind.

     Eine Beispielkonfiguration finden Sie unter [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

3. Drücken Sie zum Speichern der Änderungen __STRG+X__, __Y__ und dann die __EINGABETASTE__.

Weitere Informationen zum Konfigurieren der Connectorquelle finden Sie unter [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

## <a name="configure-the-sink-connection"></a>Konfigurieren der Senkenverbindung

Um die Senkenverbindung für die Arbeit mit Ihrer IoT Hub-Instanz zu konfigurieren, führen Sie die folgenden Aktionen über eine SSH-Verbindung mit dem Edgeknoten aus:

1. Erstellen Sie eine Kopie von der Datei `connect-iothub-sink.properties` im Verzeichnis `/usr/hdp/current/kafka-broker/config/`. Um die Datei aus dem Projekt „toketi-kafka-connect-iothub“ herunterzuladen, verwenden Sie den folgenden Befehl:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

2. Verwenden Sie zum Bearbeiten der Datei `connect-iothub-sink.properties` und Hinzufügen der IoT Hub-Informationen den folgenden Befehl:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

    Suchen Sie im Editor nach den folgenden Einträgen, und ändern Sie sie:

   * `topics=PLACEHOLDER`: Ersetzen Sie  durch `iotout`. Nachrichten, die in das Thema `iotout` geschrieben wurden, werden an IoT Hub weitergeleitet.
   * `IotHub.ConnectionString=PLACEHOLDER`: Ersetzen Sie `PLACEHOLDER` durch die Verbindungszeichenfolge für die Richtlinie `service`.

     Eine Beispielkonfiguration finden Sie unter [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

3. Drücken Sie zum Speichern der Änderungen __STRG+X__, __Y__ und dann die __EINGABETASTE__.

Weitere Informationen zum Konfigurieren der Connectorsenke finden Sie unter [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="start-the-source-connector"></a>Starten des Quellconnectors

Geben Sie zum Starten des Quellconnectors den folgenden Befehl über eine SSH-Verbindung mit dem Edgeknoten ein:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
```

Sobald der Connector gestartet wurde, senden Sie Nachrichten an IoT Hub von Ihren Geräten. Während der Connector Nachrichten von IoT Hub liest und im Kafka-Thema speichert, protokolliert er Informationen in der Konsole:

```text
[2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.co
nnect.IotHubSourceTask:39)
[2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (
org.apache.kafka.connect.runtime.WorkerSourceTask:356)
```

> [!NOTE]  
> Beim Starten des Connectors werden möglicherweise mehrere Warnungen angezeigt. Diese Warnungen führen nicht zu Problemen mit dem Empfang von Nachrichten von IoT Hub.

Drücken Sie zum Beenden des Connectors __STRG+C__.

## <a name="start-the-sink-connector"></a>Starten des Senkenconnectors

Geben Sie über eine SSH-Verbindung mit dem Edgeknoten den folgenden Befehl ein, um den Senkenconnector zu starten:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Bei der Ausführung des Connectors werden Informationen ähnlich dem folgenden Text angezeigt:

```text
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connec
t.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Beim Starten des Connectors werden möglicherweise mehrere Warnungen angezeigt. Sie können dies ignorieren.

Führen Sie zum Senden von Nachrichten über den Connector folgende Schritte durch:

1. Öffnen Sie eine andere SSH-Sitzung mit dem Kafka-Cluster:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```
2. Um Nachrichten an das Thema `iotout` zu senden, verwenden Sie folgenden Befehl:

    > [!WARNING]  
    > Da es sich hierbei um eine neue SSH-Verbindung handelt, enthält die Variable `$KAFKABROKERS` keine Informationen. Verwenden Sie eine der folgenden Methoden, um diese festzulegen:
    >
    > * Führen Sie die ersten drei Schritte im Abschnitt [Konfigurieren von Apache Kafka](#configure-apache-kafka) aus.
    > * Verwenden Sie zum Abrufen der Werte `echo $KAFKABROKERS` aus der vorherigen SSH-Verbindung, und ersetzen Sie `$KAFKABROKERS` im folgenden Befehl durch die tatsächlichen Werte.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Bei diesem Befehl werden Sie nicht zur gewohnten Bash-Eingabeaufforderung weitergeleitet. Stattdessen sendet diese Tastatureingaben an das Thema `iotout`.

3. Fügen Sie zum Senden einer Nachricht an Ihr Gerät ein JSON-Dokument in der SSH-Sitzung für `kafka-console-producer` ein.

    > [!IMPORTANT]  
    > Sie müssen den Wert des Eintrags `"deviceId"` auf die ID Ihres Geräts festlegen. Im folgenden Beispiel ist das Gerät mit dem Namen `fakepi` versehen:

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"fakepi"}
    ```

    Das Schema für dieses JSON-Dokument wird unter [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) ausführlicher beschrieben.

    Wenn Sie das simulierte Raspberry Pi-Gerät verwenden und dieses ausgeführt wird, wird vom Gerät die folgende Nachricht protokolliert:

    ```text
    Receive message: Turn On
    ```

    Senden Sie erneut das JSON-Dokument, aber ändern Sie den Wert des Eintrags `"message"`. Der neue Wert wird vom Gerät protokolliert.

Weitere Informationen zur Verwendung des Senkenconnectors finden Sie unter [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie gelernt, wie Sie mithilfe der Apache Kafka Connect-API den IoT Kafka-Connector in HDInsight starten. Verwenden Sie die folgenden Links, um weitere Möglichkeiten zur Arbeit mit Kafka kennenzulernen:

* [Verwenden von Apache Spark mit Apache Kafka in HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Verwenden von Apache Storm mit Apache Kafka in HDInsight](../hdinsight-apache-storm-with-kafka.md)
