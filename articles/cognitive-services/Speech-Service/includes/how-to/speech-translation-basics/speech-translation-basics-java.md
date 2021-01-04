---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: 050ba52115a3d46963ea91095025b43ead03e5fc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026419"
---
Eines der zentralen Features des Speech-Diensts ist die Fähigkeit, menschliche Sprache zu erkennen und in andere Sprachen zu übersetzen. In diesem Schnellstart erfahren Sie, wie Sie das Speech SDK in Ihren Apps und Produkten verwenden, um hochwertige Sprachübersetzungen durchzuführen. In diesem Schnellstart werden folgende Themen behandelt:

* Übersetzen von Sprache in Text
* Übersetzen von Sprache in mehrere Zielsprachen
* Durchführen einer direkten Übersetzung von Sprache in Sprache

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [Java-Schnellstartbeispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre/translate-speech-to-text) zu, falls Sie direkt zum Beispielcode springen möchten.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Zuallererst muss das Speech SDK installiert werden. Folgen Sie je nach Plattform den Anweisungen im Abschnitt <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Abrufen des Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span></a> des Artikels _Abrufen des Speech SDK_.

## <a name="import-dependencies"></a>Importieren von Abhängigkeiten

Fügen Sie die folgenden `import`-Anweisungen oben in der **Java*-Codedatei ein, um die Beispiele in diesem Artikel auszuführen.

```java
package speech;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.*;
import com.microsoft.cognitiveservices.speech.translation.*;
```

## <a name="sensitive-data-and-environment-variables"></a>Sensible Daten und Umgebungsvariablen

Der Beispielquellcode in diesem Artikel hängt von Umgebungsvariablen zum Speichern sensibler Daten ab, wie z. B. dem Schlüssel und der Region des Sprachressourcenabonnements. Die Java-Codedatei enthält zwei `static final String`-Werte, die von den Umgebungsvariablen des Hostcomputers zugewiesen werden, nämlich `SPEECH__SUBSCRIPTION__KEY` und `SPEECH__SERVICE__REGION`. Beide Felder befinden sich im Klassenbereich, sodass sie innerhalb vom Methodentext der Klasse zugänglich sind. Weitere Informationen zu Umgebungsvariablen finden Sie unter [Umgebungsvariablen und Anwendungskonfiguration](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SUBSCRIPTION__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) { }
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

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SERVICE__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) {
        try {
            translateSpeech();
            System.exit(0);
        } catch (Exception ex) {
            System.out.println(ex);
            System.exit(1);
        }
    }

    static void translateSpeech() {
        SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription(
            SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Ändern der Ausgangssprache

Eine häufige Aufgabe bei der Sprachübersetzung ist die Angabe der Eingabesprache (Quellsprache). Im folgenden Beispiel wird gezeigt, wie Sie die Eingabesprache auf Italienisch festlegen. Interagieren Sie in Ihrem Code mit der [`SpeechTranslationConfig`][config]-Instanz, und rufen Sie die `setSpeechRecognitionLanguage`-Methode auf.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    // Source (input) language
    translationConfig.setSpeechRecognitionLanguage("it-IT");
}
```

Von der Funktion [`setSpeechRecognitionLanguage`][recognitionlang] wird eine Zeichenfolge im Format „Sprache-Gebietsschema“ erwartet. Sie können einen beliebigen Wert aus der Spalte **Gebietsschema** der Liste mit den unterstützten [Gebietsschemas/Sprachen](../../../language-support.md) angeben.

## <a name="add-translation-language"></a>Hinzufügen der Übersetzungssprache

Eine weitere häufige Aufgabe bei der Sprachübersetzung ist die Angabe von Zielübersetzungssprachen, wobei mindestens eine erforderlich ist, jedoch mehrere unterstützt werden. Im folgenden Codeausschnitt sind sowohl Französisch als auch Deutsch als Zielübersetzungssprachen angegeben.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    translationConfig.setSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.addTargetLanguage("fr");
    translationConfig.addTargetLanguage("de");
}
```

Mit jedem Aufruf von [`addTargetLanguage`][addlang] wird eine neue Zielübersetzungssprache angegeben. Anders ausgedrückt: Wenn für die Ausgangssprache eine Spracherkennung erfolgt, wird im Rahmen des daraus resultierenden Übersetzungsvorgangs eine Zielübersetzung bereitgestellt.

## <a name="initialize-a-translation-recognizer"></a>Initialisieren einer Übersetzungserkennung

Nach der Erstellung einer Sprachkonfiguration ([`SpeechTranslationConfig`][config]) muss eine Spracherkennung ([`TranslationRecognizer`][recognizer]) initialisiert werden. Wenn Sie eine Spracherkennung ([`TranslationRecognizer`][recognizer]) initialisieren, müssen Sie Ihre Sprachkonfiguration (`translationConfig`) übergeben. Das Konfigurationsobjekt stellt Anmeldeinformationen bereit, die der Speech-Dienst zur Überprüfung Ihrer Anforderung benötigt.

Wenn Sie für die Spracherkennung das Standardmikrofon Ihres Geräts verwenden, sollte [`TranslationRecognizer`][recognizer] wie folgt aussehen:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
    }
}
```

Wenn Sie das Audioeingabegerät angeben möchten, müssen Sie eine Audiokonfiguration ([`AudioConfig`][audioconfig]) erstellen und beim Initialisieren Ihrer Spracherkennung ([`TranslationRecognizer`][recognizer]) den Parameter `audioConfig` angeben.

> [!TIP]
> Informationen zum Abrufen der Geräte-ID für Ihr Audioeingabegerät finden Sie [hier](../../../how-to-select-audio-input-devices.md).

Verweisen Sie zunächst auf das `AudioConfig`-Objekt. Gehen Sie dabei wie folgt vor:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

Eine Audiokonfiguration (`audioConfig`) ist auch erforderlich, wenn Sie anstelle eines Mikrofons eine Audiodatei verwenden möchten. In diesem Fall muss beim Erstellen einer Audiokonfiguration ([`AudioConfig`][audioconfig]) allerdings `fromWavFileInput` (anstelle von `fromDefaultMicrophoneInput`) aufgerufen und der Parameter `filename` übergeben werden.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

## <a name="translate-speech"></a>Übersetzen von gesprochener Sprache

Zum Übersetzen von Sprache benötigt das Speech SDK eine Audioeingabe von einem Mikrofon oder einer Audiodatei. Die Spracherkennung erfolgt vor der Sprachübersetzung. Nachdem alle Objekte initialisiert wurden, verwenden Sie die Funktion „Einmal erkennen“, und rufen Sie das Ergebnis ab.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                System.out.printf("Translated into '%s': %s\n", pair.getKey(), pair.getValue());
            }
        }
    }
}
```

Weitere Informationen zur Spracherkennung finden Sie unter [Grundlegendes zur Spracherkennung](../../../get-started-speech-to-text.md).

## <a name="synthesize-translations"></a>Synthetisieren von Übersetzungen

Nach erfolgreicher Spracherkennung und -übersetzung werden alle Übersetzungen in einem Wörterbuch bereitgestellt. Die [`getTranslations`][translations]-Funktion gibt ein Wörterbuch mit dem Schlüssel als Zielübersetzungssprache zurück, und der Wert ist der übersetzte Text. Erkannte Sprache kann übersetzt und dann in einer anderen Sprache synthetisiert werden (Sprache-zu-Sprache).

### <a name="event-based-synthesis"></a>Ereignisbasierte Synthese

Das `TranslationRecognizer`-Objekt macht ein einzelnes `synthesizing`-Ereignis verfügbar. Das Ereignis wird mehrmals ausgelöst und bietet einen Mechanismus, mit dem das synthetische Audioformat aus dem Übersetzungserkennungsergebnis abgerufen werden kann. Weitere Informationen zum Übersetzen in mehrere Sprachen finden Sie unter [Manuelle Synthese](#manual-synthesis). Geben Sie die Synthesestimme an, indem Sie einen [`setVoiceName`][voicename] zuweisen und einen Ereignishandler für das `synthesizing`-Ereignis zum Abrufen der Audioinhalte bereitstellen. Im folgenden Beispiel wird die übersetzte Audiodatei als *WAV*-Datei gespeichert.

> [!IMPORTANT]
> Die ereignisbasierte Synthese funktioniert nur mit einer einzigen Übersetzung. Fügen Sie deshalb **nur eine** Zielübersetzungssprache hinzu. Zudem muss die Sprache in [`setVoiceName`][voicename] mit der Zielübersetzungssprache übereinstimmen. So wird `"de"` beispielsweise `"de-DE-Hedda"` zugeordnet.

```java
static void translateSpeech() throws ExecutionException, FileNotFoundException, InterruptedException, IOException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    String fromLanguage = "en-US";
    String toLanguage = "de";
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    translationConfig.addTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.setVoiceName("de-DE-Hedda");

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        recognizer.synthesizing.addEventListener((s, e) -> {
            byte[] audio = e.getResult().getAudio();
            int size = audio.length;
            System.out.println("Audio synthesized: " + size + " byte(s)" + (size == 0 ? "(COMPLETE)" : ""));

            if (size > 0) {
                try (FileOutputStream file = new FileOutputStream("translation.wav")) {
                    file.write(audio);
                } catch (IOException ex) {
                    ex.printStackTrace();
                }
            }
        });

        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);
            }
        }
    }
}
```

### <a name="manual-synthesis"></a>Manuelle Synthese

Das [`getTranslations`][translations]-Funktion gibt ein Wörterbuch zurück, das zum Synthetisieren von Audiodaten aus dem Übersetzungstext verwendet werden kann. Durchlaufen Sie die Übersetzungen, und synthetisieren Sie die jeweiligen Übersetzungen. Beim Erstellen einer `SpeechSynthesizer`-Instanz muss die [`setSpeechSynthesisVoiceName`][speechsynthesisvoicename]-Eigenschaft des `SpeechConfig`-Objekts auf die gewünschte Stimme festgelegt werden. Im folgenden Beispiel wird in fünf Sprachen übersetzt. Jede Übersetzung wird anschließend in eine Audiodatei in der entsprechenden neuronalen Sprache synthetisiert.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
            Map<String, String> languageToVoiceMap = new HashMap<String, String>();
            languageToVoiceMap.put("de", "de-DE-KatjaNeural");
            languageToVoiceMap.put("en", "en-US-AriaNeural");
            languageToVoiceMap.put("it", "it-IT-ElsaNeural");
            languageToVoiceMap.put("pt", "pt-BR-FranciscaNeural");
            languageToVoiceMap.put("zh-Hans", "zh-CN-XiaoxiaoNeural");

            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);

                SpeechConfig speechConfig =
                    SpeechConfig.fromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
                speechConfig.setSpeechSynthesisVoiceName(languageToVoiceMap.get(language));

                AudioConfig audioConfig = AudioConfig.fromWavFileOutput(language + "-translation.wav");
                try (SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig)) {
                    synthesizer.SpeakTextAsync(translation).get();
                }
            }
        }
    }
}
```

Weitere Informationen zur Sprachsynthese finden Sie unter [Grundlegendes zur Sprachsynthese](../../../get-started-text-to-speech.md).

[config]: /java/api/com.microsoft.cognitiveservices.speech.translation.SpeechTranslationConfig
[audioconfig]: /java/api/com.microsoft.cognitiveservices.speech.audio.AudioConfig
[recognizer]: /java/api/com.microsoft.cognitiveservices.speech.translation.TranslationRecognizer
[recognitionlang]: /java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage
[addlang]: /java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.addtargetlanguage
[translations]: /java/api/com.microsoft.cognitiveservices.speech.translation.translationrecognitionresult.gettranslations
[voicename]: /java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.setvoicename
[speechsynthesisvoicename]: /java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechsynthesisvoicename