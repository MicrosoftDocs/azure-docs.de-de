---
title: 'Verwenden von Akka Streams für Apache Kafka: Azure Event Hubs | Microsoft-Dokumentation'
description: Dieser Artikel enthält Informationen dazu, wie Sie Akka Streams mit einem Apache Kafka-fähigen Azure Event Hub verbinden können.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 32d710464cf61f998e18af28887561cefd2b1b3f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60821549"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Verwenden von Akka Streams mit Event Hubs für Apache Kafka
Dieses Tutorial veranschaulicht, wie Sie Akka Streams mit Kafka-fähigen Event Hubs verbinden können, ohne Ihre Protokollclients ändern oder Ihre eigenen Cluster ausführen zu müssen. Azure Event Hubs für Kafka unterstützt [Apache Kafka Version 1.0](https://kafka.apache.org/10/documentation.html).

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Erstellen eines Event Hubs-Namespace
> * Klonen des Beispielprojekts
> * Ausführen des Akka Streams-Producers 
> * Ausführen des Akka Streams-Consumers

> [!NOTE]
> Dieses Beispiel ist auf [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor dem Durchführen dieses Tutorials sicher, dass die folgenden Voraussetzungen erfüllt sind:

* Lesen Sie den Artikel [Event Hubs für Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Ein Azure-Abonnement. Wenn Sie keins besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.
* [Java Development Kit (JDK) 1.8 oder höher](https://aka.ms/azure-jdks)
    * Führen Sie unter Ubuntu `apt-get install default-jdk` aus, um das JDK zu installieren.
    * Achten Sie darauf, dass die Umgebungsvariable „JAVA_HOME“ auf den Ordner verweist, in dem das JDK installiert ist.
* Führen Sie den [Download](https://maven.apache.org/download.cgi) und die [Installation](https://maven.apache.org/install.html) eines binären Maven-Archivs durch.
    * Unter Ubuntu können Sie `apt-get install maven` ausführen, um Maven zu installieren.
* [Git-Client](https://www.git-scm.com/downloads)
    * Unter Ubuntu können Sie `sudo apt-get install git` ausführen, um Git zu installieren.

## <a name="create-an-event-hubs-namespace"></a>Erstellen eines Event Hubs-Namespace

Ein Event Hubs-Namespace ist erforderlich, um Nachrichten an einen Event Hubs-Dienst zu senden oder von diesem zu empfangen. Informationen zum Abrufen eines Kafka-fähigen Event Hubs-Endpunkts finden Sie unter [Create a Kafka Enabled Event Hub](event-hubs-create-kafka-enabled.md) (Erstellen eines Kafka-fähigen Event Hub). Kopieren Sie die Event Hubs-Verbindungszeichenfolge zur späteren Verwendung.

## <a name="clone-the-example-project"></a>Klonen des Beispielprojekts

Nachdem Sie nun über eine Kafka-fähige Event Hubs-Verbindungszeichenfolge verfügen, können Sie das Azure Event Hubs für Kafka-Repository klonen und zum Unterordner `akka` navigieren:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Ausführen des Akka Streams-Producers

Verwenden Sie das bereitgestellte Beispiel des Akka Streams-Producers, und senden Sie Nachrichten an den Event Hubs-Dienst.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Bereitstellen eines Event Hubs-Kafka-Endpunkts

#### <a name="producer-applicationconf"></a>„application.conf“ des Producers

Aktualisieren Sie die `bootstrap.servers`- und `sasl.jaas.config`-Werte in `producer/src/main/resources/application.conf` zur Weiterleitung des Producers an den Event Hubs-Kafka-Endpunkt mit der richtigen Authentifizierung.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>Ausführen des Producers über die Befehlszeile

Zum Ausführen des Producers über die Befehlszeile generieren Sie die JAR-Datei, und führen Sie sie in Maven aus (oder generieren Sie die JAR-Datei mit Maven, und führen Sie sie dann durch Hinzufügen der erforderlichen Kafka JAR(s) an den Klassenpfad in Java aus):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

Der Producer beginnt mit dem Senden von Ereignissen an den Kafka-fähigen Event Hub zum Thema `test` und druckt die Ereignisse an „stdout“.

## <a name="run-akka-streams-consumer"></a>Ausführen des Akka Streams-Consumers

Verwenden Sie das bereitgestellte Consumer-Beispiel, und empfangen Sie Nachrichten aus den Kafka-fähigen Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Bereitstellen eines Event Hubs-Kafka-Endpunkts

#### <a name="consumer-applicationconf"></a>„application.conf“ des Consumers

Aktualisieren Sie die `bootstrap.servers`- und `sasl.jaas.config`-Werte in `consumer/src/main/resources/application.conf` zur Weiterleitung des Consumers an den Event Hubs-Kafka-Endpunkt mit der richtigen Authentifizierung.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>Ausführen des Consumers über die Befehlszeile

Zum Ausführen des Consumers über die Befehlszeile generieren Sie die JAR-Datei, und führen Sie sie in Maven aus (oder generieren Sie die JAR-Datei mit Maven, und führen Sie sie dann durch Hinzufügen der erforderlichen Kafka JAR(s) an den Klassenpfad in Java aus):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Wenn der Kafka-fähige Event Hub über Ereignisse verfügt (wenn z.B. der Producer auch ausgeführt wird), erhält der Consumer jetzt Ereignisse aus dem Thema `test`. 

Ausführlichere Informationen zu Akka Streams finden Sie im [Akka Streams-Kafka-Handbuch](https://doc.akka.io/docs/akka-stream-kafka/current/home.html).

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie erfahren, wie Sie Akka Streams mit Kafka-fähigen Event Hubs verbinden können, ohne Ihre Protokollclients ändern oder Ihre eigenen Cluster ausführen zu müssen. Azure Event Hubs für Kafka unterstützt [Apache Kafka Version 1.0](https://kafka.apache.org/10/documentation.html). Im Rahmen dieses Tutorials haben Sie die folgenden Aktionen ausgeführt: 

> [!div class="checklist"]
> * Erstellen eines Event Hubs-Namespace
> * Klonen des Beispielprojekts
> * Ausführen des Akka Streams-Producers 
> * Ausführen des Akka Streams-Consumers

Weitere Informationen zu Event Hubs und Event Hubs für Kafka finden Sie unter folgendem Thema:  

- [Informationen zu Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs für Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Erstellen von Kafka-fähigen Event Hubs](event-hubs-create-kafka-enabled.md)
- [Streamen von Ereignissen in Event Hubs aus Kafka-Anwendungen](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Spiegeln eines Kafka-Brokers in einem Kafka-fähigen Event Hub](event-hubs-kafka-mirror-maker-tutorial.md)
- [Verbinden von Apache Spark mit einem Kafka-fähigen Event Hub](event-hubs-kafka-spark-tutorial.md)
- [Verbinden von Apache Flink mit einem Kafka-fähigen Event Hub](event-hubs-kafka-flink-tutorial.md)
- [Integrieren von Kafka Connect in einen Kafka-fähigen Event Hub](event-hubs-kafka-connect-tutorial.md)
- [Erkunden von Beispielen auf GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
