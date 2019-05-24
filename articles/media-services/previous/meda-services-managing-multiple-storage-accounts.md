---
title: Verwalten von Media Services-Medienobjekten für mehrere Speicherkonten | Microsoft-Dokumentation
description: In diesem Artikel erhalten Sie Anweisungen zum Verwalten von Media Services-Medienobjekten für mehrere Speicherkonten.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 252d5e551dad56108ad952eb0c7c3b39df0585d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66169276"
---
# <a name="managing-media-services-assets-across-multiple-storage-accounts"></a>Verwalten von Media Services-Medienobjekten über mehrere Speicherkonten  

Sie können mehrere Speicherkonten an ein einzelnes Media Services-Konto anfügen. Die Möglichkeit, mehrere Speicherkonten an ein Media Services-Konto anzufügen, bietet die folgenden Vorteile:

* Lastenausgleich der Assets für mehrere Speicherkonten.
* Skalierung von Media Services für umfangreiche Inhaltsverarbeitung (derzeit ist ein Speicherkonto auf eine maximale Größe von 500 TB beschränkt). 

In diesem Artikel wird erläutert, wie mehrere Speicherkonten mit [Azure Resource Manager-APIs](/rest/api/media/operations/azure-media-services-rest-api-reference) und [PowerShell](/powershell/module/az.media) an ein Media Services-Konto angefügt werden. Es wird zudem beschrieben, wie beim Erstellen von Medienobjekten mit dem Media Services-SDK verschiedene Speicherkonten angegeben werden. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="considerations"></a>Überlegungen

Wenn Sie Ihrem Media Services-Konto mehrere Speicherkonten zuordnen, gelten die folgenden Überlegungen:

* Das Media Services-Konto und alle zugeordneten Speicherkonten müssen im gleichen Azure-Abonnement enthalten sein. Es wird empfohlen, Speicherkonten zu verwenden, die sich an demselben Ort wie das Media Services-Konto befinden.
* Ein Speicherkonto kann nicht mehr getrennt werden, nachdem es an ein Media Services-Konto angefügt wurde.
* Das primäre Speicherkonto wird zum Zeitpunkt der Erstellung des Media Services-Kontos angegeben. Derzeit können Sie das Standardspeicherkonto nicht ändern. 
* Wenn Sie ein Konto für kalten Speicher zu einem AMS-Konto hinzufügen möchten, muss das Speicherkonto ein Blobtyp sein. Zudem muss es auf nicht primär festgelegt sein.

Weitere Überlegungen:

Media Services verwendet beim Erstellen von URLs für den Streaminginhalt den Wert der **IAssetFile.Name**-Eigenschaft (z.B. http://{WAMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters). Aus diesem Grund ist die Prozentkodierung nicht zulässig. Der Wert der „Name“-Eigenschaft darf keines der folgenden für die [Prozentcodierung reservierten Zeichen](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) enthalten: !*'();:@&=+$,/?%#[]". Darüber hinaus darf „.“ nur einmal für die Dateinamenerweiterung vorhanden sein.

## <a name="to-attach-storage-accounts"></a>So fügen Sie Speicherkonten an  

Um Ihre Speicherkonten Ihrem AMS-Konto anzufügen, verwenden Sie [Azure Resource Manager-APIs](/rest/api/media/operations/azure-media-services-rest-api-reference) und [PowerShell](/powershell/module/az.media), wie im folgenden Beispiel gezeigt:

    $regionName = "West US"
    $subscriptionId = " xxxxxxxx-xxxx-xxxx-xxxx- xxxxxxxxxxxx "
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccount1Name = "skystorage1"
    $storageAccount2Name = "skystorage2"
    $storageAccount1Id = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccount1Name"
    $storageAccount2Id = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccount2Name"
    $storageAccount1 = New-AzMediaServiceStorageConfig -StorageAccountId $storageAccount1Id -IsPrimary
    $storageAccount2 = New-AzMediaServiceStorageConfig -StorageAccountId $storageAccount2Id
    $storageAccounts = @($storageAccount1, $storageAccount2)
    
    Set-AzMediaService -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccounts $storageAccounts

### <a name="support-for-cool-storage"></a>Unterstützung für kalten Speicher

Wenn Sie gegenwärtig ein Konto für kalten Speicher zu einem AMS-Konto hinzufügen möchten, muss das Speicherkonto ein Blobtyp sein. Zudem muss es auf nicht primär festgelegt sein.

## <a name="to-manage-media-services-assets-across-multiple-storage-accounts"></a>So verwalten Sie Media Services-Medienobjekte für mehrere Speicherkonten
Im folgenden Code werden mit dem aktuellen Media Services-SDK die folgenden Aufgaben ausgeführt:

1. Anzeigen aller Speicherkonten, die dem angegebenen Media Services-Konto zugeordnet sind.
2. Abrufen des Namens des Standardspeicherkontos.
3. Erstellen eines neuen Medienobjekts im Standardspeicherkonto.
4. Erstellen eines Ausgabemedienobjekts für den Codierungsauftrag im angegebenen Speicherkonto.
   
```cs
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace MultipleStorageAccounts
{
    class Program
    {
        // Location of the media file that you want to encode. 
        private static readonly string _singleInputFilePath =
            Path.GetFullPath(@"../..\supportFiles\multifile\interview2.wmv");

        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Display the storage accounts associated with 
            // the specified Media Services account:
            foreach (var sa in _context.StorageAccounts)
                Console.WriteLine(sa.Name);

            // Retrieve the name of the default storage account.
            var defaultStorageName = _context.StorageAccounts.Where(s => s.IsDefault == true).FirstOrDefault();
            Console.WriteLine("Name: {0}", defaultStorageName.Name);
            Console.WriteLine("IsDefault: {0}", defaultStorageName.IsDefault);

            // Retrieve the name of a storage account that is not the default one.
            var notDefaultStorageName = _context.StorageAccounts.Where(s => s.IsDefault == false).FirstOrDefault();
            Console.WriteLine("Name: {0}", notDefaultStorageName.Name);
            Console.WriteLine("IsDefault: {0}", notDefaultStorageName.IsDefault);

            // Create the original asset in the default storage account.
            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None,
                defaultStorageName.Name, _singleInputFilePath);
            Console.WriteLine("Created the asset in the {0} storage account", asset.StorageAccountName);

            // Create an output asset of the encoding job in the other storage account.
            IAsset outputAsset = CreateEncodingJob(asset, notDefaultStorageName.Name, _singleInputFilePath);
            if (outputAsset != null)
                Console.WriteLine("Created the output asset in the {0} storage account", outputAsset.StorageAccountName);

        }

        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string storageName, string singleFilePath)
        {
            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();

            // If you are creating an asset in the default storage account, you can omit the StorageName parameter.
            var asset = _context.Assets.Create(assetName, storageName, assetCreationOptions);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);

            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return asset;
        }

        static IAsset CreateEncodingJob(IAsset asset, string storageName, string inputMediaFilePath)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("My encoding job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "Adaptive Streaming",
                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset", storageName,
                AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish. 
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // Get an updated job reference.
            job = GetJob(job.Id);

            // If job state is Error the event handling 
            // method for job progress should log errors.  Here we check 
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                Console.WriteLine("\nExiting method due to job error.");
                return null;
            }

            // Get a reference to the output asset from the job.
            IAsset outputAsset = job.OutputMediaAssets[0];

            return outputAsset;
        }

        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }

        private static void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("********************");
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine("Please wait while local tasks or downloads complete...");
                    Console.WriteLine("********************");
                    Console.WriteLine();
                    Console.WriteLine();
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
                    // Display or log error details as needed.
                    Console.WriteLine("An error occurred in {0}", job.Id);
                    break;
                default:
                    break;
            }
        }

        static IJob GetJob(string jobId)
        {
            // Use a Linq select query to get an updated 
            // reference by Id. 
            var jobInstance =
                from j in _context.Jobs
                where j.Id == jobId
                select j;
            // Return the job reference as an Ijob. 
            IJob job = jobInstance.FirstOrDefault();

            return job;
        }
    }
}
```

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

