---
title: Verwenden der Video Indexer-API
titleSuffix: Azure Media Services
description: In diesem Artikel werden die ersten Schritte für die Verwendung der Video Indexer-API von Azure Media Services beschrieben.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/10/2020
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: cdba4ce36322f9c3fb0f898cb7eb1d1185ed1dc6
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636944"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Tutorial: Verwenden der Video Indexer-API

In Video Indexer ist unterschiedliche KI-Technologie (Künstliche Intelligenz) für Audio- und Videodaten zusammengefasst, und der Dienst wird von Microsoft als integrierter Dienst angeboten, um die Entwicklung zu vereinfachen. Die APIs sind so konzipiert, dass sich Entwickler auf die Nutzung der KI-Technologie für Medien konzentrieren können, ohne sich um Dinge wie die Skalierung, globale Reichweite, Verfügbarkeit und Zuverlässigkeit von Cloudplattformen kümmern zu müssen. Sie können die API verwenden, um Ihre Dateien hochzuladen, ausführliche Videoinformationen zu erhalten, URLs von einbettbaren Erkenntnisse- und Player-Widgets abzurufen, usw.

Beim Erstellen eines Video Indexer-Kontos können Sie ein kostenloses Testkonto (mit einer bestimmten Anzahl von kostenlosen Indizierungsminuten) oder eine kostenpflichtige Option wählen (ohne Einschränkung durch eine Kontingentvorgabe). Bei einer kostenlosen Testversion stellt Video Indexer bis zu 600 Minuten an kostenloser Indizierungszeit für Websitebenutzer und bis zu 2400 Minuten an kostenloser Indizierungszeit für API-Benutzer bereit. Bei einer kostenpflichtigen Option erstellen Sie ein Video Indexer-Konto, [das mit Ihrem Azure-Abonnement und einem Azure Media Services-Konto verbunden ist](connect-to-azure.md). Sie bezahlen pro Minute Indizierungszeit. Weitere Informationen finden Sie unter [Media Services – Preise](https://azure.microsoft.com/pricing/details/media-services/).

In diesem Artikel wird veranschaulicht, wie Entwickler die [Video Indexer-API](https://api-portal.videoindexer.ai/) nutzen können.

## <a name="subscribe-to-the-api"></a>Abonnieren der API

1. Melden Sie sich beim [Portal für Video Indexer-Entwickler an](https://api-portal.videoindexer.ai/).
    
    ![Beim Video Indexer-Entwicklerportal anmelden](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * Sie müssen denselben Anbieter verwenden, den Sie beim Anmelden für Video Indexer genutzt haben.
   > * Persönliche Google- und Microsoft-Konten (Outlook/Live) können nur für Testkonten verwendet werden. Für Konten, die über eine Verbindung mit Azure verfügen, ist Azure AD erforderlich.
   > * Pro E-Mail-Adresse kann nur ein aktives Konto vorhanden sein. Wenn ein Benutzer versucht, sich mit user@gmail.com für LinkedIn anzumelden, und später user@gmail.com auch für Google verwendet, wird für die zweite Anmeldung eine Fehlerseite mit dem Hinweis angezeigt, dass der Benutzer bereits vorhanden ist.
2. Führen Sie den Schritt zum Abonnieren aus.

    Wählen Sie die Registerkarte [Produkte](https://api-portal.videoindexer.ai/products). Wählen Sie anschließend „Authorization“ (Autorisierung), und führen Sie den Vorgang für die Einrichtung des Abonnements durch.
    
    ![Registerkarte „Products“ im Video Indexer-Entwicklerportal](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Für neue Benutzer wird das Abonnement der Autorisierung automatisch eingerichtet.
    
    Nach Abschluss des Abonnementvorgangs finden Sie Ihr Abonnement unter **Produkte** -> **Autorisierung**. Auf der Abonnementseite wird der primäre und sekundäre Schlüssel angezeigt. Die Schlüssel sollten geschützt werden. Die Schlüssel sollten nur von Ihrem Servercode verwendet werden. Sie sollten nicht auf Clientseite (.js, .html usw.) verfügbar sein.

    ![Abonnement und Schlüssel im Video Indexer-Entwicklerportal](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> Video Indexer-Benutzer können einen einzelnen Abonnementschlüssel verwenden, um eine Verbindung mit mehreren Video Indexer-Konten herzustellen. Sie können diese Video Indexer-Konten dann mit unterschiedlichen Media Services-Konten verknüpfen.

## <a name="obtain-access-token-using-the-authorization-api"></a>Abrufen von Zugriffstoken mit der Authorization-API

Sobald Sie die Authorization-API abonniert haben, können Sie Zugriffstoken abrufen. Diese Zugriffstoken werden zur Authentifizierung für die Operations-API verwendet.

Jeder Aufruf der Operations-API sollte einem Zugriffstoken zugeordnet sein und mit dem Autorisierungsbereich des Aufrufs übereinstimmen.

- Benutzerebene: Mit Zugriffstoken auf Benutzerebene können Sie Vorgänge auf der **Benutzer**-Ebene ausführen. Ein Beispiel hierfür ist das Abrufen von zugeordneten Konten.
- Kontoebene: Mit Zugriffstoken auf Kontoebene können Sie Vorgänge auf der **Konto**-Ebene oder der **Video**-Ebene ausführen. Beispiele hierfür sind das Hochladen von Videos, Auflisten aller Videos, Abrufen von Videoerkenntnissen usw.
- Videoebene: Mit Zugriffstoken auf Videoebene können Sie Vorgänge für ein bestimmtes **Video** ausführen. Beispiele hierfür sind das Abrufen von Videoinformationen, Herunterladen von Untertiteln, Abrufen von Widgets usw.

Sie können durch Angeben von **allowEdit=true/false** steuern, ob diese Token schreibgeschützt sind oder bearbeitet werden können.

Für die meisten Server-zu-Server-Szenarien nutzen Sie voraussichtlich dasselbe Token vom Typ „Konto“ (**account**), da hiermit sowohl **account**-Vorgänge als auch **video**-Vorgänge abgedeckt sind. Falls Sie planen, clientseitig Aufrufe von Video Indexer auszuführen (z. B. per JavaScript), sollten Sie aber ein **video**-Zugriffstoken verwenden, um zu verhindern, dass Clients Zugriff auf das gesamte Konto erhalten. Dies ist auch der Grund, warum Sie beim Einbetten von Video Indexer-Clientcode in Ihren Client (z. B. per **Get Insights Widget** (Insights-Widget abrufen) oder **Get Player Widget** (Player-Widget abrufen)) ein **video**-Zugriffstoken angeben müssen.

Zur Vereinfachung können Sie „**Authorization**-API“ > „**GetAccounts**“ verwenden, um Ihre Konten abzurufen, ohne dass Sie zuerst ein Benutzertoken beschaffen müssen. Sie können auch darum bitten, dass Sie die Konten mit gültigen Token erhalten, um einen zusätzlichen Aufruf zum Abrufen eines Kontotokens zu sparen.

Zugriffstoken laufen nach einer Stunde ab. Stellen Sie sicher, dass Ihr Zugriffstoken gültig ist, bevor Sie die Operations-API verwenden. Wenn es abgelaufen ist, müssen Sie die Authorization-API erneut aufrufen, um ein neues Zugriffstoken zu erhalten.

Sie sind nun soweit, dass Sie mit dem Integrieren für die API beginnen können. Lesen Sie sich die [ausführliche Beschreibung zu den einzelnen Video Indexer-REST-APIs](https://api-portal.videoindexer.ai/) durch.

## <a name="account-id"></a>Konto-ID

Der Parameter für die Konto-ID (Account ID) ist in allen API-Aufrufen für Vorgänge erforderlich. Die Konto-ID ist eine GUID, die auf eine der folgenden Arten abgerufen werden kann:

* Rufen Sie auf der **Video Indexer**-Website die Konto-ID ab:

    1. Navigieren Sie zur [Video Indexer](https://www.videoindexer.ai/)-Website, und melden Sie sich an.
    2. Navigieren Sie zur Seite **Einstellungen**.
    3. Kopieren Sie die Konto-ID.

        ![Einstellungen und Konto-ID für Video Indexer](./media/video-indexer-use-apis/account-id.png)

* Im  **Portal für Video Indexer-Entwickler** können Sie die Konto-ID programmgesteuert abrufen.

    Verwenden Sie die API [Get account](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account?) (Konto abrufen).

    > [!TIP]
    > Sie können Zugriffstoken für die Konten generieren, indem Sie `generateAccessTokens=true` definieren.

* Rufen Sie die Konto-ID aus der URL einer Playerseite in Ihrem Konto ab.

    Beim Wiedergeben eines Videos wird die ID nach dem Abschnitt `accounts` und vor dem Abschnitt `videos` angezeigt.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Empfehlungen

In diesem Abschnitt sind einige Empfehlungen für die Verwendung der Video Indexer-API aufgeführt.

- Wenn Sie planen, ein Video hochzuladen, sollten Sie die Datei an einem öffentlichen Netzwerkspeicherort ablegen (z. B.in einem Azure Blob Storage-Konto). Rufen Sie den Link zum Video ab, und geben Sie die URL als Parameter für den Dateiupload an.

    Die für Video Indexer angegebene URL muss auf eine Mediendatei (Audio oder Video) verweisen. Eine einfache Möglichkeit zum Überprüfen der URL (oder SAS-URL) besteht darin, sie in einen Browser einzufügen: Wenn die Datei geöffnet wird bzw. der Download der Datei beginnt, ist die URL mit hoher Wahrscheinlichkeit korrekt. Falls im Browser eine Visualisierung gerendert wird, handelt es sich wahrscheinlich nicht um einen Link zu einer Datei, sondern zu einer HTML-Seite.

- Wenn Sie die API aufrufen, mit der Videoinformationen für das angegebene Video abgerufen werden, erhalten Sie eine ausführliche JSON-Ausgabe als Inhalt der Antwort. [Lesen Sie die Details zum zurückgegebenen JSON-Code in diesem Thema](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Codebeispiel

Mit dem folgenden C#-Codeausschnitt wird die Nutzung aller Video Indexer-APIs zusammen veranschaulicht.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2"; // replace with the account's location, or with “trial” if this is a trial account
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer = new byte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(new ByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

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
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nachdem Sie dieses Tutorial abgeschlossen haben, löschen Sie Ressourcen, die Sie nicht verwenden möchten.

## <a name="see-also"></a>Weitere Informationen

- [What is Video Indexer? (preview)](video-indexer-overview.md) (Was ist Video Indexer? (Vorschauversion))
- [Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Nächste Schritte

- [Untersuchen der Details der JSON-Ausgabe](video-indexer-output-json-v2.md)
- Sehen Sie sich den [Beispielcode](https://github.com/Azure-Samples/media-services-video-indexer) an, in dem wichtige Aspekte zum Hochladen und Indizieren eines Videos veranschaulicht werden. Wenn Sie sich an den Code halten, erhalten Sie eine gute Übersicht über die Verwendung unserer API für grundlegende Funktionen. Lesen Sie unbedingt die enthaltenen Kommentare, und beachten Sie unsere Hinweise zu bewährten Methoden.

