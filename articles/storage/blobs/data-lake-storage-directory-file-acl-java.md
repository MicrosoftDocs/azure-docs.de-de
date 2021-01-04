---
title: Azure Data Lake Storage Gen2 Java SDK für Dateien und Zugriffssteuerungslisten
description: Verwenden Sie Azure Storage-Bibliotheken für Java, um Verzeichnisse, Dateien und Zugriffssteuerungslisten (ACLs) in Speicherkonten zu verwalten, für die der hierarchische Namespace aktiviert ist.
author: normesta
ms.service: storage
ms.date: 09/10/2020
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: f6e8219f744a91628f9860f0af133c07eddb4253
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913384"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Verwenden von Java zum Verwalten von Verzeichnissen, Dateien und Zugriffssteuerungslisten in Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie Java zum Erstellen und Verwalten von Verzeichnissen, Dateien und Berechtigungen in Speicherkonten verwenden, für die der hierarchische Namespace aktiviert ist. 

[Maven-Paket](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [Beispiele](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API-Referenz](/java/api/overview/azure/storage-file-datalake-readme) | [Zuordnung von Gen1 zu Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Feedback geben](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Voraussetzungen

> [!div class="checklist"]
> * Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
> * Ein Speicherkonto, für das der hierarchische Namespace aktiviert ist. Befolgen Sie [diese Anleitung](../common/storage-account-create.md) für die Erstellung.

## <a name="set-up-your-project"></a>Einrichten des Projekts

Öffnen Sie zunächst [diese Seite](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake), und suchen Sie die neueste Version der Java-Bibliothek. Öffnen Sie dann die Datei *pom.xml* in Ihrem Text-Editor. Fügen Sie ein Abhängigkeitselement hinzu, das auf diese Version verweist.

Wenn Sie beabsichtigen, die Clientanwendung mithilfe von Azure Active Directory (AD) zu authentifizieren, fügen Sie der Azure-Geheimnisclientbibliothek eine Abhängigkeit hinzu. Informationen dazu finden Sie unter [Hinzufügen des Pakets für die Geheimnisclientbibliothek zu Ihrem Projekt](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Fügen Sie als Nächstes Ihrer Codedatei die folgenden Import-Anweisungen hinzu.

```java
import com.azure.core.credential.TokenCredential;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.RolePermissions;
```

## <a name="connect-to-the-account"></a>Herstellen einer Verbindung mit dem Konto 

Wenn Sie die Codeausschnitte in diesem Artikel verwenden möchten, müssen Sie eine **DataLakeServiceClient**-Instanz erstellen, die das Speicherkonto darstellt. 

### <a name="connect-by-using-an-account-key"></a>Herstellen einer Verbindung per Kontoschlüssel

Dies ist die einfachste Möglichkeit, eine Verbindung mit einem Konto herzustellen. 

In diesem Beispiel wird eine **DataLakeServiceClient**-Instanz mithilfe eines Kontoschlüssels erstellt.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Herstellen einer Verbindung mit Azure Active Directory (Azure AD)

Sie können die [Azure-Identitätsclientbibliothek für Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) verwenden, um Ihre Anwendung bei Azure AD zu authentifizieren.

In diesem Beispiel wird eine **DataLakeServiceClient**-Instanz mithilfe einer Client-ID, eines Clientgeheimnisses und einer Mandanten-ID erstellt.  Informationen zum Abrufen dieser Werte finden Sie unter [Abrufen eines Tokens von Azure AD zum Autorisieren von Anforderungen von einer Clientanwendung](../common/storage-auth-aad-app.md).

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> Weitere Beispiele finden Sie in der Dokumentation zur [Azure-Identitätsclientbibliothek für Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity).


## <a name="create-a-container"></a>Erstellen eines Containers

Ein Container fungiert als Dateisystem für Ihre Dateien. Sie können eines erstellen, indem Sie die Methode **DataLakeServiceClient.createFileSystem** aufrufen.

In diesem Beispiel wird ein Container namens `my-file-system` erstellt. 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

Erstellen Sie eine Verzeichnisreferenz, indem Sie die Methode **DataLakeFileSystemClient.createDirectory** aufrufen.

In diesem Beispiel wird einem Container ein Verzeichnis namens `my-directory` und anschließend ein Unterverzeichnis namens `my-subdirectory` hinzugefügt. 

```java
static public DataLakeDirectoryClient CreateDirectory
(DataLakeServiceClient serviceClient, String fileSystemName){
    
    DataLakeFileSystemClient fileSystemClient =
    serviceClient.getFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.createDirectory("my-directory");

    return directoryClient.createSubDirectory("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Umbenennen oder Verschieben eines Verzeichnisses

Rufen Sie die Methode **DataLakeDirectoryClient.rename** auf, um ein Verzeichnis umzubenennen oder zu verschieben. Übergeben Sie den Pfad des gewünschten Verzeichnisses als Parameter. 

Im folgenden Beispiel wird ein Unterverzeichnis in `my-subdirectory-renamed` umbenannt.

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-subdirectory-renamed");
}
```

Im folgenden Beispiel wird ein Verzeichnis namens `my-subdirectory-renamed` in ein Unterverzeichnis eines Verzeichnisses namens `my-directory-2` verschoben. 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Löschen eines Verzeichnisses

Sie können ein Verzeichnis löschen, indem Sie die Methode **DataLakeDirectoryClient.deleteWithResponse** aufrufen.

In diesem Beispiel wird das Verzeichnis `my-directory` gelöscht.   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="upload-a-file-to-a-directory"></a>Hochladen einer Datei in ein Verzeichnis

Erstellen Sie zunächst eine Dateireferenz im Zielverzeichnis, in dem Sie eine Instanz der Klasse **DataLakeFileClient** erstellen. Laden Sie eine Datei hoch, indem Sie die Methode **DataLakeFileClient.append** aufrufen. Stellen Sie sicher, dass Sie den Uploadvorgang abschließen, indem Sie die Methode **DataLakeFileClient.FlushAsync** aufrufen.

Im folgenden Beispiel wird eine Textdatei in ein Verzeichnis namens `my-directory` hochgeladen.

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new BufferedInputStream(new FileInputStream(file));

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

> [!TIP]
> Wenn Ihre Datei groß ist, muss Ihr Code mehrere Aufrufe der **DataLakeFileClient.append**-Methode durchführen. Erwägen Sie stattdessen die Verwendung der **DataLakeFileClient.uploadFromFile**-Methode. Auf diese Weise können Sie die gesamte Datei in einem einzigen Aufruf hochladen. 
>
> Ein Beispiel finden Sie im nächsten Abschnitt.

## <a name="upload-a-large-file-to-a-directory"></a>Hochladen einer großen Datei in ein Verzeichnis

Verwenden Sie die **DataLakeFileClient.uploadFromFile**-Methode, um große Dateien hochzuladen, ohne die **DataLakeFileClient.append**-Methode mehrfach aufrufen zu müssen.

```java
static public void UploadFileBulk(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.getFileClient("uploaded-file.txt");

    fileClient.uploadFromFile("C:\\mytestfile.txt");

    }

```

## <a name="download-from-a-directory"></a>Herunterladen aus einem Verzeichnis

Erstellen Sie zunächst eine **DataLakeFileClient**-Instanz, die die herunterzuladende Datei darstellt. Verwenden Sie die Methode **DataLakeFileClient.read**, um die Datei zu lesen. Verwenden Sie eine beliebige .NET-API für die Dateiverarbeitung, um Bytes aus dem Datenstrom in einer Datei zu speichern. 

```java
static public void DownloadFile(DataLakeFileSystemClient fileSystemClient)
    throws FileNotFoundException, java.io.IOException{

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    File file = new File("C:\\downloadedFile.txt");

    OutputStream targetStream = new FileOutputStream(file);

    fileClient.read(targetStream);

    targetStream.close();
      
}

```

## <a name="list-directory-contents"></a>Auflisten des Verzeichnisinhalts

Im folgenden Beispiel werden die Namen aller Dateien ausgegeben, die sich in einem Verzeichnis namens `my-directory` befinden.

```java
static public void ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient){
        
    ListPathsOptions options = new ListPathsOptions();
    options.setPath("my-directory");
     
    PagedIterable<PathItem> pagedIterable = 
    fileSystemClient.listPaths(options, null);

    java.util.Iterator<PathItem> iterator = pagedIterable.iterator();
       
    PathItem item = iterator.next();

    while (item != null)
    {
        System.out.println(item.getName());


        if (!iterator.hasNext())
        {
            break;
        }
            
        item = iterator.next();
    }

}
```

## <a name="manage-access-control-lists-acls"></a>Verwalten von Zugriffssteuerungslisten (Access Control Lists, ACLs)

Sie können Zugriffsberechtigungen für Verzeichnisse und Dateien abrufen, festlegen und aktualisieren.

> [!NOTE]
> Wenn Sie Azure Active Directory (Azure AD) verwenden, um Zugriff zu autorisieren, stellen Sie sicher, dass Ihrem Sicherheitsprinzipal die Rolle [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen wurde. Weitere Informationen dazu, wie ACL-Berechtigungen angewandt werden und wie sich Änderungen daran auswirken, finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="manage-a-directory-acl"></a>Verwalten einer Zugriffssteuerungsliste eines Verzeichnisses

Im folgenden Beispiel wird die ACL (Zugriffssteuerungsliste) eines Verzeichnisses namens `my-directory` abgerufen und dann festgelegt. Im folgenden Beispiel werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen lediglich Lesezugriff gewährt wird.

> [!NOTE]
> Wenn in Ihrer Anwendung der Zugriff mithilfe von Azure Active Directory (Azure AD) autorisiert wird, müssen Sie sicherstellen, dass dem Sicherheitsprinzipal, der in der Anwendung zum Autorisieren des Zugriffs verwendet wird, die [Rolle „Besitzer von Speicherblobdaten“](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen ist. Weitere Informationen dazu, wie ACL-Berechtigungen angewandt werden und wie sich Änderungen daran auswirken, finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);
  
    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);
  
    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);
  
    PathPermissions permissions = new PathPermissions();
  
    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

Sie können auch die ACL des Stammverzeichnisses eines Containers abrufen und festlegen. Übergeben Sie zum Abrufen des Stammverzeichnisses eine leere Zeichenfolge (`""`) an die Methode **DataLakeFileSystemClient.getDirectoryClient**.

### <a name="manage-a-file-acl"></a>Verwalten einer Zugriffssteuerungsliste einer Datei

Im folgenden Beispiel wird die ACL einer Datei namens `upload-file.txt` abgerufen und dann festgelegt. Im folgenden Beispiel werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen lediglich Lesezugriff gewährt wird.

> [!NOTE]
> Wenn in Ihrer Anwendung der Zugriff mithilfe von Azure Active Directory (Azure AD) autorisiert wird, müssen Sie sicherstellen, dass dem Sicherheitsprinzipal, der in der Anwendung zum Autorisieren des Zugriffs verwendet wird, die [Rolle „Besitzer von Speicherblobdaten“](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen ist. Weitere Informationen dazu, wie ACL-Berechtigungen angewandt werden und wie sich Änderungen daran auswirken, finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

```java
static public void ManageFileACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    PathAccessControl fileAccessControl =
        fileClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = fileAccessControl.getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
           
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);

    PathPermissions permissions = new PathPermissions();

    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

### <a name="set-an-acl-recursively"></a>Rekursives Festlegen einer Zugriffssteuerungsliste

Sie können Zugriffssteuerungslisten rekursiv für die vorhandenen untergeordneten Elemente eines übergeordneten Verzeichnisses hinzufügen, aktualisieren und entfernen, ohne diese Änderungen für jedes untergeordnete Element einzeln vornehmen zu müssen. Weitere Informationen finden Sie unter [Festlegen von Zugriffssteuerungslisten (ACLs) für Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Weitere Informationen

* [API-Referenzdokumentation](/java/api/overview/azure/storage-file-datalake-readme)
* [Maven-Paket](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Beispiele](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Zuordnung von Gen1 zu Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Bekannte Probleme](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Feedback geben](https://github.com/Azure/azure-sdk-for-java/issues)