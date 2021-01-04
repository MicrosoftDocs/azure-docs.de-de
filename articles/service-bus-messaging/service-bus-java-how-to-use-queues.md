---
title: Verwenden von Azure Service Bus-Warteschlangen mit Java (azure-messaging-servicebus)
description: In diesem Tutorial wird beschrieben, wie Sie Java verwenden, um Nachrichten an eine Azure Service Bus-Warteschlange zu senden und Antworten zu empfangen. Sie verwenden das neue Paket „azure-messaging-servicebus“.
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 4eae72b38d36348a6bdc98d3cbd98a5a712b3161
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97399297"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Senden und Empfangen von Nachrichten für Azure Service Bus-Warteschlangen (Java)
In dieser Schnellstartanleitung erstellen Sie eine Java-App zum Senden und Empfangen von Nachrichten für eine Azure Service Bus-Warteschlange. 

> [!IMPORTANT]
> In dieser Schnellstartanleitung wird das neue Paket „azure-messaging-servicebus“ verwendet. Eine Schnellstartanleitung mit dem alten Paket „azure-servicebus“ finden Sie im Artikel zum [Senden und Empfangen von Nachrichten mit „azure-servicebus“](service-bus-java-how-to-use-queues-legacy.md).


## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure-Abonnement. Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF) registrieren.
- Wenn Sie über keine Warteschlange verfügen, führen Sie die Schritte im Artikel [Schnellstart: Erstellen einer Service Bus-Warteschlange mithilfe des Azure-Portals](service-bus-quickstart-portal.md) aus, um eine Warteschlange zu erstellen. Notieren Sie sich die **Verbindungszeichenfolge** für Ihren Service Bus-Namespace und den Namen der **Warteschlange**, die Sie erstellt haben.
- Installieren Sie das [Azure SDK für Java][Azure SDK for Java]. Wenn Sie Eclipse verwenden, können Sie das [Azure-Toolkit für Eclipse][Azure Toolkit for Eclipse] installieren, in dem das Azure SDK für Java enthalten ist. Sie können dann Ihrem Projekt die **Microsoft Azure-Bibliotheken für Java** hinzufügen. Lesen Sie bei Verwendung von IntelliJ die Informationen unter [Installieren des Azure-Toolkits für IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-queue"></a>Senden von Nachrichten an eine Warteschlange
In diesem Abschnitt erstellen Sie ein Java-Konsolenprojekt und fügen Code zum Senden von Nachrichten an die von Ihnen weiter oben erstellte Warteschlange hinzu. 

### <a name="create-a-java-console-project"></a>Erstellen eines Java-Konsolenprojekts
Erstellen Sie ein Java-Projekt mit Eclipse oder einem Tool Ihrer Wahl. 

### <a name="configure-your-application-to-use-service-bus"></a>Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus
Fügen Sie einen Verweis auf die Azure Service Bus-Bibliothek hinzu. Die Java-Clientbibliothek für Service Bus steht über das [zentrale Maven-Repository](https://search.maven.org/search?q=a:azure-messaging-servicebus) zur Verfügung. Sie können über die folgende Abhängigkeitsdeklaration in Ihrer Maven-Projektdatei auf diese Bibliothek verweisen:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-queue"></a>Hinzufügen von Code für das Senden von Nachrichten an die Warteschlange
1. Fügen Sie die folgenden `import`-Anweisungen für das Thema der Java-Datei hinzu. 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. Definieren Sie in der Klasse Variablen für die Verbindungszeichenfolge und den Warteschlangennamen wie unten dargestellt: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    Ersetzen Sie `<NAMESPACE CONNECTION STRING>` durch die Verbindungszeichenfolge für Ihren Service Bus-Namespace. Ersetzen Sie außerdem `<QUEUE NAME>` durch den Namen der Warteschlange.
3. Fügen Sie in der Klasse eine Methode mit dem Namen `sendMessage` zum Senden einer Nachricht an die Warteschlange hinzu. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();
        
        // send one message to the queue
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the queue: " + queueName);        
    }
    ```
1. Fügen Sie in der Klasse eine Methode mit dem Namen `createMessages` zum Erstellen einer Liste mit Nachrichten hinzu. Normalerweise erhalten Sie diese Nachrichten von den verschiedenen Teilen Ihrer Anwendung. Hier erstellen wir eine Liste mit Beispielnachrichten.

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. Fügen Sie eine Methode mit dem Namen `sendMessageBatch` zum Senden von Nachrichten an die von Ihnen erstellte Warteschlange hinzu. Mit dieser Methode wird ein `ServiceBusSenderClient` für die Warteschlange erstellt, die `createMessages`-Methode zum Abrufen der Liste mit den Nachrichten aufgerufen und mindestens ein Batch vorbereitet. Anschließend werden die Batches an die Warteschlange gesendet. 

```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }
        
        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
        }

        //close the client
        senderClient.close();
    }
```

## <a name="receive-messages-from-a-queue"></a>Empfangen von Nachrichten aus einer Warteschlange
In diesem Abschnitt fügen Sie Code hinzu, mit dem Nachrichten aus der Warteschlange abgerufen werden. 

1. Fügen Sie eine Methode mit dem Namen `receiveMessages` hinzu, um Nachrichten von der Warteschlange zu empfangen. Mit dieser Methode wird ein `ServiceBusProcessorClient`-Element für die Warteschlange erstellt, indem ein Handler für die Verarbeitung von Nachrichten und ein anderer für die Verarbeitung von Fehlern angegeben wird. Anschließend wird der Prozessor gestartet, und es wird einige Sekunden lang abgewartet. Die empfangenen Nachrichten werden ausgegeben, und dann wird der Prozessor angehalten und geschlossen.

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString());
        };

        // handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
            .processMessage(messageProcessor)
            .processError(errorHandler)
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }    
    ```
2. Aktualisieren Sie die `main`-Methode, um die Methoden `sendMessage`, `sendMessageBatch` und `receiveMessages` aufzurufen und `InterruptedException` auszulösen.     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Ausführen der App
Wenn Sie die Anwendung ausführen, werden im Konsolenfenster die folgenden Meldungen angezeigt. 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Starting the processor
Received message: Hello, World!
Received message: First message in the batch
Received message: Second message in the batch
Received message: Three message in the batch
Stopping and closing the processor
```

Auf der Seite **Übersicht** für den Service Bus-Namespace im Azure-Portal wird die Anzahl **eingehender** und **ausgehender** Nachrichten angezeigt. Unter Umständen müssen Sie ca. eine Minute lang warten und die Seite dann aktualisieren, damit die neuesten Werte angezeigt werden. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Anzahl ein- und ausgehender Nachrichten" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Wählen Sie die Warteschlange auf der Seite **Übersicht** aus, um zur Seite **Service Bus-Warteschlange** zu navigieren. Auf dieser Seite wird auch die Anzahl **eingehender** und **ausgehender** Nachrichten angezeigt. Darüber hinaus werden noch weitere Informationen angezeigt, z. B. die **aktuelle Größe** der Warteschlange, die **maximale Größe**, die **Anzahl aktiver Nachrichten** usw. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Warteschlangendetails" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in der folgenden Dokumentation bzw. unter den folgenden Beispielen:

- [Azure Service Bus-Clientbibliothek für Java: Infodatei](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Beispiele bei GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/servicebus/azure-messaging-servicebus/src/samples)
- [Java-API-Referenz](/java/api/overview/azure/servicebus?preserve-view=true&view=azure-java-stable)

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
