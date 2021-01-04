---
title: Worum handelt es sich bei der Bing-Videosuche-API?
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie mithilfe der Bing-Videosuche-API nach Videos im Internet suchen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: d5a4c3110186329516f10465c5e80a10b0ceb7b7
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94379819"
---
# <a name="what-is-the-bing-video-search-api"></a>Worum handelt es sich bei der Bing-Videosuche-API?

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Über die Bing-Videosuche-API lassen sich Videosuchfunktionen einfach zu Ihren Diensten und Anwendungen hinzufügen. Sie können Suchabfragen von Benutzern über die API senden und ähnlich wie bei [Bing Video](https://www.bing.com/video) relevante Videos in hoher Qualität abrufen und anzeigen. Verwenden Sie diese API für Suchergebnisse, die nur Videos enthalten. Mit der [Bing-Websuche-API](../bing-web-search/overview.md) können andere Arten von Webinhalten zurückgegeben werden, etwa Webseiten, Videos, Nachrichten und Bilder.

## <a name="bing-video-search-api-features"></a>Features der Bing-Videosuche-API

| Funktion                                                                                                                                                                                 | BESCHREIBUNG                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [In Echtzeit vorgeschlagene Suchbegriffe](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | Verwenden Sie die [Bing-Vorschlagssuche-API](../bing-autosuggest/get-suggested-search-terms.md), um während der Eingabe Suchvorschläge anzuzeigen und die Benutzerfreundlichkeit Ihrer App zu verbessern. |
| [Filtern und Einschränken von Videoergebnissen](concepts/get-videos.md#filtering-videos)                      | Filtern Sie durch die Bearbeitung von Abfrageparametern die zurückgegebenen Videos.                                                                                                       |
| [Zuschneiden, Ändern der Größe und Anzeigen Miniaturansichten](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Für die von der Bing-Videosuche-API zurückgegebenen Videos können Miniaturansichten als Vorschau bearbeitet und angezeigt werden.                                                                                      |
| [Abrufen von beliebten Videos](trending-videos.md) | Suchen Sie nach beliebten Videos aus der ganzen Welt.                                                                                                          |
| [Gewinnen von Erkenntnissen aus Videos](video-insights.md) | Richten Sie eine Suche nach beliebten Videos aus der ganzen Welt ein.                                                                                                          |

## <a name="workflow"></a>Workflow

Die Bing-Videosuche-API ist ein RESTful-Webdienst und kann somit problemlos in jeder Programmiersprache aufgerufen werden, die HTTP-Anforderungen beherrscht und JSON analysieren kann. Der Dienst kann entweder über die [REST-API](./quickstarts/csharp.md) oder über das [SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) verwendet werden.

1. Erstellen Sie ein [Cognitive Services-API-Konto](../cognitive-services-apis-create-account.md) mit Zugriff auf die Bing-Suche-APIs. Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses [Konto erstellen](https://azure.microsoft.com/free/cognitive-services/).
2. Senden Sie eine Anforderung mit einer gültigen Suchabfrage an die API.
3. Analysieren Sie die zurückgegebene JSON-Nachricht, um die API-Antwort zu verarbeiten.


## <a name="next-steps"></a>Nächste Schritte

In der [interaktiven Demo](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) zur Bing-Videosuche-API wird veranschaulicht, wie Sie eine Suchabfrage anpassen und im Web nach Videos suchen können.

Sehen Sie sich die [Schnellstartanleitung](./quickstarts/csharp.md) an, um schnell mit Ihrer ersten API-Anforderung zu beginnen.

## <a name="see-also"></a>Weitere Informationen

* Die Referenzseite zur [ Bing-Videosuche-API v7](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) enthält die Liste mit den Endpunkten, Headern und Abfrageparametern zum Anfordern von Suchergebnissen.

* In den [Verwendungs- und Anzeigeanforderungen für Bing](../bing-web-search/use-display-requirements.md) erfahren Sie, wie Inhalte und Informationen verwendet werden dürfen, die über die Bing-Suche-APIs gefunden wurden.

* Besuchen Sie die Seite [Was ist die Bing-Websuche-API?](../bing-web-search/overview.md), um die anderen verfügbaren APIs zu untersuchen.