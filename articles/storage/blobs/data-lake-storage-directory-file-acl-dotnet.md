---
title: Azure Data Lake Storage Gen2 .NET SDK für Dateien und Zugriffssteuerungslisten
description: Verwenden Sie die Azure Storage-Clientbibliothek, um Verzeichnisse, Dateien und Zugriffssteuerungslisten (ACLs) in Speicherkonten zu verwalten, für die der hierarchische Namespace aktiviert ist.
author: normesta
ms.service: storage
ms.date: 08/26/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 01f23abe3ef06bc43a3f7043f48b75f684a4478e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913469"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Verwenden von .NET zum Verwalten von Verzeichnissen, Dateien und Zugriffssteuerungslisten in Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie .NET zum Erstellen und Verwalten von Verzeichnissen, Dateien und Berechtigungen in Speicherkonten verwenden, für die der hierarchische Namespace aktiviert ist. 

[NuGet-Paket](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [Beispiele](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [API-Referenz](/dotnet/api/azure.storage.files.datalake) | [Zuordnung von Gen1 zu Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [Feedback geben](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Voraussetzungen

> [!div class="checklist"]
> * Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
> * Ein Speicherkonto, für das der hierarchische Namespace aktiviert ist. Befolgen Sie [diese Anleitung](../common/storage-account-create.md) für die Erstellung.

## <a name="set-up-your-project"></a>Einrichten des Projekts

Installieren Sie zunächst das NuGet-Paket [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/).

Weitere Informationen zum Installieren von NuGet-Paketen finden Sie unter [Installieren und Verwalten von Paketen in Visual Studio mit dem NuGet-Paket-Manager](/nuget/consume-packages/install-use-packages-visual-studio).

Fügen Sie dann die folgenden using-Anweisungen am Anfang Ihrer Codedatei ein.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Herstellen einer Verbindung mit dem Konto

Wenn Sie die Codeausschnitte in diesem Artikel verwenden möchten, müssen Sie eine [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient)-Instanz erstellen, die das Speicherkonto darstellt. 

### <a name="connect-by-using-an-account-key"></a>Herstellen einer Verbindung per Kontoschlüssel

Dies ist die einfachste Möglichkeit, eine Verbindung mit einem Konto herzustellen. 

In diesem Beispiel wird eine [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient)-Instanz mithilfe eines Kontoschlüssels erstellt.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

### <a name="connect-by-using-azure-active-directory-ad"></a>Herstellen einer Verbindung mit Azure Active Directory (AD)

Sie können die [Azure-Identitätsclientbibliothek für .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) verwenden, um Ihre Anwendung bei Azure AD zu authentifizieren.

In diesem Beispiel wird eine [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient)-Instanz mithilfe einer Client-ID, eines Clientgeheimnisses und einer Mandanten-ID erstellt.  Informationen zum Abrufen dieser Werte finden Sie unter [Abrufen eines Tokens von Azure AD zum Autorisieren von Anforderungen von einer Clientanwendung](../common/storage-auth-aad-app.md).

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    string accountName, string clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

```

> [!NOTE]
> Weitere Beispiele finden Sie in der Dokumentation zur [Azure-Identitätsclientbibliothek für .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="create-a-container"></a>Erstellen eines Containers

Ein Container fungiert als Dateisystem für Ihre Dateien. Sie können ein solches System erstellen, indem Sie die Methode [DataLakeServiceClient.createFileSystem](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) aufrufen.

In diesem Beispiel wird ein Container namens `my-file-system` erstellt. 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

Erstellen Sie eine Verzeichnisreferenz, indem Sie die Methode [DataLakeFileSystemClient.CreateDirectoryAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) aufrufen.

In diesem Beispiel wird einem Container ein Verzeichnis namens `my-directory` und anschließend ein Unterverzeichnis namens `my-subdirectory` hinzugefügt. 

```cs
public async Task<DataLakeDirectoryClient> CreateDirectory
    (DataLakeServiceClient serviceClient, string fileSystemName)
{
    DataLakeFileSystemClient fileSystemClient =
        serviceClient.GetFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        await fileSystemClient.CreateDirectoryAsync("my-directory");

    return await directoryClient.CreateSubDirectoryAsync("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Umbenennen oder Verschieben eines Verzeichnisses

Sie können ein Verzeichnis umbenennen oder löschen, indem Sie die Methode [DataLakeDirectoryClient.RenameAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) aufrufen. Übergeben Sie den Pfad des gewünschten Verzeichnisses als Parameter. 

Im folgenden Beispiel wird ein Unterverzeichnis in `my-subdirectory-renamed` umbenannt.

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

Im folgenden Beispiel wird ein Verzeichnis namens `my-subdirectory-renamed` in ein Unterverzeichnis eines Verzeichnisses namens `my-directory-2` verschoben. 

```cs
public async Task<DataLakeDirectoryClient> MoveDirectory
    (DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
            fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory-renamed");

    return await directoryClient.RenameAsync("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Löschen eines Verzeichnisses

Sie können ein Verzeichnis löschen, indem Sie die Methode [DataLakeDirectoryClient.Delete](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) aufrufen.

In diesem Beispiel wird das Verzeichnis `my-directory` gelöscht.  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="upload-a-file-to-a-directory"></a>Hochladen einer Datei in ein Verzeichnis

Erstellen Sie zunächst eine Dateireferenz im Zielverzeichnis, in dem Sie eine Instanz der Klasse [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) erstellen. Laden Sie eine Datei hoch, indem Sie die Methode [DataLakeFileClient.AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) aufrufen. Stellen Sie sicher, dass Sie den Uploadvorgang abschließen, indem Sie die Methode [DataLakeFileClient.FlushAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) aufrufen.

In diesem Beispiel wird eine Textdatei in das Verzeichnis `my-directory` hochgeladen.    

```cs
public async Task UploadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = await directoryClient.CreateFileAsync("uploaded-file.txt");

    FileStream fileStream = 
        File.OpenRead("C:\\file-to-upload.txt");

    long fileSize = fileStream.Length;

    await fileClient.AppendAsync(fileStream, offset: 0);

    await fileClient.FlushAsync(position: fileSize);

}
```

> [!TIP]
> Wenn Ihre Datei groß ist, muss Ihr Code mehrere Aufrufe von [DataLakeFileClient.AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) durchführen. Erwägen Sie stattdessen die Verwendung der [DataLakeFileClient.UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_)-Methode. Auf diese Weise können Sie die gesamte Datei in einem einzigen Aufruf hochladen. 
>
> Ein Beispiel finden Sie im nächsten Abschnitt.

## <a name="upload-a-large-file-to-a-directory"></a>Hochladen einer großen Datei in ein Verzeichnis

Verwenden Sie die [DataLakeFileClient.UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_)-Methode, um große Dateien hochzuladen, ohne die [DataLakeFileClient.AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)-Methode mehrfach aufrufen zu müssen.

```cs
public async Task UploadFileBulk(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.GetFileClient("uploaded-file.txt");

    FileStream fileStream =
        File.OpenRead("C:\\file-to-upload.txt");

    await fileClient.UploadAsync(fileStream);

}

```

## <a name="download-from-a-directory"></a>Herunterladen aus einem Verzeichnis 

Erstellen Sie zunächst eine [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient)-Instanz, die die herunterzuladende Datei darstellt. Verwenden Sie die Methode [DataLakeFileClient.ReadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync), und analysieren Sie den Rückgabewert, um ein [Stream](/dotnet/api/system.io.stream)-Objekt zu erhalten. Verwenden Sie eine beliebige .NET-API für die Dateiverarbeitung, um Bytes aus dem Datenstrom in einer Datei zu speichern. 

Im folgenden Beispiel werden [BinaryReader](/dotnet/api/system.io.binaryreader) und [FileStream](/dotnet/api/system.io.filestream) verwendet, um Bytes in einer Datei zu speichern. 

```cs
public async Task DownloadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("my-image.png");

    Response<FileDownloadInfo> downloadResponse = await fileClient.ReadAsync();

    BinaryReader reader = new BinaryReader(downloadResponse.Value.Content);

    FileStream fileStream = 
        File.OpenWrite("C:\\my-image-downloaded.png");

    int bufferSize = 4096;

    byte[] buffer = new byte[bufferSize];

    int count;

    while ((count = reader.Read(buffer, 0, buffer.Length)) != 0)
    {
        fileStream.Write(buffer, 0, count);
    }

    await fileStream.FlushAsync();

    fileStream.Close();
}
```

## <a name="list-directory-contents"></a>Auflisten des Verzeichnisinhalts

Listen Sie den Verzeichnisinhalt auf, indem Sie die Methode [FileSystemClient.GetPathsAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) aufrufen und die Ergebnisse anschließend durchlaufen.

Im folgenden Beispiel werden die Namen aller Dateien ausgegeben, die sich in einem Verzeichnis namens `my-directory` befinden.

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.GetPathsAsync("my-directory").GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    PathItem item = enumerator.Current;

    while (item != null)
    {
        Console.WriteLine(item.Name);

        if (!await enumerator.MoveNextAsync())
        {
            break;
        }
                
        item = enumerator.Current;
    }

}
```

## <a name="manage-access-control-lists-acls"></a>Verwalten von Zugriffssteuerungslisten (Access Control Lists, ACLs)

Sie können Zugriffsberechtigungen für Verzeichnisse und Dateien abrufen, festlegen und aktualisieren.

> [!NOTE]
> Wenn Sie Azure Active Directory (Azure AD) verwenden, um den Zugriff zu autorisieren, sollten Sie sicherstellen, dass Ihrem Sicherheitsprinzipal die Rolle [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen wurde. Weitere Informationen dazu, wie ACL-Berechtigungen angewandt werden und wie sich Änderungen daran auswirken, finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="manage-a-directory-acl"></a>Verwalten einer Zugriffssteuerungsliste eines Verzeichnisses

Rufen Sie die Zugriffssteuerungsliste (Access Control List, ACL) eines Verzeichnisses ab, indem Sie die Methode [DataLakeDirectoryClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) aufrufen, und legen Sie die ACL durch Aufrufen der Methode [DataLakeDirectoryClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) fest.

> [!NOTE]
> Wenn in Ihrer Anwendung der Zugriff mithilfe von Azure Active Directory (Azure AD) autorisiert wird, müssen Sie sicherstellen, dass dem Sicherheitsprinzipal, mit dem in der Anwendung der Zugriff autorisiert wird, die [Rolle „Besitzer von Speicherblobdaten“](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen ist. Weitere Informationen dazu, wie ACL-Berechtigungen angewandt werden und wie sich Änderungen daran auswirken, finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md). 

In diesem Beispiel wird die ACL des Verzeichnisses `my-directory` abgerufen und dann festgelegt. Mit der Zeichenfolge `user::rwx,group::r-x,other::rw-` werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen lediglich Lese- und Schreibzugriff gewährt wird.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    foreach (var item in directoryAccessControl.AccessControlList)
    {
        Console.WriteLine(item.ToString());
    }


    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    directoryClient.SetAccessControlList(accessControlList);

}

```

Sie können auch die ACL des Stammverzeichnisses eines Containers abrufen und festlegen. Übergeben Sie zum Abrufen des Stammverzeichnisses eine leere Zeichenfolge (`""`) an die Methode [DataLakeFileSystemClient.GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient).

### <a name="manage-a-file-acl"></a>Verwalten einer Zugriffssteuerungsliste einer Datei

Rufen Sie die Zugriffssteuerungsliste (Access Control List, ACL) einer Datei ab, indem Sie die Methode [DataLakeFileClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) aufrufen, und legen Sie die ACL durch Aufrufen der Methode [DataLakeFileClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) fest.

> [!NOTE]
> Wenn in Ihrer Anwendung der Zugriff mithilfe von Azure Active Directory (Azure AD) autorisiert wird, müssen Sie sicherstellen, dass dem Sicherheitsprinzipal, mit dem in der Anwendung der Zugriff autorisiert wird, die [Rolle „Besitzer von Speicherblobdaten“](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen ist. Weitere Informationen dazu, wie ACL-Berechtigungen angewandt werden und wie sich Änderungen daran auswirken, finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md). 

Im folgenden Beispiel wird die ACL der Datei `my-file.txt` abgerufen und dann festgelegt. Mit der Zeichenfolge `user::rwx,group::r-x,other::rw-` werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen lediglich Lese- und Schreibzugriff gewährt wird.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    foreach (var item in FileAccessControl.AccessControlList)
    {
        Console.WriteLine(item.ToString());
    }

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    fileClient.SetAccessControlList(accessControlList);
}
```

### <a name="set-an-acl-recursively"></a>Rekursives Festlegen einer Zugriffssteuerungsliste

Sie können Zugriffssteuerungslisten rekursiv für die vorhandenen untergeordneten Elemente eines übergeordneten Verzeichnisses hinzufügen, aktualisieren und entfernen, ohne diese Änderungen für jedes untergeordnete Element einzeln vornehmen zu müssen. Weitere Informationen finden Sie unter [Festlegen von Zugriffssteuerungslisten (ACLs) für Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Weitere Informationen

* [API-Referenzdokumentation](/dotnet/api/azure.storage.files.datalake)
* [Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [Beispiele](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Zuordnung von Gen1 zu Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Bekannte Probleme](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Feedback geben](https://github.com/Azure/azure-sdk-for-net/issues)