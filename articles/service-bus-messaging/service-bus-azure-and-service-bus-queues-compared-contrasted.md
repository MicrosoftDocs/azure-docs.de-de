---
title: Azure Storage- und Service Bus-Warteschlangen – Vergleich und Gegenüberstellung | Microsoft-Dokumentation
description: Analysiert die Unterschiede und Gemeinsamkeiten zwischen den beiden zurzeit von Azure angebotenen Warteschlangentypen.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: dbbc43bc7a2f42f8a72ce12d84da1ae406a588d2
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799351"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Storage-Warteschlangen und Service Bus-Warteschlangen – Vergleich und Gegenüberstellung
In diesem Artikel werden die Unterschiede und Ähnlichkeiten zwischen den beiden Warteschlangentypen untersucht, die derzeit in Microsoft Azure angeboten werden: Storage-Warteschlangen und Service Bus-Warteschlangen. Mithilfe dieser Informationen können Sie die beiden Technologien vergleichen und abgrenzen und sind in der Lage, besser informierte Entscheidungen darüber zu treffen, welche Lösung Ihre Anforderungen am besten erfüllt.

## <a name="introduction"></a>Einführung
Azure unterstützt zwei Arten von Warteschlangenmechanismen: **Storage-Warteschlangen** und **Service Bus-Warteschlangen**.

**Storage-Warteschlangen** gehören zur Infrastruktur von [Azure Storage](https://azure.microsoft.com/services/storage/). Sie verfügen über eine einfache REST-basierte GET/PUT/PEEK-Oberfläche, die ein zuverlässiges und konsequentes Messaging innerhalb von Diensten und zwischen diesen gewährleistet.

**Service Bus-Warteschlangen** gehören der weiter gefassten [Azure-Messaging](https://azure.microsoft.com/services/service-bus/)-Infrastruktur an, die neben Warteschlangen auch Veröffentlichungen/Abonnements und fortgeschrittenere Integrationsmuster unterstützt. Weitere Informationen zu Service Bus-Warteschlangen/Themen/Abonnements finden Sie in der [Übersicht über Service Bus](service-bus-messaging-overview.md).

Obwohl beide Warteschlangentechnologien parallel vorhanden sind, wurden Storage-Warteschlangen zuerst als dedizierter Warteschlangenspeicher-Mechanismus eingeführt, der auf den Azure Storage-Diensten aufsetzt. Service Bus-Warteschlangen basieren auf der umfassenderen Messaginginfrastruktur. Diese dient der Integration von Anwendungen oder Anwendungskomponenten, die sich über mehrere Kommunikationsprotokolle, Datenverträge, vertrauenswürdige Domänen oder Netzwerkumgebungen erstrecken.

## <a name="technology-selection-considerations"></a>Überlegungen zur Wahl der richtigen Technologie
Storage-Warteschlangen und Service Bus-Warteschlangen sind Implementierungen des Message Queuing-Diensts, der zurzeit von Microsoft Azure angeboten wird. Die beiden Implementierungen weisen geringfügig unterschiedliche Funktionsmerkmale auf. Das heißt, Sie können sich für eine entscheiden oder beide verwenden. Welche Sie auswählen, hängt von den Anforderungen Ihrer spezifischen Lösung bzw. dem zu lösenden geschäftlichen oder technischen Problem ab.

Bei der Wahl der Warteschlangentechnologie, die für eine bestimmte Lösung am besten geeignet ist, sollten sich Lösungsarchitekten und -entwickler an die folgenden Empfehlungen halten. Weitere Informationen finden Sie im nächsten Abschnitt.

Als Lösungsarchitekt/-entwickler sollten Sie die **Verwendung von Storage-Warteschlangen** in den folgenden Situationen in Betracht ziehen:

* In der Anwendung müssen Nachrichten mit einer Kapazität von über 80 GB in einer Warteschlange gespeichert werden.
* Der Verarbeitungsfortschritt einer Nachricht soll von der Anwendung innerhalb der Warteschlange nachverfolgt werden. Dies ist beim Absturz eines Workerprozesses, von dem die Nachricht verarbeitet wird, von Vorteil. Ein nachfolgender Workerprozess kann diese Informationen verwenden, um die Verarbeitung an der Stelle, an der sich der Absturz ereignet hat, fortzusetzen.
* Sie benötigen serverseitige Protokolle aller Transaktionen, die für die Warteschlangen ausgeführt wurden.

Als Lösungsarchitekt/-entwickler sollten Sie die **Verwendung von Service Bus-Warteschlangen** in den folgenden Situationen in Betracht ziehen:

* Die Lösung muss in der Lage sein, Nachrichten ohne Abruf der Warteschlange empfangen zu können. Bei Service Bus kann dies erreicht werden, indem ein Empfangsvorgang mit langem Abrufintervall mithilfe der auf TCP basierenden Protokolle verwendet wird, die Service Bus unterstützen.
* Die Lösung erfordert von der Warteschlange die Zustellung nach dem FIFO-Prinzip (First-In-First-Out).
* Die Lösung muss in der Lage sein, die automatische Duplikaterkennung zu unterstützen.
* Sie wünschen eine Anwendung, die Nachrichten als parallele Datenströme mit langer Ausführungsdauer verarbeitet (Nachrichten werden mithilfe der [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid)-Eigenschaft für die Nachricht einem Datenstrom zugeordnet). In diesem Modell konkurriert jeder Knoten in der verarbeitenden Anwendung um Datenströme und nicht um Nachrichten. Wenn ein Datenstrom an einen verarbeitenden Knoten übergeben wird, kann der Knoten den Status des Anwendungsdatenstroms mithilfe von Transaktionen untersuchen.
* Beim Senden oder Empfangen mehrerer Nachrichten über eine Warteschlange muss sich die Lösung durch Transaktionsfähigkeit und Unteilbarkeit auszeichnen.
* Die Anwendung verarbeitet Nachrichten, die zwar 64 KB überschreiten können, die Grenze von 256 KB aber wahrscheinlich nicht erreichen werden.
* Sie müssen ein rollenbasiertes Zugriffsmodell für Warteschlangen bereitstellen, die Absendern und Empfängern unterschiedliche Rechte/Berechtigungen gewähren.
* Die Warteschlangengröße überschreitet 80 GB nicht.
* Sie möchten das auf Standards basierende AMQP 1.0-Messagingprotokoll verwenden. Weitere Informationen zu AMQP finden Sie unter [Service Bus AMQP Overview (Service Bus AMQP – Übersicht)](service-bus-amqp-overview.md).
* Sie können schließlich eine Migration von der warteschlangenbasierten Punkt-zu-Punkt-Kommunikation zu einem Nachrichtenaustauschmodell in Erwägung ziehen, um zusätzliche Empfänger (Abonnenten) nahtlos zu integrieren, von denen jeder eine Kopie einiger oder aller an die Warteschlange gesendeten Nachrichten erhält. Der letzte Punkt bezieht sich auf die Veröffentlichungs-/Abonnementfunktion, die von Service Bus selbst bereitgestellt wird.
* Ihre Messaginglösung muss in der Lage sein, die „At-Most-Once“-Zustellung zu garantieren, ohne dass zusätzliche Infrastrukturkomponenten implementiert werden müssen.
* Sie möchten in der Lage sein, Nachrichtenbatches zu veröffentlichen und zu verarbeiten.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Vergleich von Storage-Warteschlangen und Service Bus-Warteschlangen
In den Tabellen der folgenden Abschnitte sind die Warteschlangenfunktionen logisch gruppiert. Sie erkennen also auf einen Blick, welche Funktionen in Azure Storage-Warteschlangen und Service Bus-Warteschlangen verfügbar sind.

## <a name="foundational-capabilities"></a>Grundlegende Funktionen
In diesem Abschnitt werden einige der grundlegenden Warteschlangenfunktionen verglichen, die von Storage-Warteschlangen und Azure Service Bus-Warteschlangen bereitgestellt werden.

| Vergleichskriterien | Storage-Warteschlangen | Service Bus-Warteschlangen |
| --- | --- | --- |
| Reihenfolgengarantie |**Nein** <br/><br>Weitere Informationen finden Sie in der ersten Anmerkung im Abschnitt „Weitere Informationen“.</br> |**Ja – First In, First Out (FIFO)**<br/><br>(durch Verwendung von Messagingsitzungen) |
| Zustellungsgarantie |**At-Least-Once** |**At-Least-Once**<br/><br/>**At-Most-Once** |
| Unterstützung für atomare Operationen |**Nein** |**Ja**<br/><br/> |
| Empfangsverhalten |**Nicht blockierend**<br/><br/>(wird sofort beendet, wenn keine neue Nachricht gefunden wird) |**Blockieren mit/ohne Timeout**<br/><br/>(bietet ein langes Abrufintervall oder die [„Comet-Technik“](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Nicht blockierend**<br/><br/>(nur durch die Verwendung von .NET-verwalteter API) |
| API im Pushstil |**Nein** |**Ja**<br/><br/>[OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__)- und **OnMessage**-Sitzungs-.NET-API. |
| Empfangsmodus |**Peek &amp; Lease** |**Peek &amp; Lock**<br/><br/>**Receive &amp; Delete** |
| Exklusiver Zugriffsmodus |**Leasebasiert** |**Sperrenbasiert** |
| Lease-/Sperrdauer |**30 Sekunden (Standardwert)**<br/><br/>**7 Tage (maximal)** (Sie können eine Nachrichtenlease mithilfe der [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage)-API erneuern oder freigeben.) |**60 Sekunden (Standardwert)**<br/><br/>Eine Nachrichtensperre kann mit der [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock)-API verlängert werden. |
| Lease-/Sperrengranularität |**Nachrichtenebene**<br/><br/>(Jede Nachricht kann einen anderen Timeoutwert aufweisen, den Sie nach Bedarf bei der Verarbeitung der Nachricht mithilfe der [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage)-API aktualisieren können.) |**Warteschlangenebene**<br/><br/>(Jede Warteschlange weist eine Sperrengranularität auf, die auf alle enthaltenen Nachrichten angewendet wird; die Sperre kann jedoch mit der [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock)-API verlängert werden.) |
| Batchempfang |**Ja**<br/><br/>(explizit angebende Nachrichtenanzahl beim Abrufen von Nachrichten, bis maximal 32 Nachrichten) |**Ja**<br/><br/>(implizites Aktivieren einer PreFetch-Eigenschaft oder explizites Aktivieren durch Verwendung von Transaktionen) |
| Batchversand |**Nein** |**Ja**<br/><br/>(durch Verwendung von Transaktionen oder clientseitiger Batchverarbeitung) |

### <a name="additional-information"></a>Zusätzliche Informationen
* Für Nachrichten in Storage-Warteschlangen gilt normalerweise das FIFO-Prinzip, aber manchmal kann die Reihenfolge auch falsch sein, z.B. wenn die Sichtbarkeitstimeoutdauer der Nachricht abläuft (etwa als Folge des Absturzes einer Clientanwendung während der Verarbeitung). Wenn das Sichtbarkeitstimeout abläuft, wird die Nachricht für die Warteschlange erneut sichtbar, damit sie von einem anderen Worker aus der Warteschlange entnommen werden kann. Zu diesem Zeitpunkt wird die erneut sichtbare Nachricht ggf. hinter eine Nachricht in der Warteschlange platziert (um erneut aus dieser entnommen zu werden), die ursprünglich nach ihr in die Warteschlange gestellt wurde.
* Das garantierte FIFO-Prinzip in Service Bus-Warteschlangen erfordert die Verwendung von Messagingsitzungen. Wenn die Anwendung bei der Verarbeitung einer im **Peek & Lock**-Modus empfangenen Nachricht abstürzt, startet die Anwendung, sobald ein Warteschlangenempfänger das nächste Mal eine Messagingsitzung akzeptiert, mit der Fehlernachricht, nachdem die zugehörige Gültigkeitsdauer (TTL) abgelaufen ist.
* Storage-Warteschlangen unterstützen Standardwarteschlangenszenarios, z.B. das Abkoppeln von Anwendungskomponenten zur Erhöhung von Skalierbarkeit und Fehlertoleranz, Lastenausgleich und das Erstellen von Prozessworkflows.
* Service Bus-Warteschlangen unterstützen die *At-Least-Once*-Zustellungsgarantie. 
* Inkonsistenzen in Bezug auf die Nachrichtenbehandlung im Kontext von Service Bus-Sitzungen können vermieden werden, indem der Sitzungszustand verwendet wird, um den Zustand der Anwendung im Verhältnis zum Fortschritt bei der Behandlung der Nachrichtensequenz der Sitzung zu speichern, und indem Transaktionen zum Klären empfangener Nachrichten und zum Aktualisieren des Sitzungszustands verwendet werden. Diese Art von Konsistenzfeature wird in Produkten anderer Anbieter manchmal als *Exactly-Once Processing* (genau einmalige Verarbeitung) bezeichnet, aber Transaktionsfehler führen offensichtlich dazu, dass Nachrichten erneut zugestellt werden. Daher ist der Begriff nicht genau zutreffend.
* Storage-Warteschlangen bieten ein einheitliches und konsistentes Programmiermodell für Warteschlangen, Tabellen und Blobs – für Entwickler und für Betriebsteams.
* Service Bus-Warteschlangen unterstützen lokale Transaktionen im Kontext einer einzelnen Warteschlange.
* Der von Service Bus unterstützte **Receive & Delete**-Modus bietet die Möglichkeit, die Anzahl von Übermittlungsvorgängen (und damit verbundenen Gebühren) auf Kosten einer verminderten Zustellungssicherheit zu reduzieren.
* Storage-Warteschlangen stellen ein Leasingprinzip bereit, über das die Leasedauer für Nachrichten verlängert werden kann. Auf diese Weise können die Workerprozesse eine kurze Leasedauer für Nachrichten beibehalten. Sollte ein Workerprozess abstürzen, kann die Nachricht folglich von einem anderen Workerprozess schnell verarbeitet werden. Ein Workerprozess kann darüber hinaus die Leasedauer für eine Nachricht verlängern, wenn deren Verarbeitungszeit über die aktuelle Leasedauer hinausgeht.
* Speicherwarteschlangen verfügen über ein Sichtbarkeitstimeout, das Sie festlegen können, wenn eine Nachricht der Warteschlange hinzugefügt bzw. aus dieser entfernt wird. Außerdem können Sie eine Nachricht anhand verschiedener Leasewerte zur Laufzeit aktualisieren und unterschiedliche Werte für mehrere Nachrichten aktualisieren, die sich in derselben Warteschlange befinden. Service Bus-Sperrtimeouts sind in den Warteschlangenmetadaten definiert. Sie können die Sperre jedoch verlängern, indem Sie die [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock)-Methode aufrufen.
* Das maximale Timeout für das Blockieren einer empfangenen Nachricht in Service Bus-Warteschlangen beträgt 24 Tage. REST-basierte Timeouts verfügen jedoch über einen maximalen Wert von 55 Sekunden.
* Mithilfe der clientseitigen Batchverarbeitung von Service Bus kann ein Warteschlangenclient mehrere Nachrichten als Batch in einen einzelnen Sendevorgang einfügen. Die Batchverarbeitung ist nur für asynchrone Sendevorgänge verfügbar.
* Durch Features wie etwa die Obergrenze von 200 TB für Storage-Warteschlangen (bzw. ein höherer Wert, wenn Sie Konten virtualisieren) und eine uneingeschränkte Anzahl von Warteschlangen ist die Anwendung eine ideale Plattform für SaaS-Anbieter.
* Storage-Warteschlangen bieten einen flexiblen und leistungsstarken delegierten Zugriffssteuerungsmechanismus.

## <a name="advanced-capabilities"></a>Erweiterte Funktionen
In diesem Abschnitt werden die von Storage-Warteschlangen und Service Bus-Warteschlangen bereitgestellten erweiterten Funktionen verglichen.

| Vergleichskriterien | Storage-Warteschlangen | Service Bus-Warteschlangen |
| --- | --- | --- |
| Zeitgesteuerte Zustellung |**Ja** |**Ja** |
| Automatisch generierte unzustellbare Nachrichten |**Nein** |**Ja** |
| Vergrößern des TTL-Werts einer Warteschlange |**Ja**<br/><br/>(über direktes Update des Sichtbarkeitstimeouts) |**Ja**<br/><br/>(über eine dedizierte API-Funktion bereitgestellt) |
| Unterstützung für nicht verarbeitbare Nachrichten |**Ja** |**Ja** |
| Direktes Update |**Ja** |**Ja** |
| Serverseitiges Transaktionsprotokoll |**Ja** |**Nein** |
| Speichermetrik |**Ja**<br/><br/>**Minutenmetriken**: stellen in Echtzeit Metriken für die Verfügbarkeit, TPS, Anzahl von API-Aufrufen, Fehleranzahl usw. zur Verfügung (aggregiert pro Minute und gemeldet innerhalb weniger Minuten nach Ereigniseintritt in der Produktion). Weitere Informationen finden Sie unter [Informationen zu Metriken der Speicheranalyse](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Ja**<br/><br/>(Massenabfragen durch Aufrufen von [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Zustandsverwaltung |**Nein** |**Ja**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Automatische Nachrichtenweiterleitung |**Nein** |**Ja** |
| Warteschlangeninhalt endgültig löschen |**Ja** |**Nein** |
| Nachrichtengruppen |**Nein** |**Ja**<br/><br/>(durch Verwendung von Messagingsitzungen) |
| Anwendungszustand pro Nachrichtengruppe |**Nein** |**Ja** |
| Duplikaterkennung |**Nein** |**Ja**<br/><br/>(auf der Absenderseite konfigurierbar) |
| Durchsuchen von Nachrichtengruppen |**Nein** |**Ja** |
| Abrufen von Nachrichtensitzungen anhand der ID |**Nein** |**Ja** |

### <a name="additional-information"></a>Zusätzliche Informationen
* Beide Warteschlangentechnologien ermöglichen das Planen der Zustellung einer Nachricht zu einem späteren Zeitpunkt.
* Die automatische Warteschlangenweiterleitung ermöglicht, dass Tausende von Warteschlangen ihre Nachrichten automatisch an eine einzelne Warteschlange weiterleiten, aus der die empfangene Anwendung die Nachricht abruft. Sie können diesen Mechanismus verwenden, um Sicherheit zu erzielen, den Datenfluss zu steuern und Speicher zwischen den einzelnen Nachrichtenverlegern zu isolieren.
* Storage-Warteschlangen bieten Unterstützung zum Aktualisieren von Nachrichteninhalten. Sie können diese Funktionen verwenden, um Zustandsinformationen und inkrementelle Statusupdates in der Nachricht beizubehalten, sodass sie vom letzten bekannten Prüfpunkt statt von Anfang an verarbeitet wird. Mit Service Bus-Warteschlangen können Sie das gleiche Szenario mithilfe von Nachrichtensitzungen aktivieren. Sitzungen ermöglichen das Speichern und Abrufen des Anwendungsverarbeitungszustands (mithilfe von [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) und [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* [Unzustellbare Nachrichten](service-bus-dead-letter-queues.md), die nur von Service Bus-Warteschlangen unterstützt werden, leisten wertvolle Hilfe beim Isolieren von Nachrichten, die von der Empfangsanwendung nicht erfolgreich verarbeitet werden können bzw. die ihr Ziel aufgrund einer abgelaufenen TTL-Eigenschaft (Time-To-Live) nicht erreichen können. Der TTL-Wert gibt an, wie lange eine Nachricht in der Warteschlange verbleibt. Bei Service Bus wird die Nachricht in eine bestimmte Warteschlange mit dem Namen "$DeadLetterQueue" verschoben, sobald die Gültigkeitsdauer abläuft.
* Für die Suche nach nicht verarbeitbaren Nachrichten in Storage-Warteschlangen überprüft die Anwendung die [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount)-Eigenschaft der Nachricht, wenn eine Nachricht aus der Warteschlange entfernt wird. Wenn **DequeueCount** über einem angegebenen Schwellenwert liegt, verschiebt die Anwendung die Nachricht in eine von der Anwendung definierte „Warteschlange für unzustellbare Nachrichten“.
* Bei Storage-Warteschlangen können Sie ein ausführliches Protokoll aller für die Warteschlange ausgeführten Transaktionen sowie aggregierte Metriken abrufen. Beide erleichtern das Debuggen und verdeutlichen, wie Storage-Warteschlangen von der Anwendung verwendet werden. Weiter dienen diese Informationen dazu, die Anwendungsleistung zu optimieren und die Kosten für die Verwendung von Warteschlangen zu senken.
* Das von Service Bus unterstützte Konzept „Nachrichtensitzung“ macht es möglich, dass Nachrichten, die einer bestimmten logischen Gruppe angehören, einem vorgegebenen Empfänger zugeordnet werden. Auf diese Weise entsteht eine sitzungsähnliche Affinität zwischen Nachrichten und den betreffenden Empfängern. Sie können diese erweiterte Funktionalität in Service Bus aktivieren, indem Sie die [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId)-Eigenschaft für eine Nachricht festlegen. Empfänger können dann auf eine bestimmte Sitzungs-ID lauschen und Nachrichten empfangen, die den angegebenen Sitzungsbezeichner gemeinsam haben.
* Die von Service Bus-Warteschlangen unterstützte Duplikaterkennung entfernt gemäß dem Wert der [MessageID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId)-Eigenschaft automatisch doppelt vorhandene Nachrichten, die an eine Warteschlange bzw. ein Thema gesendet wurden.

## <a name="capacity-and-quotas"></a>Kapazität und Kontingente
In diesem Abschnitt werden Storage-Warteschlangen und Service Bus-Warteschlangen im Hinblick auf die [Kapazität und ggf. gültige Kontingente](service-bus-quotas.md) verglichen.

| Vergleichskriterien | Storage-Warteschlangen | Service Bus-Warteschlangen |
| --- | --- | --- |
| Maximale Warteschlangengröße |**500 TB**<br/><br/>(beschränkt auf die Kapazität eines [einzelnen Speicherkontos](../storage/common/storage-introduction.md#queue-storage)) |**1 GB bis 80 GB**<br/><br/>(wird bei Erstellung einer Warteschlange und dem [Aktivieren von Partitionierung](service-bus-partitioning.md) definiert – weitere Informationen finden Sie im Abschnitt „Zusätzliche Informationen“) |
| Maximale Nachrichtengröße |**64 KB**<br/><br/>(48 KB bei Verwendung der **Base64** Codierung)<br/><br/>Azure unterstützt große Nachrichten, indem Warteschlangen und Blobs kombiniert werden – in diesem Fall können bis zu 200 GB für ein einzelnes Element in der Warteschlange gespeichert werden. |**256 KB** oder **1 MB**<br/><br/>(Einschließlich Header und Text, maximale Headergröße: 64 KB.)<br/><br/>Hängt von der [Dienstebene](service-bus-premium-messaging.md) ab. |
| Maximaler TTL-Wert der Nachricht |**Unbegrenzt** (ab API-Version 2017-07-27) |**TimeSpan.Max** |
| Maximale Anzahl von Warteschlangen |**Unbegrenzt** |**10.000**<br/><br/>(pro Dienstnamespace) |
| Maximale Anzahl gleichzeitiger Clients |**Unbegrenzt** |**Unbegrenzt**<br/><br/>(Die Beschränkung auf 100 gleichzeitige Verbindungen gilt nur für die Kommunikation über das TCP-Protokoll.) |

### <a name="additional-information"></a>Zusätzliche Informationen
* In Service Bus werden Grenzwerte für die Warteschlangengröße durchgesetzt. Die maximale Warteschlangengröße wird bei der Erstellung der Warteschlange angegeben und kann einen Wert zwischen 1 und 80 GB aufweisen. Wenn der bei der Erstellung der Warteschlange festgelegte Größenwert erreicht ist, werden zusätzlich eingehende Nachrichten abgelehnt, und vom aufrufenden Code wird eine Ausnahme empfangen. Weitere Informationen zu Kontingenten in Service Bus finden Sie unter [Service Bus-Kontingente](service-bus-quotas.md).
* Im [Premium-Tarif](service-bus-premium-messaging.md) wird keine Partitionierung unterstützt. Im Standard-Tarif können Sie Service Bus-Warteschlangen mit einer Größe von 1, 2, 3, 4 oder 5 GB erstellen. (Die Standardgröße ist 1 GB.) Im Standard-Tarif erstellt Service Bus bei aktivierter Partitionierung (Standardeinstellung) 16 Partitionen für jedes angegebene GB. Wenn Sie also eine Warteschlange mit einer Größe von 5 GB erstellen, beträgt die maximale Warteschlangengröße bei 16 Partitionen 5*16 = 80 GB. Die maximale Größe der partitionierten Warteschlange oder des Themas wird im zugehörigen Eintrag im [Azure-Portal][Azure portal] angezeigt.
* Wenn der Inhalt der Nachricht bei Storage-Warteschlangen nicht XML-sicher ist, muss er mit **Base64** codiert werden. Wenn Sie die Nachricht mit **Base64** codieren, darf die Benutzernutzlast statt 64 KB nur 48 KB betragen.
* Bei Service Bus-Warteschlangen besteht jede in einer Warteschlange gespeicherte Nachricht aus zwei Teilen: einem Header und einem Text. Die Gesamtgröße der Nachricht darf die maximale, von der Dienstebene unterstützte Nachrichtengröße nicht überschreiten.
* Wenn Clients über das TCP-Protokoll mit Service Bus-Warteschlangen kommunizieren, ist die maximale Anzahl gleichzeitiger Verbindungen mit einer einzelnen Service Bus-Warteschlange auf 100 beschränkt. Diese Anzahl wird zwischen Absendern und Empfängern aufgeteilt. Wenn dieses Kontingent erreicht wird, werden nachfolgende Anforderungen für zusätzliche Verbindungen abgelehnt, und vom aufrufenden Code wird eine Ausnahme empfangen. Diese Begrenzung gilt nicht für Clients, die über die REST-basierte API eine Verbindung mit Warteschlangen herstellen.
* Wenn Sie mehr als 10.000 Warteschlangen in einem einzelnen Service Bus-Dienstnamespace benötigen, können Sie sich mit dem Azure-Supportteam in Verbindung setzen und eine Erhöhung des Werts anfordern. Um mit Service Bus auf mehr als 10.000 Warteschlangen zu skalieren, können Sie mithilfe des [Azure-Portals][Azure portal] auch zusätzliche Namespaces erstellen.

## <a name="management-and-operations"></a>Verwaltung und Abläufe
In diesem Abschnitt werden die von Storage-Warteschlangen und Service Bus-Warteschlangen bereitgestellten Verwaltungsfunktionen verglichen.

| Vergleichskriterien | Storage-Warteschlangen | Service Bus-Warteschlangen |
| --- | --- | --- |
| Verwaltungsprotokoll |**REST über HTTP/HTTPS** |**REST über HTTPS** |
| Laufzeitprotokoll |**REST über HTTP/HTTPS** |**REST über HTTPS**<br/><br/>**AMQP 1.0-Standard (TCP mit TLS)** |
| .NET API |**Ja**<br/><br/>(.NET-Storage-Client-API) |**Ja**<br/><br/>(.NET-Service Bus-API) |
| Systemeigenes C++ |**Ja** |**Ja** |
| Java-API |**Ja** |**Ja** |
| PHP-API |**Ja** |**Ja** |
| Node.js-API |**Ja** |**Ja** |
| Unterstützung beliebiger Metadaten |**Ja** |**Nein** |
| Benennungsregeln für Warteschlangen |**Bis zu 63 Zeichen lang**<br/><br/>(Warteschlangennamen müssen in Kleinbuchstaben geschrieben sein.) |**Bis zu 260 Zeichen lang**<br/><br/>(bei Warteschlangenpfaden und -namen wird die Groß-/Kleinschreibung nicht berücksichtigt.) |
| Funktion zum Abrufen der Warteschlangenlänge |**Ja**<br/><br/>(ungefährer Wert, wenn Nachrichten nach dem TTL-Wert ablaufen, ohne gelöscht zu werden.) |**Ja**<br/><br/>(genauer Zeitpunktwert.) |
| Peek-Funktion |**Ja** |**Ja** |

### <a name="additional-information"></a>Zusätzliche Informationen
* Storage-Warteschlangen unterstützen beliebige Attribute, die auf die Warteschlangenbeschreibung angewendet werden können, in Form von Name-Wert-Paaren.
* Beide Warteschlangentechnologien bieten außerdem die Möglichkeit, einen Blick in eine Nachricht zu werfen, ohne sie zu sperren. Dies kann bei der Implementierung eines Explorer-/Browsertools für die Warteschlange hilfreich sein.
* Die verwalteten .NET-APIs für Brokermessaging von Service Bus nutzen im Vergleich zu "REST über HTTP" Vollduplex-TCP-Verbindungen zur Leistungsoptimierung und unterstützen das AMQP 1.0-Standardprotokoll.
* Die Namen von Storage-Warteschlangen dürfen 3 bis 63 Zeichen lang sein und Kleinbuchstaben, Ziffern und Bindestriche enthalten. Weitere Informationen finden Sie unter [Benennen von Warteschlangen und Metadaten](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Namen von Service Bus-Warteschlangen können bis zu 260 Zeichen lang sein und verfügen über weniger restriktive Benennungsregeln. Namen von Service Bus-Warteschlangen dürfen Buchstaben, Ziffern, Punkte, Bindestriche und Unterstriche enthalten.

## <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung
In diesem Abschnitt werden die von Storage-Warteschlangen und Service Bus-Warteschlangen unterstützten Autorisierungsfunktionen erläutert.

| Vergleichskriterien | Storage-Warteschlangen | Service Bus-Warteschlangen |
| --- | --- | --- |
| Authentication |**Symmetrischer Schlüssel** |**Symmetrischer Schlüssel** |
| Sicherheitsmodell |Delegierter Zugriff über SAS-Token |SAS |
| Verbund von Identitätsanbietern |**Nein** |**Ja** |

### <a name="additional-information"></a>Zusätzliche Informationen
* Jede Anforderung an eine der beiden Warteschlangentechnologien muss authentifiziert werden. Öffentliche Warteschlangen mit anonymem Zugriff werden nicht unterstützt. Mithilfe von [SAS](service-bus-sas.md) können Sie dieses Szenario verwalten, indem Sie eine lesegeschützte SAS, eine schreibgeschützte SAS oder sogar eine SAS mit Vollzugriff veröffentlichen.
* Das von Storage-Warteschlangen bereitgestellte Authentifizierungsschema schließt die Verwendung eines symmetrischen Schlüssels ein. Dieser entspricht einem hashbasierten Nachrichtenauthentifizierungscode (hash-based Message Authentication Code, HMAC), der mit dem SHA-256-Algorithmus berechnet und als **Base64**-Zeichenfolge codiert wird. Weitere Informationen zum jeweiligen Protokoll finden Sie unter [Authentifizierung für die Azure-Speicherdienste](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Service Bus-Warteschlangen unterstützen ein ähnliches Modell mithilfe symmetrischer Schlüssel. Weitere Informationen finden Sie unter [SAS-Authentifizierung (Shared Access Signature) mit Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Zusammenfassung
Durch ein tieferes Verständnis der beiden Technologien können Sie eine fundiertere Entscheidung zu der verwendeten Warteschlangentechnologie treffen. Die Entscheidung für Storage-Warteschlangen oder Service Bus-Warteschlangen hängt eindeutig von mehreren Faktoren ab. Diese Faktoren können sich nach den individuellen Anforderungen der Anwendung und der Architektur richten. Wenn die Anwendung bereits die Kernfunktionen von Microsoft Azure verwendet, ziehen Sie es möglicherweise vor, Storage-Warteschlangen zu verwenden, insbesondere, wenn es um grundlegende Kommunikation und Messaging zwischen Diensten geht oder Sie Warteschlangen benötigen, die größer sind als 80 GB.

Da Service Bus-Warteschlangen eine Vielzahl erweiterter Funktionen wie Sitzungen, Transaktionen, Duplikaterkennung, automatische unzustellbare Nachrichten und bewährte Funktionen für Veröffentlichung/Abonnements bieten, werden Sie diese vielleicht vorziehen, wenn Sie eine Hybridanwendung erstellen bzw. diese Funktionen von der Anwendung benötigt werden.

## <a name="next-steps"></a>Nächste Schritte
Die folgenden Artikel enthalten weitere Anleitungen und Informationen zur Verwendung von Storage-Warteschlangen oder Service Bus-Warteschlangen.

* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Verwenden des Warteschlangenspeicherdiensts](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Bewährte Methoden für Leistungsoptimierungen mithilfe von Service Bus-Brokermessaging](service-bus-performance-improvements.md)
* [Introducing Queues and Topics in Azure Service Bus (Einführung in Warteschlangen und Themen in Azure Service Bus) (Blogbeitrag)](https://www.code-magazine.com/article.aspx?quickid=1112041)
* [The Developer's Guide to Service Bus (Entwicklerhandbuch für Service Bus)](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Using the Queuing Service in Azure (Verwenden des Warteschlangendiensts in Azure)](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

