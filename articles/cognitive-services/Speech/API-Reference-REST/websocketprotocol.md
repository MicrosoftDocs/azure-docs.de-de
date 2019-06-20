---
title: WebSocket-Protokoll für die Bing-Spracheingabe | Microsoft-Dokumentation
titlesuffix: Azure Cognitive Services
description: Protokolldokumentation für die Bing-Spracheingabe basierend auf WebSockets
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d6601f57d87b518b2061df64174818432b822755
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515321"
---
# <a name="bing-speech-websocket-protocol"></a>WebSocket-Protokoll für die Bing-Spracheingabe

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Die Bing-Spracheingabe ist eine cloudbasierte Plattform mit den am weitesten entwickelten Algorithmen, die für die Konvertierung von Sprachaudiodaten in Text verfügbar sind. Mit dem Protokoll für die Bing-Spracheingabe wird die [Verbindungseinrichtung](#connection-establishment) zwischen Clientanwendungen und dem Dienst und den Spracherkennungsnachrichten definiert, die zwischen den beiden Seiten ausgetauscht werden ([Nachrichten vom Client](#client-originated-messages) und [Nachrichten vom Dienst](#service-originated-messages)). Außerdem werden [Telemetrienachrichten](#telemetry-schema) und die [Fehlerbehandlung](#error-handling) beschrieben.

## <a name="connection-establishment"></a>Verbindungsherstellung

Für das Spracherkennungsdienst-Protokoll wird die WebSocket-Standardspezifikation [IETF RFC 6455](https://tools.ietf.org/html/rfc6455) beachtet. Am Anfang einer WebSocket-Verbindung steht eine HTTP-Anforderung mit HTTP-Headern, mit denen die Absicht des Clients angegeben wird, für die Verbindung ein Upgrade auf ein WebSocket durchzuführen, anstatt die HTTP-Semantik zu verwenden. Der Server gibt die Bereitschaft an, sich an der WebSocket-Verbindung zu beteiligen, indem die HTTP-Antwort `101 Switching Protocols` zurückgegeben wird. Nach dem Austausch dieses Handshakes lassen sowohl der Client als auch der Dienst den Socket geöffnet und beginnen damit, ein nachrichtenbasiertes Protokoll zum Senden und Empfangen von Informationen zu nutzen.

Zum Starten des Vorgangs für den WebSocket-Handshake sendet die Clientanwendung eine HTTPS GET-Anforderung an den Dienst. Sie enthält WebSocket-Standardheader für das Upgrade und weitere sprachspezifische Header.

```HTTP
GET /speech/recognition/interactive/cognitiveservices/v1 HTTP/1.1
Host: speech.platform.bing.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
Authorization: t=EwCIAgALBAAUWkziSCJKS1VkhugDegv7L0eAAJqBYKKTzpPZOeGk7RfZmdBhYY28jl&p=
X-ConnectionId: A140CAF92F71469FA41C72C7B5849253
Origin: https://speech.platform.bing.com
```

Der Dienst antwortet wie folgt:

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

Für alle Sprachanforderungen ist die [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security)-Verschlüsselung erforderlich. Die Verwendung von unverschlüsselten Sprachanforderungen wird nicht unterstützt. Die folgende TLS-Version wird unterstützt:

* TLS 1.2

### <a name="connection-identifier"></a>Verbindungsbezeichner

Für den Spracherkennungsdienst ist es erforderlich, dass alle Clients eine eindeutige ID zum Identifizieren der Verbindung enthalten. Clients *müssen* den *X-ConnectionId*-Header enthalten, wenn sie einen WebSocket-Handshake starten. Der *X-ConnectionId*-Header muss ein [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)-Wert (Universally Unique Identifier) sein. WebSocket-Upgradeanforderungen, die *X-ConnectionId* nicht enthalten, keinen Wert für den *X-ConnectionId*-Header angeben oder keinen gültigen UUID-Wert enthalten, werden vom Dienst mit der HTTP-Antwort `400 Bad Request` abgelehnt.

### <a name="authorization"></a>Autorisierung

Zusätzlich zu Standardheadern für den WebSocket-Handshake wird für Sprachanforderungen ein *Authorization*-Header benötigt. Verbindungsanforderungen ohne diesen Header werden vom Dienst mit der HTTP-Antwort `403 Forbidden` abgelehnt.

Der *Authorization*-Header muss ein JWT-Zugriffstoken (JSON Web Token) enthalten.

Informationen dazu, wie Sie API-Schlüssel abonnieren und beziehen, die zum Abrufen von gültigen JWT-Zugriffstoken verwendet werden, finden Sie auf der Seite [Cognitive Services ausprobieren](https://azure.microsoft.com/try/cognitive-services/).

Der API-Schlüssel wird an den Tokendienst übergeben. Beispiel:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Für den Tokenzugriff sind die folgenden Headerinformationen erforderlich.

| NAME | Format | BESCHREIBUNG |
|----|----|----|
| Ocp-Apim-Subscription-Key | ASCII | Your subscription key (Ihr Abonnementschlüssel) |

Der Tokendienst gibt das JWT-Zugriffstoken im Format `text/plain` zurück. Das JWT wird dann im Format `Base64 access_token` als *Authorization*-Header mit der Zeichenfolge `Bearer` als Präfix an den Handshake übergeben. Beispiel:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Cookies

Clients *müssen* HTTP-Cookies unterstützen, wie unter [RFC 6265](https://tools.ietf.org/html/rfc6265) angegeben.

### <a name="http-redirection"></a>HTTP-Umleitung

Clients *müssen* die Standardumleitungsmechanismen unterstützen, die über die [HTTP-Protokollspezifikation](https://www.w3.org/Protocols/rfc2616/rfc2616.html) angegeben werden.

### <a name="speech-endpoints"></a>Sprachendpunkte

Clients *müssen* einen geeigneten Endpunkt des Spracherkennungsdiensts nutzen. Der Endpunkt basiert auf dem Erkennungsmodus und der Sprache. In der Tabelle sind einige Beispiele angegeben.

| Mode | `Path` | Dienst-URI |
| -----|-----|-----|
| Interactive | /speech/recognition/interactive/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Unterhaltung | /speech/recognition/conversation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| Diktieren | /speech/recognition/dictation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

Weitere Informationen finden Sie auf der Seite zum [Dienst-URI](../GetStarted/GetStartedREST.md#service-uri).

### <a name="report-connection-problems"></a>Melden von Verbindungsproblemen

Clients sollten sofort alle Probleme melden, die beim Herstellen einer Verbindung auftreten. Das Nachrichtenprotokoll für das Melden von fehlgeschlagenen Verbindungen ist unter [Telemetriedaten für Verbindungsfehler](#connection-failure-telemetry) beschrieben.

### <a name="connection-duration-limitations"></a>Einschränkungen der Verbindungsdauer

Im Vergleich zu typischen Webdienst-HTTP-Verbindungen haben WebSocket-Verbindungen eine *lange* Dauer. Der Spracherkennungsdienst schränkt die Dauer der WebSocket-Verbindungen mit dem Dienst ein:

 * Die maximale Dauer für aktive WebSocket-Verbindungen beträgt 10 Minuten. Eine Verbindung ist aktiv, wenn entweder der Dienst oder der Client WebSocket-Nachrichten über diese Verbindung sendet. Der Dienst beendet die Verbindung ohne Warnung, wenn die Höchstdauer erreicht ist. Für Clients sollten Benutzerszenarien entwickelt werden, bei denen die Verbindung nicht so lange aktiv bleiben muss, dass die maximale Verbindungslebensdauer erreicht bzw. fast erreicht wird.

 * Die maximale Dauer für inaktive WebSocket-Verbindungen beträgt 180 Sekunden. Eine Verbindung ist inaktiv, wenn weder der Dienst noch der Client eine WebSocket-Nachricht über die Verbindung sendet. Nachdem die maximale Lebensdauer für Inaktivität erreicht wurde, beendet der Dienst die inaktive WebSocket-Verbindung.

## <a name="message-types"></a>Nachrichtentypen

Nachdem zwischen dem Client und dem Dienst eine WebSocket-Verbindung hergestellt wurde, kann sowohl der Client als auch der Dienst Nachrichten senden. In diesem Abschnitt wird das Format dieser WebSocket-Nachrichten beschrieben.

In der Spezifikation [IETF RFC 6455](https://tools.ietf.org/html/rfc6455) ist angegeben, dass WebSocket-Nachrichten Daten übertragen können, indem entweder eine Text- oder binäre Codierung verwendet wird. Für die beiden Codierungen werden unterschiedliche „On-the-Wire-Formate“ verwendet. Jedes Format ist für das effiziente Codieren, Übertragen und Decodieren der Nachrichtennutzlast optimiert.

### <a name="text-websocket-messages"></a>WebSocket-Textnachrichten

WebSocket-Textnachrichten verfügen über eine Nutzlast mit Textinformationen, die einen Abschnitt mit Headern und einen Text umfassen, die durch die vertrauten doppelten Absatzzeichenpaare für HTTP-Nachrichten getrennt sind. Und wie bei HTTP-Nachrichten auch, werden in WebSocket-Textnachrichten Header im Format *Name:Wert* getrennt durch ein einzelnes Absatzzeichenpaar angegeben. Für alle Texte, die in einer WebSocket-Textnachricht enthalten sind, *muss* die [UTF-8](https://tools.ietf.org/html/rfc3629)-Codierung verwendet werden.

Bei WebSocket-Textnachrichten muss ein Nachrichtenpfad im *Path*-Header angegeben werden. Der Wert dieses Headers muss einen der Sprachprotokollnachricht-Typen aufweisen, die weiter unten in diesem Dokument definiert sind.

### <a name="binary-websocket-messages"></a>Binäre WebSocket-Nachrichten

Binäre WebSocket-Nachrichten verfügen über eine binäre Nutzlast. Im Spracherkennungsdienst-Protokoll werden Audiodaten an den bzw. vom Dienst übertragen, indem binäre WebSocket-Nachrichten verwendet werden. Alle andere Nachrichten sind WebSocket-Textnachrichten.

Wie WebSocket-Textnachrichten auch, bestehen binäre WebSocket-Nachrichten aus einem Header und einem Textabschnitt. In den ersten beiden Bytes der binären WebSocket-Nachricht ist in [Big-Endian](https://en.wikipedia.org/wiki/Endianness)-Reihenfolge die 16-Bit-Integergröße des Headerabschnitts angegeben. Die Mindestgröße für den Headerabschnitt beträgt 0 Byte. Die maximale Größe beträgt 8.192 Byte. Für den Text in den Headern von binären WebSocket-Nachrichten *muss* die [US-ASCII](https://tools.ietf.org/html/rfc20)-Codierung verwendet werden.

Header in einer binären WebSocket-Nachricht sind in demselben Format wie in WebSocket-Textnachrichten codiert. Das Format *Name:Wert* ist durch ein einzelnes Absatzzeichenpaar getrennt. Bei binären WebSocket-Nachrichten muss ein Nachrichtenpfad im *Path*-Header angegeben werden. Der Wert dieses Headers muss einen der Sprachprotokollnachricht-Typen aufweisen, die weiter unten in diesem Dokument definiert sind.

Im Spracherkennungsdienst-Protokoll werden WebSocket-Nachrichten sowohl in Textform als auch in binärer Form verwendet.

## <a name="client-originated-messages"></a>Nachrichten vom Client

Nachdem die Verbindung hergestellt wurde, können sowohl der Client als auch der Dienst mit dem Senden von Nachrichten beginnen. In diesem Abschnitt werden das Format und die Nutzlast von Nachrichten beschrieben, die von Clientanwendungen an den Spracherkennungsdienst gesendet werden. Im Abschnitt [Nachrichten vom Dienst](#service-originated-messages) sind die Nachrichten angegeben, die vom Spracherkennungsdienst stammen und an die Clientanwendungen gesendet werden.

Die Hauptnachrichten, die vom Client an die Dienste gesendet werden, sind Nachrichten der Art `speech.config`, `audio` und `telemetry`. Bevor wir uns die einzelnen Nachrichten genauer ansehen, werden die allgemein erforderlichen Nachrichtenheader für diese Nachrichten beschrieben.

### <a name="required-message-headers"></a>Erforderliche Nachrichtenheader

Die unten angegebenen Header sind für alle vom Client stammenden Nachrichten erforderlich.

| Header | Wert |
|----|----|
| `Path` | Der Nachrichtenpfad gemäß Angabe in diesem Dokument |
| X-RequestId | UUID im Format „no-dash“ |
| X-Timestamp | Zeitstempel der UTC-Uhrzeit des Clients im Format ISO 8601 |

#### <a name="x-requestid-header"></a>X-RequestId-Header

Vom Client stammende Anforderungen werden mit dem *X-RequestId*-Nachrichtenheader eindeutig identifiziert. Dieser Header ist für alle vom Client stammenden Nachrichten erforderlich. Der Wert des *X-RequestId*-Headers muss eine UUID im Format „no-dash“ (ohne Bindestriche) sein, z.B. *123e4567e89b12d3a456426655440000*. Das kanonische Format *123e4567-e89b-12d3-a456-426655440000* ist *nicht möglich*. Anforderungen ohne *X-RequestId*-Header oder mit einem Headerwert, für die ein falsches Format für UUIDs verwendet wird, führen dazu, dass der Dienst die WebSocket-Verbindung beendet.

#### <a name="x-timestamp-header"></a>X-Timestamp-Header

Jede Nachricht, die vom Spracherkennungsdienst an eine Clientanwendung gesendet wird, *muss* einen *X-Timestamp*-Header enthalten. Der Wert für diesen Header ist die Uhrzeit, zu der der Client die Nachricht sendet. Anforderungen ohne *X-Timestamp*-Header oder mit einem Headerwert, für die ein falsches Format verwendet wird, führen dazu, dass der Dienst die WebSocket-Verbindung beendet.

Der Wert des *X-Timestamp*-Headers muss das folgende Format aufweisen: 'jjjj'-'MM'-'tt'T'HH':'mm':'ss'.'fffffffZ'. Hierbei steht „fffffffZ“ für den Bruchteil einer Sekunde. Beispielsweise steht der Wert „12,5“ für „12 + 5/10 Sekunden“ und „12,526“ für „12 plus 526/1000 Sekunden“. Dieses Format erfüllt die Anforderungen von [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) und ermöglicht – im Gegensatz zum HTTP-Standardheader *Date* – eine Auflösung auf Millisekundenebene. Clientanwendungen runden Zeitstempel ggf. auf die nächste Millisekunde. Für Clientanwendungen muss sichergestellt werden, dass die Geräteuhr die Uhrzeit genau nachverfolgt, indem ein [NTP-Server (Network Time Protocol, Netzwerkzeitprotokoll)](https://en.wikipedia.org/wiki/Network_Time_Protocol) verwendet wird.

### <a name="message-speechconfig"></a>`speech.config`-Nachricht

Der Spracherkennungsdienst muss die Merkmale Ihrer Anwendung kennen, um die bestmögliche Spracherkennung bereitstellen zu können. Die Daten zu den erforderlichen Merkmalen umfassen Informationen zum Gerät und zum Betriebssystem, auf dem Ihre Anwendung basiert. Sie geben diese Informationen in der `speech.config`-Nachricht an.

Clients *müssen* sofort eine `speech.config`-Nachricht senden, nachdem sie die Verbindung mit dem Spracherkennungsdienst hergestellt haben und bevor sie `audio`-Nachrichten senden. Sie müssen eine `speech.config`-Nachricht nur einmal pro Verbindung senden.

| Feld | BESCHREIBUNG |
|----|----|
| Codierung von WebSocket-Nachrichten | Text |
| Body | Nutzlast als JSON-Struktur |

#### <a name="required-message-headers"></a>Erforderliche Nachrichtenheader

| Headername | Wert |
|----|----|
| `Path` | `speech.config` |
| X-Timestamp | Zeitstempel der UTC-Uhrzeit des Clients im Format ISO 8601 |
| Content-Typ | application/json; charset=utf-8 |

Wie bei allen vom Client stammenden Nachrichten im Spracherkennungsdienst-Protokoll, *muss* die `speech.config`-Nachricht einen *X-Timestamp*-Header enthalten, mit dem die UTC-Uhrzeit des Clients für den Zeitpunkt aufgezeichnet wird, zu dem die Nachricht an den Dienst gesendet wurde. Für die `speech.config`-Nachricht ist *kein* *X-RequestId*-Header erforderlich, da diese Nachricht nicht einer bestimmten Sprachanforderung zugeordnet ist.

#### <a name="message-payload"></a>Nachrichtennutzlast
Die Nutzlast der `speech.config`-Nachricht ist eine JSON-Struktur, die Informationen zur Anwendung enthält. Diese Informationen sind im folgenden Beispiel dargestellt. Informationen im Client- und Gerätekontext sind im *context*-Element der JSON-Struktur enthalten.

```JSON
{
  "context": {
    "system": {
      "version": "2.0.12341",
    },
    "os": {
      "platform": "Linux",
      "name": "Debian",
      "version": "2.14324324"
    },
    "device": {
      "manufacturer": "Contoso",
      "model": "Fabrikan",
      "version": "7.341"
      }
    },
  }
}
```

##### <a name="system-element"></a>Systemelement

Das system.version-Element der `speech.config`-Nachricht enthält die Version der Sprach-SDK-Software, die von der Clientanwendung oder vom Gerät verwendet wird. Der Wert hat das Format *major.minor.build.branch*. Sie können die Komponente *branch* weglassen, falls sie nicht benötigt wird.

##### <a name="os-element"></a>Betriebssystemelement

| Feld | BESCHREIBUNG | Verwendung |
|-|-|-|
| os.platform | Die Betriebssystemplattform, auf der die Anwendung gehostet wird, z.B. Windows, Android, iOS oder Linux |Erforderlich |
| os.name | Der Produktname des Betriebssystems, z.B. Debian oder Windows 10 | Erforderlich |
| os.version | Die Version des Betriebssystems im Format *major.minor.build.branch* | Erforderlich |

##### <a name="device-element"></a>Geräteelement

| Feld | BESCHREIBUNG | Verwendung |
|-|-|-|
| device.manufacturer | Hersteller der Gerätehardware | Erforderlich |
| device.model | Gerätemodell | Erforderlich |
| device.version | Die Version der Gerätesoftware, die vom Gerätehersteller angegeben wird. Mit diesem Wert wird eine Version des Geräts angegeben, die vom Hersteller nachverfolgt werden kann. | Erforderlich |

### <a name="message-audio"></a>`audio`-Nachricht

Für Sprache aktivierte Clientanwendungen senden Audiodaten an den Spracherkennungsdienst, indem der Audiodatenstrom in eine Reihe von Audioblöcken unterteilt wird. Mit jedem Audioblock wird ein Segment mit den Sprachaudiodaten übertragen, das vom Dienst transkribiert werden muss. Die maximale Größe eines einzelnen Audioblocks beträgt 8.192 Byte. Bei Audiodatenstrom-Nachrichten handelt es sich um *Binäre WebSocket-Nachrichten*.

Clients nutzen die `audio`-Nachricht, um einen Audioblock an den Dienst zu senden. Clients lesen Audiodaten in Blöcken über das Mikrofon und senden diese Blöcke zum Transkribieren an den Spracherkennungsdienst. Die erste `audio`-Nachricht muss einen wohlgeformten Header enthalten, über den richtig angegeben wird, dass die Audiodaten mit einem der vom Dienst unterstützten Codierungsformate konform sind. Zusätzliche `audio`-Nachrichten enthalten nur den binären Audiodatenstrom, der über das Mikrofon eingelesen wird.

Clients können optional eine `audio`-Nachricht mit einer Textlänge von null senden. Über diese Nachricht wird dem Dienst mitgeteilt, dass der Client über die Beendigung der Spracheingabe durch den Benutzer informiert ist, die Äußerung beendet ist und das Mikrofon ausgeschaltet wurde.

Der Spracherkennungsdienst verwendet die erste `audio`-Nachricht, die einen eindeutigen Anforderungsbezeichner enthält, um den Beginn eines neuen Anforderung/Antwort-Zyklus oder Vorgangs (als *Turn* bezeichnet) zu signalisieren. Nachdem der Dienst eine `audio`-Nachricht mit einem neuen Anforderungsbezeichner empfangen hat, werden alle in die Warteschlange eingereihten oder nicht gesendeten Nachrichten verworfen, die einem vorherigen „Turn“ zugeordnet sind.

| Feld | BESCHREIBUNG |
|-------------|----------------|
| Codierung von WebSocket-Nachrichten | Binary |
| Body | Die Binärdaten für den Audioblock. Die maximale Größe beträgt 8.192 Byte. |

#### <a name="required-message-headers"></a>Erforderliche Nachrichtenheader

Die folgenden Header sind für alle `audio`-Nachrichten erforderlich.

| Header         |  Wert     |
| ------------- | ---------------- |
| `Path` | `audio` |
| X-RequestId | UUID im Format „no-dash“ |
| X-Timestamp | Zeitstempel der UTC-Uhrzeit des Clients im Format ISO 8601 |
| Content-Typ | Der Audioinhaltstyp. Der Typ muss entweder *audio/x-wav* (PCM) oder *audio/silk* (SILK) lauten. |

#### <a name="supported-audio-encodings"></a>Unterstützte Audiocodierungen

In diesem Abschnitt sind die vom Spracherkennungsdienst unterstützten Audiocodecs beschrieben.

##### <a name="pcm"></a>PCM

Der Spracherkennungsdienst akzeptiert unkomprimierte PCM-Audiodaten (Pulse Code Modulation). Da Audiodaten im [WAV](https://en.wikipedia.org/wiki/WAV)-Format an den Dienst gesendet werden, *muss* der erste Audioblock einen gültigen [RIFF](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format)-Header (Resource Interchange File Format) enthalten. Wenn ein Client einen „Turn“ mit einem Audioblock initiiert, der *keinen* gültigen RIFF-Header enthält, lehnt der Dienst die Anforderung ab und beendet die WebSocket-Verbindung.

Das Sampling von PCM-Audiodaten *muss* mit 16 kHz und 16 Bit pro Abtastvorgang sowie mit einem Kanal (*riff-16khz-16bit-mono-pcm*) durchgeführt werden. Der Spracherkennungsdienst unterstützt keine Stereo-Audiodatenströme und lehnt Audiodatenströme ab, für die nicht die angegebene Bitrate, Abtastrate oder Anzahl von Kanälen verwendet wird.

##### <a name="opus"></a>Opus

Opus ist ein offener, gebührenfreier und sehr vielseitiger Audiocodec. Der Spracherkennungsdienst unterstützt Opus mit einer konstanten Bitrate von `32000` oder `16000`. Nur der Container `OGG` für Opus, der über den MIME-Typ `audio/ogg` angegeben wird, wird derzeit unterstützt.

Ändern Sie zum Nutzen von Opus das [JavaScript-Beispiel](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101), und ändern Sie die `RecognizerSetup`-Methode in den Rückgabemodus.

```javascript
return SDK.CreateRecognizerWithCustomAudioSource(
            recognizerConfig,
            authentication,
            new SDK.MicAudioSource(
                     new SDK.OpusRecorder(
                     {
                         mimeType: "audio/ogg",
                         bitsPerSecond: 32000
                     }
              )
          ));
```

#### <a name="detect-end-of-speech"></a>Erkennen des Endes der Spracheingabe

Menschen geben nicht explizit an, dass sie die Spracheingabe beendet haben. Alle Anwendungen, die Sprache als Eingabe akzeptieren, haben zwei Möglichkeiten, um das Ende der Spracheingabe in einem Audiodatenstrom zu verarbeiten: Erkennung auf Dienstseite und Erkennung auf Clientseite. Die Erkennung auf Dienstseite bietet hierbei normalerweise eine höhere Benutzerfreundlichkeit.

##### <a name="service-end-of-speech-detection"></a>Erkennung des Endes der Spracheingabe auf Dienstseite

Um ein möglichst gutes Freisprechen zu ermöglichen, erlauben Anwendungen es dem Dienst, die Schritte zur Erkennung des Endes der Spracheingabe durch den Benutzer auszuführen. Clients senden über das Mikrofon eingegangene Audiodaten als *audio*-Blöcke, bis der Dienst keine Sprache mehr wahrnimmt und mit der Nachricht `speech.endDetected` antwortet.

##### <a name="client-end-of-speech-detection"></a>Erkennung des Endes der Spracheingabe auf Clientseite

Clientanwendungen, die für Benutzer das Signalisieren des Endes der Spracheingabe ermöglichen, können ebenfalls das entsprechende Signal an den Dienst übermitteln. Beispielsweise kann eine Clientanwendung über die Schaltfläche „Beenden“ oder „Stumm schalten“ verfügen, die Benutzer betätigen können. Um das Ende der Spracheingabe zu signalisieren, senden Clientanwendungen eine *audio*-Blocknachricht mit einer Textlänge von null. Der Spracherkennungsdienst interpretiert diese Nachricht als das Ende des eingehenden Audiodatenstroms.

### <a name="message-telemetry"></a>`telemetry`-Nachricht

Clientanwendungen *müssen* das Ende jedes „Turns“ bestätigen, indem Telemetriedaten dazu an den Spracherkennungsdienst gesendet werden. Durch die Bestätigung des „Turn“-Endes kann der Spracherkennungsdienst sicherstellen, dass alle Nachrichten, die für den Abschluss des Anforderung/Antwort-Vorgangs erforderlich sind, vom Client richtig empfangen wurden. Außerdem kann der Spracherkennungsdienst durch das Bestätigen des „Turn“-Endes auch überprüfen, ob die Clientanwendungen wie erwartet funktionieren. Diese Informationen sind wertvoll, falls Sie Hilfe bei der Problembehandlung für Ihre sprachaktivierte Anwendung benötigen.

Clients müssen das Ende eines „Turns“ bestätigen, indem sie eine `telemetry`-Nachricht senden, kurz nachdem sie eine `turn.end`-Nachricht erhalten haben. Clients sollten versuchen, `turn.end` so bald wie möglich zu bestätigen. Wenn eine Clientanwendung das Ende eines „Turns“ nicht bestätigt, beendet der Spracherkennungsdienst die Verbindung unter Umständen mit einem Fehler. Clients müssen nur eine `telemetry`-Nachricht pro Anforderung und Antwort senden, die jeweils über den Wert von *X-RequestId* identifiziert werden.

| Feld | BESCHREIBUNG |
| ------------- | ---------------- |
| Codierung von WebSocket-Nachrichten | Text |
| `Path` | `telemetry` |
| X-Timestamp | Zeitstempel der UTC-Uhrzeit des Clients im Format ISO 8601 |
| Content-Typ | `application/json` |
| Body | Eine JSON-Struktur, die Clientinformationen zum „Turn“ enthält. |

Eine Definition des Schemas für den Text der `telemetry`-Nachricht finden Sie im Abschnitt [Schema der Telemetriedaten](#telemetry-schema).

#### <a name="telemetry-for-interrupted-connections"></a>Telemetriedaten für unterbrochene Verbindungen

Wenn die Netzwerkverbindung während eines „Turns“ aus irgendeinem Grund fehlschlägt und der Client *keine* `turn.end`-Nachricht vom Dienst empfängt, sendet der Client eine `telemetry`-Nachricht. In dieser Nachricht wird die fehlgeschlagene Anforderung für die nächste Verbindungsherstellung mit dem Dienst durch den Client beschrieben. Clients müssen nicht sofort versuchen, eine Verbindung herzustellen, um die `telemetry`-Nachricht zu senden. Die Nachricht wird ggf. auf dem Client gepuffert und über eine zukünftige vom Benutzer angeforderte Verbindung gesendet. Die `telemetry`-Nachricht für die fehlgeschlagene Anforderung *muss* den Wert von *X-RequestId* aus der fehlgeschlagenen Anforderung verwenden. Dieser kann an den Dienst gesendet werden, sobald eine Verbindung hergestellt wurde, ohne auf das Senden oder Empfangen anderer Nachrichten zu warten.

## <a name="service-originated-messages"></a>Nachrichten vom Dienst

In diesem Abschnitt werden die Nachrichten beschrieben, die vom Spracherkennungsdienst stammen und an den Client gesendet werden. Der Spracherkennungsdienst umfasst eine Registrierung mit Clientfunktionen und generiert die Nachrichten, die für die einzelnen Clients erforderlich sind. Es ist also so, dass nicht alle Clients alle hier beschriebenen Nachrichten erhalten. Zur Verbesserung der Übersichtlichkeit wird auf Nachrichten anhand des Werts des *Path*-Headers verwiesen. Auf eine WebSocket-Textnachricht mit dem *Path*-Wert `speech.hypothesis` wird beispielsweise als „speech.hypothesis“-Nachricht verwiesen.

### <a name="message-speechstartdetected"></a>`speech.startDetected`-Nachricht

Mit der `speech.startDetected`-Nachricht wird angegeben, dass der Spracherkennungsdienst Sprachdaten im Audiodatenstrom erkannt hat.

| Feld | BESCHREIBUNG |
| ------------- | ---------------- |
| Codierung von WebSocket-Nachrichten | Text |
| `Path` | `speech.startDetected` |
| Content-Typ | application/json; charset=utf-8 |
| Body | Die JSON-Struktur mit Informationen zu den Bedingungen, die bei der Erkennung der Spracheingabe geherrscht haben. Über das Feld *Offset* dieser Struktur wird der Versatz (in Einheiten von 100 Nanosekunden) angegeben, der zwischen der Erkennung der Sprachdaten im Audiodatenstrom und dem Beginn des Datenstroms besteht. |

#### <a name="sample-message"></a>Beispielnachricht

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>`speech.hypothesis`-Nachricht

Während der Spracherkennung generiert der Spracherkennungsdienst regelmäßig Hypothesen zu den vom Dienst erkannten Wörtern. Der Spracherkennungsdienst sendet diese Hypothesen ca. alle 300 Millisekunden an den Client. `speech.hypothesis` ist *nur* für die Verbesserung der Benutzerfreundlichkeit bei der Spracheingabe geeignet. Es darf keine Abhängigkeit vom Inhalt oder von der Genauigkeit des Texts in diesen Nachrichten bestehen.

 Die `speech.hypothesis`-Nachricht gilt für die Clients, die über eine Funktion für das Textrendering verfügen und die für die sprechende Person nahezu in Echtzeit Feedback zur laufenden Erkennung bereitstellen sollen.

| Feld | BESCHREIBUNG |
| ------------- | ---------------- |
| Codierung von WebSocket-Nachrichten | Text |
| `Path` | `speech.hypothesis` |
| X-RequestId | UUID im Format „no-dash“ |
| Content-Typ | Anwendung/json |
| Body | JSON-Struktur für die Sprachhypothese |

#### <a name="sample-message"></a>Beispielnachricht

```HTML
Path: speech.hypothesis
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Text": "this is a speech hypothesis",
  "Offset": 0,
  "Duration": 23600000
}
```

Mit dem *Offset*-Element wird der Versatz (in Einheiten von 100 Nanosekunden) angegeben, der zwischen der Erkennung des Ausdrucks und dem Beginn des Audiodatenstroms besteht.

Mit dem *Duration*-Element wird die Dauer (in Einheiten von 100 Nanosekunden) für diese gesprochenen Wörter angegeben.

Clients dürfen keine Annahmen zu Häufigkeit, Timing oder Text einer Sprachhypothese oder zur Konsistenz von Text in zwei beliebigen Sprachhypothesen treffen. Die Hypothesen sind nur Momentaufnahmen im Transkriptionsprozess des Diensts. Sie stellen keine stabile Akkumulation in Bezug auf die Transkription dar. Beispielsweise kann die erste Sprachhypothese im Englischen die Wörter „fine fun“ und die zweite Hypothese die Wörter „find funny“ enthalten. Der Spracherkennungsdienst führt keine Nachbearbeitung (z.B. Großschreibung, Zeichensetzung) für den Text der Sprachhypothese durch.

### <a name="message-speechphrase"></a>`speech.phrase`-Nachricht

Wenn der Spracherkennungsdienst ermittelt, dass er über ausreichende Informationen zum Produzieren eines Erkennungsergebnisses verfügt, das sich nicht ändert, erstellt der Dienst eine `speech.phrase`-Nachricht. Der Spracherkennungsdienst produziert diese Ergebnisse, nachdem er erkannt hat, dass der Benutzer einen Satz oder einen Ausdruck beendet hat.

| Feld | BESCHREIBUNG |
| ------------- | ---------------- |
| Codierung von WebSocket-Nachrichten | Text |
| `Path` | `speech.phrase` |
| Content-Typ | Anwendung/json |
| Body | JSON-Struktur des gesprochenen Ausdrucks |

Das JSON-Schema für gesprochene Ausdrücke enthält die folgenden Felder: `RecognitionStatus`, `DisplayText`, `Offset` und `Duration`. Weitere Informationen zu diesen Feldern finden Sie unter [Transcription responses](../concepts.md#transcription-responses) (Transkriptionsantworten).

#### <a name="sample-message"></a>Beispielnachricht

```HTML
Path: speech.phrase
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": 0,
  "Duration": 12300000
}
```

### <a name="message-speechenddetected"></a>`speech.endDetected`-Nachricht

Die `speech.endDetected`-Nachricht gibt an, dass die Clientanwendung das Streamen von Audiodaten an den Dienst beenden soll.

| Feld | BESCHREIBUNG |
| ------------- | ---------------- |
| Codierung von WebSocket-Nachrichten | Text |
| `Path` | `speech.endDetected` |
| Body | Die JSON-Struktur mit dem Versatz für die Erkennung des Endes der Spracheingabe. Der Versatz wird in Einheiten von 100 Nanosekunden ab dem Beginn der Audiodaten dargestellt, die für die Erkennung verwendet werden. |
| Content-Typ | application/json; charset=utf-8 |

#### <a name="sample-message"></a>Beispielnachricht

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

Mit dem *Offset*-Element wird der Versatz (in Einheiten von 100 Nanosekunden) angegeben, der zwischen der Erkennung des Ausdrucks und dem Beginn des Audiodatenstroms besteht.

### <a name="message-turnstart"></a>`turn.start`-Nachricht

Mit `turn.start` wird der Beginn eines „Turns“ aus Sicht des Diensts signalisiert. Die `turn.start`-Nachricht ist immer die erste Antwortnachricht, die Sie für eine Anforderung empfangen. Wenn Sie keine `turn.start`-Nachricht erhalten, sollten Sie annehmen, dass der Status der Dienstverbindung ungültig ist.

| Feld | BESCHREIBUNG |
| ------------- | ---------------- |
| Codierung von WebSocket-Nachrichten | Text |
| `Path` | `turn.start` |
| Content-Typ | application/json; charset=utf-8 |
| Body | JSON-Struktur |

#### <a name="sample-message"></a>Beispielnachricht

```HTML
Path: turn.start
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "context": {
    "serviceTag": "7B33613B91714B32817815DC89633AFA"
  }
}
```

Der Text der `turn.start`-Nachricht ist eine JSON-Struktur, die Kontext für den Beginn des „Turns“ enthält. Das *context*-Element enthält eine *serviceTag*-Eigenschaft. Mit dieser Eigenschaft wird ein Tagwert angegeben, der dem „Turn“ vom Dienst zugeordnet wurde. Dieser Wert kann von Microsoft genutzt werden, falls Sie bei der Problembehandlung für Fehler in Ihrer Anwendung Hilfe benötigen.

### <a name="message-turnend"></a>`turn.end`-Nachricht

Mit `turn.end` wird das Ende eines „Turns“ aus Sicht des Diensts signalisiert. Die `turn.end`-Nachricht ist immer die letzte Antwortnachricht, die Sie für eine Anforderung empfangen. Clients können den Empfang dieser Nachricht als Signal für Bereinigungsaktivitäten und den Übergang in einen Leerlaufzustand nutzen. Wenn Sie keine `turn.end`-Nachricht erhalten, sollten Sie annehmen, dass der Status der Dienstverbindung ungültig ist. Schließen Sie in diesem Fall die vorhandene Verbindung mit dem Dienst, und stellen Sie eine neue Verbindung her.

| Feld | BESCHREIBUNG |
| ------------- | ---------------- |
| Codierung von WebSocket-Nachrichten | Text |
| `Path` | `turn.end` |
| Body | Keine |

#### <a name="sample-message"></a>Beispielnachricht

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Schema der Telemetriedaten

Der Text der *telemetry*-Nachricht ist eine JSON-Struktur, die Clientinformationen zu einem „Turn“ oder einer versuchten Verbindungsherstellung enthält. Die Struktur besteht aus Clientzeitstempeln, mit denen aufgezeichnet wird, wann Clientereignisse eintreten. Jeder Zeitstempel muss das ISO 8601-Format haben, wie im Abschnitt „X-Timestamp-Header“ beschrieben. *telemetry*-Nachrichten, für die nicht alle erforderlichen Felder in der JSON-Struktur angegeben werden oder die nicht das richtige Zeitstempelformat nutzen, können dazu führen, dass der Dienst die Verbindung mit dem Client beendet. Clients *müssen* gültige Werte für alle erforderlichen Felder angeben. Clients *sollten* Werte für optionale Felder angeben, sofern dies erforderlich ist. Die Werte in den Beispielen dieses Abschnitts dienen nur zu Illustrationszwecken.

Das Schema für Telemetriedaten ist in die folgenden Bestandteile unterteilt: Zeitstempel der empfangenen Nachricht und Metriken. Das Format und die Nutzung der einzelnen Teile ist in den folgenden Abschnitten angegeben.

### <a name="received-message-time-stamps"></a>Zeitstempel der empfangenen Nachricht

Clients müssen Empfangszeitpunkt-Werte für alle Nachrichten enthalten, die sie erhalten, nachdem die Verbindung mit dem Dienst erfolgreich hergestellt wurde. Mit diesen Werten muss der Zeitpunkt aufgezeichnet werden, zu dem der Client die einzelnen Nachrichten über das Netzwerk *empfangen* hat. Mit dem Wert sollten keine anderen Zeitpunkte aufgezeichnet werden. Beispielsweise sollte der Client nicht den Zeitpunkt aufzeichnen, zu dem er für die Nachricht *aktiv* geworden ist. Die Zeitstempel von empfangenen Nachrichten werden in einem Array mit *Name:Wert*-Paaren angegeben. Mit dem Namen des Paars wird der *Path*-Wert der Nachricht angegeben. Der Wert des Paars gibt den Zeitpunkt an, zu dem der Client die Nachricht empfangen hat. Falls mehr als eine Nachricht mit dem angegebenen Namen empfangen wurde, ist der Wert des Paars ein Array mit Zeitstempeln, die darauf hinweisen, wann die Nachrichten empfangen wurden.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

Clients *müssen* den Empfang aller Nachrichten bestätigen, die vom Dienst gesendet werden, indem Zeitstempel für diese Nachrichten in den JSON-Text eingefügt werden. Falls ein Client den Empfang einer Nachricht nicht bestätigt, beendet der Dienst ggf. die Verbindung.

### <a name="metrics"></a>metrics

Clients müssen Informationen zu Ereignissen enthalten, die während der Lebensdauer einer Anforderung aufgetreten sind. Die folgenden Metriken werden unterstützt: `Connection`, `Microphone` und `ListeningTrigger`.

### <a name="metric-connection"></a>`Connection`-Metrik

Mit der `Connection`-Metrik werden Details zu Verbindungsversuchen des Clients angegeben. Die Metrik muss Zeitstempel für den Beginn und das Ende der WebSocket-Verbindung enthalten. Die `Connection`-Metrik ist *nur für den ersten „Turn“ einer Verbindung* erforderlich. Nachfolgende „Turns“ müssen diese Informationen nicht enthalten. Wenn ein Client mehrere Verbindungsversuche unternimmt, bevor eine Verbindung hergestellt wird, sollten Informationen zu *allen* Verbindungsversuchen eingefügt werden. Weitere Informationen finden Sie unter [Telemetriedaten für Verbindungsfehler](#connection-failure-telemetry).

| Feld | BESCHREIBUNG | Verwendung |
| ----- | ----------- | ----- |
| NAME | `Connection` | Erforderlich |
| id | Der Wert des Verbindungsbezeichners, der im *X-ConnectionId*-Header für diese Verbindungsanforderung verwendet wurde. | Erforderlich |
| Start | Der Zeitpunkt, zu dem der Client die Verbindungsanforderung gesendet hat. | Erforderlich |
| End | Der Zeitpunkt, zu dem der Client die Benachrichtigung erhalten hat, dass die Verbindung erfolgreich hergestellt wurde oder bei einem Fehler abgelehnt bzw. verweigert wurde oder fehlgeschlagen ist. | Erforderlich |
| Error | Eine Beschreibung des Fehlers, der aufgetreten ist (falls zutreffend). Wenn die Verbindung erfolgreich hergestellt wurde, sollten Clients dieses Feld weglassen. Dieses Feld darf maximal 50 Zeichen lang sein. | Erforderlich für Fehlerfälle, andernfalls weggelassen |

Die Fehlerbeschreibung sollte maximal 50 Zeichen lang sein und idealerweise einem der Werte entsprechen, die in der folgenden Tabelle aufgeführt sind. Wenn die Fehlerbedingung nicht mit einem dieser Wert übereinstimmt, können Clients eine kurze Beschreibung der Fehlerbedingung nutzen, indem sie Binnenmajuskeln (Englisch: [CamelCasing](https://en.wikipedia.org/wiki/Camel_case)) ohne Leerzeichen nutzen. Für das Senden einer *telemetry*-Nachricht ist eine Verbindung mit dem Dienst erforderlich, sodass nur vorübergehende oder temporäre Fehlerbedingungen in der *telemetry*-Nachricht gemeldet werden können. Fehlerbedingungen, die einen Client *dauerhaft* daran hindern, eine Verbindung mit dem Dienst herzustellen, verhindern für den Client das Senden von Nachrichten an den Dienst (einschließlich *telemetry*-Nachrichten).

| Error | Verwendung |
| ----- | ----- |
| DNSfailure | Der Client konnte aufgrund eines DNS-Fehlers im Netzwerkstapel keine Verbindung mit dem Dienst herstellen. |
| NoNetwork | Der Client hat versucht, eine Verbindung herzustellen, aber vom Netzwerkstapel wurde gemeldet, dass kein physisches Netzwerk verfügbar war. |
| NoAuthorization | Die Clientverbindung ist fehlgeschlagen, während versucht wurde, ein Autorisierungstoken für die Verbindung zu beziehen. |
| NoResources | Eine lokale Ressource (z.B. der Arbeitsspeicher) des Clients war erschöpft, während versucht wurde, eine Verbindung herzustellen. |
| Verboten | Der Client konnte keine Verbindung mit dem Dienst herstellen, da der Dienst den HTTP-Statuscode `403 Forbidden` für die WebSocket-Upgradeanforderung zurückgegeben hat. |
| Nicht autorisiert | Der Client konnte keine Verbindung mit dem Dienst herstellen, da der Dienst den HTTP-Statuscode `401 Unauthorized` für die WebSocket-Upgradeanforderung zurückgegeben hat. |
| BadRequest | Der Client konnte keine Verbindung mit dem Dienst herstellen, da der Dienst den HTTP-Statuscode `400 Bad Request` für die WebSocket-Upgradeanforderung zurückgegeben hat. |
| ServerUnavailable | Der Client konnte keine Verbindung mit dem Dienst herstellen, da der Dienst den HTTP-Statuscode `503 Server Unavailable` für die WebSocket-Upgradeanforderung zurückgegeben hat. |
| ServerError | Der Client konnte keine Verbindung mit dem Dienst herstellen, da der Dienst den Statuscode `HTTP 500` für interne Fehler für die WebSocket-Upgradeanforderung zurückgegeben hat. |
| Timeout | Für die Verbindungsanforderung des Clients ist eine Zeitüberschreitung aufgetreten, ohne dass eine Antwort vom Dienst erhalten wurde. Das Feld *End* enthält den Zeitpunkt, zu dem für den Client die Zeitüberschreitung aufgetreten ist und das Warten auf die Verbindung beendet wurde. |
| ClientError | Der Client hat die Verbindung aufgrund eines internen Clientfehlers beendet. |

### <a name="metric-microphone"></a>`Microphone`-Metrik

Die `Microphone`-Metrik ist für alle Sprach-„Turns“ erforderlich. Mit dieser Metrik wird der Zeitraum auf dem Client gemessen, in dem die Audioeingabe aktiv für eine Sprachanforderung verwendet wird.

Verwenden Sie die folgenden Beispiele als Richtlinien für die Aufzeichnung von *Start*-Zeitwerten für die `Microphone`-Metrik in Ihrer Clientanwendung:

* Für eine Clientanwendung ist es erforderlich, dass ein Benutzer eine physische Taste drücken muss, um die Mikrofonnutzung zu starten. Nach dem Betätigen der Taste liest die Clientanwendung die Eingabe über das Mikrofon und sendet sie an den Spracherkennungsdienst. Mit dem *Start*-Wert für die `Microphone`-Metrik wird die Zeit nach der Betätigung der Taste aufgezeichnet, bis das Mikrofon initialisiert wurde und für die Eingabe bereit ist. Mit dem *End*-Wert für die `Microphone`-Metrik wird der Zeitpunkt aufgezeichnet, zu dem die Clientanwendung das Streamen von Audiodaten an den Dienst beendet hat, nachdem sie die `speech.endDetected`-Nachricht vom Dienst erhalten hat.

* Eine Clientanwendung nutzt eine Schlüsselworterkennung („Keyword Spotter“), die „immer“ lauscht. Erst nachdem die Schlüsselworterkennung einen gesprochenen Triggerausdruck erkannt hat, erfasst die Clientanwendung die Eingabe vom Mikrofon und sendet sie an den Spracherkennungsdienst. Der *Start*-Wert für die `Microphone`-Metrik zeichnet den Zeitpunkt auf, zu dem der Client von der Schlüsselworterkennung aufgefordert wurde, mit der Verwendung der Eingabe über das Mikrofon zu beginnen. Mit dem *End*-Wert für die `Microphone`-Metrik wird der Zeitpunkt aufgezeichnet, zu dem die Clientanwendung das Streamen von Audiodaten an den Dienst beendet hat, nachdem sie die `speech.endDetected`-Nachricht vom Dienst erhalten hat.

* Eine Clientanwendung hat Zugriff auf einen konstanten Audiodatenstrom und führt die Erkennung von Schweigen bzw. Sprache für diesen Audiodatenstrom in einem *Spracherkennungsmodul* durch. Mit dem *Start*-Wert für die `Microphone`-Metrik wird der Zeitpunkt aufgezeichnet, zu dem das *Spracherkennungsmodul* den Client aufgefordert hat, mit der Verwendung der Eingabe des Audiodatenstroms zu beginnen. Mit dem *End*-Wert für die `Microphone`-Metrik wird der Zeitpunkt aufgezeichnet, zu dem die Clientanwendung das Streamen von Audiodaten an den Dienst beendet hat, nachdem sie die `speech.endDetected`-Nachricht vom Dienst erhalten hat.

* Eine Clientanwendung verarbeitet den zweiten „Turn“ einer Anforderung mit mehreren „Turns“ und wird per Dienstantwortnachricht aufgefordert, das Mikrofon einzuschalten, um Eingabedaten für den zweiten „Turn“ zu sammeln. Mit dem *Start*-Wert für die `Microphone`-Metrik wird der Zeitpunkt aufgezeichnet, zu dem die Clientanwendung das Mikrofon aktiviert und mit der Verwendung der Eingabedaten von dieser Audioquelle beginnt. Mit dem *End*-Wert für die `Microphone`-Metrik wird der Zeitpunkt aufgezeichnet, zu dem die Clientanwendung das Streamen von Audiodaten an den Dienst beendet hat, nachdem sie die `speech.endDetected`-Nachricht vom Dienst erhalten hat.

Mit dem *End*-Zeitwert für die `Microphone`-Metrik wird der Zeitpunkt aufgezeichnet, zu dem die Clientanwendung das Streamen der Audioeingabe beendet hat. In den meisten Fällen tritt dieses Ereignis kurze Zeit nach dem Empfang der `speech.endDetected`-Nachricht durch den Client vom Dienst auf. Für Clientanwendungen kann ggf. überprüft werden, dass die Konformität mit dem Protokoll gewährleistet ist. Hierzu wird sichergestellt, dass der *End*-Zeitwert für die `Microphone`-Metrik nach dem Empfangszeitwert für die `speech.endDetected`-Nachricht liegt. Und da es zwischen dem Ende eines „Turns“ und dem Start eines anderen „Turns“ normalerweise zu einer Verzögerung kommt, können Clients auch die Konformität des Protokolls überprüfen. Hierzu wird sichergestellt, dass der *Start*-Zeitpunkt der `Microphone`-Metrik für alle nachfolgenden „Turns“ richtig den Zeitpunkt aufzeichnet, zu dem der Client mit der Verwendung des Mikrofons zum Streamen der Audioeingabe an den Dienst *begonnen* hat.

| Feld | BESCHREIBUNG | Verwendung |
| ----- | ----------- | ----- |
| NAME | Mikrofon | Erforderlich |
| Start | Der Zeitpunkt, zu dem der Client mit der Verwendung der Audioeingabe vom Mikrofon oder anderer Audiodatenströme begonnen hat oder einen Trigger von der Schlüsselworterkennung erhalten hat. | Erforderlich |
| End | Der Zeitpunkt, zu dem der Client die Verwendung des Mikrofons bzw. des Audiodatenstroms beendet hat. | Erforderlich |
| Error | Eine Beschreibung des Fehlers, der aufgetreten ist (falls zutreffend). Wenn die Mikrofonvorgänge erfolgreich waren, sollten Clients dieses Feld weglassen. Dieses Feld darf maximal 50 Zeichen lang sein. | Erforderlich für Fehlerfälle, andernfalls weggelassen |

### <a name="metric-listeningtrigger"></a>`ListeningTrigger`-Metrik
Mit der `ListeningTrigger`-Metrik wird der Zeitpunkt gemessen, zu dem der Benutzer die Aktion ausführt, mit der die Spracheingabe initiiert wird. Die `ListeningTrigger`-Metrik ist optional, aber für Clients, die diese Metrik bereitstellen können, wird die Verwendung empfohlen.

Verwenden Sie die folgenden Beispiele als Richtlinien für die Aufzeichnung von *Start*- und *End*-Zeitwerten für die `ListeningTrigger`-Metrik in Ihrer Clientanwendung.

* Für eine Clientanwendung ist es erforderlich, dass ein Benutzer eine physische Taste drücken muss, um die Mikrofonnutzung zu starten. Mit dem *Start*-Wert für diese Metrik wird der Zeitpunkt der Tastenbetätigung aufgezeichnet. Mit dem *End*-Wert wird der Zeitpunkt aufgezeichnet, zu dem die Betätigung der Taste beendet wurde.

* Eine Clientanwendung nutzt eine Schlüsselworterkennung („Keyword Spotter“), die „immer“ lauscht. Nachdem die Schlüsselworterkennung einen gesprochenen Triggerausdruck erkannt hat, liest die Clientanwendung die Eingabe über das Mikrofon und sendet sie an den Spracherkennungsdienst. Mit dem *Start*-Wert für diese Metrik wird der Zeitpunkt aufgezeichnet, zu dem die Schlüsselworterkennung Audiodaten empfangen hat, die dann als Triggerausdruck erkannt wurden. Mit dem *End*-Wert wird der Zeitpunkt aufgezeichnet, zu dem das letzte Wort des Triggerausdrucks vom Benutzer gesprochen wurde.

* Eine Clientanwendung hat Zugriff auf einen konstanten Audiodatenstrom und führt die Erkennung von Schweigen bzw. Sprache für diesen Audiodatenstrom in einem *Spracherkennungsmodul* durch. Mit dem *Start*-Wert für diese Metrik wird der Zeitpunkt aufgezeichnet, zu dem das *Spracherkennungsmodul* Audiodaten empfangen hat, die dann als Sprache erkannt wurden. Mit dem *End*-Wert wird der Zeitpunkt aufgezeichnet, zu dem das *Spracherkennungsmodul* Sprache erkannt hat.

* Eine Clientanwendung verarbeitet den zweiten „Turn“ einer Anforderung mit mehreren „Turns“ und wird per Dienstantwortnachricht aufgefordert, das Mikrofon einzuschalten, um Eingabedaten für den zweiten „Turn“ zu sammeln. Die Clientanwendung sollte *keine* `ListeningTrigger`-Metrik für diesen „Turn“ enthalten.

| Feld | BESCHREIBUNG | Verwendung |
| ----- | ----------- | ----- |
| NAME | ListeningTrigger | Optional |
| Start | Der Zeitpunkt, zu dem der Lauschtrigger für den Client gestartet wurde. | Erforderlich |
| End | Der Zeitpunkt, zu dem der Lauschtrigger für den Client beendet wurde. | Erforderlich |
| Error | Eine Beschreibung des Fehlers, der aufgetreten ist (falls zutreffend). Wenn dieser Triggervorgang erfolgreich war, sollten Clients dieses Feld weglassen. Dieses Feld darf maximal 50 Zeichen lang sein. | Erforderlich für Fehlerfälle, andernfalls weggelassen |

#### <a name="sample-message"></a>Beispielnachricht

Das folgende Beispiel enthält eine Telemetrienachricht mit den Teilen „ReceivedMessages“ und „Metrics“:

```HTML
Path: telemetry
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000
X-Timestamp: 2016-08-16T15:03:54.183Z

{
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.171Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ],
  "Metrics": [
    {
      "Name": "Connection",
      "Id": "A140CAF92F71469FA41C72C7B5849253",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:48.000Z",
    },
    {
      "Name": "ListeningTrigger",
      "Start": "2016-08-16T15:03:48.776Z",
      "End": "2016-08-16T15:03:48.777Z",
    },
    {
      "Name": "Microphone",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:51.921Z",
    },
  ],
}
```

## <a name="error-handling"></a>Fehlerbehandlung

In diesem Abschnitt werden die Arten von Fehlermeldungen und -bedingungen beschrieben, die von Ihrer Anwendung verarbeitet werden müssen.

### <a name="http-status-codes"></a>HTTP-Statuscodes

Während der WebSocket-Upgradeanforderung kann der Spracherkennungsdienst HTTP-Standardstatuscodes zurückgeben, z.B. `400 Bad Request` usw. Ihre Anwendung muss diese Fehlerbedingungen richtig verarbeiten.

#### <a name="authorization-errors"></a>Autorisierungsfehler

Wenn die fehlerhafte Autorisierung während des WebSocket-Upgrades bereitgestellt wird, gibt der Spracherkennungsdienst den HTTP-Statuscode `403 Forbidden` zurück. Beispiele für die Bedingungen, die diesen Fehlercode auslösen können, sind:

* Fehlender *Authorization*-Header

* Ungültiges Autorisierungstoken

* Abgelaufenes Autorisierungstoken

Die Fehlermeldung `403 Forbidden` weist nicht auf ein Problem mit dem Spracherkennungsdienst hin. Diese Fehlermeldung steht für ein Problem mit der Clientanwendung.

### <a name="protocol-violation-errors"></a>Protokollverletzungsfehler

Wenn der Spracherkennungsdienst Protokollverletzungen eines Clients erkennt, beendet der Dienst die WebSocket-Verbindung, nachdem ein *Statuscode* und ein *Grund* für die Beendigung zurückgegeben wurde. Clientanwendungen können diese Informationen nutzen, um die Problembehandlung durchzuführen und Verletzungen zu beheben.

#### <a name="incorrect-message-format"></a>Fehlerhaftes Nachrichtenformat

Wenn ein Client eine Textnachricht oder binäre Nachricht an den Dienst sendet, die nicht im richtigen Format gemäß dieser Spezifikation codiert ist, schließt der Dienst die Verbindung mit dem Statuscode *1007 Invalid Payload Data*.

Der Dienst gibt diesen Statuscode aus verschiedenen Gründen zurück. Dies wird anhand der folgenden Beispiele veranschaulicht:

* „Incorrect message format. Binary message has invalid header size prefix.“ (Fehlerhaftes Nachrichtenformat. Die binäre Nachricht enthält ein ungültiges Präfix für die Headergröße.) Der Client hat eine binäre Nachricht mit einem ungültigen Präfix für die Headergröße gesendet.

* „Incorrect message format. Binary message has invalid header size." (Fehlerhaftes Nachrichtenformat. Die binäre Nachricht enthält einen Header mit ungültiger Größe.) Der Client hat eine binäre Nachricht gesendet, in der eine ungültige Headergröße enthalten war.

* „Incorrect message format. Binary message headers decoding into UTF-8 failed.“ (Fehlerhaftes Nachrichtenformat. Fehler beim Decodieren von Headern binärer Nachrichten in UTF-8.) Der Client hat eine binäre Nachricht gesendet, die Header mit einer fehlerhaften Codierung in UTF-8 enthält.

* „Incorrect message format. Text message contains no data.“ (Fehlerhaftes Nachrichtenformat. Textnachricht enthält keine Daten.) Der Client hat eine Textnachricht ohne Textdaten gesendet.

* „Incorrect message format. Text message decoding into UTF-8 failed.“ (Fehlerhaftes Nachrichtenformat. Fehler beim Decodieren der Textnachricht in UTF-8.) Der Client hat eine Textnachricht gesendet, die nicht richtig für UTF-8 codiert war.

* „Incorrect message format. Text message contains no header separator.“ (Fehlerhaftes Nachrichtenformat. Die Textnachricht enthält kein Headertrennzeichen.) Der Client hat eine Textnachricht gesendet, die kein Headertrennzeichen oder ein falsches Headertrennzeichen enthalten hat.

#### <a name="missing-or-empty-headers"></a>Fehlende oder leere Header

Wenn ein Client eine Nachricht sendet, die nicht über die erforderlichen Header *X-RequestId* oder *Path* verfügt, schließt der Dienst die Verbindung mit dem Statuscode *1002 Protocol Error*. Die Meldung lautet „Missing/Empty header. {Header name}.“ (Fehlender/leerer Header. {Headername}.)

#### <a name="requestid-values"></a>RequestId-Werte

Wenn ein Client eine Nachricht sendet, für die ein *X-RequestId*-Header mit einem fehlerhaften Format angegeben wurde, schließt der Dienst die Verbindung und gibt den Status *1002 Protocol Error* zurück. Die Meldung lautet „Invalid request. X-RequestId header value was not specified in no-dash UUID format.“ (Ungültige Anforderung. X-RequestId-Headerwert wurde nicht im UUID-Format „no-dash“ angegeben.)

#### <a name="audio-encoding-errors"></a>Audiocodierungsfehler

Wenn ein Client einen Audioblock sendet, mit dem ein „Turn“ initiiert wird, und das Audioformat oder die Codierung nicht mit der erforderlichen Spezifikation konform ist, schließt der Dienst die Verbindung und gibt den Statuscode *1007 Invalid Payload Data* zurück. In der Meldung ist die Quelle für den Formatcodierungsfehler angegeben.

#### <a name="requestid-reuse"></a>Wiederverwendung der RequestId

Wenn ein Client nach Abschluss eines „Turns“ eine Nachricht sendet, in der der Anforderungsbezeichner dieses „Turns“ wiederverwendet wird, schließt der Dienst die Verbindung und gibt den Statuscode *1002 Protocol Error* zurück. Die Meldung lautet „Invalid request. Reuse of request identifiers is not allowed.“ (Ungültige Anforderung. Die Wiederverwendung von Anforderungsbezeichnern ist nicht zulässig.)

## <a name="connection-failure-telemetry"></a>Telemetriedaten für Verbindungsfehler

Zur Sicherstellung der bestmöglichen Benutzerfreundlichkeit müssen Clients den Spracherkennungsdienst über die Zeitstempel für wichtige Prüfpunkte innerhalb einer Verbindung informieren, indem sie die *telemetry*-Nachricht verwenden. Es ist genauso wichtig, dass Clients den Dienst über Verbindungen informieren, bei denen beim Versuch, die Verbindung herzustellen, ein Fehler aufgetreten ist.

Für jeden nicht erfolgreichen Verbindungsversuch erstellen Clients eine *telemetry*-Nachricht mit einem eindeutigen *X-RequestId*-Headerwert. Da der Client keine Verbindung herstellen konnte, kann das Feld *ReceivedMessages* im JSON-Text weggelassen werden. Nur der `Connection`-Eintrag im Feld *Metrics* wird verwendet. Dieser Eintrag enthält die Zeitstempel für Start und Ende sowie die Fehlerbedingung, die erkannt wurde.

### <a name="connection-retries-in-telemetry"></a>Wiederholungsversuche zum Herstellen einer Verbindung in der Telemetrie

Clients sollten *Wiederholungsversuche* von *mehreren Verbindungsversuchen* anhand des Ereignisses unterscheiden, mit dem der Verbindungsversuch ausgelöst wird. Verbindungsversuche, die programmgesteuert ohne Benutzereingabe durchgeführt werden, sind Wiederholungsversuche. Mehrere Verbindungsversuche erfolgen als Reaktion auf die Benutzereingabe. Clients ordnen jedem vom Benutzer ausgelösten Verbindungsversuch eine eindeutige *X-RequestId*- und *telemetry*-Nachricht zu. *X-RequestId* wird von Clients für programmgesteuerte Wiederholungen wiederverwendet. Wenn für einen einzelnen Verbindungsversuch mehrere Wiederholungsversuche durchgeführt wurden, wird jeder Versuch als `Connection`-Eintrag in die *telemetry*-Nachricht eingefügt.

Angenommen, ein Benutzer spricht das auslösende Schlüsselwort aus, um eine Verbindung zu starten, und der erste Verbindungsversuch ist aufgrund von DNS-Fehlern nicht erfolgreich. Ein zweiter Versuch, der programmgesteuert vom Client unternommen wird, ist erfolgreich. Da der Client den Verbindungsversuch wiederholt hat, ohne dass eine zusätzliche Eingabe durch den Benutzer erforderlich war, nutzt der Client eine einzelne *telemetry*-Nachricht mit mehreren `Connection`-Einträgen, um die Verbindung zu beschreiben.

Ein weiteres Beispiel: Ein Benutzer spricht das auslösende Schlüsselwort aus, um eine Verbindung zu starten, und dieser Verbindungsversuch schlägt nach drei Wiederholungen fehl. Der Client gibt auf, beendet die Versuche zum Herstellen einer Verbindung mit dem Dienst und informiert den Benutzer darüber, dass ein Fehler aufgetreten ist. Der Benutzer spricht dann erneut das auslösende Schlüsselwort aus. Jetzt gehen wir davon aus, dass der Client die Verbindung mit dem Dienst herstellt. Nach der Verbindungsherstellung sendet der Client sofort eine *telemetry*-Nachricht an den Dienst, die die drei `Connection`-Einträge zum Beschreiben der Verbindungsfehler enthält. Nach Erhalt der `turn.end`-Nachricht sendet der Client eine weitere *telemetry*-Nachricht, in der die erfolgreiche Verbindungsherstellung beschrieben ist.

## <a name="error-message-reference"></a>Referenz zu Fehlermeldungen

### <a name="http-status-codes"></a>HTTP-Statuscodes

| HTTP-Statuscode | BESCHREIBUNG | Problembehandlung |
| - | - | - |
| 400 – Ungültige Anforderung | Der Client hat eine fehlerhafte WebSocket-Verbindungsanforderung gesendet. | Überprüfen Sie, ob alle erforderlichen Parameter und HTTP-Header angegeben wurden und die Werte korrekt sind. |
| 401 – Nicht autorisiert | Der Client hat die erforderlichen Informationen für die Autorisierung nicht eingefügt. | Überprüfen Sie, ob Sie für die WebSocket-Verbindung den *Authorization*-Header senden. |
| 403 Verboten | Der Client hat Informationen zur Autorisierung gesendet, aber diese waren ungültig. | Stellen Sie sicher, dass Sie im *Authorization*-Header keinen abgelaufenen oder ungültigen Wert senden. |
| 404 – Nicht gefunden | Der Client hat versucht, auf einen nicht unterstützten URL-Pfad zuzugreifen. | Überprüfen Sie, ob Sie die richtige URL für die WebSocket-Verbindung verwenden. |
| 500 Serverfehler | Der Dienst hat einen internen Fehler erkannt und konnte die Anforderung nicht erfüllen. | In den meisten Fällen ist dies ein vorübergehender Fehler. Wiederholen Sie die Anforderung. |
| 503 Dienst nicht verfügbar | Der Dienst konnte die Anforderung nicht verarbeiten. | In den meisten Fällen ist dies ein vorübergehender Fehler. Wiederholen Sie die Anforderung. |

### <a name="websocket-error-codes"></a>WebSocket-Fehlercodes

| WebSocketsStatus-Code | BESCHREIBUNG | Problembehandlung |
| - | - | - |
| 1000 Normales Schließen | Der Dienst hat die WebSocket-Verbindung ohne Fehler geschlossen. | Falls das Schließen der WebSocket-Verbindung unerwartet aufgetreten ist, sollten Sie in der Dokumentation nachlesen. Stellen Sie sicher, dass Sie verstanden haben, wie und wann der Dienst die WebSocket-Verbindung beenden kann. |
| 1002 Protokollfehler | Der Client hat die Protokollanforderungen nicht erfüllt. | Stellen Sie sicher, dass Sie die Protokolldokumentation verstehen und mit den Anforderungen vertraut sind. Lesen Sie die Dokumentation zu den Fehlerursachen, um zu ermitteln, ob Sie gegen die Protokollanforderungen verstoßen. |
| 1007 Ungültige Nutzlastdaten | Der Client hat eine ungültige Nutzlast in einer Protokollnachricht gesendet. | Überprüfen Sie die letzte Meldung, die Sie an den Dienst gesendet haben, auf Fehler. Lesen Sie die Dokumentation zu Nutzlastfehlern. |
| 1011 Serverfehler | Der Dienst hat einen internen Fehler erkannt und konnte die Anforderung nicht erfüllen. | In den meisten Fällen ist dies ein vorübergehender Fehler. Wiederholen Sie die Anforderung. |

## <a name="related-topics"></a>Verwandte Themen

Sehen Sie sich ein [JavaScript SDK](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) an, bei dem es sich um eine Implementierung des WebSocket-basierten Spracherkennungsdienst-Protokolls handelt.
