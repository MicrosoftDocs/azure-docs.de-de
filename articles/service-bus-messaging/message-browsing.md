---
title: Einsehen von Azure Service Bus-Nachrichten | Microsoft-Dokumentation
description: Einsehen von Azure Service Bus-Nachrichten
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
ms.openlocfilehash: 425cf262b80e83a4d06074a567a2921eee12f9c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60402750"
---
# <a name="message-browsing"></a>Einsehen von Nachrichten

Das Einsehen von Nachrichten ermöglicht einem Service Bus-Client, alle Nachrichten in einer Warteschlange oder einem Abonnement aufzählen, in der Regel zu Diagnose- und Debugzwecken.

Die Einsehvorgänge geben alle Nachrichten zurück, die im Warteschlangen- oder Abonnementnachrichtenprotokoll vorhanden sind, und nicht nur diejenigen, die zum sofortigen Abruf mit `Receive()` oder der `OnMessage()`-Schleife verfügbar sind. Die `State`-Eigenschaft jeder Nachricht gibt an, ob die Nachricht aktiv (für den Empfang verfügbar), [verzögert](message-deferral.md) oder [geplant](message-sequencing.md) ist.

Genutzte und abgelaufene Nachrichten werden durch einen asynchronen Garbage Collection-Lauf und nicht unbedingt genau dann bereinigt, wenn Nachrichten ablaufen. Daher kann der `Peek`-Vorgang Nachrichten zurückgeben, die bereits abgelaufen sind und gelöscht oder in die Warteschlange für unzustellbare Nachrichten gestellt werden, wenn ein Empfangsvorgang das nächste Mal in der Warteschlange oder im Abonnement aufgerufen wird.

Dies ist besonders zu beachten, wenn Sie versuchen, verzögerte Nachrichten aus der Warteschlange wiederherzustellen. Eine Nachricht, deren [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc)-Zeitpunkt verstrichen ist, kann nicht mehr auf andere Weise regelmäßig abgerufen werden, selbst wenn sie von Peek zurückgegeben wird. Die Rückgabe dieser Nachrichten ist beabsichtigt, da Peek ein Diagnosetool ist, das den aktuellen Status des Protokolls widerspiegelt.

Peek gibt auch Nachrichten zurück, die gesperrt wurden und aktuell von anderen Empfängern verarbeitet werden, ohne dass dieser Vorgang abgeschlossen ist. Da Peek jedoch eine getrennte Momentaufnahme zurückgibt, kann der Sperrzustand einer Nachricht bei eingesehenen Nachrichten nicht überprüft werden, und die Eigenschaften [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) und [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) lösen eine [InvalidOperationException](/dotnet/api/system.invalidoperationexception) aus, wenn die Anwendung versucht, sie zu lesen.

## <a name="peek-apis"></a>Peek-APIs

Die Methoden [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) und [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) sind in allen .NET- und Java-Clientbibliotheken und Empfängerobjekten vorhanden: **MessageReceiver**, **MessageSession**, **QueueClient** und **SubscriptionClient**. Peek funktioniert für alle Warteschlangen und Abonnements und ihre jeweiligen Warteschlangen für unzustellbare Nachrichten.

Bei wiederholtem Aufruf listet die Peek-Methode alle Nachrichten auf, die im Warteschlangen- oder Abonnementprotokoll vorhanden sind, und zwar in der Reihenfolge der Sequenznummern von der niedrigsten verfügbaren Sequenznummer bis zur höchsten. Dies ist die Reihenfolge, in der Nachrichten in die Warteschlange eingereiht wurden, und nicht die Reihenfolge, in der Nachrichten schließlich abgerufen werden.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) ruft mehrere Nachrichten ab und gibt diese als Enumeration zurück. Wenn keine Nachrichten verfügbar sind, ist das Enumerationsobjekt leer, nicht NULL.

Sie können auch eine Überladung der Methode mit einer [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) auslösen, bei der gestartet werden soll, und dann die parameterlose Methodenüberladung aufrufen, um weiter aufzuzählen. **PeekBatch** funktioniert vergleichbar, ruft aber eine Gruppe von Nachrichten auf einmal ab.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Service Bus-Messaging finden Sie in folgenden Themen:

* [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Verwenden von Service Bus-Themen und -Abonnements](service-bus-dotnet-how-to-use-topics-subscriptions.md)
