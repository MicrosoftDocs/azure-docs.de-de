---
title: Verwenden von Azure Media Content Moderator, um nicht jugendfreie und rassistische Inhalte zu erkennen | Microsoft Docs
description: Videomoderation hilft Ihnen, nicht jugendfreie und rassistische Inhalte in Videos zu erkennen.
services: media-services
documentationcenter: ''
author: sanjeev3
manager: mikemcca
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: eb16f5e1e72e5a9379ad530ab9677adba2ccbbcd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61465676"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Verwenden von Azure Media Content Moderator, um nicht jugendfreie und rassistische Inhalte zu erkennen 

## <a name="overview"></a>Übersicht
Mit dem Medienprozessor (MP) von **Azure Media Content Moderator** können Sie die hardwareunterstützte Moderation für Ihre Videos verwenden. Sie möchten beispielsweise nicht jugendfreie und rassistische Inhalte in Videos erkennen und die gekennzeichneten Inhalte von Ihren menschlichen Moderationsteams überprüfen lassen.

Der Medienprozessor von **Azure Media Content Moderator** befindet sich derzeit in der Vorschauphase.

Dieser Artikel enthält Details zu **Azure Media Content Moderator** und zeigt die Verwendung mit dem Media Services SDK für .NET.

## <a name="content-moderator-input-files"></a>Content Moderator-Eingabedateien
Videodateien. Die folgenden Formate werden derzeit unterstützt: MP4, MOV und WMV.

## <a name="content-moderator-output-files"></a>Content Moderator-Ausgabedateien
Die moderierte Ausgabe im JSON-Format umfasst automatisch erkannte Aufnahmen und Keyframes. Die Keyframes werden mit Zuverlässigkeitsbewertung für nicht jugendfreie oder rassistische Inhalte zurückgegeben. Sie umfassen auch ein boolesches Flag, das angibt, ob eine Überprüfung ratsam ist. Die Kennzeichnung für die Überprüfungsempfehlung wird basierend auf den internen Schwellenwerten für nicht jugendfreie und rassistische Bewertungen zugewiesen.

## <a name="elements-of-the-output-json-file"></a>Elemente der JSON-Ausgabedatei

Der Auftrag erzeugt eine JSON-Ausgabedatei, die Metadaten zu erkannten Aufnahmen und Keyframes enthält und angibt, ob sie nicht jugendfreie oder rassistische Inhalte enthalten.

Die JSON-Ausgabe enthält die folgenden Elemente:

### <a name="root-json-elements"></a>JSON-Stammelemente

| Element | BESCHREIBUNG |
| --- | --- |
| version |Die Version von Content Moderator. |
| timescale |„Ticks“ pro Sekunde des Videos. |
| offset |Der Zeitoffset für Zeitstempel. In Version 1.0 von Video-APIs lautet dieser Wert immer 0. Dieser Wert kann sich in der Zukunft ändern. |
| framerate |Frames des Videos pro Sekunde. |
| width |Die Breite des Ausgabevideobilds in Pixeln.|
| height |Die Höhe des Ausgabevideobilds in Pixeln.|
| totalDuration |Die Dauer des Eingabevideos in „Ticks“. |
| [fragments](#fragments-json-elements) |Die Metadaten werden in verschiedene, als Fragmente bezeichnete Segmente aufgeteilt. Jedes Fragment ist eine automatisch erkannte Aufnahme mit Start, Dauer, Intervallnummer und Ereignis(sen). |

### <a name="fragments-json-elements"></a>JSON-Elemente für Fragmente

|Element|BESCHREIBUNG|
|---|---|
| start |Die Startzeit des ersten Ereignisses in „Ticks“. |
| duration |Die Länge des Fragments in „Ticks“. |
| interval |Das Intervall jedes Ereigniseintrags innerhalb des Fragments in „Ticks“. |
| [events](#events-json-elements) |Jedes Ereignis stellt einen Clip dar, und jeder Clip enthält Keyframes, die innerhalb dieser Zeitspanne entdeckt und nachverfolgt werden. Es ist ein Array von Ereignissen. Das äußere Array stellt ein Zeitintervall dar. Das innere Array besteht aus 0 oder mehr Ereignissen, die zu diesem Zeitpunkt aufgetreten sind.|

### <a name="events-json-elements"></a>JSON-Elemente für Fragmente

|Element|BESCHREIBUNG|
|---|---|
| reviewRecommended | `true` oder `false` je nachdem, ob **AdultScore** oder **RacyScore** die internen Schwellenwerte überschreiten. |
| adultScore | Die Zuverlässigkeitsbewertung für nicht jugendfreie Inhalte auf einer Skala von 0,00 bis 0,99. |
| racyScore | Die Zuverlässigkeitsbewertung für rassistische Inhalte auf einer Skala von 0,00 bis 0,99. |
| index | Der Index des Frames auf einer Skala vom ersten bis zum letzten Frameindex. |
| timestamp | Der Speicherort des Frames in „Ticks“. |
| shotIndex | Der Index der übergeordneten Aufnahme. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Schnellstart und Beispielausgaben für Content Moderation

### <a name="task-configuration-preset"></a>Taskkonfiguration (Voreinstellung)
Wenn Sie eine Aufgabe mit **Azure Media Content Moderator**erstellen, müssen Sie eine Konfigurationsvoreinstellung angeben. Die folgende Konfigurationsvoreinstellung ist nur zur Inhaltsmoderation bestimmt.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>Codebeispiel für .NET

Im folgenden Codebeispiel für .NET wird das Media Services .NET SDK zum Ausführen eines Content Moderator-Auftrags verwendet. Ein Media Services Asset wird als Eingabe verwendet, die das zu moderierende Video enthält.
Unter [Content Moderator video quickstart](../../cognitive-services/Content-Moderator/video-moderation-api.md) (Videoschnellstart zu Content Moderator) finden Sie den vollständigen Quellcode und das Visual Studio-Projekt.


```csharp
    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
                    Thread.Sleep(10000);
             }
             while (jobQuery.State != JobState.Finished &&
             jobQuery.State != JobState.Error &&
             jobQuery.State != JobState.Canceled);
        });
        progressPrintTask.Start();

        Task progressJobTask = job.GetExecutionProgressTask(
        CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            ErrorDetail error = job.Tasks.First().ErrorDetails.First();
            Console.WriteLine(string.Format("Error: {0}. {1}",
            error.Code,
            error.Message));
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

For the full source code and the Visual Studio project, check out the [Content Moderator video quickstart](../../cognitive-services/Content-Moderator/video-moderation-api.md).

### JSON output

The following example of a Content Moderator JSON output was truncated.

> [!NOTE]
> Location of a keyframe in seconds = timestamp/timescale

    {
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]
    }
    ]
    }
```

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Verwandte Links
[Azure Media Services Analytics – Übersicht](media-services-analytics-overview.md)

[Azure Media Analytics-Demos](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die [Lösung zur Videomoderation und -überprüfung](../../cognitive-services/Content-Moderator/video-moderation-human-review.md) von Content Moderator.

Den vollständigen Quellcode und das Visual Studio-Projekt erhalten Sie unter [Content Moderator video quickstart](../../cognitive-services/Content-Moderator/video-moderation-api.md) (Videoschnellstart zu Content Moderator). 

Erfahren Sie mehr über das Generieren von [Videoüberprüfungen](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) aus der moderierten Ausgabe und das [Moderieren von Transkriptionen](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) in .NET.

Sehen Sie das detaillierte [Tutorial zur Videomoderation und -überprüfung](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md) in .NET an. 
