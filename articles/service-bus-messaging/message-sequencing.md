---
title: Nachrichtensequenzierung und -zeitstempel mit Azure Service Bus | Microsoft-Dokumentation
description: Beibehalten der Service Bus-Nachrichtensequenz und der Reihenfolge mit Zeitstempeln
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 8665d0a1fccecf5521a553a894e2a55e52384ec3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60402716"
---
# <a name="message-sequencing-and-timestamps"></a>Nachrichtensequenzierung und -zeitstempel

Die Sequenzier- und Zeitstempelfunktionen sind in allen Service Bus-Entitäten und -Oberflächen über die Eigenschaften [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) und [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) von empfangenen oder durchsuchten Nachrichten immer aktiviert.

In Fällen, in denen die absolute Reihenfolge der Nachrichten von Bedeutung ist und/oder ein Kunde einen vertrauenswürdigen eindeutigen Bezeichner für Nachrichten benötigt, versieht der Broker Nachrichten mit einem Stempel mit einer lückenlosen, aufsteigenden Sequenznummer relativ zur Warteschlange oder zum Thema. Bei partitionierten Entitäten wird die Sequenznummer relativ zur Partition ausgegeben.

Der Wert **SequenceNumber** ist eine eindeutige ganze 64-Bit-Zahl, die einer Nachricht zugeordnet wird, sobald sie vom Broker akzeptiert und gespeichert wird, und fungiert als ihr interner Bezeichner. Bei partitionierte Entitäten stellen die obersten 16 Bits den Partitionsbezeichner dar. Für Sequenznummern wird ein Rollover auf Null ausgeführt, wenn der 48-/64-Bit-Bereich erschöpft ist.

Die Sequenznummer kann als eindeutiger Bezeichner vertrauenswürdig sein, da sie nicht von Clients, sondern von einer zentralen und einer neutralen Autorität vergeben wird. Sie stellt auch die wahre Ankunftsreihenfolge dar und ist als Sortierkriterium genauer als ein Zeitstempel, da Zeitstempel bei extremen Nachrichtenraten möglicherweise keine ausreichend hohe Auflösung aufweisen und in Situationen, in denen das Brokereigentum zwischen Knoten übergeht, einer (allerdings minimalen) Uhrabweichung unterliegen kann.

Die absolute Ankunftsreihenfolge ist beispielsweise in Geschäftsszenarien von Bedeutung, in denen eine begrenzte Anzahl von angebotenen Waren auf Grundlage des First-Come-First-Serve-Prinzips verarbeitet wird, solange der Vorrat reicht (z.B. beim Konzertkartenverkauf).

Die Zeitstempelfunktion dient als neutrale und vertrauenswürdige Instanz, die den UTC-Zeitpunkt eines Nachrichteneingangs über die Eigenschaft **EnqueuedTimeUtc** präzise erfasst. Der Wert ist für terminabhängige Geschäftsszenarien nützlich, z.B. bei der Frage, ob ein Arbeitselement vor Mitternacht an einem bestimmten Datum übermittelt wurde, die Verarbeitung jedoch weit hinter dem Warteschlangenbacklog zurückliegt.

## <a name="scheduled-messages"></a>Geplante Nachrichten

Sie können Nachrichten zur verzögerten Verarbeitung an eine Warteschlange oder ein Thema übergeben, um z.B. einen Auftrag zu planen, der an einem bestimmten Zeitpunkt für die Verarbeitung von einem System zur Verfügung gestellt werden soll. Mit dieser Funktion wird ein zuverlässiger, verteilter zeitbasierter Scheduler bereitgestellt.

Geplante Nachrichten materialisieren in der Warteschlange erst nach dem definierten Zeitpunkt der Einreihung in die Warteschlange. Davor können geplante Nachrichten abgebrochen werden. Durch einen Abbruch wird die Nachricht gelöscht.

Sie können Nachrichten entweder durch Festlegen der Eigenschaft [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) beim Senden einer Nachricht über den regulären Sendepfad oder explizit mit der API [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) planen. Letztere gibt sofort **SequenceNumber** der geplanten Nachricht zurück, mit der Sie die geplante Nachricht bei Bedarf später löschen können. Geplante Nachrichten und deren Sequenznummern können auch durch das [Durchsuchen von Nachrichten](message-browsing.md) ermittelt werden.

Die Eigenschaft **SequenceNumber** für eine geplante Nachricht ist nur gültig, wenn sich die Nachricht in diesem Zustand befindet. Wenn die Nachricht in den aktiven Zustand übergeht, wird sie an die Warteschlange angefügt, als ob sie zum aktuellen Zeitpunkt in die Warteschlange gereiht worden wäre, was auch die Zuweisung einer neuen **SequenceNumber**-Eigenschaft einschließt.

Da das Feature auf einzelne Nachrichten verankert ist und Nachrichten nur einmal abgefragt werden können, unterstützt Service Bus keine wiederkehrenden Zeitpläne für Nachrichten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Service Bus-Messaging finden Sie in folgenden Themen:

* [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Verwenden von Service Bus-Themen und -Abonnements](service-bus-dotnet-how-to-use-topics-subscriptions.md)