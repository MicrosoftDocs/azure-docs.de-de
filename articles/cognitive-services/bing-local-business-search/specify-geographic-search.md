---
title: Filtern von Ergebnissen aus der API für die Bing-Suche nach ortsansässigen Unternehmen mit geografischen Begrenzungen | Microsoft-Dokumentation
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie Suchergebnisse aus der API für die Bing-Suche nach ortsansässigen Unternehmen filtern.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: e87ef96184974d10f99028be13399d1422a1c774
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65796595"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Filtern von Ergebnissen aus der API für die Bing-Suche nach ortsansässigen Unternehmen mit geografischen Begrenzungen

Mit der API für die Bing-Suche nach ortsansässigen Unternehmen können Sie mit dem Abfrageparameter `localCircularView` oder `localMapView` Begrenzungen für den geografischen Bereich festlegen, in dem Sie die Suche ausführen möchten. Verwenden Sie in Ihren Abfragen nur einen Parameter. 

Wenn ein Suchbegriff einen bestimmten geografischen Ort enthält, verwendet die API für die Bing-Suche nach ortsansässigen Unternehmen diesen automatisch, um die Suchergebnisse einzugrenzen. Wenn der Suchbegriff beispielsweise `sailing in San Diego` lautet, wird `San Diego` als Ort verwendet und alle anderen angegebenen Orte in den Abfrageparametern oder Benutzerheadern werden ignoriert. 

Wenn im Suchbegriff kein geografischer Ort erkannt und mit den Abfrageparametern kein geografischer Ort angegeben wird, versucht die API für die Bing-Suche nach ortsansässigen Unternehmen den Ort mithilfe des Anforderungsheaders `X-Search-ClientIP` oder `X-Search-Location` zu bestimmen. Wenn kein Header angegeben ist, bestimmt die API den Ort entweder anhand der Client-IP der Anforderung oder anhand der GPS-Koordinaten des mobilen Geräts.

## <a name="localcircularview"></a>localCircularView

Der Parameter `localCircularView` erstellt einen kreisförmigen geografischen Bereich mit einem Koordinatensatz aus einem Breiten- und Längengrad, der durch einen Radius definiert ist. Wenn Sie diesen Parameter verwenden, schließen die Antworten der API für die Bing-Suche nach ortsansässigen Unternehmen nur Orte innerhalb dieses Kreises ein. Im Gegensatz dazu berücksichtigt Parameter `localMapView` auch Orte, die sich ein wenig außerhalb des Suchbereichs befinden.

Um einen kreisförmigen geografischen Suchbereich anzugeben, wählen Sie einen Breitengrad und einen Längengrad aus – der Punkt dient als Mittelpunkt des Kreises – sowie einen Radius in Metern. Dieser Parameter kann dann z.B. einer Abfragezeichenfolge angefügt werden: `q=Restaurants&localCircularView=47.6421,-122.13715,5000`.

Vollständige Abfrage:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

Der Parameter `localMapView` gibt einen rechteckigen geografischen Suchbereich an, wobei zwei Koordinatensätze verwendet werden, die die südöstlichen und nordwestlichen Ecken bestimmen. Wenn Sie diesen Parameter verwenden, schließen die Antworten der API für die Bing-Suche nach ortsansässigen Unternehmen Orte innerhalb sowie außerhalb dieses Kreises ein. Im Gegensatz dazu berücksichtigt Parameter `localCircularView` nur Orte, die sich innerhalb des Suchbereichs befinden.

Um ein rechteckiges Suchgebiet anzugeben, wählen Sie zwei Koordinatensätze aus jeweils einem Breiten-/Längengrad aus, die als südöstliche und nordwestliche Ecken der Begrenzung dienen. Achten Sie darauf, dass Sie zuerst die Südostkoordinaten definieren: `localMapView=47.619987,-122.181671,47.6421,-122.13715`.

Vollständige Abfrage:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>Nächste Schritte
- [Schnellstart: Suche nach ortsansässigen Unternehmen mit Java](quickstarts/local-search-java-quickstart.md)
- [Schnellstart: Suche nach ortsansässigen Unternehmen mit C#](quickstarts/local-quickstart.md)
- [Schnellstart: Suche nach ortsansässigen Unternehmen mit Node](quickstarts/local-search-node-quickstart.md)
- [Schnellstart: Suche nach ortsansässigen Unternehmen mit Python](quickstarts/local-search-python-quickstart.md)
