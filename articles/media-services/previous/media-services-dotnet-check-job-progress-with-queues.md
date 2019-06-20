---
title: Verwenden von Azure Queue Storage zum Überwachen von Media Services-Auftragsbenachrichtigungen mit .NET | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Media Services-Auftragsbenachrichtigungen mithilfe des Azure-Warteschlangenspeichers überwachen. Das Codebeispiel ist in C# geschrieben und verwendet das Media Services SDK für .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: f535d0b5-f86c-465f-81c6-177f4f490987
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 2a7f15eb7e90ba4dec9bc614a45d2de46c07bdfd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64868102"
---
# <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications-with-net"></a>Verwenden von Azure-Warteschlangenspeicher zum Überwachen von Media Services-Auftragsbenachrichtigungen mit .NET 

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

Beim Ausführen von Codierungsaufträgen ist es nützlich, deren Status nachverfolgen zu können. Sie können Media Services zur Übermittlung von Benachrichtigungen an [Azure Queue Storage](../../storage/storage-dotnet-how-to-use-queues.md) konfigurieren. Sie können den Status des Auftrags überwachen, indem Sie Benachrichtigungen aus Queue Storage abrufen. 

Auf die an den Warteschlangenspeicher übermittelten Nachrichten kann von überall auf der Welt aus zugegriffen werden. Die Benachrichtigungsarchitektur von Queue Storage ist zuverlässig und hochgradig skalierbar. Das Abrufen von Nachrichten aus Queue Storage wird gegenüber anderen Methoden empfohlen.

Häufig werden Media Services-Benachrichtigungen überwacht, wenn Sie ein Content Management System entwickeln, das nach Abschluss der Codierung eines Auftrags einige zusätzliche Aufgaben durchführen soll (z.B. den nächsten Schritt in einem Workflow auslösen oder Inhalte veröffentlichen).

In diesem Artikel wird gezeigt, wie Sie Benachrichtigungen aus Queue Storage abrufen.  

## <a name="considerations"></a>Überlegungen
Beachten Sie Folgendes beim Entwickeln von Media Services-Anwendungen, die Queue Storage verwenden:

* Queue Storage bietet keine Garantie, dass die Übermittlung nach First-in-First-out (FIFO) sortiert erfolgt. Weitere Informationen finden sie unter [Azure-Warteschlangen und Azure Service Bus-Warteschlangen – Vergleich und Gegenüberstellung](https://msdn.microsoft.com/library/azure/hh767287.aspx).
* Queue Storage ist kein Pushdienst. Sie müssen die Warteschlange abfragen.
* Sie können eine beliebige Anzahl von Warteschlangen verwenden. Weitere Informationen finden Sie unter [REST-API des Warteschlangendiensts](https://docs.microsoft.com/rest/api/storageservices/Queue-Service-REST-API).
* Queue Storage verfügt über einige Einschränkungen und Besonderheiten, die Sie berücksichtigen müssen. Diese werden unter [Azure-Warteschlangen und Azure Service Bus-Warteschlangen – Vergleich und Gegenüberstellung](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted) besprochen.

## <a name="net-code-example"></a>Codebeispiel für .NET

Das Codebeispiel in diesem Abschnitt erfüllt die folgenden Aufgaben:

1. Definiert die **EncodingJobMessage** -Klasse, die das Format der Benachrichtigung zuordnet. Der Code deserialisiert Nachrichten aus der Warteschlange in Objekte vom Typ **EncodingJobMessage** .
2. Lädt die Informationen zu Media Services und Speicherkonto aus der Datei "app.config". Das Codebeispiel verwendet diese Informationen zum Erstellen des **CloudMediaContext**-Objekts und des **CloudQueue**-Objekts.
3. Erstellt die Warteschlange, die Benachrichtigungen zum Codierungsauftrag empfängt.
4. Erstellt den Endpunkt für Benachrichtigungen, der der Warteschlange zugeordnet ist.
5. Fügt den Endpunkt für die Benachrichtigung an den Auftrag an und übermittelt den Codierungsauftrag. Sie können mehrere Benachrichtigungsendpunkte an einen Auftrag anfügen.
6. Übergibt **NotificationJobState.FinalStatesOnly** an die **AddNew**-Methode. (In diesem Beispiel sind wir nur am abschließenden Status der Verarbeitung des Auftrags interessiert.)

        job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
7. Wenn Sie **NotificationJobState.All** übergeben, erhalten Sie damit alle folgenden Benachrichtigungen über Statusänderungen: „Warteschlange“, „Geplant“, „Verarbeitung“ und „Abgeschlossen“. Wie aber bereits erwähnt, garantiert Queue Storage keine geordnete Übermittlung. Sie können die **Timestamp**-Eigenschaft (im Beispiel unten im Typ **EncodingJobMessage** definiert) zum Ordnen der Nachrichten verwenden. Doppelte Nachrichten sind möglich. Mithilfe der **ETag**-Eigenschaft (im Typ **EncodingJobMessage** definiert) können Sie nach Duplikaten suchen. Andererseits kann es auch vorkommen, dass Statusänderungsbenachrichtigungen ausgelassen werden.
8. Wartet, bis der Auftrag abgeschlossen ist, indem er die Warteschlange alle zehn Sekunden überprüft. Löscht Nachrichten, nachdem sie verarbeitet wurden.
9. Löscht die Warteschlange und den Benachrichtigungsendpunkt.

> [!NOTE]
> Die empfohlene Methode zum Überwachen des Auftragsstatus ist das Überwachen von Benachrichtigungen, wie im folgenden Beispiel gezeigt:
>
> Sie können aber auch den Status eines Auftrags mithilfe der **IJob.State** -Eigenschaft überprüfen.  Eine Benachrichtigung zum Abschluss eines Auftrags kann eintreffen, bevor der Zustand für **IJob** auf **Abgeschlossen** festgelegt wurde. Die **IJob.State**-Eigenschaft gibt den korrekten Zustand leicht verzögert an.
>
>

### <a name="create-and-configure-a-visual-studio-project"></a>Erstellen und Konfigurieren eines Visual Studio-Projekts

1. Richten Sie Ihre Entwicklungsumgebung ein, und füllen Sie die Datei „app.config“ mit Verbindungsinformationen, wie unter [Media Services-Entwicklung mit .NET](media-services-dotnet-how-to-use.md) beschrieben. 
2. Erstellen Sie (unter einem beliebigen Pfad auf Ihrem lokalen Laufwerk) einen neuen Ordner, und kopieren Sie eine MP4-Datei, die Sie codieren und streamen oder progressiv herunterladen möchten. In diesem Beispiel wird der Pfad „C:\Media“ verwendet.
3. Fügen Sie der Bibliothek **System.Runtime.Serialization** einen Verweis hinzu.

### <a name="code"></a>Code

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Collections.Generic;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Queue;
using System.Runtime.Serialization.Json;

namespace JobNotification
{
    public class EncodingJobMessage
    {
        // MessageVersion is used for version control.
        public String MessageVersion { get; set; }

        // Type of the event. Valid values are
        // JobStateChange and NotificationEndpointRegistration.
        public String EventType { get; set; }

        // ETag is used to help the customer detect if
        // the message is a duplicate of another message previously sent.
        public String ETag { get; set; }

        // Time of occurrence of the event.
        public String TimeStamp { get; set; }

        // Collection of values specific to the event.

        // For the JobStateChange event the values are:
        //     JobId - Id of the Job that triggered the notification.
        //     NewState- The new state of the Job. Valid values are:
        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
        //     OldState- The old state of the Job. Valid values are:
        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished

        // For the NotificationEndpointRegistration event the values are:
        //     NotificationEndpointId- Id of the NotificationEndpoint
        //          that triggered the notification.
        //     State- The state of the Endpoint.
        //          Valid values are: Registered and Unregistered.

        public IDictionary<string, object> Properties { get; set; }
    }

    class Program
    {

        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly string _StorageConnectionString = 
            ConfigurationManager.AppSettings["StorageConnectionString"];

        private static CloudMediaContext _context = null;
        private static CloudQueue _queue = null;
        private static INotificationEndPoint _notificationEndPoint = null;

        private static readonly string _singleInputMp4Path =
            Path.GetFullPath(@"C:\Media\BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            string endPointAddress = Guid.NewGuid().ToString();

            // Create the context.
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
            
            // Create the queue that will be receiving the notification messages.
            _queue = CreateQueue(_StorageConnectionString, endPointAddress);

            // Create the notification point that is mapped to the queue.
            _notificationEndPoint =
                    _context.NotificationEndPoints.Create(
                    Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);


            if (_notificationEndPoint != null)
            {
                IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
                WaitForJobToReachedFinishedState(job.Id);
            }

            // Clean up.
            _queue.Delete();
            _notificationEndPoint.Delete();
        }


        static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);

            // Create the queue client
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

            // Retrieve a reference to a queue
            CloudQueue queue = queueClient.GetQueueReference(endPointAddress);

            // Create the queue if it doesn't already exist
            queue.CreateIfNotExists();

            return queue;
        }


        public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");

            //Create an encrypted asset and upload the mp4.
            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted,
                inputMediaFilePath);

            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the conversion details, using a configuration file.
            ITask task = job.Tasks.AddNew("My encoding Task",
                processor,
                "Adaptive Streaming",
                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Add a notification point to the job. You can add multiple notification points.  
            job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly,
                _notificationEndPoint);

            job.Submit();

            return job;
        }

        public static void WaitForJobToReachedFinishedState(string jobId)
        {
            int expectedState = (int)JobState.Finished;
            int timeOutInSeconds = 600;

            bool jobReachedExpectedState = false;
            DateTime startTime = DateTime.Now;
            int jobState = -1;

            while (!jobReachedExpectedState)
            {
                // Specify how often you want to get messages from the queue.
                Thread.Sleep(TimeSpan.FromSeconds(10));

                foreach (var message in _queue.GetMessages(10))
                {
                    using (Stream stream = new MemoryStream(message.AsBytes))
                    {
                        DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
                        settings.UseSimpleDictionaryFormat = true;
                        DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
                        EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);

                        Console.WriteLine();

                        // Display the message information.
                        Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
                        Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
                        Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
                        Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
                        foreach (var property in encodingJobMsg.Properties)
                        {
                            Console.WriteLine("    {0}: {1}", property.Key, property.Value);
                        }

                        // We are only interested in messages
                        // where EventType is "JobStateChange".
                        if (encodingJobMsg.EventType == "JobStateChange")
                        {
                            string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
                            if (JobId == jobId)
                            {
                                string oldJobStateStr = (String)encodingJobMsg.Properties.
                                                            Where(j => j.Key == "OldState").FirstOrDefault().Value;
                                string newJobStateStr = (String)encodingJobMsg.Properties.
                                                            Where(j => j.Key == "NewState").FirstOrDefault().Value;

                                JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
                                JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);

                                if (newJobState == (JobState)expectedState)
                                {
                                    Console.WriteLine("job with Id: {0} reached expected state: {1}",
                                        jobId, newJobState);
                                    jobReachedExpectedState = true;
                                    break;
                                }
                            }
                        }
                    }
                    // Delete the message after we've read it.
                    _queue.DeleteMessage(message);
                }

                // Wait until timeout
                TimeSpan timeDiff = DateTime.Now - startTime;
                bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
                if (timedOut)
                {
                    Console.WriteLine(@"Timeout for checking job notification messages,
                                        latest found state ='{0}', wait time = {1} secs",
                        jobState,
                        timeDiff.TotalSeconds);

                    throw new TimeoutException();
                }
            }
        }

        static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(),
                assetCreationOptions);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);
            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading of {0}", assetFile.Name);

            return asset;
        }

        static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

Das Beispiel oben generiert die folgende Ausgabe: Die Werte können variieren.

    Created assetFile BigBuckBunny.mp4
    Upload BigBuckBunny.mp4
    Done uploading of BigBuckBunny.mp4

    EventType: NotificationEndPointRegistration
    MessageVersion: 1.0
    ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
    TimeStamp: 2013-05-14T20:22:37
        NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
        State: Registered
        Name: dde957b2-006e-41f2-9869-a978870ac620
        Created: 2013-05-14T20:22:35

    EventType: JobStateChange
    MessageVersion: 1.0
    ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
    TimeStamp: 2013-05-14T20:24:40
        JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
        JobName: My MP4 to Smooth Streaming encoding job
        NewState: Finished
        OldState: Processing
        AccountName: westeuropewamsaccount
    job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected
    State: Finished


## <a name="next-step"></a>Nächster Schritt
Überprüfen Sie die Media Services-Lernpfade.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
