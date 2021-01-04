---
title: Entwickeln für Azure Files mit .NET | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie .NET-Anwendungen und -Dienste entwickeln, die Azure Files zum Speichern von Daten verwenden.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-csharp
ms.openlocfilehash: e112060db4a44884d3094a939b03ff106ba72e65
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492198"
---
# <a name="develop-for-azure-files-with-net"></a>Entwickeln für Azure Files mit .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Lernen Sie die Grundlagen der Entwicklung von .NET-Anwendungen kennen, die [Azure Files](storage-files-introduction.md) zum Speichern von Daten verwenden. Dieser Artikel zeigt, wie Sie eine einfache Konsolenanwendung erstellen, um Folgendes mit .NET und Azure Files durchzuführen:

- Abrufen der Inhalte einer Datei
- Festlegen der maximalen Größe (des Kontingents) für eine Dateifreigabe
- Erstellen einer SAS (Shared Access Signature) für eine Datei
- Kopieren einer Datei in eine andere Datei im gleichen Speicherkonto
- Kopieren einer Datei in ein Blob im gleichen Speicherkonto
- Erstellen einer Momentaufnahme einer Dateifreigabe
- Wiederherstellen aus der Momentaufnahme einer Freigabe
- Verwenden von Azure-Speichermetriken für die Problembehandlung

Weitere Informationen zu Azure Files finden Sie unter [Was ist Azure Files?](storage-files-introduction.md).

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Grundlegendes zu den .NET-APIs

Azure Files bietet zwei allgemeine Ansätze für Clientansätze: Server Message Block (SMB) und REST. Innerhalb von .NET werden diese Ansätze durch die APIs `System.IO` und `Azure.Storage.Files.Shares` abstrahiert.

API | Verwendung | Notizen
----|-------------|------
[System.IO](/dotnet/api/system.io) | Ihre Anwendung: <ul><li>Muss Dateien mithilfe von SMB lesen/schreiben</li><li>Wird auf einem Gerät ausgeführt, das über Port 445 Zugriff auf Ihr Azure Files-Konto hat</li><li>Muss keine Verwaltungseinstellungen der Dateifreigabe verwalten</li></ul> | Datei-E/A-Vorgänge, die mit Azure Files über SMB implementiert wurden, entsprechen normalerweise E/A-Vorgängen bei einer beliebigen Netzwerkdateifreigabe oder einem beliebigen lokalen Speichergerät. Eine Einführung in mehrere Features in .NET, einschließlich Datei-E/A-Vorgängen, finden Sie im Tutorial zur [Konsolenanwendung](/dotnet/csharp/tutorials/console-teleprompter).
[Azure.Storage.Files.Shares](/dotnet/api/azure.storage.files.shares) | Ihre Anwendung: <ul><li>Kann aufgrund von Firewall- oder ISP-Einschränkungen nicht mithilfe von SMB an Port 445 auf Azure Files zugreifen</li><li>Benötigt Verwaltungsfunktionen, etwa die Möglichkeit, das Kontingent einer Dateifreigabe festzulegen oder eine SAS (Shared Access Signature) zu erstellen</li></ul> | Dieser Artikel veranschaulicht die Verwendung von `Azure.Storage.Files.Shares` für Datei-E/A-Vorgänge mithilfe von REST statt SMB sowie die Verwaltung der Dateifreigabe.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Erstellen der Konsolenanwendung und Erhalten der Assembly

Sie können die Azure Files-Clientbibliothek in jeder Art von .NET-App verwenden. Dies umfasst Azure-Cloud-Apps, Web-Apps, Desktop-Apps und mobile Apps. In diesem Leitfaden erstellen wir der Einfachheit halber eine Konsolenanwendung.

Erstellen Sie in Visual Studio eine neue Windows-Konsolenanwendung. In den folgenden Schritten wird veranschaulicht, wie Sie eine Konsolenanwendung in Visual Studio 2019 erstellen. Die Schritte sind in anderen Versionen von Visual Studio ähnlich.

1. Starten Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.
1. Wählen Sie unter **Neues Projekt erstellen** die Option **Konsolen-App (.NET Framework)** für C# und dann **Weiter** aus.
1. Geben Sie unter **Neues Projekt konfigurieren** einen Namen für die App ein, und wählen Sie **Erstellen** aus.

Fügen Sie alle Codebeispiele in diesem Artikel der Klasse `Program` in der Datei *Program.cs* hinzu.

## <a name="use-nuget-to-install-the-required-packages"></a>Verwenden von NuGet zum Installieren der erforderlichen Pakete

Verweisen Sie in Ihrem Projekt auf die folgenden Pakete:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

- [Azure Core-Bibliothek für .NET](https://www.nuget.org/packages/Azure.Core/): Dieses Paket ist die Implementierung der Azure-Clientpipeline.
- [Azure Storage Blob-Clientbibliothek für .NET](https://www.nuget.org/packages/Azure.Storage.Blobs/): Mit diesem Paket erhalten Sie programmgesteuerten Zugriff auf Blobressourcen in Ihrem Speicherkonto.
- [Azure Storage Files-Clientbibliothek für .NET](https://www.nuget.org/packages/Azure.Storage.Files.Shares/): Mit diesem Paket erhalten Sie programmgesteuerten Zugriff auf Dateiressourcen in Ihrem Speicherkonto.
- [Systemkonfigurations-Manager-Bibliothek für .NET](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/): Dieses Paket stellt eine Klasse zum Speichern und Abrufen von Werten in einer Konfigurationsdatei bereit.

Sie können die Pakete über NuGet abrufen. Folgen Sie diesen Schritten:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.
1. Wählen Sie in **NuGet Package Manager** die Option **Durchsuchen** aus. Suchen Sie nach **Azure.Core**, und wählen Sie das Paket aus. Klicken Sie dann auf **Installieren**.

   Mit diesem Schritt werden das Paket und seine Abhängigkeiten installiert.

1. Suchen und installieren Sie diese Pakete:

   - **Azure.Storage.Blobs**
   - **Azure.Storage.Files.Shares**
   - **System.Configuration.ConfigurationManager**

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [Allgemeine Microsoft Azure Storage-Bibliothek für .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): Mit diesem Paket erhalten Sie programmgesteuerten Zugriff auf allgemeine Ressourcen in Ihrem Speicherkonto.
- [Microsoft Azure Storage Blob-Bibliothek für .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/): Mit diesem Paket erhalten Sie programmgesteuerten Zugriff auf Blobressourcen in Ihrem Speicherkonto.
- [Microsoft Azure Storage File-Bibliothek für .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/): Mit diesem Paket erhalten Sie programmgesteuerten Zugriff auf Dateiressourcen in Ihrem Speicherkonto.
- [Microsoft Azure Configuration Manager-Bibliothek für .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): Mit diesem Paket wird eine Klasse zum Analysieren einer Verbindungszeichenfolge in einer Konfigurationsdatei bereitgestellt – wo auch immer Ihre Anwendung ausgeführt wird.

Sie können die Pakete über NuGet abrufen. Folgen Sie diesen Schritten:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.
1. Wählen Sie in **NuGet Package Manager** die Option **Durchsuchen** aus. Suchen Sie dann nach **Microsoft.Azure.Storage.Blob**, und wählen Sie es aus. Wählen Sie dann **Installieren** aus.

   Mit diesem Schritt werden das Paket und seine Abhängigkeiten installiert.
1. Suchen und installieren Sie diese Pakete:

   - **Microsoft.Azure.Storage.Common**
   - **Microsoft.Azure.Storage.File**
   - **Microsoft.Azure.ConfigurationManager**

---

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Speichern der Anmeldeinformationen für Ihr Speicherkonto in der Datei „App.config“

Als Nächstes speichern Sie Ihre Anmeldeinformationen in der Datei *App.config* des Projekts. Doppelklicken Sie im **Projektmappen-Explorer** auf `App.config`, und bearbeiten Sie die Datei so, dass sie dem folgenden Beispiel ähnelt.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Ersetzen Sie `myaccount` durch den Namen Ihres Speicherkontos und `mykey` durch Ihren Speicherkontoschlüssel.

:::code language="xml" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/app.config" highlight="5,6,7":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Ersetzen Sie `myaccount` durch den Namen Ihres Speicherkontos und `StorageAccountKeyEndingIn==` durch Ihren Speicherkontoschlüssel.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString"
      value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
  </appSettings>
</configuration>
```

---

> [!NOTE]
> Der Azure-Speicheremulator unterstützt Azure Files derzeit nicht. Die Verbindungszeichenfolge muss auf ein Azure-Speicherkonto in der Cloud verweisen, damit die Verwendung von Azure Files funktioniert.

## <a name="add-using-directives"></a>Hinzufügen von using-Direktiven

Öffnen Sie im **Projektmappen-Explorer** die Datei *Program.cs*, und fügen Sie am Anfang der Datei mithilfe von using-Anweisungen Folgendes hinzu.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

---

## <a name="access-the-file-share-programmatically"></a>Programmgesteuertes Zugreifen auf die Dateifreigabe

Fügen Sie in der Datei *Program.cs* folgenden Code hinzu, um programmgesteuert auf die Dateifreigabe zuzugreifen.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Die folgende Methode erstellt eine Dateifreigabe, falls noch keine vorhanden ist. Zuerst erstellt die Methode ein [ShareClient](/dotnet/api/azure.storage.files.shares.shareclient)-Objekt aus einer Verbindungszeichenfolge. Das Beispiel versucht dann, eine Datei herunterzuladen, die wir zu einem früheren Zeitpunkt erstellt haben. Rufen Sie diese Methode aus `Main()` auf.

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShare":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Fügen Sie als Nächstes den folgenden Inhalt zur `Main()`-Methode (nach dem oben angegebenen Code) hinzu, um die Verbindungszeichenfolge abzurufen. Dieser Code ruft einen Verweis auf die zuvor erstellte Datei ab und gibt deren Inhalt aus.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Führen Sie die Konsolenanwendung aus, um die Ausgabe zu sehen.

---

## <a name="set-the-maximum-size-for-a-file-share"></a>Festlegen der maximalen Größe für eine Dateifreigabe

Ab Version 5.x der Azure Files-Clientbibliothek können Sie das Kontingent (die maximale Größe) für eine Dateifreigabe festlegen. Sie können auch überprüfen, wie viele Daten sich aktuell auf der Freigabe befinden.

Eine Festlegung des Kontingents für eine Freigabe begrenzt die Gesamtgröße der Dateien, die in der Freigabe gespeichert werden. Wenn die Gesamtgröße der Dateien in der Freigabe das Kontingent überschreitet, können Clients die Größe vorhandener Dateien nicht erhöhen. Clients können auch keine neuen Dateien erstellen, außer wenn diese Dateien leer sind.

Das folgende Beispiel zeigt, wie Sie die aktuelle Nutzung einer Freigabe überprüfen und das Kontingent für die Freigabe festlegen.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_SetMaxShareSize":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

---

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generieren einer SAS für eine Datei oder Dateifreigabe

Ab Version 5.x der Azure Files-Clientbibliothek können Sie eine SAS (Shared Access Signature) für eine Dateifreigabe oder für eine einzelne Datei generieren.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Die folgende Beispielmethode gibt eine SAS in einer Datei in der angegebenen Freigabe zurück.

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_GetFileSasUri":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Sie können auch eine gespeicherte Zugriffsrichtlinie für eine Dateifreigabe erstellen, um Shared Access Signatures zu verwalten. Es wird empfohlen, eine gespeicherte Zugriffsrichtlinie zu erstellen, weil Sie die SAS widerrufen können, wenn sie gefährdet ist. Im folgenden Beispiel wird eine gespeicherte Zugriffsrichtlinie für eine Freigabe erstellt. Diese Richtlinie wird verwendet, um die Einschränkungen bei einer SAS für eine Datei in der Freigabe bereitzustellen.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

---

Weitere Informationen zum Erstellen und Verwenden von Shared Access Signatures finden Sie unter [Funktionsweise von Shared Access Signatures](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works).

## <a name="copy-files"></a>Kopieren von Dateien

Ab Version 5.x der Azure Files-Clientbibliothek können Sie eine Datei in eine andere Datei, eine Datei in ein Blob oder ein Blob in eine Datei kopieren.

Sie können auch AzCopy verwenden, um eine Datei in eine andere oder ein Blob in eine Datei (oder umgekehrt) zu kopieren. Informationen finden Sie unter [Übertragen von Daten mit AzCopy v10](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Wenn Sie ein BLOB in eine Datei oder eine Datei in ein BLOB kopieren, müssen Sie eine SAS verwenden, um den Zugriff auf das Quellobjekt zu autorisieren. Dies gilt selbst dann, wenn Sie innerhalb desselben Speicherkontos kopieren.

### <a name="copy-a-file-to-another-file"></a>Kopieren einer Datei in eine andere Datei

Im folgenden Beispiel wird eine Datei in eine andere Datei in derselben Freigabe kopiert. Für den Kopiervorgang können Sie die [Authentifizierung mit gemeinsam verwendetem Schlüssel](/rest/api/storageservices/authorize-with-shared-key) verwenden, weil dieser Vorgang Dateien innerhalb ein und desselben Speicherkontos kopiert.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFile":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

---

### <a name="copy-a-file-to-a-blob"></a>Kopieren einer Datei in ein Blob

Im folgenden Beispiel wird eine Datei erstellt und in ein Blob im selben Speicherkonto kopiert. In dem Beispiel wird für die Quelldatei eine SAS erstellt, die der Dienst dazu verwendet, während des Kopiervorgangs den Zugriff auf die Quelldatei zu autorisieren.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFileToBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

---

Auf gleiche Weise können Sie ein BLOB in eine Datei kopieren. Wenn das Quellobjekt ein BLOB ist, erstellen Sie eine SAS, um den Zugriff auf dieses BLOB während des Kopiervorgangs zu autorisieren.

## <a name="share-snapshots"></a>Freigabemomentaufnahmen

Ab Version 8.5 der Azure Files-Clientbibliothek können Sie eine Freigabemomentaufnahme erstellen. Außerdem können Sie Freigabemomentaufnahmen auflisten, durchsuchen und löschen. Freigabemomentaufnahmen sind schreibgeschützt.

### <a name="create-share-snapshots"></a>Erstellen von Freigabemomentaufnahmen

Im folgenden Beispiel wird eine Freigabemomentaufnahme erstellt.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShareSnapshot":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

---

### <a name="list-share-snapshots"></a>Auflisten von Freigabemomentaufnahmen

Das folgende Beispiel listet die Momentaufnahmen in einer Freigabe auf.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListShareSnapshots":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

---

### <a name="list-files-and-directories-within-share-snapshots"></a>Auflisten von Dateien und Verzeichnissen in Freigabemomentaufnahmen

Das folgende Beispiel durchsucht Dateien und Verzeichnisse in Freigabemomentaufnahmen.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListSnapshotContents":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

---

### <a name="restore-file-shares-or-files-from-share-snapshots"></a>Wiederherstellen von Dateifreigaben oder Dateien aus Freigabemomentaufnahmen

Die Erstellung einer Momentaufnahme von einer Dateifreigabe ermöglicht die Wiederherstellung einzelner Dateien oder der gesamten Dateifreigabe.

Zum Wiederherstellen einer Datei aus einer Dateifreigabemomentaufnahme können Sie die Freigabemomentaufnahmen einer Dateifreigabe abfragen. Danach können Sie eine Datei abrufen, die zu einer bestimmten Freigabemomentaufnahme gehört. Verwenden Sie diese Version, um die Datei direkt zu lesen oder wiederherzustellen.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_RestoreFileFromSnapshot":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

---

### <a name="delete-share-snapshots"></a>Löschen von Freigabemomentaufnahmen

Im folgenden Beispiel wird eine Freigabemomentaufnahme gelöscht.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_DeleteSnapshot":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

---

## <a name="troubleshoot-azure-files-by-using-metrics"></a>Behandeln von Azure Files-Problemen mithilfe von Metriken<a name="troubleshooting-azure-files-using-metrics"></a>

Azure Storage Analytics unterstützt Metriken für Azure Files. Mit Metrikdaten können Sie Anforderungen verfolgen und Probleme diagnostizieren.

Die Metriken für Azure Files können über das [Azure-Portal](https://portal.azure.com) aktiviert werden. Sie können Metriken auch programmgesteuert aktivieren, indem Sie den Vorgang [Set File Service Properties](/rest/api/storageservices/set-file-service-properties) mit der REST-API oder einem entsprechenden Vorgang in der Azure Files-Clientbibliothek aufrufen.

Das folgende Codebeispiel zeigt, wie Sie die .NET-Clientbibliothek zum Aktivieren von Metriken für Azure Files verwenden.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UseMetrics":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Fügen Sie Ihrer Datei *Program.cs* zunächst die folgenden `using`-Anweisungen zusätzlich zu den oben hinzugefügten Anweisungen hinzu:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Obwohl bei Azure-Blobs, Azure-Tabellen und Azure-Warteschlangen der gemeinsam genutzte Typ `ServiceProperties` im Namespace `Microsoft.Azure.Storage.Shared.Protocol` verwendet wird, verwendet Azure Files seinen eigenen Typ (`FileServiceProperties`) im Namespace `Microsoft.Azure.Storage.File.Protocol`. Sie müssen aber in Ihrem Code auf beide Namespaces verweisen, damit der folgende Code kompiliert werden kann.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

---

Wenn Probleme auftreten, können Sie auf [Behandeln von Azure Files-Problemen unter Windows](storage-troubleshoot-windows-file-connection-problems.md) verweisen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Files finden Sie in den folgenden Ressourcen:

### <a name="conceptual-articles-and-videos"></a>Konzeptionelle Artikel und Videos

- [Azure Files: a frictionless cloud SMB file system for Windows and Linux (Azure Files: ein reibungsloses Cloud-SMB-Dateisystem für Windows und Linux)](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [Verwenden von Azure Files mit Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Toolunterstützung für Dateispeicher

- [Erste Schritte mit AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
- [Behandeln von Azure Files-Problemen unter Windows](./storage-troubleshoot-windows-file-connection-problems.md)

### <a name="reference"></a>Verweis

- [Azure Storage-APIs für .NET](/dotnet/api/overview/azure/storage)
- [REST-API des Dateidiensts](/rest/api/storageservices/File-Service-REST-API)