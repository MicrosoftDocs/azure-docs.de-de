---
title: Worum handelt es sich bei der Textanalyse-API? - Funktionen -
titleSuffix: Azure Cognitive Services
description: Verwenden Sie die Textanalyse-API von Azure Cognitive Services für Stimmungsanalyse, Schlüsselbegriffserkennung, Sprachenerkennung und Entitätserkennung.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/03/2019
ms.author: aahi
ms.openlocfilehash: 7d52585b51af09c430130141c3680b5630f7b95e
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417278"
---
# <a name="what-is-text-analytics-api"></a>Worum handelt es sich bei der Textanalyse-API?

Die Textanalyse-API ist ein cloudbasierter Dienst für die erweiterte Verarbeitung natürlicher Sprache aus unformatiertem Text. Sie bietet vier Hauptfunktionen: Standpunktanalyse, Schlüsselbegriffserkennung, Sprachenerkennung und Entitätserkennung.

Die API ist ein Teil von [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), einer Sammlung von Algorithmen für maschinelles Lernen und künstliche Intelligenz (KI) in der Cloud, die Sie für Ihre Entwicklungsprojekte verwenden können.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

Der Begriff „Textanalyse“ hat verschiedene Bedeutungen, in Cognitive Services stellt die Textanalyse-API jedoch vier Analysearten bereit, die nachfolgend beschrieben werden.

## <a name="sentiment-analysis"></a>Standpunktanalyse
Ermitteln Sie mithilfe der [Standpunktanalyse](how-tos/text-analytics-how-to-sentiment-analysis.md), was die Kunden von Ihrer Marke oder von Ihrem Thema halten, indem Sie reinen Text auf Hinweise für positive oder negative Stimmung analysieren. Die API gibt für jedes Dokument eine Bewertung des Standpunkts zwischen 0 und 1 zurück. Hierbei entspricht 1 einer positiven Bewertung.<br /> Die Analysemodelle sind mithilfe großer Textmengen und Microsoft-Technologien für natürliche Sprache vortrainiert. Für [ausgewählte Sprachen](text-analytics-supported-languages.md) kann die API jeden bereitgestellten unformatierten Text analysieren und bewerten und der aufrufenden Anwendung direkt Ergebnisse zurückgeben. Sie können die [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c9)-API oder das [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) SDK verwenden.

## <a name="key-phrase-extraction"></a>Schlüsselwortextraktion
[Extrahieren Sie automatisch Schlüsselbegriffe](how-tos/text-analytics-how-to-keyword-extraction.md), um schnell die wichtigsten Punkte zu ermitteln. Wenn der eingegebene Text beispielsweise „Das Essen war köstlich, und es gab hervorragendes Personal“ lautet, gibt die API die Kernpunkte „Essen“ und „hervorragendes Personal“ zurück. Sie können die [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)-API oder das [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) SDK verwenden.

## <a name="language-detection"></a>Spracherkennung
Sie können für eine Vielzahl von Sprachen, Varianten und Dialekten sowie einige Regional- und Kultursprachen [die Sprache von Eingabetexten erkennen](how-tos/text-analytics-how-to-language-detection.md) und einen einzigen Sprachcode für jedes Dokument melden, das auf Anforderung gesendet wird. Der Sprachcode ist mit einem Wert kombiniert, der die Stärke der Bewertung angibt. Sie können die [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c7)-API oder das [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) SDK verwenden.

## <a name="named-entity-recognition"></a>Erkennung benannter Entitäten
[Identifizieren und kategorisieren Sie Entitäten](how-tos/text-analytics-how-to-entity-linking.md) in Ihrem Text als Personen, Orte, Organisationen, Datums-/Zeitangaben, Mengen, Prozentsätze, Währungen usw. Bekannte Entitäten werden ebenfalls erkannt und mit weiteren Informationen im Internet verknüpft. Sie können die [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634)-API verwenden.

## <a name="use-containers"></a>Verwenden von Containern

[Verwenden Sie die Container für die Textanalyse](how-tos/text-analytics-how-to-install-containers.md), um lokal Schlüsselbegriffe zu extrahieren, die Sprache zu ermitteln and die Stimmung zu analysieren. Installieren Sie dazu standardisierte Dock-Container, die sich näher bei Ihren Daten befinden.

## <a name="typical-workflow"></a>Typischer Workflow

Der Workflow ist einfach: Sie übermitteln Daten für die Analyse und verarbeiten Ausgaben in Ihrem Code. Analysetool werden unverändert (d.h. ohne Konfigurationen oder Anpassungen) verwendet.

1. [Registrieren Sie sich](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) für einen [Zugriffsschlüssel](how-tos/text-analytics-how-to-access-key.md). Der Schlüssel muss bei jeder Anforderung übergeben werden.

2. [Formulieren Sie eine Anforderung](how-tos/text-analytics-how-to-call-api.md#json-schema), die Ihre Daten als unformatierten, unstrukturierten Text im JSON-Format enthält.

3. Übermitteln Sie die Anforderung an den Endpunkt, der während der Registrierung eingerichtet wurde, und fügen Sie die gewünschten Ressourcen an: Standpunktanalyse, Schlüsselbegriffserkennung, Sprachenerkennung und Entitätsidentifikation.

4. Streamen oder speichern Sie die Antwort lokal. Je nach Anforderung ist das Ergebnis entweder eine Standpunktbewertung, eine Sammlung von extrahierten Schlüsselbegriffen oder ein Sprachcode.

Die Ausgabe wird als einzelnes JSON-Dokument mit Ergebnissen für jedes bereitgestellte Textdokument zurückgegeben und basiert auf der ID. Sie können die Ergebnisse anschließend analysieren, visualisieren oder in verwertbare Erkenntnisse kategorisieren.

Daten werden nicht in Ihrem Konto gespeichert. Von der Textanalyse-API durchgeführte Vorgänge sind zustandslos. Dies bedeutet, dass der bereitgestellte Text verarbeitet wird und die Ergebnisse sofort zurückgegeben werden.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Textanalyse für mehrere Stufen der Programmierungserfahrung

Sie können mit der Verwendung der Textanalyse-API in Ihren Prozessen beginnen, auch wenn Sie über wenig Erfahrung bei der Programmierung verfügen. Anhand dieser Tutorials erfahren Sie, wie Sie die API verwenden können, um Text gemäß Ihrem Erfahrungsstand auf verschiedene Weise zu analysieren. 

* Minimaler Programmieraufwand:
    * [Verwenden der Textanalyse-API und von MS Flow zum Ermitteln der Stimmung in Kommentaren in einer Yammer-Gruppe](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integrieren von Power BI mit der Textanalyse-API zur Analyse von Kundenfeedback](tutorials/tutorial-power-bi-key-phrases.md)
* Programmiererfahrung empfohlen:
    * [Standpunktanalyse für Streamingdaten mit Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Erstellen einer Flask-App zum Übersetzen von Text, Analysieren der Stimmung und Synthetisieren von Sprache](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Unterstützte Sprachen

Für eine bessere Auffindbarkeit wurde dieser Abschnitt in einen separaten Artikel verschoben. Diesen Inhalt finden Sie unter [Supported languages in the Text Analytics API (Unterstützte Sprachen in der Textanalyse-API)](text-analytics-supported-languages.md).

<a name="data-limits"></a>

## <a name="data-limits"></a>Datengrenzwerte

Alle Endpunkte der Textanalyse-API akzeptieren unformatierte Textdaten. Der aktuelle Grenzwert liegt bei 5.120 Zeichen für jedes Dokument. Wenn Sie größere Dokumente analysieren müssen, können Sie diese in kleinere Blöcke aufteilen. Wenn Sie dennoch einen höheren Grenzwert benötigen, [kontaktieren Sie uns](https://azure.microsoft.com/overview/sales-number/), damit Ihre Anforderungen besprochen werden können.

| Begrenzung | Wert |
|------------------------|---------------|
| Maximale Größe eines einzelnen Dokuments | 5.120 Zeichen (von [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) gemessen) |
| Maximale Größe der gesamte Anforderung | 1 MB |
| Maximale Anzahl von Dokumenten in einer Anforderung | 1.000 Dokumente |

Das Ratenlimit ist 100 Anforderungen pro Sekunde und 1.000 Anforderungen pro Minute. Sie können viele Dokumente (bis zu 1.000) in einem einzigen Aufruf übermitteln.

## <a name="unicode-encoding"></a>Unicode-Codierung

Die Textanalyse-API verwendet Unicode-Codierung für die Textdarstellung und die Berechnung der Zeichenanzahl. Anforderungen können in UTF-8 und UTF-16 übermittelt werden, dabei gibt es keine messbaren Unterschiede bei der Zeichenanzahl. Unicode-Codepunkte werden als Heuristik für die Zeichenlänge verwendet und werden für die Grenzwerte der Textanalysedaten als äquivalent betrachtet. Wenn Sie [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) verwenden, um die Zeichenanzahl abzurufen, verwenden Sie die gleiche Methode wie wir, um die Datengröße zu ermitteln.

## <a name="next-steps"></a>Nächste Schritte

+ [Registrieren Sie sich](how-tos/text-analytics-how-to-signup.md), um einen Zugriffsschlüssel zu erhalten, und befolgen Sie die Schritte zum [Aufrufen der API](how-tos/text-analytics-how-to-call-api.md).

+ Der [Schnellstart](quickstarts/csharp.md) stellt eine exemplarische Vorgehensweise für REST-API-Aufrufe in C# dar. Erfahren Sie, wie Sie mit minimalem Code Text übermitteln, eine Analyse auswählen und Ergebnisse anzeigen. Sie können stattdessen auch mit der [Schnellstartanleitung für Python](quickstarts/python.md) beginnen.

+ In [diesem Tutorial zur Standpunktanalyse](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) mit Azure Databricks erhalten Sie ausführlichere Informationen.

+ Eine Liste von Blogbeiträgen und Videos, in denen die Verwendung der Textanalyse-API mit anderen Tools und Technologien veranschaulicht wird, finden Sie auf der Seite [Externe Inhalte und Community-Inhalte für die Textanalyse in Cognitive Service](text-analytics-resource-external-community.md).
