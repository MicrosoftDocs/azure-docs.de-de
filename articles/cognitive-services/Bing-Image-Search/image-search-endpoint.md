---
title: Endpunkte für die Bing-Bildersuche-API
titleSuffix: Azure Cognitive Services
description: Die Bildersuche-API umfasst drei Endpunkte. Endpunkt 1 gibt Bilder aus dem Internet zurück. Endpunkt 2 gibt „ImageInsights“ zurück. Endpunkt 3 gibt beliebte Videos zurück.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 67fec77136f5d593279be2846e63c51b60e16bb4
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593517"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Endpunkte für die Bing-Bildersuche-API

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Die **Bildersuche-API**  umfasst drei Endpunkte.  Endpunkt 1 gibt Bilder aus dem Internet basierend auf einer Abfrage zurück. Endpunkt 2 gibt [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) zurück.  Endpunkt 3 gibt beliebte Videos zurück.

## <a name="endpoints"></a>Endpunkte

Um mithilfe der Bing-API Bildergebnisse zu erhalten, senden Sie eine Anforderung an einen der folgenden Endpunkte. Verwenden Sie die Header und die URL-Parameter, um Spezifikationen genauer zu definieren.

**Endpunkt 1:** gibt Entitäten Bilder, die für die von `?q=""` definierte Suchabfrage des Benutzers relevant sind
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Endpunkt 2:** gibt entweder mit `GET` oder `POST` Einblicke zum Bild zurück.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Eine GET-Anforderung gibt Einblicke zu einem Bild zurück, z.B. Webseiten, die dieses Bild enthalten. Integrieren Sie den [InsightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)-Parameter in eine `GET`-Anforderung.

Oder Sie können ein Binärbild in den Text einer `POST`-Anforderung einfügen und den [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested)-Parameter auf `RecognizedEntities` setzen. Dies gibt einen [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken)-Parameter zurück, der in einer nachfolgenden `GET`-Anforderung verwendet wird, die Informationen über Personen im Bild zurückgibt.  Setzen Sie `modules` auf `All`, um alle Einblicke zu erhalten, außer `RecognizedEntities` in den Ergebnissen von `POST`, ohne einen weiteren Aufruf mit `insightsToken` zu machen.


**Endpunkt 3:** gibt Bilder zurück, die auf der Grundlage von Suchanfragen anderer beliebt sind. Die Bilder sind in verschiedene Kategorien eingeteilt, z.B. nach Personen oder Ereignissen.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Eine Liste der Märkte, die beliebte Bilder unterstützen, finden Sie unter [Beliebte Bilder](./trending-images.md).

Weitere Informationen zu Headern, Parametern, Marktcodes, Antwortobjekten, Fehlern usw. finden Sie in der Referenz [API für die Bing-Bildersuche v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).
## <a name="response-json"></a>JSON-Antwort
Die Antwort auf eine Bildersuchanforderung enthält die Ergebnisse als JSON-Objekten. Beispiele für die Analyse der Ergebnisse finden Sie unter [Tutorial](./tutorial-bing-image-search-single-page-app.md) und [Quellcode](./tutorial-bing-image-search-single-page-app.md).

## <a name="next-steps"></a>Nächste Schritte
Die **Bing**-APIs unterstützen Suchaktionen, die Ergebnisse gemäß ihrem Typ zurückgeben.  Alle Suchendpunkte geben Ergebnisse als JSON-Antwortobjekte zurück.  Alle Endpunkte unterstützen Abfragen, die eine bestimmte Sprache und/oder einen bestimmten Ort nach Längengrad, Breitengrad und Suchradius zurückgeben.

Die umfassenden Informationen zu den Parametern, die von jedem Endpunkt unterstützt werden, finden Sie auf den Referenzseiten des jeweiligen Typs.
Beispiele für grundlegende Anforderung mithilfe der Bildersuche-API finden Sie in den [Bildersuche-Schnellstarts](./overview.md).