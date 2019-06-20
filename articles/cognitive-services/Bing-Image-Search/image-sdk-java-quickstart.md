---
title: 'Schnellstart: Suchen nach Bildern – Bing-Bildersuche-SDK für Java'
description: Führen Sie mithilfe dieses Schnellstarts Ihre erste Bildersuche mit dem Bing-Bildersuche-SDK aus, das ein Wrapper für die API ist und die gleichen Funktionen enthält. Diese einfache Java-Anwendung sendet eine Bildersuchabfrage, analysiert die JSON-Antwort und zeigt die URL des ersten zurückgegebenen Bilds an.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 02/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 991eae9a83325118caaea5c7e3fdb64c6caa1f16
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386610"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-java"></a>Schnellstart: Suchen nach Bildern mit dem Bing-Bildersuche-SDK für Java

Führen Sie mithilfe dieses Schnellstarts Ihre erste Bildersuche mit dem Bing-Bildersuche-SDK aus, das ein Wrapper für die API ist und die gleichen Funktionen enthält. Diese einfache Java-Anwendung sendet eine Bildersuchabfrage, analysiert die JSON-Antwort und zeigt die URL des ersten zurückgegebenen Bilds an.

Der Quellcode für dieses Beispiel ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart) mit zusätzlichen Fehlerbehandlungen und Anmerkungen verfügbar.

## <a name="prerequisites"></a>Voraussetzungen
Rufen Sie unter **Suchen** einen [Cognitive Services-Zugriffsschlüssel](https://azure.microsoft.com/try/cognitive-services/) ab.  Siehe auch [Cognitive Services-Preise – Bing-Suche-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)

Sie benötigen die aktuelle Version des [Java Development Kits (JDK)](https://aka.ms/azure-jdks).

Installieren Sie die Abhängigkeit für das Bing-Bildersuche-SDK mithilfe von Maven, Gradle oder anderen Systemen zum Verwalten von Abhängigkeiten. Die Maven-POM-Datei erfordert die folgende Deklaration:

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>1.0.1</version>
    </dependency>
 </dependencies>
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

1. Erstellen Sie in Ihrer bevorzugten IDE bzw. in einem Editor ein neues Java-Projekt, und fügen Sie Ihrer Klassenimplementierung die folgenden Importe hinzu:

    ```java
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchAPI;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchManager;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImageObject;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImagesModel;
    ```

2. Erstellen Sie in Ihrer Main-Methode Variablen für Ihren Abonnementschlüssel und einen Suchbegriff. Instanziieren Sie dann den Bing-Bildersuche-Client.

    ```java
    final String subscriptionKey = "COPY_YOUR_KEY_HERE";
    String searchTerm = "canadian rockies";
    //Image search client
    BingImageSearchAPI client = BingImageSearchManager.authenticate(subscriptionKey);
    ```

## <a name="send-a-search-request-to-the-api"></a>Senden einer Suchanforderung an die API

1. Senden Sie mithilfe von `bingImages().search()` die HTTP-Anforderung, die die Suchabfrage enthält. Speichern Sie die Antwort als `ImagesModel`-Objekt.

   ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>Analysieren und Anzeigen von Ergebnissen

Analysieren Sie die Bildergebnisse, die in der Antwort zurückgegeben werden.
Wenn die Antwort Suchergebnisse enthält, speichern Sie das erste Ergebnis, und drucken Sie die Details aus, z. B. eine Miniaturansichts-URL, die ursprüngliche URL und die Gesamtzahl zurückgegebener Bilder.  

```java
if (imageResults != null && imageResults.value().size() > 0) {
    // Image results
    ImageObject firstImageResult = imageResults.value().get(0);

    System.out.println(String.format("Total number of images found: %d", imageResults.value().size()));
    System.out.println(String.format("First image thumbnail url: %s", firstImageResult.thumbnailUrl()));
    System.out.println(String.format("First image content url: %s", firstImageResult.contentUrl()));
}
else {
        System.out.println("Couldn't find image results!");
     }

```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Einseitige Web-App für die Bing-Bildersuche](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Weitere Informationen

* [Was ist die Bing-Bildersuche?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Interaktive Onlinedemo testen](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Abrufen eines kostenlosen Cognitive Services-Zugriffsschlüssels](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Java-Beispiele für das Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
* [Dokumentation zu Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenz zur Bing-Bildersuche-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
