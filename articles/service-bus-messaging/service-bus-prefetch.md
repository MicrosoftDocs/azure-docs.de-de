---
title: Vorabruf von Azure Service Bus-Nachrichten | Microsoft-Dokumentation
description: Verbessern Sie die Leistung durch den Vorabruf von Azure Service Bus-Nachrichten.
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
ms.date: 08/30/2018
ms.author: aschhab
ms.openlocfilehash: c63e6bf66e4832a1a5b0b5e6fc3dfbbf02d1e490
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125847"
---
# <a name="prefetch-azure-service-bus-messages"></a>Vorabruf von Azure Service Bus-Nachrichten

Wenn *Prefetch* (der Vorabruf) in einem der offiziellen Service Bus-Clients aktiviert ist, erhält der Empfänger automatisch mehr Nachrichten, bis zum [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount)-Grenzwert, als ursprünglich von der Anwendung gefordert.

Ein einzelner anfänglicher [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive)- oder [ReceiverAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync)-Aufruf erhält daher eine Nachricht zur sofortigen Nutzung, die zurückgegeben wird, sobald sie verfügbar ist. Der Client erhält dann weitere Nachrichten im Hintergrund, um den Vorabrufpuffer zu füllen.

## <a name="enable-prefetch"></a>Aktivieren des Vorabrufs

Mit .NET aktivieren Sie den Vorabruf, indem Sie die [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount)-Eigenschaft von **MessageReceiver**, **QueueClient** oder **SubscriptionClient** auf eine Zahl größer null festlegen. Durch Festlegen des Werts auf 0 (null) wird der Vorabruf deaktiviert.

Sie können diese Einstellung ganz einfach zur Empfangsseite der Einstellungen der Beispiele [QueuesGettingStart](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) oder [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) hinzufügen, um die Auswirkung in diesen Kontexten zu beobachten.

Solange Nachrichten im Vorabrufpuffer verfügbar sind, werden nachfolgende **Receive**/**ReceiveAsync**-Aufrufe sofort aus dem Puffer erfüllt. Der Puffer im Hintergrund wird ergänzt, sobald Platz verfügbar ist. Wenn keine zu übermittelnden Nachrichten vorhanden sind, leert der Empfangsvorgang den Puffer und wartet oder sperrt dann wie erwartet.

Der Vorabruf funktioniert auch auf gleiche Weise mit den APIs [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) und [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync).

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Wenn er mehr Tempo bietet, warum ist dann der Vorabruf nicht die Standardoption?

Der Vorabruf beschleunigt den Nachrichtenfluss, indem eine Nachricht zum lokalen Abruf bereitgehalten wird, wenn (und bevor) die Anwendung sie anfragt. Diese Durchsatzbeschleunigung ist das Ergebnis eines Kompromisses, die der Anwendungsautor explizit treffen muss:

Im Empfangsmodus [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) sind alle Nachrichten, die in den Vorabrufpuffer eingelesen werden, nicht mehr in der Warteschlange verfügbar. Sie befinden sich nur noch im speicherinternen Vorabrufpuffer, bis sie über die APIs **Receive**/**ReceiveAsync** or **OnMessage**/**OnMessageAsync** in der Anwendung empfangen werden. Wenn die Anwendung vor dem Eingang der Nachrichten in der Anwendung beendet wird, gehen diese Nachrichten unwiederbringlich verloren.

Im Empfangsmodus [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) werden Nachrichten, die in den Vorabrufpuffer abgerufen wurden, im gesperrten Zustand im Puffer erfasst, und für sie wird das Zeitlimit für die Sperre aktiviert. Wenn der Vorabrufpuffer groß ist und die Verarbeitung so lange dauert, dass Sperren für Nachrichten ablaufen, während sie sich im Vorabrufpuffer befinden oder sogar während die Anwendung die Nachricht verarbeitet, kann es einige verwirrende Ereignisse geben, die von der Anwendung behandelt werden müssen.

Die Anwendung kann eine Nachricht mit einer abgelaufenen oder in Kürze ablaufenden Sperre empfangen. Wenn dies der Fall ist, kann die Anwendung die Nachricht möglicherweise verarbeiten, diesen Vorgang aber aufgrund des Ablaufs einer Sperre nicht abschließen kann. Das Programm kann die [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc)-Eigenschaft überprüfen (die der Taktverschiebung zwischen dem Broker und der Uhr des lokalen Computers unterliegt). Wenn die Nachrichtensperre abgelaufen ist, muss die Anwendung die Nachricht ignorieren. Es sollte kein API-Aufruf für oder mit der Nachricht erfolgen. Wenn die Nachricht noch nicht abgelaufen ist, der Ablauf aber bevorsteht, kann die Sperre erneuert und durch Aufruf von [message.RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) um eine weitere Standardsperrdauer verlängert werden.

Wenn die Sperre im Vorabrufpuffer automatisch abläuft, wird die Nachricht als abgebrochen behandelt und wieder für den Abruf aus der Warteschlange zur Verfügung gestellt. Dies kann dazu führen, dass sie in den Vorabrufpuffer abgerufen und am Ende platziert wird. Wenn der Vorabrufpuffer während des Nachrichtenablaufs in der Regel nicht abgearbeitet werden kann, führt dies dazu, dass Nachrichten zwar wiederholt vorabgerufen, aber nie effektiv in einen nutzbaren (gültig gesperrten) Zustand versetzt werden. Sie werden schließlich in die Warteschlange für unzustellbare Nachrichten verschoben, sobald die maximale Anzahl der Zustellungen überschritten ist.

Wenn Sie für die Nachrichtenverarbeitung ein hohes Maß an Zuverlässigkeit benötigen und die Verarbeitung viel Arbeit und Zeit in Anspruch nimmt, empfiehlt es sich, die Vorabruffunktion dosiert oder gar nicht zu verwenden.

Wenn Sie hohe Durchsatzraten benötigen und die Nachrichtenverarbeitung im Allgemeinen günstig ist, bietet der Vorabruf erhebliche Durchsatzvorteile.

Die maximale Anzahl der Vorabrufvorgänge und die für die Warteschlange oder das Abonnement konfigurierte Sperrdauer müssen so abgestimmt werden, dass das Zeitlimit für die Sperre mindestens die kumulative erwartete Verarbeitungszeit der erwarteten Nachrichten für die maximale Größe des Vorabrufpuffers plus eine Nachricht überschreitet. Zugleich darf das Zeitlimit für die Sperre nicht so lang sein, dass Nachrichten ihren maximalen [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive)-Wert überschreiten können, wenn sie versehentlich gelöscht werden. Deshalb muss ihre Sperre ablaufen, bevor sie erneut zugestellt werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Service Bus-Messaging finden Sie in folgenden Themen:

* [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Verwenden von Service Bus-Themen und -Abonnements](service-bus-dotnet-how-to-use-topics-subscriptions.md)
