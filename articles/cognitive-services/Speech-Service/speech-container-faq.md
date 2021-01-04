---
title: Häufig gestellte Fragen (FAQ) zu Containern für den Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Installieren Sie Speech-Container, und führen Sie sie aus. Die Spracherkennung wandelt Audiodatenströme in Echtzeit in Text um, der von Ihren Anwendungen, Tools oder Geräten genutzt oder angezeigt werden kann. Die Sprachsynthese konvertiert Eingabetext in menschenähnliche synthetische Sprache.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/12/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a657f43ef2d889cad1608d34e9235b1d5e7cb576
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95894149"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Häufig gestellte Fragen (FAQ) zu Containern für den Speech-Dienst

Ziehen Sie bei der Verwendung des Speech-Diensts mit Containern diese Sammlung häufig gestellter Fragen zu Rate, bevor Sie sich an den Support wenden. In diesem Artikel werden Fragen von unterschiedlicher Bedeutung erfasst, von allgemeinen bis hin zu technischen Fragen. Um eine Antwort zu erweitern, klicken Sie auf die Frage.

## <a name="general-questions"></a>Allgemeine Fragen

<details>
<summary>
<b>Wie funktionieren Speech-Container und wie richte ich sie ein?</b>
</summary>

**Antwort:** Bei der Einrichtung des Produktionsclusters sind mehrere Punkte zu beachten. Zunächst sollte das Einrichten einer einzelnen Sprache und mehrerer Container auf demselben Computer kein großes Problem darstellen. Wenn Probleme auftreten, könnte es sich um ein Problem mit der Hardware handeln. Daher würden wir zunächst die Ressourcen, d. h. die CPU- und Speicherspezifikationen, betrachten.

Betrachten Sie für einen Moment den `ja-JP`-Container und das neueste Modell. Das Akustikmodell ist hinsichtlich der CPU die anspruchsvollste Komponente, während das Sprachmodell den größten Arbeitsspeicherbedarf aufweist. Beim Benchmarking der Verwendung benötigten wir etwa 0,6 CPU-Kerne für die Verarbeitung einer einzelnen Spracherkennungsanforderung, wenn Audiodaten in Echtzeit eingehen (z. B. über das Mikrofon). Wenn Sie Audiodaten schneller als in Echtzeit zuführen (z. B. aus einer Datei), kann sich diese Nutzung verdoppeln (1,2 Kerne). Inzwischen wird der unten aufgeführte Arbeitsspeicher für die Sprachdecodierung verwendet. Dabei wird *nicht* die tatsächliche vollständige Größe des Sprachmodells berücksichtigt, das sich im Dateicache befinden wird. Für `ja-JP` sind das zusätzliche 2 GB. Für `en-US` können es mehr sein (6-7 GB).

Wenn Sie einen Computer besitzen, auf dem der Arbeitsspeicher knapp ist, und Sie versuchen, mehrere Sprachen darauf bereitzustellen, ist es möglich, dass der Dateicache voll ist und das Betriebssystem gezwungen ist, Modelle ein- und auszulagern. Für eine laufende Transkription könnte das verheerend sein und zu Verzögerungen und anderen Leistungsbeeinträchtigungen führen.

Darüber hinaus packen wir ausführbare Dateien für Computer mit dem [AVX2](speech-container-howto.md#advanced-vector-extension-support)-Anweisungssatz (Advanced Vector Extension) vorab. Ein Computer mit dem AVX512-Anweisungssatz erfordert die Codegenerierung für dieses Ziel, und das Starten von 10 Containern für 10 Sprachen kann die CPU vorübergehend überlasten. Eine Meldung wie diese wird in den Docker-Protokollen angezeigt:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Sie können mit der `DECODER MAX_COUNT`-Variablen die Anzahl der gewünschten Decoder innerhalb eines *einzelnen* Containers festlegen. Im Grunde genommen sollten wir also mit Ihrer SKU (CPU/Speicher) beginnen, und wir können Ihnen empfehlen, wie Sie sie optimal nutzen können. Ein geeigneter Ausgangspunkt ist der Verweis auf die empfohlenen Ressourcenspezifikationen für Hostcomputer.

<br>
</details>

<details>
<summary>
<b>Könnten Sie bei der Kapazitätsplanung und Kostenvorkalkulation von lokalen Spracherkennungscontainern helfen?</b>
</summary>

**Antwort:** Für die Containerkapazität im Batchverarbeitungsmodus könnte jeder Decoder 2-3x in Echtzeit mit zwei CPU-Kernen für eine einzelne Erkennung verarbeiten. Es wird nicht empfohlen, mehr als zwei gleichzeitige Erkennungen pro Containerinstanz zu behalten, aber es wird empfohlen, aus Gründen der Zuverlässigkeit/Verfügbarkeit mehr Containerinstanzen hinter einem Lastenausgleich auszuführen.

Obwohl wir jede Containerinstanz mit mehr Decodern ausführen könnten. So können wir z. B. sieben Decoder pro Containerinstanz auf einem Computer mit acht Kernen (mit jeweils mehr als 2x) einrichten, was einen 15-fachen Durchsatz ergibt. Es gibt einen `DECODER_MAX_COUNT`-Parameter, der beachtet werden muss. Für den Extremfall ergeben sich Probleme mit der Zuverlässigkeit und Wartezeit, wobei der Durchsatz deutlich erhöht wird. Für ein Mikrofon entspricht dies 1x der Echtzeit. Die Gesamtverwendung sollte bei etwa einem Kern für eine einzelne Erkennung liegen.

Für das Szenario der Verarbeitung von 1 K Stunden/Tag im Batchverarbeitungsmodus könnten im Extremfall drei virtuelle Computer dies innerhalb von 24 Stunden verarbeiten, aber ohne Garantie. Zur Bewältigung von Spitzentagen, Failover, Update und zur Bereitstellung einer minimalen Sicherung/BCP empfehlen wir 4-5 anstelle von drei Computern pro Cluster, wenn mehr als zwei Cluster vorliegen.

Für die Hardware verwenden wir den standardmäßigen virtuellen Azure-Computer `DS13_v2` als Referenz (jeder Kern muss mindestens 2,6 GHz mit aktiviertem AVX2-Anweisungssatz aufweisen).

| Instanz  | vCPU(s) | RAM    | Temporärer Speicher | Nutzungsbasierte Zahlung mit AHB | 1-Jahr-Reservierung mit AHB (Einsparungen in Prozent) | 3-Jahre-Reservierung mit AHB (Einsparungen in Prozent) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | 0,598 USD/Stunde            | 0,3528 USD/Stunde (~41 %)                 | 0,2333 USD/Stunde (~61 %)                  |

Basierend auf der Designreferenz (zwei Cluster mit fünf virtuellen Computern zur Verarbeitung von 1 K Stunden/Tag Audiobatchverarbeitung) werden die folgenden Hardwarekosten für ein Jahr angesetzt:

> 2 (Cluster) * 5 (VMs pro Cluster) * 0,3528 USD/Stunde * 365 (Tage) * 24 (Stunden) = 31.000 USD/Jahr

Bei der Zuordnung zu einem physischen Computer ist die allgemeine Schätzung: 1 vCPU = 1 physischer CPU-Kern. In der Realität ist eine vCPU leistungsfähiger als ein einzelner Kern.

Für eine lokale Lösung werden die folgenden zusätzlichen Faktoren berücksichtigt:

- Zu welchem Typ die physische CPU gehört und wie viele Kerne sie umfasst
- Wie viele CPUs im gleichen Gerät/Computer ausgeführt werden
- Wie die virtuellen Computer eingerichtet sind
- Wie Hyperthreading/Multithreading verwendet werden
- Wie der Arbeitsspeicher freigegeben wird
- Das Betriebssystem usw.

Normalerweise ist sie nicht so gut abgestimmt wie die Azure-Umgebung. Unter Berücksichtigung weiterer Komponenten würde ich sagen, eine sichere Schätzung ist: 10 physische CPU-Kerne = 8 Azure vCPUs. Obwohl beliebte CPUs nur über acht Kerne verfügen. Bei einer lokalen Bereitstellung werden die Kosten höher sein als bei der Verwendung von virtuellen Azure-Computern. Beachten Sie auch den Abschreibungssatz.

Die Kosten für den Dienst sind dieselben wie für den Onlinedienst: 1 USD/Stunde für die Spracherkennung. Kosten für den Speech-Dienst:

> 1 USD * 1000 * 365 = 365.000 USD

Die an Microsoft gezahlten Wartungskosten hängen vom Servicelevel und vom Inhalt des Diensts ab. Sie variieren von 29,99 USD/Monat für die Stufe „Basic“ bis zu Hunderttausenden USDs, wenn ein lokaler Service erforderlich ist. Ein ungefährer Wert ist 300 USD/Stunde für Service/Wartung. Die Kosten für das Personal sind nicht inbegriffen. Andere Infrastrukturkosten (z. B. Speicher, Netzwerke und Lastenausgleiche) sind nicht inbegriffen.

<br>
</details>

<details>
<summary>
<b>Warum fehlt die Interpunktion in der Transkription?</b>
</summary>

**Antwort:** Die `speech_recognition_language=<YOUR_LANGUAGE>` sollten in der Anforderung explizit konfiguriert werden, wenn sie den Carbon-Client verwenden.

Beispiel:

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
Hier sehen Sie die Ausgabe:

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>Kann ich ein benutzerdefiniertes Akustikmodell und Sprachmodell mit dem Speech-Container verwenden?</b>
</summary>

Wir können derzeit nur eine Modell-ID, entweder ein benutzerdefiniertes Sprachmodell oder benutzerdefiniertes Akustikmodell, übergeben.

**Antwort:** Es wurde die Entscheidung getroffen, Akustik- und Sprachmodelle *nicht* gleichzeitig zu unterstützen. Dies bleibt so lange wirksam, bis ein einheitlicher Bezeichner erstellt wird, um API-Unterbrechungen zu reduzieren. Daher wird dies leider im Moment nicht unterstützt.

<br>
</details>

<details>
<summary>
<b>Können Sie diese Fehler des benutzerdefinierten Spracherkennungscontainers erklären?</b>
</summary>

**Fehler 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Antwort 1:** Wenn Sie mit dem aktuellen benutzerdefinierten Modell trainieren, wird dies derzeit nicht unterstützt. Wenn Sie mit einer älteren Version trainieren, sollte es möglich sein, diese zu verwenden. Wir arbeiten weiterhin an der Unterstützung der neuesten Versionen.

Im Wesentlichen unterstützen die benutzerdefinierten Container keine Halide- oder ONNX-basierten Akustikmodelle (dies ist der Standard im benutzerdefinierten Trainingsportal). Das liegt daran, dass benutzerdefinierte Modelle nicht verschlüsselt sind, und wir wollen ONNX-Modelle jedoch nicht verfügbar machen. Sprachmodelle sind in Ordnung. Der Kunde muss explizit ein älteres Nicht-ONNX-Modell für benutzerdefinierte Trainings auswählen. Die Genauigkeit wird nicht beeinträchtigt. Die Modellgröße kann größer sein (um 100 MB).

> Supportmodell > 20190220 (v4.5 Unified)

**Fehler 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Antwort 2:** Sie müssen in der Anforderung den richtigen Sprachnamen angeben, wobei die Groß- und Kleinschreibung zu beachten ist. Weitere Informationen finden Sie in der vollständigen Zuordnung der Dienstnamen. Sie müssen `en-US-JessaRUS` verwenden, da `en-US-JessaNeural` in der Containerversion der Sprachsynthese derzeit nicht verfügbar ist.

**Fehler 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Antwort 3:** Sie müssen eine Speech-Ressource erstellen, nicht eine Ressource für Cognitive Services.


<br>
</details>

<details>
<summary>
<b>Welche API-Protokolle werden unterstützt, REST oder WS?</b>
</summary>

**Antwort:** Für Spracherkennungs- und benutzerdefinierte Spracherkennungscontainer unterstützen wir derzeit nur das Websocket-basierte Protokoll. Das SDK unterstützt nur den Aufruf in WS, aber nicht in REST. Es gibt einen Plan, die REST-Unterstützung hinzuzufügen, aber es gibt im Moment keinen Zeitpunkt der Verfügbarkeit. Weitere Informationen finden Sie in der offiziellen Dokumentation unter [Endpunkte der Abfragevorhersage](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>Wird CentOS für Speech-Container unterstützt?</b>
</summary>

**Antwort:** CentOS 7 wird von Python SDK noch nicht unterstützt, auch Ubuntu 19.04 wird nicht unterstützt.

Das Python Speech SDK-Paket ist für die folgenden Betriebssysteme verfügbar:
- **Windows**: x64 und x86
- **Mac**: macOS X Version 10.12 oder höher
- **Linux**: Ubuntu 16.04, Ubuntu 18.04, Debian 9 unter x64

Weitere Informationen zum Einrichten der Umgebung finden Sie unter [Einrichten der Python-Plattform](quickstarts/setup-platform.md?pivots=programming-language-python). Momentan ist Ubuntu 18.04 die empfohlene Version.

<br>
</details>

<details>
<summary>
<b>Warum erhalte ich beim Versuch, LUIS-Vorhersageendpunkte aufzurufen, Fehler?</b>
</summary>

Ich verwende den LUIS-Container in einer IoT-Edge-Bereitstellung und versuche, den LUIS-Vorhersageendpunkt von einem anderen Container aus aufzurufen. Der LUIS-Container lauscht an Port 5001, und die verwendete URL ist:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

Der von mir erhaltene Fehler ist:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

Ich sehe die Anforderung in den LUIS-Containerprotokollen und die Meldung lautet:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Was bedeutet dies? Was habe ich übersehen? Ich bin dem Beispiel für das Speech SDK von [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk) aus gefolgt. Im Szenario wird der Ton direkt vom PC-Mikrofon erfasst und versucht, auf der Grundlage der von uns trainierten LUIS-App die Absicht zu bestimmen. Das Beispiel, mit dem ich verknüpft bin, geht genau so vor. Und es funktioniert gut mit dem cloudbasierten LUIS-Dienst. Die Verwendung des Speech SDKs hat uns offenbar erspart, einen separaten expliziten Aufruf der Spracherkennungs-API und dann einen zweiten Aufruf von LUIS durchführen zu müssen.

Ich versuche also nur, vom Szenario der Verwendung von LUIS in der Cloud zur Verwendung des LUIS-Containers zu wechseln. Ich kann mir nicht vorstellen, dass das Speech SDK nur für den einen, aber nicht für den anderen Fall funktioniert.

**Antwort:** Das Speech SDK sollte nicht zusammen mit einem LUIS-Container verwendet werden. Für die Verwendung des LUIS-Containers sollte das LUIS SDK oder die LUIS-REST-API verwendet werden. Das Speech SDK sollte für einen Speech-Container verwendet werden.

Eine Cloud unterscheidet sich von einem Container. Eine Cloud kann aus mehreren aggregierten Containern (manchmal auch Microservices genannt) bestehen. Es gibt also einen LUIS-Container und einen Speech-Container – zwei separate Container. Der Speech-Container dient ausschließlich der Spracheingabe/-ausgabe. Der LUIS-Container führt nur LUIS aus. Da in der Cloud beide Container bekanntermaßen bereitgestellt werden und ein Remoteclient eine schlechte Leistung erzielt, wenn er zur Cloud wechselt, die Spracheingabe/-ausgabe vornimmt, wieder zurückkehrt, dann wieder zur Cloud wechselt und LUIS durchführt, bieten wir ein Feature, das es dem Client ermöglicht, zur Spracheingabe/-ausgabe zu wechseln, in der Cloud zu verbleiben, zu LUIS zu wechseln und dann wieder zum Client zurückzukehren. So wechselt auch in diesem Szenario das Speech SDK zum Speech-Cloudcontainer mit Audio, und dann spricht der Speech-Cloudcontainer mit dem LUIS-Cloudcontainer mit Text. Der LUIS-Container besitzt kein Konzept zur Übernahme von Audio (es wäre nicht sinnvoll, wenn der LUIS-Container Audiostreaming akzeptieren würde, da LUIS ein textbasierter Dienst ist). Bei dem lokalen Szenario haben wir keine Gewissheit, dass unser Kunde beide Container bereitgestellt hat. Wir maßen uns nicht an, zwischen den Containern in den Räumlichkeiten unserer Kunden zu orchestrieren, und wenn beide Container lokal beim Kunden bereitgestellt werden, ist es keine Mühe, zuerst zum SR und dann zurück zum Kunden zu wechseln, damit der Kunde dann den Text übernimmt und zu LUIS wechselt.

<br>
</details>

<details>
<summary>
<b>Warum erhalten wir Fehler beim macOS, Speech-Container und dem Python SDK?</b>
</summary>

Wenn wir eine *WAV*-Datei zur Transkription senden, kommt das Ergebnis mit Folgendem zurück:

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

Wir wissen, dass der Websocket ordnungsgemäß eingerichtet ist.

**Antwort:** Wenn das der Fall ist, dann finden Sie weitere Informationen unter [diesem GitHub-Problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310). Wir verfügen über eine Problemumgehung, [die hier vorgeschlagen wird](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722).

Carbon hat dies in Version 1.8 behoben.


<br>
</details>

<details>
<summary>
<b>Was sind die Unterschiede bei den Endpunkten des Speech-Containers?</b>
</summary>

Könnten Sie helfen, die folgenden Testmetriken auszufüllen, einschließlich der zu testenden Funktionen und der Vorgehensweise beim Testen der SDK- und REST-APIs? Insbesondere Unterschiede bei „interaktiv“ und „Unterhaltung“, die ich bei bestehenden Dokumenten/Beispielen nicht gesehen habe.

| Endpunkt                                                | Funktionstest                                                   | SDK | REST-API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Text synthetisieren (Sprachsynthese)                                  |     | Ja      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Cognitive Services: on-prem dictation v1 – Websocket-Endpunkt        | Ja | Nein       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | Cognitive Services: on-prem interactive v1 – Websocket-Endpunkt  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | Cognitive Services: on-prem conversation v1 – Websocket-Endpunkt |     |          |

**Antwort:** Dies ist eine Verbindung von:
- Leute, die den Diktatendpunkt für Container testen (ich bin nicht sicher, wie sie an diese URL gekommen sind)
- Der<sup> </sup>Erstanbieterendpunkt ist derjenige in einem Container.
- Der<sup> </sup>Erstanbieterendpunkt, der speech.fragment-Meldungen anstelle der `speech.hypothesis`-Meldungen zurückgibt, die die<sup> </sup>Drittanbieterendpunkte für den Diktatendpunkt zurückgeben.
- Die Carbon-Schnellstarts verwenden alle `RecognizeOnce` (interaktiver Modus).
- Carbon verfügt über eine Assertion, dass für `speech.fragment`-Meldungen erforderlich ist, dass sie nicht im interaktiven Modus zurückgegeben werden.
- Carbon verfügt über Assertionen, die in Releasebuilds ausgelöst werden (der Prozess wird beendet).

Die Problemumgehung besteht entweder darin, zur kontinuierlichen Erkennung in Ihrem Code zu wechseln oder (schneller) eine Verbindung zu den interaktiven oder kontinuierlichen Endpunkten im Container herzustellen.
Legen Sie für Ihren Code den Endpunkt auf „`host:port`/speech/recognition/interactive/cognitives services/v1“ fest.

Informationen zu den verschiedenen Modi finden Sie nachfolgend unter „Speech-Modi“:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Die richtige Lösung folgt mit SDK 1.8, das über die lokale Unterstützung verfügt (es wird den richtigen Endpunkt auswählen, sodass wir nicht schlechter sein werden als ein Onlinedienst). In der Zwischenzeit gibt es ein Beispiel für die kontinuierliche Erkennung. Warum verweisen wir nicht darauf?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Welchen Modus sollte ich für verschiedene Audiodateien verwenden?</b>
</summary>

**Antwort:** Hier ist ein [Schnellstart mit Python](./get-started-speech-to-text.md?pivots=programming-language-python). Die anderen Sprachen finden Sie auf der Website für die Dokumentationen.

Nur zur Verdeutlichung für den Modus „Interaktiv“, „Unterhaltung“ und „Diktat“: Dies ist eine fortgeschrittene Art und Weise, die besondere Vorgehensweise zu spezifizieren, in der unser Dienst die Speech-Anforderung behandeln wird. Leider müssen wir bei den lokalen Containern den vollständigen URI angeben (da er den lokalen Computer einschließt), sodass diese Informationen aus der Abstraktion durchgesickert sind. Wir arbeiten mit dem SDK-Team zusammen, um dies in Zukunft besser nutzen zu können.

<br>
</details>

<details>
<summary>
<b>Wie können wir einen Vergleichstest für eine grobe Kennzahl für Transaktionen/Sekunde/Kern durchführen?</b>
</summary>

**Antwort:** Hier sind einige der groben Werte, die vom bestehenden Modell zu erwarten sind (wird sich in dem Modell, das wir in der GA ausliefern werden, verbessern):

- Bei Dateien wird die Drosselung im Speech SDK bei 2x liegen. Die ersten fünf Sekunden der Audiodaten werden nicht gedrosselt. Der Decoder ist in der Lage, etwa 3x in Echtzeit zu verarbeiten. Hierfür wird die gesamte CPU-Auslastung nahezu zwei Kerne für eine einzelne Erkennung betragen.
- Für das Mikrofon wird es 1x in Echtzeit betragen. Die Gesamtauslastung sollte bei etwa einem Kern für eine einzelne Erkennung liegen.

All dies kann anhand der Docker-Protokolle überprüft werden. Wir geben die Zeile tatsächlich mit den Sitzungs- und Phrasen-/Äußerungsstatistiken aus, und dazu gehören auch die RTF-Werte.


<br>
</details>

<details>
<summary>
<b>Ist es üblich, Audiodateien für die Verwendung in Speech-Containern in Blöcke aufzuteilen?</b>
</summary>

Mein derzeitiger Plan besteht darin, eine vorhandene Audiodatei in 10-Sekunden-Blöcke aufzuteilen und diese durch den Container zu senden. Ist das ein akzeptables Szenario?  Gibt es eine bessere Möglichkeit, größere Audiodateien mit dem Container zu verarbeiten?

**Antwort:** Verwenden Sie einfach das Speech SDK, und übergeben Sie ihm die Datei. Es wird die richtigen Schritte durchführen. Warum müssen Sie die Datei in Blöcke aufteilen?


<br>
</details>

<details>
<summary>
<b>Wie kann ich mehrere Container auf demselben Host ausführen lassen?</b>
</summary>

Das Dokument besagt, dass wir einen anderen Port verfügbar machen sollen (was ich auch tue), aber der LUIS-Container lauscht immer noch an Port 5000?

**Antwort:** Versuchen Sie `-p <outside_unique_port>:5000`. Beispiel: `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Technische Fragen

<details>
<summary>
<b>Wie kann ich Nicht-Batch-APIs dazu bringen, Audiodaten &lt;15 Sekunden lang zu verarbeiten?</b>
</summary>

**Antwort:** `RecognizeOnce()` verarbeitet im interaktiven Modus nur bis zu 15 Sekunden an Audiodaten, da dieser Modus für die Sprachsteuerung gedacht ist, bei der kurze Äußerungen erwartet werden. Wenn Sie `StartContinuousRecognition()` für Diktat oder Unterhaltung verwenden, gibt es kein Limit von 15 Sekunden.


<br>
</details>

<details>
<summary>
<b>Was sind die empfohlenen Ressourcen, CPU und RAM; für 50 gleichzeitige Anforderungen?</b>
</summary>

Wie viele gleichzeitige Anforderungen können von einem Szenario mit vier Kernen und 4 GB RAM verarbeitet werden? Wenn wir z. B. 50 gleichzeitige Anforderungen verarbeiten müssen, welche Werte werden für Kerne und RAM empfohlen?

**Antwort:** In Echtzeit acht mit unserem neuesten `en-US`, daher empfehlen wir, mehr Docker-Container bei mehr als sechs gleichzeitigen Anforderungen zu verwenden. Ab 16 Kernen wird es komplizierter und es wird empfindlich auf NUMA-Knoten (Non-Uniform Memory Access) reagiert. Die folgende Tabelle beschreibt die minimale und empfohlene Zuordnung von Ressourcen für jeden Speech-Container.

# <a name="speech-to-text"></a>[Spracherkennung](#tab/stt)

| Container      | Minimum             | Empfohlen         |
|----------------|---------------------|---------------------|
| Spracherkennung | 2 Kerne, 2 GB Arbeitsspeicher | 4 Kerne, 4 GB Arbeitsspeicher |

# <a name="custom-speech-to-text"></a>[Benutzerdefinierte Spracherkennung](#tab/cstt)

| Container             | Minimum             | Empfohlen         |
|-----------------------|---------------------|---------------------|
| Benutzerdefinierte Spracherkennung | 2 Kerne, 2 GB Arbeitsspeicher | 4 Kerne, 4 GB Arbeitsspeicher |

# <a name="text-to-speech"></a>[Sprachsynthese](#tab/tts)

| Container      | Minimum             | Empfohlen         |
|----------------|---------------------|---------------------|
| Text-zu-Sprache | Ein Kern, 2 GB Arbeitsspeicher | 2 Kerne, 3 GB Arbeitsspeicher |

# <a name="custom-text-to-speech"></a>[Benutzerdefinierte Sprachsynthese](#tab/ctts)

| Container             | Minimum             | Empfohlen         |
|-----------------------|---------------------|---------------------|
| Benutzerdefinierte Sprachsynthese | Ein Kern, 2 GB Arbeitsspeicher | 2 Kerne, 3 GB Arbeitsspeicher |

**_

- Jeder Kern muss eine Geschwindigkeit von mindestens 2,6 GHz aufweisen.
- Bei Dateien wird die Drosselung im Speech SDK auf 2x festgelegt (die ersten fünf Sekunden der Audiodaten werden nicht gedrosselt).
- Der Decoder ist in der Lage, etwa 2-3x in Echtzeit zu verarbeiten. Hierfür wird die gesamte CPU-Auslastung nahezu zwei Kerne für eine einzelne Erkennung betragen. Aus diesem Grund wird nicht empfohlen, mehr als zwei aktive Verbindungen pro Containerinstanz zu verwenden. Der Extremfall wäre, etwa 10 Decoder bei 2x der Echtzeit in einen Computer mit acht Kernen wie `DS13_V2` zu integrieren. Für die Containerversion 1.3 und höher gibt es einen Parameter, den Sie versuchen könnten, auf `DECODER_MAX_COUNT=20` festzulegen.
- Für ein Mikrofon entspricht dies 1x der Echtzeit. Die Gesamtverwendung sollte bei etwa einem Kern für eine einzelne Erkennung liegen.

Berücksichtigen Sie die Gesamtzahl der Audiodatenstunden, über die Sie verfügen. Wenn die Anzahl groß ist, schlagen wir zur Verbesserung der Zuverlässigkeit/Verfügbarkeit vor, mehr Containerinstanzen, entweder für eine einzelne oder mehrere Boxen, hinter einem Lastenausgleich auszuführen. Die Orchestrierung könnte mit Kubernetes (K8S) und Helm oder mit Docker Compose erfolgen.

Um z. B. 1000 Stunden/24 Stunden zu verarbeiten, haben wir versucht, 3-4 virtuelle Computer mit 10 Instanzen/Decodern pro virtuellem Computer einzurichten.

<br>
</details>

<details>
<summary>
<b>Unterstützt der Speech-Container die Interpunktion?</b>
</summary>

_ *Antwort:* * Im lokalen Container ist die Großschreibung (ITN) verfügbar. Die Interpunktion ist sprachabhängig und wird für einige Sprachen, darunter Chinesisch und Japanisch, nicht unterstützt.

Wir *verfügen* über eine implizite und grundlegende Unterstützung der Interpunktion für die bestehenden Container, aber sie ist standardmäßig `off`. Das bedeutet, dass Sie in Ihrem Beispiel das Zeichen `.` erhalten können, aber nicht das Zeichen `。`. Um diese implizite Logik zu aktivieren, finden Sie hier ein Beispiel dafür, wie dies in Python mit unserem Speech SDK möglich ist (in anderen Sprachen wäre es ähnlich):

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>Warum erhalte ich den Fehler 404, wenn ich versuche, Daten in einen Spracherkennungscontainer zu veröffentlichen?</b>
</summary>

Hier ist ein Beispiel für HTTP POST:

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**Antwort:** Wir unterstützen keine REST-API in beiden Spracherkennungscontainern, sondern nur WebSockets über das Speech SDK. Weitere Informationen finden Sie in der offiziellen Dokumentation unter [Endpunkte der Abfragevorhersage](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>


<details>
<summary>
<b> Warum wird der Container als Nicht-Root-Benutzer ausgeführt? Welche Probleme könnten dadurch entstehen?</b>
</summary>

**Antwort:** Beachten Sie, dass der Standardbenutzer innerhalb des Containers ein Nicht-Root-Benutzer ist. Dies bietet Schutz vor Prozessen, die aus dem Container entweichen und ausgeweitete Berechtigungen auf dem Hostknoten erhalten. Standardmäßig gehen einige Plattformen wie die OpenShift Container-Plattform bereits entsprechend vor, indem sie Container mithilfe einer willkürlich zugewiesenen Benutzer-ID ausführen. Für diese Plattformen muss der Nicht-Root-Benutzer über die Berechtigung verfügen, auf jedes extern zugeordnete Volume zu schreiben, das Schreibvorgänge erfordert. Beispiel: ein Protokollordner oder ein Downloadordner für benutzerdefinierte Modelle.
<br>
</details>

<details>
<summary>
<b>Warum erhalte ich diesen Fehler, wenn ich den Spracherkennungsdienst verwende?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Antwort:** Dies ist normalerweise der Fall, wenn Sie die Audiodaten schneller zuführen, als sie vom Spracherkennungscontainer verarbeitet werden können. Die Clientpuffer füllen sich, und der Abbruch wird ausgelöst. Sie müssen die Parallelität und das RTF, mit dem Sie die Audiodaten senden, steuern.

<br>
</details>

<details>
<summary>
<b>Könnten Sie diese Fehler des Sprachsynthesecontainers anhand der C++-Beispiele erklären?</b>
</summary>

**Antwort:** Wenn die Containerversion älter als Version 1.3 ist, dann sollte der folgende Code verwendet werden:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Ältere Container verfügen nicht über den erforderlichen Endpunkt, damit Carbon mit der `FromHost`-API arbeiten kann. Wenn die Container für Version 1.3 verwendet werden, dann sollte dieser Code verwendet werden:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Nachfolgend finden Sie ein Beispiel für die Verwendung der `FromEndpoint`-API:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 Die Funktion `SetSpeechSynthesisVoiceName` wird aufgerufen, da die Container mit einer aktualisierten Sprachsyntheseengine den Sprachnamen benötigen.

<br>
</details>

<details>
<summary>
<b>Wie kann ich Version 1.7 des Speech SDK mit einem Speech-Container verwenden?</b>
</summary>

**Antwort:** Es gibt drei Endpunkte im Speech-Container für verschiedene Anwendungsbereiche. Sie sind als Speech-Modi definiert – siehe unten:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Sie dienen unterschiedlichen Zwecken und werden unterschiedlich genutzt.

Python-[Beispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py):
- Zur Einzelerkennung (interaktiver Modus) mit einem benutzerdefinierten Endpunkt (d. h. `SpeechConfig` mit einem Endpunktparameter) – siehe `speech_recognize_once_from_file_with_custom_endpoint_parameters()`.
- Für die kontinuierliche Erkennung (Modus „Unterhaltung“) und zum einfachen Wechsel zur Verwendung eines benutzerdefinierten Endpunkts wie oben – siehe `speech_recognize_continuous_from_file()`.
- Um das Diktieren in Beispielen wie oben zu aktivieren (nur wenn Sie es wirklich benötigen), fügen Sie direkt nach dem Erstellen von `speech_config` den Code `speech_config.enable_dictation()` hinzu.

Rufen Sie in C# die Funktion `SpeechConfig.EnableDictation()` auf, um das Diktieren zu aktivieren.

### <a name="fromendpoint-apis"></a>`FromEndpoint`-APIs
| Sprache | API-Informationen |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Wird derzeit nicht unterstützt und ist auch nicht geplant. |

<br>
</details>

<details>
<summary>
<b>Wie kann ich Version 1.8 des Speech SDK mit einem Speech-Container verwenden?</b>
</summary>

**Antwort:** Es gibt eine neue `FromHost`-API. Dadurch werden keine vorhandenen APIs ersetzt oder geändert. Es wird lediglich eine alternative Möglichkeit zum Erstellen einer Speech-Konfiguration mithilfe eines benutzerdefinierten Hosts hinzugefügt.

### <a name="fromhost-apis"></a>`FromHost`-APIs

| Sprache | API-Informationen |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Derzeit nicht unterstützt |

> Parameter: Host (obligatorisch), Abonnementschlüssel (optional, wenn Sie den Dienst auch ohne ihn nutzen können).

Das Format für den Host ist `protocol://hostname:port`, wobei `:port` optional ist (siehe unten):
- Wenn der Container lokal ausgeführt wird, ist der Hostname `localhost`.
- Wenn der Container auf einem Remoteserver ausgeführt wird, verwenden Sie den Hostnamen oder die IPv4-Adresse dieses Servers.

Beispiele für Hostparameter für die Spracherkennung:
- `ws://localhost:5000`: Nicht sichere Verbindung mit einem lokalen Container über Port 5000
- `ws://some.host.com:5000`: Nicht sichere Verbindung mit einem Container, der auf einem Remoteserver ausgeführt wird

Python-Beispiele von oben, aber verwenden Sie den Parameter `host` anstelle von `endpoint`:

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Containerunterstützung in Azure Cognitive Services](speech-container-howto.md)