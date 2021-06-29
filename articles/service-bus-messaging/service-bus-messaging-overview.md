---
title: Übersicht über Azure Service Bus-Messaging | Microsoft-Dokumentation
description: Dieser Artikel enthält eine allgemeine Übersicht über Azure Service Bus (ein vollständig verwalteter Nachrichtenbroker für die Unternehmensintegration).
ms.topic: overview
ms.date: 02/16/2021
ms.openlocfilehash: 897729b9748d69ad3c6de507e800dbb3a1a3619c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570470"
---
# <a name="what-is-azure-service-bus"></a>Was ist Azure Service Bus?
Bei Microsoft Azure Service Bus handelt es sich um einen vollständig verwalteten Nachrichtenbroker für Unternehmen mit Nachrichtenwarteschlangen und Veröffentlichen-Abonnieren-Themen. Service Bus wird verwendet, um Anwendungen und Dienste voneinander zu entkoppeln und so die folgenden Vorteile zu erzielen:

- Übergreifender Lastenausgleich für konkurrierende Worker
- Sicherheit beim Routing und der Übertragung von Daten sowie Kontrolle über Dienst- und Anwendungsgrenzen hinweg
- Koordinierung von Transaktionsausgaben mit hohen Anforderungen an die Zuverlässigkeit 

## <a name="overview"></a>Übersicht
Die Datenübertragung zwischen verschiedenen Anwendungen und Diensten erfolgt mithilfe von *Nachrichten*. Eine Nachricht ist ein Container, der über Metadaten verfügt und Daten enthält. Bei den Daten kann es sich um eine beliebige Art von Informationen handeln, z. B. strukturierte Daten in den folgenden gängigen Formaten: JSON, XML, Apache Avro, Nur-Text.

Im Anschluss finden Sie einige gängige Messagingszenarien:

* *Messaging*: Übertragung von Geschäftsdaten (beispielsweise Verkäufe/Bestellungen, Journale oder Bestandsbewegungen)
* *Entkoppelung von Anwendungen*: Höhere Zuverlässigkeit und bessere Skalierbarkeit von Anwendungen und Diensten. Producer und Consumer müssen nicht gleichzeitig online bzw. immer verfügbar sein. Die [Last wird verteilt](/azure/architecture/patterns/queue-based-load-leveling), damit ein Dienst nicht aufgrund von Datenverkehrsspitzen überlastet wird. 
* *Lastenausgleich*: Ermöglicht es, dass mehrere [konkurrierende Consumer](/azure/architecture/patterns/competing-consumers) gleichzeitig Daten aus einer Warteschlange auslesen und die Consumer dann jeweils auf sichere Weise die exklusive Eigentümerschaft für bestimmte Nachrichten erhalten. 
* *Themen und Abonnements*: Ermöglichen 1:*n*-Beziehungen zwischen [Herausgebern und Abonnenten](/azure/architecture/patterns/publisher-subscriber), damit Abonnenten bestimmte Nachrichten aus einem Datenstrom mit veröffentlichten Nachrichten auswählen können.
* *Transaktionen*: Ermöglichen Ihnen die Durchführung mehrerer Vorgänge im Rahmen einer atomaren Transaktion. Beispielsweise können die folgenden Vorgänge im Rahmen einer Transaktion durchgeführt werden.  

    1. Abrufen einer Nachricht aus einer Warteschlange
    2. Posten von Ergebnissen in einer oder mehreren anderen Warteschlangen
    3. Verschieben der Eingabenachricht aus der ursprünglichen Warteschlange 
    
    Die Ergebnisse werden für nachgeschaltete Consumer erst im Erfolgsfall sichtbar, z. B. der erfolgreiche Abgleich der Eingabenachricht. Dies ermöglicht die Verwendung einer Semantik mit einmaliger Verarbeitung. Dieses Transaktionsmodell ist eine stabile Grundlage für das Muster für [Kompensierende Transaktionen](/azure/architecture/patterns/compensating-transaction) im weiteren Lösungskontext. 
* *Nachrichtensitzungen*: Hiermit wird eine umfassende Koordinierung von Workflows und Multiplex-Übertragungen implementiert, für die eine strikte Nachrichtensortierung oder -verzögerung erforderlich ist.

Wenn Sie bereits mit anderen Nachrichtenbrokern wie Apache ActiveMQ vertraut sind, werden Ihnen die Service Bus-Konzepte bekannt vorkommen, weil sie ähnlich sind. Da es sich bei Service Bus um ein PaaS-Angebot (Platform-as-a-Service) handelt, besteht ein wichtiger Unterschied darin, dass Sie sich nicht um die folgenden Aktionen kümmern müssen. Azure übernimmt die Durchführung dieser Aufgaben für Sie. 

- Anordnen von Protokollen und Verwalten des Speicherplatzes
- Verarbeiten von Sicherungsvorgängen
- Sicherstellen der Patches für die Betriebssysteme oder die Produkte
- Achten auf Hardwarefehler 
- Ausführen von Failovern auf einen Reservecomputer

## <a name="compliance-with-standards-and-protocols"></a>Sicherstellen der Konformität mit Standards und Protokollen

Das primäre Verbindungsprotokoll für Service Bus ist [Advanced Messaging Queueing Protocol (AMQP) 1.0](service-bus-amqp-overview.md) (offener ISO/IEC-Standard). Es ermöglicht Kunden das Schreiben von Anwendungen für die Verwendung mit Service Bus und lokalen Brokern wie ActiveMQ oder RabbitMQ. Der [Protokollleitfaden zu AMQP](service-bus-amqp-protocol-guide.md) enthält ausführliche Informationen, die Ihnen weiterhelfen, falls Sie eine Abstraktion dieser Art erstellen möchten.

[Service Bus Premium](service-bus-premium-messaging.md) ist vollständig mit der [Java Message Service (JMS) 2.0](how-to-use-java-message-service-20.md)-API für Java/Jakarta EE konform. Darüber hinaus wird von Service Bus Standard auch die JMS 1.1-Unterversion für Warteschlangen unterstützt. JMS ist eine gängige Abstraktion für Nachrichtenbroker und kann mit vielen Anwendungen und Frameworks integriert werden, z. B. das häufig verwendete Spring-Framework. Für den Wechsel von anderen Brokern zu Azure Service Bus müssen Sie lediglich die Topologie mit den Warteschlangen und Themen neu erstellen und die Abhängigkeiten und die Konfiguration für den Clientanbieter ändern. Ein Beispiel finden Sie im [Migrationsleitfaden für ActiveMQ](migrate-jms-activemq-to-servicebus.md).

## <a name="concepts-and-terminology"></a>Konzepte und Terminologie 
In diesem Abschnitt werden die Konzepte und die Terminologie von Service Bus beschrieben.

### <a name="namespaces"></a>Namespaces
Ein Namespace ist ein Container für alle Messagingkomponenten. Ein einzelner Namespace kann mehrere Warteschlangen und Themen enthalten, und Namespaces fungieren häufig als Anwendungscontainer. 

Ein Namespace ist damit vergleichbar, was in der Terminologie anderer Broker als „Server“ bezeichnet wird, aber die Konzepte sind nicht völlig äquivalent. Ein Service Bus-Namespace ist Ihr eigenes Kapazitätssegment eines großen Clusters, der aus Dutzenden von aktiven virtuellen Computern besteht. Optional kann er über drei [Azure-Verfügbarkeitszonen](../availability-zones/az-overview.md) reichen. So profitieren Sie von allen Vorteilen in Bezug auf die Verfügbarkeit und Stabilität, die sich aus der umfassenden Ausführung des Nachrichtenbrokers ergeben. Zudem müssen Sie sich nicht mit den zugrunde liegenden komplexen Zusammenhängen beschäftigen. Service Bus steht für „serverloses“ Messaging.

### <a name="queues"></a>Warteschlangen
Nachrichten werden an *Warteschlangen* gesendet und daraus empfangen. Warteschlangen dienen zum Speichern von Nachrichten, bis die empfangende Anwendung empfangs- und verarbeitungsbereit ist.

![Warteschlange](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Nachrichten in Warteschlangen werden bei ihrem Eingang sortiert und mit einem Zeitstempel versehen. Nachdem die Nachricht vom Broker akzeptiert wurde, wird sie immer dauerhaft im dreifach redundanten Speicher vorgehalten – verteilt auf die Verfügbarkeitszonen, falls die Nutzung in Zonen für den Namespace aktiviert ist. Von Service Bus werden Nachrichten niemals im Arbeitsspeicher oder im flüchtigen Speicher belassen, nachdem sie dem Client als akzeptiert gemeldet wurden.

Nachrichten werden im *Pull-Modus* (also nur auf Anforderung) zugestellt. Im Gegensatz zum Busy-Polling-Modell einiger anderer Cloudwarteschlangen ist der Pullvorgang langlebig und wird erst abgeschlossen, nachdem eine Nachricht verfügbar ist. 

### <a name="topics"></a>Themen

Nachrichten können auch unter Verwendung von *Themen* gesendet und empfangen werden. Themen sind in Veröffentlichungs-/Abonnementszenarien hilfreich. Bei der Punkt-zu-Punkt-Kommunikation werden dagegen häufig Warteschlangen verwendet.

![Thema](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Themen können über mehrere, unabhängige Abonnements verfügen, die an das Thema angefügt sind und ansonsten genauso wie Warteschlangen auf der Empfängerseite funktionieren. Ein Abonnent eines Themas kann eine Kopie jeder Nachricht erhalten, die an das Thema gesendet wird. Bei Abonnements handelt es sich um benannte Entitäten. Abonnements sind standardmäßig dauerhafter Natur, aber sie können auch so konfiguriert werden, dass sie ablaufen und dann automatisch gelöscht werden. Über die JMS-API ermöglicht Service Bus Premium Ihnen auch die Erstellung von flüchtigen Abonnements, die nur für die Dauer der Verbindung bestehen.

Sie können Regeln für ein Abonnement definieren. Eine Abonnementregel verfügt über einen *Filter*, um für die Nachricht eine Bedingung für das Kopieren in das Abonnement zu definieren, und über eine optionale *Aktion*, mit der die Metadaten der Nachricht geändert werden können. Weitere Informationen finden Sie unter [Themenfilter und -aktionen](topic-filters.md). Dieses Feature ist in den folgenden Szenarien nützlich:

- Sie möchten nicht, dass ein Abonnement alle Nachrichten empfängt, die an ein Thema gesendet werden.
- Sie möchten Nachrichten mit zusätzlichen Metadaten kennzeichnen, wenn sie ein Abonnement durchlaufen.

## <a name="advanced-features"></a>Erweiterte Funktionen

Service Bus enthält auch erweiterte Features für komplexere Messagingszenarien. In den folgenden Abschnitten werden einige dieser Features beschrieben.

### <a name="message-sessions"></a>Nachrichtensitzungen

Mithilfe von Sitzungen können Sie in Service Bus eine FIFO-Garantie (First In, First Out) erstellen. Nachrichtensitzungen ermöglichen die exklusive geordnete Verarbeitung von unbegrenzten Sequenzen verwandter Nachrichten. Um die Verarbeitung von Sitzungen in Systemen mit hoher Skalierung und Hochverfügbarkeit zu ermöglichen, kann mit dem Feature auch der Sitzungszustand gespeichert werden, damit Sitzungen auf sichere Weise zwischen Handlern verschoben werden können. Weitere Informationen finden Sie unter [Nachrichtensitzungen: FIFO (First In, First Out)](message-sessions.md).

### <a name="autoforwarding"></a>Automatische Weiterleitung

Bei der automatischen Weiterleitung wird eine Warteschlange oder ein Abonnement mit einer anderen Warteschlange oder einem anderen Thema im selben Namespace verkettet. Bei Verwendung dieses Features werden Nachrichten von Service Bus automatisch aus einer Warteschlange oder einem Abonnement in eine Zielwarteschlange bzw. ein Zielthema verschoben. Alle Verschiebungen dieser Art werden als Transaktionen durchgeführt. Weitere Informationen finden Sie unter [Verketten von Service Bus-Entitäten mit automatischer Weiterleitung](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Warteschlange für unzustellbare Nachrichten

Alle Abonnements von Service Bus-Warteschlangen und -Themen verfügen über eine Warteschlange für unzustellbare Nachrichten (Dead-Letter Queue, DLQ). Eine Warteschlange dieser Art enthält Nachrichten, die die folgenden Kriterien erfüllen: 

- Die erfolgreiche Zustellung an einen Empfänger ist nicht möglich.
- Es ist ein Timeout aufgetreten.
- Sie werden von der empfangenden Anwendung explizit zurückgestellt. 

Für Nachrichten in der Warteschlange für unzustellbare Nachrichten wird der Grund angegeben, warum sie darin platziert wurden. Die Warteschlange für unzustellbare Nachrichten verfügt über einen speziellen Endpunkt, verhält sich aber sonst wie eine reguläre Warteschlange. Eine Anwendung oder ein Tool kann eine Warteschlange für unzustellbare Nachrichten durchsuchen oder die Entfernung daraus durchführen. Sie können auch automatische Weiterleitungen aus einer Warteschlange für unzustellbare Nachrichten durchführen. Weitere Informationen finden Sie unter [Übersicht über Service Bus-Warteschlangen für unzustellbare Nachrichten](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Zeitgesteuerte Zustellung

Sie können Nachrichten zur verzögerten Verarbeitung an eine Warteschlange oder ein Thema übermitteln und einen Zeitpunkt festlegen, zu dem die Nachricht für die Nutzung verfügbar wird. Es ist auch möglich, geplante Nachrichten abzubrechen. Weitere Informationen finden Sie unter [Geplante Nachrichten](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>Nachrichtenverzögerung

Ein Warteschlangen- oder Abonnementclient kann das Abrufen einer empfangenen Nachricht auf einen späteren Zeitpunkt verschieben. Unter Umständen wurde die Nachricht nicht in der erwarteten Reihenfolge veröffentlicht, und der Client möchte warten, bis eine andere Nachricht empfangen wird. Verzögerte Nachrichten verbleiben in der Warteschlange bzw. im Abonnement und müssen explizit erneut aktiviert werden, indem die vom Dienst zugewiesene Sequenznummer verwendet wird. Weitere Informationen finden Sie unter [Nachrichtenverzögerung](message-deferral.md).

### <a name="batching"></a>Batching

Beim clientseitigen Batching kann ein Warteschlangen- oder Themenclient mehrere Nachrichten zusammenfassen und dann gemeinsam übertragen. Dies wird häufig genutzt, um Bandbreite zu sparen oder den Durchsatz zu erhöhen. Weitere Informationen finden Sie unter [Clientseitige Batchverarbeitung](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Transaktionen

Eine Transaktion fasst mehrere Vorgänge zu einem *Ausführungsbereich* zusammen. Service Bus ermöglicht Ihnen das Gruppieren von Vorgängen für mehrere Nachrichtenentitäten innerhalb einer Transaktion. Eine Nachrichtenentität kann eine Warteschlange, ein Thema oder ein Abonnement sein. Weitere Informationen finden Sie unter [Übersicht über die Service Bus-Transaktionsverarbeitung](service-bus-transactions.md).

### <a name="autodelete-on-idle"></a>Automatisches Löschen nach Leerlauf
Automatisches Löschen nach Leerlauf ermöglicht die Angabe eines Leerlaufintervalls, nach dessen Ablauf ein Warteschlangen- oder Themenabonnement automatisch gelöscht wird. Die Mindestdauer ist fünf Minuten. 

### <a name="duplicate-detection"></a>Duplikaterkennung
Mit dem Feature „Duplikaterkennung“ kann der Absender die gleiche Nachricht erneut senden, und der Broker kann ein potenzielles Duplikat verwerfen. Weitere Informationen finden Sie unter [Duplikaterkennung](duplicate-detection.md).

### <a name="geo-disaster-recovery"></a>Georedundante Notfallwiederherstellung

Wenn es für eine Azure-Region zu Downtime kommt, ermöglicht das Feature „Notfallwiederherstellung“ die Datenverarbeitung, um den Betrieb in einer anderen Region oder einem anderen Rechenzentrum fortsetzen zu können. Bei diesem Feature wird eine strukturelle Spiegelung eines Namespace in der sekundären Region vorgehalten, sodass für die Namespaceidentität zum sekundären Namespace gewechselt werden kann. Bereits gepostete Nachrichten verbleiben im ersten primären Namespace, damit nach der Behebung des Verfügbarkeitsproblems die Wiederherstellung durchgeführt werden kann. Weitere Informationen finden Sie unter [Georedundante Notfallwiederherstellung in Azure Service Bus](service-bus-geo-dr.md).

### <a name="security"></a>Sicherheit

Für Service Bus werden die Standardprotokolle [AMQP 1.0](service-bus-amqp-overview.md) und [HTTP oder REST](/rest/api/servicebus/) sowie die zugehörigen Sicherheitsfunktionen unterstützt, z. B. Transport Layer Security (TLS). Für Clients kann der Zugriff autorisiert werden, indem die rollenbasierte Sicherheit per [Shared Access Signature](service-bus-sas.md) oder [Azure Active Directory](service-bus-authentication-and-authorization.md) verwendet wird. 

Als Schutz vor unerwünschtem Datenverkehr verfügt Service Bus über [Sicherheitsfunktionen](network-security.md), z. B. IP-Firewall und Integration in virtuelle Netzwerke. 

## <a name="client-libraries"></a>Clientbibliotheken

Vollständig unterstützte Service Bus-Clientbibliotheken sind über das Azure SDK verfügbar.

- [Azure Service Bus für .NET](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Azure Service Bus-Bibliotheken für Java](/java/api/overview/azure/servicebus?preserve-view=true)
- [Azure Service Bus-Anbieter für Java JMS 2.0](how-to-use-java-message-service-20.md)
- [Azure Service Bus-Module für JavaScript und TypeScript](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Azure Service Bus-Bibliotheken für Python](/python/api/overview/azure/servicebus?preserve-view=true)

Das [primäre Protokoll von Azure Service Bus ist AMQP 1.0](service-bus-amqp-overview.md). Es kann über jeden AMQP 1.0-konformen Protokollclient genutzt werden. Einige Open-Source-AMQP-Clients verfügen über Beispiele, mit denen die Service Bus-Interoperabilität explizit demonstriert wird. Machen Sie sich im [AMQP 1.0-Protokollleitfaden](service-bus-amqp-protocol-guide.md) damit vertraut, wie Sie die Features von Service Bus direkt mit AMQP 1.0-Clients verwenden.

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="integration"></a>Integration

Service Bus kann vollständig mit vielen Microsoft- und Azure-Diensten integriert werden, z. B.:

* [Event Grid](service-bus-to-event-grid-integration-example.md)
* [Logik-Apps](../connectors/connectors-create-api-servicebus.md)
* [Azure-Funktionen](../azure-functions/functions-bindings-service-bus.md)
* [Power Platform](../connectors/connectors-create-api-servicebus.md)
* [Dynamics 365](/dynamics365/fin-ops-core/dev-itpro/business-events/how-to/how-to-servicebus)
* [Azure Stream Analytics](../stream-analytics/stream-analytics-define-outputs.md)

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit Service Bus-Messaging finden Sie in folgenden Artikeln:

* Eine Gegenüberstellung von Azure-Messagingdiensten finden Sie unter [Auswahl von Azure-Messagingdiensten: Event Grid, Event Hubs und Service Bus](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
* Sehen Sie sich die Schnellstartanleitung für [.NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md) oder [JMS](service-bus-java-how-to-use-jms-api-amqp.md) an.
* Informationen zur Verwaltung von Service Bus-Ressourcen finden Sie unter [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Informationen zu Standard- und Premium-Tarifen und den jeweiligen Preisen finden Sie unter [Service Bus – Preise](https://azure.microsoft.com/pricing/details/service-bus/).
* Informationen zu Leistung und Wartezeit für den Premium-Tarif finden Sie unter [Premium-Messaging: Wie schnell ist es?](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722).
