---
title: Verwenden von Azure Service Bus-Themen und -Abonnements mit dem Python-Paket „azure/service-bus“ (Version 7.0.0)
description: In diesem Artikel wird veranschaulicht, wie Sie Python zum Senden von Nachrichten an ein Thema und Empfangen von Nachrichten vom Abonnement verwenden.
documentationcenter: python
author: spelluru
ms.devlang: python
ms.topic: quickstart
ms.date: 11/18/2020
ms.author: spelluru
ms.custom: devx-track-python
ms.openlocfilehash: 43f633e427e20cfb0b044bd42b77f866e4cc0c61
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489410"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-python"></a>Senden von Nachrichten an ein Azure Service Bus-Thema und Empfangen von Nachrichten von Abonnements für das Thema (Python)
In diesem Artikel wird veranschaulicht, wie Sie Python zum Senden von Nachrichten an ein Service Bus-Thema und Empfangen von Nachrichten von einem Abonnement für das Thema verwenden. 

## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure-Abonnement. Sie können [Ihre Visual Studio-oder MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) oder [sich für ein kostenloses Konto anmelden](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Befolgen Sie die Schritte im [Schnellstart: Erstellen eines Service Bus-Themas und eines oder mehrerer Abonnements dieses Themas im Azure-Portal](service-bus-quickstart-topics-subscriptions-portal.md). Notieren Sie sich die Verbindungszeichenfolge, den Themennamen und einen Abonnementnamen. Für diese Schnellstartanleitung verwenden Sie nur ein Abonnement. 
- Python 2.7 oder höher mit installiertem [Azure Python SDK][Azure Python-Paket]. Weitere Informationen finden Sie im [Python-Installationshandbuch](/azure/developer/python/azure-sdk-install).

## <a name="send-messages-to-a-topic"></a>Senden von Nachrichten an ein Thema

1. Fügen Sie die folgende import-Anweisung hinzu. 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. Fügen Sie die folgenden Konstanten hinzu. 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    TOPIC_NAME = "<TOPIC NAME>"
    SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"
    ```
    
    > [!IMPORTANT]
    > - Ersetzen Sie `<NAMESPACE CONNECTION STRING>` durch die Verbindungszeichenfolge für Ihren Namespace.
    > - Ersetzen Sie `<TOPIC NAME>` durch den Namen des Themas.
    > - Ersetzen Sie `<SUBSCRIPTION NAME>` durch den Namen des Abonnements für das Thema. 
3. Fügen Sie eine Methode zum Senden einer einzelnen Nachricht hinzu.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the topic
        sender.send_messages(message)
        print("Sent a single message")
    ```

    Der Absender ist ein Objekt, das als Client für das von Ihnen erstellte Thema fungiert. Sie erstellen es später und senden es als Argument an diese Funktion. 
4. Fügen Sie eine Methode zum Senden einer Liste mit Nachrichten hinzu.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the topic
        sender.send_messages(messages)
        print("Sent a list of 5 messages")
    ```
5. Fügen Sie eine Methode zum Senden eines Batchs mit Nachrichten hinzu.

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
        # send the batch of messages to the topic
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Erstellen Sie einen Service Bus-Client und dann ein Absenderobjekt für das Thema, um Nachrichten senden zu können.

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Topic Sender object to send messages to the topic
        sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
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
 
## <a name="receive-messages-from-a-subscription"></a>Empfangen von Nachrichten aus einem Abonnement
Fügen Sie nach der print-Anweisung den folgenden Code hinzu. Mit diesem Code werden fortlaufend neue Nachrichten empfangen, bis fünf Sekunden lang (`max_wait_time`) keine neuen Nachrichten mehr empfangen werden. 

```python
with servicebus_client:
    # get the Subscription Receiver object for the subscription    
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the subscription
            receiver.complete_message(msg)
```

## <a name="full-code"></a>Vollständiger Code

```python
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
TOPIC_NAME = "<TOPIC NAME>"
SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"

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
    sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
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

Navigieren Sie im Azure-Portal zu Ihrem Service Bus-Namespace. Vergewissern Sie sich auf der Seite **Übersicht**, dass die Anzahl für die **eingehenden** und **ausgehenden** Nachrichten „16“ beträgt. Falls diese Zahlen nicht angezeigt werden, sollten Sie die Seite nach einigen Minuten aktualisieren. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Anzahl ein- und ausgehender Nachrichten":::

Wählen Sie im unteren Bereich das Thema aus, um die Seite **Service Bus-Thema** für Ihr Thema anzuzeigen. Auf dieser Seite sollten im Diagramm **Nachrichten** drei ein- und drei ausgehende Nachrichten angezeigt werden. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Ein- und ausgehende Nachrichten":::

Wenn Sie auf dieser Seite ein Abonnement auswählen, gelangen Sie zur Seite **Service Bus-Abonnement**. Auf dieser Seite werden die Anzahl aktiver Nachrichten, die Anzahl unzustellbarer Nachrichten und weitere Informationen angezeigt. In diesem Beispiel wurden alle Nachrichten empfangen, sodass die Anzahl aktiver Nachrichten „0“ ist. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Anzahl aktiver Nachrichten":::

Wenn Sie den Code für den Empfang auskommentieren, wird „16“ als Anzahl aktiver Nachrichten angezeigt. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count-2.png" alt-text="Anzahl aktiver Nachrichten: Ohne Empfangscode":::

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in der folgenden Dokumentation bzw. unter den folgenden Beispielen: 

- [Azure Service Bus-Clientbibliothek für Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Beispiele:](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples) 
    - Der Ordner **sync_samples** enthält Beispiele zur synchronen Interaktion mit Service Bus. In dieser Schnellstartanleitung haben Sie diese Methode verwendet. 
    - Der Ordner **async_samples** enthält Beispiele zur asynchronen Interaktion mit Service Bus. 
- [azure-servicebus: Referenzdokumentation](/python/api/azure-servicebus/azure.servicebus?preserve-view=true&view=azure-python-preview)