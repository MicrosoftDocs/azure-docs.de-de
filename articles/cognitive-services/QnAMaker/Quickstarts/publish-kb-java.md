---
title: Veröffentlichen einer Wissensdatenbank, REST, Java
titleSuffix: QnA Maker - Azure Cognitive Services
description: In diesem Java-REST-basierten Schnellstart wird Schritt für Schritt erläutert, wie Sie Ihre Wissensdatenbank veröffentlichen. Durch das Veröffentlichen wird die aktuelle Version der getesteten Wissensdatenbank in einen dedizierten Azure Search-Index gepusht, der die veröffentlichte Wissensdatenbank darstellt. Außerdem wird ein Endpunkt erstellt, der in Ihrer Anwendung oder Ihrem Chatbot aufgerufen werden kann.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: ae94d02b93880b7c81d359e5b2606b720b38b554
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787942"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Schnellstart: Veröffentlichen einer Wissensdatenbank in QnA Maker mit Java

In diesem REST-basierten Schnellstart wird das programmgesteuerte Veröffentlichen Ihrer Wissensdatenbank (Knowledge Base, KB) Schritt für Schritt beschrieben. Bei der Veröffentlichung wird die aktuelle Version der Wissensdatenbank per Pushvorgang an einen dedizierten Azure Search-Index übertragen und ein Endpunkt erstellt, der in Ihrer Anwendung oder Ihrem Chatbot aufgerufen werden kann.

In dieser Schnellstartanleitung werden QnA Maker-APIs aufgerufen:
* [Veröffentlichen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish): Für diese API sind keine Informationen im Text der Anforderung erforderlich.

## <a name="prerequisites"></a>Voraussetzungen

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* In diesem Beispiel wird der Apache-[HTTP-Client](https://hc.apache.org/httpcomponents-client-ga/) von HTTP Components verwendet. Sie müssen Ihrem Projekt die folgenden Apache-HTTP-Clientbibliotheken hinzufügen: 
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Sie benötigen einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md). Wählen Sie zum Abrufen Ihres Schlüssels im Azure-Dashboard für Ihre QnA Maker-Ressource unter **Ressourcenverwaltung** die Option **Schlüssel** aus. . 
* ID der QnA Maker-Wissensdatenbank, die in der URL des Abfragezeichenfolgenparameters „kbid“ enthalten ist, wie nachfolgend gezeigt:

    ![ID der QnA Maker-Wissensdatenbank](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Falls Sie noch keine Wissensdatenbank besitzen, können Sie für diese Schnellstartanleitung eine Beispieldatenbank erstellen: [Erstellen Sie eine Wissensdatenbank](create-new-kb-csharp.md).

> [!NOTE] 
> Die vollständigen Projektmappendateien sind über das [Github-Repository **Azure-Samples/cognitive-services-qnamaker-java**](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base) verfügbar.

## <a name="create-a-java-file"></a>Erstellen einer Java-Datei

Öffnen Sie Visual Studio Code, und erstellen Sie eine neue Datei mit dem Namen `PublishKB.java`.

## <a name="add-the-required-dependencies"></a>Hinzufügen der erforderlichen Abhängigkeiten

Fügen Sie oben in der Datei `PublishKB.java` oberhalb der Klasse die folgenden Zeilen hinzu, um dem Projekt die erforderlichen Abhängigkeiten hinzuzufügen:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>Erstellen einer PublishKB-Klasse mit der main-Methode

Fügen Sie nach den Abhängigkeiten die folgende Klasse hinzu:

```Go
public class PublishKB {

    public static void main(String[] args) 
    {
    }
}
```

## <a name="add-required-constants"></a>Hinzufügen von erforderlichen Konstanten

Fügen Sie in der Methode **main** die erforderlichen Konstanten für den Zugriff auf QnA Maker hinzu. Ersetzen Sie die Werte dabei durch Ihre eigenen Werte.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Hinzufügen einer POST-Anforderung zur Veröffentlichung der Wissensdatenbank

Fügen Sie nach den erforderlichen Konstanten den folgenden Code hinzu, der eine HTTPS-Anforderung an die QnA Maker-API sendet, um eine Wissensdatenbank zu veröffentlichen, und die Antwort empfängt:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

Der API-Aufruf gibt den Status 204 für eine erfolgreiche Veröffentlichung ohne Inhalt im Text der Antwort zurück. Mit dem Code wird Inhalt für Antworten vom Typ 204 hinzugefügt.

Für alle anderen Antworten wird diese Antwort unverändert zurückgegeben.

## <a name="build-and-run-the-program"></a>Erstellen und Ausführen des Programms

Verwenden Sie die Befehlszeile, um das Programm zu erstellen und auszuführen. Das Programm sendet die Anforderung automatisch an die QnA Maker-API und zeigt die Antwort im Konsolenfenster an.

1. Erstellen Sie die Datei:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Führen Sie die Datei aus:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Nächste Schritte

Nachdem die Wissensdatenbank veröffentlicht wurde, benötigen Sie die [Endpunkt-URL, um eine Antwort zu generieren](../Tutorials/create-publish-answer.md#generating-an-answer).  

> [!div class="nextstepaction"]
> [REST-API-Referenz für QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
