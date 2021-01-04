---
title: 'Schnellstart: Azure Blob Storage-Clientbibliothek v8 für Java'
description: Erstellen Sie ein Speicherkonto und einen Container im Objektspeicher (Blob). Anschließend verwenden Sie die Azure Storage-Clientbibliothek v8 für Java, um ein Blob in Azure Storage hochzuladen, ein Blob herunterzuladen und die Blobs in einem Container aufzulisten.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 39b5a812c5f9b8e148b5180569af110c72e2c75b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544597"
---
# <a name="quickstart-manage-blobs-with-java-v8-sdk"></a>Schnellstart: Verwalten von Blobs mit Java SDK, Version 8

In dieser Schnellstartanleitung erfahren Sie, wie Sie Blobs mithilfe von Java verwalten. Bei Blobs handelt es sich um Objekte, die große Mengen von Text oder Binärdaten enthalten können, z. B. Bilder, Dokumente, Streamingmedien und Archivdaten. Hier werden Sie Blobs hochladen, herunterladen und auflisten. Außerdem erstellen Sie Container, legen Berechtigungen dafür fest und löschen Container.

> [!NOTE]
> In dieser Schnellstartanleitung wird eine Legacy-Version der Azure Blob Storage-Clientbibliothek verwendet. Informationen zu den ersten Schritten mit der neuesten Version finden Sie unter [Schnellstart: Verwalten von Blobs per Java v12 SDK](storage-quickstart-blobs-java.md).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure Storage-Konto [Informationen zu Azure-Speicherkonten](../common/storage-account-create.md)
- Eine IDE mit Maven-Integration. In dieser Anleitung wird [Eclipse](https://www.eclipse.org/downloads/) mit der Konfiguration „Eclipse-IDE für Java-Entwickler“ verwendet.

## <a name="download-the-sample-application"></a>Herunterladen der Beispielanwendung

Die [Beispielanwendung](https://github.com/Azure-Samples/storage-blobs-java-quickstart) ist eine einfache Konsolenanwendung.

Verwenden Sie [Git](https://git-scm.com/), um eine Kopie der Anwendung in Ihre Entwicklungsumgebung herunterzuladen.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Mit diesem Befehl wird das Repository in Ihren lokalen Git-Ordner geklont. Um das Projekt zu öffnen, starten Sie Eclipse, und schließen Sie die Willkommensseite. Wählen Sie **File** (Datei) und anschließend **Open Projects from File System** (Projekte aus Dateisystem öffnen) aus. Stellen Sie sicher, dass **Detect and configure project natures** (Projektarten erkennen und konfigurieren) aktiviert ist. Wählen Sie **Directory** (Verzeichnis) aus, und navigieren Sie zum Speicherort des geklonten Repositorys. Wählen Sie innerhalb des geklonten Repositorys den Ordner **blobAzureApp** aus. Vergewissern Sie sich, dass das Projekt **blobAzureApp** als Eclipse-Projekt angezeigt wird, und wählen Sie anschließend **Finish** (Fertig stellen) aus.

Nachdem das Importieren des Projekts abgeschlossen ist, öffnen Sie **AzureApp.java** (unter **blobQuickstart.blobAzureApp** in **src/main/java**), und ersetzen Sie in der Zeichenfolge `storageConnectionString` die Angaben `accountname` und `accountkey`. Führen Sie die Anwendung dann aus. Spezifische Anweisungen für diese Aufgaben finden Sie in den folgenden Abschnitten.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurieren der Speicherverbindungszeichenfolge

Sie müssen in der Anwendung die Verbindungszeichenfolge für das Speicherkonto angeben. Öffnen Sie die Datei **AzureApp.Java**. Suchen Sie die Variable `storageConnectionString`, und fügen Sie den Wert der Verbindungszeichenfolge ein, den Sie im vorherigen Abschnitt kopiert haben. Die Variable `storageConnectionString` sollte in etwa wie im folgenden Codebeispiel aussehen:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Ausführen des Beispiels

Mit dieser Beispielanwendung wird eine Testdatei in Ihrem Standardverzeichnis (*C:\Benutzer\<user>\AppData\Local\Temp* für Windows-Benutzer) erstellt. Anschließend wird diese Datei in Blob Storage hochgeladen, die Blobs werden im Container aufgelistet und die Datei wird mit einem neuen Namen heruntergeladen, sodass Sie die alte und die neue Datei vergleichen können.

Führen Sie das Beispiel mithilfe von Maven über die Befehlszeile aus. Öffnen Sie eine Shell, und navigieren Sie in Ihrem geklonten Verzeichnis zu **blobAzureApp**. Geben Sie dann `mvn compile exec:java` ein.

Das folgende Beispiel zeigt die Ausgabe für Benutzer, die die Anwendung unter Windows ausführen.

```
Azure Blob storage quick start sample
Creating container: quickstartcontainer
Creating a sample file at: C:\Users\<user>\AppData\Local\Temp\sampleFile514658495642546986.txt
Uploading the sample file
URI of blob is: https://myexamplesacct.blob.core.windows.net/quickstartcontainer/sampleFile514658495642546986.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.

Deleting the container
Deleting the source, and downloaded files
```

Überprüfen Sie, ob die Beispieldatei in Ihrem Standardverzeichnis (*C:\Benutzer\<user>\AppData\Local\Temp* für Windows-Benutzer) vorhanden ist, bevor Sie fortfahren. Kopieren Sie die URL für das Blob aus dem Konsolenfenster, und fügen Sie sie in einem Browser ein, um den Inhalt der Datei in Blob Storage anzuzeigen. Wenn Sie die Beispieldatei in Ihrem Verzeichnis mit dem in Blob Storage gespeicherten Inhalt vergleichen, sehen Sie, dass sie identisch sind.

  >[!NOTE]
  >Sie können zum Anzeigen der Dateien in Blob Storage auch ein Tool, z.B. den [Azure Storage-Explorer](https://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), verwenden. Der Azure Storage-Explorer ist ein kostenloses plattformübergreifendes Tool, das Ihnen den Zugriff auf die Speicherkontoinformationen ermöglicht.

Drücken Sie nach dem Überprüfen der Dateien die **EINGABETASTE**, um die Demo zu beenden und die Testdateien zu löschen. Da Sie jetzt wissen, welche Aktionen das Beispiel ausführt, öffnen Sie die Datei **AzureApp.java**, um den Code zu betrachten.

## <a name="understand-the-sample-code"></a>Grundlagen des Beispielcodes

Als Nächstes gehen wir schrittweise durch den Beispielcode, damit Sie verstehen können, wie er funktioniert.

### <a name="get-references-to-the-storage-objects"></a>Abrufen von Verweisen auf die Speicherobjekte

Zunächst müssen die Verweise auf die Objekte erstellt werden, die zum Zugreifen auf und Verwalten von Blob Storage verwendet werden. Diese Objekte bauen aufeinander auf – jedes wird vom jeweils nächsten in der Liste verwendet.

* Erstellen Sie eine Instanz des [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage.storageaccount)-Objekts, das auf das Speicherkonto verweist.

    Das **CloudStorageAccount**-Objekt ist eine Darstellung Ihres Speicherobjekts, und es ermöglicht den programmgesteuerten Zugriff und das programmgesteuerte Festlegen von Eigenschaften des Speicherkontos. Mithilfe des **CloudStorageAccount**-Objekts können Sie eine Instanz von **CloudBlobClient** erstellen, was für den Zugriff auf den Blob-Dienst erforderlich ist.

* Erstellen Sie eine Instanz des **CloudBlobClient**-Objekts, das auf den [Blob-Dienst](/java/api/com.microsoft.azure.storage.blob.cloudblobclient) im Speicherkonto verweist.

    Der Cloudblobclient (**CloudBlobClient**) stellt einen Zugriffspunkt für den Blob-Dienst zur Verfügung, mit dem Sie programmgesteuert auf Blob-Speichereigenschaften zugreifen und sie festlegen können. Mithilfe von **CloudBlobClient** können Sie eine Instanz des **CloudBlobClient**-Objekts erstellen, was zum Erstellen von Containern erforderlich ist.

* Erstellen Sie eine Instanz des [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer)-Objekts, das den Container darstellt, auf den Sie zugreifen. Container dienen dazu, Ihre Blobs zu strukturieren – genau wie Ordner, die Sie auf Ihrem Computer zum Strukturieren Ihrer Dateien verwenden.

    Wenn Sie über den **CloudBlobContainer** verfügen, können Sie eine Instanz des [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob.cloudblockblob)-Objekts erstellen, die auf das gewünschte Blob verweist, und beispielsweise einen Upload-, Download- oder Kopiervorgang ausführen.

> [!IMPORTANT]
> Die Containernamen müssen klein geschrieben werden. Weitere Informationen zu Containern finden Sie unter [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Benennen von Containern, Blobs und Metadaten und Verweisen auf diese).

### <a name="create-a-container"></a>Erstellen eines Containers

In diesem Abschnitt erstellen Sie eine Instanz der Objekte und einen neuen Container und legen dann Berechtigungen für den Container fest, damit die Blobs öffentlich sind und einfach mit einer URL auf sie zugegriffen werden kann. Der Name des Containers lautet **quickstartcontainer**.

In diesem Beispiel wird [CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists) verwendet, da bei jedem Ausführen des Beispiels ein neuer Container erstellt werden soll. In einer Produktionsumgebung, in der Sie in der gesamten Anwendung den gleichen Container verwenden, empfiehlt es sich hingegen, **CreateIfNotExists** nur einmal aufzurufen. Alternativ können Sie den Container im Voraus erstellen, damit er nicht im Code erstellt werden muss.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>Hochladen von Blobs in den Container

Um eine Datei in ein Blockblob hochzuladen, rufen Sie einen Verweis auf das Blob im Zielcontainer ab. Nachdem Sie den Blobverweis abgerufen haben, können Sie mithilfe von [CloudBlockBlob.Upload](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.upload) Daten in das Blob hochladen. Dieser Vorgang erstellt das Blob, sofern noch keines vorhanden ist, oder überschreibt das vorhandene Blob.

Der Beispielcode erstellt eine lokale Datei für den Upload und Download. Dabei wird die hochzuladende Datei als **Quelle** und der Name des Blob in **Blob** gespeichert. Im folgenden Beispiel wird die Datei in einen Container mit dem Namen **quickstartcontainer** hochgeladen.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

Es gibt mehrere `upload`-Methoden (unter anderem [upload](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.upload), [uploadBlock](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadblock), [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadfullblob), [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadstandardblobtier) und [uploadText](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadtext)), die Sie mit Blob Storage verwenden können. Für eine Zeichenfolge können Sie beispielsweise anstelle der `Upload`-Methode die `UploadText`-Methode verwenden.

Blockblobs können jede Art von Text oder Binärdateien darstellen. Seitenblobs werden in erster Linie für die VHD-Dateien verwendet, die IaaS-VMs zugrunde liegen. Anfügeblobs dienen zur Protokollierung und werden beispielsweise verwendet, wenn Sie etwas in eine Datei schreiben und ihr nach und nach weitere Informationen hinzufügen möchten. Die meisten Objekte, die in Blob Storage gespeichert werden, sind allerdings Blockblobs.

### <a name="list-the-blobs-in-a-container"></a>Auflisten der Blobs in einem Container

Mit [CloudBlobContainer.ListBlobs](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobs) können Sie eine Liste von Dateien im Container abrufen. Der folgende Code ruft die Liste der Blobs ab und durchläuft sie, um die URIs der gefundenen Blobs anzuzeigen. Sie können den URI aus dem Befehlsfenster kopieren und in einen Browser einfügen, um die Datei anzuzeigen.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Herunterladen von Blobs

Laden Sie mit [CloudBlob.DownloadToFile](/java/api/com.microsoft.azure.storage.blob.cloudblob.downloadtofile) Blobs auf den lokalen Datenträger herunter.

Der folgende Code lädt das im vorherigen Abschnitt hochgeladene Blob herunter und fügt dem Blobnamen das Suffix „_DOWNLOADED“ hinzu, damit auf dem lokalen Datenträger beide Dateien angezeigt werden.

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and
// haven't changed the blob we're interested in, so we can reuse it.
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die hochgeladenen Blobs nicht mehr benötigen, können Sie mit [CloudBlobContainer.DeleteIfExists](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists) den gesamten Container löschen. Mit dieser Methode werden auch die Dateien im Container gelöscht.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();

if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie mit Java Dateien zwischen einem lokalen Datenträger und Azure Blob Storage übertragen. Weitere Informationen zum Arbeiten mit Java finden Sie in unserem Quellcoderepository auf GitHub.

> [!div class="nextstepaction"]
> [Java-API-Referenz](/java/api/overview/azure/storage?view=azure-java-legacy)
> [Codebeispiele für Java](../common/storage-samples-java.md)