---
title: Bing-News-Suche-Endpunkte
titlesuffix: Azure Cognitive Services
description: Überblick über den API-Endpunkt für die News-Suche.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 6ea218da23d65696c76008cb15e183fcc4bbda10
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383231"
---
# <a name="bing-news-search-api-endpoints"></a>Bing-News-Suche-API-Endpunkte

Die **News-Suche-API** gibt Nachrichtenartikel, Webseiten, Bilder, Videos und [Entitäten](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) zurück. Entitäten enthalten zusammenfassende Informationen zur einer Person, einem Ort oder einem Thema.

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

Gibt die wichtigsten Nachrichten geordnet nach Kategorien zurück. Mit `category=business`, `category=sports` oder `category=entertainment` können Sie insbesondere die obersten Artikel aus den Bereichen „Wirtschaft“, „Sport“ oder „Unterhaltung“ anfordern.  Der `category`-Parameter kann nur mit der `/news`-URL verwendet werden. Es gibt einige formale Anforderungen für die Angabe von Kategorien; siehe `category` in der Dokumentation der [Abfrageparameter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters). Senden Sie eine Abfrage durch URL-Codierung Ihres Suchbegriffs, und fügen Sie sie dem `q=""`-Parameter an. Informationen zur Verfügbarkeit finden Sie unter [Unterstützte Länder/Regionen und Märkte](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Beliebteste Nachrichtenthemen 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Gibt Nachrichtenthemen zurück, die derzeit in sozialen Netzwerken beliebt sind. Wenn die `/trendingtopics`-Option enthalten ist, ignoriert die Bing-Suche einige andere Parameter, wie z.B. `freshness` und `?q=""`. Informationen zur Verfügbarkeit finden Sie unter [Unterstützte Länder/Regionen und Märkte](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Headern, Parametern, Marktcodes, Antwortobjekten, Fehlern usw. finden Sie in der Referenz [API für die Bing-News-Suche v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).

Die umfassenden Informationen zu den Parametern, die von jedem Endpunkt unterstützt werden, finden Sie auf den Referenzseiten des jeweiligen Typs.
Beispiele für grundlegende Anforderung mithilfe der Videosuche-API finden Sie in den [Bing-News-Suche-Schnellstarts](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
