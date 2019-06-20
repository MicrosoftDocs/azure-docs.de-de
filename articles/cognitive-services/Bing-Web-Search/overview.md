---
title: Was ist die Bing-Websuche-API?
titleSuffix: Azure Cognitive Services
description: Die Bing-Websuche-API ist ein RESTful-Dienst, der umgehend Antworten auf Benutzerabfragen liefert. Suchergebnisse lassen sich einfach konfigurieren und können unter anderem Webseiten, Bilder, Videos, Nachrichten und Übersetzungen enthalten. Ergebnisse werden im JSON-Format sowie auf der Grundlage der Suchrelevanz und Ihrer Bing-Websuche-Abonnements zurückgegeben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 03/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 65e28b557a3708c155bf80f21a784374c4497783
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383033"
---
# <a name="what-is-the-bing-web-search-api"></a>Was ist die Bing-Websuche-API?

Die Bing-Websuche-API ist ein RESTful-Dienst, der umgehend Antworten auf Benutzerabfragen liefert. Suchergebnisse lassen sich einfach konfigurieren und können unter anderem Webseiten, Bilder, Videos, Nachrichten und Übersetzungen enthalten. Die Ergebnisse werden von der Bing-Websuche im JSON-Format auf der Grundlage der Suchrelevanz und Ihrer Bing-Websuche-Abonnements zurückgegeben.

Diese API eignet sich perfekt für Anwendungen, die Zugriff auf alle Inhalte benötigen, die für die Suchabfrage eines Benutzers relevant sind. Wenn Sie eine Anwendung erstellen, die nur Ergebnisse eines bestimmten Typs erfordert, empfiehlt sich unter Umständen die Verwendung der [Bing-Bildersuche-API](../Bing-Image-Search/overview.md), der [Bing-Videosuche-API](../Bing-Video-Search/search-the-web.md) oder der [Bing News-Suche-API](../Bing-News-Search/search-the-web.md). Eine vollständige Liste der Bing-Suche-APIs finden Sie unter [Cognitive Services-APIs](https://docs.microsoft.com/azure/cognitive-services).

Neugierig geworden? Sehen Sie sich unsere [Demo der Bing-Websuche-API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) an.

## <a name="features"></a>Features  

Die Bing-Websuche bietet nicht nur Zugang zu sofortigen Antworten. Sie bietet auch Features und Funktionen, mit denen Sie Suchergebnisse für Ihre Benutzer anpassen können.

| Feature | BESCHREIBUNG |
|---------|-------------|
| [In Echtzeit vorgeschlagene Suchbegriffe](../bing-autosuggest/get-suggested-search-terms.md) | Verwenden Sie die Bing-Vorschlagssuche-API, um während der Eingabe Suchvorschläge anzuzeigen und die Benutzerfreundlichkeit Ihrer Anwendung zu verbessern. |
| [Filtern und Einschränken von Ergebnissen nach Inhaltstyp](filter-answers.md) | Verwenden Sie Filter und Abfrageparameter für Webseiten, Bilder, Videos, sichere Suche und mehr, um Suchergebnisse anzupassen und einzugrenzen. |
| [Treffermarkierung für Unicode-Zeichen](hit-highlighting.md) | Identifizieren und entfernen Sie mithilfe der Treffermarkierung unerwünschte Unicode-Zeichen aus Suchergebnissen, bevor sie Benutzern angezeigt werden. |
| [Lokalisieren von Suchergebnissen nach Land, Region und/oder Markt](supported-countries-markets.md) | Die Bing-Websuche unterstützt mehr als drei Dutzend Länder/Regionen. Verwenden Sie dieses Feature, um die Suchergebnisse für ein bestimmtes Land/eine bestimmte Region oder für einen bestimmten Markt einzugrenzen. |
| [Analysieren von Suchmetriken mittels Bing-Statistik](bing-web-stats.md) | Die Bing-Statistik ist ein kostenpflichtiges Abonnement mit Analysen für das Aufrufvolumen, die häufigsten Abfragezeichenfolgen, die geografische Verteilung und vieles mehr. |

## <a name="workflow"></a>Workflow

Die Bing-Websuche-API kann problemlos in jeder Programmiersprache aufgerufen werden, die HTTP-Anforderungen beherrscht und JSON-Antworten analysieren kann. Auf den Dienst kann über die [REST-API](quickstarts/python.md) oder unter Verwendung der [Bing-Websuche-SDKs](web-sdk-python-quickstart.md) zugegriffen werden.

1. Erstellen Sie ein [Cognitive Services-API-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) mit Zugriff auf die Bing-Suche-APIs. Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) erstellen.  
2. Senden Sie eine [Anforderung an die Bing-Websuche-API](quickstarts/python.md).
3. Analysieren Sie die JSON-Antwort.

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie unsere [Python-Schnellstartanleitung](quickstarts/python.md), um die Bing-Websuche-API zum ersten Mal aufzurufen.  
* [Erstellen Sie eine Web-App mit einer einzelnen Seite.](tutorial-bing-web-search-single-page-app.md)
* Sehen Sie sich die [Referenzdokumentation für die Websuche-API (Version 7)](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) an.  
* Informieren Sie sich über die [Verwendungs- und Anzeigeanforderungen](UseAndDisplayRequirements.md) für die Bing-Websuche.  
