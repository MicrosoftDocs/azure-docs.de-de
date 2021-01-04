---
title: Häufig gestellte Fragen (FAQ) – Azure Event Hubs | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Liste häufig gestellter Fragen (FAQ) zu Azure Event Hubs sowie die zugehörigen Antworten.
ms.topic: article
ms.date: 10/27/2020
ms.openlocfilehash: c756d0bccd9b2ad303bd97d3bfb7aed8b0b82b09
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96002788"
---
# <a name="event-hubs-frequently-asked-questions"></a>Häufig gestellte Fragen zu Event Hubs

## <a name="general"></a>Allgemein

### <a name="what-is-an-event-hubs-namespace"></a>Was ist ein Event Hubs-Namespace?
Ein Namespace ist ein Bereichscontainer für Event Hub- und Kafka-Themen. Dadurch erhalten Sie einen eindeutigen [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Ein Namespace dient als Anwendungscontainer, der mehrere Event Hub- und Kafka-Themen aufnehmen kann. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Wann erstelle ich einen neuen Namespace bzw. wann verwende ich einen bereits vorhandenen?
Kapazitätszuteilungen ([Durchsatzeinheiten (Througput Units, TUs)](#throughput-units)) werden auf Namespaceebene abgerechnet. Ein Namespace ist auch mit einer Region verbunden.

Es empfiehlt sich, einen neuen Namespace zu erstellen, anstatt einen vorhandenen zu verwenden, wenn eines der folgenden Szenarios zutrifft: 

- Sie benötigen einen Event Hub, der mit einer neuen Region verbunden ist.
- Sie benötigen einen Event Hub, der mit einem anderen Abonnement verbunden ist.
- Sie benötigen einen Event Hub mit einer anderen Kapazitätszuteilung, d. h., der Kapazitätsbedarf für den Namespace zusammen mit dem hinzugefügten Event Hub würde den Schwellenwert von 40 TUs überschreiten, und Sie möchten die Verwendung eines dedizierten Clusters vermeiden.  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Was ist der Unterschied zwischen den Event Hubs-Ebenen Basic und Standard?

Im Standard-Tarif von Azure Event Hubs stehen Ihnen mehr Funktionen als im Basic-Tarif zur Verfügung. Die folgenden Features sind in Standard enthalten:

* Längere Aufbewahrung von Ereignissen
* Zusätzliche Brokerverbindungen (Bei Überschreitung der enthaltenen Anzahl fällt eine Zusatzgebühr an.)
* Mehr als eine [Consumergruppe](event-hubs-features.md#consumer-groups)
* [Capture](event-hubs-capture-overview.md)
* [Kafka-Integration](event-hubs-for-kafka-ecosystem-overview.md)

Weitere Informationen zu Tarifen, u.a. für Event Hubs Dedicated, finden Sie unter [Event Hubs – Preisdetails](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Wo steht Azure Event Hubs zur Verfügung?

Azure Event Hubs ist in allen unterstützten Azure-Regionen verfügbar. Eine Liste finden Sie auf der Seite [Azure-Regionen](https://azure.microsoft.com/regions/).  

### <a name="can-i-use-a-single-advanced-message-queuing-protocol-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Kann ich eine einzelne AMQP-Verbindung (Advance Message Queueing Protocol) zum Senden und Empfangen von mehreren Event Hubs verwenden?

Ja, solange sich alle Event Hubs im gleichen Namespace befinden.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Was ist die maximale Beibehaltungsdauer für Ereignisse?

Im Standard-Tarif für Event Hubs wird derzeit ein maximaler Aufbewahrungszeitraum von sieben Tagen unterstützt. Event Hubs sind nicht als dauerhafter Datenspeicher vorgesehen. Aufbewahrungszeiträume von mehr als 24 Stunden sind für Szenarien vorgesehen, in denen es praktisch ist, einen Stream von Ereignissen erneut in die gleichen Systeme wiederzugeben. Dies gilt beispielsweise, wenn Sie ein neues Machine Learning-Modell für vorhandene Daten trainieren oder überprüfen möchten. Wenn Sie Nachrichten länger als sieben Tage aufbewahren möchten, werden die Daten durch Aktivieren von [Event Hubs Capture](event-hubs-capture-overview.md) in Ihrem Event Hub von Ihrem Event Hub per Pull in das Storage-Konto oder das von Ihnen ausgewählte Azure Data Lake-Dienstkonto übertragen. Abhängig von Ihrer erworbenen Durchsatzeinheit entstehen durch das Aktivieren von „Capture“ Kosten.

Sie können den Aufbewahrungszeitraum für die erfassten Daten in Ihrem Speicherkonto konfigurieren. Die **Lebenszyklusverwaltung** von Azure Storage bietet eine umfassende, regelbasierte Richtlinie für universelle v2- und Blob Storage-Konten. Verwenden Sie die Richtlinie, um Ihre Daten in die entsprechenden Zugriffsebenen zu übertragen oder am Ende des Lebenszyklus der Daten ablaufen zu lassen. Weitere Informationen finden Sie unter [Verwalten des Azure Blob Storage-Lebenszyklus](../storage/blobs/storage-lifecycle-management-concepts.md). 

### <a name="how-do-i-monitor-my-event-hubs"></a>Wie überwache ich Event Hubs?
Event Hubs gibt umfassende Metriken aus, die den Zustand Ihrer Ressourcen in [Azure Monitor](../azure-monitor/overview.md) angeben. Mit den Metriken können Sie zudem die allgemeine Integrität des Event Hubs-Diensts nicht nur auf Namespaceebene, sondern auch auf Entitätsebene bewerten. Erfahren Sie mehr über die angebotene Überwachung für [Azure Event Hubs](event-hubs-metrics-azure-monitor.md).

### <a name="where-does-azure-event-hubs-store-customer-data"></a><a name="in-region-data-residency"></a>Wo speichert Azure Event Hubs Kundendaten?
Azure Event Hubs speichert Kundendaten. Diese Daten werden von Event Hubs automatisch in einer einzigen Region gespeichert, sodass dieser Dienst in der Region automatisch die Anforderungen an Datenresidenz erfüllt, einschließlich der im [Trust Center](https://azuredatacentermap.azurewebsites.net/) angegebenen Anforderungen.

[!INCLUDE [event-hubs-connectivity](../../includes/event-hubs-connectivity.md)]

## <a name="apache-kafka-integration"></a>Apache Kafka-Integration

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Wie integriere ich meine vorhandene Kafka-Anwendung in Event Hubs?
Event Hubs stellt einen Kafka-Endpunkt bereit, der in Ihren vorhandenen Apache Kafka-basierten Anwendungen verwendet werden kann. Es ist lediglich eine Konfigurationsänderung erforderlich, damit Sie die PaaS-Kafka-Funktionen nutzen können. Dies stellt eine Alternative zum Ausführen eines eigenen Kafka-Clusters dar. Event Hubs unterstützt Apache Kafka 1.0 und neuere Clientversionen und kann mit Ihren vorhandenen Kafka-Anwendungen, -Tools und -Frameworks verwendet werden. Weitere Informationen finden Sie unter [Event Hubs für Kafka-Repository](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Welche Konfigurationsänderungen muss ich an meiner vorhandenen Anwendung für die Kommunikation mit Event Hubs vornehmen?
Um eine Verbindung mit einem Event Hub herzustellen, müssen Sie die Kafka-Clientkonfigurationen aktualisieren. Dies erfolgt durch Erstellen eines Event Hub-Namespace und Abrufen der [Verbindungszeichenfolge](event-hubs-get-connection-string.md). Ändern Sie „bootstrap.servers“ so, dass der Event Hubs-FQDN und der Port auf 9093 verweisen. Aktualisieren Sie „sasl.jaas.config“ so, dass der Kafka-Client mit der richtigen Authentifizierung wie folgt an den Event Hubs-Endpunkt geleitet wird (dabei handelt es sich um die abgerufene Verbindungszeichenfolge):

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

Beispiel:

```properties
bootstrap.servers=dummynamespace.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXX";
```
Hinweis: Wenn „sasl.jaas.config“ in Ihrem Framework als Konfiguration nicht unterstützt wird, müssen Sie die Konfigurationen suchen, die zum Festlegen des SASL-Benutzernamens und -Kennworts verwendet werden, und diese stattdessen verwenden. Legen Sie den Benutzernamen auf $ConnectionString und das Kennwort auf Ihre Event Hubs-Verbindungszeichenfolge fest.

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Welche Nachrichten- oder Ereignisgröße gilt für Event Hubs?
Die maximal zulässige Nachrichtengröße für Event Hubs beträgt 1 MB.

## <a name="throughput-units"></a>Durchsatzeinheiten

### <a name="what-are-event-hubs-throughput-units"></a>Was sind Event Hubs-Durchsatzeinheiten?
Mit dem Durchsatz wird in Event Hubs die Menge der Daten in Megabyte oder die Anzahl der 1-KB-Ereignisse (in Tausenderschritten) definiert, die über Event Hubs ein- oder ausgehen. Dieser Durchsatz wird in Durchsatzeinheiten (Throughput Units, TUs) gemessen. Damit Sie den Event Hubs-Dienst verwenden können, müssen Sie zunächst TUs erwerben. Sie können Event Hubs-Durchsatzeinheiten über das Portal oder mithilfe von Event Hubs-Resource Manager-Vorlagen explizit auswählen. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Werden Durchsatzeinheiten auf alle Event Hubs in einem Namespace angewandt?
Ja, Durchsatzeinheiten (Throughput Units, TUs) werden auf alle Event Hubs in einem Event Hubs-Namespace angewandt. Das bedeutet, dass Sie TUs auf Namespaceebene erwerben und diese auf die Event Hubs unter dem Namespace aufgeteilt werden. Durch jede TU erhält der Namespace die Berechtigung für folgende Funktionen:

- Eingangsereignisse bis zu 1 MB pro Sekunde (Ereignisse, die an einen Event Hub gesendet werden), aber nicht mehr als 1000 Eingangsereignisse, Verwaltungsvorgänge oder Steuer-API-Aufrufe pro Sekunde
- Ausgangsereignisse (Ereignisse, die von einem Event Hub genutzt werden) mit bis zu 2 MB pro Sekunde, aber maximal 4096 Ausgangsereignissen
- Bis zu 84 GB Ereignisspeicher (ausreichend für die Standardaufbewahrungsdauer im 24-Stunden-Format)

### <a name="how-are-throughput-units-billed"></a>Wie werden Durchsatzeinheiten abgerechnet?
Durchsatzeinheiten (Throughput Units, TUs) werden auf Stundenbasis abgerechnet. Die Abrechnung basiert auf der maximalen Anzahl von Einheiten, die während der jeweiligen Stunde ausgewählt wurden. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Wie kann ich die Nutzung für meine Durchsatzeinheiten optimieren?
Sie können mit nur einer Durchsatzeinheit (Throughput Unit, TU) beginnen und die [automatische Vergrößerung](event-hubs-auto-inflate.md) aktivieren. Mit dem Feature für die automatische Vergrößerung können Sie die TUs erhöhen, wenn sich der Datenverkehr oder die Nutzlast erhöht. Sie können zudem eine Obergrenze für die Anzahl von TUs festlegen.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Wie funktioniert das Feature für die automatische Vergrößerung von Event Hubs?
Mit dem Feature für die automatische Vergrößerung können Sie die Durchsatzeinheiten (Throughput Units, TUs) hochskalieren. Das bedeutet, dass Sie zunächst eine geringe Anzahl von TUs erwerben können und die TUs über die Funktion für die automatische Vergrößerung zentral hochskaliert werden, wenn der eingehende Datenverkehr zunimmt. Dies bietet Ihnen eine kostengünstige Option und die vollständige Kontrolle über die Anzahl der zu verwaltenden TUs. Dieses Feature betrifft nur das **zentrale Hochskalieren**, das zentrale Herunterskalieren der Anzahl von TUs können Sie vollständig steuern, indem Sie sie aktualisieren. 

Sie können mit einer niedrigen Anzahl von Durchsatzeinheiten (TUs) beginnen, z.B. mit 2 TUs. Wenn Sie davon ausgehen, dass der Datenverkehr möglicherweise bis auf 15 TUs ansteigen kann, aktivieren Sie das Feature für die automatische Vergrößerung für den Namespace, und legen Sie die maximale Anzahl auf 15 TUs fest. Damit werden die TUs bei zunehmendem Datenverkehr nun automatisch erhöht.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Fallen Kosten an, wenn ich das Feature für die automatische Vergrößerung aktiviere?
Mit diesem Feature sind **keine Kosten** verbunden. 

### <a name="how-are-throughput-limits-enforced"></a>Wie werden Durchsatzlimits durchgesetzt?
Wenn der gesamte **eingehende** Durchsatz oder die gesamte eingehende Ereignisrate über alle Event Hubs hinweg in einem Namespace das Einheitenkontingent für den aggregierten Durchsatz überschreitet, werden Absender gedrosselt und erhalten die Fehlermeldung, dass das Eingangskontingent überschritten wurde.

Wenn der gesamte **ausgehende** Durchsatz oder die gesamte ausgehende Ereignisrate über alle Event Hubs hinweg in einem Namespace das Einheitenkontingent für den aggregierten Durchsatz überschreitet, werden Empfänger gedrosselt. Es werden jedoch keine Fehlermeldungen generiert. 

Eingangs-und Ausgangskontingente werden separat durchgesetzt, sodass kein Absender die Verlangsamung der Nutzung herbeiführen kann und kein Empfänger verhindern kann, dass Ereignisse an einen Event Hub gesendet werden.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-reservedselected"></a>Gibt es eine Beschränkung für die Anzahl der Durchsatzeinheiten, die reserviert und ausgewählt werden können?

Wenn Sie im Azure-Portal einen Namespace im Basic- oder Standard-Tarif erstellen, können Sie bis zu 20 TUs (Durchsatzeinheiten) für den Namespace auswählen. Wenn Sie den Wert auf **genau** 40 TUs erhöhen möchten, übermitteln Sie eine [Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md).  

1. Wählen Sie auf der Seite **Event Hub-Namespace** im linken Menü die Option **Neue Supportanfrage** aus. 
1. Führen Sie auf der Seite **Neue Supportanfrage** diese Schritte aus:
    1. Beschreiben Sie das Problem unter **Zusammenfassung** in wenigen Worten. 
    1. Wählen Sie als **Problemtyp****Kontingent** aus. 
    1. Wählen Sie als **Problemuntertyp** die Option **Anforderung zum Erhöhen oder Verringern von Durchsatzeinheiten** aus. 
    
        :::image type="content" source="./media/event-hubs-faq/support-request-throughput-units.png" alt-text="Seite „Supportanfrage“":::

Für eine Anzahl über 40 TUs umfasst Event Hubs das ressourcen- und kapazitätsbasierte Modell der Event Hubs Dedicated-Cluster. Dedicated-Cluster werden in Kapazitätseinheiten (Capacity Units, CUs) angeboten. Weitere Informationen finden Sie unter [Übersicht über Event Hubs Dedicated](event-hubs-dedicated-overview.md).

## <a name="dedicated-clusters"></a>Dedicated-Cluster

### <a name="what-are-event-hubs-dedicated-clusters"></a>Was sind Event Hubs Dedicated-Cluster?
Event Hubs Dedicated-Cluster bieten Bereitstellungen mit einem Mandanten für Kunden mit äußerst anspruchsvollen Anforderungen. Bei diesem Angebot wird ein kapazitätsbasierter Cluster erstellt, der nicht durch Durchsatzeinheiten gebunden ist. Das heißt, dass Sie den Cluster verwenden können, um Daten nach Vorgabe von CPU und Speicherauslastung des Clusters zu erfassen und zu streamen. Weitere Informationen finden Sie unter [Event Hubs Dedicated-Cluster](event-hubs-dedicated-overview.md).

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Wie erstelle ich einen Event Hubs Dedicated-Cluster?
Schrittanleitungen und weitere Informationen zum Einrichten eines Event Hubs Dedicated-Clusters finden Sie im [Schnellstart: Erstellen eines Event Hubs Dedicated-Clusters mithilfe des Azure-Portals](event-hubs-dedicated-cluster-create-portal.md). 


[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]


## <a name="partitions"></a>Partitionen

### <a name="how-many-partitions-do-i-need"></a>Wie viele Partitionen benötige ich?
Die Anzahl der Partitionen wird bei der Erstellung angegeben und muss zwischen 1 und 32 liegen. Die Partitionenanzahl kann nicht geändert werden. Behalten Sie daher beim Festlegen der Partitionenanzahl die langfristige Skalierung im Hinterkopf. Partitionen sind ein Mechanismus zum Organisieren von Daten, der sich auf die erforderliche Downstreamparallelität in verarbeitenden Anwendungen bezieht. Die Anzahl der Partitionen in einem Event Hub steht in direktem Zusammenhang mit der erwarteten Anzahl von gleichzeitigen Lesern. Weitere Informationen zur Partitionen finden Sie unter [Partitionen](event-hubs-features.md#partitions).

Es empfiehlt sich ggf., zum Zeitpunkt der Erstellung den höchstmöglichen Wert (32) festzulegen. Beachten Sie, dass bei Verwendung mehrerer Partitionen Ereignisse ohne Berücksichtigung der Reihenfolge an mehrere Partitionen gesendet werden – es sei denn, Sie konfigurieren die Absender so, dass sie Ereignisse nur an eine einzelne der 32 Partitionen senden und die anderen 31 Partitionen redundant sind. Im ersten Fall müssen Ereignisse für alle 32 Partitionen gelesen werden. Im zweiten Fall sind mit Ausnahme der zusätzlichen Konfiguration für den Ereignisprozessorhost keine offensichtlichen Zusatzschritte erforderlich.

Event Hubs ist für einen einzelnen Partitionsleser pro Verbrauchergruppe ausgelegt. In den meisten Fällen reicht die Standardeinstellung von vier Partitionen aus. Wenn Sie Ihre Ereignisverarbeitung skalieren, möchten Sie vielleicht die Möglichkeit haben, das Hinzufügen weiterer Partitionen zu erwägen. Es gibt keine bestimmte Durchsatzbegrenzung für eine Partition, aber der aggregierte Durchsatz in Ihrem Namespace ist durch die Anzahl der Durchsatzeinheiten beschränkt. Wenn Sie die Anzahl der Durchsatzeinheiten in Ihrem Namespace erhöhen, wünschen Sie vielleicht zusätzliche Partitionen, um gleichzeitigen Lesern zu ermöglichen, ihren eigenen maximalen Durchsatz zu erzielen.

Aber wenn Sie über ein Modell verfügen, in dem die Anwendung eine bestimmte Partition bevorzugt, ist eine höhere Anzahl von Partitionen für Sie nicht unbedingt von Vorteil. Weitere Informationen finden Sie unter [Verfügbarkeit und Konsistenz](event-hubs-availability-and-consistency.md).

### <a name="increase-partitions"></a>Erhöhen der Anzahl von Partitionen
Sie können eine Erhöhung der Anzahl von Partitionen auf (genau) 40 anfordern, indem Sie eine Supportanfrage übermitteln. 

1. Wählen Sie auf der Seite **Event Hub-Namespace** im linken Menü die Option **Neue Supportanfrage** aus. 
1. Führen Sie auf der Seite **Neue Supportanfrage** diese Schritte aus:
    1. Beschreiben Sie das Problem unter **Zusammenfassung** in wenigen Worten. 
    1. Wählen Sie als **Problemtyp****Kontingent** aus. 
    1. Wählen Sie für **Problemuntertyp** die Option **Anforderung einer Partitionsänderung** aus. 
    
        :::image type="content" source="./media/event-hubs-faq/support-request-increase-partitions.png" alt-text="Erhöhen der Anzahl von Partitionen":::

Die Partitionsanzahl kann auf genau 40 erhöht werden. In diesem Fall muss die Anzahl der Durchsatzeinheiten (TUs) ebenfalls auf 40 heraufgesetzt werden. Wenn Sie zu einem späteren Zeitpunkt das TU-Limit auf <= 20 senken möchten, wird der Grenzwert für die maximale Anzahl von Partitionen auf 32 reduziert. 

Das Verringern der Anzahl von Partitionen hat keine Auswirkung auf vorhandene Event Hubs, da Partitionen auf Event Hub-Ebene angewandt werden und nach der Erstellung des Hubs unveränderlich sind. 

## <a name="pricing"></a>Preise

### <a name="where-can-i-find-more-pricing-information"></a>Wo finde ich weitere Preisinformationen?

Umfassende Informationen zu den Preisen von Event Hubs finden Sie unter [Event Hubs – Preisübersicht](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Gibt es eine Gebühr für die Beibehaltung von Event Hubs-Ereignissen für mehr als 24 Stunden?

Der Standard-Tarif für Event Hubs ermöglicht eine Aufbewahrung von Nachrichten länger als 24 Stunden, maximal 7 Tage. Wenn die Größe der Gesamtanzahl der gespeicherten Ereignisse das Speicherkontingent für die Anzahl der ausgewählten Durchsatzeinheiten (84 GB pro Durchsatzeinheit) überschreitet, wird die überschrittene Größe zur veröffentlichten Rate für den Azure Blob Storage in Rechnung gestellt. Das Speicherkontingent in allen Durchsatzeinheiten deckt alle Speicherkosten für die Aufbewahrungsdauer von 24 Stunden (Standard), selbst, wenn die Durchsatzeinheit bis zum maximal erlaubten Eingang verbraucht ist.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Wie wird die Event Hubs-Speichergröße berechnet und in Rechnung gestellt?

Die Gesamtgröße aller gespeicherten Ereignisse, einschließlich des gesamten internen Mehraufwands für Ereignisheader oder Speicherstrukturen auf Datenträgern in allen Event Hubs, wird im Laufe des Tages gemessen. Am Ende des Tages wird die maximale Speichergröße berechnet. Das tägliche Speicherkontingent wird auf Grundlage der Mindestanzahl der Durchsatzeinheiten berechnet, die im Laufe des Tages ausgewählt wurden (jede Durchsatzeinheit bietet ein Kontingent von 84 GB). Wenn die Gesamtgröße das berechnete tägliche Speicherkontingent überschreitet, wird der überschüssige Speicher zu Azure Blob Storage-Sätzen in Rechnung gestellt (mit der Rate **lokal redundanter Speicher**).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Wie werden Eingangsereignisse von Event Hubs berechnet?

Jedes an einen Event Hub gesendete Ereignis wird als abrechenbare Nachricht gezählt. Ein *Eingangsereignis* ist als eine Dateneinheit definiert, die kleiner oder gleich 64 KB ist. Jedes Ereignis, das kleiner als oder gleich 64 KB ist, gilt als kostenpflichtig. Wenn das Ereignis größer als 64 KB ist, wird die Anzahl der abzurechnenden Ereignisse gemäß der Ereignisgröße in Vielfachen von 64 KB berechnet. Beispielsweise wird ein an einen Event Hub gesendetes 8-KB-Ereignis als ein Ereignis abgerechnet, aber eine Nachricht mit einer Größe von 96 KB, die an den Event Hub gesendet wird, wird als zwei Ereignisse in Rechnung gestellt.

Von einem Event Hub genutzte Ereignisse sowie Verwaltungsvorgänge und Kontrollaufrufe wie etwa Checkpoints werden nicht als abzurechnende Eingangsereignisse gezählt, sondern bis zur erlaubten Durchsatzeinheit angesammelt.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Gelten vermittelte Verbindungsgebühren für Event Hubs?

Verbindungsgebühren fallen nur an, wenn das AMQP-Protokoll verwendet wird. Es gibt keine Verbindungsgebühren für das Senden von Ereignissen über HTTP, unabhängig von der Anzahl der sendenden Systeme oder Geräte. Wenn Sie AMQP verwenden möchten (z.B. um effizienteres Ereignisstreaming zu erreichen oder bidirektionale Kommunikation in Befehls- und Steuerungsszenarios von IoT zu ermöglichen), lesen Sie sich bitte die Seite [Event Hubs Preise](https://azure.microsoft.com/pricing/details/event-hubs/) durch, um Näheres darüber zu erfahren, wie viele Verbindungen in jeder Dienstebene enthalten sind.

### <a name="how-is-event-hubs-capture-billed"></a>Wie erfolgt die Abrechnung für Event Hubs Capture?

Capture wird aktiviert, wenn für eine Event Hub-Instanz im Namespace die Capture-Option aktiviert wird. Event Hubs Capture wird stundenweise pro erworbener Durchsatzeinheit abgerechnet. Wenn die Anzahl von Durchsatzeinheiten erhöht oder verringert wird, werden diese Änderungen bei der Abrechnung für Event Hubs Capture in Schritten ganzer Stunden übernommen. Weitere Informationen zur Abrechnung von Event Hubs Capture finden Sie unter [Event Hubs Preise](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Werden Speichergebühren für das für Event Hubs Capture ausgewählte Speicherkonto abgerechnet?

Capture verwendet ein Speicherkonto, das Sie angeben, wenn es auf einem Event Hub aktiviert ist. Da es sich um Ihr Speicherkonto handelt, werden alle Änderungen an dieser Konfiguration über Ihr Azure-Abonnement abgerechnet.

## <a name="quotas"></a>Kontingente

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Sind Event Hubs bestimmte Kontingente zugeordnet?

Eine Liste aller Kontingente für Event Hubs finden Sie unter [Event Hubs-Kontingente](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Problembehandlung

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Warum kann ich keinen Namespace erstellen, nachdem ich ihn aus einem anderen Abonnement gelöscht habe? 
Wenn Sie einen Namespace aus einem Abonnement löschen, warten Sie vier Stunden, bevor Sie ihn mit dem gleichen Namen in einem anderen Abonnement erstellen. Sie erhalten andernfalls unter Umständen die folgende Fehlermeldung: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Welche Beispiele gibt es für Ausnahmen, die von Event Hubs generiert werden, und die vorgeschlagenen Aktionen?

Eine Liste der möglichen Event Hubs-Ausnahmen finden Sie unter [Event Hubs-Messagingausnahmen](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Diagnoseprotokolle

Event Hubs unterstützt zwei Arten von [Diagnoseprotokollen](event-hubs-diagnostic-logs.md): Erfassungsfehlerprotokolle und Betriebsprotokolle. Beide werden im JSON-Format dargestellt und können über das Azure-Portal aktiviert werden.

### <a name="support-and-sla"></a>Support und SLA

Technischer Support für Event Hubs steht über die Seite mit [häufig gestellten Fragen zu Azure Service Bus](/answers/topics/azure-service-bus.html) von Microsoft zur Verfügung. Der Support für die Abrechnungs- und Abonnementverwaltung wird kostenlos bereitgestellt.

Weitere Informationen zu unserem SLA finden Sie auf der Seite [Vereinbarungen zum Servicelevel](https://azure.microsoft.com/support/legal/sla/) .

## <a name="azure-stack-hub"></a>Azure Stack Hub

### <a name="how-can-i-target-a-specific-version-of-azure-storage-sdk-when-using-azure-blob-storage-as-a-checkpoint-store"></a>Wie kann ich eine bestimmte Version des Azure Storage SDK als Ziel angeben, wenn ich Azure Blob Storage als Prüfpunktspeicher verwende?
Wenn Sie diesen Code in Azure Stack Hub ausführen, treten Laufzeitfehler auf, es sei denn, Sie verwenden eine bestimmte Storage-API-Version als Ziel. Dies liegt daran, dass das Event Hubs SDK die neueste verfügbare Azure Storage API verwendet, die in Azure verfügbar ist und auf Ihrer Azure Stack Hub-Plattform möglicherweise nicht verfügbar ist. Azure Stack Hub unterstützt möglicherweise eine andere Storage Blob SDK-Version als die üblicherweise in Azure verfügbaren SDKs. Wenn Sie Azure Blob Storage als Prüfpunktspeicher verwenden, überprüfen Sie die [unterstützte Azure Storage-API-Version für Ihren Azure Stack Hub-Build](/azure-stack/user/azure-stack-acs-differences?#api-version), und verwenden Sie diese Version im Code als Ziel. 

Wenn Sie z. B. die Azure Stack Hub-Version 2005 verwenden, ist die Version 2019-02-02 die höchste verfügbare Version für den Storage-Dienst. Standardmäßig verwendet die Clientbibliothek des Event Hubs SDK die höchste verfügbare Version in Azure (2019-07-07 zum Zeitpunkt der Veröffentlichung des SDK). In diesem Fall müssen Sie neben den folgenden Schritten in diesem Abschnitt auch Code für die API-Version 2019-02-02 des Storage-Diensts hinzufügen. Ein Beispiel für die Verwendung einer bestimmten Storage-API-Version als Ziel finden Sie in den folgenden Beispielen für C#, Java, Python und JavaScript/TypeScript.  

Ein Beispiel für die Verwendung einer bestimmten Storage-API-Version als Ziel aus Ihrem Code finden Sie in den folgenden Beispielen auf GitHub: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)
- Python: [Synchron](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py), [asynchron](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) und [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Event Hubs finden Sie unter den folgenden Links:

* [Übersicht über Event Hubs](./event-hubs-about.md)
* [Erstellen eines Event Hubs](event-hubs-create.md)
* [Event Hubs Auto-inflate (Event Hubs – Automatische Vergrößerung)](event-hubs-auto-inflate.md)
