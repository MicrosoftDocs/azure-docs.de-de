---
title: Translator-Methode „BreakSentence“
titleSuffix: Azure Cognitive Services
description: Die Translator-Methode „BreakSentence“ dient zum Ermitteln der Position von Satzgrenzen in einem Text.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/06/2020
ms.author: swmachan
ms.openlocfilehash: 27069cf38da2567a3af3ed0fb65baead43665186
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369476"
---
# <a name="translator-30-breaksentence"></a>Translator 3.0: BreakSentence

Erkennt die Positionierung von Satzgrenzen in einem Textabschnitt.

## <a name="request-url"></a>Anfrage-URL

Sendet eine `POST`-Anforderung an:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Anforderungsparameter

Die folgenden Anforderungsparameter werden in der Abfragezeichenfolge übergeben:

| Abfrageparameter | BESCHREIBUNG |
| -------| ----------- |
| api-version <img width=200/>   | **Erforderlicher Abfrageparameter**<br/>Die vom Client angeforderte Version der API. Der Wert muss `3.0` sein. |
| language | **Optionaler Abfrageparameter**<br/>Sprachtag, das die Sprache des Eingabetexts angibt. Wenn kein Code angegeben ist, wird die automatische Spracherkennung angewendet. |
| script    | **Optionaler Abfrageparameter**<br/>Skripttag, das das Skript erkennt, das vom Eingabetext verwendet wird. Wenn kein Skript angegeben ist, wird vom Standardskript der Sprache ausgegangen.  | 

Anforderungsheader enthalten Folgendes:

| Header | BESCHREIBUNG |
| ------- | ----------- |
| Authentifizierungsheader <img width=200/>  | **Erforderlicher Anforderungsheader**.<br/>Weitere Informationen finden Sie in den <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">verfügbaren Optionen für die Authentifizierung</a>. |
| Content-Type | **Erforderlicher Anforderungsheader**.<br/>Gibt den Inhaltstyp der Nutzlast an. Mögliche Werte: `application/json`. |
| Content-Length    | **Erforderlicher Anforderungsheader**.<br/>Die Länge des Anforderungstexts.  | 
| X-ClientTraceId   | **Optional:**<br/>Eine vom Client erstellte GUID zur eindeutigen Identifizierung der Anforderung. Beachten Sie, dass Sie diesen Header weglassen können, wenn Sie die Ablaufverfolgungs-ID mithilfe eines Abfrageparameters namens `ClientTraceId` in die Abfragezeichenfolge einschließen.  | 

## <a name="request-body"></a>Anforderungstext

Der Anforderungstext ist ein JSON-Array. Jedes Arrayelement ist ein JSON-Objekt mit einer Zeichenfolgeneigenschaft namens `Text`. Satzgrenzen werden für den Wert der `Text`-Eigenschaft berechnet. Nachfolgend finden Sie ein Beispiel für einen Anforderungstext, der aus einem Abschnitt besteht:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Es gelten die folgenden Einschränkungen:

* Das Array kann höchstens über 100 Elemente verfügen.
* Der Textwert eines Arrayelements darf 50.000 Zeichen (einschließlich Leerzeichen) nicht überschreiten.
* Der gesamte Text, der in einer Anforderung enthalten ist, kann 50.000 Zeichen (einschließlich Leerzeichen) nicht überschreiten.
* Wenn der Abfrageparameter `language` angegeben ist, müssen alle Arrayelemente in derselben Sprache sein. Wenn dies nicht der Fall ist, wird die automatische Spracherkennung auf jedes Arrayelement einzeln angewendet.

## <a name="response-body"></a>Antworttext

Eine erfolgreiche Antwort ist ein JSON-Array mit einem Ergebnis für jede Zeichenfolge im Eingabearray. Ein Ergebnisobjekt enthält die folgenden Eigenschaften:

  * `sentLen`: Ein Integerarray stellt die Länge der Sätze im Textelement dar. Die Länge des Arrays stellt die Anzahl von Sätzen dar, und die Werte stehen jeweils für die Länge der einzelnen Sätze. 

  * `detectedLanguage`: Ein Objekt, das die erkannte Sprache durch die folgenden Eigenschaften beschreibt:

     * `language`: Code der erkannten Sprache.

     * `score`: Ein float-Wert, der die Zuverlässigkeit des Ergebnisses angibt. Die Bewertung bewegt sich zwischen 0 (null) und 1, und eine niedrige Bewertung gibt an, dass die Zuverlässigkeit zweifelhaft ist.
     
    Beachten Sie, dass die `detectedLanguage`-Eigenschaft nur im Ergebnisobjekt enthalten ist, wenn die automatische Spracherkennung angefordert wird.

Eine JSON-Beispielantwort lautet wie folgt:

```json
[
  {
    "sentLen": [ 13, 11, 22 ]
    "detectedLanguage": {
      "language": "en",
      "score": 401
    },
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

Das folgende Beispiel zeigt, wie Sie Satzgrenzen für einen einzelnen Satz abrufen können. Die Sprache des Satzes wird automatisch vom Dienst erkannt.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```