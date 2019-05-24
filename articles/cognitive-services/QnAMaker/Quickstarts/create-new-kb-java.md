---
title: 'Schnellstart: Erstellen einer Wissensdatenbank – REST, Java – QnA Maker'
titlesuffix: Azure Cognitive Services
description: Diese REST-basierte Schnellstartanleitung für Java führt Sie durch das programmgesteuerte Erstellen einer Beispielwissensdatenbank für QnA Maker, die auf dem Azure-Dashboard Ihres Cognitive Services-API-Kontos angezeigt wird.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: diberry
ms.openlocfilehash: 85d5fa1cc036737497e276413985e0828e3b51f6
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788716"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Schnellstart: Erstellen einer Wissensdatenbank in QnA Maker mithilfe von Java

In dieser Schnellstartanleitung wird das programmgesteuerte Erstellen eines Beispiels für eine QnA Maker-Wissensdatenbank Schritt für Schritt beschrieben. QnA Maker extrahiert automatisch Fragen und Antworten aus teilweise strukturiertem Inhalt (z.B. häufig gestellten Fragen) von [Datenquellen](../Concepts/data-sources-supported.md). Das Modell für die Wissensdatenbank wird im JSON-Code definiert, der im Text der API-Anforderung gesendet wird.

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-java-repo-note.md)]

## <a name="create-a-knowledge-base-file"></a>Erstellen einer Wissensdatenbankdatei

Erstellen Sie eine Datei mit dem Namen `CreateKB.java`.

## <a name="add-the-required-dependencies"></a>Hinzufügen der erforderlichen Abhängigkeiten

Fügen Sie oben in der Datei `CreateKB.java` die folgenden Zeilen hinzu, um dem Projekt die erforderlichen Abhängigkeiten hinzuzufügen:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=1-5 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Hinzufügen der erforderlichen Konstanten
Fügen Sie nach den obigen erforderlichen Abhängigkeiten der Klasse `CreateKB` die erforderlichen Konstanten für den Zugriff auf QnA Maker hinzu. Ersetzen Sie den Wert der Variablen `subscriptionKey` durch Ihren eigenen QnA Maker-Schlüssel. Die letzte geschweifte Klammer muss nicht hinzugefügt werden, um die Klasse zu beenden. Sie befindet sich im fertigen Codeausschnitt am Ende dieser Schnellstartanleitung.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=26-34 "Add the required constants")]


## <a name="add-the-kb-model-definition-classes"></a>Hinzufügen der KB-Modelldefinitionsklassen
Fügen Sie nach den Konstanten die folgenden Klassen und Funktionen innerhalb der Klasse `CreateKB` hinzu, um das Modelldefinitionsobjekt in JSON zu serialisieren.

[!code-java[Add the KB model definition classes](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=36-80 "Add the KB model definition classes")]

## <a name="add-supporting-functions"></a>Hinzufügen von unterstützenden Funktionen

Fügen Sie als Nächstes innerhalb der Klasse `CreateKB` die folgenden unterstützenden Funktionen hinzu:

1. Fügen Sie die folgende Funktion zum Ausgeben von JSON in einem lesbaren Format hinzu:

    [!code-java[Add the PrettyPrint function](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=82-87 "Add the KB model definition classes")]

2. Fügen Sie die folgende Klasse zum Verwalten der HTTP-Antwort hinzu:

    [!code-java[Add class to manage the HTTP response](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=89-97 "Add class to manage the HTTP response")]

3. Fügen Sie die folgende Methode hinzu, um eine POST-Anforderung an die QnA Maker-APIs zu richten. `Ocp-Apim-Subscription-Key` ist der QnA Maker-Dienstschlüssel, der für die Authentifizierung verwendet wird.

    [!code-java[Add POST method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=99-121 "Add POST method")]

4. Fügen Sie die folgende Methode hinzu, um eine GET-Anforderung an die QnA Maker-APIs zu richten:

    [!code-java[Add GET method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=123-137 "Add GET method")]

## <a name="add-a-method-to-create-the-kb"></a>Hinzufügen einer Methode zum Erstellen der Wissensdatenbank
Fügen Sie die folgende Methode hinzu, um die Wissensdatenbank durch Aufrufen der Post-Methode zu erstellen:

[!code-java[Add CreateKB method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=139-144 "Add CreateKB method")]

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

## <a name="add-a-method-to-get-status"></a>Hinzufügen einer Methode zum Abrufen des Status
Fügen Sie die folgende Methode hinzu, um den Erstellungsstatus zu überprüfen:

[!code-java[Add GetStatus method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=146-150 "Add GetStatus method")]

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

## <a name="add-a-main-method"></a>Hinzufügen einer main-Methode
Die main-Methode erstellt die Wissensdatenbank und fragt anschließend den Status ab. Die **Vorgangs-ID** für „create“ wird im Feld **Location** des POST-Antwortheaders zurückgegeben und anschließend als Teil der Route in der GET-Anforderung verwendet. Die `while`-Schleife fragt den Status erneut ab, sofern er nicht abgeschlossen ist.

[!code-java[Add main method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=152-191 "Add main method")]

## <a name="compile-and-run-the-program"></a>Kompilieren und Ausführen des Programms

1. Vergewissern Sie sich, dass sich die Gson-Bibliothek im Verzeichnis `./libs` befindet. Kompilieren Sie an der Befehlszeile die Datei `CreateKB.java`:

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Geben Sie den folgenden Befehl in einer Befehlszeile ein, um das Programm auszuführen. Hiermit wird die Anforderung an die QnA Maker-API gesendet, um die Wissensdatenbank zu erstellen, und dann werden alle 30 Sekunden die Ergebnisse abgefragt. Jede Antwort wird im Konsolenfenster ausgegeben.

    ```base
    java -cp ",;libs/*" CreateKB
    ```

Nach der Erstellung der Wissensdatenbank können Sie sie im QnA Maker-Portal auf der Seite [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Meine Wissensdatenbanken) anzeigen.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz für QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
