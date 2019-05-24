---
title: 'Erstellen einer Wissensdatenbank: REST, C#'
titlesuffix: QnA Maker- Azure Cognitive Services
description: Diese C#-REST-basierte Schnellstartanleitung führt Sie durch das programmgesteuerte Erstellen einer Beispielwissensdatenbank für QnA Maker, die auf dem Azure-Dashboard Ihres Cognitive Services-API-Kontos angezeigt wird.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: diberry
ms.openlocfilehash: c1771224c8e6ee8a9fdca3ab2fba59411ac92a27
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788526"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c"></a>Schnellstart: Erstellen einer Wissensdatenbank in QnA Maker mithilfe von C#

In dieser Schnellstartanleitung wird Schritt für Schritt das programmgesteuerte Erstellen und Veröffentlichen eines Beispiels für eine QnA Maker-Wissensdatenbank beschrieben. QnA Maker extrahiert automatisch Fragen und Antworten aus teilweise strukturiertem Inhalt (z.B. häufig gestellten Fragen) von [Datenquellen](../Concepts/data-sources-supported.md). Das Modell für die Wissensdatenbank wird im JSON-Code definiert, der im Text der API-Anforderung gesendet wird. 

In dieser Schnellstartanleitung werden QnA Maker-APIs aufgerufen:
* [Erstellen einer Wissensdatenbank](https://go.microsoft.com/fwlink/?linkid=2092179)
* [Abrufen von Vorgangsdetails](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)
* [Veröffentlichen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) 

## <a name="prerequisites"></a>Voraussetzungen

* Aktuelle [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/)
* Sie benötigen einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md). Wählen Sie zum Abrufen des Schlüssels auf Ihrem Dashboard unter **Ressourcenverwaltung** die Option **Schlüssel** aus. 

> [!NOTE] 
> Die vollständigen Projektmappendateien sind über das [GitHub-Repository **Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp) verfügbar.

## <a name="create-a-knowledge-base-project"></a>Erstellen eines Wissensdatenbank-Projekts

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>Hinzufügen der erforderlichen Abhängigkeiten

Ersetzen Sie am Anfang von „Program.cs“ die einzelne using-Anweisung durch die folgenden Zeilen, um dem Projekt die erforderlichen Abhängigkeiten hinzuzufügen:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Hinzufügen der erforderlichen Konstanten

Fügen Sie am Anfang der Program-Klasse die folgenden Konstanten hinzu, um auf QnA Maker zuzugreifen:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=17-24 "Add the required constants")]

## <a name="add-the-kb-definition"></a>Hinzufügen der Definition der Wissensdatenbank

Fügen Sie nach den Konstanten die folgende Definition für die Wissensdatenbank hinzu:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=32-57 "Add the required constants")]

## <a name="add-supporting-functions-and-structures"></a>Hinzuzufügen von unterstützenden Funktionen und Strukturen
Fügen Sie innerhalb der Program-Klasse den folgenden Codeblock hinzu:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=62-82 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>Hinzufügen einer POST-Anforderung zum Erstellen der Wissensdatenbank

Der folgende Code sendet eine HTTPS-Anforderung an die QnA Maker-API, um eine Wissensdatenbank zu erstellen, und empfängt die Antwort:

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=91-105 "Add a POST request to create KB")]

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

## <a name="add-get-request-to-determine-creation-status"></a>Hinzufügen einer GET-Anforderung zum Ermitteln des Erstellungsstatus

Überprüfen Sie den Status des Vorgangs.

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=159-170 "Add GET request to determine creation status")]

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

## <a name="add-createkb-method"></a>Hinzufügen der CreateKB-Methode

Mit der folgenden Methode wird die Wissensdatenbank erstellt, und der Status wird wiederholt überprüft.  Die **Vorgangs-ID** für _create_ wird im Feld **Location** des POST-Antwortheaders zurückgegeben und anschließend als Teil der Route in der GET-Anforderung verwendet. Da die Erstellung der Wissensdatenbank einige Zeit dauern kann, müssen Sie die Aufrufe zum Überprüfen des Status wiederholen, bis der Status entweder „Erfolgreich“ oder „Fehler“ lautet. Wenn der Vorgang erfolgreich war, wird die ID der Wissensdatenbank in **resourceLocation** zurückgegeben. 

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=176-237 "Add CreateKB method")]

## <a name="add-the-createkb-method-to-main"></a>Hinzufügen der CreateKB-Methode zu „Main“

Ändern Sie die Main-Methode, um die CreateKB-Methode aufzurufen:

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=239-248 "Add CreateKB method")]

## <a name="build-and-run-the-program"></a>Erstellen und Ausführen des Programms

Erstellen Sie das Programm, und führen Sie es aus. Die Anforderung wird automatisch an die QnA Maker-API gesendet, um die Wissensdatenbank zu erstellen, und dann werden die Ergebnisse alle 30 Sekunden abgefragt. Jede Antwort wird im Konsolenfenster ausgegeben.

Nach der Erstellung der Wissensdatenbank können Sie sie im QnA Maker-Portal auf der Seite [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Meine Wissensdatenbanken) anzeigen. 


[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz für QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
