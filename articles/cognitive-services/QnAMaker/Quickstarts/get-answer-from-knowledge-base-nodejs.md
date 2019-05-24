---
title: 'Schnellstart: Abrufen von Antworten aus einer Wissensdatenbank – REST, Node.js – QnA Maker'
titlesuffix: Azure Cognitive Services
description: In diesem Node.js-REST-basierten Schnellstart wird Schritt für Schritt erläutert, wie Sie programmgesteuert eine Antwort auf eine Frage aus einer Wissensdatenbank abrufen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 389d6ed369de513125a2bcd0d0de881f524dcd82
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791539"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-nodejs"></a>Abrufen von Antworten auf Fragen aus einer Wissensdatenbank mit Node.js

In diesem Schnellstart wird Schritt für Schritt erläutert, wie Sie programmgesteuert eine Antwort auf eine Frage aus einer veröffentlichten QnA Maker-Wissensdatenbank abrufen. Die Wissensdatenbank enthält Fragen und Antworten aus [Datenquellen](../Concepts/data-sources-supported.md) (beispielsweise häufig gestellte Fragen). Die [Frage](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) wird an den QnA Maker-Dienst gesendet. Die [Antwort](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) enthält die Antwort mit der bestbewerteten Vorhersage. 

## <a name="prerequisites"></a>Voraussetzungen

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Sie benötigen einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md). Wählen Sie zum Abrufen Ihres Schlüssels im Azure-Dashboard für Ihre QnA Maker-Ressource unter **Ressourcenverwaltung** die Option **Schlüssel** aus. 
* Einstellungen auf der Seite **Veröffentlichen**. Wenn Sie keine veröffentlichte Wissensdatenbank haben, erstellen Sie eine leere Wissensdatenbank. Importieren Sie anschließend auf der Seite **Einstellungen** eine Wissensdatenbank, und veröffentlichen Sie sie. Sie können [diese einfache Wissensdatenbank](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv) herunterladen und verwenden. 

    Zu den Einstellungen auf der Seite „Veröffentlichen“ zählen der Wert für die POST-Route, der Hostwert und der EndpointKey-Wert. 

    ![Veröffentlichungseinstellungen](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Der Code für diesen Schnellstart ist im Repository unter [https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/get-answer) verfügbar. 

## <a name="create-a-nodejs-file"></a>Erstellen einer Node.js-Datei

Öffnen Sie Visual Studio Code, und erstellen Sie eine neue Datei mit dem Namen `get-answer.js`. 

## <a name="add-the-required-dependencies"></a>Hinzufügen der erforderlichen Abhängigkeiten

Fügen Sie dem Projekt am Anfang der Datei `get-answer.js` die erforderlichen Abhängigkeiten hinzu:

[!code-nodejs[Add the required dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=1-4 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Hinzufügen der erforderlichen Konstanten

Fügen Sie anschließend die erforderlichen Konstanten für den Zugriff auf QnA Maker hinzu. Diese Werte werden auf der Seite **Veröffentlichen** angezeigt, nachdem Sie die Wissensdatenbank veröffentlicht haben. 

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=6-22 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Hinzufügen einer POST-Anforderung zum Senden einer Frage und Abrufen der Antwort

Der folgende Code sendet eine HTTPS-Anforderung an die QnA Maker-API, um die Frage an die Wissensdatenbank zu senden, und empfängt die Antwort:

[!code-nodejs[Add a POST request to send question to knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=24-49 "Add a POST request to send question to knowledge base")]

Der Wert des `Authorization`-Headers enthält die Zeichenfolge `EndpointKey`. 

## <a name="install-the-dependencies"></a>Installieren der Abhängigkeiten

Installieren Sie die Abhängigkeiten über die Befehlszeile:

```bash
npm install request request-promise
```

## <a name="run-the-program"></a>Ausführen des Programms

Führen Sie das Programm über die Befehlszeile aus. Das Programm sendet die Anforderung automatisch an die QnA Maker-API und zeigt die Antwort im Konsolenfenster an.

Führen Sie die Datei aus:

```bash
node get-answer.js
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

Informieren Sie sich ausführlicher über [Anforderung](../how-to/metadata-generateanswer-usage.md#generateanswer-request) und [Antwort](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz für QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
