---
title: 'Schnellstart: Durchführen einer Neuigkeitensuche – Bing-News-Suche-SDK für Java'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung, um mit dem Bing-News-Suche-SDK für Java nach Nachrichten zu suchen und die Antwort zu verarbeiten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: b128f43637649f4faf104a81940f2937c2354bc6
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799723"
---
# <a name="quickstart-search-for-news-with-the-bing-news-search-sdk-for-java"></a>Schnellstart: Suchen nach Nachrichten mit dem Bing-News-Suche-SDK für Java

Verwenden Sie diese Schnellstartanleitung, um unter Verwendung des Bing-News-Suche-SDK für Java mit der Suche nach Nachrichten zu beginnen. Die Bing-News-Suche verfügt zwar über eine REST-API, die mit den meisten Programmiersprachen kompatibel ist, aber das SDK ist eine einfache Möglichkeit, den Dienst in Ihre Anwendungen zu integrieren. Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch).

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie die Abhängigkeit für das Bing-News-Suche-SDK mithilfe von Maven, Gradle oder anderen Systemen zum Verwalten von Abhängigkeiten. Die Maven-POM-Datei erfordert die folgende Deklaration:

```xml
    <dependencies>
    <dependency>
        <groupId>com.microsoft.azure.cognitiveservices</groupId>
        <artifactId>azure-cognitiveservices-newssearch</artifactId>
        <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
    </dependencies>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Erstellen und Initialisieren eines Projekts

Erstellen Sie in Ihrer bevorzugten IDE oder in Ihrem bevorzugten Editor ein neues Java-Projekt, und importieren Sie die folgenden Bibliotheken.

```java
import com.microsoft.azure.cognitiveservices.newssearch.*;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsInner;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsSearchAPIImpl;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.TrendingTopicsInner;
import com.microsoft.rest.credentials.ServiceClientCredentials;
import okhttp3.Interceptor;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;
import java.io.IOException;
```

## <a name="create-a-search-client-and-store-credentials"></a>Erstellen eines Suchclients und Speichern von Anmeldeinformationen

1. Erstellen Sie eine Methode namens `getClient()`, die einen neuen `NewsSearchAPIImpl`-Suchclient zurückgibt. Fügen Sie Ihren Endpunkt als ersten Parameter für das neue `NewsSearchAPIImpl`-Objekt und ein neues `ServiceClientCredentials`-Objekt zum Speichern Ihrer Anmeldeinformationen hinzu.

    ```java
    public static NewsSearchAPIImpl getClient(final String subscriptionKey) {
        return new NewsSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                });
    }
    ```

2. Setzen Sie zum Erstellen des `ServiceClientCredentials`-Objekts die Funktion `applyCredentialsFilter()` außer Kraft. Übergeben Sie `OkHttpClient.Builder` an die Methode, und erstellen Sie mithilfe der Methode `addNetworkInterceptor()` des Generators Ihre Anmeldeinformationen für den SDK-Aufruf.

    ```java
    new ServiceClientCredentials() {
        @Override
        public void applyCredentialsFilter(OkHttpClient.Builder builder) {
            builder.addNetworkInterceptor(
                    new Interceptor() {
                        @Override
                        public Response intercept(Chain chain) throws IOException {
                            Request request = null;
                            Request original = chain.request();
                            // Request customization: add request headers.
                            Request.Builder requestBuilder = original.newBuilder()
                                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
                            request = requestBuilder.build();
                            return chain.proceed(request);
                        }
                    });
        }
    });
    ```

## <a name="send-and-receive-a-search-request"></a>Senden und Empfangen einer Suchanforderung

1. Erstellen Sie eine Methode, die `getClient()` aufruft und eine Suchanforderung an den Bing-News-Suche-Dienst sendet. Filtern Sie die Suche mit den Parametern *market* und *count*. Geben Sie dann die folgenden Informationen zum ersten Nachrichtenergebnis aus: Name, URL, Veröffentlichungsdatum, Beschreibung, Anbietername und Gesamtanzahl der geschätzten Übereinstimmungen für Ihre Suche.

    ```java
    public static void newsSearch(String subscriptionKey)
    {
        NewsSearchAPIImpl client = getClient(subscriptionKey);
        String searchTerm = "Quantum Computing";
    
        NewsInner newsResults = client.searchs().list(searchTerm, null, null, null,
                null, null, 100, null, "en-us",
                null, null, null, null, null,
                null, null);
    
        if (newsResults.value().size() > 0)
        {
            NewsArticle firstNewsResult = newsResults.value().get(0);
    
            System.out.println(String.format("TotalEstimatedMatches value: %d", newsResults.totalEstimatedMatches()));
            System.out.println(String.format("News result count: %d", newsResults.value().size()));
            System.out.println(String.format("First news name: %s", firstNewsResult.name()));
            System.out.println(String.format("First news url: %s", firstNewsResult.url()));
            System.out.println(String.format("First news description: %s", firstNewsResult.description()));
            System.out.println(String.format("First news published time: %s", firstNewsResult.datePublished()));
            System.out.println(String.format("First news provider: %s", firstNewsResult.provider().get(0).name()));
        }
        else
        {
            System.out.println("Couldn't find news results!");
        }
    
    }
    
    ```

2. Fügen Sie Ihre Suchmethode zu einer Methode vom Typ `main()` hinzu, um den Code auszuführen.

    ```java 
    public static void main(String[] args) {
        String subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
        NewsSearchSDK.newsSearch(subscriptionKey);
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Single-Page-Web-App](tutorial-bing-news-search-single-page-app.md)
