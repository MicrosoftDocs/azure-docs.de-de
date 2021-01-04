---
title: 'Schnellstart: Erstellen einer Wissensdatenbank – REST, Go – QnA Maker'
description: Diese REST-basierte Schnellstartanleitung für Go führt Sie durch das programmgesteuerte Erstellen einer Beispielwissensdatenbank für QnA Maker, die auf dem Azure-Dashboard Ihres Cognitive Services-API-Kontos angezeigt wird.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: cff2d141e8108d9a3e2e12764174a0bf4978182f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352319"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Schnellstart: Erstellen einer Wissensdatenbank in QnA Maker mithilfe von Go

In dieser Schnellstartanleitung wird das programmgesteuerte Erstellen eines Beispiels für eine QnA Maker-Wissensdatenbank Schritt für Schritt beschrieben. QnA Maker extrahiert automatisch Fragen und Antworten aus teilweise strukturiertem Inhalt (z.B. häufig gestellten Fragen) von [Datenquellen](../index.yml). Das Modell für die Wissensdatenbank wird im JSON-Code definiert, der im Text der API-Anforderung gesendet wird.

In dieser Schnellstartanleitung werden QnA Maker-APIs aufgerufen:
* [Erstellen einer Wissensdatenbank](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Abrufen von Vorgangsdetails](/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Referenzdokumentation](/rest/api/cognitiveservices/qnamaker/knowledgebase) | [GO-Beispiel](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* [Go 1.10.1](https://golang.org/dl/)
* Sie benötigen einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md). Wählen Sie für Ihre Ressource im Azure-Portal die Option **Schnellstart** aus, um den Schlüssel und den Endpunkt (der den Ressourcennamen enthält) abzurufen.

## <a name="create-a-knowledge-base-go-file"></a>Erstellen einer Go-Datei für die Wissensdatenbank

Erstellen Sie eine Datei mit dem Namen `create-new-knowledge-base.go`.

## <a name="add-the-required-dependencies"></a>Hinzufügen der erforderlichen Abhängigkeiten

Fügen Sie oben in der Datei `create-new-knowledge-base.go` die folgenden Zeilen hinzu, um dem Projekt die erforderlichen Abhängigkeiten hinzuzufügen:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="dependencies":::

## <a name="add-the-kb-model-definition"></a>Hinzufügen der KB-Modelldefinition
Fügen Sie nach den Konstanten die folgende Modelldefinition für die Wissensdatenbank hinzu. Das Modell wird nach der Definition in eine Zeichenfolge konvertiert.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="model":::

## <a name="add-supporting-structures-and-functions"></a>Hinzuzufügen von unterstützenden Strukturen und Funktionen

Als Nächstes fügen Sie die folgenden unterstützenden Funktionen hinzu.

1. Fügen Sie die Struktur für eine HTTP-Antwort hinzu:

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="response":::

1. Fügen Sie die folgende Methode hinzu, um eine POST-Anforderung an die QnA Maker-APIs zu behandeln. Im Rahmen dieser Schnellstartanleitung wird die POST-Anforderung verwendet, um die Definition der Wissensdatenbank an QnA Maker zu senden.

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="post":::

1. Fügen Sie die folgende Methode hinzu, um eine GET-Anforderung an die QnA Maker-APIs zu behandeln. Im Rahmen dieser Schnellstartanleitung wird die GET-Anforderung verwendet, um den Status des Erstellungsvorgangs zu überprüfen.

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="get":::

## <a name="add-function-to-create-kb"></a>Hinzufügen einer Funktion zum Erstellen der Wissensdatenbank

Fügen Sie die folgenden Funktionen hinzu, um eine HTTP POST-Anforderung zum Erstellen der Wissensdatenbank zu senden. Die **Vorgangs-ID** für _create_ wird im Feld **Location** des POST-Antwortheaders zurückgegeben und anschließend als Teil der Route in der GET-Anforderung verwendet. `Ocp-Apim-Subscription-Key` ist der QnA Maker-Dienstschlüssel, der für die Authentifizierung verwendet wird.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="create_kb":::

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

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="get_status":::

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

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="main":::


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
> [REST-API-Referenz für QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)