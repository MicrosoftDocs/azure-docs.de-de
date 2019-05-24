---
title: Ändern, Trainieren der App, Node.js
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung für Node.js fügen Sie einer Home Automation-App Beispieläußerungen hinzu, und Sie trainieren die App.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/17/2019
ms.author: diberry
ms.openlocfilehash: 6ee3ecc37cfb21630b671e70260b44f1532de2b6
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65594124"
---
# <a name="quickstart-change-model-using-nodejs"></a>Schnellstart: Ändern des Modells mit Node.js

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* Aktuelle Version von [**Node.js**](https://nodejs.org/en/download/) mit npm
* npm-Abhängigkeiten für diesen Artikel: [**request**](https://www.npmjs.com/package/request), [**request-promise**](https://www.npmjs.com/package/request-promise), [**fs-extra**](https://www.npmjs.com/package/fs-extra)  
* [Visual Studio Code](https://code.visualstudio.com/).

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>JSON-Datei mit Beispieläußerungen

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Erstellen des Schnellstartcodes 

Fügen Sie die npm-Abhängigkeiten der Datei namens `add-utterances.js` hinzu.

   [!code-javascript[NPM Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=8-11 "NPM Dependencies")]

Fügen Sie die LUIS-Konstanten zur Datei hinzu. Kopieren Sie den folgenden Code, und fügen Sie Ihren Erstellungsschlüssel, die Anwendungs-ID und die Versions-ID ein.

   [!code-javascript[LUIS key and IDs](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=13-22 "LUIS key and IDs")]

Fügen Sie den Namen und den Speicherort der Uploaddatei mit Ihren Äußerungen hinzu: 

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=24-26 "Add upload file")]

Fügen Sie die Methode und das Objekt für die `addUtterance`-Funktion hinzu.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=28-67 "Add configuration information for adding utterance")]

Fügen Sie die Methode und das Objekt für die `train`-Funktion hinzu.

   [!code-javascript[Add configuration information for training LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=69-101 "Add configuration information for training LUIS")]

Fügen Sie die Funktion `sendUtteranceToApi` zum Senden und Empfangen von HTTP-Aufrufen hinzu: 

   [!code-javascript[Send the HTTP request](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=103-119 "Send the HTTP request")]

Fügen Sie den **main**-Code hinzu, der die Aktion auswählt.

   [!code-javascript[Main function](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=121-143 "Main function")]

### <a name="install-dependencies"></a>Installieren von Abhängigkeiten

Erstellen Sie die Datei `package.json` mit dem folgenden Text:

   [!code-json[Package.json](~/samples-luis/documentation-samples/quickstarts/change-model/node/package.json "Package.json")]

Installieren Sie in der Befehlszeile mit npm die Abhängigkeiten aus dem Verzeichnis, das die Datei „package.json“ enthält: `npm install`.

## <a name="run-code"></a>Ausführen des Codes

Führen Sie die Anwendung über eine Befehlszeile mit Node.js aus.

Durch den Aufruf von `npm start` werden die Äußerungen hinzugefügt und trainiert sowie der Trainingsstatus abgerufen.

```console
> npm start 
```

Die folgende Befehlszeile zeigt das Ergebnis des Aufrufs der API zum Hinzufügen von Äußerungen an. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Entfernen Sie nach dem Durchführen dieses Schnellstarts alle Dateien, die Sie im Rahmen der Schnellstartanleitung erstellt haben. 

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"] 
> [Programmgesteuertes Erstellen einer LUIS-App](luis-tutorial-node-import-utterances-csv.md)
