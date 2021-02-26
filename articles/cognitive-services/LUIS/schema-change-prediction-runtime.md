---
title: 'Erweitern der App zur Laufzeit: LUIS'
description: Hier erfahren Sie, wie Sie einen bereits veröffentlichten Vorhersageendpunkt erweitern, um neue Informationen zu übergeben.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 1d94e9f59062e4d730b8f3b71022442e81e6eeda
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953020"
---
# <a name="extend-app-at-prediction-runtime"></a>Erweitern der App zur Laufzeit der Vorhersage

Das Schema der App (Modelle und Features) wird trainiert und zum Vorhersageendpunkt veröffentlicht. Dieses veröffentlichte Modell wird in der Laufzeit der Vorhersage verwendet. Sie können neue Informationen zusammen mit der Äußerung des Benutzers an die Laufzeit der Vorhersage übergeben, um die Vorhersage zu verbessern.

Es gibt zwei Änderungen am Schema der Laufzeit der Vorhersage:
* [Externe Entitäten](#external-entities)
* [Dynamische Listen](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>Externe Entitäten

Mit externen Entitäten kann Ihre LUIS-App zur Laufzeit Entitäten identifizieren und bezeichnen. Dieses Verhalten kann als Feature für andere vorhandene Entitäten verwendet werden. Dadurch können Sie eigene separate und benutzerdefinierte Entitätsextraktionen verwenden, bevor Abfragen an den Vorhersageendpunkt gesendet werden. Da dies am Endpunkt der Abfragevorhersage geschieht, müssen Sie Ihr Modell nicht erneut trainieren und veröffentlichen.

Die Clientanwendung stellt eine eigene Entitätsextraktion bereit, indem sie die Ermittlung von Entitätsübereinstimmungen verwaltet und innerhalb der Äußerung die Position der gefundenen Entität bestimmt. Anschließend übergibt sie diese Informationen der Anforderung, die gesendet wird.

Externe Entitäten werden zur Erweiterung von Entitätstypen genutzt. Gleichzeitig werden sie weiterhin als Signale für andere Modelle verwendet.

Dies ist nützlich für eine Entität, der nur zur Laufzeit der Abfragevorhersage Daten zur Verfügung stehen. Beispiele für diese Art von Daten sind benutzerspezifische oder sich ständig verändernde Daten. Sie können eine LUIS-Kontaktentität um externe Informationen aus der Kontaktliste eines Benutzers erweitern.

Externe Entitäten sind Teil von Version 3 der Erstellungs-API. Erfahren Sie mehr zum [Migrieren](luis-migration-api-v3.md) zu dieser Version.

### <a name="entity-already-exists-in-app"></a>Bereits in der App vorhandene Entität

Der `entityName`-Wert für die externe Entität, der mit dem POST-Anforderungstext an den Endpunkt übergeben wird, muss zum Zeitpunkt der Anforderung bereits in der trainierten und veröffentlichten App vorhanden sein. Der Entitätstyp spielt keine Rolle, da alle Typen unterstützt werden.

### <a name="first-turn-in-conversation"></a>Erster Gesprächsbeitrag in einer Unterhaltung

Sehen Sie sich die folgende unvollständige Äußerung an, die ein Benutzer zu Beginn einer Chatbotunterhaltung eingeben könnte:

`Send Hazem a new message`

In den Text der POST-Anforderung, die der Chatbot an LUIS sendet, können Informationen zu `Hazem` integriert werden. Dadurch wird der Name direkt als Kontakt des Benutzers identifiziert.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Die Vorhersageantwort enthält die externe Entität (und alle anderen vorhergesagten Entitäten), da diese in der Anforderung definiert ist.

### <a name="second-turn-in-conversation"></a>Zweiter Gesprächsbeitrag in einer Unterhaltung

Die nächste Äußerung des Benutzers in der Chatbotunterhaltung ist durch die Verwendung eines anderen Worts ungenauer:

`Send him a calendar reminder for the party.`

In dieser Wendung der Unterhaltung verwendet die Äußerung `him` als Verweis auf `Hazem`. Der Chatbot kann im POST-Anforderungstext `him` dem Entitätswert `Hazem` zuordnen, der aus der ersten Äußerung extrahiert wurde.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Die Vorhersageantwort enthält die externe Entität (und alle anderen vorhergesagten Entitäten), da diese in der Anforderung definiert ist.

### <a name="override-existing-model-predictions"></a>Außerkraftsetzen von vorhandenen Modellvorhersagen

Mit der `preferExternalEntities`-Optionseigenschaft wird Folgendes angegeben: Wenn der Benutzer eine externe Entität sendet, die sich mit einer vorhergesagten Entität mit dem gleichen Namen überschneidet, wird von LUIS die übergebene Entität oder die im Modell vorhandene Entität ausgewählt.

Sehen Sie sich dies beispielsweise für die Abfrage `today I'm free` an. LUIS erkennt `today` als datetimeV2-Element mit der folgenden Antwort:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Wenn der Benutzer die externe Entität sendet:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

Wenn `preferExternalEntities` auf `false` festgelegt ist, gibt LUIS eine Antwort zurück, die dem Fall entspricht, in dem die externe Entität nicht gesendet wurde.

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Wenn `preferExternalEntities` auf `true` festgelegt ist, gibt LUIS eine Antwort mit folgendem Inhalt zurück:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Lösung

Die _optionale_ `resolution`-Eigenschaft wird in der Vorhersageantwort zurückgegeben. Dadurch können Sie Metadaten für die externe Entität übergeben und diese Daten dann wieder aus der Antwort abrufen.

Diese Vorgehensweise dient vor allem der Erweiterung vordefinierter Entitäten, kann aber auch auf andere Entitätstypen angewendet werden.

Die `resolution`-Eigenschaft kann eine Zahl, eine Zeichenfolge, ein Objekt oder ein Array sein:

* "Dallas"
* {"text": "value"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>Dynamische Listen

Mit dynamischen Listen können Sie eine trainierte und veröffentlichte Listenentität erweitern, die bereits Teil der LUIS-App ist.

Verwenden Sie dieses Feature, wenn die Werte Ihrer Listenentität regelmäßig angepasst werden müssen. Sie können die bereits trainierte und veröffentlichte Listenentität unter folgenden Umständen erweitern:

* Die Erweiterung findet zum Zeitpunkt der Anforderung für den Endpunkt der Abfragevorhersage statt.
* Die Erweiterung betrifft eine einzelne Anforderung.

Die Listenentität in der LUIS-App kann leer sein, muss aber existieren. Diese Entität wird nicht geändert, aber die Vorhersagefunktion am Endpunkt wird so erweitert, dass zwei Listen mit ca. 1000 Elementen verwendet werden.

### <a name="dynamic-list-json-request-body"></a>JSON-Anforderungstext für dynamische Listen

Wenn Sie den folgenden JSON-Anforderungstext senden, wird der Liste eine neue Unterliste mit Synonymen hinzugefügt. Mit der `POST`-Anforderung für die Abfragevorhersage können Sie die Listenentität dann für den Text (`LUIS`) vorhersagen:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

Die Vorhersageantwort enthält die Listenentität (und alle anderen vorhergesagten Entitäten), da diese in der Anforderung definiert ist.

## <a name="next-steps"></a>Nächste Schritte

* [Vorhersageergebnis](luis-concept-prediction-score.md)
* [Änderungen in Version 3 der Erstellungs-API](luis-migration-api-v3.md)
