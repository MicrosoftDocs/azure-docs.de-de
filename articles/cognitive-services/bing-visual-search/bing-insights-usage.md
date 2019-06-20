---
title: Beispiele für Bing-Auswertungen – Visuelle Bing-Suche
titleSuffix: Azure Cognitive Services
description: Hier finden Sie Beispiele für Bildauswertungen, die auf bing.com angezeigt werden.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: 94f8d4ea98f42ad919597ef53dc63281825f0e6b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60610332"
---
# <a name="examples-of-bing-insights-usage"></a>Beispiele für die Nutzung von Bing-Auswertungen

Dieser Artikel enthält Beispiele dafür, wie Bing Bildauswertungen auf bing.com verwendet und anzeigt.

## <a name="pagesincluding-insight-example"></a>Beispiel für eine Auswertung vom Typ PagesIncluding

Im folgenden Beispiel wird ein Link zur ersten Webseite angezeigt, und der Benutzer kann die Liste der anderen Webseiten, die das Bild enthalten, erweitern und reduzieren:

![Erweiterte Liste von Seiten mit dem Bild](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>Beispiel für eine Auswertung vom Typ ShoppingSources

Das folgende Beispiel zeigt, wie Bing Einkaufsquellen für Produkte anzeigen kann, die im Bild zu sehen sind:

![Einkaufsquellen](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>Beispiel für eine Auswertung vom Typ VisualSearch

Nachfolgend wird gezeigt, wie Bing visuell ähnliche Bilder anzeigen kann (siehe **Verwandte Bilder** im Beispiel):

![Visuell ähnliche Bilder](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Beispiel für eine Auswertung vom Typ Recipes

Nachfolgend sehen Sie, wie Bing Rezepte für die im Bild gezeigten Nahrungsmittel anzeigen kann. Im Beispiel wird dem Benutzer mitgeteilt, dass Rezepte verfügbar sind:

![Rezepte und Seiten mit dem Bild](./media/recipes-pages-including.PNG)

 Wenn der Benutzer die Liste erweitert, werden die Links zu den Rezepten bereitgestellt:

![Erweiterte Liste von Rezeptseiten mit dem Bild](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>Beispiel für eine Auswertung vom Typ RelatedSearches

Nachfolgend wird gezeigt, wie Bing verwandte Suchvorgänge für Bilder anzeigen kann, die von anderen Personen ausgeführt wurden. Wenn der Benutzer auf das Bild klickt, wird er zur Suchergebnisseite „bing.com/images“ für die zugehörige Abfrage weitergeleitet.

![Verwandte Suchvorgänge für Bilder](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Beispiel für eine Auswertung zur Entität

Nachfolgend sehen Sie, wie Bing Informationen zu der im Bild dargestellten Entität (Person, Ort oder Sache) anzeigen kann. Wenn der Benutzer auf den Link für die Entität klickt, wird er zur Suchergebnisseite von Bing.com für die Entität weitergeleitet:

![Im Bild gezeigte Entität](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>Anzeigen weiterer Auswertungen, die vom Benutzer durchsucht werden können

Das folgende Beispiel zeigt, wie Bing weitere Informationen zu dem Bild anzeigen kann, die vom Benutzer durchsucht werden können.

![Durchsuchen weiterer Auswertungen zum Bild](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Begrenzungsrahmen und Hotspots

Nicht standardmäßige Tags umfassen den Begrenzungsrahmen, der den relevanten Bereich im Bild angibt, für den das Tag gilt. Wenn der Begrenzungsrahmen nicht das gesamte Bild angibt, verwenden Sie ihn, um einen Hotspot auf dem Bild zu erstellen. Der Benutzer kann auf den Hotspot klicken, um Informationen zu dem Inhalt abrufen, der sich unter dem Hotspot (oder Rechteck) befindet. Wenn es sich beispielsweise um eine Modebild handelt, können die Ergebnisse Tags (und Begrenzungsrahmen) für im Bild gezeigte Accessoires wie Handtaschen, Schmuck, Schals usw. enthalten. Das folgende Beispiel zeigt ein Rechteck als Hotspot für die im Bild gezeigte Sonnenbrille:

![Begrenzungsrahmen und Hotspot](./media/click-to-search.PNG)

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Schnellstartanleitungen ermöglichen den Einstieg in die Verwendung Ihrer ersten Anforderung: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)





