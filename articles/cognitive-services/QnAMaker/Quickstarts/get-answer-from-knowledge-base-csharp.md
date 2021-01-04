---
title: 'Schnellstart: Abrufen von Antworten aus einer Wissensdatenbank – REST, C# – QnA Maker'
description: In diesem C#-REST-basierten Schnellstart wird Schritt für Schritt erläutert, wie Sie programmgesteuert eine Antwort auf eine Frage aus einer Wissensdatenbank abrufen.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-csharp
ms.topic: how-to
ms.openlocfilehash: 0e41bc819f63a54f37087f71a50b11088f4578ae
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96346047"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-c"></a>Schnellstart: Abrufen von Antworten auf Fragen aus einer Wissensdatenbank mit C#

In diesem Schnellstart wird Schritt für Schritt erläutert, wie Sie programmgesteuert eine Antwort auf eine Frage aus einer veröffentlichten QnA Maker-Wissensdatenbank abrufen. Die Wissensdatenbank enthält Fragen und Antworten aus [Datenquellen](../index.yml) (beispielsweise häufig gestellte Fragen). Die [Frage](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) wird an den QnA Maker-Dienst gesendet. Die [Antwort](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) enthält die Antwort mit der bestbewerteten Vorhersage.

[Referenzdokumentation](/rest/api/cognitiveservices/qnamaker4.0/Runtime) | [Beispiel](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs)

## <a name="prerequisites"></a>Voraussetzungen

* Aktuelle [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/)
* Sie benötigen einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md). Wählen Sie zum Abrufen Ihres Schlüssels im Azure-Dashboard für Ihre QnA Maker-Ressource unter **Ressourcenverwaltung** die Option **Schlüssel** aus.
* Einstellungen auf der Seite **Veröffentlichen**. Wenn Sie keine veröffentlichte Wissensdatenbank haben, erstellen Sie eine leere Wissensdatenbank. Importieren Sie anschließend auf der Seite **Einstellungen** eine Wissensdatenbank, und veröffentlichen Sie sie. Sie können [diese einfache Wissensdatenbank](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv) herunterladen und verwenden.

    Zu den Einstellungen auf der Seite „Veröffentlichen“ zählen der Wert für die POST-Route, der Hostwert und der EndpointKey-Wert.

    ![Veröffentlichungseinstellungen](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-knowledge-base-project"></a>Erstellen eines Wissensdatenbank-Projekts

1. Öffnen Sie Visual Studio 2019 Community Edition.
1. Erstellen Sie ein neues Projekt vom Typ „Konsolen-App (.NET Core)“, und nennen Sie es „QnaMakerQuickstart“. Übernehmen Sie für die übrigen Einstellungen die Standardwerte.

## <a name="add-the-required-dependencies"></a>Hinzufügen der erforderlichen Abhängigkeiten

Ersetzen Sie am Anfang der Datei „Program.cs“ die einzelne using-Anweisung durch die folgenden Zeilen, um dem Projekt die erforderlichen Abhängigkeiten hinzuzufügen:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/query-kb.cs" id="dependencies":::

## <a name="add-the-required-constants"></a>Hinzufügen der erforderlichen Konstanten

Fügen Sie am Anfang der `Program`-Klasse innerhalb der `Main`-Funktion die erforderlichen Konstanten für den Zugriff auf QnA Maker hinzu. Diese Werte werden auf der Seite **Veröffentlichen** angezeigt, nachdem Sie die Wissensdatenbank veröffentlicht haben.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/query-kb.cs" id="constants":::

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Hinzufügen einer POST-Anforderung zum Senden einer Frage und Abrufen der Antwort

Der folgende Code sendet eine HTTPS-Anforderung an die QnA Maker-API, um die Frage an die Wissensdatenbank zu senden, und empfängt die Antwort:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/query-kb.cs" id="post":::

Der Wert des `Authorization`-Headers enthält die Zeichenfolge `EndpointKey`.

Informieren Sie sich ausführlicher über [Anforderung](../how-to/metadata-generateanswer-usage.md#generateanswer-request) und [Antwort](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Erstellen und Ausführen des Programms

Erstellen Sie das Programm in Visual Studio, und führen Sie es dort aus. Das Programm sendet die Anforderung automatisch an die QnA Maker-API und zeigt die Antwort im Konsolenfenster an.

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz für QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)