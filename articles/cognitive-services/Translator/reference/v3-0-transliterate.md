---
title: Translator-Methode „Transliterate“
titleSuffix: Azure Cognitive Services
description: Konvertieren Sie Text in einer Sprache aus einem Skript in ein anderes Skript mithilfe der Translator-Methode „Transliterate“.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 4b985d7b2eb07ca8bbd3556a237fbcaba5392fe5
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016466"
---
# <a name="translator-30-transliterate"></a>Translator 3.0: Transliterate

Konvertiert Text in einer Sprache aus einem Skript in ein anderes Skript.

## <a name="request-url"></a>Anfrage-URL

Sendet eine `POST`-Anforderung an:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
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
  <tr>
    <td>language</td>
    <td>*Erforderlicher Parameter*.<br/>Gibt die Sprache des Texts an, der von einem Skript in ein anderes konvertiert werden soll. Mögliche Sprachen sind im `transliteration`-Bereich aufgeführt, der durch Abfrage des Diensts nach den [unterstützten Sprachen](./v3-0-languages.md) abgerufen wird.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Erforderlicher Parameter*.<br/>Gibt das vom Eingabetext verwendete Skript an. Suchen Sie mithilfe des `transliteration`-Bereichs nach [unterstützten Sprachen](./v3-0-languages.md), um Eingabeskripts zu finden, die für die ausgewählte Sprache verfügbar sind.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Erforderlicher Parameter*.<br/>Gibt das Ausgabeskript an. Suchen Sie mithilfe des `transliteration`-Bereichs nach [unterstützten Sprachen](./v3-0-languages.md), um Ausgabeskripts zu finden, die für die ausgewählte Kombination aus Sprache und Eingabeskript verfügbar sind.</td>
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

Der Anforderungstext ist ein JSON-Array. Jedes Arrayelement ist ein JSON-Objekt mit einer Zeichenfolgeneigenschaft namens `Text`, die die zu suchende Zeichenfolge repräsentiert.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Es gelten die folgenden Einschränkungen:

* Das Array darf höchstens 10 Elemente enthalten.
* Der Textwert eines Arrayelements darf 1.000 Zeichen (einschließlich Leerzeichen) nicht überschreiten.
* Der gesamte Text, der in der Anforderung enthalten ist, darf 5.000 Zeichen (einschließlich Leerzeichen) nicht überschreiten.

## <a name="response-body"></a>Antworttext

Eine erfolgreiche Antwort ist ein JSON-Array mit einem Ergebnis für jedes Element im Eingabearray. Ein Ergebnisobjekt enthält die folgenden Eigenschaften:

  * `text`: Eine Zeichenfolge, die aus der Konvertierung der Eingabezeichenfolge in das Ausgabeskript resultiert.
  
  * `script`: Eine Zeichenfolge, die das in der Ausgabe verwendete Skript angibt.

Eine JSON-Beispielantwort lautet wie folgt:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
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

Sollte ein Fehler auftreten, gibt die Anforderung auch eine JSON-Fehlerantwort zurück. Der Fehlercode ist eine 6-stellige Zahl, die aus dem 3-stelligen HTTP-Statuscode gefolgt von einer 3-stelligen Zahl zur Kategorisierung des Fehlers besteht. Häufige Fehlercodes finden Sie in der [Referenz zu Version 3 von Translator](./v3-0-reference.md#errors). 

## <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt, wie Sie zwei japanische Zeichenfolgen in lateinische Buchstaben konvertieren.

Die JSON-Nutzlast für die Anforderung in diesem Beispiel:

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Wenn Sie cURL in einem Befehlszeilenfenster verwenden, das keine Unicode-Zeichen unterstützt, verwenden Sie die folgende JSON-Nutzlast, und speichern Sie sie in eine Datei namens `request.txt`. Stellen Sie sicher, dass Sie diese Datei mit `UTF-8`-Codierung speichern.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```