---
title: 'Tutorial: Extrahieren von Bilddetails mit der Bing-Bildersuche-API und C#'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie in diesem Artikel, wie Sie eine C#-Anwendung erstellen, die Bilddetails mithilfe der Bing-Bildersuche-API extrahiert.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 995ccc8ffeb436526608803362cc361c74a75316
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790031"
---
# <a name="tutorial-extract-image-details-using-the-bing-image-search-api-and-c"></a>Tutorial: Extrahieren von Bilddetails mit der Bing-Bildersuche-API und C#

Die Bing-Bildersuche-API stellt mehrere [Endpunkte](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint) zur Verfügung. Der `/details`-Endpunkt akzeptiert eine POST-Anforderung mit einem Bild und kann zahlreiche Bilddetails zurückgegeben. Diese C#-Anwendung sendet ein Bild über diese API und zeigt die von Bing zurückgegebenen Details an, die JSON-Objekte sind, wie beispielsweise:

![[JSON-Ergebnisse]](media/cognitive-services-bing-images-api/jsonResult.jpg)

In diesem Tutorial werden folgende Punkte erläutert:

> [!div class="checklist"]
> * Verwenden des `/details`-Endpunkts der Bildersuche in einer `POST`-Anforderung
> * Angeben von Headern für die Anforderung
> * Verwenden von URL-Parametern, um Ergebnisse anzugeben
> * Hochladen der Bilddaten und senden der `POST`-Anforderung
> * Ausgeben von JSON-Ergebnisse an die Konsole

Den Quellcode des Beispiels finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingGetSimilarImages.cs).

## <a name="prerequisites"></a>Voraussetzungen

* Eine beliebige Edition von [Visual Studio 2017 oder höher](https://visualstudio.microsoft.com/downloads/).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="construct-an-image-details-search-request"></a>Erstellen einer Suchanforderung für Bilddetails

Im Folgenden finden Sie den `/details`-Endpunkt, der POST-Anforderungen mit Bilddaten im Anforderungstext akzeptiert.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```

Beim Erstellen der Suchanforderungs-URL, folgt der `modules`-Parameter dem obigen Endpunkt und gibt die Art der Details an, die die Ergebnisse enthalten sollen:

* `modules=All`
* `modules=RecognizedEntities` (im Bild sichtbare Personen oder Orte)

Geben Sie `modules=All` in der POST-Anforderung an, um JSON-Text mit den folgenden Angaben abzurufen:

* `bestRepresentativeQuery` -eine Bing-Abfrage, die ähnliche Bilder wie das hochgeladene Bild zurückgibt
* `detectedObjects` – ein im Bild gefundenes Objekt
* `image` – Metadaten des Bildes
* `imageInsightsToken` – ein Token für eine spätere GET-Anforderung, das `RecognizedEntities` (im Bild sichtbare Personen oder Orte) aus dem Bild abruft
* `imageTags` – Tags des Bildes
* `pagesIncluding` -Webseiten, die das Bild enthalten
* `relatedSearches` – Suchvorgänge basierend auf Bilddetails
* `visuallySimilarImages` – ähnliche Bilder im Web

Geben Sie `modules=RecognizedEntities` in der POST-Anforderung an, um nur `imageInsightsToken` abzurufen. Dieses Objekt kann in einer nachfolgenden GET-Anforderung verwendet werden, um Personen oder Orte im Bild zu identifizieren.

## <a name="create-a-webclient-object-and-set-headers-for-the-api-request"></a>Erstellen eines WebClient-Objekts und Festlegen von Headern für die API-Anforderung

Erstellen Sie ein `WebClient`-Objekt, und legen Sie die Header fest. Für alle Anforderungen an die Bing-Suche-API ist ein `Ocp-Apim-Subscription-Key` erforderlich. Eine `POST`-Anforderung zum Hochladen eines Bildes muss außerdem `ContentType: multipart/form-data` angeben.

```javascript
WebClient client = new WebClient();
client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
client.Headers["ContentType"] = "multipart/form-data";
```

## <a name="upload-the-image-and-display-the-results"></a>Hochladen des Bildes und Anzeigen der Ergebnisse

Die Methode `UpLoadFile()` der Klasse `WebClient` formatiert Daten für die `POST`-Anforderung, einschließlich der Formatierung von `RequestStream` und des Aufrufs von `HttpWebRequest`.

Rufen Sie `WebClient.UpLoadFile()` mit dem `/details`-Endpunkt und der hochzuladende Bilddatei ab. Verwenden Sie die JSON-Antwort zum Initialisieren einer Instanz der `SearchResult`-Struktur, und speichern Sie die Antwort.

```javascript        
const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";
// The image to upload. Replace with your file and path.
const string imageFile = "your-image.jpg";
byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
var json = System.Text.Encoding.Default.GetString(resp);
// Create result object for return
var searchResult = new SearchResult()
{
    jsonResult = json,
    relevantHeaders = new Dictionary<String, String>()
};
```
Diese JSON-Antwort kann dann in der Konsole ausgegeben werden.

## <a name="use-an-image-insights-token-in-a-request"></a>Verwenden von ImageInsightsToken in einer Anforderung

Um das `ImageInsightsToken`-Objekt zu verwenden, das mit den Ergebnissen von `POST` zurückgegeben wird, können Sie es einer `GET`-Anforderung hinzufügen. Beispiel: 

```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```

Wenn es identifizierbare Personen oder Orte im Bild gibt, wird diese Anforderung Informationen über sie zurückgeben.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Anzeigen von Bildern und Suchoptionen in einer Single-Page-Web-App](tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Weitere Informationen

* [Referenz zur Bing-Bildersuche-API](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
