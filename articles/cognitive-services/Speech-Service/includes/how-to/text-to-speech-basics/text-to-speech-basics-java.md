---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: 2da56514870f279da342976ac074697be5196021
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026418"
---
In dieser Schnellstartanleitung werden gängige Entwurfsmuster für die Sprachsynthese per Speech SDK vermittelt. Hierzu werden zunächst eine grundlegende Konfiguration und eine einfache Synthese durchgeführt, gefolgt von komplexeren Beispielen für die Entwicklung benutzerdefinierter Anwendungen:

* Erhalten von Antworten als In-Memory-Datenstrom
* Anpassen der Abtast- und Bitrate der Ausgabe
* Übermitteln von Syntheseanforderungen mithilfe von SSML (Speech Synthesis Markup Language, Markupsprache für Sprachsynthese)
* Verwenden neuronaler Stimmen

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [Java-Schnellstartbeispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre/text-to-speech) zu, falls Sie direkt zum Beispielcode springen möchten.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Zuallererst muss das Speech SDK installiert werden. Verwenden Sie dazu die folgenden plattformspezifischen Anleitungen:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java Runtime <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Importieren von Abhängigkeiten

Fügen Sie zum Ausführen der Beispiele in diesem Artikel oben in Ihrem Skript die folgenden Importanweisungen ein.

```java
import com.microsoft.cognitiveservices.speech.AudioDataStream;
import com.microsoft.cognitiveservices.speech.SpeechConfig;
import com.microsoft.cognitiveservices.speech.SpeechSynthesizer;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisOutputFormat;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisResult;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;

import java.io.*;
import java.util.Scanner;
```

## <a name="create-a-speech-configuration"></a>Erstellen einer Sprachkonfiguration

Um den Speech-Dienst über das Speech SDK aufrufen zu können, muss eine Sprachkonfiguration ([`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig)) erstellt werden. Diese Klasse enthält Informationen zu Ihrem Abonnement. Hierzu zählen etwa Ihr Schlüssel und die zugeordnete Region, der Endpunkt, der Host oder das Autorisierungstoken.

> [!NOTE]
> Eine Konfiguration ist immer erforderlich. Dabei spielt es keine Rolle, ob Sie eine Spracherkennung, eine Sprachsynthese, eine Übersetzung oder eine Absichtserkennung durchführen möchten.

Eine Sprachkonfiguration ([`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig)) kann auf unterschiedliche Weise initialisiert werden:

* Mit einem Abonnement: Übergeben Sie einen Schlüssel und die zugeordnete Region.
* Mit einem Endpunkt: Übergeben Sie einen Endpunkt für den Speech-Dienst. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Host: Übergeben Sie eine Hostadresse. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Autorisierungstoken: Übergeben Sie ein Autorisierungstoken und die zugeordnete Region.

In diesem Beispiel erstellen Sie das Objekt [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) mit einem Abonnementschlüssel und einer Region. Diese Anmeldeinformationen können Sie mithilfe der Schritte unter [Kostenloses Testen des Speech-Diensts](../../../overview.md#try-the-speech-service-for-free) abrufen. Sie können für den restlichen Teil dieses Artikels auch einfache Codebausteine erstellen, an denen Sie dann jeweils die entsprechenden Anpassungen vornehmen.

```java
public class Program 
{
    public static void main(String[] args) {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>Synthetisieren von Sprache in eine Datei

Als Nächstes erstellen Sie das Objekt [`SpeechSynthesizer`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer), mit dem Konvertierungen von Text in Sprache und Ausgaben an Lautsprecher, in Dateien oder andere Ausgabestreams erfolgen. Für [`SpeechSynthesizer`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer) werden folgende Parameter akzeptiert: das Objekt [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) und das Objekt [`AudioConfig`](/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig) aus dem vorherigen Schritt, mit dem angegeben wird, wie Ausgabeergebnisse verarbeitet werden sollten.

Erstellen Sie zum Beginnen ein `AudioConfig`-Objekt, um die Ausgabe mit der statischen Funktion `fromWavFileOutput()` in eine `.wav`-Datei zu schreiben.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");
}
```

Instanziieren Sie als Nächstes ein `SpeechSynthesizer`-Objekt, und übergeben Sie Ihr `speechConfig`-Objekt und das `audioConfig`-Objekt als Parameter. Das Ausführen der Sprachsynthese und Schreiben der Daten in eine Datei ist so einfach wie das Ausführen von `SpeakText()` mit einer Textzeichenfolge.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("A simple test to write to a file.");
}
```

Führen Sie das Programm aus. Eine synthetisierte Datei vom Typ `.wav` wird an den von Ihnen angegebenen Speicherort geschrieben. Dies ist ein gutes Beispiel für die einfachste Nutzung. Als Nächstes informieren Sie sich über die Anpassung der Ausgabe und die Verarbeitung der Ausgabeantwort als InMemory-Datenstrom für benutzerdefinierte Szenarien.

## <a name="synthesize-to-speaker-output"></a>Synthetisieren der Lautsprecherausgabe

In bestimmten Fällen kann es ratsam sein, synthetisierte Sprache direkt über einen Lautsprecher auszugeben. Instanziieren Sie zu diesem Zweck `AudioConfig`, indem Sie die statische Funktion `fromDefaultSpeakerOutput()` verwenden. Die Ausgabe erfolgt auf dem derzeit aktiven Ausgabegerät.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Abrufen des Ergebnisses als InMemory-Datenstrom

Bei vielen Entwicklungsszenarien für Sprachanwendungen benötigen Sie die sich ergebenden Audiodaten als InMemory-Datenstrom, anstatt die Daten direkt in eine Datei zu schreiben. Auf diese Weise können Sie ein benutzerdefiniertes Verhalten erzielen, z. B.:

* Abstrahieren des sich ergebenden Bytearrays als durchsuchbaren Datenstrom für benutzerdefinierte nachgelagerte Dienste
* Integrieren des Ergebnisses in andere APIs oder Dienste
* Ändern der Audiodaten, Schreiben benutzerdefinierter `.wav`-Header usw.

Diese Änderung lässt sich einfach am vorherigen Beispiel vornehmen. Entfernen Sie zunächst den Block `AudioConfig`, weil Sie das Ausgabeverhalten ab jetzt manuell verwalten, um eine bessere Steuerung zu erzielen. Übergeben Sie anschließend `null` für das Objekt `AudioConfig` im Konstruktor `SpeechSynthesizer`. 

> [!NOTE]
> Wenn `null` für `AudioConfig` übergeben wird, anstatt dieses Element wie im obigen Beispiel für die Lautsprecherausgabe wegzulassen, werden die Audiodaten auf dem derzeit aktiven Ausgabegerät nicht standardmäßig wiedergegeben.

Hierbei speichern Sie das Ergebnis nun in einer Variablen vom Typ [`SpeechSynthesisResult`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult). Die Funktion `SpeechSynthesisResult.getAudioData()` gibt ein `byte []`-Element der Ausgabedaten zurück. Sie können dieses `byte []`-Element manuell nutzen oder die [`AudioDataStream`](/java/api/com.microsoft.cognitiveservices.speech.audiodatastream)-Klasse verwenden, um den speicherinternen Stream zu verwalten. In diesem Beispiel verwenden Sie die statische Funktion `AudioDataStream.fromResult()`, um einen Datenstrom aus dem Ergebnis abzurufen.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);
    
    SpeechSynthesisResult result = synthesizer.SpeakText("Getting the response as an in-memory stream.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    System.out.print(stream.getStatus());
}
```

Ab hier können Sie mit dem sich ergebenden `stream`-Objekt ein beliebiges benutzerdefiniertes Verhalten implementieren.

## <a name="customize-audio-format"></a>Anpassen des Audioformats

Im folgenden Abschnitt wird veranschaulicht, wie Sie die Attribute der Audioausgabe anpassen, z. B.:

* Audiodateityp
* Abtastrate
* Bittiefe

Zum Ändern des Audioformats wenden Sie die Funktion `setSpeechSynthesisOutputFormat()` auf das Objekt `SpeechConfig` an. Diese Funktion erwartet für `enum` den Typ [`SpeechSynthesisOutputFormat`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat), den Sie zum Auswählen des Ausgabeformats verwenden. In den Referenzdokumenten finden Sie eine [Liste mit den verfügbaren Audioformaten](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?preserve-view=true&view=azure-dotnet).

Sie enthält verschiedene Optionen für unterschiedliche Anforderungen in Bezug auf den Dateityp. Beachten Sie, dass Rohformate wie `Raw24Khz16BitMonoPcm` gemäß Definition keine Audioheader enthalten. Nutzen Sie Rohformate nur, wenn Sie wissen, dass Ihre nachgelagerte Implementierung einen unformatierten Bitstream decodieren kann, oder wenn Sie planen, Header basierend auf Bittiefe, Abtastrate, Kanalanzahl usw. manuell zu erstellen.

In diesem Beispiel geben Sie das High-Fidelity-RIFF-Format `Riff24Khz16BitMonoPcm` an, indem Sie `SpeechSynthesisOutputFormat` für das Objekt `SpeechConfig` festlegen. Ähnlich wie im Beispiel im vorherigen Abschnitt nutzen Sie [`AudioDataStream`](/java/api/com.microsoft.cognitiveservices.speech.audiodatastream), um einen InMemory-Datenstrom des Ergebnisses zu erhalten, das Sie anschließend in eine Datei schreiben.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // set the output format
    speechConfig.setSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);
    SpeechSynthesisResult result = synthesizer.SpeakText("Customizing audio output format.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
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

Als Nächstes müssen Sie die Anforderung der Sprachsynthese so ändern, dass darin auf Ihre XML-Datei verwiesen wird. Die Anforderung bleibt größtenteils unverändert, aber Sie verwenden nun `SpeakSsml()` anstelle von `SpeakText()`. Von dieser Funktion wird eine XML-Zeichenfolge erwartet. Sie erstellen also zuerst eine Funktion, um eine XML-Datei zu laden und als Zeichenfolge zurückzugeben.

```java
private static String xmlToString(String filePath) {
    File file = new File(filePath);
    StringBuilder fileContents = new StringBuilder((int)file.length());

    try (Scanner scanner = new Scanner(file)) {
        while(scanner.hasNextLine()) {
            fileContents.append(scanner.nextLine() + System.lineSeparator());
        }
        return fileContents.toString().trim();
    } catch (FileNotFoundException ex) {
        return "File not found.";
    }
}
```

Ab diesem Punkt stimmt das sich ergebende Objekt genau mit den Objekten in den vorherigen Beispielen überein.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);

    String ssml = xmlToString("ssml.xml");
    SpeechSynthesisResult result = synthesizer.SpeakSsml(ssml);
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
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
> Neuronale Stimmen werden **nur** für Speech-Ressourcen unterstützt, die in den Regionen *USA, Osten*, *Asien, Südosten* und *Europa, Westen* erstellt werden.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```