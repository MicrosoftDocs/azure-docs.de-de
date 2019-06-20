---
title: Verwenden der Languages-Methode der Sprachübersetzungs-API
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die Languages-Methode der Sprachübersetzungs-API verwenden.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: v-jansko
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7498ba08b9ce7b6aae10f38a393eb8cba37f3f4e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60827900"
---
# <a name="translator-speech-api-languages"></a>Sprachübersetzungs-API: Languages

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Die Sprachübersetzung erweitert ständig die Liste der von den Diensten unterstützten Sprachen. Verwenden Sie diese API, um die Sprachen zu ermitteln, die zur Verwendung mit dem Sprachübersetzungsdienst verfügbar sind.

Codebeispiele, die die Verwendung der API zum Abrufen der verfügbaren Sprachen veranschaulichen, finden Sie auf der [GitHub-Website zu Microsoft Translator](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Hinweise zur Implementierung

### <a name="get-languages"></a>GET /languages

Eine umfangreiche Auswahl an Sprachen ist zum Transkribieren von Sprache, Übersetzen der transkribierten Sprache und Erzeugen synthetisierter Sprache der Übersetzung verfügbar.

Ein Client verwendet den Abfrageparameter `scope`, um zu definieren, an welchen Sprachen er interessiert ist.

* **Spracherkennung:** Verwenden Sie den Abfrageparameter `scope=speech`, um die Sprachen abzurufen, die zum Transkribieren gesprochener Sprache verfügbar sind.
* **Textübersetzung:** Verwenden Sie den Abfrageparameter `scope=text`, um die Sprachen abzurufen, die zum Übersetzen von transkribierten Texten verfügbar sind.
* **Sprachsynthese:**  Verwenden Sie den Abfrageparameter `scope=tts`, um die Sprachen und Stimmen abzurufen, die für die Sprachsynthese von übersetzten Texten in Sprache verfügbar sind.

Ein Client kann mehrere Gruppen gleichzeitig abrufen, indem er eine durch Trennzeichen getrennte Liste der Optionen angibt. Beispiel: `scope=speech,text,tts`.

Eine erfolgreiche Antwort ist ein JSON-Objekt mit einer Eigenschaft für jede angeforderte Gruppe.

```
{
    "speech": {
        //... Set of languages supported for speech-to-text
    },
    "text": {
        //... Set of languages supported for text translation
    },
    "tts": {
        //... Set of languages supported for text-to-speech
    }
}
```

Da ein Client den Abfrageparameter `scope` verwenden kann, um auszuwählen, welche der unterstützten Sprachen zurückgegeben werden sollen, kann eine tatsächliche Antwort auch nur eine Teilmenge der oben gezeigten Eigenschaften enthalten.

Der Wert, der mit jeder Eigenschaft bereitgestellt wird, lautet wie folgt.

### <a name="speech-to-text-speech"></a>Spracherkennung (Sprache)

Der Wert, der der Spracherkennungseigenschaft (`speech`) zugeordnet ist, entspricht einem Wörterbuch von Paaren (Schlüssel und Wert). Jeder Schlüssel bestimmt eine Sprache, die für die Spracherkennung unterstützt wird. Der Schlüssel ist der Bezeichner, den der Client an die API übergibt. Der Wert, der dem Schlüssel zugeordnet ist, ist ein Objekt mit den folgenden Eigenschaften:

* `name`: Anzeigename der Sprache.
* `language`: Sprachtag der zugeordneten geschriebenen Sprache. Informationen finden Sie unten unter „Texttransaktion“.
Im folgenden Code wird ein Beispiel veranschaulicht:

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Textübersetzung (Text)

Der Wert, der der `text`-Eigenschaft zugeordnet ist, ist ebenfalls ein Wörterbuch, bei dem jeder Schlüssel eine für die Textübersetzung unterstützte Sprache bestimmt. Der Wert, der dem Schlüssel zugeordnet ist, beschreibt die Sprache:

* `name`: Anzeigename der Sprache.
* `dir`: Die Direktionalität, also `rtl` für Sprachen, die von rechts nach links gelesen werden, bzw. `ltr` für Sprachen, die von links nach rechts gelesen werden.

Im folgenden Code wird ein Beispiel veranschaulicht:

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Sprachsynthese (tts)

Der Wert, der der tts-Eigenschaft zugeordnet ist, ist ebenfalls ein Wörterbuch, bei dem jeder Schlüssel eine unterstützte Stimme identifiziert. Die Attribute eines Stimmenobjekts sind:

* `displayName`: Der Anzeigename für die Stimme.
* `gender`: Geschlecht der Stimme (männlich oder weiblich).
* `locale`: Sprachtag der Stimme mit der primären Sprache und der Region als untergeordnete Tags.
* `language`: Sprachtag der zugeordneten geschriebenen Sprache.
* `languageName`: Anzeigename der Sprache.
* `regionName`: Anzeigename der Region für diese Sprache.

Im folgenden Code wird ein Beispiel veranschaulicht:

```
{
    "tts": {
        "en-US-Zira": {
            "displayName": "Zira",
            "gender": "female",
            "locale": "en-US",
            "languageName": "English",
            "regionName": "United States",
            "language": "en"
        }
}
```

### <a name="localization"></a>Lokalisierung
Für alle Sprachen, die in der Textübersetzung unterstützt werden, gibt der Dienst alle Namen in der Sprache des Headers „Accept-Language“ zurück.

### <a name="response-class-status-200"></a>Antwortklasse (Status 200)
Das Objekt, das die unterstützten Sprachen beschreibt.

ModelExample-Wert:

Languages {speech (object, optional), text (object, optional), tts (object, optional)}

### <a name="headers"></a>Header

|Header|BESCHREIBUNG|Type|
:--|:--|:--|
X-RequestId|Ein Wert, der vom Server generiert wird, um die Anforderung zu identifizieren und Probleme zu behandeln.|Zeichenfolge|

### <a name="parameters"></a>Parameter

|Parameter|BESCHREIBUNG|Parametertyp|Datentyp|
|:--|:--|:--|:--|
|api-version    |Die vom Client angeforderte Version der API. Zulässige Werte sind: `1.0`.|query|Zeichenfolge|
|scope  |Unterstützte Sprachen oder Stimmen, die an den Client zurückgegeben werden. Dieser Parameter wird als eine durch Trennzeichen getrennte Liste von Schlüsselwörtern angegeben. Die folgenden Schlüsselwörter sind verfügbar:<ul><li>`speech`: Gibt die zum Transkribieren der gesprochenen Sprache unterstützten Sprachen an.</li><li>`tts`: Gibt die für die Sprachsynthese unterstützten Stimmen an.</li><li>`text`: Gibt die zum Übersetzen des Texts unterstützten Sprachen an.</li></ul>Wenn kein Wert angegeben ist, wird der Wert `scope` standardmäßig auf `text` festgelegt.|query|Zeichenfolge|
|X-ClientTraceId    |Eine vom Client generierte GUID zur Ablaufverfolgung einer Anforderung. Zur Erleichterung der Problembehandlung sollten Clients mit jeder Anforderung einen neuen Wert angeben und protokollieren.|Header|Zeichenfolge|
|Accept-Language    |Einige der Felder in der Antwort sind Namen von Sprachen oder Regionen. Verwenden Sie diesen Parameter, um die Sprache zu definieren, in der die Namen zurückgegeben werden. Die Sprache wird durch die Bereitstellung eines wohlgeformten BCP 47-Sprachtags angegeben. Wählen Sie ein Tag aus der Liste der Sprachbezeichner aus, die mit dem `text`-Bereich zurückgegeben wurden. Bei nicht unterstützten Sprachen werden die Namen in Englisch angegeben.<br/>Verwenden Sie z.B. den Wert `fr` zum Anfordern von Namen in Französisch, oder verwenden Sie den Wert `zh-Hant` zum Anfordern von Namen in Chinesisch (traditionell).|Header|Zeichenfolge|

### <a name="response-messages"></a>Antwortnachrichten

|HTTP-Statuscode|`Reason`|
|:--|:--|
|400|Ungültige Anforderung. Überprüfen Sie die Eingabeparameter, und stellen Sie sicher, dass sie gültig sind. Das Antwortobjekt enthält eine ausführlichere Beschreibung des Fehlers.|
|429|Too many requests. (Zu viele Anforderungen.)|
|500|Ein Fehler ist aufgetreten. Wenn der Fehler weiterhin besteht, melden Sie ihn mit dem Ablaufverfolgungsbezeichner des Clients (X-ClientTraceId) oder dem Anforderungsbezeichner (X-RequestId).|
|503|Der Server ist vorübergehend nicht verfügbar. Versuchen Sie die Anforderung erneut. Wenn der Fehler weiterhin besteht, melden Sie ihn mit dem Ablaufverfolgungsbezeichner des Clients (X-ClientTraceId) oder dem Anforderungsbezeichner (X-RequestId).|
