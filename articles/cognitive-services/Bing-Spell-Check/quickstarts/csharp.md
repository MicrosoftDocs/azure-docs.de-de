---
title: 'Schnellstart: Überprüfen der Rechtschreibung mit der REST-API und C# – Bing-Rechtschreibprüfung'
titleSuffix: Azure Cognitive Services
description: Erste Schritte mit der Bing-Rechtschreibprüfungs-REST-API und C# zum Überprüfen von Rechtschreibung und Grammatik.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: ace7a0ccaba533c9e72961536159d32af5ab8d98
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352727"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Schnellstart: Überprüfen der Rechtschreibung mit der Bing-Rechtschreibprüfungs-REST-API und C#

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Verwenden Sie diese Schnellstartanleitung, um die Bing-Rechtschreibprüfungs-REST-API zum ersten Mal aufzurufen. Diese einfache C#-Anwendung sendet eine Anforderung an die API und gibt eine Liste mit Korrekturvorschlägen zurück. 

Die Anwendung ist zwar in C# geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist. Der Quellcode für diese Anwendung ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

* Eine beliebige Edition von [Visual Studio 2017 oder höher](https://www.visualstudio.com/downloads/).
* Das NuGet-Paket „Newtonsoft.Json“. 
     
   So installieren Sie das Paket in Visual Studio:

     1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappendatei.
     1. Klicken Sie auf **NuGet-Pakete für Projektmappe verwalten**.
     1. Suchen Sie nach *Newtonsoft.Json*, und installieren Sie das Paket.

* Unter Linux/macOS können Sie diese Anwendung mit [Mono](https://www.mono-project.com/) ausführen.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Erstellen und Initialisieren eines Projekts

1. Erstellen Sie eine neue Konsolenprojektmappe in Visual Studio mit der Bezeichnung „SpellCheckSample“. Fügen Sie dann die folgenden Namespaces in die Hauptcodedatei ein:
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Erstellen Sie Variablen für den API-Endpunkt, Ihren Abonnementschlüssel und den Text, für den die Rechtschreibprüfung durchgeführt werden soll. Sie können den globalen Endpunkt im folgenden Code oder den Endpunkt der [benutzerdefinierten Unterdomäne](../../../cognitive-services/cognitive-services-custom-subdomains.md) verwenden, der im Azure-Portal für Ihre Ressource angezeigt wird.

    ```csharp
    namespace SpellCheckSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/spellcheck?";
            static string key = "<ENTER-KEY-HERE>";
            //text to be spell-checked
            static string text = "Hollo, wrld!";
        }
    }
    ```

3. Erstellen Sie eine Zeichenfolge für Ihre Suchparameter: 

   1. Weisen Sie dem `mkt`-Parameter mit dem `=`-Operator Ihren Marktcode zu. Der Marktcode ist der Code Lands bzw. der Region, aus dem/der Sie die Anforderung stellen. 

   1. Fügen Sie den Parameter `mode` mit dem Operator `&` hinzu, und weisen Sie dann den Rechtschreibprüfungsmodus zu. Der Modus kann entweder `proof` (fängt die meisten Rechtschreib-/Grammatikfehler ab) oder `spell` (fängt die meisten Rechtschreibfehler, aber nicht so viele Grammatikfehler ab) sein.
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Erstellen und Senden einer Rechtschreibprüfungsanforderung

1. Erstellen Sie eine asynchrone Funktion mit dem Namen `SpellCheck()`, um eine Anforderung an die API zu senden. Erstellen Sie ein `HttpClient`-Objekt, und fügen Sie dem Header `Ocp-Apim-Subscription-Key` Ihren Abonnementschlüssel hinzu. Führen Sie in der-Funktion die nächsten Schritte aus.

    ```csharp
    async static void SpellCheck()
    {
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = null;
        // add the rest of the code snippets here (except for main())...
    }
    ```

2. Erstellen Sie den URI für die Anforderung, indem Sie Ihren Host, den Pfad und die Parameter anfügen.
    
    ```csharp
    string uri = host + path + params_;
    ```

3. Erstellen Sie eine Liste mit einem `KeyValuePair`-Objekt, das Ihren Text enthält, und verwenden Sie sie zum Erstellen eines `FormUrlEncodedContent`-Objekts. Legen Sie die Headerinformationen fest, und verwenden Sie `PostAsync()`, um die Anforderung zu senden.

    ```csharp
    var values = new Dictionary<string, string>();
    values.Add("text", text);
    var content = new FormUrlEncodedContent(values);
    content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
    response = await client.PostAsync(uri, new FormUrlEncodedContent(values));
    ```

## <a name="get-and-print-the-api-response"></a>Abrufen und Ausgeben der API-Antwort

### <a name="get-the-client-id-header"></a>Abrufen des Client-ID-Headers

Wenn die Antwort einen `X-MSEdge-ClientID`-Header enthält, können Sie den Wert abrufen und ausgeben.

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>Abrufen der Antwort

Rufen Sie die Antwort aus der API ab. Deserialisieren Sie das JSON-Objekt, und geben Sie es in der Konsole aus.

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>Aufrufen der Funktion für die Rechtschreibprüfung

Rufen Sie in der `SpellCheck()`-Funktion Ihres Projekts `Main()` auf.

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="run-the-application"></a>Ausführen der Anwendung

Erstellen Sie Ihr Projekt, und führen Sie es aus. Wenn Sie Visual Studio verwenden, drücken Sie **F5**, um die Datei zu debuggen.

## <a name="example-json-response"></a>JSON-Beispielantwort

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Single-Page-Web-App](../tutorials/spellcheck.md)

- [Worum handelt es sich bei der Bing-Rechtschreibprüfungs-API?](../overview.md)
- [Referenz zur Bing-Rechtschreibprüfungs-API v7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)