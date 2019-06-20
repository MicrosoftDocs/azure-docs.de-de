---
title: 'Verwenden von Metadaten mit der GenerateAnswer-API: QnA Maker'
titleSuffix: Azure Cognitive Services
description: QnA Maker ermöglicht Ihnen das Hinzufügen von Metadaten in Form von Schlüssel-Wert-Paaren zu Ihren Frage-Antwort-Sätzen. Sie können Ergebnisse nach Benutzerabfragen filtern und zusätzliche Informationen speichern, die in Folgekonversationen verwendet werden können.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/30/2019
ms.author: tulasim
ms.openlocfilehash: b18d47b4b09c6fa9c4d5f0ef87d7ebe73f151c60
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693243"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Abrufen einer Antwort mit der GenerateAnswer-API und Metadaten

Um die vorhergesagte Antwort auf die Frage eines Benutzers zu erhalten, verwenden Sie die GenerateAnswer-API. Wenn Sie eine Wissensdatenbank veröffentlichen, werden Informationen zur Verwendung dieser API auf der Seite **Veröffentlichen** angezeigt. Sie können die API auch so konfigurieren, dass sie Antworten anhand von Metadatentags filtert, und Sie können die Wissensdatenbank vom Endpunkt aus mit dem Testabfrage-Zeichenfolgenparameter testen.

QnA Maker ermöglicht es Ihnen, Ihren Frage-Antwort-Sätzen Metadaten in Form von Schlüssel-Wert-Paaren hinzuzufügen. Mit diesen Informationen können Sie Ergebnisse nach Benutzerabfragen filtern und zusätzliche Informationen speichern, die in Folgekonversationen verwendet werden können. Weitere Informationen finden Sie unter [Knowledge Base](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Speichern von Fragen und Antworten mit einer QnA-Entität

Zunächst müssen Sie wissen, wie QnA Maker die Frage-Antwort-Daten speichert. Die folgende Abbildung zeigt eine QnA-Entität:

![Illustration einer QnA-Entität](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Jede QnA-Entität hat eine eindeutige und dauerhafte ID. Sie können die ID verwenden, um bestimmte QnA-Entitäten zu aktualisieren.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Abrufen von Antwortvorhersagen mit der GenerateAnswer-API

Sie verwenden die GenerateAnswer-API in Ihrem Bot oder Ihrer Anwendung, um Ihre Wissensdatenbank mit einer Benutzerfrage abzufragen und die beste Übereinstimmung aus den Frage-Antwort-Sätzen zu erhalten.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Veröffentlichen am GenerateAnswer-Endpunkt 

Nachdem Sie Ihre Wissensdatenbank im [QnA Maker-Portal](https://www.qnamaker.ai) oder mithilfe der [API](https://go.microsoft.com/fwlink/?linkid=2092179) veröffentlicht haben, können Sie die Details von Ihrem GenerateAnswer-Endpunkt abrufen.

So rufen Sie Endpunktdetails ab
1. Melden Sie sich bei [https://www.qnamaker.ai](https://www.qnamaker.ai) an.
1. Klicken Sie in **Meine Wissensdatenbanken** für Ihre Wissensdatenbank auf **Code anzeigen**.
    ![Screenshot von „Meine Wissensdatenbanken“](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Rufen Sie Ihre GenerateAnswer-Endpunktdetails ab.

    ![Screenshot der Endpunktdetails](../media/qnamaker-how-to-metadata-usage/view-code.png)

Sie können Ihre Endpunktdetails auch auf der Registerkarte **Einstellungen** Ihrer Knowledge Base abrufen.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer-Anforderungskonfiguration

Sie rufen GenerateAnswer über eine HTTP POST-Anforderung auf. Beispielcode, der zeigt, wie GenerateAnswer aufgerufen wird, finden Sie unter [Schnellstarts](../quickstarts/csharp.md).

Die **Anforderungs-URL** weist das folgende Format auf: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

|HTTP-Anforderungseigenschaft|NAME|Type|Zweck|
|--|--|--|--|
|URL-Routenparameter|Wissensdatenbank-ID|Zeichenfolge|GUID der Knowledge Base|
|URL-Routenparameter|QnAMaker-Endpunkthost|Zeichenfolge|Hostname des Endpunkts, der in Ihrem Azure-Abonnement bereitgestellt wurde Dieser Wert ist auf der Seite **Einstellungen** verfügbar, nachdem Sie die Wissensdatenbank veröffentlicht haben. |
|Header|Content-Typ|Zeichenfolge|Medientyp des an die API gesendeten Texts Der Standardwert ist ``.|
|Header|Autorisierung|Zeichenfolge|Ihr Endpunktschlüssel (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Posttext|JSON-Objekt|JSON|Die Frage mit Einstellungen.|


Der JSON-Text verfügt über mehrere Einstellungen:

|JSON-Texteigenschaft|Erforderlich|Type|Zweck|
|--|--|--|--|
|`question`|required|Zeichenfolge|Eine Benutzerfrage, die an die Wissensdatenbank gesendet werden soll.|
|`top`|optional|integer|Anzahl der priorisierten Ergebnisse für die Ausgabe Der Standardwert ist 1.|
|`userId`|optional|Zeichenfolge|Eindeutige ID zur Identifizierung des Benutzers. Diese ID wird in den Chatprotokollen aufgezeichnet.|
|`scoreThreshold`|optional|integer|Es werden nur Antworten mit einer Zuverlässigkeitsbewertung über diesem Schwellenwert zurückgegeben. Der Standardwert ist 0.|
|`isTest`|optional|Boolean|Wenn auf TRUE festgelegt, werden Ergebnisse aus dem `testkb`-Suchindex anstatt aus dem veröffentlichten Index zurückgegeben.|
|`strictFilters`|optional|Zeichenfolge|Weist (sofern angegeben) QnA Maker an, nur Antworten mit den angegebenen Metadaten zurückzugeben. Verwenden Sie `none`, um festzulegen, dass die Antwort keine Metadatenfilter enthalten soll. |
|`RankerType`|optional|Zeichenfolge|Bei Angabe als `QuestionOnly` wird QnA Maker angewiesen, nur Fragen zu durchsuchen. Wenn Sie hier nichts angeben, durchsucht QnA Maker Fragen und Antworten.

Ein JSON-Beispieltext sieht folgendermaßen aus:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer-Antworteigenschaften

Eine erfolgreiche Antwort gibt den Status 200 und eine JSON-Antwort zurück. 

|Answers-Eigenschaft (sortiert nach Bewertung)|Zweck|
|--|--|
|Ergebnis Ihrer App|Rangfolgewert zwischen 0 und 100|
|id|Die der Antwort zugewiesene eindeutige ID|
|Fragen|Fragen des Benutzers|
|answer (Annehmen)|Die Antwort auf die Frage|
|source|Name der Quelle, aus der die Antwort extrahiert oder in der Wissensdatenbank gespeichert wurde|
|metadata|Die der Antwort zugeordneten Metadaten|
|metadata.name|Metadatenname (Zeichenfolge, maximale Länge: 100, erforderlich)|
|metadata.value|Metadatenwert (Zeichenfolge, maximale Länge: 100, erforderlich)|


```json
{
    "answers": [
        {
            "score": 28.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Verwenden von Metadaten zum Filtern von Antworten nach benutzerdefinierten Metadatentags

Durch das Hinzufügen von Metadaten können Sie die Antworten nach diesen Metadatentags filtern. Fügen Sie die Metadatenspalte aus dem Menü **Ansichtsoptionen** hinzu. Fügen Sie Ihrer Wissensdatenbank Metadaten hinzu, indem Sie auf das Metadatensymbol **+** klicken, um ein Metadatenpaar hinzuzufügen. Dieses Paar besteht aus einem Schlüssel und einen Wert.

![Screenshot: Hinzufügen von Metadaten](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtern von Ergebnissen mit „strictFilters“ nach Metadatentags

Betrachten Sie die Benutzerfrage „When does this hotel close?“ (Wann schließt dieses Hotel?); die Absicht zielt auf das Restaurant „Paradise“ ab.

Da nur Ergebnisse für das Restaurant „Paradise“ erforderlich sind, können Sie im GenerateAnswer-Aufruf einen Filter für die Metadaten zum Restaurantnamen festlegen. Dies wird im folgenden Beispiel veranschaulicht:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Verwenden von Frage und Antwortergebnis zum Beibehalten des Unterhaltungskontexts

Die Antwort auf den GenerateAnswer-Aufruf enthält die entsprechenden Metadateninformationen des übereinstimmenden Frage-Antwort-Satzes. Sie können diese Informationen in Ihrer Clientanwendung verwenden, um den Kontext der vorherigen Unterhaltung für die Verwendung in späteren Unterhaltungen zu speichern. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="match-questions-only-by-text"></a>Nur Fragen nach Textübereinstimmungen durchsuchen

Standardmäßig durchsucht QnA Maker Fragen und Antworten. Wenn Sie nur Fragen durchsuchen möchten, um eine Antwort zu generieren, verwenden Sie `RankerType=QuestionOnly` im POST-Text der GenerateAnswer-Anforderung.

Sie können mit `isTest=false` die veröffentlichte Wissensdatenbank oder mit `isTest=true` die Testwissensdatenbank durchsuchen.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="next-steps"></a>Nächste Schritte

Die Seite **Veröffentlichen** enthält auch Informationen zum Generieren einer Antwort mit [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) und [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Erstellen einer Wissensdatenbank](./create-knowledge-base.md)
