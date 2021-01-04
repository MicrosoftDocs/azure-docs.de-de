---
title: 'Schnellstart: Java-Clientbibliothek für Bing-Websuche'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/05/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 042a3edf8efe080064a3c41fd55975a40bf47e5e
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94386642"
---
Die Clientbibliothek für die Bing-Websuche erleichtert die Integration der Bing-Websuche in Ihre Java-Anwendung. In dieser Schnellstartanleitung erfahren Sie, wie Sie eine Anforderung senden, eine JSON-Antwort empfangen und die Ergebnisse filtern und analysieren.

Möchten Sie den Code sofort sehen? Beispiele für die [Clientbibliotheken für die Bing-Suche für Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search) sind auf GitHub verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

Im Folgenden sind die Tools aufgeführt, die Sie zum Ausführen dieser Schnellstartanleitung benötigen:

* [JDK 7 oder 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* [Apache Maven](https://maven.apache.org/download.cgi) oder ein anderes Buildautomatisierungstool
* Abonnementschlüssel

[!INCLUDE [bing-web-search-quickstart-signup](~/includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-set-up-your-pom-file"></a>Erstellen eines Projekts und Einrichten Ihrer POM-Datei

Erstellen Sie mit Maven oder einem anderen Buildautomatisierungstool ein neues Java-Projekt. Wenn Sie Maven verwenden, fügen Sie Ihrer [POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)-Datei (Projektobjektmodell) die folgenden Zeilen hinzu. Ersetzen Sie alle Instanzen von `mainClass` durch Ihre Anwendung.

```xml
<build>
    <plugins>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.4.0</version>
        <configuration>
          <!--Your comment
            Replace the mainClass with the path to your java application.
            It should begin with com and doesn't require the .java extension.
            For example: com.bingwebsearch.app.BingWebSearchSample. This maps to
            The following directory structure:
            src/main/java/com/bingwebsearch/app/BingWebSearchSample.java.
          -->
          <mainClass>com.path.to.your.app.APP_NAME</mainClass>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.0</version>
        <configuration>
          <source>1.7</source>
          <target>1.7</target>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>attached</goal>
            </goals>
            <configuration>
              <descriptorRefs>
                <descriptorRef>jar-with-dependencies</descriptorRef>
              </descriptorRefs>
              <archive>
                <manifest>
                  <!--Your comment
                    Replace the mainClass with the path to your java application.
                    For example: com.bingwebsearch.app.BingWebSearchSample.java.
                    This maps to the following directory structure:
                    src/main/java/com/bingwebsearch/app/BingWebSearchSample.java.
                  -->
                  <mainClass>com.path.to.your.app.APP_NAME.java</mainClass>
                </manifest>
              </archive>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.9.0</version>
    </dependency>
    <dependency>
      <groupId>commons-net</groupId>
      <artifactId>commons-net</artifactId>
      <version>3.3</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-websearch</artifactId>
      <version>1.0.1</version>
    </dependency>
  </dependencies>
```

## <a name="declare-dependencies"></a>Deklarieren von Abhängigkeiten

Öffnen Sie Ihr Projekt in Ihrer bevorzugten IDE oder in Ihrem bevorzugten Editor, und importieren Sie die folgenden Abhängigkeiten:

```java
import com.microsoft.azure.cognitiveservices.search.websearch.BingWebSearchAPI;
import com.microsoft.azure.cognitiveservices.search.websearch.BingWebSearchManager;
import com.microsoft.azure.cognitiveservices.search.websearch.models.ImageObject;
import com.microsoft.azure.cognitiveservices.search.websearch.models.NewsArticle;
import com.microsoft.azure.cognitiveservices.search.websearch.models.SearchResponse;
import com.microsoft.azure.cognitiveservices.search.websearch.models.VideoObject;
import com.microsoft.azure.cognitiveservices.search.websearch.models.WebPage;
```

Wenn Sie das Projekt mit Maven erstellt haben, sollte das Paket bereits deklariert sein. Falls nicht, deklarieren Sie das Paket. Beispiel:

```java
package com.bingwebsearch.app
```

## <a name="declare-the-bingwebsearchsample-class"></a>Deklarieren der BingWebSearchSample-Klasse

Deklarieren Sie die Klasse `BingWebSearchSample`. Sie enthält den Großteil unseres Codes, einschließlich der Methode `main`.  

```java
public class BingWebSearchSample {

// The code in the following sections goes here.

}
```

## <a name="construct-a-request"></a>Erstellen einer Anforderung

Die Methode `runSample` in der Klasse `BingWebSearchSample` erstellt die Anforderung. Kopieren Sie den folgenden Code in Ihre Anwendung:

```java
public static boolean runSample(BingWebSearchAPI client) {
    /*
     * This function performs the search.
     *
     * @param client instance of the Bing Web Search API client
     * @return true if sample runs successfully
     */
    try {
        /*
         * Performs a search based on the .withQuery and prints the name and
         * url for the first web pages, image, news, and video result
         * included in the response.
         */
        System.out.println("Searched Web for \"Xbox\"");
        // Construct the request.
        SearchResponse webData = client.bingWebs().search()
            .withQuery("Xbox")
            .withMarket("en-us")
            .withCount(10)
            .execute();

// Code continues in the next section...
```

## <a name="handle-the-response"></a>Verarbeiten der Antwort

Als Nächstes fügen Sie Code zum Analysieren der Antwort und Ausgeben der Ergebnisse hinzu. Der `name` und die `url` für die erste Webseite, das erste Bild, den ersten Nachrichtenartikel und das erste Video werden ausgegeben, wenn sie im Antwortobjekt enthalten sind.

```java
/*
* WebPages
* If the search response has web pages, the first result's name
* and url are printed.
*/
if (webData != null && webData.webPages() != null && webData.webPages().value() != null &&
        webData.webPages().value().size() > 0) {
    // find the first web page
    WebPage firstWebPagesResult = webData.webPages().value().get(0);

    if (firstWebPagesResult != null) {
        System.out.println(String.format("Webpage Results#%d", webData.webPages().value().size()));
        System.out.println(String.format("First web page name: %s ", firstWebPagesResult.name()));
        System.out.println(String.format("First web page URL: %s ", firstWebPagesResult.url()));
    } else {
        System.out.println("Couldn't find the first web result!");
    }
} else {
    System.out.println("Didn't find any web pages...");
}
/*
 * Images
 * If the search response has images, the first result's name
 * and url are printed.
 */
if (webData != null && webData.images() != null && webData.images().value() != null &&
        webData.images().value().size() > 0) {
    // find the first image result
    ImageObject firstImageResult = webData.images().value().get(0);

    if (firstImageResult != null) {
        System.out.println(String.format("Image Results#%d", webData.images().value().size()));
        System.out.println(String.format("First Image result name: %s ", firstImageResult.name()));
        System.out.println(String.format("First Image result URL: %s ", firstImageResult.contentUrl()));
    } else {
        System.out.println("Couldn't find the first image result!");
    }
} else {
    System.out.println("Didn't find any images...");
}
/*
 * News
 * If the search response has news articles, the first result's name
 * and url are printed.
 */
if (webData != null && webData.news() != null && webData.news().value() != null &&
        webData.news().value().size() > 0) {
    // find the first news result
    NewsArticle firstNewsResult = webData.news().value().get(0);
    if (firstNewsResult != null) {
        System.out.println(String.format("News Results#%d", webData.news().value().size()));
        System.out.println(String.format("First news result name: %s ", firstNewsResult.name()));
        System.out.println(String.format("First news result URL: %s ", firstNewsResult.url()));
    } else {
        System.out.println("Couldn't find the first news result!");
    }
} else {
    System.out.println("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response has videos, the first result's name
 * and url are printed.
 */
if (webData != null && webData.videos() != null && webData.videos().value() != null &&
        webData.videos().value().size() > 0) {
    // find the first video result
    VideoObject firstVideoResult = webData.videos().value().get(0);

    if (firstVideoResult != null) {
        System.out.println(String.format("Video Results#%s", webData.videos().value().size()));
        System.out.println(String.format("First Video result name: %s ", firstVideoResult.name()));
        System.out.println(String.format("First Video result URL: %s ", firstVideoResult.contentUrl()));
    } else {
        System.out.println("Couldn't find the first video result!");
    }
} else {
    System.out.println("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>Deklarieren der main-Methode

In dieser Anwendung enthält die main-Methode Code, der den Client instanziiert, `subscriptionKey` überprüft und `runSample` aufruft. Geben Sie einen gültigen Abonnementschlüssel für Ihr Azure-Konto ein, bevor Sie fortfahren.

```java
public static void main(String[] args) {
    try {
        // Enter a valid subscription key for your account.
        final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
        // Instantiate the client.
        BingWebSearchAPI client = BingWebSearchManager.authenticate(subscriptionKey);
        // Make a call to the Bing Web Search API.
        runSample(client);
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }
}
```

## <a name="run-the-program"></a>Ausführen des Programms

Im letzten Schritt wird das Programm ausgeführt.

```console
mvn compile exec:java
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Denken Sie daran, nach Abschluss dieses Projekts Ihren Abonnementschlüssel aus dem Anwendungscode zu entfernen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Cognitive Services Java SDK samples (Cognitive Services SDK-Beispiele für Java)](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch)

## <a name="see-also"></a>Weitere Informationen

* [Referenz zum Azure Java SDK](/java/api/overview/azure/cognitiveservices/client/bingwebsearchapi?view=azure-java-stable)