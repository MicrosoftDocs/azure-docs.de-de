---
title: 'Schnellstart: Azure Blob Storage-Bibliothek v12 – Python'
description: In dieser Schnellstartanleitung erfahren Sie, wie mithilfe der Azure Blob Storage-Clientbibliothek Version 12 für Python ein Container und ein Blob in Blob Storage (Objektspeicher) erstellt werden. Als Nächstes erfahren Sie, wie Sie den Blob auf Ihren lokalen Computer herunterladen, und wie Sie alle Blobs in einem Container auflisten.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/28/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: e315f0f4f7bfff03a659de430e6fe182037f1b8a
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99096405"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Schnellstart: Verwalten von Blobs per Python v12 SDK

In dieser Schnellstartanleitung erfahren Sie, wie Sie Blobs mit Python verwalten. Bei Blobs handelt es sich um Objekte, die große Mengen von Text oder Binärdaten enthalten können, z. B. Bilder, Dokumente, Streamingmedien und Archivdaten. Sie führen das Hochladen, Herunterladen und Auflisten von Blobs und das Erstellen und Löschen von Containern durch.

Weitere Ressourcen:

* [API-Referenzdokumentation](/python/api/azure-storage-blob)
* [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
* [Paket (Python-Paketindex)](https://pypi.org/project/azure-storage-blob/)
* [Beispiele](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure Storage-Konto [Informationen zu Azure-Speicherkonten](../common/storage-account-create.md)
- [Python](https://www.python.org/downloads/) 2.7, 3.5 oder höher.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Einrichten

In diesem Abschnitt wird beschrieben, wie ein Projekt für die Verwendung mit der Azure Blob Storage-Clientbibliothek v12 für Python vorbereitet wird.

### <a name="create-the-project"></a>Erstellen des Projekts

Erstellen Sie eine Python-Anwendung mit dem Namen *blob-quickstart-v12*.

1. Erstellen Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) ein neues Verzeichnis für das Projekt.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Wechseln Sie zu dem neu erstellten Verzeichnis *blob-quickstart-v12*.

    ```console
    cd blob-quickstart-v12
    ```

1. Erstellen Sie im Verzeichnis *blob-quickstart-v12* ein weiteres Verzeichnis namens *data*. In diesem Verzeichnis werden die Blobdatendateien erstellt und gespeichert.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie im Anwendungsverzeichnis mit dem Befehl `pip install` das Paket mit der Azure Blob Storage-Clientbibliothek für Python.

```console
pip install azure-storage-blob
```

Dieser Befehl installiert das Paket mit der Azure Blob Storage-Clientbibliothek für Python und alle Bibliotheken, von denen die Bibliothek abhängig ist. In diesem Fall ist das nur die Azure Core-Bibliothek für Python.

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Über das Projektverzeichnis:

1. Öffnen einer neuen Textdatei im Code-Editor
1. Hinzufügen von `import`-Anweisungen
1. Erstellen der Struktur für das Programm, einschließlich einer einfachen Ausnahmebehandlung

    Der Code lautet wie folgt:

    :::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/app_framework.py":::

1. Speichern Sie die neue Datei als *blob-quickstart-v12.py* im Verzeichnis *blob-quickstart-v12*.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Blob Storage ist für die Speicherung großer Mengen unstrukturierter Daten optimiert. Unstrukturierte Daten sind Daten, die keinem bestimmten Datenmodell und keiner bestimmten Definition entsprechen (also beispielsweise Text- oder Binärdaten). Blob Storage bietet drei Typen von Ressourcen:

* Das Speicherkonto
* Einen Container im Speicherkonto
* Ein Blob im Container

Im folgenden Diagramm ist die Beziehung zwischen diesen Ressourcen dargestellt.

![Diagramm der Blob Storage-Architektur](./media/storage-blobs-introduction/blob1.png)

Verwenden Sie die folgenden Python-Klassen zur Interaktion mit diesen Ressourcen:

* [BlobServiceClient:](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) Die `BlobServiceClient`-Klasse ermöglicht Ihnen, Azure Storage-Ressourcen und Blobcontainer zu bearbeiten.
* [ContainerClient:](/python/api/azure-storage-blob/azure.storage.blob.containerclient) Die `ContainerClient`-Klasse ermöglicht Ihnen, Azure Storage-Container und deren Blobs zu bearbeiten.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): Die `BlobClient`-Klasse ermöglicht Ihnen, Azure Storage-Blobs zu bearbeiten.

## <a name="code-examples"></a>Codebeispiele

Mit diesen Beispielcodeausschnitten wird veranschaulicht, wie folgende Vorgänge mit der Azure Blob Storage-Clientbibliothek für Python durchgeführt werden:

* [Abrufen der Verbindungszeichenfolge](#get-the-connection-string)
* [Container erstellen](#create-a-container)
* [Hochladen von Blobs in einen Container](#upload-blobs-to-a-container)
* [Auflisten der Blobs in einem Container](#list-the-blobs-in-a-container)
* [Herunterladen von Blobs](#download-blobs)
* [Löschen eines Containers](#delete-a-container)

### <a name="get-the-connection-string"></a>Abrufen der Verbindungszeichenfolge

Der folgende Code ruft die Verbindungszeichenfolge für das Speicherkonto aus der Umgebungsvariablen ab, die im Abschnitt [Konfigurieren der Speicherverbindungszeichenfolge](#configure-your-storage-connection-string) erstellt wurde.

Fügen Sie diesen Code im `try`-Block hinzu:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Erstellen eines Containers

Legen Sie einen Namen für den neuen Container fest. Der folgende Code hängt einen UUID-Wert an den Containernamen an, damit dieser eindeutig ist.

> [!IMPORTANT]
> Die Containernamen müssen klein geschrieben werden. Weitere Informationen zum Benennen von Containern und Blobs finden Sie unter [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Benennen von Containern, Blobs und Metadaten und Verweisen auf diese).

Erstellen Sie eine Instanz der [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient)-Klasse, indem Sie die [from_connection-string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-)-Methode aufrufen. Rufen Sie dann die [create-container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-)-Methode auf, um den Container in Ihrem Speicherkonto zu erstellen.

Fügen Sie diesen Code am Ende des `try`-Blocks hinzu:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Hochladen von Blobs in einen Container

Der folgende Codeausschnitt führt folgende Aktionen durch:

1. Erstellen eines lokalen Verzeichnisses zum Speichern von Datendateien
1. Erstellen einer Textdatei im lokalen Verzeichnis
1. Abrufen eines Verweises auf ein [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient)-Objekt durch Aufrufen der [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-)-Methode für den [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) aus dem Abschnitt [Erstellen eines Containers](#create-a-container)
1. Hochladen der lokalen Textdatei in das Blob durch Aufrufen der [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-)-Methode

Fügen Sie diesen Code am Ende des `try`-Blocks hinzu:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Auflisten der Blobs in einem Container

Listen Sie die Blobs im Container auf, indem Sie die [list-blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-)-Methode aufrufen. In diesem Fall wurde dem Container nur ein Blob hinzugefügt, sodass beim Auflisten auch nur ein Blob zurückgegeben wird.

Fügen Sie diesen Code am Ende des `try`-Blocks hinzu:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Herunterladen von Blobs

Laden Sie das zuvor erstellte Blob herunter, indem Sie die [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-)-Methode aufrufen. Im Beispielcode wird das Suffix „DOWNLOAD“ an den Dateinamen angefügt, damit beide Dateien im lokalen Dateisystem angezeigt werden können.

Fügen Sie diesen Code am Ende des `try`-Blocks hinzu:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Löschen eines Containers

Im folgenden Code werden die mit der App erstellten Ressourcen bereinigt, indem der gesamte Container mit der [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-)-Methode gelöscht wird. Sie können bei Bedarf auch die lokalen Dateien löschen.

Die App wird unterbrochen, um auf Benutzereingaben zu warten, indem `input()` aufgerufen wird, bevor Blob, Container und lokale Dateien gelöscht werden. Überprüfen Sie, ob die Ressourcen ordnungsgemäß erstellt wurden, bevor sie gelöscht werden.

Fügen Sie diesen Code am Ende des `try`-Blocks hinzu:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CleanUp":::

## <a name="run-the-code"></a>Ausführen des Codes

Mit dieser App wird in Ihrem lokalen Ordner eine Testdatei erstellt und in Azure Blob Storage hochgeladen. Im Beispiel werden dann die Blobs im Container aufgelistet, und die Datei wird mit einem neuen Namen heruntergeladen. Sie können die alten und neuen Dateien vergleichen.

Navigieren Sie zu dem Verzeichnis, das die Datei *blob-quickstart-v12.py* enthält, und führen Sie dann den folgenden `python`-Befehl aus, um die App auszuführen.

```console
python blob-quickstart-v12.py
```

Die Ausgabe der App sieht etwa wie das folgende Beispiel aus:

```output
Azure Blob Storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Überprüfen Sie den Ordner *data* vor dem Start der Bereinigung auf die beiden Dateien. Sie können sie öffnen und sich vergewissern, dass sie identisch sind.

Nachdem Sie die Dateien erfolgreich überprüft haben, drücken Sie die **EINGABETASTE**, um die Testdateien zu löschen und die Demo zu beenden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart wurde beschrieben, wie Sie Blobs mit Python hochladen, herunterladen und auflisten.

Weitere Beispiel-Apps für Blob Storage finden Sie unter:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK v12 – Python-Beispiele](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Weitere Informationen finden Sie unter [Azure Storage-Clientbibliotheken für Python](/azure/developer/python/sdk/storage/overview).
* Tutorials, Beispiele, Schnellstarts und weitere Dokumentation finden Sie unter [Azure für Python-Entwickler](/azure/python/).
