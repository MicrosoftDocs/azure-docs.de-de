---
title: 'Schnellstart: Abrufen von Antworten aus einer Wissensdatenbank mit cURL – QnA Maker'
titleSuffix: Azure Cognitive Services
description: In diesem Schnellstart wird Schritt für Schritt erläutert, wie Sie mit cURL eine Antwort aus einer Wissensdatenbank abrufen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: 817a19d5cabd7d20dc17154f29b4430e6b96c5fa
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072043"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>Schnellstart: Abrufen einer Antwort aus einer Wissensdatenbank mit cURL

In diesem Schnellstart auf cURL-Basis wird Schritt für Schritt erläutert, wie Sie eine Antwort aus einer Wissensdatenbank abrufen.

## <a name="prerequisites"></a>Voraussetzungen

* Neueste Version von [**cURL**](https://curl.haxx.se/).
* Sie benötigen einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md) und eine [Wissensdatenbank mit Fragen und Antworten](../Tutorials/create-publish-query-in-portal.md).

## <a name="publish-to-get-endpoint"></a>Veröffentlichen zum Abrufen des Endpunkts

Wenn Sie bereit sind, eine Antwort auf eine Frage aus Ihrer Wissensdatenbank zu generieren, [veröffentlichen](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) Sie Ihre Wissensdatenbank.

## <a name="use-production-endpoint-with-curl"></a>Verwenden des Produktionsendpunkts mit cURL

Wenn Ihre Wissensdatenbank veröffentlicht wird, werden auf der Seite **Veröffentlichen** die HTTP-Anforderungseinstellungen zum Generieren einer Antwort angezeigt. Die Registerkarte **CURL** zeigt die Einstellungen, die zum Generieren einer Antwort über das Befehlszeilentool [cURL](https://www.getpostman.com) erforderlich sind.

[![Veröffentlichen der Ergebnisse](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

Um eine Antwort mit cURL zu generieren, führen Sie die folgenden Schritte aus:

1. Kopieren Sie den Text auf der Registerkarte „CURL“. 
1. Öffnen Sie eine Befehlszeile oder ein Terminal, und fügen Sie den Text ein.
1. Bearbeiten Sie die Frage, um sie für Ihre Wissensdatenbank relevant zu machen. Achten Sie darauf, nicht den JSON-Code um die Frage zu entfernen.
1. Geben Sie den Befehl ein. 
1. Die Antwort enthält die relevanten Informationen über die Antwort. 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
          "metadata": [
            {
              "name": "category",
              "value": "api"
            }
          ]
        }
      ]
    }
    ```

## <a name="use-staging-endpoint-with-curl"></a>Verwenden des Stagingendpunkts mit cURL

Wenn Sie eine Antwort vom Stagingendpunkt erhalten möchten, verwenden Sie die `isTest`-Texteigenschaft.

```json
isTest:true
```

## <a name="next-steps"></a>Nächste Schritte

Die Veröffentlichungsseite enthält auch Informationen zum [Generieren einer Antwort](get-answer-from-kb-using-postman.md) mit Postman. 

> [!div class="nextstepaction"]
> [Verwenden von Metadaten beim Generieren einer Antwort](../How-to/metadata-generateanswer-usage.md)
