---
title: Vordefinierte DatetimeV2-Entitäten
titleSuffix: Azure
description: In diesem Artikel erhalten Sie Informationen zur vordefinierten Entität „datetimeV2“ in Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: e7577dcf4859b1192121fe0406d0efb63a9f5990
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148635"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>Vordefinierte DatetimeV2-Entität für eine LUIS-App

Die vordefinierte Entität **datetimeV2** extrahiert Datums- und Uhrzeitwerte. Diese Werte werden in ein standardisiertes Format aufgelöst, um in Clientprogrammen verwendet werden zu können. Wenn eine Äußerung ein unvollständiges Datum oder eine unvollständige Uhrzeit enthält, fügt LUIS _vergangene und zukünftige Werte_ in die Endpunktantwort ein. Da diese Entität bereits trainiert wurde, müssen Sie den Anwendungsabsichten keine Beispieläußerungen mit datetimeV2 hinzufügen. 

## <a name="types-of-datetimev2"></a>Typen von datetimeV2
DatetimeV2 wird über das GitHub-Repository [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) verwaltet.

## <a name="example-json"></a>JSON-Beispiel 
Die folgende JSON-Beispielantwort enthält eine `datetimeV2`-Entität mit einem `datetime`-Untertyp. Beispiele für andere Typen von datetimeV2-Entitäten finden Sie unter [Untertypen von datetimeV2](#subtypes-of-datetimev2)</a>.

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
  ```

## <a name="json-property-descriptions"></a>Beschreibungen der JSON-Eigenschaften

|Eigenschaftenname |Eigenschaftentyp und Beschreibung|
|---|---|
|Entität|**string**: Text, der aus der Äußerung mit Datumstyp, Uhrzeit, Datumsbereich oder Zeitraum extrahiert wurde.|
|type|**string**: Einer der [Untertypen von datetimeV2](#subtypes-of-datetimev2)
|startIndex|**int**: Der Index in der Äußerung, an dem die Entität beginnt.|
|endIndex|**int**: Der Index in der Äußerung, an dem die Entität endet.|
|resolution|Enthält ein `values`-Array mit einem, zwei oder vier [Werten von „resolution“](#values-of-resolution).|
|end|Der Endwert einer Uhrzeit oder eines Datumsbereichs, der das gleiche Format wie `value` aufweist. Wird nur verwendet, wenn `type` `daterange`, `timerange` oder `datetimerange` ist.|

## <a name="subtypes-of-datetimev2"></a>Untertypen von datetimeV2

Die vordefinierte **datetimeV2**-Entität enthält folgende Untertypen. In der folgenden Tabelle werden Beispiele für diese bereitgestellt:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

## <a name="values-of-resolution"></a>Werte von „resolution“
* Das Array enthält ein Element, wenn das Datum oder die Uhrzeit in der Äußerung vollständig und eindeutig ist.
* Das Array enthält zwei Elemente, wenn der datetimeV2-Wert mehrdeutig ist. Als Mehrdeutigkeit gilt z.B. das Fehlen der Jahresangabe, der Uhrzeit oder des Zeitraums. Beispiele finden Sie unter [Mehrdeutige Datumsangaben](#ambiguous-dates). Wenn nicht eindeutig ist, ob die Zeitangabe für den Vormittag oder Nachmittag (a.m. bzw. p.m.) gilt, werden beide Werte eingefügt.
* Das Array enthält vier Elemente, wenn die Äußerung zwei mehrdeutige Elemente enthält. Diese Mehrdeutigkeit umfasst Elemente, die Folgendes enthalten:
  * Ein Datum oder einen Datumsbereich, bei dem das Jahr nicht eindeutig ist
  * Eine Uhrzeit oder einen Zeitraum, bei dem nicht eindeutig ist, ob diese für den Vormittag oder den Nachmittag gilt (a.m. oder p.m.). Beispiel: 3. April 3:00

Jedes Element des `values`-Arrays kann folgende Felder enthalten: 

|Eigenschaftenname|Eigenschaftenbeschreibung|
|--|--|
|timex|Uhrzeiten, Datumsangaben oder Datumsbereiche, die im TIMEX-Format angegeben sind, das dem [ISO-Standard 8601](https://en.wikipedia.org/wiki/ISO_8601) folgt, und die TIMEX3-Attribute für TimeML-Anmerkungen. Diese Anmerkungen werden in den [TIMEX-Richtlinien](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf) beschrieben.|
|type|Der Untertyp; kann eines der folgenden Elemente sein: `datetime`, `date`, `time`, `daterange`, `timerange`, `datetimerange`, `duration`, `set`.|
|value|**Optional.** Ein datetime-Objekt im Format JJJJ-MM-TT (date), hh:mm:ss (time), JJJJ-MM-TT hh:mm:ss (datetime). Wenn `type` `duration` ist, entspricht der Wert der Anzahl der Sekunden (duration). <br/> Dies wird nur verwendet, wenn `type` `datetime`, `date`, `time` oder duration ist.|

## <a name="valid-date-values"></a>Gültige Datumswerte

**datetimeV2** unterstützt Datumsangaben in folgenden Bereichen:

| Min | max |
|----------|-------------|
| 1\. Januar 1900   | 31\. Dezember 2099 |

## <a name="ambiguous-dates"></a>Mehrdeutige Datumsangaben

Wenn das Datum in der Vergangenheit oder Zukunft liegen kann, stellt LUIS beide Werte bereit. Ein Beispiel hierfür ist eine Äußerung, die den Monat und das Datum ohne das Jahr enthält.  

Betrachten Sie das Beispiel „May 2nd“:
* Wenn das heutige Datum der 3. Mai 2017 ist, stellt LUIS die Werte „2017-05-02“ and „2018-05-02“ bereit. 
* Wenn das heutige Datum der 1. Mai 2017 ist, stellt LUIS die Werte „2016-05-02“ and „2017-05-02“ bereit.

Im folgenden Beispiel wird die Auflösung der Entität „may 2nd“ veranschaulicht. Bei der Auflösung wird davon ausgegangen, dass das heutige Datum ein Datum zwischen dem 2. Mai 2017 und dem 1. Mai 2018 ist.
Die `X`-Zeichen im `timex`-Feld stellen die Teile des Datums dar, die in der Äußerung nicht explizit angegeben wurden.

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-numeric-date"></a>Beispiel zur Auflösung des Datumsbereichs für ein numerisches Datum

Die `datetimeV2`-Entität extrahiert Datumsbereiche und Zeiträume. Die Felder `start` und `end` geben den Anfang und das Ende des Bereichs an. Für die Äußerung „May 2nd to May 5th“ stellt LUIS **daterange**-Werte für das aktuelle und das nächste Jahr bereit. Der `XXXX`-Wert im `timex`-Feld gibt die Mehrdeutigkeit des Jahrs an. `P3D` gibt an, dass der Zeitraum drei Tage beträgt.

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-day-of-week"></a>Beispiel zur Auflösung des Datumsbereichs für den Wochentag

Im folgenden Beispiel wird veranschaulicht, wie LUIS **datetimeV2** verwendet, um die Äußerung „Tuesday to Thursday“ (Dienstag bis Donnerstag) aufzulösen. Im folgenden Beispiel ist der 19. Juni das aktuelles Datum. LUIS fügt **daterange**-Werte für die Datumsbereiche ein, die vor und nach dem aktuellen Datum liegen.

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
## <a name="ambiguous-time"></a>Mehrdeutige Zeitangaben
Das Array des Werts enthält zwei time-Elemente, wenn die Uhrzeit oder der Zeitraum mehrdeutig ist. Wenn die Uhrzeit nicht eindeutig ist, werden die Werte für den Vormittag und Nachmittag bereitgestellt.

## <a name="time-range-resolution-example"></a>Beispiel zur Auflösung des Zeitraums

Im folgenden Beispiel wird veranschaulicht, wie LUIS **datetimeV2** verwendet, um die Äußerung mit dem Zeitraum aufzulösen.

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

## <a name="preview-api-version-3x"></a>Vorschau-API-Version 3.x

Die JSON-Antwort für DatetimeV2 wurde in der API-Version 3 geändert. 

Änderungen von API-Version 2:
* Die `datetimeV2.timex.type`-Eigenschaft wird nicht mehr zurückgegeben, da sie mit der `datetimev2.type`-Eigenschaft auf der übergeordneten Ebene zurückgegeben wird. 
* Die `datetimeV2.timex`-Eigenschaft wurde in `datetimeV2.value` umbenannt.

Für die Äußerung `8am on may 2nd 2017` lautet die Version 3 von DatetimeV2 wie folgt:

```JSON
{
    "query": "8am on may 2nd 2017",
    "prediction": {
        "normalizedQuery": "8am on may 2nd 2017",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6826963
            }
        },
        "entities": {
            "datetimeV2": [
                {
                    "type": "datetime",
                    "values": [
                        {
                            "timex": "2017-05-02T08",
                            "value": "2017-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    }
}
```

Beim folgenden JSON-Code wurde der `verbose`-Parameter auf `false` festgelegt:

```json
{
    "query": "8am on may 2nd 2017",
    "prediction": {
        "normalizedQuery": "8am on may 2nd 2017",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6826963
            }
        },
        "entities": {
            "datetimeV2": [
                {
                    "type": "datetime",
                    "values": [
                        {
                            "timex": "2017-05-02T08",
                            "value": "2017-05-02 08:00:00"
                        }
                    ]
                }
            ],
            "$instance": {
                "datetimeV2": [
                    {
                        "type": "builtin.datetimeV2.datetime",
                        "text": "8am on may 2nd 2017",
                        "startIndex": 0,
                        "length": 19,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

## <a name="deprecated-prebuilt-datetime"></a>Die veraltete vordefinierte datetime-Entität

Die vordefinierte `datetime`-Entität ist veraltet und wurde durch **datetimeV2** ersetzt. 

Führen Sie folgende Schritte durch, um `datetime` in Ihrer LUIS-App durch `datetimeV2` zu ersetzen:

1. Öffnen Sie den Bereich **Entitäten** auf der LUIS-Weboberfläche. 
2. Löschen Sie die vordefinierte **datetime**-Entität.
3. Klicken Sie auf **Add prebuilt entity** (Vordefinierte Entität hinzufügen).
4. Wählen Sie **datetimeV2** aus, und klicken Sie auf **Speichern**.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Entitäten [dimension](luis-reference-prebuilt-dimension.md), [email](luis-reference-prebuilt-email.md) und [number](luis-reference-prebuilt-number.md). 

