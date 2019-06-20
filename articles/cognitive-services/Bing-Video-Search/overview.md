---
title: Worum handelt es sich bei der Bing-Videosuche-API?
titlesuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie mithilfe der Bing-Videosuche-API nach Videos im Internet suchen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 2f6be01a3aee4e156b7af184fa2537455fc73f3f
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386532"
---
# <a name="what-is-the-bing-video-search-api"></a>Worum handelt es sich bei der Bing-Videosuche-API?

Über die Bing-Videosuche-API lassen sich Videosuchfunktionen einfach zu Ihren Diensten und Anwendungen hinzufügen. Sie können Suchabfragen von Benutzern über die API senden und ähnlich wie bei [Bing Video](https://www.bing.com/video) relevante Videos in hoher Qualität abrufen und anzeigen. Verwenden Sie diese API für Suchergebnisse, die nur Videos enthalten. Mit der [Bing-Websuche-API](../bing-web-search/search-the-web.md) können andere Arten von Webinhalten zurückgegeben werden, etwa Webseiten, Videos, Nachrichten und Bilder.

## <a name="bing-video-search-api-features"></a>Features der Bing-Videosuche-API

| Feature                                                                                                                                                                                 | BESCHREIBUNG                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [In Echtzeit vorgeschlagene Suchbegriffe](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | Verwenden Sie die [Bing-Vorschlagssuche-API](../bing-autosuggest/get-suggested-search-terms.md), um während der Eingabe Suchvorschläge anzuzeigen und die Benutzerfreundlichkeit Ihrer App zu verbessern. |
| [Filtern und Einschränken von Videoergebnissen](concepts/get-videos.md#filtering-videos)                      | Filtern Sie durch die Bearbeitung von Abfrageparametern die zurückgegebenen Videos.                                                                                                       |
| [Zuschneiden, Ändern der Größe und Anzeigen Miniaturansichten](resize-and-crop-thumbnails.md)                                                | Für die von der Bing-Videosuche-API zurückgegebenen Videos können Miniaturansichten als Vorschau bearbeitet und angezeigt werden.                                                                                      |
| [Abrufen von beliebten Videos](trending-videos.md) | Suchen Sie nach beliebten Videos aus der ganzen Welt.                                                                                                          |
| [Gewinnen von Erkenntnissen aus Videos](video-insights.md) | Richten Sie eine Suche nach beliebten Videos aus der ganzen Welt ein.                                                                                                          |

## <a name="workflow"></a>Workflow

Die Bing-Videosuche-API ist ein RESTful-Webdienst und kann somit problemlos in jeder Programmiersprache aufgerufen werden, die HTTP-Anforderungen beherrscht und JSON analysieren kann. Der Dienst kann entweder über die [REST-API](csharp.md) oder über das [SDK](video-search-sdk-quickstart.md) verwendet werden.

1. Erstellen Sie ein [Cognitive Services-API-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) mit Zugriff auf die Bing-Suche-APIs. Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses [Konto erstellen](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Senden Sie eine Anforderung mit einer gültigen Suchabfrage an die API.
3. Analysieren Sie die zurückgegebene JSON-Nachricht, um die API-Antwort zu verarbeiten.


## <a name="next-steps"></a>Nächste Schritte

In der [interaktiven Demo](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) zur Bing-Videosuche-API wird veranschaulicht, wie Sie eine Suchabfrage anpassen und im Web nach Videos suchen können.

Wenn Sie zum Aufrufen der API bereit sind, erstellen Sie ein [Cognitive Services-API-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses [Konto erstellen](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Sehen Sie sich die [Schnellstartanleitung](csharp.md) an, um schnell mit Ihrer ersten API-Anforderung zu beginnen.

## <a name="see-also"></a>Weitere Informationen

* Die Referenzseite zur [ Bing-Videosuche-API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) enthält die Liste mit den Endpunkten, Headern und Abfrageparametern zum Anfordern von Suchergebnissen.

* In den [Verwendungs- und Anzeigeanforderungen für Bing](./useanddisplayrequirements.md) erfahren Sie, wie Inhalte und Informationen verwendet werden dürfen, die über die Bing-Suche-APIs gefunden wurden.