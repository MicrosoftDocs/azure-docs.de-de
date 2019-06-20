---
title: Hochladen und Indizieren von Videos mit Video Indexer
titlesuffix: Azure Media Services
description: In diesem Thema wird veranschaulicht, wie Sie APIs zum Hochladen und Indizieren Ihrer Videos mit Video Indexer verwenden.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: e92086ca18887b9b2c2362e97d855c33834b83bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799205"
---
# <a name="upload-and-index-your-videos"></a>Hochladen und Indizieren Ihrer Videos  

Beim Hochladen von Videos mit der Video Indexer-API haben Sie die folgenden Optionen: 

* Hochladen des Videos von einer URL (bevorzugt)
* Senden der Videodatei als Bytearray im Hauptteil der Anforderung
* Verwenden Sie ein vorhandenes Azure Media Services-Medienobjekt, indem Sie die [Medienobjekt-ID](https://docs.microsoft.com/azure/media-services/latest/assets-concept) angeben (wird nur in kostenpflichtigen Konten unterstützt).

In diesem Artikel wird veranschaulicht, wie Sie die API zum [Hochladen eines Videos ](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) verwenden, um Ihre Videos über eine URL hochzuladen und zu indizieren. Das Codebeispiel in diesem Artikel schließt den auskommentierten Code ein, der zeigt, wie Sie das Bytearray hochladen. <br/>Außerdem werden in diesem Artikel einige Parameter beschrieben, die Sie für die API festlegen können, um den Prozess und die Ausgabe der API zu ändern.

Nachdem Ihr Video hochgeladen wurde, kann das Video von Video Indexer optional codiert werden (wie im Artikel beschrieben). Beim Erstellen eines Video Indexer-Kontos können Sie ein kostenloses Testkonto (mit einer bestimmten Anzahl von kostenlosen Indizierungsminuten) oder eine kostenpflichtige Option wählen (ohne Einschränkung durch eine Kontingentvorgabe). Bei der kostenlosen Testversion stellt Video Indexer bis zu 600 Minuten an kostenloser Indizierungszeit für Websitebenutzer und bis zu 2.400 Minuten an kostenloser Indizierungszeit für API-Benutzer bereit. Bei der kostenpflichtigen Option erstellen Sie ein Video Indexer-Konto, das [mit Ihrem Azure-Abonnement und einem Azure Media Services-Konto verbunden](connect-to-azure.md) ist. Sie bezahlen für die Minuten der Indizierungszeit und die Gebühren für das Media Services-Konto. 

## <a name="uploading-considerations"></a>Überlegungen zum Hochladen

- Wenn Sie das Video über eine URL hochladen (bevorzugt), muss der Endpunkt mit TLS 1.2 (oder höher) gesichert werden.
- Die Uploadgröße mit der URL-Option ist auf 30 GB begrenzt.
- Die Länge der Anforderungs-URL ist auf 2.048 Zeichen begrenzt.
- Die Uploadgröße mit der Bytearray-Option ist auf 2 GB begrenzt.
- Die Bytearray-Option umfasst ein Timeout nach 30 Minuten.
- Die im Parameter `videoURL` angegebene URL muss codiert sein.
- Bei der Indizierung von Media Services-Medienobjekten gibt es die gleiche Begrenzung wie bei der Indizierung aus einer URL.
- Video Indexer weist eine maximale Dauer von 4 Stunden für eine einzelne Datei auf

> [!Tip]
> Es wird empfohlen, .NET Framework-Version 4.6.2 oder höher zu verwenden, da für frühere .NET Frameworks nicht standardmäßig TLS 1.2 genutzt wird.
>
> Falls Sie frühere .NET Frameworks verwenden müssen, sollten Sie Ihrem Code eine Zeile hinzufügen, bevor Sie den REST-API-Aufruf durchführen:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="configurations-and-params"></a>Konfigurationen und Parameter

In diesem Abschnitt werden einige optionale Parameter und ihre Anwendung beschrieben.

### <a name="externalid"></a>externalID 

Dieser Parameter ermöglicht Ihnen das Angeben einer ID, die dem Video zugeordnet wird. Die ID kann auf die externe Systemintegration für die Verwaltung der Videoinhalte (Video Content Management, VCM) angewendet werden. Die Videos, die im Video Indexer-Portal enthalten sind, können anhand der angegebenen externen ID durchsucht werden.

### <a name="callbackurl"></a>callbackUrl

Eine URL, die zum Benachrichtigen des Kunden über die folgenden Ereignisse (mithilfe einer POST-Anforderung) verwendet wird:

- Änderung des Indizierungszustands: 
    - Eigenschaften:    
    
        |NAME|BESCHREIBUNG|
        |---|---|
        |id|Video-ID|
        |state|Videozustand|  
    - Beispiel: https://test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- Im Video identifizierte Person:
  - Eigenschaften
    
      |NAME|BESCHREIBUNG|
      |---|---|
      |id| Video-ID|
      |faceId|Die Gesichts-ID, die im Videoindex angezeigt wird|
      |knownPersonId|Die Personen-ID, die innerhalb eines Gesichtsmodells eindeutig ist|
      |personName|Der Name der Person|
        
    - Beispiel: https://test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

#### <a name="notes"></a>Notizen

- Video Indexer gibt vorhandene Parameter aus der ursprünglichen URL zurück.
- Die angegebene URL muss codiert werden.

### <a name="indexingpreset"></a>indexingPreset

Verwenden Sie diesen Parameter, wenn unformatierte oder externe Aufzeichnungen Hintergrundgeräusche enthalten. Dieser Parameter wird verwendet, um den Indizierungsprozess zu konfigurieren. Sie können die folgenden Werte angeben:

- `Default`: Indizieren und Extrahieren von Erkenntnissen für Audio- und Videodaten
- `AudioOnly`: Indizieren und Extrahieren von Erkenntnissen ausschließlich für Audiodaten (Videodaten werden ignoriert)
- `DefaultWithNoiseReduction`: Indizieren und Extrahieren von Erkenntnissen aus Audio- und Videodaten mit Anwendung von Algorithmen für die Rauschunterdrückung auf den Audiodatenstrom

Der Preis richtet sich nach der gewählten Indizierungsoption.  

### <a name="priority"></a>priority

Videos werden von Video Indexer gemäß ihrer Priorität indiziert. Geben Sie mithilfe des Parameters **priority** die Indexpriorität an. Die folgenden Werte sind gültig: **Niedrig**, **Normal** (Standard) und **Hoch**.

Der Parameter **priority** wird nur in kostenpflichtigen Konten unterstützt.

### <a name="streamingpreset"></a>streamingPreset

Nachdem Ihr Video hochgeladen wurde, kann das Video von Video Indexer optional codiert werden. Anschließend wird der Vorgang mit dem Indizieren und Analysieren des Videos fortgesetzt. Nachdem Video Indexer die Analyse abgeschlossen hat, erhalten Sie eine Benachrichtigung mit der Video-ID.  

Bei Verwendung der [Upload video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)- oder [Re-Index Video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)-API lautet einer der optionalen Parameter `streamingPreset`. Wenn Sie `streamingPreset` auf `Default`, `SingleBitrate` oder `AdaptiveBitrate` festlegen, wird der Codierungsprozess ausgelöst. Wenn die Indizierung und Codierung von Aufträgen abgeschlossen ist, wird das Video veröffentlicht, damit Sie Ihr Video auch streamen können. Der Streamingendpunkt, von dem aus Sie das Video streamen möchten, muss sich im Status **Wird ausgeführt** befinden.

Zum Ausführen der Indizierung und Codierung von Aufträgen sind für das [Azure Media Services-Konto, das mit Ihrem Video Indexer-Konto verbunden ist](connect-to-azure.md), reservierte Einheiten (Reserved Units, RUs) erforderlich. Weitere Informationen finden Sie unter [Übersicht über das Skalieren der Medienverarbeitung](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview). Da es sich hierbei um rechenintensive Aufträge handelt, wird dringend die Verwendung des Einheitentyps S3 empfohlen. Die Anzahl von RUs definiert die maximale Anzahl von Aufträgen, die parallel ausgeführt werden können. Die Baseline-Empfehlung lautet: zehn RUs vom Typ S3. 

Wenn Sie Ihr Video nicht codieren, sondern nur indizieren möchten, können Sie `streamingPreset`auf `NoStreaming` festlegen.

### <a name="videourl"></a>videoUrl

Eine URL der zu indizierenden Video-/Audiodatei. Die URL muss auf eine Mediendatei zeigen (HTML-Seiten werden nicht unterstützt). Die Datei kann durch ein Zugriffstoken als Teil des URI geschützt werden, und der Endpunkt für die Datei muss mit TLS 1.2 oder höher gesichert werden. Die URL muss codiert sein. 

Wenn `videoUrl` nicht angegeben ist, erwartet Video Indexer, dass Sie die Datei als „multipart/form“ im Hauptteil übergeben.

## <a name="code-sample"></a>Codebeispiel

Mit dem folgenden C#-Codeausschnitt wird die Nutzung aller Video Indexer-APIs zusammen veranschaulicht.

```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var location = "westus2";
    var apiKey = "...";

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    // take the relevant account, here we simply take the first
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Debug.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =newbyte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(newByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Debug.WriteLine("Uploaded");
    Debug.WriteLine("Video ID:");
    Debug.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Debug.WriteLine("");
        Debug.WriteLine("State:");
        Debug.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Debug.WriteLine("");
            Debug.WriteLine("Full JSON:");
            Debug.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Debug.WriteLine("");
    Debug.WriteLine("Search:");
    Debug.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Debug.WriteLine("Insights Widget url:");
    Debug.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
    Debug.WriteLine("");
    Debug.WriteLine("Player Widget url:");
    Debug.WriteLine(playerWidgetLink);
}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```
## <a name="common-errors"></a>Häufige Fehler

Die in der folgenden Tabelle aufgeführten Statuscodes können über den Uploadvorgang zurückgegeben werden.

|Statuscode|ErrorType (im Antworttext)|BESCHREIBUNG|
|---|---|---|
|400|VIDEO_ALREADY_IN_PROGRESS|Dasselbe Video wird unter dem angegebenen Konto bereits verarbeitet.|
|400|VIDEO_ALREADY_FAILED|Dasselbe Video konnte unter dem angegebenen Konto vor weniger als zwei Stunden nicht verarbeitet werden. API-Clients sollten mindestens zwei Stunden warten, bevor ein Video erneut hochgeladen wird.|

## <a name="next-steps"></a>Nächste Schritte

[Untersuchen der von der API erstellten Azure Video Indexer-Ausgabe](video-indexer-output-json-v2.md)
