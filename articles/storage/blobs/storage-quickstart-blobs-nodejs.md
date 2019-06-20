---
title: Erstellen eines Blobs in Azure Storage mithilfe der Clientbibliothek für Node.js v2
description: Erstellen Sie ein Speicherkonto und einen Container im Objektspeicher (Blob). Verwenden Sie anschließend die Azure Storage-Clientbibliothek für Node.js v2, um ein Blob in Azure Storage hochzuladen, ein Blob herunterzuladen und die Blobs in einem Container aufzulisten.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: mhopkins
ms.reviewer: seguler
ms.openlocfilehash: 7ca1eacd1add7453833b4b9ad1d4bd0fbef13ca7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65149415"
---
# <a name="how-to-upload-download-and-list-blobs-using-the-client-library-for-nodejs-v2"></a>Hochladen, Herunterladen und Auflisten von Blobs mit der Clientbibliothek für Node.js v2

In dieser Schrittanleitung erfahren Sie, wie Sie mit der Clientbibliothek für Node.js v2 Blobs in Azure Blob Storage hochladen, herunterladen und auflisten.

> [!TIP]
> Die neueste Version der Azure Storage-Clientbibliothek für Node.js ist v10. Microsoft empfiehlt, nach Möglichkeit die neueste Version der Clientbibliothek zu verwenden. Informationen zum Einstieg in v10 finden Sie unter [Schnellstart: Hochladen, Herunterladen, Auflisten und Löschen von Blobs mit der Azure Storage-Clientbibliothek für JavaScript v10 (Vorschau)](storage-quickstart-blobs-nodejs-v10.md).

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Erstellen Sie ein Azure-Speicherkonto im [Azure-Portal](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Hilfe zur Erstellung des Kontos finden Sie unter [Erstellen eines Speicherkontos](../common/storage-quickstart-create-account.md).

## <a name="download-the-sample-application"></a>Herunterladen der Beispielanwendung

Bei der [Beispielanwendung](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) handelt es sich um eine einfache Node.js-Konsolenanwendung. Klonen Sie zunächst das Repository auf Ihren Computer, indem Sie den folgenden Befehl verwenden:

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Suchen Sie zum Öffnen der Anwendung nach dem Ordner *storage-blobs-node-quickstart*, und öffnen Sie ihn in Ihrer bevorzugten Bearbeitungsumgebung.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurieren der Speicherverbindungszeichenfolge

Vor dem Ausführen der Anwendung müssen Sie die Verbindungszeichenfolge für das Speicherkonto angeben. Das Beispielrepository enthält eine Datei mit dem Namen *.env.example*. Sie können diese Datei umbenennen, indem Sie die Erweiterung *.example* entfernen, sodass die Datei nur noch *.env* heißt. Fügen Sie in der Datei *.env* Ihren Wert für die Verbindungszeichenfolge nach dem Schlüssel *AZURE_STORAGE_CONNECTION_STRING* hinzu.

## <a name="install-required-packages"></a>Installieren erforderlicher Pakete

Führen Sie im Anwendungsverzeichnis *npm install* aus, um die erforderlichen Pakete für die Anwendung zu installieren.

```bash
npm install
```

## <a name="run-the-sample"></a>Ausführen des Beispiels
Nachdem nun die Abhängigkeiten installiert wurden, können Sie das Beispiel ausführen, indem Sie den folgenden Befehl ausgeben:

```bash
npm start
```

Die Ausgabe des Skripts sollte ungefähr wie folgt aussehen:

```bash
Containers:
 - container-one
 - container-two
Container "demo" is created
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme.md
Blob downloaded blob content: "hello Blob SDK"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Wenn Sie für dieses Beispiel ein neues Speicherkonto verwenden, werden unter „*Container*“ möglicherweise keine Containernamen angezeigt.

## <a name="understanding-the-code"></a>Grundlegendes zum Code
Der erste Ausdruck wird verwendet, um Werte in Umgebungsvariablen zu laden.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
```

Das Modul *dotenv* lädt Umgebungsvariablen, wenn die App lokal zum Debuggen ausgeführt wird. Werte werden in der Datei *.env* definiert und in den aktuellen Ausführungskontext geladen. In Kontexten von Produktionsumgebungen stellt die Serverkonfiguration diese Werte zur Verfügung, weshalb dieser Code nur ausgeführt wird, wenn das Skript nicht im Kontext einer Produktionsumgebung ausgeführt wird.

```javascript
const path = require('path');
const storage = require('azure-storage');
```

Die Module haben den folgenden Zweck: 

Datei namens *.env* in den aktuellen Ausführungskontext
- *path* ist erforderlich, um den absoluten Dateipfad der Datei zu ermitteln, die in Blobspeicher hochgeladen werden soll.
- *azure-storage* ist das [Azure Storage-Clientbibliothek](https://docs.microsoft.com/javascript/api/azure-storage)-Modul für Node.js.

Anschließend wird die Variable **blobService** als neue Instanz des Azure Blob-Diensts initialisiert.

```javascript
const blobService = storage.createBlobService();
```

In der folgenden Implementierung sind die *blobService*-Funktionen jeweils von einem *Promise*-Element (Zusage) umschlossen, mit dem der Zugriff auf die *async*-Funktion und den *await*-Operator von JavaScript ermöglicht wird, um das Rückrufverhalten der [Azure Storage-API](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) zu optimieren. Wenn für jede Funktion eine erfolgreiche Antwort zurückgegeben wird, wird die Zusage mit relevanten Daten und einer spezifischen Nachricht für die Aktion aufgelöst.

### <a name="list-containers"></a>Auflisten von Containern

Die Funktion *listContainers* ruft [listContainersSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) auf, um Sammlungen von Containern in Gruppen zurückzugeben.

```javascript
const listContainers = async () => {
    return new Promise((resolve, reject) => {
        blobService.listContainersSegmented(null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} containers`, containers: data.entries });
            }
        });
    });
};
```

Die Größe der Gruppen ist über [ListContainersOptions](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.listcontaineroptions?view=azure-node-latest) konfigurierbar. Der Aufruf von *listContainersSegmented* gibt Blobmetadaten als Array von [ContainerResult](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.containerresult?view=azure-node-latest)-Instanzen zurück. Ergebnisse werden in Batches mit einem Inkrement von 5.000 (Segmenten) zurückgegeben. Wenn in einem Container mehr als 5.000 Blobs vorhanden sind, enthalten die Ergebnisse einen Wert für *continuationToken*. Zum Auflisten nachfolgender Segmente aus dem Blobcontainer können Sie das Fortsetzungstoken als zweites Argument zurück an *listContainersSegment* übergeben.

### <a name="create-a-container"></a>Erstellen eines Containers

Die Funktion *createContainer* ruft [createContainerIfNotExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) auf und legt die passende Zugriffsebene für das Blob fest.

```javascript
const createContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

Der zweite Parameter (*options*) für **createContainerIfNotExists** akzeptiert einen Wert für [publicAccessLevel](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest). Der Wert *blob* für *publicAccessLevel* gibt an, dass bestimmte Blobdaten öffentlich verfügbar gemacht werden. Diese Einstellung ist anders als die Zugriffsebene *container*, bei der die Möglichkeit gewährt wird, den Inhalt des Containers aufzulisten.

Durch die Verwendung von **createContainerIfNotExists** kann die Anwendung den Befehl mehrfach *createContainer* ausführen, ohne Fehler zurückzugeben, wenn der Container bereits vorhanden ist. In einer Produktionsumgebung rufen Sie **createContainerIfNotExists** häufig nur einmal auf, da derselbe Container für die gesamte Anwendung verwendet wird. In diesen Fällen können Sie den Container im Voraus über das Portal oder die Azure CLI erstellen.

### <a name="upload-text"></a>Hochladen von Text

Die Funktion *uploadString* ruft [createBlockBlobFromText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) auf, um eine beliebige Zeichenkette in den Blobcontainer zu schreiben (oder sie zu überschreiben).

```javascript
const uploadString = async (containerName, blobName, text) => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromText(containerName, blobName, text, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Text "${text}" is written to blob storage` });
            }
        });
    });
};
```
### <a name="upload-a-local-file"></a>Hochladen einer lokalen Datei

Für die Funktion *uploadLocalFile* wird die Funktion [createBlockBlobFromLocalFile](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromlocalfile-string--string--string--errororresult-blobresult--) verwendet, um eine Datei aus dem Dateisystem in Blobspeicher hochzuladen und zu schreiben (bzw. zu überschreiben). 

```javascript
const uploadLocalFile = async (containerName, filePath) => {
    return new Promise((resolve, reject) => {
        const fullPath = path.resolve(filePath);
        const blobName = path.basename(filePath);
        blobService.createBlockBlobFromLocalFile(containerName, blobName, fullPath, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Local file "${filePath}" is uploaded` });
            }
        });
    });
};
```
Weitere Ansätze zum Hochladen von Inhalt in Blobs sind die Verwendung von [text](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest-string--string--string---buffer--errororresult-blobresult--) und [streams](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromstream-string--string--stream-readable--number--errororresult-blobresult--). Für die Überprüfung des Dateiuploads in Ihren Blobspeicher können Sie den [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) verwenden, um die Daten in Ihrem Konto anzuzeigen.

### <a name="list-the-blobs"></a>Auflisten der Blobs

Mit der Funktion *listBlobs* rufen Sie die [listBlobsSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#listblobssegmented-string--continuationtoken--errororresult-listblobsresult--)-Methode auf, um eine Liste mit Blobmetadaten in einem Container zurückzugeben. 

```javascript
const listBlobs = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} blobs in '${containerName}'`, blobs: data.entries });
            }
        });
    });
};
```

Durch das Aufrufen von *listBlobsSegmented* werden Blobmetadaten als Array mit [BlobResult](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.blobresult?view=azure-node-latest)-Instanzen zurückgegeben. Ergebnisse werden in Batches mit einem Inkrement von 5.000 (Segmenten) zurückgegeben. Wenn in einem Container mehr als 5.000 Blobs vorhanden sind, enthalten die Ergebnisse einen Wert für **continuationToken**. Zum Auflisten nachfolgender Segmente aus dem Blobcontainer können Sie das Fortsetzungstoken als zweites Argument zurück an **listBlobSegmented** übergeben.

### <a name="download-a-blob"></a>Herunterladen eines Blobs

In der Funktion *downloadBlob* wird [getBlobToText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) verwendet, um den Inhalt des Blobs an den Speicherort des angegebenen absoluten Dateipfads herunterzuladen.

```javascript
const downloadBlob = async (containerName, blobName) => {
    const dowloadFilePath = path.resolve('./' + blobName.replace('.txt', '.downloaded.txt'));
    return new Promise((resolve, reject) => {
        blobService.getBlobToText(containerName, blobName, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Blob downloaded "${data}"`, text: data });
            }
        });
    });
};
```
Die hier gezeigte Implementierung ändert die Quelle und gibt den Blobinhalt als Zeichenkette zurück. Sie können den Blob auch als [Datenstrom](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) sowie direkt in eine [lokale Datei](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) herunterladen.

### <a name="delete-a-blob"></a>Löschen eines BLOBs

Die Funktion *deleteBlob* ruft die Funktion [deleteBlobIfExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#deleteblobifexists-string--string--errororresult-boolean--) auf. Wie der Name schon erahnen lässt, gibt diese Funktion keinen Fehler zurück, wenn das Blob bereits gelöscht wurde.

```javascript
const deleteBlob = async (containerName, blobName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="delete-a-container"></a>Löschen eines Containers

Container werden durch Aufrufen der *deleteContainer*-Methode aus dem Blobdienst und durch Übergabe des Containernamens gelöscht.

```javascript
const deleteContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteContainer(containerName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' deleted` });
            }
        });
    });
};
```

### <a name="calling-code"></a>Aufrufen von Code

Zur Unterstützung der *async/await*-Syntax von JavaScript wird der gesamte Aufrufcode in eine Funktion namens *execute* eingebettet. Dann wird „execute“ aufgerufen und als Zusicherung behandelt.

```javascript
async function execute() {
    // commands 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```
Der gesamte folgende Code wird innerhalb der Funktion „execute“ ausgeführt, in der der Kommentar `// commands` platziert ist.

Zunächst werden die relevanten Variablen deklariert, um Namen, Beispielinhalt zuzuweisen und auf die lokale Datei zu verweisen, die in den Blobspeicher hochgeladen wird.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello Node SDK";
const localFilePath = "./readme.md";
let response;
```

Um die Container im Speicherkonto aufzulisten, wird die Funktion „listContainers“ aufgerufen und die zurückgegebene Liste der Container wird im Ausgabefenster protokolliert.

```javascript
console.log("Containers:");
response = await listContainers();
response.containers.forEach((container) => console.log(` -  ${container.name}`));
```

Nachdem die Liste der Container verfügbar ist, können Sie mit der *findIndex*-Methode überprüfen, ob der zu erstellende Container bereits vorhanden ist. Wenn der Container nicht vorhanden ist, wird er erstellt.

```javascript
const containerDoesNotExist = response.containers.findIndex((container) => container.name === containerName) === -1;

if (containerDoesNotExist) {
    await createContainer(containerName);
    console.log(`Container "${containerName}" is created`);
}
```
Als nächstes werden eine Zeichenkette und eine lokale Datei in den Blobspeicher hochgeladen.

```javascript
await uploadString(containerName, blobName, content);
console.log(`Blob "${blobName}" is uploaded`);

response = await uploadLocalFile(containerName, localFilePath);
console.log(response.message);
```
Der Vorgang zum Auflisten von Blobs ist identisch mit dem Auflisten von Containern. Der Aufruf von *listBlobs* gibt ein Array von Blobs im Container zurück und wird im Ausgabefenster protokolliert.

```javascript
console.log(`Blobs in "${containerName}" container:`);
response = await listBlobs(containerName);
response.blobs.forEach((blob) => console.log(` - ${blob.name}`));
```

Um einen Blob herunterzuladen, wird die Antwort erfasst und verwendet, um auf den Wert des Blobs zuzugreifen. Aus der Antwort wird „readableStreamBody“ in eine Zeichenfolge umgewandelt und im Ausgabefenster abgemeldet.

```javascript
response = await downloadBlob(containerName, blobName);
console.log(`Downloaded blob content: "${response.text}"`);
```

Abschließend werden Blob und Container aus dem Speicherkonto gelöscht.

```javascript
await deleteBlob(containerName, blobName);
console.log(`Blob "${blobName}" is deleted`);

await deleteContainer(containerName);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Alle in das Speicherkonto geschriebenen Daten werden am Ende des Codebeispiels automatisch gelöscht. 

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Ressourcen für die Entwicklung von Node.js-Anwendungen mit Blobs

Sehen Sie sich diese zusätzlichen Ressourcen zur Node.js-Entwicklung mit Blobspeicher an:

### <a name="binaries-and-source-code"></a>Binärdateien und Quellcode

- Zeigen Sie den [Quellcode der Node.js-Clientbibliothek](https://github.com/Azure/azure-storage-node) für Azure Storage auf GitHub an, und installieren Sie ihn.

### <a name="client-library-reference-and-samples"></a>Clientbibliothek – Referenz und Beispiele

- Weitere Informationen zur Node.js-Clientbibliothek finden Sie in der [Referenz zur Node.js-API](https://docs.microsoft.com/javascript/api/overview/azure/storage).
- Erkunden Sie die [Beispiele für Blobspeicher](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob), die mit der Node.js-Clientbibliothek geschrieben wurden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde veranschaulicht, wie Sie den Upload einer Datei zwischen einem lokalen Datenträger und Azure Blob Storage mit Node.js durchführen. Weitere Informationen zur Arbeit mit Blobspeicher finden Sie im GitHub-Repository.

> [!div class="nextstepaction"]
> [Microsoft Azure Storage SDK für Node.js und JavaScript für Browser](https://github.com/Azure/azure-storage-node)
