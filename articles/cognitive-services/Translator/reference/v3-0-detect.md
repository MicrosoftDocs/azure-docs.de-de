---
title: Translator-Methode „Detect“
titleSuffix: Azure Cognitive Services
description: Ermitteln Sie die Sprache eines Texts mit der Methode „Detect“ von Azure Cognitive Services Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 12029d7a77eb7cbbb10cb5330eb18d6316520a2d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021319"
---
# <a name="translator-30-detect"></a>Translator 3.0: Detect

Identifiziert die Sprache eines Textabschnitts.

## <a name="request-url"></a>Anfrage-URL

Sendet eine `POST`-Anforderung an:

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Anforderungsparameter

Die folgenden Anforderungsparameter werden in der Abfragezeichenfolge übergeben:

<table width="100%">
  <th width="20%">Query parameter (Abfrageparameter)</th>
  <th>BESCHREIBUNG</th>
  <tr>
    <td>api-version</td>
    <td>*Erforderlicher Parameter*.<br/>Die vom Client angeforderte Version der API. Der Wert muss `3.0` sein.</td>
  </tr>
</table> 

Anforderungsheader enthalten Folgendes:

<table width="100%">
  <th width="20%">Header</th>
  <th>BESCHREIBUNG</th>
  <tr>
    <td>Authentifizierungsheader</td>
    <td><em>Erforderlicher Anforderungsheader</em>.<br/>Weitere Informationen finden Sie in den <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">verfügbaren Optionen für die Authentifizierung</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Erforderlicher Anforderungsheader*.<br/>Gibt den Inhaltstyp der Nutzlast an. Mögliche Werte: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Erforderlicher Anforderungsheader*.<br/>Die Länge des Anforderungstexts.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Optional:*<br/>Eine vom Client erstellte GUID zur eindeutigen Identifizierung der Anforderung. Beachten Sie, dass Sie diesen Header weglassen können, wenn Sie die Ablaufverfolgungs-ID mithilfe eines Abfrageparameters namens `ClientTraceId` in die Abfragezeichenfolge einschließen.</td>
  </tr>
</table> 

## <a name="request-body"></a>Anforderungstext

Der Anforderungstext ist ein JSON-Array. Jedes Arrayelement ist ein JSON-Objekt mit einer Zeichenfolgeneigenschaft namens `Text`. Die Spracherkennung wird auf den Wert der `Text`-Eigenschaft angewendet. Ein längerer Eingabetext erhöht die Zuverlässigkeit der automatischen Spracherkennung. Ein Beispiel-Anforderungstext sieht folgendermaßen aus:

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

Es gelten die folgenden Einschränkungen:

* Das Array kann höchstens über 100 Elemente verfügen.
* Der gesamte Text, der in einer Anforderung enthalten ist, kann 50.000 Zeichen (einschließlich Leerzeichen) nicht überschreiten.

## <a name="response-body"></a>Antworttext

Eine erfolgreiche Antwort ist ein JSON-Array mit einem Ergebnis für jede Zeichenfolge im Eingabearray. Ein Ergebnisobjekt enthält die folgenden Eigenschaften:

  * `language`: Code der erkannten Sprache.

  * `score`: Ein float-Wert, der die Zuverlässigkeit des Ergebnisses angibt. Die Bewertung bewegt sich zwischen 0 (null) und 1, und eine niedrige Bewertung gibt an, dass die Zuverlässigkeit zweifelhaft ist.

  * `isTranslationSupported`: Ein boolescher Wert, der TRUE lautet, wenn die erkannte Sprache eine der für die Textübersetzung unterstützten Sprachen ist.

  * `isTransliterationSupported`: Ein boolescher Wert, der TRUE lautet, wenn die erkannte Sprache eine der für die Transliteration unterstützten Sprachen ist.
  
  * `alternatives`: Ein Array anderer möglicher Sprachen. Jedes Element des Arrays ist ein anderes Objekt mit den gleichen Eigenschaften wie oben: `language`, `score`, `isTranslationSupported` und `isTransliterationSupported`.

Eine JSON-Beispielantwort lautet wie folgt:

```json
[
  {
    "language": "de",
    "score": 0.92,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "sk",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="response-headers"></a>Antwortheader

<table width="100%">
  <th width="20%">Header</th>
  <th>BESCHREIBUNG</th>
  <tr>
    <td>X-RequestId</td>
    <td>Der Wert, der vom Dienst für die Identifizierung der Anforderung generiert wird. Er wird zu Problembehandlungszwecken verwendet.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Antwortstatuscodes

Im Folgenden finden Sie die möglichen HTTP-Statuscodes, die eine Anforderung zurückgeben kann. 

<table width="100%">
  <th width="20%">Statuscode</th>
  <th>BESCHREIBUNG</th>
  <tr>
    <td>200</td>
    <td>Erfolg.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Einer der Abfrageparameter fehlt oder ist ungültig. Korrigieren Sie die Anforderungsparameter, bevor Sie es erneut versuchen.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Die Anforderung konnte nicht authentifiziert werden. Überprüfen Sie, ob die Anmeldeinformationen angegeben und gültig sind.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Die Anforderung ist nicht autorisiert. Weitere Informationen finden Sie in der Fehlermeldung. Diese weist oft darauf hin, dass alle kostenlosen Übersetzungen, die mit einer Testversion bereitgestellt wurden, aufgebraucht sind.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Der Server hat die Anforderung abgelehnt, da der Client die Anforderungsgrenzwerte überschritten hat.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Ein unerwarteter Fehler ist aufgetreten. Wenn der Fehler weiterhin besteht, melden Sie ihn, und gebe Sie Folgendes an: Datum und Zeitpunkt des Fehlers, Anforderungsbezeichner aus dem Anforderungsheader `X-RequestId` und Clientbezeichner aus dem Anforderungsheader `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Der Server ist vorübergehend nicht verfügbar. Wiederholen Sie die Anforderung. Wenn der Fehler weiterhin besteht, melden Sie ihn, und gebe Sie Folgendes an: Datum und Zeitpunkt des Fehlers, Anforderungsbezeichner aus dem Anforderungsheader `X-RequestId` und Clientbezeichner aus dem Anforderungsheader `X-ClientTraceId`.</td>
  </tr>
</table> 

Wenn ein Fehler auftritt, gibt die Anforderung auch eine JSON-Fehlerantwort zurück. Der Fehlercode ist eine 6-stellige Zahl, die aus dem 3-stelligen HTTP-Statuscode gefolgt von einer 3-stelligen Zahl zur Kategorisierung des Fehlers besteht. Häufige Fehlercodes finden Sie in der [Referenz zu Version 3 von Translator](./v3-0-reference.md#errors). 

## <a name="examples"></a>Beispiele

Im folgenden Beispiel wird dargestellt, wie Sprachen abgerufen werden, die für die Textübersetzung unterstützt werden.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```