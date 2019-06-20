---
title: 'Schnellstart: Übersetzen von Text, Java: Textübersetzungs-API'
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Java und der Textübersetzungs-API eine Textzeichenfolge vom Englischen ins Italienische und Deutsche übersetzen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: fbbe139160a7efd528e63e206aa37ac72b2542df
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515188"
---
# <a name="quickstart-use-the-translator-text-api-to-translate-a-string-using-java"></a>Schnellstart: Verwenden der Textübersetzungs-API zum Übersetzen einer Zeichenfolge mit Java

In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Java und der Textübersetzungs-API eine Textzeichenfolge vom Englischen ins Italienische und Deutsche übersetzen.

Für diese Schnellstartanleitung wird ein [Azure Cognitive Services-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) mit einer Textübersetzungsressource benötigt. Wenn Sie über kein Konto verfügen, können Sie über die [kostenlose Testversion](https://azure.microsoft.com/try/cognitive-services/) einen Abonnementschlüssel abrufen.

## <a name="prerequisites"></a>Voraussetzungen

* [Mindestens JDK 7](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Ein Azure-Abonnementschlüssel für die Textübersetzung

## <a name="initialize-a-project-with-gradle"></a>Initialisieren eines Projekts mit Gradle

Erstellen Sie zunächst ein Arbeitsverzeichnis für dieses Projekt. Führen Sie den folgenden Befehl über die Befehlszeile (oder das Terminal) aus:

```console
mkdir translator-sample
cd translator-sample
```

Als Nächstes initialisieren Sie ein Gradle-Projekt. Mit diesem Befehl werden grundlegende Builddateien für Gradle und insbesondere die Datei `build.gradle.kts` erstellt. Diese Datei wird zur Laufzeit zum Erstellen und Konfigurieren Ihrer Anwendung verwendet. Führen Sie den folgenden Befehl in Ihrem Arbeitsverzeichnis aus:

```console
gradle init --type basic
```

Wenn Sie zur Auswahl einer **DSL** aufgefordert werden, wählen Sie **Kotlin** aus.

## <a name="configure-the-build-file"></a>Konfigurieren der Builddatei

Navigieren Sie zu `build.gradle.kts`, und öffnen Sie die Datei in einer IDE oder einem Text-Editor Ihrer Wahl. Kopieren Sie anschließend diese Buildkonfiguration:

```
plugins {
    java
    application
}
application {
    mainClassName = "Translate"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Beachten Sie, dass dieses Beispiel Abhängigkeiten von OkHttp für HTTP-Anforderungen und von Gson für das Bearbeiten und Analysieren von JSON aufweist. Weitere Informationen zu Buildkonfigurationen finden Sie unter [Creating New Gradle Builds](https://guides.gradle.org/creating-new-gradle-builds/) (Erstellen neuer Gradle-Builds).

## <a name="create-a-java-file"></a>Erstellen einer Java-Datei

Erstellen Sie einen Ordner für Ihre Beispiel-App. Führen Sie in Ihrem Arbeitsverzeichnis den folgenden Befehl aus:

```console
mkdir -p src/main/java
```

Erstellen Sie als Nächstes in diesem Ordner eine Datei namens `Translate.java`.

## <a name="import-required-libraries"></a>Importieren der erforderlichen Bibliotheken

Öffnen Sie `Translate.java`, und fügen Sie die folgenden Importanweisungen hinzu:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```

## <a name="define-variables"></a>Definieren von Variablen

Sie müssen zunächst eine öffentliche Klasse für Ihr Projekt erstellen:

```java
public class Translate {
  // All project code goes here...
}
```

Fügen Sie der Klasse `Translate` die folgenden Zeilen hinzu. Sie sehen, dass neben `api-version` zwei weitere Parameter an `url` angefügt wurden. Diese Parameter werden zum Festlegen der Übersetzungsausgaben verwendet. In diesem Beispiel sind sie auf Deutsch (`de`) und Italienisch (`it`) festgelegt. Aktualisieren Sie unbedingt den Wert des Abonnementschlüssels.

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=de,it";
```

## <a name="create-a-client-and-build-a-request"></a>Erstellen eines Clients und einer Anforderung

Fügen Sie der Klasse `Translate` die folgende Zeile hinzu, um `OkHttpClient` zu instanziieren:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Als Nächstes erstellen Sie die POST-Anforderung. Sie können den Text für die Übersetzung ändern. Der Text muss mit Escapezeichen versehen werden.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Welcome to Microsoft Translator. Guess how many languages I speak!\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Erstellen einer Funktion zum Analysieren der Antwort

Mit dieser einfachen Funktion wird die JSON-Antwort vom Textübersetzungsdienst analysiert und übersichtlicher gemacht.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Korrektes Zusammenfügen

Im letzten Schritt wird eine Anforderung gesendet und eine Antwort empfangen. Fügen Sie Ihrem Projekt die folgenden Zeilen hinzu:

```java
public static void main(String[] args) {
    try {
        Translate translateRequest = new Translate();
        String response = translateRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Ausführen der Beispiel-App

Sie können Ihre Beispiel-App jetzt ausführen. Navigieren Sie über die Befehlszeile (oder Terminalsitzung) zum Stamm Ihres Projektverzeichnisses, und führen Sie Folgendes aus:

```console
gradle build
```

Führen Sie nach Abschluss des Buildvorgangs Folgendes aus:

```console
gradle run
```

## <a name="sample-response"></a>Beispiel für eine Antwort

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Willkommen bei Microsoft Translator. Erraten Sie, wie viele Sprachen ich spreche!",
        "to": "de"
      },
      {
        "text": "Benvenuti a Microsoft Translator. Indovinate quante lingue parlo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den Beispielcode für diese und andere Schnellstartanleitungen (einschließlich Transliteration und Sprachermittlung) sowie weitere Beispielprojekte für die Textübersetzung auf GitHub an.

> [!div class="nextstepaction"]
> [Java-Beispiele auf GitHub](https://aka.ms/TranslatorGitHub?type=&language=java)

## <a name="see-also"></a>Weitere Informationen

* [Transliteration von Text](quickstart-java-transliterate.md)
* [Identifizieren der Sprache anhand der Eingabe](quickstart-java-detect.md)
* [Ermitteln alternativer Übersetzungen](quickstart-java-dictionary.md)
* [Abrufen einer Liste der unterstützten Sprachen](quickstart-java-languages.md)
* [Bestimmen der Satzlänge aus einer Eingabe](quickstart-java-sentences.md)
