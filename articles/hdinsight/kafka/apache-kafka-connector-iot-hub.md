---
title: Verwendung von Apache Kafka in HDInsight mit Azure IoT Hub
description: In diesem Artikel erfahren Sie, wie Apache Kafka in HDInsight mit Azure IoT Hub verwendet wird. Das Projekt Kafka Connect für Azure IoT Hub stellt einen Quell- und einen Senkenconnector für Kafka bereit. Der Quellconnector kann Daten von IoT Hub lesen, während der Senkenconnector Daten in IoT Hub schreibt.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 11/26/2019
ms.openlocfilehash: 66b14e435b777595e23fcf5a98d4820f36d21a1a
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742039"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Verwendung von Apache Kafka in HDInsight mit Azure IoT Hub

Hier erfahren Sie, wie Sie mithilfe des [Apache Kafka Connect-Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub)-Connectors Daten zwischen Apache Kafka in HDInsight und Azure IoT Hub verschieben. In diesem Dokument erfahren Sie, wie der IoT Hub-Connector über einen Edgeknoten im Cluster ausgeführt wird.

Mit der Kafka Connect-API können Sie Connector implementieren, die kontinuierlich Daten in Kafka abrufen oder Daten über Kafka mithilfe von Push an ein anderes System übertragen. [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) ist ein Connector, der Daten aus Azure IoT Hub in Kafka pullt. Dieser kann Daten auch über Kafka mithilfe von Push an IoT Hub übertragen.

Für den Abruf von Daten aus IoT Hub verwenden Sie einen __Quellconnector__ . Für die Übertragung von Daten mithilfe von Push an IoT Hub verwenden Sie einen __Senkenconnector__ . Der IoT Hub-Connector stellt sowohl Quell- als auch Senkenconnector bereit.

In der folgenden Abbildung wird der Datenfluss zwischen Azure IoT Hub und Kafka in HDInsight unter Verwendung des Connectors gezeigt.

![Abbildung zum Datenfluss zwischen IoT Hub und Kafka über den Connector](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Weitere Informationen zur Connect-API finden Sie unter [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Apache Kafka-Cluster in HDInsight. Weitere Informationen finden Sie im Dokument [Schnellstart für Kafka in HDInsight](apache-kafka-get-started.md).

* Ein Edgeknoten im Kafka-Cluster. Weitere Informationen finden Sie im Dokument [Verwenden leerer Edgeknoten in Hadoop-Clustern in HDInsight](../hdinsight-apps-use-edge-node.md).

* Einen SSH-Client. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Eine Azure IoT Hub-Instanz und ein Gerät. In Rahmen dieses Artikels sollten Sie das [Verbinden des Raspberry Pi-Onlinesimulators mit Azure IoT Hub](../../iot-hub/iot-hub-raspberry-pi-web-simulator-get-started.md) in Betracht ziehen.

* [Scala-Buildtool](https://www.scala-sbt.org/).

## <a name="build-the-connector"></a>Erstellen des Connectors

1. Laden Sie die Quelle für den Connector unter [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) in Ihre lokale Umgebung herunter.

2. Navigieren Sie in einer Eingabeaufforderung zum Verzeichnis `toketi-kafka-connect-iothub-master`. Erstellen und packen Sie das Projekt dann mit dem folgenden Befehl:

    ```cmd
    sbt assembly
    ```

    Der Erstellungsvorgang nimmt einige Minuten in Anspruch. Mit diesem Befehl wird die Datei `kafka-connect-iothub-assembly_2.11-0.7.0.jar` im Zielverzeichnis `toketi-kafka-connect-iothub-master\target\scala-2.11` für das Projekt erstellt.

## <a name="install-the-connector"></a>Installieren des Connectors

1. Laden Sie die JAR-Datei auf den Edgeknoten Ihres Kafka in HDInsight-Clusters hoch. Bearbeiten Sie den folgenden Befehl, indem Sie `CLUSTERNAME` durch den Namen Ihres Clusters ersetzen. Nachstehend werden die Standardwerte für das SSH-Benutzerkonto und den Namen des [Edgeknotens](../hdinsight-apps-use-edge-node.md#access-an-edge-node) verwendet. Ändern Sie diese nach Bedarf.

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Stellen Sie nach dem Dateikopiervorgang über SSH eine Verbindung mit dem Edgeknoten her:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Verwenden Sie für die Installation des Connectors im Kafka-Verzeichnis `libs` den folgenden Befehl:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

Halten Sie die SSH-Verbindung für die restlichen Schritte aufrecht.

## <a name="configure-apache-kafka"></a>Konfigurieren von Apache Kafka

Führen Sie über Ihre SSH-Verbindung mit dem Edgeknoten die folgenden Schritte durch, um Kafka für die Ausführung des Connectors im eigenständigen Modus zu konfigurieren:

1. Richten Sie eine Kennwortvariable ein. Ersetzen Sie PASSWORD durch das Kennwort für die Clusteranmeldung, und geben Sie dann den folgenden Befehl ein:

    ```bash
    export password='PASSWORD'
    ```

1. Installieren Sie das Hilfsprogramm [jq](https://stedolan.github.io/jq/). Durch jq können die von Ambari-Abfragen zurückgegebenen JSON-Dokumente einfacher verarbeitet werden. Geben Sie den folgenden Befehl ein:

    ```bash
    sudo apt -y install jq
    ```

1. Rufen Sie die Adresse der Kafka-Broker ab. Auch wenn in Ihrem Cluster möglicherweise viele Broker vorhanden sind, müssen Sie nur auf einen oder zwei verweisen. Um die Adresse der zwei Brokerhosts zu ermitteln, verwenden Sie den folgenden Befehl:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Kopieren Sie diese Werte zur späteren Verwendung. Der zurückgegebene Wert ähnelt dem folgenden Text:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Rufen Sie die Adresse der Apache ZooKeeper-Knoten ab. Auch wenn in Ihrem Cluster möglicherweise mehrere ZooKeeper-Knoten vorhanden sind, müssen Sie nur auf einen oder zwei verweisen. Verwenden Sie den folgenden Befehl, um die Adressen in der Variable `KAFKAZKHOSTS` zu speichern:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. Bei der Ausführung des Connectors im eigenständigen Modus wird für die Kommunikation mit den Kafka-Brokern die Datei `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` verwendet. Verwenden Sie zum Bearbeiten der Datei `connect-standalone.properties` den folgenden Befehl:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. Nehmen Sie die folgenden Bearbeitungen vor:

    |Aktueller Wert |Neuer Wert | Comment |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|Ersetzen Sie den Wert `localhost:9092` durch die Brokerhosts aus dem vorherigen Schritt.|Konfiguriert die eigenständige Konfiguration für den Edgeknoten zum Suchen der Kafka-Broker.|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|Durch diese Änderung können Sie Tests mit dem Konsolenproducer, der bei Kafka enthalten ist, durchführen. Eventuell benötigen Sie für andere Producer und Consumer unterschiedliche Konverter. Informationen zur Verwendung anderer Konverterwerte finden Sie unter [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|Wie oben.|
    |–|`consumer.max.poll.records=10`|Am Ende der Datei hinzufügen. Durch diese Änderung soll Timeouts im Senkenconnector verhindert werden, indem dieser auf jeweils 10 Datensätze begrenzt wird. Weitere Informationen finden Sie unter [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).|

1. Drücken Sie zum Speichern der Datei __STRG+X__ , __Y__ und dann die __EINGABETASTE__ .

1. Um die vom Connector verwendeten Themen zu erstellen, verwenden Sie die folgenden Befehle:

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

     1. Navigieren Sie zu Ihrer IoT Hub-Instanz, und klicken Sie auf __Endpunkte__ .
     2. Wählen Sie unter __Integrierte Endpunkte__ die Option __Ereignisse__ aus.
     3. Kopieren Sie unter __Eigenschaften__ den Wert der folgenden Felder:

         * __Event Hub-kompatibler Name__
         * __Event Hub-kompatibler Endpunkt__
         * __Partitionen__

        > [!IMPORTANT]  
        > Der Endpunktwert aus dem Portal enthält möglicherweise zusätzlichen Text, der in diesem Beispiel nicht benötigt wird. Extrahieren Sie den Text, der dem Muster `sb://<randomnamespace>.servicebus.windows.net/` entspricht.

   * __Geben Sie über die [Azure CLI](/cli/azure/get-started-with-azure-cli)__ den folgenden Befehl ein:

       ```azurecli
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

        1. Wählen Sie __SAS-Richtlinien__ aus, und klicken Sie dann auf __Dienst__ .
        2. Kopieren Sie den Wert unter __Primärschlüssel__ .
        3. Kopieren Sie den Wert von __Verbindungszeichenfolge – Primärschlüssel__ .

    * __Geben Sie über die [Azure CLI](/cli/azure/get-started-with-azure-cli)__ den folgenden Befehl ein:

        1. Um den Wert des Primärschlüssels zu ermitteln, verwenden Sie den folgenden Befehl:

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Ersetzen Sie `myhubname` durch den Namen Ihrer IoT Hub-Instanz. Die Antwort ist der Primärschlüssel für die Richtlinie `service` für diesen Hub.

        2. Verwenden Sie zum Abrufen der Verbindungszeichenfolge für die Richtlinie `service` den folgenden Befehl:

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Ersetzen Sie `myhubname` durch den Namen Ihrer IoT Hub-Instanz. Die Antwort ist die Verbindungszeichenfolge für die Richtlinie `service`.

## <a name="configure-the-source-connection"></a>Konfigurieren der Quellverbindung

Um die Quelle für die Arbeit mit Ihrer IoT Hub-Instanz zu konfigurieren, führen Sie die folgenden Aktionen über eine SSH-Verbindung mit dem Edgeknoten aus:

1. Erstellen Sie eine Kopie von der Datei `connect-iot-source.properties` im Verzeichnis `/usr/hdp/current/kafka-broker/config/`. Um die Datei aus dem Projekt „toketi-kafka-connect-iothub“ herunterzuladen, verwenden Sie den folgenden Befehl:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. Verwenden Sie zum Bearbeiten der Datei `connect-iot-source.properties` und Hinzufügen der IoT Hub-Informationen den folgenden Befehl:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. Suchen Sie im Editor nach den folgenden Einträgen, und ändern Sie sie:

    |Aktueller Wert |Edit (Bearbeiten)|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|Ersetzen Sie `PLACEHOLDER` durch `iotin`. Die vom IoT Hub empfangenen Nachrichten werden in das Thema `iotin` platziert.|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|Ersetzen Sie `PLACEHOLDER` durch den Event Hub-kompatiblen Namen.|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|Ersetzen Sie `PLACEHOLDER` durch den Event Hub-kompatiblen Endpunkt.|
    |`IotHub.AccessKeyName=PLACEHOLDER`|Ersetzen Sie `PLACEHOLDER` durch `service`.|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|Ersetzen Sie `PLACEHOLDER` durch den Primärschlüssel der Richtlinie `service`.|
    |`IotHub.Partitions=PLACEHOLDER`|Ersetzen Sie `PLACEHOLDER` durch die Anzahl von Partitionen aus den vorherigen Schritten.|
    |`IotHub.StartTime=PLACEHOLDER`|Ersetzen Sie `PLACEHOLDER` durch ein Datum im UTC-Format. Dieses Datum bezieht sich auf den Zeitpunkt, an dem der Connector mit der Prüfung auf Nachrichten begonnen hat. Das Datumsformat ist `yyyy-mm-ddThh:mm:ssZ`.|
    |`BatchSize=100`|Ersetzen Sie `100` durch `5`. Diese Änderung bewirkt, dass der Connector Nachrichten in Kafka liest, sobald fünf neue Nachrichten in IoT Hub vorhanden sind.|

    Eine Beispielkonfiguration finden Sie unter [Kafka Connect Source Connector for Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md) (Kafka Connect-Quellconnector für Azure IoT Hub).

1. Drücken Sie zum Speichern der Änderungen __STRG+X__ , __Y__ und dann die __EINGABETASTE__ .

Weitere Informationen zum Konfigurieren der Connectorquelle finden Sie unter [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

## <a name="configure-the-sink-connection"></a>Konfigurieren der Senkenverbindung

Um die Senkenverbindung für die Arbeit mit Ihrer IoT Hub-Instanz zu konfigurieren, führen Sie die folgenden Aktionen über eine SSH-Verbindung mit dem Edgeknoten aus:

1. Erstellen Sie eine Kopie von der Datei `connect-iothub-sink.properties` im Verzeichnis `/usr/hdp/current/kafka-broker/config/`. Um die Datei aus dem Projekt „toketi-kafka-connect-iothub“ herunterzuladen, verwenden Sie den folgenden Befehl:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. Verwenden Sie zum Bearbeiten der Datei `connect-iothub-sink.properties` und Hinzufügen der IoT Hub-Informationen den folgenden Befehl:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. Suchen Sie im Editor nach den folgenden Einträgen, und ändern Sie sie:

    |Aktueller Wert |Edit (Bearbeiten)|
    |---|---|
    |`topics=PLACEHOLDER`|Ersetzen Sie `PLACEHOLDER` durch `iotout`. Nachrichten, die in das Thema `iotout` geschrieben wurden, werden an IoT Hub weitergeleitet.|
    |`IotHub.ConnectionString=PLACEHOLDER`|Ersetzen Sie `PLACEHOLDER` durch die Verbindungszeichenfolge für die Richtlinie `service`.|

    Eine Beispielkonfiguration finden Sie unter [Kafka Connect Sink Connector for Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) (Kafka Connect-Senkenconnector für Azure IoT Hub).

1. Drücken Sie zum Speichern der Änderungen __STRG+X__ , __Y__ und dann die __EINGABETASTE__ .

Weitere Informationen zum Konfigurieren der Connectorsenke finden Sie unter [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="start-the-source-connector"></a>Starten des Quellconnectors

1. Geben Sie zum Starten des Quellconnectors den folgenden Befehl über eine SSH-Verbindung mit dem Edgeknoten ein:

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    Sobald der Connector gestartet wurde, senden Sie Nachrichten an IoT Hub von Ihren Geräten. Während der Connector Nachrichten von IoT Hub liest und im Kafka-Thema speichert, protokolliert er Informationen in der Konsole:

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > Beim Starten des Connectors werden möglicherweise mehrere Warnungen angezeigt. Diese Warnungen führen nicht zu Problemen mit dem Empfang von Nachrichten von IoT Hub.

1. Brechen Sie den Connector nach einigen Minuten ab, indem Sie zweimal **STRG+C** drücken. Das Anhalten des Connectors dauert einige Minuten.

## <a name="start-the-sink-connector"></a>Starten des Senkenconnectors

Geben Sie über eine SSH-Verbindung mit dem Edgeknoten den folgenden Befehl ein, um den Senkenconnector zu starten:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Bei der Ausführung des Connectors werden Informationen ähnlich dem folgenden Text angezeigt:

```output
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Beim Starten des Connectors werden möglicherweise mehrere Warnungen angezeigt. Sie können dies ignorieren.

## <a name="send-messages"></a>Senden von Nachrichten

Führen Sie zum Senden von Nachrichten über den Connector folgende Schritte durch:

1. Öffnen Sie *eine zweite* SSH-Sitzung mit dem Kafka-Cluster:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Rufen Sie die Adresse der Kafka-Broker für die neue SSH-Sitzung ab. Ersetzen Sie PASSWORD durch das Kennwort für die Clusteranmeldung, und geben Sie dann den folgenden Befehl ein:

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. Um Nachrichten an das Thema `iotout` zu senden, verwenden Sie folgenden Befehl:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Bei diesem Befehl werden Sie nicht zur gewohnten Bash-Eingabeaufforderung weitergeleitet. Stattdessen sendet diese Tastatureingaben an das Thema `iotout`.

1. Fügen Sie zum Senden einer Nachricht an Ihr Gerät ein JSON-Dokument in der SSH-Sitzung für `kafka-console-producer` ein.

    > [!IMPORTANT]  
    > Sie müssen den Wert des Eintrags `"deviceId"` auf die ID Ihres Geräts festlegen. Im folgenden Beispiel ist das Gerät mit dem Namen `myDeviceId` versehen:

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    Das Schema für dieses JSON-Dokument wird unter [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) ausführlicher beschrieben.

    Wenn Sie das simulierte Raspberry Pi-Gerät verwenden und dieses ausgeführt wird, protokolliert das Gerät die folgende Nachricht:

    ```output
    Receive message: Turn On
    ```

    Senden Sie erneut das JSON-Dokument, aber ändern Sie den Wert des Eintrags `"message"`. Der neue Wert wird vom Gerät protokolliert.

Weitere Informationen zur Verwendung des Senkenconnectors finden Sie unter [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie gelernt, wie Sie mithilfe der Apache Kafka Connect-API den IoT Kafka-Connector in HDInsight starten. Verwenden Sie die folgenden Links, um weitere Möglichkeiten zur Arbeit mit Kafka kennenzulernen:

* [Verwenden von Apache Spark mit Apache Kafka in HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Verwenden von Apache Storm mit Apache Kafka in HDInsight](../hdinsight-apache-storm-with-kafka.md)
