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
ms.date: 12/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: c52098d86efe60ee524735e6158add5260a0757f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338230"
---
# <a name="what-is-the-bing-image-search-api"></a>Was ist die Bing-Bildersuche-API?

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Die Bing-Bildersuche-API ermöglicht die Verwendung der Bildersuchfunktionen von Bing in Ihrer Anwendung. Sie können Suchabfragen an die API senden, um ähnlich wie bei [bing.com/images](https://www.bing.com/images) Bilder in hoher Qualität zu erhalten.

Die Bing-Bildersuche-API gibt als Suchergebnisse ausschließlich Bilder zurück. Für die Suche nach anderen Arten von Webinhalten können Sie die anderen verfügbaren [Bing-Suche-APIs](../bing-web-search/bing-api-comparison.md) verwenden oder sie mit der Bing-Bildersuche-API kombinieren.

## <a name="bing-image-search-features"></a>Features der Bing-Bildersuche

| Funktion                                                                                                                                                                                 | BESCHREIBUNG                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [In Echtzeit vorgeschlagene Suchbegriffe](./concepts/bing-image-search-sending-queries.md) | Verwenden Sie die [Bing-Vorschlagssuche-API](../bing-autosuggest/get-suggested-search-terms.md), um während der Eingabe Suchvorschläge anzuzeigen und die Benutzerfreundlichkeit Ihrer App zu verbessern. |
| [Filtern und Einschränken von Bildergebnissen](./concepts/bing-image-search-get-images.md)                       | Bearbeiten Sie Abfrageparameter, um die von Bing zurückgegebenen Bilder zu filtern.                                                                                                       |
| [Zuschneiden, Ändern der Größe und Anzeigen Miniaturansichten](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Für die von der Bing-Bildersuche zurückgegebenen Bilder können Miniaturansichten als Vorschau bearbeitet und angezeigt werden.                                                                                      |
| [Pivotieren und Erweitern der Suchabfragen von Benutzern](./concepts/bing-image-search-sending-queries.md)               | Erweitern Sie Ihre Suchfunktionen, indem Sie von Bing vorgeschlagene Suchbegriffe in Abfragen einschließen und anzeigen.                                                                    |
| [Abrufen von beliebten Bildern](trending-images.md)                                                                     | Richten Sie eine Suche nach beliebten Bildern aus der ganzen Welt ein.                                                                                                          |

## <a name="workflow"></a>Workflow

Die Bing-Bildersuche-API ist ein RESTful-Webdienst und kann somit problemlos in jeder Programmiersprache aufgerufen werden, die HTTP-Anforderungen beherrscht und JSON analysieren kann. Der Dienst kann entweder über die [REST-API](./quickstarts/csharp.md) oder über das [SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) verwendet werden.

1. Erstellen Sie ein [Cognitive Services-API-Konto](../cognitive-services-apis-create-account.md) mit Zugriff auf die Bing-Suche-APIs. Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses [Konto erstellen](https://azure.microsoft.com/free/cognitive-services/).
2. Senden Sie eine Anforderung mit einer gültigen [Suchabfrage](./concepts/bing-image-search-sending-queries.md) an die API.
3. Analysieren Sie die zurückgegebene JSON-Nachricht, um die API-Antwort zu verarbeiten.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich zunächst die [interaktive Demo](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) für die Bing-Bildersuche-API an.
Diese Demo zeigt, wie Sie schnell eine Suchabfrage anpassen und das Web nach Bildern durchsuchen.

Um schnell mit Ihrer ersten API-Anforderung zu beginnen, können Sie sich mit Folgendem vertraut machen:

* [Senden von Suchabfragen an Bing](./quickstarts/csharp.md) mithilfe der REST-API. Oder:
* [Anfordern und Filtern](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) der von Bing zurückgegebenen Bilder mithilfe des SDK.

## <a name="see-also"></a>Weitere Informationen

* [Details zu den Preisen](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) für die Bing-Suche-APIs 

* Der Abschnitt [Image Search API v7 reference](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) (Referenz für die Bing-Bildersuche-API v7) enthält Informationen zu den Endpunkten, Headern, API-Antworten und Abfrageparametern der API.

* In den [Verwendungs- und Anzeigeanforderungen für Bing](../bing-web-search/use-display-requirements.md) erfahren Sie, wie Inhalte und Informationen verwendet werden dürfen, die über die Bing-Suche-APIs gefunden wurden.

* Im Artikel [Abrufen von Bildern aus dem Web mit der Bing-Bildersuche-API](./concepts/bing-image-search-get-images.md) erfahren Sie, wie Bilder im Web gesucht und abgerufen werden.

* Der Artikel [Senden von Abfragen an die Bing-Bildersuche-API](./concepts/bing-image-search-sending-queries.md) enthält Informationen zum Ausführen, Anpassen und Pivotieren von Suchabfragen.

* Besuchen Sie die Seite [Was ist die Bing-Websuche-API?](../bing-web-search/overview.md), um die anderen verfügbaren APIs zu untersuchen.