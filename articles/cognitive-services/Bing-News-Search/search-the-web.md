---
title: Was ist die Bing-News-Suche-API?
titlesuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie mit der Bing News-Suche-API im Web nach aktuellen Schlagzeilen in mehreren Kategorien suchen, einschließlich Überschriften und Trendthemen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 01/10/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: e02f81ef6bdf8bf4dc39b70cb7a2d9330660d1d6
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390295"
---
# <a name="what-is-the-bing-news-search-api"></a>Was ist die Bing-News-Suche-API?

Die Bing-News-Suche-API vereinfacht das Integrieren der Funktionen der kognitiven Bing-News-Suche in Anwendungen. Die API bietet eine ähnliche Oberfläche wie [Bing News](https://www.bing.com/news). Sie können Suchabfragen senden und relevante Nachrichtenartikel empfangen.

Hinweis: Die Bing-News-Suche-API liefert ausschließlich Nachrichten als Suchergebnisse. Verwenden Sie für andere Arten von Webinhalten die [Bing-Websuche-API](../bing-web-search/search-the-web.md), die [Videosuche-API](../bing-video-search/search-the-web.md) und die [Bildersuche-API](../bing-image-search/overview.md).

## <a name="bing-news-search-api-features"></a>Funktionen der Bing-News-Suche-API

Die Bing News-Suche-API dient zwar in erster Linie zum Suchen und Zurückgeben von relevanten Nachrichtenartikeln, sie bietet jedoch auch verschiedene Funktionen für den intelligenten und gezielten Abruf von Nachrichten im Web.

|Feature  |BESCHREIBUNG  |
|---------|---------|
|[Vorschlagen und Verwenden von Suchbegriffen](concepts/search-for-news.md#suggest-and-use-search-terms)     | Verwenden Sie die [Bing-Vorschlagssuche-API](../bing-autosuggest/get-suggested-search-terms.md), um während der Eingabe Suchvorschläge anzuzeigen und die Benutzerfreundlichkeit Ihrer Suchumgebung zu verbessern.         |
|[Abrufen allgemeiner Nachrichten](concepts/search-for-news.md#get-general-news)     | Senden Sie eine Suchabfrage an die Bing-News-Suche-API, um eine Liste relevanter Nachrichtenartikel abzurufen.           |
|[Top-Nachrichten von heute](concepts/search-for-news.md#get-todays-top-news)      | Erhalten Sie die Top-Nachrichtenartikel des Tages aus allen Kategorien.       |
|[Nachrichten nach Kategorie](concepts/search-for-news.md)     | Suchen Sie nach Nachrichten in bestimmten Kategorien.        | 
|[Schlagzeilen](concepts/search-for-news.md)     | Suchen Sie nach Schlagzeilen in allen Kategorien.         |

## <a name="workflow"></a>Workflow

Die Bing-News-Suche-API ist ein RESTful-Webdienst und kann somit problemlos in jeder Programmiersprache aufgerufen werden, die HTTP-Anforderungen beherrscht und JSON analysieren kann. Der Dienst kann entweder über die REST-API oder über das SDK verwendet werden.

1. Erstellen Sie ein Cognitive Services-API-Konto mit Zugriff auf die Bing-Suche-APIs. Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-news-api).

2. Senden Sie eine Anforderung mit einer gültigen Suchabfrage an die API.

3. Analysieren Sie die zurückgegebene JSON-Nachricht, um die API-Antwort zu verarbeiten.

## <a name="next-steps"></a>Nächste Schritte

Testen Sie zuerst die [interaktive Demo](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) für die Bing-News-Suche-API. Diese Demo zeigt, wie Sie schnell eine Suchabfrage anpassen und Nachrichten im Web finden.

Nutzen Sie einen Schnellstart für die [REST-API](quickstart.md) oder eines der [SDKs](sdk.md), um schnell mit Ihrer ersten API-Anforderung zu beginnen.

## <a name="see-also"></a>Weitere Informationen

* Der Referenzabschnitt für die [Bing-News-Suche-API (Version 7)](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) enthält Definitionen und Informationen zu den Endpunkten, Headern, API-Antworten und Abfrageparametern, die Sie verwenden können, um bildbasierte Suchergebnisse anzufordern.

* In den [Verwendungs- und Anzeigeanforderungen für Bing](./useanddisplayrequirements.md) erfahren Sie, wie Inhalte und Informationen verwendet werden dürfen, die über die Bing-Suche-APIs gefunden wurden.
