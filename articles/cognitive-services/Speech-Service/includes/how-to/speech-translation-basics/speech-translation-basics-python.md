---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 388bc634ccc798a91985aec7367c51c61ed12507
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551636"
---
Eines der zentralen Features des Speech-Diensts ist die Fähigkeit, menschliche Sprache zu erkennen und in andere Sprachen zu übersetzen. In diesem Schnellstart erfahren Sie, wie Sie das Speech SDK in Ihren Apps und Produkten verwenden, um hochwertige Sprachübersetzungen durchzuführen. In diesem Schnellstart werden folgende Themen behandelt:

* Übersetzen von Sprache in Text
* Übersetzen von Sprache in mehrere Zielsprachen
* Durchführen einer direkten Übersetzung von Sprache in Sprache

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [Python-Schnellstartbeispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python) zu, falls Sie direkt zum Beispielcode springen möchten.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Zuallererst muss das Speech SDK installiert werden. Folgen Sie je nach Plattform den Anweisungen im Abschnitt <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Abrufen des Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span></a> des Artikels _Abrufen des Speech SDK_.

## <a name="import-dependencies"></a>Importieren von Abhängigkeiten

Fügen Sie die folgenden `import`-Anweisungen oben in der Python-Codedatei ein, um die Beispiele in diesem Artikel auszuführen.

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>Vertrauliche Daten und Umgebungsvariablen

Der Beispielquellcode in diesem Artikel benötigt Umgebungsvariablen zum Speichern vertraulicher Daten, wie z. B. den Schlüssel und die Region des Speech-Ressourcenabonnements. Die Python-Codedatei enthält zwei Werte, die von den Umgebungsvariablen des Hostcomputers zugewiesen werden, nämlich `SPEECH__SUBSCRIPTION__KEY` und `SPEECH__SERVICE__REGION`. Beide Variablen befinden sich im globalen Bereich und sind daher innerhalb der Funktionsdefinition der Codedatei zugänglich. Weitere Informationen zu Umgebungsvariablen finden Sie unter [Umgebungsvariablen und Anwendungskonfiguration](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
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

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>Ändern der Ausgangssprache

Eine häufige Aufgabe bei der Sprachübersetzung ist die Angabe der Eingabesprache (Quellsprache). Im folgenden Beispiel wird gezeigt, wie Sie die Eingabesprache auf Italienisch festlegen. Interagieren Sie in Ihrem Code mit der [`SpeechTranslationConfig`][config]-Instanz, und weisen Sie die `speech_recognition_language`-Eigenschaft zu.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

Von der Eigenschaft [`speech_recognition_language`][recognitionlang] wird eine Zeichenfolge im Format „Sprache-Gebietsschema“ erwartet. Sie können einen beliebigen Wert aus der Spalte **Gebietsschema** der Liste mit den unterstützten [Gebietsschemas/Sprachen](../../../language-support.md) angeben.

## <a name="add-translation-language"></a>Hinzufügen der Übersetzungssprache

Eine weitere häufige Aufgabe bei der Sprachübersetzung ist die Angabe von Zielübersetzungssprachen, wobei mindestens eine erforderlich ist, jedoch mehrere unterstützt werden. Im folgenden Codeausschnitt sind sowohl Französisch als auch Deutsch als Zielübersetzungssprachen angegeben.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

Mit jedem Aufruf von [`add_target_language`][addlang] wird eine neue Zielübersetzungssprache angegeben. Anders ausgedrückt: Wenn für die Ausgangssprache eine Spracherkennung erfolgt, wird im Rahmen des daraus resultierenden Übersetzungsvorgangs eine Zielübersetzung bereitgestellt.

## <a name="initialize-a-translation-recognizer"></a>Initialisieren einer Übersetzungserkennung

Nach der Erstellung einer Sprachkonfiguration ([`SpeechTranslationConfig`][config]) muss eine Spracherkennung ([`TranslationRecognizer`][recognizer]) initialisiert werden. Wenn Sie eine Spracherkennung ([`TranslationRecognizer`][recognizer]) initialisieren, müssen Sie Ihre Sprachkonfiguration (`translation_config`) übergeben. Das Konfigurationsobjekt stellt Anmeldeinformationen bereit, die der Speech-Dienst zur Überprüfung Ihrer Anforderung benötigt.

Wenn Sie für die Spracherkennung das Standardmikrofon Ihres Geräts verwenden, sollte [`TranslationRecognizer`][recognizer] wie folgt aussehen:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

Wenn Sie das Audioeingabegerät angeben möchten, müssen Sie eine Audiokonfiguration ([`AudioConfig`][audioconfig]) erstellen und beim Initialisieren Ihrer Spracherkennung ([`TranslationRecognizer`][recognizer]) den Parameter `audio_config` angeben.

> [!TIP]
> Informationen zum Abrufen der Geräte-ID für Ihr Audioeingabegerät finden Sie [hier](../../../how-to-select-audio-input-devices.md).

Verweisen Sie zunächst auf das `AudioConfig`-Objekt. Gehen Sie dabei wie folgt vor:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

Eine Audiokonfiguration (`audioConfig`) ist auch erforderlich, wenn Sie anstelle eines Mikrofons eine Audiodatei verwenden möchten. In diesem Fall müssen Sie beim Erstellen einer Audiokonfiguration ([`AudioConfig`][audioconfig]) für den Aufruf allerdings `filename="path-to-file.wav"` anstelle von `use_default_microphone=True` verwenden und den Parameter `filename` angeben.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(filename="path-to-file.wav")
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

## <a name="translate-speech"></a>Übersetzen von gesprochener Sprache

Zum Übersetzen von Sprache benötigt das Speech SDK eine Audioeingabe von einem Mikrofon oder einer Audiodatei. Die Spracherkennung erfolgt vor der Sprachübersetzung. Nachdem alle Objekte initialisiert wurden, verwenden Sie die Funktion „Einmal erkennen“, und rufen Sie das Ergebnis ab.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'RECOGNIZED "{from_language}": {result.text}\n' +
            f'TRANSLATED into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

Weitere Informationen zur Spracherkennung finden Sie unter [Grundlegendes zur Spracherkennung](../../../get-started-speech-to-text.md).

## <a name="synthesize-translations"></a>Synthetisieren von Übersetzungen

Nach erfolgreicher Spracherkennung und -übersetzung werden alle Übersetzungen in einem Wörterbuch bereitgestellt. Der Wörterbuchschlüssel [`translations`][translations] ist die Zielübersetzungssprache, und der Wert ist der übersetzte Text. Erkannte Sprache kann übersetzt und dann in einer anderen Sprache synthetisiert werden (Sprache-zu-Sprache).

### <a name="event-based-synthesis"></a>Ereignisbasierte Synthese

Das `TranslationRecognizer`-Objekt macht ein einzelnes `Synthesizing`-Ereignis verfügbar. Das Ereignis wird mehrmals ausgelöst und bietet einen Mechanismus, mit dem das synthetische Audioformat aus dem Übersetzungserkennungsergebnis abgerufen werden kann. Weitere Informationen zum Übersetzen in mehrere Sprachen finden Sie unter [Manuelle Synthese](#manual-synthesis). Geben Sie die Synthesestimme an, indem Sie einen [`voice_name`][voicename] zuweisen und einen Ereignishandler für das `Synthesizing`-Ereignis zum Abrufen der Audioinhalte bereitstellen. Im folgenden Beispiel wird die übersetzte Audiodatei als *WAV*-Datei gespeichert.

> [!IMPORTANT]
> Die ereignisbasierte Synthese funktioniert nur mit einer einzigen Übersetzung. Fügen Sie deshalb **nur eine** Zielübersetzungssprache hinzu. Zudem muss die Sprache in [`voice_name`][voicename] mit der Zielübersetzungssprache übereinstimmen. So wird `"de"` beispielsweise `"de-DE-Hedda"` zugeordnet.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    # See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translation_config.voice_name = "de-DE-Hedda"

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)

    def synthesis_callback(evt):
        size = len(evt.result.audio)
        print(f'Audio synthesized: {size} byte(s) {"(COMPLETED)" if size == 0 else ""}')

        if size > 0:
            file = open('translation.wav', 'wb+')
            file.write(evt.result.audio)
            file.close()

    recognizer.synthesizing.connect(synthesis_callback)

    print(f'Say something in "{from_language}" and we\'ll translate into "{to_language}".')

    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'Recognized "{from_language}": {result.text}\n' +
            f'Translated into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

### <a name="manual-synthesis"></a>Manuelle Synthese

Das [`translations`][translations]-Wörterbuch kann verwendet werden, um Audiodaten aus dem Übersetzungstext zu synthetisieren. Durchlaufen Sie die Übersetzungen, und synthetisieren Sie die jeweiligen Übersetzungen. Beim Erstellen einer `SpeechSynthesizer`-Instanz muss die [`speech_synthesis_voice_name`][speechsynthesisvoicename]-Eigenschaft des `SpeechConfig`-Objekts auf die gewünschte Stimme festgelegt werden. Im folgenden Beispiel wird in fünf Sprachen übersetzt. Jede Übersetzung wird anschließend in eine Audiodatei in der entsprechenden neuronalen Sprache synthetisiert.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', [ 'de', 'en', 'it', 'pt', 'zh-Hans' ]

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    synthesize_translations(result=result)

def synthesize_translations(result):
    language_to_voice_map = {
        "de": "de-DE-KatjaNeural",
        "en": "en-US-AriaNeural",
        "it": "it-IT-ElsaNeural",
        "pt": "pt-BR-FranciscaNeural",
        "zh-Hans": "zh-CN-XiaoxiaoNeural"
    }
    print(f'Recognized: "{result.text}"')

    for language in result.translations:
        translation = result.translations[language]
        print(f'Translated into "{language}": {translation}')

        speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
        speech_config.speech_synthesis_voice_name = language_to_voice_map.get(language)
        
        audio_config = speechsdk.audio.AudioOutputConfig(filename=f'{language}-translation.wav')
        speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
        speech_synthesizer.speak_text_async(translation).get()

translate_speech_to_text()
```

Weitere Informationen zur Sprachsynthese finden Sie unter [Grundlegendes zur Sprachsynthese](../../../get-started-text-to-speech.md).

[config]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig
[audioconfig]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig
[recognizer]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognizer
[recognitionlang]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig
[addlang]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig#add-target-language-language--str-
[translations]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognitionresult#translations
[voicename]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig#voice-name
[speechsynthesisvoicename]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#speech-synthesis-voice-name