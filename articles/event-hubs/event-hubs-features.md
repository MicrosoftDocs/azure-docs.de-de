---
title: 'Übersicht über die Features: Azure Event Hubs | Microsoft-Dokumentation'
description: Dieser Artikel enthält Details zu Features und Terminologie von Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a38cf4ba6a06dc6e977f9ea168fcf67ce83ff5de
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339981"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Features und Terminologie in Azure Event Hubs

Azure Event Hubs ist ein skalierbarer Ereignisverarbeitungsdienst, der große Mengen von Ereignissen und Daten mit kurzer Wartezeit und hoher Zuverlässigkeit erfasst und verarbeitet. Unter [Was ist Event Hubs?](./event-hubs-about.md) finden Sie einen allgemeinen Überblick.

Dieser Artikel setzt auf den Informationen in der [Übersichtsartikel](./event-hubs-about.md) auf und bietet technische und Implementierungsdetails zu Event Hubs-Komponenten und -Features.

## <a name="namespace"></a>Namespace
Ein Event Hubs-Namespace stellt einen eindeutigen Bereichscontainer bereit, auf den über den [vollqualifizierten Domänennamen](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) verwiesen wird, und in dem Sie mindestens einen Event Hub bzw. mindestens ein Kafka-Thema erstellen. 

## <a name="event-hubs-for-apache-kafka"></a>Event Hubs für Apache Kafka

[Dieses Feature](event-hubs-for-kafka-ecosystem-overview.md) stellt einen Endpunkt bereit, der Kunden die Kommunikation mit Event Hubs über das Kafka-Protokoll ermöglicht. Mit dieser Integration verfügen Kunden über einen Kafka-Endpunkt. Dadurch können Kunden ihre vorhandenen Kafka-Anwendungen so konfigurieren, dass die Kommunikation mit Event Hubs ermöglicht wird. Dies stellt eine Alternative zum Ausführen ihrer eigenen Kafka-Cluster dar. Event Hubs für Apache Kafka unterstützt das Kafka-Protokoll 1.0 und höher. 

Mit dieser Integration ist es nicht erforderlich, dass Sie Kafka-Cluster ausführen oder mit ZooKeeper verwalten. So können Sie auch einige der anspruchsvollsten Features von Event Hubs verwenden, z.B. Capture, automatische Vergrößerung und georedundante Notfallwiederherstellung.

Außerdem können durch diese Integration Anwendungen wie Mirror Maker oder Frameworks wie Kafka Connect nur durch Konfigurationsänderungen ohne Cluster verwendet werden. 

## <a name="event-publishers"></a>Ereignisherausgeber

Jede Entität, die Daten an einen Event Hub sendet, ist ein Ereigniserzeuger bzw. *Ereignisherausgeber*. Ereignisherausgeber können Ereignisse über HTTPS, AMQP 1.0 oder Kafka 1.0 und höher veröffentlichen. Ereignisherausgeber identifizieren sich mit einem SAS-Token (Shared Access Signature) bei einem Event Hub und können eine eindeutige Identität aufweisen oder ein gemeinsames SAS-Token verwenden.

### <a name="publishing-an-event"></a>Veröffentlichen eines Ereignisses

Sie können ein Ereignis über AMQP 1.0, Kafka 1.0 (und höher) oder HTTPS veröffentlichen. Der Event Hubs-Dienst stellt eine [REST-API](/rest/api/eventhub/) sowie [.NET](event-hubs-dotnet-standard-getstarted-send.md)-, [Java](event-hubs-java-get-started-send.md)-, [Python](event-hubs-python-get-started-send.md)-, [JavaScript](event-hubs-node-get-started-send.md)- und [Go](event-hubs-go-get-started-send.md)-Clientbibliotheken für die Veröffentlichung von Ereignissen in einem Event Hub bereit. Für andere Runtimes und Plattformen können beliebige AMQP 1.0-Clients verwendet werden, z.B. [Apache Qpid](https://qpid.apache.org/). 

Sie können Ereignisse einzeln oder als Batch veröffentlichen. Jede Veröffentlichung (Ereignisdateninstanz) ist auf 1 MB beschränkt, unabhängig davon, ob es sich um ein einzelnes Ereignis oder einen Batch handelt. Das Veröffentlichen von Ereignissen, die größer als dieser Schwellenwert sind, führt zu einem Fehler. Es ist eine bewährte Methode für Herausgeber, die Partitionen innerhalb des Event Hubs nicht zu beachten und nur einen *Partitionsschlüssel* (im nächsten Abschnitt beschrieben) oder die eigene Identität über das SAS-Token anzugeben.

Die Wahl zwischen AMQP oder HTTPS ist auf das Verwendungsszenario bezogen. AMQP erfordert die Einrichtung eines persistenten bidirektionalen Sockets zusätzlich zu TLS (Transport Level Security) oder SSL/TLS. AMQP weist höhere Netzwerkkosten beim Initialisieren der Sitzung auf, HTTPS erfordert jedoch zusätzlichen TLS-Mehraufwand für jede Anforderung. AMQP verfügt über eine höhere Leistung für häufige Herausgeber.

![Partitionsschlüssel](./media/event-hubs-features/partition_keys.png)

Event Hubs stellt sicher, dass alle Ereignisse mit dem gleichen Partitionsschlüsselwert in der richtigen Reihenfolge und an die gleiche Partition übermittelt werden. Wenn Partitionsschlüssel mit Herausgeberrichtlinien verwendet werden, müssen die Identität des Herausgebers und der Wert des Partitionsschlüssels übereinstimmen. Andernfalls tritt ein Fehler auf.

### <a name="publisher-policy"></a>Herausgeberrichtlinie

Event Hubs ermöglicht eine differenzierte Kontrolle über Ereignisherausgeber durch *Herausgeberrichtlinien*. Herausgeberrichtlinien sind Laufzeitfunktionen, mit denen große Mengen unabhängiger Herausgeber verwaltet werden können. Mit Herausgeberrichtlinien verwendet jeder Herausgeber einen eigenen eindeutigen Bezeichner für die Veröffentlichung von Ereignissen in einem Event Hub. Dabei kommt der folgende Mechanismus zum Einsatz:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>
```

Sie müssen Herausgebernamen nicht im Voraus erstellen, jedoch müssen diese mit dem SAS-Token übereinstimmen, das beim Veröffentlichen eines Ereignisses verwendet wird, um die Identitäten unabhängiger Herausgeber sicherzustellen. Bei Verwendung von Herausgeberrichtlinien wird der Wert **PartitionKey** auf den Herausgebernamen festgelegt. Diese Werte müssen übereinstimmen, damit alles ordnungsgemäß funktioniert.

## <a name="capture"></a>Erfassung

Mit [Event Hubs Capture](event-hubs-capture-overview.md) lassen sich die Streamingdaten automatisch in Event Hubs aufzeichnen und in einem Blob Storage-Konto oder einem Azure Data Lake-Dienstkonto speichern. Sie können Event Hubs Capture im Azure-Portal aktivieren und eine Mindestgröße sowie ein Mindestzeitfenster für die Aufzeichnung angeben. Mit Event Hubs Capture legen Sie ein eigenes Azure Blob Storage-Konto und einen Container bzw. ein Azure Data Lake-Dienstkonto fest, wovon eines zum Speichern der aufgezeichneten Daten verwendet wird. Die aufgezeichneten Daten werden im Apache Avro-Format geschrieben.

## <a name="partitions"></a>Partitionen
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>SAS-Token

Event Hubs verwendet *Shared Access Signatures*, die auf Namespace- und Event Hub-Ebene verfügbar sind. Ein SAS-Token wird aus einem SAS-Schlüssel generiert und ist ein SHA-Hash einer URL, der in einem bestimmten Format codiert ist. Mit dem Namen des Schlüssels (Richtlinie) und dem Token kann Event Hubs den Hash erneut generieren und somit den Absender authentifizieren. In der Regel werden SAS-Token für Ereignisherausgeber nur mit **Senden**-Berechtigung für einen bestimmten Event Hub erstellt. Dieser SAS-Token-URL-Mechanismus bildet die Grundlage für die Herausgeberidentifizierung, die in der Herausgeberrichtlinie eingeführt wurde. Weitere Informationen zur Verwendung von SAS finden Sie unter [SAS-Authentifizierung (Shared Access Signature) mit Service Bus](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Ereignisconsumer

Eine Entität, die Ereignisdaten von einem Event Hub liest, ist ein *Ereignisconsumer*. Alle Event Hubs-Consumer stellen über eine AMQP 1.0-Sitzung eine Verbindung her und Ereignisse werden über die Sitzung übermittelt, sobald sie verfügbar sind. Der Client muss die Verfügbarkeit der Daten nicht abfragen.

### <a name="consumer-groups"></a>Verbrauchergruppen

Der Veröffentlichen-/Abonnieren-Mechanismus von Event Hubs erfolgt durch *Consumergruppen*. Eine Consumergruppe ist eine Ansicht (Status, Position oder Offset) des gesamten Event Hubs. Mithilfe von Consumergruppen können mehrere verarbeitende Anwendungen jeweils eine separate Ansicht des Ereignisdatenstroms aufweisen und den Datenstrom unabhängig voneinander in einem unabhängigen Tempo und mit eigenen Offsets lesen.

In einer Streamverarbeitungsarchitektur entspricht jede Downstreamanwendung einer Consumergruppe. Wenn Sie Ereignisdaten in den langfristigen Speicher schreiben möchten, ist die entsprechende Speicherschreibanwendung eine Consumergruppe. Komplexe Ereignisverarbeitung kann von einer anderen separaten Consumergruppe ausgeführt werden. Sie können auf Partitionen nur über eine Consumergruppe zugreifen. In einem Event Hub gibt es immer eine Standardconsumergruppe, und Sie können bis zu 20 Consumergruppen für einen Event Hub auf Standardebene erstellen.

Pro Consumergruppe sind maximal 5 gleichzeitige Leser in einer Partition zulässig. Jedoch **wird nur ein aktiver Empfänger in einer Partition pro Consumergruppe empfohlen**. Innerhalb einer einzelnen Partition erhält jeder Leser alle Nachrichten. Wenn Sie für die gleiche Partition über mehrere Leser verfügen, verarbeiten Sie doppelte Nachrichten. Sie müssen dies in Ihrem Code berücksichtigen, was möglicherweise nicht trivial ist. In einigen Szenarien ist dies jedoch ein gültiger Ansatz.

Bei einigen Clients der Azure SDKs handelt es sich um intelligente Consumer-Agents, die im Detail sicherstellen, dass jede Partition über einen einzelnen Reader verfügt und dass alle Partitionen eines Event Hubs ausgelesen werden. Ihr Code muss daher nur noch die aus dem Event Hub gelesenen Ereignisse verarbeiten und kann viele Details der Partitionen ignorieren. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit einer Partition](#connect-to-a-partition).

Nachstehend finden Sie einige Beispiele für die URI-Konvention für Consumergruppen:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>
```

Die folgende Abbildung zeigt die Datenstromverarbeitungsarchitektur von Event Hubs:

![Event Hubs-Architektur](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Streamoffsets

Ein *Offset* ist die Position eines Ereignisses innerhalb einer Partition. Sie können sich einen Offset als einen clientseitigen Zeiger vorstellen. Der Offset ist eine Nummerierung des Ereignisses in Byte. Dieser Offset ermöglicht es Ereignisconsumern (Lesern), einen Punkt im Ereignisstream anzugeben, ab dem Ereignisse gelesen werden sollen. Sie können den Offset als Zeitstempel oder als Offsetwert angeben. Es liegt in der Verantwortung jedes Consumers, seine eigenen Offsetwerte außerhalb des Event Hubs-Diensts zu speichern. Innerhalb einer Partition enthält jedes Ereignis einen Offset.

![Partitionsoffset](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Setzen von Prüfpunkten

*Setzen von Prüfpunkten* ist ein Vorgang, bei dem Leser ihre Position innerhalb einer Partitionsereignissequenz markieren oder bestätigen. Dies liegt in der Verantwortung des Consumers und erfolgt auf Partitionsbasis innerhalb einer Consumergruppe. Das bedeutet, dass jeder Partitionsleser für jede Consumergruppe seine aktuelle Position im Ereignisstream nachverfolgen muss und den Dienst informieren kann, wenn er den Datenstrom als abgeschlossen betrachtet.

Wenn ein Leser die Verbindung zu eine Partition trennt, beginnt nach dem erneuten Herstellen der Verbindung das Lesen bei dem Prüfpunkt, der zuvor durch den letzten Leser dieser Partition in dieser Consumergruppe übermittelt wurde. Wenn der Leser verbunden ist, übergibt er diesen Offset an den Event Hub, um die Position für den nächsten Lesevorgang anzugeben. Auf diese Weise können mithilfe von Prüfpunkten Ereignisse von Downstreamanwendungen als abgeschlossen markiert werden. Darüber hinaus sorgen Prüfpunkte für Resilienz bei einem Failover zwischen Lesern, die auf unterschiedlichen Computern ausgeführt werden. Sie können ältere Daten zurückgeben, indem Sie einen niedrigeren Offset aus diesem Prüfpunktprozess angeben. Durch diesen Mechanismus ermöglicht das Setzen von Prüfpunkten Failoverstabilität und eine erneute Wiedergabe des Ereignisstreams.

> [!NOTE]
> Wenn Sie Azure Blob Storage als Prüfpunktspeicher in einer Umgebung verwenden, die eine andere Version des Storage Blob SDK unterstützt als diejenigen, die in der Regel in Azure verfügbar sind, müssen Sie Code verwenden, um die Version der Speicherdienst-API in die von dieser Umgebung unterstützte Version zu ändern. Wenn Sie z. B. [Event Hubs mit einer Azure Stack Hub-Version 2002](/azure-stack/user/event-hubs-overview) ausführen, ist die höchste verfügbare Version für den Speicherdienst Version 2017-11-09. In diesem Fall müssen Sie Code verwenden, um Version 2017-11-09 der Storage Service-API als Ziel zu nutzen. Ein Beispiel für die Verwendung einer bestimmten Storage-API-Version als Ziel finden Sie in den folgenden Beispielen auf GitHub: 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) oder [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

### <a name="common-consumer-tasks"></a>Allgemeine Consumeraufgaben

Alle Event Hubs-Consumer stellen eine Verbindung über eine AMQP 1.0-Sitzung (statusaktivierter bidirektionaler Kommunikationskanal) her. Jede Partition weist eine AMQP 1.0-Sitzung auf, die den Transport von Ereignissen nach Partition getrennt ermöglicht.

#### <a name="connect-to-a-partition"></a>Herstellen einer Verbindung mit einer Partition

Bei der Verbindungsherstellung zu Partitionen erfolgt die Koordination von Readerverbindungen zu bestimmten Partitionen oft mithilfe eines Leasingmechanismus. Auf diese Weise ist es möglich, dass jede Partition in einer Consumergruppe nur über einen aktiven Reader verfügt. Wenn Sie die Clients in den Event Hubs-SDKs verwenden, erleichtern Sie sich das Leasing und die Verwaltung von Readern sowie das Setzen von Prüfpunkten für diese, denn diese Clients verhalten sich wie intelligente Consumer-Agents. Dies sind:

- [EventProcessorClient](/dotnet/api/azure.messaging.eventhubs.eventprocessorclient) für .NET
- [EventProcessorClient](/java/api/com.azure.messaging.eventhubs.eventprocessorclient) für Java
- [EventHubConsumerClient](/python/api/azure-eventhub/azure.eventhub.aio.eventhubconsumerclient) für Python
- [EventHubConsumerClient](/javascript/api/@azure/event-hubs/eventhubconsumerclient) für JavaScript/TypeScript

#### <a name="read-events"></a>Lesen von Ereignissen

Nachdem eine AMQP 1.0-Sitzung und ein Link für eine bestimmte Partition geöffnet wurde, werden Ereignisse vom Event Hubs-Dienst an den AMQP 1.0-Client übergeben. Dieser Mechanismus ermöglicht einen höheren Durchsatz und eine niedrigere Latenz als Pull-basierte Mechanismen wie z. B. HTTP GET. Wenn Ereignisse an den Client gesendet werden, enthält jede Ereignisdateninstanz wichtige Metadaten wie z. B. den Offset und die Sequenznummer, die zur Vereinfachung des Setzens von Prüfpunkten in der Ereignissequenz verwendet werden.

Ereignisdaten:
* Offset
* Sequenznummer
* Body
* Benutzereigenschaften
* Systemeigenschaften

Die Verwaltung des Offsets liegt in Ihrer Verantwortung.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Event Hubs erhalten Sie unter den folgenden Links:

- Erste Schritte mit Event Hubs
    - [.NET](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Programmierleitfaden für Event Hubs](event-hubs-programming-guide.md)
* [Verfügbarkeit und Konsistenz in Event Hubs](event-hubs-availability-and-consistency.md)
* [Event Hubs – häufig gestellte Fragen](event-hubs-faq.md)
* [Event Hubs-Beispiele](event-hubs-samples.md)