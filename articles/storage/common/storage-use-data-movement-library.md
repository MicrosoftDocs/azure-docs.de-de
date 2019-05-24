---
title: Übertragen von Daten mit der Microsoft Azure Storage Data Movement-Bibliothek | Microsoft-Dokumentation
description: Verwenden Sie die Data Movement-Bibliothek zum Verschieben oder Kopieren von Daten in oder aus Blob- und Dateiinhalten. Kopieren Sie Daten aus lokalen Dateien nach Azure Storage oder innerhalb von bzw. zwischen Speicherkonten. Migrieren Sie Ihre Daten ganz einfach nach Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 09/27/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 8e09e2c33359c94275d9819b335544d15d4c7d78
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790092"
---
# <a name="transfer-data-with-the-microsoft-azure-storage-data-movement-library"></a>Übertragen von Daten mit der Microsoft Azure Storage Data Movement-Bibliothek

## <a name="overview"></a>Übersicht
Die Microsoft Azure Storage Data Movement-Bibliothek ist eine plattformübergreifende Open Source-Bibliothek, die für Hochleistung in den Bereichen Hochladen, Herunterladen und Kopieren von Azure Storage-Blobs und -Dateien entwickelt wurde. Diese Bibliothek ist das wichtigste Framework für die Datenverschiebung, das [AzCopy](../storage-use-azcopy.md) betreibt. Die Data Movement-Bibliothek stellt praktische Methoden bereit, die nicht in unserer herkömmlichen [.NET Azure Storage-Clientbibliothek](../blobs/storage-dotnet-how-to-use-blobs.md) verfügbar sind. Dies beinhaltet die Möglichkeit zum Festlegen der Anzahl paralleler Vorgänge, zum Verfolgen des Übertragungsfortschritts, zum einfachen Fortsetzen einer abgebrochenen Übertragung und vieles mehr.

Diese Bibliothek verwendet auch .NET Core, was bedeutet, dass Sie es beim Erstellen von .NET-Apps für Windows, Linux und macOS verwenden können. Weitere Informationen zu .NET Core finden Sie in der [.NET Core-Dokumentation](https://dotnet.github.io/). Diese Bibliothek funktioniert auch bei herkömmlichen .NET Framework-Apps für Windows.

In diesem Dokument wird erläutert, wie eine .NET Core-Konsolenanwendung erstellt wird, die unter Windows, Linux und macOS ausgeführt wird. Zudem werden die folgenden Szenarien ausgeführt:

- Hochladen von Dateien und Verzeichnissen in Blob Storage
- Definieren der Anzahl paralleler Vorgänge bei der Datenübertragung
- Nachverfolgen des Datenübertragungsfortschritts
- Fortsetzen einer abgebrochenen Datenübertragung
- Kopieren der Datei aus der URL in Blob Storage
- Kopieren aus Blob Storage in Blob Storage

**Erforderliche Komponenten:**

* [Visual Studio Code](https://code.visualstudio.com/)
* Ein [Azure Storage-Konto](storage-quickstart-create-account.md)

> [!NOTE]
> Diese Anleitung setzt voraus, dass Sie sich bereits mit [Azure Storage](https://azure.microsoft.com/services/storage/)auskennen. Andernfalls lesen Sie bitte zunächst die Dokumentation [Einführung in Azure Storage](storage-introduction.md). Vor allem müssen Sie [ein Storage-Konto erstellen](storage-quickstart-create-account.md), um mit der Nutzung der Data Movement-Bibliothek zu beginnen.
>
>

## <a name="setup"></a>Einrichtung

1. Rufen Sie die [.NET Core-Installationsanleitung](https://www.microsoft.com/net/core) auf, um .NET Core zu installieren. Wählen Sie bei der Auswahl Ihrer Umgebung die Befehlszeilenoption.
2. Erstellen Sie über die Befehlszeile ein Verzeichnis für das Projekt. Navigieren Sie zu diesem Verzeichnis, und geben Sie dann `dotnet new console -o <sample-project-name>` ein, um ein C#-Konsolenprojekt zu erstellen.
3. Öffnen Sie dieses Verzeichnis in Visual Studio Code. Dieser Schritt kann schnell über die Befehlszeile ausgeführt werden, indem Sie `code .` in Windows eingeben.
4. Installieren Sie die [C#-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) über Visual Studio Code Marketplace. Starten Sie Visual Studio Code neu.
5. An diesem Punkt sehen Sie zwei Eingabeaufforderungen. Eine dient zum Hinzufügen von „erforderlichen Ressourcen zum Erstellen und Debuggen“. Klicken Sie auf „Ja“. Eine andere Eingabeaufforderung dient zum Wiederherstellen nicht aufgelöster Abhängigkeiten. Klicken Sie auf „Wiederherstellen“.
6. Ändern Sie `launch.json` unter `.vscode`, um ein externes Terminal als Konsole zu verwenden. Diese Einstellung sollte als `"console": "externalTerminal"` gelesen werden.
7. Mit Visual Studio Code können Sie .NET Core-Anwendungen debuggen. Klicken Sie auf `F5`, um die Anwendung auszuführen, und stellen Sie sicher, dass das Setup funktioniert. „Hello World!“ muss auf der Konsole ausgegeben werden.

## <a name="add-data-movement-library-to-your-project"></a>Hinzufügen der Data Movement-Bibliothek zu Ihrem Projekt

1. Fügen Sie die neueste Version der Data Movement-Bibliothek zum `dependencies`-Abschnitt Ihrer `<project-name>.csproj`-Datei hinzu. Zum Zeitpunkt des Verfassens wäre diese Version `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`.
2. Eine Eingabeaufforderung muss angezeigt werden, um das Projekt wiederherzustellen. Klicken Sie auf die Schaltfläche „Wiederherstellen“. Sie können das Projekt auch über die Befehlszeile wiederherstellen, indem Sie den Befehl `dotnet restore` in das Stammverzeichnis des Projekts eingeben.

Ändern Sie `<project-name>.csproj`:

    <Project Sdk="Microsoft.NET.Sdk">

        <PropertyGroup>
            <OutputType>Exe</OutputType>
            <TargetFramework>netcoreapp2.0</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
            <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
            </ItemGroup>
        </Project>

## <a name="set-up-the-skeleton-of-your-application"></a>Einrichten des Grundgerüsts Ihrer Anwendung
Zuerst richten wir den grundlegenden Code der Anwendung ein. Dieser Code verlangt einen Speicherkontonamen und Kontoschlüssel und verwendet diese Anmeldeinformationen zum Erstellen eines `CloudStorageAccount`-Objekts. Dieses Objekt wird verwendet, um in allen Szenarien der Übertragung mit unserem Speicherkonto zu interagieren. Der Code fordert uns auch auf, den Typ des auszuführenden Übertragungsvorgangs zu wählen.

Ändern Sie `Program.cs`:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.Storage.DataMovement;

namespace DMLibSample
{
    public class Program
    {
        public static void Main()
        {
            Console.WriteLine("Enter Storage account name:");
            string accountName = Console.ReadLine();

            Console.WriteLine("\nEnter Storage account key:");
            string accountKey = Console.ReadLine();

            string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + accountName + ";AccountKey=" + accountKey;
            CloudStorageAccount account = CloudStorageAccount.Parse(storageConnectionString);

            ExecuteChoice(account);
        }

        public static void ExecuteChoice(CloudStorageAccount account)
        {
            Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
            int choice = int.Parse(Console.ReadLine());

            if(choice == 1)
            {
                TransferLocalFileToAzureBlob(account).Wait();
            }
            else if(choice == 2)
            {
                TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
            }
            else if(choice == 3)
            {
                TransferUrlToAzureBlob(account).Wait();
            }
            else if(choice == 4)
            {
                TransferAzureBlobToAzureBlob(account).Wait();
            }
        }

        public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
        {

        }

        public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
        {

        }
    }
}
```

## <a name="transfer-local-file-to-azure-blob"></a>Übertragen der lokalen Datei in das Azure-Blob
Fügen Sie die Methoden `GetSourcePath` und `GetBlob` zu `Program.cs` hinzu:

```csharp
public static string GetSourcePath()
{
    Console.WriteLine("\nProvide path for source:");
    string sourcePath = Console.ReadLine();

    return sourcePath;
}

public static CloudBlockBlob GetBlob(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    Console.WriteLine("\nProvide name of new Blob:");
    string blobName = Console.ReadLine();
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    return blob;
}
```

Ändern Sie die `TransferLocalFileToAzureBlob`-Methode:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    await TransferManager.UploadAsync(localFilePath, blob);
    Console.WriteLine("\nTransfer operation complete.");
    ExecuteChoice(account);
}
```

Dieser Code fragt uns nach dem Pfad zu einer lokalen Datei, dem Namen eines neuen oder vorhandenen Containers und dem Namen eines neuen Blobs. Die `TransferManager.UploadAsync`-Methode führt das Hochladen mithilfe dieser Informationen aus.

Klicken Sie auf `F5`, um die Anwendung auszuführen. Sie können überprüfen, ob der Upload erfolgt ist, indem Sie Ihr Storage-Konto mit dem [Microsoft Azure Storage-Explorer](https://storageexplorer.com/) anzeigen.

## <a name="set-number-of-parallel-operations"></a>Festlegen der Anzahl paralleler Vorgänge
Eine großartige Funktion der Data Movement-Bibliothek ist die Möglichkeit, die Anzahl der parallelen Vorgänge festzulegen, um den Durchsatz bei der Datenübertragung zu erhöhen. Standardmäßig ist die Anzahl der parallelen Vorgänge in der Data Movement-Bibliothek auf 8 * das Anzahl von Kernen im Computer festgelegt.

Beachten Sie, dass viele parallele Vorgänge in einer Umgebung mit geringer Bandbreite die Netzwerkverbindung überlasten können, wodurch verhindert wird, dass die Vorgänge vollständig abgeschlossen werden. Sie müssen mit dieser Einstellung experimentieren, um zu ermitteln, was auf Grundlage der verfügbaren Netzwerkbandbreite am besten funktioniert.

Fügen Sie Code hinzu, der das Festlegen der Anzahl paralleler Vorgänge ermöglicht. Fügen Sie zudem Code hinzu, der die Dauer der Übertragung festlegt.

Fügen Sie eine `SetNumberOfParallelOperations`-Methode zu `Program.cs` hinzu:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Ändern Sie die `ExecuteChoice`-Methode, um `SetNumberOfParallelOperations` zu verwenden:

```csharp
public static void ExecuteChoice(CloudStorageAccount account)
{
    Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
    int choice = int.Parse(Console.ReadLine());

    SetNumberOfParallelOperations();

    if(choice == 1)
    {
        TransferLocalFileToAzureBlob(account).Wait();
    }
    else if(choice == 2)
    {
        TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
    }
    else if(choice == 3)
    {
        TransferUrlToAzureBlob(account).Wait();
    }
    else if(choice == 4)
    {
        TransferAzureBlobToAzureBlob(account).Wait();
    }
}
```

Ändern Sie die `TransferLocalFileToAzureBlob`-Methode, um einen Timer zu verwenden:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="track-transfer-progress"></a>Nachverfolgen des Übertragungsfortschritts
Es ist gut zu wissen, wie lange unsere Datenübertragung gedauert hat. Es wäre allerdings noch besser, den Fortschritt unserer Übertragung *während* des Übertragungsvorgangs sehen zu können. Für dieses Szenario müssen wir ein `TransferContext`-Objekt erstellen. Das `TransferContext`-Objekt ist in zwei Formen verfügbar: `SingleTransferContext` und `DirectoryTransferContext`. Ersteres dient zur Übertragung einer einzelnen Datei (jetzt ausgeführte Aktion). Letzteres dient zur Übertragung eines Dateiverzeichnisses (wird später hinzugefügt).

Fügen Sie die Methoden `GetSingleTransferContext` und `GetDirectoryTransferContext` zu `Program.cs` hinzu:

```csharp
public static SingleTransferContext GetSingleTransferContext(TransferCheckpoint checkpoint)
{
    SingleTransferContext context = new SingleTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}

public static DirectoryTransferContext GetDirectoryTransferContext(TransferCheckpoint checkpoint)
{
    DirectoryTransferContext context = new DirectoryTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}
```

Ändern Sie die `TransferLocalFileToAzureBlob`-Methode, um `GetSingleTransferContext` zu verwenden:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    Console.WriteLine("\nTransfer started...\n");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob, null, context);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="resume-a-canceled-transfer"></a>Fortsetzen einer abgebrochenen Übertragung
Eine weitere praktische Funktion der Data Movement-Bibliothek ist die Fortsetzung einer abgebrochenen Übertragung. Fügen Sie Code hinzu, der das vorübergehende Abbrechen der Übertragung durch Eingabe von `c` ermöglicht, und setzen Sie die Übertragung dann drei Sekunden später fort.

Ändern Sie `TransferLocalFileToAzureBlob`:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.UploadAsync(localFilePath, blob, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadAsync(localFilePath, blob, null, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Bis jetzt wurde der `checkpoint`-Wert immer auf `null` gesetzt. Wenn wir die Übertragung jetzt abbrechen, rufen wir den letzten Prüfpunkt unserer Übertragung ab und verwenden diesen neuen Prüfpunkt dann in unserem Übertragungskontext.

## <a name="transfer-local-directory-to-azure-blob-directory"></a>Übertragen eines lokalen Verzeichnisses in ein Azure-Blobverzeichnis
Es wäre schade, wenn die Data Movement-Bibliothek jeweils nur eine Datei übertragen könnte. Glücklicherweise ist dies nicht der Fall. Die Data Movement-Bibliothek kann ein Dateiverzeichnis einschließlich sämtlicher Unterverzeichnisse übertragen. Fügen Sie Code hinzu, der genau das ermöglicht.

Fügen Sie zunächst die Methode `GetBlobDirectory` zu `Program.cs` hinzu:

```csharp
public static CloudBlobDirectory GetBlobDirectory(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container. This can be a new or existing Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    CloudBlobDirectory blobDirectory = container.GetDirectoryReference("");

    return blobDirectory;
}
```

Ändern Sie dann `TransferLocalDirectoryToAzureBlobDirectory`:

```csharp
public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
{
    string localDirectoryPath = GetSourcePath();
    CloudBlobDirectory blobDirectory = GetBlobDirectory(account);
    TransferCheckpoint checkpoint = null;
    DirectoryTransferContext context = GetDirectoryTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    UploadDirectoryOptions options = new UploadDirectoryOptions()
    {
        Recursive = true
    };

    try
    {
        task = TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetDirectoryTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Zwischen dieser Methode und der Methode zum Hochladen einer einzelnen Datei bestehen einige Unterschiede. Wir verwenden jetzt `TransferManager.UploadDirectoryAsync` und die zuvor erstellte `getDirectoryTransferContext`-Methode. Zudem stellen wir unserem Hochladevorgang jetzt einen `options`-Wert bereit, mit dem wir angeben können, dass Unterverzeichnisse ebenfalls hochgeladen werden sollen.

## <a name="copy-file-from-url-to-azure-blob"></a>Kopieren der Datei aus der URL in das Azure-Blob
Fügen Sie jetzt Code hinzu, der das Kopieren einer Datei aus einer URL in ein Azure-Blob ermöglicht.

Ändern Sie `TransferUrlToAzureBlob`:

```csharp
public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
{
    Uri uri = new Uri(GetSourcePath());
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Eine wichtige Nutzungsart dieser Funktion ist das Verschieben von Daten aus einem anderen Clouddienst (z. B. AWS) in Azure. Solange Sie eine URL mit Zugriff auf die Ressource haben, können Sie diese Ressource einfach anhand der `TransferManager.CopyAsync`-Methode in Azure-Blobs verschieben. Diese Methode führt außerdem einen neuen booleschen Parameter ein. Durch Festlegen dieses Parameters auf `true` geben Sie an, dass eine asynchrone serverseitige Kopie erstellt werden soll. Durch Festlegen dieses Parameters auf `false` geben Sie eine synchrone Kopie an, d.h. die Ressource wird zunächst auf unseren lokalen Computer heruntergeladen und dann in das Azure-Blob hochgeladen. Eine synchrone Kopie ist derzeit jedoch nur beim Kopieren aus einer Azure Storage-Ressource in eine andere verfügbar.

## <a name="transfer-azure-blob-to-azure-blob"></a>Übertragen von einem Azure-Blob in ein Azure-Blob
Eine weitere einzigartige Funktion der Data Movement-Bibliothek ist das Kopieren aus einer Azure Storage-Ressource in eine andere.

Ändern Sie `TransferAzureBlobToAzureBlob`:

```csharp
public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
{
    CloudBlockBlob sourceBlob = GetBlob(account);
    CloudBlockBlob destinationBlob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(sourceBlob, destinationBlob, false, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

In diesem Beispiel legen wir den booleschen Parameter in `TransferManager.CopyAsync` auf `false` fest und geben so an, dass wir eine synchrone Kopie erstellen möchten. Das bedeutet, dass die Ressource zuerst auf unseren lokalen Computer heruntergeladen und dann in ein Azure-Blob hochgeladen wird. Die Option zum synchronen Kopieren ist eine hervorragende Möglichkeit zum Gewährleisten einer konsistenten Geschwindigkeit für den Kopiervorgang. Im Gegensatz dazu hängt die Geschwindigkeit einer asynchronen serverseitigen Kopie von der verfügbaren Netzwerkbandbreite auf dem Server ab, die schwanken kann. Synchrone Kopien können jedoch zu zusätzlichen Ausgangskosten im Vergleich zu asynchronen Kopien führen. Zum Vermeiden von Ausgangskosten empfiehlt sich die Verwendung einer synchronen Kopie auf einem virtuellen Azure-Computer, der sich in derselben Region wie das Quellspeicherkonto befindet.

## <a name="conclusion"></a>Zusammenfassung
Unsere Data Movement-Anwendung ist jetzt abgeschlossen. [Den vollständigen Beispielcode finden Sie auf GitHub](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>Nächste Schritte
In dieser Einführung haben wir eine Anwendung erstellt, die mit Azure Storage interagiert und unter Windows, Linux und macOS ausgeführt wird. Erste Schritte mit Fokus auf Blobspeicher. Allerdings können dieselben Kenntnisse auf File Storage angewendet werden. Weitere Informationen finden Sie in der [Referenzdokumentation zur Azure Storage Data Movement-Bibliothek](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
