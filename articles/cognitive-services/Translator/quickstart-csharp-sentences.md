---
title: 'Schnellstart: Abrufen von Satzlängen, C# – Textübersetzungs-API'
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie mit .NET Core und der Textübersetzungs-API die Satzlänge bestimmen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: e8940de90b925a1ca252de3cf75acd192531edeb
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514214"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-c"></a>Schnellstart: Verwenden der Textübersetzungs-API zum Ermitteln der Satzlänge mit C#

In dieser Schnellstartanleitung wird beschrieben, wie Sie mit .NET Core und der Textübersetzungs-API Satzlängen bestimmen.

Für diese Schnellstartanleitung wird ein [Azure Cognitive Services-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) mit einer Textübersetzungsressource benötigt. Wenn Sie über kein Konto verfügen, können Sie über die [kostenlose Testversion](https://azure.microsoft.com/try/cognitive-services/) einen Abonnementschlüssel abrufen.

## <a name="prerequisites"></a>Voraussetzungen

* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET-NuGet-Paket](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) oder ein anderer Editor
* Ein Azure-Abonnementschlüssel für die Textübersetzung

## <a name="create-a-net-core-project"></a>Erstellen eines .NET Core-Projekts

Öffnen Sie eine neue Eingabeaufforderung (oder Terminalsitzung), und führen Sie die folgenden Befehle aus:

```console
dotnet new console -o sentences-sample
cd sentences-sample
```

Mit dem ersten Befehl werden zwei Aufgaben ausgeführt. Es werden eine neue .NET-Konsolenanwendung und ein Verzeichnis mit dem Namen `sentences-sample` erstellt. Mit dem zweiten Befehl wird in das Verzeichnis für Ihr Projekt gewechselt.

Als Nächstes müssen Sie Json.Net installieren. Führen Sie im Verzeichnis Ihres Projekts den folgenden Befehl aus:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Hinzufügen von erforderlichen Namespaces zu Ihrem Projekt

Mit dem zuvor ausgeführten Befehl `dotnet new console` wurde ein Projekt (einschließlich `Program.cs`) erstellt. In diese Datei fügen Sie Ihren Anwendungscode ein. Öffnen Sie `Program.cs`, und ersetzen Sie die vorhandenen using-Anweisungen. Mit diesen Anweisungen wird sichergestellt, dass Sie Zugriff auf alle Typen haben, die zum Erstellen und Ausführen der Beispiel-App erforderlich sind.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="create-a-function-to-determine-sentence-length"></a>Erstellen einer Funktion zum Ermitteln der Satzlänge

Erstellen Sie in der `Program`-Klasse eine Funktion mit dem Namen `BreakSentence`. Diese Klasse kapselt den zum Aufrufen der BreakSentence-Ressource verwendeten Code und gibt das Ergebnis in der Konsole aus.

```csharp
static void BreakSentence()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-subscription-key-host-name-and-path"></a>Festlegen des Abonnementschlüssels, des Hostnamens und des Pfads

Fügen Sie der Funktion `BreakSentence` die folgenden Zeilen hinzu: Zusätzlich zu `api-version` können Sie die Eingabesprache definieren. In diesem Beispiel ist die Eingabesprache Englisch.

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/breaksentence?api-version=3.0&language=en";
string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
```

Als Nächstes müssen Sie das JSON-Objekt erstellen und serialisieren, das den Text enthält. Beachten Sie, dass Sie mehrere Objekte im Array `body` übergeben können.

```csharp
System.Object[] body = new System.Object[] { new { Text = @"How are you? I am fine. What did you do today?" } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Instanziieren des Clients und Senden einer Anforderung

Diese Zeilen instanziieren `HttpClient` und `HttpRequestMessage`:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Erstellen der Anforderung und Ausgeben der Antwort

In `HttpRequestMessage` führen Sie die folgenden Aufgaben aus:

* Deklarieren der HTTP-Methode
* Erstellen des Anforderungs-URIs
* Einfügen des Anforderungstexts (serialisiertes JSON-Objekt)
* Hinzufügen der erforderlichen Header
* Senden einer asynchronen Anforderung
* Ausgeben der Antwort

Fügen Sie `HttpRequestMessage` den folgenden Code hinzu:

```csharp
// Set the method to POST
request.Method = HttpMethod.Post;

// Construct the full URI
request.RequestUri = new Uri(host + route);

// Add the serialized JSON object to your request
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");

// Add the authorization header
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;

// Print the response
Console.WriteLine(jsonResponse);
Console.WriteLine("Press any key to continue.");
```

## <a name="put-it-all-together"></a>Korrektes Zusammenfügen

Im letzten Schritt rufen Sie `BreakSentence()` in der `Main`-Funktion auf. Suchen Sie `static void Main(string[] args)`, und fügen Sie die folgenden Zeilen hinzu:

```csharp
BreakSentence();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>Ausführen der Beispiel-App

Sie können Ihre Beispiel-App jetzt ausführen. Navigieren Sie über die Befehlszeile (oder Terminalsitzung) zu Ihrem Projektverzeichnis, und führen Sie Folgendes aus:

```console
dotnet run
```

## <a name="sample-response"></a>Beispiel für eine Antwort

```json
[
    {
        "sentLen": [
            13,
            11,
            22
        ]
    }
]
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Entfernen Sie unbedingt alle vertraulichen Informationen wie etwa Abonnementschlüssel aus dem Quellcode Ihrer Beispiel-App.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den Beispielcode für diese und andere Schnellstartanleitungen (einschließlich Transliteration und Sprachermittlung) sowie weitere Beispielprojekte für die Textübersetzung auf GitHub an.

> [!div class="nextstepaction"]
> [C#-Beispiele auf GitHub](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>Weitere Informationen

* [Übersetzen von Text](quickstart-csharp-translate.md)
* [Transliteration von Text](quickstart-csharp-transliterate.md)
* [Identifizieren der Sprache anhand der Eingabe](quickstart-csharp-detect.md)
* [Ermitteln alternativer Übersetzungen](quickstart-csharp-dictionary.md)
* [Abrufen einer Liste der unterstützten Sprachen](quickstart-csharp-languages.md)
* [Bestimmen der Satzlänge aus einer Eingabe](quickstart-csharp-sentences.md)
