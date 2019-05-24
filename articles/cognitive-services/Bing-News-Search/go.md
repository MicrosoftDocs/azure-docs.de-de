---
title: 'Schnellstart: Abrufen von Nachrichten mit der Bing-News-Suche-REST-API und Go'
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie mit der Bing-News-Suche-API Ergebnisse für Nachrichten abrufen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 2/21/2019
ms.author: aahi
ms.openlocfilehash: 79e93e3ba0bbf9ac71a01bad0502b84dfee85297
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798499"
---
# <a name="quickstart-get-news-results-using-the-bing-news-search-rest-api-and-go"></a>Schnellstart: Abrufen von Ergebnissen für Nachrichten mit der Bing-News-Suche-REST-API und Go

In dieser Schnellstartanleitung wird Go zum Aufrufen der Bing-News-Suche-API verwendet. Die Ergebnisse enthalten Namen und URLs von Nachrichtenquellen, die in der Abfragezeichenfolge angegeben wurden.

## <a name="prerequisites"></a>Voraussetzungen
* Installieren Sie die [Go-Binärdateien](https://golang.org/dl/).
* Installieren Sie die Bibliothek „go-spew“ zum Anzeigen der Ergebnisse mit Pretty Printer
    * Installieren Sie diese Bibliothek: `$ go get -u https://github.com/davecgh/go-spew`.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-import-libraries"></a>Erstellen eines Projekts und Importieren von Bibliotheken

Erstellen Sie in Ihrer IDE oder in einem Editor ein neues Go-Projekt. Importieren Sie anschließend `net/http` für Anforderungen, `ioutil` zum Lesen der Antwort und `encoding/json` zum Verarbeiten des JSON-Texts der Ergebnisse. Die Bibliothek „go-spew“ ist zum Analysieren des JSON-Texts erforderlich. 

```
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

Mit der `NewsAnswer`-Struktur werden die in der Antwort bereitgestellten Daten formatiert. Die JSON-Antwort enthält mehrere Ebenen und ist sehr komplex.  Mit der folgenden Implementierung sind die Grundlagen abgedeckt.

```
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
            Description  string  `json: "description"`
            Provider  []struct   {
                Type   string    `json: "_type"`
                Name  string     `json: "name"`
            } `json: "provider"` 
            DatePublished   string   `json: "datePublished"`
        } `json: "image"` 
    } `json: "value"` 
}

```

## <a name="declare-the-main-function-and-define-variables"></a>Deklarieren der main-Funktion und Definieren der Variablen  

Der folgende Code dient zum Deklarieren der main-Funktion und zum Zuweisen der erforderlichen Variablen. Vergewissern Sie sich, dass der Endpunkt korrekt ist, und ersetzen Sie den Wert `token` durch einen gültigen Abonnementschlüssel aus Ihrem Azure-Konto.

```
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

```
// Add the query to the request.  
param := req.URL.Query()
param.Add("q", searchTerm)
req.URL.RawQuery = param.Encode()

// Insert the subscription-key header.  
req.Header.Add("Ocp-Apim-Subscription-Key", token)

```

## <a name="get-request"></a>Get-Anforderung

Erstellen Sie den Client, und senden Sie die Get-Anforderung. 

```
// Instantiate a client.  
client := new(http.Client)

// Send the request to Bing.  
resp, err := client.Do(req)
if err != nil {
    panic(err)
}

```

## <a name="send-the-request"></a>Senden der Anforderung

Senden Sie die Anforderung, und lesen Sie die Antwort mit `ioutil`.

```
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

Die Funktion `Unmarshall` extrahiert Informationen aus dem JSON-Text, der von der News-Suche-API zurückgegeben wird.  Anschließend können Sie Knoten aus den Ergebnissen mit Pretty Printer von `go-spew` anzeigen.

```
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

Die Ergebnisse enthalten Name und URL der einzelnen Ergebnisse.

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
