---
title: 'Schnellstart: Abrufen von Nachrichten mit der Bing-News-Suche-REST-API und Go'
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung wird Go zum Aufrufen der Bing-News-Suche-API verwendet. Die Ergebnisse enthalten Namen und URLs von Nachrichtenquellen, die in der Abfragezeichenfolge angegeben wurden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.openlocfilehash: 5314e1c2b13ab3b00fca3660c27d06179a4cbc2f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351979"
---
# <a name="quickstart-get-news-results-using-the-bing-news-search-rest-api-and-go"></a>Schnellstart: Abrufen von Ergebnissen für Nachrichten mit der Bing-News-Suche-REST-API und Go

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

In dieser Schnellstartanleitung wird Go zum Aufrufen der Bing-News-Suche-API verwendet. Die Ergebnisse enthalten Namen und URLs von Nachrichtenquellen, die in der Abfragezeichenfolge angegeben wurden.

## <a name="prerequisites"></a>Voraussetzungen
* Installieren Sie die [Go-Binärdateien](https://golang.org/dl/).
* Installieren Sie die Bibliothek „go-spew“, um einen Drucker für Schöndruck (Deep Pretty Printer) zum Anzeigen der Ergebnisse zu verwenden. Verwenden Sie diesen Befehl, um die Bibliothek zu installieren: `$ go get -u https://github.com/davecgh/go-spew`.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-a-project-and-import-libraries"></a>Erstellen eines Projekts und Importieren von Bibliotheken

Erstellen Sie in Ihrer IDE oder in einem Editor ein neues Go-Projekt. Importieren Sie anschließend `net/http` für Anforderungen, `ioutil` zum Lesen der Antwort, `encoding/json` zum Verarbeiten des JSON-Texts der Ergebnisse und die `go-spew`-Bibliothek zum Analysieren der JSON-Ergebnisse. 

```go
package main

import (
    "fmt"
    "net/http"
    "io/ioutil"
    "encoding/json"
    "github.com/davecgh/go-spew/spew"
)

```

## <a name="create-a-struct-to-format-the-news-search-results"></a>Erstellen einer Struktur zum Formatieren der Suchergebnisse für Nachrichten

Die `NewsAnswer`-Struktur formatiert die in der JSON-Antwort bereitgestellten Daten, bei denen es sich um komplexe Daten mit mehreren Ebenen handelt. Mit der folgenden Implementierung sind die Grundlagen abgedeckt:

```go
// This struct formats the answer provided by the Bing News Search API.
type NewsAnswer struct {
    ReadLink       string `json: "readLink"` 
    QueryContext   struct {
        OriginalQuery   string   `json: "originalQuery"`
        AdultIntent     bool        `json: "adultIntent"`
    } `json: "queryContext"`
    TotalEstimatedMatches   int  `json: totalEstimatedMatches"` 
    Sort  []struct {
        Name    string  `json: "name"`
        ID       string    `json: "id"`
        IsSelected       bool  `json: "isSelected"`
        URL      string   `json: "url"`
    }  `json: "sort"` 
    Value   []struct   {
        Name     string   `json: "name"`
        URL   string    `json: "url"`
        Image   struct   {
            Thumbnail   struct  {
                ContentUrl  string  `json: "thumbnail"`
                Width   int  `json: "width"`
                Height  int   `json: "height"`
            } `json: "thumbnail"` 
            } `json: "image"` 
            Description  string  `json: "description"`
            Provider  []struct   {
                Type   string    `json: "_type"`
                Name  string     `json: "name"`
            } `json: "provider"` 
            DatePublished   string   `json: "datePublished"`
    } `json: "value"` 
}

```

## <a name="declare-the-main-function-and-define-variables"></a>Deklarieren der main-Funktion und Definieren der Variablen  

Der folgende Code dient zum Deklarieren der main-Funktion und zum Zuweisen der erforderlichen Variablen. Vergewissern Sie sich, dass der Endpunkt korrekt ist, und ersetzen Sie den Wert `token` dann durch einen gültigen Abonnementschlüssel aus Ihrem Azure-Konto. Sie können den globalen Endpunkt im folgenden Code oder den Endpunkt der [benutzerdefinierten Unterdomäne](../../cognitive-services/cognitive-services-custom-subdomains.md) verwenden, der im Azure-Portal für Ihre Ressource angezeigt wird.

```go
func main() {
    // Verify the endpoint URI and replace the token string with a valid subscription key.  
    const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
    token := "YOUR-ACCESS-KEY"
    searchTerm := "Microsoft Cognitive Services"

    // Declare a new GET request.
    req, err := http.NewRequest("GET", endpoint, nil)
    if err != nil {
        panic(err)
    }

    // The rest of the code in this example goes here in the main function.
}
```

## <a name="query-and-header"></a>Abfrage und Header

Fügen Sie die Abfragezeichenfolge und den Zugriffsschlüsselheader hinzu.

```go
// Add the query to the request.  
param := req.URL.Query()
param.Add("q", searchTerm)
req.URL.RawQuery = param.Encode()

// Insert the subscription-key header.  
req.Header.Add("Ocp-Apim-Subscription-Key", token)

```

## <a name="get-request"></a>GET-Anforderung

Erstellen Sie den Client, und senden Sie die GET-Anforderung. 

```go
// Instantiate a client.  
client := new(http.Client)

// Send the request to Bing.  
resp, err := client.Do(req)
if err != nil {
    panic(err)
}

```

## <a name="send-the-request"></a>Senden der Anforderung

Senden Sie die Anforderung, und lesen Sie die Ergebnisse unter Verwendung von `ioutil`.

```go
resp, err := client.Do(req)
    if err != nil {
        panic(err)
}

// Close the connection.    
defer resp.Body.Close()

// Read the results
body, err := ioutil.ReadAll(resp.Body)
if err != nil {
    panic(err)
}

```

## <a name="handle-the-response"></a>Verarbeiten der Antwort

Die Funktion `Unmarshall` extrahiert Informationen aus dem JSON-Text, der von der Bing-News-Suche-API zurückgegeben wird. Anschließend zeigen Sie Knoten aus den Ergebnissen mit dem `go-spew`-Drucker für Schöndruck an.

```go
// Create a new answer object 
ans := new(NewsAnswer)
err = json.Unmarshal(body, &ans)
if err != nil {
    fmt.Println(err)
}
    
fmt.Print("Output of BingAnswer: \r\n\r\n")
    
// Iterate over search results and print the result name and URL.
for _, result := range ans.Value{
spew.Dump(result.Name, result.URL)
}

```

## <a name="results"></a>Ergebnisse

Die folgende Ausgabe enthält den Namen und die URL der einzelnen Ergebnisse:

```
(string) (len=91) "Cognitive Services Market: Global Industry Analysis and Opportunity Assessment, 2019 - 2025"
(string) (len=142) "https://www.marketwatch.com/press-release/cognitive-services-market-global-industry-analysis-and-opportunity-assessment-2019---2025-2019-02-21"
(string) (len=104) "Microsoft calls for greater collaboration to harness the power of AI to empower people with disabilities"
(string) (len=115) "https://indiaeducationdiary.in/microsoft-calls-greater-collaboration-harness-power-ai-empower-people-disabilities-2/"
(string) (len=70) "Microsoft 'Intelligent Cloud Hub' to build AI-ready workforce in India"
(string) (len=139) "https://cio.economictimes.indiatimes.com/news/cloud-computing/microsoft-intelligent-cloud-hub-to-build-ai-ready-workforce-in-india/67187807"
(string) (len=81) "Skills shortage is stopping many Asian companies from embracing A.I., study shows"
(string) (len=106) "https://www.cnbc.com/2019/02/20/microsoft-idc-study-skills-shortages-stopping-companies-from-using-ai.html"
(string) (len=143) "Cognitive Computing in Healthcare Market Emerging Top Key Vendors- Apixio, MedWhat, Healthcare X.0, Apple, Google, Microsoft, and IBM 2017-2025"
(string) (len=40) "http://www.digitaljournal.com/pr/4163064"
(string) (len=49) "Microsoft launches AI skills initiative in Brazil"
(string) (len=80) "https://www.zdnet.com/article/microsoft-launches-ai-skills-initiative-in-brazil/"
(string) (len=45) "Kuwait's CITRA and Microsoft host AI OpenHack"
(string) (len=70) "http://www.itp.net/618639-kuwaits-citra-and-microsoft-host-ai-openhack"
(string) (len=51) "CITRA and Microsoft collaborate to host AI workshop"
(string) (len=123) "https://www.zawya.com/mena/en/press-releases/story/CITRA_and_Microsoft_collaborate_to_host_AI_workshop-ZAWYA20190212105751/"

```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Worum handelt es sich bei der Bing-News-Suche?](search-the-web.md)