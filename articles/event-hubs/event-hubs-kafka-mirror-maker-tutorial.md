---
title: Verwenden von Apache Kafka MirrorMaker – Azure Event Hubs | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zur Verwendung von Kafka MirrorMaker zum Spiegeln eines Kafka-Clusters in Azure Event Hubs.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: f2e7ac6951c84adfd8fc313995724021640ee0ab
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503198"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Verwenden von Kafka MirrorMaker mit Event Hubs für Apache Kafka

In diesem Tutorial wird gezeigt, wie Sie einen Kafka-Broker in einem Event Hub mit Kafka MirrorMaker spiegeln.

   ![Kafka MirrorMaker mit Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Dieses Beispiel ist auf [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) verfügbar.

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Whitelist*, den Microsoft nicht länger verwendet. Sobald der Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Erstellen eines Event Hubs-Namespace
> * Klonen des Beispielprojekts
> * Einrichten eines Kafka-Clusters
> * Konfigurieren von Kafka MirrorMaker
> * Ausführen von Kafka MirrorMaker

## <a name="introduction"></a>Einführung
Ein wichtiger Aspekt moderner Cloud-Apps ist die Möglichkeit, die Infrastruktur zu aktualisieren, zu verbessern und zu ändern, ohne den Dienst zu unterbrechen. In diesem Tutorial erfahren Sie, wie Sie mit einem Event Hub und Kafka MirrorMaker eine vorhandene Kafka-Pipeline in Azure integrieren, indem Sie den Kafka-Eingabedatenstrom im Event Hubs-Dienst spiegeln. 

Mit einem Azure Event Hubs-Kafka-Endpunkt können Sie eine Verbindung mit Azure Event Hubs herstellen, indem Sie das Kafka-Protokoll (Kafka-Clients) verwenden. Durch minimale Änderungen an einer Kafka-Anwendung können Sie eine Verbindung mit Azure Event Hubs herstellen und die Vorteile des Azure-Ökosystems nutzen. Event Hubs unterstützen derzeit die Kafka-Version 1.0 und höher.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie dieses Tutorial ausführen können, benötigen Sie folgende Komponenten:

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

Ein Event Hubs-Namespace ist erforderlich, um Nachrichten an einen Event Hubs-Dienst zu senden und von diesem zu empfangen. Anweisungen zum Erstellen eines Namespace und eines Event Hub finden Sie unter [Erstellen eines Event Hubs](event-hubs-create.md). Kopieren Sie die Event Hubs-Verbindungszeichenfolge zur späteren Verwendung.

## <a name="clone-the-example-project"></a>Klonen des Beispielprojekts

Nachdem Sie nun über eine Event Hubs-Verbindungszeichenfolge verfügen, können Sie das Azure Event Hubs für Kafka-Repository klonen und zum Unterordner `mirror-maker` navigieren:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Einrichten eines Kafka-Clusters

Verwenden Sie die [Kafka-Schnellstartanleitung](https://kafka.apache.org/quickstart), um einen Cluster mit den gewünschten Einstellungen einzurichten (oder verwenden Sie einen vorhandenen Kafka-Cluster).

## <a name="configure-kafka-mirrormaker"></a>Konfigurieren von Kafka MirrorMaker

Mit Kafka MirrorMaker wird die „Spiegelung“ eines Datenstroms ermöglicht. Für Kafka-Quell- und -Zielcluster wird mit MirrorMaker sichergestellt, dass alle Nachrichten, die an den Quellcluster gesendet werden, sowohl vom Quell- als auch vom Zielcluster empfangen werden. In diesem Beispiel wird gezeigt, wie Sie einen Kafka-Quellcluster mit einem Event Hub als Ziel spiegeln. Dieses Szenario kann verwendet werden, um Daten aus einer vorhandenen Kafka-Pipeline an Event Hubs zu senden, ohne den Datenfluss zu unterbrechen. 

Ausführlichere Informationen zu Kafka MirrorMaker finden Sie im [Leitfaden zur Kafka-Spiegelung und zu MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Richten Sie zum Konfigurieren von Kafka MirrorMaker einen Kafka-Cluster als Consumer/Quelle und einen Event Hub als Producer/Ziel ein.

#### <a name="consumer-configuration"></a>Consumerkonfiguration

Aktualisieren Sie die Konfigurationsdatei `source-kafka.config` für den Consumer, über die MirrorMaker die Eigenschaften des Kafka-Quellclusters mitgeteilt werden.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Producerkonfiguration

Aktualisieren Sie nun die Konfigurationsdatei `mirror-eventhub.config` für den Producer, mit der MirrorMaker angewiesen wird, die duplizierten (bzw. „gespiegelten“) Daten an den Event Hubs-Dienst zu senden. Ändern Sie `bootstrap.servers` und `sasl.jaas.config` so, dass darin auf Ihren Event Hubs-Kafka-Endpunkt verwiesen wird. Für den Event Hubs-Dienst ist eine sichere Kommunikation (SASL) erforderlich. Dies wird erreicht, indem die letzten drei Eigenschaften in der folgenden Konfiguration festgelegt werden: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Ersetzen Sie `{YOUR.EVENTHUBS.CONNECTION.STRING}` durch die Verbindungszeichenfolge für Ihren Event Hubs-Namespace. Anweisungen zum Abrufen der Verbindungszeichenfolge finden Sie unter [Abrufen einer Event Hubs-Verbindungszeichenfolge](event-hubs-get-connection-string.md). Hier sehen Sie eine Beispielkonfiguration: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

## <a name="run-kafka-mirrormaker"></a>Ausführen von Kafka MirrorMaker

Führen Sie das Kafka MirrorMaker-Skript im Kafka-Stammverzeichnis aus, indem Sie die aktualisierten Konfigurationsdateien verwenden. Achten Sie darauf, dass Sie entweder die Konfigurationsdateien in das Kafka-Stammverzeichnis kopieren oder die Pfade im folgenden Befehl aktualisieren.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Sie können überprüfen, ob die Ereignisse den Event Hub erreichen, indem Sie im [Azure-Portal](https://azure.microsoft.com/features/azure-portal/) die Eingangsstatistiken anzeigen oder einen Consumer für den Event Hub ausführen.

Bei Ausführung von MirrorMaker werden alle Ereignisse, die an den Kafka-Quellcluster gesendet werden, sowohl vom Kafka-Cluster als auch vom gespiegelten Event Hub empfangen. Indem MirrorMaker und ein Event Hubs-Kafka-Endpunkt verwendet werden, können Sie eine vorhandene Kafka-Pipeline zum verwalteten Azure Event Hubs-Dienst migrieren, ohne den vorhandenen Cluster zu ändern oder einen ausgehenden Datenfluss zu unterbrechen.

## <a name="samples"></a>Beispiele
Im Folgenden sind auf GitHub verfügbare Beispiele aufgeführt:

- [Beispielcode für dieses Tutorial auf GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure Event Hubs Kafka MirrorMaker unter Azure Container Instances](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Event Hubs für Kafka finden Sie in folgenden Artikeln:  

- [Verbinden von Apache Spark mit einem Event Hub](event-hubs-kafka-spark-tutorial.md)
- [Verbinden von Apache Flink mit einem Event Hub](event-hubs-kafka-flink-tutorial.md)
- [Integrieren von Kafka Connect in einen Event Hub](event-hubs-kafka-connect-tutorial.md)
- [Erkunden von Beispielen auf GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Verbinden von Akka Streams mit einem Event Hub](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka-Entwicklerleitfaden für Azure Event Hubs](apache-kafka-developer-guide.md)