---
title: Verwenden von Azure Service Bus-Warteschlangen mit dem Python-Paket „azure/service-bus“ (Version 7.0.0)
description: In diesem Artikel erfahren Sie, wie Sie mithilfe von Python Nachrichten an Azure Service Bus-Warteschlangen senden und Nachrichten aus ihnen empfangen.
author: spelluru
documentationcenter: python
ms.devlang: python
ms.topic: quickstart
ms.date: 11/18/2020
ms.author: spelluru
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 7275e33e44c20ece6eb7d620e2c1e8032be41a7b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498658"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-python"></a>Senden und Empfangen von Nachrichten für Azure Service Bus-Warteschlangen (Python)
In diesem Artikel erfahren Sie, wie Sie mithilfe von Python Nachrichten an Azure Service Bus-Warteschlangen senden und Nachrichten aus ihnen empfangen. 

## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure-Abonnement. Sie können [Ihre Visual Studio-oder MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) oder [sich für ein kostenloses Konto anmelden](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Wenn Sie über keine Warteschlange verfügen, führen Sie die Schritte im Artikel [Schnellstart: Erstellen einer Service Bus-Warteschlange mithilfe des Azure-Portals](service-bus-quickstart-portal.md) aus, um eine Warteschlange zu erstellen. Notieren Sie sich die **Verbindungszeichenfolge** für Ihren Service Bus-Namespace sowie den Namen der **Warteschlange**, die Sie erstellt haben.
- Python (ab Version 2.7) mit installiertem Paket [Python Azure Service Bus](https://pypi.python.org/pypi/azure-servicebus). Weitere Informationen finden Sie im [Python-Installationshandbuch](/azure/developer/python/azure-sdk-install). 

## <a name="send-messages-to-a-queue"></a>Senden von Nachrichten an eine Warteschlange

1. Fügen Sie die folgende import-Anweisung hinzu: 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. Fügen Sie die folgenden Konstanten hinzu: 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    QUEUE_NAME = "<QUEUE NAME>"
    ```

    > [!IMPORTANT]
    > - Ersetzen Sie `<NAMESPACE CONNECTION STRING>` durch die Verbindungszeichenfolge für Ihren Service Bus-Namespace.
    > - Ersetzen Sie `<QUEUE NAME>` durch den Namen der Warteschlange. 
3. Fügen Sie eine Methode zum Senden einer einzelnen Nachricht hinzu:

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the queue
        sender.send_messages(message)
        print("Sent a single message")
    ```

    Der Absender ist ein Objekt, das als Client für die von Ihnen erstellte Warteschlange fungiert. Es wird später erstellt und als Argument an diese Funktion gesendet. 
4. Fügen Sie eine Methode zum Senden einer Liste mit Nachrichten hinzu:

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the queue
        sender.send_messages(messages)
        print("Sent a list of 5 messages")
    ```
5. Fügen Sie eine Methode zum Senden eines Batchs mit Nachrichten hinzu:

    ```python
    def send_batch_message(sender):
        # create a batch of messages
        batch_message = sender.create_message_batch()
        for _ in range(10):
            try:
                # add a message to the batch
                batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
            except ValueError:
                # ServiceBusMessageBatch object reaches max_size.
                # New ServiceBusMessageBatch object can be created here to send more data.
                break
        # send the batch of messages to the queue
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Erstellen Sie einen Service Bus-Client und dann ein Absenderobjekt für die Warteschlange, um Nachrichten senden zu können:

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Queue Sender object to send messages to the queue
        sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
        with sender:
            # send one message        
            send_single_message(sender)
            # send a list of messages
            send_a_list_of_messages(sender)
            # send a batch of messages
            send_batch_message(sender)
    
    print("Done sending messages")
    print("-----------------------")
    ```
 
## <a name="receive-messages-from-a-queue"></a>Empfangen von Nachrichten aus einer Warteschlange
Fügen Sie nach der print-Anweisung den folgenden Code hinzu. Mit diesem Code werden fortlaufend neue Nachrichten empfangen, bis fünf Sekunden lang (`max_wait_time`) keine neuen Nachrichten mehr empfangen werden. 

```python
with servicebus_client:
    # get the Queue Receiver object for the queue
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the queue
            receiver.complete_message(msg)
```

## <a name="full-code"></a>Vollständiger Code

```python
# import os
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
QUEUE_NAME = "<QUEUE NAME>"

def send_single_message(sender):
    message = ServiceBusMessage("Single Message")
    sender.send_messages(message)
    print("Sent a single message")

def send_a_list_of_messages(sender):
    messages = [ServiceBusMessage("Message in list") for _ in range(5)]
    sender.send_messages(messages)
    print("Sent a list of 5 messages")

def send_batch_message(sender):
    batch_message = sender.create_message_batch()
    for _ in range(10):
        try:
            batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
        except ValueError:
            # ServiceBusMessageBatch object reaches max_size.
            # New ServiceBusMessageBatch object can be created here to send more data.
            break
    sender.send_messages(batch_message)
    print("Sent a batch of 10 messages")

servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)

with servicebus_client:
    sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            receiver.complete_message(msg)
```

## <a name="run-the-app"></a>Ausführen der App
Wenn Sie die Anwendung ausführen, sollte die folgende Ausgabe angezeigt werden: 

```console
Sent a single message
Sent a list of 5 messages
Sent a batch of 10 messages
Done sending messages
-----------------------
Received: Single Message
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
```

Navigieren Sie im Azure-Portal zu Ihrem Service Bus-Namespace. Vergewissern Sie sich auf der Seite **Übersicht**, dass die Anzahl für die **eingehenden** und **ausgehenden** Nachrichten „16“ beträgt. Falls diese Zahlen nicht angezeigt werden, warten Sie einige Minuten, und aktualisieren Sie dann die Seite. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Anzahl ein- und ausgehender Nachrichten":::

Wählen Sie auf der Seite **Übersicht** die Warteschlange aus, um zur Seite **Service Bus-Warteschlange** zu navigieren. Auf dieser Seite wird auch die Anzahl **eingehender** und **ausgehender** Nachrichten angezeigt. Darüber hinaus werden noch weitere Informationen wie etwa die **aktuelle Größe** der Warteschlange und die **Anzahl aktiver Nachrichten** angezeigt. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/queue-details.png" alt-text="Warteschlangendetails":::


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in der folgenden Dokumentation bzw. unter den folgenden Beispielen: 

- [Azure Service Bus-Clientbibliothek für Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Beispiele:](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples) 
    - Der Ordner **sync_samples** enthält Beispiele zur synchronen Interaktion mit Service Bus. In dieser Schnellstartanleitung haben Sie diese Methode verwendet. 
    - Der Ordner **async_samples** enthält Beispiele zur asynchronen Interaktion mit Service Bus. 
- [azure-servicebus: Referenzdokumentation](/python/api/azure-servicebus/azure.servicebus?preserve-view=true&view=azure-python-preview)