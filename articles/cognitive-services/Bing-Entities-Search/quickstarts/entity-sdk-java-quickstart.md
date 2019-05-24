---
title: 'Schnellstart: Suchen nach Entitäten mit dem Bing-Entitätssuche-SDK für Java'
titlesuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung, um unter Verwendung des Bing-Entitätssuche-SDK für Java nach Entitäten zu suchen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 15958033ef305a44f9c5254409fa111acd9d1eb5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65813713"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-java"></a>Schnellstart: Senden einer Suchanforderung mit dem Bing-Entitätssuche-SDK für Java

Verwenden Sie diese Schnellstartanleitung, um unter Verwendung des Bing-Entitätssuche-SDK für Java mit der Suche nach Entitäten zu beginnen. Die Bing-Entitätssuche verfügt zwar über eine REST-API, die mit den meisten Programmiersprachen kompatibel ist, aber das SDK ist eine einfache Möglichkeit, den Dienst in Ihre Anwendungen zu integrieren. Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch).

## <a name="prerequisites"></a>Voraussetzungen

* Das [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

* Das Bing-Entitätssuche-SDK für Java

Installieren Sie die Abhängigkeit für das Bing-Entitätssuche-SDK mithilfe von Maven, Gradle oder anderen Systemen zum Verwalten von Abhängigkeiten. Die POM-Datei für Maven erfordert die folgende Deklaration:

```xml
<dependency>
  <groupId>com.microsoft.azure.cognitiveservices</groupId>
  <artifactId>azure-cognitiveservices-entitysearch</artifactId>
  <version>1.0.2</version>
</dependency>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Erstellen und Initialisieren eines Projekts

1. Erstellen Sie in Ihrer bevorzugten IDE oder in Ihrem bevorzugten Editor ein neues Java-Projekt, und importieren Sie die folgenden Bibliotheken.

    ```java
    import com.microsoft.azure.cognitiveservices.entitysearch.*;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.EntitySearchAPIImpl;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.SearchResponseInner;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List;
    ```

2. Erstellen Sie eine Variable für Ihren Abonnementschlüssel.

    ```java
    String subscriptionKey = "your-key-here"
    ```

## <a name="create-a-search-client"></a>Erstellen eines Suchclients

1. Implementieren Sie den `dominantEntityLookup`-Client, der Ihren API-Endpunkt und eine Instanz der `ServiceClientCredentials`-Klasse erfordert.

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Führen Sie zum Implementieren von `ServiceClientCredentials` die folgenden Schritte aus:

   1. Überschreiben Sie die Funktion `applyCredentialsFilter()` mit einem `OkHttpClient.Builder`-Objekt als Parameter. 
        
       ```java
       //...
       new ServiceClientCredentials() {
               @Override
               public void applyCredentialsFilter(OkHttpClient.Builder builder) {
               //...
               }
       //...
       ```
    
   2. Rufen Sie `builder.addNetworkInterceptor()` in `applyCredentialsFilter()` auf. Erstellen Sie ein neues `Interceptor`-Objekt, und überschreiben Sie seine `intercept()`-Methode, um ein Interceptor-Objekt vom Typ `Chain` zu übernehmen.

       ```java
       //...
       builder.addNetworkInterceptor(
           new Interceptor() {
               @Override
               public Response intercept(Chain chain) throws IOException {
               //...    
               }
           });
       ///...
       ```

   3. Erstellen Sie in der Funktion `intercept` Variablen für Ihre Anforderung. Verwenden Sie `Request.Builder()` zum Erstellen Ihrer Anforderung. Fügen Sie dem Header `Ocp-Apim-Subscription-Key` Ihren Abonnementschlüssel hinzu, und geben Sie `chain.proceed()` für das Anforderungsobjekt zurück.
            
       ```java
       //...
       public Response intercept(Chain chain) throws IOException {
           Request request = null;
           Request original = chain.request();
           Request.Builder requestBuilder = original.newBuilder()
                   .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
           request = requestBuilder.build();
           return chain.proceed(request);
       }
       //...
       ```
      ## <a name="send-a-request-and-receive-a-response"></a>Senden einer Anforderung und Erhalten einer Antwort

1. Erstellen Sie eine neue Instanz des Suchclients mit Ihrem Abonnementschlüssel. Verwenden Sie `client.entities().search()`, um eine Suchanforderung für die Suchabfrage `satya nadella` zu senden und eine Antwort zu erhalten. 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

1. Wenn Entitäten zurückgegeben wurden, konvertieren Sie sie in eine Liste. Durchlaufen Sie sie, und geben Sie die dominante Entität aus.

    ```java
    if (entityData.entities().value().size() > 0){
        // Find the entity that represents the dominant entity
        List<Thing> entrys = entityData.entities().value();
        Thing dominateEntry = null;
        for(Thing thing : entrys) {
            if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                System.out.println("\r\nSearched for \"Satya Nadella\" and found a dominant entity with this description:");
                System.out.println(thing.description());
                break;
            }
        }
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Einzelseiten-Web-App](../tutorial-bing-entities-search-single-page-app.md)

* [Was ist die Bing-Entitätssuche-API?](../overview.md )
