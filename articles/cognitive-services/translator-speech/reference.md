---
title: Referenz für die Sprachübersetzungs-API
titleSuffix: Azure Cognitive Services
description: Referenzdokumentation für die Sprachübersetzungs-API.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: reference
ms.date: 05/18/2018
ms.author: v-jansko
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3493f6d25461836d8f6e48ce4213b0f5b78b6372
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60539178"
---
# <a name="translator-speech-api"></a>Sprachübersetzungs-API

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Dieser Dienst stellt eine Streaming-API für die Übertragung von Konversationen aus einer Sprache in Text in einer anderen Sprache bereit. Darüber hinaus integriert die API auch Text-to-Speech-Funktionen, damit der übersetzte Text in Form einer Audioaufnahme zurückübertragen werden kann. Die Sprachübersetzungs-API ermöglicht Szenarien wie Echtzeitübersetzungen von Konversationen in Skype Translator.

Mit der Sprachübersetzungs-API streamen Clientanwendungen Audio an den Dienst und empfangen einen Stream mit textbasierten Ergebnissen. Diese umfassen den erkannten Text in der Ausgangssprache und die entsprechende Übersetzung in der Zielsprache. Zur Generierung der Textergebnisse wird auf den eingehenden Audiostream eine auf neuronalen Netzwerken basierende automatische Spracherkennung (Automatic Speech Recognition, ASR) angewendet. Eine unformatierte ASR-Ausgabe wird mithilfe einer neuen Technik, TrueText, weiter verbessert, damit die Benutzerabsicht genauer widergespiegelt werden kann. So entfernt TrueText beispielsweise Komponenten, die den Textfluss stören (z.B. „Hmm“ und Husten), und stellt eine ordnungsgemäße Interpunktion und Großschreibung wieder her. Es besteht auch die Möglichkeit, Obszönitäten zu maskieren oder auszuschließen. Die Erkennungs- und Übersetzungsengines werden speziell für die Verarbeitung von Konversationen trainiert. Der Sprachübersetzungsdienst verwendet die Stille-Erkennung, um das Ende einer Äußerung zu bestimmen. Nach einer Sprechpause gibt der Dienst mittels Streaming ein Endergebnis der abgeschlossenen Äußerung zurück. Der Dienst kann auch Teilergebnisse zurücksenden, die Zwischeninformationen zu Erkennungen und Übersetzungen einer noch nicht abgeschlossenen Äußerung geben. Für Endergebnisse stellt der Dienst die Funktion zur Verfügung, Sprache aus dem gesprochenen Text in die Zielsprachen zu synthetisieren (Text-to-Speech). Text-to-Speech-Audio wird in dem vom Client angegebenen Format erstellt. Verfügbare Formate sind WAV und MP3.

Die Sprachübersetzungs-API nutzt für die Bereitstellung eines Vollduplex-Kommunikationskanals zwischen dem Client und dem Server das WebSocket-Protokoll. Für die Verwendung des Diensts müssen in einer Anwendung die folgenden Schritte ausgeführt werden:

## <a name="1-getting-started"></a>1. Erste Schritte
Für den Zugriff auf die Textübersetzungs-API müssen Sie sich bei [Microsoft Azure registrieren](translator-speech-how-to-signup.md).

## <a name="2-authentication"></a>2. Authentication

Authentifizieren Sie sich mithilfe des Abonnementschlüssels. Die Sprachübersetzungs-API unterstützt zwei Authentifizierungsmodi:

* **Verwendung eines Zugriffstokens:** Rufen Sie in Ihrer Anwendung ein Zugriffstoken vom Tokendienst ab. Rufen Sie mithilfe Ihres Abonnementschlüssels für die Sprachübersetzungs-API ein Zugriffstoken vom Azure Cognitive Services-Authentifizierungsdienst ab. Das Zugriffstoken ist 10 Minuten lang gültig. Rufen Sie alle 10 Minuten ein neues Zugriffstoken ab, und verwenden Sie weiterhin das gleiche Zugriffstoken für wiederholte Anforderungen innerhalb dieser 10 Minuten.

* **Direkte Verwendung eines Abonnementschlüssels:** Übergeben Sie Ihren Abonnementschlüssel in Ihrer Anwendung als Wert im Header `Ocp-Apim-Subscription-Key`.

Behandeln Sie Ihren Abonnementschlüssel und das Zugriffstoken als geheime Schlüssel, die nicht in der Ansicht dargestellt werden sollten.

## <a name="3-query-languages"></a>3. Abfragen von Sprachen
**Fragen Sie die Ressource „Sprachen“ nach den aktuellen unterstützten Sprache an.** Die [Ressource „Sprachen“](languages-reference.md) macht die Sprachen und Stimmen für die Spracherkennung, für Textübersetzungen und für Text-to-Speech verfügbar. Jeder Sprache oder Stimme wird ein Bezeichner zugeordnet, den die Sprachübersetzungs-API für die Ermittlung der gleichen Sprache oder Stimme verwendet.

## <a name="4-stream-audio"></a>4. Streamen von Audio
**Öffnen Sie eine Verbindung und beginnen Sie mit dem Streaming von Audio an den Dienst.** Die Dienst-URL lautet `wss://dev.microsofttranslator.com/speech/translate`. Vom Dienst erwartete Parameter und Audioformate werden unten, im Vorgang `/speech/translate`, beschrieben. Einer der Parameter wird für die Übergabe des Zugriffstokens aus Schritt 2 verwendet.

## <a name="5-process-the-results"></a>5. Verarbeiten der Ergebnisse
**Verarbeiten Sie die Ergebnisse, die vom Dienst zurück gestreamt werden.** Das Format der Teilergebnisse, Endergebnisse und Text-to-Speech-Audiosegmente wird unten in der Dokumentation zum Vorgang `/speech/translate` beschrieben.

Codebeispiele, die die Verwendung der Sprachübersetzungs-API veranschaulichen, sind auf der [GitHub-Website zu Microsoft Translator](https://github.com/MicrosoftTranslator) verfügbar.

## <a name="implementation-notes"></a>Hinweise zur Implementierung

Mit „GET/speech/translate“ wird eine Sitzung für die Sprachübersetzung eingerichtet

### <a name="connecting"></a>Herstellen einer Verbindung
Überprüfen Sie die Liste der später in diesem Abschnitt angegebenen Parameter, bevor Sie eine Verbindung mit dem Dienst herstellen. Im Folgenden wird ein Beispiel für eine Anforderung aufgeführt:

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

Die Anforderung gibt an, dass gesprochenes English an den Dienst gestreamt und in Italienisch übersetzt wird. Jedes finale Erkennungsergebnis generiert eine Text-to-Speech-Audioantwort mit der weiblichen Stimme namens Elsa. Beachten Sie, dass die Anforderung Anmeldeinformationen im `Ocp-Apim-Subscription-Key header` enthält. Darüber hinaus folgt die Anforderung einer bewährten Methode, indem im Header `X-ClientTraceId` ein Globally Unique Identifier festgelegt wird. Eine Clientanwendung sollte die Ablaufverfolgungs-ID protokollieren, damit diese bei möglicherweise auftretenden Fehlern zur Problembehandlung verwendet werden kann.

### <a name="sending-audio"></a>Senden von Audio
Nachdem die Verbindung hergestellt wurde, beginnt der Client mit dem Streaming von Audio an den Dienst. Der Client sendet die Audioaufnahmen in Blöcken. Jeder Block wird mit einer WebSocket-Nachricht vom Typ „Binär“ übertragen.

Die Audioeingabe erfolgt im WAVE-Format (Waveform Audio File) bzw. im WAV-Format (unter diesem Namen ist es aufgrund der Dateinamenserweiterung besser bekannt). Die Clientanwendung sollte einkanalige, signierte 16-Bit-PCM-Audioaufnahmen mit einer Samplingrate von 16 kHz streamen. In der ersten Bytes, die vom Client gestreamt werden, ist der WAV-Header enthalten. Ein 44-Byte-Header für einen einkanaligen, signierten 16-Bit-PCM-Stream mit einer Samplingrate von 16 kHz sieht wie folgt aus:

|Offset|Wert|
|:---|:---|
|0 – 3|„RIFF“|
|4 – 7|0|
|8 – 11|„WAVE“|
|12 – 15|„fmt“|
|16 – 19|16|
|20 – 21|1|
|22 – 23|1|
|24 – 27|16000|
|28 – 31|32000|
|32 – 33|2|
|34 – 35|16|
|36 – 39|„data“|
|40 – 43|0|

Beachten Sie, dass die Gesamtgröße der Datei (Bytes 4 – 7) und die Größe des Werts „data“ (Bytes 40 – 43) auf 0 (null) festgelegt werden. Dies ist für das Streamingszenario, in dem die Gesamtgröße nicht unbedingt im Vorfeld bekannt ist, in Ordnung.

Nach dem Senden des WAV-Headers (RIFF) sendet der Client Blöcke der Audiodaten. Der Client streamt in der Regel Blöcke mit einer festen Größe, die eine feste Dauer darstellen (z.B. ein Stream mit jeweils 100 ms Audio).

### <a name="signal-the-end-of-the-utterance"></a>Signalisieren des Endes einer Äußerung
Die Sprachübersetzungs-API gibt das Transkript und die Übersetzung des Audiostreams zurück, während Sie die Audiodaten übermitteln. Das letzte Transkript, die letzte Übersetzung und die übersetzten Audiodaten werden erst nach dem Ende der Äußerung zurückgegeben. In einigen Fällen ist es eventuell notwendig, das Ende der Äußerung zu erzwingen. Übermitteln Sie 2,5 Sekunden Stille, um damit das Ende der Äußerung zu erzwingen.

### <a name="final-result"></a>Endergebnis
Ein finales Spracherkennungsergebnis wird am Ende einer Äußerung generiert. Der Dienst überträgt ein Ergebnis mit einer WebSocket-Nachricht vom Typ „Text“ an den Client. Die Nachricht beinhaltet die JSON-Serialisierung eines Objekts mit den folgenden Eigenschaften:

* `type`: Zeichenfolgenkonstante zur Ermittlung des Ergebnistyps. Der Wert ist für die Endergebnisse final.
* `id`: Dem Erkennungsergebnis zugewiesener Zeichenfolgebezeichner.
* `recognition`: Erkannter Text in der Ausgangssprache. Im Falle einer falschen Erkennung besteht der Text möglicherweise aus einer leeren Zeichenfolge.
* `translation`: In die Zielsprache übersetzter erkannter Text.
* `audioTimeOffset`: Zeitoffset des Beginns der Erkennung in Takten (1 Takt = 100 Nanosekunden). Der Offsetwert ist relativ zum Anfang des Streamings.
* `audioTimeSize`: Dauer der Erkennung in Takten (100 Nanosekunden).
* `audioStreamPosition`: Byteoffset des Anfangs der Erkennung. Der Offsetwert ist relativ zum Anfang des Streams.
* `audioSizeBytes`: Größe der Erkennung in Byte.

Beachten Sie, dass die Positionierung der Erkennung im Audiostream in den Ergebnissen standardmäßig nicht inbegriffen ist. Die Funktion `TimingInfo` muss vom Client ausgewählte werden (siehe Parameter `features`).

Im Folgenden wird ein Beispiel für ein Endergebnis aufgeführt:

```
{
  type: "final"
  id: "23",
  recognition: "what was said",
  translation: "translation of what was said",
  audioStreamPosition: 319680,
  audioSizeBytes: 35840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 21900000
}
```

### <a name="partial-result"></a>Teilergebnis
Teil- oder Zwischenergebnisse der Spracherkennung werden standardmäßig nicht an den Client gestreamt. Der Client kann diese mithilfe des Abfrageparameters „features“ anfordern.

Der Dienst überträgt ein Teilergebnis mit einer WebSocket-Nachricht vom Typ „Text“ an den Client. Die Nachricht beinhaltet die JSON-Serialisierung eines Objekts mit den folgenden Eigenschaften:

* `type`: Zeichenfolgenkonstante zur Ermittlung des Ergebnistyps. Der Wert bei Teilergebnissen lautet „partiell“.
* `id`: Dem Erkennungsergebnis zugewiesener Zeichenfolgebezeichner.
* `recognition`: Erkannter Text in der Ausgangssprache.
* `translation`: In die Zielsprache übersetzter erkannter Text.
* `audioTimeOffset`: Zeitoffset des Beginns der Erkennung in Takten (1 Takt = 100 Nanosekunden). Der Offsetwert ist relativ zum Anfang des Streamings.
* `audioTimeSize`: Dauer der Erkennung in Takten (100 Nanosekunden).
* `audioStreamPosition`: Byteoffset des Anfangs der Erkennung. Der Offsetwert ist relativ zum Anfang des Streams.
* `audioSizeBytes`: Größe der Erkennung in Byte.

Beachten Sie, dass die Positionierung der Erkennung im Audiostream in den Ergebnissen standardmäßig nicht inbegriffen ist. Die Funktion „TimingInfo“ muss vom Client ausgewählte werden (siehe Parameter „features“).

Im Folgenden wird ein Beispiel für ein Endergebnis aufgeführt:

```
{
  type: "partial"
  id: "23.2",
  recognition: "what was",
  translation: "translation of what was",
  audioStreamPosition: 319680,
  audioSizeBytes: 25840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 11900000
}
```

### <a name="text-to-speech"></a>Text-zu-Sprache
Wenn die Text-zu-Sprache-Funktion aktiviert ist (siehe den nachfolgenden Parameter `features`), ist ein Endergebnis gefolgt von dem Audio des gesprochenen übersetzten Texts. Audiodaten werden aufgeteilt und vom Dienst als Sequenz von WebSocket-Nachrichten vom Typ „Binär“ an den Client gesendet. Ein Client kann das Ende des Streams erkennen, indem er das FIN-Bit der einzelnen Nachrichten prüft. In der letzten Nachricht vom Typ „Binär“ ist das FIN-Bit auf 1 festgelegt, um auf das Ende des Streams hinzuweisen. Das Format des Streams hängt vom Wert des Parameters `format` ab.

### <a name="closing-the-connection"></a>Schließen der Verbindung
Wenn eine Clientanwendung die Audiowiedergabe abgeschlossen und das letzte Endergebnis empfangen hat, sollte sie die Verbindung durch Initiieren des Handshakes zum Schließen des WebSockets schließen. Es gibt Bedingungen, die eine Beendigung der Verbindung durch den Server verursachen. Folgende Codes zum Schließen von WebSockets können vom Client empfangen werden:

* `1003 - Invalid Message Type`: Der Server beendet die Verbindung, da er den empfangenen Datentyp nicht akzeptieren kann. Dies geschieht häufig, wenn eingehende Audioaufnahmen nicht mit einem geeigneten Header beginnen.
* `1000 - Normal closure`: Die Verbindung wurde geschlossen, nachdem die Anforderung verarbeitet wurde. Der Server schließt die Verbindung: Wenn der Client über einen längeren Zeitraum keine Audioaufnahmen empfangen hat; wenn über einen längeren Zeitraum kein Ton gestreamt wird; wenn eine Sitzung die maximal zulässige Dauer erreicht hat (etwa 90 Minuten).
* `1001 - Endpoint Unavailable`: Gibt an, dass der Server nicht mehr verfügbar sein wird. Die Clientanwendung kann mit einer begrenzten Anzahl an Wiederholungsversuchen versuchen, erneut eine Verbindung herzustellen.
* `1011 - Internal Server Error`: Die Verbindung wird aufgrund eines Fehlers auf dem Server vom Server geschlossen.

### <a name="parameters"></a>Parameter

|Parameter|Wert|BESCHREIBUNG|Parametertyp|Datentyp|
|:---|:---|:---|:---|:---|
|api-version|1.0|Die vom Client angeforderte Version der API. Zulässige Werte sind: `1.0`.|query   |Zeichenfolge|
|from|(leer)   |Gibt die Sprache der Spracheingabe an. Der Wert ist eine der Sprachen-IDs aus dem Bereich `speech` in der Antwort von der Sprachen-API.|query|Zeichenfolge|
|zu|(leer)|Gibt die Sprache an, in die der transkribierte Text übersetzt werden soll. Der Wert ist eine der Sprachen-IDs aus dem Bereich `text` in der Antwort von der Sprachen-API.|query|Zeichenfolge|
|Features|(leer)   |Vom Client ausgewählte, durch Kommas getrennte Funktionen. Zu den verfügbaren Funktionen zählen:<ul><li>`TextToSpeech`: Gibt an, dass der Dienst die übersetzte Audioaufnahme des letzten übersetzten Satzes zurückgeben muss.</li><li>`Partial`: Gibt an, dass der Dienst Zwischenergebnisse der Erkennung zurückgeben muss, während die Audioaufnahme an den Dienst gestreamt wird.</li><li>`TimingInfo`: Gibt an, dass der Dienst Zeitmessungsergebnisse zurückgeben muss, die den einzelnen Erkennungsvorgängen zugeordnet sind.</li></ul>So würde ein Client beispielsweise `features=partial,texttospeech` angeben, um Teilergebnisse und Text-to-Speech zu empfangen, jedoch keine Zeitmessungsinformationen. Beachten Sie, dass Endergebnisse immer an den Client gestreamt werden.|query|Zeichenfolge|
|voice|(leer)|Gibt an, welche Stimme für das Text-to-Speech-Rendering des übersetzten Texts verwendet werden soll. Der Wert ist eine der Stimmenkennungen aus dem TTS-Bereich in der Antwort von der Sprachen-API. Wenn eine Stimme nicht angegeben ist, wählt das System automatisch eine Stimme aus, wenn die Text-to-Speech-Funktion aktiviert ist.|query|Zeichenfolge|
|format|(leer)|Gibt das Format des Text-to-Speech-Audiostreams an, der vom Dienst zurückgegeben wurde. Die verfügbaren Optionen lauten wie folgt:<ul><li>`audio/wav`: Waveform-Audiostream. Der Client sollte den WAV-Header verwenden, um das Audioformat richtig interpretieren zu können. Das WAV-Audio für Text-to-Speech ist einkanaliges 16-Bit-PCM mit einer Samplingrate von 24 kHz oder 16 kHz.</li><li>`audio/mp3`: MP3-Audiostream.</li></ul>Der Standardwert ist `audio/wav`.|query|Zeichenfolge|
|ProfanityAction    |(leer)    |Gibt an, wie der Dienst mit in der Sprache erkannten Obszönitäten umgehen soll. Gültige Aktionen sind:<ul><li>`NoAction`: Obszönitäten bleiben unverändert.</li><li>`Marked`: Obszönitäten werden durch einen Marker ersetzt. Siehe Parameter `ProfanityMarker`.</li><li>`Deleted`: Obszönitäten werden gelöscht. Wenn das Wort `"jackass"` beispielsweise als Obszönität behandelt wird, wird der Ausdruck `"He is a jackass."` in `"He is a .".` geändert</li></ul>Die Standardaktion ist „Marked“.|query|Zeichenfolge|
|ProfanityMarker|(leer)    |Gibt an, wie erkannte Obszönitäten behandelt werden, wenn `ProfanityAction` auf `Marked` festgelegt ist. Gültige Optionen sind:<ul><li>`Asterisk`: Obszönitäten werden durch die Zeichenfolge `***` ersetzt. Wenn das Wort `"jackass"` beispielsweise als Obszönität behandelt wird, wird der Ausdruck `"He is a jackass."` in `"He is a ***.".` geändert</li><li>`Tag`: Obszönitäten werden von einem XML-Tag für Obszönitäten umgeben. Wenn das Wort `"jackass"` beispielsweise als Obszönität behandelt wird, wird der Ausdruck `"He is a jackass."` in `"He is a <profanity>jackass</profanity>."` geändert.</li></ul>Der Standardwert lautet `Asterisk`.|query|Zeichenfolge|
|Autorisierung|(leer)  |Gibt den Wert des Bearertokens des Clients an. Verwenden Sie das Präfix `Bearer` gefolgt vom Wert des Werts `access_token`, der vom Authentifizierungstokendienst zurückgegeben wird.|Header   |Zeichenfolge|
|Ocp-Apim-Subscription-Key|(leer)|Erforderlich, falls der Header `Authorization` nicht angegeben ist.|Header|Zeichenfolge|
|access_token|(leer)   |Alternative Möglichkeit für die Übergabe eines gültigen OAuth-Zugriffstokens. Das Bearertoken wird in der Regel mit dem Header `Authorization` bereitgestellt. In einigen WebSocket-Bibliotheken dürfen mit Clientcode keine Header festgelegt werden. In diesem Fall kann der Client den Abfrageparameter `access_token` für die Übergabe eines gültigen Tokens verwenden. Bei der Verwendung eines Zugriffstokens für die Authentifizierung muss `access_token` festgelegt werden, wenn der Header `Authorization` nicht festgelegt ist. Wenn sowohl der Header als auch der Abfrageparameter festgelegt sind, wird der Abfrageparameter ignoriert. Clients sollten nur eine Methode für die Übergabe des Tokens verwenden.|query|Zeichenfolge|
|subscription-key|(leer)   |Alternative Möglichkeit für die Übergabe eines Abonnementschlüssels. In einigen WebSocket-Bibliotheken dürfen mit Clientcode keine Header festgelegt werden. In diesem Fall kann der Client den Abfrageparameter `subscription-key` für die Übergabe eines gültigen Abonnementschlüssels verwenden. Wenn Sie einen Abonnementschlüssel für die Authentifizierung verwenden, muss „subscription-key“ festgelegt werden, wenn der Header `Ocp-Apim-Subscription-Key` nicht festgelegt ist. Wenn sowohl der Header als auch der Abfrageparameter festgelegt sind, wird der Abfrageparameter ignoriert. Clients sollten nur eine Methode für die Übergabe des `subscription key` verwenden.|query|Zeichenfolge|
|X-ClientTraceId    |(leer)    |Eine vom Client generierte GUID zur Ablaufverfolgung einer Anforderung. Zur ordnungsgemäßen Problembehandlung sollten Clients mit jeder Anforderung einen neuen Wert angeben und protokollieren.<br/>Dieser Wert kann anstelle der Verwendung eines Headers mit dem Abfrageparameter `X-ClientTraceId` übergeben werden. Wenn sowohl der Header als auch der Abfrageparameter festgelegt sind, wird der Abfrageparameter ignoriert.|Header|Zeichenfolge|
|X-CorrelationId|(leer)    |Ein vom Client generierter Bezeichner zum Korrelieren mehrere Kanäle in einer Konversation. Es können mehrere Sitzungen zur Sprachübersetzung erstellt werden, um Konversationen zwischen Benutzern zu ermöglichen. In solchen Szenarios wird in allen Sitzungen zur Sprachübersetzung die gleiche Korrelations-ID zur Verbindung der Kanäle verwendet. Dies erleichtert die Ablaufverfolgung und die Diagnose. Der Bezeichner sollte `^[a-zA-Z0-9-_.]{1,64}$` entsprechen.<br/>Dieser Wert kann anstelle der Verwendung eines Headers mit dem Abfrageparameter `X-CorrelationId` übergeben werden. Wenn sowohl der Header als auch der Abfrageparameter festgelegt sind, wird der Abfrageparameter ignoriert.|Header|Zeichenfolge|
|X-ClientVersion|(leer)    |Gibt die Version der Clientanwendung an. Beispiel: „2.1.0.123“.<br/>Dieser Wert kann anstelle der Verwendung eines Headers mit dem Abfrageparameter `X-ClientVersion` übergeben werden. Wenn sowohl der Header als auch der Abfrageparameter festgelegt sind, wird der Abfrageparameter ignoriert.|Header|Zeichenfolge|
|X-OsPlatform|(leer)   |Gibt den Namen und Version des Betriebssystems an, unter dem die Clientanwendung ausgeführt wird. Beispiele: „Android 5.0“, „iOS 8.1.3“, „Windows 8.1“.<br/>Dieser Wert kann anstelle der Verwendung eines Headers mit dem Abfrageparameter `X-OsPlatform` übergeben werden. Wenn sowohl der Header als auch der Abfrageparameter festgelegt sind, wird der Abfrageparameter ignoriert.|Header|Zeichenfolge|

### <a name="response-messages"></a>Antwortnachrichten

|HTTP-Statuscode|`Reason`|Antwortmodell|Header|
|:--|:--|:--|:--|
|101    |WebSocket-Upgrade.|Beispielwert für das Modell <br/> Objekt {}|X-RequestId<br/>Ein Wert, der die Anforderung für die Problembehandlung identifiziert.<br/>Zeichenfolge|
|400    |Ungültige Anforderung. Überprüfen Sie die Eingabeparameter, und stellen Sie sicher, dass sie gültig sind. Das Antwortobjekt enthält eine ausführlichere Beschreibung des Fehlers.|||
|401    |Nicht autorisiert. Stellen Sie sicher, dass Anmeldeinformationen festgelegt sind, dass diese gültig sind und dass es bei Ihrem Abonnement für den Azure-Datenmarkt keine Probleme gibt und ein Saldo verfügbar ist.|||
|500    |Ein Fehler ist aufgetreten. Wenn der Fehler weiterhin besteht, melden Sie ihn mit dem Ablaufverfolgungsbezeichner des Clients (X-ClientTraceId) oder dem Anforderungsbezeichner (X-RequestId).|||
|503    |Der Server ist vorübergehend nicht verfügbar. Versuchen Sie die Anforderung erneut. Wenn der Fehler weiterhin besteht, melden Sie ihn mit dem Ablaufverfolgungsbezeichner des Clients (X-ClientTraceId) oder dem Anforderungsbezeichner (X-RequestId).|||
