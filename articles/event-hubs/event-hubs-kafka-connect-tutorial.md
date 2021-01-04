---
title: Apache Kafka Connect-Integration – Azure Event Hubs | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zur Verwendung von Kafka Connect mit Azure Event Hubs für Kafka.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: d37d2465d9389a0bcfaabdec32bad0c86846cfb2
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369538"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview"></a>Integrieren der Apache Kafka Connect-Unterstützung in Azure Event Hubs (Vorschauversion)
Wenn der Umfang der Erfassungen für geschäftliche Zwecke zunimmt, gilt dies auch für die Erfassung für verschiedene externe Quellen und Senken. [Apache Kafka Connect](https://kafka.apache.org/documentation/#connect) stellt ein Framework dieser Art über einen Kafka-Cluster bereit, um für Daten die Verbindungsherstellung und den Import bzw. Export für alle externen Systeme durchzuführen, z.B. MySQL, Hadoop Distributed File System und das Dateisystem. In diesem Tutorial wird die Nutzung eines Kafka Connect-Frameworks mit Event Hubs Schritt für Schritt beschrieben.

In diesem Tutorial wird die Vorgehensweise zum Integrieren von Kafka Connect in einen Event Hub und zum Bereitstellen von einfachen FileStreamSource- und FileStreamSink-Connectors beschrieben. Diese Funktion steht derzeit als Vorschau zur Verfügung. Die Connectors sind zwar nicht für die Verwendung in der Produktion bestimmt, aber sie stellen ein End-to-End-Szenario für Kafka Connect dar, bei dem Azure Event Hubs als Kafka-Broker fungiert.

> [!NOTE]
> Dieses Beispiel ist auf [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) verfügbar.

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen eines Event Hubs-Namespace
> * Klonen des Beispielprojekts
> * Konfigurieren von Kafka Connect für Event Hubs
> * Ausführen von Kafka Connect
> * Erstellen von Connectors

## <a name="prerequisites"></a>Voraussetzungen
Stellen Sie für das Durcharbeiten dieses Tutorials zur Vorgehensweise sicher, dass die folgenden Voraussetzungen erfüllt sind:

- Azure-Abonnement. Falls Sie nicht über ein Abonnement verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).
- [Git-Client](https://www.git-scm.com/downloads)
- Linux/macOS
- Kafka-Release (Version 1.1.1, Scala-Version 2.11), verfügbar unter [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- Lesen Sie den Einführungsartikel [Event Hubs für Apache Kafka](./event-hubs-for-kafka-ecosystem-overview.md).

## <a name="create-an-event-hubs-namespace"></a>Erstellen eines Event Hubs-Namespace
Ein Event Hubs-Namespace ist erforderlich, um Nachrichten an einen Event Hubs-Dienst zu senden und von diesem zu empfangen. Anweisungen zum Erstellen eines Namespace und eines Event Hub finden Sie unter [Erstellen eines Event Hubs](event-hubs-create.md). Rufen Sie die Event Hubs-Verbindungszeichenfolge und den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) zur späteren Verwendung ab. Anweisungen hierzu finden Sie unter [Get an Event Hubs connection string](event-hubs-get-connection-string.md) (Abrufen einer Event Hubs-Verbindungszeichenfolge). 

## <a name="clone-the-example-project"></a>Klonen des Beispielprojekts
Klonen Sie das Azure Event Hubs-Repository, und navigieren Sie zum Unterordner „tutorials/connect“: 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>Konfigurieren von Kafka Connect für Event Hubs
Es ist nur eine minimale Neukonfiguration erforderlich, wenn Sie den Kafka Connect-Durchsatz von Kafka an Event Hubs umleiten.  Im folgenden Beispiel `connect-distributed.properties` ist dargestellt, wie Sie Connect konfigurieren, um die Authentifizierung und Kommunikation mit dem Kafka-Endpunkt unter Event Hubs einzurichten:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

> [!IMPORTANT]
> Ersetzen Sie `{YOUR.EVENTHUBS.CONNECTION.STRING}` durch die Verbindungszeichenfolge für Ihren Event Hubs-Namespace. Anweisungen zum Abrufen der Verbindungszeichenfolge finden Sie unter [Abrufen einer Event Hubs-Verbindungszeichenfolge](event-hubs-get-connection-string.md). Hier sehen Sie eine Beispielkonfiguration: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


## <a name="run-kafka-connect"></a>Ausführen von Kafka Connect

In diesem Schritt wird ein Kafka Connect-Worker lokal im verteilten Modus gestartet, indem Event Hubs zum Warten des Clusterzustands verwendet wird.

1. Speichern Sie die obige Datei `connect-distributed.properties` lokal.  Achten Sie darauf, alle Werte in geschweiften Klammern zu ersetzen.
2. Navigieren Sie zum Speicherort des Kafka-Release auf Ihrem Computer.
4. Führen Sie `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` aus.  Die Connect-Worker-REST-API ist für die Interaktion bereit, wenn `'INFO Finished starting connectors and tasks'` angezeigt wird. 

> [!NOTE]
> Kafka Connect verwendet die Kafka-AdminClient-API, um automatisch Themen mit empfohlenen Konfigurationen, einschließlich Komprimierung, zu erstellen. Eine schnelle Überprüfung des Namespace im Azure-Portal ergibt, dass die internen Themen des Connect-Workers automatisch erstellt wurden.
>
>Interne Kafka Connect-Themen **müssen Komprimierung verwenden**.  Das Event Hubs-Team ist nicht für die Korrektur falscher Konfigurationen zuständig, sollten interne Connect-Themen nicht ordnungsgemäß konfiguriert sein.

### <a name="create-connectors"></a>Erstellen von Connectors
In diesem Abschnitt wird die Einrichtung der Connectors FileStreamSource und FileStreamSink Schritt für Schritt beschrieben. 

1. Erstellen Sie ein Verzeichnis für Ein- und Ausgabedatendateien.
    ```bash
    mkdir ~/connect-quickstart
    ```

2. Erstellen Sie zwei Dateien: eine Datei mit Startdaten, aus der der FileStreamSource-Connector liest, und eine weitere Datei, in die der FileStreamSink-Connector schreibt.
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. Erstellen Sie einen FileStreamSource-Connector.  Achten Sie darauf, dass Sie den Inhalt der geschweiften Klammern durch den Pfad Ihres Basisverzeichnisses ersetzen.
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    Auf Ihrer Event Hubs-Instanz sollte nach dem Ausführen des obigen Befehls der Event Hub `connect-quickstart` angezeigt werden.
4. Überprüfen Sie den Status des Quellconnectors.
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    Optional können Sie den [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases) nutzen, um zu überprüfen, ob Ereignisse im Thema `connect-quickstart` eingegangen sind.

5. Erstellen Sie einen FileStreamSink-Connector.  Achten Sie auch hier wieder darauf, dass Sie den Inhalt der geschweiften Klammern durch den Pfad Ihres Basisverzeichnisses ersetzen.
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. Überprüfen Sie den Status des Senkenconnectors.
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. Stellen Sie sicher, dass die Daten zwischen den Dateien repliziert wurden und in beiden Dateien identisch sind.
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>Cleanup
Mit Kafka Connect werden Event Hub-Themen zum Speichern von Konfigurationen, Offsets und des Status erstellt, die auch dann beibehalten werden, nachdem der Connect-Cluster heruntergefahren wurde. Wenn diese Beibehaltung nicht erwünscht ist, ist es ratsam, diese Themen zu löschen. Sie können auch den Event Hub `connect-quickstart` löschen, der in dieser exemplarischen Vorgehensweise erstellt wurde.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Event Hubs für Kafka finden Sie in folgenden Artikeln:  

- [Spiegeln eines Kafka-Brokers in einem Event Hub](event-hubs-kafka-mirror-maker-tutorial.md)
- [Verbinden von Apache Spark mit einem Event Hub](event-hubs-kafka-spark-tutorial.md)
- [Verbinden von Apache Flink mit einem Event Hub](event-hubs-kafka-flink-tutorial.md)
- [Erkunden von Beispielen auf GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Verbinden von Akka Streams mit einem Event Hub](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka-Entwicklerleitfaden für Azure Event Hubs](apache-kafka-developer-guide.md)
