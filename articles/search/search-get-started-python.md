---
title: 'Schnellstart: Erstellen eines Suchindex in Python'
titleSuffix: Azure Cognitive Search
description: Es wird beschrieben, wie Sie einen Suchindex erstellen, Daten laden und Abfragen ausführen, indem Sie Python, Jupyter Notebook und die Azure.Documents.Search-Clientbibliothek für Python verwenden.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/29/2021
ms.custom: devx-track-python
ms.openlocfilehash: eb5de33fd41d3a454f4d0b8d44325ed30f9c5d47
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071629"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebook"></a>Schnellstart: Erstellen eines Azure Cognitive Search-Index in Python mit Jupyter Notebook

> [!div class="op_single_selector"]
> * [Python](search-get-started-python.md)
> * [PowerShell (REST)](search-get-started-powershell.md)
> * [C#](search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [Portal](search-get-started-portal.md)
>

Erstellen Sie ein Notebook zum Erstellen, Laden und Abfragen eines Azure Cognitive Search-Index unter Verwendung von Python und der [Bibliothek „azure-search-documents“](/python/api/overview/azure/search-documents-readme) im Azure SDK für Python. In diesem Artikel wird erläutert, wie Sie ein Notebook Schritt für Schritt erstellen. Alternativ können Sie [ein bereits fertiges Jupyter Python Notebook herunterladen und installieren](https://github.com/Azure-Samples/azure-search-python-samples).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Schnellstart sind die folgenden Dienste und Tools erforderlich.

* [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section) mit Python 3.x und Jupyter Notebook.

* [Paket „azure-search-documents“](https://pypi.org/project/azure-search-documents/)

* [Erstellen Sie einen Suchdienst](search-create-service-portal.md), oder suchen Sie in Ihrem aktuellen Abonnement [nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Sie können den Free-Tarif für diesen Schnellstart verwenden. 

## <a name="copy-a-key-and-url"></a>Kopieren eines Schlüssels und einer URL

Für REST-Aufrufe sind die Dienst-URL und ein Zugriffsschlüssel für jede Anforderung erforderlich. Ein Suchdienst wird mit beidem erstellt. Gehen Sie daher wie folgt vor, um die erforderlichen Informationen zu erhalten, falls Sie Azure Cognitive Search Ihrem Abonnement hinzugefügt haben:

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com/), und rufen Sie auf der Seite **Übersicht** Ihres Suchdiensts die URL ab. Ein Beispiel für einen Endpunkt ist `https://mydemo.search.windows.net`.

1. Rufen Sie unter **Einstellungen** > **Schlüssel** einen Administratorschlüssel ab, um Vollzugriff auf den Dienst zu erhalten. Es gibt zwei austauschbare Administratorschlüssel – diese wurden zum Zweck der Geschäftskontinuität bereitgestellt, falls Sie einen Rollover für einen Schlüssel durchführen müssen. Für Anforderungen zum Hinzufügen, Ändern und Löschen von Objekten können Sie den primären oder den sekundären Schlüssel verwenden.

   ![Abrufen eines HTTP-Endpunkts und eines Zugriffsschlüssels](media/search-get-started-rest/get-url-key.png "Abrufen eines HTTP-Endpunkts und eines Zugriffsschlüssels")

Für alle an Ihren Dienst gesendeten Anforderungen ist ein API-Schlüssel erforderlich. Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

## <a name="connect-to-azure-cognitive-search"></a>Herstellen einer Verbindung mit Azure Cognitive Search

In dieser Aufgabe starten Sie Jupyter Notebook und überprüfen, ob eine Verbindung mit Azure Cognitive Search hergestellt werden kann. Dazu müssen Sie eine Liste der Indizes von Ihrem Dienst anfordern. Unter Windows mit Anaconda 3 können Sie ein Notebook mit Anaconda Navigator starten.

1. Erstellen Sie ein neues Python 3-Notebook.

1. Laden Sie in der ersten Zelle die Bibliotheken aus dem Azure SDK für Python, einschließlich [azure-search-documents](/python/api/azure-search-documents).

   ```python
    !pip install azure-search-documents --pre
    !pip show azure-search-documents
    
    import os
    from azure.core.credentials import AzureKeyCredential
    from azure.search.documents.indexes import SearchIndexClient 
    from azure.search.documents import SearchClient
    from azure.search.documents.indexes.models import (
        ComplexField,
        CorsOptions,
        SearchIndex,
        ScoringProfile,
        SearchFieldDataType,
        SimpleField,
        SearchableField
    )
   ```

1. Geben Sie in der zweiten Zelle die Anforderungselemente ein, die Konstanten in jeder Anforderung sind. Geben Sie den Suchdienstnamen, den Admin-API-Schlüssel und den Abfrage-API-Schlüssel aus dem vorherigen Schritt an. In dieser Zelle werden auch die Clients eingerichtet, die Sie für bestimmte Vorgänge verwenden: [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) zum Erstellen eines Index und [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) zum Abfragen eines Index.

   ```python
    service_name = "YOUR-SEARCH-SERIVCE-NAME"
    admin_key = "YOUR-SEARCH-SERVICE-ADMIN-API-KEY"
    
    index_name = "hotels-quickstart"
    
    # Create an SDK client
    endpoint = "https://{}.search.windows.net/".format(service_name)
    admin_client = SearchIndexClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
    
    search_client = SearchClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
   ```

1. Führen Sie in der dritten Zelle einen „delete_index“-Vorgang aus, um für Ihren Dienst alle vorhandenen *hotels-quickstart*-Indizes zu löschen. Wenn Sie den Index löschen, können Sie einen anderen *hotels-quickstart*-Index mit dem gleichen Namen erstellen.

   ```python
    try:
        result = admin_client.delete_index(index_name)
        print ('Index', index_name, 'Deleted')
    except Exception as ex:
        print (ex)
   ```

1. Führen Sie die einzelnen Schritte aus.

## <a name="1---create-an-index"></a>1\. Erstellen eines Index

Erforderliche Elemente eines Index sind beispielsweise ein Name, Feldsammlung und ein Schlüssel. Über die Feldsammlung wird die Struktur eines logischen *Suchdokuments* definiert, das sowohl zum Laden von Daten als auch zum Zurückgeben von Ergebnissen verwendet wird. 

Jedes Feld verfügt über Name, Typ und Attribute zur Bestimmung der Nutzung des Felds (z.B. Volltextsuche, Filterbarkeit oder Abrufbarkeit in Suchergebnissen). In einem Index muss eines der Felder vom Typ `Edm.String` als *Schlüssel* für die Dokumentidentität angegeben werden.

Dieser Index trägt den Namen „hotels-quickstart“ und hat die unten gezeigten Felddefinitionen. Es ist eine Teilmenge eines größeren [Hotelindexes](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON), der in anderen exemplarischen Vorgehensweisen verwendet wird. Wir haben ihn in diesem Schnellstart gekürzt.

1. Fügen Sie in der nächsten Zelle das folgende Beispiel in einer Zelle ein, um das Schema bereitzustellen.

    ```python
    # Specify the index schema
    name = index_name
    fields = [
            SimpleField(name="HotelId", type=SearchFieldDataType.String, key=True),
            SearchableField(name="HotelName", type=SearchFieldDataType.String, sortable=True),
            SearchableField(name="Description", type=SearchFieldDataType.String, analyzer_name="en.lucene"),
            SearchableField(name="Description_fr", type=SearchFieldDataType.String, analyzer_name="fr.lucene"),
            SearchableField(name="Category", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
        
            SearchableField(name="Tags", collection=True, type=SearchFieldDataType.String, facetable=True, filterable=True),
    
            SimpleField(name="ParkingIncluded", type=SearchFieldDataType.Boolean, facetable=True, filterable=True, sortable=True),
            SimpleField(name="LastRenovationDate", type=SearchFieldDataType.DateTimeOffset, facetable=True, filterable=True, sortable=True),
            SimpleField(name="Rating", type=SearchFieldDataType.Double, facetable=True, filterable=True, sortable=True),
    
            ComplexField(name="Address", fields=[
                SearchableField(name="StreetAddress", type=SearchFieldDataType.String),
                SearchableField(name="City", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="StateProvince", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="PostalCode", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="Country", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
            ])
        ]
    cors_options = CorsOptions(allowed_origins=["*"], max_age_in_seconds=60)
    scoring_profiles = []
    suggester = [{'name': 'sg', 'source_fields': ['Tags', 'Address/City', 'Address/Country']}]
    ```

1. Formulieren Sie die Anforderung in einer anderen Zelle. Diese „create_index“-Anforderung ist auf die Indexsammlung Ihres Suchdiensts ausgerichtet und erstellt ein [SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex)-Element basierend auf dem Indexschema, das Sie in der vorherigen Zelle angegeben haben.

    ```python
    index = SearchIndex(
        name=name,
        fields=fields,
        scoring_profiles=scoring_profiles,
        suggesters = suggester,
        cors_options=cors_options)
    
    try:
        result = admin_client.create_index(index)
        print ('Index', result.name, 'created')
    except Exception as ex:
        print (ex)
    ```

1. Führen Sie die einzelnen Schritte aus.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2\. Laden von Dokumenten

Zum Laden von Dokumenten erstellen Sie eine Dokumentsammlung, indem Sie ein [IndexAction](/python/api/azure-search-documents/azure.search.documents.models.indexaction)-Element für den Vorgangstyp (Hochladen, Zusammenführen und hochladen usw.) verwenden. Die Dokumente stammen von [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) auf GitHub.

1. Geben Sie in einer neuen Zelle vier Dokumente an, die dem Indexschema entsprechen. Geben Sie eine Uploadaktion für jedes Dokument an.

    ```python
    documents = [
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
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel's restaurant services.",
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
    ```  

1. Formulieren Sie die Anforderung in einer anderen Zelle. Diese „upload_documents“-Anforderung zielt auf die Dokumentsammlung des Index „hotels-quickstart“ ab und verschiebt die im vorherigen Schritt bereitgestellten Dokumente in den Cognitive Search-Index.

    ```python
    try:
        result = search_client.upload_documents(documents=documents)
        print("Upload of new document succeeded: {}".format(result[0].succeeded))
    except Exception as ex:
        print (ex.message)
    ```

1. Führen Sie die einzelnen Schritte aus, um die Dokumente in einen Index des Suchdiensts zu pushen.

## <a name="3---search-an-index"></a>3\. Durchsuchen eines Index

In diesem Schritt wird beschrieben, wie Sie einen Index abfragen, indem Sie [Durchsuchen von Dokumenten (REST)](/rest/api/searchservice/search-documents) verwenden.

1. Verwenden Sie für diesen Vorgang „search_client“. Bei dieser Abfrage wird eine leere Suche ausgeführt (`search=*`) und eine unsortierte Liste (search score = 1.0) mit beliebigen Dokumenten zurückgegeben. Da keine einschränkenden Kriterien vorhanden sind, umfasst das Ergebnis alle Dokumente. Bei dieser Abfrage werden nur jeweils zwei der Felder eines Dokuments ausgegeben. Darüber hinaus wird `include_total_count=True` hinzugefügt, um die Anzahl aller Dokumente (4) in den Ergebnissen zu erhalten.

    ```python
    results =  search_client.search(search_text="*", include_total_count=True)
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. Bei der nächsten Abfrage werden dem Suchausdruck („wifi“) ganze Begriffe hinzugefügt. Bei dieser Abfrage wird angegeben, dass das Ergebnis nur die Felder in der `select`-Anweisung umfasst. Durch die Einschränkung der zurückgegebenen Felder wird die Menge der Daten für die Rückübertragung verringert und die Latenz der Suche reduziert.

    ```python
    results =  search_client.search(search_text="wifi", include_total_count=True, select='HotelId,HotelName,Tags')
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}: {}".format(result["HotelId"], result["HotelName"], result["Tags"]))
    ```

1. Wenden Sie als Nächstes einen Filterausdruck an, bei dem nur die Hotels mit einer höheren Bewertung als 4 in absteigender Reihenfolge zurückgegeben werden.

    ```python
    results =  search_client.search(search_text="hotels", select='HotelId,HotelName,Rating', filter='Rating gt 4', order_by='Rating desc')
    
    for result in results:
        print("{}: {} - {} rating".format(result["HotelId"], result["HotelName"], result["Rating"]))
    ```

1. Fügen Sie `search_fields` hinzu, um den Abfrageabgleich auf ein einzelnes Feld festzulegen.

    ```python
    results =  search_client.search(search_text="sublime", search_fields='HotelName', select='HotelId,HotelName')
    
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. Facetten sind Bezeichnungen, die verwendet werden können, um die Struktur für die Facettennavigation zu erstellen. Bei dieser Abfrage werden Facetten und die Anzahl für die Kategorie zurückgegeben.

    ```python
    results =  search_client.search(search_text="*", facets=["Category"])
    
    facets = results.get_facets()
    
    for facet in facets["Category"]:
        print("    {}".format(facet))
    ```

1. Suchen Sie in diesem Beispiel anhand des zugehörigen Schlüssels nach einem bestimmten Dokument. Normalerweise soll ein Dokument zurückgegeben werden, wenn ein Benutzer im Suchergebnis auf ein Dokument klickt.

    ```python
    result = search_client.get_document(key="3")
    
    print("Details for hotel '3' are:")
    print("Name: {}".format(result["HotelName"]))
    print("Rating: {}".format(result["Rating"]))
    print("Category: {}".format(result["Category"]))
    ```

1. In diesem Beispiel verwenden wir die Funktion „AutoVervollständigen“. Sie wird in einem Suchfeld in der Regel für die automatische Vervollständigung potenzieller Übereinstimmungen verwendet, wenn der Benutzer Text in das Suchfeld eingibt.

   Während der Erstellung des Index wurde für die Anforderung auch eine Vorschlagsfunktion mit dem Namen „sg“ erstellt. In der Definition der Vorschlagsfunktion wird angegeben, welche Felder genutzt werden können, um für Anforderungen der Vorschlagsfunktion potenzielle Übereinstimmungen zu finden. In diesem Beispiel lauten diese Felder „Tags“, „Address/City“ und „Address/Country“. Übergeben Sie die Buchstaben „sa“ als Teilzeichenfolge, um die automatische Vervollständigung zu simulieren. Mit der autocomplete-Methode von [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) werden potenzielle Übereinstimmungen mit Begriffen zurückgesendet.

    ```python
    search_suggestion = 'sa'
    results = search_client.autocomplete(search_text=search_suggestion, suggester_name="sg", mode='twoTerms')
    
    print("Autocomplete for:", search_suggestion)
    for result in results:
        print (result['text'])
    ```

## <a name="clean-up"></a>Bereinigung

Wenn Sie in Ihrem eigenen Abonnement arbeiten, sollten Sie sich am Ende eines Projekts überlegen, ob Sie die erstellten Ressourcen noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link **Alle Ressourcen** oder **Ressourcengruppen** im linken Navigationsbereich gesucht und verwaltet werden.

Denken Sie bei Verwendung eines kostenlosen Diensts an die Beschränkung auf maximal drei Indizes, Indexer und Datenquellen. Sie können einzelne Elemente über das Portal löschen, um unter dem Limit zu bleiben. 

## <a name="next-steps"></a>Nächste Schritte

Zur Vereinfachung wird in diesem Schnellstart eine verkürzte Version des Index „Hotels“ verwendet. Sie können die vollständige Version erstellen, um weitere Abfragen zu testen. Führen Sie zum Abrufen der vollständigen Version und aller 50 Dokumente den Assistenten **Daten importieren** aus, und wählen Sie *hotels-sample* aus den integrierten Beispieldatenquellen aus.

> [!div class="nextstepaction"]
> [Schnellstart: Erstellen eines Index im Azure-Portal](search-get-started-portal.md)