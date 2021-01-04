---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: 07d118073a73f539df8ea9c5428562a8cdd26db3
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96912118"
---
Die Funktion zum Erkennen und Transkribieren von menschlicher Sprache (Spracherkennung) ist eines der zentralen Features des Speech-Diensts. In diesem Schnellstart erfahren Sie, wie Sie das Speech SDK in Ihren Apps und Produkten verwenden, um hochwertige Spracherkennungen durchzuführen.

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [Java-Schnellstartbeispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre) zu, falls Sie direkt zum Beispielcode springen möchten.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Zuallererst muss das Speech SDK installiert werden. Verwenden Sie dazu die folgenden plattformspezifischen Anleitungen:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java Runtime <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Erstellen einer Sprachkonfiguration

Um den Speech-Dienst über das Speech SDK aufrufen zu können, muss eine Sprachkonfiguration ([`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig)) erstellt werden. Diese Klasse enthält Informationen zu Ihrem Abonnement. Hierzu zählen etwa Ihr Schlüssel und die zugeordnete Region, der Endpunkt, der Host oder das Autorisierungstoken. Erstellen Sie mithilfe des Schlüssels und der Region eine Sprachkonfiguration ([`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig)). Ihr Schlüssel-Regions-Paar finden Sie auf der Seite [Ermitteln von Schlüsseln und Region](../../../overview.md#find-keys-and-region).

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
    }
}
```

Es gibt noch einige andere Möglichkeiten, eine Sprachkonfiguration ([`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig)) zu initialisieren:

* Mit einem Endpunkt: Übergeben Sie einen Endpunkt für den Speech-Dienst. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Host: Übergeben Sie eine Hostadresse. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Autorisierungstoken: Übergeben Sie ein Autorisierungstoken und die zugeordnete Region.

> [!NOTE]
> Eine Konfiguration ist immer erforderlich. Dabei spielt es keine Rolle, ob Sie eine Spracherkennung, eine Sprachsynthese, eine Übersetzung oder eine Absichtserkennung durchführen möchten.

## <a name="recognize-from-microphone"></a>Erkennen über Mikrofon

Um Sprache über das Mikrofon Ihres Geräts zu erkennen, erstellen Sie mithilfe von `fromDefaultMicrophoneInput()` eine Audiokonfiguration (`AudioConfig`). Initialisieren Sie dann [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer), und übergeben Sie `audioConfig` und `config`.

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        fromMic(speechConfig);
    }

    public static void fromMic(SpeechConfig speechConfig) throws InterruptedException, ExecutionException {
        AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
        SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        System.out.println("Speak into your microphone.");
        Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
        SpeechRecognitionResult result = task.get();
        System.out.println("RECOGNIZED: Text=" + result.getText());
    }
}
```

Wenn Sie ein *bestimmtes* Audioeingabegerät verwenden möchten, müssen Sie in `AudioConfig` die Geräte-ID angeben. Informationen zum Abrufen der Geräte-ID für Ihr Audioeingabegerät finden Sie [hier](../../../how-to-select-audio-input-devices.md).

## <a name="recognize-from-file"></a>Erkennen aus Datei

Wenn Sie Sprache aus einer Audiodatei erkennen möchten, anstatt ein Mikrofon zu verwenden, müssen Sie trotzdem eine Audiokonfiguration (`AudioConfig`) erstellen. Beim Erstellen der Audiokonfiguration ([`AudioConfig`](/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig)) rufen Sie jedoch `fromWavFileInput()` (anstelle von `fromDefaultMicrophoneInput()`) auf und übergeben den Dateipfad.

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        fromFile(speechConfig);
    }

    public static void fromFile(SpeechConfig speechConfig) throws InterruptedException, ExecutionException {
        AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
        SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);
        
        Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
        SpeechRecognitionResult result = task.get();
        System.out.println("RECOGNIZED: Text=" + result.getText());
    }
}
```

## <a name="error-handling"></a>Fehlerbehandlung

In den vorherigen Beispielen wird lediglich der erkannte Text mithilfe von `result.getText()` abgerufen. Zur Behandlung von Fehlern und anderen Antworten müssen Sie jedoch Code schreiben, um das Ergebnis zu verarbeiten. Im folgenden Beispiel wird [`result.getReason()`](/java/api/com.microsoft.cognitiveservices.speech.recognitionresult.getreason) ausgewertet und:

* Das Erkennungsergebnis wird ausgegeben: `ResultReason.RecognizedSpeech`
* Wurde kein Erkennungstreffer gefunden, wird der Benutzer davon in Kenntnis gesetzt: `ResultReason.NoMatch`
* Ist ein Fehler aufgetreten, wird die Fehlermeldung ausgegeben: `ResultReason.Canceled`

```java
switch (result.getReason()) {
    case ResultReason.RecognizedSpeech:
        System.out.println("We recognized: " + result.getText());
        exitCode = 0;
        break;
    case ResultReason.NoMatch:
        System.out.println("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled: {
            CancellationDetails cancellation = CancellationDetails.fromResult(result);
            System.out.println("CANCELED: Reason=" + cancellation.getReason());

            if (cancellation.getReason() == CancellationReason.Error) {
                System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                System.out.println("CANCELED: Did you update the subscription info?");
            }
        }
        break;
}
```

## <a name="continuous-recognition"></a>Kontinuierliche Erkennung

In den vorherigen Beispielen wird Einzelerkennung verwendet, bei der eine einzelne Äußerung erkannt wird. Zur Erkennung des Endes einer einzelnen Äußerung wird auf Stille am Ende gelauscht oder gewartet, bis maximal 15 Sekunden an Audiodaten verarbeitet wurden.

Im Gegensatz dazu wird die kontinuierliche Erkennung verwendet, wenn Sie **steuern** möchten, wann die Erkennung beendet wird. Für die kontinuierliche Erkennung müssen die Ereignisse `recognizing`, `recognized` und `canceled` abonniert werden, um die Erkennungsergebnisse zu erhalten. Zum Beenden der Erkennung muss [`stopContinuousRecognitionAsync`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync) aufgerufen werden. Im folgenden Beispiel wird eine kontinuierliche Erkennung für eine Audioeingabedatei durchgeführt.

Als Erstes wird die Eingabe definiert und eine Spracherkennung ([`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer)) initialisiert:

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Als Nächstes wird eine Variable erstellt, um den Zustand der Spracherkennung zu verwalten. Zunächst deklarieren wir einen Semaphor (`Semaphore`) für den Klassenbereich.

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

Wir abonnieren die von [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer) gesendeten Ereignisse:

* [`recognizing`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing): Signal für Ereignisse mit Zwischenergebnissen der Erkennung.
* [`recognized`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized): Signal für Ereignisse mit Endergebnissen der Erkennung. (Der Erkennungsversuch war also erfolgreich.)
* [`sessionStopped`](/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped): Signal für Ereignisse, die das Ende einer Erkennungssitzung (bzw. eines Erkennungsvorgangs) angeben.
* [`canceled`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled): Signal für Ereignisse mit Ergebnissen einer abgebrochenen Erkennung. (Der Erkennungsversuch wurde also aufgrund einer direkten Abbruchanforderungen oder infolge eines Transport- oder Protokollfehlers abgebrochen.)

```java
// First initialize the semaphore.
stopTranslationWithFileSemaphore = new Semaphore(0);

recognizer.recognizing.addEventListener((s, e) -> {
    System.out.println("RECOGNIZING: Text=" + e.getResult().getText());
});

recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
    }
    else if (e.getResult().getReason() == ResultReason.NoMatch) {
        System.out.println("NOMATCH: Speech could not be recognized.");
    }
});

recognizer.canceled.addEventListener((s, e) -> {
    System.out.println("CANCELED: Reason=" + e.getReason());

    if (e.getReason() == CancellationReason.Error) {
        System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
        System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
        System.out.println("CANCELED: Did you update the subscription info?");
    }

    stopTranslationWithFileSemaphore.release();
});

recognizer.sessionStopped.addEventListener((s, e) -> {
    System.out.println("\n    Session stopped event.");
    stopTranslationWithFileSemaphore.release();
});
```

Nach Abschluss der Einrichtung kann [`startContinuousRecognitionAsync`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.startcontinuousrecognitionasync) aufgerufen werden.

```java
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync().get();

// Waits for completion.
stopTranslationWithFileSemaphore.acquire();

// Stops recognition.
recognizer.stopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Diktiermodus

Bei Verwendung der kontinuierlichen Erkennung können Sie die Diktatverarbeitung mithilfe der entsprechenden Funktion „Diktat aktivieren“ aktivieren. Dieser Modus bewirkt, dass die Sprachkonfigurationsinstanz Wortbeschreibungen von Satzstrukturen wie z. B. Interpunktion interpretiert. Beispielsweise würde die Äußerung „Leben Sie in der Stadt Fragezeichen“ als Text „Leben Sie in der Stadt?“ interpretiert.

Verwenden Sie zum Aktivieren des Diktiermodus die Methode [`enableDictation`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation) in Ihrer Sprachkonfiguration ([`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig)).

```java
config.enableDictation();
```

## <a name="change-source-language"></a>Ändern der Ausgangssprache

Eine gängige Aufgabe für die Spracherkennung ist die Angabe der Eingabesprache (Ausgangssprache). Im folgenden Beispiel wird gezeigt, wie Sie die Eingabesprache auf Französisch festlegen. Navigieren Sie in Ihrem Code zu [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig), und fügen Sie direkt darunter die folgende Zeile hinzu:

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage) ist ein Parameter, der eine Zeichenfolge als Argument akzeptiert. Sie können einen beliebigen Wert aus der Liste der unterstützten [Gebietsschemas/Sprachen](../../../language-support.md) angeben.

## <a name="improve-recognition-accuracy"></a>Verbessern der Erkennungsgenauigkeit

Begriffslisten dienen zur Identifizierung bekannter Begriffe in Audiodaten, wie beispielsweise den Namen einer Person oder einen bestimmten Ort. Durch die Bereitstellung einer Begriffsliste können Sie die Genauigkeit der Spracherkennung verbessern.

Wenn Sie beispielsweise den Befehl „Move to“ und „Ward“ als mögliches Ziel haben, das gesprochen werden kann, können Sie den Eintrag „Move to Ward“ hinzufügen. Das Hinzufügen eines Begriffs erhöht die Wahrscheinlichkeit, dass bei der Audioerkennung „Move to Ward“ anstelle von „Move toward“ erkannt wird.

Einer Begriffsliste können einzelne Wörter oder ganze Phrasen hinzugefügt werden. Bei der Erkennung wird ein Eintrag in einer Begriffsliste verwendet, um die Erkennung der Wörter und Begriffe in der Liste zu optimieren, auch wenn sich die Einträge in der Mitte der Äußerung befinden. 

> [!IMPORTANT]
> Das Begriffslistenfeature steht für folgende Sprachen zur Verfügung: en-US, de-DE, en-AU, en-CA, en-GB, es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN

Wenn Sie eine Begriffsliste verwenden möchten, erstellen Sie zunächst ein Objekt vom Typ [`PhraseListGrammar`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar), und fügen Sie anschließend mithilfe von [`AddPhrase`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_) bestimmte Wörter und Begriffe hinzu.

Änderungen an [`PhraseListGrammar`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar) werden bei der nächsten Erkennung oder nach erneuter Verbindungsherstellung mit dem Speech-Dienst wirksam.

```java
PhraseListGrammar phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Wenn Sie Ihre Begriffsliste löschen möchten, verwenden Sie Folgendes: 

```java
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Weitere Optionen zum Verbessern der Erkennungsgenauigkeit

Begriffslisten sind nur eine von mehreren Optionen zur Verbesserung der Erkennungsgenauigkeit. Sie können außerdem: 

* [Verbessern der Genauigkeit mithilfe von Custom Speech](../../../custom-speech-overview.md)
* [Verbessern der Genauigkeit mithilfe von Mandantenmodellen](../../../tutorial-tenant-model.md)