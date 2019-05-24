---
title: Veröffentlichen einer Wissensdatenbank – REST, Go
titleSuffix: QnA Maker - Azure Cognitive Services
description: In dieser REST-basierten Schnellstartanleitung für Go wird Schritt für Schritt erläutert, wie Sie Ihre Wissensdatenbank veröffentlichen. Durch das Veröffentlichen wird die aktuelle Version der getesteten Wissensdatenbank in einen dedizierten Azure Search-Index gepusht, der die veröffentlichte Wissensdatenbank darstellt. Außerdem wird ein Endpunkt erstellt, der in Ihrer Anwendung oder Ihrem Chatbot aufgerufen werden kann.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 5c0d3c5d33d41ddd01b9d0c0ccf4f468d52f6a9e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790797"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Schnellstart: Veröffentlichen einer Wissensdatenbank in QnA Maker mit Go

In diesem REST-basierten Schnellstart wird das programmgesteuerte Veröffentlichen Ihrer Wissensdatenbank (Knowledge Base, KB) Schritt für Schritt beschrieben. Bei der Veröffentlichung wird die aktuelle Version der Wissensdatenbank per Pushvorgang an einen dedizierten Azure Search-Index übertragen und ein Endpunkt erstellt, der in Ihrer Anwendung oder Ihrem Chatbot aufgerufen werden kann.

In dieser Schnellstartanleitung werden QnA Maker-APIs aufgerufen:
* [Veröffentlichen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish): Für diese API sind keine Informationen im Text der Anforderung erforderlich.

## <a name="prerequisites"></a>Voraussetzungen

* [Go 1.10.1](https://golang.org/dl/)
* Sie benötigen einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md). Wählen Sie zum Abrufen des Schlüssels auf Ihrem Dashboard unter **Ressourcenverwaltung** die Option **Schlüssel** aus. 

* ID der QnA Maker-Wissensdatenbank, die in der URL des Abfragezeichenfolgenparameters „kbid“ enthalten ist, wie nachfolgend gezeigt:

    ![ID der QnA Maker-Wissensdatenbank](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Falls Sie noch keine Wissensdatenbank besitzen, können Sie für diese Schnellstartanleitung eine Beispieldatenbank erstellen: [Erstellen einer neuen Wissensdatenbank](create-new-kb-csharp.md).

> [!NOTE] 
> Die vollständigen Projektmappendateien sind im [GitHub-Repository **Azure-Samples/cognitive-services-qnamaker-go**](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base) verfügbar.

## <a name="create-a-go-file"></a>Erstellen einer Go-Datei

Öffnen Sie Visual Studio Code, und erstellen Sie eine neue Datei mit dem Namen `publish-kb.go`.

## <a name="add-the-required-dependencies"></a>Hinzufügen der erforderlichen Abhängigkeiten

Fügen Sie oben in der Datei `publish-kb.go` die folgenden Zeilen hinzu, um dem Projekt die erforderlichen Abhängigkeiten hinzuzufügen:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=3-7 "Add the required dependencies")]

## <a name="create-the-main-function"></a>Erstellen der main-Funktion

Fügen Sie nach den erforderlichen Abhängigkeiten die folgende Klasse hinzu:

```Go
package main

func main() {

}
```

## <a name="add-required-constants"></a>Hinzufügen von erforderlichen Konstanten

Fügen Sie innerhalb der **main**-Funktion


 die erforderlichen Konstanten für den Zugriff auf QnA Maker hinzu. Ersetzen Sie die Werte dabei durch Ihre eigenen Werte.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=16-20 "Add the required constants")]

## <a name="add-post-request-to-publish-kb"></a>Hinzufügen einer POST-Anforderung zur Veröffentlichung der Wissensdatenbank

Fügen Sie nach den erforderlichen Konstanten den folgenden Code hinzu, der eine HTTPS-Anforderung an die QnA Maker-API sendet, um eine Wissensdatenbank zu veröffentlichen, und die Antwort empfängt:

[!code-go[Add a POST request to publish KB](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to publish KB")]

Der API-Aufruf gibt den Status 204 für eine erfolgreiche Veröffentlichung ohne Inhalt im Text der Antwort zurück. Mit dem Code wird Inhalt für Antworten vom Typ 204 hinzugefügt.

Für alle anderen Antworten wird diese Antwort unverändert zurückgegeben.

## <a name="build-and-run-the-program"></a>Erstellen und Ausführen des Programms

Geben Sie den folgenden Befehl ein, um die Datei zu kompilieren. Die Eingabeaufforderung gibt keine Informationen für eine erfolgreiche Erstellung zurück.

```bash
go build publish-kb.go
```

Geben Sie den folgenden Befehl in einer Befehlszeile ein, um das Programm auszuführen. Der Befehl sendet die Anforderung an die QnA Maker-API, um die Wissensdatenbank zu veröffentlichen, und gibt dann 204 für die erfolgreiche Ausführung oder Fehler aus.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Nächste Schritte

Nachdem die Wissensdatenbank veröffentlicht wurde, benötigen Sie die [Endpunkt-URL, um eine Antwort zu generieren](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [REST-API-Referenz für QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
