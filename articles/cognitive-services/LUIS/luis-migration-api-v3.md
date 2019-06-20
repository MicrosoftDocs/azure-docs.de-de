---
title: Migration von API V2 zu API V3
titleSuffix: Azure Cognitive Services
description: Die Endpunkte der API-Version 3 wurden angepasst. In dieser Anleitung erfahren Sie, wie Sie zu Endpunkten der API-Version 3 migrieren.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/22/2019
ms.author: diberry
ms.openlocfilehash: b7b4e25c78ef08bdf9a7c2f3faf96725fc5f5fc8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66123884"
---
# <a name="preview-migrate-to-api-version-3x--for-luis-apps"></a>Vorschau: Migrieren zu API-Version 3.x für LUIS-Apps

Die Endpunkt-APIs für Abfragevorhersagen wurden angepasst. In dieser Anleitung erfahren Sie, wie Sie zur Endpunkt-API-Version 3 migrieren. 

Die API V3 stellt folgende neue Features bereit, die weitreichende Änderungen für JSON-Anforderungen und/oder -Antworten beinhalten: 

* [Externe Entitäten](#external-entities-passed-in-at-prediction-time)
* [Dynamische Listen](#dynamic-lists-passed-in-at-prediction-time)
* [JSON-Anpassungen für vordefinierte Entitäten](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

An [Anforderungen](#request-changes) und [Antworten](#response-changes) im Kontext des Endpunkts für Abfragevorhersagen wurden zahlreiche Änderungen vorgenommen, um die oben genannten neuen Features zu unterstützen. Diese Änderungen umfassen u. a. Folgendes:

* [Anpassungen des Antwortobjekts](#top-level-json-changes)
* [Verwendung von Entitätsrollennamen als Verweise anstelle von Entitätsnamen](#entity-role-name-instead-of-entity-name)
* [Verwendung von Eigenschaften zur Kennzeichnung von Entitäten in Äußerungen](#marking-placement-of-entities-in-utterances)

Die folgenden LUIS-Features werden in der API V3 **nicht unterstützt**:

* Bing-Rechtschreibprüfung V7

Für V3 ist eine [Referenzdokumentation](https://aka.ms/luis-api-v3) verfügbar.

## <a name="prebuilt-entities-with-new-json"></a>Vordefinierte Entitäten mit neuem JSON-Code

In V3 wurden die Antwortobjekte u. a. so angepasst, dass diese nun [vordefinierte Entitäten](luis-reference-prebuilt-entities.md) enthalten. 

## <a name="request-changes"></a>Anforderungsänderungen 

### <a name="query-string-parameters"></a>Abfragezeichenfolge-Parameter

In der API V3 stehen andere Abfragezeichenfolgen-Parameter zur Verfügung.

|Parametername|Type|Version|Zweck|
|--|--|--|--|
|`query`|Zeichenfolge|Nur V3|**In V2** enthält der `q`-Parameter die vorherzusagende Äußerung. <br><br>**In V3** wird der `query`-Parameter verwendet, um anzugeben, dass dieses Feature verwendet werden soll.|
|`show-all-intents`|boolean|Nur V3|Alle Absichten mit der entsprechenden Bewertung werden innerhalb des **prediction.intents**-Objekts zurückgegeben. Absichten werden als Objekte in einem übergeordneten `intents`-Objekt zurückgegeben. `prediction.intents.give` ermöglicht den programmgesteuerten Zugriff, ohne die Absicht im Array suchen zu müssen. In V2 werden diese Absichten in einem Array zurückgegeben. |
|`verbose`|boolean|V2 und V3|Wenn **in V2** TRUE festgelegt wird, werden alle vorhergesagten Absichten zurückgegeben. Wenn Sie alle vorhergesagten Absichten abrufen müssen, verwenden Sie den V3-Parameter von `show-all-intents`.<br><br>**In V3** stellt dieser Parameter nur Details zu Entitätsmetadaten einer Entitätsvorhersage bereit.  |

<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>JSON-Text der Abfragevorhersage für `POST`-Anforderung

```JSON
{
    "query":"your utterance here",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

## <a name="response-changes"></a>Änderungen an Antworten

Der JSON-Code für Abfrageantworten wurde geändert, um den programmgesteuerten Zugriff auf die am häufigsten verwendeten Daten zu vereinfachen. 

### <a name="top-level-json-changes"></a>JSON-Änderungen auf oberster Ebene

Wenn `verbose` auf TRUE festgelegt wird, werden alle Absichten und die zugehörigen Bewertungen in der `intents`-Eigenschaft zurückgegeben. Folgende JSON-Eigenschaften der obersten Ebene werden für V2 verwendet:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

Folgende JSON-Eigenschaften der obersten Ebene werden für V3 verwendet:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

Das `intents`-Objekt ist eine ungeordnete Liste. Beachten Sie, dass das erste untergeordnete Element in `intents` nicht zwangsläufig `topIntent` entspricht. Verwenden Sie daher den `topIntent`-Wert, um die Bewertung zu ermitteln:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Die Änderungen am JSON-Antwortschema ermöglichen Folgendes:

* Klare Trennung zwischen der ursprünglichen Äußerung (`query`) und der zurückgegebenen Vorhersage (`prediction`).
* Vereinfachter programmgesteuerter Zugriff auf vorhergesagte Daten. Sie müssen nun nicht mehr wie in V2 das Array durchsuchen, sondern können mit **named** auf Werte für Absichten und Entitäten zugreifen. Für vorhergesagte Entitätsrollen wird der Rollenname zurückgegeben, da dieser überall in der App eindeutig ist.
* Wenn Datentypen ermittelt werden, werden diese auch berücksichtigt. Numerische Typen werden nicht mehr als Zeichenfolgen zurückgegeben.
* Unterscheidung zwischen Vorhersageinformationen der ersten Prioritätsstufe und zusätzlichen Metadaten, die innerhalb des `$instance`-Objekts zurückgegeben werden. 

### <a name="access-instance-for-entity-metadata"></a>Zugreifen auf `$instance` zum Abrufen von Entitätsmetadaten

Wenn Sie Entitätsmetadaten abrufen müssen, muss für die Abfragezeichenfolge das `verbose=true`-Flag verwendet werden. Dadurch werden die Metadaten in das `$instance`-Objekt der Antwort integriert. Beispiele finden Sie in den JSON-Antworten in den folgenden Abschnitten.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>Jede vorhergesagte Entität wird als Array dargestellt

Das `prediction.entities.<entity-name>`-Objekt enthält ein Array, da jede Entität mehrfach in der Äußerung vorhergesagt werden kann. 

### <a name="list-entity-prediction-changes"></a>Änderungen an Vorhersagen von Listenentitäten

Der JSON-Code für Vorhersagen von Listenentitäten wurde so geändert, dass nun ein zweidimensionales Array verwendet wird:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Jedes innere Array entspricht Text in der Äußerung. Das innere Objekt ist ein Array, da der gleiche Text in mehr als einer Unterliste der Listenentität auftreten kann. 

Bei der Zuordnung zwischen dem `entities`- und dem `$instance`-Objekt wird die Reihenfolge der Objekte für die Vorhersagen der Listenentitäten beibehalten.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>Verwendung des Entitätsrollennamens anstelle des Entitätsnamens 

In V2 werden innerhalb des `entities`-Arrays alle vorhergesagten Entitäten zurückgegeben, wobei als eindeutiger Bezeichner der Entitätsname verwendet wird. In V3 ist die primäre ID der Rollenname, wenn für die Entität Rollen verwendet werden und die Vorhersage sich auf eine Entitätsrolle bezieht. Dies ist möglich, da Entitätsrollennamen überall in der App und auch gegenüber anderen Namen von Modellen und deren Absichten sowie Entitäten eindeutig sein müssen.

Im folgenden Beispiel sehen Sie eine Äußerung mit dem Text `Yellow Bird Lane`. Dieser wird als benutzerdefinierte `Location`-Entität mit der Rolle `Destination` vorhergesagt.

|Äußerungstext|Name der Entität|Rollenname|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

In V2 wird die Entität durch den _Entitätsnamen_ identifiziert, wobei die Rolle eine Eigenschaft des Objekts ist:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

In V3 wird mit der _Entitätsrolle_ auf die Entität verwiesen, wenn sich die Vorhersage auf die Rolle bezieht:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

In V3 wird das gleiche Ergebnis mit dem `verbose`-Flag zur Rückgabe von Entitätsmetadaten erzielt:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

## <a name="external-entities-passed-in-at-prediction-time"></a>Externe Entitäten, die zum Zeitpunkt der Vorhersage übergeben werden

Mit externen Entitäten kann Ihre LUIS-App zur Laufzeit Entitäten identifizieren und bezeichnen. Dieses Verhalten kann als Feature für andere vorhandene Entitäten verwendet werden. Dadurch können Sie eigene separate und benutzerdefinierte Entitätsextraktionen verwenden, bevor Abfragen an den Vorhersageendpunkt gesendet werden. Da dies am Endpunkt der Abfragevorhersage geschieht, müssen Sie Ihr Modell nicht erneut trainieren und veröffentlichen.

Die Clientanwendung stellt eine eigene Entitätsextraktion bereit, indem sie die Ermittlung von Entitätsübereinstimmungen verwaltet und innerhalb der Äußerung die Position der gefundenen Entität bestimmt. Anschließend übergibt sie diese Informationen der Anforderung, die gesendet wird. 

Externe Entitäten werden zur Erweiterung von Entitätstypen genutzt. Gleichzeitig werden sie weiterhin als Signale für andere Modelle wie Rollen oder Verbundentitäten verwendet.

Dies ist nützlich für eine Entität, der nur zur Laufzeit der Abfragevorhersage Daten zur Verfügung stehen. Beispiele für diese Art von Daten sind benutzerspezifische oder sich ständig verändernde Daten. Sie können eine LUIS-Kontaktentität um externe Informationen aus der Kontaktliste eines Benutzers erweitern. 

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

In dieser Äußerung wird mit `him` auf `Hazem` verwiesen. Der Chatbot kann im POST-Anforderungstext `him` dem Entitätswert `Hazem` zuordnen, der aus der ersten Äußerung extrahiert wurde.

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

#### <a name="resolution"></a>Lösung

Die _optionale_ `resolution`-Eigenschaft wird in der Vorhersageantwort zurückgegeben. Dadurch können Sie Metadaten für die externe Entität übergeben und diese Daten dann wieder aus der Antwort abrufen. 

Diese Vorgehensweise dient vor allem der Erweiterung vordefinierter Entitäten, kann aber auch auf andere Entitätstypen angewendet werden. 

Die `resolution`-Eigenschaft kann eine Zahl, eine Zeichenfolge, ein Objekt oder ein Array sein:

* "Dallas"
* {"text": "value"}
* 12345 
* ["a", "b", "c"]


## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Dynamische Listen, die zum Zeitpunkt der Vorhersage übergeben werden

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
            "listEntityName":"ProductList",
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

## <a name="timezoneoffset-renamed-to-datetimereference"></a>„timeZoneOffset“ wurde in „datetimeReference“ umbenannt

**In V2** wird der `timezoneOffset`-[Parameter](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) innerhalb der Vorhersageanforderung als Abfragezeichenfolgen-Parameter gesendet. Ob für die Anforderung GET oder POST verwendet wird, spielt dabei keine Rolle. 

**In V3** wird dieselbe Funktion mit dem POST-Anforderungstextparameter `datetimeReference` bereitgestellt. 

## <a name="marking-placement-of-entities-in-utterances"></a>Kennzeichnen der Position von Entitäten in Äußerungen

**In V2** wird eine Entität in einer Äußerung mit `startIndex` und `endIndex` gekennzeichnet. 

**In V3** wird die Entität mit `startIndex` und `entityLength` gekennzeichnet.

## <a name="deprecation"></a>Eingestellte Unterstützung 

Die V2-API wird mindestens neun Monate nach der V3-Vorschau nicht eingestellt. 

## <a name="next-steps"></a>Nächste Schritte

Aktualisieren Sie mithilfe der Dokumentation zur API V3 vorhandene REST-Aufrufe der [LUIS-Endpunkt-APIs](https://aka.ms/luis-api-v3). 