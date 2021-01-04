---
title: Erstellen von Videoüberprüfungen mit .NET – Content Moderator
titleSuffix: Azure Cognitive Services
description: Dieser Artikel enthält Informationen und Codebeispiele, die Ihnen den schnellen Einstieg in die Verwendung des Content Moderator SDK mit C# erleichtern, um Videoüberprüfungen zu erstellen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: d9f80de5a18e27de4a9f8e85613e3c2eee6c111c
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853474"
---
# <a name="create-video-reviews-using-net"></a>Erstellen von Videoüberprüfungen per .NET

Dieser Artikel enthält Informationen und Codebeispiele, die Ihnen den schnellen Einstieg in die Verwendung des [Content Moderator SDK mit C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) erleichtern, um Folgendes durchzuführen:

- Erstellen einer Videoüberprüfung für menschliche Moderatoren
- Hinzufügen von Frames zu einer Überprüfung
- Abrufen der Frames für die Überprüfung
- Abrufen des Status und von Details zur Überprüfung
- Veröffentlichen der Überprüfung

## <a name="prerequisites"></a>Voraussetzungen

- Melden Sie sich auf der Website des [Content Moderator-Prüfungstools](https://contentmoderator.cognitive.microsoft.com/) an, oder erstellen Sie dort ein Konto.
- In diesem Artikel wird vorausgesetzt, dass Sie [das Video moderiert haben (siehe Schnellstart)](video-moderation-api.md) und über die Antwortdaten verfügen. Sie benötigen sie zum Erstellen von framebasierten Überprüfungen für menschliche Moderatoren.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Sicherstellen, dass Ihr API-Schlüssel die Überprüfungs-API zur Erstellung der Überprüfung aufrufen kann

Nach Abschluss der vorhergehenden Schritte verfügen Sie möglicherweise über zwei Content Moderator-Schlüssel, wenn Sie über das Azure-Portal begonnen haben.

Wenn Sie den von Azure bereitgestellten API-Schlüssel in Ihrem SDK-Beispiel verwenden möchten, führen Sie die Schritte im Abschnitt [Verwenden des Azure-Schlüssels bei der Überprüfungs-API](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) aus, damit Ihre Anwendung die Überprüfungs API aufrufen und Überprüfungen erstellen kann.

Wenn Sie den vom Überprüfungstool generierten Schlüssel für eine kostenlose Testversion verwenden, ist der Schlüssel dem Überprüfungstoolkonto bereits bekannt. Deshalb sind keine weiteren Schritte erforderlich.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Vorbereiten Ihres Videos und der Videoframes für die Überprüfung

Das zu überprüfende Video und die Beispiel-Videoframes müssen online veröffentlicht werden, da Sie die URLs benötigen.

> [!NOTE]
> Im Programm werden manuell gespeicherte Screenshots aus dem Video mit zufälligen Bewertungen für Inhalte, die nur für Erwachsene bestimmt oder anzüglich sind, verwendet, um die Nutzung der API für die Überprüfung zu veranschaulichen. In der Praxis verwenden Sie die [Ausgabe der Videomoderation](video-moderation-api.md#run-the-program-and-review-the-output), um Bilder zu erstellen und Bewertungen zuzuweisen. 

Für das Video benötigen Sie einen Streamingendpunkt, damit das Prüfungstool das Video in der Playeransicht wiedergibt.

![Miniaturansicht für Demonstrationsvideo](images/ams-video-demo-view.PNG)

- Kopieren Sie die **URL** auf [dieser Azure Media Services-Demoseite](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) für die Manifest-URL.

Verwenden Sie für die Videoframes (Bilder) die folgenden Bilder:

![Videoframe-Miniaturansicht 1](images/ams-video-frame-thumbnails-1.PNG) | ![Videoframe-Miniaturansicht 2](images/ams-video-frame-thumbnails-2.PNG) | ![Videoframe-Miniaturansicht 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[Frame 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [Frame 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [Frame 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Erstellen Ihres Visual Studio-Projekts

1. Fügen Sie Ihrer Projektmappe ein neues Projekt vom Typ **Konsolen-App (.NET Framework)** hinzu.

1. Geben Sie dem Projekt den Namen **VideoReviews**.

1. Wählen Sie dieses Projekt als einzelnes Startprojekt für die Projektmappe aus.

### <a name="install-required-packages"></a>Installieren erforderlicher Pakete

Installieren Sie die folgenden NuGet-Pakete für das TermLists-Projekt.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualisieren der using-Anweisungen des Programms

Ändern Sie die using-Anweisungen des Programms wie folgt:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Hinzufügen von privaten Eigenschaften

Fügen Sie dem Namespace **VideoReviews** (**Program**-Klasse) die folgenden privaten Eigenschaften hinzu. Aktualisieren Sie die Felder `AzureEndpoint` und `CMSubscriptionKey` mit den Werten Ihrer Endpunkt-URL und Ihres Abonnementschlüssels. Diese finden Sie auf der Registerkarte **Schnellstart** Ihrer Ressource im Azure-Portal.


```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Enter a valid endpoint URL
        /// <summary>
        /// The endpoint URL of your subscription
        /// </summary>
        private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

        // NOTE: Enter a valid subscription key.
        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

        // NOTE: Replace this example team name with your Content Moderator team name.
        /// <summary>
        /// The name of the team to assign the job to.
        /// </summary>
        /// <remarks>This must be the team name you used to create your 
        /// Content Moderator account. You can retrieve your team name from
        /// the Content Moderator web site. Your team name is the Id associated 
        /// with your subscription.</remarks>
        private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

        /// <summary>
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Erstellen des Content Moderator-Clientobjekts

Fügen Sie dem Namespace **VideoReviews** (**Program**-Klasse) die folgenden Methodendefinitionen hinzu.

```csharp
/// <summary>
/// Returns a new Content Moderator client for your subscription.
/// </summary>
/// <returns>The new client.</returns>
/// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
/// When you have finished using the client,
/// you should dispose of it either directly or indirectly. </remarks>
public static ContentModeratorClient NewClient()
{
    return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
    {
        Endpoint = AzureEndpoint
    };
}
```

## <a name="create-a-video-review"></a>Erstellen einer Videoüberprüfung

Erstellen Sie mit **ContentModeratorClient.Reviews.CreateVideoReviews** eine Videoüberprüfung. Weitere Informationen finden Sie in der [API-Referenz](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** verfügt über die folgenden erforderlichen Parameter:
1. Eine Zeichenfolge, die einen MIME-Typ enthält. Dieser sollte „application/json“ lauten. 
1. Ihr Content Moderator-Teamname.
1. Ein **IList\<CreateVideoReviewsBodyItem>**-Objekt. Jedes **CreateVideoReviewsBodyItem**-Objekt stellt eine Videoüberprüfung dar. In dieser Schnellstartanleitung wird jeweils nur eine Überprüfung erstellt.

**CreateVideoReviewsBodyItem** verfügt über mehrere Eigenschaften. Sie legen mindestens die folgenden Eigenschaften fest:
- **Content**: Die URL des zu überprüfenden Videos.
- **ContentId**: Eine ID, die der Videoüberprüfung zugewiesen werden soll.
- **Status**: Legen Sie den Wert auf „Unpublished“ (Veröffentlichung aufgehoben) fest. Wenn Sie nichts festlegen, wird standardmäßig „Pending“ (Ausstehend) verwendet. Dies bedeutet, dass die Videoüberprüfung veröffentlicht wird und dass die Prüfung durch den Menschen noch aussteht. Nachdem eine Videoüberprüfung veröffentlicht wurde, können Sie ihr keine Videoframes, kein Transkript und auch kein Transkriptmoderationsergebnis mehr hinzufügen.

> [!NOTE]
> **CreateVideoReviews** gibt „IList\<string>“ zurück. Jede dieser Zeichenfolgen enthält eine ID für eine Videoüberprüfung. Diese IDs sind GUIDs und nicht mit dem Wert der **ContentId**-Eigenschaft identisch. 

Fügen Sie dem VideoReviews-Namespace (Program-Klasse) die folgende Methodendefinition hinzu.

```csharp
/// <summary>
/// Create a video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="id">The ID to assign to the video review.</param>
/// <param name="content">The URL of the video to review.</param>
/// <returns>The ID of the video review.</returns>
private static string CreateReview(ContentModeratorClient client, string id, string content)
{
    Console.WriteLine("Creating a video review.");

    List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
        new CreateVideoReviewsBodyItem
        {
            Content = content,
            ContentId = id,
            /* Note: to create a published review, set the Status to "Pending".
            However, you cannot add video frames or a transcript to a published review. */
            Status = "Unpublished",
        }
    };

    var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

    Thread.Sleep(throttleRate);

    // We created only one review.
    return result[0];
}
```

> [!NOTE]
> Die RPS-Rate (Requests Per Second, Anforderungen pro Sekunde) Ihres Content Moderator-Dienstschlüssels ist begrenzt. Bei Überschreitung des Limits löst das SDK eine Ausnahme mit dem Fehlercode 429 aus.
>
> Bei einem Schlüssel des Free-Tarifs ist die Anforderungsrate auf eine einzelne Anforderung pro Sekunde beschränkt.

## <a name="add-video-frames-to-the-video-review"></a>Hinzufügen von Videoframes zur Videoüberprüfung

Sie fügen einer Videoüberprüfung Videoframes mit **ContentModeratorClient.Reviews.AddVideoFrameUrl** (falls Ihre Videoframes online gehostet werden) oder mit **ContentModeratorClient.Reviews.AddVideoFrameStream** (falls Ihre Videoframes lokal gehostet werden) hinzu. Da in dieser Schnellstartanleitung angenommen wird, dass Ihre Videoframes online gehostet werden, wird **AddVideoFrameUrl** verwendet. Weitere Informationen finden Sie in der [API-Referenz](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** verfügt über die folgenden erforderlichen Parameter:
1. Eine Zeichenfolge, die einen MIME-Typ enthält. Dieser sollte „application/json“ lauten.
1. Ihr Content Moderator-Teamname.
1. Die von **CreateVideoReviews** zurückgegebene ID für die Videoüberprüfung.
1. Ein **IList\<VideoFrameBodyItem>**-Objekt. Jedes **VideoFrameBodyItem**-Objekt steht für einen Videoframe.

**VideoFrameBodyItem** verfügt über die folgenden Eigenschaften:
- **Timestamp**: Eine Zeichenfolge, die den Zeitpunkt des Videos (in Sekunden) enthält, ab dem der Videoframe beginnt.
- **FrameImage**: Die URL des Videoframes.
- **Metadata**. Ein „IList\<VideoFrameBodyItemMetadataItem>“-Element: **VideoFrameBodyItemMetadataItem** ist ein einfaches Schlüssel-Wert-Paar. Gültige Schlüssel sind:
- **reviewRecommended**: Ist „true“, wenn eine menschliche Prüfung des Videoframes empfohlen wird.
- **adultScore**: Ein Wert zwischen 0 und 1, mit dem der Schweregrad des für Erwachsene bestimmten Inhalts im Videoframe angegeben wird.
- **a**: „true“, wenn das Video nicht jugendfreien Inhalt enthält.
- **racyScore**: Ein Wert zwischen 0 und 1, mit dem der Schweregrad von anzüglichem Inhalt im Videoframe angegeben wird.
- **r**: „true“, wenn der Videoframe anzüglichen Inhalt enthält.
- **ReviewerResultTags**: Ein „IList\<VideoFrameBodyItemReviewerResultTagsItem>“-Element: **VideoFrameBodyItemReviewerResultTagsItem** ist ein einfaches Schlüssel-Wert-Paar. Eine Anwendung kann diese Tags zum Organisieren von Videoframes nutzen.

> [!NOTE]
> In dieser Schnellstartanleitung werden Zufallswerte für die Eigenschaften **adultScore** und **racyScore** generiert. In einer Produktionsanwendung erhalten Sie diese Werte über den [Videomoderationsdienst](video-moderation-api.md), der unter Azure Media Services bereitgestellt wird.

Fügen Sie dem VideoReviews-Namespace (Program-Klasse) die folgenden Methodendefinitionen hinzu.

```csharp
<summary>
/// Create a video frame to add to a video review after the video review is created.
/// </summary>
/// <param name="url">The URL of the video frame image.</param>
/// <returns>The video frame.</returns>
private static VideoFrameBodyItem CreateFrameToAddToReview(string url, string timestamp_seconds)
{
    // We generate random "adult" and "racy" scores for the video frame.
    Random rand = new Random();

    var frame = new VideoFrameBodyItem
    {
        // The timestamp is measured in milliseconds. Convert from seconds.
        Timestamp = (int.Parse(timestamp_seconds) * 1000).ToString(),
        FrameImage = url,

        Metadata = new List<VideoFrameBodyItemMetadataItem>
        {
            new VideoFrameBodyItemMetadataItem("reviewRecommended", "true"),
            new VideoFrameBodyItemMetadataItem("adultScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("a", "false"),
            new VideoFrameBodyItemMetadataItem("racyScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("r", "false")
        },

        ReviewerResultTags = new List<VideoFrameBodyItemReviewerResultTagsItem>()
        {
            new VideoFrameBodyItemReviewerResultTagsItem("tag1", "value1")
        }
    };

    return frame;
}
```

```csharp
/// <summary>
/// Add a video frame to the indicated video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="url">The URL of the video frame image.</param>
static void AddFrame(ContentModeratorClient client, string review_id, string url, string timestamp_seconds)
{
    Console.WriteLine("Adding a frame to the review with ID {0}.", review_id);

    var frames = new List<VideoFrameBodyItem>()
    {
        CreateFrameToAddToReview(url, timestamp_seconds)
    };
        
    client.Reviews.AddVideoFrameUrl("application/json", TeamName, review_id, frames);

    Thread.Sleep(throttleRate);
```

## <a name="get-video-frames-for-video-review"></a>Abrufen von Videoframes für die Videoüberprüfung

Sie erhalten die Videoframes mit **ContentModeratorClient.Reviews.GetVideoFrames**. **GetVideoFrames** verfügt über die folgenden erforderlichen Parameter:
1. Ihr Content Moderator-Teamname.
1. Die von **CreateVideoReviews** zurückgegebene ID für die Videoüberprüfung.
1. Der nullbasierte Index des ersten abzurufenden Videoframes.
1. Die Anzahl von abzurufenden Videoframes.

Fügen Sie dem VideoReviews-Namespace (Program-Klasse) die folgende Methodendefinition hinzu.

```csharp
/// <summary>
/// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
static void GetFrames(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

    Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="get-video-review-information"></a>Abrufen von Informationen zur Videoüberprüfung

Sie können Informationen zu einer Videoüberprüfung mit **ContentModeratorClient.Reviews.GetReview** erhalten. **GetReview** verfügt über die folgenden erforderlichen Parameter:
1. Ihr Content Moderator-Teamname.
1. Die von **CreateVideoReviews** zurückgegebene ID für die Videoüberprüfung.

Fügen Sie dem VideoReviews-Namespace (Program-Klasse) die folgende Methodendefinition hinzu.

```csharp
/// <summary>
/// Get the information for the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void GetReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting the status for the review with ID {0}.", review_id);

    var result = client.Reviews.GetReview(ModeratorHelper.Clients.TeamName, review_id);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="publish-video-review"></a>Veröffentlichen der Videoüberprüfung

Sie veröffentlichen eine Videoüberprüfung mit **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** verfügt über die folgenden erforderlichen Parameter:
1. Ihr Content Moderator-Teamname.
1. Die von **CreateVideoReviews** zurückgegebene ID für die Videoüberprüfung.

Fügen Sie dem VideoReviews-Namespace (Program-Klasse) die folgende Methodendefinition hinzu.

```csharp
/// <summary>
/// Publish the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void PublishReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Publishing the review with ID {0}.", review_id);
    client.Reviews.PublishVideoReview(TeamName, review_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="putting-it-all-together"></a>Zusammenfügen des Gesamtbilds

Fügen Sie dem VideoReviews-Namespace (Program-Klasse) die **Main**-Methodendefinition hinzu. Schließen Sie zuletzt die Program-Klasse und den VideoReviews-Namespace.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var frame1_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG";
        var frame2_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG";
        var frame3_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG";

        // Add the frames from 17, 64, and 144 seconds.
        AddFrame(client, review_id, frame1_url, "17");
        AddFrame(client, review_id, frame2_url, "64");
        AddFrame(client, review_id, frame3_url, "144");

        // Get frames information and show
        GetFrames(client, review_id);
        GetReview(client, review_id);

        // Publish the review
        PublishReview(client, review_id);

        Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
        Console.WriteLine("Press any key to close the application.");
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>Ausführen des Programms und Überprüfen der Ausgabe
Wenn Sie die Anwendung ausführen, wird eine Ausgabe in den folgenden Zeilen angezeigt:

```json
Creating a video review.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Getting frames for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "VideoFrames": [
    {
        "Timestamp": "17000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_17000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.808312381528463"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.846378884206702"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "64000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_64000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.576078300166912"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.244768953064815"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "144000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_144000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.664480847150311"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.933817870418456"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
        ]
    }
    ]
}

Getting the status for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "SubTeam": "public",
    "Status": "UnPublished",
    "ReviewerResultTags": [],
    "CreatedBy": "testreview6",
    "Metadata": [
    {
        "Key": "FrameCount",
        "Value": "3"
    }
    ],
    "Type": "Video",
    "Content": "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    "ContentId": "review1",
    "CallbackEndpoint": null
}

Publishing the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="check-out-your-video-review"></a>Anzeigen der Videoüberprüfung

Abschließend zeigen Sie die Videoüberprüfung in Ihrem Konto für das Content Moderator-Prüfungstool unter **Überprüfung**>**Video** an.

![Videoüberprüfung für menschliche Moderatoren](images/ams-video-review.PNG)

## <a name="next-steps"></a>Nächste Schritte

Rufen Sie das [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) und die [Visual Studio-Projektmappe](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dafür sowie andere Content Moderator-Schnellstarts für .NET ab.
