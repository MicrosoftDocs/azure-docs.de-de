---
title: 'Azure Service Bus: Durchsuchen von Nachrichten'
description: Durch das Durchsuchen und Einsehen von Service Bus-Nachrichten kann ein Azure Service Bus-Client alle Nachrichten in einer Warteschlange oder in einem Abonnement aufzählen.
ms.topic: article
ms.date: 11/11/2020
ms.openlocfilehash: c52c9c967d4eada1a931e188ed4d25f7691cfb91
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553640"
---
# <a name="message-browsing"></a>Einsehen von Nachrichten

Das Einsehen von Nachrichten ermöglicht einem Service Bus-Client, alle Nachrichten in einer Warteschlange oder einem Abonnement zu Diagnose- und Debugzwecken aufzuzählen.

Das Einsehen einer Warteschlange gibt alle Nachrichten in der Warteschlange zurück, nicht nur diejenigen, die für die sofortige Übernahme mit `Receive()` oder der `OnMessage()`-Schleife verfügbar sind. Die `State`-Eigenschaft jeder Nachricht gibt an, ob die Nachricht aktiv (für den Empfang verfügbar), [verzögert](message-deferral.md) oder [geplant](message-sequencing.md) ist. Das Einsehen für ein Abonnement gibt alle Nachrichten außer geplante Nachrichten im Abonnementnachrichtenprotokoll zurück. 

Verbrauchte und abgelaufene Nachrichten werden durch eine asynchrone Ausführung von „Garbage Collection“ bereinigt. Dieser Schritt tritt möglicherweise nicht unmittelbar nach dem Ablauf von Nachrichten auf. Aus diesem Grund kann `Peek` Nachrichten zurückgeben, die bereits abgelaufen sind. Diese Nachrichten werden entfernt oder in die Warteschlange für unzustellbare Nachrichten verschoben, wenn das nächste Mal ein Empfangsvorgang für die Warteschlange oder das Abonnement aufgerufen wird. Beachten Sie dieses Verhalten, wenn Sie versuchen, verzögerte Nachrichten aus der Warteschlange wiederherzustellen. Eine abgelaufene kann nicht mehr auf andere Weise regelmäßig abgerufen werden, selbst wenn sie von Peek zurückgegeben wird. Die Rückgabe dieser Nachrichten ist beabsichtigt, da Peek ein Diagnosetool ist, das den aktuellen Status des Protokolls widerspiegelt.

Peek gibt auch Nachrichten zurück, die gesperrt wurden und aktuell von anderen Empfängern verarbeitet werden. Da Peek jedoch eine getrennte Momentaufnahme zurückgibt, kann der Sperrzustand einer Nachricht für eingesehene Nachrichten nicht beobachtet werden. Die Eigenschaften [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) und [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) lösen eine [InvalidOperationException](/dotnet/api/system.invalidoperationexception) aus, wenn die Anwendung versucht, sie zu lesen.

## <a name="peek-apis"></a>Peek-APIs

Die Methoden [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) und [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) sind in .NET- und Java-Clientbibliotheken und für Empfängerobjekte vorhanden: **MessageReceiver**, **MessageSession**. Peek funktioniert für Warteschlangen, Abonnements und ihre jeweiligen Warteschlangen für unzustellbare Nachrichten.

Bei wiederholtem Aufruf listet **Peek** alle Nachrichten in der Warteschlange oder dem Abonnementprotokoll auf, und zwar in der Reihenfolge der Sequenznummern von der niedrigsten verfügbaren bis zur höchsten. Dies ist die Reihenfolge, in der Nachrichten in die Warteschlange gestellt wurden, und nicht die Reihenfolge, in der Nachrichten schließlich abgerufen werden.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) ruft mehrere Nachrichten ab und gibt diese als Enumeration zurück. Wenn keine Nachrichten verfügbar sind, ist das Enumerationsobjekt leer, nicht NULL.

Sie können auch eine Überladung der Methode mit einer [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) auslösen, bei der gestartet werden soll, und dann die parameterlose Methodenüberladung aufrufen, um die Aufzählung fortzusetzen. **PeekBatch** funktioniert vergleichbar, ruft aber eine Gruppe von Nachrichten auf einmal ab.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Service Bus-Messaging finden Sie in folgenden Themen:

* [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Verwenden von Service Bus-Themen und -Abonnements](service-bus-dotnet-how-to-use-topics-subscriptions.md)
