---
title: Beibehalten der Ausgabe von Aufträgen und Tasks in Azure Storage mit der Azure Batch-Dienst-API | Microsoft-Dokumentation
description: In diesem Artikel erhalten Sie Informationen zur Verwendung der Batch-Dienst-API zur Beibehaltung der Ausgabe von Batch-Tasks und -Aufträgen in Azure Storage.
services: batch
author: laurenhughes
manager: jeconnoc
editor: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 03/05/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 982fe5cea633d9fd1bbbe7dc862b69d89f5f1c1c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65595281"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Beibehalten von Taskdaten mithilfe der Batch-Dienst-API in Azure Storage

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Die Batch-Dienst-API unterstützt das Beibehalten von Ausgabedaten in Azure Storage für Tasks und Auftrags-Manager-Tasks, die für Pools mit der Konfiguration des virtuellen Computers ausgeführt werden. Wenn Sie einen Task hinzufügen, können Sie in Azure Storage einen Container als Ziel für die Ausgabe des Tasks angeben. Der Batch-Dienst schreibt anschließend alle Ausgabedaten in diesen Container, wenn der Task abgeschlossen ist.

Ein Vorteil der Verwendung der Batch-Dienst-API zur Beibehaltung der Ausgabe von Tasks ist, dass Sie die Anwendung, die Ihr Task ausführt, nicht ändern müssen. Stattdessen können Sie mit einigen wenigen Änderungen Ihrer Clientanwendung die Ausgabe des Tasks beibehalten. Dies geschieht innerhalb des Codes, der auch den Task erstellt.

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Wann verwenden ich die Batch-Dienst-API, um die Taskausgabe beizubehalten?

Azure Batch bietet mehr als eine Möglichkeit zum Beibehalten der Taskausgabe. Die Verwendung der Batch-Dienst-API ist ein praktischer Ansatz, der am besten für die folgenden Szenarios geeignet ist:

- Sie möchten Code schreiben, um die Taskausgabe von innerhalb Ihrer Clientanwendung beizubehalten, ohne die Anwendung ändern zu müssen, die der Task ausführt.
- Sie möchten die Ausgabe von Batch-Tasks und Auftrags-Manager-Aufgaben in Pools beibehalten, die mit den Konfigurationen des virtuellen Computers erstellt wurden.
- Sie möchten die Ausgabe in einem Azure Storage-Container mit einem beliebigen Namen beibehalten.
- Sie möchten die Ausgabe in einem Azure Storage-Container beibehalten, der auf Grundlage des [Batch-Dateikonventionenstandards](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) benannt wurde. 

Wenn Ihr Szenario sich von den oben aufgeführten unterscheidet, müssen Sie möglicherweise einen anderen Ansatz in Betracht ziehen. Die Batch-Dienst-API unterstützt beispielsweise derzeit kein Streaming von Ausgaben an Azure Storage während der Ausführung des Tasks. Um die Ausgabe zu streamen, sollten Sie die Bibliothek für Batch-Dateikonventionen für .NET verwenden. Für andere Sprachen müssen Sie Ihre eigene Lösung implementieren. Weitere Informationen zu anderen Optionen für das Beibehalten der Taskausgabe finden Sie unter [Persistente Aufträge und Aufgabenausgabe in Azure Storage](batch-task-output.md).

## <a name="create-a-container-in-azure-storage"></a>Erstellen eines Containers in Azure Storage

Damit Sie die Taskausgabe in Azure Storage beibehalten können, müssen Sie einen Container als Ziel für Ihre Ausgabedateien erstellen. Erstellen Sie den Container vor Ausführung des Tasks, vorzugsweise vor Übermittlung Ihres Auftrags. Verwenden Sie die entsprechende Azure Storage-Clientbibliothek oder -SDK, um den Container zu erstellen. Weitere Informationen zu Azure Storage-APIs finden Sie in der [Azure Storage-Dokumentation](https://docs.microsoft.com/azure/storage/).

Wenn Sie beispielsweise Ihre Anwendung in C# schreiben, verwenden Sie die [Azure Storage-Clientbibliothek für .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Das folgende Beispiel zeigt das Erstellen eines Containers:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Generieren einer Shared Access Signature für den Container

Generieren Sie nach der Erstellung des Containers eine Shared Access Signature (SAS) mit Schreibzugriff auf den Container. Eine SAS bietet delegierten Zugriff auf den Container. Die SAS gewährt den Zugriff mit angegebenen Berechtigungen und über ein angegebenes Zeitintervall. Der Batch-Dienst benötigt eine SAS mit Schreibberechtigungen, um die Taskausgabe in den Container schreiben zu können. Weitere Informationen zu SAS finden Sie unter [Verwenden von Shared Access Signatures\(SAS\) in Azure Storage](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Wenn Sie mithilfe der Azure Storage-APIs eine SAS abrufen, gibt die API eine SAS-Tokenzeichenfolge zurück. Diese Tokenzeichenfolge enthält alle Parameter der SAS, darunter die Berechtigungen und den Intervallzeitraum der SAS-Gültigkeit. Sie müssen die SAS-Tokenzeichenfolge an den Ressourcen-URI anfügen, um die SAS für den Zugriff auf einen Container in Azure Storage zu verwenden. Der Ressourcen-URI und das angefügte SAS-Token ermöglichen den authentifizierten Zugriff auf Azure Storage.

Im folgenden Beispiel wird gezeigt, wie Sie eine lesegeschützte SAS-Tokenzeichenfolge für den Container abrufen und anschließend die SAS an den Container-URI anfügen:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Angeben von Ausgabedateien für die Taskausgabe

Erstellen Sie zum Angeben von Ausgabedateien für einen Task eine Auflistung von [OutputFile](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile)-Objekten, und weisen Sie sie der Eigenschaft [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) zu, wenn Sie den Task erstellen.

Im folgenden Codebeispiel für C# wird ein Task erstellt, der Zufallszahlen in eine Datei mit dem Namen `output.txt` schreibt. Das Beispiel erstellt eine Ausgabedatei für `output.txt`, die in den Container geschrieben werden soll. Außerdem erstellt das Beispiel Ausgabedateien für alle Protokolldateien, die mit dem Dateimuster `std*.txt` (_z.B._ , `stdout.txt` und `stderr.txt`) übereinstimmen. Die Container-URL benötigt die vorher für den Container erstellte SAS. Der Batch-Dienst verwendet die SAS für die Authentifizierung des Zugriffs auf den Container:

```csharp
new CloudTask(taskId, "cmd /v:ON /c \"echo off && set && (FOR /L %i IN (1,1,100000) DO (ECHO !RANDOM!)) > output.txt\"")
{
    OutputFiles = new List<OutputFile>
    {
        new OutputFile(
            filePattern: @"..\std*.txt",
            destination: new OutputFileDestination(
         new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId)),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
        new OutputFile(
            filePattern: @"output.txt",
            destination: 
         new OutputFileDestination(new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId + @"\output.txt")),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
}
```

### <a name="specify-a-file-pattern-for-matching"></a>Angeben eines Dateimusters für den Abgleich

Wenn Sie eine Ausgabedatei angeben, können Sie die Eigenschaft [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) verwenden, um ein Dateimuster für den Abgleich anzugeben. Das Dateimuster entspricht entweder keinen Dateien, einer einzelnen Datei oder einem Satz Dateien, die vom Task erstellt werden.

Die Eigenschaft **FilePattern** unterstützt Standardplatzhalter für das Dateisystem, z.B. `*` (für nicht-rekursive Übereinstimmungen) und `**` (für rekursive Übereinstimmungen). Im obigen Codebeispiel wird das Dateimuster beispielsweise so angegeben, dass es nicht-rekursiv mit `std*.txt` übereinstimmt:

`filePattern: @"..\std*.txt"`

Geben Sie zum Hochladen einer einzelnen Datei ein Dateimuster ohne Platzhalter an. Im obigen Codebeispiel wird das Dateimuster beispielsweise so angegeben, dass es mit `output.txt` übereinstimmt:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Angeben einer Upload-Bedingung

Die Eigenschaft [OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) ermöglicht das bedingte Hochladen von Ausgabedateien. Ein häufiges Szenario ist das Hochladen eines Satzes von Dateien, wenn der Task erfolgreich ausgeführt wird, und eines anderen Satzes von Dateien, wenn ein Fehler auftritt. Sie können z.B. ausführliche Protokolldateien nur hochladen, wenn der Task fehlschlägt und mit einem Exitcode ungleich 0 beendet wird. Ähnlich sollten Sie Ergebnisdateien nur bei Erfolg des Tasks hochladen, da diese Dateien fehlen können oder unvollständig sind, wenn der Task fehlschlägt.

Im obigen Codebeispiel wird die Eigenschaft **UploadCondition** auf **TaskCompletion** festgelegt. Diese Einstellung gibt an, dass die Datei nach Abschluss des Tasks hochgeladen wird, unabhängig vom Wert des Exitcode.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Weitere Einstellungen finden Sie unter der [OutputFileUploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition)-Enumeration.

### <a name="disambiguate-files-with-the-same-name"></a>Dateien mit dem gleichen Namen unterscheiden

Die Tasks in einem Auftrag erzeugen möglicherweise Dateien, die den gleichen Namen tragen. Beispielsweise werden `stdout.txt` und `stderr.txt` für jeden Task erstellt, der in einem Auftrag ausgeführt wird. Da jeder Task in seinem eigenen Kontext ausgeführt wird, stehen diese Dateien im Dateisystem des Knotens nicht in Konflikt. Wenn Sie allerdings Dateien aus mehreren Tasks in einen gemeinsamen Container hochladen, müssen Sie Dateien mit dem gleichen Namen unterscheiden.

Die Eigenschaft [OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) gibt den Zielblob oder das virtuelle Verzeichnis für Ausgabedateien an. Sie können die Eigenschaft **Path** verwenden, um den Blob oder das virtuelle Verzeichnis so zu benennen, dass Ausgabedateien mit dem gleichen Namen in Azure Storage einen eindeutigen Namen führen. Die Task-ID im Pfad zu verwenden ist eine gute Möglichkeit, um eindeutige Namen und problemlos identifizierbare Dateien sicherzustellen.

Wenn die Eigenschaft **FilePattern** auf einen Platzhalterausdruck festgelegt ist, werden alle Dateien, die mit dem Muster übereinstimmen, in das virtuelle Verzeichnis hochgeladen, das von der Eigenschaft **Path** angegeben wird. Wenn es sich beim Container z.B. um `mycontainer` handelt, die Task-ID `mytask` lautet und das Dateimuster `..\std*.txt` entspricht, ähneln die absoluten URIs der Ausgabedateien in Azure Storage folgendem:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Wenn die Eigenschaft **FilePattern** darauf festgelegt ist, mit einem einzigen Dateinamen übereinzustimmen (d.h. er enthält keine Platzhalterzeichen), gibt der Wert der Eigenschaft **Path** den vollqualifizierten Namen des Blobs an. Wenn Sie Namenskonflikte mit einer einzelnen Datei aus mehreren Tasks vermuten, können Sie den Namen des virtuellen Verzeichnisses als Teil des Dateinamens mit einschließen, um diese Dateien zu unterscheiden. Legen Sie z.B. die Eigenschaft **Path** so fest, dass sie die Task-ID, das Trennzeichen (normalerweise ein Schrägstrich) und den Dateinamen enthält:

`path: taskId + @"/output.txt"`

Die absoluten URIs für die Ausgabedateien für eine Reihe von Tasks werden in etwa so aussehen:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Weitere Informationen zu virtuellen Verzeichnissen in Azure Storage finden Sie unter [Auflisten der Blobs in einem Container](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).

## <a name="diagnose-file-upload-errors"></a>Diagnostizieren von Fehlern beim Dateiupload

Wenn beim Hochladen von Ausgabedateien in Azure Storage ein Fehler auftritt, wechselt der Task in den Status **Abgeschlossen**, und die Eigenschaft [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) wird festgelegt. Überprüfen Sie die Eigenschaft **FailureInformation**, um festzustellen, welcher Fehler aufgetreten ist. Hier sehen Sie z.B. einen Fehler, der beim Dateiupload auftritt, wenn der Container nicht gefunden werden kann:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Bei jedem Dateiupload schreibt Batch zwei Protokolldateien in den Computeknoten: `fileuploadout.txt` und `fileuploaderr.txt`. Sie können diese Protokolldateien überprüfen, um weitere Informationen zu einem bestimmten Fehler zu erhalten. In Fällen, in denen das Hochladen der Datei nicht versucht wurde, da z.B. der Task selbst nicht ausgeführt werden konnte, sind diese Protokolldateien nicht vorhanden.

## <a name="diagnose-file-upload-performance"></a>Diagnostizieren der Leistung beim Dateiupload

Die Datei `fileuploadout.txt` protokolliert den Uploadvorgang. Sie können diese Datei überprüfen, um weitere Informationen über die Dauer Ihrer Dateiuploads zu erhalten. Beachten Sie, dass die Uploadleistung von vielen Faktoren bestimmt wird, darunter die Größe des Knoten, andere Aktivitäten auf dem Knoten zum Zeitpunkt des Upload, ob der Zielcontainer sich in derselben Region befindet wie der Batch-Pool, wie viele Knoten gleichzeitig etwas in das Speicherkonto hochladen und so weiter.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Verwenden der Batch-Dienst-API mit dem Batch-Dateikonventionenstandard

Wenn Sie die Taskausgabe mit der Batch-Dienst-API beibehalten, können Sie Ihren Zielcontainern und Blobs einen beliebigen Namen geben. Sie können sie auch in Übereinstimmung mit dem [Batch-Dateikonventionenstandard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) benennen. Der Dateikonventionenstandard bestimmt die Namen des Zielcontainers und des Blobs in Azure Storage für eine gegebene Ausgabedatei, die auf den Namen des Auftrags und der Aufgabe basiert. Wenn sie den Dateikonventionenstandard zum Benennen von Ausgabedateien verwenden, sind Ihre Ausgabedateien zur Anzeige im [Azure-Portal](https://portal.azure.com) verfügbar.

Wenn Sie in C# entwickeln, können Sie die Methoden verwenden, die in der [Bibliothek für Batch-Dateikonventionen für .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files) integriert sind. Diese Bibliothek erstellt Container und Blobpfade mit ordnungsgemäßen Namen für Sie. Sie können die API beispielsweise aufrufen, um den korrekten Namen für den Container auf Grundlage des Auftragsnamens abzurufen:

```csharp
string containerName = job.OutputStorageContainerName();
```

Sie können die Methode [CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) verwenden, um eine Shared Access Signature-URL (SAS) zurückzugeben, die zum Schreiben in den Container verwendet wird. Sie können diese SAS anschließend an den [OutputFileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination)-Konstruktor übergeben.

Wenn Sie in einer anderen Sprache als C# entwickeln, müssen Sie den Dateikonventionenstandard in Ihrer Anwendung selbst implementieren.

## <a name="code-sample"></a>Codebeispiel

Das Beispielprojekt [PersistOutputs][github_persistoutputs] ist eines der [Azure Batch-Codebeispiele][github_samples] auf GitHub. Diese Visual Studio-Projektmappe veranschaulicht, wie die Batch-Clientbibliothek für .NET verwendet werden kann, um die Taskausgabe in dauerhaftem Speicher beizubehalten. Gehen Sie folgendermaßen vor, um das Beispiel auszuführen:

1. Öffnen Sie das Projekt in **Visual Studio 2019**.
2. Fügen Sie die **Anmeldeinformationen** für Ihr Batch- und Storage-Konto zu **AccountSettings.settings** im Microsoft.Azure.Batch.Samples.Common-Projekt hinzu.
3. **Erstellen** Sie die Lösung (aber führen Sie sie nicht aus). Stellen Sie NuGet-Pakete wieder her, wenn Sie dazu aufgefordert werden.
4. Laden Sie im Azure-Portal ein [Anwendungspaket](batch-application-packages.md) für **PersistOutputsTask**hoch. Fügen Sie `PersistOutputsTask.exe` und die abhängigen Assemblys dem ZIP-Paket hinzu, und legen Sie die Anwendungs-ID auf „PersistOutputsTask“ und die Version des Anwendungspakets auf „1.0“ fest.
5. **Starten** Sie das **PersistOutputs**-Projekt (d.h. führen Sie es aus).
6. Geben Sie **2** ein, wenn Sie aufgefordert werden, die Persistenztechnologie für die Ausführung des Beispiels auszuwählen. Hierdurch wird das Beispiel mithilfe der Batch-Dienst-API ausgeführt, um die Taskausgabe beizubehalten.
7. Falls gewünscht können Sie das Beispiel erneut ausführen und dieses Mal **3** eingeben, um die Ausgabe mit der Batch-Dienst-API beizubehalten und gleichzeitig den Zielcontainer und den Blobpfad gemäß des Dateikonventionenstandards zu benennen.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Beibehalten von Taskausgaben mit der Dateikonventionenbibliothek für .NET finden Sie unter [Beibehalten von Auftrags- und Taskdaten in Azure Storage mit der Batch-Dateikonventionenbibliothek für .NET](batch-task-output-file-conventions.md).
- Informationen zu anderen Ansätzen für das Beibehalten von Ausgabedaten in Azure Batch finden Sie unter [Persistente Aufträge und Aufgabenausgabe in Azure Storage](batch-task-output.md).

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
