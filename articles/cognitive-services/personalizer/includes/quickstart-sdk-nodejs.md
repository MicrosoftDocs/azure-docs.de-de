---
title: include file
description: include file
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/27/2020
ms.openlocfilehash: d2b0d4435f681ce6dedea7cace929a03e6782bce
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371761"
---
[Referenzdokumentation](/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) |[Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [Paket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [Beispiele](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Die aktuelle Version von [Node.js](https://nodejs.org) und npm
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="Erstellen einer Personalisierungsressource"  target="_blank"> im Azure-Portal eine Personalisierungsressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Personalisierungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

[!INCLUDE [Change model frequency](change-model-frequency.md)]

### <a name="create-a-new-nodejs-application"></a>Erstellen einer neuen Node.js-Anwendung

Erstellen Sie in einem Konsolenfenster (etwa cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App, und rufen Sie es auf.

```console
mkdir myapp && cd myapp
```

Führen Sie den Befehl `npm init -y` aus, um die Datei `package.json` zu erstellen.

```console
npm init -y
```

Erstellen Sie in Ihrem bevorzugten Editor oder Ihrer bevorzugten IDE eine neue Node.js-Anwendung mit dem Namen `sample.js` sowie Variablen für den Endpunkt und Abonnementschlüssel Ihrer Ressource. 

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```javascript
const uuidv1 = require('uuid/v1');
const Personalizer = require('@azure/cognitiveservices-personalizer');
const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
const readline = require('readline-sync');

// The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
const serviceKey = "<REPLACE-WITH-YOUR-PERSONALIZER-KEY>";

// The endpoint specific to your personalization service instance; 
// e.g. https://<your-resource-name>.cognitiveservices.azure.com
const baseUri = "https://<REPLACE-WITH-YOUR-PERSONALIZER-ENDPOINT>.cognitiveservices.azure.com";
```

### <a name="install-the-nodejs-library-for-personalizer"></a>Installieren der Node.js-Bibliothek für die Personalisierung

Installieren Sie mit dem folgenden Befehl die Personalisierungsclientbibliothek für Node.js:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Installieren Sie die verbleibenden NPM-Pakete für diese Schnellstartanleitung:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Objektmodell

Der Personalisierungsclient ist ein Objekt vom Typ [PersonalizerClient](/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest), das mithilfe der Klasse „Microsoft.Rest.ServiceClientCredentials“, die Ihren Schlüssel enthält, bei Azure authentifiziert wird.

Erstellen Sie zum Anfordern des besten Inhaltselements eine Rangfolgeanforderung ([RankRequest](/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest)), und übergeben Sie sie an die Methode [client.Rank](/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-). Die Methode „Rank“ gibt eine Rangfolgeantwort (RankResponse) zurück.

Erstellen Sie eine Relevanzanforderung ([RewardRequest](/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest)), und übergeben Sie sie an die Methode [Reward](/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) der Klasse „Events“, um eine Relevanz an die Personalisierung zu senden.

Im Rahmen dieser Schnellstartanleitung ist die Bestimmung der Relevanz ganz einfach. In einem Produktionssystem kann die Bestimmung der Einflussfaktoren für die [Relevanzbewertung](../concept-rewards.md) sowie deren jeweilige Gewichtung allerdings eine komplexe Angelegenheit sein und muss unter Umständen im Laufe der Zeit überarbeitet werden. Hierbei handelt es sich um eine der wichtigsten Entwurfsentscheidungen im Zusammenhang mit Ihrer Personalisierungsarchitektur.

## <a name="code-examples"></a>Codebeispiele

In diesen Codeausschnitten werden folgende Vorgänge mit der Personalisierungsclientbibliothek für Node.js veranschaulicht:

* [Erstellen eines Personalisierungsclients](#authenticate-the-client)
* [Rangfolge-API](#request-the-best-action)
* [Relevanz-API](#send-a-reward)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie `PersonalizerClient` mit den zuvor erstellten Elementen `serviceKey` und `baseUri`.

```javascript
const credentials = new CognitiveServicesCredentials(serviceKey);

// Initialize Personalization client.
const personalizerClient = new Personalizer.PersonalizerClient(credentials, baseUri);
```

## <a name="get-content-choices-represented-as-actions"></a>Abrufen von Inhaltsoptionen, dargestellt als Aktionen

Aktionen stellen die Inhaltsoptionen dar, aus denen die Personalisierung das beste Inhaltselement auswählen soll. Fügen Sie der Klasse „Program“ die folgenden Methoden hinzu, um die Gruppe der Aktionen und deren Features darzustellen.

```javascript
function getContextFeaturesList() {
  const timeOfDayFeatures = ['morning', 'afternoon', 'evening', 'night'];
  const tasteFeatures = ['salty', 'sweet'];

  let answer = readline.question("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n");
  let selection = parseInt(answer);
  const timeOfDay = selection >= 1 && selection <= 4 ? timeOfDayFeatures[selection - 1] : timeOfDayFeatures[0];

  answer = readline.question("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet\n");
  selection = parseInt(answer);
  const taste = selection >= 1 && selection <= 2 ? tasteFeatures[selection - 1] : tasteFeatures[0];

  console.log("Selected features:\n");
  console.log("Time of day: " + timeOfDay + "\n");
  console.log("Taste: " + taste + "\n");

  return [
    {
      "time": timeOfDay
    },
    {
      "taste": taste
    }
  ];
}
```

```javascript
function getActionsList() {
  return [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium"
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none"
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none"
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low"
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ];
}
```

## <a name="create-the-learning-loop"></a>Erstellen der Lernschleife

Die Lernschleife der Personalisierung ist ein Zyklus aus [Rangfolge-](#request-the-best-action) und [Relevanzaufrufen](#send-a-reward). In dieser Schnellstartanleitung folgt auf jeden Rangfolgeaufruf zur Personalisierung des Inhalts ein Relevanzaufruf, um der Personalisierung mitzuteilen, wie gut der Dienst abgeschnitten hat.

Der folgende Code durchläuft einen Zyklus in Form einer Schleife: Der Benutzer wird an der Befehlszeile nach seinen Präferenzen befragt, die Angaben werden zur Wahl der besten Aktion an die Personalisierung gesendet, die Auswahl wird dem Kunden in einer Auswahlliste angezeigt, und anschließend wird eine Relevanz an die Personalisierung gesendet, die angibt, wie gut die Wahl des Diensts war.

```javascript
let runLoop = true;

do {

  let rankRequest = {}

  // Generate an ID to associate with the request.
  rankRequest.eventId = uuidv1();

  // Get context information from the user.
  rankRequest.contextFeatures = getContextFeaturesList();

  // Get the actions list to choose from personalization with their features.
  rankRequest.actions = getActionsList();

  // Exclude an action for personalization ranking. This action will be held at its current position.
  rankRequest.excludedActions = getExcludedActionsList();

  rankRequest.deferActivation = false;

  // Rank the actions
  const rankResponse = await personalizerClient.rank(rankRequest);

  console.log("\nPersonalization service thinks you would like to have:\n")
  console.log(rankResponse.rewardActionId);

  // Display top choice to user, user agrees or disagrees with top choice
  const reward = getReward();

  console.log("\nPersonalization service ranked the actions with the probabilities as below:\n");
  for (let i = 0; i < rankResponse.ranking.length; i++) {
    console.log(JSON.stringify(rankResponse.ranking[i]) + "\n");
  }

  // Send the reward for the action based on user response.

  const rewardRequest = {
    value: reward
  }

  await personalizerClient.events.reward(rankRequest.eventId, rewardRequest);

  runLoop = continueLoop();

} while (runLoop);
```

Sehen Sie sich die Rangfolge- und Relevanzaufrufe in den folgenden Abschnitten genauer an.

Fügen Sie vor dem Ausführen der Codedatei die folgenden Methoden hinzu, die die [Inhaltsoptionen abrufen](#get-content-choices-represented-as-actions):

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>Anfordern der besten Aktion

Für die Rangfolgeanforderung erfragt das Programm die Präferenzen des Benutzers, um Inhaltsoptionen zu erstellen. Der Prozess kann Inhalte erstellen, die von den Aktionen ausgeschlossen werden sollen (angezeigt als `excludeActions`). Die Rangfolgeanforderung muss die [Aktionen](../concepts-features.md#actions-represent-a-list-of-options) und ihre Features, die Features des aktuellen Kontexts (currentContext), die auszuschließenden Aktionen (excludeActions) und eine eindeutige Rangfolgeereignis-ID enthalten, um die bewertete Antwort zu erhalten.

In dieser Schnellstartanleitung werden die einfachen Kontextmerkmale „Tageszeit“ und „Essenswunsch des Benutzers“ verwendet. In Produktionssystemen kann die Bestimmung und [Auswertung](../concept-feature-evaluation.md) von [Aktionen und Merkmalen](../concepts-features.md) allerdings eine komplexe Angelegenheit sein.

```javascript
let rankRequest = {}

// Generate an ID to associate with the request.
rankRequest.eventId = uuidv1();

// Get context information from the user.
rankRequest.contextFeatures = getContextFeaturesList();

// Get the actions list to choose from personalization with their features.
rankRequest.actions = getActionsList();

// Exclude an action for personalization ranking. This action will be held at its current position.
rankRequest.excludedActions = getExcludedActionsList();

rankRequest.deferActivation = false;

// Rank the actions
const rankResponse = await personalizerClient.rank(rankRequest);
```

## <a name="send-a-reward"></a>Senden einer Relevanz

Um die Relevanzbewertung zu erhalten, die in der Relevanzanforderung gesendet wird, ruft das Programm die Auswahl des Benutzers aus der Befehlszeile ab, weist der Auswahl einen numerischen Wert zu und sendet dann die eindeutige Ereignis-ID und die Relevanzbewertung als numerischen Wert an die Relevanz-API.

In dieser Schnellstartanleitung wird als Relevanzbewertung eine einfache Zahl (0 oder 1) zugewiesen. In Produktionssystemen ist die Entscheidung, was wann an den [Relevanzaufruf](../concept-rewards.md) gesendet werden soll (abhängig von Ihren spezifischen Anforderungen), unter Umständen etwas komplizierter.

```javascript
const rewardRequest = {
  value: reward
}

await personalizerClient.events.reward(rankRequest.eventId, rewardRequest);
```

## <a name="run-the-program"></a>Ausführen des Programms

Führen Sie die Anwendung mit dem Node.js-Beispiel aus dem Anwendungsverzeichnis aus.

```console
node sample.js
```

![Das Schnellstartprogramm stellt ein paar Fragen zum Sammeln von Benutzereinstellungen – auch bekannt als Features – und stellt dann die am besten bewertete Aktion bereit.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)