---
title: Erkennung doppelter Nachrichten von Azure Service Bus | Microsoft-Dokumentation
description: Erkennen doppelter Nachrichten von Service Bus
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
ms.openlocfilehash: d9f814a49924ca95078f3b3decca4f3922c74c2b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65413661"
---
# <a name="duplicate-detection"></a>Duplikaterkennung

Tritt bei einer Anwendung unmittelbar nach dem Senden einer Nachricht ein schwerwiegender Fehler auf und geht die neu gestartete Anwendungsinstanz fälschlicherweise davon aus, dass die vorherige Nachrichtenübermittlung nicht stattgefunden hat, ist die Nachricht nach einem weiteren Sendevorgang zweimal im System enthalten.

Es ist auch möglich, dass kurz zuvor ein Fehler auf Client- oder Netzwerkebene aufgetreten ist, sodass eine gesendete Nachricht in die Warteschlange gestellt wird, ohne dass die Bestätigung erfolgreich an den Client zurückgesendet werden kann. In diesem Fall weiß der Client nicht sicher, welches Ergebnis der Sendevorgang hatte.

Durch die Erkennung von Duplikaten werden diese Situationen aufgelöst, indem der Absender die gleiche Nachricht erneut senden kann, während die Warteschlange oder das Thema mögliche Duplikate verwirft.

Das Aktivieren der Erkennung von Duplikaten unterstützt das Nachverfolgen der anwendungsgesteuerten *MessageId* aller in einem angegebenen Zeitfenster an eine Warteschlange oder ein Thema gesendeten Nachrichten. Wenn eine neue Nachricht mit einer *MessageId* gesendet wird, die während des Zeitfensters erfasst wurde, wird die Nachricht als akzeptiert gemeldet (der Sendevorgang war erfolgreich). Die neu gesendete Nachricht wird jedoch sofort ignoriert und verworfen. Es werden keine anderen Teile der Nachricht außer der *MessageId* ausgewertet.

Die Anwendungssteuerung der ID ist wichtig, da nur diese es der Anwendung erlaubt, die *MessageId* einem Geschäftsprozesskontext zuzuordnen, aus dem sie bei einem Fehler vorhersagbar rekonstruiert werden kann.

Für einen Geschäftsprozess, bei dem für die Behandlung von Anwendungskontext mehrere Nachrichten gesendet werden, setzt sich die *MessageId* möglicherweise aus dem Kontextbezeichner auf Anwendungsebene (z.B. einer Bestellnummer) und dem Betreff der Nachricht (z.B. **12345.2017/Bezahlung**) zusammen.

Die *MessageId* kann immer auch eine GUID sein, aber das Verankern des Bezeichners in den Geschäftsprozess sorgt für eine vorhersagbare Wiederholbarkeit, die für die effektive Erkennung von Duplikaten gewünscht wird.

> [!NOTE]
> Wenn Duplikaterkennung aktiviert ist und die Sitzungs-ID oder der Partitionsschlüssel nicht festgelegt ist, wird die Nachrichten-ID als Partitionsschlüssel verwendet. Wenn die Nachrichten-ID ebenfalls nicht festgelegt ist, generieren .NET- und AMQP-Bibliotheken automatisch eine Nachrichten-ID für die Nachricht. Weitere Informationen finden Sie unter [Verwenden von Partitionsschlüsseln](service-bus-partitioning.md#use-of-partition-keys).

## <a name="enable-duplicate-detection"></a>Aktivieren der Duplikaterkennung

Im Portal wird das Feature während der Erstellung der Entität mit dem Kontrollkästchen **Duplikaterkennung aktivieren** aktiviert, das standardmäßig deaktiviert ist. Die Einstellung für das Erstellen neuer Themen funktioniert genauso.

![][1]

> [!IMPORTANT]
> Sie können die Duplikaterkennung nach der Erstellung der Warteschlange nicht aktivieren/deaktivieren. Dies ist nur zum Zeitpunkt der Erstellung der Warteschlange möglich. 

Programmgesteuert legen Sie das Flag mit der [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection)-Eigenschaft im vollständigen Framework der .NET-API fest. Mit der Azure Resource Manager-API wird der Wert mit der [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values)-Eigenschaft festgelegt.

Die Zeit für den Verlauf der Duplikaterkennung beträgt für Warteschlangen und Themen standardmäßig 30 Sekunden. Der Höchstwert beträgt 7 Tage. Sie können diese Einstellung im Eigenschaftenfenster für Warteschlangen und Themen im Azure-Portal ändern.

![][2]

Programmgesteuert können Sie die Dauer der Duplikaterkennung, während der Nachrichten-IDs beibehalten werden, mithilfe der [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow)-Eigenschaft im vollständigen Framework der .NET-API konfigurieren. Mit der Azure Resource Manager-API wird der Wert mit der [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values)-Eigenschaft festgelegt.

Das Aktivieren der Duplikaterkennung und die Größe des Zeitfensters haben direkte Auswirkungen auf den Durchsatz von Warteschlangen (und Themen), da alle aufgezeichneten Nachrichten-IDs mit den neu übermittelten Nachrichten-IDs verglichen werden müssen.

Ein kürzeres Zeitfenster bedeutet, dass weniger Nachrichten-IDs beibehalten und verglichen werden müssen, sodass der Durchsatz weniger beeinträchtigt wird. Für Entitäten mit einem hohen Durchsatz, die eine Duplikaterkennung erfordern, sollten Sie das Zeitfenster so klein wie möglich halten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Service Bus-Messaging finden Sie in folgenden Themen:

* [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Verwenden von Service Bus-Themen und -Abonnements](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
