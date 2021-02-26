---
title: 'Schnellstart: Erstellen eines Suchindex in JavaScript'
titleSuffix: Azure Cognitive Search
description: In diesem JavaScript-Schnellstart erfahren Sie, wie Sie mit JavaScript in Azure Cognitive Search einen Index erstellen, Daten laden und Abfragen ausführen.
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.devlang: javascript
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/02/2021
ms.custom: devx-track-js
ms.openlocfilehash: 7d31201f9d1a4519538aba3ac57ddfd340d936d1
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509400"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-the-javascript-sdk"></a>Schnellstart: Erstellen eines Index für Azure Cognitive Search mit dem JavaScript-SDK
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-javascript.md)
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](./search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [REST](search-get-started-rest.md)


Verwenden Sie das [Javascript/Typscript-SDK für Azure Cognitive Search](/javascript/api/overview/azure/search-documents-readme), um eine Node.js-Anwendung in JavaScript zum Erstellen, Laden und Abfragen eines Suchindex zu erstellen.

Dieser Artikel veranschaulicht Schritt für Schritt, wie Sie die Anwendung erstellen. Alternativ können Sie [den Quellcode und die Daten herunterladen](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) und die Anwendung über die Befehlszeile ausführen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, benötigen Sie die folgenden Tools und Dienste:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).

+ Ein Azure Cognitive Search-Dienst [Erstellen Sie einen Dienst](search-create-service-portal.md), oder [suchen Sie nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Für diesen Schnellstart können Sie einen kostenlosen Dienst verwenden. 

+ [Node.js](https://nodejs.org) und [NPM](https://www.npmjs.com)

+ [Visual Studio Code](https://code.visualstudio.com) oder ein anderer IDE


## <a name="set-up-your-project"></a>Einrichten des Projekts

Beginnen Sie, indem Sie den Endpunkt und den Schlüssel für Ihren Suchdienst abrufen. Erstellen Sie dann ein neues Projekt mit NPM wie unten beschrieben.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Kopieren eines Schlüssels und Endpunkts

Aufrufe, die an den Dienst gerichtet werden, erfordern jeweils einen URL-Endpunkt und einen Zugriffsschlüssel. Suchen Sie in einem ersten Schritt den API-Schlüssel und die URL, die Sie zu Ihrem Projekt hinzufügen möchten. Sie werden beide Werte bei der Erstellung des Clients in einem späteren Schritt angeben.

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com/), und rufen Sie auf der Seite **Übersicht** Ihres Suchdiensts die URL ab. Ein Beispiel für einen Endpunkt ist `https://mydemo.search.windows.net`.

2. Rufen Sie in **Einstellungen** > **Schlüssel** einen Administratorschlüssel für vollständige Rechte für den Dienst ab. Dieser ist erforderlich, wenn Sie Objekte erstellen oder löschen. Es gibt einen Primär- und einen Sekundärschlüssel, die austauschbar sind. Sie können beide verwenden.

   ![Abrufen eines HTTP-Endpunkts und eines Zugriffsschlüssels](media/search-get-started-rest/get-url-key.png "Abrufen eines HTTP-Endpunkts und eines Zugriffsschlüssels")

Für alle an Ihren Dienst gesendeten Anforderungen ist ein API-Schlüssel erforderlich. Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

### <a name="create-a-new-npm-project"></a>Erstellen eines neuen NPM-Projekts

Öffnen Sie zuerst Visual Studio Code und das zugehörige [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) oder ein anderes Terminal, z. B. die Node.js-Eingabeaufforderung.

1. Erstellen Sie ein Entwicklungsverzeichnis mit dem Namen `quickstart`:

    ```cmd
    mkdir quickstart
    cd quickstart
    ```

2. Initialisieren Sie mit NPM ein leeres Projekt durch Ausführen von 

    ```cmd
    npm init
    ```
     Übernehmen Sie die Standardwerte, außer für die Lizenz, die Sie auf „MIT“ festlegen sollten. 

3. Installieren Sie `@azure/search-documents`, das [Javascript/Typscript-SDK für Azure Cognitive Search](/javascript/api/overview/azure/search-documents-readme).

    ```cmd
    npm install @azure/search-documents
    ```

4. Installieren Sie `dotenv`, um das Importieren der Umgebungsvariablen, z. B. Dienstname und API-Schlüssel, zu ermöglichen.
    ```cmd
    npm install dotenv
    ```

5. Um sicherzustellen, dass die Projekte und ihre Abhängigkeiten ordnungsgemäß konfiguriert wurden, überprüfen Sie, ob Ihre **package.json**-Datei wie die folgende json-Datei aussieht:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Cognitive Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "@azure/search-documents": "^11.0.3",
        "dotenv": "^8.2.0"
      }
    }
    ```

6. Erstellen Sie eine **ENV**-Datei zum Speichern Ihrer Suchdienstparameter:

    ```
    SEARCH_API_KEY=<search-admin-key>
    SEARCH_API_ENDPOINT=https://<search-service-name>.search.windows.net
    ```

Ersetzen Sie den Wert `<search-service-name>` durch den Namen Ihres Suchdiensts. Ersetzen Sie `<search-admin-key>` durch den zuvor notierten Schlüsselwert. 

### <a name="create-indexjs-file"></a>Erstellen der Datei „index.js“

Als Nächstes erstellen Sie eine **index.js**-Datei, die die Hauptdatei ist, die den Code hostet.

Importieren Sie oben in dieser Datei die Bibliothek `@azure/search-documents`:

```javascript
const { SearchIndexClient, SearchClient, AzureKeyCredential, odata } = require("@azure/search-documents");
```

Als nächstes muss das `dotenv`-Paket die Parameter aus der **ENV**-Datei wie folgt einlesen:

```javascript
// Load the .env file if it exists
require("dotenv").config();

// Getting endpoint and apiKey from .env file
const endpoint = process.env.SEARCH_API_ENDPOINT || "";
const apiKey = process.env.SEARCH_API_KEY || "";
```

Nachdem die Importe und Umgebungsvariablen eingerichtet wurden, können Sie die main-Funktion definieren.

Die meisten Funktionen im SDK sind asynchron, sodass Sie die main-Funktion ebenfalls `async` machen. Sie fügen außerdem ein `main().catch()`-Element unter der main-Funktion ein, um auftretende Fehler abzufangen und zu protokollieren:

```javascript
async function main() {
    console.log(`Running Azure Cognitive Search Javascript quickstart...`);
    if (!endpoint || !apiKey) {
        console.log("Make sure to set valid values for endpoint and apiKey with proper authorization.");
        return;
    }

    // remaining quickstart code will go here
}

main().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

Damit sind Sie bereit, einen Index zu erstellen.

## <a name="1---create-index"></a>1\. Erstellen des Index 

Erstellen Sie die Datei **hotels_quickstart_index.json**.  Diese Datei definiert, wie Azure Cognitive Search mit den Dokumenten verfährt, die Sie im nächsten Schritt laden. Jedes Feld wird durch einen `name` identifiziert und weist einen bestimmten `type` auf. Jedes Feld verfügt zudem über eine Reihe von Indexattributen, die angeben, ob Azure Cognitive Search das Feld durchsuchen, filtern, sortieren und facettieren kann. Bei den meisten Feldern handelt es sich um einfache Datentypen, einige wie z.B. `AddressType` sind aber komplexe Typen, die es Ihnen ermöglichen, umfangreiche Datenstrukturen in Ihrem Index zu erstellen.  Informieren Sie sich weiter über die [unterstützten Datentypen](/rest/api/searchservice/supported-data-types) und die Indexattribute, die unter [Erstellen eines Index (REST)](/rest/api/searchservice/create-index) beschrieben sind. 

Fügen Sie der Datei **hotels_quickstart_index.json** folgenden Code hinzu, oder [laden Sie die Datei herunter](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels_quickstart_index.json). 

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```

Wenn die Indexdefinition eingerichtet ist, importieren Sie **hotels_quickstart_index.json** am Anfang von **index.js**, damit die main-Funktion auf die Indexdefinition zugreifen kann.

```javascript
const indexDefinition = require('./hotels_quickstart_index.json');
```

In der main-Funktion erstellen Sie ein `SearchIndexClient`-Objekt, das zum Erstellen und Verwalten von Indizes für Azure Cognitive Search verwendet wird. 

```javascript
const indexClient = new SearchIndexClient(endpoint, new AzureKeyCredential(apiKey));
```

Als nächstes löschen Sie den Index, falls er bereits existiert. Dies ist eine gängige Vorgehensweise für Test-/Democode.

Dazu wird eine einfache Funktion definiert, die versucht, den Index zu löschen.

```javascript
async function deleteIndexIfExists(indexClient, indexName) {
    try {
        await indexClient.deleteIndex(indexName);
        console.log('Deleting index...');
    } catch {
        console.log('Index does not exist yet.');
    }
}
```

Um die Funktion auszuführen, wird der Indexname aus der Indexdefinition extrahiert und das `indexName`-Objekt zusammen mit dem `indexClient`-Objekt an die `deleteIndexIfExists()`-Funktion übergeben.

```javascript
const indexName = indexDefinition["name"];

console.log('Checking if index exists...');
await deleteIndexIfExists(indexClient, indexName);
```

Danach sind Sie bereit, den Index mit der `createIndex()`-Methode zu erstellen.

```javascript
console.log('Creating index...');
let index = await indexClient.createIndex(indexDefinition);

console.log(`Index named ${index.name} has been created.`);
```

### <a name="run-the-sample"></a>Ausführen des Beispiels

Sie können das Beispiel jetzt starten. Führen Sie den folgenden Befehl in einem Terminalfenster aus:

```cmd
node index.js
```

Wenn Sie [den Quellcode heruntergeladen haben](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) und die erforderlichen Pakete noch nicht installiert haben, führen Sie zuerst `npm install` aus.

Es wird eine Reihe von Meldungen angezeigt, die die vom Programm ausgeführten Aktionen beschreiben. 

Öffnen Sie die **Übersicht** Ihres Suchdiensts im Azure-Portal. Wählen Sie die Registerkarte **Indizes** aus. Folgendes sollte angezeigt werden:

:::image type="content" source="media/search-get-started-javascript/create-index-no-data.png" alt-text="Screenshot des Azure-Portals, Übersicht über den Suchdienst, Registerkarte „Indizes“" border="false":::

Im nächsten Schritt fügen Sie Daten zum Index hinzu. 

## <a name="2---load-documents"></a>2\. Laden von Dokumenten 


In Azure Cognitive Search sind Dokumente Datenstrukturen, bei denen es sich sowohl um Eingaben für die Indizierung als auch um Ausgaben von Abfragen handeln kann. Sie können diese Daten per Push an den Index senden oder einen [Indexer](search-indexer-overview.md) verwenden. In diesem Fall werden die Dokumente programmseitig per Push an den Index gesendet.

Dokumenteingaben können Zeilen in einer Datenbank, Blobs in einem Blobspeicher oder JSON-Dokumente auf einem Datenträger sein. Sie können entweder [hotels.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels.json) herunterladen oder selbst eine **hotels.json**-Datei mit folgendem Inhalt erstellen:

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}
```

Ähnlich wie bei der Indexdefinition müssen Sie auch hierbei `hotels.json` am Anfang von **index.js** importieren, damit in der main-Funktion auf die Daten zugegriffen werden kann.

```javascript
const hotelData = require('./hotels.json');
```


Um Daten in den Suchindex zu indizieren, müssen Sie nun ein `SearchClient`-Objekt erstellen. Während das `SearchIndexClient`-Objekt zum Erstellen und Verwalten eines Index verwendet wird, dient das `SearchClient`-Objekt zum Hochladen von Dokumenten und Abfragen des Index.

Es gibt zwei Möglichkeiten zum Erstellen einer `SearchClient`: Die erste Option besteht darin, ein `SearchClient`-Objekt von Grund auf neu zu erstellen:

```javascript
 const searchClient = new SearchClient(endpoint, indexName, new AzureKeyCredential(apiKey));
```

Alternativ können Sie die `getSearchClient()`-Methode des `SearchIndexClient`-Objekts verwenden, um das `SearchClient`-Objekt zu erstellen:

```javascript
const searchClient = indexClient.getSearchClient(indexName);
```

Nachdem der Client definiert wurde, laden Sie die Dokumente in den Suchindex hoch. In diesem Fall verwenden Sie die `mergeOrUploadDocuments()`-Methode zum Hochladen der Dokumente, oder Sie führen sie mit einem bestehenden Dokument zusammen, wenn bereits ein Dokument mit demselben Schlüssel existiert.

```javascript
console.log('Uploading documents...');
let indexDocumentsResult = await searchClient.mergeOrUploadDocuments(hotelData['value']);

console.log(`Index operations succeeded: ${JSON.stringify(indexDocumentsResult.results[0].succeeded)}`);
```

Führen Sie das Programm erneut mit `node index.js` aus. Jetzt werden etwas andere Meldungen angezeigt als in Schritt 1. Dieses Mal ist der Index *vorhanden*, und es sollte eine Meldung angezeigt werden, die besagt, dass dieser gelöscht wird, bevor die App den neuen Index erstellt und dort Daten einfügt. 

Bevor Sie die Abfragen im nächsten Schritt ausführen, definieren Sie eine Funktion, die das Programm eine Sekunde lang warten lässt. Dies geschieht nur zu Test-/Demozwecken, um sicherzustellen, dass die Indizierung abgeschlossen ist und die Dokumente im Index für Abfragen zur Verfügung stehen.

```javascript
function sleep(ms) {
    var d = new Date();
    var d2 = null;
    do {
        d2 = new Date();
    } while (d2 - d < ms);
}
```

Um das Programm eine Sekunde lang warten zu lassen, rufen Sie die Funktion `sleep` wie unten beschrieben auf:

```javascript
sleep(1000);
```

## <a name="3---search-an-index"></a>3\. Durchsuchen eines Index

Nachdem Sie einen Index erstellt und Dokumente hochgeladen haben, können Sie nun Abfragen an den Index senden. In diesem Abschnitt werden fünf verschiedene Abfragen an den Suchindex gesendet, um verschiedene verfügbare Abfragefunktionen zu veranschaulichen.

Die Abfragen werden in einer `sendQueries()`-Funktion geschrieben, die in der main-Funktion wie folgt aufgerufen wird:

```javascript
await sendQueries(searchClient);
```

Abfragen werden mithilfe der `search()`-Methode von `searchClient` gesendet. Der erste Parameter ist der Suchtext, und der zweite Parameter ist eine beliebige zusätzliche Suchoption.

Die erste Abfrage durchsucht `*`, was dem Durchsuchen aller Elemente entspricht, und wählt drei der Felder im Index aus. Es ist ein bewährtes Verfahren, nur die Felder per `select` auszuwählen, die Sie benötigen, da das Pullen unnötiger Daten die Latenz Ihrer Abfragen erhöhen kann.

Beim `searchOptions`-Objekt für diese Abfrage ist zudem `includeTotalCount` auf `true` festgelegt, wodurch die Anzahl der gefundenen Treffer zurückgegeben wird.

```javascript
async function sendQueries(searchClient) {
    console.log('Query #1 - search everything:');
    let searchOptions = {
        includeTotalCount: true,
        select: ["HotelId", "HotelName", "Rating"]
    };

    let searchResults = await searchClient.search("*", searchOptions);
    for await (const result of searchResults.results) {
        console.log(`${JSON.stringify(result.document)}`);
    }
    console.log(`Result count: ${searchResults.count}`);

    // remaining queries go here
}
```

Die übrigen unten beschriebenen Abfragen sollten auch der `sendQueries()`-Funktion hinzugefügt werden. Sie sind hier aus Gründen der Lesbarkeit getrennt.

In der nächsten Abfrage geben Sie den Suchbegriff `"wifi"` an und fügen zudem einen Filter hinzu, um nur Ergebnisse zurückzugeben, deren Zustand gleich `'FL'` ist. Die Ergebnisse werden außerdem nach dem `Rating`-Objekt des Hotels sortiert.

```javascript
console.log('Query #2 - Search with filter, orderBy, and select:');
let state = 'FL';
searchOptions = {
    filter: odata`Address/StateProvince eq ${state}`,
    orderBy: ["Rating desc"],
    select: ["HotelId", "HotelName", "Rating"]
};

searchResults = await searchClient.search("wifi", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

Anschließend wird die Suche mit dem Parameter `searchFields` auf ein einzelnes durchsuchbares Feld beschränkt. Dies ist eine großartige Möglichkeit, Ihre Abfrage effizienter zu gestalten, wenn Sie wissen, dass Sie nur an Übereinstimmungen in bestimmten Feldern interessiert sind. 

```javascript
console.log('Query #3 - Limit searchFields:');
searchOptions = {
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("sublime cliff", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
console.log();
```

Eine weitere gängige Option, die in eine Abfrage einbezogen werden kann, ist `facets`. Mithilfe von Facetten können Sie Filter auf Ihrer Benutzeroberfläche erstellen, durch die Ihre Benutzern leichter erkennen, nach welchen Werten sie filtern können.

```javascript
console.log('Query #4 - Use facets:');
searchOptions = {
    facets: ["Category"],
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("*", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

Die abschließende Abfrage verwendet die `getDocument()`-Methode des `searchClient`-Objekts. Auf diese Weise können Sie ein Dokument effizient anhand seines Schlüssels abrufen. 

```javascript
console.log('Query #5 - Lookup document:');
let documentResult = await searchClient.getDocument(key='3')
console.log(`HotelId: ${documentResult.HotelId}; HotelName: ${documentResult.HotelName}`)
```

### <a name="run-the-sample"></a>Ausführen des Beispiels

Führen Sie das Programm mit `node index.js` aus. Jetzt werden zusätzlich zu den vorherigen Schritten die Abfragen gesendet, und die Ergebnisse werden in die Konsole geschrieben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, sollten Sie sich am Ende eines Projekts überlegen, ob Sie die erstellten Ressourcen noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link **Alle Ressourcen** oder **Ressourcengruppen** im linken Navigationsbereich gesucht und verwaltet werden.

Denken Sie bei Verwendung eines kostenlosen Diensts an die Beschränkung auf maximal drei Indizes, Indexer und Datenquellen. Sie können einzelne Elemente über das Portal löschen, um unter dem Limit zu bleiben. 

## <a name="next-steps"></a>Nächste Schritte

In dieser JavaScript-Schnellstartanleitung haben Sie eine Reihe von Aufgaben ausgeführt, um einen Index zu erstellen, Dokumente in den Index zu laden und Abfragen auszuführen. 

Wenn Sie bereits über Erfahrungen mit Azure Cognitive Search verfügen, können Sie dieses Beispiel als Ausgangspunkt für das Testen von Vorschlägen (Typ-ahead- oder AutoVervollständigen-Abfragen), Filtern und Facettennavigation verwenden. Wenn Sie mit Azure Cognitive Search noch nicht vertraut sind, empfiehlt es sich, weitere Tutorials zu bearbeiten, um ein Verständnis dafür zu entwickeln, was Sie erstellen können. Besuchen Sie unsere [Dokumentationsseite](https://azure.microsoft.com/documentation/services/search/) , um weitere Ressourcen zu finden. 

> [!div class="nextstepaction"]
> [Erstellen eines React-Front-End für Azure Cognitive Search](https://github.com/dereklegenzoff/azure-search-react-template)