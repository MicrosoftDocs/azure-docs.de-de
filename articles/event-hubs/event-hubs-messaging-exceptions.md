---
title: 'Messagingausnahmen: Azure Event Hubs | Microsoft-Dokumentation'
description: Dieser Artikel stellt eine Liste von Azure Event Hubs-Messagingausnahmen und vorgeschlagenen Aktionen zur Verfügung.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 7b6323e02225be3d954e4ee91ea06952bb3ce396
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001763"
---
# <a name="event-hubs-messaging-exceptions"></a>Event Hubs-Messagingausnahmen

In diesem Artikel werden einige der Ausnahmen aufgeführt, die von der Azure Service Bus-Messaging-API-Bibliothek generiert werden, einschließlich .NET Framework-Event Hubs-APIs. Diese Referenz kann geändert werden. Prüfen Sie darum bei Bedarf, ob Aktualisierungen vorgenommen wurden.

## <a name="exception-categories"></a>Ausnahmekategorien

Die von den Event Hubs-APIs generierten Ausnahmen können – zusammen mit den zugehörigen Korrekturmaßnahmen – zu den folgenden Kategorien gehören.

1. Codierfehler des Benutzers: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Allgemeine Maßnahme: Korrigieren Sie den Code, bevor Sie fortfahren.
2. Fehler bei der Einrichtung oder Konfiguration: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Allgemeine Maßnahme: Überprüfen Sie die Konfiguration, und ändern Sie diese gegebenenfalls.
3. Vorübergehende Ausnahmen: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Allgemeine Maßnahme: Wiederholen Sie den Vorgang, oder benachrichtigen Sie die Benutzer.
4. Andere Ausnahmen: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Allgemeine Aktion: spezifisch für den Typ der Ausnahme; zu finden in der Tabelle im folgenden Abschnitt. 

## <a name="exception-types"></a>Ausnahmetypen
In der folgenden Tabelle werden die Typen von Messagingausnahmen, ihre Ursachen und Vorschläge für Korrekturmaßnahmen aufgelistet.

| Ausnahmetyp | Beschreibung/Ursache/Beispiele | Vorgeschlagene Maßnahme | Hinweis zur automatischen/sofortigen Wiederholung |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Der Server hat nicht innerhalb der von [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings)vorgegebenen Zeit auf den angeforderten Vorgang reagiert. Unter Umständen hat der Server den angeforderten Vorgang abgeschlossen. Diese Ausnahme kann aufgrund von Verzögerungen im Netzwerk oder in der Infrastruktur auftreten. |Überprüfen Sie den Systemzustand auf Konsistenz, und wiederholen Sie den Vorgang bei Bedarf.<br /> Siehe [TimeoutException](#timeoutexception). | In einigen Fällen kann eine Wiederholung helfen. Fügen Sie dem Code eine Wiederholungslogik hinzu. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Der angeforderte Benutzervorgang ist auf dem Server oder innerhalb des Diensts nicht zulässig. Sehen Sie sich die Details in der Ausnahmemeldung an. Beispielsweise generiert [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) diese Ausnahme, wenn die Meldung im [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) -Modus empfangen wurde. | Überprüfen Sie den Code und die Dokumentation. Stellen Sie sicher, dass der angeforderte Vorgang gültig ist. | Eine Wiederholung hilft nicht. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Es wurde versucht, einen Vorgang für ein Objekt aufzurufen, das bereits geschlossen, abgebrochen oder verworfen wurde. In seltenen Fällen wurde die Ambient-Transaktion bereits verworfen. | Überprüfen Sie den Code, und stellen Sie sicher, dass keine Vorgänge für verworfene Objekte aufgerufen werden. | Eine Wiederholung hilft nicht. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | Das [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) -Objekt konnte kein Token abrufen, das Token ist ungültig, oder das Token enthält nicht die Ansprüche, die zum Ausführen des Vorgangs erforderlich sind. | Stellen Sie sicher, dass der Tokenanbieter mit den richtigen Werten erstellt wird. Überprüfen Sie die Konfiguration für den Access Control Service. | In einigen Fällen kann eine Wiederholung helfen. Fügen Sie dem Code eine Wiederholungslogik hinzu. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Mindestens eines der für die Methode bereitgestellten Argumente ist ungültig. Der für [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) oder [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) bereitgestellte URI enthält Pfadsegmente. Das für [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) oder für [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) bereitgestellte URI-Schema ist ungültig. Der Eigenschaftswert ist größer als 32 KB. | Überprüfen Sie den aufrufenden Code, und stellen Sie sicher, dass die Argumente richtig sind. | Eine Wiederholung hilft nicht. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | Die dem Vorgang zugeordnete Entität ist nicht vorhanden oder wurde gelöscht. | Stellen Sie sicher, dass die Entität vorhanden ist. | Eine Wiederholung hilft nicht. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Der Client kann keine Verbindung mit Event Hub herstellen. |Stellen Sie sicher, dass der angegebene Hostname richtig und der Host erreichbar ist. | Eine Wiederholung kann helfen, wenn zeitweilige Verbindungsprobleme vorliegen. |
| [Microsoft.ServiceBus.Messaging ServerBusyException ](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Der Dienst kann die Anforderung derzeit nicht verarbeiten. | Der Client kann eine gewisse Zeit warten und dann den Vorgang wiederholen. <br /> Siehe [ServerBusyException](#serverbusyexception). | Der Client kann den Vorgang nach einer gewissen Zeitspanne wiederholen. Wenn die Wiederholung zu einer anderen Ausnahme führt, überprüfen Sie das Wiederholungsverhalten dieser Ausnahme. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Eine allgemeine Messagingausnahme, die in folgenden Fällen ausgelöst werden kann: Es wird versucht, ein [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient)-Element mit einem Namen oder einem Pfad zu erstellen, der zu einem anderen Entitätstyp gehört (z.B. zu einem Thema). Es wird versucht, eine Nachricht zu senden, die größer als 1 MB ist. Der Server oder der Dienst hat beim Verarbeiten der Anforderung einen Fehler festgestellt. Sehen Sie sich die Details in der Ausnahmemeldung an. Dies ist meist eine vorübergehende Ausnahme. | Überprüfen Sie den Code, und stellen Sie sicher, dass nur serialisierbare Objekte für den Nachrichtentext verwendet werden (oder verwenden Sie ein benutzerdefiniertes Serialisierungsprogramm). Überprüfen Sie die Dokumentation für die unterstützten Werttypen der Eigenschaften, und verwenden Sie nur unterstützte Typen. Überprüfen Sie die [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) -Eigenschaft. Wenn sie den Wert **True**aufweist, können Sie versuchen, den Vorgang zu wiederholen. | Das Verhalten einer Wiederholung ist nicht definiert, u. U. hilft sie nicht. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Es wurde versucht, eine Entität mit einem Namen zu erstellen, der bereits von einer anderen Entität in diesem Dienstnamespace verwendet wird. | Löschen Sie die vorhandene Entität, oder wählen Sie einen anderen Namen für die zu erstellende Entität. | Eine Wiederholung hilft nicht. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Die Messagingentität hat die maximal zulässige Größe erreicht. Diese Ausnahme kann auftreten, wenn bereits die maximale Anzahl von fünf Empfängern für eine bestimmte Consumergruppe geöffnet wurde. | Schaffen Sie Platz in der Entität, indem Sie Nachrichten aus der Entität oder ihren Unterwarteschlangen empfangen. <br /> Siehe [QuotaExceededException](#quotaexceededexception) | Eine Wiederholung kann helfen, wenn in der Zwischenzeit Nachrichten entfernt wurden. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Es wurde ein Laufzeitvorgang für eine deaktivierte Entität angefordert. |Aktivieren Sie die Entität. | Eine Wiederholung kann helfen, wenn die Entität in der Zwischenzeit aktiviert wurde. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Eine Nachrichtennutzlast überschreitet den Grenzwert von 1 MB. Dieser Grenzwert von 1 MB gilt für die Gesamtgröße der Nachricht, zu der auch Systemeigenschaften und .NET-Mehraufwand gehören. | Reduzieren Sie die Größe der Nachrichtennutzlast, und wiederholen Sie den Vorgang. |Eine Wiederholung hilft nicht. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) gibt an, dass das Kontingent für eine bestimmte Entität überschritten wurde.

Diese Ausnahme kann auftreten, wenn bereits die maximale Anzahl von fünf Empfängern für eine bestimmte Consumergruppe geöffnet wurde.

### <a name="event-hubs"></a>Event Hubs
Für Event Hubs gilt ein Limit von 20 Verbrauchergruppen pro Event Hub. Wenn Sie versuchen, weitere zu erstellen, erhalten Sie eine [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
Eine [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) zeigt an, dass ein von einem Benutzer initiierter Vorgang länger als das Timeout des Vorgangs dauert. 

Für Event Hubs wird das Zeitlimit entweder als Teil der Verbindungszeichenfolge oder über [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder)angegeben. Die Fehlermeldung selbst kann variieren, aber sie enthält immer den Timeoutwert für den aktuellen Vorgang. 

### <a name="common-causes"></a>Häufige Ursachen
Es gibt zwei häufige Ursachen für diesen Fehler: eine falsche Konfiguration oder ein vorübergehender Dienstfehler.

1. **Falsche Konfiguration:** Das Zeitlimit für den Vorgang ist möglicherweise zu klein für die Betriebsbedingung. Der Standardwert für das Zeitlimit für den Vorgang im Client-SDK beträgt 60 Sekunden. Überprüfen Sie, ob der Wert in Ihrem Code auf einen zu geringen Wert festgelegt wurde. Beachten Sie, dass sich Netzwerk- und CPU-Auslastung auf die Zeit auswirken können, die zum Abschließen eines bestimmten Vorgangs erforderlich ist. Daher sollte das Zeitlimit für den Vorgang nicht auf einen kleinen Wert festgelegt werden.
2. **Vorübergehender Dienstfehler:** Gelegentlich kann es beim Event Hubs-Dienst zu Verzögerungen bei der Verarbeitung von Anforderungen kommen, z.B. in Zeiten mit hohem Datenverkehr. In solchen Fällen können Sie den Vorgang nach einer kurzen Verzögerung so lange wiederholen, bis der Vorgang erfolgreich ist. Wenn der gleiche Vorgang auch nach mehreren Versuchen nicht erfolgreich ist, besuchen Sie die Website mit dem [Azure-Status](https://azure.microsoft.com/status/), um zu überprüfen, ob Dienstausfälle bekannt sind.

## <a name="serverbusyexception"></a>ServerBusyException

[Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) oder [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) gibt an, dass ein Server überlastet ist. Es gibt zwei relevante Fehlercodes für diese Ausnahme.

### <a name="error-code-50002"></a>Fehlercode 50002

Dieser Fehler kann aus zwei Gründen auftreten:

1. Die Last wird nicht gleichmäßig auf alle Partitionen des Event Hubs verteilt, und eine Partition erreicht die lokale Begrenzung für Durchsatzeinheiten.
    
    Lösung: Eine Überarbeitung der Strategie für die Partitionsverteilung oder die Verwendung von [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) behebt das Problem möglicherweise.

2. Der Event Hubs-Namespace enthält nicht genügend Durchsatzeinheiten. (Überprüfen Sie die Angabe im [Azure-Portal](https://portal.azure.com) im Fenster des Event Hubs-Namespace auf dem Bildschirm **Metriken**). Im Portal werden aggregierte Informationen für einen Zeitraum von einer Minute angezeigt. Wir messen den Durchsatz jedoch in Echtzeit, daher handelt es sich nur um eine Schätzung.

    Lösung: Die Erhöhung der Durchsatzeinheiten für den Namespace kann helfen. Diesen Vorgang können Sie im Portal im Fenster **Skalieren** des Bildschirms mit dem Event Hubs-Namespace ausführen. Sie können auch die [automatische Vergrößerung](event-hubs-auto-inflate.md) verwenden.

### <a name="error-code-50001"></a>Fehlercode 50001

Dieser Fehler sollte kaum auftreten. Er tritt auf, wenn der Container, der Code für Ihren Namespace ausführt, nicht über ausreichend CPU-Kapazität verfügt – wenige Sekunden vor Beginn des Event Hubs-Lastenausgleichs.

### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>Grenzwert für Aufrufe der GetRuntimeInformation-Methode
Azure Event Hubs unterstützt bis zu 50 Aufrufe der GetRuntimeInfo pro Sekunde. Sobald der Grenzwert erreicht wurde, erhalten Sie eventuell eine ähnliche Ausnahme wie die folgende:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Event Hubs finden Sie unter den folgenden Links:

* [Übersicht über Event Hubs](event-hubs-what-is-event-hubs.md)
* [Erstellen eines Event Hubs](event-hubs-create.md)
* [Event Hubs – häufig gestellte Fragen](event-hubs-faq.md)
