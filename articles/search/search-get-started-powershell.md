---
title: 'Schnellstart: Erstellen eines Suchindex in PowerShell mithilfe von REST-APIs'
titleSuffix: Azure Cognitive Search
description: In diesem REST-API-Schnellstart erfahren Sie, wie Sie mit „Invoke-RestMethod“ von PowerShell und der Azure Cognitive Search-REST-API einen Index erstellen, Daten laden und Abfragen ausführen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9dd9de9dcb01e9be200e07e5925d8b856432b620
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94742377"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-powershell-using-rest-apis"></a>Schnellstart: Erstellen eines Azure Cognitive Search-Index in PowerShell mithilfe von REST-APIs
> [!div class="op_single_selector"]
> * [PowerShell (REST)]()
> * [C#](./search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-get-started-portal.md)
> 

Dieser Artikel führt Sie durch das Erstellen, Laden und Abfragen eines Azure Cognitive Search-Index mit PowerShell und den [Azure Cognitive Search-REST-APIs](/rest/api/searchservice/). In diesem Artikel wird erläutert, wie Sie PowerShell-Befehle interaktiv ausführen können. Alternativ können Sie ein [PowerShell-Skript herunterladen und ausführen](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart), das die gleichen Vorgänge ausführt.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Schnellstart sind die folgenden Dienste und Tools erforderlich. 

+ [PowerShell 5.1 oder höher](https://github.com/PowerShell/PowerShell) mit [Invoke-RestMethod](/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) für sequenzielle und interaktive Schritte.

+ [Erstellen Sie einen Dienst für die kognitive Azure-Suche](search-create-service-portal.md), oder [suchen Sie nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in Ihrem aktuellen Abonnement. Für diesen Schnellstart können Sie einen kostenlosen Dienst verwenden. 

## <a name="copy-a-key-and-url"></a>Kopieren eines Schlüssels und einer URL

Für REST-Aufrufe sind die Dienst-URL und ein Zugriffsschlüssel für jede Anforderung erforderlich. Ein Suchdienst wird mit beidem erstellt. Gehen Sie daher wie folgt vor, um die erforderlichen Informationen zu erhalten, falls Sie Azure Cognitive Search Ihrem Abonnement hinzugefügt haben:

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com/), und rufen Sie auf der Seite **Übersicht** Ihres Suchdiensts die URL ab. Ein Beispiel für einen Endpunkt ist `https://mydemo.search.windows.net`.

2. Rufen Sie unter **Einstellungen** > **Schlüssel** einen Administratorschlüssel ab, um Vollzugriff auf den Dienst zu erhalten. Es gibt zwei austauschbare Administratorschlüssel – diese wurden zum Zweck der Geschäftskontinuität bereitgestellt, falls Sie einen Rollover für einen Schlüssel durchführen müssen. Für Anforderungen zum Hinzufügen, Ändern und Löschen von Objekten können Sie den primären oder den sekundären Schlüssel verwenden.

![Abrufen eines HTTP-Endpunkts und eines Zugriffsschlüssels](media/search-get-started-rest/get-url-key.png "Abrufen eines HTTP-Endpunkts und eines Zugriffsschlüssels")

Für alle an Ihren Dienst gesendeten Anforderungen ist ein API-Schlüssel erforderlich. Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

## <a name="connect-to-azure-cognitive-search"></a>Herstellen einer Verbindung mit Azure Cognitive Search

1. Erstellen Sie in PowerShell ein **$headers**-Objekt zum Speichern des Inhaltstyps und des API-Schlüssels. Setzen Sie für den Admin-API-Schlüssel (YOUR-ADMIN-API-KEY) einen Schlüssel ein, der für Ihren Suchdienst gültig ist. Sie müssen diesen Header für die Dauer der Sitzung nur einmal festlegen, aber Sie fügen ihn jeder Anforderung hinzu. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Erstellen Sie ein **$url**-Objekt, mit dem die Indexsammlung des Diensts angegeben wird. Setzen Sie für den Namen des Diensts (YOUR-SEARCH-SERVICE-NAME) einen gültigen Suchdienst ein.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2020-06-30&$select=name"
    ```

3. Führen Sie **Invoke-RestMethod** aus, um eine GET-Anforderung an den Dienst zu senden und die Verbindung zu überprüfen. Fügen Sie **ConvertTo-Json** hinzu, damit Sie die Antworten verfolgen können, die vom Dienst zurückgesendet werden.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Wenn der Dienst leer ist und keine Indizes aufweist, ähneln die Ergebnisse dem folgenden Beispiel. Andernfalls wird eine JSON-Darstellung der Indexdefinitionen angezeigt.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1\. Erstellen eines Index

Sofern Sie nicht das Portal verwenden, muss im Dienst ein Index vorhanden sein, bevor Sie Daten laden können. In diesem Schritt wird der Index definiert und per Pushvorgang an den Dienst übertragen. Für diesen Schritt wird [Index erstellen (REST-API)](/rest/api/searchservice/create-index) verwendet.

Erforderliche Elemente eines Index sind beispielsweise ein Name und eine Feldsammlung. Mit der Feldsammlung wird die Struktur eines *Dokuments* definiert. Jedes Feld verfügt über Name, Typ und Attribute zur Bestimmung der Nutzung (z. B. Volltextsuche, Filterbarkeit oder Abrufbarkeit in Suchergebnissen). In einem Index muss eines der Felder vom Typ `Edm.String` als *Schlüssel* für die Dokumentidentität angegeben werden.

Dieser Index trägt den Namen „hotels-quickstart“ und hat die unten gezeigten Felddefinitionen. Es ist eine Teilmenge eines größeren [Hotelindexes](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON), der in anderen Artikeln mit exemplarischer Vorgehensweise verwendet wird. Die Felddefinitionen wurden in dieser Schnellstartanleitung gekürzt.

1. Fügen Sie dieses Beispiel in PowerShell ein, um ein **$body**-Objekt mit dem Indexschema zu erstellen.

    ```powershell
    $body = @"
    {
        "name": "hotels-quickstart",  
        "fields": [
            {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
            {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
            {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
            {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
            {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
            {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Address", "type": "Edm.ComplexType", 
            "fields": [
            {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
            {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
            ]
         }
      ]
    }
    "@
    ```

2. Legen Sie den URI auf die Indexsammlung für Ihren Dienst und den Index *hotels-quickstart* fest.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2020-06-30"
    ```

3. Führen Sie den Befehl mit **$url**, **$headers** und **$body** aus, um den Index im Dienst zu erstellen. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Die Ergebnisse sollten in etwa wie hier angegeben aussehen (aus Platzgründen auf die ersten beiden Felder beschränkt):

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6EDE28CFEABDA\"",
        "name":  "hotels-quickstart",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "HotelId",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "HotelName",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  false,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  false,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> Zur Überprüfung können Sie auch die Indexliste im Portal überprüfen.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2\. Laden von Dokumenten

Senden Sie eine HTTP POST-Anforderung an den URL-Endpunkt Ihres Index, um Dokumente per Pushvorgang zu übertragen. Die REST-API für diese Aufgabe ist [Hinzufügen, Aktualisieren oder Löschen von Dokumenten](/rest/api/searchservice/addupdate-or-delete-documents).

1. Fügen Sie dieses Beispiel in PowerShell ein, um ein **$body**-Objekt mit den hochzuladenden Dokumenten zu erstellen. 

    Diese Anforderung enthält zwei vollständige und einen partiellen Datensatz. Der partielle Datensatz veranschaulicht, dass Sie unvollständige Dokumente hochladen können. Mit dem Parameter `@search.action` wird angegeben, wie das Indizieren durchgeführt wird. Gültige Werte sind „upload“, „merge“, „mergeOrUpload“ und „delete“. Mit dem Verhalten „mergeOrUpload“ wird entweder ein neues Dokument für „hotelId = 3“ erstellt oder der Inhalt aktualisiert, falls er bereits vorhanden ist.

    ```powershell
    $body = @"
    {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": 
            {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": 
            {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    "@
    ```

1. Legen Sie den Endpunkt auf die Dokumentensammlung *hotels-quickstart* fest, und schließen Sie den Indexvorgang mit ein (indexes/hotels-quickstart/docs/index).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2020-06-30"
    ```

1. Führen Sie den Befehl mit **$url**, **$headers** und **$body** aus, um Dokumente in den Index „hotels-quickstart“ zu laden.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    Die Ergebnisse sollten in etwa dem folgenden Beispiel entsprechen. Der [Statuscode 201](/rest/api/searchservice/HTTP-status-codes) sollte angezeigt werden.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
        "value":  [
                    {
                        "key":  "1",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "2",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "3",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "4",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3\. Durchsuchen eines Index

In diesem Schritt wird beschrieben, wie Sie einen Index mit der [API zum Durchsuchen von Dokumenten](/rest/api/searchservice/search-documents) abfragen.

Verwenden Sie bei der Suche mit „$urls“ unbedingt einfache Anführungszeichen. Abfragezeichenketten enthalten das Zeichen **$** , und Sie können das Escapezeichen weglassen, wenn die gesamte Zeichenkette in einfache Anführungszeichen eingeschlossen ist.

1. Legen Sie den Endpunkt auf die Dokumentensammlung *hotels-quickstart* fest, und fügen Sie einen Parameter vom Typ **search** für die Übergabe in einer Abfragezeichenfolge hinzu. 
  
   Diese Zeichenkette führt eine leere Suche (search=*) aus und gibt eine unsortierte Liste (search score = 1.0) beliebiger Dokumente zurück. Standardmäßig gibt Azure Cognitive Search 50 Übereinstimmungen auf einmal zurück. In einer strukturierten Ausgabe werden von der Abfrage eine vollständige Dokumentstruktur sowie Werte zurückgegeben. Fügen Sie **$count=true** hinzu, um eine Anzahl aller Dokumente in den Ergebnissen zu erhalten.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=*&$count=true'
    ```

1. Führen Sie den Befehl zum Senden des **$url**-Elements an den Dienst aus.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    Die Ergebnisse sollten in etwa der folgenden Ausgabe ähneln.

    ```
    {
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#docs(*)",
    "@odata.count":  4,
    "value":  [
                  {
                      "@search.score":  0.1547872,
                      "HotelId":  "2",
                      "HotelName":  "Twin Dome Motel",
                      "Description":  "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                      "Category":  "Boutique",
                      "Tags":  "pool free wifi concierge",
                      "ParkingIncluded":  false,
                      "LastRenovationDate":  "1979-02-18T00:00:00Z",
                      "Rating":  3.6,
                      "Address":  "@{StreetAddress=140 University Town Center Dr; City=Sarasota; StateProvince=FL; PostalCode=34243; Country=USA}"
                  },
                  {
                      "@search.score":  0.009068266,
                      "HotelId":  "3",
                      "HotelName":  "Triple Landscape Hotel",
                      "Description":  "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel\u0027s restaurant services.",
                      "Category":  "Resort and Spa",
                      "Tags":  "air conditioning bar continental breakfast",
                      "ParkingIncluded":  true,
                      "LastRenovationDate":  "2015-09-20T00:00:00Z",
                      "Rating":  4.8,
                      "Address":  "@{StreetAddress=3393 Peachtree Rd; City=Atlanta; StateProvince=GA; PostalCode=30326; Country=USA}"
                  },
                . . . 
    ```

Probieren Sie einige andere Abfragebeispiele aus, um ein Gefühl für die Syntax zu bekommen. Sie können eine Zeichenfolgensuche oder wörtliche $filter-Abfragen durchführen, die Ergebnisse eingrenzen, den Bereich auf bestimmte Felder beschränken usw.

```powershell
# Query example 1
# Search the entire index for the terms 'restaurant' and 'wifi'
# Return only the HotelName, Description, and Tags fields
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=restaurant wifi&$count=true&$select=HotelName,Description,Tags'

# Query example 2 
# Apply a filter to the index to find hotels rated 4 or higher
# Returns the HotelName and Rating. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=*&$filter=Rating gt 4&$select=HotelName,Rating'

# Query example 3
# Take the top two results, and show only HotelName and Category in the results
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=boutique&$top=2&$select=HotelName,Category'

# Query example 4
# Sort by a specific field (Address/City) in ascending order

$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating'
```
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, sollten Sie sich am Ende eines Projekts überlegen, ob Sie die erstellten Ressourcen noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link **Alle Ressourcen** oder **Ressourcengruppen** im linken Navigationsbereich gesucht und verwaltet werden.

Denken Sie bei Verwendung eines kostenlosen Diensts an die Beschränkung auf maximal drei Indizes, Indexer und Datenquellen. Sie können einzelne Elemente über das Portal löschen, um unter dem Limit zu bleiben. 

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie mit PowerShell den grundlegenden Workflow für die Erstellung von Inhalten und den Zugriff auf Inhalte in Azure Cognitive Search durchlaufen. Fahren Sie mit komplexeren Szenarien (etwa der Indizierung aus Azure-Datenquellen) fort, und behalten Sie dabei diese Konzepte im Hinterkopf.

> [!div class="nextstepaction"]
> [REST-Tutorial: Indizieren und Durchsuchen von teilweise strukturierten Daten (JSON-Blobs) in Azure Cognitive Search](search-semi-structured-data.md)
