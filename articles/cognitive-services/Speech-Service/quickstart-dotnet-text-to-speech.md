---
title: 'Schnellstart: Konvertieren von Text in Sprache, .NET Core – Speech Services'
titleSuffix: Azure Cognitive Services
description: In diesem Schnellstart erfahren Sie, wie Sie mit der Text-to-Speech-REST-API Text in Sprache konvertieren. Der Beispieltext in diesem Leitfaden ist als SSML (Speech Synthesis Markup Language) strukturiert. Auf diese Weise können Sie die Stimme und Sprache für die Sprachantwort auswählen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 03/13/2019
ms.author: erhopf
ms.openlocfilehash: 5db2a1eebd5b2184ceff8793cb51f4e95b9dea38
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067585"
---
# <a name="quickstart-convert-text-to-speech-using-net-core"></a>Schnellstart: Konvertieren von Text in Sprache mit .NET Core

In diesem Schnellstart erfahren Sie, wie Sie mithilfe von .NET Core und der Text-to-Speech-REST-API Text in Sprache konvertieren. Der in diesem Leitfaden enthaltene Text ist als [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md) strukturiert, wodurch Sie die Stimme und die Sprache für die Sprachantwort auswählen können.

Für diese Schnellstartanleitung wird ein [Azure Cognitive Services-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) mit einer Ressource für den Speech-Dienst benötigt. Wenn Sie über kein Konto verfügen, können Sie über die [kostenlose Testversion](https://azure.microsoft.com/try/cognitive-services/) einen Abonnementschlüssel abrufen.

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung ist Folgendes erforderlich:

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) oder ein anderer Editor
* Ein Azure-Abonnementschlüssel für den Speech-Dienst

## <a name="create-a-net-core-project"></a>Erstellen eines .NET Core-Projekts

Öffnen Sie eine neue Eingabeaufforderung (oder Terminalsitzung), und führen Sie die folgenden Befehle aus:

```console
dotnet new console -o tts-sample
cd tts-sample
```

Mit dem ersten Befehl werden zwei Aufgaben ausgeführt. Es werden eine neue .NET-Konsolenanwendung und ein Verzeichnis mit dem Namen `tts-sample` erstellt. Mit dem zweiten Befehl wird in das Verzeichnis für Ihr Projekt gewechselt.

## <a name="select-the-c-language-version"></a>Auswählen der C#-Sprachversion

Für diese Schnellstartanleitung wird C# 7.1 oder höher benötigt. Es gibt verschiedene Möglichkeiten, die C#-Version für Ihr Projekt zu ändern. In diesem Leitfaden wird gezeigt, wie Sie die Datei `tts-sample.csproj` anpassen. Alle verfügbaren Optionen, z.B. das Ändern der Sprache in Visual Studio, finden Sie unter [Auswählen der C#-Sprachversion](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Öffnen Sie Ihr Projekt und anschließend `tts-sample.csproj`. Stellen Sie sicher, dass `LangVersion` auf 7.1 oder höher festgelegt ist. Wenn keine Eigenschaftengruppe für die Sprachversion vorhanden ist, fügen Sie diese Zeilen hinzu:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

Speichern Sie Ihre Änderungen.

## <a name="add-required-namespaces-to-your-project"></a>Hinzufügen von erforderlichen Namespaces zu Ihrem Projekt

Mit dem zuvor ausgeführten Befehl `dotnet new console` wurde ein Projekt (einschließlich `Program.cs`) erstellt. In diese Datei fügen Sie Ihren Anwendungscode ein. Öffnen Sie `Program.cs`, und ersetzen Sie die vorhandenen using-Anweisungen. Mit diesen Anweisungen wird sichergestellt, dass Sie Zugriff auf alle Typen haben, die zum Erstellen und Ausführen der Beispiel-App erforderlich sind.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.IO;
using System.Threading.Tasks;
```

## <a name="create-a-class-for-token-exchange"></a>Erstellen einer Klasse für den Tokenaustausch

Alle Anforderungen an die Text-to-Speech-REST-API erfordern ein Zugriffstoken für die Authentifizierung. Um ein Zugriffstoken anzufordern, ist ein Austausch erforderlich. In diesem Beispiel wird Ihr Abonnementschlüssel für den Speech-Dienst unter Verwendung des Endpunkts `issueToken` gegen ein Zugriffstoken ausgetauscht.

In diesem Beispiel wird vorausgesetzt, dass Ihr Abonnement für den Speech-Dienst in der Region „USA, Westen“ enthalten ist. Wenn Sie eine andere Region verwenden, aktualisieren Sie den Wert für `FetchTokenUri`. Eine vollständige Liste finden Sie unter [Regionen](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

```csharp
public class Authentication
{
    private string subscriptionKey;
    private string tokenFetchUri;

    public Authentication(string tokenFetchUri, string subscriptionKey)
    {
        if (string.IsNullOrWhiteSpace(tokenFetchUri))
        {
            throw new ArgumentNullException(nameof(tokenFetchUri));
        }
        if (string.IsNullOrWhiteSpace(subscriptionKey))
        {
            throw new ArgumentNullException(nameof(subscriptionKey));
        }
        this.tokenFetchUri = tokenFetchUri;
        this.subscriptionKey = subscriptionKey;
    }

    public async Task<string> FetchTokenAsync()
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(this.tokenFetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null).ConfigureAwait(false);
            return await result.Content.ReadAsStringAsync().ConfigureAwait(false);
        }
    }
}
```

> [!NOTE]
> Weitere Informationen zur Authentifizierung finden Sie unter [Authentifizieren mit einem Authentifizierungstoken](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="get-an-access-token-and-set-the-host-url"></a>Abrufen eines Zugriffstokens und Festlegen der Host-URL

Suchen Sie nach`static void Main(string[] args)`, und ersetzen Sie es durch `static async Task Main(string[] args)`.

Kopieren Sie diesen Code anschließend in die main-Methode. Dieser Code führt verschiedene Aufgaben aus, aber vor allem verwendet er Text als Eingabe und ruft die Funktion `Authentication` auf, um Ihren Abonnementschlüssel gegen ein Zugriffstoken auszutauschen. Wenn ein Fehler auftritt, wird dieser in der Konsole ausgegeben.

Stellen Sie sicher, dass Sie Ihren Abonnementschlüssel hinzuzufügen, bevor Sie die App ausführen.

```csharp
// Prompts the user to input text for TTS conversion
Console.Write("What would you like to convert to speech? ");
string text = Console.ReadLine();

// Gets an access token
string accessToken;
Console.WriteLine("Attempting token exchange. Please wait...\n");

// Add your subscription key here
// If your resource isn't in WEST US, change the endpoint
Authentication auth = new Authentication("https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken", "YOUR_SUBSCRIPTION_KEY");
try
{
    accessToken = await auth.FetchTokenAsync().ConfigureAwait(false);
    Console.WriteLine("Successfully obtained an access token. \n");
}
catch (Exception ex)
{
    Console.WriteLine("Failed to obtain an access token.");
    Console.WriteLine(ex.ToString());
    Console.WriteLine(ex.Message);
    return;
}
```

Legen Sie dann den Host und die Route für die Konvertierung von Text in Sprache fest:

```csharp
string host = "https://westus.tts.speech.microsoft.com/cognitiveservices/v1";
```

## <a name="build-the-ssml-request"></a>Erstellen der SSML-Anforderung

Text wird im Körper einer `POST`-Anforderung gesendet. Mit SSML können Sie die Stimme und die Sprache angeben. In diesem Schnellstart verwenden wir SSML, um die Sprache auf `en-US` und die Stimme auf `ZiraRUS` festzulegen. Erstellen wir jetzt den SSML-Code für Ihre Anforderung:

```csharp
string body = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
              <voice name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>" +
              text + "</voice></speak>";
```

> [!NOTE]
> In diesem Beispiel wird der Voicefont `ZiraRUS` verwendet. Eine vollständige Liste der von Microsoft bereitgestellten Stimmen/Sprachen finden Sie unter [Sprachunterstützung](language-support.md). Wenn Sie daran interessiert sind, eine eigene Stimme mit Wiedererkennungswert für Ihre Marke zu erstellen, finden Sie unter [Erstellen von benutzerdefinierten Voicefonts](how-to-customize-voice-font.md) weitere Informationen.

## <a name="instantiate-the-client-make-a-request-and-save-synthesized-audio-to-a-file"></a>Instanziieren des Clients, Erstellen einer Anforderung und Speichern der synthetisierten Audiodaten in einer Datei

In diesem Codebeispiel werden zahlreiche verschiedene Aufgaben ausgeführt. Schauen wir uns kurz an, was im Einzelnen passiert:

* Der Client und die Anforderung werden instanziiert.
* Die HTTP-Methode ist auf `POST` festgelegt.
* Erforderliche Header werden zur Anforderung hinzugefügt.
* Die Anforderung wird gesendet, und der Statuscode wird überprüft.
* Die Antwort wird asynchron eingelesen und in eine Datei namens „sample.wav“ geschrieben.

Kopieren Sie diesen Code in Ihr Projekt. Stellen Sie sicher, den Wert des `User-Agent`-Headers durch den Namen Ihrer Ressource aus dem Azure-Portal zu ersetzen.

```csharp
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Set the HTTP method
        request.Method = HttpMethod.Post;
        // Construct the URI
        request.RequestUri = new Uri(host);
        // Set the content type header
        request.Content = new StringContent(body, Encoding.UTF8, "application/ssml+xml");
        // Set additional header, such as Authorization and User-Agent
        request.Headers.Add("Authorization", "Bearer " + accessToken);
        request.Headers.Add("Connection", "Keep-Alive");
        // Update your resource name
        request.Headers.Add("User-Agent", "YOUR_RESOURCE_NAME");
        request.Headers.Add("X-Microsoft-OutputFormat", "riff-24khz-16bit-mono-pcm");
        // Create a request
        Console.WriteLine("Calling the TTS service. Please wait... \n");
        using (var response = await client.SendAsync(request).ConfigureAwait(false))
        {
            response.EnsureSuccessStatusCode();
            // Asynchronously read the response
            using (var dataStream = await response.Content.ReadAsStreamAsync().ConfigureAwait(false))
            {
                Console.WriteLine("Your speech file is being written to file...");
                using (var fileStream = new FileStream(@"sample.wav", FileMode.Create, FileAccess.Write, FileShare.Write))
                {
                    await dataStream.CopyToAsync(fileStream).ConfigureAwait(false);
                    fileStream.Close();
                }
                Console.WriteLine("\nYour file is ready. Press any key to exit.");
                Console.ReadLine();
            }
        }
    }
}
```

## <a name="run-the-sample-app"></a>Ausführen der Beispiel-App

Fertig! Sie können Ihre Beispiel-App jetzt ausführen. Navigieren Sie über die Befehlszeile (oder Terminalsitzung) zu Ihrem Projektverzeichnis, und führen Sie Folgendes aus:

```console
dotnet run
```

Bei erfolgreicher Ausführung wird die Sprachdatei in Ihrem Projektordner gespeichert. Geben Sie die Sprachdatei mit Ihrem bevorzugten Medienplayer wieder.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Abonnementschlüssel in Ihrem Programm hartcodiert haben, entfernen Sie ihn unbedingt, wenn Sie diese Schnellstartanleitung abgeschlossen haben.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [-NET-Beispiele auf GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NETCore)

## <a name="see-also"></a>Weitere Informationen

* [Referenz zur Text-to-Speech-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Erstellen benutzerdefinierter Voicefonts](how-to-customize-voice-font.md)
* [Aufzeichnen von Sprachbeispielen zum Erstellen einer benutzerdefinierten Stimme](record-custom-voice-samples.md)
