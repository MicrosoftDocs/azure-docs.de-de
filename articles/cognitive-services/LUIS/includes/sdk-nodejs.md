---
title: include file
description: include file
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, devx-track-js, cog-serv-seo-aug-2020
ms.openlocfilehash: 0c3a02461d8ad4bc8470383244dad7b86b47f405
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303688"
---
Verwenden Sie die LUIS-Clientbibliotheken (Language Understanding) für Node.js für Folgendes:

* Erstellen einer App
* Hinzufügen einer Absicht (eine durch maschinelles Lernen erworbene Entität) mit einer Beispieläußerung
* Trainieren und Veröffentlichen der App
* Abfragen der Vorhersagelaufzeit

[Referenzdokumentation](/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) |  Quellcode für die [Erstellungs-](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) und [Vorhersagebibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | NPM für [Erstellung](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring) und [Vorhersage](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Beispiele](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/sdk-3x/index.js)

## <a name="prerequisites"></a>Voraussetzungen

* [Node.js](https://nodejs.org)
* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Sobald Sie über Ihr Azure-Abonnement verfügen, [erstellen Sie eine Language Understanding-Erstellungsressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) im Azure-Portal, um an Ihren Schlüssel und Ihren Endpunkt zu gelangen. Warten Sie ihre Bereitstellung ab, und klicken Sie auf die Schaltfläche **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen [erstellten](../luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) Ressource, um Ihre Anwendung mit der Language Understanding-Erstellung zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt. Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst auszuprobieren.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-javascript-application"></a>Erstellen einer neuen JavaScript-Anwendung

1. Erstellen Sie in einem Konsolenfenster ein neues Verzeichnis für Ihre Anwendung, und wechseln Sie in dieses Verzeichnis.

    ```console
    mkdir quickstart-sdk && cd quickstart-sdk
    ```

1. Initialisieren Sie das Verzeichnis als JavaScript-Anwendung, indem Sie eine `package.json`-Datei erstellen.

    ```console
    npm init -y
    ```

1. Erstellen Sie eine Datei mit dem Namen `index.js` für Ihren JavaScript-Code.

    ```console
    touch index.js
    ```

### <a name="install-the-npm-libraries"></a>Installieren der NPM-Bibliotheken

Führen Sie nacheinander die folgenden Befehle aus, um die Abhängigkeiten in Ihrem Anwendungsverzeichnis zu installieren:

```console
npm install @azure/ms-rest-js
npm install @azure/cognitiveservices-luis-authoring
npm install @azure/cognitiveservices-luis-runtime
```

`package.json` sollte wie folgt aussehen:

```json
{
  "name": "quickstart-sdk",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/cognitiveservices-luis-authoring": "^4.0.0-preview.3",
    "@azure/cognitiveservices-luis-runtime": "^5.0.0",
    "@azure/ms-rest-js": "^2.0.8"
  }
}
```

## <a name="authoring-object-model"></a>Erstellungsobjektmodell

Der LUIS-Erstellungsclient (Language Understanding) ist ein Objekt vom Typ [LUISAuthoringClient](/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest), das sich bei Azure authentifiziert und Ihren Erstellungsschlüssel enthält.

## <a name="code-examples-for-authoring"></a>Codebeispiele für die Erstellung

Nach der Erstellung des Clients können Sie damit unter anderem auf folgende Funktionen zugreifen:

* Apps: [Hinzufügen](/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-), [Löschen](/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-), [Veröffentlichen](/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Beispieläußerungen: [Hinzufügen per Batch](/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), [Löschen anhand der ID](/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Features: Verwalten von [Ausdruckslisten](/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* Modell: Verwalten von [Absichten](/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) und Entitäten
* Muster: Verwalten von [Mustern](/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* Trainieren: [Trainieren](/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) der App und Abfragen des [Trainingsstatus](/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [Versionen:](/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) Verwalten mittels Klonen, Exportieren und Löschen

## <a name="prediction-object-model"></a>Vorhersageobjektmodell

Der LUIS-Erstellungsclient (Language Understanding) ist ein Objekt vom Typ [LUISAuthoringClient](/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest), das sich bei Azure authentifiziert und Ihren Erstellungsschlüssel enthält.

## <a name="code-examples-for-prediction-runtime"></a>Codebeispiele für die Vorhersagelaufzeit

Nach der Erstellung des Clients können Sie damit unter anderem auf folgende Funktionen zugreifen:

* [Vorhersage](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) nach Slot (`staging` oder `production`)
* [Vorhersage nach Version](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>Hinzufügen der Abhängigkeiten

Öffnen Sie die Datei `index.js` in Ihrem bevorzugten Editor bzw. Ihrer bevorzugten IDE, und fügen Sie dann die folgenden Abhängigkeiten hinzu:

[!code-javascript[Add NPM libraries to code file](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=Dependencies)]

## <a name="add-boilerplate-code"></a>Hinzufügen von Codebausteinen

1. Fügen Sie die `quickstart`-Methode und Ihren Aufruf hinzu. Diese Methode enthält den Großteil des restlichen Codes. Diese Methode wird am Ende der Datei aufgerufen.

    ```javascript
    const quickstart = async () => {

        // add calls here


    }
    quickstart()
        .then(result => console.log("Done"))
        .catch(err => {
            console.log(`Error: ${err}`)
            })
    ```

1. Fügen Sie den verbleibenden Code in der quickstart-Methode hinzu, sofern nicht anders angegeben.

## <a name="create-variables-for-the-app"></a>Erstellen von Variablen für die App

Erstellen Sie zwei Sätze von Variablen: Sie ändern den ersten Satz, der zweite bleibt, wie er im Codebeispiel angezeigt wird. 

1. Erstellen Sie Variablen für Ihren Erstellungsschlüssel und Ihre Ressourcennamen.

    [!code-javascript[Variables you need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouChange)]

1. Erstellen Sie Variablen für Ihre Endpunkte, den App-Namen, die Version und den Namen der Absicht.

    [!code-javascript[Variables you don't need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Erstellen Sie ein Objekt vom Typ [CognitiveServicesCredentials](/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) mit Ihrem Schlüssel, und verwenden Sie es mit Ihrem Endpunkt, um ein Objekt vom Typ [LUISAuthoringClient](/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) zu erstellen.

[!code-javascript[Authenticate the client](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Erstellen einer LUIS-App

Eine LUIS-App enthält das NLP-Modell (Natural Language Processing, Verarbeitung natürlicher Sprache) mit Absichten, Entitäten und Beispieläußerungen.

Erstellen Sie zum Generieren der App eine Methode vom Typ [add](/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) für das [AppsOperation](/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest)-Objekt. Name und Sprachkultur sind erforderliche Eigenschaften.

[!code-javascript[Create a LUIS app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateApplication)]


## <a name="create-intent-for-the-app"></a>Erstellen der Absicht für die App
Das primäre Objekt im Modell einer LUIS-App ist die Absicht. Die Absicht orientiert sich an einer Gruppierung der _Absichten_ von Benutzeräußerungen. Ein Benutzer kann eine Frage stellen oder etwas sagen, um eine bestimmte _beabsichtigte_ Antwort von einem Bot (oder von einer anderen Clientanwendung) zu erhalten. Beispiele für Absichten wären etwa das Buchen eines Flugs, das Einholen von Wetterinformationen für eine Zielstadt oder das Erfragen von Kontaktinformationen für den Kundendienst.

Verwenden Sie die Methode [model.add_intent](/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) mit dem Namen der eindeutigen Absicht, und übergeben Sie anschließend die App-ID, die Versions-ID und den Namen der neuen Absicht.

Der Wert `intentName` wird im Abschnitt [Erstellen von Variablen für die App](#create-variables-for-the-app) als `OrderPizzaIntent` hartcodiert und ist Teil der Variablen.

[!code-javascript[Create intent for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Erstellen von Entitäten für die App

Entitäten sind zwar nicht erforderlich, aber in den meisten Apps vorhanden. Die Entität extrahiert Informationen aus der Benutzeräußerung, die zur Erfüllung der Absicht des Benutzers erforderlich sind. Es gibt mehrere Arten von [vordefinierten](/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) und benutzerdefinierten Entitäten, die jeweils über ein eigenes DTO-Modell (Data Transformation Object) verfügen.  Zu den gängigen vordefinierten Entitäten, die Sie Ihrer App hinzufügen können, zählen etwa [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md) und [ordinal](../luis-reference-prebuilt-ordinal.md).

Wichtig: Entitäten sind nicht mit einer Absicht gekennzeichnet. Sie gelten in der Regel für mehrere Absichten. Nur exemplarische Benutzeräußerungen sind für eine bestimmte, einzelne Absicht gekennzeichnet.

Erstellungsmethoden für Entitäten sind Teil der Klasse [Model](/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest). Jeder Entitätstyp verfügt über ein eigenes DTO-Modell (Data Transformation Object).

Mit dem Code für die Entitätserstellung wird eine Machine Learning-Entität mit untergeordneten Entitäten und Features erstellt, die auf die untergeordneten Entitäten vom Typ `Quantity` angewendet werden.

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="Ausschnitt aus einem Screenshot des Portals: erstellte Entität, Machine Learning-Entität mit untergeordneten Entitäten und Features, die auf die untergeordneten Entitäten vom Typ „Quantity“ angewendet werden":::

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddEntities)]

Fügen Sie die folgende Methode oberhalb der `quickstart`-Methode ein, um die ID der untergeordneten Entität vom Typ „Quantity“ zu ermitteln und dieser untergeordneten Entität die entsprechenden Features zuzuweisen.

[!code-javascript[Find subentity id](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Hinzufügen einer Beispieläußerung zu einer Absicht

Um die Absicht einer Äußerung ermitteln und Entitäten extrahieren zu können, benötigt die App Beispiele für Äußerungen. Die Beispiele müssen auf eine bestimmte, einzelne Absicht ausgerichtet sein und alle benutzerdefinierten Entitäten markieren. Vordefinierte Entitäten müssen nicht markiert werden.

Fügen Sie Beispieläußerungen hinzu, indem Sie eine Liste mit Objekten vom Typ [ExampleLabelObject](/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) erstellen (jeweils ein Objekt pro Beispieläußerung). Jedes Beispiel muss alle Entitäten mit einem Wörterbuch mit Name-Wert-Paaren (Entitätsname und Entitätswert) markieren. Der Entitätswert muss exakt dem Vorkommen im Text der Beispieläußerung entsprechen.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="Ausschnitt eines Screenshots mit der bezeichneten Beispieläußerung im Portal":::

Rufen Sie [examples.add](/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-lates) mit der App-ID, der Versions-ID und dem Beispiel auf.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>Trainieren der App

Nach Erstellung des Modells muss die LUIS-App für diese Version des Modells trainiert werden. Ein trainiertes Modell kann in einem [Container](../luis-container-howto.md) verwendet oder in den Staging- oder Produktslots [veröffentlicht](../luis-how-to-publish-app.md) werden.

Für die Methode [train.trainVersion](/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) müssen die App-ID und die Versions-ID angegeben werden.

Bei sehr kleinen Modellen (wie etwa in dieser Schnellstartanleitung) dauert das Training nicht sehr lang. Bei Produktionsanwendungen muss das App-Training auch einen Abfrageaufruf für die Methode [get_status](/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) enthalten, um zu bestimmen, wann bzw. ob das Training erfolgreich war. Die Antwort ist eine Liste mit Objekten vom Typ [ModelTrainingInfo](/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest), die jeweils einen separaten Status für die einzelnen Objekte besitzen. Alle Objekte müssen erfolgreich sein, damit das Training als abgeschlossen gilt.

[!code-javascript[Train the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>Veröffentlichen der App im Produktionsslot

Veröffentlichen Sie die LUIS-App mithilfe der Methode [app.publish](/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-). Dadurch wird die aktuelle trainierte Version im angegebenen Slot am Endpunkt veröffentlicht. Ihre Clientanwendung verwendet diesen Endpunkt zum Senden von Benutzeräußerungen für die Absichtsvorhersage und die Entitätsextraktion.

[!code-javascript[Publish app to production slot](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PublishVersion)]


## <a name="authenticate-the-prediction-runtime-client"></a>Authentifizieren des Vorhersageruntimeclients

Verwenden Sie ein Objekt vom Typ „msRest.ApiKeyCredentials“ mit Ihrem Schlüssel, und verwenden Sie es mit Ihrem Endpunkt, um ein Objekt vom Typ [LUIS.LUISRuntimeClient](/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) zu erstellen.

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-javascript [Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PredictionCreateClient)]

## <a name="get-prediction-from-runtime"></a>Abrufen der Vorhersage aus der Laufzeit

Fügen Sie den folgenden Code hinzu, um die Anforderung an die Vorhersageruntime zu erstellen. Die Benutzeräußerung ist Teil des Objekts [predictionRequest](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest).

Die Methode **[luisRuntimeClient.prediction.getSlotPrediction](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** benötigt mehrere Parameter, um die Anforderung erfüllen zu können. Hierzu zählen beispielsweise die App-ID, der Slotname und das Vorhersageanforderungsobjekt. Die anderen Optionen, wie z. B. „Ausführlich“, „Alle Absichten anzeigen“ und „Protokollieren“, sind optional.

[!code-javascript [Get prediction from runtime](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `node index.js` für die Schnellstartdatei aus.

```console
node index.js
```