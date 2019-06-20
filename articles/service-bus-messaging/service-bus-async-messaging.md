---
title: Asynchrones Messaging mit Service Bus | Microsoft Docs
description: Beschreibung des asynchronen Messaging mit Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 50778ae742c1ec66857a6c2fa6250dc3d67e5601
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60531115"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Asynchrone Nachrichtenmuster und Hochverfügbarkeit

Asynchrones Messaging kann auf verschiedenen Arten implementiert werden. Mit Warteschlangen, Themen und Abonnements unterstützt Azure Service Bus Asynchronität über einen Speicher- und Weiterleitungsmechanismus. Im normalen (synchronen) Betrieb senden Sie Nachrichten an Warteschlangen und Themen und erhalten Nachrichten von Warteschlangen und Abonnements. Anwendungen, die Sie schreiben, hängen davon ab, dass diese Entitäten immer verfügbar sind. Wenn sich der Zustand der Entität aufgrund von verschiedenen Umständen ändert, benötigen Sie eine Möglichkeit, eine Entität mit geringerer Funktionalität bereitzustellen, die die meisten Anforderungen erfüllen kann.

Anwendungen verwenden typischerweise asynchrone Nachrichtenmuster, um eine Reihe von Kommunikationsszenarien zu ermöglichen. Sie können Anwendungen erstellen, in denen Clients Nachrichten an Dienste senden können, selbst wenn der Dienst nicht ausgeführt wird. Für Anwendungen mit Kommunikationsspitzen kann eine Warteschlange dabei helfen, die Last durch Bereitstellen eines Puffers für die Kommunikation auszugleichen. Schließlich erhalten Sie einen einfachen und effektiven Lastenausgleich, um Nachrichten auf mehrere Computer zu verteilen.

Um die Verfügbarkeit dieser Entitäten zu gewährleisten, sollten Sie eine Reihe von Möglichkeiten in Betracht ziehen, in denen diese Entitäten für ein langlebiges Messagingsystem als nicht verfügbar erscheinen können. Im Allgemeinen ist die Entität für Anwendungen, die wir schreiben, auf folgende Arten nicht mehr verfügbar:

* Nachrichten können nicht gesendet werden.
* Nachrichten können nicht empfangen werden.
* Entitäten können nicht verwaltet werden (Erstellen, Abrufen, Aktualisieren oder Löschen von Entitäten).
* Mit dem Dienst kann keine Verbindung hergestellt werden.

Für jeden dieser Fehler sind verschiedene Fehlermodi vorhanden, durch die eine Anwendung mit geringerer Funktionalität Arbeiten weiterhin erledigen kann. Beispiel: Ein System, das Nachrichten senden, jedoch nicht empfangen kann, kann weiterhin Aufträge von Kunden erhalten, diese Aufträge aber nicht verarbeiten. In diesem Thema werden mögliche Probleme behandelt, die auftreten können, und wie diese Probleme gemindert werden. Mit Service Bus wurde eine Reihe von Lösungen eingeführt, die Sie abonnieren müssen, und in diesem Thema werden auch die Regeln erläutert, die die Verwendung dieser abonnierten Lösungen steuern.

## <a name="reliability-in-service-bus"></a>Zuverlässigkeit in Service Bus
Es gibt mehrere Arten, Probleme mit Nachrichten und Entitäten zu behandeln, und Richtlinien regeln die entsprechende Verwendung dieser Lösungen. Um die Richtlinien zu verstehen, müssen Sie zuerst verstehen, welche Fehler in Service Bus auftreten können. Aufgrund des Entwurfs von Azure-Systemen sind diese Probleme häufig kurzlebig. Auf allgemeiner Ebene treten die folgenden Gründe für Nichtverfügbarkeit auf:

* Drosselung durch ein externes System, von dem Service Bus abhängt. Die Drosselung ergibt sich durch Interaktionen mit Speicher- und Computeressourcen.
* Problem für ein System, von dem Service Bus abhängt. Beispielsweise können in einem bestimmten Teil des Speichers Probleme auftreten.
* Ausfall von Service Bus in einem einzelnen Subsystem. In diesem Fall kann auf dem Computeknoten ein inkonsistenter Zustand auftreten, und er muss neu gestartet werden, wodurch für alle Entitäten, die von dem Knoten verarbeitet werden, ein Lastenausgleich auf andere Knoten ausgeführt wird. Dies kann wiederum für einen kurzen Zeitraum zu einer langsamen Nachrichtenverarbeitung führen.
* Ausfall von Service Bus in einem Azure-Rechenzentrum. Dies ist ein „schwerwiegender Fehler“, durch den das System für viele Minuten oder einige Stunden nicht erreichbar ist.

> [!NOTE]
> Mit dem Begriff **Speicher** kann sowohl Azure Storage als auch SQL Azure gemeint sein.
> 
> 

Service Bus enthält eine Reihe von Entschärfungen für diese Probleme. In den folgenden Abschnitten werden die einzelnen Probleme und die entsprechenden Lösungen beschrieben.

### <a name="throttling"></a>Drosselung
Mit Service Bus ermöglicht die Drosselung eine kooperative Nachrichtenratenverwaltung. Jeder einzelne Service Bus-Knoten enthält viele Entitäten. Jede dieser Entitäten stellt die Anforderungen an das System in Bezug auf CPU, Arbeitsspeicher, Speicher und andere Facets. Wenn für diese Facets eine Nutzung ermittelt wird, die definierte Schwellenwerte überschreitet, kann Service Bus eine bestimmte Anforderung verweigern. Der Aufrufer empfängt [ServerBusyException][ServerBusyException] und führt nach 10 Sekunden einen Wiederholungsversuch durch.

Als Lösung muss der Code den Fehler lesen und alle Wiederholungsversuche für die Nachricht für mindestens 10 Sekunden anhalten. Da der Fehler auf Teilen der Kundenanwendung auftreten kann, wird erwartet, dass die Wiederholungslogik auf allen Teilen unabhängig ausgeführt wird. Der Code kann die Wahrscheinlichkeit der Drosselung durch Aktivierung der Partitionierung für eine Warteschlange oder ein Thema reduzieren.

### <a name="issue-for-an-azure-dependency"></a>Problem für eine Azure-Abhängigkeit
Bei anderen Komponenten in Azure können gelegentlich Dienstprobleme auftreten. Beispielsweise kann bei der Aktualisierung eines Systems, das Service Bus verwendet, das System vorübergehend eine geringere Funktionalität aufweisen. Um derartige Probleme zu umgehen, untersucht und implementiert Service Bus regelmäßig Lösungen. Bei diesen Lösungen treten Nebeneffekte auf. Beispielsweise implementiert Service Bus zur Behandlung vorübergehender Speicherprobleme ein System, mit dem Nachrichtensendevorgänge einheitlich arbeiten können. Aufgrund der Natur der Lösung kann es bis zu 15 Minuten dauern, bis eine gesendete Nachricht in der betroffenen Warteschlange oder im Abonnement angezeigt wird und für einen Empfangsvorgang bereit ist. Im Allgemeinen tritt dieses Problem bei den meisten Entitäten nicht auf. Allerdings ist angesichts der Anzahl von Entitäten in Service Bus in Azure diese Lösung manchmal für eine kleine Teilmenge der Service Bus-Kunden erforderlich.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Ausfall von Service Bus in einem einzelnen Subsystem
Bei jeder Anwendung können Umstände dazu führen, dass eine interne Komponente von Service Bus nicht mehr konsistent ist. Wenn Service Bus dies erkennt, werden Daten von der Anwendung erfasst, um die Diagnose des Problems zu unterstützen. Nachdem die Daten erfasst wurden, wird die Anwendung neu gestartet, um zu versuchen, einen konsistenten Status wiederherzustellen. Dieser Prozess läuft ziemlich schnell ab und führt dazu, dass eine Entität für einige Minuten nicht verfügbar zu sein scheint, obwohl die normalen Ausfallzeiten viel kürzer sind.

In diesen Fällen generiert die Clientanwendung eine Ausnahme des Typs [System.TimeoutException][System.TimeoutException] oder [MessagingException][MessagingException]. Service Bus enthält eine Lösung zur Behebung dieses Problems in Form einer automatisierten Clientwiederholungslogik. Wenn der Wiederholungszeitraum beendet ist und die Nachricht nicht zugestellt wurde, können Sie die Untersuchung mit anderen Features durchführen, die im Artikel [Behandeln von Ausfällen und Notfällen][handling outages and disasters] beschrieben werden.

## <a name="next-steps"></a>Nächste Schritte
Da Sie nun mit den Grundlagen des asynchronen Messagings in Service Bus vertraut sind, können Sie sich ausführlicher mit [Ausfällen und Notfällen][handling outages and disasters] beschäftigen.

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN
[handling outages and disasters]: service-bus-outages-disasters.md
