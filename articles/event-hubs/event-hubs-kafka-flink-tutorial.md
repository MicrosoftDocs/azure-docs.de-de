---
title: Verwenden von Apache Flink für Apache Kafka – Azure Event Hubs | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen dazu, wie Sie Apache Flink mit einem Azure Event Hub verbinden können.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 1d382270248e95b1b973f57f67ebf81160f03a16
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369504"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Verwenden von Apache Flink mit Azure Event Hubs für Apache Kafka
Dieses Tutorial veranschaulicht, wie Sie Apache Flink mit einem Event Hub verbinden können, ohne Ihre Protokollclients ändern oder Ihre eigenen Cluster ausführen zu müssen. Weitere Informationen zur Event Hubs-Unterstützung für das Apache Kafka-Consumerprotokoll finden Sie unter [Event Hubs für Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).


In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Erstellen eines Event Hubs-Namespace
> * Klonen des Beispielprojekts
> * Ausführen des Flink-Producers 
> * Ausführen des Flink-Consumers

> [!NOTE]
> Dieses Beispiel ist auf [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor dem Durchführen dieses Tutorials sicher, dass die folgenden Voraussetzungen erfüllt sind:

* Lesen Sie den Artikel [Event Hubs für Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Ein Azure-Abonnement. Wenn Sie keins besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.
* [Java Development Kit (JDK) 1.7+](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * Führen Sie unter Ubuntu `apt-get install default-jdk` aus, um das JDK zu installieren.
    * Achten Sie darauf, dass die Umgebungsvariable „JAVA_HOME“ auf den Ordner verweist, in dem das JDK installiert ist.
* Führen Sie den [Download](https://maven.apache.org/download.cgi) und die [Installation](https://maven.apache.org/install.html) eines binären Maven-Archivs durch.
    * Unter Ubuntu können Sie `apt-get install maven` ausführen, um Maven zu installieren.
* [Git-Client](https://www.git-scm.com/downloads)
    * Unter Ubuntu können Sie `sudo apt-get install git` ausführen, um Git zu installieren.

## <a name="create-an-event-hubs-namespace"></a>Erstellen eines Event Hubs-Namespace

Ein Event Hubs-Namespace ist erforderlich, um Nachrichten an einen Event Hubs-Dienst zu senden oder von diesem zu empfangen. Anweisungen zum Erstellen eines Namespace und eines Event Hub finden Sie unter [Erstellen eines Event Hubs](event-hubs-create.md). Kopieren Sie die Event Hubs-Verbindungszeichenfolge zur späteren Verwendung.

## <a name="clone-the-example-project"></a>Klonen des Beispielprojekts

Nachdem Sie nun über die Event Hubs-Verbindungszeichenfolge verfügen, können Sie das Azure Event Hubs für Kafka-Repository klonen und zum Unterordner `flink` navigieren:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Ausführen des Flink-Producers

Verwenden Sie das bereitgestellte Beispiel des Flink-Producers, und senden Sie Nachrichten an den Event Hubs-Dienst.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Bereitstellen eines Kafka-fähigen Event Hubs-Endpunkts

#### <a name="producerconfig"></a>producer.config

Update der `bootstrap.servers`- und `sasl.jaas.config`-Werte in `producer/src/main/resources/producer.config` zur Weiterleitung des Producers an den Kafka-fähigen Event Hubs-Endpunkt mit der richtigen Authentifizierung.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Ersetzen Sie `{YOUR.EVENTHUBS.CONNECTION.STRING}` durch die Verbindungszeichenfolge für Ihren Event Hubs-Namespace. Anweisungen zum Abrufen der Verbindungszeichenfolge finden Sie unter [Abrufen einer Event Hubs-Verbindungszeichenfolge](event-hubs-get-connection-string.md). Hier sehen Sie eine Beispielkonfiguration: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

### <a name="run-producer-from-the-command-line"></a>Ausführen des Producers über die Befehlszeile

Zum Ausführen des Producers über die Befehlszeile generieren Sie die JAR-Datei. Führen Sie sie innerhalb von Maven aus (oder generieren Sie die JAR-Datei mit Maven, und führen Sie sie dann durch Hinzufügen der erforderlichen Kafka-JAR(s) an den Klassenpfad in Java aus):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Der Producer beginnt mit dem Senden von Ereignissen an den Event Hub zum Thema `test` und dem Drucken der Ereignisse an „stdout“.

## <a name="run-flink-consumer"></a>Ausführen des Flink-Consumers

Verwenden Sie das bereitgestellte Consumer-Beispiel, und empfangen Sie Nachrichten vom Event Hub. 

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Bereitstellen eines Kafka-fähigen Event Hubs-Endpunkts

#### <a name="consumerconfig"></a>consumer.config

Aktualisieren Sie die `bootstrap.servers`- und `sasl.jaas.config`-Werte in `consumer/src/main/resources/consumer.config` zur Weiterleitung des Consumers an den Kafka-fähigen Event Hubs-Endpunkt mit der richtigen Authentifizierung.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Ersetzen Sie `{YOUR.EVENTHUBS.CONNECTION.STRING}` durch die Verbindungszeichenfolge für Ihren Event Hubs-Namespace. Anweisungen zum Abrufen der Verbindungszeichenfolge finden Sie unter [Abrufen einer Event Hubs-Verbindungszeichenfolge](event-hubs-get-connection-string.md). Hier sehen Sie eine Beispielkonfiguration: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-consumer-from-the-command-line"></a>Ausführen des Consumers über die Befehlszeile

Zum Ausführen des Consumers über die Befehlszeile generieren Sie die JAR-Datei, und führen Sie sie innerhalb von Maven aus (oder generieren Sie die JAR-Datei mit Maven, und führen Sie sie dann durch Hinzufügen der erforderlichen Kafka-JAR(s) an den Klassenpfad in Java aus):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Wenn der Event Hub über Ereignisse verfügt (wenn der Producer z. B. ebenfalls ausgeführt wird), erhält der Consumer ab sofort Ereignisse aus dem Thema `test`.

Weitere Informationen zur Verbindung von Flink und Kafka finden Sie unter [Flink's Kafka Connector Guide (Flinks Leitfaden zur Kafka-Verbindung)](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Event Hubs für Kafka finden Sie in folgenden Artikeln:  

- [Spiegeln eines Kafka-Brokers in einem Event Hub](event-hubs-kafka-mirror-maker-tutorial.md)
- [Verbinden von Apache Spark mit einem Event Hub](event-hubs-kafka-spark-tutorial.md)
- [Integrieren von Kafka Connect in einen Event Hub](event-hubs-kafka-connect-tutorial.md)
- [Erkunden von Beispielen auf GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Verbinden von Akka Streams mit einem Event Hub](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka-Entwicklerleitfaden für Azure Event Hubs](apache-kafka-developer-guide.md)