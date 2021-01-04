---
title: Beispielbenutzerszenarien für die Textanalyse-API
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie mehr über einige gängige Szenarien für die Integration der Textanalyse-API in Ihre Dienste und Prozesse.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: a4c551a6e9b8d79fdeb7b5219f311c7c7c969757
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505357"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Beispielbenutzerszenarien für die Textanalyse-API

Die Textanalyse-API ist ein Clouddienst für die erweiterte Verarbeitung natürlicher Sprache von Text. Dieser Artikel beschreibt einige Beispiele von Anwendungsfällen für die Integration der API in Ihre Unternehmenslösungen und -prozesse. 

## <a name="analyze-survey-results"></a>Analysieren von Befragungsergebnissen

Gewinnen Sie Erkenntnisse aus den Ergebnissen von Kunden- und Mitarbeiterbefragungen, indem Sie die Rohtextantworten mithilfe der Standpunktanalyse verarbeiten. Aggregieren Sie die Ergebnisse für die Analyse, Nachbereitung und Förderung der Motivation.

![Abbildung mit einer Beschreibung der Standpunktanalyse bei Kunden- und Mitarbeiterbefragungen.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analysieren aufgezeichneter eingehender Kundenanrufe

Gewinnen Sie Erkenntnisse aus Anrufen beim Kundendienst mithilfe der Spracherkennung, Standpunktanalyse und Schlüsselbegriffserkennung. Zeigen Sie die Ergebnisse im Power BI-Dashboard oder in einem Portal an, um Kunden besser zu verstehen, Trends im Kundendienst aufzuzeigen und die Kundenbindung zu fördern. Senden Sie API-Anforderungen als Batch für die Berichterstellung oder in Echtzeit für Eingriffe. Sehen Sie sich den Beispielcode auf [GitHub](https://github.com/rlagh2/callcenteranalytics) an.

![Abbildung mit einer Beschreibung, wie das Gewinnen von Erkenntnissen aus Anrufen beim Kundendienst mithilfe der Standpunktanalyse automatisiert werden kann](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Verarbeiten und Kategorisieren von Supportvorfällen

Verwenden Sie die Schlüsselbegriffs- und Entitätserkennung zum Verarbeiten von Supportanfragen, die in einem unstrukturierte Textformat eingehen. Verwenden Sie die extrahierten Begriffe und Entitäten, um die Anforderungen für die Ressourcenplanung und Trendanalyse zu kategorisieren.

![Eine Abbildung mit einer Beschreibung, wie die Schlüsselbegriffs- und Entitätserkennung verwendet werden kann, um Schadensberichte und Trends zu kategorisieren](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Überwachen der Feeds in sozialen Medien zu Ihrem Produkt

Überwachen Sie auf der Twitter- oder Facebook-Seite Ihres Produkts das Benutzerfeedback. Nutzen Sie die Daten, um die Stimmung der Kunden bei der Einführung neuer Produkte zu analysieren, Schlüsselbegriffe zu Funktionen und Funktionswünschen zu extrahieren oder Kundenbeschwerden sofort zu bearbeiten. Weitere Informationen finden Sie im Beispiel [Microsoft Power Automate-Vorlage](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/).

![Abbildung mit einer Beschreibung, wie Sie das Feedback zu Ihrem Produkt und Unternehmen in sozialen Medien mithilfe der Schlüsselbegriffserkennung überwachen können](media/use-cases/social-feed.svg)

## <a name="classify-and-redact-documents-that-have-sensitive-information"></a>Klassifizieren und bearbeiten Sie Dokumente, die vertrauliche Informationen enthalten.

Verwenden Sie die Erkennung benannter Entitäten, um in Dokumenten personenbezogene und vertrauliche Informationen zu identifizieren. Verwenden Sie die Daten zum Klassifizieren oder Bearbeiten von Dokumenten, damit sie auf sichere Weise freigegeben werden können.

![Abbildung zur Verwendung der Erkennung benannter Entitäten, um personenbezogene Informationen zu erkennen und Dokumente zu klassifizieren und zu bearbeiten](media/use-cases/sensitive-docs.jpg)

## <a name="perform-opinion-mining"></a>Ausführen von Opinion Mining

Gruppieren Sie Meinungen im Zusammenhang mit bestimmten Aspekten eines Produkts oder Diensts in Umfragen und Kundenfeedback oder in Text, der eine Meinung zu einem Aspekt enthält. Verwenden Sie dieses Verfahren, um Produkteinführungen und -verbesserungen sowie Marketingmaßnahmen zu steuern und die Leistung Ihres Produkts oder Diensts hervorzuheben. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="media/use-cases/aspect-based-sentiment.png" alt-text="Beispielmeinungen zu einem Hotel":::

## <a name="next-steps"></a>Nächste Schritte

* [Worum handelt es sich bei der Textanalyse-API?](overview.md)
* [Senden einer Anforderung an die Textanalyse-API mit der Clientbibliothek](quickstarts/client-libraries-rest-api.md)
