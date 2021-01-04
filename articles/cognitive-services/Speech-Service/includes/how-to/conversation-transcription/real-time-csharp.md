---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: trbye
ms.openlocfilehash: bae83ec446263fe8fad2a7dfb6323051838566ff
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486781"
---
## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Zuallererst muss das Speech SDK installiert werden. Verwenden Sie dazu die folgenden plattformspezifischen Anleitungen:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-voice-signatures"></a>Erstellen von Stimmsignaturen

Der erste Schritt besteht im Erstellen von Stimmsignaturen für die Teilnehmer der Unterhaltung, damit sie eindeutig als Sprecher erkannt werden können. Die `.wav`-Datei für die Audioeingabe zum Erstellen von Stimmsignaturen sollte in 16-Bit, mit 16-kHz-Abtastrate und im Monoformat (einzelner Kanal) vorliegen. Die empfohlene Länge der einzelnen Audiostichproben liegt zwischen 30 Sekunden und zwei Minuten. Bei der `.wav`-Datei sollte es sich um ein Beispiel für die Stimme **einer Person** handeln, damit ein eindeutiges Sprachprofil erstellt wird.

Das folgende Beispiel zeigt das Erstellen einer Stimmsignatur [mithilfe der REST-API](https://aka.ms/cts/signaturegenservice) in C#. Beachten Sie, dass Sie `subscriptionKey`, `region` und den Pfad der `.wav`-Beispieldatei durch echte Informationen ersetzen müssen.

```csharp
using System;
using System.IO;
using System.Net.Http;
using System.Runtime.Serialization;
using System.Threading.Tasks;
using Newtonsoft.Json;

[DataContract]
internal class VoiceSignature
{
    [DataMember]
    public string Status { get; private set; }

    [DataMember]
    public VoiceSignatureData Signature { get; private set; }

    [DataMember]
    public string Transcription { get; private set; }
}

[DataContract]
internal class VoiceSignatureData
{
    internal VoiceSignatureData()
    { }

    internal VoiceSignatureData(int version, string tag, string data)
    {
        this.Version = version;
        this.Tag = tag;
        this.Data = data;
    }

    [DataMember]
    public int Version { get; private set; }

    [DataMember]
    public string Tag { get; private set; }

    [DataMember]
    public string Data { get; private set; }
}

private static async Task<string> GetVoiceSignatureString()
{
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";

    byte[] fileBytes = File.ReadAllBytes("path-to-voice-sample.wav");
    var content = new ByteArrayContent(fileBytes);
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
    
    var jsonData = await response.Content.ReadAsStringAsync();
    var result = JsonConvert.DeserializeObject<VoiceSignature>(jsonData);
    return JsonConvert.SerializeObject(result.Signature);
}
```

Beim Ausführen der Funktion `GetVoiceSignatureString()` wird eine Stimmsignatur-Zeichenfolge im richtigen Format zurückgegeben. Führen Sie die Funktion zweimal aus, damit Sie über zwei Zeichenfolgen verfügen, die Sie unten als Eingabe für die Variablen `voiceSignatureStringUser1` und `voiceSignatureStringUser2` verwenden können.

> [!NOTE]
> Stimmsignaturen können **nur** mithilfe der REST-API erstellt werden.

## <a name="transcribe-conversations"></a>Transkribieren von Konversationen

Der folgende Beispielcode veranschaulicht das Transkribieren von Konversationen in Echtzeit für zwei Sprecher. Dabei wird davon ausgegangen, dass Sie bereits für alle Sprecher Stimmsignatur-Zeichenfolgen erstellt haben, wie oben gezeigt. Ersetzen Sie `subscriptionKey`, `region` und den Pfad `filepath` für das zu transkribierende Audiomaterial durch echte Informationen.

Der Beispielcode führt die folgenden Aufgaben aus:

* Erstellt einen `AudioStreamReader` aus der `.wav`-Beispieldatei für die Transkription.
* Erstellt eine `Conversation` mithilfe von `CreateConversationAsync()`.
* Erstellt einen `ConversationTranscriber` mit dem-Konstruktor und abonniert die erforderlichen Ereignisse.
* Fügt der Unterhaltung Teilnehmer hinzu. Die Zeichenfolgen `voiceSignatureStringUser1` und `voiceSignatureStringUser2` sollten sich als Ausgabe aus den oben dargestellten Schritten aus der Funktion `GetVoiceSignatureString()` ergeben.
* Tritt der Unterhaltung bei und beginnt mit der Transkription.

Ruft die `TranscribeConversationsAsync()`-Funktion auf, um die Transkription der Unterhaltung zu starten.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Transcription;

public static async Task TranscribeConversationsAsync(string voiceSignatureStringUser1, string voiceSignatureStringUser2)
{
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav";

    var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
    var stopRecognition = new TaskCompletionSource<int>();

    using (var audioInput = AudioStreamReader.OpenWavFile(filepath))
    {
        var meetingID = Guid.NewGuid().ToString();
        using (var conversation = await Conversation.CreateConversationAsync(config, meetingID))
        {
            // create a conversation transcriber using audio stream input
            using (var conversationTranscriber = new ConversationTranscriber(audioInput))
            {
                conversationTranscriber.Transcribing += (s, e) =>
                {
                    Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text} SpeakerId={e.Result.UserId}");
                };

                conversationTranscriber.Transcribed += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text} SpeakerId={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                conversationTranscriber.Canceled += (s, e) =>
                {
                    Console.WriteLine($"CANCELED: Reason={e.Reason}");

                    if (e.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        stopRecognition.TrySetResult(0);
                    }
                };

                conversationTranscriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine($"\nSession started event. SessionId={e.SessionId}");
                };

                conversationTranscriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine($"\nSession stopped event. SessionId={e.SessionId}");
                    Console.WriteLine("\nStop recognition.");
                    stopRecognition.TrySetResult(0);
                };

                // Add participants to the conversation.
                var speaker1 = Participant.From("User1", "en-US", voiceSignatureStringUser1);
                var speaker2 = Participant.From("User2", "en-US", voiceSignatureStringUser2);
                await conversation.AddParticipantAsync(speaker1);
                await conversation.AddParticipantAsync(speaker2);

                // Join to the conversation and start transcribing
                await conversationTranscriber.JoinConversationAsync(conversation);
                await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                // waits for completion, then stop transcription
                Task.WaitAny(new[] { stopRecognition.Task });
                await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
            }
        }
    }
}
```