---
title: Abrufen der Anzahl von Azure Service Bus-Nachrichten | Microsoft-Dokumentation
description: Informationen zum Abrufen der Anzahl von Azure Service Bus-Nachrichten.
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
ms.openlocfilehash: adfd8c5849cfee69805715378a3f56ec9f685b00
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60403957"
---
# <a name="message-counters"></a>Nachrichtenzähler

Sie können die Anzahl der Nachrichten, die in Warteschlangen und Abonnements gespeichert sind, mit Azure Resource Manager und den [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager)-APIs von Service Bus im .NET Framework SDK abrufen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Mit PowerShell können Sie die Anzahl wie folgt abrufen:

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Details zur Anzahl von Nachrichten

Die Anzahl aktiver Nachrichten zu kennen ist nützlich, um festzustellen, ob eine Warteschlange einen Rückstand aufbaut, der mehr Ressourcen für die Verarbeitung benötigt als diejenigen, die derzeit bereitgestellt sind. Die [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails)-Klasse bietet die folgenden Details zur Anzahl:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): Nachrichten in der Warteschlange oder im Abonnement, die sich im aktiven Zustand befinden und zustellbereit sind.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): Nachrichten in der Warteschlange für unzustellbare Nachrichten.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): Nachrichten im Zustand „Geplant“.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): Nachrichten, die nicht in eine andere Warteschlange oder ein anderes Thema übertragen wurden und die in die Warteschlange für unzustellbare Nachrichten verschoben wurden.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): Nachrichten, deren Übertragung in eine andere Warteschlange oder ein anderes Thema aussteht.

Wenn eine Anwendung Ressourcen anhand der Länge der Warteschlange skalieren möchte, sollte sie dies langsam tun. Die Erfassung der Nachrichtenzählern ist ein aufwendiger Vorgang innerhalb des Nachrichtenbrokers, dessen Ausführung häufig direkte und negative Auswirkungen auf die Leistung der Entität hat.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Service Bus-Messaging finden Sie in folgenden Themen:

* [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Verwenden von Service Bus-Themen und -Abonnements](service-bus-dotnet-how-to-use-topics-subscriptions.md)
