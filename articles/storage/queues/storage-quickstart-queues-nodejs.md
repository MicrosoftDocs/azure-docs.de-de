---
title: 'Schnellstart: Azure Queue Storage-Bibliothek v12 – JavaScript'
description: Erfahren Sie, wie Sie die Azure Queue Storage-Bibliothek v12 für JavaScript verwenden, um eine Warteschlange zu erstellen und dieser Nachrichten hinzuzufügen. Dann lernen Sie, wie Sie Nachrichten in der Warteschlange lesen und löschen. Zudem erfahren Sie, wie Sie eine Warteschlange löschen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/13/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 5f50e42bc33adb8f40520f3f98bf1dcfba190a41
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491909"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>Schnellstart: Azure Queue Storage-Clientbibliothek v12 für JavaScript

Beginnen Sie mit dem Einsatz der Azure Queue Storage-Clientbibliothek v12 für JavaScript. Azure Queue Storage ist ein Dienst für die Speicherung einer großen Anzahl von Nachrichten, die später abgerufen und verarbeitet werden. Führen Sie die nachfolgenden Schritte aus, um das Paket zu installieren und den Beispielcode für grundlegende Aufgaben zu testen.

Mit der Azure Queue Storage-Clientbibliothek v12 für JavaScript können Sie Folgendes ausführen:

- Erstellen einer Warteschlange
- Hinzufügen von Nachrichten zu einer Warteschlange
- Einsehen von Nachrichten in einer Warteschlange
- Aktualisieren einer Nachricht in einer Warteschlange
- Empfangen von Nachrichten aus einer Warteschlange
- Löschen von Nachrichten aus einer Warteschlange
- Löschen einer Warteschlange

Zusätzliche Ressourcen:

- [API-Referenzdokumentation](/javascript/api/@azure/storage-queue/)
- [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)
- [Paket (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-queue)
- [Beispiele](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- Azure Storage-Konto – [Erstellen eines Speicherkontos](../common/storage-account-create.md)
- Aktuelle [Node.js](https://nodejs.org/en/download/) für Ihr Betriebssystem.

## <a name="setting-up"></a>Einrichten

In diesem Abschnitt wird beschrieben, wie Sie ein Projekt zur Verwendung mit der Azure Queue Storage-Clientbibliothek v12 für JavaScript vorbereiten.

### <a name="create-the-project"></a>Erstellen des Projekts

Erstellen Sie eine Node.js-Anwendung mit dem Namen *queues-quickstart-v12*.

1. Erstellen Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) ein neues Verzeichnis für das Projekt.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Wechseln Sie zum neu erstellten Verzeichnis *queues-quickstart-v12*.

    ```console
    cd queues-quickstart-v12
    ```

1. Erstellen Sie eine neue Textdatei mit dem Namen *package.json*. Diese Datei definiert das Node.js-Projekt. Speichern Sie diese Datei im Verzeichnis *queues-quickstart-v12*. Der Inhalt der Datei ist nachstehend aufgeführt:

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Sie können ggf. Ihren eigenen Namen für das `author`-Feld eingeben.

### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie das Paket mit der Azure Queue Storage-Clientbibliothek für JavaScript mithilfe des Befehls `npm install` im Verzeichnis *queues-quickstart-v12*.

```console
npm install
```

 Dieser Befehl liest die Datei *package.json* und installiert das Paket mit der Azure Queue Storage-Clientbibliothek v12 für JavaScript sowie alle Bibliotheken, von denen die Bibliothek abhängig ist.

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Über das Projektverzeichnis:

1. Öffnen einer weiteren neuen Textdatei im Code-Editor
1. Hinzufügen von `require`-Aufrufen zum Laden von Azure- und Node.js-Modulen
1. Erstellen der Struktur für das Programm, einschließlich einer sehr einfachen Ausnahmebehandlung

    Der Code lautet wie folgt:

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue storage v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. Speichern Sie die neue Datei als *queues-quickstart-v12.js* im Verzeichnis *queues-quickstart-v12*.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Queue Storage ist ein Dienst für die Speicherung einer großen Anzahl von Nachrichten. Eine Warteschlangennachricht kann bis zu 64 KB groß sein. Eine Warteschlange kann Millionen Nachrichten enthalten, bis die maximale Kapazität eines Speicherkontos erreicht ist. Warteschlangen werden häufig verwendet, um ein Arbeits-Backlog zur asynchronen Verarbeitung zu erstellen. Queue Storage bietet drei Arten von Ressourcen:

- Das Speicherkonto
- Eine Warteschlange im Speicherkonto
- Nachrichten in der Warteschlange

Im folgenden Diagramm ist die Beziehung zwischen diesen Ressourcen dargestellt.

![Diagramm der Queue Storage-Architektur](./media/storage-queues-introduction/queue1.png)

Verwenden Sie die folgenden JavaScript-Klassen für die Interaktion mit folgenden Ressourcen:

- [QueueServiceClient](/javascript/api/@azure/storage-queue/queueserviceclient): Mit dem `QueueServiceClient` können Sie alle Warteschlangen in Ihrem Speicherkonto verwalten.
- [QueueClient](/javascript/api/@azure/storage-queue/queueclient): Mit der `QueueClient`-Klasse können Sie eine einzelne Warteschlange und die darin enthaltenen Nachrichten verwalten und bearbeiten.
- [QueueMessage](/javascript/api/@azure/storage-queue/queuemessage): Die `QueueMessage`-Klasse repräsentiert die einzelnen Objekte, die beim Aufrufen von [receiveMessages](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) in einer Warteschlange zurückgegeben werden.

## <a name="code-examples"></a>Codebeispiele

Diese Beispielcodeausschnitte veranschaulichen, wie folgende Vorgänge mit der Azure Queue Storage-Clientbibliothek für JavaScript ausgeführt werden:

- [Abrufen der Verbindungszeichenfolge](#get-the-connection-string)
- [Erstellen einer Warteschlange](#create-a-queue)
- [Hinzufügen von Nachrichten zu einer Warteschlange](#add-messages-to-a-queue)
- [Einsehen von Nachrichten in einer Warteschlange](#peek-at-messages-in-a-queue)
- [Aktualisieren einer Nachricht in einer Warteschlange](#update-a-message-in-a-queue)
- [Empfangen von Nachrichten aus einer Warteschlange](#receive-messages-from-a-queue)
- [Löschen von Nachrichten aus einer Warteschlange](#delete-messages-from-a-queue)
- [Löschen einer Warteschlange](#delete-a-queue)

### <a name="get-the-connection-string"></a>Abrufen der Verbindungszeichenfolge

Der folgende Code ruft die Verbindungszeichenfolge für das Speicherkonto aus der Umgebungsvariable ab, die im Abschnitt [Konfigurieren der Speicherverbindungszeichenfolge](#configure-your-storage-connection-string) erstellt wurde.

Fügen Sie diesen Code in der `main`-Funktion hinzu:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>Erstellen einer Warteschlange

Legen Sie einen Namen für die neue Warteschlange fest. Der folgende Code hängt einen UUID-Wert an den Warteschlangennamen an, damit dieser eindeutig ist.

> [!IMPORTANT]
> Warteschlangennamen dürfen nur Kleinbuchstaben, Ziffern und Bindestriche enthalten und müssen mit einem Buchstaben oder einer Ziffer beginnen. Vor und nach jedem Bindestrich muss ein Zeichen stehen, das kein Bindestrich ist. Der Name muss außerdem zwischen 3 und 63 Zeichen lang sein. Weitere Informationen zum Benennen von Warteschlangen finden Sie unter [Benennen von Warteschlangen und Metadaten](/rest/api/storageservices/naming-queues-and-metadata).

Erstellen Sie eine Instanz der [QueueClient](/javascript/api/@azure/storage-queue/queueclient)-Klasse. Rufen Sie dann die [create](/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-)-Methode auf, um die Warteschlange in Ihrem Speicherkonto zu erstellen.

Fügen Sie diesen Code am Ende der `main`-Funktion hinzu:

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>Hinzufügen von Nachrichten zu einer Warteschlange

Der folgende Codeausschnitt fügt der Warteschlange durch Aufrufen der [sendMessage](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-)-Methode Nachrichten hinzu. Zudem wird die [QueueMessage](/javascript/api/@azure/storage-queue/queuemessage) gespeichert, die vom dritten `sendMessage`-Aufruf zurückgegeben wird. Die zurückgegebene `sendMessageResponse` wird später im Programm zum Aktualisieren des Nachrichteninhalts verwendet.

Fügen Sie diesen Code am Ende der `main`-Funktion hinzu:

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>Einsehen von Nachrichten in einer Warteschlange

Durch Aufrufen der [peekMessages](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-)-Methode können Sie die Nachrichten in der Warteschlange einsehen. Die `peekMessages`-Methode ruft mindestens eine Nachricht vom Anfang der Warteschlange ab, ändert aber nicht die Sichtbarkeit der Nachricht.

Fügen Sie diesen Code am Ende der `main`-Funktion hinzu:

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>Aktualisieren einer Nachricht in einer Warteschlange

Aktualisieren Sie den Inhalt einer Nachricht, indem Sie die [updateMessage](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-)-Methode aufrufen. Die `updateMessage`-Methode kann das Sichtbarkeitstimeout und den Inhalt einer Nachricht ändern. Beim Nachrichteninhalt muss es sich um eine UTF-8-codierte Zeichenfolge handeln, die bis zu 64 KB groß sein darf. Übergeben Sie zusammen mit dem neuen Inhalt die Objekte `messageId` und `popReceipt` aus der Antwort, die weiter oben im Code gespeichert wurde. Die `sendMessageResponse`-Eigenschaften identifizieren die Nachricht, die aktualisiert werden soll.

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>Empfangen von Nachrichten aus einer Warteschlange

Laden Sie zuvor hinzugefügte Nachrichten durch Aufrufen der [receiveMessages](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-)-Methode herunter. Übergeben Sie im Feld `numberOfMessages` die maximale Anzahl von Nachrichten, die für diesen Aufruf empfangen werden dürfen.

Fügen Sie diesen Code am Ende der `main`-Funktion hinzu:

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>Löschen von Nachrichten aus einer Warteschlange

Löschen Sie Nachrichten aus einer Warteschlange, nachdem sie empfangen und verarbeitet wurden. In diesem Fall besteht die Verarbeitung nur darin, dass die Nachricht in der Konsole angezeigt wird.

Sie löschen Nachrichten, indem Sie die [deleteMessage](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-)-Methode aufrufen. Alle Nachrichten, die nicht explizit gelöscht werden, werden schlussendlich wieder in der Warteschlange angezeigt und ggf. erneut verarbeitet.

Fügen Sie diesen Code am Ende der `main`-Funktion hinzu:

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>Löschen einer Warteschlange

Der folgende Code bereinigt die von der App erstellten Ressourcen, indem die Warteschlange mithilfe der [delete](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-)-Methode gelöscht wird.

Fügen Sie diesen Code am Ende der `main`-Funktion hinzu, und speichern Sie die Datei:

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>Ausführen des Codes

Diese App erstellt drei Nachrichten und fügt sie einer Azure-Warteschlange hinzu. Der Code listet die Nachrichten in der Warteschlange auf, ruft sie ab und löscht sie und löscht letztendlich die Warteschlange.

Navigieren Sie im Konsolenfenster zu dem Verzeichnis, das die Datei *queues-quickstart-v12.js* enthält, und führen Sie dann den folgenden `node`-Befehl aus, um die App auszuführen.

```console
node queues-quickstart-v12.js
```

Die Ausgabe der App sieht etwa wie das folgende Beispiel aus:

```output
Azure Queue storage v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

Durchlaufen Sie den Code Schritt für Schritt im Debugger, und überprüfen Sie das [Azure-Portal](https://portal.azure.com) während des gesamten Vorgangs. Überprüfen Sie in Ihrem Speicherkonto, ob Nachrichten in der Warteschlange erstellt und gelöscht werden.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie mithilfe von JavaScript-Code eine Warteschlange erstellen und dieser Nachrichten hinzufügen. Danach haben Sie erfahren, wie Sie Nachrichten einsehen, abrufen und löschen. Zum Schluss haben Sie gelernt, wie Sie eine Nachrichtenwarteschlange löschen.

Tutorials, Beispiele, Schnellstartanleitungen und weiteres Dokumentationsmaterial finden Sie hier:

> [!div class="nextstepaction"]
> [Dokumentation zu Azure für JavaScript](/azure/developer/javascript/)

- Weitere Informationen finden Sie in der [Azure Storage Queue-Clientbibliothek für JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue).
- Weitere Beispiel-Apps für Azure Queue Storage finden Sie bei den [
Beispielen für die Azure Queue Storage-Clientbibliothek v12 für JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples).
