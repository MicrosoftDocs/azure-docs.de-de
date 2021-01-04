---
title: Verwendung von Dekorationsmarkierungen zum Hervorheben von Text – Bing-Websuche-API
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie mit der Bing-Websuche-API Textverzierungen und Hervorhebungen in Ihren Suchergebnissen verwenden können.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: scottwhi
ms.openlocfilehash: 32c3279cacddf10e77e8d245ba525ab766efd6fd
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351877"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Verwenden von Dekorationsmarkierungen zum Markieren von Text

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Für Bing wird die Treffermarkierung unterstützt, bei der Abfrageausdrücke (oder andere Ausdrücke, die von Bing als relevant eingestuft werden) in den Anzeigezeichenfolgen einiger Antworten markiert werden. Beispielsweise können die Felder `name`, `displayUrl` und `snippet` der Ergebnisse einer Webseite markierte Abfragebegriffe enthalten. 

Standardmäßig werden in Bing keine Hervorhebungsmarkierungen in Anzeigezeichenfolgen eingefügt. Fügen Sie zum Aktivieren der Markierungen den Abfrageparameter `textDecorations` in Ihre Anforderung ein, und legen Sie ihn auf `true` fest.

## <a name="hit-highlighting-example"></a>Beispiel für Treffermarkierung

Im folgenden Beispiel wird ein Webergebnis für `Sailing Dinghy` veranschaulicht. In Bing werden Anfang und Ende von Abfrageausdrücken mit den Unicode-Zeichen E000 und E001 markiert.
  
![Treffermarkierung](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Bevor das Ergebnis auf Ihrer Benutzeroberfläche angezeigt wird, sollten Sie die Unicode-Zeichen durch Zeichen ersetzen, die für Ihr Anzeigeformat geeignet sind.

## <a name="marker-formatting"></a>Markerformatierung

In Bing haben Sie die Möglichkeit, Unicode-Zeichen oder HTML-Tags als Marker bereitzustellen. Fügen Sie den Abfrageparameter [textFormat](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) ein, um anzugeben, welche Marker verwendet werden sollen. 

| Wert             | Marker                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Unicode-Zeichen (Standard) |
| `textFormat=HTML` | HTML-Zeichen              |

## <a name="additional-text-decorations"></a>Zusätzliche Textdekorationen

Mit Bing können mehrere verschiedene Textdekorationen zurückgegeben werden. Eine `Computation`-Antwort kann z. B. tiefgestellte Marker für den Abfragebegriff `log(2)` im Feld `expression` enthalten.

![Computation-Markierungen](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Wenn die Anforderung keine Dekorationen angibt, würde das Feld `expression` den Wert `log10(2)` enthalten. 

Wenn `textDecorations` auf `true` festgelegt ist, können in Bing die unten angegebenen Marker in die Anzeigezeichenfolgen von Antworten eingefügt werden. Falls keine Entsprechung als HTML-Tag vorhanden ist, enthält die Tabellenzelle keine Angabe.

|Unicode|HTML|Beschreibung
|-|-|-
|U+E000|\<b>|Markiert den Anfang des Abfrageausdrucks (Treffermarkierung)
|U+E001|\</b>|Markiert das Ende des Abfrageausdrucks
|U+E002|\<i>|Markiert den Anfang von Text in Kursivdruck 
|U+E003|\</i>|Markiert das Ende von Text in Kursivdruck
|U+E004|\<br/>|Markiert einen Zeilenumbruch
|U+E005||Markiert den Anfang einer Telefonnummer
|U+E006||Markiert das Ende einer Telefonnummer
|U+E007||Markiert den Anfang einer Adresse
|U+E008||Markiert das Ende einer Adresse
|U+E009|\&nbsp;|Markiert ein geschütztes Leerzeichen
|U+E00C|\<strong>|Markiert den Anfang von Text in Fettdruck
|U+E00D|\</strong>|Markiert das Ende von Text in Fettdruck
|U+E00E||Markiert den Anfang von Text, dessen Hintergrund heller als der umgebende Hintergrund sein soll
|U+E00F||Markiert das Ende von Text, dessen Hintergrund heller als der umgebende Hintergrund sein soll
|U+E010||Markiert den Anfang von Text, dessen Hintergrund dunkler als der umgebende Hintergrund sein soll
|U+E011||Markiert das Ende von Text, dessen Hintergrund dunkler als der umgebende Hintergrund sein soll
|U+E012|\<del>|Markiert den Anfang von Text, der durchgestrichen sein soll
|U+E013|\</del>|Markiert das Ende von Text, der durchgestrichen sein soll
|U+E016|\<sub>|Markiert den Anfang von tiefgestelltem Text
|U+E017|\</sub>|Markiert das Ende von tiefgestelltem Text
|U+E018|\<sup>|Markiert den Anfang von hochgestelltem Text
|U+E019|\</sup>|Markiert das Ende von hochgestelltem Text

## <a name="next-steps"></a>Nächste Schritte

* [Was ist die Bing-Websuche-API?](overview.md) 
* [Ändern der Größe und Zuschneiden von Miniaturansichten](resize-and-crop-thumbnails.md)