---
title: Integrierte Datenextraktion, natürliche Sprache, Bildverarbeitung – Azure Search
description: Durch kognitive Qualifikationen für Datenextraktion, natürliche Sprache und Bildverarbeitung erhält der Rohdateninhalt in einer Azure Search-Pipeline Semantik und Struktur.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7925f3aef4123fddd3a96c6e62971b881ae4cbc3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65021871"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Vordefinierte Skills für die Inhaltsanreicherung (Azure Search)

In diesem Artikel erfahren Sie mehr über die kognitiven Skills, die mit Azure Search bereitgestellt werden. Ein *kognitiver Skill* ist ein Vorgang, der Inhalte in irgendeiner Weise transformiert. Oft handelt es sich um eine Komponente, die Daten extrahiert oder die Struktur ableitet und Eingabedaten somit verständlicher macht. Die Ausgabe erfolgt fast immer textbasiert. Ein *Skillset* ist eine Sammlung von Skills, die die Anreicherungspipeline definieren. 

> [!NOTE]
> Wenn Sie den Umfang erweitern, indem Sie die Verarbeitungsfrequenz erhöhen oder weitere Dokumente oder KI-Algorithmen hinzufügen, müssen Sie [eine kostenpflichtige Cognitive Services-Ressource anfügen](cognitive-search-attach-cognitive-services.md). Gebühren fallen beim Aufrufen von APIs in Cognitive Services sowie für die Bildextraktion im Rahmen der Dokumentaufschlüsselungsphase in Azure Search an. Für die Textextraktion aus Dokumenten fallen keine Gebühren an.
>
> Die Ausführung integrierter Qualifikationen wird nach dem bestehenden [nutzungsbasierten Preis für Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) berechnet. Die Preise für die Bildextraktion werden auf der [Preisseite von Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) beschrieben.


## <a name="predefined-skills"></a>Vordefinierte Skills

Mehrere Skills sind flexibel in dem, was sie verwenden oder generieren. Im Allgemeinen basieren die meisten Skills auf vortrainierten Modellen, d.h. Sie können das Modell nicht mit Ihren eigenen Trainingsdaten trainieren. In der folgenden Tabelle werden die von Microsoft bereitgestellten Skills aufgelistet und beschrieben. 

| Skill | BESCHREIBUNG |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Dieser Skill verwendet ein vortrainiertes Modell, um wichtige Phrasen zu erkennen, die auf der Platzierung von Begriffen, sprachlichen Regeln, der Nähe zu anderen Begriffen und der Ungewöhnlichkeit des Begriffs innerhalb der Quelldaten basieren. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Dieser Skill verwendet ein vortrainiertes Modell, um die verwendete Sprache zu erkennen (eine Sprachen-ID pro Dokument). Wenn mehrere Sprachen in den gleichen Textsegmenten verwendet werden, wird die LCID der vorwiegend verwendeten Sprache ausgegeben.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Konsolidiert Text aus einer Sammlung von Feldern in einem einzigen Feld.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Dieser Skill verwendet ein vortrainiertes Modell, um Entitäten für eine feste Gruppe von Kategorien zu erstellen: Personen, Ort, Organisation, E-Mails, URLs, datetime-Felder. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Dieser Skill verwendet ein vortrainiertes Modell, um eine positive oder negative Stimmung anhand von Datensätzen zu bewerten. Der Wert liegt zwischen 0 und 1. Neutrale Bewertungen treten sowohl im Fall „0“ auf, wenn die Stimmung nicht erkannt werden kann, als auch für Text, der als neutral angesehen wird.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Teilt Text in Seiten auf, damit Sie Inhalt inkrementell anreichern oder erweitern können. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Dieser Skill verwendet einen Bilderkennungsalgorithmus, um den Inhalt eines Bildes zu identifizieren und eine Textbeschreibung zu erzeugen. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optische Zeichenerkennung |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Ordnet die Ausgabe einem komplexen Typ zu (ein mehrteiliger Datentyp, der für einen vollständigen Namen, eine mehrzeilige Adresse oder eine Kombination aus Nachname und persönlicher ID verwendet werden kann). |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Ermöglicht die Erweiterbarkeit der kognitiven Suchpipelines, indem ein HTTP-Aufruf in eine benutzerdefinierte Web-API erfolgt. |


Eine Anleitung zum Erstellen eines [benutzerdefinierten Skills](cognitive-search-custom-skill-web-api.md) finden Sie unter [Definieren einer benutzerdefinierten Schnittstelle](cognitive-search-custom-skill-interface.md) und [Beispiel: Erstellen eines benutzerdefinierten Skills](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Weitere Informationen

+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Schnittstellendefinition für benutzerdefinierte Skills](cognitive-search-custom-skill-interface.md)
+ [Tutorial: Angereicherte Indizierung mit kognitiver Suche](cognitive-search-tutorial-blob.md)
