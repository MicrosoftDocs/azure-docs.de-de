---
title: Verbessern der Wissensdatenbank – QnA Maker
titleSuffix: Azure Cognitive Services
description: Durch aktives Lernen lässt sich die Qualität Ihrer Wissensdatenbank verbessern, indem auf der Grundlage von Benutzerbeiträgen alternative Fragen zu Ihrem Frage-Antwort-Paar vorgeschlagen werden. Sie überprüfen diese Vorschläge, indem Sie sie entweder zu vorhandenen Fragen hinzufügen oder ablehnen. Ihre Wissensdatenbank wird nicht automatisch geändert. Sie müssen die Vorschläge akzeptieren, damit eine Änderung wirksam wird. Mit diesen Vorschlägen werden Fragen hinzugefügt, vorhandene Fragen werden aber weder geändert noch entfernt.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/13/2019
ms.author: diberry
ms.openlocfilehash: f80e6a765cc165033a548ba6a5ee7bead0de872e
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65594090"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Nutzen des aktiven Lernens zum Verbessern Ihrer Wissensdatenbank

Durch aktives Lernen lässt sich die Qualität Ihrer Wissensdatenbank verbessern, indem auf der Grundlage von Benutzerbeiträgen alternative Fragen zu Ihrem Frage-Antwort-Paar vorgeschlagen werden. Sie überprüfen diese Vorschläge, indem Sie sie entweder zu vorhandenen Fragen hinzufügen oder ablehnen. 

Ihre Wissensdatenbank wird nicht automatisch geändert. Sie müssen die Vorschläge akzeptieren, damit eine Änderung wirksam wird. Mit diesen Vorschlägen werden Fragen hinzugefügt, vorhandene Fragen werden aber weder geändert noch entfernt.

## <a name="what-is-active-learning"></a>Was ist aktives Lernen?

QnA Maker erlernt neue Fragevarianten über implizites und explizites Feedback.
 
* Implizites Feedback: Die Bewertung erkennt, wenn für eine Benutzerfrage mehrere Antworten vorliegen, deren Bewertungen sehr nah beieinander liegen, und betrachtet dies als Feedback. 
* Explizites Feedback: Wenn von der Wissensdatenbank mehrere Antworten zurückgegeben werden, deren Bewertungen sich kaum unterscheiden, fordert die Clientanwendung den Benutzer auf, die richtige Frage auszuwählen. Das explizite Feedback des Benutzers wird mit der Trainings-API an QnA Maker gesendet. 

Über beide Methoden werden der Bewertung ähnliche Abfragen bereitgestellt, die gruppiert werden.

## <a name="how-active-learning-works"></a>Aktives Lernen: Funktionsweise

Das Feature für aktives Lernen wird basierend auf den Bewertungen der relevantesten Antworten ausgelöst, die von QnA Maker für eine bestimmte Abfrage zurückgegeben werden. Liegt der Bewertungsunterschied innerhalb einer geringen Bandbreite, dann wird die Abfrage als möglicher _Vorschlag_ für jede der möglichen Antworten betrachtet. 

Alle Vorschläge werden nach Ähnlichkeit gruppiert, und die besten Vorschläge für alternative Fragen werden basierend auf der Häufigkeit der jeweiligen Abfragen von Endbenutzern angezeigt. Das Feature für aktives Lernen liefert die bestmöglichen Vorschläge in Fällen, in denen die Endpunkte eine angemessene Anzahl und Vielfalt von Nutzungsabfragen erhalten.

Wenn mindestens 5 ähnliche Abfragen gruppiert werden, sendet QnA Maker alle 30 Minuten die benutzerbasierten Fragen als Vorschläge an den Entwickler der Wissensdatenbank, um diese zu akzeptieren oder abzulehnen.

Sobald Fragen im QnA Maker-Portal vorgeschlagen werden, müssen Sie diese überprüfen und akzeptieren oder ablehnen. 

## <a name="upgrade-your-version-to-use-active-learning"></a>Ausführen eines Upgrades Ihrer Version, um aktives Lernen zu nutzen

Das Feature für aktives Lernen wird in der Runtimeversion 4.4.0 und höher unterstützt. Wenn Ihre Wissensdatenbank mit einer Vorgängerversion erstellt wurde, [führen Sie ein Upgrade Ihrer Runtime durch](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates), um dieses Feature zu nutzen. 

## <a name="best-practices"></a>Bewährte Methoden

Bewährte Methoden bei der Verwendung des Features für aktives Lernen finden Sie unter [Best Practices](../Concepts/best-practices.md#active-learning).

## <a name="score-proximity-between-knowledge-base-questions"></a>Nähe der Bewertungen bei Fragen der Wissensdatenbank

Wenn die Bewertung einer Frage eine hohe Zuverlässigkeit aufweist, wie z.B. 80%, ist die Bandbreite der für das aktive Lernen in Betracht gezogenen Bewertungen mit etwa 10% recht groß. Wenn der Zuverlässigkeitswert einer Bewertung sinkt, z.B. auf 40%, nimmt auch die Bandbreite der Bewertungen ab und liegt nur etwa innerhalb von 4%. 

Der Algorithmus zur Bestimmung der Nähe ist keine einfache Berechnung. Die Bereiche in den vorstehenden Beispielen sind keine festen Werte, sondern dienen als Richtlinie zum Verständnis der Auswirkungen des Algorithmus.

## <a name="turn-on-active-learning"></a>Aktivieren des Features für aktives Lernen

Das Feature für aktives Lernen ist standardmäßig deaktiviert. Aktivieren Sie das Feature, um die vorgeschlagenen Fragen anzuzeigen. 

1. Wählen Sie **Veröffentlichen** aus, um die Wissensdatenbank zu veröffentlichen. Aktive Lernabfragen werden nur vom Vorhersageendpunkt der GenerateAnswer-API erfasst. Die Abfragen im Bereich „Test“ im QnA Maker-Portal besitzen keine Auswirkungen auf das aktive Lernen.

1. Um das Feature für aktives Lernen zu aktivieren, klicken Sie auf Ihren **Namen**. Wechseln Sie dann rechts oben im QnA Maker-Portal zu [**Diensteinstellungen**](https://www.qnamaker.ai/UserSettings).  

    ![Aktivieren Sie auf der Seite „Diensteinstellungen“ Alternativen für die vom Feature für aktives Lernen vorgeschlagenen Fragen. Wählen Sie im Menü rechts oben Ihren Benutzernamen und dann „Diensteinstellungen“ aus.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Suchen Sie nach dem QnA Maker-Dienst, und aktivieren Sie die Option **Active Learning**. 

    [![Aktivieren Sie auf der Seite „Diensteinstellungen“ das Feature für aktives Lernen. Wenn Sie das Feature nicht aktivieren können, müssen Sie möglicherweise ein Upgrade des Diensts ausführen.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Nachdem Sie **Active Learning** aktiviert haben, schlägt die Wissensdatenbank basierend auf den von Benutzern übermittelten Fragen in regelmäßigen Abständen neue Fragen vor. Sie können **Active Learning** deaktivieren, indem Sie die Einstellung wieder umschalten.

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>Hinzufügen von Vorschlägen zur Wissensdatenbank über das Feature für aktives Lernen

1. Um die vorgeschlagenen Fragen anzuzeigen, wählen Sie auf der Seite **Bearbeiten** der Wissensdatenbank **Optionen anzeigen** und danach **Show active learning suggestions** (Vorschläge für aktives Lernen anzeigen) aus. 

    [![Wählen Sie im Bereich „Bearbeiten“ des Portals „Vorschläge anzeigen“ aus, um die Alternativen für neue Fragen des Features für aktives Lernen anzuzeigen.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtern Sie die Wissensdatenbank nach Frage-Antwort-Paaren, um nur Vorschläge anzuzeigen, indem Sie **Nach Vorschlägen filtern** auswählen.

    [![Verwenden Sie den Umschalter „Nach Vorschlägen filtern“, um nur die vom Feature für aktives Lernen vorgeschlagenen Alternativen für Fragen anzuzeigen.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  Jeder Fragenabschnitt mit Vorschlägen zeigt die neuen Fragen mit einem Häkchen (`✔`) an, um die Frage zu akzeptieren, oder mit einem Kreuz (`x`), um die Vorschläge abzulehnen. Aktivieren Sie das Häkchen, um die Frage hinzuzufügen. 

    [![Sie können die vorgeschlagenen die vom Feature für aktives Lernen vorgeschlagenen Alternativen für Fragen auswählen oder ablehnen, indem Sie auf das grüne Häkchen oder das rote Löschsymbol klicken.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Sie können _alle Vorschläge_ hinzufügen oder löschen, indem Sie **Alle hinzufügen** oder **Alle ablehnen** auswählen.

1. Wählen Sie **Speichern und trainieren** aus, um die Änderungen an der Wissensdatenbank zu speichern.

1. Wählen Sie **Veröffentlichen** aus, damit die Änderungen über die GenerateAnswer-API zur Verfügung stehen.

    Wenn mindestens 5 ähnliche Abfragen gruppiert werden, sendet QnA Maker alle 30 Minuten die benutzerbasierten Fragen als Vorschläge an den Entwickler der Wissensdatenbank, um diese zu akzeptieren oder abzulehnen.

## <a name="determine-best-choice-when-several-questions-have-similar-scores"></a>Bestimmen der besten Auswahl bei mehreren Fragen mit ähnlichen Bewertungen

Wenn eine Frage bezüglich ihrer Bewertung zu nahe an anderen Fragen liegt, kann der Entwickler der Clientanwendung um Klärung bitten.

### <a name="use-the-top-property-in-the-generateanswer-request"></a>Verwenden der top-Eigenschaft in der GenerateAnswer-Anforderung

Wenn Sie eine Frage zur Beantwortung an QnA Maker übermitteln, ermöglicht es der JSON-Text, mehr als eine Top-Antwort zurückzugeben:

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

Wenn die Clientanwendung (in diesem Fall der Chatbot) die Antwort empfängt, werden die drei relevantesten Fragen zurückgegeben:

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
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

### <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Nachverfolgung durch die Clientanwendung bei Fragen mit ähnlicher Bewertung

Die Clientanwendung zeigt alle Fragen an und gibt dem Benutzer die Möglichkeit, die am besten geeignete Frage auszuwählen. 

Sobald der Benutzer eine der vorhandenen Fragen ausgewählt hat, sendet die Clientanwendung die Auswahl des Benutzers als Feedback mithilfe der Trainings-API von QnA Maker. Dieses Feedback vervollständigt die Feedbackschleifen des Features für aktives Lernen. 

Verwenden Sie das [Beispiel für Azure Bot](https://aka.ms/activelearningsamplebot), um aktives Lernen in einem End-to-End-Szenario zu verfolgen.

## <a name="train-api"></a>Trainings-API

Feedback zum Feature für aktives Lernen wird über eine POST-Anforderung der Trainings-API an QnA Maker gesendet. Die API-Signatur ist wie folgt:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP-Anforderungseigenschaft|NAME|Type|Zweck|
|--|--|--|--|
|URL-Routenparameter|Wissensdatenbank-ID|Zeichenfolge|GUID der Knowledge Base|
|Unterdomäne des Hosts|Name der QnAMaker-Ressource|Zeichenfolge|Der Hostname für Ihren QnA Maker in Ihrem Azure-Abonnement. Dieser Wert ist auf der Seite „Einstellungen“ verfügbar, nachdem Sie die Wissensdatenbank veröffentlicht haben. |
|Header|Content-Typ|Zeichenfolge|Medientyp des an die API gesendeten Texts Der Standardwert ist: `application/json`|
|Header|Autorisierung|Zeichenfolge|Ihr Endpunktschlüssel (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Posttext|JSON-Objekt|JSON|Das Feedback zum Training|

Der JSON-Text verfügt über mehrere Einstellungen:

|JSON-Texteigenschaft|Type|Zweck|
|--|--|--|--|
|`feedbackRecords`|Array|Liste des Feedbacks|
|`userId`|Zeichenfolge|Die Benutzer-ID der Person, die die vorgeschlagenen Fragen akzeptiert. Das Format der Benutzer-ID bleibt Ihnen überlassen. Beispielsweise kann eine E-Mail-Adresse eine in Ihrer Architektur gültige Benutzer-ID sein. Optional.|
|`userQuestion`|Zeichenfolge|Der genaue Text der Frage. Erforderlich.|
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

<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Aktives Lernen wird in der exportierten Wissensdatenbank gespeichert.

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

## <a name="next-steps"></a>Nächste Schritte
 
> [!div class="nextstepaction"]
> [Verwenden von Metadaten mit der GenerateAnswer-API](metadata-generateanswer-usage.md)
