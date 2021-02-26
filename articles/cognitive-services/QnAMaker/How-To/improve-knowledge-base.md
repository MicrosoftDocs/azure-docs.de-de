---
title: 'Fragevorschläge für aktives Lernen: QnA Maker'
description: Nutzen Sie aktives Lernen, um die Qualität Ihrer Wissensdatenbank zu verbessern. Sie können Einträge überprüfen, annehmen oder ablehnen und hinzufügen, ohne vorhandene Fragen zu entfernen oder zu ändern.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 7c477655dfb24eebab9a2669697d9ef610088198
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592023"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>Akzeptieren von Fragevorschlägen des aktiven Lernens in der Wissensdatenbank


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

Aktives Lernen ändert die Wissensdatenbank oder den Suchdienst, nachdem Sie den Vorschlag bestätigt und dann gespeichert und trainiert haben. Wenn Sie den Vorschlag bestätigen, wird er als alternative Frage hinzugefügt.

## <a name="turn-on-active-learning"></a>Aktivieren des Features für aktives Lernen

Damit vorgeschlagene Fragen angezeigt werden, müssen Sie für Ihre QnA Maker-Ressource [aktives Lernen aktivieren](../concepts/active-learning-suggestions.md).

## <a name="view-suggested-questions"></a>Anzeigen vorgeschlagener Fragen

1. Um die vorgeschlagenen Fragen anzuzeigen, wählen Sie auf der Seite **Bearbeiten** der Wissensdatenbank **Optionen anzeigen** und danach **Show active learning suggestions** (Vorschläge für aktives Lernen anzeigen) aus. Diese Option wird deaktiviert, wenn für die Frage-Antwort-Paare keine Vorschläge vorhanden sind.  

    [![Wählen Sie im Bereich „Bearbeiten“ des Portals „Vorschläge anzeigen“ aus, um die Alternativen für neue Fragen des Features für aktives Lernen anzuzeigen.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtern Sie die Wissensdatenbank nach Frage-Antwort-Paaren, um nur Vorschläge anzuzeigen, indem Sie **Nach Vorschlägen filtern** auswählen.

    [![Verwenden Sie den Umschalter „Nach Vorschlägen filtern“, um nur die vom Feature für aktives Lernen vorgeschlagenen Alternativen für Fragen anzuzeigen.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Bei den einzelnen QnA-Paaren stehen für die vorgeschlagenen neuen Fragealternativen jeweils ein Häkchen (`✔`) zum Akzeptieren der Frage und ein Kreuz (`x`) zum Ablehnen der Vorschläge zur Verfügung. Aktivieren Sie das Häkchen, um die Frage hinzuzufügen.

    [![Sie können die vorgeschlagenen die vom Feature für aktives Lernen vorgeschlagenen Alternativen für Fragen auswählen oder ablehnen, indem Sie auf das grüne Häkchen oder das rote Löschsymbol klicken.](../media/improve-knowledge-base/accept-active-learning-suggestions-small.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Sie können _alle Vorschläge_ hinzufügen oder löschen, indem Sie auf der kontextbezogenen Symbolleiste **Alle hinzufügen** oder **Alle ablehnen** auswählen.

1. Wählen Sie **Speichern und trainieren** aus, um die Änderungen an der Wissensdatenbank zu speichern.

1. Wählen Sie **Veröffentlichen** aus, damit die Änderungen über die [GenerateAnswer-API](metadata-generateanswer-usage.md#generateanswer-request-configuration) zur Verfügung stehen.

    Wenn mindestens fünf ähnliche Abfragen gruppiert wurden, schlägt Ihnen QnA Maker alle 30 Minuten die alternativen Fragen vor, die Sie dann akzeptieren oder ablehnen können.


<a name="#score-proximity-between-knowledge-base-questions"></a>

## <a name="active-learning-suggestions-are-saved-in-the-exported-knowledge-base"></a>Vorschläge für aktives Lernen werden in der exportierten Wissensdatenbank gespeichert.

Wenn aktives Lernen für Ihre App aktiviert ist Sie die App exportieren, enthält die Spalte `SuggestedQuestions` in der TSV-Datei die Daten für aktives Lernen.

Die Spalte `SuggestedQuestions` ist ein JSON-Objekt mit Informationen zu implizitem (`autosuggested`) und explizitem (`usersuggested`) Feedback. Ein Beispiel für dieses JSON-Objekt für eine einzelne, vom Benutzer übermittelte Bitte um `help` ist:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

Wenn Sie diese App erneut importieren, erfasst das aktive Lernen weiterhin Informationen und empfiehlt Vorschläge für Ihre Wissensdatenbank.


### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Architekturfluss für die Verwendung der GenerateAnswer- und der Trainings-API über einen Bot

Von einem Bot oder einer anderen Clientanwendung muss zur Verwendung des aktiven Lernens der folgende Architekturfluss verwendet werden:

* Der Bot [ruft die Antwort aus der Wissensdatenbank ab](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) (mit der GenerateAnswer-API) und verwendet dabei die Eigenschaft `top`, um eine Reihe von Antworten zu erhalten.

    #### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Verwenden der Eigenschaft „top“ in der GenerateAnswer-Anforderung, um mehrere passende Antworten abzurufen

    Wenn Sie eine Frage zur Beantwortung an QnA Maker senden, legt die Eigenschaft `top` im JSON-Text die Anzahl der zurückzugebenden Antworten fest.

    ```json
    {
        "question": "wi-fi",
        "isTest": false,
        "top": 3
    }
    ```

* Der Bot bestimmt explizites Feedback:
    * Niedrige Bewertungen werden mit Ihrer eigenen [benutzerdefinierten Geschäftslogik](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user) herausgefiltert.
    * Im Bot oder in der Clientanwendung wird dem Benutzer eine Liste mit möglichen Antworten angezeigt und die vom Benutzer ausgewählte Antwort abgerufen.
* Der Bot [sendet die ausgewählte Antwort an QnA Maker zurück](#bot-framework-sample-code) (mit der [Trainings-API](#train-api)).


### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Verwenden der Eigenschaft „score“ in Kombination mit Geschäftslogik, um eine Liste mit Antworten abzurufen, die dem Benutzer angezeigt werden kann

Wenn die Clientanwendung (beispielsweise ein Chatbot) die Antwort empfängt, werden die drei relevantesten Fragen zurückgegeben. Verwenden Sie die Eigenschaft `score`, um die Nähe zwischen den Bewertungen zu analysieren. Dieser Nähebereich wird durch Ihre eigene Geschäftslogik bestimmt.

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ *+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Nachverfolgung durch die Clientanwendung bei Fragen mit ähnlicher Bewertung

Ihre Clientanwendung zeigt die Fragen an und gibt dem Benutzer die Möglichkeit, _die am besten geeignete Frage_ auszuwählen.

Sobald der Benutzer eine der vorhandenen Fragen ausgewählt hat, sendet die Clientanwendung die Auswahl des Benutzers als Feedback mithilfe der Trainings-API von QnA Maker. Dieses Feedback vervollständigt die Feedbackschleifen des Features für aktives Lernen.

## <a name="train-api"></a>Trainings-API

Feedback zum Feature für aktives Lernen wird über eine POST-Anforderung der Trainings-API an QnA Maker gesendet. Die API-Signatur ist wie folgt:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP-Anforderungseigenschaft|Name|type|Zweck|
|--|--|--|--|
|URL-Routenparameter|Wissensdatenbank-ID|Zeichenfolge|GUID der Knowledge Base|
|Benutzerdefinierte Unterdomäne|Name der QnAMaker-Ressource|Zeichenfolge|Der Ressourcenname wird als benutzerdefinierte Unterdomäne für Ihre QnA Maker verwendet. Dieser Wert ist auf der Seite „Einstellungen“ verfügbar, nachdem Sie die Wissensdatenbank veröffentlicht haben. Sie wird als `host` aufgeführt.|
|Header|Content-Type|Zeichenfolge|Medientyp des an die API gesendeten Texts Der Standardwert ist: `application/json`|
|Header|Authorization|Zeichenfolge|Ihr Endpunktschlüssel (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Posttext|JSON-Objekt|JSON|Das Feedback zum Training|

Der JSON-Text verfügt über mehrere Einstellungen:

|JSON-Texteigenschaft|type|Zweck|
|--|--|--|--|
|`feedbackRecords`|array|Liste des Feedbacks|
|`userId`|Zeichenfolge|Die Benutzer-ID der Person, die die vorgeschlagenen Fragen akzeptiert. Das Format der Benutzer-ID bleibt Ihnen überlassen. Beispielsweise kann eine E-Mail-Adresse eine in Ihrer Architektur gültige Benutzer-ID sein. Optional.|
|`userQuestion`|Zeichenfolge|Exakter Text der Benutzerabfrage. Erforderlich.|
|`qnaID`|number|Die ID der Frage in der [GenerateAnswer-Antwort](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Ein JSON-Beispieltext sieht folgendermaßen aus:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

Eine erfolgreiche Antwort gibt den Status 204 und einen JSON-Antworttext zurück.

### <a name="batch-many-feedback-records-into-a-single-call"></a>Zusammenfassen zahlreicher Feedbackdatensätze in einem einzelnen Aufruf

Sie können die Daten in der clientseitigen Anwendung (etwa in einem Bot) speichern und dann mithilfe des Arrays `feedbackRecords` zahlreiche Datensätze in einem einzelnen JSON-Text senden.

Ein JSON-Beispieltext sieht folgendermaßen aus:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Bot Framework-Beispielcode

Ihr Bot Framework-Code muss die Trainings-API aufrufen, wenn die Abfrage des Benutzers für aktives Lernen verwendet werden soll. Es müssen zwei Codekomponenten geschrieben werden:

* Bestimmen, ob die Abfrage für aktives Lernen verwendet werden soll
* Zurücksenden der Abfrage an die Trainings-API für aktives Lernen von QnA Maker

Im [Azure-Botbeispiel](https://github.com/microsoft/BotBuilder-Samples) wurden beide Aktivitäten programmiert.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>C#-Beispielcode für die Trainings-API mit Bot Framework 4.x

Der folgende Code veranschaulicht das Zurücksenden von Informationen an QnA Maker mithilfe der Trainings-API.

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Node.js-Beispielcode für die Trainings-API mit Bot Framework 4.x

Der folgende Code veranschaulicht das Zurücksenden von Informationen an QnA Maker mithilfe der Trainings-API.

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="best-practices"></a>Bewährte Methoden

Bewährte Methoden bei der Verwendung des Features für aktives Lernen finden Sie unter [Best Practices](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwenden von Metadaten mit der GenerateAnswer-API](metadata-generateanswer-usage.md)
