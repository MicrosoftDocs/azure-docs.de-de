---
title: Dokumentationsressourcen zur kognitiven Suche – Azure Search
description: Eine kommentierte Liste von Artikeln, Tutorials, Beispielen und Blogbeiträgen zu Workloads der kognitiven Suche in Azure Search.
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 225ac86efba8a32b085a5fb7fc41bed48c499eab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65022179"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Dokumentationsressourcen für Workloads der kognitiven Suche

Die kognitive Suche – jetzt allgemein verfügbar – ist eine neue Anreicherungsebene bei der Azure Search-Indizierung, die latente Informationen in Nicht-Textquellen und undifferenziertem Text findet und sie in durchsuchbare Volltextinhalte in Azure Search umwandelt.

Die folgenden Artikel sind die vollständige Dokumentation zur kognitiven Suche.

## <a name="getting-started"></a>Erste Schritte
+ [Was ist die kognitive Suche?](cognitive-search-concept-intro.md)
+ [Schnellstart: Testen der kognitiven Suche im Portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: Informationen zum Aufrufen von APIs der kognitiven Suche](cognitive-search-tutorial-blob.md)
+ [Beispiel: Erstellen eines benutzerdefinierten Skills](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Leitfäden zur Vorgehensweise
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Verweisen auf Anmerkungen in einem Skillset](cognitive-search-concept-annotations-syntax.md)
+ [Zuordnen von Feldern zu einem Index](cognitive-search-output-field-mapping.md)
+ [Verarbeiten und Extrahieren von Informationen aus Bildern](cognitive-search-concept-image-scenarios.md)
+ [Neuerstellen eines Azure Search-Indexes](search-howto-reindex.md)
+ [Definieren einer benutzerdefinierten Skillsschnittstelle](cognitive-search-custom-skill-interface.md)
+ [Tipps zur Problembehandlung](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Verweis

+ [Vordefinierte Skills](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ [REST-API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Erstellen einer Qualifikationsgruppe (API-Version 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Erstellen eines Indexers (API-Version 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Weitere Informationen

+ [Azure Search-REST-API](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexer in Azure Search](search-indexer-overview.md)
+ [Was ist Azure Search?](search-what-is-azure-search.md)
