---
title: 'Schnellstart: Überprüfen der Rechtschreibung mit der REST-API und Java – Bing-Rechtschreibprüfung'
titleSuffix: Azure Cognitive Services
description: Erste Schritte mit der Bing-Rechtschreibprüfungs-REST-API und Java zum Überprüfen von Rechtschreibung und Grammatik.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: cc6ad88b70a1addbbf7df6a53218810de683e5db
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352710"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Schnellstart: Überprüfen der Rechtschreibung mit der Bing-Rechtschreibprüfungs-REST-API und Java

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Verwenden Sie diese Schnellstartanleitung, um die Bing-Rechtschreibprüfungs-REST-API zum ersten Mal aufzurufen. Diese einfache Java-Anwendung sendet eine Anforderung an die API und gibt eine Liste mit Korrekturvorschlägen zurück. 

Die Anwendung ist zwar in Java geschrieben, an sich ist die API aber ein RESTful-Webdienst und mit den meisten Programmiersprachen kompatibel. Der Quellcode für diese Anwendung ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheck.java) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

* Das Java Development Kit (JDK) 7 oder höher

* Importieren Sie [gson-2.8.5.jar](https://libraries.io/maven/com.google.code.gson%3Agson) oder die neueste [Gson](https://github.com/google/gson)-Version. Für die Befehlszeilenausführung fügen Sie `.jar` mit der „main“-Klasse Ihrem Java-Ordner hinzu.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Erstellen und Initialisieren einer Anwendung

1. Erstellen Sie ein neues Java-Projekt in Ihrer bevorzugten IDE oder in Ihrem bevorzugten Editor mit einem Klassennamen Ihrer Wahl, und importieren Sie die folgenden Pakete:

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Erstellen Sie Variablen für den Host, Pfad und Abonnementschlüssel Ihres API-Endpunkts. Erstellen Sie anschließend Variablen für Ihren Markt, für den Text, für den die Rechtschreibprüfung ausgeführt werden soll, und für eine Zeichenfolge für den Rechtschreibprüfungsmodus. Sie können den globalen Endpunkt im folgenden Code oder den Endpunkt der [benutzerdefinierten Unterdomäne](../../../cognitive-services/cognitive-services-custom-subdomains.md) verwenden, der im Azure-Portal für Ihre Ressource angezeigt wird.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Erstellen und Senden einer API-Anforderung

1. Erstellen Sie eine Funktion namens `check()`, um die API-Anforderung zu erstellen und zu senden. Fügen Sie in dieser Funktion den in den nächsten Schritten angegebenen Code hinzu. Erstellen Sie eine Zeichenfolge für die Anforderungsparameter:

   1. Weisen Sie dem `mkt`-Parameter mit dem `=`-Operator Ihren Marktcode zu. 

   1. Fügen Sie den Parameter `mode` mit dem Operator `&` hinzu, und weisen Sie dann den Rechtschreibprüfungsmodus zu. 

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Kombinieren Sie Host, Pfad und Parameterzeichenfolge Ihres Endpunkts, um eine URL zu erstellen. Erstellen Sie ein neues `HttpsURLConnection`-Objekt.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Öffnen Sie die URL. Legen Sie die Anforderungsmethode auf `POST` fest, und fügen Sie Ihre Anforderungsparameter hinzu. Achten Sie darauf, dass Sie dem Header `Ocp-Apim-Subscription-Key` Ihren Abonnementschlüssel hinzufügen.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Erstellen Sie ein neues `DataOutputStream`-Objekt, und senden Sie die Anforderung an die API.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Formatieren und Lesen der API-Antwort

1. Fügen Sie Ihrer Klasse die `prettify()`-Methode hinzu, die den JSON-Code für eine besser lesbare Ausgabe formatiert.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. Erstellen Sie ein `BufferedReader`-Element, und lesen Sie die Antwort der API. Geben Sie sie in der Konsole aus.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line));
    }
    in.close();
    ```

## <a name="call-the-api"></a>Aufrufen der API

Rufen Sie in der „main“-Funktion Ihrer Anwendung die zuvor erstellte `check()`-Methode auf.
```java
        public static void main(String[] args) {
            try {
                check();
            }
            catch (Exception e) {
                System.out.println (e);
            }
        }
```

## <a name="run-the-application"></a>Ausführen der Anwendung

Erstellen Sie Ihr Projekt, und führen Sie es aus. Geben Sie bei Verwenden der Befehlszeile die folgenden Befehle an, um die Anwendung zu erstellen und auszuführen:

1. Erstellen Sie die Anwendung:

   ```bash
   javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
   ```

2. Führen Sie die Anwendung aus.

   ```bash
   java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
   ```

## <a name="example-json-response"></a>JSON-Beispielantwort

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Single-Page-Web-App](../tutorials/spellcheck.md)

- [Worum handelt es sich bei der Bing-Rechtschreibprüfungs-API?](../overview.md)
- [Referenz zur Bing-Rechtschreibprüfungs-API v7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)