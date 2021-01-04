---
title: Durchführen von Stimmungsanalysen und Opinion Mining mit der Textanalyse-REST-API
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie mithilfe der Textanalyse-REST-API von Azure Cognitive Services die Stimmung sowie Meinungen in einem Text ermitteln.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 12/04/2020
ms.author: aahi
ms.openlocfilehash: 4f5c47000355a004f8d76d8b740b705618748380
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505221"
---
# <a name="how-to-sentiment-analysis-and-opinion-mining"></a>Vorgehensweise: Stimmungsanalyse und Opinion Mining

Das Standpunktanalysefeature der Textanalyse-API bietet zwei Möglichkeiten zur Erkennung von positiver und negativer Stimmung. Wenn Sie eine Standpunktanalyseanforderung senden, gibt die API Stimmungsbezeichnungen (z. B. „negativ“, „neutral“ und „positiv“) und Zuverlässigkeitsbewertungen auf Satz- und Dokumentebene zurück. Der Endpunkt der Standpunktanalyse kann auch zum Senden von Opinion Mining-Anforderungen verwendet werden. Dieses Feature liefert detaillierte Informationen zu den Meinungen in Bezug auf im Text enthaltene Aspekte (beispielsweise Attribute von Produkten oder Dienstleistungen). 

Die von der API verwendeten KI-Modelle werden vom Dienst bereitgestellt. Sie müssen lediglich Inhalte für die Analyse senden.

## <a name="sentiment-analysis-versions-and-features"></a>Versionen und Features der Standpunktanalyse

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Funktion                                   | Standpunktanalyse v3 | Standpunktanalyse v3.1 (Vorschauversion) |
|-------------------------------------------|-----------------------|-----------------------------------|
| Methoden für Einzel- und Batchabfragen    | X                     | X                                 |
| Bewertungen und Bezeichnungen der Standpunktanalyse             | X                     | X                                 |
| Linux-basierte [Docker-Container](text-analytics-how-to-install-containers.md) | X  |  |
| Opinion Mining                            |                       | X                                 |

## <a name="sentiment-analysis"></a>Standpunktanalyse

In Version 3.x wendet die Standpunktanalyse Stimmungsbezeichnungen auf Texte an, die auf Satz- und Dokumentebene zurückgegeben werden, und gibt jeweils eine Zuverlässigkeitsbewertung an. 

Die Bezeichnungen sind *positiv*, *negativ* und *neutral*. Auf Dokumentebene kann auch die Stimmungsbezeichnung *gemischt* zurückgegeben werden. Die Stimmung des Dokuments wird unten bestimmt:

| Stimmung von Sätzen                                                                            | Zurückgegebene Dokumentbezeichnung |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Mindestens ein `positive`-Satz ist im Dokument enthalten. Die restlichen Sätze sind `neutral`. | `positive`              |
| Mindestens ein `negative`-Satz ist im Dokument enthalten. Die restlichen Sätze sind `neutral`. | `negative`              |
| Mindestens ein `negative`- und mindestens ein `positive`-Satz ist im Dokument enthalten.    | `mixed`                 |
| Alle Sätze im Dokument sind `neutral`.                                                  | `neutral`               |

Zuverlässigkeitsbewertungen liegen zwischen 1 und 0. Werte, die näher an 1 liegen, weisen auf eine höhere Zuverlässigkeit der Bezeichnungsklassifizierung hin, während niedrigere Bewertungen eine geringere Zuverlässigkeit bedeuten. Für jedes Dokument oder jeden Satz werden die vorhergesagten Bewertungen, die den Bezeichnungen zugeordnet sind (positiv, negativ und neutral), bis zu 1 hinzugefügt.

## <a name="opinion-mining"></a>Opinion Mining

Opinion Mining ist ein Feature der Standpunktanalyse ab Version 3.1 Preview. Dieses Feature wird in der Verarbeitung natürlicher Sprache (Natural Language Processing, NLP) auch als aspektbasierte Standpunktanalyse bezeichnet und bietet feiner abgestufte Informationen zu den Meinungen in Bezug auf Aspekte (z. B. Attribute von Produkten oder Dienstleistungen) in Texten.

Wenn ein Kunde für ein Hotel beispielsweise Feedback wie „Das Zimmer war toll, aber das Personal war unfreundlich.“ hinterlässt, werden vom Opinion Mining Aspekte im Text sowie die zugehörigen Meinungen und Stimmungen ermittelt. Von der Standpunktanalyse wird unter Umständen nur eine negative Stimmung gemeldet.

:::image type="content" source="../media/how-tos/opinion-mining.png" alt-text="Diagramm: Opinion Mining-Beispiel" lightbox="../media/how-tos/opinion-mining.png":::

Wenn Sie Opinion Mining in Ihre Ergebnisse einbeziehen möchten, müssen Sie das Flag `opinionMining=true` in eine Stimmungsanalyseanforderung einschließen. Die Opinion Mining-Ergebnisse werden in die Antwort der Stimmungsanalyse eingeschlossen. Opinion Mining ist eine Standpunktanalyse-Erweiterung und in Ihrem aktuellen [Tarif](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) enthalten.


## <a name="sending-a-rest-api-request"></a>Senden einer REST-API-Anforderung 

### <a name="preparation"></a>Vorbereitung

Die Standpunktanalyse liefert bessere Ergebnisse, wenn Sie ihr kleinere Textmengen zuführen. Bei der Schlüsselbegriffserkennung verhält es sich genau umgekehrt: Sie funktioniert besser, wenn sie für große Textblöcke durchgeführt wird. Um für beide Vorgänge optimale Ergebnisse zu erzielen, empfiehlt es sich ggf., die Eingaben entsprechend umzustrukturieren.

Sie benötigen JSON-Dokumente im folgenden Format: ID, Text und Sprache. Die Standpunktanalyse unterstützt eine Vielzahl von Sprachen – weitere befinden sich in der Vorschau. Weitere Informationen finden Sie unter [Unterstützte Sprachen](../language-support.md).

Ein Dokument darf maximal 5.120 Zeichen enthalten. Die maximal zulässige Anzahl von Dokumenten in einer Sammlung finden Sie im Artikel [Datengrenzwerte und Ratenbegrenzungen für die Textanalyse-API](../concepts/data-limits.md?tabs=version-3) unter „Konzepte“. Die Sammlung wird im Hauptteil der Anforderung übermittelt.

## <a name="structure-the-request"></a>Strukturieren der Anforderung

Erstellen Sie eine POST-Anforderung. Um eine Anforderung schnell zu strukturieren und zu senden, können Sie in den folgenden Verweislinks [Postman](text-analytics-how-to-call-api.md) oder die **API-Testkonsole** verwenden. 

#### <a name="version-31-preview3"></a>[Version 3.1-preview.3](#tab/version-3-1)

[Referenz zu Standpunktanalyse v3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

#### <a name="version-30"></a>[Version 3.0](#tab/version-3)

[Referenz zu Standpunktanalyse v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

---

### <a name="request-endpoints"></a>Anforderungsendpunkte

Legen Sie den HTTPS-Endpunkt für die Standpunktanalyse entweder mithilfe einer Textanalyseressource in Azure oder mithilfe eines instanziierten [Textanalysecontainers](text-analytics-how-to-install-containers.md) fest. Sie müssen die richtige URL für die Version einschließen, die Sie verwenden möchten. Beispiel:

> [!NOTE]
> Den Schlüssel und den Endpunkt für Ihre Textanalyseressource finden Sie im Azure-Portal. Sie befinden sich auf der Seite **Schnellstart** der Ressource unter **Ressourcenverwaltung**. 

#### <a name="version-31-preview3"></a>[Version 3.1-preview.3](#tab/version-3-1)

**Standpunktanalyse**

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/sentiment`

**Opinion Mining**

Um Opinion Mining-Ergebnisse zu erhalten, muss der Parameter `opinionMining=true` eingeschlossen werden. Beispiel:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/sentiment?opinionMining=true`

Dieser Parameter ist standardmäßig auf `false` festgelegt. 

#### <a name="version-30"></a>[Version 3.0](#tab/version-3)

**Standpunktanalyse**

In Version 3.0 ist nur der Endpunkt für die Standpunktanalyse verfügbar.
 
`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

---

Legen Sie einen Anforderungsheader fest, um Ihren Textanalyse-API-Schlüssel einzubeziehen. Geben Sie im Anforderungstext die JSON-Dokumentsammlung an, die Sie für diese Analyse vorbereitet haben.

### <a name="example-request-for-sentiment-analysis-and-opinion-mining"></a>Beispielanforderung für Standpunktanalyse und Opinion Mining  

Das folgende Beispiel zeigt Inhalte, die Sie ggf. für die Standpunktanalyse übermitteln können: Das Anforderungsformat ist für `v3.0` und `v3.1-preview` identisch.
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>Übermitteln der Anforderung

Die Analyse erfolgt, wenn die Anforderung eingeht. Informationen zur Größe und Anzahl von Anforderungen, die Sie pro Minute und Sekunde senden können, finden Sie in der Übersicht im Abschnitt [Datengrenzwerte](../overview.md#data-limits).

Die Textanalyse-API ist zustandslos. Auf Ihrem Konto werden keine Daten gespeichert, und die Ergebnisse werden sofort in der Antwort zurückgegeben.


### <a name="view-the-results"></a>Zeigen Sie die Ergebnisse an

Die Ausgabe wird umgehend zurückgegeben. Sie können die Ergebnisse an eine Anwendung streamen, die JSON akzeptiert, oder die Ausgabe in einer Datei im lokalen System speichern. Importieren Sie dann die Ausgabe in eine Anwendung, mit der Sie die Daten sortieren, durchsuchen und bearbeiten können. Aufgrund der Unterstützung von Emojis und mehreren Sprachen enthält der Antworttext unter Umständen Textversätze. Weitere Informationen finden Sie unter [Textversätze in der Ausgabe der Textanalyse-API](../concepts/text-offsets.md).

#### <a name="version-31-preview3"></a>[Version 3.1-preview.3](#tab/version-3-1)

### <a name="sentiment-analysis-and-opinion-mining-example-response"></a>Beispielantwort für Standpunktanalyse und Opinion Mining

> [!IMPORTANT]
> Der folgende JSON-Code ist ein Beispiel für die Verwendung von Opinion Mining mit der Standpunktanalyse (in der API-Version 3.1). Wenn Sie kein Opinion Mining anfordern, ist die API-Antwort mit der Antwort auf der Registerkarte **Version 3.0** identisch.  

Von Version 3.1 der Standpunktanalyse können Antwortobjekte für die Standpunktanalyse und für das Opinion Mining zurückgegeben werden.
  
Die Standpunktanalyse gibt eine Stimmungsbezeichnung und eine Zuverlässigkeitsbewertung für das gesamte Dokument und jeden Satz darin zurück. Werte, die näher an 1 liegen, weisen auf eine höhere Zuverlässigkeit der Bezeichnungsklassifizierung hin, während niedrigere Bewertungen eine geringere Zuverlässigkeit bedeuten. Ein Dokument kann mehrere Sätze enthalten, und die Zuverlässigkeitsbewertungen in jedem Dokument oder Satz ergeben addiert 1.

Beim Opinion Mining werden Aspekte im Text sowie die zugehörigen Meinungen und Stimmungen ermittelt. In der nachstehenden Antwort weist der Satz *Im Restaurant gab es großartiges Essen, und der Kellner war freundlich* zwei Aspekte auf: *Essen* und *Kellner*. Die `relations`-Eigenschaft jedes Aspekts enthält einen `ref`-Wert mit dem URI-Verweis auf die zugeordneten Objekte `documents`, `sentences` und `opinions`.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly.",
                    "aspects": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 25,
                            "length": 4,
                            "text": "food",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/0"
                                }
                            ]
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 38,
                            "length": 6,
                            "text": "waiter",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/1"
                                }
                            ]
                        }
                    ],
                    "opinions": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 19,
                            "length": 5,
                            "text": "great",
                            "isNegated": false
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 49,
                            "length": 8,
                            "text": "friendly",
                            "isNegated": false
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

#### <a name="version-30"></a>[Version 3.0](#tab/version-3)

### <a name="sentiment-analysis-example-response"></a>Beispielantwort der Standpunktanalyse

Die Standpunktanalyse gibt eine Stimmungsbezeichnung und eine Zuverlässigkeitsbewertung für das gesamte Dokument und jeden Satz darin zurück. Werte, die näher an 1 liegen, weisen auf eine höhere Zuverlässigkeit der Bezeichnungsklassifizierung hin, während niedrigere Bewertungen eine geringere Zuverlässigkeit bedeuten. Ein Dokument kann mehrere Sätze enthalten, und die Zuverlässigkeitsbewertungen in jedem Dokument oder Satz ergeben addiert 1.

Antworten von Standpunktanalyse v3 enthalten Stimmungsbezeichnungen und Standpunktergebnisse für jeden analysierten Satz und jedes analysierte Dokument.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

---

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie sich mit Konzepten und dem Workflow für die Standpunktanalyse unter Verwendung der Textanalyse-API vertraut gemacht. Zusammenfassung:

+ Standpunktanalyse und Opinion Mining sind für ausgewählte Sprachen verfügbar.
+ JSON-Dokumente im Anforderungstext umfassen eine ID, Text und einen Sprachcode.
+ Die POST-Anforderung wird an einen Endpunkt vom Typ `/sentiment` gesendet. Dabei werden ein personalisierter [Zugriffsschlüssel und ein Endpunkt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) verwendet, der für Ihr Abonnement gültig ist.
+ Verwenden Sie `opinionMining=true` in Standpunktanalyseanforderungen, um Opinion Mining-Ergebnisse zu erhalten.
+ Bei der Antwortausgabe handelt es sich um eine Stimmungspunktzahl für die jeweilige Dokument-ID. Sie kann an eine beliebige JSON-fähige App gestreamt werden. Beispielsweise Excel und Power BI.

## <a name="see-also"></a>Weitere Informationen

* [Übersicht über die Textanalyse](../overview.md)
* [Verwenden der Textanalyse-Clientbibliothek](../quickstarts/client-libraries-rest-api.md)
* [Neuigkeiten](../whats-new.md)
