---
title: 'Schnellstart: Java-Clientbibliothek für die visuelle Bing-Suche'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.custom: devx-track-java, devx-track-csharp
ms.author: aahi
ms.openlocfilehash: afac19a6debb3804c99492338428669928f1118b
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371619"
---
Verwenden Sie diese Schnellstartanleitung, um über den Dienst für die visuelle Bing-Suche mithilfe der Java-Clientbibliothek Erkenntnisse zu Bildern zu gewinnen. Die visuelle Bing-Suche verfügt zwar über eine REST-API, die mit den meisten Programmiersprachen kompatibel ist, die Clientbibliothek bietet jedoch eine einfache Möglichkeit, um den Dienst in Ihre Anwendungen zu integrieren. Den Quellcode für diese Schnellstartanleitung finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVisualSearch).

Verwenden Sie die Clientbibliothek der visuellen Bing-Suche für Java für Folgendes:

* Hochladen eines Bilds zum Senden einer Anforderung für die visuelle Suche
* Abrufen von Bildauswertungstoken und Tags für die visuelle Suche

[Referenzdokumentation](/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch) | [Artefakt (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) | [Beispiele](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Aktuelle Version des [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle-Buildtool](https://gradle.org/install/) oder ein anderer Abhängigkeits-Manager

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

Nachdem Sie einen Schlüssel von ihrer Ressource erhalten haben, [erstellen Sie eine Umgebungsvariable](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) für den Schlüssel namens `BING_SEARCH_V7_SUBSCRIPTION_KEY`.

### <a name="create-a-new-gradle-project"></a>Erstellen eines neuen Gradle-Projekts

Erstellen Sie in einem Konsolenfenster (etwa cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App, und rufen Sie es auf. 

```console
mkdir myapp && cd myapp
```

Führen Sie den Befehl `gradle init` in Ihrem Arbeitsverzeichnis aus. Mit diesem Befehl werden grundlegende Builddateien für Gradle erstellt, u. a. die Datei *build.gradle.kts*. Diese Datei wird zur Laufzeit zum Erstellen und Konfigurieren Ihrer Anwendung verwendet.

```console
gradle init --type basic
```

Wenn Sie zur Auswahl einer **DSL** aufgefordert werden, wählen Sie **Kotlin** aus.

Navigieren Sie zur Datei *build.gradle.kts* , und öffnen Sie sie in Ihrer bevorzugten IDE bzw. Ihrem bevorzugten Text-Editor. Kopieren Sie anschließend diese Buildkonfiguration:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingVisualSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Erstellen Sie einen Ordner für Ihre Beispiel-App. Führen Sie in Ihrem Arbeitsverzeichnis den folgenden Befehl aus:

```console
mkdir -p src/main/java
```

Erstellen Sie einen Ordner für das Bild, das Sie in die API hochladen möchten. Platzieren Sie das Bild im Ordner **Ressourcen**.

```console
mkdir -p src/main/resources
``` 

Navigieren Sie zum neuen Ordner, und erstellen Sie eine Datei mit dem Namen *BingVisualSearchSample.java*. Öffnen Sie sie in Ihrem bevorzugten Editor bzw. Ihrer bevorzugten IDE, und fügen Sie die folgenden `import`-Anweisungen hinzu:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Erstellen Sie anschließend eine neue Klasse.

```java
public class BingVisualSearchSample {
}
```

Erstellen Sie in der Methode `main` der Anwendung Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource. Wenn Sie die Umgebungsvariable nach dem Start der Anwendung erstellt haben, müssen der Editor, die IDE oder die Shell, in dem bzw. der sie ausgeführt wird, geschlossen und erneut geöffnet werden, damit der Zugriff auf die Variable möglich ist. Erstellen Sie dann ein `byte[]`-Element für das Bild, das Sie hochladen möchten. Erstellen Sie einen `try`-Block für die Methoden, die Sie später definieren, laden Sie das Bild hoch, und konvertieren Sie es mithilfe von `toByteArray()` in Bytes.

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

In dieser Schnellstartanleitung wird der Gradle-Abhängigkeits-Manager verwendet. Die Clientbibliothek und Informationen zu anderen Abhängigkeits-Managern finden Sie im [zentralen Maven-Repository](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

Fügen Sie in die Datei *build.gradle.kts* Ihres Projekts die Clientbibliothek der Maschinelles Sehen-API als `implementation`-Anweisung ein. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der Clientbibliothek der visuellen Bing-Suche und Java ausgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Senden einer Anforderung für die visuelle Suche](#send-a-visual-search-request)
* [Ausgeben von Bildauswertungstoken und Tags für die visuelle Suche](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

> [!NOTE]
> In dieser Schnellstartanleitung wird vorausgesetzt, dass Sie für den Schlüssel für die visuelle Bing-Suche mit dem Namen `BING_SEARCH_V7_SUBSCRIPTION_KEY`[eine Umgebungsvariable erstellt haben](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication).


Verwenden Sie in der Main-Methode unbedingt Ihren Abonnementschlüssel, um ein [BingVisualSearchAPI](/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable)-Objekt zu instanziieren.

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Senden einer Anforderung für die visuelle Suche

Senden Sie in einer neuen Methode das (in der `main()`-Methode erstellte) Bytearray für Bilder. Verwenden Sie dazu die Methode [bingImages().visualSearch()](/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) des Clients. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>Ausgeben von Bildauswertungstoken und Tags für die visuelle Suche

Überprüfen Sie, ob das Objekt [ImageKnowledge](/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) NULL ist. Falls nicht, geben Sie das Bildauswertungstoken, die Anzahl von Tags, die Anzahl von Aktionen und den ersten Aktionstyp aus.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>Ausführen der Anwendung

Sie können die App mit folgendem Befehl erstellen:

```console
gradle build
```

Führen Sie die Anwendung mit dem Ziel `run` aus:

```console
gradle run
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Einzelseiten-Web-App](../../tutorial-bing-visual-search-single-page-app.md)