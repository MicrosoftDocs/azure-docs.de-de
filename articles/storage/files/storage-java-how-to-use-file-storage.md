---
title: Entwickeln für Azure Files mit Java | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Java-Anwendungen und -Dienste entwickeln, die Azure Files zum Speichern von Dateidaten verwenden.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 25baa278961b93b04e60f2e997b98753cb6cf3ab
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024108"
---
# <a name="develop-for-azure-files-with-java"></a>Entwickeln für Azure Files mit Java

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Lernen Sie die Grundlagen der Entwicklung von Java-Anwendungen kennen, die Azure Files zum Speichern von Daten verwenden. Erstellen einer Konsolenanwendung und Kennenlernen der grundlegenden Aktionen mithilfe von Azure Files-APIs:

- Erstellen und Löschen von Azure-Dateifreigaben
- Erstellen und Löschen von Verzeichnissen
- Auflisten von Dateien und Verzeichnissen in einer Azure-Dateifreigabe
- Hochladen, Herunterladen und Löschen einer Datei

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="create-a-java-application"></a>Erstellen einer Java-Anwendung

Zum Erstellen der Beispiele benötigen Sie das Java Development Kit (JDK) und das [Azure Storage SDK für Java](https://github.com/azure/azure-sdk-for-java). Sie sollten auch ein Azure-Speicherkonto erstellt haben.

## <a name="set-up-your-application-to-use-azure-files"></a>Einrichten der Anwendung für die Verwendung von Azure Files

Um die Azure Files-APIs zu verwenden, fügen Sie folgenden Code am Anfang der Java-Datei dort ein, wo der Zugriff auf Azure Files erfolgen soll.

# <a name="java-v12"></a>[Java v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Einrichten einer Azure-Speicherverbindungszeichenfolge

Zum Verwenden von Azure Files müssen Sie eine Verbindung mit Ihrem Azure Storage-Konto herstellen. Konfigurieren Sie eine Verbindungszeichenfolge, und verwenden Sie diese zum Herstellen der Verbindung mit Ihrem Speicherkonto. Definieren Sie eine statische Variable, die die Verbindungszeichenfolge enthalten soll.

# <a name="java-v12"></a>[Java v12](#tab/java)

Ersetzen Sie *\<storage_account_name\>* und *\<storage_account_key\>* durch die tatsächlichen Werte für Ihr Speicherkonto.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Ersetzen Sie *your_storage_account_name* und *your_storage_account_key* durch die tatsächlichen Werte für Ihr Speicherkonto.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-azure-files-storage"></a>Zugreifen auf den Azure Files-Speicher

# <a name="java-v12"></a>[Java v12](#tab/java)

Um auf Azure Files zuzugreifen, erstellen Sie ein [ShareClient](/java/api/com.azure.storage.file.share.shareclient)-Objekt. Erstellen Sie mithilfe der [ShareClientBuilder](/java/api/com.azure.storage.file.share.shareclientbuilder)-Klasse ein neues **ShareClient**-Objekt.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Um auf Ihr Speicherkonto zuzugreifen, müssen Sie das **CloudStorageAccount**-Objekt verwenden und die Verbindungszeichenfolge an die zugehörige **parse**-Methode übergeben.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** löst eine InvalidKeyException aus, daher müssen Sie sie in einem try/catch-Block platzieren.

---

## <a name="create-a-file-share"></a>Erstellen einer Dateifreigabe

Alle Dateien und Verzeichnisse in Azure Files werden in einem Container gespeichert, der als „Freigabe“ bezeichnet wird.

# <a name="java-v12"></a>[Java v12](#tab/java)

Die [ShareClient.create](/java/api/com.azure.storage.file.share.shareclient.create)-Methode löst eine Ausnahme aus, wenn die Freigabe bereits vorhanden ist. Fügen Sie den Aufruf von **create** in einen `try/catch`-Block ein, und behandeln Sie die Ausnahme.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Um Zugriff auf eine Freigabe und ihren Inhalt zu erhalten, erstellen Sie einen Azure Files-Client.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Mit dem Azure Files-Client können Sie dann einen Verweis auf die Freigabe abrufen.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Um die Freigabe zu erstellen, verwenden Sie die **createIfNotExists**-Methode des **CloudFileShare**-Objekts.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

An diesem Punkt enthält **share** einen Verweis auf eine Freigabe mit dem Namen **sample share**.

---

## <a name="delete-a-file-share"></a>Löschen einer Dateifreigabe

Mit dem folgenden Beispielcode wird eine Dateifreigabe gelöscht.

# <a name="java-v12"></a>[Java v12](#tab/java)

Sie löschen eine Freigabe, indem Sie die [ShareClient.delete](/java/api/com.azure.storage.file.share.shareclient.delete)-Methode aufrufen.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Sie löschen eine Freigabe, indem Sie die **deleteIfExists**-Methode für ein **CloudFileShare**-Objekt aufrufen.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

---

## <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

Organisieren Sie den Speicher, indem Sie Dateien in Unterverzeichnissen ablegen, anstatt alle Dateien im Stammverzeichnis zu speichern.

# <a name="java-v12"></a>[Java v12](#tab/java)

Der folgende Code erstellt ein Verzeichnis durch Aufrufen von [ShareDirectoryClient.create](/java/api/com.azure.storage.file.share.sharedirectoryclient.create). Die Beispielmethode gibt einen `Boolean`-Wert zurück, der angibt, ob das Verzeichnis erfolgreich erstellt wurde.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Mit dem folgenden Code wird ein Unterverzeichnis mit dem Namen **sampledir** im Stammverzeichnis erstellt.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

---

## <a name="delete-a-directory"></a>Löschen eines Verzeichnisses

Das Löschen eines Verzeichnisses ist einfach. Sie können kein Verzeichnis löschen, das noch Dateien oder Unterverzeichnisse enthält.

# <a name="java-v12"></a>[Java v12](#tab/java)

Die [ShareDirectoryClient.delete](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete)-Methode löst eine Ausnahme aus, wenn das Verzeichnis nicht vorhanden oder nicht leer ist. Fügen Sie den Aufruf von **delete** in einen `try/catch`-Block ein, und behandeln Sie die Ausnahme.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Auflisten von Dateien und Verzeichnissen in einer Azure-Dateifreigabe

# <a name="java-v12"></a>[Java v12](#tab/java)

Sie rufen eine Liste von Dateien und Verzeichnissen ab, indem Sie [ShareDirectoryClient.listFilesAndDirectories](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories) aufrufen. Die Methode gibt eine Liste von [ShareFileItem](/java/api/com.azure.storage.file.share.models.sharefileitem)-Objekten zurück, die Sie durchlaufen können. Der folgende Code listet die Dateien und Verzeichnisse in dem durch den *dirName*-Parameter angegebenen Verzeichnis auf.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Sie rufen eine Liste von Dateien und Verzeichnissen ab, indem Sie **listFilesAndDirectories** in einem **CloudFileDirectory**-Verweis aufrufen. Die Methode gibt eine Liste von **ListFileItem**-Objekten zurück, die Sie durchlaufen können. Mit dem folgenden Code werden Dateien und Verzeichnisse im Stammverzeichnis aufgelistet.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

---

## <a name="upload-a-file"></a>Hochladen einer Datei

Erfahren Sie, wie Sie eine Datei aus dem lokalen Speicher hochladen.

# <a name="java-v12"></a>[Java v12](#tab/java)

Mit dem folgenden Code wird eine lokale Datei in Azure Files-Speicher hochgeladen, indem die [ShareFileClient.uploadFromFile](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile)-Methode aufgerufen wird. Die folgende Beispielmethode gibt einen `Boolean`-Wert zurück, der angibt, ob die angegebene Datei erfolgreich hochgeladen wurde.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Sie rufen einen Verweis auf das Verzeichnis für den Upload der Datei ab, indem Sie die **getRootDirectoryReference**-Methode für das share-Objekt aufrufen.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Da Sie nun einen Verweis auf das Stammverzeichnis der Freigabe erstellt haben, können Sie mit dem folgenden Code eine Datei in dieses Verzeichnis hochladen.

```java
// Define the path to a local file.
final String filePath = "C:\\temp\\Readme.txt";

CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
cloudFile.uploadFromFile(filePath);
```

---

## <a name="download-a-file"></a>Herunterladen einer Datei

Ein Vorgang, den Sie häufiger durchführen werden, ist das Herunterladen von Dateien aus dem Azure Files-Speicher.

# <a name="java-v12"></a>[Java v12](#tab/java)

Im folgenden Beispiel wird die angegebene Datei in das lokale Verzeichnis heruntergeladen, das im Parameter *destDir* angegeben ist. Die Beispielmethode macht den Namen der heruntergeladenen Datei eindeutig, indem ihm das Datum und die Uhrzeit vorangestellt werden.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Im folgenden Beispiel wird die Datei „SampleFile.txt“ heruntergeladen und ihr Inhalt angezeigt.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

---

## <a name="delete-a-file"></a>Löschen von Dateien

Ein weiterer häufiger Azure Files-Vorgang ist das Löschen von Dateien.

# <a name="java-v12"></a>[Java v12](#tab/java)

Der folgende Code löscht die angegebene Datei. Zunächst wird im Beispiel ein [ShareDirectoryClient](/java/api/com.azure.storage.file.share.sharedirectoryclient) erstellt, der auf dem *dirName*-Parameter basiert. Anschließend ruft der Code basierend auf dem Parameter *fileName* einen [ShareFileClient](/java/api/com.azure.storage.file.share.sharefileclient) vom Verzeichnisclient ab. Zum Schluss ruft die Beispielmethode [ShareFileClient.delete](/java/api/com.azure.storage.file.share.sharefileclient.delete) auf, um die Datei zu löschen.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Mit dem folgenden Code wird die Datei "SampleFile.txt" gelöscht, die im Verzeichnis **sampledir** gespeichert ist.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

---

## <a name="next-steps"></a>Nächste Schritte

Folgen Sie diesen Links, wenn Sie mehr über andere Azure-Speicher-APIs erfahren möchten.

- [Azure für Java-Entwickler](/azure/developer/java)
- [Azure SDK für Java](https://github.com/azure/azure-sdk-for-java)
- [Azure SDK für Android](https://github.com/azure/azure-sdk-for-android)
- [SDK-Referenz zur Azure-Dateifreigaben-Clientbibliothek für Java](/java/api/overview/azure/storage-file-share-readme)
- [REST-API für Azure-Speicherdienste](/rest/api/storageservices/)
- [Azure Storage-Teamblog](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](../common/storage-use-azcopy-v10.md)
- [Behandeln von Azure Files-Problemen unter Windows](storage-troubleshoot-windows-file-connection-problems.md)
