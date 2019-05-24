---
title: 'Schnellstart: Erstellen einer Wissensdatenbank – REST, Python – QnA Maker'
titlesuffix: Azure Cognitive Services
description: Diese Python-basierte Schnellstartanleitung für Java führt Sie durch das programmgesteuerte Erstellen einer Beispielwissensdatenbank für QnA Maker, die auf dem Azure-Dashboard Ihres Cognitive Services-API-Kontos angezeigt wird.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: 9aa5b85a00de495478897ec70393f6c22ee20fc5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788753"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-python"></a>Schnellstart: Erstellen einer Wissensdatenbank in QnA Maker mithilfe von Python

In dieser Schnellstartanleitung wird Schritt für Schritt das programmgesteuerte Erstellen und Veröffentlichen eines Beispiels für eine QnA Maker-Wissensdatenbank beschrieben. QnA Maker extrahiert automatisch Fragen und Antworten aus teilweise strukturiertem Inhalt (z.B. häufig gestellten Fragen) von [Datenquellen](../Concepts/data-sources-supported.md). Das Modell für die Wissensdatenbank wird im JSON-Code definiert, der im Text der API-Anforderung gesendet wird. 

In dieser Schnellstartanleitung werden QnA Maker-APIs aufgerufen:
* [Erstellen einer Wissensdatenbank](https://go.microsoft.com/fwlink/?linkid=2092179)
* [Abrufen von Vorgangsdetails](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

## <a name="prerequisites"></a>Voraussetzungen

* [Python 3.7](https://www.python.org/downloads/)
* Sie benötigen einen QnA Maker-Dienst. Wählen Sie zum Abrufen des Schlüssels auf Ihrem Dashboard unter „Ressourcenverwaltung“ die Option „Schlüssel“ aus.

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

## <a name="create-a-knowledge-base-python-file"></a>Erstellen einer Python-Datei für die Wissensdatenbank

Erstellen Sie eine Datei mit dem Namen `create-new-knowledge-base-3x.py`.

## <a name="add-the-required-dependencies"></a>Hinzufügen der erforderlichen Abhängigkeiten

Fügen Sie oben in der Datei `create-new-knowledge-base-3x.py` die folgenden Zeilen hinzu, um dem Projekt die erforderlichen Abhängigkeiten hinzuzufügen:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Hinzufügen der erforderlichen Konstanten
Fügen Sie nach den obigen erforderlichen Abhängigkeiten die erforderlichen Konstanten für den Zugriff auf QnA Maker hinzu. Ersetzen Sie den Wert der Variablen `subscriptionKey` durch Ihren eigenen QnA Maker-Schlüssel.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=5-13 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Hinzufügen der KB-Modelldefinition

Fügen Sie nach den Konstanten die folgende Modelldefinition für die Wissensdatenbank hinzu. Das Modell wird nach der Definition in eine Zeichenfolge konvertiert.

[!code-python[Add the KB model definition](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=15-41 "Add the KB model definition")]

## <a name="add-supporting-function"></a>Hinzufügen einer unterstützenden Funktion

Fügen Sie die folgende Funktion zum Ausgeben von JSON in einem lesbaren Format hinzu:

[!code-python[Add supporting function](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=43-45 "Add supporting function")]

## <a name="add-function-to-create-kb"></a>Hinzufügen einer Funktion zum Erstellen der Wissensdatenbank

Fügen Sie die folgende Funktion hinzu, um eine HTTP-POST-Anforderung zum Erstellen der Wissensdatenbank zu übermitteln. Dieser API-Aufruf gibt eine JSON-Antwort zurück, die die Vorgangs-ID im Headerfeld **Location** enthält. Verwenden Sie die Vorgangs-ID, um zu ermitteln, ob die Erstellung der Wissensdatenbank erfolgreich war. `Ocp-Apim-Subscription-Key` ist der QnA Maker-Dienstschlüssel, der für die Authentifizierung verwendet wird. 

[!code-python[Add function to create KB](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=48-59 "Add function to create KB")]

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

## <a name="add-function-to-check-creation-status"></a>Hinzufügen einer Funktion zum Überprüfen des Erstellungsstatus

Die folgende Funktion überprüft den Erstellungsstatus und sendet dazu die Vorgangs-ID am Ende der URL-Route. Der Aufruf von `check_status` erfolgt innerhalb der Hauptschleife vom Typ _while_.

[!code-python[Add function to check creation status](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=61-67 "Add function to check creation status")]

Dieser API-Aufruf gibt eine JSON-Antwort zurück, die den Vorgangsstatus enthält: 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

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

## <a name="add-main-code-block"></a>Hinzufügen des Hauptcodeblocks
Die folgende Schleife fragt in regelmäßigen Abständen den Status des Erstellungsvorgangs ab, bis der Vorgang abgeschlossen ist. 

[!code-python[Add main code block](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=70-96 "Add main code block")]

## <a name="build-and-run-the-program"></a>Erstellen und Ausführen des Programms

Geben Sie den folgenden Befehl in einer Befehlszeile ein, um das Programm auszuführen. Hiermit wird die Anforderung an die QnA Maker-API gesendet, um die Wissensdatenbank zu erstellen, und dann werden alle 30 Sekunden die Ergebnisse abgefragt. Jede Antwort wird im Konsolenfenster ausgegeben.

```bash
python create-new-knowledge-base-3x.py
```

Nach der Erstellung der Wissensdatenbank können Sie sie im QnA Maker-Portal auf der Seite [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Meine Wissensdatenbanken) anzeigen. Wählen Sie zum Anzeigen den Namen Ihrer Wissensdatenbank aus, etwa „QnA Maker FAQ“.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz für QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
