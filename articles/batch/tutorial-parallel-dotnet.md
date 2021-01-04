---
title: Ausführen einer parallelen Workload mithilfe der .NET-API
description: 'Tutorial: Paralleles Transcodieren von Mediendateien mit ffmpeg in Azure Batch per .NET-Clientbibliothek in Batch'
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 09/29/2020
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 396d0f6a2ef9a8c24fc92b641c889ef9e1a7df49
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578296"
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-net-api"></a>Tutorial: Ausführen einer parallelen Workload mit Azure Batch über die .NET-API

Mithilfe von Azure Batch können Sie umfangreiche auf Parallelverarbeitung ausgelegte HPC-Batchaufträge (High Performance Computing) effizient in Azure ausführen. In diesem Tutorial wird Schritt für Schritt ein C#-Beispiel für die Ausführung einer parallelen Workload per Batch beschrieben. Sie erfahren, wie Sie einen gängigen Batch-Anwendungsworkflow durchführen und programmgesteuert mit Batch- und Storage-Ressourcen interagieren. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Hinzufügen eines Anwendungspakets zu Ihrem Batch-Konto
> * Authentifizieren mit Batch- und Storage-Konten
> * Hochladen von Eingabedateien in Storage
> * Erstellen eines Pools mit Computeknoten für die Ausführung einer Anwendung
> * Erstellen eines Auftrags und von Aufgaben zum Verarbeiten von Eingabedateien
> * Überwachen der Aufgabenausführung
> * Abrufen von Ausgabedateien

In diesem Tutorial konvertieren Sie MP4-Mediendateien parallel in das MP3-Format, indem Sie das Open-Source-Tool [ffmpeg](https://ffmpeg.org/) verwenden. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* [Visual Studio 2017 oder höher](https://www.visualstudio.com/vs) bzw. [.NET Core 2.1](https://www.microsoft.com/net/download/dotnet-core/2.1) für Linux, macOS oder Windows.

* Ein Batch-Konto und ein verknüpftes Azure Storage-Konto. Informationen zur Erstellung dieser Konten finden Sie in den Batch-Schnellstartanleitungen zum [Azure-Portal](quick-create-portal.md) und zur [Azure CLI](quick-create-cli.md).

* [Windows-Version (64 Bit) von ffmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08) (.zip). Laden Sie die ZIP-Datei auf Ihren lokalen Computer herunter. Für dieses Tutorial benötigen Sie nur die ZIP-Datei. Es ist nicht erforderlich, die Datei zu entzippen oder sie lokal zu installieren.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="add-an-application-package"></a>Hinzufügen eines Anwendungspakets

Verwenden Sie das Azure-Portal, um ffmpeg Ihrem Batch-Konto als [Anwendungspaket](batch-application-packages.md) hinzuzufügen. Mit Anwendungspaketen können Sie Aufgabenanwendungen und die dazugehörige Bereitstellung auf den Computeknoten in Ihrem Pool verwalten. 

1. Klicken Sie im Azure-Portal auf **Weitere Dienste** > **Batch-Konten** und dann auf den Namen Ihres Batch-Kontos.
3. Klicken Sie auf **Anwendungen** > **Hinzufügen**.
4. Geben Sie für **Anwendungs-ID** das Tool *ffmpeg* und als Paketversion *4.3.1* ein. Wählen Sie die ffmpeg-ZIP-Datei aus, die Sie heruntergeladen haben, und klicken Sie auf **OK**. Das ffmpeg-Anwendungspaket wird Ihrem Batch-Konto hinzugefügt.

![Hinzufügen eines Anwendungspakets](./media/tutorial-parallel-dotnet/add-application.png)

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="download-and-run-the-sample"></a>Herunterladen und Ausführen des Beispiels

### <a name="download-the-sample"></a>Herunterladen des Beispiels

[Laden Sie die Beispiel-App von GitHub herunter, oder klonen Sie sie](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial). Verwenden Sie den folgenden Befehl, um das Beispiel-App-Repository mit einem Git-Client zu klonen:

```
git clone https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial.git
```

Navigieren Sie zu dem Verzeichnis, in dem die Visual Studio-Projektmappendatei `BatchDotNetFfmpegTutorial.sln` enthalten ist.

Öffnen Sie die Projektmappendatei in Visual Studio, und aktualisieren Sie die Zeichenfolgen mit den Anmeldeinformationen in `Program.cs` mit den Werten für Ihre Konten. Beispiel:

```csharp
// Batch account credentials
private const string BatchAccountName = "mybatchaccount";
private const string BatchAccountKey  = "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==";
private const string BatchAccountUrl  = "https://mybatchaccount.mybatchregion.batch.azure.com";

// Storage account credentials
private const string StorageAccountName = "mystorageaccount";
private const string StorageAccountKey  = "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ==";
```

[!INCLUDE [batch-credentials-include](../../includes/batch-credentials-include.md)]

Stellen Sie außerdem sicher, dass der Verweis auf das ffmpeg-Anwendungspaket in der Lösung mit der ID und Version des ffmpeg-Pakets übereinstimmt, das Sie in Ihr Batch-Konto hochgeladen haben.

```csharp
const string appPackageId = "ffmpeg";
const string appPackageVersion = "4.3.1";
```

### <a name="build-and-run-the-sample-project"></a>Erstellen und Ausführen des Beispielprojekts

Verwenden Sie für das Erstellen und Ausführen der Anwendung Visual Studio oder die Befehlszeile mit den Befehlen `dotnet build` und `dotnet run`. Sehen Sie sich nach dem Ausführen der Anwendung den Code an, um zu erfahren, welche Aufgabe die einzelnen Teile der Anwendung jeweils haben. In Visual Studio:

* Klicken Sie mit der rechten Maustaste im Projektmappen-Explorer auf die Projektmappe, und klicken Sie dann auf **Projektmappe erstellen**. 

* Bestätigen Sie die Wiederherstellung von NuGet-Paketen, wenn Sie hierzu aufgefordert werden. Stellen Sie beim Herunterladen von fehlenden Paketen sicher, dass der [NuGet-Paket-Manager](https://docs.nuget.org/consume/installing-nuget) installiert ist.

Führen Sie ihn anschließend aus. Beim Ausführen der Beispielanwendung sieht die Konsolenausgabe in etwa wie folgt aus: Bei der Ausführung kommt es bei `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` zu einer Pause, während die Computeknoten des Pools gestartet werden. 

```
Sample start: 11/19/2018 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [WinFFmpegPool]...
Creating job [WinFFmpegJob]...
Adding 5 tasks to job [WinFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]...

Sample end: 11/19/2018 3:29:36 PM
Elapsed time: 00:09:14.3418742
```

Navigieren Sie im Azure-Portal zu Ihrem Batch-Konto, um den Pool, die Computeknoten, den Auftrag und die Aufgaben zu überwachen. Klicken Sie beispielsweise auf **Pools** > *WinFFmpegPool*, um ein Wärmebild mit den Computeknoten Ihres Pools anzuzeigen.

Bei der Ausführung von Aufgaben sieht das Wärmebild in etwa wie folgt aus:

![Wärmebild für Pool](./media/tutorial-parallel-dotnet/pool.png)

Die normale Ausführungsdauer beträgt ca. **zehn Minuten**, wenn die Anwendung in der Standardkonfiguration ausgeführt wird. Die meiste Zeit wird für die Poolerstellung benötigt.

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>Überprüfen des Codes

In den folgenden Abschnitten ist die Beispielanwendung in die Schritte unterteilt, die ausgeführt werden, um eine Workload im Batch-Dienst zu verarbeiten. Verwenden Sie beim Lesen des restlichen Teils dieses Artikels die Datei `Program.cs` der Projektmappe, da nicht jede Codezeile im Beispiel beschrieben wird.

### <a name="authenticate-blob-and-batch-clients"></a>Authentifizieren des Blobs und der Batch-Clients

Zum Interagieren mit dem verknüpften Speicherkonto verwendet die App die Azure Storage-Clientbibliothek für .NET. Sie erstellt mit [CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) einen Verweis auf das Konto, und es wird die Authentifizierung mit gemeinsam verwendetem Schlüssel genutzt. Anschließend wird ein [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient)-Element erstellt.

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
                                StorageAccountName, StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Die App erstellt ein [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient)-Objekt zum Erstellen und Verwalten von Pools, Aufträgen und Aufgaben im Batch-Dienst. Für den Batch-Client im Beispiel wird die Authentifizierung mit gemeinsam verwendetem Schlüssel genutzt. Batch unterstützt auch die Authentifizierung per [Azure Active Directory](batch-aad-auth.md), um einzelne Benutzer oder eine unbeaufsichtigte Anwendung zu authentifizieren.

```csharp
BatchSharedKeyCredentials sharedKeyCredentials = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(sharedKeyCredentials))
...
```

### <a name="upload-input-files"></a>Hochladen von Eingabedateien

Die App übergibt das `blobClient`-Objekt an die `CreateContainerIfNotExistAsync`-Methode, um einen Speichercontainer für die Eingabedateien (MP4-Format) und einen Container für die Aufgabenausgabe zu erstellen.

```csharp
CreateContainerIfNotExistAsync(blobClient, inputContainerName);
CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

Anschließend werden Dateien aus dem lokalen Ordner `InputFiles` in den Eingabecontainer hochgeladen. Die Dateien im Speicher werden als Batch-[ResourceFile](/dotnet/api/microsoft.azure.batch.resourcefile)-Objekte definiert, die von Batch später auf Computeknoten heruntergeladen werden können. 

Am Upload der Dateien sind in `Program.cs` zwei Methoden beteiligt:

* `UploadFilesToContainerAsync`: Gibt eine Sammlung mit ResourceFile-Objekten zurück und ruft intern `UploadResourceFileToContainerAsync` auf, um die einzelnen Dateien hochzuladen, die im Parameter `inputFilePaths` übergeben werden.
* `UploadResourceFileToContainerAsync`: Lädt jede Datei als Blob in den Eingabecontainer hoch. Nach dem Hochladen der Datei wird eine Shared Access Signature (SAS) für das Blob abgerufen, und es wird ein ResourceFile-Objekt zurückgegeben, um das Blob darzustellen.

```csharp
string inputPath = Path.Combine(Environment.CurrentDirectory, "InputFiles");

List<string> inputFilePaths = new List<string>(Directory.GetFileSystemEntries(inputPath, "*.mp4",
    SearchOption.TopDirectoryOnly));

List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
  blobClient,
  inputContainerName,
  inputFilePaths);
```

Weitere Informationen zum Hochladen von Dateien als Blobs in ein Speicherkonto mit .NET finden Sie unter [Schnellstart: Hochladen, Herunterladen und Auflisten von Blobs mit .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md).

### <a name="create-a-pool-of-compute-nodes"></a>Erstellen eines Pools mit Computeknoten

Als Nächstes erstellt das Beispiel im Batch-Konto durch Aufrufen von `CreatePoolIfNotExistAsync` einen Pool mit Computeknoten. In dieser definierten Methode wird die [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool)-Methode verwendet, um die Anzahl von Knoten, die VM-Größe und eine Poolkonfiguration festzulegen. Hier gibt ein [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration)-Objekt einen [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference)-Verweis auf ein Windows Server-Image an, das im Azure Marketplace veröffentlicht wurde. Batch unterstützt viele verschiedene VM-Images im Azure Marketplace und auch benutzerdefinierte VM-Images.

Die Anzahl von Knoten und die VM-Größe werden mit definierten Konstanten festgelegt. Batch unterstützt dedizierte Knoten und [Knoten mit niedriger Priorität](batch-low-pri-vms.md), und Sie können eine oder beide Arten von Knoten in Ihren Pools verwenden. Dedizierte Knoten sind für Ihren Pool reserviert. Knoten mit niedriger Priorität werden zu einem reduzierten Preis basierend auf überschüssiger VM-Kapazität in Azure angeboten. Knoten mit niedriger Priorität sind nicht mehr verfügbar, wenn in Azure nicht genügend Kapazität vorhanden ist. Im Beispiel wird standardmäßig ein Pool mit nur fünf Knoten mit niedriger Priorität und der Größe *Standard_A1_v2* erstellt.

>[!Note]
>Überprüfen Sie Ihre Knotenkontingente. Eine Anleitung zum Erstellen einer Kontingentanforderung finden Sie unter [Batch-Dienst – Kontingente und Limits](batch-quota-limit.md#increase-a-quota).

Die Anwendung ffmpeg wird auf den Computeknoten bereitgestellt, indem der Poolkonfiguration ein [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference)-Element hinzugefügt wird.

Mit der [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync)-Methode wird der Pool an den Batch-Dienst übermittelt.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-smalldisk",
    version: "latest");

VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.windows amd64");

pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: DedicatedNodeCount,
    targetLowPriorityComputeNodes: LowPriorityNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

pool.ApplicationPackageReferences = new List<ApplicationPackageReference>
    {
    new ApplicationPackageReference {
    ApplicationId = appPackageId,
    Version = appPackageVersion}};

await pool.CommitAsync();  
```

### <a name="create-a-job"></a>Erstellen eines Auftrags

Für einen Batch-Auftrag werden ein Pool zum Ausführen von Aufgaben und optionale Einstellungen wie eine Priorität und ein Zeitplan für die Arbeitsschritte angegeben. Im Beispiel wird ein Auftrag mit einem Aufruf von `CreateJobAsync` erstellt. In dieser definierten Methode wird die [BatchClient.JobOperations.CreateJob](/dotnet/api/microsoft.azure.batch.joboperations.createjob)-Methode zum Erstellen eines Auftrags in Ihrem Pool verwendet.

Mit der [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudjob.commitasync)-Methode wird der Auftrag an den Batch-Dienst übermittelt. Der Auftrag enthält ursprünglich keine Aufgaben.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob();
job.Id = JobId;
job.PoolInformation = new PoolInformation { PoolId = PoolId };

await job.CommitAsync();
```

### <a name="create-tasks"></a>Erstellen von Aufgaben

Im Beispiel werden Aufgaben im Auftrag durch einen Aufruf der `AddTasksAsync`-Methode erstellt, mit der eine Liste mit [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask)-Objekten erstellt wird. Für jede `CloudTask` wird ffmpeg ausgeführt, um ein `ResourceFile`-Eingabeobjekt per [CommandLine](/dotnet/api/microsoft.azure.batch.cloudtask.commandline)-Eigenschaft zu verarbeiten. ffmpeg wurde zuvor bei der Erstellung des Pools auf jedem Knoten installiert. Hier wird in der Befehlszeile ffmpeg ausgeführt, um jede MP4-Eingabedatei (Video) in eine MP3-Datei (Audio) zu konvertieren.

Im Beispiel wird nach der Ausführung über die Befehlszeile ein [OutputFile](/dotnet/api/microsoft.azure.batch.outputfile)-Objekt für die MP3-Datei erstellt. Die Ausgabedateien (in diesem Fall eine Datei) jeder Aufgabe werden in einen Container im verknüpften Speicherkonto hochgeladen, indem die [OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles)-Eigenschaft der Aufgabe verwendet wird. Früher im Codebeispiel wurde eine Shared Access Signature-URL (`outputContainerSasUrl`) abgerufen, um Schreibzugriff auf den Ausgabecontainer bereitzustellen. Beachten Sie die Bedingungen, die für das `outputFile`-Objekt festgelegt sind. Eine Ausgabedatei von einer Aufgabe wird nur in den Container hochgeladen, nachdem die Aufgabe erfolgreich abgeschlossen wurde (`OutputFileUploadCondition.TaskSuccess`). Weitere Implementierungsdetails finden Sie im vollständigen [Codebeispiel](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) auf GitHub.

Anschließend werden dem Auftrag im Beispiel mit der [AddTaskAsync](/dotnet/api/microsoft.azure.batch.joboperations.addtaskasync)-Methode Aufgaben hinzugefügt und für die Ausführung auf den Computeknoten in die Warteschlange eingereiht.

```csharp
 // Create a collection to hold the tasks added to the job.
List<CloudTask> tasks = new List<CloudTask>();

for (int i = 0; i < inputFiles.Count; i++)
{
    string taskId = String.Format("Task{0}", i);

    // Define task command line to convert each input file.
    string appPath = String.Format("%AZ_BATCH_APP_PACKAGE_{0}#{1}%", appPackageId, appPackageVersion);
    string inputMediaFile = inputFiles[i].FilePath;
    string outputMediaFile = String.Format("{0}{1}",
        System.IO.Path.GetFileNameWithoutExtension(inputMediaFile),
        ".mp3");
    string taskCommandLine = String.Format("cmd /c {0}\\ffmpeg-4.3.1-2020-09-21-full_build\\bin\\ffmpeg.exe -i {1} {2}", appPath, inputMediaFile, outputMediaFile);

    // Create a cloud task (with the task ID and command line)
    CloudTask task = new CloudTask(taskId, taskCommandLine);
    task.ResourceFiles = new List<ResourceFile> { inputFiles[i] };

    // Task output file
    List<OutputFile> outputFileList = new List<OutputFile>();
    OutputFileBlobContainerDestination outputContainer = new OutputFileBlobContainerDestination(outputContainerSasUrl);
    OutputFile outputFile = new OutputFile(outputMediaFile,
       new OutputFileDestination(outputContainer),
       new OutputFileUploadOptions(OutputFileUploadCondition.TaskSuccess));
    outputFileList.Add(outputFile);
    task.OutputFiles = outputFileList;
    tasks.Add(task);
}

// Add tasks as a collection
await batchClient.JobOperations.AddTaskAsync(jobId, tasks);
return tasks
```

### <a name="monitor-tasks"></a>Überwachen von Aufgaben

Wenn Batch einem Auftrag Aufgaben hinzufügt, werden sie vom Dienst automatisch in die Warteschlange eingereiht und für die Ausführung auf Computeknoten im zugeordneten Pool eingeplant. Basierend auf den Einstellungen, die Sie angeben, führt Batch das Einreihen, Planen und erneute Ausführen sowie andere Schritte der Aufgabenverwaltung aus.

Es gibt viele Ansätze für die Überwachung der Aufgabenausführung. In diesem Beispiel wird eine `MonitorTasks`-Methode definiert, mit der für Aufgaben nur der Abschluss und die fehlerhafte bzw. erfolgreiche Durchführung gemeldet wird. Im `MonitorTasks`-Code ist ein [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel)-Element angegeben, um eine effiziente Auswahl von minimal erforderlichen Informationen zu den Aufgaben zu ermöglichen. Anschließend wird ein [TaskStateMonitor](/dotnet/api/microsoft.azure.batch.taskstatemonitor)-Element erstellt, mit dem Hilfsprogramme zum Überwachen von Aufgabenstatus bereitgestellt werden. Bei `MonitorTasks` wird im Beispiel gewartet, bis alle Aufgaben innerhalb eines bestimmten Zeitlimits den Status `TaskState.Completed` erreichen. Anschließend wird der Auftrag beendet, und es werden alle Aufgaben gemeldet, die zwar abgeschlossen wurden, aber für die ggf. ein Fehler wie „Exitcode ungleich null“ aufgetreten ist.

```csharp
TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
try
{
    await taskStateMonitor.WhenAll(addedTasks, TaskState.Completed, timeout);
}
catch (TimeoutException)
{
    batchClient.JobOperations.TerminateJob(jobId);
    Console.WriteLine(incompleteMessage);
    return false;
}
batchClient.JobOperations.TerminateJob(jobId);
 Console.WriteLine(completeMessage);
...

```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nach dem Ausführen der Aufgaben löscht die App den erstellten Eingabespeichercontainer automatisch und ermöglicht Ihnen das Löschen des Batch-Pools und -Auftrags. [JobOperations](/dotnet/api/microsoft.azure.batch.batchclient.joboperations) und [PoolOperations](/dotnet/api/microsoft.azure.batch.batchclient.pooloperations) von BatchClient verfügen jeweils über entsprechende Löschmethoden, die aufgerufen werden, wenn Sie das Löschen bestätigen. Für die Aufträge und Aufgaben fallen zwar keine Kosten an, für Computeknoten dagegen schon. Daher empfehlen wir Ihnen, Pools nur bei Bedarf zuzuordnen. Beim Löschen des Pools werden alle Aufgabenausgaben auf den Knoten gelöscht. Die Ausgabedateien verbleiben aber im Speicherkonto.

Löschen Sie die Ressourcengruppe, das Batch-Konto und das Speicherkonto, wenn diese Elemente nicht mehr benötigt werden. Wählen Sie hierzu im Azure-Portal die Ressourcengruppe für das Batch-Konto aus, und klicken Sie auf **Ressourcengruppe löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Hinzufügen eines Anwendungspakets zu Ihrem Batch-Konto
> * Authentifizieren mit Batch- und Storage-Konten
> * Hochladen von Eingabedateien in Storage
> * Erstellen eines Pools mit Computeknoten für die Ausführung einer Anwendung
> * Erstellen eines Auftrags und von Aufgaben zum Verarbeiten von Eingabedateien
> * Überwachen der Aufgabenausführung
> * Abrufen von Ausgabedateien

Weitere Beispiele zur Verwendung der .NET-API zum Planen und Verarbeiten von Batch-Workloads finden Sie in den Beispielen auf GitHub.

> [!div class="nextstepaction"]
> [C#-Beispiele für Batch](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp)


Das Problem wurde behoben, indem die Instanzvariable auf LowPriorityNodeCount=0 und DedicatedNodeCount=5 festgelegt wurde, und der Auftrag konnte abgeschlossen werden.
