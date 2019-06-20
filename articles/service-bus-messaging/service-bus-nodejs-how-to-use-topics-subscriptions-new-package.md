---
title: Verwenden von Azure Service Bus-Themen und -Abonnements mit Node.js | Microsoft Docs
description: Erfahren Sie mehr zur Verwendung von Service Bus-Themen und -Abonnements in Azure aus einer Node.js-App.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: 7686014adb989494e6df277de4137b76c3125696
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65992130"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Verwenden von Service Bus-Themen und -Abonnements mit Node.js und dem Paket „azure/service-bus“
> [!div class="op_multi_selector" title1="Programming language" title2="Node.js pacakge"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

In diesem Tutorial erfahren Sie, wie Sie ein Node.js-Programm schreiben, um mithilfe des neuen Pakets [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) Nachrichten an ein Service Bus-Thema zu senden und Nachrichten von einem Service Bus-Abonnement zu empfangen. Dieses Paket verwendet das schnellere [Advance Message Queueing Protocol 1.0](service-bus-amqp-overview.md). Im Gegensatz dazu hat das ältere Paket [azure-sb](https://www.npmjs.com/package/azure-sb) die [Service Bus-REST-Laufzeit-APIs](/rest/api/servicebus/service-bus-runtime-rest) verwendet. Die Beispiele sind in JavaScript geschrieben.

## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure-Abonnement. Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF) registrieren.
- Wenn Sie über kein Thema und kein Abonnement verfügen, führen Sie die Schritte im Artikel [Schnellstart: Erstellen eines Service Bus-Themas und eines oder mehrerer Abonnements dieses Themas im Azure-Portal](service-bus-quickstart-topics-subscriptions-portal.md) aus, um sie zu erstellen. Notieren Sie sich die Verbindungszeichenfolge für Ihre Service Bus-Instanz und den Namen des Themas und des Abonnements, die Sie erstellt haben. Diese Werte benötigen Sie für die Beispiele.

> [!NOTE]
> - Dieses Tutorial funktioniert für die Beispiele, die Sie mit [Node.js](https://nodejs.org/) kopieren und ausführen können. Anweisungen zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website](../app-service/app-service-web-get-started-nodejs.md) oder [Node.js-Clouddienst mithilfe von Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Mit dem neuen Paket [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) wird die Erstellung von Themen und Abonnements noch nicht unterstützt. Wenn Sie sie programmgesteuert erstellen möchten, verwenden Sie das Paket [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Verwenden des Node-Paket-Managers (NPM) zum Installieren des Pakets
Öffnen Sie eine Eingabeaufforderung, die `npm` im Pfad enthält, ändern Sie das Verzeichnis in den Ordner, in dem die Beispiele gespeichert werden sollen, und führen Sie dann den folgenden Befehl aus, um das npm-Paket für Service Bus zu installieren.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Senden von Nachrichten an ein Thema
Die Interaktion mit einem Service Bus-Thema beginnt damit, die [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient)-Klasse zu instanziieren und diese dann zum Instanziieren der [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient)-Klasse zu verwenden. Wenn Sie über den Themenclient verfügen, können Sie einen Sender erstellen und mit diesem die Methoden [send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) oder [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) zum Senden von Nachrichten verwenden.

1. Öffnen Sie Ihren bevorzugten Editor, z. B. [Visual Studio Code](https://code.visualstudio.com/).
2. Erstellen Sie eine Datei namens `send.js`, und fügen Sie den folgenden Code ein. Mit diesem Code werden 10 Nachrichten an das Thema gesendet.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const topicClient = sbClient.createTopicClient(topicName);
      const sender = topicClient.createSender();
      
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

            await topicClient.close();
          } finally {
            await sbClient.close();
          }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Geben Sie die Verbindungszeichenfolge und den Namen des Themas im obigen Code ein.
4. Führen Sie anschließend den Befehl `node send.js` in einer Eingabeaufforderung aus, um diese Datei auszuführen. 

Glückwunsch! Sie haben soeben Nachrichten an eine Service Bus-Warteschlange gesendet.

Nachrichten verfügen über einige Standardeigenschaften wie `label` und `messageId`, die Sie beim Senden festlegen können. Wenn Sie benutzerdefinierte Eigenschaften festlegen möchten, verwenden Sie `userProperties`. Dies ist ein JSON-Objekt, das Schlüssel-Wert-Paare Ihrer benutzerdefinierten Daten enthalten kann.

Service Bus-Themen unterstützen eine maximale Nachrichtengröße von 256 KB im [Standard-Tarif](service-bus-premium-messaging.md) und 1 MB im [Premium-Tarif](service-bus-premium-messaging.md). Es gibt keine Beschränkung für die Anzahl der Nachrichten, die ein Thema enthält. Es gibt jedoch eine Grenze für die Gesamtgröße der Nachrichten eines Themas. Die Themengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB. Weitere Informationen zu Kontingenten finden Sie unter [Service Bus-Kontingente](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Empfangen von Nachrichten aus einem Abonnement
Die Interaktion mit einem Service Bus-Abonnement beginnt damit, die [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient)-Klasse zu instanziieren und diese dann zum Instanziieren der [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient)-Klasse zu verwenden. Wenn Sie über den Abonnementclient verfügen, können Sie einen Empfänger erstellen und mit diesem die Methoden [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) oder [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) zum Empfangen von Nachrichten verwenden.

1. Öffnen Sie Ihren bevorzugten Editor, z. B. [Visual Studio Code](https://code.visualstudio.com/).
2. Erstellen Sie eine Datei namens `recieve.js`, und fügen Sie den folgenden Code ein. Mit diesem Code wird versucht, 10 Nachrichten aus dem Abonnement zu empfangen. Die tatsächliche Anzahl der empfangenen Nachrichten hängt von der Anzahl der Nachrichten im Abonnement und der Netzwerklatenz ab.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.ReceiveAndDelete);
      
      try {
        const messages = await receiver.receiveMessages(10);
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
        
        await subscriptionClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Geben Sie die Verbindungszeichenfolge und den Namen des Abonnements im obigen Code ein.
4. Führen Sie anschließend den Befehl `node receiveMessages.js` in einer Eingabeaufforderung aus, um diese Datei auszuführen.

Glückwunsch! Sie haben soeben Nachrichten aus einem Service Bus-Abonnement erhalten.

Die [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-)-Methode verwendet `ReceiveMode` (eine Enumeration mit den Werten [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) und [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations)). Denken Sie daran, [Ihre Nachrichten abzugleichen](message-transfers-locks-settlement.md#settling-receive-operations), wenn Sie den `PeekLock`-Modus verwenden, indem Sie die Methode `complete()`, `abandon()`, `defer()` oder `deadletter()` für die Nachricht verwenden.

## <a name="subscription-filters-and-actions"></a>Abonnementfilter und -aktionen
Service Bus unterstützt [Filter und Aktionen für Abonnements](topic-filters.md), sodass Sie die eingehenden Nachrichten für ein Abonnement filtern und die zugehörigen Eigenschaften bearbeiten können.

Nachdem Sie eine Instanz von `SubscriptionClient` erstellt haben, können Sie mithilfe der folgenden Methoden Regeln für das Abonnement abrufen, hinzufügen und entfernen, um die Filter und Aktionen zu steuern.

- getRules
- addRule
- removeRule

Jedes Abonnement umfasst eine Standardregel, die den Filter „true“ verwendet, um alle eingehenden Nachrichten zuzulassen. Wenn Sie eine neue Regel hinzufügen, denken Sie daran, den Standardfilter zu entfernen, damit der Filter in der neuen Regel verwendet werden kann. Wenn ein Abonnement keine Regeln enthält, empfängt es keine Nachrichten.

> [!NOTE]
> Sie können Service Bus-Ressourcen mit dem [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) verwalten. Mit dem Service Bus-Explorer können Benutzer eine Verbindung mit einem Service Bus-Namespace herstellen und Messagingentitäten auf einfache Weise verwalten. Das Tool stellt erweiterte Features wie Import-/Exportfunktionen oder Testmöglichkeiten für Themen, Warteschlangen, Abonnements, Relaydienste, Notification Hubs und Event Hubs zur Verfügung. 

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den folgenden Ressourcen.

- [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
- Weitere [Node.js-Beispiele für Service Bus auf GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js Developer Center (in englischer Sprache)](https://azure.microsoft.com/develop/nodejs/)


