---
title: Übersicht über die Transaktionsverarbeitung in Azure Service Bus
description: In diesem Artikel erhalten Sie eine Übersicht über die Transaktionsverarbeitung und die Funktion „Senden über“ in Azure Service Bus.
ms.topic: article
ms.date: 10/28/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 9162b8578fe4f48cc3740b38d9d84ffaa2f260de
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023600"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Übersicht über die Service Bus-Transaktionsverarbeitung

Dieser Artikel beschreibt die Transaktionsfunktionen von Microsoft Azure Service Bus. Ein Großteil der Diskussion wird in dem Beispiel [AMQP Transactions with Service Bus sample](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia) (Beispiel: AMQP-Transaktionen mit Service Bus) veranschaulicht. Dieser Artikel bietet lediglich eine Übersicht über die Transaktionsverarbeitung in Service Bus und die Service Bus-Funktion *send via* (Senden über), während das Beispiel zu den atomaren Transaktionen umfangreicher und komplexer ist.

## <a name="transactions-in-service-bus"></a>Transaktionen in Service Bus

Eine *Transaktion* gruppiert zwei oder mehr Vorgänge in einem *Ausführungsbereich*. Solch eine Transaktion muss von sich aus sicherstellen, dass alle Vorgänge, die zu einer bestimmten Gruppe von Vorgängen gehören, entweder gemeinsam gelingen oder gemeinsam fehlschlagen. In dieser Hinsicht agieren Transaktionen als eine Einheit. Dieses Verhalten wird häufig als *Unteilbarkeit* bezeichnet.

Service Bus ist ein Transaktionsnachrichtenbroker und gewährleistet die Transaktionsintegrität für alle internen Vorgänge in seinen Nachrichtenspeichern. Alle Nachrichtenübertragungen innerhalb von Service Bus wie das Verschieben von Nachrichten in eine [Warteschlange für unzustellbare Nachrichten](service-bus-dead-letter-queues.md) oder die [automatische Weiterleitung](service-bus-auto-forwarding.md) von Nachrichten zwischen Entitäten werden als Transaktionen bezeichnet. Wenn Service Bus also eine Meldung akzeptiert, wurde diese bereits gespeichert und mit einer Sequenznummer versehen. Alle darauf folgenden Nachrichtenübermittlungen innerhalb von Service Bus sind koordinierte Vorgänge zwischen Entitäten und führen weder zum Verlust (Quelle erfolgreich, Ziel schlägt fehl) noch zur Duplizierung (Quelle schlägt fehl, Ziel erfolgreich) der Nachricht.

Service Bus unterstützt Gruppierungsvorgänge für eine einzelne Nachrichtenentität (Warteschlange, Thema, Abonnement) innerhalb eines Transaktionsbereichs. Sie können beispielsweise mehrere Nachrichten aus einem Transaktionsbereich an eine Warteschlange senden. Die Nachrichten werden allerdings erst im Warteschlangenprotokoll committet, wenn die Transaktion erfolgreich abgeschlossen wurde.

## <a name="operations-within-a-transaction-scope"></a>Vorgänge innerhalb eines Transaktionsbereichs

Die innerhalb eines Transaktionsbereichs ausführbaren Vorgänge sind:

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)** : `Send`, `SendAsync`, `SendBatch`, `SendBatchAsync`
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)** : `Complete`, `CompleteAsync`, `Abandon`, `AbandonAsync`, `Deadletter`, `DeadletterAsync`, `Defer`, `DeferAsync`, `RenewLock`, `RenewLockAsync` 

Empfangsvorgänge sind nicht enthalten, da davon ausgegangen wird, dass die Anwendung Nachrichten mithilfe des Modus [ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) in einer Empfangsschleife oder über einen [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage)-Rückruf erhält. Erst dann öffnet die Anwendung einen Transaktionsbereich für die Verarbeitung der Nachricht.

Die Disposition der Nachricht (vollständig, verworfen, unzustellbar, zurückgestellt) tritt innerhalb des Bereichs und abhängig von dem Gesamtergebnis der Transaktion auf.

## <a name="transfers-and-send-via"></a>Übertragungen und „send via“

Service Bus unterstützt *Übertragungen*, um transaktionsbasierte Übergaben von Daten aus einer Warteschlange oder einem Thema an einen Prozessor und von dort an eine andere Warteschlange oder ein anderes Thema zu ermöglichen. Bei einem Übertragungsvorgang sendet ein Sender eine Nachricht zuerst an eine *Übertragungswarteschlange oder ein Übertragungsthema*. Die Übertragungswarteschlange bzw. das Übertragungsthema verschiebt die Nachricht sofort an die gewünschte Zielwarteschlange bzw. das gewünschte Zielthema und verwendet dazu dieselbe stabile Übertragungsimplementierung, auf der auch die automatische Weiterleitung basiert. Die Nachricht wird an das Protokoll der Übertragungswarteschlange oder des Themas niemals in einer Weise committet, dass sie für die Consumer der Übertragungswarteschlangebzw. des Themas sichtbar wird.

Wenn die Übertragungswarteschlange oder das Übertragungsthema selbst die Quelle der eingehenden Nachrichten des Absenders ist, offenbart sich die Leistungsfähigkeit dieser Transaktionsfunktion. Anders gesagt: Service Bus kann die Nachricht über die Übertragungswarteschlange bzw. das Übertragungsthema an die Zielwarteschlange bzw. das Zielthema übermitteln. Gleichzeitig führt Service Bus einen vollständigen (oder einen zurückgestellten oder unzustellbaren) Vorgang für die Eingabenachricht aus. All dies erfolgt in einem einzigen atomaren Vorgang. 

### <a name="see-it-in-code"></a>Codebeispiel

Erstellen Sie einen Nachrichtenabsender, der sich über die Übertragungswarteschlange an die Zielwarteschlange richtet, um solche Übertragungen einzurichten. Sie verfügen außerdem über einen Empfänger, der Nachrichten aus derselben Warteschlange abruft. Beispiel:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

Eine einfache Transaktion verwendet diese Elemente anschließend wie im folgenden Beispiel. Um das vollständige Beispiel zu sehen, sehen Sie sich den [Quellcode auf GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia) an:

```csharp
var receivedMessage = await receiver.ReceiveAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    try
    {
        // do some processing
        if (receivedMessage != null)
            await receiver.CompleteAsync(receivedMessage.SystemProperties.LockToken);

        var myMsgBody = new MyMessage
        {
            Name = "Some name",
            Address = "Some street address",
            ZipCode = "Some zip code"
        };

        // send message
        var message = myMsgBody.AsMessage();
        await sender.SendAsync(message).ConfigureAwait(false);
        Console.WriteLine("Message has been sent");

        // complete the transaction
        ts.Complete();
    }
    catch (Exception ex)
    {
        // This rolls back send and complete in case an exception happens
        ts.Dispose();
        Console.WriteLine(ex.ToString());
    }
}
```

## <a name="timeout"></a>Timeout
Eine Transaktion weist nach zwei Minuten einen Timeout auf. Der Transaktionszeitgeber startet, wenn der erste Vorgang in der Transaktion gestartet wird. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Service Bus-Warteschlangen finden Sie in den folgenden Artikeln:

* [Verwenden von Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Verketten von Service Bus-Entitäten mit automatischer Weiterleitung](service-bus-auto-forwarding.md)
* [Beispiel zur automatischen Weiterleitung](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Atomic Transactions with Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions) (Atomare Transaktionen mit Service Bus)
* [Vergleich von Azure-Warteschlangen und Service Bus-Warteschlangen](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


