---
title: 'Schnellstart: Veröffentlichen einer Wissensdatenbank, REST, C# – QnA Maker'
description: Diese auf C# REST basierende Schnellstartanleitung veröffentlicht Ihre Wissensdatenbank und erstellt einen Endpunkt, der in Ihrer Anwendung oder Ihrem Chatbot aufgerufen werden kann.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-csharp
ms.topic: how-to
ms.openlocfilehash: abaccfb1a1f36e1f7f47d3f5acefb08e2091cb39
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351179"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Schnellstart: Veröffentlichen einer Wissensdatenbank in QnA Maker mit C#

In diesem REST-basierten Schnellstart wird das programmgesteuerte Veröffentlichen Ihrer Wissensdatenbank (Knowledge Base, KB) Schritt für Schritt beschrieben. Bei der Veröffentlichung wird die aktuelle Version der Wissensdatenbank per Pushvorgang an einen dedizierten Azure Cognitive Search-Index übertragen und ein Endpunkt erstellt, der in Ihrer Anwendung oder Ihrem Chatbot aufgerufen werden kann.

In dieser Schnellstartanleitung werden QnA Maker-APIs aufgerufen:
* [Veröffentlichen](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish): Für diese API sind keine Informationen im Text der Anforderung erforderlich.

## <a name="prerequisites"></a>Voraussetzungen

* Aktuelle [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/)
* Sie benötigen einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md). Wählen Sie für Ihre Ressource im Azure-Portal die Option **Schnellstart** aus, um den Schlüssel und den Endpunkt (der den Ressourcennamen enthält) abzurufen.
* ID der QnA Maker-Wissensdatenbank, die in der URL des Abfragezeichenfolgenparameters `kbid` enthalten ist, wie nachfolgend gezeigt.

    ![ID der QnA Maker-Wissensdatenbank](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Falls Sie noch keine Wissensdatenbank besitzen, können Sie für diese Schnellstartanleitung eine Beispieldatenbank erstellen: [Erstellen einer neuen Wissensdatenbank](create-new-kb-csharp.md).

> [!NOTE]
> Die vollständigen Projektmappendateien sind über das [GitHub-Repository **Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base) verfügbar.

## <a name="create-knowledge-base-project"></a>Erstellen eines Wissensdatenbank-Projekts

1. Öffnen Sie Visual Studio 2019 Community Edition.
1. Erstellen Sie ein neues Projekt vom Typ **Konsolen-App (.NET Core)** , und nennen Sie es `QnaMakerQuickstart`. Übernehmen Sie für die übrigen Einstellungen die Standardwerte.

## <a name="add-required-dependencies"></a>Hinzufügen von erforderlichen Abhängigkeiten

Ersetzen Sie am Anfang von „Program.cs“ die einzelne using-Anweisung durch die folgenden Zeilen, um dem Projekt die erforderlichen Abhängigkeiten hinzuzufügen:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="dependencies":::

## <a name="add-required-constants"></a>Hinzufügen von erforderlichen Konstanten

Fügen Sie am Anfang der **Program**-Klasse die erforderlichen Konstanten für den Zugriff auf QnA Maker hinzu.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="constants":::

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>Hinzufügen der Main-Methode zum Veröffentlichen der Wissensdatenbank

Fügen Sie nach den erforderlichen Konstanten den folgenden Code hinzu, der eine HTTPS-Anforderung an die QnA Maker-API sendet, um eine Wissensdatenbank zu veröffentlichen, und die Antwort empfängt:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="post":::

Der API-Aufruf gibt den Status 204 für eine erfolgreiche Veröffentlichung ohne Inhalt im Text der Antwort zurück.

## <a name="build-and-run-the-program"></a>Erstellen und Ausführen des Programms

Erstellen Sie das Programm, und führen Sie es aus. Die Anforderung wird automatisch an die QnA Maker-API gesendet, um die Wissensdatenbank zu veröffentlichen. Anschließend wird die Antwort im Konsolenfenster angezeigt.

Nachdem Ihre Wissensdatenbank veröffentlicht wurde, können Sie sie vom Endpunkt mit einer Clientanwendung oder einem Chatbot abfragen.

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem die Wissensdatenbank veröffentlicht wurde, benötigen Sie die [Endpunkt-URL, um eine Antwort zu generieren](./get-answer-from-knowledge-base-csharp.md).

> [!div class="nextstepaction"]
> [REST-API-Referenz für QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)