---
title: Neuigkeiten bei der Textanalyse-API
titleSuffix: Text Analytics - Azure Cognitive Services
description: Dieser Artikel bietet Informationen zu neuen Releases und Features für Azure Cognitive Services-Textanalysen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: c304c2aad3ba40d7b3a044f0e3222e31bb013648
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503980"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Neuigkeiten bei der Textanalyse-API

Die Textanalyse-API wird fortlaufend aktualisiert. Damit Sie bezüglich der aktuellen Entwicklungen auf dem neuesten Stand bleiben, bietet dieser Artikel Informationen zu neuen Releases und Funktionen.

## <a name="december-2020"></a>Dezember 2020

* [Aktualisierte Preise](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) für die Textanalyse-API

## <a name="november-2020"></a>November 2020

* Ein [neuer Endpunkt](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Analyze) mit der Textanalyse-API-Version v3.1-preview.3 für die neue asynchrone [Analyse-API](how-tos/text-analytics-how-to-call-api.md?tabs=analyze), die Batchverarbeitung bei NER (Named Entity Recognition, Erkennung benannter Entitäten), personenbezogenen Informationen und Schlüsselwortextraktionsvorgängen unterstützt.
* Ein [neuer Endpunkt](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) mit der Textanalyse-API-Version v3.1-preview.3 für die neue gehostete asynchrone API für [Textanalyse im Gesundheitsbereich](how-tos/text-analytics-for-health.md), mit Unterstützung für Batchverarbeitung.
* Beide oben aufgeführten neuen Features sind nur in den folgenden Regionen verfügbar: `West US 2`, `East US 2`, `Central US`, `North Europe` und `West Europe`.
* Portugiesisch (Brasilien) `pt-BR` wird jetzt in [Standpunktanalyse](how-tos/text-analytics-how-to-sentiment-analysis.md) v3.x unterstützt, und zwar ab Modellversion `2020-04-01`. Es wird der bereits vorhandenen `pt-PT`-Unterstützung für Portugiesisch hinzugefügt.
* Aktualisierte Clientbibliotheken, die asynchrone Analyse und Textanalyse für Vorgänge im Gesundheitsbereich enthalten. Beispiele dazu finden Sie auf GitHub:

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)


> [!div class="nextstepaction"]
> [Weitere Informationen zur Textanalyse-API-Version v3.1-Preview.3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

## <a name="october-2020"></a>Oktober 2020

* Hindi-Unterstützung für Version 3.x der Standpunktanalyse ab Modellversion `2020-04-01`. 
* Modellversion `2020-09-01` für den Sprachenendpunkt von Version 3 mit Verbesserungen bei der Spracherkennung und Genauigkeit.
* Verfügbarkeit von v3 in Zentralindien und VAE, Norden.

## <a name="september-2020"></a>September 2020

### <a name="general-api-updates"></a>Allgemeine API-Updates

* Veröffentlichung einer neuen URL für die öffentliche Vorschauversion (3.1) der Textanalyse, um Updates für die folgenden V3-Endpunkte der Erkennung benannter Entitäten zu unterstützen: 
    * Der Endpunkt `/pii` enthält jetzt die neue `redactedText`-Eigenschaft in der JSON-Antwort, in der in Entitäten, in denen personenbezogene Informationen im Eingabetext erkannt wurden, sämtliche Zeichen durch ein `*` ersetzt werden.
    * Der Endpunkt `/linking` enthält jetzt die `bingID`-Eigenschaft in der JSON-Antwort für verknüpfte Entitäten.
* Die folgenden API-Endpunkte der Vorschauversion der Textanalyse wurden am 4. September 2020 eingestellt:
    * v2.1-preview
    * v3.0-preview
    * v3.0-preview.1
    
> [!div class="nextstepaction"]
> [Weitere Informationen zur Textanalyse-API-Version 3.1-preview.2](quickstarts/client-libraries-rest-api.md)

### <a name="text-analytics-for-health-container-updates"></a>Updates beim Container für die Textanalyse im Gesundheitsbereich

Die folgenden Updates gelten speziell nur für das September-Release des Containers für die Textanalyse im Gesundheitsbereich.
* Ein neues Containerimage mit dem Tag `1.1.013530001-amd64-preview` und der neuen Modellversion `2020-09-03` wurde im Repository „containerpreview“ veröffentlicht. 
* Diese Modellversion bietet Verbesserungen bei der Erkennung von Entitäten und Abkürzungen und bei der Latenz.

> [!div class="nextstepaction"]
> [Weitere Informationen zur Textanalyse im Gesundheitsbereich](how-tos/text-analytics-for-health.md)

## <a name="august-2020"></a>August 2020

### <a name="general-api-updates"></a>Allgemeine API-Updates

* Modellversion `2020-07-01` für die v3-`/keyphrases`, `/pii` -und `/languages`-Endpunkte, die Folgendes hinzufügt:
    * Zusätzliche behörden- und länderspezifische [Entitätskategorien](named-entity-types.md?tabs=personal) für die Erkennung benannter Entitäten.
    * Unterstützung für Norwegisch und Türkisch in Standpunktanalyse v3.
* Ein HTTP 400-Fehler wird jetzt für v3-API-Anforderungen zurückgegeben, die die veröffentlichten [Datenlimits](concepts/data-limits.md) überschreiten. 
* Endpunkte, die einen Offset zurückgeben, unterstützen jetzt den optionalen Parameter `stringIndexType`, der die zurückgegebenen Werte `offset` und `length` so anpasst, dass sie einem unterstützten [Zeichenfolgenindexschema](concepts/text-offsets.md) entsprechen.

### <a name="text-analytics-for-health-container-updates"></a>Updates beim Container für die Textanalyse im Gesundheitsbereich

Die folgenden Updates gelten spezifisch nur für das August-Release des Containers „Text Analytics for Health“.

* Neue Modellversion für Text Analytics for Health: `2020-07-24`
* Neue URL zum Senden von Text Analytics for Health-Anforderungen: `http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health` (Beachten Sie, dass ein Löschvorgang für den Browsercache erforderlich ist, um die in diesem neuen Containerimage enthaltene Demo-Web-App zu verwenden.)

In der JSON-Antwort wurden die folgenden Eigenschaften geändert:

* `type` wurde in `category` umbenannt. 
* `score` wurde in `confidenceScore` umbenannt.
* Entitäten im `category`-Feld der JSON-Ausgabe sind jetzt in Pascal-Schreibweise. Die folgenden Entitäten wurden umbenannt:
    * `EXAMINATION_RELATION` wurde umbenannt in `RelationalOperator`.
    * `EXAMINATION_UNIT` wurde umbenannt in `MeasurementUnit`.
    * `EXAMINATION_VALUE` wurde umbenannt in `MeasurementValue`.
    * `ROUTE_OR_MODE` wurde in `MedicationRoute` umbenannt.
    * Die relationale Entität `ROUTE_OR_MODE_OF_MEDICATION` wurde in `RouteOfMedication` umbenannt.

Die folgenden Entitäten wurden hinzugefügt:

* NER
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* Beziehungsextrahierung
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [Weitere Informationen zum Container für die Textanalyse im Gesundheitsbereich](how-tos/text-analytics-for-health.md)

## <a name="july-2020"></a>Juli 2020 

### <a name="text-analytics-for-health-container---public-gated-preview"></a>Geschlossene öffentliche Vorschau des Containers für die Textanalyse im Gesundheitsbereich

Der Container für die Textanalyse im Gesundheitsbereich befindet sich nun in der geschlossenen öffentlichen Vorschau. Mit ihm können Sie aus unstrukturiertem Text in englischer Sprache in klinischen Dokumenten Informationen wie die folgenden extrahieren: Patientenaufnahmeformulare, ärztliche Notizen, Forschungspapiere und Entlassungsübersichten. Derzeit wird Ihnen die Verwendung von Containern für die Textanalyse im Gesundheitsbereich nicht in Rechnung gestellt.

Der Container bietet folgende Features:

* Erkennung benannter Entitäten
* Beziehungsextrahierung
* Entitätsverknüpfung
* Negation

## <a name="may-2020"></a>Mai 2020

### <a name="text-analytics-api-v3-general-availability"></a>Textanalyse-API v3: Allgemeine Verfügbarkeit

Die Textanalyse-API v3 ist jetzt mit den folgenden Updates allgemein verfügbar:

* Modellversion `2020-04-01`
* Neue [Datengrenzwerte](concepts/data-limits.md) für jedes Feature
* Aktualisierte [Sprachunterstützung](language-support.md) für [Standpunktanalyse v3](how-tos/text-analytics-how-to-sentiment-analysis.md)
* Separater Endpunkt für Entitätsverknüpfung 
* Neue Entitätskategorie „Address“ bei der [Erkennung benannter Entitäten (NER) v3](how-tos/text-analytics-how-to-entity-linking.md).
* Neue Unterkategorien in NER v3:
   * Standort: Geografisch
   * Standort: Strukturell
   * Organisation: Börse
   * Organisation: Medizin
   * Organisation: Sport
   * Veranstaltung: Kultur
   * Veranstaltung: Natur
   * Veranstaltung: Sport

Der JSON-Antwort wurden die folgenden Eigenschaften hinzugefügt:
   * `SentenceText` in Standpunktanalyse
   * `Warnings` für jedes Dokument 

Die Namen der folgenden Eigenschaften in der JSON-Antwort wurden geändert (soweit zutreffend):

* `score` wurde in `confidenceScore` umbenannt.
    * `confidenceScore` verfügt über eine Genauigkeit von zwei Dezimalstellen. 
* `type` wurde in `category` umbenannt.
* `subtype` wurde in `subcategory` umbenannt.

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

> [!div class="nextstepaction"]
> [Weitere Informationen zur Textanalyse-API v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>Textanalyse-API v3.1: Public Preview
   * Neues Feature für die Standpunktanalyse: [Opinion Mining](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Neuer Filter für den persönlichen Bereich (`PII`) für geschützte Informationen zur Gesundheit (`PHI`).

> [!div class="nextstepaction"]
> [Weitere Informationen zur Textanalyse-API v3.1 (Vorschau)](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>Februar 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>SDK-Unterstützung für die öffentliche Vorschauversion der Textanalyse-API v3

Als Teil der [vereinheitlichten Azure SDK-Veröffentlichung](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290) ist das Textanalyse-API v3 SDK jetzt als öffentliche Vorschauversion für die folgenden Programmiersprachen verfügbar:
   * [C#](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-csharp&tabs=version-3)
   * [Python](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-python&tabs=version-3)
   * [JavaScript (Node.js)](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-javascript&tabs=version-3)
   * [Java](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-java&tabs=version-3)
   
> [!div class="nextstepaction"]
> [Weitere Informationen zum Textanalyse-API v3 SDK](./quickstarts/client-libraries-rest-api.md?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Named Entity Recognition v3 – öffentliche Vorschau

Weitere Entitätstypen stehen jetzt in der öffentlichen Vorschauversion des Diensts zur Erkennung benannter Entitäten (NER) v3 zur Verfügung, da wir die Erkennung allgemeiner und persönlicher Informationsentitäten, die in Text erkannt werden, ausweiten. Dieses Update führt die [Modellversion](concepts/model-versioning.md) `2020-02-01` ein, die Folgendes beinhaltet:

* Erkennung der folgenden allgemeinen Entitätstypen (nur in englischer Sprache):
    * PersonType
    * Produkt
    * Ereignis
    * Geopolitische Entität (GPE) als Untertyp unter Standort
    * Skill

* Erkennung der folgenden persönlichen Entitätstypen (nur in englischer Sprache):
    * Person
    * Organization
    * Alter als Untertyp unter Menge
    * Datum als Untertyp unter DateTime
    * Email 
    * Telefonnummer (nur USA)
    * URL
    * IP-Adresse

### <a name="october-2019"></a>Oktober 2019

#### <a name="named-entity-recognition-ner"></a>Erkennung benannter Entitäten (NER)

* Einen [neuen Endpunkt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii) für das Erkennen von Entitätstypen für personenbezogene Informationen (nur für die englische Sprache)

* Separate Endpunkte für die [Entitätserkennung](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral) und [Entitätsverknüpfung](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking).

* [Modellversion](concepts/model-versioning.md) `2019-10-01`, die Folgendes beinhaltet:
    * Erweiterte Erkennung und Kategorisierung von Entitäten, die im Text erkannt wurden. 
    * Erkennung der folgenden neuen Entitätstypen:
        * Telefonnummer
        * IP-Adresse

Die Entitätsverknüpfung unterstützt Englisch und Spanisch. Die NER-Sprachunterstützung ist je nach Entitätstyp unterschiedlich.

#### <a name="sentiment-analysis-v3-public-preview"></a>Öffentliche Vorschau der Standpunktanalyse v3

* Einen [neuen Endpunkt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/Sentiment) für die Stimmungsanalyse.
* [Modellversion](concepts/model-versioning.md) `2019-10-01`, die Folgendes beinhaltet:

    * Deutliche Verbesserungen bei Genauigkeit und Details der Textkategorisierung und -bewertung der API.
    * Automatische Bezeichnung für verschiedene Stimmungen im Text
    * Standpunktanalyse und Ausgabe auf Dokument- und Satzebene 

Unterstützt derzeit Englisch (`en`), Japanisch (`ja`), vereinfachtes Chinesisch (`zh-Hans`), traditionelles Chinesisch (`zh-Hant`), Französisch (`fr`), Italienisch (`it`), Spanisch (`es`), Niederländisch (`nl`), Portugiesisch (`pt`) und Deutsch (`de`) und ist in den folgenden Regionen erhältlich: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe` und `West US 2`. 

> [!div class="nextstepaction"]
> [Weitere Informationen zur Standpunktanalyse v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Nächste Schritte

* [Worum handelt es sich bei der Textanalyse-API?](overview.md)  
* [Beispielbenutzerszenarien](text-analytics-user-scenarios.md)
* [Standpunktanalyse](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Sprachenerkennung](how-tos/text-analytics-how-to-language-detection.md)
* [Entitätserkennung](how-tos/text-analytics-how-to-entity-linking.md)
* [Schlüsselbegriffserkennung](how-tos/text-analytics-how-to-keyword-extraction.md)
