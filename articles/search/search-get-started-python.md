---
title: 'Schnellstart: Python und REST-APIs – Azure Search'
description: Erstellen und laden Sie einen Index mit Python, Jupyter Notebooks und der Azure Search-REST-API, und fragen Sie den Index ab.
ms.date: 05/23/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 99b4ec0be8e9fa631c5081edd42474ea89dc5dc3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244791"
---
# <a name="quickstart-create-an-azure-search-index-using-jupyter-python-notebooks"></a>Schnellstart: Erstellen eines Azure Search-Index mithilfe von Python-Jupyter Notebooks
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Portal](search-create-index-portal.md)
> 

Erstellen Sie ein Jupyter Notebook, mit dem ein Azure Search-Index mithilfe von Python und den [Azure Search-REST-APIs](https://docs.microsoft.com/rest/api/searchservice/) erstellt, geladen und abgefragt wird. In diesem Artikel wird erläutert, wie Sie ein Notebook Schritt für Schritt von Grund auf neu erstellen. Alternativ können Sie ein abgeschlossenes Notebook ausführen. Eine Kopie können Sie unter [Azure Search Python Samples repository](https://github.com/Azure-Samples/azure-search-python-samples) herunterladen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Schnellstart werden die folgenden Dienste und Tools verwendet. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section) mit Python 3.x und Jupyter Notebooks.

+ [Erstellen Sie einen Azure Search-Dienst](search-create-service-portal.md), oder suchen Sie in Ihrem aktuellen Abonnement [nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Sie können den Free-Tarif für diesen Schnellstart verwenden. 

## <a name="get-a-key-and-url"></a>Abrufen eines Schlüssels und einer URL

Für REST-Aufrufe sind die Dienst-URL und ein Zugriffsschlüssel für jede Anforderung erforderlich. Hierfür wird jeweils ein Suchdienst erstellt. Wenn Sie Azure Search also Ihrem Abonnement hinzugefügt haben, können Sie diese Schritte ausführen, um die erforderlichen Informationen zu erhalten:

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com/), und rufen Sie auf der Seite **Übersicht** Ihres Suchdiensts die URL ab. Ein Beispiel für einen Endpunkt ist `https://mydemo.search.windows.net`.

1. Rufen Sie unter **Einstellungen** > **Schlüssel** einen Administratorschlüssel ab, um Vollzugriff auf den Dienst zu erhalten. Es gibt zwei austauschbare Administratorschlüssel – diese wurden zum Zweck der Geschäftskontinuität bereitgestellt, falls Sie einen Rollover für einen Schlüssel durchführen müssen. Für Anforderungen zum Hinzufügen, Ändern und Löschen von Objekten können Sie den primären oder den sekundären Schlüssel verwenden.

![Abrufen eines HTTP-Endpunkts und Zugriffsschlüssels](media/search-fiddler/get-url-key.png "Abrufen eines HTTP-Endpunkts und Zugriffsschlüssels")

Für alle an Ihren Dienst gesendeten Anforderungen ist ein API-Schlüssel erforderlich. Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

## <a name="connect-to-azure-search"></a>Herstellen einer Verbindung mit Azure Search

Starten Sie in dieser Aufgabe ein Jupyter-Notebook, und stellen Sie sicher, dass Sie eine Verbindung mit Azure Search herstellen können. Dazu müssen Sie eine Liste der Indizes von Ihrem Dienst anfordern. Unter Windows mit Anaconda 3 können Sie ein Notebook mit Anaconda Navigator starten.

1. Erstellen Sie ein neues Python 3-Notebook.

1. Laden Sie in der ersten Zelle die Bibliotheken, die zum Verwenden von JSON und Formulieren von HTTP-Anforderungen verwendet werden.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. Geben Sie in der zweiten Zelle die Anforderungselemente ein, die Konstanten in jeder Anforderung sind. Setzen Sie für den Namen des Suchdiensts (YOUR-SEARCH-SERVICE-NAME) und den Admin-API-Schlüssel (YOUR-ADMIN-API-KEY) gültige Werte ein. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

1. Formulieren Sie in der dritten Zelle die Anforderung. Diese GET-Anforderung ist auf die Indexsammlung Ihres Suchdiensts ausgerichtet und wählt die name-Eigenschaft vorhandener Indizes aus.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Führen Sie die einzelnen Schritte aus. Wenn Indizes vorhanden sind, enthält die Antwort eine Liste der Indexnamen. Im folgenden Screenshot enthält der Dienst bereits einen „azureblob-index“ und einen „realestate-us-sample index“.

   ![Python-Skript in Jupyter Notebook mit HTTP-Anforderungen für Azure Search](media/search-get-started-python/connect-azure-search.png "Python-Skript in Jupyter Notebook mit HTTP-Anforderungen für Azure Search")

   Im Gegensatz dazu gibt eine leere Indexsammlung folgende Antwort zurück: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

> [!Tip]
> Ein kostenloser Dienst ist auf drei Indizes, Indexer und Datenquellen beschränkt. In diesem Schnellstart wird jeweils ein Objekt davon erstellt. Stellen Sie sicher, dass Sie Platz zum Erstellen neuer Objekte haben, bevor wir fortfahren.

## <a name="1---create-an-index"></a>1. Erstellen eines Index

Sofern Sie nicht das Portal verwenden, muss im Dienst ein Index vorhanden sein, bevor Sie Daten laden können. Für diesen Schritt wird die [REST-API für die Indexerstellung](https://docs.microsoft.com/rest/api/searchservice/create-index) verwendet, um ein Indexschema an den Dienst zu pushen.

Erforderliche Elemente eines Index sind beispielsweise ein Name, Feldsammlung und ein Schlüssel. Mit der Feldsammlung wird die Struktur eines *Dokuments* definiert. Jedes Feld verfügt über Name, Typ und Attribute zur Bestimmung der Nutzung des Felds (z.B. Volltextsuche, Filterbarkeit oder Abrufbarkeit in Suchergebnissen). In einem Index muss eines der Felder vom Typ `Edm.String` als *Schlüssel* für die Dokumentidentität angegeben werden.

Dieser Index hat den Namen „hotels-py“ und verfügt über die unten angegebenen Felddefinitionen. Es ist eine Teilmenge eines größeren [Hotelindexes](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON), der in anderen exemplarischen Vorgehensweisen verwendet wird. Wir haben ihn in diesem Schnellstart gekürzt.

1. Fügen Sie in der nächsten Zelle das folgende Beispiel in einer Zelle ein, um das Schema bereitzustellen. 

    ```python
    index_schema = {
       "name": "hotels-py",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. Formulieren Sie die Anforderung in einer anderen Zelle. Diese PUT-Anforderung ist auf die Indexsammlung Ihres Suchdiensts ausgerichtet und erstellt einen Index basierend auf dem Indexschema, das Sie in der vorherigen Zelle bereitgestellt haben.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Führen Sie die einzelnen Schritte aus.

   Die Antwort enthält die JSON-Darstellung des Schemas. Der folgende Screenshot zeigt nur einen Teil der Antwort.

    ![Anforderung zum Erstellen eines Index](media/search-get-started-python/create-index.png "Anforderung zum Erstellen eines Index")

> [!Tip]
> Eine weitere Möglichkeit zum Überprüfen der Indexerstellung ist die Überprüfung der Indexliste im Portal.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2. Laden von Dokumenten

Senden Sie eine HTTP POST-Anforderung an den URL-Endpunkt Ihres Index, um Dokumente per Pushvorgang zu übertragen. Die REST-API ist [Hinzufügen, Aktualisieren oder Löschen von Dokumenten](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Die Dokumente stammen von [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) auf GitHub.

1. Geben Sie in einer neuen Zelle vier Dokumente an, die dem Indexschema entsprechen. Geben Sie eine Uploadaktion für jedes Dokument an.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
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
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    ```   

2. Formulieren Sie die Anforderung in einer anderen Zelle. Diese POST-Anforderung ist auf die Dokumentensammlung des Index „hotels-py“ ausgerichtet und pusht die im vorherigen Schritt angegebenen Dokumente.

   ```python
   url = endpoint + "indexes/hotels-py/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Führen Sie die einzelnen Schritte aus, um die Dokumente in einen Index des Suchdiensts zu pushen. Die Ergebnisse sollten in etwa dem folgenden Beispiel entsprechen. 

    ![Senden von Dokumenten an einen Index](media/search-get-started-python/load-index.png "Senden von Dokumenten an einen Index")

## <a name="3---search-an-index"></a>3. Durchsuchen eines Index

In diesem Schritt wird beschrieben, wie Sie einen Index mit der [REST-API zum Durchsuchen von Dokumenten](https://docs.microsoft.com/rest/api/searchservice/search-documents) abfragen.


1. Geben Sie in einer neuen Zelle einen Abfrageausdruck an. Im folgenden Beispiel werden die Begriffe „hotels“ und „wifi“ gesucht. Zudem wird die *Anzahl* der übereinstimmenden Dokumente zurückgegeben und *ausgewählt*, welche Felder in den Suchergebnissen enthalten sein sollen.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

2. Formulieren Sie eine Anforderung in einer anderen Zelle. Diese GET-Anforderung ist auf die Dokumentensammlung des Index „hotels-py“ ausgerichtet und fügt die Abfrage an, die Sie im vorherigen Schritt angegeben haben.

   ```python
   url = endpoint + "indexes/hotels-py/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

3. Führen Sie die einzelnen Schritte aus. Die Ergebnisse sollten in etwa der folgenden Ausgabe ähneln. 

    ![Durchsuchen eines Index](media/search-get-started-python/search-index.png "Durchsuchen eines Index")

4. Probieren Sie einige andere Abfragebeispiele aus, um ein Gefühl für die Syntax zu bekommen. Sie können die Suchzeichenfolge durch die folgenden Beispiele ersetzen und die Suchanforderung dann erneut ausführen. 

   Anwenden eines Filters: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description'
   ```

   Verwenden der beiden oberen Ergebnisse:

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description'
   ```

    Sortieren nach einem bestimmten Feld:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince'
   ```

## <a name="clean-up"></a>Bereinigen 

Es ist ratsam, den Index zu löschen, wenn Sie ihn nicht mehr benötigen. Für einen kostenlosen Dienst gilt eine Beschränkung auf drei Indizes. Es kann ratsam sein, alle Indizes zu löschen, die Sie nicht aktiv verwenden, um Platz für andere Tutorials freizugeben.

   ```python
  url = endpoint + "indexes/hotels-py" + api_version
  response  = requests.delete(url, headers=headers)
   ```

Sie können die Indexlöschung überprüfen, indem Sie eine Liste der vorhandenen Indizes zurückgeben. Wenn „hotels-py“ nicht mehr vorhanden ist, wurde die Anforderung erfolgreich ausgeführt.

```python
url = endpoint + "indexes" + api_version + "&$select=name"

response  = requests.get(url, headers=headers)
index_list = response.json()
pprint(index_list)
```

## <a name="next-steps"></a>Nächste Schritte

Zur Vereinfachung wird in diesem Schnellstart eine verkürzte Version des Index „Hotels“ verwendet. Sie können die vollständige Version erstellen, um weitere Abfragen zu testen. Führen Sie zum Abrufen der vollständigen Version und aller 50 Dokumente den Assistenten **Daten importieren** aus, und wählen Sie *hotels-sample* aus den integrierten Beispieldatenquellen aus.

> [!div class="nextstepaction"]
> [Schnellstart: Erstellen eines Index im Azure-Portal](search-get-started-portal.md)
