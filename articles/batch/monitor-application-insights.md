---
title: Überwachen von Batch mit Azure Application Insights | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Azure Batch-.NET-Anwendung mithilfe der Azure Application Insights-Bibliothek instrumentieren.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: .NET
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: c527b0b10a2b9a351b242d0858fdbe64687970a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65595289"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Überwachen und Debuggen einer Azure Batch-.NET-Anwendung mit Application Insights

[Application Insights](../azure-monitor/app/app-insights-overview.md) bietet Entwicklern eine elegante und effektive Methode zum Überwachen und Debuggen von in Azure-Diensten bereitgestellten Anwendungen. Mit Application Insights können Sie Leistungsindikatoren und Ausnahmen überwachen sowie Ihren Code mit benutzerdefinierten Metriken und einer Ablaufverfolgung instrumentieren. Die Integration von Application Insights in Ihre Azure Batch-Anwendung bietet Ihnen die Möglichkeit, umfassende Einblicke in das Verhalten zu gewinnen und Probleme nahezu in Echtzeit zu untersuchen.

In diesem Artikel wird beschrieben, wie Sie die Application Insights-Bibliothek in Ihrer Azure Batch-.NET-Lösung hinzufügen und konfigurieren und den Anwendungscode instrumentieren. Zudem werden Methoden zum Überwachen Ihrer Anwendung über das Azure-Portal und Erstellen benutzerdefinierter Dashboards vorgestellt. Informationen zur Unterstützung von Application Insights in anderen Programmiersprachen finden Sie in der [Dokumentation zu Sprachen, Plattformen und Integrationen](../azure-monitor/app/platforms.md).

Für diesen Artikel ist auf [GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights) eine begleitende C#-Beispiellösung mit Code verfügbar. In diesem Beispiel wird dem Beispiel [TopNWords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) Application Insights-Instrumentierungscode hinzugefügt. Falls Sie nicht mit diesem Beispiel vertraut sind, sollten Sie versuchen, zunächst TopNWords zu erstellen und auszuführen. Dadurch können Sie sich mit dem grundlegenden Batch-Workflow der parallelen Verarbeitung eines Satzes von Eingabeblobs auf mehreren Computeknoten vertraut machen. 

> [!TIP]
> Konfigurieren Sie alternativ dazu die Batch-Lösung zum Anzeigen von Application Insights-Daten (etwa VM-Leistungsindikatoren) in Batch Explorer. Der [Batch Explorer](https://github.com/Azure/BatchExplorer) ist ein kostenloses eigenständiges Clienttool mit zahlreichen Features, das Sie beim Erstellen, Debuggen und Überwachen von Azure Batch-Anwendungen unterstützt. Ein Installationspaket für Mac, Linux oder Windows können Sie [hier](https://azure.github.io/BatchExplorer/) herunterladen. Im [Batch Insights-Repository](https://github.com/Azure/batch-insights) finden Sie schnelle Schritte zum Aktivieren von Application Insights-Daten im Batch Explorer. 
>

## <a name="prerequisites"></a>Voraussetzungen
* [Visual Studio 2017 oder höher](https://www.visualstudio.com/vs)

* [Ein Batch-Konto und ein verknüpftes Speicherkonto](batch-account-create-portal.md)

* [Eine Application Insights-Ressource](../azure-monitor/app/create-new-resource.md )
  
   * Verwenden Sie das Azure-Portal, um eine Application Insights-*Ressource* zu erstellen. Klicken Sie auf *Allgemein* **Anwendungstyp**.

   * Kopieren Sie den [Instrumentierungsschlüssel](../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) aus dem Portal. Sie benötigen ihn an späterer Stelle dieses Artikels.
  
  > [!NOTE]
  > Möglicherweise werden Ihnen Gebühren für die in Application Insights gespeicherten Daten [in Rechnung gestellt](https://azure.microsoft.com/pricing/details/application-insights/). Dies gilt auch für die in diesem Artikel beschriebenen Diagnose- und Überwachungsdaten.
  > 

## <a name="add-application-insights-to-your-project"></a>Hinzufügen von Application Insights zu Ihrem Projekt

Für Ihr Projekt benötigen Sie das NuGet-Paket **Microsoft.ApplicationInsights.WindowsServer** und die zugehörigen Abhängigkeiten. Fügen Sie sie dem Projekt Ihrer Anwendung hinzu, oder stellen Sie sie darin wieder her. Verwenden Sie zum Installieren des Pakets den Befehl `Install-Package` oder den NuGet-Paket-Manager.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Verweisen Sie in Ihrer .NET-Anwendung mit dem Namespace **Microsoft.ApplicationInsights** auf Application Insights.

## <a name="instrument-your-code"></a>Instrumentieren des Codes

Um Ihren Code zu instrumentieren, muss Ihre Lösung einen Application Insights-[TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient) erstellen. Im Beispiel lädt der TelemetryClient seine Konfiguration aus der Datei [ApplicationInsights.config](../azure-monitor/app/configuration-with-applicationinsights-config.md). Denken Sie daran, die Datei „ApplicationInsights.config“ in den folgenden Projekten mit Ihrem Application Insights-Instrumentierungsschlüssel zu aktualisieren: „Microsoft.Azure.Batch.Samples.TelemetryStartTask“ und „TopNWordsSample“.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Fügen Sie den Instrumentierungsschlüssel auch in der Datei „TopNWords.cs“ hinzu.

Das Beispiel in „TopNWords.cs“ verwendet die folgenden [Instrumentierungsaufrufe](../azure-monitor/app/api-custom-events-metrics.md) aus der Application Insights-API:
* `TrackMetric()`: Verfolgt nach, wie lange ein Computeknoten im Durchschnitt zum Herunterladen der erforderlichen Textdatei benötigt.
* `TrackTrace()`: Fügt dem Code Debugaufrufe hinzu.
* `TrackEvent()`: Verfolgt relevante Ereignisse nach, die aufgezeichnet werden sollen.

In diesem Beispiel wurde die Ausnahmebehandlung bewusst weggelassen. Stattdessen meldet Application Insights Ausnahmefehler automatisch, wodurch der Debugvorgang erheblich verbessert wird. 

Der folgende Codeausschnitt veranschaulicht die Verwendung dieser Methoden.

```csharp
public void CountWords(string blobName, int numTopN, string storageAccountName, string storageAccountKey)
{
    // simulate exception for some set of tasks
    Random rand = new Random();
    if (rand.Next(0, 10) % 10 == 0)
    {
        blobName += ".badUrl";
    }

    // log the url we are downloading the file from
    insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Download file from: {1}", this.taskId, blobName), SeverityLevel.Verbose));

    // open the cloud blob that contains the book
    var storageCred = new StorageCredentials(storageAccountName, storageAccountKey);
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), storageCred);
    using (Stream memoryStream = new MemoryStream())
    {
        // calculate blob download time
        DateTime start = DateTime.Now;
        blob.DownloadToStream(memoryStream);
        TimeSpan downloadTime = DateTime.Now.Subtract(start);

        // track how long the blob takes to download on this node
        // this will help debug timing issues or identify poorly performing nodes
        insightsClient.TrackMetric("Blob download in seconds", downloadTime.TotalSeconds, this.CommonProperties);

        memoryStream.Position = 0; //Reset the stream
        var sr = new StreamReader(memoryStream);
        var myStr = sr.ReadToEnd();
        string[] words = myStr.Split(' ');

        // log how many words were found in the text file
        insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Found {1} words", this.taskId, words.Length), SeverityLevel.Verbose));
        var topNWords =
            words.
                Where(word => word.Length > 0).
                GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
                OrderByDescending(x => x.Value).
                Take(numTopN).
                ToList();
        foreach (var pair in topNWords)
        {
            Console.WriteLine("{0} {1}", pair.Key, pair.Value);
        }

        // emit an event to track the completion of the task
        insightsClient.TrackEvent("Done counting words");
    }
}
```

### <a name="azure-batch-telemetry-initializer-helper"></a>Azure Batch-Telemetrie-Initialisierer-Hilfsprogramm
Beim Melden von Telemetriedaten für einen bestimmten Server und eine bestimmte Instanz verwendet Application Insights die Azure-VM-Rolle und den VM-Namen für die Standardwerte. Im Kontext von Azure Batch zeigt das Beispiel, wie stattdessen der Name des Pools und der Name des Computeknotens verwendet werden. Verwenden Sie einen [Telemetrie-Initialisierer](../azure-monitor/app/api-filtering-sampling.md#add-properties), um die Standardwerte außer Kraft zu setzen. 

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using System;
using System.Threading;

namespace Microsoft.Azure.Batch.Samples.TelemetryInitializer
{
    public class AzureBatchNodeTelemetryInitializer : ITelemetryInitializer
    {
        // Azure Batch environment variables
        private const string PoolIdEnvironmentVariable = "AZ_BATCH_POOL_ID";
        private const string NodeIdEnvironmentVariable = "AZ_BATCH_NODE_ID";

        private string roleInstanceName;
        private string roleName;

        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                // override the role name with the Azure Batch Pool name
                string name = LazyInitializer.EnsureInitialized(ref this.roleName, this.GetPoolName);
                telemetry.Context.Cloud.RoleName = name;
            }

            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // override the role instance with the Azure Batch Compute Node name
                string name = LazyInitializer.EnsureInitialized(ref this.roleInstanceName, this.GetNodeName);
                telemetry.Context.Cloud.RoleInstance = name;
            }
        }

        private string GetPoolName()
        {
            return Environment.GetEnvironmentVariable(PoolIdEnvironmentVariable) ?? string.Empty;
        }

        private string GetNodeName()
        {
            return Environment.GetEnvironmentVariable(NodeIdEnvironmentVariable) ?? string.Empty;
        }
    }
}
```

Zum Aktivieren des Telemetrie-Initialisierers enthält die Datei „ApplicationInsights.config“ im Projekt „TopNWordsSample“ Folgendes:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Aktualisieren des Auftrags und der Aufgaben zum Einschließen der Application Insights-Binärdateien

Damit Application Insights korrekt auf Ihren Computeknoten ausgeführt wird, müssen die Binärdateien richtig platziert werden. Fügen Sie die erforderlichen Binärdateien der Sammlung von Ressourcendateien Ihrer Aufgabe hinzu, sodass sie bei der Ausführung der Aufgabe heruntergeladen werden. Die folgenden Codeausschnitte ähneln Code in „Job.cs“.

Erstellen Sie zunächst eine statische Liste der Application Insights-Dateien, die hochgeladen werden sollen.

```csharp
private static readonly List<string> AIFilesToUpload = new List<string>()
{
    // Application Insights config and assemblies
    "ApplicationInsights.config",
    "Microsoft.ApplicationInsights.dll",
    "Microsoft.AI.Agent.Intercept.dll",
    "Microsoft.AI.DependencyCollector.dll",
    "Microsoft.AI.PerfCounterCollector.dll",
    "Microsoft.AI.ServerTelemetryChannel.dll",
    "Microsoft.AI.WindowsServer.dll",
    
    // custom telemetry initializer assemblies
    "Microsoft.Azure.Batch.Samples.TelemetryInitializer.dll",
 };
...
```

Erstellen Sie anschließend die Stagingdateien, die von der Aufgabe verwendet werden.
```csharp
...
// create file staging objects that represent the executable and its dependent assembly to run as the task.
// These files are copied to every node before the corresponding task is scheduled to run on that node.
FileToStage topNWordExe = new FileToStage(TopNWordsExeName, stagingStorageAccount);
FileToStage storageDll = new FileToStage(StorageClientDllName, stagingStorageAccount);

// Upload Application Insights assemblies
List<FileToStage> aiStagedFiles = new List<FileToStage>();
foreach (string aiFile in AIFilesToUpload)
{
    aiStagedFiles.Add(new FileToStage(aiFile, stagingStorageAccount));
}
...
```

Die `FileToStage`-Methode ist eine Hilfsfunktion im Codebeispiel, mit der Sie eine Datei einfach vom lokalen Datenträger in einen Azure Storage-Blob hochladen können. Jede Datei wird später auf einen Computeknoten heruntergeladen und von einer Aufgabe referenziert.

Fügen Sie zum Schluss die Aufgaben dem Auftrag hinzu, und schließen Sie die erforderlichen Application Insights-Binärdateien ein.
```csharp
...
// initialize a collection to hold the tasks that will be submitted in their entirety
List<CloudTask> tasksToRun = new List<CloudTask>(topNWordsConfiguration.NumberOfTasks);
for (int i = 1; i <= topNWordsConfiguration.NumberOfTasks; i++)
{
    CloudTask task = new CloudTask("task_no_" + i, String.Format("{0} --Task {1} {2} {3} {4}",
        TopNWordsExeName,
        string.Format("https://{0}.blob.core.windows.net/{1}",
            accountSettings.StorageAccountName,
            documents[i]),
        topNWordsConfiguration.TopWordCount,
        accountSettings.StorageAccountName,
        accountSettings.StorageAccountKey));

    //This is the list of files to stage to a container -- for each job, one container is created and 
    //files all resolve to Azure Blobs by their name (so two tasks with the same named file will create just 1 blob in
    //the container).
    task.FilesToStage = new List<IFileStagingProvider>
                        {
                            // required application binaries
                            topNWordExe,
                            storageDll,
                        };
    foreach (FileToStage stagedFile in aiStagedFiles)
   {
        task.FilesToStage.Add(stagedFile);
   }    
    task.RunElevated = false;
    tasksToRun.Add(task);
}
```

## <a name="view-data-in-the-azure-portal"></a>Anzeigen von Daten im Azure-Portal

Nachdem Sie den Auftrag und die Aufgaben zur Verwendung von Application Insights konfiguriert haben, führen Sie den Beispielauftrag in Ihrem Pool aus. Navigieren Sie zum Azure-Portal, und öffnen Sie die Application Insights-Ressource, die Sie bereitgestellt haben. Nachdem der Pool bereitgestellt wurde, sollten Sie einen Datenfluss sehen, und es sollten Daten protokolliert werden. Im verbleibenden Teil dieses Artikels werden nur einige Features von Application Insights vorgestellt, Sie können aber natürlich gern sämtliche Features erkunden.

### <a name="view-live-stream-data"></a>Anzeigen von Live Stream-Daten

Um Ablaufverfolgungsprotokolle in Ihrer Application Insights-Ressource anzuzeigen, klicken Sie auf **Live Stream**. Der folgende Screenshot zeigt, wie Sie Livedaten von den Computeknoten im Pool anzeigen, beispielsweise die CPU-Nutzung pro Computeknoten.

![Live Stream-Daten zu Computeknoten](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Anzeigen von Ablaufverfolgungsprotokollen

Um Ablaufverfolgungsprotokolle in Ihrer Application Insights-Ressource anzuzeigen, klicken Sie auf **Suchen**. Diese Ansicht zeigt eine Liste von Diagnosedaten, die von Application Insights erfasst wurden, und enthält auch Ablaufverfolgungen, Ereignisse und Ausnahmen. 

Der folgende Screenshot zeigt, wie eine einzelne Ablaufverfolgung für eine Aufgabe protokolliert und später zu Debugzwecken abgefragt wird.

![Screenshot von Ablaufverfolgungsprotokollen](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Anzeigen von Ausnahmefehlern

Der folgende Screenshot zeigt, wie Application Insights von Ihrer Anwendung ausgelöste Ausnahmen protokolliert. In diesem Fall können Sie innerhalb von Sekunden, nachdem die Anwendung die Ausnahme ausgelöst hat, Details zu einer bestimmten Ausnahme anzeigen und das Problem diagnostizieren.

![Nicht behandelte Ausnahmen](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Messen der Downloadzeit von Blobs

Ein weiteres nützliches Tool, das Ihnen im Portal zur Verfügung steht, sind benutzerdefinierte Metriken. Beispielsweise können Sie die durchschnittliche Zeit anzeigen, die jeder Computeknoten zum Herunterladen der von ihm verarbeiteten erforderlichen Textdatei benötigt hat.

So erstellen Sie ein Beispieldiagramm:
1. Klicken Sie in Ihrer Application Insights-Ressource auf **Metrik-Explorer** > **Diagramm hinzufügen**.
2. Klicken Sie im hinzugefügten Diagramm auf **Bearbeiten**.
2. Aktualisieren Sie die Diagrammdetails wie folgt:
   * Legen Sie **Diagrammtyp** auf **Raster** fest.
   * Legen Sie **Aggregation** auf **Durchschnitt** fest.
   * Legen Sie **Gruppieren nach** auf **NodeId** fest.
   * Klicken Sie unter **Metriken** auf **Benutzerdefiniert** > **Blob download in seconds** (Blobdownload in Sekunden).
   * Passen Sie die **Color palette** (Farbpalette) der Anzeige wie gewünscht an. 

![Blobdownloadzeit pro Knoten](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Kontinuierliche Überwachung von Computeknoten

Möglicherweise haben Sie bemerkt, dass alle Metriken (einschließlich der Leistungsindikatoren) nur während der Ausführung der Aufgaben protokolliert werden. Dieses Verhalten ist nützlich, da es die von Application Insights protokollierte Datenmenge beschränkt. Es gibt jedoch Fälle, in denen Sie die Computeknoten immer überwachen möchten. Beispielsweise kann eine Hintergrundaufgabe ausgeführt werden, die nicht über den Batch-Dienst geplant wird. In diesem Fall richten Sie einen Überwachungsprozess ein, der für die Lebensdauer des Computeknotens ausgeführt wird. 

Eine Möglichkeit dazu besteht darin, einen Prozess zu erzeugen, der die Application Insights-Bibliothek lädt und im Hintergrund ausgeführt wird. Die Startaufgabe im Beispiel lädt die Binärdateien auf den Computer und führt einen Prozess für unbegrenzte Dauer aus. Konfigurieren Sie die Application Insights-Konfigurationsdatei für diesen Prozess zur Ausgabe zusätzlicher Daten, an denen Sie interessiert sind, beispielsweise Leistungsindikatoren.

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = "StartTask";
private const string BatchStartTaskTelemetryRunnerName = "Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe";
private const string BatchStartTaskTelemetryRunnerAIConfig = "ApplicationInsights.config";
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
...

// Create a start task which will run a dummy exe in background that simply emits performance
// counter data as defined in the relevant ApplicationInsights.config.
// Note that the waitForSuccess on the start task was not set so the Compute Node will be
// available immediately after this command is run.
pool.StartTask = new StartTask()
{
    CommandLine = string.Format("cmd /c {0}", BatchStartTaskTelemetryRunnerName),
    ResourceFiles = resourceFiles
};
...
```

> [!TIP]
> Um die Verwaltbarkeit Ihrer Lösung zu verbessern, können Sie die Assembly in einem [Anwendungspaket](./batch-application-packages.md) bündeln. Fügen Sie der Poolkonfiguration dann einen Verweis auf das Anwendungspaket hinzu, um es automatisch in Ihren Pools bereitzustellen.
>

## <a name="throttle-and-sample-data"></a>Drosselung und Beispieldaten 

Da in der Produktionsumgebung ausgeführte Azure Batch-Anwendungen sehr umfangreich sind, möchten Sie möglicherweise zur Kostenkontrolle die Menge an Daten begrenzen, die von Application Insights erfasst werden. Einige Mechanismen, die Sie dazu verwenden können, finden Sie unter [Erstellen von Stichproben in Application Insights](../azure-monitor/app/sampling.md).


## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [Application Insights](../azure-monitor/app/app-insights-overview.md).

* Informationen zur Unterstützung von Application Insights in anderen Programmiersprachen finden Sie in der [Dokumentation zu Sprachen, Plattformen und Integrationen](../azure-monitor/app/platforms.md).


