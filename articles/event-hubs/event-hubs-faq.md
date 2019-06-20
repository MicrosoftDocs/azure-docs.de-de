---
title: Häufig gestellte Fragen (FAQ) – Azure Event Hubs | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Liste häufig gestellter Fragen (FAQ) zu Azure Event Hubs sowie die zugehörigen Antworten.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 05/15/2019
ms.author: shvija
ms.openlocfilehash: c5e58f7bc89fbe2d93f6610465abf4a92fd31406
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476120"
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

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Kann ich eine einzelne AMQP-Verbindung zum Senden und Empfangen von mehreren Event Hubs verwenden?

Ja, solange sich alle Event Hubs im gleichen Namespace befinden.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Was ist die maximale Beibehaltungsdauer für Ereignisse?

Im Standard-Tarif für Event Hubs wird derzeit ein maximaler Aufbewahrungszeitraum von sieben Tagen unterstützt. Event Hubs sind nicht als dauerhafter Datenspeicher vorgesehen. Beibehaltungsdauern größer als 24 Stunden sind für Szenarien vorgesehen, in denen es praktisch ist, einen Stream von Ereignissen in die gleichen Systeme wiederzugeben, wenn Sie beispielsweise ein neues Computerlernmodell für vorhandene Daten trainieren oder überprüfen wollen. Wenn Sie Nachrichten länger als sieben Tage aufbewahren möchten, werden die Daten durch Aktivieren von [Event Hubs Capture](event-hubs-capture-overview.md) in Ihrem Event Hub von Ihrem Event Hub per Pull in das Storage-Konto oder das von Ihnen ausgewählte Azure Data Lake-Dienstkonto übertragen. Abhängig von Ihrer erworbenen Durchsatzeinheit entstehen durch das Aktivieren von „Capture“ Kosten.

### <a name="how-do-i-monitor-my-event-hubs"></a>Wie überwache ich Event Hubs?
Event Hubs gibt umfassende Metriken aus, die den Zustand Ihrer Ressourcen in [Azure Monitor](../azure-monitor/overview.md) angeben. Mit den Metriken können Sie zudem die allgemeine Integrität des Event Hubs-Diensts nicht nur auf Namespaceebene, sondern auch auf Entitätsebene bewerten. Erfahren Sie mehr über die angebotene Überwachung für [Azure Event Hubs](event-hubs-metrics-azure-monitor.md).

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Welche Ports muss ich in der Firewall öffnen? 
Sie können die folgenden Protokolle mit Azure Service Bus verwenden, um Nachrichten zu senden und zu empfangen:

- Advanced Message Queuing Protocol (AMQP)
- HTTP
- Apache Kafka

In der folgenden Tabelle finden Sie die ausgehenden Ports, die Sie öffnen müssen, um diese Protokolle für die Kommunikation mit Azure Event Hubs verwenden zu können. 

| Protocol | Ports | Details | 
| -------- | ----- | ------- | 
| AMQP | 5671 und 5672 | Weitere Informationen finden Sie im [AMQP 1.0 in Azure Service Bus und Event Hubs – Protokollleitfaden](../service-bus-messaging/service-bus-amqp-protocol-guide.md). | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Weitere Informationen finden Sie unter [Verwenden von Azure Event Hubs aus Apache Kafka-Anwendungen](event-hubs-for-kafka-ecosystem-overview.md).

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Welche IP-Adressen muss ich in die Whitelist aufnehmen?
Um die richtigen IP-Adressen für die Whitelist für Ihre Verbindungen zu ermitteln, führen Sie diese Schritte aus:

1. Führen Sie den folgenden Befehl an einer Eingabeaufforderung aus: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Notieren Sie sich die IP-Adresse, die in `Non-authoritative answer` zurückgegeben werden. Diese IP-Adresse ist statisch. Sie würde sich nur dann ändern, wenn Sie den Namespace auf einem anderen Cluster wiederherstellen.

Wenn Sie die Zonenredundanz für Ihren Namespace verwenden, müssen Sie einige zusätzliche Schritte durchführen: 

1. Führen Sie zunächst nslookup für den Namespace aus.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Notieren Sie sich den Namen im Abschnitt **non-authoritative answer** (nicht autorisierende Antwort), der in einem der folgenden Formate vorliegt: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Führen Sie den Befehl „nslookup“ für jeden Namen mit den Suffixen s1, s2 und s3 aus, um die IP-Adressen aller drei Instanzen zu erhalten, die in drei Verfügbarkeitszonen ausgeführt werden. 

## <a name="apache-kafka-integration"></a>Apache Kafka-Integration

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Wie integriere ich meine vorhandene Kafka-Anwendung in Event Hubs?
Event Hubs stellt einen Kafka-Endpunkt bereit, der in Ihren vorhandenen Apache Kafka-basierten Anwendungen verwendet werden kann. Es ist lediglich eine Konfigurationsänderung erforderlich, damit Sie die PaaS-Kafka-Funktionen nutzen können. Dies stellt eine Alternative zum Ausführen eines eigenen Kafka-Clusters dar. Event Hubs unterstützt Apache Kafka 1.0 und neuere Clientversionen und kann mit Ihren vorhandenen Kafka-Anwendungen, -Tools und -Frameworks verwendet werden. Weitere Informationen finden Sie unter [Event Hubs für Kafka-Repository](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Welche Konfigurationsänderungen muss ich an meiner vorhandenen Anwendung für die Kommunikation mit Event Hubs vornehmen?
Um eine Verbindung mit einem Kafka-fähigen Event Hub herzustellen, müssen Sie die Kafka-Clientkonfigurationen aktualisieren. Dies erfolgt durch Erstellen eines Event Hubs-Namespace und Abrufen der [Verbindungszeichenfolge](event-hubs-get-connection-string.md). Ändern Sie „bootstrap.servers“ so, dass der Event Hubs-FQDN und der Port auf 9093 verweisen. Aktualisieren Sie „sasl.jaas.config“ so, dass der Kafka-Client mit der richtigen Authentifizierung wie folgt an den Kafka-fähigen Event Hubs-Endpunkt geleitet wird (dabei handelt es sich um die abgerufene Verbindungszeichenfolge):

bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

Beispiel:

bootstrap.servers=dummynamespace.servicebus.windows.net:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=";

Hinweis: Wenn „sasl.jaas.config“ in Ihrem Framework als Konfiguration nicht unterstützt wird, müssen Sie die Konfigurationen suchen, die zum Festlegen des SASL-Benutzernamens und -Kennworts verwendet werden, und diese stattdessen verwenden. Legen Sie den Benutzernamen auf $ConnectionString und das Kennwort auf Ihre Event Hubs-Verbindungszeichenfolge fest.

### <a name="what-is-the-messageevent-size-for-kafka-enabled-event-hubs"></a>Welche Nachrichten- oder Ereignisgröße gilt für Kafka-fähige Event Hubs?
Die maximal zulässige Nachrichtengröße für Kafka-fähige Event Hubs beträgt 1 MB.

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
Mit dem Feature für die automatische Vergrößerung können Sie die Durchsatzeinheiten (Throughput Units, TUs) zentral hochskalieren. Das bedeutet, dass Sie zunächst eine geringe Anzahl von TUs erwerben können und die TUs über die Funktion für die automatische Vergrößerung zentral hochskaliert werden, wenn der eingehende Datenverkehr zunimmt. Dies bietet Ihnen eine kostengünstige Option und die vollständige Kontrolle über die Anzahl der zu verwaltenden TUs. Dieses Feature betrifft nur das **zentrale Hochskalieren**, das zentrale Herunterskalieren der Anzahl von TUs können Sie vollständig steuern, indem Sie sie aktualisieren. 

Sie können mit einer niedrigen Anzahl von Durchsatzeinheiten (TUs) beginnen, z.B. mit 2 TUs. Wenn Sie davon ausgehen, dass der Datenverkehr möglicherweise bis auf 15 TUs ansteigen kann, aktivieren Sie das Feature für die automatische Vergrößerung für den Namespace, und legen Sie die maximale Anzahl auf 15 TUs fest. Damit werden die TUs bei zunehmendem Datenverkehr nun automatisch erhöht.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Fallen Kosten an, wenn ich das Feature für die automatische Vergrößerung aktiviere?
Mit diesem Feature sind **keine Kosten** verbunden. 

### <a name="how-are-throughput-limits-enforced"></a>Wie werden Durchsatzlimits durchgesetzt?
Wenn der gesamte eingehende Durchsatz oder die gesamte eingehende Ereignisrate über alle Event Hubs hinweg in einem Namespace das Einheitenkontingent für den aggregierten Durchsatz überschreitet, werden Absender gedrosselt und erhalten die Fehlermeldung, dass das Eingangskontingent überschritten wurde.

Wenn der gesamte ausgehende Durchsatz oder die gesamte ausgehende Ereignisrate über alle Event Hubs hinweg in einem Namespace das Einheitenkontingent für den aggregierten Durchsatz überschreitet, werden Empfänger gedrosselt und erhalten die Fehlermeldung, dass das Ausgangskontingent überschritten wurde. Eingangs-und Ausgangskontingente werden separat durchgesetzt, sodass kein Absender die Verlangsamung der Nutzung herbeiführen kann und kein Empfänger verhindern kann, dass Ereignisse an einen Event Hub gesendet werden.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Gibt es eine Beschränkung für die Anzahl der Durchsatzeinheiten (TUs), die reserviert und ausgewählt werden können?
Bei einem mehrinstanzenfähigen Angebot können die Durchsatzeinheiten auf maximal 40 TUs erhöht werden. (Sie können bis zu 20 TUs im Portal auswählen und ein Supportticket erstellen, um diese Anzahl für den Namespace auf 40 TUs zu erhöhen.) Für eine Anzahl über 40 TUs umfasst Event Hubs das ressourcen- und kapazitätsbasierte Modell der **Event Hubs Dedicated-Cluster**. Dedicated-Cluster werden in Kapazitätseinheiten (Capacity Units, CUs) angeboten.

## <a name="dedicated-clusters"></a>Dedicated-Cluster

### <a name="what-are-event-hubs-dedicated-clusters"></a>Was sind Event Hubs Dedicated-Cluster?
Event Hubs Dedicated-Cluster bieten Bereitstellungen mit einem Mandanten für Kunden mit äußerst anspruchsvollen Anforderungen. Bei diesem Angebot wird ein kapazitätsbasierter Cluster erstellt, der nicht durch Durchsatzeinheiten gebunden ist. Das heißt, dass Sie den Cluster verwenden können, um Daten nach Vorgabe von CPU und Speicherauslastung des Clusters zu erfassen und zu streamen. Weitere Informationen finden Sie unter [Event Hubs Dedicated-Cluster](event-hubs-dedicated-overview.md).

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>Welche Limits gelten für eine Kapazitätseinheit?
Welche Datenmengen Sie für einen dedizierten Cluster erfassen und streamen können, hängt von verschiedenen Faktoren ab, z.B. von den Producern, den Consumern, der Rate für die Erfassung und Verarbeitung und vielem mehr. 

In der folgenden Tabelle sind die Ergebnisse aufgeführt, die bei unseren Vergleichstests erreicht wurden:

| Form der Nutzlast | Empfänger | Eingangsbandbreite| Eingangsnachrichten | Ausgangsbandbreite | Ausgangsnachrichten | TUs gesamt | TUs pro CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batches von 100 x 1 KB | 2 | 400 MB/s | 400\.000 Nachrichten/s | 800 MB/s | 800\.000 Nachrichten/s | 400 TUs | 100 TUs | 
| Batches von 10 x 10 KB | 2 | 666 MB/s | 666.000 Nachrichten/s | 1,33 GB/s | 133\.000 Nachrichten/s | 666 TUs | 166 TUs |
| Batches von 6 x 32 KB | 1 | 1,05 GB/s | 34\.000 Nachrichten/s | 1,05 GB/s | 34\.000 Nachrichten/s | 1\.000 TUs | 250 TUs |

Bei den Tests wurden folgende Kriterien verwendet:

- Es wurde ein dedizierter Event Hubs-Cluster mit vier Kapazitätseinheiten (Capacity Units, CUs) verwendet. 
- Der für die Erfassung verwendete Event Hub umfasste 200 Partitionen. 
- Die erfassten und von allen Partitionen eingehenden Daten wurden von zwei Empfängeranwendungen empfangen.

Anhand dieser Ergebnisse bekommen Sie eine Vorstellung davon, was mit einem dedizierten Event Hubs-Cluster möglich ist. Zudem ist bei einem dedizierten Cluster Event Hub Capture für Szenarien mit Microbatch und Langzeitaufbewahrung aktiviert.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Wie erstelle ich einen Event Hubs Dedicated-Cluster?
Einen Event Hub Dedicated-Cluster erstellen Sie, indem Sie eine [Supportanfrage zur Kontingenterhöhung](https://portal.azure.com/#create/Microsoft.Support) senden oder sich an das [Event Hubs-Team](mailto:askeventhubs@microsoft.com) wenden. Es dauert in der Regel etwa zwei Wochen, bis der Cluster bereitgestellt und Ihnen zur Verwendung zur Verfügung gestellt wird. Dieser Vorgang ist vorübergehend, bis ein vollständiger Self-Service über das Azure-Portal oder Azure Resource Manager-Vorlagen zur Verfügung steht, bei dem der Cluster nach etwa zwei Stunden bereitgestellt wird.

## <a name="best-practices"></a>Bewährte Methoden

### <a name="how-many-partitions-do-i-need"></a>Wie viele Partitionen benötige ich?

Die Anzahl von Partitionen auf einem Event Hub kann nach der Einrichtung nicht mehr geändert werden. Vor diesem Hintergrund ist es wichtig, dass Sie vor Beginn genau überlegen, wie viele Partitionen Sie benötigen. 

Event Hubs ist für einen einzelnen Partitionsleser pro Verbrauchergruppe ausgelegt. In den meisten Fällen reicht die Standardeinstellung von vier Partitionen aus. Wenn Sie Ihre Ereignisverarbeitung skalieren möchten, möchten Sie vielleicht die Möglichkeit haben, das Hinzufügen weiterer Partitionen zu erwägen. Es gibt keine bestimmte Durchsatzbegrenzung für eine Partition, aber der aggregierte Durchsatz in Ihrem Namespace ist durch die Anzahl der Durchsatzeinheiten beschränkt. Wenn Sie die Anzahl der Durchsatzeinheiten in Ihrem Namespace erhöhen, wünschen Sie vielleicht zusätzliche Partitionen, um gleichzeitigen Lesern zu ermöglichen, ihren eigenen maximalen Durchsatz zu erzielen.

Aber wenn Sie über ein Modell verfügen, in dem die Anwendung eine bestimmte Partition bevorzugt, ist eine höhere Anzahl von Partitionen für Sie nicht unbedingt von Vorteil. Weitere Informationen finden Sie unter [Verfügbarkeit und Konsistenz](event-hubs-availability-and-consistency.md).

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

Technischer Support für Event Hubs steht über die [Communityforen](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus)bereit. Der Support für die Abrechnungs- und Abonnementverwaltung wird kostenlos bereitgestellt.

Weitere Informationen zu unserem SLA finden Sie auf der Seite [Vereinbarungen zum Servicelevel](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Event Hubs finden Sie unter den folgenden Links:

* [Übersicht über Event Hubs](event-hubs-what-is-event-hubs.md)
* [Erstellen eines Event Hubs](event-hubs-create.md)
* [Event Hubs Auto-inflate (Event Hubs – Automatische Vergrößerung)](event-hubs-auto-inflate.md)
