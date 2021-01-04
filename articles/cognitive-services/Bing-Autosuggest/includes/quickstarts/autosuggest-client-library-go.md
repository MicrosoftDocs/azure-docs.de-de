---
title: 'Clientbibliothek für die Bing-Vorschlagssuche für Go: Schnellstart'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2020
ms.author: aahi
ms.openlocfilehash: 8c0715b3570bf60205c83390ab93b272e49e8733
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371827"
---
Beginnen Sie mit dem Einsatz der Clientbibliothek für die Bing-Vorschlagssuche für Go. Führen Sie die nachfolgenden Schritte aus, um die Bibliothek zu installieren und unsere Beispiele für grundlegende Aufgaben zu testen.

Verwenden Sie die Clientbibliothek für die Bing-Vorschlagssuche für Go, um Suchvorschläge basierend auf Abfragen mit Teilzeichenfolgen zu erhalten.

[Referenzdokumentation](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Beispielcode](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie noch nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/cognitive-services).
* Aktuelle Version von [Go](https://golang.org/dl/).

Beginnen Sie mit der Verwendung der Clientbibliothek für die Bing-Vorschlagssuche, indem Sie eine Azure-Ressource erstellen. Wählen Sie den geeigneten Ressourcentyp aus:

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-environment-variables"></a>Erstellen von Umgebungsvariablen

>[!NOTE]
> Die nach dem 1. Juli 2019 erstellten Endpunkte für Ressourcen nutzen das unten gezeigte benutzerdefinierte Format für Subdomänen. Weitere Informationen und eine vollständige Liste mit regionalen Endpunkten finden Sie unter [Benutzerdefinierte Unterdomänennamen für Cognitive Services](../../../cognitive-services-custom-subdomains.md).

Erstellen Sie unter Verwendung des Schlüssels und des Endpunkts der von Ihnen erstellten Ressource zwei Umgebungsvariablen für die Authentifizierung:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`: Der Ressourcenschlüssel zum Authentifizieren Ihrer Anforderungen.
* `AUTOSUGGEST_ENDPOINT`: Der Ressourcenendpunkt zum Senden von API-Anforderungen. Dieser sollte wie folgt aussehen: `https://<your-custom-subdomain>.api.cognitive.microsoft.com`

Führen Sie die Schritte für Ihr Betriebssystem aus:
<!-- replace the below endpoint and key examples -->
### <a name="windows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Starten Sie das Konsolenfenster neu, nachdem Sie die Umgebungsvariable hinzugefügt haben.

### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Führen Sie nach dem Hinzufügen der Umgebungsvariablen im Konsolenfenster `source ~/.bashrc` aus, damit die Änderungen wirksam werden.

### <a name="macos"></a>[macOS](#tab/unix)

Bearbeiten Sie `.bash_profile`, und fügen Sie die Umgebungsvariable hinzu:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Führen Sie nach dem Hinzufügen der Umgebungsvariablen im Konsolenfenster `source .bash_profile` aus, damit die Änderungen wirksam werden.
***

## <a name="create-a-new-go-project"></a>Erstellen eines neuen Go-Projekts

Erstellen Sie in einem Konsolenfenster (cmd, PowerShell, Terminal, Bash) einen neuen Arbeitsbereich für Ihr Go-Projekt, und navigieren Sie zu diesem Projekt. Ihr Arbeitsbereich enthält drei Ordner:

* **src** : Dieses Verzeichnis enthält Quellcode und Pakete. Alle Pakete, die mit dem Befehl `go get` installiert werden, befinden sich in diesem Verzeichnis.
* **pkg** : Dieses Verzeichnis enthält die kompilierten Go-Paketobjekte. Diese Dateien haben alle die Erweiterung `.a`.
* **bin** : Dieses Verzeichnis enthält die ausführbaren Binärdateien, die beim Ausführen von `go install` erstellt werden.

> [!TIP]
> Informieren Sie sich über die Struktur eines [Go-Arbeitsbereichs](https://golang.org/doc/code.html#Workspaces). Dieses Handbuch enthält Informationen zum Festlegen von `$GOPATH` und `$GOROOT`.

Erstellen Sie jetzt einen Arbeitsbereich mit dem Namen `my-app` und den erforderlichen Unterverzeichnissen für `src`, `pkg` und `bin`:

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

## <a name="install-the-client-library-for-go"></a>Installieren der Clientbibliothek für Go

Installieren Sie als Nächstes die Clientbibliothek für Go:

```bash
$ go get -u <library-location-or-url>
```

Bei Verwendung von DEP können Sie alternativ Folgendes in Ihrem Repository ausführen:

```bash
$ dep ensure -add <library-location-or-url>
```

## <a name="create-your-go-application"></a>Erstellen Ihrer Go-Anwendung

Jetzt erstellen Sie eine Datei namens `src/sample-app.go`:

```bash
$ cd src
$ touch sample-app.go
```

Öffnen Sie `sample-app.go`, fügen Sie den Paketnamen hinzu, und importieren Sie dann die folgenden Bibliotheken:

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

Erstellen Sie eine Funktion mit dem Namen `main`. Erstellen Sie anschließend die Umgebungsvariablen für den Schlüssel und den Endpunkt der Bing-Vorschlagssuche:

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>Codebeispiele

Diese Codebeispiele veranschaulichen, wie Sie mithilfe der Clientbibliothek der Bing-Vorschlagssuche für Go grundlegende Aufgaben ausführen:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Senden einer API-Anforderung](#send-an-api-request)

### <a name="authenticate-the-client"></a>Authentifizieren des Clients

> [!NOTE]
> In dieser Schnellstartanleitung wird vorausgesetzt, dass Sie eine [Umgebungsvariable](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) namens `BING_AUTOSUGGEST_SUBSCRIPTION_KEY` für Ihren Schlüssel für die Bing-Vorschlagssuche sowie eine weitere Umgebungsvariable namens `BING_AUTOSUGGEST_ENDPOINT` für Ihren Endpunkt erstellt haben.

Instanziieren Sie in der `main()`-Funktion einen Client mit Ihrem Endpunkt und Schlüssel.

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

### <a name="send-an-api-request"></a>Senden einer API-Anforderung

Verwenden Sie in derselben Methode die [AutoSuggestMethodAsync](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_)-Methode des Clients, um eine Abfrage an Bing zu senden. Durchlaufen Sie dann die [Suggestions](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet)-Antwort, und geben Sie den ersten Vorschlag aus.

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Go-Anwendung mit dem Befehl `go run [arguments]` aus dem Anwendungsverzeichnis aus.

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Löschen einer Ressourcengruppe im Azure-Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Löschen einer Ressourcengruppe über die Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial für die Bing-Vorschlagssuche](../../tutorials/autosuggest.md)

## <a name="see-also"></a>Weitere Informationen

- [Worum handelt es sich bei der Bing-Vorschlagssuche?](../../get-suggested-search-terms.md)
- [Referenz für die Bing-Vorschlagssuche-API V7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)