---
title: Aufrufen der Textanalyse-API
titleSuffix: Azure Cognitive Services
description: In diesem Artikel wird erläutert, wie Sie die Textanalyse-REST-API von Azure Cognitive Services und Postman aufrufen können.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: bf53ce5ed3f9505572538533263f0d17c5dcbf45
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562564"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Aufrufen der Textanalyse-REST-API

In diesem Artikel werden mithilfe der Textanalyse-REST-API und [Postman](https://www.postman.com/downloads/) wichtige Konzepte veranschaulicht. Die API bietet mehrere synchrone und asynchrone Endpunkte für die Verwendung der Dienstfeatures. 

## <a name="create-a-text-analytics-resource"></a>Erstellen einer Textanalyseressource

> [!NOTE]
> * Sie benötigen eine Ressource der Textanalyse mit einem [Tarif](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) des Typs „Standard (S)“, wenn Sie die Endpunkte `/analyze` oder `/health` verwenden möchten. Der `/analyze`-Endpunkt ist in Ihrem [Tarif](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) enthalten.

Bevor Sie die Textanalyse-API verwenden, müssen Sie eine Azure-Ressource mit einem Schlüssel und Endpunkt für Ihre Anwendungen erstellen. 

1.  Melden Sie sich zunächst im [Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) an, und erstellen Sie eine neue Ressource der Textanalyse, sofern Sie noch keine haben. Wählen Sie einen [Tarif](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) aus.

2.  Wählen Sie die Region aus, die Sie für Ihren Endpunkt verwenden möchten.  Beachten Sie, dass die Endpunkte `/analyze` und `/health` nur in den folgenden Regionen verfügbar sind: USA, Westen 2; USA, Osten 2; USA, Mitte; Europa, Norden; Europa, Westen.

3.  Erstellen Sie die Ressource der Textanalyse, und wechseln Sie links auf der Seite zum Blatt mit den Schlüsseln und Endpunkten. Kopieren Sie den Schlüssel, mit dem Sie später die APIs aufrufen werden. Sie werden ihn später als Wert für den Header `Ocp-Apim-Subscription-Key` hinzufügen.

## <a name="using-the-api-synchronously"></a>Synchrones Verwenden der API

Sie können die Textanalyse-API synchron aufrufen (Szenarios mit geringer Latenz). Sie müssen jede API (Feature) einzeln aufrufen, wenn Sie die API synchron verwenden. Wenn Sie mehrere Features aufrufen müssen, sehen Sie sich den Abschnitt unten an, wie Sie die Textanalyse-API asynchron aufrufen. 

## <a name="using-the-api-asynchronously"></a>Asynchrones Verwenden der API

Ab Version „v3.1-preview.3“ stellt die Textanalyse-API zwei asynchrone Endpunkte bereit: 

* Mit dem Endpunkt `/analyze` für die Textanalyse können Sie dieselben Textdokumente mit mehreren Textanalysefeatures in einem einzigen API-Aufruf analysieren. Bisher mussten Sie für jeden Vorgang separate API-Aufrufe durchführen, um mehrere Features verwenden zu können. Denken Sie an diese Funktion, wenn Sie große Mengen von Dokumenten mit mehr als einem Textanalyse-Feature analysieren müssen.

* Mit dem Endpunkt `/health` für die Textanalyse für das Gesundheitssystem können relevante medizinische Informationen aus klinischen Dokumenten extrahiert und gekennzeichnet werden.  

In der folgenden Tabelle wird gezeigt, welche Features asynchron verwendet werden können. Beachten Sie, dass nur einige wenige Features über den Endpunkt `/analyze` aufgerufen werden können. 

| Funktion | Synchron | Asynchron |
|--|--|--|
| Spracherkennung | ✔ |  |
| Stimmungsanalyse | ✔ |  |
| Opinion Mining | ✔ |  |
| Schlüsselwortextraktion | ✔ | ✔* |
| Erkennung benannter Entitäten (einschließlich personenbezogener Informationen und geschützter Gesundheitsdaten) | ✔ | ✔* |
| Textanalyse für das Gesundheitssystem (Container) | ✔ |  |
| Textanalyse für das Gesundheitssystem (API) |  | ✔  |

`*`: wird asynchron über den `/analyze`-Endpunkt aufgerufen


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]


<a name="json-schema"></a>

## <a name="api-request-formats"></a>API-Anforderungsformate

Sie können sowohl synchrone als auch asynchrone Aufrufe an die Textanalyse-API senden.

#### <a name="synchronous"></a>[Synchron](#tab/synchronous)

### <a name="synchronous-requests"></a>Synchrone Anforderungen

Das Format für API-Anforderungen ist für alle synchronen Vorgänge identisch. Dokumente werden als unformatierter, unstrukturierter Text in einem JSON-Objekt übermittelt. XML wird nicht unterstützt. Das JSON-Schema besteht aus den folgenden Elementen.

| Element | Gültige Werte | Erforderlich? | Verwendung |
|---------|--------------|-----------|-------|
|`id` |Der Datentyp ist „Zeichenfolge“ (string), aber in der Praxis sind Dokument-IDs eher ganze Zahlen (integer). | Erforderlich | Das System verwendet die IDs, die Sie angeben, um die Ausgabe zu strukturieren. Sprachcodes, Schlüsselbegriffe und Stimmungspunktzahlen werden für jede ID in der Anforderung generiert.|
|`text` | Unstrukturierter, unformatierter Text mit bis zu 5.120 Zeichen. | Erforderlich | Für die Erkennung der Sprache kann der Text in einer beliebigen Sprache ausgedrückt werden. Für die Standpunktanalyse, Schlüsselbegriffserkennung und Entitätsidentifikation muss der Text in einer [unterstützten Sprache](../language-support.md) sein. |
|`language` | Aus 2 Zeichen bestehender [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes)-Code für eine [unterstützte Sprache](../language-support.md) | Varies | Erforderlich für die Standpunktanalyse, Schlüsselbegriffserkennung und Entitätsverknüpfung, optional für die Sprachenerkennung. Es gibt keinen Fehler, wenn Sie sie ausschließen, aber die Analyse wird ohne geschwächt. Der Sprachcode sollte dem von Ihnen bereitgestellten `text` entsprechen. |

Der folgende Code ist ein Beispiel einer API-Anforderung für die synchronen Endpunkte der Textanalyse. 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="asynchronous"></a>[Asynchron](#tab/asynchronous)

### <a name="asynchronous-requests-to-the-analyze-endpoint"></a>Asynchrone Anforderungen an den `/analyze`-Endpunkt

> [!NOTE]
> Das neueste Vorabrelease der Textanalyse-Clientbibliothek ermöglicht es Ihnen, asynchrone Analyze-Vorgänge mithilfe eines Clientobjekts aufzurufen. Beispiele dazu finden Sie auf GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

Mit dem Endpunkt `/analyze` können Sie auswählen, welche der unterstützten Features der Textanalyse Sie in einem einzigen API-Aufruf verwenden möchten. Dieser Endpunkt unterstützt derzeit Folgendes:

* oder Entitätserkennung 
* Erkennung benannter Entitäten (einschließlich personenbezogener Informationen und geschützter Gesundheitsdaten)

| Element | Gültige Werte | Erforderlich? | Verwendung |
|---------|--------------|-----------|-------|
|`displayName` | String | Optional | Wird als Anzeigename für den eindeutigen Bezeichner des Auftrags verwendet|
|`analysisInput` | Schließt das Feld `documents` unten ein | Erforderlich | Enthält die Informationen zu den Dokumenten, die Sie senden möchten |
|`documents` | Enthält die Felder `id` und `text` weiter unten | Erforderlich | Enthält Informationen zu jedem gesendeten Dokument sowie den unformatierten Text des Dokuments |
|`id` | String | Erforderlich | Mithilfe der von Ihnen bereitgestellten IDs wird die Ausgabe strukturiert. |
|`text` | Unstrukturierter, unformatierter Text mit bis zu 125.000 Zeichen | Erforderlich | Muss in englischer Sprache vorliegen. Dies ist die einzige derzeit unterstützte Sprache. |
|`tasks` | Umfasst die folgenden Textanalyse-Features: `entityRecognitionTasks`, `keyPhraseExtractionTasks` oder `entityRecognitionPiiTasks` | Erforderlich | Mindestens eines der Textanalyse-Features, das Sie verwenden möchten. Beachten Sie, dass `entityRecognitionPiiTasks` über einen optionalen `domain`-Parameter verfügt, der auf `pii` oder `phi` festgelegt werden kann. Wenn kein Wert angegeben wird, verwendet das System den Standardwert `pii`. |
|`parameters` | Enthält die Felder `model-version` und `stringIndexType` weiter unten | Erforderlich | Dieses Feld ist in den oben aufgeführten Featuretasks enthalten, die Sie auswählen. Sie enthalten Informationen über die gewünschte Modellversion und den Indextyp. |
|`model-version` | String | Erforderlich | Geben Sie an, welche Version des Modells aufgerufen werden soll.  |
|`stringIndexType` | String | Erforderlich | Geben Sie den Textdecoder an, der Ihrer Programmierumgebung entspricht.  Die unterstützten Typen sind `textElement_v8` (Standard), `unicodeCodePoint`, `utf16CodeUnit`. Weitere Informationen finden Sie im Artikel über [Textoffsets](../concepts/text-offsets.md#offsets-in-api-version-31-preview).  |
|`domain` | String | Optional | Gilt nur als Parameter für den `entityRecognitionPiiTasks`-Task und kann auf `pii` oder `phi` festgelegt werden. Wenn nichts angegeben wird, wird standardmäßig `pii` verwendet.  |

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
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}

```

### <a name="asynchronous-requests-to-the-health-endpoint"></a>Asynchrone Anforderungen an den `/health`-Endpunkt

Das Format für API-Anforderungen an die von der Textanalyse für das Gesundheitssystem gehosteten API ist identisch mit dem für den Container. Dokumente werden als unformatierter, unstrukturierter Text in einem JSON-Objekt übermittelt. XML wird nicht unterstützt. Das JSON-Schema besteht aus den folgenden Elementen.  Füllen Sie das [Anforderungsformular für Cognitive Services](https://aka.ms/csgate) aus, und reichen Sie es ein, um Zugriff auf die öffentliche Vorschauversion der Textanalyse für das Gesundheitssystem anzufordern. Für die Verwendung von Text Analytics for Health fallen keine Gebühren an. 

| Element | Gültige Werte | Erforderlich? | Verwendung |
|---------|--------------|-----------|-------|
|`id` |Der Datentyp ist „Zeichenfolge“ (string), aber in der Praxis sind Dokument-IDs eher ganze Zahlen (integer). | Erforderlich | Das System verwendet die IDs, die Sie angeben, um die Ausgabe zu strukturieren. |
|`text` | Unstrukturierter, unformatierter Text mit bis zu 5.120 Zeichen. | Erforderlich | Beachten Sie, dass derzeit nur Englisch unterstützt wird. |
|`language` | Aus 2 Zeichen bestehender [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes)-Code für eine [unterstützte Sprache](../language-support.md) | Erforderlich | Derzeit wird nur `en` unterstützt. |

Der folgende Code ist ein Beispiel einer API-Anforderung für die Endpunkte der Textanalyse für das Gesundheitssystem. 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> Informationen zu den Raten- und Größenbeschränkungen für das Senden von Daten an die Textanalyse-API finden Sie im Artikel [Daten- und Ratenbeschränkungen](../concepts/data-limits.md).


## <a name="set-up-a-request"></a>Einrichten einer Anforderung 

Fügen Sie in Postman (oder einem anderen Web-API-Testtool) den Endpunkt für das gewünschte Feature hinzu. Ermitteln Sie mithilfe der folgende Tabelle das richtige Endpunktformat, und ersetzen Sie `<your-text-analytics-resource>` durch Ihren Ressourcenendpunkt. Zum Beispiel:

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Synchron](#tab/synchronous)

### <a name="endpoints-for-sending-synchronous-requests"></a>Endpunkte für das Senden synchroner Anforderungen

| Funktion | Anforderungstyp | Ressourcenendpunkte |
|--|--|--|
| Spracherkennung | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| Stimmungsanalyse | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Opinion Mining | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| Schlüsselwortextraktion | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Erkennung benannter Entitäten – allgemein | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Erkennung benannter Entitäten – personenbezogene Informationen | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Erkennung benannter Entitäten – geschützte Gesundheitsdaten | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="asynchronous"></a>[Asynchron](#tab/asynchronous)

### <a name="endpoints-for-sending-asynchronous-requests-to-the-analyze-endpoint"></a>Endpunkte für das Senden asynchroner Anforderungen an den `/analyze`-Endpunkt

| Funktion | Anforderungstyp | Ressourcenendpunkte |
|--|--|--|
| Analyseauftrag übermitteln | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze` |
| Analysestatus und -ergebnisse abrufen | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>` |

### <a name="endpoints-for-sending-asynchronous-requests-to-the-health-endpoint"></a>Endpunkte für das Senden asynchroner Anforderungen an den `/health`-Endpunkt

| Funktion | Anforderungstyp | Ressourcenendpunkte |
|--|--|--|
| Auftrag der Textanalyse für das Gesundheitssystem übermitteln  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs` |
| Auftragsstatus und -ergebnisse abrufen | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |
| Auftrag abbrechen | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |

--- 

Wenn Sie Ihren Endpunkt haben, gehen Sie wie folgt in Postman (oder einem anderen Web-API-Testtool) vor:

1. Wählen Sie den Anforderungstyp für das gewünschte Feature aus.
2. Fügen Sie den Endpunkt des gewünschten Vorgangs aus der obigen Tabelle ein.
3. Legen Sie die drei Anforderungsheader fest:

   + `Ocp-Apim-Subscription-Key`: Ihr aus dem Azure-Portal abgerufener Zugriffsschlüssel
   + `Content-Type`: application/json
   + `Accept`: application/json

    Wenn Sie Postman verwenden, sollte Ihre Anforderung bei einem `/keyPhrases`-Endpunkt in etwa wie im folgenden Screenshot aussehen.
    
    ![Screenshot der Anforderung mit Endpunkt und Headern](../media/postman-request-keyphrase-1.png)
    
4. Wählen Sie **raw** (Unformatiert) als Format für **Body** (Textkörper) aus.
    
    ![Screenshot der Anforderung mit Textkörpereinstellungen (body)](../media/postman-request-body-raw.png)

5. Fügen Sie einige JSON-Dokumente in einem gültigen Format ein. Verwenden Sie die Beispiele im Abschnitt **API-Anforderungsformat** weiter oben. Weitere Informationen und Beispiele finden Sie in den folgenden Themen:

      + [Sprachenerkennung](text-analytics-how-to-language-detection.md)
      + [Schlüsselbegriffserkennung](text-analytics-how-to-keyword-extraction.md)
      + [Standpunktanalyse](text-analytics-how-to-sentiment-analysis.md)
      + [Entitätserkennung](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>Senden der Anforderung

Senden Sie die API-Anforderung. Wenn Sie einen synchronen Endpunkt aufgerufen haben, wird die Antwort sofort als einzelnes JSON-Dokument mit einem Element für jede Dokument-ID angezeigt, die in der Anforderung angegeben ist.

Wenn Sie die asynchronen Endpunkte `/analyze` oder `/health` aufgerufen haben, überprüfen Sie, ob Sie einen 202-Antwortcode erhalten haben. Sie müssen die Antwort abrufen, um die Ergebnisse anzuzeigen:

1. Suchen Sie `Operation-Location` im Header der API-Antwort, der den Auftrag identifiziert, den Sie an die API gesendet haben. 
2. Erstellen Sie eine GET-Anforderung für den verwendeten Endpunkt. In der [obigen Tabelle](#set-up-a-request) finden Sie das Endpunktformat. Außerdem sollten Sie die [Referenzdokumentation zur API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus) lesen. Zum Beispiel:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>`

3. Fügen Sie der Anforderung den Wert von `Operation-Location` hinzu.

4. Die Antwort ist ein einzelnes JSON-Dokument, das für jede in der Anforderung angegebene Dokument-ID ein Element enthält.

Beachten Sie, dass für beide asynchronen `/analyze`- oder `/health`-Vorgänge die Ergebnisse der GET-Anforderung in Schritt 2 oben für 24 Stunden ab dem Zeitpunkt der Erstellung des Auftrags zur Verfügung stehen.  Diese Zeit wird durch den `expirationDateTime`-Wert in der GET-Antwort angegeben.  Nach diesem Zeitraum werden die Ergebnisse endgültig gelöscht und stehen nicht mehr zum Abruf zur Verfügung.    

## <a name="example-api-responses"></a>Beispiel-API-Antworten
 
# <a name="synchronous"></a>[Synchron](#tab/synchronous)

### <a name="example-responses-for-synchronous-operation"></a>Beispielantworten für synchrone Vorgänge

Die Antworten der synchronen Endpunkte variieren je nach verwendetem Endpunkt. In den folgenden Artikeln finden Sie einige Antwortbeispiele.

+ [Sprachenerkennung](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Schlüsselbegriffserkennung](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Standpunktanalyse](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Entitätserkennung](text-analytics-how-to-entity-linking.md#view-results)

# <a name="asynchronous"></a>[Asynchron](#tab/asynchronous)

### <a name="example-responses-for-asynchronous-operations"></a>Beispielantworten für asynchrone Vorgänge

Bei erfolgreicher Ausführung gibt die an den Endpunkt `/analyze` gesendete GET-Anforderung ein Objekt zurück, das die zugewiesenen Tasks enthält. Beispiel: `keyPhraseExtractionTasks`. Diese Tasks enthalten das Antwortobjekt aus dem entsprechenden Textanalyse-Feature. Weitere Informationen finden Sie in den folgenden Artikeln.

+ [Schlüsselbegriffserkennung](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Entitätserkennung](text-analytics-how-to-entity-linking.md#view-results)
+ [Textanalyse für Gesundheit](text-analytics-for-health.md#hosted-asynchronous-web-api-response)

--- 

## <a name="see-also"></a>Weitere Informationen

* [Übersicht über die Textanalyse](../overview.md)
* [Häufig gestellte Fragen (FAQ)](../text-analytics-resource-faq.md)</br>
* [Textanalysen (Produktseite)](//go.microsoft.com/fwlink/?LinkID=759712)
* [Verwenden der Textanalyse-Clientbibliothek](../quickstarts/client-libraries-rest-api.md)
* [Neuigkeiten](../whats-new.md)
