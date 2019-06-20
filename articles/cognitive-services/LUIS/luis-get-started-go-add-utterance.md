---
title: Ändern, Trainieren der App, Go
description: In dieser Schnellstartanleitung für die Sprache Go fügen Sie einer Home Automation-App Beispieläußerungen hinzu, und Sie trainieren die App.
titleSuffix: Language Understanding - Microsoft Cognitive Services
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: diberry
ms.openlocfilehash: 973323cd14d9472d9845f0709fc6d2924efc56f5
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357154"
---
# <a name="quickstart-change-model-using-go"></a>Schnellstart: Ändern des Modells mit Go

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* Programmiersprache [Go](https://golang.org/) installiert
* [VSCode](https://code.visualstudio.com) 

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>JSON-Datei mit Beispieläußerungen

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Erstellen des Schnellstartcodes 

1. Erstellen Sie `add-utterances.go` mit VSCode. 

2. Fügen Sie Abhängigkeiten hinzu. 

   [!code-go[Add dependencies.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=2-10 "Add dependencies.")]

3. Fügen Sie die generische HTTP-Anforderungsfunktion hinzu, die das Übergeben des Erstellungsschlüssels im Header beinhaltet. 

   [!code-go[Add HTTP request function which includes passing authoring key in header.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=12-36 "Add HTTP request function, which includes passing authoring key in header. ")]

4. Fügen Sie Beispieläußerungen aus der JSON-Datei hinzu.

   [!code-go[Add example utterances from JSON file.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=62-76 "Add example utterances from JSON file.")]

5. Fordern Sie das Training an. Hierbei wird eine Hilfsfunktion verwendet, um das VERB für die gleiche Route als Trainingsstatus festzulegen. 

   [!code-go[Request training.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=77-86 "Request training. ")]

6. Fordern Sie den Trainingsstatus an. Hierbei wird eine Hilfsfunktion verwendet, um das VERB für die gleiche Route als Trainingsanforderung festzulegen. 

   [!code-go[Request training status.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=87-90 "Request training status. ")]

7. Fügen Sie die main-Funktion zum Behandeln der Befehlszeilenanalyse hinzu.

   [!code-go[Add main function to handle command line parsing.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=38-60 "Add main function to handle command-line parsing.")]

## <a name="add-an-utterance-from-the-command-line-train-and-get-status"></a>Hinzufügen einer Äußerung über die Befehlszeile, Trainieren und Abrufen des Status

1. Geben Sie in dem Verzeichnis, in dem Sie die Go-Datei erstellt haben, an einer Eingabeaufforderung `go build add-utterances.go` ein, um die Go-Datei zu kompilieren. Die Eingabeaufforderung gibt keine Informationen für eine erfolgreiche Erstellung zurück.

2. Führen Sie die Go-Anwendung über die Befehlszeile aus, indem Sie den folgenden Text an der Eingabeaufforderung eingeben: 

    ```console
    add-utterances -appID <your-app-id> -authoringKey <add-your-authoring-key> -version <your-version-id> -region westus -utteranceFile utterances.json

    ```

    Ersetzen Sie `<add-your-authoring-key>` durch den Wert Ihres Erstellungsschlüssels (auch bezeichnet als Startschlüssel). Ersetzen Sie `<your-app-id>` durch den Wert Ihrer App-ID. Ersetzen Sie `<your-version-id>` durch den Wert Ihrer Version. Die Standardversion ist `0.1`.

    Mit dieser Eingabeaufforderung werden die Ergebnisse angezeigt:

    ```console
    add example utterances requested
    [
        {
            "text": "go lang 1",
            "intentName": "None",
            "entityLabels": []
        }
    ,
        {
            "text": "go lang 2",
            "intentName": "None",
            "entityLabels": []
        }
    ]
    201
    [
        {
            "value": {
                "ExampleId": 77783998,
                "UtteranceText": "go lang 1"
            },
            "hasError": false
        },
        {
            "value": {
                "ExampleId": 77783999,
                "UtteranceText": "go lang 2"
            },
            "hasError": false
        }
    ]
    training selected
    202
    {"statusId":9,"status":"Queued"}
    training status selected
    200
    [
        {
            "modelId": "c52d6509-9261-459e-90bc-b3c872ee4a4b",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "5119cbe8-97a1-4c1f-85e6-6449f3a38d77",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "01e6b6bc-9872-47f9-8a52-da510cddfafe",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "33b409b2-32b0-4b0c-9e91-31c6cfaf93fb",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "1fb210be-2a19-496d-bb72-e0c2dd35cbc1",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "3d098beb-a1aa-423f-a0ae-ce08ced216d6",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "cce854f8-8f8f-4ed9-a7df-44dfea562f62",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "4d97bf0d-5213-4502-9712-2d6e77c96045",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        }
    ]
    ```

    Diese Antwort enthält den HTTP-Statuscode für jeden der drei HTTP-Aufrufe und alle im Text der Antwort zurückgegebenen JSON-Antworten. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Entfernen Sie nach dem Durchführen dieses Schnellstarts alle Dateien, die Sie im Rahmen der Schnellstartanleitung erstellt haben. 

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"] 
> [Erstellen einer App mit einer benutzerdefinierten Domäne](luis-quickstart-intents-only.md) 
