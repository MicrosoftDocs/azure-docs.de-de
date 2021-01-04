---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: f0335e28b3f0fd2d339aa329b92203ddb8d3216c
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425208"
---
In dieser Schnellstartanleitung werden gängige Entwurfsmuster für die Sprachsynthese per Speech SDK vermittelt. Hierzu werden zunächst eine grundlegende Konfiguration und eine einfache Synthese durchgeführt, gefolgt von komplexeren Beispielen für die Entwicklung benutzerdefinierter Anwendungen:

* Erhalten von Antworten als In-Memory-Datenstrom
* Anpassen der Abtast- und Bitrate der Ausgabe
* Übermitteln von Syntheseanforderungen mithilfe von SSML (Speech Synthesis Markup Language, Markupsprache für Sprachsynthese)
* Verwenden neuronaler Stimmen

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [JavaScript-Schnellstartbeispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech) zu, falls Sie direkt zum Beispielcode springen möchten.

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


## <a name="create-a-speech-configuration"></a>Erstellen einer Sprachkonfiguration

Um den Speech-Dienst über das Speech SDK aufrufen zu können, muss eine Sprachkonfiguration ([`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest)) erstellt werden. Diese Klasse enthält Informationen zu Ihrem Abonnement. Hierzu zählen etwa Ihr Schlüssel und die zugeordnete Region, der Endpunkt, der Host oder das Autorisierungstoken.

> [!NOTE]
> Eine Konfiguration ist immer erforderlich. Dabei spielt es keine Rolle, ob Sie eine Spracherkennung, eine Sprachsynthese, eine Übersetzung oder eine Absichtserkennung durchführen möchten.

Eine Sprachkonfiguration ([`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest)) kann auf unterschiedliche Weise initialisiert werden:

* Mit einem Abonnement: Übergeben Sie einen Schlüssel und die zugeordnete Region.
* Mit einem Endpunkt: Übergeben Sie einen Endpunkt für den Speech-Dienst. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Host: Übergeben Sie eine Hostadresse. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Autorisierungstoken: Übergeben Sie ein Autorisierungstoken und die zugeordnete Region.

In diesem Beispiel erstellen Sie das Objekt [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) mit einem Abonnementschlüssel und einer Region. Diese Anmeldeinformationen können Sie mithilfe der Schritte unter [Kostenloses Testen des Speech-Diensts](../../../overview.md#try-the-speech-service-for-free) abrufen. Sie können für den restlichen Teil dieses Artikels auch einfache Codebausteine erstellen, an denen Sie dann jeweils die entsprechenden Anpassungen vornehmen.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}

synthesizeSpeech();
```

## <a name="synthesize-speech-to-a-file"></a>Synthetisieren von Sprache in eine Datei

Als Nächstes erstellen Sie das Objekt [`SpeechSynthesizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?preserve-view=true&view=azure-node-latest), mit dem Konvertierungen von Text in Sprache und Ausgaben an Lautsprecher, in Dateien oder andere Ausgabestreams erfolgen. Für [`SpeechSynthesizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?preserve-view=true&view=azure-node-latest) werden folgende Parameter akzeptiert: das Objekt [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) und das Objekt [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?preserve-view=true&view=azure-node-latest) aus dem vorherigen Schritt, mit dem angegeben wird, wie Ausgabeergebnisse verarbeitet werden sollten.

Erstellen Sie zum Beginnen ein `AudioConfig`-Objekt, um die Ausgabe mit der statischen Funktion `fromAudioFileOutput()` in eine `.wav`-Datei zu schreiben.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

Instanziieren Sie als Nächstes ein `SpeechSynthesizer`-Objekt, und übergeben Sie Ihr `speechConfig`-Objekt und das `audioConfig`-Objekt als Parameter. Das Ausführen der Sprachsynthese und Schreiben der Daten in eine Datei ist so einfach wie das Ausführen von `speakTextAsync()` mit einer Textzeichenfolge. Der Ergebnisrückruf eignet sich gut zum Aufrufen von `synthesizer.close()` und ist sogar erforderlich, damit die Synthese ordnungsgemäß funktioniert.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Führen Sie das Programm aus. Eine synthetisierte Datei vom Typ `.wav` wird an den von Ihnen angegebenen Speicherort geschrieben. Dies ist ein gutes Beispiel für die einfachste Nutzung. Als Nächstes informieren Sie sich über die Anpassung der Ausgabe und die Verarbeitung der Ausgabeantwort als InMemory-Datenstrom für benutzerdefinierte Szenarien.

## <a name="synthesize-to-speaker-output"></a>Synthetisieren der Lautsprecherausgabe

In bestimmten Fällen kann es ratsam sein, synthetisierte Sprache direkt über einen Lautsprecher auszugeben. Instanziieren Sie zu diesem Zweck `AudioConfig`, indem Sie die statische Funktion `fromDefaultSpeakerOutput()` verwenden. Die Ausgabe erfolgt auf dem derzeit aktiven Ausgabegerät.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Abrufen des Ergebnisses als InMemory-Datenstrom

Bei vielen Entwicklungsszenarien für Sprachanwendungen benötigen Sie die sich ergebenden Audiodaten als InMemory-Datenstrom, anstatt die Daten direkt in eine Datei zu schreiben. Auf diese Weise können Sie ein benutzerdefiniertes Verhalten erzielen, z. B.:

* Abstrahieren des sich ergebenden Bytearrays als durchsuchbaren Datenstrom für benutzerdefinierte nachgelagerte Dienste
* Integrieren des Ergebnisses in andere APIs oder Dienste
* Ändern der Audiodaten, Schreiben benutzerdefinierter `.wav`-Header usw.

Diese Änderung lässt sich einfach am vorherigen Beispiel vornehmen. Entfernen Sie zunächst den Block `AudioConfig`, weil Sie das Ausgabeverhalten ab jetzt manuell verwalten, um eine bessere Steuerung zu erzielen. Übergeben Sie anschließend `undefined` für das Objekt `AudioConfig` im Konstruktor `SpeechSynthesizer`. 

> [!NOTE]
> Wenn `undefined` für `AudioConfig` übergeben wird, anstatt dieses Element wie im obigen Beispiel für die Lautsprecherausgabe wegzulassen, werden die Audiodaten auf dem derzeit aktiven Ausgabegerät nicht standardmäßig wiedergegeben.

Dieses Mal speichern Sie das Ergebnis in einer Variablen des Typs [`SpeechSynthesisResult`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult?preserve-view=true&view=azure-node-latest). Die Eigenschaft `SpeechSynthesisResult.audioData` gibt ein `ArrayBuffer`-Element der Ausgabedaten zurück. Sie können dieses `ArrayBuffer`-Element manuell verwenden.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Ab hier können Sie mit dem sich ergebenden `ArrayBuffer`-Objekt ein beliebiges benutzerdefiniertes Verhalten implementieren.

## <a name="customize-audio-format"></a>Anpassen des Audioformats

Im folgenden Abschnitt wird veranschaulicht, wie Sie die Attribute der Audioausgabe anpassen, z. B.:

* Audiodateityp
* Abtastrate
* Bittiefe

Zum Ändern des Audioformats wenden Sie die Eigenschaft `speechSynthesisOutputFormat` auf das Objekt `SpeechConfig` an. Diese Eigenschaft erwartet für `enum` den Typ [`SpeechSynthesisOutputFormat`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?preserve-view=true&view=azure-node-latest), den Sie zum Auswählen des Ausgabeformats verwenden. In den Referenzdokumenten finden Sie eine [Liste mit den verfügbaren Audioformaten](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?preserve-view=true&view=azure-node-latest).

Sie enthält verschiedene Optionen für unterschiedliche Anforderungen in Bezug auf den Dateityp. Beachten Sie, dass Rohformate wie `Raw24Khz16BitMonoPcm` gemäß Definition keine Audioheader enthalten. Nutzen Sie Rohformate nur, wenn Sie wissen, dass Ihre nachgelagerte Implementierung einen unformatierten Bitstream decodieren kann, oder wenn Sie planen, Header basierend auf Bittiefe, Abtastrate, Kanalanzahl usw. manuell zu erstellen.

In diesem Beispiel geben Sie das High-Fidelity-RIFF-Format `Riff24Khz16BitMonoPcm` an, indem Sie `speechSynthesisOutputFormat` für das Objekt `SpeechConfig` festlegen. Rufen Sie die `ArrayBuffer`-Audiodaten ähnlich wie im Beispiel im vorherigen Abschnitt ab, und interagieren Sie damit.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Beim erneuten Ausführen Ihres Programms wird eine Datei vom Typ `.wav` in den angegebenen Pfad geschrieben.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Verwenden von SSML zum Anpassen von Sprachmerkmalen

Mit der Speech Synthesis Markup Language (SSML, Markupsprache für Sprachsynthese) können Sie Tonhöhe, Aussprache, Sprechgeschwindigkeit, Lautstärke und weitere Aspekte der Ausgabe der Sprachsynthese optimieren, indem Sie Ihre Anforderungen per XML-Schema übermitteln. Dieser Abschnitt enthält einige Beispiele zur praktischen Nutzung. Eine ausführlichere Anleitung finden Sie im [Artikel zur SSML-Vorgehensweise](../../../speech-synthesis-markup.md).

Wenn Sie SSML für die Anpassung nutzen möchten, nehmen Sie eine einfache Änderung vor, um die Stimme zu wechseln.
Erstellen Sie zuerst in Ihrem Stammverzeichnis des Projekts eine neue XML-Datei für die SSML-Konfiguration. In diesem Beispiel ist dies `ssml.xml`. Das Stammelement ist immer `<speak>`. Indem Sie den Text mit einem `<voice>`-Element umschließen, können Sie die Stimme mit dem Parameter `name` ändern. In diesem Beispiel wird die Stimme in eine Stimme eines männlichen britischen Sprechers geändert. Beachten Sie, dass es sich hierbei um eine **Standardstimme** handelt, für die andere Preise und Verfügbarkeiten als für **neuronale** Stimmen gelten. Sehen Sie sich die [gesamte Liste](../../../language-support.md#standard-voices) mit den unterstützten **Standardstimmen** an.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Als Nächstes müssen Sie die Anforderung der Sprachsynthese so ändern, dass darin auf Ihre XML-Datei verwiesen wird. Die Anforderung bleibt größtenteils unverändert, aber Sie verwenden nun `speakSsmlAsync()` anstelle von `speakTextAsync()`. Von dieser Funktion wird eine XML-Zeichenfolge erwartet. Sie erstellen also zuerst eine Funktion, um eine XML-Datei zu laden und als Zeichenfolge zurückzugeben.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

Weitere Informationen zu `readFileSync` finden Sie unter <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">Node.js-Dateisystem<span class="docon docon-navigate-external x-hidden-focus"></span></a>. Ab hier ist das Ergebnisobjekt genau identisch mit vorhergehenden Beispielen.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Die Ausgabe funktioniert, aber Sie können einige zusätzliche einfache Änderungen vornehmen, um einen natürlicheren Klang zu erzielen. Das allgemeine Sprechtempo ist etwas zu hoch. Wir fügen daher das Tag `<prosody>` hinzu und verringern das Tempo auf **90 %** des Standardwerts. Darüber hinaus ist die Pause nach dem Komma im Satz etwas zu kurz und klingt unnatürlich. Fügen Sie zum Beheben dieses Problems das Tag `<break>` hinzu, um das Weitersprechen zu verzögern, und legen Sie den Zeitparameter auf **200 ms** fest. Führen Sie die Synthese erneut durch, um zu prüfen, wie sich diese Anpassungen auf die Ausgabe ausgewirkt haben.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Neuronale Stimmen

Bei neuronalen Stimmen handelt es sich um Sprachsynthesealgorithmen, die auf Deep Neural Networks basieren. Wenn Sie eine neuronale Stimme verwenden, kann die synthetisierte Sprache kaum von menschlichen Aufzeichnungen unterschieden werden. Durch natürliche, menschenähnliche Intonation und klare Aussprache von Wörtern können neuronale Stimmen die Hörermüdung bei der Interaktion mit KI-Systemen erheblich verringern.

Ändern Sie zum Wechseln zu einer neuronalen Stimme das Element `name` in eine der [Optionen für neuronale Stimmen](../../../language-support.md#neural-voices). Fügen Sie anschließend einen XML-Namespace für `mstts` hinzu, und umschließen Sie Ihren Text mit dem Tag `<mstts:express-as>`. Verwenden Sie den Parameter `style`, um den Sprechstil anzupassen. Im Beispiel wird `cheerful` verwendet. Ändern Sie dies in `customerservice` oder `chat`, um unterschiedliche Sprechstile auszuprobieren.

> [!IMPORTANT]
> Neuronale Stimmen werden **nur** für Speech-Ressourcen unterstützt, die in den Regionen *USA, Osten* , *Asien, Südosten* und *Europa, Westen* erstellt werden.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
    xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```