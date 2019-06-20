---
title: 'Senden und Empfangen von Ereignissen unter Verwendung von Node.js: Azure Event Hubs | Microsoft-Dokumentation'
description: Dieser Artikel enthält eine exemplarische Vorgehensweise für die Erstellung einer Node.js-Anwendung, die Ereignisse aus Azure Event Hubs sendet.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: e67be59e0ed78b2080986acb73a33fc87599c9d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539343"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Senden von Ereignissen an oder Empfangen von Ereignissen aus Event Hubs mithilfe von Node.js

Azure Event Hubs ist eine Big Data-Streamingplattform und ein Ereigniserfassungsdienst, der Millionen von Ereignissen pro Sekunde empfangen und verarbeiten kann. Event Hubs kann Ereignisse, Daten oder Telemetriedaten, die von verteilter Software und verteilten Geräten erzeugt wurden, verarbeiten und speichern. An einen Event Hub gesendete Daten können transformiert und mit einem beliebigen Echtzeitanalyse-Anbieter oder Batchverarbeitungs-/Speicheradapter gespeichert werden. Eine ausführliche Übersicht über Event Hubs finden Sie unter [Was ist Azure Event Hubs?](event-hubs-about.md) und [Event Hubs-Features im Überblick](event-hubs-features.md).

In diesem Tutorial wird beschrieben, wie Sie Node.js-Anwendungen erstellen, die Ereignisse an einen Event Hub senden oder von diesem empfangen.

> [!NOTE]
> Sie können diese Schnellstartvorlage als Beispiel aus [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client) herunterladen, die Zeichenfolgen `EventHubConnectionString` und `EventHubName` durch die Werte für Ihren Event Hub ersetzen und das Beispiel dann ausführen. Alternativ können Sie auch die Schritte in diesem Tutorial ausführen, um eine eigene Vorlage zu erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durchführen dieses Tutorials benötigen Sie Folgendes:

- Ein aktives Azure-Konto. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.
- Node.js, Version 8.x oder höher. Laden Sie die neueste LTS-Version von [https://nodejs.org](https://nodejs.org) herunter.
- Visual Studio Code (empfohlen) oder eine beliebige andere IDE.
- **Erstellen Sie einen Event Hubs-Namespace und einen Event Hub**. Verwenden Sie zunächst das [Azure-Portal](https://portal.azure.com), um einen Namespace vom Typ „Event Hubs“ zu erstellen, und beschaffen Sie die Verwaltungsanmeldeinformationen, die Ihre Anwendung für die Kommunikation mit dem Event Hub benötigt. Folgen Sie dem Ablauf in [diesem Artikel](event-hubs-create.md), um einen Namespace und einen Event Hub zu erstellen, und fahren Sie dann mit den folgenden Schritten in diesem Tutorial fort. Gehen Sie dann wie im folgenden Artikel beschrieben vor, um die Verbindungszeichenfolge für den Event Hub-Namespace abzurufen: [Abrufen der Verbindungszeichenfolge](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Die Verbindungszeichenfolge wird im weiteren Verlauf dieses Tutorials benötigt.


### <a name="install-npm-package"></a>Installieren des npm-Pakets
Öffnen Sie eine Eingabeaufforderung, die `npm` im Pfad enthält, ändern Sie das Verzeichnis in den Ordner, in dem die Beispiele gespeichert werden sollen, und führen Sie dann den folgenden Befehl aus, um das [npm-Paket für Event Hubs](https://www.npmjs.com/package/@azure/event-hubs) zu installieren.

```shell
npm install @azure/event-hubs
```

Führen Sie stattdessen den folgenden Befehl aus, um das [npm-Paket für den Ereignisprozessorhost](https://www.npmjs.com/package/@azure/event-processor-host) zu installieren.

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Senden von Ereignisse

In diesem Abschnitt erfahren Sie, wie Sie eine Node.js-Anwendung zum Senden von Ereignissen an einen Event Hub erstellen. 

1. Öffnen Sie Ihren bevorzugten Editor, z. B. [Visual Studio Code](https://code.visualstudio.com). 
2. Erstellen Sie eine Datei namens `send.js`, und fügen Sie den folgenden Code ein.
    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

    // Define connection string and the name of the Event Hub
    const connectionString = "";
    const eventHubsName = "";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Geben Sie die Verbindungszeichenfolge und den Namen Ihres Event Hubs in den obigen Code ein.
4. Führen Sie anschließend den Befehl `node send.js` in einer Eingabeaufforderung aus, um diese Datei auszuführen. Dadurch werden 100 Ereignisse an Ihren Event Hub gesendet.

Glückwunsch! Sie haben jetzt Ereignisse an einen Event Hub gesendet.


## <a name="receive-events"></a>Empfangen von Ereignissen

In diesem Abschnitt wird veranschaulicht, wie Sie eine Node.js-Anwendung erstellen, die Ereignisse von einer einzelnen Partition der Standardconsumergruppe in einem Event Hub empfängt. 

1. Öffnen Sie Ihren bevorzugten Editor, z. B. [Visual Studio Code](https://code.visualstudio.com). 
2. Erstellen Sie eine Datei namens `receive.js`, und fügen Sie den folgenden Code ein.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

    // Define connection string and related Event Hubs entity name here
    const connectionString = "";
    const eventHubsName = "";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Geben Sie die Verbindungszeichenfolge und den Namen Ihres Event Hubs in den obigen Code ein.
4. Führen Sie anschließend den Befehl `node receive.js` in einer Eingabeaufforderung aus, um diese Datei auszuführen. Dadurch werden Ereignisse aus einer anderen Partition der Standardconsumergruppe in Ihrem Event Hub empfangen.

Glückwunsch! Hiermit haben Sie erfolgreich Ereignisse von Event Hub empfangen.

## <a name="receive-events-using-event-processor-host"></a>Empfangen von Ereignissen mithilfe des Ereignisprozessorhosts

In diesem Abschnitt erfahren Sie, wie Sie mithilfe eines Azure-[Ereignisprozessorhosts](event-hubs-event-processor-host.md) (EventProcessorHost) in einer Node.js-Anwendung Ereignisse von einem Event Hub empfangen. Der Ereignisprozessorhost ermöglicht den effizienten Empfang von Ereignissen von einem Event Hub. Zu diesem Zweck werden auf allen Partitionen in der Consumergruppe eines Event Hubs Empfänger erstellt. In einer Azure Storage Blob-Instanz werden die Metadaten empfangener Nachrichten in regelmäßigen Abständen mit Prüfpunkten versehen. Dadurch kann der Empfang von Nachrichten später an der Stelle fortgesetzt werden, an der Sie aufgehört haben.

1. Öffnen Sie Ihren bevorzugten Editor, z. B. [Visual Studio Code](https://code.visualstudio.com). 
2. Erstellen Sie eine Datei namens `receiveAll.js`, und fügen Sie den folgenden Code ein.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Define connection string and related Event Hubs entity name here
    const eventHubConnectionString = "";
    const eventHubName = "";
    const storageConnectionString = "";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. Geben Sie die Verbindungszeichenfolge und den Namen Ihres Event Hubs sowie die Verbindungszeichenfolge für eine Azure Blob Storage-Instanz in den obigen Code ein.
4. Führen Sie anschließend den Befehl `node receiveAll.js` in einer Eingabeaufforderung aus, um diese Datei auszuführen.

Glückwunsch! Soeben haben Sie Ereignisse von einem Event Hub mithilfe des Ereignisprozessorhosts erhalten. Dadurch werden Ereignisse aus allen anderen Partitionen der Standardconsumergruppe in Ihrem Event Hub empfangen.

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die folgenden Artikel:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Features und Terminologie in Azure Event Hubs](event-hubs-features.md)
- [Event Hubs – häufig gestellte Fragen](event-hubs-faq.md)
- Sehen Sie sich weitere Node.js-Beispiele für [Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) und den [Ereignisprozessorhost](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) auf GitHub an.
