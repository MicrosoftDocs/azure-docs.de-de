---
title: 'Transkribieren von Unterhaltungen mit mehreren Teilnehmern mithilfe des Speech SDK: Speech-Dienste'
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie die Unterhaltungstranskription mit dem Speech SDK verwenden. Verfügbar für C++, C# und Java
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jhakulin
ms.openlocfilehash: 80ec606fee30c239d47bca94188d3b9cbb7c82d5
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604417"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Transkribieren von Unterhaltungen mit mehreren Teilnehmern mithilfe des Speech SDK

Mit der **ConversationTranscriber**-API des Speech SDK können Sie Besprechungen/Unterhaltungen transkribieren und dabei Teilnehmer hinzufügen, entfernen und identifizieren, indem Sie Audio mithilfe von `PullStream` oder `PushStream` an die Speech-Dienste streamen.

## <a name="limitations"></a>Einschränkungen

* Die Unterhaltungstranskription wird für C++, C# und Java unter Windows, Linux und Android unterstützt.
* Das ROOBO DevKit ist die unterstützte Hardwareumgebung für die Erstellung von Unterhaltungstranskriptionen, da mehrere Mikrofone im Kreis angeordnet sind, die effizient für die Sprecheridentifikation verwendet werden können. Weitere Informationen finden Sie unter [Informationen zum Speech-Geräte-SDK (Vorschau)](speech-devices-sdk.md).
* Die Unterstützung des Speech SDK für die Unterhaltungstranskription ist auf die Verwendung von Audiostreams im Pull- und Pushmodus mit acht PCM-Audiokanälen (16-Bit 16 kHz) beschränkt.
* Die Unterhaltungstranskription ist derzeit in den Sprachen „en-US“ und „zh-CN“ in den folgenden Regionen verfügbar: USA, Mitte (centralus) und Asien, Osten (eastasia).

## <a name="prerequisites"></a>Voraussetzungen

* [Machen Sie sich mit der Verwendung der Spracherkennung mit dem Speech SDK vertraut.](quickstart-csharp-dotnet-windows.md)
* [Beziehen Sie ein Testabonnements für Speech.](https://azure.microsoft.com/try/cognitive-services/)
* Das Speech SDK Version 1.5.1 oder höher ist erforderlich.

## <a name="create-voice-signatures-for-participants"></a>Erstellen von Stimmsignaturen für Teilnehmer

Erstellen Sie als Erstes Stimmsignaturen für die Teilnehmer der Unterhaltung. Die Erstellung von Stimmsignaturen ist für die effiziente Sprecheridentifikation erforderlich.

### <a name="requirements-for-input-wave-file"></a>Anforderungen an die Eingabewellendatei

* Die eingegebene Audiowellendatei zum Erstellen von Stimmsignaturen muss in 16-Bit-Abtastungen, 16-kHz-Abtastrate und einem Monoformat (einzelner Kanal) vorliegen.
* Die empfohlene Länge der einzelnen Audiobeispiele liegt zwischen 30 Sekunden und zwei Minuten.

Das folgende Beispiel zeigt zwei verschiedene Möglichkeiten zum Erstellen der Stimmsignatur [über die REST-API] (https://aka.ms/cts/signaturegenservice) von C#:

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribing-conversations"></a>Transkribieren von Unterhaltungen

Erstellen Sie zum Transkribieren von Unterhaltungen mit mehreren Teilnehmern das Objekt `ConversationTranscriber`, das dem für die Unterhaltungssitzung erstellten Objekt `AudioConfig` zugeordnet ist, und streamen Sie Audio mithilfe von `PullAudioInputStream` oder `PushAudioInputStream`.

Angenommen, Sie verwenden eine ConversationTranscriber-Klasse namens `MyConversationTranscriber`. Ihr Code könnte wie folgt aussehen:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Conversation;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own subscription key and region.
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file.
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            // Creates a conversation transcriber using audio stream input.
            using (var transcriber = new ConversationTranscriber(config, audioInput))
            {
                // Subscribes to events.
                transcriber.Recognizing += (s, e) =>
                {
                    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                };

                transcriber.Recognized += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                transcriber.Canceled += (s, e) =>
                {
                    Console.WriteLine($"CANCELED: Reason={e.Reason}");

                    if (e.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        stopTranscription.TrySetResult(0);
                    }
                };

                transcriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine("\nSession started event.");
                };

                transcriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine("\nSession stopped event.");
                    Console.WriteLine("\nStop recognition.");
                    stopTranscription.TrySetResult(0);
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create voice signatures using REST API described in the earlier section in this document. 
                // Voice signature needs to be in the following format:
                // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                transcriber.AddParticipant(speakerA);
                transcriber.AddParticipant(speakerB);
                transcriber.AddParticipant(speakerC);

                // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                await transcriber.StartTranscribingAsync().ConfigureAwait(false);

                // Waits for completion.
                // Use Task.WaitAny to keep the task rooted.
                Task.WaitAny(new[] { stopTranscription.Task });

                // Stop transcribing the conversation.
                await transcriber.StopTranscribingAsync().ConfigureAwait(false);

                // Ends the conversation.
                await transcriber.EndConversationAsync().ConfigureAwait(false);
            }
        }
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erkunden unserer Beispiele auf GitHub](https://aka.ms/csspeech/samples)
