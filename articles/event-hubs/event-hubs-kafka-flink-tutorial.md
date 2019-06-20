---
title: Verwenden von Apache Flink für Apache Kafka – Azure Event Hubs | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen dazu, wie Sie Apache Flink mit einem Apache Kafka-fähigen Azure Event Hub verbinden können.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: dc4a982dde62f62eb8f2d91a61fd70ba79fa13d5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60821431"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Verwenden von Apache Flink mit Azure Event Hubs für Apache Kafka
Dieses Tutorial veranschaulicht, wie Sie Apache Flink mit Kafka-fähigen Event Hubs verbinden können, ohne Ihre Protokollclients ändern oder Ihre eigenen Cluster ausführen zu müssen. Azure Event Hubs unterstützt [Apache Kafka Version 1.0](https://kafka.apache.org/10/documentation.html).

Einer der Hauptvorteile der Verwendung von Apache Kafka ist das Ökosystem an Frameworks, mit dem eine Verbindung hergestellt werden kann. Kafka-fähige Event Hubs kombiniert die Flexibilität von Kafka mit der Skalierbarkeit, Konsistenz und Unterstützung des Azure-Ökosystems.

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
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * Führen Sie unter Ubuntu `apt-get install default-jdk` aus, um das JDK zu installieren.
    * Achten Sie darauf, dass die Umgebungsvariable „JAVA_HOME“ auf den Ordner verweist, in dem das JDK installiert ist.
* Führen Sie den [Download](https://maven.apache.org/download.cgi) und die [Installation](https://maven.apache.org/install.html) eines binären Maven-Archivs durch.
    * Unter Ubuntu können Sie `apt-get install maven` ausführen, um Maven zu installieren.
* [Git-Client](https://www.git-scm.com/downloads)
    * Unter Ubuntu können Sie `sudo apt-get install git` ausführen, um Git zu installieren.

## <a name="create-an-event-hubs-namespace"></a>Erstellen eines Event Hubs-Namespace

Ein Event Hubs-Namespace ist erforderlich, um Nachrichten an einen Event Hubs-Dienst zu senden oder von diesem zu empfangen. Informationen zum Abrufen eines Kafka-fähigen Event Hubs-Endpunkts finden Sie unter [Create a Kafka Enabled Event Hub](event-hubs-create-kafka-enabled.md) (Erstellen eines Kafka-fähigen Event Hub). Kopieren Sie die Event Hubs-Verbindungszeichenfolge zur späteren Verwendung.

## <a name="clone-the-example-project"></a>Klonen des Beispielprojekts

Nachdem Sie nun über eine Kafka-fähige Event Hubs-Verbindungszeichenfolge verfügen, können Sie das Azure Event Hubs für Kafka-Repository klonen und zum Unterordner `flink` navigieren:

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

### <a name="run-producer-from-the-command-line"></a>Ausführen des Producers über die Befehlszeile

Zum Ausführen des Producers über die Befehlszeile generieren Sie die JAR-Datei. Führen Sie sie innerhalb von Maven aus (oder generieren Sie die JAR-Datei mit Maven, und führen Sie sie dann durch Hinzufügen der erforderlichen Kafka-JAR(s) an den Klassenpfad in Java aus):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Der Producer beginnt mit dem Senden von Ereignissen an den Kafka-fähigen Event Hub zu Thema `test` und dem Drucken der Ereignisse an „stdout“.

## <a name="run-flink-consumer"></a>Ausführen des Flink-Consumers

Verwenden Sie das bereitgestellte Consumer-Beispiel, und empfangen Sie Nachrichten aus dem Kafka-fähigen Event Hubs-Dienst.

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

### <a name="run-consumer-from-the-command-line"></a>Ausführen des Consumers über die Befehlszeile

Zum Ausführen des Consumers über die Befehlszeile generieren Sie die JAR-Datei, und führen Sie sie innerhalb von Maven aus (oder generieren Sie die JAR-Datei mit Maven, und führen Sie sie dann durch Hinzufügen der erforderlichen Kafka-JAR(s) an den Klassenpfad in Java aus):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Wenn der Kafka-fähige Event Hub über Ereignisse verfügt (z.B., wenn der Producer auch ausgeführt wird), erhält der Consumer jetzt Ereignisse aus dem Thema `test`.

Weitere Informationen zur Verbindung von Flink und Kafka finden Sie unter [Flink's Kafka Connector Guide (Flinks Leitfaden zur Kafka-Verbindung)](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html).

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie erfahren, wie Sie Apache Flink mit Kafka-fähigen Event Hubs verbinden können, ohne Ihre Protokollclients ändern oder Ihre eigenen Cluster ausführen zu müssen. Im Rahmen dieses Tutorials haben Sie die folgenden Schritte ausgeführt: 

> [!div class="checklist"]
> * Erstellen eines Event Hubs-Namespace
> * Klonen des Beispielprojekts
> * Ausführen des Flink-Producers 
> * Ausführen des Flink-Consumers

Weitere Informationen zu Event Hubs und Event Hubs für Kafka finden Sie unter folgendem Thema:  

- [Informationen zu Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs für Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Erstellen von Kafka-fähigen Event Hubs](event-hubs-create-kafka-enabled.md)
- [Streamen von Ereignissen in Event Hubs aus Kafka-Anwendungen](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Spiegeln eines Kafka-Brokers in einem Kafka-fähigen Event Hub](event-hubs-kafka-mirror-maker-tutorial.md)
- [Verbinden von Apache Spark mit einem Kafka-fähigen Event Hub](event-hubs-kafka-spark-tutorial.md)
- [Integrieren von Kafka Connect in einen Kafka-fähigen Event Hub](event-hubs-kafka-connect-tutorial.md)
- [Verbinden von Akka Streams mit einem Kafka-fähigen Event Hub](event-hubs-kafka-akka-streams-tutorial.md)
- [Erkunden von Beispielen auf GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
