---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: d5489c5c548d1242ebac9f17faa8ed55fa79108b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424665"
---
Eines der zentralen Features des Speech-Diensts ist die Fähigkeit, menschliche Sprache zu erkennen und in andere Sprachen zu übersetzen. In diesem Schnellstart erfahren Sie, wie Sie das Speech SDK in Ihren Apps und Produkten verwenden, um hochwertige Sprachübersetzungen durchzuführen. In diesem Schnellstart werden folgende Themen behandelt:

* Übersetzen von Sprache in Text
* Übersetzen von Sprache in mehrere Zielsprachen
* Durchführen einer direkten Übersetzung von Sprache in Sprache

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [C++-Schnellstartbeispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/translate-speech-to-text) zu, falls Sie direkt zum Beispielcode springen möchten.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Zuallererst muss das Speech SDK installiert werden. Folgen Sie je nach Plattform den Anweisungen im Abschnitt <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Abrufen des Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span></a> des Artikels _Abrufen des Speech SDK_.

## <a name="import-dependencies"></a>Importieren von Abhängigkeiten

Fügen Sie die folgenden `#include`- und `using`-Anweisungen oben in der C/C++-Codedatei ein, um die Beispiele in diesem Artikel auszuführen.

```cpp
#include <iostream> // cin, cout
#include <fstream>
#include <string>
#include <stdio.h>
#include <stdlib.h>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
using namespace Microsoft::CognitiveServices::Speech::Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Sensible Daten und Umgebungsvariablen

Der Beispielquellcode in diesem Artikel hängt von Umgebungsvariablen zum Speichern sensibler Daten ab, wie z. B. dem Schlüssel und der Region des Sprachressourcenabonnements. Die C++-Codedatei enthält zwei Zeichenfolgenwerte, die von den Umgebungsvariablen des Hostcomputers zugewiesen werden, nämlich `SPEECH__SUBSCRIPTION__KEY` und `SPEECH__SERVICE__REGION`. Beide Felder befinden sich im Klassenbereich, sodass sie innerhalb vom Methodentext der Klasse zugänglich sind. Weitere Informationen zu Umgebungsvariablen finden Sie unter [Umgebungsvariablen und Anwendungskonfiguration](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");
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

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");

void translateSpeech() {
    auto config =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
}

int main(int argc, char** argv) {
    setlocale(LC_ALL, "");
    translateSpeech();
    return 0;
}
```

## <a name="change-source-language"></a>Ändern der Ausgangssprache

Eine häufige Aufgabe bei der Sprachübersetzung ist die Angabe der Eingabesprache (Quellsprache). Im folgenden Beispiel wird gezeigt, wie Sie die Eingabesprache auf Italienisch festlegen. Interagieren Sie in Ihrem Code mit der [`SpeechTranslationConfig`][config]-Instanz, und rufen Sie die `SetSpeechRecognitionLanguage`-Methode auf.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig->SetSpeechRecognitionLanguage("it-IT");
}
```

Von der Eigenschaft [`SpeechRecognitionLanguage`][recognitionlang] wird eine Zeichenfolge im Format „Sprache-Gebietsschema“ erwartet. Sie können einen beliebigen Wert aus der Spalte **Gebietsschema** der Liste mit den unterstützten [Gebietsschemas/Sprachen](../../../language-support.md) angeben.

## <a name="add-translation-language"></a>Hinzufügen der Übersetzungssprache

Eine weitere häufige Aufgabe bei der Sprachübersetzung ist die Angabe von Zielübersetzungssprachen, wobei mindestens eine erforderlich ist, jedoch mehrere unterstützt werden. Im folgenden Codeausschnitt sind sowohl Französisch als auch Deutsch als Zielübersetzungssprachen angegeben.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig->SetSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig->AddTargetLanguage("fr");
    translationConfig->AddTargetLanguage("de");
}
```

Mit jedem Aufruf von [`AddTargetLanguage`][addlang] wird eine neue Zielübersetzungssprache angegeben. Anders ausgedrückt: Wenn für die Ausgangssprache eine Spracherkennung erfolgt, wird im Rahmen des daraus resultierenden Übersetzungsvorgangs eine Zielübersetzung bereitgestellt.

## <a name="initialize-a-translation-recognizer"></a>Initialisieren einer Übersetzungserkennung

Nach der Erstellung einer Sprachkonfiguration ([`SpeechTranslationConfig`][config]) muss eine Spracherkennung ([`TranslationRecognizer`][recognizer]) initialisiert werden. Wenn Sie eine Spracherkennung ([`TranslationRecognizer`][recognizer]) initialisieren, müssen Sie Ihre Sprachkonfiguration (`translationConfig`) übergeben. Das Konfigurationsobjekt stellt Anmeldeinformationen bereit, die der Speech-Dienst zur Überprüfung Ihrer Anforderung benötigt.

Wenn Sie für die Spracherkennung das Standardmikrofon Ihres Geräts verwenden, sollte [`TranslationRecognizer`][recognizer] wie folgt aussehen:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
}
```

Wenn Sie das Audioeingabegerät angeben möchten, müssen Sie eine Audiokonfiguration ([`AudioConfig`][audioconfig]) erstellen und beim Initialisieren Ihrer Spracherkennung ([`TranslationRecognizer`][recognizer]) den Parameter `audioConfig` angeben.

> [!TIP]
> Informationen zum Abrufen der Geräte-ID für Ihr Audioeingabegerät finden Sie [hier](../../../how-to-select-audio-input-devices.md).

Verweisen Sie zunächst auf das `AudioConfig`-Objekt. Gehen Sie dabei wie folgt vor:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

Eine Audiokonfiguration (`audioConfig`) ist auch erforderlich, wenn Sie anstelle eines Mikrofons eine Audiodatei verwenden möchten. In diesem Fall muss beim Erstellen einer Audiokonfiguration ([`AudioConfig`][audioconfig]) allerdings `FromWavFileInput` (anstelle von `FromDefaultMicrophoneInput`) aufgerufen und der Parameter `filename` übergeben werden.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromWavFileInput("YourAudioFile.wav");
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Übersetzen von gesprochener Sprache

Zum Übersetzen von Sprache benötigt das Speech SDK eine Audioeingabe von einem Mikrofon oder einer Audiodatei. Die Spracherkennung erfolgt vor der Sprachübersetzung. Nachdem alle Objekte initialisiert wurden, verwenden Sie die Funktion „Einmal erkennen“, und rufen Sie das Ergebnis ab.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    string fromLanguage = "en-US";
    string toLanguages[3] = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

Weitere Informationen zur Spracherkennung finden Sie unter [Grundlegendes zur Spracherkennung](../../../get-started-speech-to-text.md).

## <a name="synthesize-translations"></a>Synthetisieren von Übersetzungen

Nach erfolgreicher Spracherkennung und -übersetzung werden alle Übersetzungen in einem Wörterbuch bereitgestellt. Der Wörterbuchschlüssel [`Translations`][translations] ist die Zielübersetzungssprache, und der Wert ist der übersetzte Text. Erkannte Sprache kann übersetzt und dann in einer anderen Sprache synthetisiert werden (Sprache-zu-Sprache).

### <a name="event-based-synthesis"></a>Ereignisbasierte Synthese

Das `TranslationRecognizer`-Objekt macht ein einzelnes `Synthesizing`-Ereignis verfügbar. Das Ereignis wird mehrmals ausgelöst und bietet einen Mechanismus, mit dem das synthetische Audioformat aus dem Übersetzungserkennungsergebnis abgerufen werden kann. Weitere Informationen zum Übersetzen in mehrere Sprachen finden Sie unter [Manuelle Synthese](#manual-synthesis). Geben Sie die Synthesestimme an, indem Sie einen [`SetVoiceName`][voicename] zuweisen und einen Ereignishandler für das `Synthesizing`-Ereignis zum Abrufen der Audioinhalte bereitstellen. Im folgenden Beispiel wird die übersetzte Audiodatei als *WAV* -Datei gespeichert.

> [!IMPORTANT]
> Die ereignisbasierte Synthese funktioniert nur mit einer einzigen Übersetzung. Fügen Sie deshalb **nur eine** Zielübersetzungssprache hinzu. Zudem muss die Sprache in [`SetVoiceName`][voicename] mit der Zielübersetzungssprache übereinstimmen. So wird `"de"` beispielsweise `"de-DE-Hedda"` zugeordnet.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguage = "de";
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    translationConfig->AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig->SetVoiceName("de-DE-Hedda");

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    recognizer->Synthesizing.Connect([](const TranslationSynthesisEventArgs& e)
        {
            auto audio = e.Result->Audio;
            auto size = audio.size();
            cout << "Audio synthesized: " << size << " byte(s)" << (size == 0 ? "(COMPLETE)" : "") << std::endl;

            if (size > 0) {
                ofstream file("translation.wav", ios::out | ios::binary);
                auto audioData = audio.data();
                file.write((const char*)audioData, sizeof(audio[0]) * size);
                file.close();
            }
        });

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

### <a name="manual-synthesis"></a>Manuelle Synthese

Das [`Translations`][translations]-Wörterbuch kann verwendet werden, um Audiodaten aus dem Übersetzungstext zu synthetisieren. Durchlaufen Sie die Übersetzungen, und synthetisieren Sie die jeweiligen Übersetzungen. Beim Erstellen einer `SpeechSynthesizer`-Instanz muss die [`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename]-Eigenschaft des `SpeechConfig`-Objekts auf die gewünschte Stimme festgelegt werden. Im folgenden Beispiel wird in fünf Sprachen übersetzt. Jede Übersetzung wird anschließend in eine Audiodatei in der entsprechenden neuronalen Sprache synthetisiert.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        map<string, string> languageToVoiceMap;
        languageToVoiceMap["de"] = "de-DE-KatjaNeural";
        languageToVoiceMap["en"] = "en-US-AriaNeural";
        languageToVoiceMap["it"] = "it-IT-ElsaNeural";
        languageToVoiceMap["pt"] = "pt-BR-FranciscaNeural";
        languageToVoiceMap["zh-Hans"] = "zh-CN-XiaoxiaoNeural";

        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;

            auto speech_config =
                SpeechConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
            speech_config->SetSpeechSynthesisVoiceName(languageToVoiceMap[language]);

            auto audio_config = AudioConfig::FromWavFileOutput(language + "-translation.wav");
            auto synthesizer = SpeechSynthesizer::FromConfig(speech_config, audio_config);

            synthesizer->SpeakTextAsync(translation).get();
        }
    }
}
```

Weitere Informationen zur Sprachsynthese finden Sie unter [Grundlegendes zur Sprachsynthese](../../../get-started-text-to-speech.md).

[config]: /cpp/cognitive-services/speech/translation-speechtranslationconfig
[audioconfig]: /cpp/cognitive-services/speech/audio-audioconfig
[recognizer]: /cpp/cognitive-services/speech/translation-translationrecognizer
[recognitionlang]: /cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage
[addlang]: /cpp/cognitive-services/speech/translation-speechtranslationconfig#addtargetlanguage
[translations]: /cpp/cognitive-services/speech/translation-translationrecognitionresult#translations
[voicename]: /cpp/cognitive-services/speech/translation-speechtranslationconfig#setvoicename
[speechsynthesisvoicename]: /cpp/cognitive-services/speech/speechconfig#setspeechsynthesisvoicename