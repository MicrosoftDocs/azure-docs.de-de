---
title: 'Schnellstart: Erstellen einer Wissensdatenbank – REST, Go – QnA Maker'
titlesuffix: Azure Cognitive Services
description: Diese REST-basierte Schnellstartanleitung für Go führt Sie durch das programmgesteuerte Erstellen einer Beispielwissensdatenbank für QnA Maker, die auf dem Azure-Dashboard Ihres Cognitive Services-API-Kontos angezeigt wird.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: diberry
ms.openlocfilehash: 4831fca8a72f62d64c1977b510f039065f6cd3c5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788568"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Schnellstart: Erstellen einer Wissensdatenbank in QnA Maker mithilfe von Go

In dieser Schnellstartanleitung wird das programmgesteuerte Erstellen eines Beispiels für eine QnA Maker-Wissensdatenbank Schritt für Schritt beschrieben. QnA Maker extrahiert automatisch Fragen und Antworten aus teilweise strukturiertem Inhalt (z.B. häufig gestellten Fragen) von [Datenquellen](../Concepts/data-sources-supported.md). Das Modell für die Wissensdatenbank wird im JSON-Code definiert, der im Text der API-Anforderung gesendet wird. 

In dieser Schnellstartanleitung werden QnA Maker-APIs aufgerufen:
* [Erstellen einer Wissensdatenbank](https://go.microsoft.com/fwlink/?linkid=2092179)
* [Abrufen von Vorgangsdetails](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

## <a name="prerequisites"></a>Voraussetzungen

* [Go 1.10.1](https://golang.org/dl/)
* Sie benötigen einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md). Wählen Sie zum Abrufen des Schlüssels auf Ihrem Dashboard unter **Ressourcenverwaltung** die Option **Schlüssel** aus. 

## <a name="create-a-knowledge-base-go-file"></a>Erstellen einer Go-Datei für die Wissensdatenbank

Erstellen Sie eine Datei mit dem Namen `create-new-knowledge-base.go`.

## <a name="add-the-required-dependencies"></a>Hinzufügen der erforderlichen Abhängigkeiten

Fügen Sie oben in der Datei `create-new-knowledge-base.go` die folgenden Zeilen hinzu, um dem Projekt die erforderlichen Abhängigkeiten hinzuzufügen:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Hinzufügen der erforderlichen Konstanten
Fügen Sie nach den obigen erforderlichen Abhängigkeiten die erforderlichen Konstanten für den Zugriff auf QnA Maker hinzu. Ersetzen Sie den Wert der Variablen `subscriptionKey` durch Ihren eigenen QnA Maker-Schlüssel.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=13-20 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Hinzufügen der KB-Modelldefinition
Fügen Sie nach den Konstanten die folgende Modelldefinition für die Wissensdatenbank hinzu. Das Modell wird nach der Definition in eine Zeichenfolge konvertiert.

[!code-go[Add the KB model definition](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=22-44 "Add the KB model definition")]

## <a name="add-supporting-structures-and-functions"></a>Hinzuzufügen von unterstützenden Strukturen und Funktionen

Als Nächstes fügen Sie die folgenden unterstützenden Funktionen hinzu.

1. Fügen Sie die Struktur für eine HTTP-Anforderung hinzu:

    [!code-go[Add the structure for an HTTP request](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=46-49 "Add the structure for an HTTP request")]

2. Fügen Sie die folgende Methode hinzu, um eine POST-Anforderung an die QnA Maker-APIs zu behandeln. Im Rahmen dieser Schnellstartanleitung wird die POST-Anforderung verwendet, um die Definition der Wissensdatenbank an QnA Maker zu senden.

    [!code-go[Add the POST method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=51-66 "Add the POST method")]

3. Fügen Sie die folgende Methode hinzu, um eine GET-Anforderung an die QnA Maker-APIs zu behandeln. Im Rahmen dieser Schnellstartanleitung wird die GET-Anforderung verwendet, um den Status des Erstellungsvorgangs zu überprüfen. 

    [!code-go[Add the GET method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=68-83 "Add the GET method")]

## <a name="add-function-to-create-kb"></a>Hinzufügen einer Funktion zum Erstellen der Wissensdatenbank

Fügen Sie die folgenden Funktionen hinzu, um eine HTTP POST-Anforderung zum Erstellen der Wissensdatenbank zu senden. Die **Vorgangs-ID** für _create_ wird im Feld **Location** des POST-Antwortheaders zurückgegeben und anschließend als Teil der Route in der GET-Anforderung verwendet. `Ocp-Apim-Subscription-Key` ist der QnA Maker-Dienstschlüssel, der für die Authentifizierung verwendet wird. 

[!code-go[Add the create_kb method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=85-97 "Add the create_kb method")]

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

## <a name="add-function-to-get-status"></a>Hinzufügen einer Funktion zum Abrufen des Status

Fügen Sie die folgende Funktion hinzu, um eine HTTP GET-Anforderung zum Überprüfen des Vorgangsstatus zu senden. `Ocp-Apim-Subscription-Key` ist der QnA Maker-Dienstschlüssel, der für die Authentifizierung verwendet wird. 

[!code-go[Add the check_status method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=99-108 "Add the check_status method")]

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
## <a name="add-main-function"></a>Hinzufügen der Hauptfunktion

Die folgende Funktion ist die Hauptfunktion, mit der die Wissensdatenbank erstellt und wiederholt der Status überprüft wird. Da die Erstellung der Wissensdatenbank einige Zeit dauern kann, müssen Sie die Aufrufe zum Überprüfen des Status wiederholen, bis der Status entweder „Erfolgreich“ oder „Fehler“ lautet.

[!code-go[Add the main method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=110-140 "Add the main method")]


## <a name="compile-the-program"></a>Kompilieren des Programms
Geben Sie den folgenden Befehl ein, um die Datei zu kompilieren. Die Eingabeaufforderung gibt keine Informationen für eine erfolgreiche Erstellung zurück.

```bash
go build create-new-knowledge-base.go
```

## <a name="run-the-program"></a>Ausführen des Programms

Geben Sie den folgenden Befehl in einer Befehlszeile ein, um das Programm auszuführen. Hiermit wird die Anforderung an die QnA Maker-API gesendet, um die Wissensdatenbank zu erstellen, und dann werden alle 30 Sekunden die Ergebnisse abgefragt. Jede Antwort wird im Konsolenfenster ausgegeben.

```bash
go run create-new-knowledge-base
```

Nach der Erstellung der Wissensdatenbank können Sie sie im QnA Maker-Portal auf der Seite [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Meine Wissensdatenbanken) anzeigen. 

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz für QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
