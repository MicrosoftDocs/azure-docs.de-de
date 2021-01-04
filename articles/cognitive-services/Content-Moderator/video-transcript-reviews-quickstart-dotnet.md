---
title: Erstellen von Videotranskriptüberprüfungen mit .NET – Content Moderator
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Videotranskriptüberprüfungen mit dem Azure Cognitive Services Content Moderator SDK für .NET erstellen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 326fc2cc162a2ab54b40888250fbeef55ad8800a
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853457"
---
# <a name="create-video-transcript-reviews-using-net"></a>Erstellen von Videotranskriptüberprüfungen per .NET

Dieser Artikel enthält Informationen und Codebeispiele, die Ihnen den schnellen Einstieg in die Verwendung des [Content Moderator SDK mit C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) erleichtern, um Folgendes durchzuführen:

- Erstellen einer Videoüberprüfung für menschliche Moderatoren
- Hinzufügen eines moderierten Transkripts zur Überprüfung
- Veröffentlichen der Überprüfung

## <a name="prerequisites"></a>Voraussetzungen

- Melden Sie sich auf der Website des [Content Moderator-Prüfungstools](https://contentmoderator.cognitive.microsoft.com/) an, oder erstellen Sie dort ein Konto, wenn dieser Schritt nicht bereits erfolgt ist.
- In diesem Artikel wird vorausgesetzt, dass Sie [das Video moderiert](video-moderation-api.md) und [die Videoüberprüfung erstellt](video-reviews-quickstart-dotnet.md) haben, indem Sie das Prüfungstool für die Entscheidungsfindung durch Menschen verwendet haben. Nun möchten Sie im Prüfungstool moderierte Videotranskripts hinzufügen.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Sicherstellen, dass Ihr API-Schlüssel die Überprüfungs-API aufrufen kann (Auftragserstellung)

Nach Abschluss der vorhergehenden Schritte verfügen Sie möglicherweise über zwei Content Moderator-Schlüssel, wenn Sie über das Azure-Portal begonnen haben.

Wenn Sie den von Azure bereitgestellten API-Schlüssel in Ihrem SDK-Beispiel verwenden möchten, führen Sie die Schritte im Abschnitt [Verwenden des Azure-Schlüssels bei der Überprüfungs-API](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) aus, damit Ihre Anwendung die Überprüfungs API aufrufen und Überprüfungen erstellen kann.

Wenn Sie den vom Überprüfungstool generierten Schlüssel für eine kostenlose Testversion verwenden, ist der Schlüssel dem Überprüfungstoolkonto bereits bekannt. Deshalb sind keine weiteren Schritte erforderlich.

## <a name="prepare-your-video-for-review"></a>Vorbereiten Ihres Videos auf die Überprüfung

Fügen Sie das Transkript einer Videoüberprüfung hinzu. Das Video muss online veröffentlicht werden. Sie benötigen seinen Streamingendpunkt. Mit dem Streamingendpunkt kann der Videoplayer des Prüfungstools das Video wiedergeben.

![Miniaturansicht für Demonstrationsvideo](images/ams-video-demo-view.PNG)

- Kopieren Sie die **URL** auf [dieser Azure Media Services-Demoseite](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) für die Manifest-URL.

## <a name="create-your-visual-studio-project"></a>Erstellen Ihres Visual Studio-Projekts

1. Fügen Sie Ihrer Projektmappe ein neues Projekt vom Typ **Konsolen-App (.NET Framework)** hinzu.

1. Geben Sie dem Projekt den Namen **VideoTranscriptReviews**.

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

Fügen Sie die folgenden privaten Eigenschaften dem **VideoTranscriptReviews**-Namespace (**Program**-Klasse) hinzu. Aktualisieren Sie die Felder `AzureEndpoint` und `CMSubscriptionKey` mit den Werten Ihrer Endpunkt-URL und Ihres Abonnementschlüssels. Diese finden Sie auf der Registerkarte **Schnellstart** Ihrer Ressource im Azure-Portal.

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

Fügen Sie die folgende Methodendefinition dem VideoTranscriptReviews-Namespace (Program-Klasse) hinzu.

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
> Für Ihren Content Moderator-Dienstschlüssel gilt ein Ratenlimit vom Typ „Anforderungen pro Sekunde“ (Requests Per Second, RPS). Wenn Sie das Limit überschreiten, löst das SDK eine Ausnahme mit dem Fehlercode 429 aus.
>
> Bei einem Schlüssel des Free-Tarifs ist die Anforderungsrate auf eine einzelne Anforderung pro Sekunde beschränkt.

## <a name="add-transcript-to-video-review"></a>Hinzufügen eines Transkripts zur Videoüberprüfung

Sie fügen ein Transkript einer Videoüberprüfung mit **ContentModeratorClient.Reviews.AddVideoTranscript** hinzu. **AddVideoTranscript** verfügt über die folgenden erforderlichen Parameter:
1. Ihre Content Moderator-Team-ID
1. Die von **CreateVideoReviews** zurückgegebene ID für die Videoüberprüfung.
1. Ein **Stream**-Objekt mit dem Transkript

Das Transkript muss im WebVTT-Format vorliegen. Weitere Informationen finden Sie unter [WebVTT: The Web Video Text Tracks Format](https://www.w3.org/TR/webvtt1/) (WebVTT: Webvideo-Texttitelformat).

> [!NOTE]
> Im Programm wird ein Beispieltranskript im VTT-Format verwendet. In einer realen Lösung verwenden Sie den Azure Media Indexer-Dienst, um aus einem Video [ein Transkript zu generieren](../../media-services/previous/media-services-index-content.md).

Fügen Sie die folgende Methodendefinition dem VideoTranscriptReviews-Namespace (Program-Klasse) hinzu.

```csharp
/// <summary>
/// Add a transcript to the indicated video review.
/// The transcript must be in the WebVTT format.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
    client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Hinzufügen eines Transkriptmoderationsergebnisses zur Videoüberprüfung

Zusätzlich zum Hinzufügen eines Transkripts zu einer Videoüberprüfung fügen Sie auch das Ergebnis der Moderation dieses Transkripts hinzu. Hierfür verwenden Sie **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. Weitere Informationen finden Sie in der [API-Referenz](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** verfügt über die folgenden erforderlichen Parameter:
1. Eine Zeichenfolge, die einen MIME-Typ enthält. Dieser sollte „application/json“ lauten. 
1. Ihr Content Moderator-Teamname.
1. Die von **CreateVideoReviews** zurückgegebene ID für die Videoüberprüfung.
1. Ein „IList\<TranscriptModerationBodyItem>“-Element: Ein **TranscriptModerationBodyItem**-Element verfügt über die folgenden Eigenschaften:
1. **Terms**: Ein „IList\<TranscriptModerationBodyItemTermsItem>“-Element: Ein **TranscriptModerationBodyItemTermsItem**-Element verfügt über die folgenden Eigenschaften:
1. **Index**: Der nullbasierte Index des Begriffs
1. **Term**: Eine Zeichenfolge, die den Begriff enthält
1. **Timestamp**: Eine Zeichenfolge mit der Zeit (in Sekunden) im Transkript, nach der die Begriffe gefunden werden.

Das Transkript muss im WebVTT-Format vorliegen. Weitere Informationen finden Sie unter [WebVTT: The Web Video Text Tracks Format](https://www.w3.org/TR/webvtt1/) (WebVTT: Webvideo-Texttitelformat).

Fügen Sie die folgende Methodendefinition dem VideoTranscriptReviews-Namespace (Program-Klasse) hinzu. Mit dieser Methode wird ein Transkript an die **ContentModeratorClient.TextModeration.ScreenText**-Methode übermittelt. Außerdem wird das Ergebnis in ein „IList\<TranscriptModerationBodyItem>“-Element übersetzt und an **AddVideoTranscriptModerationResult** übermittelt.

```csharp
/// <summary>
/// Add the results of moderating a video transcript to the indicated video review.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

    // Screen the transcript using the Text Moderation API. For more information, see:
    // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
    Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

    // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
    List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
    if (null != screen.Terms)
    {
        foreach (var term in screen.Terms)
        {
            if (term.Index.HasValue)
            {
                terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
            }
        }
    }

    List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
    {
        new TranscriptModerationBodyItem()
        {
            Timestamp = "0",
            Terms = terms
        }
    };

    client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

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

Fügen Sie die **Main**-Methodendefinition dem VideoTranscriptReviews-Namespace (Program-Klasse) hinzu. Schließen Sie zuletzt die Program-Klasse und den VideoTranscriptReviews-Namespace.

> [!NOTE]
> Im Programm wird ein Beispieltranskript im VTT-Format verwendet. In einer realen Lösung verwenden Sie den Azure Media Indexer-Dienst, um aus einem Video [ein Transkript zu generieren](../../media-services/previous/media-services-index-content.md).

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var transcript = @"WEBVTT

        01:01.000 --> 02:02.000
        First line with a negative word in a transcript.

        02:03.000 --> 02:25.000
        This is another line in the transcript.
        ";

        AddTranscript(client, review_id, transcript);

        AddTranscriptModerationResult(client, review_id, transcript);

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

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>Navigieren zu Ihrer Videotranskriptüberprüfung

Navigieren Sie im Content Moderator-Prüfungstool unter **Überprüfung**>**Video**>**Transkript** zur Videotranskriptüberprüfung.

Die folgenden Features werden angezeigt:
- Die zwei Transkriptzeilen, die Sie hinzugefügt haben
- Der obszöne Begriff, der vom Dienst für die Textmoderation gefunden und hervorgehoben wurde
- Bei Auswahl eines Transkriptionstexts wird das Video ab diesem Zeitstempel gestartet

![Videotranskriptüberprüfung für menschliche Moderatoren](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Nächste Schritte

Rufen Sie das [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) und die [Visual Studio-Projektmappe](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dafür sowie andere Content Moderator-Schnellstarts für .NET ab.

Informieren Sie sich darüber, wie Sie im Prüfungstool [Videoüberprüfungen](video-reviews-quickstart-dotnet.md) generieren.
