---
title: Verwenden von Azure Service Bus-Warteschlangen in JavaScript
description: Hier erfahren Sie, wie Sie ein JavaScript-Programm schreiben, in dem die aktuelle Vorschauversion des Pakets @azure/service-bus verwendet wird, um Nachrichten an eine Service Bus-Warteschlange zu senden und daraus zu empfangen.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: 3f37fcc6d58eb1c206aef6db15c7826cfdcda274
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489427"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-javascript"></a>Senden und Empfangen von Nachrichten für Azure Service Bus-Warteschlangen (JavaScript)
In diesem Tutorial erfahren Sie, wie Sie das Paket [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) in einem JavaScript-Programm verwenden, um Nachrichten an eine Service Bus-Warteschlange zu senden und daraus zu empfangen.

## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure-Abonnement. Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF) registrieren.
- Wenn Sie über keine Warteschlange verfügen, führen Sie die Schritte im Artikel [Schnellstart: Erstellen einer Service Bus-Warteschlange mithilfe des Azure-Portals](service-bus-quickstart-portal.md) aus, um eine Warteschlange zu erstellen. Notieren Sie sich die **Verbindungszeichenfolge** für Ihren Service Bus-Namespace sowie den Namen der **Warteschlange**, die Sie erstellt haben.

> [!NOTE]
> - Dieses Tutorial funktioniert für die Beispiele, die Sie mit [Node.js](https://nodejs.org/) kopieren und ausführen können. Anweisungen zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen einer Node.js-Web-App in Azure](../app-service/quickstart-nodejs.md) und [Erstellen und Bereitstellen einer Node.js-Anwendung in einem Azure-Clouddienst](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Verwenden des Node-Paket-Managers (NPM) zum Installieren des Pakets
Öffnen Sie eine Eingabeaufforderung, die `npm` im Pfad enthält, ändern Sie das Verzeichnis in den Ordner, in dem die Beispiele gespeichert werden sollen, und führen Sie dann den folgenden Befehl aus, um das npm-Paket für Service Bus zu installieren.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Senden von Nachrichten an eine Warteschlange
Im folgenden Beispielcode wird das Senden einer Nachricht an eine Warteschlange veranschaulicht.

1. Öffnen Sie Ihren bevorzugten Editor, z. B. [Visual Studio Code](https://code.visualstudio.com/).
2. Erstellen Sie eine Datei namens `send.js`, und fügen Sie den folgenden Code ein. Mit diesem Code wird eine Nachricht an Ihre Warteschlange gesendet. Die Nachricht setzt sich aus einer Bezeichnung (Scientist) und Text (Einstein) zusammen.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"
    
    const messages = [
        { body: "Albert Einstein" },
        { body: "Werner Heisenberg" },
        { body: "Marie Curie" },
        { body: "Steven Hawking" },
        { body: "Isaac Newton" },
        { body: "Niels Bohr" },
        { body: "Michael Faraday" },
        { body: "Galileo Galilei" },
        { body: "Johannes Kepler" },
        { body: "Nikolaus Kopernikus" }
     ];
    
    async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createSender() can also be used to create a sender for a topic.
        const sender = sbClient.createSender(queueName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the arry         
    
                // try to add the message to the batch
                if (!batch.tryAddMessage(messages[i])) {            
                    // if it fails to add the message to the current batch
                    // send the current batch as it is full
                    await sender.sendMessages(batch);
    
                    // then, create a new batch 
                    batch = await sender.createBatch();
     
                    // now, add the message failed to be added to the previous batch to this batch
                    if (!batch.tryAddMessage(messages[i])) {
                        // if it still can't be added to the batch, the message is probably too big to fit in a batch
                        throw new Error("Message too big to fit in a batch");
                    }
                }
            }
    
            // Send the last created batch of messages to the queue
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the queue: ${queueName}`);
                    
            // Close the sender
            await sender.close();
        } finally {
            await sbClient.close();
        }
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });
    ```
3. Ersetzen Sie `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` durch die Verbindungszeichenfolge für Ihren Service Bus-Namespace.
1. Ersetzen Sie `<QUEUE NAME>` durch den Namen der Warteschlange. 
1. Führen Sie den Befehl anschließend an einer Eingabeaufforderung aus, um diese Datei auszuführen.

    ```console
    node send.js 
    ```
1. Die folgende Ausgabe wird angezeigt.

    ```console
    Sent a batch of messages to the queue: myqueue
    ```

## <a name="receive-messages-from-a-queue"></a>Empfangen von Nachrichten aus einer Warteschlange

1. Öffnen Sie Ihren bevorzugten Editor, z. B. [Visual Studio Code](https://code.visualstudio.com/).
2. Erstellen Sie eine Datei namens `receive.js`, und fügen Sie den folgenden Code in die Datei ein.

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");

    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"

     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a subscription.
        const receiver = sbClient.createReceiver(queueName);
    
        // function to handle messages
        const myMessageHandler = async (messageReceived) => {
            console.log(`Received message: ${messageReceived.body}`);
        };
    
        // function to handle any errors
        const myErrorHandler = async (error) => {
            console.log(error);
        };
    
        // subscribe and specify the message and error handlers
        receiver.subscribe({
            processMessage: myMessageHandler,
            processError: myErrorHandler
        });
    
        // Waiting long enough before closing the sender to send messages
        await delay(5000);
    
        await receiver.close(); 
        await sbClient.close();
    }    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });
    ```
3. Ersetzen Sie `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` durch die Verbindungszeichenfolge für Ihren Service Bus-Namespace.
1. Ersetzen Sie `<QUEUE NAME>` durch den Namen der Warteschlange. 
1. Führen Sie den Befehl anschließend an einer Eingabeaufforderung aus, um diese Datei auszuführen.

    ```console
    node receive.js
    ```
1. Die folgende Ausgabe wird angezeigt.

    ```console
    Received message: Albert Einstein
    Received message: Werner Heisenberg
    Received message: Marie Curie
    Received message: Steven Hawking
    Received message: Isaac Newton
    Received message: Niels Bohr
    Received message: Michael Faraday
    Received message: Galileo Galilei
    Received message: Johannes Kepler
    Received message: Nikolaus Kopernikus
    ```

Im Azure-Portal wird auf der Seite **Übersicht** für den Service Bus-Namespace die Anzahl **eingehender** und **ausgehender** Nachrichten angezeigt. Unter Umständen müssen Sie ca. eine Minute warten und die Seite dann aktualisieren, damit die neuesten Werte angezeigt werden. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Anzahl ein- und ausgehender Nachrichten":::

Wählen Sie auf der Seite **Übersicht** die Warteschlange aus, um zur Seite **Service Bus-Warteschlange** zu navigieren. Auf dieser Seite wird auch die Anzahl **eingehender** und **ausgehender** Nachrichten angezeigt. Darüber hinaus werden noch weitere Informationen wie etwa die **aktuelle Größe** der Warteschlange, die **maximale Größe** und die **Anzahl aktiver Nachrichten** angezeigt. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Warteschlangendetails":::
## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in der folgenden Dokumentation bzw. unter den folgenden Beispielen: 

- [Azure Service Bus-Clientbibliothek für Python](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [Beispiele:](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples) Der Ordner **javascript** enthält JavaScript-Beispiele, und im Ordner **typescript** finden Sie TypeScript-Beispiele. 
- [azure-servicebus: Referenzdokumentation](/javascript/api/overview/azure/service-bus)