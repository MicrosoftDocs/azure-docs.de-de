---
title: 'Schnellstart: Suchen nach Bildern – Bing-Bildersuche-SDK für Node.js'
description: Führen Sie mithilfe dieses Schnellstarts Ihre erste Bildersuche mit dem Bing-Bildersuche-SDK aus, das ein Wrapper für die API ist und die gleichen Funktionen enthält. Diese einfache Node.js-Anwendung sendet ein Bildsuchabfrage, analysiert die JSON-Antwort und zeigt die URL des ersten zurückgegebenen Bilds an.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 43de847452c5318fd66ae883301ba1667e6a4f05
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384353"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-nodejs"></a>Schnellstart: Suchen nach Bildern mit dem Bing-Bildersuche-SDK für Node.js

Führen Sie mithilfe dieses Schnellstarts Ihre erste Bildersuche mit dem Bing-Bildersuche-SDK aus, das ein Wrapper für die API ist und die gleichen Funktionen enthält. Diese einfache JavaScript-Anwendung sendet ein Bildsuchabfrage, analysiert die JSON-Antwort und zeigt die URL des ersten zurückgegebenen Bilds an.

Der Quellcode für dieses Beispiel ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) mit zusätzlichen Fehlerbehandlungen und Anmerkungen verfügbar.

## <a name="prerequisites"></a>Voraussetzungen
Rufen Sie unter **Suchen** einen [Cognitive Services-Zugriffsschlüssel](https://azure.microsoft.com/try/cognitive-services/) ab.  Siehe auch [Cognitive Services-Preise – Bing-Suche-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)  Siehe auch [Cognitive Services-Preise – Bing-Suche-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)

* Das [Cognitive Services-Bildersuche-SDK für Node.js](https://www.npmjs.com/package/azure-cognitiveservices-imagesearch)
    * Installation mithilfe von `npm install azure-cognitiveservices-imagesearch`
* Das Modul [Node.js Azure REST](https://www.npmjs.com/package/ms-rest-azure)
    * Installation mithilfe von `npm install ms-rest-azure`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Erstellen Sie und Initialisieren der Anwendung

1. Erstellen Sie eine neue JavaScript-Datei in Ihrer bevorzugten IDE oder Ihrem bevorzugten Editor, und stellen Sie die Genauigkeit, https und andere Anforderungen ein.

    ```javascript
    'use strict';
    const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    ```

2. Erstellen Sie in der Hauptmethode Ihres Projekts Variablen für Ihren gültigen Abonnementschlüssel, die von Bing zurückzugebenden Bildergebnisse und einen Suchbegriff. Instanziieren Sie dann den Bildsuchclient mit dem Schlüssel.

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
> [Tutorial: Einseitige Web-App für die Bing-Bildersuche](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Weitere Informationen

* [Was ist die Bing-Bildersuche?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Interaktive Onlinedemo testen](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Abrufen eines kostenlosen Cognitive Services-Zugriffsschlüssels](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Node.js-Beispiele für das Azure Cognitive Services-SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
* [Dokumentation zu Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenz zur Bing-Bildersuche-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
