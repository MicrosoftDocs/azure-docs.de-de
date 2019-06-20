---
title: Was ist die Bing-Bildersuche-API?
titleSuffix: Azure Cognitive Services
description: Die Bing-Bildersuche-API ermöglicht die Verwendung kognitiver Bildersuchfunktionen in Ihrer Anwendung. Sie können Suchabfragen von Benutzern über die API senden und ähnlich wie bei der Bing-Bildersuche relevante Bilder in hoher Qualität abrufen und anzeigen.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 02/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: c8213e7d725c7c06d40555176fff24be4cff7607
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388672"
---
# <a name="what-is-the-bing-image-search-api"></a>Was ist die Bing-Bildersuche-API?

Die Bing-Bildersuche-API ermöglicht die Verwendung der Bildersuchfunktionen von Bing in Ihrer Anwendung. Sie können Suchabfragen an die API senden, um ähnlich wie bei [bing.com/images](https://www.bing.com/images) Bilder in hoher Qualität zu erhalten.

Die Bing-Bildersuche-API gibt als Suchergebnisse ausschließlich Bilder zurück. Für die Suche nach anderen Arten von Webinhalten können Sie die anderen verfügbaren [Bing-Suche-APIs](../bing-web-search/bing-api-comparison.md) verwenden oder sie mit der Bing-Bildersuche-API kombinieren.

## <a name="bing-image-search-features"></a>Features der Bing-Bildersuche

| Feature                                                                                                                                                                                 | BESCHREIBUNG                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [In Echtzeit vorgeschlagene Suchbegriffe](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | Verwenden Sie die [Bing-Vorschlagssuche-API](../bing-autosuggest/get-suggested-search-terms.md), um während der Eingabe Suchvorschläge anzuzeigen und die Benutzerfreundlichkeit Ihrer App zu verbessern. |
| [Filtern und Einschränken von Bildergebnissen](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images)                       | Bearbeiten Sie Abfrageparameter, um die von Bing zurückgegebenen Bilder zu filtern.                                                                                                       |
| [Zuschneiden, Ändern der Größe und Anzeigen Miniaturansichten](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/resize-and-crop-thumbnails)                                                | Für die von der Bing-Bildersuche zurückgegebenen Bilder können Miniaturansichten als Vorschau bearbeitet und angezeigt werden.                                                                                      |
| [Pivotieren und Erweitern der Suchabfragen von Benutzern](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)               | Erweitern Sie Ihre Suchfunktionen, indem Sie von Bing vorgeschlagene Suchbegriffe in Abfragen einschließen und anzeigen.                                                                    |
| [Abrufen von beliebten Bildern](https://review.docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)                                                                     | Richten Sie eine Suche nach beliebten Bildern aus der ganzen Welt ein.                                                                                                          |

## <a name="workflow"></a>Workflow

Die Bing-Bildersuche-API ist ein RESTful-Webdienst und kann somit problemlos in jeder Programmiersprache aufgerufen werden, die HTTP-Anforderungen beherrscht und JSON analysieren kann. Der Dienst kann entweder über die [REST-API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?) oder über das [SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) verwendet werden.

1. Erstellen Sie ein [Cognitive Services-API-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) mit Zugriff auf die Bing-Suche-APIs. Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses [Konto erstellen](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Senden Sie eine Anforderung mit einer gültigen [Suchabfrage](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) an die API.
3. Analysieren Sie die zurückgegebene JSON-Nachricht, um die API-Antwort zu verarbeiten.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich zunächst die [interaktive Demo](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) für die Bing-Bildersuche-API an.
Diese Demo zeigt, wie Sie schnell eine Suchabfrage anpassen und das Web nach Bildern durchsuchen.

Wenn Sie zum Aufrufen der API bereit sind, erstellen Sie ein [Cognitive Services-API-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses [Konto erstellen](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Um schnell mit Ihrer ersten API-Anforderung zu beginnen, können Sie sich mit Folgendem vertraut machen:

* [Senden von Suchabfragen an Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp) mithilfe der REST-API. Oder:
* [Anfordern und Filtern](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) der von Bing zurückgegebenen Bilder mithilfe des SDK.

## <a name="see-also"></a>Weitere Informationen

* [Details zu den Preisen](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) für die Bing-Suche-APIs. 

* Der Abschnitt [Image Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) (Referenz für die Bing-Bildersuche-API v7) enthält Informationen zu den Endpunkten, Headern, API-Antworten und Abfrageparametern der API.

* In den [Verwendungs- und Anzeigeanforderungen für Bing](./useanddisplayrequirements.md) erfahren Sie, wie Inhalte und Informationen verwendet werden dürfen, die über die Bing-Suche-APIs gefunden wurden.

* Im Artikel [Abrufen von Bildern aus dem Web mit der Bing-Bildersuche-API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) erfahren Sie, wie Bilder im Web gesucht und abgerufen werden.

* Der Artikel [Senden von Abfragen an die Bing-Bildersuche-API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) enthält Informationen zum Ausführen, Anpassen und Pivotieren von Suchabfragen.
