---
title: 'Schnellstart: Erstellen der Wissensdatenbank – REST, Node.js – QnA Maker'
description: Diese REST-basierte Schnellstartanleitung führt Sie durch das programmgesteuerte Erstellen einer Beispielwissensdatenbank für QnA Maker, die auf dem Azure-Dashboard Ihres Cognitive Services-API-Kontos angezeigt wird.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: diberry
ms.openlocfilehash: e2ab1a37115ab65cddbb65d28402d431a615b5f1
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788586"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-nodejs"></a>Schnellstart: Erstellen einer Wissensdatenbank in QnA Maker mit Node.js

In dieser Schnellstartanleitung wird Schritt für Schritt das programmgesteuerte Erstellen und Veröffentlichen eines Beispiels für eine QnA Maker-Wissensdatenbank beschrieben. QnA Maker extrahiert automatisch Fragen und Antworten aus teilweise strukturiertem Inhalt (z.B. häufig gestellten Fragen) von [Datenquellen](../Concepts/data-sources-supported.md). Das Modell für die Wissensdatenbank wird im JSON-Code definiert, der im Text der API-Anforderung gesendet wird. 

In dieser Schnellstartanleitung werden QnA Maker-APIs aufgerufen:
* [Erstellen einer Wissensdatenbank](https://go.microsoft.com/fwlink/?linkid=2092179)
* [Abrufen von Vorgangsdetails](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

## <a name="prerequisites"></a>Voraussetzungen

* [Node.js 6+](https://nodejs.org/en/download/)
* Sie benötigen einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md). Wählen Sie zum Abrufen des Schlüssels auf Ihrem Dashboard unter **Ressourcenverwaltung** die Option **Schlüssel** aus. 

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>Erstellen einer Node.js-Datei für die Wissensdatenbank

Erstellen Sie eine Datei mit dem Namen `create-new-knowledge-base.js`.

## <a name="add-the-required-dependencies"></a>Hinzufügen der erforderlichen Abhängigkeiten

Fügen Sie oben in der Datei `create-new-knowledge-base.js` die folgenden Zeilen hinzu, um dem Projekt die erforderlichen Abhängigkeiten hinzuzufügen:

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-the-required-constants"></a>Hinzufügen der erforderlichen Konstanten
Fügen Sie nach den obigen erforderlichen Abhängigkeiten die erforderlichen Konstanten für den Zugriff auf QnA Maker hinzu. Ersetzen Sie den Wert der Variablen `subscriptionKey` durch Ihren eigenen QnA Maker-Schlüssel.

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=10-19 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Hinzufügen der KB-Modelldefinition

Fügen Sie nach den Konstanten die folgende Modelldefinition für die Wissensdatenbank hinzu. Das Modell wird nach der Definition in eine Zeichenfolge konvertiert.

[!code-nodejs[Add the KB model definition](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=21-51 "Add the KB model definition")]

## <a name="add-supporting-functions"></a>Hinzufügen von unterstützenden Funktionen

Als Nächstes fügen Sie die folgenden unterstützenden Funktionen hinzu.

1. Fügen Sie die folgende Funktion zum Ausgeben von JSON in einem lesbaren Format hinzu:

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=53-56 "Add supporting functions, step 1")]

2. Fügen Sie die folgenden Funktionen zum Verwalten der HTTP-Antwort hinzu:

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=58-80 "Add supporting functions, step 2")]

## <a name="add-functions-to-create-kb"></a>Hinzufügen von Funktionen zum Erstellen der Wissensdatenbank

Fügen Sie die folgenden Funktionen hinzu, um eine HTTP POST-Anforderung zum Erstellen der Wissensdatenbank zu senden. `Ocp-Apim-Subscription-Key` ist der QnA Maker-Dienstschlüssel, der für die Authentifizierung verwendet wird. 

[!code-nodejs[POST Request to API](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=82-109 "POST Request to API")]

Mit diesem API-Aufruf wird eine JSON-Antwort zurückgegeben, die die Vorgangs-ID enthält. Verwenden Sie die Vorgangs-ID, um zu ermitteln, ob die Erstellung der Wissensdatenbank erfolgreich war. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-functions-to-determine-creation-status"></a>Hinzufügen von Funktionen zum Ermitteln des Erstellungsstatus

Fügen Sie die folgende Funktion hinzu, um eine HTTP GET-Anforderung zum Überprüfen des Vorgangsstatus zu senden. `Ocp-Apim-Subscription-Key` ist der QnA Maker-Dienstschlüssel, der für die Authentifizierung verwendet wird. 

[!code-nodejs[Determine creation status](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=112-135 "Determine creation status")]

Wiederholen Sie den Aufruf, bis er erfolgreich abgeschlossen wird oder ein Fehler auftritt: 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```


## <a name="add-create-kb-function"></a>Hinzufügen der Funktion „create-kb“

Die folgende Funktion ist die Hauptfunktion, mit der die Wissensdatenbank erstellt und wiederholt der Status überprüft wird. Die **Vorgangs-ID** für _create_ wird im Feld **Location** des POST-Antwortheaders zurückgegeben und anschließend als Teil der Route in der GET-Anforderung verwendet. Da die Erstellung der Wissensdatenbank einige Zeit dauern kann, müssen Sie die Aufrufe zum Überprüfen des Status wiederholen, bis der Status entweder „Erfolgreich“ oder „Fehler“ lautet.

[!code-nodejs[Add create-kb function](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=137-167 "Add create-kb function")]

## <a name="run-the-program"></a>Ausführen des Programms

Geben Sie den folgenden Befehl in einer Befehlszeile ein, um das Programm auszuführen. Hiermit wird die Anforderung an die QnA Maker-API gesendet, um die Wissensdatenbank zu erstellen, und dann werden alle 30 Sekunden die Ergebnisse abgefragt. Jede Antwort wird im Konsolenfenster ausgegeben.

```bash
node create-new-knowledge-base.js
```

Nach der Erstellung der Wissensdatenbank können Sie sie im QnA Maker-Portal auf der Seite [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Meine Wissensdatenbanken) anzeigen. 

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz für QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
