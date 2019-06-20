---
title: Extrahieren von Entitäten mit Textübereinstimmungen
description: Erfahren Sie, wie Sie eine Listenentität hinzufügen, um den LUIS bei der Bezeichnung von Varianten eines Worts oder Ausdrucks zu unterstützen.
services: cognitive-services
author: diberry
titleSuffix: Azure
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 929dc7a86d141446a2070b046c6febfda4a07f0f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62117447"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Erhöhen der Entitätserkennung mithilfe einer Listenentität 
Dieses Tutorial veranschaulicht die Verwendung einer [Listenentität](luis-concept-entity-types.md) zur Erhöhung der Entitätserkennung. Listenentitäten müssen nicht bezeichnet werden, da sie exakte Übereinstimmungen der Benennungen darstellen.  

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Listenentität 
> * Hinzufügen von normalisierten Werten und Synonymen
> * Überprüfen der verbesserten Entitätsidentifizierung

## <a name="prerequisites"></a>Voraussetzungen

> [!div class="checklist"]
> * Aktuelle Version von [Node.js](https://nodejs.org)
> * [HomeAutomation-LUIS-App](luis-get-started-create-app.md). Wenn Sie die HomeAutomation-App nicht erstellt haben, erstellen Sie eine neue App, und fügen Sie die vorgefertigten Domäne, **HomeAutomation**, hinzu. Trainieren und veröffentlichen Sie die App. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (bei häufigen Abfragen), appID, versionID und [region](luis-reference-regions.md) für die LUIS-App.

> [!Tip]
> Wenn Sie noch kein Abonnement besitzen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/free/) registrieren.

Der gesamte Code in diesem Tutorial ist im [GitHub-Repository mit Azure-Beispielen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity) verfügbar. 

## <a name="use-homeautomation-app"></a>Verwenden der HomeAutomation-App
Die HomeAutomation-App ermöglicht die Steuerung von Geräten wie Beleuchtungen, Unterhaltungselektronik und Gebäudesystemen (z.B. Heizung und Kühlung). Solche Systeme werden unterschiedlich benannt, beispielsweise mit herstellerspezifischen Namen, umgangssprachlichen Bezeichnungen und Akronymen. 

Ein System, das in verschiedenen Kulturen und Gesellschaftskreisen unterschiedliche Benennungen aufweist, ist das Thermostat. Ein Thermostat kann sowohl Kühl- als auch Heizanlagen eines Hauses oder Gebäude steuern.

Im Idealfall sollten die folgenden Äußerungen von der vorgefertigten Entität **HomeAutomation.Device** aufgelöst werden können:

|#|Äußerung|Identifizierte Entität|Ergebnis Ihrer App|
|--|--|--|--|
|1|„turn on the ac“ (Klimaanlage einschalten)|HomeAutomation.Device - "ac"|0.8748562|
|2|„turn up the heat“ (Heizung hochdrehen)|HomeAutomation.Device - "heat"|0.784990132|
|3|„make it colder“ (Kühler einstellen)|||

Die ersten beiden Äußerungen bezeichnen verschiedene Geräte. Die dritte Äußerung „make it colder“ (Kälter einstellen) bezieht sich nicht auf ein Gerät, sondern erfordert ein Ergebnis. Der LUIS weiß nicht, dass mit der Benennung „colder“ (kälter) das Thermostat gemeint ist. Im Idealfall sollte der LUIS all diese Äußerungen demselben Gerät zuordnen. 

## <a name="use-a-list-entity"></a>Verwenden einer Listenentität
Die HomeAutomation.Device-Entität ist bei einer kleinen Anzahl von Geräten oder bei wenigen Varianten der Namen hervorragend geeignet. Bei einem Bürogebäude oder Hochschulgelände ist die Anzahl der Gerätenamen so immens hoch, dass der Nutzen der HomeAutomation.Device-Entität nicht zum Tragen kommt. 

In diesem Szenario ist eine **Listenentität** eine gute Option, da die Gruppe von Benennungen für ein Gerät in einem Gebäude oder Hochschulgelände bekannt ist, selbst bei einer großen Gruppe. Mithilfe einer Listenentität erhält der LUIS alle möglichen Werte in der Gruppe für das Thermostat und ordnet diese einem einzigen Gerät zu, nämlich dem „Thermostat“. 

In diesem Tutorial wird eine Entitätsliste mit dem Thermostat erstellt. In diesem Tutorial werden folgende alternative Bezeichnungen für ein Thermostat verwendet: 

|Alternative Bezeichnungen für ein Thermostat|
|--|
| ac (Klimaanlage) |
| a/c (Klimaanlage)|
| a-c (Klimaanlage)|
|heater (Heizung)|
|hot (warm)|
|hotter (wärmer)|
|cold (kalt)|
|colder (kälter)|

Wenn der LUIS häufig eine neue Alternative festlegen muss, stellt eine [Begriffsliste](luis-concept-feature.md#how-to-use-phrase-lists) eine bessere Lösung dar.

## <a name="create-a-list-entity"></a>Erstellen einer Listenentität
Erstellen Sie eine Node.js-Datei, und fügen Sie folgenden Code ein. Ändern Sie die Werte von „authoringKey“, „appId“, „versionId“ und „region“.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Verwenden Sie den folgenden Befehl, um NPM-Abhängigkeiten zu installieren und den Code zum Erstellen der Listenentität auszuführen:

```console
npm install && node add-entity-list.js
```

Die Ausgabe der Ausführung ist die ID der Listenentität:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Modelltraining
Trainieren Sie den LUIS, damit die neue Liste auf die Abfrageergebnisse angewendet wird. Das Training besteht aus zwei Teilen: dem Training selbst und der Prüfung, ob das Training erfolgt ist. Es kann eine Weile dauern, bis eine App mit vielen Modellen trainiert ist. Der folgende Code trainiert die App und wartet, bis das Training erfolgreich ist. Der Code verwendet eine Warte- und Wiederholungsstrategie, um zu verhindern, dass zu viele 429-Fehler („Zu viele Anforderungen“) auftreten. 

Erstellen Sie eine Node.js-Datei, und fügen Sie folgenden Code ein. Ändern Sie die Werte von „authoringKey“, „appId“, „versionId“ und „region“.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Führen Sie den Code zum Trainieren der App mithilfe des folgenden Befehls aus:

```console
node train.js
```

Die Ausgabe der Ausführung ist der Status jeder Iteration des Trainings der LUIS Modelle. Die folgende Ausführung erforderte nur eine Überprüfung des Trainings:

```console
1 trained = true
[ { modelId: '2c549f95-867a-4189-9c35-44b95c78b70f',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '5530e900-571d-40ec-9c78-63e66b50c7d4',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '519faa39-ae1a-4d98-965c-abff6f743fe6',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9671a485-36a9-46d5-aacd-b16d05115415',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9ef7d891-54ab-48bf-8112-c34dcd75d5e2',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '8e16a660-8781-4abf-bf3d-f296ebe1bf2d',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } } ]

```
## <a name="publish-the-model"></a>Veröffentlichen des Modells
Veröffentlichen Sie das Modell, damit die Listenentität über den Endpunkt verfügbar ist.

Erstellen Sie eine Node.js-Datei, und fügen Sie folgenden Code ein. Ändern Sie die Werte „endpointKey“, „appId“ und „region“. Wenn Sie diese Datei nicht über die Kontingentgrenze hinaus aufrufen, können Sie Ihren authoringKey verwenden.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Führen Sie den Code zum Abfragen der App mithilfe des folgenden Befehls aus:

```console
node publish.js
```

Die folgende Ausgabe enthält die Endpunkt-URL für alle Abfragen. Echte JSON-Ergebnisse würden die echte appID einschließen. 

```json
{ 
  versionId: null,
  isStaging: false,
  endpointUrl: 'https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>',
  region: null,
  assignedEndpointKey: null,
  endpointRegion: 'westus',
  publishedDateTime: '2018-01-29T22:17:38Z' }
}
```

## <a name="query-the-app"></a>Abfragen der App 
Fragen Sie die App über den Endpunkt ab, um nachzuweisen, dass der LUIS den Gerätetyp mithilfe der Listenentität bestimmen kann.

Erstellen Sie eine Node.js-Datei, und fügen Sie folgenden Code ein. Ändern Sie die Werte „endpointKey“, „appId“ und „region“. Wenn Sie diese Datei nicht über die Kontingentgrenze hinaus aufrufen, können Sie Ihren authoringKey verwenden.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Führen Sie den Code mithilfe des folgenden Befehls aus, und fragen Sie die App ab:

```console
node train.js
```

Die Ausgabe enthält die Abfrageergebnisse. Da der Code den **ausführlichen** Name-Wert-Paar zur Abfragezeichenfolge hinzugefügt hat, umfasst die Ausgabe alle Intents und deren Ergebnisse:

```json
{
  "query": "turn up the heat",
  "topScoringIntent": {
    "intent": "HomeAutomation.TurnOn",
    "score": 0.139018849
  },
  "intents": [
    {
      "intent": "HomeAutomation.TurnOn",
      "score": 0.139018849
    },
    {
      "intent": "None",
      "score": 0.120624863
    },
    {
      "intent": "HomeAutomation.TurnOff",
      "score": 0.06746891
    }
  ],
  "entities": [
    {
      "entity": "heat",
      "type": "HomeAutomation.Device",
      "startIndex": 12,
      "endIndex": 15,
      "score": 0.784990132
    },
    {
      "entity": "heat",
      "type": "DevicesList",
      "startIndex": 12,
      "endIndex": 15,
      "resolution": {
        "values": [
          "Thermostat"
        ]
      }
    }
  ]
}
```

Das jeweilige Gerät von **Thermostat** wird durch eine ergebnisorientierter Abfrage von „Heizung hochdrehen“ identifiziert. Da die ursprüngliche HomeAutomation.Device-Entität noch in der App enthalten ist, können Sie auch die entsprechenden Ergebnisse sehen. 

Wiederholen Sie die anderen zwei Äußerungen, um festzustellen, ob diese ebenfalls als Thermostat zurückgegeben werden. 

|#|Äußerung|Entität|type|value|
|--|--|--|--|--|
|1|„turn on the ac“ (Klimaanlage einschalten)| ac (Klimaanlage) | DevicesList | Thermostat|
|2|„turn up the heat“ (Heizung hochdrehen)|heat| DevicesList |Thermostat|
|3|„make it colder“ (Kühler einstellen)|colder|DevicesList|Thermostat|

## <a name="next-steps"></a>Nächste Schritte

Sie können eine andere Listenentität erstellen, um Gerätestandorte auf Räume, Etagen oder Gebäude zu erweitern. 
