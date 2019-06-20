---
title: 'Schnellstart: PowerShell und REST-APIs – Azure Search'
description: Erstellen und laden Sie einen Index mit der Invoke-RestMethod in PowerShell und der Azure Search-REST-API, und erstellen Sie eine Abfrage für den Index.
ms.date: 05/16/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: a82ee51a168a018a4df537c05d987974e775b6cc
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65795938"
---
# <a name="quickstart-create-an-azure-search-index-using-powershell"></a>Schnellstart: Erstellen eines Azure Search-Index mit PowerShell
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-create-index-portal.md)
> 

In diesem Artikel wird Schritt für Schritt das Erstellen, Laden und Abfragen eines Azure Search-[Index](search-what-is-an-index.md) mit PowerShell und den [REST-APIs des Azure Search-Diensts](https://docs.microsoft.com/rest/api/searchservice/) beschrieben. Die Indexdefinition und der durchsuchbare Inhalt werden im Anforderungstext als wohlgeformter JSON-Inhalt bereitgestellt.

Wenn Sie kein Azure-Abonnement besitzen, können Sie zuerst ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen und dann die [Registrierung für Azure Search](search-create-service-portal.md) durchführen.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Schnellstart werden die folgenden Dienste und Tools verwendet. 

+ [Erstellen Sie einen Azure Search-Dienst](search-create-service-portal.md), oder suchen Sie in Ihrem aktuellen Abonnement [nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Für diesen Schnellstart können Sie einen kostenlosen Dienst verwenden. 

+ [PowerShell 5.1 oder höher](https://github.com/PowerShell/PowerShell) mit [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) für sequenzielle und interaktive Schritte.

## <a name="get-a-key-and-url"></a>Abrufen eines Schlüssels und einer URL

Für REST-Aufrufe sind die Dienst-URL und ein Zugriffsschlüssel für jede Anforderung erforderlich. Hierfür wird jeweils ein Suchdienst erstellt. Wenn Sie Azure Search also Ihrem Abonnement hinzugefügt haben, können Sie diese Schritte ausführen, um die erforderlichen Informationen zu erhalten:

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com/), und rufen Sie auf der Seite **Übersicht** Ihres Suchdiensts die URL ab. Ein Beispiel für einen Endpunkt ist `https://mydemo.search.windows.net`.

2. Rufen Sie unter **Einstellungen** > **Schlüssel** einen Administratorschlüssel ab, um Vollzugriff auf den Dienst zu erhalten. Es gibt zwei austauschbare Administratorschlüssel – diese wurden zum Zweck der Geschäftskontinuität bereitgestellt, falls Sie einen Rollover für einen Schlüssel durchführen müssen. Für Anforderungen zum Hinzufügen, Ändern und Löschen von Objekten können Sie den primären oder den sekundären Schlüssel verwenden.

![Abrufen eines HTTP-Endpunkts und Zugriffsschlüssels](media/search-fiddler/get-url-key.png "Abrufen eines HTTP-Endpunkts und Zugriffsschlüssels")

Für alle an Ihren Dienst gesendeten Anforderungen ist ein API-Schlüssel erforderlich. Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

## <a name="connect-to-azure-search"></a>Herstellen einer Verbindung mit Azure Search

1. Erstellen Sie in PowerShell ein **$headers**-Objekt zum Speichern des Inhaltstyps und des API-Schlüssels. Setzen Sie für den Admin-API-Schlüssel (YOUR-ADMIN-API-KEY) einen Schlüssel ein, der für Ihren Suchdienst gültig ist. Sie müssen diesen Header für die Dauer der Sitzung nur einmal festlegen, aber Sie fügen ihn jeder Anforderung hinzu. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Erstellen Sie ein **$url**-Objekt, mit dem die Indexsammlung des Diensts angegeben wird. Setzen Sie für den Namen des Diensts (YOUR-SEARCH-SERVICE-NAME) einen gültigen Suchdienst ein.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06"
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

## <a name="1---create-an-index"></a>1. Erstellen eines Index

Sofern Sie nicht das Portal verwenden, muss im Dienst ein Index vorhanden sein, bevor Sie Daten laden können. In diesem Schritt wird der Index definiert und per Pushvorgang an den Dienst übertragen. Für diesen Schritt wird [Index erstellen (REST-API)](https://docs.microsoft.com/rest/api/searchservice/create-index) verwendet.

Erforderliche Elemente eines Index sind beispielsweise ein Name und eine Feldsammlung. Mit der Feldsammlung wird die Struktur eines *Dokuments* definiert. Jedes Feld verfügt über Name, Typ und Attribute zur Bestimmung der Nutzung (z. B. Volltextsuche, Filterbarkeit oder Abrufbarkeit in Suchergebnissen). In einem Index muss eines der Felder vom Typ `Edm.String` als *Schlüssel* für die Dokumentidentität angegeben werden.

Dieser Index hat den Namen „hotels-powershell“ und verfügt über die unten angegebenen Felddefinitionen. Es ist eine Teilmenge eines größeren [Hotelindexes](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON), der in anderen exemplarischen Vorgehensweisen verwendet wird. Wir haben ihn in diesem Schnellstart gekürzt.

1. Fügen Sie dieses Beispiel in PowerShell ein, um ein **$body**-Objekt mit dem Indexschema zu erstellen.

    ```powershell
    $body = @"
    {
        "name": "hotels-powershell",  
        "fields": [
            {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
            {"name": "baseRate", "type": "Edm.Double"},
            {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
            {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
            {"name": "hotelName", "type": "Edm.String", "facetable": false},
            {"name": "category", "type": "Edm.String"},
            {"name": "tags", "type": "Collection(Edm.String)"},
            {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
            {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
            {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
            {"name": "rating", "type": "Edm.Int32"},
            {"name": "location", "type": "Edm.GeographyPoint"}
        ]
    }
    "@
    ```

2. Legen Sie den URI auf die Indexsammlung Ihres Diensts und den Index *hotels-powershell* fest.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-powershell?api-version=2019-05-06"
    ```

3. Führen Sie den Befehl mit **$url**, **$headers** und **$body** aus, um den Index im Dienst zu erstellen. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Die Ergebnisse sollten in etwa wie hier angegeben aussehen (aus Platzgründen auf die ersten beiden Felder beschränkt):

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6A99E2DED96B0\"",
        "name":  "hotels-powershell",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "hotelId",
                        "type":  "Edm.String",
                        "searchable":  false,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  false,
                        "facetable":  false,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "baseRate",
                        "type":  "Edm.Double",
                        "searchable":  false,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> Zur Überprüfung können Sie auch die Liste „Indizes“ im Portal verwenden oder den Befehl zum Überprüfen der Dienstverbindung erneut ausführen, um den Index *hotels-powershell* in der Sammlung mit den Indizes anzuzeigen.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2. Laden von Dokumenten

Senden Sie eine HTTP POST-Anforderung an den URL-Endpunkt Ihres Index, um Dokumente per Pushvorgang zu übertragen. Die REST-API für diese Aufgabe ist [Hinzufügen, Aktualisieren oder Löschen von Dokumenten](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

1. Fügen Sie dieses Beispiel in PowerShell ein, um ein **$body**-Objekt mit den hochzuladenden Dokumenten zu erstellen. 

    Diese Anforderung enthält zwei vollständige und einen partiellen Datensatz. Der partielle Datensatz veranschaulicht, dass Sie unvollständige Dokumente hochladen können. Mit dem Parameter `@search.action` wird angegeben, wie das Indizieren durchgeführt wird. Gültige Werte sind „upload“, „merge“, „mergeOrUpload“ und „delete“. Mit dem Verhalten „mergeOrUpload“ wird entweder ein neues Dokument für „hotelId = 3“ erstellt oder der Inhalt aktualisiert, falls er bereits vorhanden ist.

    ```powershell
    $body = @"
    {
        "value": [
            {
                "@search.action": "upload",
                "hotelId": "1",
                "baseRate": 199.0,
                "description": "Best hotel in town",
                "hotelName": "Fancy Stay",
                "category": "Luxury",
                "tags": ["pool", "view", "wifi", "concierge"],
                "parkingIncluded": false,
                "smokingAllowed": false,
                "lastRenovationDate": "2010-06-27T00:00:00Z",
                "rating": 5,
                "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
            },
            {
                "@search.action": "upload",
                "hotelId": "2",
                "baseRate": 79.99,
                "description": "Cheapest hotel in town",
                "hotelName": "Roach Motel",
                "category": "Budget",
                "tags": ["motel", "budget"],
                "parkingIncluded": true,
                "smokingAllowed": true,
                "lastRenovationDate": "1982-04-28T00:00:00Z",
                "rating": 1,
                "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
            },
            {
                "@search.action": "mergeOrUpload",
                "hotelId": "3",
                "baseRate": 129.99,
                "description": "Close to town hall and the river"
            }
        ]
    }
    "@
    ```

1. Legen Sie den Endpunkt auf die Dokumentensammlung *hotels-powershell* fest, und binden Sie den Indexvorgang ein (indexes/hotels-powershell/docs/index).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-powershell/docs/index?api-version=2019-05-06"
    ```

1. Führen Sie den Befehl mit **$url**, **$headers** und **$body** aus, um Dokumente in den Index „hotels-powershell“ zu laden.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    Die Ergebnisse sollten in etwa dem folgenden Beispiel entsprechen. Der Statuscode 201 sollte angezeigt werden. Eine Beschreibung aller Statuscodes finden Sie unter [HTTP status codes (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes) (HTTP-Statuscodes (Azure Search)).

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes/hotels-powershell/$metadata#Collection(Microsoft.Azure.Search.V2017_11_11.IndexResult)",
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
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3. Durchsuchen eines Index

In diesem Schritt wird beschrieben, wie Sie einen Index mit der [API zum Durchsuchen von Dokumenten](https://docs.microsoft.com/rest/api/searchservice/search-documents) abfragen.

1. Legen Sie den Endpunkt auf die Dokumentensammlung *hotels-powershell* fest, und fügen Sie einen **search**-Parameter hinzu, um Abfragezeichenfolgen einzubinden. Diese Zeichenfolge ist eine leere Suche, und es wird eine unsortierte Liste mit allen Dokumenten zurückgegeben.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-powershell/docs?api-version=2019-05-06&search=*'
    ```

1. Führen Sie den Befehl zum Senden des **$url**-Elements an den Dienst aus.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    Die Ergebnisse sollten in etwa der folgenden Ausgabe ähneln.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes/hotels-powershell/$metadata#docs(*)",
        "value":  [
                    {
                        "@search.score":  1.0,
                        "hotelId":  "1",
                        "baseRate":  199.0,
                        "description":  "Best hotel in town",
                        "description_fr":  null,
                        "hotelName":  "Fancy Stay",
                        "category":  "Luxury",
                        "tags":  "pool view wifi concierge",
                        "parkingIncluded":  false,
                        "smokingAllowed":  false,
                        "lastRenovationDate":  "2010-06-27T00:00:00Z",
                        "rating":  5,
                        "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                    },
                    {
                        "@search.score":  1.0,
                        "hotelId":  "2",
                        "baseRate":  79.99,
                        "description":  "Cheapest hotel in town",
                        "description_fr":  null,
                        "hotelName":  "Roach Motel",
                        "category":  "Budget",
                        "tags":  "motel budget",
                        "parkingIncluded":  true,
                        "smokingAllowed":  true,
                        "lastRenovationDate":  "1982-04-28T00:00:00Z",
                        "rating":  1,
                        "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                    },
                    {
                        "@search.score":  1.0,
                        "hotelId":  "3",
                        "baseRate":  129.99,
                        "description":  "Close to town hall and the river",
                        "description_fr":  null,
                        "hotelName":  null,
                        "category":  null,
                        "tags":  "",
                        "parkingIncluded":  null,
                        "smokingAllowed":  null,
                        "lastRenovationDate":  null,
                        "rating":  null,
                        "location":  null
                    }
                ]
    }
    ```

Probieren Sie einige andere Abfragebeispiele aus, um ein Gefühl für die Syntax zu bekommen. Sie können eine Zeichenfolgensuche oder wörtliche $filter-Abfragen durchführen, die Ergebnisse eingrenzen, den Bereich auf bestimmte Felder beschränken usw.

```powershell
# Query example 1
# Search the entire index for the term 'budget'
# Return only the `hotelName` field, "Roach hotel"
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-powershell/docs?api-version=2019-05-06&search=budget&$select=hotelName'

# Query example 2 
# Apply a filter to the index to find hotels cheaper than $150 per night
# Returns the `hotelId` and `description`. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-powershell/docs?api-version=2019-05-06&search=*&$filter=baseRate lt 150&$select=hotelId,description'

# Query example 3
# Search the entire index, order by a specific field (`lastRenovationDate`) in descending order
# Take the top two results, and show only `hotelName` and `lastRenovationDate`
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-powershell/docs?api-version=2019-05-06&search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate'
```
## <a name="clean-up"></a>Bereinigen 

Es ist ratsam, den Index zu löschen, wenn Sie ihn nicht mehr benötigen. Für einen kostenlosen Dienst gilt eine Beschränkung auf drei Indizes. Es kann ratsam sein, alle Indizes zu löschen, die Sie nicht aktiv nutzen, damit Sie andere Tutorials durcharbeiten können.

```powershell
# Set the URI to the hotel index
$url = 'https://mydemo.search.windows.net/indexes/hotels-powershell?api-version=2019-05-06'

# Delete the index
Invoke-RestMethod -Uri $url -Headers $headers -Method Delete
```

## <a name="next-steps"></a>Nächste Schritte

Versuchen Sie, dem Index Beschreibungen auf Französisch hinzuzufügen. Das folgende Beispiel enthält französische Zeichenfolgen, und es werden zusätzliche Suchaktionen veranschaulicht. Verwenden Sie „mergeOrUpload“, um vorhandene Felder zu erstellen oder hinzuzufügen. Die folgenden Zeichenfolgen müssen UTF-8 codiert sein.

```json
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "1",
            "description_fr": "Meilleur hôtel en ville"
        },
        {
            "@search.action": "merge",
            "hotelId": "2",
            "description_fr": "Hôtel le moins cher en ville"
        }
    ]
}
```
