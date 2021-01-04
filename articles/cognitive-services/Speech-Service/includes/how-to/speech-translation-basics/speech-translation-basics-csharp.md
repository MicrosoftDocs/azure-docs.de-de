---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: c8392770d7edb18f776f7664657c6985b9f7fb63
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424867"
---
Eines der zentralen Features des Speech-Diensts ist die Fähigkeit, menschliche Sprache zu erkennen und in andere Sprachen zu übersetzen. In diesem Schnellstart erfahren Sie, wie Sie das Speech SDK in Ihren Apps und Produkten verwenden, um hochwertige Sprachübersetzungen durchzuführen. In diesem Schnellstart werden folgende Themen behandelt:

* Übersetzen von Sprache in Text
* Übersetzen von Sprache in mehrere Zielsprachen
* Durchführen einer direkten Übersetzung von Sprache in Sprache

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [C#-Schnellstartbeispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/translate-speech-to-text) zu, falls Sie direkt zum Beispielcode springen möchten.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Zuallererst muss das Speech SDK installiert werden. Folgen Sie je nach Plattform den Anweisungen im Abschnitt <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Abrufen des Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span></a> des Artikels _Abrufen des Speech SDK_.

## <a name="import-dependencies"></a>Importieren von Abhängigkeiten

Fügen Sie die folgenden `using`-Anweisungen oben in der *Program.cs* -Codedatei ein, um die Beispiele in diesem Artikel auszuführen.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Vertrauliche Daten und Umgebungsvariablen

Der Beispielquellcode in diesem Artikel benötigt Umgebungsvariablen zum Speichern vertraulicher Daten, wie z. B. den Schlüssel und die Region des Speech-Ressourcenabonnements. Die `Program`-Klasse enthält zwei `static readonly string`-Werte, die von den Umgebungsvariablen des Hostcomputers zugewiesen werden, nämlich `SPEECH__SUBSCRIPTION__KEY` und `SPEECH__SERVICE__REGION`. Beide Felder befinden sich im Klassenbereich, sodass sie innerhalb vom Methodentext der Klasse zugänglich sind. Weitere Informationen zu Umgebungsvariablen finden Sie unter [Umgebungsvariablen und Anwendungskonfiguration](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => Task.CompletedTask;
}
```

## <a name="create-a-speech-translation-configuration"></a>Erstellen einer Sprachübersetzungskonfiguration

Um den Speech-Dienst über das Speech SDK aufrufen zu können, muss eine Sprachkonfiguration ([`SpeechTranslationConfig`][config]) erstellt werden. Diese Klasse enthält Informationen zu Ihrem Abonnement. Hierzu zählen etwa Ihr Schlüssel und die zugeordnete Region, der Endpunkt, der Host oder das Autorisierungstoken.

> [!TIP]
> Eine Konfiguration ist immer erforderlich. Dabei spielt es keine Rolle, ob Sie eine Spracherkennung, eine Sprachsynthese, eine Übersetzung oder eine Absichtserkennung durchführen möchten.

Eine Sprachkonfiguration ([`SpeechTranslationConfig`][config]) kann auf unterschiedliche Weise initialisiert werden:

* Mit einem Abonnement: Übergeben Sie einen Schlüssel und die zugeordnete Region.
* Mit einem Endpunkt: Übergeben Sie einen Endpunkt für den Speech-Dienst. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Host: Übergeben Sie eine Hostadresse. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Autorisierungstoken: Übergeben Sie ein Autorisierungstoken und die zugeordnete Region.

Hier sehen Sie, wie eine Sprachkonfiguration ([`SpeechTranslationConfig`][config]) mit einem Schlüssel und einer Region erstellt wird. Diese Anmeldeinformationen können Sie mithilfe der Schritte unter [Kostenloses Testen des Speech-Diensts](../../../overview.md#try-the-speech-service-for-free) abrufen.

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => TranslateSpeechAsync();

    static async Task TranslateSpeechAsync()
    {
        var translationConfig =
            SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Ändern der Ausgangssprache

Eine häufige Aufgabe bei der Sprachübersetzung ist die Angabe der Eingabesprache (Quellsprache). Im folgenden Beispiel wird gezeigt, wie Sie die Eingabesprache auf Italienisch festlegen. Interagieren Sie in Ihrem Code mit der [`SpeechTranslationConfig`][config]-Instanz, und weisen Sie die `SpeechRecognitionLanguage`-Eigenschaft zu.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

Von der Eigenschaft [`SpeechRecognitionLanguage`][recognitionlang] wird eine Zeichenfolge im Format „Sprache-Gebietsschema“ erwartet. Sie können einen beliebigen Wert aus der Spalte **Gebietsschema** der Liste mit den unterstützten [Gebietsschemas/Sprachen](../../../language-support.md) angeben.

## <a name="add-translation-language"></a>Hinzufügen der Übersetzungssprache

Eine weitere häufige Aufgabe bei der Sprachübersetzung ist die Angabe von Zielübersetzungssprachen, wobei mindestens eine erforderlich ist, jedoch mehrere unterstützt werden. Im folgenden Codeausschnitt sind sowohl Französisch als auch Deutsch als Zielübersetzungssprachen angegeben.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig.SpeechRecognitionLanguage = "it-IT";
    
    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("de");
}
```

Mit jedem Aufruf von [`AddTargetLanguage`][addlang] wird eine neue Zielübersetzungssprache angegeben. Anders ausgedrückt: Wenn für die Ausgangssprache eine Spracherkennung erfolgt, wird im Rahmen des daraus resultierenden Übersetzungsvorgangs eine Zielübersetzung bereitgestellt.

## <a name="initialize-a-translation-recognizer"></a>Initialisieren einer Übersetzungserkennung

Nach der Erstellung einer Sprachkonfiguration ([`SpeechTranslationConfig`][config]) muss eine Spracherkennung ([`TranslationRecognizer`][recognizer]) initialisiert werden. Wenn Sie eine Spracherkennung ([`TranslationRecognizer`][recognizer]) initialisieren, müssen Sie Ihre Sprachkonfiguration (`translationConfig`) übergeben. Das Konfigurationsobjekt stellt Anmeldeinformationen bereit, die der Speech-Dienst zur Überprüfung Ihrer Anforderung benötigt.

Wenn Sie für die Spracherkennung das Standardmikrofon Ihres Geräts verwenden, sollte [`TranslationRecognizer`][recognizer] wie folgt aussehen:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);
}
```

Wenn Sie das Audioeingabegerät angeben möchten, müssen Sie eine Audiokonfiguration ([`AudioConfig`][audioconfig]) erstellen und beim Initialisieren Ihrer Spracherkennung ([`TranslationRecognizer`][recognizer]) den Parameter `audioConfig` angeben.

> [!TIP]
> Informationen zum Abrufen der Geräte-ID für Ihr Audioeingabegerät finden Sie [hier](../../../how-to-select-audio-input-devices.md).

Verweisen Sie zunächst auf das `AudioConfig`-Objekt. Gehen Sie dabei wie folgt vor:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

Eine Audiokonfiguration (`audioConfig`) ist auch erforderlich, wenn Sie anstelle eines Mikrofons eine Audiodatei verwenden möchten. In diesem Fall muss beim Erstellen einer Audiokonfiguration ([`AudioConfig`][audioconfig]) allerdings `FromWavFileInput` (anstelle von `FromDefaultMicrophoneInput`) aufgerufen und der Parameter `filename` übergeben werden.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Übersetzen von gesprochener Sprache

Zum Übersetzen von Sprache benötigt das Speech SDK eine Audioeingabe von einem Mikrofon oder einer Audiodatei. Die Spracherkennung erfolgt vor der Sprachübersetzung. Nachdem alle Objekte initialisiert wurden, verwenden Sie die Funktion „Einmal erkennen“, und rufen Sie das Ergebnis ab.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");
    
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\":");
        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");
        }
    }
}
```

Weitere Informationen zur Spracherkennung finden Sie unter [Grundlegendes zur Spracherkennung](../../../get-started-speech-to-text.md).

## <a name="synthesize-translations"></a>Synthetisieren von Übersetzungen

Nach erfolgreicher Spracherkennung und -übersetzung werden alle Übersetzungen in einem Wörterbuch bereitgestellt. Der Wörterbuchschlüssel [`Translations`][translations] ist die Zielübersetzungssprache, und der Wert ist der übersetzte Text. Erkannte Sprache kann übersetzt und dann in einer anderen Sprache synthetisiert werden (Sprache-zu-Sprache).

### <a name="event-based-synthesis"></a>Ereignisbasierte Synthese

Das `TranslationRecognizer`-Objekt macht ein einzelnes `Synthesizing`-Ereignis verfügbar. Das Ereignis wird mehrmals ausgelöst und bietet einen Mechanismus, mit dem das synthetische Audioformat aus dem Übersetzungserkennungsergebnis abgerufen werden kann. Weitere Informationen zum Übersetzen in mehrere Sprachen finden Sie unter [Manuelle Synthese](#manual-synthesis). Geben Sie die Synthesestimme an, indem Sie einen [`VoiceName`][voicename] zuweisen und einen Ereignishandler für das `Synthesizing`-Ereignis zum Abrufen der Audioinhalte bereitstellen. Im folgenden Beispiel wird die übersetzte Audiodatei als *WAV* -Datei gespeichert.

> [!IMPORTANT]
> Die ereignisbasierte Synthese funktioniert nur mit einer einzigen Übersetzung. Fügen Sie deshalb **nur eine** Zielübersetzungssprache hinzu. Zudem muss die Sprache in [`VoiceName`][voicename] mit der Zielübersetzungssprache übereinstimmen. So wird `"de"` beispielsweise `"de-DE-Hedda"` zugeordnet.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguage = "de";
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    translationConfig.AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.VoiceName = "de-DE-Hedda";

    using var recognizer = new TranslationRecognizer(translationConfig);

    recognizer.Synthesizing += (_, e) =>
    {
        var audio = e.Result.GetAudio();
        Console.WriteLine($"Audio synthesized: {audio.Length:#,0} byte(s) {(audio.Length == 0 ? "(Complete)" : "")}");

        if (audio.Length > 0)
        {
            File.WriteAllBytes("YourAudioFile.wav", audio);
        }
    };

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{toLanguage}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\"");
        Console.WriteLine($"Translated into '{toLanguage}': {result.Translations[toLanguage]}");
    }
}
```

### <a name="manual-synthesis"></a>Manuelle Synthese

Das [`Translations`][translations]-Wörterbuch kann verwendet werden, um Audiodaten aus dem Übersetzungstext zu synthetisieren. Durchlaufen Sie die Übersetzungen, und synthetisieren Sie die jeweiligen Übersetzungen. Beim Erstellen einer `SpeechSynthesizer`-Instanz muss die [`SpeechSynthesisVoiceName`][speechsynthesisvoicename]-Eigenschaft des `SpeechConfig`-Objekts auf die gewünschte Stimme festgelegt werden. Im folgenden Beispiel wird in fünf Sprachen übersetzt. Jede Übersetzung wird anschließend in eine Audiodatei in der entsprechenden neuronalen Sprache synthetisiert.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        var languageToVoiceMap = new Dictionary<string, string>
        {
            ["de"] = "de-DE-KatjaNeural",
            ["en"] = "en-US-AriaNeural",
            ["it"] = "it-IT-ElsaNeural",
            ["pt"] = "pt-BR-FranciscaNeural",
            ["zh-Hans"] = "zh-CN-XiaoxiaoNeural"
        };

        Console.WriteLine($"Recognized: \"{result.Text}\"");

        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");

            var speechConfig =
                SpeechConfig.FromSubscription(
                    SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);
            speechConfig.SpeechSynthesisVoiceName = languageToVoiceMap[language];

            using var audioConfig = AudioConfig.FromWavFileOutput($"{language}-translation.wav");
            using var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
            
            await synthesizer.SpeakTextAsync(translation);
        }
    }
}
```

Weitere Informationen zur Sprachsynthese finden Sie unter [Grundlegendes zur Sprachsynthese](../../../get-started-text-to-speech.md).

[config]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig?view=azure-dotnet
[audioconfig]: /dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet
[recognizer]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer?view=azure-dotnet
[recognitionlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet
[addlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage?view=azure-dotnet
[translations]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations?view=azure-dotnet
[voicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename?view=azure-dotnet
[speechsynthesisvoicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename?view=azure-dotnet