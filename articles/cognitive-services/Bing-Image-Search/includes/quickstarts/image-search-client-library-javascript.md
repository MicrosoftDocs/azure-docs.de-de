---
title: 'Schnellstart: JavaScript-Clientbibliothek für Bing-Bildersuche'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 037137cf5a6e4ddd66fc15e8ad9775ea77177ef6
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625313"
---
Führen Sie mithilfe dieses Schnellstarts Ihre erste Bildersuche mit der Bing-Bildersuche-Clientbibliothek aus, die ein Wrapper für die API ist und die gleichen Funktionen enthält. Diese einfache JavaScript-Anwendung sendet ein Bildsuchabfrage, analysiert die JSON-Antwort und zeigt die URL des ersten zurückgegebenen Bilds an.

Der Quellcode für dieses Beispiel ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) mit zusätzlichen Fehlerbehandlungen und Anmerkungen verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

* Die aktuelle Version von [Node.js](https://nodejs.org/en/download/)
* Das [Bing Bildsuche-SDK für JavaScript](https://www.npmjs.com/package/@azure/cognitiveservices-imagesearch)
     *  Führen Sie zum Installieren `npm install @azure/cognitiveservices-imagesearch` aus.
* Die `CognitiveServicesCredentials`-Klasse aus dem Paket `@azure/ms-rest-azure-js` zum Authentifizieren des Clients.
     * Führen Sie zum Installieren `npm install @azure/ms-rest-azure-js` aus.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

1. Erstellen Sie eine neue JavaScript-Datei in Ihrer bevorzugten IDE oder Ihrem bevorzugten Editor, und stellen Sie die Genauigkeit, https und andere Anforderungen ein.

    ```javascript
    'use strict';
    const ImageSearchAPIClient = require('@azure/cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    ```

2. Erstellen Sie in der Main-Methode Ihres Projekts Variablen für Ihren gültigen Abonnementschlüssel, die von Bing zurückzugebenden Bildergebnisse und einen Suchbegriff. Instanziieren Sie dann den Client für die Bildersuche mit dem Schlüssel.

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request
    let searchTerm = "canadian rockies";

    //instantiate the image search client
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new ImageSearchAPIClient(credentials);

    ```

## <a name="create-an-asynchronous-helper-function"></a>Erstellen einer asynchronen Hilfsfunktion

1. Erstellen Sie eine Funktion, um den Client asynchron aufzurufen und die Antwort vom Bing-Bildersuchdienst zurückzugeben.
    ```javascript
    //a helper function to perform an async call to the Bing Image Search API
    const sendQuery = async () => {
        return await imageSearchApiClient.imagesOperations.search(searchTerm);
    };
    ```
   ## <a name="send-a-query-and-handle-the-response"></a>Senden einer Abfrage und Verarbeiten der Antwort

1. Rufen Sie die Hilfsfunktion auf und verwenden Sie `promise`, um die in der Antwort zurückgegebenen Bildergebnisse zu analysieren.

    Wenn die Antwort Suchergebnisse enthält, speichern Sie das erste Ergebnis, und drucken Sie die Details aus, z.B. eine Miniaturansichts-URL, die ursprüngliche URL und die Gesamtzahl der zurückgegebenen Bilder.
    ```javascript
    sendQuery().then(imageResults => {
        if (imageResults == null) {
        console.log("No image results were found.");
        }
        else {
            console.log(`Total number of images returned: ${imageResults.value.length}`);
            let firstImageResult = imageResults.value[0];
            //display the details for the first image result. After running the application,
            //you can copy the resulting URLs from the console into your browser to view the image.
            console.log(`Total number of images found: ${imageResults.value.length}`);
            console.log(`Copy these URLs to view the first image returned:`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image content url: ${firstImageResult.contentUrl}`);
        }
      })
      .catch(err => console.error(err))
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Einseitige Web-App für die Bing-Bildersuche](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Weitere Informationen

* [Was ist die Bing-Bildersuche?](../../overview.md)
* [Interaktive Onlinedemo testen](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)
* [Node.js-Beispiele für das Azure Cognitive Services-SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
* [Dokumentation zu Azure Cognitive Services](../../../index.yml)
* [Referenz zur Bing-Bildersuche-API](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)