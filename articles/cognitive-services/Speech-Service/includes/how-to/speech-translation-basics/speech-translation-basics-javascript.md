---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 07/14/2020
ms.author: v-demjoh
ms.custom: devx-track-js
ms.openlocfilehash: c7e9f1ee6a347caa71be2057905eb4ec6e6a1b0b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424670"
---
Eines der zentralen Features des Speech-Diensts ist die Fähigkeit, menschliche Sprache zu erkennen und in andere Sprachen zu übersetzen. In diesem Schnellstart erfahren Sie, wie Sie das Speech SDK in Ihren Apps und Produkten verwenden, um hochwertige Sprachübersetzungen durchzuführen. In diesem Schnellstart werden folgende Themen behandelt:

* Übersetzen von Sprache in Text
* Übersetzen von Sprache in mehrere Zielsprachen
* Durchführen einer direkten Übersetzung von Sprache in Sprache

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [JavaScript-Schnellstartbeispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) zu, falls Sie direkt zum Beispielcode springen möchten.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Zuallererst müssen Sie das <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK für JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a> installieren. Verwenden Sie dazu die folgenden plattformspezifischen Anleitungen:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webbrowser <span class="docon docon-navigate-external x-hidden-focus"></span></a>

Verwenden Sie außerdem abhängig von der Zielumgebung eine der folgenden Funktionen:

# <a name="script"></a>[script](#tab/script)

Laden Sie die Datei *microsoft.cognitiveservices.speech.sdk.bundle.js* des <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">Speech SDK für JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a> herunter, und extrahieren Sie sie. Speichern Sie sie in einem Ordner, auf den von der HTML-Datei zugegriffen werden kann.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Verwenden Sie für einen Webbrowser das Tag `<script>`. Das Präfix `sdk` ist nicht erforderlich. Das Präfix `sdk` ist ein Alias, mit dem das Modul `require` benannt wird.

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Weitere Informationen zu `import` finden Sie unter <a href="https://javascript.info/import-export" target="_blank">Exportieren und Importieren <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Weitere Informationen zu `require` finden Sie unter <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Was ist „require“? <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="create-a-translation-configuration"></a>Erstellen einer Übersetzungskonfiguration

Um den Übersetzungsdienst über das Speech SDK aufrufen zu können, muss eine Konfiguration für die Sprachübersetzung ([`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?preserve-view=true&view=azure-node-latest)) erstellt werden. Diese Klasse enthält Informationen zu Ihrem Abonnement. Hierzu zählen etwa Ihr Schlüssel und die zugeordnete Region, der Endpunkt, der Host oder das Autorisierungstoken.

> [!NOTE]
> Eine Konfiguration ist immer erforderlich. Dabei spielt es keine Rolle, ob Sie eine Spracherkennung, eine Sprachsynthese, eine Übersetzung oder eine Absichtserkennung durchführen möchten.
Eine Sprachkonfiguration ([`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?preserve-view=true&view=azure-node-latest)) kann auf unterschiedliche Weise initialisiert werden:

* Mit einem Abonnement: Übergeben Sie einen Schlüssel und die zugeordnete Region.
* Mit einem Endpunkt: Übergeben Sie einen Endpunkt für den Speech-Dienst. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Host: Übergeben Sie eine Hostadresse. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Autorisierungstoken: Übergeben Sie ein Autorisierungstoken und die zugeordnete Region.

Hier sehen Sie, wie eine Sprachkonfiguration ([`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?preserve-view=true&view=azure-node-latest)) mit einem Schlüssel und einer Region erstellt wird. Diese Anmeldeinformationen können Sie mithilfe der Schritte unter [Kostenloses Testen des Speech-Diensts](../../../overview.md#try-the-speech-service-for-free) abrufen.

```javascript
const speechTranslationConfig = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-translator"></a>Initialisieren eines Übersetzers

Nach der Erstellung einer Sprachkonfiguration ([`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?preserve-view=true&view=azure-node-latest)) muss eine Spracherkennung ([`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?preserve-view=true&view=azure-node-latest)) initialisiert werden. Wenn Sie eine Spracherkennung ([`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?preserve-view=true&view=azure-node-latest)) initialisieren, müssen Sie Ihre Sprachkonfiguration (`speechTranslationConfig`) übergeben. Bei diesem Schritt werden die Anmeldeinformationen bereitgestellt, die der Übersetzungsdienst zur Überprüfung Ihrer Anforderung benötigt.

Wenn Sie gesprochene Sprache übersetzen, die über das Standardmikrofon Ihres Geräts bereitgestellt wird, sollte [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?preserve-view=true&view=azure-node-latest) wie folgt aussehen:

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Wenn Sie das Audioeingabegerät angeben möchten, müssen Sie eine Audiokonfiguration ([`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?preserve-view=true&view=azure-node-latest)) erstellen und beim Initialisieren Ihrer Spracherkennung ([`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?preserve-view=true&view=azure-node-latest)) den Parameter `audioConfig` angeben.

> [!TIP]
> Informationen zum Abrufen der Geräte-ID für Ihr Audioeingabegerät finden Sie [hier](../../../how-to-select-audio-input-devices.md).
Verweisen Sie wie folgt auf das `AudioConfig`-Objekt:

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

Eine Audiokonfiguration (`audioConfig`) ist auch erforderlich, wenn Sie anstelle eines Mikrofons eine Audiodatei verwenden möchten. Dies ist jedoch nur möglich, wenn Sie **Node.js** verwenden und beim Erstellen eines [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?preserve-view=true&view=azure-node-latest)-Objekts nicht `fromDefaultMicrophoneInput`, sondern `fromWavFileOutput` aufrufen und den Parameter `filename` übergeben.

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

## <a name="translate-speech"></a>Übersetzen von gesprochener Sprache

Von der Klasse [TranslationRecognizer](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?preserve-view=true&view=azure-node-latest) für das Speech SDK für JavaScript werden verschiedene Methoden für die Sprachübersetzung verfügbar gemacht.

* Einzelübersetzung (asynchron): Führt die Übersetzung in einem nicht blockierenden (asynchronen) Modus durch. Dadurch wird eine einzelne Äußerung übersetzt. Zur Erkennung des Endes einer einzelnen Äußerung wird auf Stille am Ende gelauscht oder gewartet, bis maximal 15 Sekunden an Audiodaten verarbeitet wurden.
* Fortlaufende Übersetzung (asynchron): Initialisiert asynchron einen fortlaufenden Übersetzungsvorgang. Der Benutzer registriert sich für Ereignisse und behandelt verschiedene Anwendungszustände. Zum Beenden der asynchronen fortlaufenden Übersetzung muss [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?preserve-view=true&view=azure-node-latest#stopcontinuousrecognitionasync) aufgerufen werden.

> [!NOTE]
> Weitere Informationen zum Auswählen eines Spracherkennungsmodus finden Sie [hier](../../../get-started-speech-to-text.md).
### <a name="specify-a-target-language"></a>Angeben einer Zielsprache

Zum Übersetzen müssen Sie eine Ausgangssprache und mindestens eine Zielsprache angeben.
Sie können eine Ausgangssprache mithilfe eines Gebietsschemas auswählen, das in der [Sprachübersetzungstabelle](../../../language-support.md#speech-translation) aufgeführt ist. Suchen Sie die Optionen für die übersetzte Sprache unter demselben Link. Die Optionen für Zielsprachen unterscheiden sich, je nachdem, ob Sie Text anzeigen oder übersetzte synthetische Sprache anhören möchten. Ändern Sie zum Übersetzen aus dem Englischen ins Deutsche das Übersetzungskonfigurationsobjekt:

```javascript
speechTranslationConfig.speechRecognitionLanguage = "en-US";
speechTranslationConfig.addTargetLanguage("de");
```

### <a name="single-shot-recognition"></a>Einzelerkennung

Das folgende Beispiel zeigt eine asynchrone Einzelübersetzung mit [`recognizeOnceAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?preserve-view=true&view=azure-node-latest#recognizeonceasync):

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Sie müssen etwas Code schreiben, um das Ergebnis zu behandeln. In diesem Beispiel wird [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognitionresult?preserve-view=true&view=azure-node-latest) für eine Übersetzung ins Deutsche ausgewertet:

```javascript
recognizer.recognizeOnceAsync(
  function (result) {
    let translation = result.translations.get("de");
    window.console.log(translation);
    recognizer.close();
  },
  function (err) {
    window.console.log(err);
    recognizer.close();
});
```

Der Code kann auch Updates verarbeiten, die während der Verarbeitung der Übersetzung bereitgestellt werden.
Sie können diese Updates verwenden, um visuelles Feedback zum Übersetzungsfortschritt bereitzustellen.
Die folgende [Node.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/js/node/translation.js)-JavaScript-Beispieldatei enthält Beispielcode, mit dem die während des Übersetzungsprozesses bereitgestellten Updates angezeigt werden. Der folgende Code zeigt auch Details an, die während des Übersetzungsprozesses erstellt wurden.

```javascript
recognizer.recognizing = function (s, e) {
    var str = ("(recognizing) Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " +  e.result.text +
            " Translation:");
    str += e.result.translations.get("de");
    console.log(str);
};
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " + e.result.text +
            " Translation:";
    str += e.result.translations.get("de");
    str += "\r\n";
    console.log(str);
};
```

### <a name="continuous-translation"></a>Fortlaufende Übersetzung

Die fortlaufende Übersetzung ist etwas komplexer als die Einzelerkennung. Für die kontinuierliche Erkennung müssen die Ereignisse `recognizing`, `recognized` und `canceled` abonniert werden, um die Erkennungsergebnisse zu erhalten. Sie müssen [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?preserve-view=true&view=azure-node-latest#stopcontinuousrecognitionasync) aufrufen, um die Übersetzung zu beenden Im folgenden Beispiel wird eine fortlaufende Übersetzung für eine Audioeingabedatei durchgeführt.

Als Erstes wird die Eingabe definiert und eine Spracherkennung ([`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?preserve-view=true&view=azure-node-latest)) initialisiert:

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Wir abonnieren die von [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?preserve-view=true&view=azure-node-latest) gesendeten Ereignisse:

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?preserve-view=true&view=azure-node-latest#recognizing): Signal für Ereignisse mit Übersetzungszwischenergebnissen.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?preserve-view=true&view=azure-node-latest#recognized): Signal für Ereignisse mit Übersetzungsendergebnissen. (Der Übersetzungsversuch war also erfolgreich.)
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?preserve-view=true&view=azure-node-latest#sessionstopped): Signal für Ereignisse, die das Ende einer Übersetzungssitzung (bzw. eines Übersetzungsvorgangs) angeben.
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?preserve-view=true&view=azure-node-latest#canceled): Signal für Ereignisse mit Ergebnissen einer abgebrochenen Übersetzung. (Der Übersetzungsversuch wurde also aufgrund einer direkten Abbruchanforderung oder infolge eines Transport- oder Protokollfehlers abgebrochen.)

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`TRANSLATING: Text=${e.result.text}`);
};
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`TRANSLATED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be translated.");
    }
};
recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);
    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }
    recognizer.stopContinuousRecognitionAsync();
};
recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Nach Abschluss der Einrichtung kann [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#startcontinuousrecognitionasync) aufgerufen werden.

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();
// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

## <a name="choose-a-source-language"></a>Auswählen einer Ausgangssprache

Eine häufige Aufgabe bei der Sprachübersetzung ist die Angabe der Eingabesprache (Ausgangssprache). Im folgenden Beispiel wird gezeigt, wie Sie die Eingabesprache auf Italienisch festlegen. Navigieren Sie in Ihrem Code zu [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?preserve-view=true&view=azure-node-latest), und fügen Sie direkt darunter die folgende Zeile hinzu:

```javascript
speechTranslationConfig.speechRecognitionLanguage = "it-IT";
```

Von der Eigenschaft [`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?preserve-view=true&view=azure-node-latest#speechrecognitionlanguage) wird eine Zeichenfolge im Format „Sprache-Gebietsschema“ erwartet. Sie können einen beliebigen Wert aus der Spalte **Gebietsschema** der Liste mit den unterstützten [Gebietsschemas/Sprachen](../../../language-support.md) angeben.

## <a name="choose-one-or-more-target-languages"></a>Auswählen mindestens einer Zielsprache

Das Speech SDK kann parallel in mehrere Zielsprachen übersetzen. Die verfügbaren Zielsprachen unterscheiden sich etwas von der Liste der Ausgangssprachen, und Sie geben Zielsprachen nicht mithilfe eines Gebietsschemas, sondern mithilfe eines Sprachcodes an.
Eine Liste der Sprachcodes für Textziele finden Sie in [der Sprachübersetzungstabelle auf der Seite „Sprachunterstützung“](../../../language-support.md#speech-translation). Hier finden Sie auch Details zur Übersetzung in synthetische Sprachen.

Mit dem folgenden Code wird Deutsch als Zielsprache hinzugefügt:

```javascript
translationConfig.addTargetLanguage("de");
```

Da mehrere Zielsprachenübersetzungen möglich sind, muss der Code die Zielsprache angeben, wenn das Ergebnis untersucht wird. Der folgende Code ruft Übersetzungsergebnisse für Deutsch ab.

```javascript
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " +
            sdk.ResultReason[e.result.reason] +
            " Text: " + e.result.text + " Translations:";
    var language = "de";
    str += " [" + language + "] " + e.result.translations.get(language);
    str += "\r\n";
    // show str somewhere
};
```