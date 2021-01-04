---
title: Translator-Methode „Languages“
titleSuffix: Azure Cognitive Services
description: Die Methode „Languages“ dient zum Abrufen der Sprachen, die derzeit von anderen Translator-Vorgängen unterstützt werden.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: d9abd1f305e47eca743dc9274e0ec1f0a950dd6a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021268"
---
# <a name="translator-30-languages"></a>Translator 3.0: Languages

Ruft die Sprachen ab, die derzeit von anderen Translator-Vorgängen unterstützt werden. 

## <a name="request-url"></a>Anfrage-URL

Sendet eine `GET`-Anforderung an:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Anforderungsparameter

Die folgenden Anforderungsparameter werden in der Abfragezeichenfolge übergeben:

<table width="100%">
  <th width="20%">Query parameter (Abfrageparameter)</th>
  <th>BESCHREIBUNG</th>
  <tr>
    <td>api-version</td>
    <td><em>Erforderlicher Parameter</em>.<br/>Die vom Client angeforderte Version der API. Der Wert muss `3.0` sein.</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Optionaler Parameter*.<br/>Eine durch Trennzeichen getrennte Liste von Namen, die die Gruppe der zurückzugebenden Sprachen definieren. Zulässige Gruppennamen sind: `translation`, `transliteration` und `dictionary`. Wenn kein Bereich angegeben wird, werden alle Gruppen zurückgegeben. Dies entspricht dem Übergeben von `scope=translation,transliteration,dictionary`. Um zu entscheiden, welche Gruppe der unterstützten Sprachen für Ihr Szenario geeignet ist, lesen Sie die Beschreibung für das [Antwortobjekt](#response-body).</td>
  </tr>
</table> 

Anforderungsheader:

<table width="100%">
  <th width="20%">Header</th>
  <th>BESCHREIBUNG</th>
  <tr>
    <td>Accept-Language</td>
    <td>*Optionaler Anforderungsheader*.<br/>Die Sprache, die für Zeichenfolgen der Benutzeroberfläche verwendet werden soll. Einige der Felder in der Antwort sind Namen von Sprachen oder Namen von Regionen. Verwenden Sie diesen Parameter, um die Sprache zu definieren, in der diese Namen zurückgegeben werden. Die Sprache wird durch die Bereitstellung eines wohlgeformten BCP 47-Sprachtags angegeben. Verwenden Sie z.B. den Wert `fr` zum Anfordern von Namen in Französisch, oder verwenden Sie den Wert `zh-Hant` zum Anfordern von Namen in Chinesisch (traditionell).<br/>Namen werden in englischer Sprache bereitgestellt, wenn keine Zielsprache angegeben wird oder keine Lokalisierung verfügbar ist.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Optionaler Anforderungsheader*.<br/>Eine vom Client erstellte GUID zur eindeutigen Identifizierung der Anforderung.</td>
  </tr>
</table> 

Authentifizierung ist nicht erforderlich, um Sprachressourcen abzurufen.

## <a name="response-body"></a>Antworttext

Ein Client verwendet den `scope`-Abfrageparameter zum Definieren, an welchen Gruppen von Sprachen er interessiert ist.

* `scope=translation` bietet unterstützte Sprachen, um Text aus einer Sprache in eine andere Sprache zu übersetzen.

* `scope=transliteration` bietet Funktionen zum Konvertieren von Text in einer Sprache aus einem Skript in ein anderes Skript.

* `scope=dictionary` bietet Sprachpaare, für die `Dictionary`-Vorgänge Daten zurückgeben.

Ein Client kann mehrere Gruppen gleichzeitig abrufen, indem er eine durch Trennzeichen getrennte Liste von Namen angibt. Beispielsweise würde `scope=translation,transliteration,dictionary` unterstützte Sprachen für alle Gruppen zurückgeben.

Eine erfolgreiche Antwort ist ein JSON-Objekt mit einer Eigenschaft für jede angeforderte Gruppe:

```json
{
    "translation": {
        //... set of languages supported to translate text (scope=translation)
    },
    "transliteration": {
        //... set of languages supported to convert between scripts (scope=transliteration)
    },
    "dictionary": {
        //... set of languages supported for alternative translations and examples (scope=dictionary)
    }
}
```

Der Wert für jede Eigenschaft lautet wie folgt.

* `translation`-Eigenschaft

  Der Wert der `translation`-Eigenschaft ist ein Wörterbuch aus Schlüssel-Wert-Paaren. Jeder Schlüssel ist ein BCP 47-Sprachtag. Ein Schlüssel identifiziert eine Sprache, in die oder aus der Text übersetzt werden kann. Der Wert, der dem Schlüssel zugeordnet ist, ist ein JSON-Objekt mit Eigenschaften, die die Sprache beschreiben:

  * `name`: Der Anzeigename der Sprache im Gebietsschema, der über den `Accept-Language`-Header angefordert wurde.

  * `nativeName`: Der Anzeigename der Sprache in dem Gebietsschema, das nativ für diese Sprache ist.

  * `dir`: Die Direktionalität, also `rtl` für Sprachen, die von rechts nach links gelesen werden, bzw. `ltr` für Sprachen, die von links nach rechts gelesen werden.

  Im folgenden Code wird ein Beispiel veranschaulicht:
          
  ```json
  {
    "translation": {
      ...
      "fr": {
        "name": "French",
        "nativeName": "Français",
        "dir": "ltr"
      },
      ...
    }
  }
  ```

* `transliteration`-Eigenschaft

  Der Wert der `transliteration`-Eigenschaft ist ein Wörterbuch aus Schlüssel-Wert-Paaren. Jeder Schlüssel ist ein BCP 47-Sprachtag. Ein Schlüssel identifiziert eine Sprache, für die Text aus einem Skript in ein anderes Skript konvertiert werden kann. Der Wert, der dem Schlüssel zugeordnet ist, ist ein JSON-Objekt mit Eigenschaften, die die Sprache und deren unterstützte Skripts beschreiben:

  * `name`: Der Anzeigename der Sprache im Gebietsschema, der über den `Accept-Language`-Header angefordert wurde.

  * `nativeName`: Der Anzeigename der Sprache in dem Gebietsschema, das nativ für diese Sprache ist.

  * `scripts`: Die Liste der Skripts, aus denen die Konvertierung erfolgen soll. Jedes Element der `scripts`-Liste verfügt über Eigenschaften:

    * `code`: Code, der das Skript identifiziert.

    * `name`: Der Anzeigename des Skripts im Gebietsschema, der über den `Accept-Language`-Header angefordert wurde.

    * `nativeName`: Der Anzeigename der Sprache in dem Gebietsschema, das nativ für die Sprache ist.

    * `dir`: Die Direktionalität, also `rtl` für Sprachen, die von rechts nach links gelesen werden, bzw. `ltr` für Sprachen, die von links nach rechts gelesen werden.

    * `toScripts`: Die Liste der Skripts, in die Text konvertiert werden kann. Jedes Element der `toScripts`-Liste verfügt über die Eigenschaften `code`, `name`, `nativeName` und `dir` (wie zuvor beschrieben).

  Im folgenden Code wird ein Beispiel veranschaulicht:

  ```json
  {
    "transliteration": {
      ...
      "ja": {
        "name": "Japanese",
        "nativeName": "日本語",
        "scripts": [
          {
            "code": "Jpan",
            "name": "Japanese",
            "nativeName": "日本語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Latn",
                "name": "Latin",
                "nativeName": "ラテン語",
                "dir": "ltr"
              }
            ]
          },
          {
            "code": "Latn",
            "name": "Latin",
            "nativeName": "ラテン語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Jpan",
                "name": "Japanese",
                "nativeName": "日本語",
                "dir": "ltr"
              }
            ]
          }
        ]
      },
      ...
    }
  }
  ```

* `dictionary`-Eigenschaft

  Der Wert der `dictionary`-Eigenschaft ist ein Wörterbuch aus Schlüssel-Wert-Paaren. Jeder Schlüssel ist ein BCP 47-Sprachtag. Der Schlüssel identifiziert eine Sprache, für die alternative Übersetzungen und Rückübersetzungen verfügbar sind. Der Wert ist ein JSON-Objekt, das die Quellsprache und die Zielsprachen mit verfügbaren Übersetzungen beschreibt:

  * `name`: Der Anzeigename der Quellsprache im Gebietsschema, der über den `Accept-Language`-Header angefordert wurde.

  * `nativeName`: Der Anzeigename der Sprache in dem Gebietsschema, das nativ für diese Sprache ist.

  * `dir`: Die Direktionalität, also `rtl` für Sprachen, die von rechts nach links gelesen werden, bzw. `ltr` für Sprachen, die von links nach rechts gelesen werden.

  * `translations`: Die Liste der Sprachen mit alternativen Übersetzungen sowie Beispiele für die in der Quellsprache ausgedrückte Abfrage. Jedes Element der `translations`-Liste verfügt über Eigenschaften:

    * `name`: Der Anzeigename der Zielsprache im Gebietsschema, der über den `Accept-Language`-Header angefordert wurde.

    * `nativeName`: Der Anzeigename der Zielsprache in dem Gebietsschema, das nativ für die Zielsprache ist.

    * `dir`: Die Direktionalität, also `rtl` für Sprachen, die von rechts nach links gelesen werden, bzw. `ltr` für Sprachen, die von links nach rechts gelesen werden.
    
    * `code`: Sprachcode, der die Zielsprache identifiziert.

  Im folgenden Code wird ein Beispiel veranschaulicht:

  ```json
  "es": {
    "name": "Spanish",
    "nativeName": "Español",
    "dir": "ltr",
    "translations": [
      {
        "name": "English",
        "nativeName": "English",
        "dir": "ltr",
        "code": "en"
      }
    ]
  },
  ```

Die Struktur des Antwortobjekts ändert sich ohne eine Änderung in der Version der API nicht. Für die gleiche Version der API kann sich die Liste der verfügbaren Sprachen im Laufe der Zeit ändern, da Microsoft Translator die Liste der von seinen Diensten unterstützten Sprachen ständig erweitert.

Die Liste der unterstützten Sprachen ändert sich nicht häufig. Um Netzwerkbandbreite zu sparen und die Reaktionsfähigkeit zu verbessern, sollte eine Clientanwendung die Zwischenspeicherung von Sprachressourcen und des entsprechenden Entitätstags (`ETag`) in Betracht ziehen. Dann kann die Clientanwendung regelmäßig (z.B. ein Mal alle 24 Stunden) den Dienst abfragen, um die neuesten unterstützten Sprachen abzurufen. Das Übergeben des aktuellen `ETag`-Werts in einem `If-None-Match`-Headerfeld ermöglicht dem Dienst, die Antwort zu optimieren. Wenn die Ressource nicht geändert wurde, gibt der Dienst den Statuscode 304 sowie einen leeren Antworttext zurück.

## <a name="response-headers"></a>Antwortheader

<table width="100%">
  <th width="20%">Header</th>
  <th>BESCHREIBUNG</th>
  <tr>
    <td>ETag</td>
    <td>Der aktuelle Wert des Entitätstags für die angeforderten Gruppen unterstützter Sprachen. Um nachfolgende Anforderungen effizienter zu gestalten, kann der Client den `ETag`-Wert in einem `If-None-Match`-Headerfeld senden.
    </td>
  </tr>
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
    <td>304</td>
    <td>Die Ressource wurde seit der in den Anforderungsheadern `If-None-Match` angegebenen Version nicht geändert.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Einer der Abfrageparameter fehlt oder ist ungültig. Korrigieren Sie die Anforderungsparameter, bevor Sie es erneut versuchen.</td>
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
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```