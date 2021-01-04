---
title: Die Qualifikation „Benutzerdefinierte Web-API“ in Skillsets
titleSuffix: Azure Cognitive Search
description: Erweitern Sie die Funktionen der Qualifikationsgruppen für die kognitive Azure-Suche durch Aufruf von Web-APIs. Verwenden Sie die Qualifikation „Benutzerdefinierte Web-API“ zum Integrieren Ihres benutzerdefinierten Codes.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: cb5ee7d3549e433fb184b8c55c28b9a28ed89272
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011930"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Qualifikation „Benutzerdefinierte Web-API“ in einer Anreicherungspipeline der kognitiven Azure-Suche

Mit der Qualifikation **Benutzerdefinierte Web-API** können Sie die KI-Anreicherung erweitern, indem Sie einen Web-API-Endpunkt aufrufen, der benutzerdefinierte Operationen bereitstellt. Ähnlich wie integrierte Qualifikationen hat die Qualifikation **Benutzerdefinierte Web-API** Eingaben und Ausgaben. Abhängig von den Eingaben erhält Ihre Web-API eine JSON-Nutzlast, wenn der Indexer ausgeführt wird, und gibt eine JSON-Nutzlast als Antwort sowie einen Statuscode für den Erfolg aus. Es wird erwartet, dass die Antwort die von Ihrer Qualifikation „Benutzerdefiniert“ spezifizierten Ergebnisse aufweist. Jede andere Antwort gilt als Fehler und es werden keine Anreicherung durchgeführt.

Die Struktur der JSON-Nutzlasten wird weiter unten in diesem Dokument beschrieben.

> [!NOTE]
> Der Indexer versucht zweimal, bestimmte Standard-HTTP-Statuscodes, die von der Web-API zurückgegeben werden, erneut zu ermitteln. Diese HTTP-Statuscodes lauten: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden.

| Parametername     | BESCHREIBUNG |
|--------------------|-------------|
| `uri` | Der URI der Web-API, an die die _JSON_-Nutzlast gesendet wird. Es ist nur ein **HTTPS**-URI-Schema zulässig. |
| `httpMethod` | Diese Methode wird zum Senden der Nutzlast verwendet: Zulässige Methoden sind `PUT` oder `POST`. |
| `httpHeaders` | Eine Sammlung von Schlüssel-Wert-Paaren, bei denen die Schlüssel Headernamen und -Werte Headerwerte darstellen, die zusammen mit der Nutzlast an Ihre Web-API gesendet werden. Die folgenden Header dürfen nicht in der Sammlung enthalten sein: `Accept`, `Accept-Charset`, `Accept-Encoding`, `Content-Length`, `Content-Type`, `Cookie`, `Host`, `TE`, `Upgrade`, `Via` |
| `timeout` | (Optional) Wenn angegeben, wird damit das Zeitlimit für den HTTP-Client angegeben, der den API-Aufruf durchführt. Es muss als XSD-Wert „dayTimeDuration“ formatiert sein (eine eingeschränkte Teilmenge eines [ISO 8601-Zeitwerts](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)). Zum Beispiel `PT60S` für 60 Sekunden. Wenn kein Wert festgelegt ist, wird ein Standardwert von 30 Sekunden ausgewählt. Das Zeitlimit kann auf maximal 230 Sekunden und mindestens 1 Sekunde festgelegt werden. |
| `batchSize` | (Optional) Gibt an, wie viele „Datensätze“ (siehe _JSON_ Nutzlaststruktur unten) pro API-Aufruf gesendet werden. Wenn kein Wert festgelegt ist, wird der Standardwert 1000 ausgewählt. Wir empfehlen Ihnen, diesen Parameter zu verwenden, um einen angemessenen Kompromiss zwischen Indexierungsdurchsatz und Auslastung Ihrer API zu erreichen. |
| `degreeOfParallelism` | (Optional) Wenn angegeben, wird die Anzahl der Aufrufe angezeigt, die der Indexer parallel zum von Ihnen bereitgestellten Endpunkt vornimmt. Sie können diesen Wert herab setzen, wenn der Endpunkt bei einer zu hohen Anforderungslast ausfällt, oder herauf setzen, wenn der Endpunkt mehr Anforderungen verarbeiten kann und Sie die Leistung des Indexers erhöhen möchten.  Wenn kein Wert festgelegt ist, wird ein Standardwert von 5 verwendet. Der `degreeOfParallelism` kann auf maximal 10 und mindestens 1 festgelegt werden. |

## <a name="skill-inputs"></a>Skilleingaben

Es gibt keine „vordefinierten“ Eingaben für diese Qualifikation. Sie können ein oder mehrere Felder auswählen, die zum Zeitpunkt der Ausführung dieser Qualifikation bereits verfügbar waren, und die an die Web-API gesendete _JSON_-Nutzlast wird unterschiedliche Felder aufweisen.

## <a name="skill-outputs"></a>Skillausgaben

Es gibt keine „vordefinierten“ Ausgaben für diese Qualifikation. Abhängig von der Antwort, die Ihre Web-API zurückgibt, fügen Sie Ausgabefelder hinzu, damit sie von der _JSON_-Antwort übernommen werden können.


## <a name="sample-definition"></a>Beispieldefinition

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can identify positions of different phrases in the source text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "phraseList",
            "source": "/document/keyphrases"
          }
        ],
        "outputs": [
          {
            "name": "hitPositions"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Beispiel für eine Eingabe-JSON-Struktur

Diese _JSON_-Struktur stellt die Nutzlast dar, die an Ihre Web-API gesendet wird.
Es gelten immer diese Einschränkungen:

* Die Entität der höchsten Ebene hat die Bezeichnung `values` und ist ein Array von Objekten. Die Anzahl der betroffenen Objekte entspricht höchstens der `batchSize`.
* Jedes Objekt im `values`-Array hat:
    * Eine `recordId`-Eigenschaft. Dies ist eine **eindeutige** Zeichenfolge, die zum Bestimmen dieses Datensatzes verwendet wird.
    * Eine `data`-Eigenschaft, die ein _JSON_-Objekt ist. Die Felder der `data`-Eigenschaft entsprechen den im Abschnitt `inputs` der Qualifikationsdefinition angegebenen „Namen“. Der Wert dieser Felder ergibt sich aus der `source` dieser Felder (die aus einem Feld im Dokument oder möglicherweise aus einer anderen Qualifikation stammen kann).

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "phraseList": ["Este", "Inglés"]
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "phraseList": ["Hi"]
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world, Hi world",
             "language": "en",
             "phraseList": ["world"]
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "phraseList": []
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Beispiel für eine Ausgabe-JSON-Struktur

Die „Ausgabe“ entspricht der Antwort, die von Ihrer Web-API zurückgegebenen wird. Die Web-API sollte nur eine _JSON_-Nutzlast zurückgeben (verifiziert durch Prüfung des `Content-Type`-Antwortheaders) und die folgenden Einschränkungen erfüllen:

* Es muss eine Entität der höchsten Ebene mit der Bezeichnung `values`, die ein Array von Objekten ist.
* Die Anzahl der Objekte im Array sollte gleich der Anzahl der an die Web-API gesendeten Objekte sein.
* Jedes Objekt benötigt:
   * Eine `recordId`-Eigenschaft
   * Eine `data`-Eigenschaft, die ein Objekt ist, bei dem die Felder Anreicherungen sind, die den „Namen“ in der `output` entsprechen und deren Wert als Anreicherung betrachtet wird.
   * Eine `errors`-Eigenschaft, ein Array, das alle aufgetretenen Fehler auflistet, die dem Ausführungsverlauf des Indexers hinzugefügt werden. Diese Eigenschaft ist erforderlich, kann jedoch ein `null`-Wert sein.
   * Eine `warnings`-Eigenschaft, ein Array, das alle aufgetretenen Warnungen auflistet, die dem Ausführungsverlauf des Indexers hinzugefügt werden. Diese Eigenschaft ist erforderlich, kann jedoch ein `null`-Wert sein.
* Die Objekte im `values`-Array müssen nicht in der gleichen Reihenfolge sein wie die Objekte im `values`-Array, die als Anforderung an die Web-API gesendet werden. Die `recordId` wird jedoch für die Korrelation verwendet, sodass jeder Datensatz in der Antwort, der eine `recordId` enthält, die nicht Teil der ursprünglichen Anforderung an die Web-API war, verworfen wird.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "'phraseList' should not be null or empty"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "hitPositions": [6, 16]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "hitPositions": [0, 23]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "hitPositions": []
            },
            "errors": null,
            "warnings": {
                "message": "No occurrences of 'Hi' were found in the input text"
            }
        },
    ]
}

```

## <a name="error-cases"></a>Auftretende Fehler
Neben der Tatsache, dass Ihre Web-API nicht verfügbar ist oder nicht erfolgreiche Statuscodes versendet werden, gelten die folgenden Fälle als fehlerhafte Fälle:

* Wenn die Web-API einen Statuscode für den Erfolg zurückgibt, die Antwort aber anzeigt, dass er nicht `application/json` ist, dann wird die Antwort als ungültig betrachtet und es werden keine Anreicherungen durchgeführt.
* Wenn es **ungültige** (mit einer `recordId`, die nicht in der ursprünglichen Anforderung enthalten war, oder mit doppelten Werten) Datensätze im Antwort-Array `values` gibt, wird keine Anreicherung für **diese** Datensätze durchgeführt.

In Fällen, in denen die Web-API nicht verfügbar ist oder einen HTTP-Fehler zurückgibt, wird ein eine Fehlermeldung mit allen verfügbaren Details über den HTTP-Fehler in den Ausführungsverlauf des Indexers aufgenommen.

## <a name="see-also"></a>Weitere Informationen

+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Hinzufügen einer benutzerdefinierten Qualifikation zu einer KI-Anreicherungspipeline](cognitive-search-custom-skill-interface.md)
+ [Beispiel: Erstellen eines benutzerdefinierten Skills für die KI-Anreicherung](cognitive-search-create-custom-skill-example.md)
