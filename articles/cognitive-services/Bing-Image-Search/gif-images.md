---
title: Suchen nach GIF-Bildern mit der Bing-Bildersuche-API
titleSuffix: Azure Cognitive Services
description: Mithilfe der Bing-Bildersuche-API können Sie das ganze Web nach den passendsten GIF-Bildern suchen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: d1fcac891db240def2e7bbdcb45b45caf7f49a82
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341902"
---
# <a name="search-for-gif-images"></a>Suchen nach GIF-Bildern 

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Mithilfe der Bing-Bildersuche-API können Sie das ganze Web nach den passendsten GIF-Bildern suchen.  Entwickler können ansprechende GIFs in verschiedene Konversationsszenarios integrieren. 

Die folgende URL ist eine Abfrage für animierte GIF-Bilder.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us
```
Der Parameter [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) gibt die Suchbegriffe an.  Die letzte Abfrage gibt außerdem `animatedGif` unter Verwendung des [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)-Filterparameters an.

Verwenden Sie die folgende URL, um bing.com zu verwenden, wenn Ergebnisse für Beispiele angezeigt werden sollen.
```
https://www.bing.com/images/search?q=interesting&qft=%20filterui%3Aphoto-animatedgif

```
## <a name="query-parameters"></a>Abfrageparameter

Weitere Informationen zu Abfrageparametern und Optionen finden Sie in der [Image Search API reference (Referenz zur Bildersuche-API)](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query-parameters). Unter der Überschrift [Example search for animated gif using Java (Beispielsuche für animierte GIFs unter Verwendung von Java)](#gifExample) finden Sie ein Beispiel.

## <a name="tips-and-suggestions"></a>Tipps und Vorschläge

- Sie können die Parameter [maxFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) und [minFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) angeben. Es wird empfohlen, maxFileSize auf 2.000.000 festzulegen, da die meisten GIFs in unserem Index kleiner als 2 MB sind.  Dadurch können Sie auch besser die Datengröße kontrollieren, wenn die Bandbreite ein Problem darstellt (z.B. in Mobilfunkszenarios).
- Laden Sie die Miniaturansicht vor der Quell-URL, um die Leistung zu verbessern.  
- Wenn Sie bei der ersten Ausführung oder auf der Landing Page noch über keine Benutzerabfrage verfügen, testen Sie unsere Suche für beliebte GIFs über die [API für beliebte Bilder](trending-images.md).
- Es gibt drei Einstellungen für den [safeSearch](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch)-Parameter.  Die `strict`-Option blockiert Inhalte, die nicht jugendfrei sind.
- Unter [mkt](./language-support.md) finden Sie eine vollständige Liste der unterstützten Sprachen und Orte.
- *AnimatedGifHttps* gibt nur animierte GIF-Bilder von sicheren HTTPS-Adressen zurück. Aus Sicherheitsgründen erfordern viele Anwendungen eine Verbindung mit externen Weblinks über HTTPS. Beispielsweise erfordert der Apple App Store eine Verbindung mit Webdiensten über HTTPS. Dadurch werden Benutzerdaten während der Übertragung sicher verschlüsselt.

<a name="gifExample"></a>

## <a name="example-search-for-animated-gif-using-java"></a>Beispielsuche für animierte GIFs unter Verwendung von Java

Die folgende URL sucht nach animierten GIF-Bildern: `q=interesting`
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us

```
Wie im folgenden Beispiel dargestellt erfordert die URL-Abfrage den Header [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#headers).

Das folgende Java-Beispiel erstellt und sendet die Anforderung.

```
package gifSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac GIFsearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GIFsearch
 */


public class GIFsearch {

    // Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "YOUR-ACCESS-KEY";

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";

    static String searchTerm = "interesting";

    public static SearchResults SearchImages (String searchQuery) throws Exception {
        // construct URL of search request (endpoint + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&imageType=AnimatedGif&mkt=en-us");
        HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

        // receive JSON body
        InputStream stream = connection.getInputStream();
        String response = new Scanner(stream).useDelimiter("\\A").next();

        // construct result object for return
        SearchResults results = new SearchResults(new HashMap<String, String>(), response);

        // extract Bing-related HTTP headers
        Map<String, List<String>> headers = connection.getHeaderFields();
        for (String header : headers.keySet()) {
            if (header == null) continue;      // may have null key
            if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                results.relevantHeaders.put(header, headers.get(header).get(0));
            }
        }

        stream.close();
        return results;
    }

    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        if (subscriptionKey.length() != 32) {
            System.out.println("Invalid Bing Search API subscription key!");
            System.out.println("Please paste yours into the source code.");
            System.exit(1);
        }

        try {
            System.out.println("Searching the Web for: " + searchTerm);

            SearchResults result = SearchImages(searchTerm);

            System.out.println("\nRelevant HTTP Headers:\n");
            for (String header : result.relevantHeaders.keySet())
                System.out.println(header + ": " + result.relevantHeaders.get(header));

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(result.jsonResponse));
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }

}

//Container class for search results encapsulates relevant headers and JSON data
class SearchResults{
 HashMap<String, String> relevantHeaders;
 String jsonResponse;
 SearchResults(HashMap<String, String> headers, String json) {
     relevantHeaders = headers;
     jsonResponse = json;
 }
}

```

## <a name="results"></a>Ergebnisse
Der Code ruft die folgenden Ergebnisse als JSON-Objekte ab:

```json
    {
      "webSearchUrl": "https://www.bing.com/images/search?view\u003ddetai...",
      "name": "Very Interesting GIF - Thats Very Interesting - ...",
      "thumbnailUrl": "https://tse1.mm.bing.net/th?id\u003dOIP.yJX6Vz345JPK...",
      "datePublished": "2017-03-12T01:35:00.0000000Z",
      "contentUrl": "https://media.contoso.co/images/c895fa573df8e493ca8d0dec7d93b/raw",
      "hostPageUrl": "https://www.contoso.co/view/thats-very-interesting-christi...",
      "contentSize": "1295633 B",
      "encodingFormat": "animatedgif",
      "hostPageDisplayUrl": "https://www.contoso.co/view/thats-very-christian...",
      "width": 440,
      "height": 186,
      "thumbnail": {
        "width": 474,
        "height": 200
      },
      "imageInsightsToken": "ccid_yJX6Vz34*mid_9FF0FFA42AADA1357F042443D2103B40EA...",
      "insightsMetadata": {
        "recipeSourcesCount": 0,
        "bestRepresentativeQuery": {
          "text": "That\u0027s Very Interesting",
          "displayText": "That\u0027s Very Interesting",
          "webSearchUrl": "https://www.bing.com/images/search?q\u003dThat..."
        },
        "pagesIncludingCount": 19,
        "availableSizesCount": 2
      },
      "imageId": "9FF0FFA42AADA1357F042443D21030EAAA225F",
      "accentColor": "62452D"
    },

```

## <a name="next-steps"></a>Nächste Schritte
- [C#-Schnellstart](quickstarts/csharp.md)
- [Tutorial Image Search single-page application (Tutorial: Bildersuche für eine Single-Page-Webanwendung)](tutorial-bing-image-search-single-page-app.md)