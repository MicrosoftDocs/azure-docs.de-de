---
title: Verwenden von Azure Service Bus-Warteschlangen in Node.js – azure/service-bus | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Verwendung von Service Bus-Warteschlangen in Azure aus einer Node.js-App.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 7aacefde9c037fcce64d9256e35082eb04e0a2f3
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988348"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Verwenden von Service Bus-Warteschlangen mit Node.js und dem Paket „azure/service-bus“
> [!div class="op_multi_selector" title1="Programming language" title2="Node.js pacakge"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

In diesem Tutorial erfahren Sie, wie Sie ein Node.js-Programm schreiben, um Nachrichten mithilfe des neuen [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)-Pakets an eine Service Bus-Warteschlange zu senden und Antworten zu empfangen. Dieses Paket verwendet das schnellere [Advance Message Queueing Protocol 1.0](service-bus-amqp-overview.md). Im Gegensatz dazu hat das ältere Paket [azure-sb](https://www.npmjs.com/package/azure-sb) die [Service Bus-REST-Laufzeit-APIs](/rest/api/servicebus/service-bus-runtime-rest) verwendet. Die Beispiele sind in JavaScript geschrieben.

## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure-Abonnement. Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF) registrieren.
- Wenn Sie über keine Warteschlange verfügen, führen Sie die Schritte im Artikel [Schnellstart: Erstellen einer Service Bus-Warteschlange mithilfe des Azure-Portals](service-bus-quickstart-portal.md) aus, um eine Warteschlange zu erstellen. Notieren Sie sich die Verbindungszeichenfolge für Ihre Service Bus-Instanz und den Namen der Warteschlange, die Sie erstellt haben. Diese Werte benötigen Sie für die Beispiele.

> [!NOTE]
> - Dieses Tutorial funktioniert für die Beispiele, die Sie mit [Node.js](https://nodejs.org/) kopieren und ausführen können. Anweisungen zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen einer Node.js-Web-App in Azure](../app-service/app-service-web-get-started-nodejs.md) und [Erstellen und Bereitstellen einer Node.js-Anwendung in einem Azure-Clouddienst](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Das neue [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)-Paket unterstützt die Erstellung von Warteschlangen noch nicht. Wenn Sie Warteschlangen programmgesteuert erstellen möchten, verwenden Sie das [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus)-Paket.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Verwenden des Node-Paket-Managers (NPM) zum Installieren des Pakets
Öffnen Sie eine Eingabeaufforderung, die `npm` im Pfad enthält, ändern Sie das Verzeichnis in den Ordner, in dem die Beispiele gespeichert werden sollen, und führen Sie dann den folgenden Befehl aus, um das npm-Paket für Service Bus zu installieren.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Senden von Nachrichten an eine Warteschlange
Die Interaktion mit einer Service Bus-Warteschlange beginnt damit, die [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient)-Klasse zu instanziieren und diese zum Instanziieren der [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient)-Klasse zu verwenden. Sobald Sie über den Warteschlangenclient verfügen, können Sie einen Sender erstellen und mit diesem die Methoden [send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) oder [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) zum Senden von Nachrichten verwenden.

1. Öffnen Sie Ihren bevorzugten Editor, z. B. [Visual Studio Code](https://code.visualstudio.com/).
2. Erstellen Sie eine Datei namens `send.js`, und fügen Sie den folgenden Code ein. Dieser Code sendet 10 Nachrichten an Ihre Warteschlange.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
      try {
        for (let i = 0; i < 10; i++) {
          const message= {
            body: `Hello world! ${i}`,
            label: `test`,
            userProperties: {
                myCustomPropertyName: `my custom property value ${i}`
           }
          };
          console.log(`Sending message: ${message.body}`);
          await sender.send(message);
        }
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Geben Sie die Verbindungszeichenfolge und den Namen Ihrer Warteschlange in den obigen Code ein.
4. Führen Sie anschließend den Befehl `node send.js` in einer Eingabeaufforderung aus, um diese Datei auszuführen.

Glückwunsch! Sie haben soeben Nachrichten an eine Service Bus-Warteschlange gesendet.

Nachrichten verfügen über einige Standardeigenschaften wie `label` und `messageId`, die Sie beim Senden festlegen können. Wenn Sie benutzerdefinierte Eigenschaften festlegen möchten, verwenden Sie `userProperties`. Dies ist ein JSON-Objekt, das Schlüssel-Wert-Paare Ihrer benutzerdefinierten Daten enthalten kann.

Service Bus-Warteschlangen unterstützen eine maximale Nachrichtengröße von 256 KB für den [Standard-Tarif](service-bus-premium-messaging.md) und 1 MB für den [Premium-Tarif](service-bus-premium-messaging.md). Bei der Anzahl der Nachrichten, die in einer Warteschlange enthalten sein können, besteht keine Beschränkung. Allerdings gilt eine Obergrenze bei der Gesamtgröße der in einer Warteschlange enthaltenen Nachrichten. Die Warteschlangengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB. Weitere Informationen zu Kontingenten finden Sie unter [Service Bus-Kontingente](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Empfangen von Nachrichten aus einer Warteschlange
Die Interaktion mit einer Service Bus-Warteschlange beginnt damit, die [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient)-Klasse zu instanziieren und diese zum Instanziieren der [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient)-Klasse zu verwenden. Sobald Sie über den Warteschlangenclient verfügen, können Sie einen Empfänger erstellen und mit diesem die Methoden [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) oder [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) zum Empfangen von Nachrichten verwenden.

1. Öffnen Sie Ihren bevorzugten Editor, z. B. [Visual Studio Code](https://code.visualstudio.com/).
2. Erstellen Sie eine Datei namens `recieve.js`, und fügen Sie den folgenden Code ein. Dieser Code versucht, 10 Nachrichten aus Ihrer Warteschlange zu empfangen. Die tatsächliche Anzahl der empfangenen Nachrichten hängt von der Anzahl der Nachrichten in der Warteschlange und der Netzwerklatenz ab.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.ReceiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Geben Sie die Verbindungszeichenfolge und den Namen Ihrer Warteschlange in den obigen Code ein.
4. Führen Sie anschließend den Befehl `node receiveMessages.js` in einer Eingabeaufforderung aus, um diese Datei auszuführen.

Glückwunsch! Sie haben soeben Nachrichten aus einer Service Bus-Warteschlange erhalten.

Die [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-)-Methode verwendet `ReceiveMode`, was eine Enumeration mit den Werten [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) und [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations) ist. Denken Sie daran, [Ihre Nachrichten abzugleichen](message-transfers-locks-settlement.md#settling-receive-operations), wenn Sie den `PeekLock`-Modus verwenden, indem Sie eine `complete()`-, `abandon()`-, `defer()`- oder `deadletter()`-Methode für die Nachricht verwenden.

> [!NOTE]
> Sie können Service Bus-Ressourcen mit dem [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) verwalten. Mit dem Service Bus-Explorer können Benutzer eine Verbindung mit einem Service Bus-Namespace herstellen und Messagingentitäten auf einfache Weise verwalten. Das Tool stellt erweiterte Features wie Import-/Exportfunktionen oder Testmöglichkeiten für Themen, Warteschlangen, Abonnements, Relaydienste, Notification Hubs und Event Hubs zur Verfügung. 

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den folgenden Ressourcen:
- [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
- [Weitere Node.js-Beispiele für Service Bus auf GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js Developer Center (in englischer Sprache)](https://azure.microsoft.com/develop/nodejs/)

