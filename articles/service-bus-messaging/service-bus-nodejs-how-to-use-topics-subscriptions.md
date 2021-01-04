---
title: Verwenden der Vorschauversion des JavaScript-Pakets „azure/service-bus“ mit Themen und Abonnements
description: Enthält eine Beschreibung des JavaScript-Programms, in dem die aktuelle Vorschauversion des Pakets @azure/service-bus verwendet wird, um Nachrichten an ein Service Bus-Thema zu senden und Nachrichten eines Abonnements für das Thema zu empfangen.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: aac3b6339c318c76e9b0c9abd0bc3778f2563a6f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498692"
---
# <a name="quickstart-service-bus-topics-and-subscriptions-with-nodejs-and-the-preview-azureservice-bus-package"></a>Schnellstart: Service Bus-Themen und -Abonnements mit Node.js und dem Paket „azure/service-bus“ (Vorschauversion)
In diesem Tutorial wird beschrieben, wie Sie das Paket [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) in einem JavaScript-Programm verwenden, um Nachrichten an ein Service Bus-Thema zu senden und Nachrichten von einem Service Bus-Abonnement für das Thema zu empfangen.

## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure-Abonnement. Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF) registrieren.
- Befolgen Sie die Schritte im [Schnellstart: Erstellen eines Service Bus-Themas und eines oder mehrerer Abonnements dieses Themas im Azure-Portal](service-bus-quickstart-topics-subscriptions-portal.md). Notieren Sie sich die Verbindungszeichenfolge, den Themennamen und einen Abonnementnamen. Für diese Schnellstartanleitung verwenden Sie nur ein Abonnement. 

> [!NOTE]
> - Dieses Tutorial funktioniert für die Beispiele, die Sie mit [Node.js](https://nodejs.org/) kopieren und ausführen können. Anweisungen zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website](../app-service/quickstart-nodejs.md) oder [Node.js-Clouddienst mithilfe von Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Verwenden des Node-Paket-Managers (NPM) zum Installieren des Pakets
Öffnen Sie eine Eingabeaufforderung, die `npm` im Pfad enthält, ändern Sie das Verzeichnis in den Ordner, in dem die Beispiele gespeichert werden sollen, und führen Sie dann den folgenden Befehl aus, um das npm-Paket für Service Bus zu installieren.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Senden von Nachrichten an ein Thema
Mit dem folgenden Beispielcode wird veranschaulicht, wie Sie einen Batch mit Nachrichten an ein Service Bus-Thema senden. Weitere Einzelheiten finden Sie in den Codekommentaren. 

1. Öffnen Sie Ihren bevorzugten Editor, z. B. [Visual Studio Code](https://code.visualstudio.com/).
2. Erstellen Sie eine Datei namens `sendtotopic.js`, und fügen Sie den folgenden Code ein. Mit diesem Code wird eine Nachricht an Ihr Thema gesendet.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    
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
     
        // createSender() can also be used to create a sender for a queue.
        const sender = sbClient.createSender(topicName);
     
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
    
            // Send the last created batch of messages to the topic
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the topic: ${topicName}`);
                    
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
3. Ersetzen Sie `<SERVICE BUS NAMESPACE CONNECTION STRING>` durch die Verbindungszeichenfolge für Ihren Service Bus-Namespace.
1. Ersetzen Sie `<TOPIC NAME>` durch den Namen des Themas. 
1. Führen Sie anschließend den Befehl in einer Eingabeaufforderung aus, um diese Datei auszuführen.

    ```console
    node sendtotopic.js 
    ```
1. Die folgende Ausgabe wird angezeigt.

    ```console
    Sent a batch of messages to the topic: mytopic
    ```

## <a name="receive-messages-from-a-subscription"></a>Empfangen von Nachrichten aus einem Abonnement
1. Öffnen Sie Ihren bevorzugten Editor, z. B. [Visual Studio Code](https://code.visualstudio.com/).
2. Erstellen Sie eine Datei mit dem Namen **receivefromsubscription.js**, und fügen Sie darin den folgenden Code ein. Weitere Einzelheiten finden Sie in den Codekommentaren. 

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    const subscriptionName = "<SUBSCRIPTION NAME>";
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a queue.
        const receiver = sbClient.createReceiver(topicName, subscriptionName);
    
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
3. Ersetzen Sie `<SERVICE BUS NAMESPACE CONNECTION STRING>` durch die Verbindungszeichenfolge für den Namespace. 
1. Ersetzen Sie `<TOPIC NAME>` durch den Namen des Themas. 
1. Ersetzen Sie `<SUBSCRIPTION NAME>` durch den Namen des Abonnements für das Thema. 
1. Führen Sie anschließend den Befehl in einer Eingabeaufforderung aus, um diese Datei auszuführen.

    ```console
    node receivefromsubscription.js
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

Navigieren Sie im Azure-Portal zu Ihrem Service Bus-Namespace, und wählen Sie im unteren Bereich das Thema aus, um die Seite **Service Bus-Thema** für Ihr Thema anzuzeigen. Auf dieser Seite sollten im Diagramm **Nachrichten** drei ein- und drei ausgehende Nachrichten angezeigt werden. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Ein- und ausgehende Nachrichten":::

Wenn Sie beim nächsten Mal nur die App für das Senden ausführen, werden auf der Seite **Service Bus-Thema** sechs eingehende (drei davon neu), aber nur drei ausgehende Nachrichten angezeigt. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Aktualisierte Themenseite":::

Wenn Sie auf dieser Seite ein Abonnement auswählen, gelangen Sie zur Seite **Service Bus-Abonnement**. Auf dieser Seite werden die Anzahl aktiver Nachrichten, die Anzahl unzustellbarer Nachrichten und weitere Informationen angezeigt. In diesem Beispiel sind drei aktive Nachrichten vorhanden, die noch nicht von einem Empfänger empfangen wurden. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Anzahl aktiver Nachrichten":::

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in der folgenden Dokumentation bzw. unter den folgenden Beispielen: 

- [Azure Service Bus-Clientbibliothek für Python](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [Beispiele:](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples) Der Ordner **javascript** enthält JavaScript-Beispiele, und im Ordner **typescript** finden Sie TypeScript-Beispiele. 
- [azure-servicebus: Referenzdokumentation](/javascript/api/overview/azure/service-bus)