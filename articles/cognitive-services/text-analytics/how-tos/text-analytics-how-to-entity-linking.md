---
title: Verwenden der Entitätserkennung mit der Textanalyse-API
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die Identität einer im Text gefundenen Entität mit der Textanalyse-REST-API identifizieren und unterscheiden können.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 12/15/2020
ms.author: aahi
ms.openlocfilehash: 9b90f177432de11f8281d03021b38bae647dadf2
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562530"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Verwenden der Erkennung benannter Entitäten in der Textanalyse

Mit der Textanalyse-API kann aus unstrukturiertem Text eine Liste mit eindeutig unterscheidbaren Entitäten und Links zu weiteren Informationen im Web zurückgegeben werden. Die API unterstützt sowohl benannte Entitätserkennung (Named Entity Recognition, NER) für mehrere Entitätskategorien als auch Entitätsverknüpfung.

## <a name="entity-linking"></a>Entitätsverknüpfung

Die Entitätsverknüpfung ist die Möglichkeit, die Identität einer im Text gefundenen Entität zu identifizieren und eindeutig zu machen (um beispielsweise zu ermitteln, ob das Wort „Mars“ den Planeten oder den römischen Kriegsgott bezeichnet). Um in diesem Prozess erkannte Entitäten im Text zu verknüpfen, ist eine Wissensdatenbank in der entsprechenden Sprache erforderlich. Die Entitätsverknüpfung verwendet [Wikipedia](https://www.wikipedia.org/) als diese Wissensdatenbank.

## <a name="named-entity-recognition-ner"></a>Erkennung benannter Entitäten (NER)

NER (Named Entity Recognition) ist die Möglichkeit, unterschiedliche Entitäten im Text zu identifizieren und sie in vordefinierte Klassen oder Typen zu kategorisieren wie z. B.: „Person“, „Standort“, „Ereignis“, „Produkt“ und „Organisation“.  

## <a name="personally-identifiable-information-pii"></a>Personenbezogene Informationen (Personally Identifiable Information, PII)

Das Feature PII ist ein Bestandteil von NER und kann vertrauliche Entitäten in Text identifizieren und bearbeiten, die einer einzelnen Person zugeordnet sind, wie z. B.: Telefonnummer, E-Mail-Adresse, Postanschrift, Reisepassnummer.

## <a name="named-entity-recognition-features-and-versions"></a>Features und Versionen der Erkennung benannter Entitäten

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Funktion                                                         | NER v3.0 | NER v3.1-preview.3 |
|-----------------------------------------------------------------|--------|----------|
| Methoden für Einzel- und Batchabfragen                          | X      | X        |
| Erweiterte Entitätserkennung in verschiedenen Kategorien           | X      | X        |
| Separate Endpunkte zum Senden von Entitätsverknüpfungs- und NER-Anforderungen. | X      | X        |
| Erkennung von Entitäten mit personenbezogenen (`PII`) und gesundheitsbezogenen (`PHI`) Informationen        |        | X        |
| Bearbeitung von `PII`        |        | X        |

Weitere Informationen finden Sie unter [Sprachunterstützung](../language-support.md).

Named Entity Recognition v3 bietet erweiterte Erkennung über mehrere Typen hinweg. Derzeit können mit NER v3.0 Entitäten der [Kategorie mit den allgemeinen Entitäten](../named-entity-types.md) erkannt werden.

Named Entity Recognition v3.1-preview.3 enthält die Erkennungsfunktionen von v3.0 sowie Folgendes: 
* Die Möglichkeit zur Erkennung personenbezogener Informationen (`PII`) mithilfe des Endpunkts `v3.1-preview.3/entities/recognition/pii`. 
* Einen optionalen Parameter `domain=phi` zur Erkennung vertraulicher gesundheitsbezogener Informationen (`PHI`).
* Einen [asynchronen Vorgang](text-analytics-how-to-call-api.md) mithilfe des Endpunkts `/analyze`.

Weitere Informationen finden Sie im Artikel zu [Entitätskategorien](../named-entity-types.md) und unten im Abschnitt [Anforderungsendpunkte](#request-endpoints). 

## <a name="sending-a-rest-api-request"></a>Senden einer REST-API-Anforderung

### <a name="preparation"></a>Vorbereitung

Sie benötigen JSON-Dokumente im folgenden Format: ID, Text, Sprache.

Jedes Dokument darf jeweils maximal 5.120 Zeichen groß sein, und pro Sammlung sind bis zu 1.000 Elemente (IDs) zulässig. Die Sammlung wird im Hauptteil der Anforderung übermittelt.

### <a name="structure-the-request"></a>Strukturieren der Anforderung

Erstellen Sie eine POST-Anforderung. Um eine Anforderung schnell zu strukturieren und zu senden, können Sie in den folgenden Links [Postman](text-analytics-how-to-call-api.md) oder die **API-Testkonsole** verwenden. 

> [!NOTE]
> Den Schlüssel und den Endpunkt für Ihre Textanalyseressource finden Sie im Azure-Portal. Sie befinden sich auf der Seite **Schnellstart** der Ressource unter **Ressourcenverwaltung**. 


### <a name="request-endpoints"></a>Anforderungsendpunkte

#### <a name="version-31-preview3"></a>[Version 3.1-preview.3](#tab/version-3-preview)

In „Erkennung benannter Entitäten“ `v3.1-preview.3` werden separate Endpunkte für NER, personenbezogene Informationen und Entitätsverknüpfungsanforderungen verwendet. Verwenden Sie basierend auf Ihrer Anforderung eines der URL-Formate unten.

**Entitätsverknüpfung**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/linking`

[Referenz zur Version 3.1-preview der Erkennung benannter Entitäten für `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesLinking)

**Erkennung benannter Entitäten**
* Allgemeine Entitäten: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/general`

[Referenz zur Version 3.1-preview der Erkennung benannter Entitäten für `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesRecognitionGeneral)

**Personenbezogene Informationen (Personally Identifiable Information, PII)**
* Personenbezogene Informationen (`PII`): `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii`

Sie können auch den optionalen Parameter `domain=phi` verwenden, um im Text Informationen zur Gesundheit (`PHI`) zu erkennen. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi`

Ab `v3.1-preview.3` enthält die JSON-Antwort eine Eigenschaft `redactedText` mit dem geänderten Eingabetext, in dem die erkannten Entitäten personenbezogener Informationen durch ein `*` für jedes Zeichen in den Entitäten ersetzt werden.

[Referenz zur Version 3.1-preview der Erkennung benannter Entitäten für `PII`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesRecognitionPii)

**Asynchroner Vorgang**

Ab `v3.1-preview.3` können Sie NER-Anforderungen mithilfe des `/analyze`-Endpunkts asynchron senden.

* Asynchroner Vorgang – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze`

Informationen zum Senden von asynchronen Anforderungen finden Sie unter [Aufrufen der Textanalyse-API](text-analytics-how-to-call-api.md).

#### <a name="version-30"></a>[Version 3.0](#tab/version-3)

Named Entity Recognition v3 verwendet separate Endpunkte für NER-Anforderungen und Entitätsverknüpfungsanforderungen. Verwenden Sie basierend auf Ihrer Anforderung ein URL-Format unten:

**Entitätsverknüpfung**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

[Referenz zur Version 3.0 der Erkennung benannter Entitäten für `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

**Erkennung benannter Entitäten**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[Referenz zur Version 3.0 der Erkennung benannter Entitäten für `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

---

Legen Sie einen Anforderungsheader fest, um Ihren Textanalyse-API-Schlüssel einzubeziehen. Geben Sie im Anforderungstext die von Ihnen vorbereiteten JSON-Dokumente an.

## <a name="example-requests"></a>Beispielanforderungen

#### <a name="version-31-preview"></a>[Version 3.1-preview](#tab/version-3-preview)

### <a name="example-synchronous-ner-request"></a>Beispiel einer synchronen NER-Anforderung 

Das folgende JSON-Beispiel zeigt Inhalte, die Sie ggf. an die API senden können. Das Anforderungsformat ist für beide Versionen der API identisch.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}
```

### <a name="example-asynchronous-ner-request"></a>Beispiel einer asynchronen NER-Anforderung

Wenn Sie den `/analyze`-Endpunkt für [asynchrone Vorgänge](text-analytics-how-to-call-api.md) verwenden, erhalten Sie eine Antwort, die die Aufgaben enthält, die Sie an die API gesendet haben.

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}
```

#### <a name="version-30"></a>[Version 3.0](#tab/version-3)

### <a name="example-synchronous-ner-request"></a>Beispiel einer synchronen NER-Anforderung 

Version 3.0 enthält nur den synchronen Vorgang. Das folgende JSON-Beispiel zeigt Inhalte, die Sie ggf. an die API senden können. Das Anforderungsformat ist für beide Versionen der API identisch.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}
```

---

## <a name="post-the-request"></a>Übermitteln der Anforderung

Die Analyse erfolgt, wenn die Anforderung eingeht. Weitere Informationen zur Größe und Anzahl von Anforderungen, die Sie pro Minute und Sekunde senden können, finden Sie in der Übersicht im Abschnitt [Datengrenzwerte](../overview.md#data-limits).

Die Textanalyse-API ist zustandslos. Auf Ihrem Konto werden keine Daten gespeichert, und die Ergebnisse werden sofort in der Antwort zurückgegeben.

## <a name="view-results"></a>Anzeigen der Ergebnisse

Alle POST-Anforderungen geben eine Antwort im JSON-Format mit den IDs und erkannten Entitätseigenschaften zurück.

Die Ausgabe wird umgehend zurückgegeben. Sie können die Ergebnisse an eine Anwendung streamen, die JSON akzeptiert, oder die Ausgabe in einer Datei auf dem lokalen System speichern und sie anschließend in eine Anwendung importieren, in der Sie die Daten sortieren, durchsuchen und bearbeiten können. Aufgrund der Unterstützung von Emojis und mehreren Sprachen enthält der Antworttext unter Umständen Textversätze. Weitere Informationen finden Sie unter [Verarbeiten von Textversätzen](../concepts/text-offsets.md).

### <a name="example-responses"></a>Beispielantworten

Version 3 stellt separate Endpunkte für die allgemeine Erkennung benannter Entitäten (NER), personenbezogene Informationen und Entitätsverknüpfung bereit. Version 3.1-preview enthält einen asynchronen Analysemodus. Die Antworten für diese Vorgänge finden Sie unten. 

#### <a name="version-31-preview"></a>[Version 3.1-preview](#tab/version-3-preview)

### <a name="synchronous-example-results"></a>Ergebnisse des Beispiels für einen synchronen Vorgang

Beispiel für eine Antwort mit allgemeiner Erkennung benannter Entitäten:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

Beispiel für eine Antwort mit personenbezogenen Informationen:

```json
{
  "documents": [
    {
    "redactedText": "You can even pre-order from their online menu at *************************, call ************ or send email to ***************************!",
    "id": "0",
    "entities": [
        {
        "text": "www.contososteakhouse.com",
        "category": "URL",
        "offset": 49,
        "length": 25,
        "confidenceScore": 0.8
        }, 
        {
        "text": "312-555-0176",
        "category": "Phone Number",
        "offset": 81,
        "length": 12,
        "confidenceScore": 0.8
        }, 
        {
        "text": "order@contososteakhouse.com",
        "category": "Email",
        "offset": 111,
        "length": 27,
        "confidenceScore": 0.8
        }
      ],
    "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-07-01"
}
```

Beispiel für eine Antwort mit Entitätsverknüpfung:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e", 
          "name": "Space Needle",
          "matches": [
            {
              "text": "Space Needle",
              "offset": 30,
              "length": 12,
              "confidenceScore": 0.4
            }
          ],
          "language": "en",
          "id": "Space Needle",
          "url": "https://en.wikipedia.org/wiki/Space_Needle",
          "dataSource": "Wikipedia"
        },
        {
          "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
          "name": "Seattle",
          "matches": [
            {
              "text": "Seattle",
              "offset": 62,
              "length": 7,
              "confidenceScore": 0.25
            }
          ],
          "language": "en",
          "id": "Seattle",
          "url": "https://en.wikipedia.org/wiki/Seattle",
          "dataSource": "Wikipedia"
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-02-01"
}
```

### <a name="example-asynchronous-result"></a>Ergebnis des Beispiels für einen asynchronen Vorgang

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


#### <a name="version-30"></a>[Version 3.0](#tab/version-3)

Beispiel für eine Antwort mit allgemeiner Erkennung benannter Entitäten:
```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
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

In diesem Artikel haben Sie sich mit Konzepten und mit dem Workflow für die Entitätsverknüpfung unter Verwendung der Textanalyse in Cognitive Services vertraut gemacht. Zusammenfassung:

* JSON-Dokumente im Anforderungstext umfassen eine ID, Text und einen Sprachcode.
* POST-Anforderungen werden an einen oder mehrere Endpunkte gesendet. Dabei werden ein personalisierter [Zugriffsschlüssel und ein Endpunkt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) verwendet, der für Ihr Abonnement gültig ist.
* Die Antwortausgabe, die aus verknüpften Entitäten besteht (z.B. Zuverlässigkeitsbewertungen, Offsets und Weblinks für jede Dokument-ID), kann in allen Anwendungen verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über die Textanalyse](../overview.md)
* [Verwenden der Textanalyse-Clientbibliothek](../quickstarts/client-libraries-rest-api.md)
* [Neuigkeiten](../whats-new.md)
