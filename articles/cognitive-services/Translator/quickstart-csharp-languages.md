---
title: 'Schnellstart: Abrufen einer Liste mit den unterstützten Sprachen, C#: Textübersetzungs-API'
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung rufen Sie mit der Textübersetzungs-API eine Liste der für Übersetzung, Transliteration und Wörterbuchsuche unterstützten Sprachen ab.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: e5e57757fbd80ca8e90b9997440050a751914a8a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514956"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-c"></a>Schnellstart: Verwenden der Textübersetzungs-API zum Abrufen einer Liste unterstützter Sprachen mit C#

In dieser Schnellstartanleitung rufen Sie mit der Textübersetzungs-API eine Liste der für Übersetzung, Transliteration und Wörterbuchsuche unterstützten Sprachen ab.

## <a name="prerequisites"></a>Voraussetzungen

* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET-NuGet-Paket](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) oder ein anderer Editor

## <a name="create-a-net-core-project"></a>Erstellen eines .NET Core-Projekts

Öffnen Sie eine neue Eingabeaufforderung (oder Terminalsitzung), und führen Sie die folgenden Befehle aus:

```console
dotnet new console -o languages-sample
cd languages-sample
```

Mit dem ersten Befehl werden zwei Aufgaben ausgeführt. Es werden eine neue .NET-Konsolenanwendung und ein Verzeichnis mit dem Namen `languages-sample` erstellt. Mit dem zweiten Befehl wird in das Verzeichnis für Ihr Projekt gewechselt.

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

## <a name="create-a-function-to-get-a-list-of-languages"></a>Erstellen einer Funktion zum Abrufen einer Liste von Sprachen

Erstellen Sie in der `Program`-Klasse eine Funktion mit dem Namen `GetLanguages`. Diese Klasse kapselt den zum Aufrufen der Sprachressource verwendeten Code und gibt das Ergebnis in der Konsole aus.

```csharp
static void GetLanguages()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-host-name-and-path"></a>Festlegen von Hostname und Pfad

Fügen Sie der Funktion `GetLanguages` die folgenden Zeilen hinzu:

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/languages?api-version=3.0";
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
* Hinzufügen der erforderlichen Header
* Senden einer asynchronen Anforderung
* Ausgeben der Antwort

Fügen Sie `HttpRequestMessage` den folgenden Code hinzu:

```csharp
// Set the method to GET
request.Method = HttpMethod.Get;
// Construct the full URI
request.RequestUri = new Uri(host + route);
// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;
// Pretty print the response
Console.WriteLine(PrettyPrint(jsonResponse));
Console.WriteLine("Press any key to continue.");
```

Um die Antwort mit „Schöndruck“ (Formatierung für die Antwort) ausgeben möchten, fügen Sie diese Funktion Ihrer „Program“-Klasse hinzu:
```
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

## <a name="put-it-all-together"></a>Korrektes Zusammenfügen

Im letzten Schritt rufen Sie `GetLanguages()` in der `Main`-Funktion auf. Suchen Sie `static void Main(string[] args)`, und fügen Sie die folgenden Zeilen hinzu:

```csharp
GetLanguages();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>Ausführen der Beispiel-App

Sie können Ihre Beispiel-App jetzt ausführen. Navigieren Sie über die Befehlszeile (oder Terminalsitzung) zu Ihrem Projektverzeichnis, und führen Sie Folgendes aus:

```console
dotnet run
```

## <a name="sample-response"></a>Beispiel für eine Antwort

Die Abkürzung für das Land bzw. die Region finden Sie in [dieser Liste der Sprachen](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
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
* [Bestimmen der Satzlänge aus einer Eingabe](quickstart-csharp-sentences.md)
