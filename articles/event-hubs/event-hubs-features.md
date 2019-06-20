---
title: 'Übersicht über die Features: Azure Event Hubs | Microsoft-Dokumentation'
description: Dieser Artikel enthält Details zu Features und Terminologie von Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: e7f292db06d4da9206aabd14a68e6acde867f92d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60821948"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Features und Terminologie in Azure Event Hubs

Azure Event Hubs ist ein skalierbarer Ereignisverarbeitungsdienst, der große Mengen von Ereignissen und Daten mit kurzer Wartezeit und hoher Zuverlässigkeit erfasst und verarbeitet. Unter [Was ist Event Hubs?](event-hubs-what-is-event-hubs.md) finden Sie einen allgemeinen Überblick.

Dieser Artikel setzt auf den Informationen in der [Übersichtsartikel](event-hubs-what-is-event-hubs.md) auf und bietet technische und Implementierungsdetails zu Event Hubs-Komponenten und -Features.

## <a name="namespace"></a>Namespace
Ein Event Hubs-Namespace stellt einen eindeutigen Bereichscontainer bereit, auf den über den [vollqualifizierten Domänennamen](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) verwiesen wird, und in dem Sie mindestens einen Event Hub bzw. mindestens ein Kafka-Thema erstellen. 

## <a name="event-hubs-for-apache-kafka"></a>Event Hubs für Apache Kafka

[Dieses Feature](event-hubs-for-kafka-ecosystem-overview.md) stellt einen Endpunkt bereit, der Kunden die Kommunikation mit Event Hubs über das Kafka-Protokoll ermöglicht. Mit dieser Integration verfügen Kunden über einen Kafka-Endpunkt. Dadurch können Kunden ihre vorhandenen Kafka-Anwendungen so konfigurieren, dass die Kommunikation mit Event Hubs ermöglicht wird. Dies stellt eine Alternative zum Ausführen ihrer eigenen Kafka-Cluster dar. Event Hubs für Apache Kafka unterstützt das Kafka-Protokoll 1.0 und höher. 

Mit dieser Integration ist es nicht erforderlich, dass Sie Kafka-Cluster ausführen oder mit ZooKeeper verwalten. So können Sie auch einige der anspruchsvollsten Features von Event Hubs verwenden, z.B. Capture, automatische Vergrößerung und georedundante Notfallwiederherstellung.

Außerdem können durch diese Integration Anwendungen wie Mirror Maker oder Frameworks wie Kafka Connect nur durch Konfigurationsänderungen ohne Cluster verwendet werden. 

## <a name="event-publishers"></a>Ereignisherausgeber

Jede Entität, die Daten an einen Event Hub sendet, ist ein Ereigniserzeuger bzw. *Ereignisherausgeber*. Ereignisherausgeber können Ereignisse über HTTPS, AMQP 1.0 oder Kafka 1.0 und höher veröffentlichen. Ereignisherausgeber identifizieren sich mit einem SAS-Token (Shared Access Signature) bei einem Event Hub und können eine eindeutige Identität aufweisen oder ein gemeinsames SAS-Token verwenden.

### <a name="publishing-an-event"></a>Veröffentlichen eines Ereignisses

Sie können ein Ereignis über AMQP 1.0, Kafka 1.0 (und höher) oder HTTPS veröffentlichen. Event Hubs stellt [Clientbibliotheken und Klassen](event-hubs-dotnet-framework-api-overview.md) zum Veröffentlichen von Ereignissen für einen Event Hub von .NET-Clients bereit. Für andere Runtimes und Plattformen können beliebige AMQP 1.0-Clients verwendet werden, z.B. [Apache Qpid](https://qpid.apache.org/). Sie können Ereignisse einzeln oder als Batch veröffentlichen. Jede Veröffentlichung (Ereignisdateninstanz) ist auf 1 MB beschränkt, unabhängig davon, ob es sich um ein einzelnes Ereignis oder einen Batch handelt. Das Veröffentlichen von Ereignissen, die größer als dieser Schwellenwert sind, führt zu einem Fehler. Es ist eine bewährte Methode für Herausgeber, die Partitionen innerhalb des Event Hubs nicht zu beachten und nur einen *Partitionsschlüssel* (im nächsten Abschnitt beschrieben) oder die eigene Identität über das SAS-Token anzugeben.

Die Wahl zwischen AMQP oder HTTPS ist auf das Verwendungsszenario bezogen. AMQP erfordert die Einrichtung eines persistenten bidirektionalen Sockets zusätzlich zu TLS (Transport Level Security) oder SSL/TLS. AMQP weist höhere Netzwerkkosten beim Initialisieren der Sitzung auf, HTTPS erfordert jedoch zusätzlichen SSL-Mehraufwand für jede Anfrage. AMQP verfügt über eine höhere Leistung für häufige Herausgeber.

![Event Hubs](./media/event-hubs-features/partition_keys.png)

Event Hubs stellt sicher, dass alle Ereignisse mit dem gleichen Partitionsschlüsselwert in der richtigen Reihenfolge und an die gleiche Partition übermittelt werden. Wenn Partitionsschlüssel mit Herausgeberrichtlinien verwendet werden, müssen die Identität des Herausgebers und der Wert des Partitionsschlüssels übereinstimmen. Andernfalls tritt ein Fehler auf.

### <a name="publisher-policy"></a>Herausgeberrichtlinie

Event Hubs ermöglicht eine differenzierte Kontrolle über Ereignisherausgeber durch *Herausgeberrichtlinien*. Herausgeberrichtlinien sind Laufzeitfunktionen, mit denen große Mengen unabhängiger Herausgeber verwaltet werden können. Mit Herausgeberrichtlinien verwendet jeder Herausgeber einen eigenen eindeutigen Bezeichner für die Veröffentlichung von Ereignissen in einem Event Hub. Dabei kommt der folgende Mechanismus zum Einsatz:

```http
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

Sie müssen Herausgebernamen nicht im Voraus erstellen, jedoch müssen diese mit dem SAS-Token übereinstimmen, das beim Veröffentlichen eines Ereignisses verwendet wird, um die Identitäten unabhängiger Herausgeber sicherzustellen. Bei Verwendung von Herausgeberrichtlinien wird der Wert **PartitionKey** auf den Herausgebernamen festgelegt. Diese Werte müssen übereinstimmen, damit alles ordnungsgemäß funktioniert.

## <a name="capture"></a>Erfassen

Mit [Event Hubs Capture](event-hubs-capture-overview.md) lassen sich die Streamingdaten automatisch in Event Hubs aufzeichnen und in einem Blob Storage-Konto oder einem Azure Data Lake-Dienstkonto speichern. Sie können Event Hubs Capture im Azure-Portal aktivieren und eine Mindestgröße sowie ein Mindestzeitfenster für die Aufzeichnung angeben. Mit Event Hubs Capture legen Sie ein eigenes Azure Blob Storage-Konto und einen Container bzw. ein Azure Data Lake-Dienstkonto fest, wovon eines zum Speichern der aufgezeichneten Daten verwendet wird. Die aufgezeichneten Daten werden im Apache Avro-Format geschrieben.

## <a name="partitions"></a>Partitionen

Event Hubs bietet Nachrichtenstreaming über ein partitioniertes Consumermuster, in dem jeder Consumer nur eine bestimmte Teilmenge oder Partition des Nachrichtenstreams liest. Dieses Muster ermöglicht eine horizontale Skalierung für die Ereignisverarbeitung und bietet andere datenstrombezogene Features, die in Warteschlangen und Themen nicht verfügbar sind.

Eine Partition ist eine geordnete Sequenz von Ereignissen, die in einem Event Hub besteht. Neu eingehende Ereignisse werden am Ende dieser Sequenz hinzugefügt. Eine Partition kann als "Commitprotokoll" betrachtet werden

![Event Hubs](./media/event-hubs-features/partition.png)

Event Hubs bewahrt Daten über einen konfigurierten Aufbewahrungszeitraum auf, der für alle Partitionen im Event Hub gilt. Ereignisse laufen nach Zeit ab. Sie können nicht direkt gelöscht werden. Da Partitionen unabhängig sind und ihre eigene Datensequenz enthalten, wachsen sie häufig mit unterschiedlicher Geschwindigkeit.

![Event Hubs](./media/event-hubs-features/multiple_partitions.png)

Die Anzahl der Partitionen wird bei der Erstellung angegeben und muss zwischen zwei und 32 liegen. Die Partitionenanzahl kann nicht geändert werden. Behalten Sie daher beim Festlegen der Partitionenanzahl die langfristige Skalierung im Hinterkopf. Partitionen sind ein Mechanismus zum Organisieren von Daten, der sich auf die erforderliche Downstreamparallelität in verarbeitenden Anwendungen bezieht. Die Anzahl der Partitionen in einem Event Hub steht in direktem Zusammenhang mit der erwarteten Anzahl von gleichzeitigen Lesern. Sie können die Anzahl der Partitionen auf mehr als 32 erhöhen, wenn Sie das Event Hubs-Team kontaktieren.

Auch wenn Partitionen identifizierbar sind und Daten direkt an sie gesendet werden können, wird dies nicht empfohlen. Stattdessen können Sie Konstrukte höherer Ebene verwenden, die in den Abschnitten [Ereignisherausgeber](#event-publishers) und „Kapazität“ erläutert werden. 

Partitionen werden mit einer Sequenz von Ereignisdaten gefüllt, die den Hauptteil des Ereignisses, einen benutzerdefinierten Eigenschaftenbehälter und Metadaten (z.B. Offset in der Partition und Nummer in der Streamsequenz) enthalten.

Weitere Informationen zu Partitionen sowie zur Abwägung von Verfügbarkeit gegen Zuverlässigkeit finden Sie im [Programmierleitfaden für Event Hubs](event-hubs-programming-guide.md#partition-key) sowie im Artikel [Verfügbarkeit und Konsistenz in Event Hubs](event-hubs-availability-and-consistency.md).

### <a name="partition-key"></a>Partitionsschlüssel

Mit einem [Partitionsschlüssel](event-hubs-programming-guide.md#partition-key) können Sie eingehende Ereignisdaten spezifischen Partitionen zuordnen und die Daten so organisieren. Der Partitionsschlüssel ist ein vom Absender bereitgestellter Wert, der an einen Event Hub übergeben wird. Er wird über eine statische Hashfunktion verarbeitet, die die Partitionszuweisung erstellt. Wenn Sie beim Veröffentlichen eines Ereignisses keinen Partitionsschlüssel angeben, wird eine Roundrobinzuordnung verwendet.

Dem Ereignisherausgeber ist nur der Partitionsschlüssel bekannt, nicht die Partition, auf der die Ereignisse veröffentlicht werden. Dieses Entkoppeln von Schlüssel und Partition entbindet den Absender davon, zu viel über die Downstreamverarbeitung wissen zu müssen. Eine gerätebezogene oder für einen Benutzer eindeutige Identität stellt einen guten Partitionsschlüssel dar, es können aber auch andere Attribute wie z. B. Geografie zum Gruppieren von verwandten Ereignissen in einer einzelnen Partition verwendet werden.

## <a name="sas-tokens"></a>SAS-Token

Event Hubs verwendet *Shared Access Signatures*, die auf Namespace- und Event Hub-Ebene verfügbar sind. Ein SAS-Token wird aus einem SAS-Schlüssel generiert und ist ein SHA-Hash einer URL, der in einem bestimmten Format codiert ist. Mit dem Namen des Schlüssels (Richtlinie) und dem Token kann Event Hubs den Hash erneut generieren und somit den Absender authentifizieren. In der Regel werden SAS-Token für Ereignisherausgeber nur mit **Senden**-Berechtigung für einen bestimmten Event Hub erstellt. Dieser SAS-Token-URL-Mechanismus bildet die Grundlage für die Herausgeberidentifizierung, die in der Herausgeberrichtlinie eingeführt wurde. Weitere Informationen zur Verwendung von SAS finden Sie unter [SAS-Authentifizierung (Shared Access Signature) mit Service Bus](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Ereignisconsumer

Eine Entität, die Ereignisdaten von einem Event Hub liest, ist ein *Ereignisconsumer*. Alle Event Hubs-Consumer stellen über eine AMQP 1.0-Sitzung eine Verbindung her und Ereignisse werden über die Sitzung übermittelt, sobald sie verfügbar sind. Der Client muss die Verfügbarkeit der Daten nicht abfragen.

### <a name="consumer-groups"></a>Verbrauchergruppen

Der Veröffentlichen-/Abonnieren-Mechanismus von Event Hubs erfolgt durch *Consumergruppen*. Eine Consumergruppe ist eine Ansicht (Status, Position oder Offset) des gesamten Event Hubs. Mithilfe von Consumergruppen können mehrere verarbeitende Anwendungen jeweils eine separate Ansicht des Ereignisstreams aufweisen und den Stream unabhängig voneinander in einem unabhängigen Tempo und mit eigenen Offsets lesen.

In einer Streamverarbeitungsarchitektur entspricht jede Downstreamanwendung einer Consumergruppe. Wenn Sie Ereignisdaten in den langfristigen Speicher schreiben möchten, ist die entsprechende Speicherschreibanwendung eine Consumergruppe. Komplexe Ereignisverarbeitung kann von einer anderen separaten Consumergruppe ausgeführt werden. Sie können auf Partitionen nur über eine Consumergruppe zugreifen. In einem Event Hub gibt es immer eine Standardconsumergruppe, und Sie können bis zu 20 Consumergruppen für einen Event Hub auf Standardebene erstellen.

Pro Consumergruppe sind maximal 5 gleichzeitige Leser in einer Partition zulässig. Jedoch **wird nur ein aktiver Empfänger in einer Partition pro Consumergruppe empfohlen**. Innerhalb einer einzelnen Partition erhält jeder Leser alle Nachrichten. Wenn Sie für die gleiche Partition über mehrere Leser verfügen, verarbeiten Sie doppelte Nachrichten. Sie müssen dies in Ihrem Code berücksichtigen, was möglicherweise nicht trivial ist. In einigen Szenarien ist dies jedoch ein gültiger Ansatz.


Es folgen Beispiele für die URI-Konvention für Consumergruppen:

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

Die folgende Abbildung zeigt die Streamverarbeitungsarchitektur von Event Hubs:

![Event Hubs](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Streamoffsets

Ein *Offset* ist die Position eines Ereignisses innerhalb einer Partition. Sie können sich einen Offset als einen clientseitigen Zeiger vorstellen. Der Offset ist eine Nummerierung des Ereignisses in Byte. Dieser Offset ermöglicht es Ereignisconsumern (Lesern), einen Punkt im Ereignisstream anzugeben, ab dem Ereignisse gelesen werden sollen. Sie können den Offset als Zeitstempel oder als Offsetwert angeben. Es liegt in der Verantwortung jedes Consumers, seine eigenen Offsetwerte außerhalb des Event Hubs-Diensts zu speichern. Innerhalb einer Partition enthält jedes Ereignis einen Offset.

![Event Hubs](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Setzen von Prüfpunkten

*Setzen von Prüfpunkten* ist ein Vorgang, bei dem Leser ihre Position innerhalb einer Partitionsereignissequenz markieren oder bestätigen. Dies liegt in der Verantwortung des Consumers und erfolgt auf Partitionsbasis innerhalb einer Consumergruppe. Das bedeutet, dass jeder Partitionsleser für jede Consumergruppe seine aktuelle Position im Ereignisstream nachverfolgen muss und den Dienst informieren kann, wenn er den Datenstrom als abgeschlossen betrachtet.

Wenn ein Leser die Verbindung zu eine Partition trennt, beginnt nach dem erneuten Herstellen der Verbindung das Lesen bei dem Prüfpunkt, der zuvor durch den letzten Leser dieser Partition in dieser Consumergruppe übermittelt wurde. Wenn der Leser verbunden ist, übergibt er diesen Offset an den Event Hub, um die Position für den nächsten Lesevorgang anzugeben. Auf diese Weise können mithilfe von Prüfpunkten Ereignisse von Downstreamanwendungen als abgeschlossen markiert werden. Darüber hinaus sorgen Prüfpunkte für Resilienz bei einem Failover zwischen Lesern, die auf unterschiedlichen Computern ausgeführt werden. Sie können ältere Daten zurückgeben, indem Sie einen niedrigeren Offset aus diesem Prüfpunktprozess angeben. Durch diesen Mechanismus ermöglicht das Setzen von Prüfpunkten Failoverstabilität und eine erneute Wiedergabe des Ereignisstreams.

### <a name="common-consumer-tasks"></a>Allgemeine Consumeraufgaben

Alle Event Hubs-Consumer stellen eine Verbindung über eine AMQP 1.0-Sitzung (statusaktivierter bidirektionaler Kommunikationskanal) her. Jede Partition weist eine AMQP 1.0-Sitzung auf, die den Transport von Ereignissen nach Partition getrennt ermöglicht.

#### <a name="connect-to-a-partition"></a>Herstellen einer Verbindung mit einer Partition

Bei Verbindungen erfolgt die Koordination von Leserverbindungen und Partitionen oft mithilfe eines Leasingmechanismus. Auf diese Weise ist es möglich, dass jede Partition in einer Consumergruppe nur über einen aktive Leser verfügt. Das Setzen von Prüfpunkten, das Leasen und das Verwalten von Lesern werden durch Nutzung der [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)-Klasse für .NET-Clients vereinfacht. Der Ereignisprozessorhost ist ein intelligenter Consumer-Agent.

#### <a name="read-events"></a>Lesen von Ereignissen

Nachdem eine AMQP 1.0-Sitzung und ein Link für eine bestimmte Partition geöffnet wurde, werden Ereignisse vom Event Hubs-Dienst an den AMQP 1.0-Client übergeben. Dieser Mechanismus ermöglicht einen höheren Durchsatz und eine niedrigere Latenz als Pull-basierte Mechanismen wie z. B. HTTP GET. Wenn Ereignisse an den Client gesendet werden, enthält jede Ereignisdateninstanz wichtige Metadaten wie z. B. den Offset und die Sequenznummer, die zur Vereinfachung des Setzens von Prüfpunkten in der Ereignissequenz verwendet werden.

Ereignisdaten:
* Offset
* Sequenznummer
* Body
* Benutzereigenschaften
* Systemeigenschaften

Die Verwaltung des Offsets liegt in Ihrer Verantwortung.

## <a name="scaling-with-event-hubs"></a>Skalierung mit Event Hubs

Es gibt zwei Faktoren, die die Skalierung mit Event Hubs beeinflussen.
*   Durchsatzeinheiten
*   Partitionen

### <a name="throughput-units"></a>Durchsatzeinheiten

Die Durchsatzkapazität von Event Hubs wird durch *Durchsatzeinheiten* gesteuert. Durchsatzeinheiten werden vorab als Kapazitätseinheiten erworben. Ein einzelner Durchsatz ermöglicht Folgendes:

* Eingehende Daten: Bis zu 1 MB pro Sekunde oder 1.000 Ereignisse pro Sekunde, je nachdem, was zuerst eintritt.
* Ausgehende Daten: Bis zu 2 MB pro Sekunde oder 4.096 Ereignisse pro Sekunde.

Bei Überschreitung der Kapazität der erworbenen Durchsatzeinheiten wird der Eingang eingeschränkt und [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) zurückgegeben. Der Ausgang erstellt zwar keine Drosselungsausnahmen, die Kapazität der erworbenen Durchsatzeinheiten ist allerdings dennoch beschränkt. Wenn Sie Ausnahmen für die Veröffentlichungsrate erhalten oder einen größeren Ausgang erwarten, überprüfen Sie, wie viele Durchsatzeinheiten Sie für den Namespace erworben haben. Sie können Durchsatzeinheiten auf dem Blatt **Skalierung** der Namespaces im [Azure-Portal](https://portal.azure.com) verwalten. Mithilfe der [Event Hubs-APIs](event-hubs-api-overview.md) können Durchsatzeinheiten auch programmgesteuert verwaltet werden.

Durchsatzeinheiten werden im Voraus erworben und auf Stundenbasis abgerechnet. Nach dem Erwerb werden Durchsatzeinheiten für mit einem Minimum von einer Stunde in Rechnung gestellt. Bis zu 20 Durchsatzeinheiten können für einen Event Hubs-Namespace erworben und in allen Event Hubs dieses Namespace gemeinsam verwendet werden.

### <a name="partitions"></a>Partitionen

Mit Partitionen können Sie die Skalierung für Ihre Downstreamverarbeitung durchführen. Aufgrund des partitionierten Consumermodells, das Event Hubs mit Partitionen bietet, können Sie die horizontale Skalierung bei gleichzeitiger Verarbeitung Ihrer Ereignisse vornehmen. Ein Event Hub kann über bis zu 32 Partitionen verfügen.

Es wird empfohlen, Durchsatzeinheiten und Partitionen 1:1 aufeinander abzustimmen, um eine optimale Skalierung zu erreichen. Eine einzelne Partition weist einen garantierten Ein- und Ausgang von bis zu einer Durchsatzeinheit auf. Sie können zwar einen höheren Durchsatz auf einer Partition erzielen, aber die Leistung ist nicht garantiert. Aus diesem Grund wird dringend empfohlen, dass die Anzahl der Partitionen in einem Event Hub größer oder gleich der Anzahl der Durchsatzeinheiten ist.

Angesichts des Gesamtdurchsatzes, den Sie planen zu benötigen, kennen Sie die Anzahl der benötigten Durchsatzeinheiten und die minimale Anzahl der Partitionen, aber wie viele Partitionen sollten Sie verwenden? Wählen Sie die Anzahl der Partitionen basierend auf der zu erreichenden Downstreamparallelität und Ihren zukünftigen Durchsatzanforderungen. Für die Anzahl der Partitionen, die Sie innerhalb eines Event Hubs verwenden, fällt keine Gebühr an.

Ausführliche Informationen zu Event Hubs-Preisen finden Sie unter [Event Hubs-Preise](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Event Hubs erhalten Sie unter den folgenden Links:

* Beginnen Sie mit einem [Event Hubs-Tutorial][Event Hubs tutorial].
* [Programmierleitfaden für Event Hubs](event-hubs-programming-guide.md)
* [Verfügbarkeit und Konsistenz in Event Hubs](event-hubs-availability-and-consistency.md)
* [Event Hubs – häufig gestellte Fragen](event-hubs-faq.md)
* [Event Hubs-Beispiele][]

[Event Hubs tutorial]: event-hubs-dotnet-standard-getstarted-send.md
[Event Hubs-Beispiele]: https://github.com/Azure/azure-event-hubs/tree/master/samples
