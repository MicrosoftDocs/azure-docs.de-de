---
title: Beibehalten von Auftrags- und Taskausgaben in Azure Storage mit der Dateikonventionenbibliothek für .NET – Azure Batch | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Azure Batch-Dateikonventionenbibliothek für .NET verwenden, um Auftrags- und Taskausgaben von Batch in Azure Storage dauerhaft zu speichern und die persistente Ausgabe im Azure-Portal anzuzeigen.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d4e16ca40e8026861d492f950396d6aa39d4c445
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65791560"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Beibehalten von Auftrags- und Taskdateien in Azure Storage mit der Batch-Dateikonventionenbibliothek für .NET

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Eine Möglichkeit zum Beibehalten von Taskdateien ist die Verwendung der [Dateikonventionenbibliothek von Azure Batch für .NET][nuget_package]. Die Dateikonventionenbibliothek vereinfacht den Vorgang des Speicherns und Abrufens von Taskausgabendateien in Azure Storage. Diese Dateikonventionenbibliothek kann sowohl in Task- und Clientcode verwendet werden &mdash; im Taskcode für das Beibehalten von Dateien, im Clientcode zum Auflisten und Abrufen dieser Dateien. Ihr Taskcode kann die Bibliothek auch zum Abrufen der Ausgaben von vorgelagerten Tasks verwenden, z.B. in einem Szenario mit [Taskabhängigkeiten](batch-task-dependencies.md).

Zum Abrufen von Ausgabedateien mit der Dateikonventionenbibliothek können Sie die Dateien für einen bestimmten Auftrag oder Task suchen, indem Sie diese nach ID und Zweck auflisten. Sie müssen die Namen oder Speicherorte der Dateien nicht kennen. Sie können die Dateikonventionenbibliothek z.B. zum Auflisten aller Zwischendateien für einen bestimmten Task oder zum Abrufen einer Vorschaudatei für einen bestimmten Auftrag verwenden.

> [!TIP]
> Ab Version 1.5.2017 unterstützt die Batch-Dienst-API das Beibehalten von Ausgabedaten in Azure Storage für Tasks und Auftrags-Manager-Tasks, die auf Pools ausgeführt werden, die mit der Konfiguration des virtuellen Computers erstellt wurden. Die Batch-Dienst-API bietet eine einfache Möglichkeit, die Ausgabe innerhalb des Codes beizubehalten, der einen Task erstellt und als Alternative zu der Dateikonventionenbibliothek dient. Sie können Ihre Batch Client-Anwendungen ändern, um die Ausgabe beizubehalten, ohne die Anwendung aktualisieren zu müssen, die ihr Task ausführt. Weitere Informationen finden Sie unter [Beibehalten von Taskdateien mithilfe der Batch-Dienst-API in Azure Storage](batch-task-output-files.md).

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Wann verwende ich die Dateikonventionenbibliothek zum Beibehalten der Taskausgabe?

Azure Batch bietet mehr als eine Möglichkeit zum Beibehalten der Taskausgabe. Die Dateikonventionen eignen sich am besten für folgende Szenarios:

- Sie können den Code für die Anwendung, die ihr Task ausführt, einfach verändern, um Dateien mithilfe der Dateikonventionenbibliothek beizubehalten.
- Sie möchten Dateien in Azure Storage streamen, während der Task noch ausgeführt wird.
- Sie möchten Daten aus Pools beibehalten, die entweder über die Clouddienstkonfiguration oder die Konfiguration des virtuellen Computers erstellt wurden.
- Ihre Clientanwendung oder andere Tasks im Auftrag müssen Taskausgabedateien nach ID oder Zweck finden und herunterladen.
- Sie möchten die Taskausgabe im Azure-Portal anzeigen.

Wenn Ihr Szenario sich von den oben aufgeführten unterscheidet, müssen Sie möglicherweise einen anderen Ansatz in Betracht ziehen. Weitere Informationen zu anderen Optionen für das Beibehalten der Taskausgabe finden Sie unter [Beibehalten von Auftrags- und Taskausgaben in Azure Storage](batch-task-output.md).

## <a name="what-is-the-batch-file-conventions-standard"></a>Was ist der Batch-Dateikonventionenstandard?

Der [Batch-Dateikonventionenstandard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) bietet ein Benennungsschema für die Zielcontainer und Blob-Pfade, in dem die Ausgabedateien geschrieben werden. In Azure Storage gespeicherte Dateien, die dem Dateikonventionenstandard entsprechen, stehen automatisch für die Anzeige im Azure-Portal zur Verfügung. Das Portal erkennt die Benennungskonvention und kann daher Dateien anzeigen, die dieser entsprechen.

Die Dateikonventionenbibliothek für .NET benennt Ihre Speichercontainer und Taskausgabedateien automatisch entsprechend des Dateikonventionenstandards. Die Dateikonventionenbibliothek bietet ebenfalls Methoden zum Abfragen von Ausgabedateien in Azure Storage nach Auftrags-ID, Task-ID oder Zweck.

Wenn Sie mit einer anderen Sprache als .NET entwickeln, können Sie den Dateikonventionenstandard selbst in Ihrer Anwendung implementieren. Weitere Informationen finden Sie unter [Implementieren des Batch-Konventionenstandards](batch-task-output.md#implement-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Verknüpfen Sie ein Azure Storage-Konto mit Ihrem Batch-Konto

Um Ausgabedaten in Azure Storage mithilfe der Dateikonventionenbibliothek beizubehalten, müssen Sie zuerst ein Azure Storage-Konto mit ihrem Batch-Konto verknüpfen. Wenn Sie dies nicht bereits getan haben, verknüpfen Sie ein Storage-Konto über das [Azure-Portal](https://portal.azure.com) mit Ihrem Batch-Konto:

1. Navigieren Sie im Azure-Portal zu Ihrem Batch-Konto.
1. Wählen Sie unter **Einstellungen** die Option **Storage-Konto**.
1. Wenn Sie noch kein dem Batch-Konto zugeordnetes Storage-Konto haben, klicken Sie auf **Storage-Konto (Keine)** .
1. Wählen Sie ein Storage-Konto aus der Liste für Ihr Abonnement. Verwenden Sie für eine optimale Leistung ein Azure Storage-Konto, das sich in der gleichen Region wie das Batch-Konto befindet, in dem Ihre Tasks ausgeführt werden.

## <a name="persist-output-data"></a>Beibehalten von Ausgabedateien

Um Auftrags- und Taskausgabedateien mit der Dateikonventionenbibliothek beizubehalten, erstellen Sie einen Container in Azure Storage, und speichern Sie die Ausgabe für den Container. Verwenden der [Azure Storage-Clientbibliothek für .NET](https://www.nuget.org/packages/WindowsAzure.Storage) in Ihrem Taskcode zum Hochladen der Taskausgabe auf den Container. 

Weitere Informationen zum Arbeiten mit Containern und Blobs in Azure Storage finden Sie unter [Erste Schritte mit Azure Blob Storage in .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Alle Auftrags-und Taskausgaben, die mit der Bibliothek beibehalten werden, werden im gleichen Container gespeichert. Wenn eine große Anzahl von Tasks versucht, Dateien zum gleichen Zeitpunkt beizubehalten, können die [Speicher-Einschränkungsgrenzwerte](../storage/common/storage-performance-checklist.md#blobs) erzwungen werden.

### <a name="create-storage-container"></a>Erstellen eines Speichercontainers

Um eine Taskausgabe in Azure Storage beizubehalten, müssen Sie zunächst einen Container durch Aufrufen von [CloudJob][net_cloudjob].[ PrepareOutputStorageAsync][net_prepareoutputasync] erstellen. Diese Erweiterungsmethode akzeptiert ein [CloudStorageAccount][net_cloudstorageaccount]-Objekt als Parameter. Sie erstellt einen Container, der gemäß dem Dateikonventionenstandard benannt wird, damit dessen Inhalt vom Azure-Portal und den Abrufmethoden, die später in diesem Artikel erläutert werden, erkannt wird.

In der Regel wird der Code zur Erstellung eines Containers in der Clientanwendung &mdash; verwendet (also in der Anwendung, die Pools, Aufträge und Tasks erstellt).

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Speichern von Taskausgaben

Nachdem Sie einen Azure Storage-Container vorbereitet haben, können Tasks Ausgaben in diesem Container speichern, indem sie die [TaskOutputStorage][net_taskoutputstorage]-Klasse aus der Dateikonventionenbibliothek verwenden.

Erstellen Sie in Ihrem Taskcode zunächst ein [TaskOutputStorage][net_taskoutputstorage]-Objekt. Wenn der Task abgeschlossen ist, rufen Sie die [TaskOutputStorage][net_taskoutputstorage].[SaveAsync][net_saveasync]-Methode auf, um die Ausgabe in Azure Storage zu speichern.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

Der `kind`-Parameter der [TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage).[ SaveAsync](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads)-Methode kategorisiert beibehaltene Dateien. Es gibt vier vordefinierte [TaskOutputKind][net_taskoutputkind]-Typen: `TaskOutput`, `TaskPreview`, `TaskLog` und `TaskIntermediate.`. Sie können auch benutzerdefinierte Kategorien der Ausgabe definieren.

Mit diesen Ausgabetypen können Sie angeben, welche Typen von Ausgaben aufgelistet werden, wenn Sie die beibehaltenen Ausgaben eines bestimmten Tasks später in Batch abfragen. Beim Auflisten der Ausgaben für einen Task können Sie die Liste also nach einem der Ausgabetypen filtern. Beispiel: „*preview*-Ausgabe für Task *109* abrufen.“ Weitere Informationen zum Auflisten und Abrufen von Ausgaben finden Sie unter „Abrufen der Ausgabe“ weiter unten in diesem Artikel.

> [!TIP]
> Die Art der Ausgabe bestimmt auch, wo im Azure-Portal eine bestimmte Datei angezeigt wird: Als *TaskOutput* kategorisierte Dateien werden unter **Aufgabenausgabedateien** und *TaskLog*-Dateien unter **Aufgabenprotokolle** angezeigt.

### <a name="store-job-outputs"></a>Speichern von Auftragsausgaben

Zusätzlich zum Speichern von Taskausgaben können Sie die Ausgaben speichern, die einem vollständigen Auftrag zugeordnet sind. Beispielsweise können Sie im Zusammenführungstask eines Filmrenderauftrags den vollständig gerenderten Film als Ausgabe des Auftrags beibehalten. Wenn der Auftrag abgeschlossen ist, kann Ihre Clientanwendung die Ausgaben für den Auftrag auflisten und abrufen, sodass nicht die einzelnen Tasks abgefragt werden müssen.

Speichern Sie die Ausgabe des Auftrags durch Aufrufen der [JobOutputStorage][net_joboutputstorage].[SaveAsync][net_joboutputstorage_saveasync]-Methode, und geben Sie [JobOutputKind][net_joboutputkind] und den Dateinamen an:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Ähnlich wie bei dem Typ **TaskOutputKind** für Taskausgaben verwenden Sie den Typ [JobOutputKind][net_joboutputkind], um die beibehaltenen Dateien eines Auftrags zu kategorisieren. Mit diesem Parameter können Sie später einen bestimmten Ausgabetyp abfragen (oder auflisten). Der **JobOutputKind**-Typ enthält sowohl die Ausgabe- als auch die Vorschaukategorien und unterstützt das Erstellen benutzerdefinierter Kategorien.

### <a name="store-task-logs"></a>Speichern von Taskprotokollen

Zusätzlich zum Beibehalten einer Datei in dauerhaftem Speicher, nachdem ein Task oder ein Auftrag abgeschlossen ist, kann es notwendig sein, Dateien beizubehalten, die während der Ausführung eines Tasks aktualisiert werden – z.B. &mdash; Protokolldateien oder `stdout.txt` und `stderr.txt`. Zu diesem Zweck stellt die Azure Batch-Dateikonventionenbibliothek die [TaskOutputStorage][net_taskoutputstorage].[SaveTrackedAsync][net_savetrackedasync]-Methode zur Verfügung. Mit [SaveTrackedAsync][net_savetrackedasync] können Sie Aktualisierungen einer Datei auf dem Knoten (in einem von Ihnen angegebenen Intervall) nachverfolgen und die Aktualisierungen in Azure Storage beibehalten.

Im folgenden Codeausschnitt wird [SaveTrackedAsync][net_savetrackedasync] verwendet, um `stdout.txt` während der Ausführung des Tasks alle 15 Sekunden in Azure Storage zu aktualisieren:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

Der kommentierte Abschnitt `Code to process data and produce output file(s)` ist ein Platzhalter für den Code, den der Task normalerweise ausführen würde. Unter Umständen verfügen Sie beispielsweise über Code, der Daten aus Azure Storage herunterlädt und eine Transformation oder Berechnung mit diesen Daten durchführt. Dieses Beispiel soll veranschaulichen, wie Code dieser Art in einem `using`-Block umschlossen werden kann, um eine Datei in regelmäßigen Abständen mit [SaveTrackedAsync][net_savetrackedasync] zu aktualisieren.

Der Knoten-Agent ist ein Programm, das auf jedem Knoten im Pool ausgeführt wird. Er stellt die Befehls- und Steuerungsschnittstelle zwischen dem Knoten und dem Batch-Dienst dar. Der `Task.Delay`-Aufruf wird am Ende dieses `using`-Blocks benötigt, um sicherzustellen, dass der Knoten-Agent genügend Zeit zum Leeren des Inhalts des Standards in die stdout.txt-Datei auf dem Knoten hat. Ohne diese Verzögerung gehen unter Umständen die letzten Sekunden der Ausgabe verloren. Diese Verzögerung ist möglicherweise nicht für alle Dateien erforderlich.

> [!NOTE]
> Wenn Sie die Dateinachverfolgung mit **SaveTrackedAsync** aktivieren, werden nur *Anfügungen* der nachverfolgten Datei in Azure Storage beibehalten. Verwenden Sie diese Methode zum Nachverfolgen von nicht rotierenden Protokolldateien oder anderen Dateien, die mit Anhängevorgängen an das Ende einer Datei geschrieben werden.

## <a name="retrieve-output-data"></a>Abrufen der Ausgabedateien

Wenn Sie die dauerhaft gespeicherte Ausgabe mithilfe der Dateikonventionenbibliothek von Azure Batch abrufen, wird dieser Vorgang task- oder auftragsbasiert ausgeführt. Sie können die Ausgabe für einen bestimmten Task oder Auftrag anfordern, ohne den Pfad in Azure Storage oder den Dateinamen kennen zu müssen. Sie können stattdessen Ausgabedateien nach Task- oder Auftrags-ID anfordern.

Der folgende Codeausschnitt durchläuft die Tasks eines Auftrags, gibt einige Informationen zu den Ausgabedateien für den Task aus und lädt dann die Dateien aus Storage herunter.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (OutputFileReference output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="view-output-files-in-the-azure-portal"></a>Anzeigen von Ausgabedateien im Azure-Portal

Im Azure-Portal werden Taskausgabedateien und Protokolle angezeigt, die unter Verwendung der [Batch-Dateikonventionenstandards](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) dauerhaft in einem verknüpften Azure Storage-Konto gespeichert werden. Sie können diese Konventionen in Ihrer bevorzugten Sprache implementieren oder die Dateikonventionenbibliothek in Ihren .NET-Anwendungen verwenden.

Um die Anzeige Ihrer Ausgabedateien im Portal zu aktivieren, müssen die folgenden Voraussetzungen erfüllt sein:

1. Verknüpfen Sie ein Azure Storage-Konto mit Ihrem Batch-Konto.
1. Halten Sie sich an die vordefinierten Namenskonventionen für Storage-Container und Dateien, wenn Sie Ausgaben dauerhaft speichern. Eine Definition dieser Konventionen finden Sie in der [Infodatei][github_file_conventions_readme] zur Dateikonventionenbibliothek. Wenn Sie die [Azure Batch-Dateikonventionen][nuget_package]-Bibliothek zur Beibehaltung der Ausgabe verwenden, werden Ihre Dateien gemäß dem Dateikonventionenstandard beibehalten.

Wenn Sie Taskausgabedateien und Protokolle im Azure-Portal anzeigen möchten, navigieren Sie zum Task, dessen Ausgabe angezeigt werden soll, und klicken Sie auf **Gespeicherte Ausgabedateien** oder auf **Gespeicherte Protokolle**. Diese Abbildung zeigt die **gespeicherten Ausgabedateien** für den Task mit der ID 007:

![Taskausgabeblatt im Azure-Portal][2]

## <a name="code-sample"></a>Codebeispiel

Das Beispielprojekt [PersistOutputs][github_persistoutputs] ist eines der [Azure Batch-Codebeispiele][github_samples] auf GitHub. Diese Visual Studio-Projektmappe veranschaulicht, wie die Azure Batch-Bibliothek für Dateikonventionen verwendet werden kann, um die Taskausgabe in dauerhaftem Speicher beizubehalten. Gehen Sie folgendermaßen vor, um das Beispiel auszuführen:

1. Öffnen Sie das Projekt in **Visual Studio 2019**.
2. Fügen Sie die **Anmeldeinformationen** für Ihr Batch- und Storage-Konto zu **AccountSettings.settings** im Microsoft.Azure.Batch.Samples.Common-Projekt hinzu.
3. **Erstellen** Sie die Lösung (aber führen Sie sie nicht aus). Stellen Sie NuGet-Pakete wieder her, wenn Sie dazu aufgefordert werden.
4. Laden Sie im Azure-Portal ein [Anwendungspaket](batch-application-packages.md) für **PersistOutputsTask**hoch. Fügen Sie `PersistOutputsTask.exe` und die abhängigen Assemblys dem ZIP-Paket hinzu, und legen Sie die Anwendungs-ID auf „PersistOutputsTask“ und die Version des Anwendungspakets auf „1.0“ fest.
5. **Starten** Sie das **PersistOutputs**-Projekt (d.h. führen Sie es aus).
6. Geben Sie **1** ein, wenn Sie aufgefordert werden, die Persistenztechnologie für die Ausführung des Beispiels auszuwählen. Hierdurch wird das Beispiel mithilfe der Dateikonventionenbibliothek ausgeführt, um die Taskausgabe beizubehalten. 

## <a name="next-steps"></a>Nächste Schritte

### <a name="get-the-batch-file-conventions-library-for-net"></a>Abrufen der Batch-Dateikonventionenbibliothek für .NET

Die Batch-Dateikonventionenbibliothek für .NET finden Sie unter [NuGet][nuget_package]. Die Bibliothek erweitert die Klassen [CloudJob][net_cloudjob] und [CloudTask][net_cloudtask] durch neue Methoden. Informationen zur Dateikonventionenbibliothek finden Sie auch in der [Referenzdokumentation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files).

Der [Quellcode][github_file_conventions] für die Dateikonventionenbibliothek ist auf GitHub im „Microsoft Azure-SDK für .NET“-Repository erhältlich. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Entdecken Sie andere Ansätze zum Beibehalten von Ausgabedaten

- Unter [Beibehalten von Auftrags- und Taskausgaben in Azure Storage](batch-task-output.md) finden Sie eine Übersicht über das dauerhafte Speichern von Task- und Auftragsdateien.
- Unter [Beibehalten von Taskdateien mithilfe der Batch-Dienst-API in Azure Storage](batch-task-output-files.md) erfahren Sie, wie Sie die Batch-Dienst-API zum Beibehalten von Ausgabedaten verwenden.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage._cloud_storage_account
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Auswahl von „Gespeicherte Ausgabedateien“ und „Gespeicherte Protokolle“ im Portal"
[2]: ./media/batch-task-output/task-output-02.png "Taskausgabeblatt im Azure-Portal"
