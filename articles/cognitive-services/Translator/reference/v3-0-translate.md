---
title: Translator-Methode „Translate“
titleSuffix: Azure Cognitive Services
description: Hier finden Sie Informationen zu den Parametern, Headern und Textnachrichten für die Methode „Translate“ von Azure Cognitive Services Translator zur Übersetzung von Text.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/06/2020
ms.author: swmachan
ms.openlocfilehash: 15f20bab91a4feab056a9f8cd6b012792f036be2
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016546"
---
# <a name="translator-30-translate"></a>Translator 3.0: Translate

Übersetzt Text.

## <a name="request-url"></a>Anfrage-URL

Sendet eine `POST`-Anforderung an:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Anforderungsparameter

Die folgenden Anforderungsparameter werden in der Abfragezeichenfolge übergeben:

### <a name="required-parameters"></a>Erforderliche Parameter

<table width="100%">
  <th width="20%">Query parameter (Abfrageparameter)</th>
  <th>BESCHREIBUNG</th>
  <tr>
    <td>api-version</td>
    <td><em>Erforderlicher Parameter</em>.<br/>Die vom Client angeforderte Version der API. Der Wert muss <code>3.0</code> sein.</td>
  </tr>
  <tr>
    <td>zu</td>
    <td><em>Erforderlicher Parameter</em>.<br/>Gibt die Sprache des Ausgabetexts an. Sie müssen eine der zum <code>translation</code>-Bereich hinzugefügten <a href="./v3-0-languages.md">unterstützten Sprachen</a> als Zielsprache auswählen. Verwenden Sie z.B. <code>to=de</code> für die Übersetzung ins Deutsche.<br/>Durch Wiederholen des Parameters in der Abfragezeichenfolge ist es möglich, in mehrere Sprachen gleichzeitig zu übersetzen. Verwenden Sie z.B. <code>to=de&to=it</code> für die Übersetzung ins Deutsche und Italienische.</td>
  </tr>
</table>

### <a name="optional-parameters"></a>Optionale Parameter

<table width="100%">
  <th width="20%">Query parameter (Abfrageparameter)</th>
  <th>BESCHREIBUNG</th>
  <tr>
    <td>from</td>
    <td><em>Optionaler Parameter</em>.<br/>Gibt die Sprache des Eingabetexts an. Finden Sie heraus, aus welchen Sprachen Sie übersetzen können, indem Sie die <a href="./v3-0-languages.md">unterstützten Sprachen</a> mithilfe des<code>translation</code>-Bereichs. Wenn kein <code>from</code>-Parameter angegeben wird, wird die automatische Sprachenerkennung zum Bestimmen der Quellsprache verwendet. <br/><br/>Sie müssen den Parameter <code>from</code> anstelle der automatischen Erkennung verwenden, wenn Sie die Funktion für ein <a href="/azure/cognitive-services/translator/dynamic-dictionary">dynamisches Wörterbuch</a> verwenden.</td>
  </tr>  
  <tr>
    <td>textType</td>
    <td><em>Optionaler Parameter</em>.<br/>Definiert, ob es sich bei dem zu übersetzenden Text um Nur-Text oder um HTML-Text handelt. Jede HTML muss ein wohlgeformtes vollständiges Element sein. Mögliche Werte: <code>plain</code> (Standard) oder <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Optionaler Parameter</em>.<br/>Eine Zeichenfolge, die die Kategorie (Domäne) der Übersetzung angibt. Dieser Parameter wird verwendet, um Übersetzungen von einem benutzerdefinierten System zu erhalten, das mit <a href="../customization.md">Custom Translator</a> erstellt wurde. Fügen Sie diesem Parameter die Kategorie-ID Ihrer „Benutzerdefinierter Translator“-<a href="/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">Projektdetails</a> hinzu, um Ihr bereitgestelltes angepasstes System zu verwenden. Der Standardwert ist <code>general</code>.</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td><em>Optionaler Parameter</em>.<br/>Gibt an, wie Obszönitäten in Übersetzungen behandelt werden. Mögliche Werte sind: <code>NoAction</code> (Standard), <code>Marked</code> oder <code>Deleted</code>. Nähere Informationen zur Handhabung von Obszönitäten finden Sie unter <a href="#handle-profanity">Behandlung von Obszönitäten</a>.</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td><em>Optionaler Parameter</em>.<br/>Gibt an, wie Obszönitäten in Übersetzungen markiert werden. Mögliche Werte sind: <code>Asterisk</code> (Standard) oder <code>Tag</code>. Nähere Informationen zur Handhabung von Obszönitäten finden Sie unter <a href="#handle-profanity">Behandlung von Obszönitäten</a>.</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>Optionaler Parameter</em>.<br/>Gibt an, ob die Ausrichtungsprojektion des Quelltexts für den übersetzten Text verwendet wird. Mögliche Werte sind: <code>true</code> oder <code>false</code> (Standard). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>Optionaler Parameter</em>.<br/>Gibt an, ob Satzgrenzen für den eingegebenen und den übersetzten Text verwendet werden. Mögliche Werte sind: <code>true</code> oder <code>false</code> (Standard).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>Optionaler Parameter</em>.<br/>Gibt eine Fallbacksprache an, wenn die Sprache des Eingabetexts nicht identifiziert werden kann. Die automatische Spracherkennung wird angewendet, wenn der <code>from</code> Parameter ausgelassen wird. Wenn die Erkennung fehlschlägt, wird die Sprache <code>suggestedFrom</code> angenommen.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>Optionaler Parameter</em>.<br/>Gibt das Skript des Eingabetexts an.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>Optionaler Parameter</em>.<br/>Gibt das Skript des Eingabetexts an.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>Optionaler Parameter</em>.<br/>Gibt an, dass für den Dienst ein Fallback zu einem allgemeinen System zulässig ist, wenn kein benutzerdefiniertes System vorhanden ist. Mögliche Werte sind: <code>true</code> (Standard) oder <code>false</code>.<br/><br/><code>allowFallback=false</code> gibt an, dass für die Übersetzung nur Systeme verwendet werden sollen, die für die angegebene <code>category</code> in der Anforderung trainiert sind. Wenn eine Übersetzung von der Sprache X in die Sprache Y die Verkettung über eine Pivot- bzw. Zwischensprache E erfordert, müssen alle Systeme in der Kette (X->E und E->Y) benutzerdefiniert sein und die gleiche Kategorie aufweisen. Wird kein System mit der jeweiligen Kategorie gefunden, gibt die Anforderung den Statuscode 400 zurück. <code>allowFallback=true</code> gibt an, dass für den Dienst ein Fallback zu einem allgemeinen System zulässig ist, wenn kein benutzerdefiniertes System vorhanden ist.
</td>
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
    <td><em>Erforderlicher Anforderungsheader</em>.<br/>Gibt den Inhaltstyp der Nutzlast an.<br/> Der zulässige Wert ist <code>application/json; charset=UTF-8</code>.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td><em>Erforderlicher Anforderungsheader</em>.<br/>Die Länge des Anforderungstexts.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Optional:</em><br/>Eine vom Client erstellte GUID zur eindeutigen Identifizierung der Anforderung. Sie können diesen Header nur weglassen, wenn Sie die Ablaufverfolgungs-ID in die Abfragezeichenfolge über einen Abfrageparameter namens <code>ClientTraceId</code> einschließen.</td>
  </tr>
</table> 

## <a name="request-body"></a>Anforderungstext

Der Anforderungstext ist ein JSON-Array. Jedes Arrayelement ist ein JSON-Objekt mit einer Zeichenfolgeneigenschaft namens `Text`, die die zu suchende Zeichenfolge repräsentiert.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Es gelten die folgenden Einschränkungen:

* Das Array kann höchstens über 100 Elemente verfügen.
* Der gesamte Anforderungstext darf nicht mehr als 10.000 Zeichen enthalten (einschließlich Leerzeichen).

## <a name="response-body"></a>Antworttext

Eine erfolgreiche Antwort ist ein JSON-Array mit einem Ergebnis für jede Zeichenfolge im Eingabearray. Ein Ergebnisobjekt enthält die folgenden Eigenschaften:

  * `detectedLanguage`: Ein Objekt, das die erkannte Sprache durch die folgenden Eigenschaften beschreibt:

      * `language`: Eine Zeichenfolge, die den Code der erkannten Sprache darstellt.

      * `score`: Ein float-Wert, der die Zuverlässigkeit des Ergebnisses angibt. Die Bewertung bewegt sich zwischen 0 (null) und 1, und eine niedrige Bewertung gibt an, dass die Zuverlässigkeit zweifelhaft ist.

    Die `detectedLanguage`-Eigenschaft ist nur im Ergebnisobjekt enthalten, wenn die automatische Spracherkennung angefordert wird.

  * `translations`: Ein Array von Übersetzungsergebnissen. Die Größe des Arrays entspricht der Anzahl der durch den `to`-Abfrageparameter angegebenen Zielsprachen. Jedes Element im Array enthält:

    * `to`: Eine Zeichenfolge, die den Sprachcode der Zielsprache darstellt.

    * `text`: Eine Zeichenfolge, die den übersetzten Text enthält.

    * `transliteration`: Ein Objekt, das den übersetzten Text in dem durch den `toScript`-Parameter angegebenen Skript enthält.

      * `script`: Eine Zeichenfolge, die das Zielskript angibt.   

      * `text`: Eine Zeichenfolge, die den übersetzten Text im Zielskript enthält.

    Das `transliteration`-Objekt ist nur dann enthalten, wenn eine Transliteration erfolgt.

    * `alignment`: Ein Objekt mit einer einzelnen Zeichenfolgeneigenschaft namens `proj`, das dem übersetzten Text Eingabetext zuordnet. Die Informationen für die Ausrichtung werden nur bereitgestellt, wenn der Anforderungsparameter `includeAlignment``true` ist. Die Ausrichtung wird als Zeichenfolgenwert mit dem folgenden Format zurückgegeben: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  Der Doppelpunkt trennt Start- und Endindex, der Bindestrich trennt die Sprachen, und Leerzeichen trennen die Wörter. Ein Wort kann mit Null, einem oder mehreren Wörtern in der anderen Sprache übereinstimmen, und die ausgerichteten Wörter sind ggf. nicht zusammenhängend. Wenn keine Informationen für die Ausrichtung verfügbar sind, ist das Ausrichtungselement leer. Beispiele und Einschränkungen finden Sie in Abschnitt [Abrufen von Ausrichtungsinformationen](#obtain-alignment-information).

    * `sentLen`: Ein Objekt, das Satzgrenzen in den Eingabe- und Ausgabetexten zurückgibt.

      * `srcSentLen`: Ein Integer-Array, das die Länge der Sätze im Eingabetext darstellt. Die Länge des Arrays stellt die Anzahl von Sätzen dar, und die Werte stehen jeweils für die Länge der einzelnen Sätze.

      * `transSentLen`:  Ein Integer-Array, das die Länge der Sätze im übersetzten Text darstellt. Die Länge des Arrays stellt die Anzahl von Sätzen dar, und die Werte stehen jeweils für die Länge der einzelnen Sätze.

    Satzgrenzen sind nur enthalten, wenn der Anforderungsparameter `includeSentenceLength``true` ist.

  * `sourceText`: Ein Objekt mit einer einzelnen Zeichenfolgeneigenschaft namens `text`, das den Eingabetext im Standardskript der Quellsprache bereitstellt. Die Eigenschaft `sourceText` ist nur vorhanden, wenn die Eingabe in einem Skript ausgedrückt wird, das nicht das übliche Skript für die Sprache ist. Wenn die Eingabe z.B. ein arabischer Text ist, der im lateinischen Skript verfasst wurde, würde `sourceText.text` diesen arabischen Text in das arabische Skript konvertieren.

Beispiele für JSON-Antworten finden Sie im Abschnitt [Beispiele](#examples).

## <a name="response-headers"></a>Antwortheader

<table width="100%">
  <th width="20%">Header</th>
  <th>BESCHREIBUNG</th>
    <tr>
    <td>X-RequestId</td>
    <td>Der Wert, der vom Dienst für die Identifizierung der Anforderung generiert wird. Er wird zu Problembehandlungszwecken verwendet.</td>
  </tr>
  <tr>
    <td>X-MT-System</td>
    <td>Gibt den Systemtyp an, der für jede zur Übersetzung angeforderte Zielsprache für die Übersetzung verwendet wurde. Bei dem Wert handelt es sich um eine durch Trennzeichen getrennte Liste von Zeichenfolgen. Jede Zeichenfolge gibt einen Typ an:<br/><ul><li>Custom: Die Anforderung enthält ein benutzerdefiniertes System, und mindestens ein benutzerdefiniertes System wurde während der Übersetzung verwendet.</li><li>Team: Alle anderen Anforderungen.</li></td>
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
    <td>408</td>
    <td>Die Anforderung konnte nicht ausgeführt werden, weil eine Ressource nicht vorhanden ist. Weitere Informationen finden Sie in der Fehlermeldung. Bei der Verwendung einer benutzerdefinierten <code>category</code> weist dies häufig darauf hin, dass das benutzerdefinierte Übersetzungssystem noch nicht zum Verarbeiten von Anforderungen verfügbar ist. Die Anforderung sollte nach einer Wartezeit (z. B. eine Minute) wiederholt werden.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Der Server hat die Anforderung abgelehnt, da der Client die Anforderungsgrenzwerte überschritten hat.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Ein unerwarteter Fehler ist aufgetreten. Wenn der Fehler weiterhin besteht, melden Sie ihn, und gebe Sie Folgendes an: Datum und Zeitpunkt des Fehlers, Anforderungsbezeichner aus dem Anforderungsheader <code>X-RequestId</code> und Clientbezeichner aus dem Anforderungsheader <code>X-ClientTraceId</code>.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Der Server ist vorübergehend nicht verfügbar. Wiederholen Sie die Anforderung. Wenn der Fehler weiterhin besteht, melden Sie ihn, und gebe Sie Folgendes an: Datum und Zeitpunkt des Fehlers, Anforderungsbezeichner aus dem Anforderungsheader <code>X-RequestId</code> und Clientbezeichner aus dem Anforderungsheader <code>X-ClientTraceId</code>.</td>
  </tr>
</table> 

Wenn ein Fehler auftritt, gibt die Anforderung auch eine JSON-Fehlerantwort zurück. Der Fehlercode ist eine 6-stellige Zahl, die aus dem 3-stelligen HTTP-Statuscode gefolgt von einer 3-stelligen Zahl zur Kategorisierung des Fehlers besteht. Häufige Fehlercodes finden Sie in der [Referenz zu Version 3 von Translator](./v3-0-reference.md#errors). 

## <a name="examples"></a>Beispiele

### <a name="translate-a-single-input"></a>Übersetzen einer einzelnen Eingabe

Dieses Beispiel zeigt, wie ein einzelner Satz aus dem Englischen ins Chinesische (vereinfacht) übersetzt wird.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Der Antworttext lautet:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

Das `translations` Array enthält ein Element, das die Übersetzung des einzelnen Textausschnitts in der Eingabe bereitstellt.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Übersetzen einer einzelnen Eingabe mit automatischer Spracherkennung

Dieses Beispiel zeigt, wie ein einzelner Satz aus dem Englischen ins Chinesische (vereinfacht) übersetzt wird. Die Anforderung gibt keine Eingabesprache an. Es wird stattdessen die automatische Erkennung der Quellsprache verwendet.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Der Antworttext lautet:

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
Die Antwort ähnelt der Antwort aus dem vorherigen Beispiel. Da die automatische Spracherkennung angefordert wurde, enthält die Antwort auch Informationen über die erkannte Sprache für den Eingabetext. Ein längerer Eingabetext erhöht die Zuverlässigkeit der automatischen Spracherkennung.

### <a name="translate-with-transliteration"></a>Übersetzen mit Transliteration

Das vorherige Beispiel wird jetzt durch Hinzufügen einer Transliteration erweitert. Die folgende Anforderung beinhaltet eine chinesische Übersetzung, die in einem lateinischen Skript verfasst wurde.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Der Antworttext lautet:

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

Das Übersetzungsergebnis beinhaltet nun eine `transliteration`-Eigenschaft, die den übersetzten Text mit lateinischen Zeichen enthält.

### <a name="translate-multiple-pieces-of-text"></a>Übersetzen mehrerer Textausschnitte

Um mehrere Strings gleichzeitig zu übersetzen, muss lediglich ein Array von Zeichenfolgen im Anforderungstext angegeben werden.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

Die Antwort enthält die Übersetzung aller Textteile in exakt derselben Reihenfolge wie in der Anforderung.
Der Antworttext lautet:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Übersetzen in mehrere Sprachen

Dieses Beispiel zeigt, wie in einer Anforderung ein- und dieselbe Eingabe in mehrere Sprachen übersetzt wird.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Der Antworttext lautet:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>Handhabung von Obszönitäten

In der Regel behält der Übersetzungsdienst Obszönitäten, die im Quelltext vorhanden sind, in der Übersetzung bei. Der Grad der Obszönität sowie der Kontext, in dem Wörter als obszön gelten, unterscheiden sich von Kultur zu Kultur. Der Grad einer Obszönität kann deshalb in der Zielsprache höher oder geringer sein.

Wenn Sie möchten, dass die Übersetzung keine Obszönitäten enthält, auch wenn diese im Quelltext vorhanden sind, können Sie mithilfe der Obszönitäten-Filteroption die entsprechenden Textstellen überarbeiten. Sie können mit dieser Option Obszönitäten löschen, mit entsprechenden Tags markieren (und so Ihre eigene Nachbearbeitung hinzufügen) oder auch keine Maßnahme ergreifen. Akzeptierte Werte von `ProfanityAction` sind `Deleted`, `Marked` und `NoAction` (Standard).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Aktion</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Dies ist das Standardverhalten. Die Obszönitäten werden von der Quell- in die Zielsprache übertragen.<br/><br/>
    <strong>Beispielquelle (Japanisch)</strong>: 彼はジャッカスです。<br/>
    <strong>Beispielübersetzung (Englisch):</strong> Er ist ein Trottel.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Obszöne Begriffe werden aus der Ausgabe entfernt, und es wird kein Ersatzbegriff gestellt.<br/><br/>
    <strong>Beispielquelle (Japanisch)</strong>: 彼はジャッカスです。<br/>
    <strong>Beispielübersetzung (Englisch):</strong> He is a. (Er ist ein.)
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Obszöne Wörter werden durch einen Marker in der Ausgabe ersetzt. Der Marker hängt von dem Parameter <code>ProfanityMarker</code> ab.<br/><br/>
Bei Verwendung von <code>ProfanityMarker=Asterisk</code> werden obszöne Wörter durch <code>***</code> ersetzt:<br/>
    <strong>Beispielquelle (Japanisch)</strong>: 彼はジャッカスです。<br/>
    <strong>Beispielübersetzung (Englisch):</strong> He is a \*\*\*.<br/><br/>
Bei Verwendung von <code>ProfanityMarker=Tag</code> werden obszöne Wörter von den XML-Tags &lt;profanity&gt; und &lt;/profanity&gt; umschlossen:<br/>
    <strong>Beispielquelle (Japanisch)</strong>: 彼はジャッカスです。<br/>
    <strong>Beispielübersetzung (Englisch):</strong> He is a &lt;profanity&gt;jackass&lt;/profanity&gt;.
  </tr>
</table> 

Beispiel:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```
Gibt Folgendes zurück:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Verglichen mit:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```

Diese letzte Anforderung gibt Folgendes zurück:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Übersetzen von Inhalten mit Markups und Auswählen des zu übersetzenden Texts

Häufig werden Inhalte übersetzt, die Markups enthalten, wie z.B. Inhalte von einer HTML-Seite oder aus einem XML-Dokument. Beziehen Sie `textType=html`-Abfrageparameter beim Übersetzen von Inhalten mit Tags ein. Manchmal ist es auch sinnvoll, bestimmten Inhalt von der Übersetzung auszuschließen. Sie können das Attribut `class=notranslate` verwenden, um Inhalt anzugeben, der in der Originalsprache übernommen werden soll. Im folgenden Beispiel wird der Inhalt im ersten `div`-Element nicht übersetzt, während der Inhalt im zweiten `div`-Element übersetzt wird.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Zur Veranschaulichung finden Sie Folgenden ein Beispiel einer Anforderung.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

Die Antwort lautet:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Abrufen von Informationen zur Ausrichtung

Die Ausrichtung wird als Zeichenfolgenwert des folgenden Formats für jedes Wort der Quelle zurückgegeben. Die Informationen für jedes Wort sind durch ein Leerzeichen getrennt. Dies gilt auch für Sprachen (Skripts), die keine Leerzeichen als Trennung enthalten, z.B. Chinesisch:

[[SourceTextStartIndex]\:[SourceTextEndIndex]–[TgtTextStartIndex]\:[TgtTextEndIndex]] *

Beispiel für Ausrichtungszeichenfolge: „0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21“.

Anders ausgedrückt: Der Doppelpunkt trennt Start- und Endindex, der Bindestrich trennt die Sprachen, und Leerzeichen trennen die Wörter. Ein Wort kann mit Null, einem oder mehreren Wörtern in der anderen Sprache übereinstimmen, und die ausgerichteten Wörter sind ggf. nicht zusammenhängend. Wenn keine Informationen für die Ausrichtung verfügbar sind, ist das Ausrichtungselement leer. In diesem Fall gibt die Methode keinen Fehler zurück.

Um Informationen über die Ausrichtung zu erhalten, geben Sie `includeAlignment=true` in der Abfragezeichenfolge ein.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation.'}]"
```

Die Antwort lautet:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

Die Informationen für die Ausrichtung beginnen mit `0:2-0:1`, was bedeutet, dass die ersten drei Zeichen im Quelltext (`The`) den beiden ersten Zeichen im übersetzten Text (`La`) zugeordnet sind.

#### <a name="limitations"></a>Einschränkungen
Das Abrufen der Ausrichtungsinformationen ist ein experimentelles Feature, das für prototypbasierte Untersuchungen und Umgebungen mit möglichen Ausdruckszuordnungen aktiviert wurde. Dieses Features wird in Zukunft unter Umständen nicht mehr unterstützt. Nachfolgend finden Sie einige relevante Einschränkungen bei der Unterstützung von Ausrichtungen:

* Ausrichtung ist für Text im HTML-Format – d.h. textType=html – nicht verfügbar.
* Die Ausrichtung wird nur für eine Teilmenge der Sprachpaare zurückgegeben:
  - Aus einer beliebigen Sprache ins Englische bzw. aus dem Englischen in eine beliebige Sprache, mit Ausnahme von Chinesisch (traditionell), Kantonesisch (traditionell) und Serbisch (Kyrillisch)
  - Aus dem Japanischen ins Koreanische und aus dem Koreanischen ins Japanische
  - Aus dem Japanischen ins Chinesische (vereinfacht) und umgekehrt 
  - Aus dem Chinesischen (vereinfacht) ins Chinesische (traditionell) und umgekehrt 
* Sie erhalten keine Ausrichtung, wenn es sich bei dem Satz um eine vordefinierte Übersetzung handelt. Beispiele für vordefinierte Übersetzung sind „This is a test“, „I love you“ und andere häufig verwendete Sätze.
* Die Ausrichtung ist nicht verfügbar, wenn Sie einen der [hier beschriebenen](../prevent-translation.md) Ansätze zum Verhindern der Übersetzung anwenden.

### <a name="obtain-sentence-boundaries"></a>Abrufen von Satzgrenzen

Um Informationen über die Satzlänge im Quell- und im übersetzten Text zu erhalten, geben Sie `includeSentenceLength=true` in der Abfragezeichenfolge an.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

Die Antwort lautet:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>Übersetzen mit dynamischem Wörterbuch

Wenn Ihnen die Übersetzung eines Worts oder eines Ausdrucks bereits bekannt ist, können Sie diese als Markup in der Anforderung angeben. Das dynamische Wörterbuch ist nur für Eigennamen wie Personen- und Produktnamen sicher.

Das anzugebende Markup verwendet die folgende Syntax.

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Sehen Sie sich hierzu den englischen Satz „The word wordomatic is a dictionary entry.“ an. Um das Wort _wordomatic_ in der Übersetzung beizubehalten, senden Sie die folgende Anforderung:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Es wird folgendes Ergebnis ausgegeben:

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Diese Funktion lässt sich gleichermaßen mit `textType=text` und ohne `textType=html` ausführen. Sie sollte in Maßen eingesetzt werden. Für die Anpassung einer Übersetzung ist Custom Translator deutlich besser geeignet. Custom Translator nutzt den Kontext und statistische Wahrscheinlichkeiten in vollem Umfang. Sie erhalten viel bessere Ergebnisse, wenn Sie über Trainingsdaten verfügen oder sich die Erstellung von Trainingsdaten leisten können, die den Kontext des Worts oder des Ausdrucks zeigen. [Hier finden Sie weitere Informationen zu Custom Translator](../customization.md).