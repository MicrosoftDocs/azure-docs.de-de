---
title: 'Schnellstart: Erstellen einer Wissensdatenbank – REST, Java – QnA Maker'
description: Diese REST-basierte Schnellstartanleitung für Java führt Sie durch das programmgesteuerte Erstellen einer Beispielwissensdatenbank für QnA Maker, die auf dem Azure-Dashboard Ihres Cognitive Services-API-Kontos angezeigt wird.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: 50433ee1e5a575ab671d562bfc3fe549b26fe00c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352302"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Schnellstart: Erstellen einer Wissensdatenbank in QnA Maker mithilfe von Java

In dieser Schnellstartanleitung wird das programmgesteuerte Erstellen eines Beispiels für eine QnA Maker-Wissensdatenbank Schritt für Schritt beschrieben. QnA Maker extrahiert automatisch Fragen und Antworten aus teilweise strukturiertem Inhalt (z.B. häufig gestellten Fragen) von [Datenquellen](../index.yml). Das Modell für die Wissensdatenbank wird im JSON-Code definiert, der im Text der API-Anforderung gesendet wird.

In dieser Schnellstartanleitung werden QnA Maker-APIs aufgerufen:
* [Erstellen einer Wissensdatenbank](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Abrufen von Vorgangsdetails](/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Referenzdokumentation](/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Java-Beispiel](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* [Go 1.10.1](https://golang.org/dl/)
* Sie benötigen einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md). Wählen Sie für Ihre Ressource im Azure-Portal die Option **Schnellstart** aus, um den Schlüssel und den Endpunkt (der den Ressourcennamen enthält) abzurufen.

Der [Beispielcode](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java) ist im GitHub-Repository für QnA Maker mit Java verfügbar.

## <a name="create-a-knowledge-base-file"></a>Erstellen einer Wissensdatenbankdatei

Erstellen Sie eine Datei mit dem Namen `CreateKB.java`.

## <a name="add-the-required-dependencies"></a>Hinzufügen der erforderlichen Abhängigkeiten

Fügen Sie oben in der Datei `CreateKB.java` die folgenden Zeilen hinzu, um dem Projekt die erforderlichen Abhängigkeiten hinzuzufügen:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="dependencies":::

## <a name="add-the-required-constants"></a>Hinzufügen der erforderlichen Konstanten
Fügen Sie nach den obigen erforderlichen Abhängigkeiten der Klasse `CreateKB` die erforderlichen Konstanten für den Zugriff auf QnA Maker hinzu.

Sie benötigen einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md). Wählen Sie für Ihre QnA Maker-Ressource im Azure-Portal die Option **Schnellstart** aus, um den Schlüssel und den Ressourcennamen abzurufen.

Legen Sie die folgenden Werte fest:

* `<your-qna-maker-subscription-key>` – Der **Key** (Schlüssel) ist eine Zeichenfolge mit 32 Zeichen und im Azure-Portal in der QnA Maker-Ressource auf der Schnellstartseite verfügbar. Dieser Schlüssel ist nicht mit dem Vorhersageendpunktschlüssel identisch.
* `<your-resource-name>` – Der **Resource Name** (Ressourcenname) wird verwendet, um die Endpunkt-URL für die Erstellung im Format `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` zu bilden. Dieser Ressourcenname ist nicht mit dem Namen identisch, der zum Abfragen des Vorhersageendpunkts verwendet wurde.

Die letzte geschweifte Klammer muss nicht hinzugefügt werden, um die Klasse zu beenden. Sie befindet sich im fertigen Codeausschnitt am Ende dieser Schnellstartanleitung.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="constants":::


## <a name="add-the-kb-model-definition-classes"></a>Hinzufügen der KB-Modelldefinitionsklassen
Fügen Sie nach den Konstanten die folgenden Klassen und Funktionen innerhalb der Klasse `CreateKB` hinzu, um das Modelldefinitionsobjekt in JSON zu serialisieren.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="model":::

## <a name="add-supporting-functions"></a>Hinzufügen von unterstützenden Funktionen

Fügen Sie als Nächstes innerhalb der Klasse `CreateKB` die folgenden unterstützenden Funktionen hinzu:

1. Fügen Sie die folgende Funktion zum Ausgeben von JSON in einem lesbaren Format hinzu:

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="pretty":::

2. Fügen Sie die folgende Klasse zum Verwalten der HTTP-Antwort hinzu:

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="response":::

3. Fügen Sie die folgende Methode hinzu, um eine POST-Anforderung an die QnA Maker-APIs zu richten. `Ocp-Apim-Subscription-Key` ist der QnA Maker-Dienstschlüssel, der für die Authentifizierung verwendet wird.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="post":::

4. Fügen Sie die folgende Methode hinzu, um eine GET-Anforderung an die QnA Maker-APIs zu richten:

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get":::

## <a name="add-a-method-to-create-the-kb"></a>Hinzufügen einer Methode zum Erstellen der Wissensdatenbank
Fügen Sie die folgende Methode hinzu, um die Wissensdatenbank durch Aufrufen der Post-Methode zu erstellen:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="create_kb":::

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

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get_status":::

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
Die main-Methode erstellt die Wissensdatenbank und fragt anschließend den Status ab. Die Vorgangs-ID wird im Feld **Location** des POST-Antwortheaders zurückgegeben und anschließend als Teil der Route in der GET-Anforderung verwendet. Die `while`-Schleife fragt den Status erneut ab, sofern er nicht abgeschlossen ist.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="main":::

## <a name="compile-and-run-the-program"></a>Kompilieren und Ausführen des Programms

1. Vergewissern Sie sich, dass sich die Gson-Bibliothek im Verzeichnis `./libs` befindet. Kompilieren Sie an der Befehlszeile die Datei `CreateKB.java`:

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Geben Sie den folgenden Befehl in einer Befehlszeile ein, um das Programm auszuführen. Hiermit wird die Anforderung an die QnA Maker-API gesendet, um die Wissensdatenbank zu erstellen, und dann werden alle 30 Sekunden die Ergebnisse abgefragt. Jede Antwort wird im Konsolenfenster ausgegeben.

    ```bash
    java -cp ",;libs/*" CreateKB
    ```

Nach der Erstellung der Wissensdatenbank können Sie sie im QnA Maker-Portal auf der Seite [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Meine Wissensdatenbanken) anzeigen.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz für QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)