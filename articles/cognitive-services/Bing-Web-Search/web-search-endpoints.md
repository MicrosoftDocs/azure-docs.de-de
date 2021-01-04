---
title: Websuche-Endpunkt
titleSuffix: Azure Cognitive Services
description: Um Ergebnisse der Websuche zu erhalten, senden Sie eine `GET`-Anforderung an den folgenden Endpunkt. Die Header und die URL-Parameter definieren weitere Spezifikationen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: c882e3e4d0cd6ba594a700f4fd53c14103a8d1d1
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381251"
---
# <a name="web-search-endpoint"></a>Websuche-Endpunkt

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Die **Websuche-API** gibt Webseiten, Nachrichten, Bilder, Videos und [Entitäten](../bing-entities-search/overview.md) zurück. Entitäten verfügen über zusammenfassende Informationen zur einer Person, einem Ort oder einem Thema.

## <a name="endpoint"></a>Endpunkt

Um mithilfe der Bing-API Websuchergebnisse zu erhalten, senden Sie eine `GET`-Anforderung an den folgenden Endpunkt. Die Header und die URL-Parameter definieren weitere Spezifikationen.

**Endpunkt:** gibt Webergebnisse zurück, die für die von `?q=""` definierte Suchabfrage des Benutzers relevant sind

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Endpunkt: Weitere Informationen zu Headern, Parametern, Marktcodes, Antwortobjekten, Fehlern und Ähnlichem finden Sie in der Referenz [API für die Bing-Websuche v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).

## <a name="response-json"></a>JSON-Antwort

Die Antwort auf eine Websuchanforderung enthält alle Ergebnisse als JSON-Objekten. Für eine Analyse sind Prozeduren zum Verarbeiten der einzelnen Elementtypen erforderlich. Beispiele finden Sie unter [Tutorial](./tutorial-bing-web-search-single-page-app.md) und [Quellcode](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search).

## <a name="next-steps"></a>Nächste Schritte

Die **Bing** -APIs unterstützen Suchaktionen, die Ergebnisse gemäß ihrem Typ zurückgeben.  Alle Suchendpunkte geben Ergebnisse als JSON-Antwortobjekte zurück.  Alle Endpunkte unterstützen Abfragen, die eine bestimmte Sprache und einen bestimmten Ort anhand von Längengrad, Breitengrad und Suchradius zurückgeben.

Die umfassenden Informationen zu den Parametern, die von jedem Endpunkt unterstützt werden, finden Sie auf den Referenzseiten des jeweiligen Typs.
Beispiele für grundlegende Anforderung mithilfe der Websuche-API finden Sie in den [Web-Schnellstarts](./overview.md).