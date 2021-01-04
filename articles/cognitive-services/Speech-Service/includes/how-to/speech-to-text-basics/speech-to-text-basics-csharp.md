---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: c44bd27108714b4c2623de49540fe1f5723ccd6a
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96912134"
---
Die Funktion zum Erkennen und Transkribieren von menschlicher Sprache (Spracherkennung) ist eines der zentralen Features des Speech-Diensts. In diesem Schnellstart erfahren Sie, wie Sie das Speech SDK in Ihren Apps und Produkten verwenden, um hochwertige Spracherkennungen durchzuführen.

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [C#-Schnellstartbeispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet) zu, falls Sie direkt zum Beispielcode springen möchten.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Führen Sie `Install-Package Microsoft.CognitiveServices.Speech` in der NuGet-Konsole aus, wenn Sie nur den Paketnamen für die ersten Schritte benötigen.

Plattformspezifische Installationsanweisungen finden Sie unter den folgenden Links:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Erstellen einer Sprachkonfiguration

Um den Speech-Dienst über das Speech SDK aufrufen zu können, muss eine Sprachkonfiguration ([`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)) erstellt werden. Diese Klasse enthält Informationen zu Ihrem Abonnement. Hierzu zählen etwa Ihr Schlüssel und die zugeordnete Region, der Endpunkt, der Host oder das Autorisierungstoken. Erstellen Sie mithilfe des Schlüssels und der Region eine Sprachkonfiguration ([`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)). Ihr Schlüssel-Regions-Paar finden Sie auf der Seite [Ermitteln von Schlüsseln und Region](../../../overview.md#find-keys-and-region).

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
    }
}
```

Es gibt noch einige andere Möglichkeiten, eine Sprachkonfiguration ([`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)) zu initialisieren:

* Mit einem Endpunkt: Übergeben Sie einen Endpunkt für den Speech-Dienst. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Host: Übergeben Sie eine Hostadresse. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Autorisierungstoken: Übergeben Sie ein Autorisierungstoken und die zugeordnete Region.

> [!NOTE]
> Eine Konfiguration ist immer erforderlich. Dabei spielt es keine Rolle, ob Sie eine Spracherkennung, eine Sprachsynthese, eine Übersetzung oder eine Absichtserkennung durchführen möchten.

## <a name="recognize-from-microphone"></a>Erkennen über Mikrofon

Um Sprache über das Mikrofon Ihres Geräts zu erkennen, erstellen Sie mithilfe von `FromDefaultMicrophoneInput()` eine Audiokonfiguration (`AudioConfig`). Initialisieren Sie dann [`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet), und übergeben Sie `audioConfig` und `speechConfig`.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromMic(SpeechConfig speechConfig)
    {
        using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        Console.WriteLine("Speak into your microphone.");
        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromMic(speechConfig);
    }
}
```

Wenn Sie ein *bestimmtes* Audioeingabegerät verwenden möchten, müssen Sie in `AudioConfig` die Geräte-ID angeben. Informationen zum Abrufen der Geräte-ID für Ihr Audioeingabegerät finden Sie [hier](../../../how-to-select-audio-input-devices.md).

## <a name="recognize-from-file"></a>Erkennen aus Datei

Wenn Sie Sprache aus einer Audiodatei erkennen möchten, anstatt ein Mikrofon zu verwenden, müssen Sie trotzdem eine Audiokonfiguration (`AudioConfig`) erstellen. In diesem Fall rufen Sie beim Erstellen der Audiokonfiguration ([`AudioConfig`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet)) allerdings `FromWavFileInput()` (anstelle von `FromDefaultMicrophoneInput()`) auf und übergeben den Dateipfad.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromFile(SpeechConfig speechConfig)
    {
        using var audioConfig = AudioConfig.FromWavFileInput("PathToFile.wav");
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromFile(speechConfig);
    }
}
```

## <a name="recognize-from-in-memory-stream"></a>Erkennen über InMemory-Datenstrom

Bei vielen Anwendungsfällen stammen Ihre Audiodaten wahrscheinlich aus Blobspeicher oder sind anderweitig bereits als `byte[]` oder eine ähnliche Rohdatenstruktur im Arbeitsspeicher vorhanden. Im folgenden Beispiel wird [`PushAudioInputStream`](/dotnet/api/microsoft.cognitiveservices.speech.audio.pushaudioinputstream?view=azure-dotnet) verwendet, um Sprache zu erkennen. Dabei handelt es sich im Prinzip um einen abstrahierten Arbeitsspeicherdatenstrom. Der Beispielcode führt die folgenden Aufgaben aus:

* Schreiben unformatierter Audiodaten (PCM) in `PushAudioInputStream` mithilfe der Funktion `Write()`, die `byte[]` akzeptiert
* Lesen einer Datei vom Typ `.wav` mithilfe von `FileReader` zu Demonstrationszwecken. Wenn `byte[]` jedoch bereits Audiodaten enthält, können Sie direkt mit dem Schreiben von Inhalten in den Eingabestream fortfahren.
* Das Standardformat ist 16 Bit, 16 KHz und Mono-PCM. Zum Anpassen des Formats können Sie mithilfe der statischen Funktion `AudioStreamFormat.GetWaveFormatPCM(sampleRate, (byte)bitRate, (byte)channels)` ein [`AudioStreamFormat`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audiostreamformat?view=azure-dotnet)-Objekt an `CreatePushStream()` übergeben.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromStream(SpeechConfig speechConfig)
    {
        var reader = new BinaryReader(File.OpenRead("PathToFile.wav"));
        using var audioInputStream = AudioInputStream.CreatePushStream();
        using var audioConfig = AudioConfig.FromStreamInput(audioInputStream);
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        byte[] readBytes;
        do
        {
            readBytes = reader.ReadBytes(1024);
            audioInputStream.Write(readBytes, readBytes.Length);
        } while (readBytes.Length > 0);

        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromStream(speechConfig);
    }
}
```

Bei der Verwendung eines Pushstreams als Eingabe wird davon ausgegangen, dass es sich bei den Audiodaten um unformatiertes PCM handelt, d. h. Header werden übersprungen.
In bestimmten Fällen kann die API weiterhin verwendet werden, wenn der Header nicht übersprungen wurde. Um die besten Ergebnisse zu erzielen, empfiehlt es sich jedoch, eine Logik zum Lesen der Header zu implementieren, sodass `byte[]` am *Anfang der Audiodaten* beginnt.

## <a name="error-handling"></a>Fehlerbehandlung

In den vorherigen Beispielen wird lediglich der erkannte Text aus `result.text` abgerufen. Zur Behandlung von Fehlern und anderen Antworten müssen Sie jedoch Code schreiben, um das Ergebnis zu verarbeiten. Der folgende Code wertet die Eigenschaft [`result.Reason`](/dotnet/api/microsoft.cognitiveservices.speech.recognitionresult.reason?preserve-view=true&view=azure-dotnet) aus und geht wie folgt vor:

* Das Erkennungsergebnis wird ausgegeben: `ResultReason.RecognizedSpeech`
* Wurde kein Erkennungstreffer gefunden, wird der Benutzer davon in Kenntnis gesetzt: `ResultReason.NoMatch`
* Ist ein Fehler aufgetreten, wird die Fehlermeldung ausgegeben: `ResultReason.Canceled`

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
        break;
    case ResultReason.NoMatch:
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        var cancellation = CancellationDetails.FromResult(result);
        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

        if (cancellation.Reason == CancellationReason.Error)
        {
            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
            Console.WriteLine($"CANCELED: Did you update the subscription info?");
        }
        break;
}
```

## <a name="continuous-recognition"></a>Kontinuierliche Erkennung

In den vorherigen Beispielen wird Einzelerkennung verwendet, bei der eine einzelne Äußerung erkannt wird. Zur Erkennung des Endes einer einzelnen Äußerung wird auf Stille am Ende gelauscht oder gewartet, bis maximal 15 Sekunden an Audiodaten verarbeitet wurden.

Im Gegensatz dazu wird die kontinuierliche Erkennung verwendet, wenn Sie **steuern** möchten, wann die Erkennung beendet wird. Für die kontinuierliche Erkennung müssen die Ereignisse `Recognizing`, `Recognized` und `Canceled` abonniert werden, um die Erkennungsergebnisse zu erhalten. Zum Beenden der Erkennung muss [`StopContinuousRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?preserve-view=true&view=azure-dotnet) aufgerufen werden. Im folgenden Beispiel wird eine kontinuierliche Erkennung für eine Audioeingabedatei durchgeführt.

Beginnen Sie mit dem Definieren der Eingabe und dem Initialisieren einer Spracherkennung ([`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?preserve-view=true&view=azure-dotnet)):

```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

Erstellen Sie als Nächstes `TaskCompletionSource<int>`, um den Zustand der Spracherkennung zu verwalten.

```csharp
var stopRecognition = new TaskCompletionSource<int>();
```

Abonnieren Sie anschließend die von [`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) gesendeten Ereignisse:

* [`Recognizing`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizing?preserve-view=true&view=azure-dotnet): Signal für Ereignisse mit Zwischenergebnissen der Erkennung.
* [`Recognized`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognized?preserve-view=true&view=azure-dotnet): Signal für Ereignisse mit Endergebnissen der Erkennung. (Der Erkennungsversuch war also erfolgreich.)
* [`SessionStopped`](/dotnet/api/microsoft.cognitiveservices.speech.recognizer.sessionstopped?preserve-view=true&view=azure-dotnet): Signal für Ereignisse, die das Ende einer Erkennungssitzung (bzw. eines Erkennungsvorgangs) angeben.
* [`Canceled`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.canceled?preserve-view=true&view=azure-dotnet): Signal für Ereignisse mit Ergebnissen einer abgebrochenen Erkennung. (Der Erkennungsversuch wurde also aufgrund einer direkten Abbruchanforderungen oder infolge eines Transport- oder Protokollfehlers abgebrochen.)

```csharp
recognizer.Recognizing += (s, e) =>
{
    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
};

recognizer.Recognized += (s, e) =>
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
    }
    else if (e.Result.Reason == ResultReason.NoMatch)
    {
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
    }
};

recognizer.Canceled += (s, e) =>
{
    Console.WriteLine($"CANCELED: Reason={e.Reason}");

    if (e.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }

    stopRecognition.TrySetResult(0);
};

recognizer.SessionStopped += (s, e) =>
{
    Console.WriteLine("\n    Session stopped event.");
    stopRecognition.TrySetResult(0);
};
```

Rufen Sie nach Abschluss der Einrichtung `StartContinuousRecognitionAsync` auf, um die Erkennung zu starten.

```csharp
await recognizer.StartContinuousRecognitionAsync();

// Waits for completion. Use Task.WaitAny to keep the task rooted.
Task.WaitAny(new[] { stopRecognition.Task });

// make the following call at some point to stop recognition.
// await recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Diktiermodus

Bei Verwendung der kontinuierlichen Erkennung können Sie die Diktatverarbeitung mithilfe der entsprechenden Funktion „Diktat aktivieren“ aktivieren. Dieser Modus bewirkt, dass die Sprachkonfigurationsinstanz Wortbeschreibungen von Satzstrukturen wie z. B. Interpunktion interpretiert. Beispielsweise würde die Äußerung „Leben Sie in der Stadt Fragezeichen“ als Text „Leben Sie in der Stadt?“ interpretiert.

Verwenden Sie zum Aktivieren des Diktiermodus die Methode [`EnableDictation`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?preserve-view=true&view=azure-dotnet) in Ihrer Sprachkonfiguration ([`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet)).

```csharp
speechConfig.EnableDictation();
```

## <a name="change-source-language"></a>Ändern der Ausgangssprache

Eine gängige Aufgabe für die Spracherkennung ist die Angabe der Eingabesprache (Ausgangssprache). Im folgenden Beispiel wird gezeigt, wie Sie die Eingabesprache auf Italienisch festlegen. Navigieren Sie in Ihrem Code zu [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet), und fügen Sie direkt darunter die folgende Zeile hinzu:

```csharp
speechConfig.SpeechRecognitionLanguage = "it-IT";
```

Von der Eigenschaft [`SpeechRecognitionLanguage`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?preserve-view=true&view=azure-dotnet) wird eine Zeichenfolge im Format „Sprache-Gebietsschema“ erwartet. Sie können einen beliebigen Wert aus der Spalte **Gebietsschema** der Liste mit den unterstützten [Gebietsschemas/Sprachen](../../../language-support.md) angeben.

## <a name="improve-recognition-accuracy"></a>Verbessern der Erkennungsgenauigkeit

Begriffslisten dienen zur Identifizierung bekannter Begriffe in Audiodaten, wie beispielsweise den Namen einer Person oder einen bestimmten Ort. Durch die Bereitstellung einer Begriffsliste können Sie die Genauigkeit der Spracherkennung verbessern.

Wenn Sie beispielsweise den Befehl „Move to“ und „Ward“ als mögliches Ziel haben, das gesprochen werden kann, können Sie den Eintrag „Move to Ward“ hinzufügen. Das Hinzufügen eines Begriffs erhöht die Wahrscheinlichkeit, dass bei der Audioerkennung „Move to Ward“ anstelle von „Move toward“ erkannt wird.

Einer Begriffsliste können einzelne Wörter oder ganze Phrasen hinzugefügt werden. Bei der Erkennung wird ein Eintrag in einer Begriffsliste verwendet, um die Erkennung der Wörter und Begriffe in der Liste zu optimieren, auch wenn sich die Einträge in der Mitte der Äußerung befinden. 

> [!IMPORTANT]
> Das Begriffslistenfeature steht für folgende Sprachen zur Verfügung: en-US, de-DE, en-AU, en-CA, en-GB, es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN

Wenn Sie eine Begriffsliste verwenden möchten, erstellen Sie zunächst ein Objekt vom Typ [`PhraseListGrammar`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?preserve-view=true&view=azure-dotnet), und fügen Sie anschließend mithilfe von [`AddPhrase`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?preserve-view=true&view=azure-dotnet) bestimmte Wörter und Begriffe hinzu.

Änderungen an [`PhraseListGrammar`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?preserve-view=true&view=azure-dotnet) werden bei der nächsten Erkennung oder nach erneuter Verbindungsherstellung mit dem Speech-Dienst wirksam.

```csharp
var phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Supercalifragilisticexpialidocious");
```

Wenn Sie Ihre Begriffsliste löschen möchten, verwenden Sie Folgendes: 

```csharp
phraseList.Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Weitere Optionen zum Verbessern der Erkennungsgenauigkeit

Begriffslisten sind nur eine von mehreren Optionen zur Verbesserung der Erkennungsgenauigkeit. Sie können außerdem: 

* [Verbessern der Genauigkeit mithilfe von Custom Speech](../../../custom-speech-overview.md)
* [Verbessern der Genauigkeit mithilfe von Mandantenmodellen](../../../tutorial-tenant-model.md)