---
title: Änderungen an Vorhersageendpunkten in der V3-API
description: Die Abfragevorhersage-Endpunkt-APIs wurden in V3 geändert. In dieser Anleitung erfahren Sie, wie Sie zur Endpunkt-API-Version 3 migrieren.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 59cf250a9db5a1f6759495c1b5a3c48cb07cde15
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018785"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Änderungen an Vorhersageendpunkten in V3

Die Abfragevorhersage-Endpunkt-APIs wurden in V3 geändert. In dieser Anleitung erfahren Sie, wie Sie zur Endpunkt-API-Version 3 migrieren.

**Status der allgemeinen Verfügbarkeit**: Diese V3-APIs enthalten erhebliche Änderungen an JSON-Anforderungen und -Antworten gegenüber der V2-API.

Die V3-API bietet die folgenden neuen Features:

* [Externe Entitäten](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [Dynamische Listen](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [JSON-Anpassungen für vordefinierte Entitäten](#prebuilt-entity-changes)

An [Anforderungen](#request-changes) und [Antworten](#response-changes) von Vorhersageendpunkten wurden erhebliche Änderungen zur Unterstützung der oben aufgelisteten neuen Features vorgenommen, darunter die folgenden:

* [Anpassungen des Antwortobjekts](#top-level-json-changes)
* [Verwendung von Entitätsrollennamen als Verweise anstelle von Entitätsnamen](#entity-role-name-instead-of-entity-name)
* [Verwendung von Eigenschaften zur Kennzeichnung von Entitäten in Äußerungen](#marking-placement-of-entities-in-utterances)

Für V3 ist eine [Referenzdokumentation](https://aka.ms/luis-api-v3) verfügbar.

## <a name="v3-changes-from-preview-to-ga"></a>V3-Änderungen von der Vorschauversion bis zur allgemeinen Verfügbarkeit

An V3 wurden im Rahmen des Übergangs zur allgemeinen Verfügbarkeit die folgenden Änderungen vorgenommen:

* Die folgenden vordefinierten Entitäten weisen abweichende JSON-Antworten auf:
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * Name des Schlüssels für messbare Einheiten von `units` zu `unit`

* Änderung am JSON-Anforderungstext:
    * von `preferExternalEntities` zu `preferExternalEntities`
    * Optionaler Parameter `score` für externe Entitäten

* Änderungen am JSON-Antworttext:
    * `normalizedQuery` wurde entfernt

## <a name="suggested-adoption-strategy"></a>Vorschlag für eine Einführungsstrategie

Wenn Sie Bot Framework oder Bing-Rechtschreibprüfung V7 verwenden oder nur Ihre LUIS-App-Erstellung migrieren möchten, verwenden Sie weiterhin den V2-Endpunkt.

Wenn Sie wissen, dass keine Ihrer Clientanwendungen oder -integrationen (Bot Framework und Bing-Rechtschreibprüfung V7) betroffen sind und Sie mit der gleichzeitigen Migration Ihrer LUIS-App-Erstellung und des Vorhersageendpunkts einverstanden sind, beginnen Sie mit der Verwendung des V3-Vorhersageendpunkts. Der V2-Vorhersageendpunkt steht weiterhin zur Verfügung und ist eine gute Rückfallstrategie.


## <a name="not-supported"></a>Nicht unterstützt

### <a name="bing-spell-check"></a>Bing-Rechtschreibprüfung

Diese API wird auf V3-Vorhersageendpunkten nicht unterstützt. Verwenden Sie für Rechtschreibkorrekturen weiterhin V2-API-Vorhersageendpunkte. Wenn Sie die V3-API verwenden und eine Rechtschreibkorrektur benötigen, lassen Sie die Clientanwendung die API [Bing-Rechtschreibprüfung](../bing-spell-check/overview.md) aufrufen, und korrigieren Sie den Text vor der Übermittlung an die LUIS-API.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Bot Framework- und Azure Bot Service-Clientanwendungen

Verwenden Sie weiterhin den Vorhersageendpunkt der V2-API, bis V4.7 von Bot Framework veröffentlicht wird.

## <a name="v2-api-deprecation"></a>Eingestellte Unterstützung der V2-API

Die V2-Vorhersage-API wird für mindestens neun Monate nach der V3-Vorschau am 8. Juni 2020 nicht eingestellt.

## <a name="endpoint-url-changes"></a>Änderungen der Endpunkt-URL

### <a name="changes-by-slot-name-and-version-name"></a>Änderungen nach Slotname und Versionsname

Das [Format des HTTP-Endpunktaufrufs für Version 3](developer-reference-resource.md#rest-endpoints) hat sich geändert.

Falls Sie nach Version abfragen möchten, müssen Sie zuerst die [Veröffentlichung per API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) mit `"directVersionPublish":true` durchführen. Fragen Sie den Endpunkt ab, und verweisen Sie dabei nicht auf die Versions-ID, sondern auf den Slotnamen.

|Gültige Werte für `SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Anforderungsänderungen

### <a name="query-string-changes"></a>Änderungen an Abfragezeichenfolgen

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

### <a name="v3-post-body"></a>V3 POST-Text

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Eigenschaft|type|Version|Standard|Zweck|
|--|--|--|--|--|
|`dynamicLists`|array|Nur V3|Nicht erforderlich.|Mit [dynamischen Listen](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time) können Sie eine trainierte und veröffentlichte Listenentität erweitern, die bereits Teil der LUIS-App ist.|
|`externalEntities`|array|Nur V3|Nicht erforderlich.|Mit [externen Entitäten](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time) kann Ihre LUIS-App zur Laufzeit Entitäten identifizieren und bezeichnen. Dieses Verhalten kann als Feature für andere vorhandene Entitäten verwendet werden. |
|`options.datetimeReference`|Zeichenfolge|Nur V3|Kein Standardwert|Wird zum Ermitteln des [datetimeV2-Offsets](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) verwendet. Das Format für „datetimeReference“ ist [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|boolean|Nur V3|false|Gibt an, ob die [externe Entität (mit dem gleichen Namen wie die vorhandene Entität)](schema-change-prediction-runtime.md#override-existing-model-predictions) des Benutzers oder die vorhandene Entität im Modell für die Vorhersage genutzt wird. |
|`query`|Zeichenfolge|Nur V3|Erforderlich.|**In V2** enthält der `q`-Parameter die vorherzusagende Äußerung. <br><br>**In V3** wird der `query`-Parameter verwendet, um anzugeben, dass dieses Feature verwendet werden soll.|

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
* Vereinfachter programmgesteuerter Zugriff auf vorhergesagte Daten. Sie müssen nun nicht mehr wie in V2 ein Array durchlaufen, sondern können sowohl für Absichten als auch Entitäten über den **Namen** auf Werte zugreifen. Für vorhergesagte Entitätsrollen wird der Rollenname zurückgegeben, da dieser überall in der App eindeutig ist.
* Wenn Datentypen ermittelt werden, werden diese auch berücksichtigt. Numerische Typen werden nicht mehr als Zeichenfolgen zurückgegeben.
* Unterscheidung zwischen Vorhersageinformationen der ersten Prioritätsstufe und zusätzlichen Metadaten, die innerhalb des `$instance`-Objekts zurückgegeben werden.

### <a name="entity-response-changes"></a>Änderungen an Entitätsantworten

#### <a name="marking-placement-of-entities-in-utterances"></a>Kennzeichnen der Position von Entitäten in Äußerungen

**In V2** wird eine Entität in einer Äußerung mit `startIndex` und `endIndex` gekennzeichnet.

**In V3** wird die Entität mit `startIndex` und `entityLength` gekennzeichnet.

#### <a name="access-instance-for-entity-metadata"></a>Zugreifen auf `$instance` zum Abrufen von Entitätsmetadaten

Wenn Sie Entitätsmetadaten abrufen müssen, muss für die Abfragezeichenfolge das `verbose=true`-Flag verwendet werden. Dadurch werden die Metadaten in das `$instance`-Objekt der Antwort integriert. Beispiele finden Sie in den JSON-Antworten in den folgenden Abschnitten.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Jede vorhergesagte Entität wird als Array dargestellt

Das `prediction.entities.<entity-name>`-Objekt enthält ein Array, da jede Entität mehrfach in der Äußerung vorhergesagt werden kann.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Änderungen an vordefinierten Entitäten

Das V3-Antwortobjekt beinhaltet Änderungen an vordefinierten Entitäten. Weitere Informationen finden Sie unter [spezifische vordefinierte Entitäten](luis-reference-prebuilt-entities.md).

#### <a name="list-entity-prediction-changes"></a>Änderungen an Vorhersagen von Listenentitäten

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

#### <a name="entity-role-name-instead-of-entity-name"></a>Verwendung des Entitätsrollennamens anstelle des Entitätsnamens

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

<a name="external-entities-passed-in-at-prediction-time"></a>
<a name="override-existing-model-predictions"></a>

## <a name="extend-the-app-at-prediction-time"></a>Erweitern der App zur Vorhersagezeit

Lernen Sie [Konzepte](schema-change-prediction-runtime.md) kennen, wie Sie die Anwendung zur Vorhersagelaufzeit erweitern können.

## <a name="deprecation"></a>Eingestellte Unterstützung

Die V2-API wird mindestens neun Monate nach der V3-Vorschau nicht eingestellt.

## <a name="next-steps"></a>Nächste Schritte

Aktualisieren Sie mithilfe der Dokumentation zur API V3 vorhandene REST-Aufrufe der [LUIS-Endpunkt-APIs](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8).