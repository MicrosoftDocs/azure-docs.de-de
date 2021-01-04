---
title: 'Schnellstart: Azure Blob Storage-Bibliothek v12 – JavaScript'
description: In dieser Schnellstartanleitung erfahren Sie, wie mithilfe der Azure Blob Storage-Clientbibliothek, Version 12 für JavaScript ein Container und ein Blob im Blobspeicher (Objektspeicher) erstellt werden. Als Nächstes erfahren Sie, wie Sie den Blob auf Ihren lokalen Computer herunterladen, und wie Sie alle Blobs in einem Container auflisten.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: ed3a88dbca1788a5c5a265fe91bdd5e88ae1fe36
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012764"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>Schnellstart: Verwalten von Blobs per JavaScript v12 SDK in Node.js

In dieser Schnellstartanleitung erfahren Sie, wie Sie Blobs mithilfe von Node.js verwalten. Bei Blobs handelt es sich um Objekte, die große Mengen von Text oder Binärdaten enthalten können, z. B. Bilder, Dokumente, Streamingmedien und Archivdaten. Sie führen das Hochladen, Herunterladen und Auflisten von Blobs durch und erstellen und löschen Container.

Zusätzliche Ressourcen:

* [API-Referenzdokumentation](/javascript/api/@azure/storage-blob)
* [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob)
* [Paket (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-blob)
* [Beispiele](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure Storage-Konto [Informationen zu Azure-Speicherkonten](../common/storage-account-create.md)
- [Node.js](https://nodejs.org/en/download/).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Einrichten

In diesem Abschnitt wird beschrieben, wie ein Projekt zur Verwendung mit der Azure Blob Storage-Clientbibliothek v12 für JavaScript vorbereitet wird.

### <a name="create-the-project"></a>Erstellen des Projekts

Erstellen Sie zunächst eine JavaScript-Anwendung mit dem Namen *blob-quickstart-v12*.

1. Erstellen Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) ein neues Verzeichnis für das Projekt.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Wechseln Sie zu dem neu erstellten Verzeichnis *blob-quickstart-v12*.

    ```console
    cd blob-quickstart-v12
    ```

1. Erstellen Sie eine neue Textdatei mit dem Namen *package.json*. Diese Datei definiert das Node.js-Projekt. Speichern Sie diese Datei im Verzeichnis *blob-quickstart-v12*. Der Inhalt der Datei ist nachstehend aufgeführt:

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Sie können ggf. Ihren eigenen Namen für das `author`-Feld eingeben.

### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie das Paket mit der Azure Blob Storage-Clientbibliothek für JavaScript im Verzeichnis *blob-quickstart-v12* mit dem Befehl `npm install`. Dieser Befehl lest die Datei *package.json* und installiert das Paket mit der Azure Blob Storage-Clientbibliothek v12 für JavaScript und alle Bibliotheken, von denen die Bibliothek abhängig ist.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Über das Projektverzeichnis:

1. Öffnen einer weiteren neuen Textdatei im Code-Editor
1. Hinzufügen von `require`-Aufrufen zum Laden von Azure- und Node.js-Modulen
1. Erstellen der Struktur für das Programm, einschließlich einer einfachen Ausnahmebehandlung

    Der Code lautet wie folgt:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const { v1: uuid} = require('uuid');

    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }

    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Speichern Sie die neue Datei als *blob-quickstart-v12.js* im Verzeichnis *blob-quickstart-v12*.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Blob Storage ist für die Speicherung großer Mengen unstrukturierter Daten optimiert. Unstrukturierte Daten sind Daten, die keinem bestimmten Datenmodell und keiner bestimmten Definition entsprechen (also beispielsweise Text- oder Binärdaten). Blob Storage bietet drei Typen von Ressourcen:

* Das Speicherkonto
* Einen Container im Speicherkonto
* Ein Blob im Container

Im folgenden Diagramm ist die Beziehung zwischen diesen Ressourcen dargestellt.

![Diagramm der Blob Storage-Architektur](./media/storage-blobs-introduction/blob1.png)

Verwenden Sie die folgenden JavaScript-Klassen für die Interaktion mit folgenden Ressourcen:

* [BlobServiceClient:](/javascript/api/@azure/storage-blob/blobserviceclient) Die `BlobServiceClient`-Klasse ermöglicht Ihnen, Azure Storage-Ressourcen und Blobcontainer zu bearbeiten.
* [ContainerClient:](/javascript/api/@azure/storage-blob/containerclient) Die `ContainerClient`-Klasse ermöglicht Ihnen, Azure Storage-Container und deren Blobs zu bearbeiten.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient): Die `BlobClient`-Klasse ermöglicht Ihnen, Azure Storage-Blobs zu bearbeiten.

## <a name="code-examples"></a>Codebeispiele

Diese Beispielcodeausschnitte veranschaulichen, wie folgende Vorgänge mit der Azure Blob Storage-Clientbibliothek für JavaScript durchgeführt werden:

* [Abrufen der Verbindungszeichenfolge](#get-the-connection-string)
* [Container erstellen](#create-a-container)
* [Hochladen von Blobs in einen Container](#upload-blobs-to-a-container)
* [Auflisten der Blobs in einem Container](#list-the-blobs-in-a-container)
* [Herunterladen von Blobs](#download-blobs)
* [Löschen eines Containers](#delete-a-container)

### <a name="get-the-connection-string"></a>Abrufen der Verbindungszeichenfolge

Der folgende Code ruft die Verbindungszeichenfolge für das Speicherkonto aus der Umgebungsvariable ab, die im Abschnitt [Konfigurieren der Speicherverbindungszeichenfolge](#configure-your-storage-connection-string) erstellt wurde.

Fügen Sie diesen Code in der `main`-Funktion hinzu:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-container"></a>Erstellen eines Containers

Legen Sie einen Namen für den neuen Container fest. Der folgende Code hängt einen UUID-Wert an den Containernamen an, damit dieser eindeutig ist.

> [!IMPORTANT]
> Die Containernamen müssen klein geschrieben werden. Weitere Informationen zum Benennen von Containern und Blobs finden Sie unter [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Benennen von Containern, Blobs und Metadaten und Verweisen auf diese).

Erstellen Sie eine Instanz der [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient)-Klasse, indem Sie die [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-)-Methode aufrufen. Rufen Sie dann die [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-)-Methode auf, um einen Verweis auf einen Container zu erhalten. Rufen Sie zum schließlich [create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) auf, um den Container in Ihrem Speicherkonto zu erstellen.

Fügen Sie diesen Code am Ende der `main`-Funktion hinzu:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>Hochladen von Blobs in einen Container

Der folgende Codeausschnitt führt folgende Aktionen durch:

1. Erstellen einer Textzeichenfolge, die in ein Blob hochgeladen werden soll.
1. Abrufen eines Verweises auf ein [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient)-Objekt durch Aufrufen der [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-)-Methode für [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) im Abschnitt [Erstellen eines Container](#create-a-container).
1. Hochladen der Textzeichenfolgedaten in das Blob durch Aufrufen der [upload](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-)-Methode.

Fügen Sie diesen Code am Ende der `main`-Funktion hinzu:

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
```

### <a name="list-the-blobs-in-a-container"></a>Auflisten der Blobs in einem Container

Listen Sie die Blobs im Container auf, indem Sie die [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-)-Methode aufrufen. In diesem Fall wurde dem Container nur ein Blob hinzugefügt, sodass beim Auflisten auch nur ein Blob zurückgegeben wird.

Fügen Sie diesen Code am Ende der `main`-Funktion hinzu:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Herunterladen von Blobs

Laden Sie das zuvor erstellte Blob herunter, indem Sie die [download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-)-Methode aufrufen. Der Beispielcode enthält eine Hilfsfunktion namens `streamToString`, die verwendet wird, um einen lesbaren Node.js-Stream in eine Zeichenfolge einzulesen.

Fügen Sie diesen Code am Ende der `main`-Funktion hinzu:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Fügen Sie diese Hilfsfunktion *hinter* der `main`-Funktion hinzu:

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>Löschen eines Containers

Im folgenden Code werden die mit der App erstellten Ressourcen bereinigt, indem der gesamte Container mit der [delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-)-Methode gelöscht wird. Sie können bei Bedarf auch die lokalen Dateien löschen.

Fügen Sie diesen Code am Ende der `main`-Funktion hinzu:

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>Ausführen des Codes

Diese App erstellt eine Textzeichenfolge und lädt sie in den Blobspeicher hoch. Im Beispiel werden dann die Blobs im Container aufgelistet, das Blob heruntergeladen und die heruntergeladenen Daten angezeigt.

Navigieren Sie in einer Konsoleneingabeaufforderung zu dem Verzeichnis, das die Datei *blob-quickstart-v12.py* enthält, und führen Sie dann den folgenden `node`-Befehl aus, um die App auszuführen.

```console
node blob-quickstart-v12.js
```

Die Ausgabe der App sieht etwa wie das folgende Beispiel aus:

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

Durchlaufen Sie den Code Schritt für Schritt im Debugger, und überprüfen Sie das [Azure-Portal](https://portal.azure.com) während des gesamten Vorgangs. Stellen Sie fest, ob der Container erstellt wurde. Sie können das Blob im Container öffnen und den Inhalt anzeigen.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung wurde beschrieben, wie Sie Blobs mit JavaScript hochladen, herunterladen und auflisten.

Tutorials, Beispiele, Schnellstartanleitungen und weiteres Dokumentationsmaterial finden Sie hier:

> [!div class="nextstepaction"]
> [Azure für JavaScript- und Node.js-Entwickler](/azure/developer/javascript/)

* Informationen zum Bereitstellen einer Web-App, die Azure Blob Storage verwendet, finden Sie unter [Tutorial: Hochladen von Bilddaten in die Cloud mit Azure Storage](./storage-upload-process-images.md?preserve-view=true&tabs=javascript).
* Blobspeicher-Beispiel-Apps finden Sie unter [Getting started with samples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples) (Erste Schritte mit Beispielen).
* Weitere Informationen finden Sie in der [Azure Storage Blob client library for JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob) (Azure Storage Blob-Clientbibliothek für JavaScript).