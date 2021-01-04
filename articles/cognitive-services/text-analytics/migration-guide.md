---
title: Handbuch für die Migration von Version 2 der Textanalyse-API
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie Ihre Anwendungen zur Verwendung von Version 3 der Textanalyse-API migrieren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 11/19/2020
ms.author: aahi
ms.openlocfilehash: 56464268b95181af0379a1895d392cc3cc26624c
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980897"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>Migration zu Version 3 der Textanalyse-API

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

Wenn Sie die Version 2.1 der Textanalyse-API verwenden, unterstützt dieser Artikel Sie dabei, ein Upgrade zur Verwendung von Version 3.x für Ihre Anwendung durchzuführen. Version 3.0 ist allgemein verfügbar und führt neue Features wie die erweiterte [Erkennung benannter Entitäten](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-features-and-versions) und die [Versionsverwaltung für Modelle](concepts/model-versioning.md). Außerdem ist eine Vorschauversion von Version 3.1 („v3.1-preview.x“) verfügbar, in der Features wie [Opinion Mining](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) hinzugefügt wurden. Für die Modelle in Version 2 werden keine weiteren Updates veröffentlicht. 

## <a name="sentiment-analysis"></a>[Standpunktanalyse](#tab/sentiment-analysis)

### <a name="feature-changes"></a>Funktionsänderungen 

Die Standpunktanalyse in Version 2.1 gibt Stimmungsbewertungen zwischen 0 (null) und 1 für alle an die API übermittelten Dokumente zurück. Bewertungen, die näher an 1 liegen, geben eine positivere Stimmung an. In Version 3 werden hingegen Stimmungsbezeichnungen (z. B. „positiv“ oder „negativ“) sowohl für die Sätze als auch für das gesamte Dokument zurückgegeben. Außerdem werden dazugehörige Zuverlässigkeitsbewertungen angegeben. 

### <a name="steps-to-migrate"></a>Migrationsschritte

#### <a name="rest-api"></a>REST-API

Wenn Ihre Anwendung die REST-API nutzt, ändern Sie den Anforderungsendpunkt in den Endpunkt der Version 3 für die Stimmungsanalyse. Beispiel: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`. Sie müssen auch die Anwendung aktualisieren, um Stimmungsbezeichnungen zu verwenden, die in der [API-Antwort](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results) zurückgegeben werden. 

Beispiele für die JSON-Antwort finden Sie in der Referenzdokumentation.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment) 
* [Version 3.1-preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

#### <a name="client-libraries"></a>Clientbibliotheken

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="ner-and-entity-linking"></a>[Erkennung benannter Entitäten und Entitätsverknüpfung](#tab/named-entity-recognition)

### <a name="feature-changes"></a>Funktionsänderungen

> [!NOTE] 
> [Entitätskategorien der Version 3](named-entity-types.md) werden derzeit nur für englischen und spanischen Text zurückgegeben. Die API gibt Ergebnisse der Version 2.1 für Anforderungen in anderen Sprachen zurück, sofern diese in Version 2.1 unterstützt wurden.

In Version 2.1 verwendet die Textanalyse-API einen Endpunkt für die Erkennung benannter Entitäten und die Entitätsverknüpfung. In Version 3 wird die erweiterte Erkennung benannter Entitäten bereitgestellt. Außerdem werden separate Endpunkte für Anforderungen der Erkennung benannter Entitäten und der Entitätsverknüpfung verwendet. Ab v3.1-preview.1 kann die Erkennung benannter Entitäten auch persönliche Informationen (`pii`) und Integritätsinformationen (`phi`) ermitteln. 

### <a name="steps-to-migrate"></a>Migrationsschritte

#### <a name="rest-api"></a>REST-API

Wenn Ihre Anwendung die REST-API nutzt, ändern Sie den Anforderungsendpunkt in die Endpunkte der Version 3 für die Erkennung benannter Entitäten und/oder die Entitätsverknüpfung.

Entitätsverknüpfung
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

Sie müssen auch Ihre Anwendung aktualisieren, um die [Entitätskategorien](named-entity-types.md) verwenden zu können, die in der [API-Antwort](how-tos/text-analytics-how-to-entity-linking.md#view-results) zurückgegeben werden.

Beispiele für die JSON-Antwort finden Sie in der Referenzdokumentation.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral) 
* [Version 3.1-preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/EntitiesRecognitionGeneral)

#### <a name="client-libraries"></a>Clientbibliotheken

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="language-detection"></a>[Sprachenerkennung](#tab/language-detection)

### <a name="feature-changes"></a>Funktionsänderungen 

Abgesehen von der Endpunktversion wurden in Version 3 keine Änderungen am Sprachenerkennungsfeature vorgenommen. Dennoch enthält die JSON-Antwort `ConfidenceScore` anstelle von `score`. Außerdem wird in Version 3 nur eine einzelne Sprache in der Ausgabe zurückgegeben. 

### <a name="steps-to-migrate"></a>Migrationsschritte

#### <a name="rest-api"></a>REST-API

Wenn Ihre Anwendung die REST-API nutzt, ändern Sie den Anforderungsendpunkt in den Endpunkt der Version 3 für die Sprachenerkennung. Beispiel: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`. Sie müssen auch die Anwendung aktualisieren, um `ConfidenceScore` anstelle von `score` in der [API-Antwort](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results) zu verwenden. 

Beispiele für die JSON-Antwort finden Sie in der Referenzdokumentation.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

#### <a name="client-libraries"></a>Clientbibliotheken

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="key-phrase-extraction"></a>[Schlüsselbegriffserkennung](#tab/key-phrase-extraction)

### <a name="feature-changes"></a>Funktionsänderungen 

Abgesehen von der Endpunktversion wurden in Version 3 keine Änderungen am Feature für die Schlüsselbegriffserkennung vorgenommen.

### <a name="steps-to-migrate"></a>Migrationsschritte

#### <a name="rest-api"></a>REST-API

Wenn Ihre Anwendung die REST-API nutzt, ändern Sie den Anforderungsendpunkt in den Endpunkt der Version 3 für die Schlüsselbegriffserkennung. Beispiel: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

Beispiele für die JSON-Antwort finden Sie in der Referenzdokumentation.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/KeyPhrases)

#### <a name="client-libraries"></a>Clientbibliotheken

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---

## <a name="see-also"></a>Weitere Informationen

* [Worum handelt es sich bei der Textanalyse-API?](overview.md)
* [Sprachunterstützung](language-support.md)
* [Versionsverwaltung für Modelle](concepts/model-versioning.md)
