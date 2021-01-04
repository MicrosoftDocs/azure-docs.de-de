---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: adc40f8c949c50570533a19d46f4b5dafa79325c
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425339"
---
In dieser Schnellstartanleitung werden gängige Entwurfsmuster für die Sprachsynthese per Speech SDK vermittelt. Hierzu werden zunächst eine grundlegende Konfiguration und eine einfache Synthese durchgeführt, gefolgt von komplexeren Beispielen für die Entwicklung benutzerdefinierter Anwendungen:

* Erhalten von Antworten als In-Memory-Datenstrom
* Anpassen der Abtast- und Bitrate der Ausgabe
* Übermitteln von Syntheseanforderungen mithilfe von SSML (Speech Synthesis Markup Language, Markupsprache für Sprachsynthese)
* Verwenden neuronaler Stimmen

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [Python-Schnellstartbeispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python/text-to-speech) zu, falls Sie direkt zum Beispielcode springen möchten.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Zuallererst muss das Speech SDK installiert werden.

```Python
pip install azure-cognitiveservices-speech
```

Sollten unter macOS Installationsprobleme auftreten, muss ggf. zuerst der folgende Befehl ausgeführt werden:

```Python
python3 -m pip install --upgrade pip
```

Fügen Sie nach der Installation des Speech SDK oben in Ihrem Skript die folgenden import-Anweisungen ein:

```Python
from azure.cognitiveservices.speech import AudioDataStream, SpeechConfig, SpeechSynthesizer, SpeechSynthesisOutputFormat
from azure.cognitiveservices.speech.audio import AudioOutputConfig
```

## <a name="create-a-speech-configuration"></a>Erstellen einer Sprachkonfiguration

Um den Speech-Dienst über das Speech SDK aufrufen zu können, muss eine Sprachkonfiguration ([`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python)) erstellt werden. Diese Klasse enthält Informationen zu Ihrem Abonnement. Hierzu zählen etwa Ihr Schlüssel und die zugeordnete Region, der Endpunkt, der Host oder das Autorisierungstoken.

> [!NOTE]
> Eine Konfiguration ist immer erforderlich. Dabei spielt es keine Rolle, ob Sie eine Spracherkennung, eine Sprachsynthese, eine Übersetzung oder eine Absichtserkennung durchführen möchten.

Eine Sprachkonfiguration ([`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python)) kann auf unterschiedliche Weise initialisiert werden:

* Mit einem Abonnement: Übergeben Sie einen Schlüssel und die zugeordnete Region.
* Mit einem Endpunkt: Übergeben Sie einen Endpunkt für den Speech-Dienst. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Host: Übergeben Sie eine Hostadresse. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Autorisierungstoken: Übergeben Sie ein Autorisierungstoken und die zugeordnete Region.

In diesem Beispiel erstellen Sie das Objekt [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python) mit einem Abonnementschlüssel und einer Region. Diese Anmeldeinformationen können Sie mithilfe der Schritte unter [Kostenloses Testen des Speech-Diensts](../../../overview.md#try-the-speech-service-for-free) abrufen.

```python
speech_config = SpeechConfig(subscription="YourSubscriptionKey", region="YourServiceRegion")
```

## <a name="synthesize-speech-to-a-file"></a>Synthetisieren von Sprache in eine Datei

Als Nächstes erstellen Sie das Objekt [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?preserve-view=true&view=azure-python), mit dem Konvertierungen von Text in Sprache und Ausgaben an Lautsprecher, in Dateien oder andere Ausgabestreams erfolgen. Für [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?preserve-view=true&view=azure-python) werden folgende Parameter akzeptiert: das Objekt [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python) aus dem vorherigen Schritt und das Objekt [`AudioOutputConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig?preserve-view=true&view=azure-python), mit dem angegeben wird, wie Ausgabeergebnisse verarbeitet werden sollten.

Erstellen Sie zum Beginnen ein `AudioOutputConfig`-Objekt, um die Ausgabe mit dem Konstruktorparameter `filename` in eine Datei vom Typ `.wav` zu schreiben.

```python
audio_config = AudioOutputConfig(filename="path/to/write/file.wav")
```

Instanziieren Sie als Nächstes ein `SpeechSynthesizer`-Objekt, indem Sie Ihr `speech_config`-Objekt und das `audio_config`-Objekt als Parameter übergeben. Das Ausführen der Sprachsynthese und Schreiben der Daten in eine Datei ist so einfach wie das Ausführen von `speak_text_async()` mit einer Textzeichenfolge.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
synthesizer.speak_text_async("A simple test to write to a file.")
```

Führen Sie das Programm aus. Eine synthetisierte Datei vom Typ `.wav` wird an den von Ihnen angegebenen Speicherort geschrieben. Dies ist ein gutes Beispiel für die einfachste Nutzung. Als Nächstes informieren Sie sich über die Anpassung der Ausgabe und die Verarbeitung der Ausgabeantwort als InMemory-Datenstrom für benutzerdefinierte Szenarien.

## <a name="synthesize-to-speaker-output"></a>Synthetisieren der Lautsprecherausgabe

In bestimmten Fällen kann es ratsam sein, synthetisierte Sprache direkt an einen Lautsprecher auszugeben. Verwenden Sie hierzu das Beispiel im vorherigen Abschnitt, ändern Sie jedoch das `AudioOutputConfig`-Objekt, indem Sie den Parameter `filename` entfernen, und legen Sie `use_default_speaker=True` fest. Die Ausgabe erfolgt auf dem derzeit aktiven Ausgabegerät.

```python
audio_config = AudioOutputConfig(use_default_speaker=True)
```

## <a name="get-result-as-an-in-memory-stream"></a>Abrufen des Ergebnisses als InMemory-Datenstrom

Bei vielen Entwicklungsszenarien für Sprachanwendungen benötigen Sie die sich ergebenden Audiodaten als InMemory-Datenstrom, anstatt die Daten direkt in eine Datei zu schreiben. Auf diese Weise können Sie ein benutzerdefiniertes Verhalten erzielen, z. B.:

* Abstrahieren des sich ergebenden Bytearrays als durchsuchbaren Datenstrom für benutzerdefinierte nachgelagerte Dienste
* Integrieren des Ergebnisses in andere APIs oder Dienste
* Ändern der Audiodaten, Schreiben benutzerdefinierter `.wav`-Header usw.

Diese Änderung lässt sich einfach am vorherigen Beispiel vornehmen. Entfernen Sie zunächst das `AudioConfig`-Objekt, weil Sie das Ausgabeverhalten ab jetzt manuell verwalten, um eine bessere Steuerung zu erzielen. Übergeben Sie anschließend `None` für das Objekt `AudioConfig` im Konstruktor `SpeechSynthesizer`. 

> [!NOTE]
> Wenn `None` für `AudioConfig` übergeben wird, anstatt dieses Element wie im obigen Beispiel für die Lautsprecherausgabe wegzulassen, werden die Audiodaten auf dem derzeit aktiven Ausgabegerät nicht standardmäßig wiedergegeben.

Dieses Mal speichern Sie das Ergebnis in einer Variablen des Typs [`SpeechSynthesisResult`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult?preserve-view=true&view=azure-python). Die `audio_data`-Eigenschaft enthält ein `bytes`-Objekt der Ausgabedaten. Sie können dieses Objekt manuell nutzen oder die [`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?preserve-view=true&view=azure-python)-Klasse verwenden, um den InMemory-Datenstrom zu verwalten. In diesem Beispiel verwenden Sie den Konstruktor `AudioDataStream`, um einen Datenstrom aus dem Ergebnis abzurufen.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = synthesizer.speak_text_async("Getting the response as an in-memory stream.").get()
stream = AudioDataStream(result)
```

Ab hier können Sie mit dem sich ergebenden `stream`-Objekt ein beliebiges benutzerdefiniertes Verhalten implementieren.

## <a name="customize-audio-format"></a>Anpassen des Audioformats

Im folgenden Abschnitt wird veranschaulicht, wie Sie die Attribute der Audioausgabe anpassen, z. B.:

* Audiodateityp
* Abtastrate
* Bittiefe

Zum Ändern des Audioformats wenden Sie die Funktion `set_speech_synthesis_output_format()` auf das Objekt `SpeechConfig` an. Diese Funktion erwartet für `enum` den Typ [`SpeechSynthesisOutputFormat`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?preserve-view=true&view=azure-python), den Sie zum Auswählen des Ausgabeformats verwenden. In den Referenzdokumenten finden Sie eine [Liste mit den verfügbaren Audioformaten](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?preserve-view=true&view=azure-python).

Sie enthält verschiedene Optionen für unterschiedliche Anforderungen in Bezug auf den Dateityp. Beachten Sie, dass Rohformate wie `Raw24Khz16BitMonoPcm` gemäß Definition keine Audioheader enthalten. Nutzen Sie Rohformate nur, wenn Sie wissen, dass Ihre nachgelagerte Implementierung einen unformatierten Bitstream decodieren kann, oder wenn Sie planen, Header basierend auf Bittiefe, Abtastrate, Kanalanzahl usw. manuell zu erstellen.

In diesem Beispiel geben Sie das High-Fidelity-RIFF-Format `Riff24Khz16BitMonoPcm` an, indem Sie `SpeechSynthesisOutputFormat` für das Objekt `SpeechConfig` festlegen. Ähnlich wie im Beispiel im vorherigen Abschnitt nutzen Sie [`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?preserve-view=true&view=azure-python), um einen InMemory-Datenstrom des Ergebnisses zu erhalten, das Sie anschließend in eine Datei schreiben.


```python
speech_config.set_speech_synthesis_output_format(SpeechSynthesisOutputFormat["Riff24Khz16BitMonoPcm"])
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

result = synthesizer.speak_text_async("Customizing audio output format.").get()
stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

Beim erneuten Ausführen Ihres Programms wird eine benutzerdefinierte Datei vom Typ `.wav` in den angegebenen Pfad geschrieben.

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

Als Nächstes müssen Sie die Anforderung der Sprachsynthese so ändern, dass darin auf Ihre XML-Datei verwiesen wird. Die Anforderung bleibt größtenteils unverändert, aber Sie verwenden nun `speak_ssml_async()` anstelle der `speak_text_async()`-Funktion. Diese Funktion erwartet eine XML-Zeichenfolge, weshalb Sie Ihre SSML-Konfiguration zunächst als Zeichenfolge lesen. Ab hier ist das Ergebnisobjekt genau identisch mit vorhergehenden Beispielen.

> [!NOTE]
> Wenn `ssml_string` am Anfang der Zeichenfolge `ï»¿` enthält, müssen Sie das BOM-Format entfernen, andernfalls gibt der Dienst einen Fehler zurück. Hierzu müssen Sie den Parameter `encoding` wie folgt festlegen: `open("ssml.xml", "r", encoding="utf-8-sig")`.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

ssml_string = open("ssml.xml", "r").read()
result = synthesizer.speak_ssml_async(ssml_string).get()

stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
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
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```