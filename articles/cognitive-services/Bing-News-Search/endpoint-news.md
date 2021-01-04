---
title: Bing-News-Suche-Endpunkte
titleSuffix: Azure Cognitive Services
description: 'Dieser Artikel bietet eine Zusammenfassung zu den Endpunkten der API für die News-Suche: Nachrichten, Top-Nachrichten und populäre Nachrichten.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 9c095a46b3a0526b23645c9cbb5e99eb8eda9067
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366399"
---
# <a name="bing-news-search-api-endpoints"></a>Bing-News-Suche-API-Endpunkte

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Die **News-Suche-API** gibt Nachrichtenartikel, Webseiten, Bilder, Videos und [Entitäten](../bing-entities-search/overview.md) zurück. Entitäten enthalten zusammenfassende Informationen zur einer Person, einem Ort oder einem Thema.

## <a name="endpoints"></a>Endpunkte

Um mithilfe der Bing-News-Suche-API Suchergebnisse zu erhalten, senden Sie eine `GET`-Anforderung an einen der folgenden Endpunkte. Die Header und die URL-Parameter definieren weitere Spezifikationen.

### <a name="news-items-by-search-query"></a>Nachrichtenelemente nach Suchabfrage

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Gibt Nachrichtenelemente basierend auf einer Suchabfrage zurück. Wenn die Suchabfrage leer ist, gibt die API Artikel mit den wichtigsten Nachrichten aus verschiedenen Kategorien zurück. Senden Sie eine Abfrage durch URL-Codierung Ihres Suchbegriffs, und fügen Sie sie dem `q=""`-Parameter an. Informationen zur Verfügbarkeit finden Sie unter [Unterstützte Länder/Regionen und Märkte](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Wichtigste Nachrichten nach Kategorie

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Gibt die wichtigsten Nachrichten geordnet nach Kategorien zurück. Mit `category=business`, `category=sports` oder `category=entertainment` können Sie insbesondere die obersten Artikel aus den Bereichen „Wirtschaft“, „Sport“ oder „Unterhaltung“ anfordern.  Der `category`-Parameter kann nur mit der `/news`-URL verwendet werden. Es gibt einige formale Anforderungen für die Angabe von Kategorien; siehe `category` in der Dokumentation der [Abfrageparameter](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters). Senden Sie eine Abfrage durch URL-Codierung Ihres Suchbegriffs, und fügen Sie sie dem `q=""`-Parameter an. Informationen zur Verfügbarkeit finden Sie unter [Unterstützte Länder/Regionen und Märkte](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Beliebteste Nachrichtenthemen 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Gibt Nachrichtenthemen zurück, die derzeit in sozialen Netzwerken beliebt sind. Wenn die `/trendingtopics`-Option enthalten ist, ignoriert die Bing-Suche einige andere Parameter, wie z.B. `freshness` und `?q=""`. Informationen zur Verfügbarkeit finden Sie unter [Unterstützte Länder/Regionen und Märkte](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Headern, Parametern, Marktcodes, Antwortobjekten, Fehlern usw. finden Sie in der Referenz [API für die Bing-News-Suche v7](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).

Die umfassenden Informationen zu den Parametern, die von jedem Endpunkt unterstützt werden, finden Sie auf den Referenzseiten des jeweiligen Typs.
Beispiele für grundlegende Anforderung mithilfe der Videosuche-API finden Sie in den [Bing-News-Suche-Schnellstarts](/azure/cognitive-services/bing-news-search).