---
title: 'Schnellstart: Abrufen von Antworten aus einer Wissensdatenbank – REST, Java – QnA Maker'
titlesuffix: Azure Cognitive Services
description: In diesem Java-REST-basierten Schnellstart wird Schritt für Schritt erläutert, wie Sie programmgesteuert eine Antwort auf eine Frage aus einer Wissensdatenbank abrufen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 6d49ce71959f80a96731046475c3de5737bf7d46
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796197"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Abrufen von Antworten auf Fragen aus einer Wissensdatenbank mit Java

In diesem Schnellstart wird Schritt für Schritt erläutert, wie Sie programmgesteuert eine Antwort auf eine Frage aus einer veröffentlichten QnA Maker-Wissensdatenbank abrufen. Die Wissensdatenbank enthält Fragen und Antworten aus [Datenquellen](../Concepts/data-sources-supported.md) (beispielsweise häufig gestellte Fragen). Die [Frage](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) wird an den QnA Maker-Dienst gesendet. Die [Antwort](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) enthält die Antwort mit der bestbewerteten Vorhersage. 

## <a name="prerequisites"></a>Voraussetzungen

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* In diesem Beispiel wird der Apache-[HTTP-Client](https://hc.apache.org/httpcomponents-client-ga/) von HTTP Components verwendet. Sie müssen Ihrem Projekt die folgenden Apache-HTTP-Clientbibliotheken hinzufügen: 
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Sie benötigen einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md). Wählen Sie zum Abrufen Ihres Schlüssels im Azure-Dashboard für Ihre QnA Maker-Ressource unter **Ressourcenverwaltung** die Option **Schlüssel** aus. 
* Einstellungen auf der Seite **Veröffentlichen**. Wenn Sie keine veröffentlichte Wissensdatenbank haben, erstellen Sie eine leere Wissensdatenbank. Importieren Sie anschließend auf der Seite **Einstellungen** eine Wissensdatenbank, und veröffentlichen Sie sie. Sie können [diese einfache Wissensdatenbank](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv) herunterladen und verwenden. 

    Zu den Einstellungen auf der Seite „Veröffentlichen“ zählen der Wert für die POST-Route, der Hostwert und der EndpointKey-Wert. 

    ![Veröffentlichungseinstellungen](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Der Code für diesen Schnellstart ist im Repository unter [https://github.com/Azure-Samples/cognitive-services-qnamaker-java](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/get-answer) verfügbar. 

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

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=5-13 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Hinzufügen der erforderlichen Konstanten

Fügen Sie am Anfang der `GetAnswer.java`-Klasse die erforderlichen Konstanten für den Zugriff auf QnA Maker hinzu. Diese Werte werden auf der Seite **Veröffentlichen** angezeigt, nachdem Sie die Wissensdatenbank veröffentlicht haben.  

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=26-42 "Add the required constants")]

## <a name="add-a-post-request-to-send-question"></a>Hinzufügen einer POST-Anforderung zum Senden einer Frage

Der folgende Code sendet eine HTTPS-Anforderung an die QnA Maker-API, um die Frage an die Wissensdatenbank zu senden, und empfängt die Antwort:

[!code-java[Add a POST request to send question to knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=44-72 "Add a POST request to send question to knowledge base")]

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
> [REST-API-Referenz für QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
