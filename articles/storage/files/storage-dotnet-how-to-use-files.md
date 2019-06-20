---
title: Entwickeln für Azure Files mit .NET | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie .NET-Anwendungen und -Dienste entwickeln, die Azure Files zum Speichern von Dateidaten verwenden.
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/22/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 38bafdb4753b41a9c8acd599e6b7215e1777c6cd
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65779464"
---
# <a name="develop-for-azure-files-with-net"></a>Entwickeln für Azure Files mit .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Dieses Tutorial veranschaulicht die grundlegende Verwendung von .NET bei der Entwicklung von Anwendungen, die [Azure Files](storage-files-introduction.md) zum Speichern von Dateidaten verwenden. In diesem Tutorial wird eine einfache Konsolenanwendung erstellt, mit der grundlegende Aktionen mit .NET und Azure Files ausgeführt werden:

* Abrufen der Inhalte einer Datei
* Festlegen des Kontingents (maximale Größe) für die Dateifreigabe
* Erstellen eines SAS-Schlüssels (Shared Access Signature) für eine Datei, die eine für die Freigabe definierte SAS-Richtlinie verwendet
* Kopieren einer Datei in eine andere Datei im gleichen Speicherkonto
* Kopieren einer Datei in ein Blob im gleichen Speicherkonto
* Verwenden von Azure-Speichermetriken für die Problembehandlung

Weitere Informationen zu Azure Files finden Sie in der [Einführung in Azure Files](storage-files-introduction.md).

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Grundlegendes zu den .NET-APIs

Azure Files bietet zwei allgemeine Ansätze für Clientanwendungen: Server Message Block (SMB) und REST. Innerhalb von .NET werden diese Ansätze von den APIs `System.IO` und `WindowsAzure.Storage` abstrahiert.

API | Einsatzgebiete | Notizen
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | Ihre Anwendung: <ul><li>Muss Dateien über SMB lesen/schreiben</li><li>Wird auf einem Gerät ausgeführt, das über Port 445 Zugriff auf Ihr Azure Files-Konto hat</li><li>Muss keine Verwaltungseinstellungen der Dateifreigabe verwalten</li></ul> | Die Codierung von Datei-E/A-Vorgängen mit Azure Files über SMB entspricht im Allgemeinen der Codierung von E/A-Vorgängen mit einer beliebigen Netzwerkdateifreigabe oder einem beliebigen lokalen Speichergerät. Eine Einführung in eine Reihe von Funktionen in .NET (einschließlich Datei-E/A) finden Sie in [diesem Tutorial](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter).
[Microsoft.Azure.Storage.File](https://docs.microsoft.com/dotnet/api/overview/azure/storage#client-library) | Ihre Anwendung: <ul><li>Kann aufgrund von Firewall- oder ISP-Einschränkungen nicht über SMB an Port 445 auf Azure Files zugreifen</li><li>Benötigt Verwaltungsfunktionen, etwa die Möglichkeit, das Kontingent einer Dateifreigabe festzulegen oder eine SAS (Shared Access Signature) zu erstellen</li></ul> | Dieser Artikel veranschaulicht die Verwendung von `Microsoft.Azure.Storage.File` für Datei-E/A-Vorgänge über REST (anstelle von SMB) sowie die Verwaltung der Dateifreigabe.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Erstellen der Konsolenanwendung und Erhalten der Assembly
Erstellen Sie in Visual Studio eine neue Windows-Konsolenanwendung. In den folgenden Schritten wird veranschaulicht, wie Sie eine Konsolenanwendung in Visual Studio 2017 erstellen. Die Schritte in anderen Versionen von Visual Studio sind aber ähnlich.

1. Wählen Sie **Datei** > **Neu** > **Projekt**.
2. Wählen Sie **Installiert** > **Vorlagen** > **Visual C#**  > **Klassischer Windows-Desktop**.
3. Wählen Sie **Konsolen-App (.NET Framework)** .
4. Geben Sie im Feld **Name:** einen Namen für Ihre Anwendung ein.
5. Klicken Sie auf **OK**.

Alle Codebeispiele in diesem Tutorial können in der Datei `Program.cs` Ihrer Konsolenanwendung der `Main()`-Methode hinzugefügt werden.

Sie können die Azure Storage-Clientbibliothek in jeder Art von .NET-Anwendung nutzen, z.B. einem Azure-Clouddienst oder einer Azure-Web-App, einer Desktopanwendung oder einer mobilen Anwendung. In diesem Leitfaden verwenden wir der Einfachheit halber eine Konsolenanwendung.

## <a name="use-nuget-to-install-the-required-packages"></a>Verwenden von NuGet zum Installieren der erforderlichen Pakete
Es gibt zwei Pakete, auf die Sie in Ihrem Projekt für dieses Tutorial verweisen müssen:

* [Allgemeine Microsoft Azure Storage-Bibliothek für .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): Mit diesem Paket erhalten Sie programmgesteuerten Zugriff auf allgemeine Ressourcen in Ihrem Speicherkonto.
* [Microsoft Azure Storage Blob-Bibliothek für .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/): Mit diesem Paket erhalten Sie programmgesteuerten Zugriff auf Blobressourcen in Ihrem Speicherkonto.
* [Microsoft Azure Configuration Manager-Bibliothek für .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): Mit diesem Paket wird eine Klasse zum Analysieren einer Verbindungszeichenfolge in einer Konfigurationsdatei bereitgestellt. Dies gilt unabhängig davon, wo die Anwendung ausgeführt wird.

Sie können NuGet verwenden, um beide Pakete zu erhalten. Folgen Sie diesen Schritten:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.
2. Suchen Sie online nach „WindowsAzure.Storage“, und klicken Sie auf **Installieren** , um die Storage Client Library und die dazugehörigen Abhängigkeiten zu installieren.
3. Suchen Sie online nach „WindowsAzure.ConfigurationManager“, und klicken Sie auf **Installieren**, um Azure Configuration Manager zu installieren.

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Speichern der Anmeldeinformationen Ihres Speicherkontos in der Datei „app.config“
Als Nächstes speichern Sie Ihre Anmeldeinformationen in der Datei „app.config“ des Projekts. Bearbeiten Sie die Datei „app.config“ ähnlich wie im folgenden Beispiel, indem Sie `myaccount` durch den Namen Ihres Speicherkontos und `mykey` durch den Schlüssel Ihres Speicherkontos ersetzen.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> Die neueste Version des Azure-Speicheremulators unterstützt Azure Files nicht. Die Verbindungszeichenfolge muss auf ein Azure Storage-Konto in der Cloud verweisen, um mit Azure Files arbeiten zu können.

## <a name="add-using-directives"></a>Hinzufügen von using-Direktiven
Öffnen Sie die Datei `Program.cs` über den Projektmappen-Explorer, und fügen Sie die folgenden using-Direktiven am Anfang der Datei hinzu.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>Programmgesteuertes Zugreifen auf die Dateifreigabe
Fügen Sie als Nächstes den folgenden Code der `Main()`-Methode (nach dem oben angegebenen Code) hinzu, um die Verbindungszeichenfolge abzurufen. Dieser Code ruft einen Verweis auf die zuvor erstellte Datei ab und gibt dessen Inhalt im Konsolenfenster an.

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

## <a name="set-the-maximum-size-for-a-file-share"></a>Festlegen der maximalen Größe für eine Dateifreigabe
Ab Version 5.x der Azure Storage-Clientbibliothek können Sie das Kontingent (oder die maximale Größe) für eine Dateifreigabe in Gigabytes festlegen. Sie können auch überprüfen, wie viele Daten sich aktuell auf der Freigabe befinden.

Durch Festlegen des Kontingents für eine Freigabe können Sie die Gesamtgröße der Dateien einschränken, die in der Freigabe gespeichert werden. Überschreitet die Gesamtgröße der Dateien in der Freigabe das für die Freigabe festgelegte Kontingent, können die Clients weder die Größe von vorhandenen Dateien ändern noch neue Dateien erstellen – es sei denn, diese sind leer.

Das folgende Beispiel zeigt, wie Sie die aktuelle Nutzung einer Freigabe überprüfen und das Kontingent für die Freigabe festlegen.

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

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generieren einer SAS für eine Datei oder Dateifreigabe
Ab Version 5.x der Azure Storage-Clientbibliothek können Sie eine SAS (Shared Access Signature) für eine Dateifreigabe oder für eine einzelne Datei generieren. Sie können auch eine SAS-Richtlinie für eine Dateifreigabe erstellen, um Shared Access Signatures zu verwalten. Erstellen einer SAS-Richtlinie wird empfohlen, weil sie eine Möglichkeit bietet, die SAS zu widerrufen, wenn diese gefährdet sein sollte.

Im folgenden Beispiel wird eine SAS-Richtlinie für eine Freigabe erstellt und die Richtlinie dann dazu verwendet, die Einschränkungen für eine SAS für eine Datei in der Freigabe bereitzustellen.

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

    // Create a new shared access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
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

Weitere Informationen zum Erstellen und Verwenden von Shared Access Signatures finden Sie unter [Verwenden von Shared Access Signatures (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="copy-files"></a>Kopieren von Dateien
Ab Version 5.x der Azure Storage-Clientbibliothek können Sie eine Datei in eine andere Datei, eine Datei in ein Blob oder ein Blob in eine Datei kopieren. In den nächsten Abschnitten wird demonstriert, wie diese Kopiervorgänge programmgesteuert ausgeführt werden.

Sie können auch AzCopy verwenden, um eine Datei in eine andere oder ein BLOB in eine Datei oder umgekehrt zu kopieren. Siehe [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Wenn Sie ein BLOB in eine Datei oder eine Datei in ein BLOB kopieren, müssen Sie eine SAS verwenden, um den Zugriff auf das Quellobjekt zu autorisieren. Dies gilt selbst dann, wenn Sie innerhalb desselben Speicherkontos kopieren.
> 
> 

**Kopieren einer Datei in eine andere Datei:** Im folgenden Beispiel wird eine Datei in eine andere Datei in der gleichen Freigabe kopiert. Weil bei diesem Kopiervorgang zwischen Dateien im selben Speicherkonto kopiert wird, können Sie die Gemeinsam verwendeter Schlüssel-Authentifizierung verwenden, um den Kopiervorgang auszuführen.

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

**Kopieren einer Datei in ein Blob:** Im folgenden Beispiel wird eine Datei erstellt und in ein Blob im gleichen Speicherkonto kopiert. In dem Beispiel wird für die Quelldatei eine SAS erstellt, die der Dienst dazu verwendet, während des Kopiervorgangs den Zugriff auf die Quelldatei zu autorisieren.

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

Auf gleiche Weise können Sie ein BLOB in eine Datei kopieren. Wenn das Quellobjekt ein BLOB ist, erstellen Sie eine SAS, um den Zugriff auf dieses BLOB während des Kopiervorgangs zu autorisieren.

## <a name="share-snapshots"></a>Freigabemomentaufnahmen
Ab Version 8.5 der Azure Storage-Clientbibliothek können Sie eine Freigabemomentaufnahme erstellen. Außerdem können Sie Freigabemomentaufnahmen auflisten, durchsuchen und löschen. Freigabemomentaufnahmen sind schreibgeschützt. Es können also keine Schreibvorgänge für Freigabemomentaufnahmen ausgeführt werden.

**Erstellen von Freigabemomentaufnahmen**

Im folgenden Beispiel wird eine Freigabemomentaufnahme erstellt.

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```
**Auflisten von Freigabemomentaufnahmen**

Im folgenden Beispiel werden die Freigabemomentaufnahmen in einer Freigabe aufgelistet.

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

**Durchsuchen von Dateien und Verzeichnissen in Freigabemomentaufnahmen**

Im folgenden Beispiel werden Dateien und das Verzeichnis in Freigabemomentaufnahmen durchsucht.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

**Auflisten von Freigaben und Freigabemomentaufnahmen sowie Wiederherstellen von Dateifreigaben oder Dateien aus Freigabemomentaufnahmen** 

Die Erstellung einer Momentaufnahme von einer Dateifreigabe ermöglicht die spätere Wiederherstellung einzelner Dateien oder der gesamten Dateifreigabe. 

Zum Wiederherstellen einer Datei aus einer Dateifreigabemomentaufnahme können Sie die Freigabemomentaufnahmen einer Dateifreigabe abfragen. Anschließend können Sie eine Datei abrufen, die zu einer bestimmten Freigabemomentaufnahme gehört, und diese Version direkt für Lese- und Vergleichsvorgänge oder für die Wiederherstellung verwenden.

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


**Löschen von Freigabemomentaufnahmen**

Im folgenden Beispiel wird eine Freigabemomentaufnahme gelöscht.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## <a name="troubleshooting-azure-files-using-metrics"></a>Behandeln von Problemen mit Azure Files mithilfe von Metriken
Azure Storage Analytics unterstützt nun Metriken für Azure Files. Mit Metrikdaten können Sie Anforderungen verfolgen und Probleme diagnostizieren.

Die Metriken für Azure Files können über das [Azure-Portal](https://portal.azure.com) aktiviert werden. Sie können Metriken auch programmgesteuert aktivieren, indem Sie den Vorgang „Set File Service Properties“ über die REST-API oder einen analogen Vorgang in der Speicherclientbibliothek aufrufen.

Im folgenden Codebeispiel wird veranschaulicht, wie Sie die Storage-Clientbibliothek für .NET zum Aktivieren von Metriken für Azure Files verwenden.

Fügen Sie der Datei `Program.cs` zusätzlich zu den obigen Direktiven zuerst die folgenden `using`-Direktiven hinzu:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Beachten Sie, dass bei Azure Blobs, Azure Table und Azure Queue zwar der gemeinsam genutzte `ServiceProperties`-Typ im `Microsoft.Azure.Storage.Shared.Protocol`-Namespace verwendet wird, Azure Files aber seinen eigenen Typ (`FileServiceProperties`) im `Microsoft.Azure.Storage.File.Protocol`-Namespace verwendet. Auf beide Namespaces muss aber im Code verwiesen werden, damit der folgende Code kompiliert werden kann.

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

Umfassende Unterstützung bei der Problembehandlung erhalten Sie auch im [Artikel zur Problembehandlung für Azure Files](storage-troubleshoot-windows-file-connection-problems.md).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Files erhalten Sie über diese Links.

### <a name="conceptual-articles-and-videos"></a>Konzeptionelle Artikel und Videos
* [Azure Files: a frictionless cloud SMB file system for Windows and Linux (Azure Files: ein reibungsloses Cloud-SMB-Dateisystem für Windows und Linux)](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [How to use Azure Files with Linux (Verwenden von Azure Files mit Linux)](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Toolunterstützung für Dateispeicher
* [Verwenden von AzCopy mit Microsoft Azure Storage](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Verwenden der Azure-Befehlszeilenschnittstelle mit Azure-Speicher](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Troubleshooting Azure Files problems (Behandeln von Azure Files-Problemen)](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Verweis
* [Referenz zur Storage-Clientbibliothek für .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Referenz zur REST-API des Dateidiensts](https://msdn.microsoft.com/library/azure/dn167006.aspx)

### <a name="blog-posts"></a>Blogbeiträge
* [Azure Files is now generally available](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/) (Azure Files ist jetzt allgemein verfügbar)
* [Inside Azure Files](https://azure.microsoft.com/blog/inside-azure-file-storage/) (Einblick in Azure Files)
* [Einführung in den Microsoft Azure-Dateidienst](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Beibehalten von Verbindungen zu Microsoft Azure-Dateien](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
