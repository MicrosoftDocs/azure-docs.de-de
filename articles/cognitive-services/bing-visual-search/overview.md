---
title: Was ist die API für die visuelle Bing-Suche?
titleSuffix: Azure Cognitive Services
description: Die visuelle Bing-Suche stellt Details oder Informationen zu einem Bild bereit, wie z. B. ähnliche Bilder oder Einkaufsquellen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 12/19/2019
ms.author: scottwhi
ms.openlocfilehash: 7dfc704fb38550993adb7835d4500dee890117a8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486979"
---
# <a name="what-is-the-bing-visual-search-api"></a>Was ist die API für die visuelle Bing-Suche?

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Die API für die visuelle Bing-Suche gibt Erkenntnisse für ein Bild zurück. Sie können entweder ein Bild hochladen oder eine URL für ein Bild eingeben. Erkenntnisse sind visuell ähnliche Bilder, Einkaufsquellen, Webseiten, auf denen das Bild vorkommt, und Ähnliches. Die von der API für die visuelle Bing-Suche zurückgegebenen Erkenntnisse ähneln den Ergebnissen auf „Bing.com/images“. 

Bei Verwendung der [Bing-Bildersuche-API](../bing-image-search/overview.md) können Sie für Ihre visuelle Bing-Suche Erkenntnistoken aus den Suchergebnissen dieser API verwenden, anstatt ein Bild hochzuladen.

> [!IMPORTANT]
> Wenn Sie Bildauswertungen mit der Bing-Bildersuche-API abrufen, können Sie auch zur API für die visuelle Bing-Suche wechseln, die umfassendere Erkenntnisse bietet.

## <a name="insights"></a>Einblicke

Mit der visuellen Bing-Suche können folgende Erkenntnisse ermittelt werden:

| Erkenntnis                              | BESCHREIBUNG |
|--------------------------------------|-------------|
| Visuell ähnliche Bilder              | Eine Liste mit Bildern, die dem Eingabebild visuell ähnlich sind. |
| Visuell ähnliche Produkte            | Produkte, die dem gezeigten Produkt visuell ähnlich sind.            |
| Einkaufsquellen                     | Orte, an denen Sie das Produkt auf dem Eingabebild kaufen können.            |
| Verwandte Suchvorgänge                     | Verwandte Suchvorgängen, die von anderen durchgeführt wurden oder die auf dem Bildinhalt basieren.            |
| Webseiten, auf denen das Bild vorkommt     | Webseiten, die das Eingabebild enthalten.            |
| Rezepte                              | Webseiten, die Rezepte für das Gericht auf dem Eingabebild enthalten.            |
| Entitäten                             | Bekannte Personen, Orte und Objekte |

Neben Erkenntnissen gibt die visuelle Bing-Suche auch verschiedene, aus dem Eingabebild abgeleitete Begriffe (Tags) zurück. Anhand dieser Tags können Benutzer Konzepte erkunden, die in dem Bild gefunden wurden. Zeigt das Eingabebild beispielsweise einen bekannten Sportler, kann etwa eines der Tags den Namen des Sportlers und ein anderes den Begriff „Sport“ enthalten. Beim Bild eines Apfelkuchens können die Tags beispielsweise „Apfelkuchen“, „Kuchen“ und „Nachtisch“ lauten.

Die Ergebnisse der visuellen Bing-Suche beinhalten auch Begrenzungsrahmen für relevante Bildbereiche. Sind auf dem Bild beispielsweise mehrere Prominente abgebildet, können die Ergebnisse etwa Begrenzungsrahmen für jeden erkannten Prominenten enthalten. Wenn Bing auf dem Bild ein Produkt oder Kleidungsstück erkennt, kann das Ergebnis einen Begrenzungsrahmen für den erkannten Artikel enthalten.

## <a name="workflow"></a>Workflow

Die API für die visuelle Bing-Suche ist ein RESTful-Webdienst und kann somit problemlos in jeder Programmiersprache aufgerufen werden, die HTTP-Anforderungen beherrscht und JSON analysieren kann. Für den Dienst kann entweder die REST-API oder das SDK verwendet werden.

1. Erstellen Sie ein [Cognitive Services-Konto](../cognitive-services-apis-create-account.md) mit Zugriff auf die Bing-Suche-APIs. Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/cognitive-services/).
2. Senden Sie eine Anforderung mit einer gültigen Suchabfrage an die API.
3. Analysieren Sie die zurückgegebene JSON-Nachricht, um die API-Antwort zu verarbeiten.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich zunächst die [interaktive Demo](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/) zur API für die visuelle Bing-Suche an.
Diese Demo zeigt, wie Sie schnell eine Suchabfrage anpassen und das Web nach Bildern durchsuchen.

Die folgenden Schnellstartanleitungen ermöglichen einen schnellen Einstieg in die Verwendung Ihrer ersten Anforderung:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [Node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)

## <a name="see-also"></a>Weitere Informationen

* Die Referenz [Images - Visual Search](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) (Bilder: visuelle Suche) enthält Definitionen und Informationen zu den Endpunkten, Anforderungsheadern, Antworten und Abfrageparametern, die Sie verwenden können, um bildbasierte Suchergebnisse anzufordern.

* In den [Anforderungen für die Verwendung und Anzeige der Bing-Suche-API](../bing-web-search/use-display-requirements.md) erfahren Sie, wie Inhalte und Informationen verwendet werden dürfen, die über die Bing-Suche-APIs gefunden wurden.

* Besuchen Sie die Seite [Was ist die Bing-Websuche-API?](../bing-web-search/overview.md), um die anderen verfügbaren APIs zu untersuchen.