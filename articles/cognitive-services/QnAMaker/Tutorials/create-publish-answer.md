---
title: Erstellen, Veröffentlichen, Beantworten
titleSuffix: QnA Maker - Azure Cognitive Services
description: In diesem REST-basierten Tutorial erfahren Sie Schritt für Schritt, wie Sie eine Wissensdatenbank programmgesteuert erstellen und veröffentlichen und anschließend zur Beantwortung von Fragen heranziehen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: a14bc14e6115c1bc25582c3de71382ae17d7debd
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792281"
---
# <a name="tutorial-using-c-create-knowledge-base-then-answer-question"></a>Tutorial: Erstellen einer Wissensdatenbank und Beantworten von Fragen unter Verwendung von C#

In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie eine Wissensdatenbank (Knowledge Base, KB) programmgesteuert erstellen und veröffentlichen und anschließend zur Beantwortung einer Kundenfrage verwenden. 

> [!div class="checklist"]
> * Erstellen einer Wissensdatenbank 
> * Überprüfen des Erstellungsstatus
> * Trainieren und Veröffentlichen der Wissensdatenbank
> * Abrufen von Endpunktinformationen
> * Abfragen der Wissensdatenbank mithilfe von Curl


In dieser Schnellstartanleitung werden QnA Maker-APIs aufgerufen:

* [Erstellen der Wissensdatenbank](https://go.microsoft.com/fwlink/?linkid=2092179)
* [Abrufen von Vorgangsdetails](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)
* [Abrufen der Details der Wissensdatenbank](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/getdetails) 
* [Abrufen der Endpunkte der Wissensdatenbank](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/getkeys)
* [Veröffentlichen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) 

## <a name="prerequisites"></a>Voraussetzungen

* Aktuelle [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/)
* Sie benötigen einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md). Wählen Sie zum Abrufen des Schlüssels auf Ihrem Dashboard unter **Ressourcenverwaltung** die Option **Schlüssel** aus. 

> [!NOTE] 
> Die vollständigen Projektmappendateien sind über das [GitHub-Repository **Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base) verfügbar.

## <a name="create-a-knowledge-base-project"></a>Erstellen eines Wissensdatenbank-Projekts

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>Hinzufügen der erforderlichen Abhängigkeiten

Ersetzen Sie am Anfang von „Program.cs“ die einzelne _using_-Anweisung durch die folgenden Zeilen, um dem Projekt die erforderlichen Abhängigkeiten hinzuzufügen:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-a-kbdetails-class"></a>Hinzufügen einer KBDetails-Klasse
Fügen Sie innerhalb der Namespaceklammern die folgende KBDetails-Klasse hinzu. Diese Klasse ermöglicht es der NewtonSoft-Bibliothek, die JSON-Antwort in eine C#-Klasse zu deserialisieren.

[!code-csharp[Add a KBDetails class](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=15-26 "Add a KBDetails class")]

## <a name="add-the-required-constants"></a>Hinzufügen der erforderlichen Konstanten

Fügen Sie am Anfang der Program-Klasse die folgenden Konstanten hinzu, um auf QnA Maker zuzugreifen:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=30-57 "Add the required constants")]

## <a name="add-the-kb-definition"></a>Hinzufügen der Definition der Wissensdatenbank

Fügen Sie nach den Konstanten die folgende Modelldefinition für die Wissensdatenbank hinzu:

[!code-csharp[Add the KB definition](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=59-85 "Add the KB definition")]

## <a name="add-supporting-functions-and-structures"></a>Hinzuzufügen von unterstützenden Funktionen und Strukturen
Fügen Sie innerhalb der Program-Klasse den folgenden Codeblock hinzu:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=87-123 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>Hinzufügen einer POST-Anforderung zum Erstellen der Wissensdatenbank

Der folgende Code sendet eine HTTPS-Anforderung an die QnA Maker-API, um eine Wissensdatenbank zu erstellen, und empfängt die Antwort:

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=124-141 "Add a POST request to create KB")]

Mit diesem API-Aufruf wird eine JSON-Antwort zurückgegeben, die die Vorgangs-ID enthält. Anhand der Vorgangs-ID ermittelt das Programm später, ob die Wissensdatenbank erfolgreich erstellt wurde. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Hinzufügen einer GET-Anforderung zum Ermitteln des Erstellungsstatus

Überprüfen Sie den Status des Erstellungsvorgangs.

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=142-151 "Add GET request to determine creation status")]

Dieser API-Aufruf gibt eine JSON-Antwort zurück, die den Vorgangsstatus enthält: 

```JSON
{
  "operationState": "Running",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

Wiederholen Sie den Aufruf, bis er erfolgreich abgeschlossen wird oder ein Fehler auftritt: 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>Hinzufügen der CreateKB-Methode

Die folgende Methode kapselt die Aufrufe für die Erstellung der Wissensdatenbank und die Statusüberprüfung.  Die **Vorgangs-ID** für _create_ wird im Feld **Location** des POST-Antwortheaders zurückgegeben und anschließend als Teil der Route in der GET-Anforderung verwendet. Da die Erstellung der Wissensdatenbank einige Zeit dauern kann, müssen Sie die Aufrufe zum Überprüfen des Status wiederholen, bis der Status entweder „Erfolgreich“ oder „Fehler“ lautet. Wenn der Vorgang erfolgreich war, wird die ID der Wissensdatenbank in **resourceLocation** zurückgegeben. 

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=152-227 "Add GET request to determine creation status")]

## <a name="add-publish-method"></a>Hinzufügen der Veröffentlichungsmethode

Nachdem die Wissensdatenbank erfolgreich erstellt wurde, muss sie veröffentlicht werden. Sie haben vielleicht einen Trainings-API-Aufruf erwartet. Das ist bei dieser Version jedoch nicht erforderlich. 

Der folgende Code sendet eine HTTPS-Anforderung an die QnA Maker-API, um eine KB zu veröffentlichen, und empfängt die Antwort:

[!code-csharp[Add publish method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=228-259 "Add publish method")]

Der API-Aufruf gibt den Status 204 für eine erfolgreiche Veröffentlichung ohne Inhalt im Text der Antwort zurück. Der Schnellstartcode fügt Text für 204-Antworten hinzu, damit Sie das Ergebnis sehen können.

Für alle anderen Antworten wird diese Antwort unverändert zurückgegeben.

## <a name="generating-an-answer"></a>Generieren einer Antwort
Damit das Programm auf die Wissensdatenbank zugreifen kann, um eine Frage zu senden und die bestmögliche Antwort zu erhalten, benötigt es den _Endpunkthost_ aus der Detail-API der Wissensdatenbank sowie den _Schlüssel des primären Endpunkts_ aus der Endpunkte-API. Diese Methoden sind in den folgenden Abschnitten angegeben – zusammen mit der Methode für die Antwortgenerierung. 

Die folgende Tabelle zeigt, wie die Daten verwendet werden, um den URI zu erstellen:

|Generieren einer Antwort-URI-Vorlage|
|--|
|https://**HOSTNAME**.azurewebsites.net/qnamaker/knowledgebases/**WISSENSDATENBANK-ID**/generateAnswer|

Der _primäre Endpunkt_ wird als Header übergeben, um die Anforderung für die Antwortgenerierung zu authentifizieren:

|Headername|Headerwert|
|--|--|
|Autorisierung|`Endpoint` + **primärer Endpunkt**<br>Beispiel: `Endpoint xxxxxxx`<br>Beachten Sie das Leerzeichen zwischen dem Text `Endpoint` und dem Wert des primären Endpunkts. 

Der Hauptteil der Anforderung muss den korrekten JSON-Code übergeben:

```JSON
{
    question: "What languages does QnA Maker support?"
}
```

## <a name="get-kb-details"></a>Abrufen der Details der Wissensdatenbank
Fügen Sie die folgende Methode hinzu, um die Details der Wissensdatenbank abzurufen. Diese Details enthalten den Hostnamen der Wissensdatenbank. Der Hostname ist der Name des QnA Maker-Azure-Webdiensts, den Sie beim Erstellen der QnA Maker-Ressource eingegeben haben. 

[!code-csharp[Get KB Details](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=260-273 "Add publish method")]

Dieser API-Aufruf gibt eine JSON-Antwort zurück: 

```JSON
{
  "id": "ZZZ31e19-cba7-48d1-8594-5c4297ecc9c1",
  "hostName": "https://qnamaker-s0-s.azurewebsites.net",
  "lastAccessedTimestamp": "2018-10-11T18:10:05Z",
  "lastChangedTimestamp": "2018-10-11T18:09:37Z",
  "lastPublishedTimestamp": "2018-10-11T18:10:15Z",
  "name": "QnA Maker FAQ from quickstart",
  "userId": "AAAc3841df0b42cdb00f53a49d51a89c",
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

## <a name="get-kb-endpoints"></a>Abrufen der Endpunkte der Wissensdatenbank
Fügen Sie die folgende Methode hinzu, um die primären Endpunkte von QnA Maker abzurufen. Diese Endpunkte sind nicht an die Wissensdatenbank gebunden und können für alle Wissensdatenbanken verwendet werden, die den QnA Maker-Ressourcenschlüsseln aus dem Azure-Portal zugeordnet sind.  

[!code-csharp[Get KB Endpoints](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=274-289 "Get KB Endpoints")]

Dieser API-Aufruf gibt eine JSON-Antwort zurück: 

```JSON
{
  "primaryEndpointKey": "AAAb5719-e2f7-4a33-937d-7a3b4736a1be",
  "secondaryEndpointKey": "BBBcba78-c1d2-4166-b98f-c77255aefaba",
  "installedVersion": "4.2.0",
  "lastStableVersion": "4.2.0"
}
```

## <a name="get-an-answer"></a>Abrufen einer Antwort
Fügen Sie die folgende Methode hinzu, um eine Antwort auf die Frage des Benutzers zu erhalten. 

[!code-csharp[Get Answer](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=290-315 "Get Answer")]

Dieser API-Aufruf gibt eine JSON-Antwort zurück: 

```JSON
{
  "answers": [
    {
      "questions": [
        "Does QnA Maker support non-English languages?"
      ],
      "answer": "See more details about [supported languages](https://docs.microsoft.com/azure/cognitive-services/qnamaker/overview/languages-supported).\n\n\nIf you have content from multiple languages, be sure to create a separate service for each language.",
      "score": 82.19,
      "id": 11,
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    }
  ]
}
```

## <a name="main-method"></a>Main-Methode
Die Main-Methode zeigt die synchronen Aufrufe zum Erstellen, Veröffentlichen und Generieren der Antwort. 

[!code-csharp[Main method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=316-337 "Main method")]

## <a name="build-and-run-the-program"></a>Erstellen und Ausführen des Programms

Erstellen Sie das Programm, und führen Sie es aus. 

Nach der Erstellung der Wissensdatenbank können Sie sie im QnA Maker-Portal auf der Seite [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Meine Wissensdatenbanken) anzeigen. Nachdem Sie mit der Verwendung der API für die Antwortgenerierung vertraut sind, können Sie sie mit beliebigen Sprachen und HTTP-Anforderungsframeworks verwenden. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz für QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
