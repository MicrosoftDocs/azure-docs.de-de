---
title: Text-to-Speech-API des Speech-Diensts von Microsoft | Microsoft-Dokumentation
titlesuffix: Azure Cognitive Services
description: Die Text-to-Speech-API ermöglicht die Umwandlung von Text in Sprache mit verschiedenen Stimmen und Sprachen in Echtzeit.
services: cognitive-services
author: priyaravi20
manager: yanbo
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: priyar
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a046bec5d81d828d88716d31c84e9cbcdcea1a08
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515422"
---
# <a name="bing-text-to-speech-api"></a>Text-to-Speech-API von Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="Introduction"></a>Einführung

Mit der Text-to-Speech-API von Bing kann Ihre Anwendung HTTP-Anforderungen an einen Cloudserver senden. Dort wird der Text dann umgehend in natürlich klingende Sprache umgewandelt und als Audiodatei zurückgegeben. Diese API kann bietet eine Echtzeitumwandlung von Text in Sprache mit verschiedenen Stimmen und Sprachen für unterschiedlichste Szenarien.

## <a name="VoiceSynReq"></a>Sprachsyntheseanforderung

### <a name="Subscription"></a>Autorisierungstoken

Jede Sprachsyntheseanforderung erfordert ein JWT-Zugriffstoken (JSON Web Token). Das JWT-Zugriffstoken wird über den Sprachanforderungsheader übergeben. Das Token ist zehn Minuten lang gültig. Informationen zum Abonnieren und Beziehen von API-Schlüsseln für den Abruf gültiger JWT-Zugriffstoken finden Sie unter [Cognitive Services ausprobieren](https://azure.microsoft.com/try/cognitive-services/).

Der API-Schlüssel wird an den Tokendienst übergeben. Beispiel:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Für den Tokenzugriff sind folgende Headerinformationen erforderlich:

NAME| Format | BESCHREIBUNG
----|----|----
Ocp-Apim-Subscription-Key | ASCII | Your subscription key (Ihr Abonnementschlüssel)

Der Tokendienst gibt das JWT-Zugriffstoken im Format `text/plain` zurück. Das JWT wird dann im Format `Base64 access_token` mit der Präfixzeichenfolge `Bearer` als Autorisierungsheader an den Sprachendpunkt übergeben. Beispiel:

`Authorization: Bearer [Base64 access_token]`

Für den Zugriff auf den Sprachsynthesedienst müssen Clients den folgenden Endpunkt verwenden:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Wenn Sie noch kein Zugriffstoken mit Ihrem Abonnementsschlüssel bezogen haben, wie weiter oben beschrieben, generiert dieser Link einen Antwortfehler vom Typ `403 Forbidden`.

### <a name="Http"></a>HTTP-Header

Die folgende Tabelle enthält die HTTP-Header für Sprachsyntheseanforderungen:

Header |Wert |Kommentare
----|----|----
Content-Type | application/ssml+xml | Der Inhaltstyp der Eingabe.
X-Microsoft-OutputFormat | **1.** ssml-16khz-16bit-mono-tts <br> **2.** raw-16khz-16bit-mono-pcm <br>**3.** audio-16khz-16kbps-mono-siren <br> **4.** riff-16khz-16kbps-mono-siren <br> **5.** riff-16khz-16bit-mono-pcm <br> **6.** audio-16khz-128kbitrate-mono-mp3 <br> **7.** audio-16khz-64kbitrate-mono-mp3 <br> **8.** audio-16khz-32kbitrate-mono-mp3 | Das Audioformat der Ausgabe.
X-Search-AppId | Eine GUID (nur hexadezimal, keine Bindestriche) | Eine ID zur eindeutigen Identifizierung der Clientanwendung. Hierbei kann es sich um die Shop-ID für Apps handeln. Sollte keine verfügbar sein, kann die ID für eine Anwendung vom Benutzer generiert werden.
X-Search-ClientID | Eine GUID (nur hexadezimal, keine Bindestriche) | Eine ID zur eindeutigen Identifizierung einer Anwendungsinstanz für jede Installation.
User-Agent | Anwendungsname | Der Anwendungsname ist erforderlich und muss weniger als 255 Zeichen umfassen.
Authorization | Autorisierungstoken |  Informationen hierzu finden Sie im Abschnitt <a href="#Subscription">Authentifizierungstoken</a>.

### <a name="InputParam"></a>Eingabeparameter

Anforderungen für die Text-to-Speech-API von Bing werden in Form von HTTP POST-Aufrufen vorgenommen. Die Header sind im vorherigen Abschnitt angegeben. Der Hauptteil enthält eine SSML-Eingabe (Speech Synthesis Markup Language, Markupsprache für Sprachsynthese), die den umzuwandelnden Text darstellt. Eine Beschreibung des Markups zur Steuerung von Sprachaspekten wie Sprache und Geschlecht des Sprechers finden Sie in der [SSML-W3C-Spezifikation](https://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>Eine SSML-Eingabe darf maximal 1.024 Zeichen (einschließlich aller Tags) umfassen.

###  <a name="SampleVoiceOR"></a>Beispiel: Sprachausgabeanforderung

Hier sehen Sie ein Beispiel für eine Sprachausgabeanforderung:

```HTTP
POST /synthesize
HTTP/1.1
Host: speech.platform.bing.com

X-Microsoft-OutputFormat: riff-8khz-8bit-mono-mulaw
Content-Type: application/ssml+xml
Host: speech.platform.bing.com
Content-Length: 197
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>Microsoft Bing Voice Output API</voice></speak>
```

## <a name="VoiceOutResponse"></a>Sprachausgabeantwort

Die Text-to-Speech-API von Bing verwendet HTTP POST, um Audio an den Client zurückzugeben. Die API-Antwort enthält den Audiostream und den Codec und entspricht dem angeforderten Ausgabeformat. Das zurückgegebene Audio für eine Anforderung darf maximal 15 Sekunden lang sein.

### <a name="SuccessfulRecResponse"></a>Beispiel: erfolgreiche Syntheseantwort

Der folgende Code ist ein Beispiel für eine JSON-Antwort auf eine erfolgreiche Sprachsyntheseanforderung. Die Kommentare und die Formatierung des Codes wurden nur für dieses Beispiel eingefügt und sind in der tatsächlichen Antwort nicht enthalten.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Beispiel: Synthesefehler

Der folgende Beispielcode zeigt eine JSON-Antwort auf eine nicht erfolgreiche Sprachsyntheseabfrage:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Fehlerantworten

Error | BESCHREIBUNG
----|----
HTTP/400 Bad Request | Ein erforderlicher Parameter fehlt oder ist leer oder NULL, oder der an einen erforderlichen oder optionalen Parameter übergebene Wert ist ungültig. Eine Antwort vom Typ „Ungültig“ kann unter anderem auf die Übergabe eines zu langen Zeichenfolgenwerts zurückzuführen sein. Eine kurze Beschreibung des problematischen Parameters ist enthalten.
HTTP/401 Unauthorized | Die Anforderung ist nicht autorisiert.
HTTP/413 RequestEntityTooLarge  | Die SSML-Eingabe ist zu groß.
HTTP/502 BadGateway | Es liegt ein Netzwerk- oder Serverproblem vor.

Hier sehen Sie ein Beispiel für eine Fehlerantwort:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>Ändern der Sprachausgabe per SSML

Die Text-to-Speech-API von Microsoft unterstützt SSML 1.0 gemäß W3C-Definition ([Speech Synthesis Markup Language (SSML) Version 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/)). Dieser Abschnitt enthält Beispiele für das Ändern bestimmter Eigenschaften der generierten Sprachausgabe wie Sprechgeschwindigkeit und Aussprache mithilfe von SSML-Tags.

1. Hinzufügen einer Pause

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
   ```

2. Ändern der Sprechgeschwindigkeit

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

3. Aussprache

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
   ```

4. Ändern der Lautstärke

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

5. Ändern der Tonhöhe

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

6. Ändern des Satzrhythmus

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
   ```

> [!NOTE]
> Beachten Sie, dass die Audiodaten in 8k- oder 16k-WAV-Dateien im folgenden Format vorliegen müssen: **CRC-Code** (CRC-32): 4 Bytes (DWORD) mit gültigen Bereich 0x00000000 ~ 0xFFFFFFFF; **Audioformatflag**: 4 Bytes (DWORD) mit gültigen Bereich 0x00000000 ~ 0xFFFFFFFF; **Beispielzahl**: 4 Bytes (DWORD) mit gültigen Bereich 0x00000000 ~ 0x7FFFFFFF; **Größe des binären Texts**: 4 Bytes (DWORD) mit gültigen Bereich 0x00000000 ~ 0x7FFFFFFF; **Binärer Text**: n Bytes.

## <a name="SampleApp"></a>Beispielanwendung

Implementierungsdetails finden Sie in der [Visual C# .NET-Beispielanwendung für die Sprachsynthese](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Unterstützte Gebietsschemas und Voicefonts

Die folgende Tabelle enthält einige der unterstützten Gebietsschemas und die dazugehörigen Voicefonts.

Gebietsschema | Geschlecht | Dienstnamenzuordnung
---------|--------|------------
ar-EG* | Female | Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)
ar-SA | Male | Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)
bg-BG | Male | Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)
ca-ES | Female | Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)
cs-CZ | Male | Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)
da-DK | Female | Microsoft Server Speech Text to Speech Voice (da-DK, HelleRUS)
de-AT | Male | Microsoft Server Speech Text to Speech Voice (de-AT, Michael)
de-CH | Male | Microsoft Server Speech Text to Speech Voice (de-CH, Karsten)
de-DE | Female | Microsoft Server Speech Text to Speech Voice (de-DE, Hedda)
de-DE | Female | Microsoft Server Speech Text to Speech Voice (de-DE, HeddaRUS)
de-DE | Male | Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)
el-GR | Male | Microsoft Server Speech Text to Speech Voice (el-GR, Stefanos)
en-AU | Female | Microsoft Server Speech Text to Speech Voice (en-AU, Catherine)
en-AU | Female | Microsoft Server Speech Text to Speech Voice (en-AU, HayleyRUS)
en-CA | Female | Microsoft Server Speech Text to Speech Voice (en-CA, Linda)
en-CA | Female | Microsoft Server Speech Text to Speech Voice (en-CA, HeatherRUS)
en-GB | Female | Microsoft Server Speech Text to Speech Voice (en-GB, Susan, Apollo)
en-GB | Female | Microsoft Server Speech Text to Speech Voice (en-GB, HazelRUS)
en-GB | Male | Microsoft Server Speech Text to Speech Voice (en-GB, George, Apollo)
en-IE | Male | Microsoft Server Speech Text to Speech Voice (en-IE, Sean)
en-IN | Female | Microsoft Server Speech Text to Speech Voice (en-IN, Heera, Apollo)
en-IN | Female | Microsoft Server Speech Text to Speech Voice (en-IN, PriyaRUS)
en-IN | Male | Microsoft Server Speech Text to Speech Voice (en-IN, Ravi, Apollo)
en-US | Female | Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)
en-US | Female | Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)
en-US | Male | Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)
es-ES | Female | Microsoft Server Speech Text to Speech Voice (es-ES, Laura, Apollo)
es-ES | Female | Microsoft Server Speech Text to Speech Voice (es-ES, HelenaRUS)
es-ES | Male | Microsoft Server Speech Text to Speech Voice (es-ES, Pablo, Apollo)
es-MX | Female | Microsoft Server Speech Text to Speech Voice (es-MX, HildaRUS)
es-MX | Male | Microsoft Server Speech Text to Speech Voice (es-MX, Raul, Apollo)
fi-FI | Female | Microsoft Server Speech Text to Speech Voice (fi-FI, HeidiRUS)
fr-CA | Female | Microsoft Server Speech Text to Speech Voice (fr-CA, Caroline)
fr-CA | Female | Microsoft Server Speech Text to Speech Voice (fr-CA, HarmonieRUS)
fr-CH | Male | Microsoft Server Speech Text to Speech Voice (fr-CH, Guillaume)
fr-FR | Female | Microsoft Server Speech Text to Speech Voice (fr-FR, Julie, Apollo)
fr-FR | Female | Microsoft Server Speech Text to Speech Voice (fr-FR, HortenseRUS)
fr-FR | Male | Microsoft Server Speech Text to Speech Voice (fr-FR, Paul, Apollo)
he-IL| Male| Microsoft Server Speech Text to Speech Voice (he-IL, Asaf)
hi-IN | Female | Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana, Apollo)
hi-IN | Female | Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana)
hi-IN | Male | Microsoft Server Speech Text to Speech Voice (hi-IN, Hemant)
hr-HR | Male | Microsoft Server Speech Text to Speech Voice (hr-HR, Matej)
hu-HU | Male | Microsoft Server Speech Text to Speech Voice (hu-HU, Szabolcs)
id-ID | Male | Microsoft Server Speech Text to Speech Voice (id-ID, Andika)
it-IT | Male | Microsoft Server Speech Text to Speech Voice (it-IT, Cosimo, Apollo)
it-IT | Female | Microsoft Server Speech Text to Speech Voice (it-IT, LuciaRUS)
ja-JP | Female | Microsoft Server Speech Text to Speech Voice (ja-JP, Ayumi, Apollo)
ja-JP | Male | Microsoft Server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)
ja-JP | Female | Microsoft Server Speech Text to Speech Voice (ja-JP, HarukaRUS)
ko-KR | Female | Microsoft Server Speech Text to Speech Voice (ko-KR, HeamiRUS)
ms-MY | Male | Microsoft Server Speech Text to Speech Voice (ms-MY, Rizwan)
nb-NO | Female | Microsoft Server Speech Text to Speech Voice (nb-NO, HuldaRUS)
nl-NL | Female | Microsoft Server Speech Text to Speech Voice (nl-NL, HannaRUS)
pl-PL | Female | Microsoft Server Speech Text to Speech Voice (pl-PL, PaulinaRUS)
pt-BR | Female | Microsoft Server Speech Text to Speech Voice (pt-BR, HeloisaRUS)
pt-BR | Male | Microsoft Server Speech Text to Speech Voice (pt-BR, Daniel, Apollo)
pt-PT | Female | Microsoft Server Speech Text to Speech Voice (pt-PT, HeliaRUS)
ro-RO | Male | Microsoft Server Speech Text to Speech Voice (ro-RO, Andrei)
ru-RU | Female | Microsoft Server Speech Text to Speech Voice (ru-RU, Irina, Apollo)
ru-RU | Male | Microsoft Server Speech Text to Speech Voice (ru-RU, Pavel, Apollo)
ru-RU | Female | Microsoft Server Speech Text to Speech Voice (ru-RU, EkaterinaRUS)
sk-SK | Male | Microsoft Server Speech Text to Speech Voice (sk-SK, Filip)
sl-SI | Male | Microsoft Server Speech Text to Speech Voice (sl-SI, Lado)
sv-SE | Female | Microsoft Server Speech Text to Speech Voice (sv-SE, HedvigRUS)
ta-IN | Male | Microsoft Server Speech Text to Speech Voice (ta-IN, Valluvar)
th-TH | Male | Microsoft Server Speech Text to Speech Voice (th-TH, Pattara)
tr-TR | Female | Microsoft Server Speech Text to Speech Voice (tr-TR, SedaRUS)
vi-VN | Male | Microsoft Server Speech Text to Speech Voice (vi-VN, An)
zh-CN | Female | Microsoft Server Speech Text to Speech Voice (zh-CN, HuihuiRUS)
zh-CN | Female | Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)
zh-CN | Male | Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)
zh-HK | Female | Microsoft Server Speech Text to Speech Voice (zh-HK, Tracy, Apollo)
zh-HK | Female | Microsoft Server Speech Text to Speech Voice (zh-HK, TracyRUS)
zh-HK | Male | Microsoft Server Speech Text to Speech Voice (zh-HK, Danny, Apollo)
zh-TW | Female | Microsoft Server Speech Text to Speech Voice (zh-TW, Yating, Apollo)
zh-TW | Female | Microsoft Server Speech Text to Speech Voice (zh-TW, HanHanRUS)
zh-TW | Male | Microsoft Server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)

 *ar-EG unterstützt modernes Hocharabisch (Modern Standard Arabic, MSA).

> [!NOTE]
> Beachten Sie, dass die vorherigen Dienstnamen **Microsoft Server Speech Text to Speech Voice (cs-CZ, Vit)** und **Microsoft Server Speech Text to Speech Voice (en-IE, Shaun)** seit dem 31.03.2018 veraltet sind, um die Funktionen der Bing-Spracheingabe-API zu optimieren. Aktualisieren Sie Ihren Code mit den aktualisierten Namen.

## <a name="TrouNSupport"></a>Problembehandlung und Support

Wenden Sie sich bei Fragen und Problemen an das MSDN-Forum für den [Bing-Spracheingabe-Dienst](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService). Geben Sie sämtliche Details an, etwa:

* Ein Beispiel der vollständigen Anforderungszeichenfolge
* Die vollständige Ausgabe einer nicht erfolgreichen Anforderung einschließlich Protokoll-IDs (sofern zutreffend)
* Den prozentualen Anteil nicht erfolgreicher Anforderungen
