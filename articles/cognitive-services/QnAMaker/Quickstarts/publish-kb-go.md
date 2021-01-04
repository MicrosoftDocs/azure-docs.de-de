---
title: 'Schnellstart: Veröffentlichen einer Wissensdatenbank, REST, Go – QnA Maker'
description: Diese auf Go REST basierende Schnellstartanleitung veröffentlicht Ihre Wissensdatenbank und erstellt einen Endpunkt, der in Ihrer Anwendung oder Ihrem Chatbot aufgerufen werden kann.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: d876be45789041cddba269784a9c1e62e5907bc2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352251"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Schnellstart: Veröffentlichen einer Wissensdatenbank in QnA Maker mit Go

In diesem REST-basierten Schnellstart wird das programmgesteuerte Veröffentlichen Ihrer Wissensdatenbank (Knowledge Base, KB) Schritt für Schritt beschrieben. Bei der Veröffentlichung wird die aktuelle Version der Wissensdatenbank per Pushvorgang an einen dedizierten Azure Cognitive Search-Index übertragen und ein Endpunkt erstellt, der in Ihrer Anwendung oder Ihrem Chatbot aufgerufen werden kann.

In dieser Schnellstartanleitung werden QnA Maker-APIs aufgerufen:
* [Veröffentlichen](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish): Für diese API sind keine Informationen im Text der Anforderung erforderlich.

## <a name="prerequisites"></a>Voraussetzungen

* [Go 1.10.1](https://golang.org/dl/)
* Sie benötigen einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md). Wählen Sie für Ihre Ressource im Azure-Portal die Option **Schnellstart** aus, um den Schlüssel und den Endpunkt (der den Ressourcennamen enthält) abzurufen.

* ID der QnA Maker-Wissensdatenbank, die in der URL des Abfragezeichenfolgenparameters `kbid` enthalten ist, wie nachfolgend gezeigt.

    ![ID der QnA Maker-Wissensdatenbank](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Falls Sie noch keine Wissensdatenbank besitzen, können Sie für diese Schnellstartanleitung eine Beispieldatenbank erstellen: [Erstellen einer neuen Wissensdatenbank](create-new-kb-csharp.md).

> [!NOTE]
> Die vollständigen Projektmappendateien sind im [GitHub-Repository **Azure-Samples/cognitive-services-qnamaker-go**](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base) verfügbar.

## <a name="create-a-go-file"></a>Erstellen einer Go-Datei

Öffnen Sie Visual Studio Code, und erstellen Sie eine neue Datei mit dem Namen `publish-kb.go`.

## <a name="add-the-required-dependencies"></a>Hinzufügen der erforderlichen Abhängigkeiten

Fügen Sie oben in der Datei `publish-kb.go` die folgenden Zeilen hinzu, um dem Projekt die erforderlichen Abhängigkeiten hinzuzufügen:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="dependencies":::

## <a name="create-the-main-function"></a>Erstellen der main-Funktion

Fügen Sie nach den erforderlichen Abhängigkeiten die folgende Klasse hinzu:

```Go
package main

func main() {

}
```

## <a name="add-post-request-to-publish-kb"></a>Hinzufügen einer POST-Anforderung zur Veröffentlichung der Wissensdatenbank

Fügen Sie den folgenden Code hinzu, der eine HTTPS-Anforderung an die QnA Maker-API sendet, um eine Wissensdatenbank zu veröffentlichen, und die Antwort empfängt:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="main":::

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

Nachdem die Wissensdatenbank veröffentlicht wurde, benötigen Sie die [Endpunkt-URL, um eine Antwort zu generieren](./get-answer-from-knowledge-base-go.md).

> [!div class="nextstepaction"]
> [REST-API-Referenz für QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)