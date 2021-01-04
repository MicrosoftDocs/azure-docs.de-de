---
title: 'Schnellstart: Abrufen von Antworten aus einer Wissensdatenbank – REST, Java – QnA Maker'
description: In diesem Java-REST-basierten Schnellstart wird Schritt für Schritt erläutert, wie Sie programmgesteuert eine Antwort auf eine Frage aus einer Wissensdatenbank abrufen.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: 8075371bddfc92d7477b3b036575836aa65f1cd0
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96346030"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Schnellstart: Abrufen von Antworten auf Fragen aus einer Wissensdatenbank mit Java

In diesem Schnellstart wird Schritt für Schritt erläutert, wie Sie programmgesteuert eine Antwort auf eine Frage aus einer veröffentlichten QnA Maker-Wissensdatenbank abrufen. Die Wissensdatenbank enthält Fragen und Antworten aus [Datenquellen](../index.yml) (beispielsweise häufig gestellte Fragen). Die [Frage](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) wird an den QnA Maker-Dienst gesendet. Die [Antwort](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) enthält die Antwort mit der bestbewerteten Vorhersage.

[Referenzdokumentation](/rest/api/cognitiveservices/qnamaker4.0/Runtime) | [Beispiel](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/get-answer/GetAnswer.java)

## <a name="prerequisites"></a>Voraussetzungen

* [JDK SE](/azure/developer/java/fundamentals/java-jdk-long-term-support) (Java Development Kit, Standard Edition)
* In diesem Beispiel wird der Apache-[HTTP-Client](https://hc.apache.org/httpcomponents-client-ga/) von HTTP Components verwendet. Sie müssen Ihrem Projekt die folgenden Apache-HTTP-Clientbibliotheken hinzufügen:
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Sie benötigen einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md). Wählen Sie zum Abrufen Ihres Schlüssels im Azure-Dashboard für Ihre QnA Maker-Ressource unter **Ressourcenverwaltung** die Option **Schlüssel** aus.
* Einstellungen auf der Seite **Veröffentlichen**. Wenn Sie keine veröffentlichte Wissensdatenbank haben, erstellen Sie eine leere Wissensdatenbank. Importieren Sie anschließend auf der Seite **Einstellungen** eine Wissensdatenbank, und veröffentlichen Sie sie. Sie können [diese einfache Wissensdatenbank](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv) herunterladen und verwenden.

    Zu den Einstellungen auf der Seite „Veröffentlichen“ zählen der Wert für die POST-Route, der Hostwert und der EndpointKey-Wert.

    ![Veröffentlichungseinstellungen](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-java-file"></a>Erstellen einer Java-Datei

Öffnen Sie Visual Studio Code, erstellen Sie eine neue Datei mit dem Namen `GetAnswer.java`, und fügen Sie die folgende Klasse hinzu:

```Java
public class GetAnswer {

    public static void main(String[] args)
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>Hinzufügen der erforderlichen Abhängigkeiten

In diesem Schnellstart werden Apache-Klassen für HTTP-Anforderungen verwendet. Fügen Sie dem Projekt am Anfang der Datei `GetAnswer.java` über der GetAnswer-Klasse die erforderlichen Abhängigkeiten hinzu:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/QueryKB.java" id="dependencies":::

## <a name="add-the-required-constants"></a>Hinzufügen der erforderlichen Konstanten

Fügen Sie am Anfang der `GetAnswer.java`-Klasse die erforderlichen Konstanten für den Zugriff auf QnA Maker hinzu. Diese Werte werden auf der Seite **Veröffentlichen** angezeigt, nachdem Sie die Wissensdatenbank veröffentlicht haben.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/QueryKB.java" id="constants":::

## <a name="add-a-post-request-to-send-question"></a>Hinzufügen einer POST-Anforderung zum Senden einer Frage

Der folgende Code sendet eine HTTPS-Anforderung an die QnA Maker-API, um die Frage an die Wissensdatenbank zu senden, und empfängt die Antwort:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/QueryKB.java" id="post":::

Der Wert des `Authorization`-Headers enthält die Zeichenfolge `EndpointKey`.

Informieren Sie sich ausführlicher über [Anforderung](../how-to/metadata-generateanswer-usage.md#generateanswer-request) und [Antwort](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Erstellen und Ausführen des Programms

Verwenden Sie die Befehlszeile, um das Programm zu erstellen und auszuführen. Das Programm sendet die Anforderung automatisch an die QnA Maker-API und zeigt die Antwort im Konsolenfenster an.

1. Erstellen Sie die Datei:

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. Führen Sie die Datei aus:

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz für QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)