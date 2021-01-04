---
title: Vergleich von Azure Storage-Warteschlangen und Service Bus-Warteschlangen
description: Analysiert die Unterschiede und Gemeinsamkeiten zwischen den beiden zurzeit von Azure angebotenen Warteschlangentypen.
ms.topic: article
ms.date: 11/04/2020
ms.openlocfilehash: 31992aa2012009c51cbeae78010ae8ced65fc872
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928306"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Storage-Warteschlangen und Service Bus-Warteschlangen – Vergleich und Gegenüberstellung
In diesem Artikel werden die Unterschiede und Ähnlichkeiten zwischen den beiden Warteschlangentypen untersucht, die in Microsoft Azure angeboten werden: Storage-Warteschlangen und Service Bus-Warteschlangen. Anhand dieser Informationen können Sie eine fundierte Entscheidung darüber treffen, welche Lösung Ihre Anforderungen am besten erfüllt.

## <a name="introduction"></a>Einführung
Azure unterstützt zwei Arten von Warteschlangenmechanismen: **Storage-Warteschlangen** und **Service Bus-Warteschlangen**.

**Speicherwarteschlangen** sind Teil der [Azure Storage](https://azure.microsoft.com/services/storage/)-Infrastruktur. Sie ermöglichen das Speichern einer großen Anzahl von Nachrichten. Sie können überall auf der Welt über authentifizierte Aufrufe mithilfe von HTTP oder HTTPS auf Nachrichten zugreifen. Eine Warteschlangennachricht kann bis zu 64 KB groß sein. Eine Warteschlange kann Millionen Nachrichten enthalten, bis die maximale Kapazität eines Speicherkontos erreicht ist. Warteschlangen werden häufig verwendet, um ein Arbeits-Backlog zur asynchronen Verarbeitung zu erstellen. Weitere Informationen finden Sie unter [Was sind Azure Storage-Warteschlangen?](../storage/queues/storage-queues-introduction.md).

**Service Bus-Warteschlangen** gehören der weiter gefassten [Azure-Messaginginfrastruktur](https://azure.microsoft.com/services/service-bus/) an, die Warteschlangen, Veröffentlichungen/Abonnements und fortgeschrittenere Integrationsmuster unterstützt. Sie dienen der Integration von Anwendungen oder Anwendungskomponenten, die sich über mehrere Kommunikationsprotokolle, Datenverträge, vertrauenswürdige Domänen oder Netzwerkumgebungen erstrecken. Weitere Informationen zu Service Bus-Warteschlangen/-Themen/-Abonnements finden Sie unter [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md).


## <a name="technology-selection-considerations"></a>Überlegungen zur Wahl der richtigen Technologie
Storage-Warteschlangen und Service Bus-Warteschlangen bieten leicht unterschiedliche Funktionen. Sie können je nach den Anforderungen Ihrer jeweiligen Lösung eine oder beide auswählen.

Bei der Auswahl der Warteschlangentechnologie, die für eine bestimmte Lösung am besten geeignet ist, sollten sich Lösungsarchitekten und -entwickler an die folgenden Empfehlungen halten. 

### <a name="consider-using-storage-queues"></a>Gründe für Storage-Warteschlangen
Als Lösungsarchitekt/-entwickler sollten Sie die **Verwendung von Storage-Warteschlangen** in den folgenden Situationen in Betracht ziehen:

* In der Anwendung müssen Nachrichten mit einer Kapazität von über 80 GB in einer Warteschlange gespeichert werden.
* Der Verarbeitungsfortschritt einer Nachricht soll von der Anwendung in der Warteschlange nachverfolgt werden. Dies ist beim Absturz eines Workers von Vorteil, von dem die Nachricht verarbeitet wird. Ein weiterer Worker kann diese Informationen nutzen, um die Verarbeitung an der Stelle fortzusetzen, an der sich der Absturz ereignet hat.
* Sie benötigen serverseitige Protokolle aller Transaktionen, die für die Warteschlangen ausgeführt wurden.

### <a name="consider-using-service-bus-queues"></a>Gründe für Service Bus-Warteschlangen
Als Lösungsarchitekt/-entwickler sollten Sie die **Verwendung von Service Bus-Warteschlangen** in den folgenden Situationen in Betracht ziehen:

* Die Lösung muss in der Lage sein, Nachrichten ohne Abruf der Warteschlange empfangen zu können. Bei Service Bus kann dies erreicht werden, indem ein Empfangsvorgang mit langem Abrufintervall mit den von Service Bus unterstützten auf TCP basierenden Protokollen verwendet wird.
* Die Lösung erfordert von der Warteschlange die Zustellung nach dem FIFO-Prinzip (First-In-First-Out).
* Die Lösung muss in der Lage sein, die automatische Duplikaterkennung zu unterstützen.
* Sie wünschen eine Anwendung, die Nachrichten als parallele Datenströme mit langer Ausführungsdauer verarbeitet (Nachrichten werden mithilfe der [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid)-Eigenschaft für die Nachricht einem Datenstrom zugeordnet). In diesem Modell konkurriert jeder Knoten in der verarbeitenden Anwendung um Datenströme und nicht um Nachrichten. Wenn ein Datenstrom an einen verarbeitenden Knoten übergeben wird, kann der Knoten den Status des Anwendungsdatenstroms mithilfe von Transaktionen untersuchen.
* Beim Senden oder Empfangen mehrerer Nachrichten über eine Warteschlange muss sich die Lösung durch Transaktionsfähigkeit und Unteilbarkeit auszeichnen.
* Die Anwendung verarbeitet Nachrichten, die zwar 64 KB überschreiten können, den Grenzwert von 256 KB aber wahrscheinlich nicht erreichen.
* Sie müssen ein rollenbasiertes Zugriffsmodell für Warteschlangen bereitstellen, die Absendern und Empfängern unterschiedliche Rechte/Berechtigungen gewähren. Weitere Informationen finden Sie in den folgenden Artikeln:
    - [Authentifizieren mit verwalteten Dienstidentitäten](service-bus-managed-service-identity.md)
    - [Authentifizieren über eine Anwendung](authenticate-application.md)
* Die Warteschlangengröße überschreitet 80 GB nicht.
* Sie möchten das auf Standards basierende AMQP 1.0-Messagingprotokoll verwenden. Weitere Informationen zu AMQP finden Sie unter [Service Bus AMQP Overview (Service Bus AMQP – Übersicht)](service-bus-amqp-overview.md).
* Sie planen eine spätere Migration von der warteschlangenbasierten Punkt-zu-Punkt-Kommunikation zu einem Veröffentlichungs-Abonnement-Messagingmuster. Dieses Muster ermöglicht die Integration zusätzlicher Empfänger (Abonnenten). Jeder Empfänger erhält unabhängige Kopien von einigen oder allen der an die Warteschlange gesendeten Nachrichten. 
* Ihre Messaginglösung muss die „At-Most-Once“-Zustellung garantieren, ohne dass zusätzliche Infrastrukturkomponenten erstellt werden müssen.
* Die Lösung muss in der Lage sein, Nachrichtenbatches zu veröffentlichen und zu verarbeiten.

## <a name="compare-storage-queues-and-service-bus-queues"></a>Vergleich von Storage-Warteschlangen und Service Bus-Warteschlangen
In den Tabellen in den folgenden Abschnitten werden die Warteschlangenfeatures logisch gruppiert. So können Sie auf einen Blick die verfügbaren Funktionen in Azure Storage-Warteschlangen und Service Bus-Warteschlangen vergleichen.

## <a name="foundational-capabilities"></a>Grundlegende Funktionen
In diesem Abschnitt werden einige der grundlegenden Warteschlangenfunktionen verglichen, die von Storage-Warteschlangen und Azure Service Bus-Warteschlangen bereitgestellt werden.

| Vergleichskriterien | Storage-Warteschlangen | Service Bus-Warteschlangen |
| --- | --- | --- |
| Reihenfolgengarantie |**Nein** <br/><br>Weitere Informationen finden Sie in der ersten Anmerkung im Abschnitt [Weitere Informationen](#additional-information).</br> | **Ja – First In, First Out (FIFO)**<br/><br>(durch Verwendung von [Nachrichtensitzungen](message-sessions.md)) |
| Zustellungsgarantie |**At-Least-Once** |**At-Least-Once** (mit dem PeekLock-Empfangsmodus, dies ist die Standardeinstellung) <br/><br/>**At-Most-Once** (mit dem ReceiveAndDelete-Empfangsmodus) <br/> <br/> Weitere Informationen zu den verschiedenen [Empfangsmodi](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| Unterstützung für atomare Operationen |**Nein** |**Ja**<br/><br/> |
| Empfangsverhalten |**Nicht blockierend**<br/><br/>(wird sofort beendet, wenn keine neue Nachricht gefunden wird) |**Blockieren mit oder ohne Timeout**<br/><br/>(bietet ein langes Abrufintervall oder die [„Comet-Technik“](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Nicht blockierend**<br/><br/>(nur durch die Verwendung von .NET-verwalteter API) |
| API im Pushstil |**Nein** |**Ja**<br/><br/>[QueueClient.OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__)- und [MessageSessionHandler.OnMessage](/dotnet/api/microsoft.servicebus.messaging.messagesessionhandler.onmessage#Microsoft_ServiceBus_Messaging_MessageSessionHandler_OnMessage_Microsoft_ServiceBus_Messaging_MessageSession_Microsoft_ServiceBus_Messaging_BrokeredMessage__)-Sitzungen (.NET-API). |
| Empfangsmodus |**Peek &amp; Lease** |**Peek &amp; Lock**<br/><br/>**Receive &amp; Delete** |
| Exklusiver Zugriffsmodus |**Leasebasiert** |**Sperrenbasiert** |
| Lease-/Sperrdauer |**30 Sekunden (Standardwert)**<br/><br/>**7 Tage (maximal)** (Sie können eine Nachrichtenlease mithilfe der [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage)-API erneuern oder freigeben.) |**60 Sekunden (Standardwert)**<br/><br/>Eine Nachrichtensperre kann mit der [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock)-API verlängert werden. |
| Lease-/Sperrengranularität |**Nachrichtenebene**<br/><br/>Jede Nachricht kann einen anderen Timeoutwert aufweisen, den Sie nach Bedarf bei der Verarbeitung der Nachricht mithilfe der [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage)-API aktualisieren können. |**Warteschlangenebene**<br/><br/>(Jede Warteschlange weist eine Sperrengranularität auf, die auf alle enthaltenen Nachrichten angewendet wird; die Sperre kann jedoch mit der [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock)-API verlängert werden.) |
| Batchempfang |**Ja**<br/><br/>(explizit angebende Nachrichtenanzahl beim Abrufen von Nachrichten, bis maximal 32 Nachrichten) |**Ja**<br/><br/>(implizites Aktivieren einer PreFetch-Eigenschaft oder explizites Aktivieren durch Verwendung von Transaktionen) |
| Batchversand |**Nein** |**Ja**<br/><br/>(durch Verwendung von Transaktionen oder clientseitiger Batchverarbeitung) |

### <a name="additional-information"></a>Zusätzliche Informationen
* Für Nachrichten in Storage-Warteschlangen gilt normalerweise das FIFO-Prinzip, manchmal kann jedoch die Reihenfolge auch falsch sein. Dies ist beispielsweise der Fall, wenn die Dauer der Sichtbarkeit einer Nachricht abläuft, da eine Clientanwendung bei der Verarbeitung einer Nachricht abgestürzt ist. Wenn das Sichtbarkeitstimeout abläuft, wird die Nachricht für die Warteschlange erneut sichtbar, damit sie von einem anderen Worker aus der Warteschlange entnommen werden kann. Zu diesem Zeitpunkt wird die neue sichtbare Nachricht ggf. hinter einer Nachricht in der Warteschlange platziert, um erneut aus dieser entnommen zu werden.
* Das garantierte FIFO-Prinzip in Service Bus-Warteschlangen erfordert die Verwendung von Messagingsitzungen. Wenn die Anwendung bei der Verarbeitung einer im **Peek & Lock**-Modus empfangenen Nachricht abstürzt, wird sie beim nächsten Akzeptieren einer Messagingsitzung durch einen Warteschlangenempfänger mit der Fehlernachricht gestartet, nachdem die zugehörige Gültigkeitsdauer (TTL) der Nachricht abgelaufen ist.
* Storage-Warteschlangen wurden für die Unterstützung von Standardwarteschlangenszenarien wie den folgenden konzipiert:
    - Entkoppeln von Anwendungskomponenten zur Steigerung von Skalierbarkeit und Fehlertoleranz
    - Belastungsausgleich
    - Erstellen von Prozessworkflows
* Inkonsistenzen in Bezug auf die Nachrichtenbehandlung im Kontext von Service Bus-Sitzungen können vermieden werden, indem der Sitzungszustand verwendet wird, um den Zustand der Anwendung im Verhältnis zum Fortschritt bei der Behandlung der Nachrichtensequenz der Sitzung zu speichern, und indem Transaktionen zum Klären empfangener Nachrichten und zum Aktualisieren des Sitzungszustands verwendet werden. Diese Art von Konsistenzfeature wird in Produkten anderer Hersteller manchmal als *Exactly-Once Processing*, also genau eine Verarbeitung bezeichnet. Alle Transaktionsfehler führen offensichtlich dazu, dass Nachrichten erneut zugestellt werden. Aus diesem Grund ist dieser Begriff jedoch nicht ganz zutreffend.
* Storage-Warteschlangen bieten ein einheitliches und konsistentes Programmiermodell für Warteschlangen, Tabellen und Blobs – für Entwickler und für Betriebsteams.
* Service Bus-Warteschlangen unterstützen lokale Transaktionen im Kontext einer einzelnen Warteschlange.
* Der von Service Bus unterstützte **Receive & Delete**-Modus bietet die Möglichkeit, die Anzahl von Übermittlungsvorgängen (und damit verbundenen Gebühren) auf Kosten einer verminderten Zustellungssicherheit zu reduzieren.
* Storage-Warteschlangen stellen ein Leasingprinzip bereit, über das die Leasedauer für Nachrichten verlängert werden kann. Mit diesem Feature können die Workerprozesse eine kurze Leasedauer für Nachrichten beibehalten. Sollte ein Worker abstürzen, kann die Nachricht schnell von einem anderen Worker verarbeitet werden. Ein Worker kann darüber hinaus die Leasedauer einer Nachricht verlängern, wenn deren Verarbeitungszeit über die aktuelle Leasedauer hinausgeht.
* Speicherwarteschlangen verfügen über ein Sichtbarkeitstimeout, das Sie festlegen können, wenn eine Nachricht der Warteschlange hinzugefügt bzw. aus dieser entfernt wird. Außerdem können Sie eine Nachricht mit verschiedenen Leasewerten zur Laufzeit aktualisieren und unterschiedliche Werte für mehrere Nachrichten in derselben Warteschlange anpassen. Die Sperrtimeouts von Service Bus werden in den Metadaten der Warteschlangen definiert. Sie können die Sperre jedoch erneuern, indem Sie die [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock)-Methode aufrufen.
* Das maximale Timeout für das Blockieren einer empfangenen Nachricht in Service Bus-Warteschlangen beträgt 24 Tage. REST-basierte Timeouts verfügen jedoch über einen maximalen Wert von 55 Sekunden.
* Mithilfe der clientseitigen Batchverarbeitung von Service Bus kann ein Warteschlangenclient mehrere Nachrichten als Batch in einen einzelnen Sendevorgang einfügen. Die Batchverarbeitung ist nur für asynchrone Sendevorgänge verfügbar.
* Durch Features wie etwa die Obergrenze von 200 TB für Storage-Warteschlangen (bzw. ein höherer Wert, wenn Sie Konten virtualisieren) und eine uneingeschränkte Anzahl von Warteschlangen ist dies die ideale Plattform für SaaS-Anbieter.
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
| Speichermetrik |**Ja**<br/><br/>**Minutenmetriken** bieten Echtzeitmetriken zu Verfügbarkeit, TPS, Anzahl von API-Aufrufen, Anzahl von Fehlern u. v. m. Diese Metriken werden in Echtzeit minutenweise aggregiert und innerhalb weniger Minuten ab dem Vorfall in der Produktionsumgebung gemeldet. Weitere Informationen finden Sie unter [Informationen zu Metriken der Speicheranalyse](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Ja**<br/><br/>(Massenabfragen durch Aufrufen von [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Zustandsverwaltung |**Nein** |**Ja**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Automatische Weiterleitung von Nachrichten |**No** |**Ja** |
| Warteschlangeninhalt endgültig löschen |**Ja** |**Nein** |
| Nachrichtengruppen |**Nein** |**Ja**<br/><br/>(durch Verwendung von Messagingsitzungen) |
| Anwendungszustand pro Nachrichtengruppe |**Nein** |**Ja** |
| Duplikaterkennung |**Nein** |**Ja**<br/><br/>(auf der Absenderseite konfigurierbar) |
| Durchsuchen von Nachrichtengruppen |**Nein** |**Ja** |
| Abrufen von Nachrichtensitzungen anhand der ID |**Nein** |**Ja** |

### <a name="additional-information"></a>Zusätzliche Informationen
* Beide Warteschlangentechnologien ermöglichen das Planen der Zustellung einer Nachricht zu einem späteren Zeitpunkt.
* Die automatische Warteschlangenweiterleitung ermöglicht, dass Tausende von Warteschlangen ihre Nachrichten automatisch an eine einzelne Warteschlange weiterleiten, aus der die empfangende Anwendung die Nachricht abruft. Sie können diesen Mechanismus verwenden, um Sicherheit zu erzielen, den Datenfluss zu steuern und Speicher zwischen den einzelnen Nachrichtenverlegern zu isolieren.
* Storage-Warteschlangen bieten Unterstützung zum Aktualisieren von Nachrichteninhalten. Sie können diese Funktionen verwenden, um Zustandsinformationen und inkrementelle Statusupdates in der Nachricht beizubehalten, sodass sie vom letzten bekannten Prüfpunkt statt von Anfang an verarbeitet wird. Mit Service Bus-Warteschlangen können Sie das gleiche Szenario mithilfe von Nachrichtensitzungen aktivieren. Sitzungen ermöglichen das Speichern und Abrufen des Anwendungsverarbeitungszustands (mithilfe von [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) und [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* Service Bus-Warteschlangen unterstützen [unzustellbare Nachrichten](service-bus-dead-letter-queues.md). Dies kann nützlich sein, um Nachrichten zu isolieren, die die folgenden Kriterien erfüllen:
    - Nachrichten können von der empfangenden Anwendung nicht erfolgreich verarbeitet werden. 
    - Nachrichten können aufgrund einer abgelaufenen Gültigkeitsdauer (TTL) nicht an ihr Ziel zugestellt werden. Der TTL-Wert gibt an, wie lange eine Nachricht in der Warteschlange verbleibt. Bei Service Bus wird die Nachricht in eine bestimmte Warteschlange mit dem Namen "$DeadLetterQueue" verschoben, sobald die Gültigkeitsdauer abläuft.
* Für die Suche nach nicht verarbeitbaren Nachrichten in Storage-Warteschlangen überprüft die Anwendung die [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount)-Eigenschaft der Nachricht, wenn eine Nachricht aus der Warteschlange entfernt wird. Wenn **DequeueCount** über einem angegebenen Schwellenwert liegt, verschiebt die Anwendung die Nachricht in eine von der Anwendung definierte „Warteschlange für unzustellbare Nachrichten“.
* Bei Storage-Warteschlangen können Sie ein ausführliches Protokoll aller für die Warteschlange ausgeführten Transaktionen sowie aggregierte Metriken abrufen. Beide erleichtern das Debuggen und verdeutlichen, wie Storage-Warteschlangen von der Anwendung verwendet werden. Außerdem dienen diese Informationen dazu, die Anwendung zu optimieren und die Kosten für die Verwendung von Warteschlangen zu senken.
* Die von Service Bus unterstützten Nachrichtensitzungen ermöglichen es, die Nachrichten einer logischen Gruppe einem Empfänger zuzuordnen. Dadurch wird eine sitzungsähnliche Affinität zwischen Nachrichten und den jeweiligen Empfängern erzeugt. Sie können diese erweiterte Funktionalität in Service Bus aktivieren, indem Sie die [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId)-Eigenschaft für eine Nachricht festlegen. Empfänger können dann auf eine bestimmte Sitzungs-ID lauschen und Nachrichten empfangen, die den angegebenen Sitzungsbezeichner gemeinsam haben.
* Das Feature zur Duplikaterkennung von Service Bus-Warteschlangen entfernt gemäß dem Wert der [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId)-Eigenschaft automatisch doppelt vorhandene Nachrichten, die an eine Warteschlange bzw. ein Thema gesendet wurden.

## <a name="capacity-and-quotas"></a>Kapazität und Kontingente
In diesem Abschnitt werden Storage-Warteschlangen und Service Bus-Warteschlangen im Hinblick auf die [Kapazität und ggf. gültige Kontingente](service-bus-quotas.md) verglichen.

| Vergleichskriterien | Storage-Warteschlangen | Service Bus-Warteschlangen |
| --- | --- | --- |
| Maximale Warteschlangengröße |**500 TB**<br/><br/>(beschränkt auf die Kapazität eines [einzelnen Speicherkontos](../storage/common/storage-introduction.md#queue-storage)) |**1 GB bis 80 GB**<br/><br/>(wird bei Erstellung einer Warteschlange und dem [Aktivieren von Partitionierung](service-bus-partitioning.md) definiert – weitere Informationen finden Sie im Abschnitt „Zusätzliche Informationen“) |
| Maximale Nachrichtengröße |**64 KB**<br/><br/>(48 KB bei Verwendung der **Base64** Codierung)<br/><br/>Azure unterstützt große Nachrichten, indem Warteschlangen und Blobs kombiniert werden – in diesem Fall können bis zu 200 GB für ein einzelnes Element in der Warteschlange gespeichert werden. |**256 KB** oder **1 MB**<br/><br/>(Einschließlich Header und Text, maximale Headergröße: 64 KB.)<br/><br/>Hängt von der [Dienstebene](service-bus-premium-messaging.md) ab. |
| Maximaler TTL-Wert der Nachricht |**Unbegrenzt** (API-Version 2017-07-27 oder höher) |**TimeSpan.Max** |
| Maximale Anzahl von Warteschlangen |**Unbegrenzt** |**10.000**<br/><br/>(pro Dienstnamespace) |
| Maximale Anzahl gleichzeitiger Clients |**Unbegrenzt** |**5\.000** |

### <a name="additional-information"></a>Zusätzliche Informationen
* In Service Bus werden Grenzwerte für die Warteschlangengröße durchgesetzt. Die maximale Warteschlangengröße wird beim Erstellen der Warteschlange angegeben. Sie kann zwischen 1 GB und 80 GB liegen. Wenn die Warteschlangengröße diesen Grenzwert erreicht, werden alle weiteren eingehenden Nachrichten zurückgewiesen, und der Aufrufer empfängt eine Ausnahme. Weitere Informationen zu Kontingenten in Service Bus finden Sie unter [Service Bus-Kontingente](service-bus-quotas.md).
* Im [Premium-Tarif](service-bus-premium-messaging.md) wird keine Partitionierung unterstützt. Im Standard-Messagingtarif können Sie Service Bus-Warteschlangen und -Themen in Größen von 1 (Standard), 2, 3, 4 oder 5 GB erstellen. Bei aktivierter Partitionierung erstellt Service Bus 16 Kopien (16 Partitionen) der Entität mit der jeweils angegebenen Größe. Wenn Sie also eine Warteschlange mit einer Größe von 5 GB erstellen, beträgt die maximale Warteschlangengröße bei 16 Partitionen 5 × 16 = 80 GB.  Die maximale Größe der partitionierten Warteschlange oder des Themas wird im [Azure-Portal][Azure portal] angezeigt.
* Wenn der Inhalt der Nachricht bei Storage-Warteschlangen nicht XML-sicher ist, muss er mit **Base64** codiert werden. Wenn Sie die Nachricht mit **Base64** codieren, darf die Benutzernutzlast statt 64 KB nur 48 KB betragen.
* Bei Service Bus-Warteschlangen besteht jede in einer Warteschlange gespeicherte Nachricht aus zwei Teilen: einem Header und einem Text. Die Gesamtgröße der Nachricht darf die maximale, von der Dienstebene unterstützte Nachrichtengröße nicht überschreiten.
* Wenn Clients über das TCP-Protokoll mit Service Bus-Warteschlangen kommunizieren, ist die maximale Anzahl gleichzeitiger Verbindungen mit einer einzelnen Service Bus-Warteschlange auf 100 beschränkt. Diese Anzahl wird zwischen Absendern und Empfängern aufgeteilt. Wenn dieses Kontingent erreicht wird, werden Anforderungen für zusätzliche Verbindungen abgelehnt, und vom aufrufenden Code wird eine Ausnahme empfangen. Dieser Grenzwert gilt nicht für Clients, die über die REST-basierte API eine Verbindung mit Warteschlangen herstellen.
* Wenn Sie mehr als 10.000 Warteschlangen in einem einzelnen Service Bus-Dienstnamespace benötigen, können Sie sich mit dem Azure-Supportteam in Verbindung setzen und eine Erhöhung des Werts anfordern. Um für Service Bus auf mehr als 10.000 Warteschlangen zu skalieren, können Sie mithilfe des [Azure-Portals][Azure portal] auch zusätzliche Namespaces erstellen.

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
* Die verwalteten .NET-APIs für Brokermessaging von Service Bus nutzen im Vergleich zu REST über HTTP Vollduplex-TCP-Verbindungen zur Leistungsoptimierung und unterstützen das AMQP 1.0-Standardprotokoll.
* Die Namen von Storage-Warteschlangen dürfen 3 bis 63 Zeichen lang sein und Kleinbuchstaben, Ziffern und Bindestriche enthalten. Weitere Informationen finden Sie unter [Benennen von Warteschlangen und Metadaten](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Namen von Service Bus-Warteschlangen können bis zu 260 Zeichen lang sein und verfügen über weniger restriktive Benennungsregeln. Namen von Service Bus-Warteschlangen dürfen Buchstaben, Ziffern, Punkte, Bindestriche und Unterstriche enthalten.

## <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung
In diesem Abschnitt werden die von Storage-Warteschlangen und Service Bus-Warteschlangen unterstützten Autorisierungsfunktionen erläutert.

| Vergleichskriterien | Storage-Warteschlangen | Service Bus-Warteschlangen |
| --- | --- | --- |
| Authentifizierung |**Symmetrischer Schlüssel** |**Symmetrischer Schlüssel** |
| Sicherheitsmodell |Delegierter Zugriff über SAS-Token |SAS |
| Verbund von Identitätsanbietern |**Nein** |**Ja** |

### <a name="additional-information"></a>Zusätzliche Informationen
* Jede Anforderung an eine der beiden Warteschlangentechnologien muss authentifiziert werden. Öffentliche Warteschlangen mit anonymem Zugriff werden nicht unterstützt. Mithilfe von [SAS](service-bus-sas.md) können Sie dieses Szenario verwalten, indem Sie eine lesegeschützte SAS, eine schreibgeschützte SAS oder sogar eine SAS mit Vollzugriff veröffentlichen.
* Für das von Storage-Warteschlangen bereitgestellte Authentifizierungsschema wird ein symmetrischer Schlüssel verwendet. Bei diesem Schlüssel handelt es sich um einen HMAC-Schlüssel (Hash-based Message Authentication Code), der mit dem SHA-256-Algorithmus berechnet und als **Base64**-Zeichenfolge codiert wird. Weitere Informationen zum jeweiligen Protokoll finden Sie unter [Authentifizierung für die Azure-Speicherdienste](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Service Bus-Warteschlangen unterstützen ein ähnliches Modell mithilfe symmetrischer Schlüssel. Weitere Informationen finden Sie unter [SAS-Authentifizierung (Shared Access Signature) mit Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Zusammenfassung
Durch ein tieferes Verständnis der beiden Technologien können Sie eine fundiertere Entscheidung zur verwendeten Warteschlangentechnologie treffen. Die Entscheidung für Storage-Warteschlangen oder Service Bus-Warteschlangen hängt eindeutig von mehreren Faktoren ab. Diese Faktoren können sich nach den individuellen Anforderungen der Anwendung und der Architektur richten. 

In folgenden Fällen empfehlen sich Storage-Warteschlangen:

- Ihre Anwendung nutzt bereits die Kernfunktionen von Microsoft Azure. 
- Sie benötigen eine grundlegende Kommunikation und Messaging zwischen Diensten. 
- Sie benötigen Warteschlangen, die auch größer als 80 GB sein können.

Service Bus-Warteschlangen bieten eine Reihe erweiterter Features wie z. B. die folgenden. Sie eignen sich daher möglicherweise besser, wenn Sie eine Hybridanwendung entwickeln oder wenn Ihre Anwendung diese Features generell benötigt.

- [Sitzungen](message-sessions.md)
- [Transaktionen](service-bus-transactions.md)
- [Duplikaterkennung](duplicate-detection.md)
- [Automatisch generierte unzustellbare Nachrichten](service-bus-dead-letter-queues.md)
- [Funktionen für dauerhafte Veröffentlichungen und Abonnements](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) 



## <a name="next-steps"></a>Nächste Schritte
Die folgenden Artikel enthalten weitere Anleitungen und Informationen zur Verwendung von Storage-Warteschlangen oder Service Bus-Warteschlangen.

* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Verwenden des Warteschlangenspeicherdiensts](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Bewährte Methoden für Leistungsoptimierungen mithilfe von Service Bus-Brokermessaging](service-bus-performance-improvements.md)
* [Introducing Queues and Topics in Azure Service Bus (Einführung in Warteschlangen und Themen in Azure Service Bus) (Blogbeitrag)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [The Developer's Guide to Service Bus (Entwicklerhandbuch für Service Bus)](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Using the Queuing Service in Azure (Verwenden des Warteschlangendiensts in Azure)](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

