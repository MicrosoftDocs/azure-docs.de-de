---
title: 'Schnellstart: Sprachübersetzungs-API (C#)'
titlesuffix: Azure Cognitive Services
description: Hier erhalten Sie Informationen und Codebeispiele für einen schnellen Einstieg in die Sprachübersetzungs-API.
services: cognitive-services
author: v-jaswel
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: quickstart
ms.date: 04/26/2019
ms.author: v-jaswel
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: b4fe4f651340ef54c2907192f3a96e9a017ab1dd
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65910512"
---
# <a name="quickstart-translator-speech-api-with-c"></a>Schnellstart: Sprachübersetzungs-API mit C#
<a name="HOLTop"></a>

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

In diesem Artikel wird gezeigt, wie Sie mit der Sprachübersetzungs-API gesprochene Sprache in einer WAV-Datei übersetzen.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen [Visual Studio 2019](https://www.visualstudio.com/downloads/), um diesen Code unter Windows ausführen zu können. (Die kostenlose Community Edition ist hierfür geeignet.) Unter Mac OS oder Linux können Sie alternativ auch den Text-Editor ([Visual Studio Code](https://code.visualstudio.com/Download)) verwenden.

Sie benötigen eine WAV-Datei namens „speak.wav“, die im gleichen Ordner gespeichert ist wie die ausführbare Datei, die Sie aus dem unten angegebenen Code kompilieren. Diese WAV-Datei muss im PCM-Standardformat mit 16 Bit, 16 kHz und in mono vorliegen.

Sie müssen über ein [Cognitive Services-API-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) mit **Microsoft-Sprachübersetzungs-API** verfügen. Sie benötigen einen kostenpflichtigen Abonnementschlüssel aus Ihrem [Azure-Dashboard](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Übersetzen von gesprochener Sprache

Mit dem folgenden Code wird gesprochene Sprache von einer Sprache in eine andere übersetzt.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues C#-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```csharp
using System;
using System.IO;
using System.Net.WebSockets;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace TranslateSpeechQuickStart
{
    class Program
    {
        static string host = "wss://dev.microsofttranslator.com";
        static string path = "/speech/translate";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        async static Task Send (ClientWebSocket client, string input_path)
        {
            var audio = File.ReadAllBytes(input_path);
            var audio_out_buffer = new ArraySegment<byte>(audio);
            Console.WriteLine("Sending audio.");
            await client.SendAsync(audio_out_buffer, WebSocketMessageType.Binary, true, CancellationToken.None);

            /* Make sure the audio file is followed by silence.
             * This lets the service know that the audio input is finished. */
            var silence = new byte[32000];
            var silence_buffer = new ArraySegment<byte>(silence);
            await client.SendAsync(silence_buffer, WebSocketMessageType.Binary, true, CancellationToken.None);

            Console.WriteLine("Done sending.");
            System.Threading.Thread.Sleep(3000);
            await client.CloseAsync(WebSocketCloseStatus.NormalClosure, "", CancellationToken.None);
        }

        async static Task Receive(ClientWebSocket client, string output_path)
        {
            var inbuf = new byte[102400];
            var segment = new ArraySegment<byte>(inbuf);
            var stream = new FileStream(output_path, FileMode.Create);

            Console.WriteLine("Awaiting response.");
            while (client.State == WebSocketState.Open)
            {
                var result = await client.ReceiveAsync(segment, CancellationToken.None);
                switch (result.MessageType)
                {
                    case WebSocketMessageType.Close:
                        Console.WriteLine("Received close message. Status: " + result.CloseStatus + ". Description: " + result.CloseStatusDescription);
                        await client.CloseAsync(WebSocketCloseStatus.NormalClosure, string.Empty, CancellationToken.None);
                        break;
                    case WebSocketMessageType.Text:
                        Console.WriteLine("Received text.");
                        Console.WriteLine(Encoding.UTF8.GetString(inbuf).TrimEnd('\0'));
                        break;
                    case WebSocketMessageType.Binary:
                        Console.WriteLine("Received binary data: " + result.Count + " bytes.");
                        stream.Write(inbuf, 0, result.Count);
                        break;
                }
            }

            stream.Close();
            stream.Dispose();
        }

        async static void TranslateSpeech()
        {
            var client = new ClientWebSocket();
            client.Options.SetRequestHeader ("Ocp-Apim-Subscription-Key", key);

            string from = "en-US";
            string to = "it-IT";
            string features = "texttospeech";
            string voice = "it-IT-Elsa";
            string api = "1.0";

            string input_path = "speak.wav";
            string output_path = "speak2.wav";

            string uri = host + path +
                "?from=" + from +
                "&to=" + to +
                "&api-version=" + api +
                "&features=" + features +
                "&voice=" + voice;

            Console.WriteLine("uri: " + uri);
            Console.WriteLine("Opening connection.");
            await client.ConnectAsync(new Uri(uri), CancellationToken.None);
            Console.WriteLine("Connection open.");
            Task.WhenAll(Send(client, input_path), Receive(client, output_path)).Wait();
        }

        static void Main()
        {
            TranslateSpeech();
            Console.ReadLine();
        }

    }
}
```

**Ergebnis der Sprachübersetzung**

Bei erfolgreicher Ausführung wird eine Datei namens „speak2.wav“ erstellt. Die Datei enthält die Übersetzung der in „speak.wav“ gesprochenen Sprache.

[Nach oben](#HOLTop)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial zur Sprachübersetzung](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Weitere Informationen

[Übersicht über die Sprachübersetzungs-API](../overview.md)
[API-Referenz](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
