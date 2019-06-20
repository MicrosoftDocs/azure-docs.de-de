---
title: Importieren von Äußerungen mithilfe von Node.js
titleSuffix: Azure
description: Erfahren Sie, wie Sie mithilfe der LUIS-Erstellungs-API eine LUIS-App programmgesteuert aus bereits vorhandenen Daten im CSV-Format erstellen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/30/2019
ms.author: diberry
ms.openlocfilehash: 314d121e8964ba1cdbb457260826d85bf8505fbc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60494896"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Programmgesteuertes Erstellen einer LUIS-App mit Node.js

LUIS bietet eine programmgesteuerte API, die alle Funktionen der [LUIS](luis-reference-regions.md)-Website aufweist. Dies kann Zeit sparen, wenn Sie über bereits vorhandene Daten verfügen und es schneller geht, eine LUIS-App programmgesteuert zu erstellen, als die Informationen manuell einzugeben. 

## <a name="prerequisites"></a>Voraussetzungen

* Melden Sie sich auf der [LUIS](luis-reference-regions.md)-Website an, und suchen Sie Ihren [Erstellungsschlüssel](luis-concept-keys.md#authoring-key) in den Kontoeinstellungen. Sie können mit diesem Schlüssel die Erstellungs-APIs aufrufen.
* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
* Dieses Tutorial beginnt mit Daten im CSV-Format für Protokolldateien von Benutzeranforderungen eines hypothetischen Unternehmens. Sie können sie [hier](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv)herunterladen.
* Installieren Sie die neueste Version von Node.js mit NPM. Laden Sie es [hier](https://nodejs.org/en/download/) herunter.
* **[Empfohlen:]** Visual Studio Code für IntelliSense und Debuggen, laden Sie es [hier](https://code.visualstudio.com/) kostenlos herunter.

## <a name="map-preexisting-data-to-intents-and-entities"></a>Zuordnen von bereits vorhandenen Daten zu Absichten und Entitäten
Selbst wenn Sie ein System haben, bei dessen Erstellung LUIS nicht berücksichtigt wurde, enthält dieses möglicherweise Textdaten, die verschiedenen möglichen Aktionen von Benutzern zugeordnet sind. In diesem Fall können Sie u.U. eine Zuordnung zwischen den vorhandenen Kategorien von Benutzereingaben und den Absichten in LUIS erstellen. Wenn es möglich ist, wichtige Wörter oder Ausdrücke in den Aussagen der Benutzern zu identifizieren, können Sie diese Wörter möglicherweise Entitäten zuordnen.

Öffnen Sie die Datei `IoT.csv` . Sie enthält ein Protokoll von Benutzerabfragen eines hypothetischen Heimautomatisierungsdiensts, einschließlich der Art ihrer Kategorisierung, und einiger Spalten mit nützlichen Informationen, die diesen entnommen wurden. 

![CSV-Datei bereits vorhandener Daten](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

Sie sehen, dass es sich bei der Spalte **RequestType** um Absichten handeln könnte und dass die Spalte **Request** eine Beispieläußerung enthält. Bei den anderen Feldern könnte es sich um Entitäten handeln, wenn sie in der Äußerung auftreten. Da es Absichten, Entitäten und Beispieläußerungen gibt, sind alle Anforderungen an eine einfache Beispiel-App erfüllt.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Schritte zum Generieren einer LUIS-App aus Nicht-LUIS-Daten
Um eine neue LUIS-App aus der Quelldatei zu generieren, analysieren Sie zuerst die Daten in der CSV-Datei und konvertieren diese Daten dann in ein Format, das Sie mithilfe der Erstellungs-API in LUIS hochladen können. Erfassen Sie aus den analysierten Daten Informationen dazu, welche Absichten und Entitäten vorhanden sind. Anschließend führen Sie API-Aufrufe zum Erstellen der App aus und fügen Absichten und Entitäten hinzu, die aus den analysierten Daten erfasst wurden. Nachdem Sie die LUIS-App erstellt haben, können Sie die Beispieläußerungen aus den analysierten Daten hinzufügen. Diesen Ablauf können Sie im letzten Teil des folgende Codes sehen. Kopieren Sie diesen Code, oder [laden Sie ihn herunter](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js), und speichern Sie ihn in `index.js`.

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Analysieren der CSV-Daten

Die Spalteneinträge, die die Äußerungen in den CSV-Daten enthalten, müssen in ein JSON-Format umgewandelt werden, das LUIS verstehen kann. Dieses JSON-Format muss ein Feld `intentName` enthalten, das die Absicht der Äußerung identifiziert. Es muss auch ein Feld `entityLabels` enthalten sein, das leer sein kann, wenn die Äußerung keine Entitäten enthält. 

Der Eintrag für „Turn on the lights“ wird z.B. folgendem JSON-Code zugeordnet:

```json
        {
            "text": "Turn on the lights",
            "intentName": "TurnOn",
            "entityLabels": [
                {
                    "entityName": "Operation",
                    "startCharIndex": 5,
                    "endCharIndex": 6
                },
                {
                    "entityName": "Device",
                    "startCharIndex": 12,
                    "endCharIndex": 17
                }
            ]
        }
```

In diesem Beispiel stammt der `intentName` von der Anforderung des Benutzers unter der Spaltenüberschrift **Request** in der CSV-Datei, und der `entityName` stammt aus den anderen Spalten mit Schlüsselinformationen. Wenn beispielsweise ein Eintrag für **Operation** oder **Device** vorliegt und diese Zeichenfolge auch in der eigentlichen Anforderung auftritt, kann sie als Entität bezeichnet werden. Der folgende Code veranschaulicht den Analyseprozesses. Sie können ihn kopieren oder [herunterladen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) und als `_parse.js` speichern.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Erstellen der LUIS-App
Nachdem die Daten in JSON analysiert wurden, fügen Sie sie einer LUIS-App hinzu. Der folgende Code erstellt die LUIS-App. Kopieren Sie ihn, oder [laden Sie ihn herunter](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js), und speichern Sie ihn in `_create.js`.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Hinzufügen von Absichten
Nachdem Sie eine App haben, müssen Sie ihr Absichten hinzufügen. Der folgende Code erstellt die LUIS-App. Kopieren Sie ihn, oder [laden Sie ihn herunter](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js), und speichern Sie ihn in `_intents.js`.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Hinzufügen von Entitäten
Der folgende Code fügt die Entitäten der LUIS-App hinzu. Kopieren Sie ihn, oder [laden Sie ihn herunter](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js), und speichern Sie ihn in `_entities.js`.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Hinzufügen von Äußerungen
Nachdem die Entitäten und Absichten in der LUIS-App definiert wurden, können Sie die Äußerungen hinzufügen. Der folgende Code verwendet die [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)-API, mit der Sie bis zu 100 Äußerungen gleichzeitig hinzufügen können.  Kopieren Sie ihn, oder [laden Sie ihn herunter](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js), und speichern Sie ihn in `_upload.js`.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Ausführen des Codes


### <a name="install-nodejs-dependencies"></a>Installieren von Node.js-Abhängigkeiten
Installieren Sie die Node.js-Abhängigkeiten von NPM im Terminal bzw. an der Befehlszeile.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Ändern der Konfigurationseinstellungen
Um diese Anwendung verwenden zu können, müssen Sie die Werte in der Datei „index.js“ in Ihren eigenen Endpunktschlüssel ändern und den gewünschten Namen für die App angeben. Sie können auch die Kultur der App festlegen oder die Versionsnummer ändern.

Öffnen Sie die Datei „Index.js“, und ändern Sie diese Werte am Anfang der Datei.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_PROGRAMMATIC_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Ausführen des Skripts
Führen Sie das Skript über ein Terminal oder die Befehlszeile mit Node.js aus.

```console
> node index.js
```

oder

```console
> npm start
```

### <a name="application-progress"></a>Anwendungsfortschritt
Während die Anwendung ausgeführt wird, wird an der Befehlszeile der Fortschritt angezeigt. Die Befehlszeilenausgabe schließt das Format der Antworten von LUIS ein.

```console
> node index.js
intents: ["TurnOn","TurnOff","Dim","Other"]
entities: ["Operation","Device","Room"]
parse done
JSON file should contain utterances. Next step is to create an app with the intents and entities it found.
Called createApp, created app with ID 314b306c-0033-4e09-92ab-94fe5ed158a2
Called addIntents for intent named TurnOn.
Called addIntents for intent named TurnOff.
Called addIntents for intent named Dim.
Called addIntents for intent named Other.
Results of all calls to addIntent = [{"response":"e7eaf224-8c61-44ed-a6b0-2ab4dc56f1d0"},{"response":"a8a17efd-f01c-488d-ad44-a31a818cf7d7"},{"response":"bc7c32fc-14a0-4b72-bad4-d345d807f965"},{"response":"727a8d73-cd3b-4096-bc8d-d7cfba12eb44"}]
called addEntity for entity named Operation.
called addEntity for entity named Device.
called addEntity for entity named Room.
Results of all calls to addEntity= [{"response":"6a7e914f-911d-4c6c-a5bc-377afdce4390"},{"response":"56c35237-593d-47f6-9d01-2912fa488760"},{"response":"f1dd440c-2ce3-4a20-a817-a57273f169f3"}]
retrying add examples...

Results of add utterances = [{"response":[{"value":{"UtteranceText":"turn on the lights","ExampleId":-67649},"hasError":false},{"value":{"UtteranceText":"turn the heat on","ExampleId":-69067},"hasError":false},{"value":{"UtteranceText":"switch on the kitchen fan","ExampleId":-3395901},"hasError":false},{"value":{"UtteranceText":"turn off bedroom lights","ExampleId":-85402},"hasError":false},{"value":{"UtteranceText":"turn off air conditioning","ExampleId":-8991572},"hasError":false},{"value":{"UtteranceText":"kill the lights","ExampleId":-70124},"hasError":false},{"value":{"UtteranceText":"dim the lights","ExampleId":-174358},"hasError":false},{"value":{"UtteranceText":"hi how are you","ExampleId":-143722},"hasError":false},{"value":{"UtteranceText":"answer the phone","ExampleId":-69939},"hasError":false},{"value":{"UtteranceText":"are you there","ExampleId":-149588},"hasError":false},{"value":{"UtteranceText":"help","ExampleId":-81949},"hasError":false},{"value":{"UtteranceText":"testing the circuit","ExampleId":-11548708},"hasError":false}]}]
upload done
```




## <a name="open-the-luis-app"></a>Öffnen der LUIS-App
Nachdem das Skript abgeschlossen wurde, können Sie sich bei [LUIS](luis-reference-regions.md) anmelden und die erstellte LUIS-App unter **My Apps** (Meine Apps) ansehen. Sie sollten die hinzugefügten Äußerungen unter den Absichten **TurnOn**, **TurnOff** und **None** sehen.

![Absicht „TurnOn“](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Testen und Trainieren Ihrer App auf der LUIS-Website](luis-interactive-test.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

In dieser Beispielanwendung werden die folgenden LUIS-APIs verwendet:
- [Erstellen einer App](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [Hinzufügen von Absichten](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [Hinzufügen von Entitäten](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [Hinzufügen von Äußerungen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
