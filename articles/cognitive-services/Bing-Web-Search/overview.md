---
title: Was ist die Bing-Websuche-API?
titleSuffix: Azure Cognitive Services
description: Die Bing-Websuche-API ist ein RESTful-Dienst, der umgehend Antworten auf Websuchabfragen liefert. Konfigurieren Sie die Ergebnisse so, dass sie Webseiten, Bilder, Videos, Neuigkeiten und vieles mehr enthalten. Ergebnisse werden im JSON-Format sowie auf der Grundlage der Suchrelevanz und Ihrer Bing-Websuche-Abonnements zurückgegeben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 03/31/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 7643486962df0516cc61857a7e31cf34bc41c732
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350635"
---
# <a name="what-is-the-bing-web-search-api"></a>Was ist die Bing-Websuche-API?

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Die Bing-Websuche-API ist ein RESTful-Dienst, der umgehend Antworten auf Benutzerabfragen liefert. Suchergebnisse lassen sich einfach konfigurieren und können unter anderem Webseiten, Bilder, Videos, Nachrichten und Übersetzungen enthalten. Die Ergebnisse werden von der Bing-Websuche im JSON-Format auf der Grundlage der Suchrelevanz und Ihrer Bing-Websuche-Abonnements zurückgegeben.

Diese API eignet sich perfekt für Anwendungen, die Zugriff auf alle Inhalte benötigen, die für die Suchabfrage eines Benutzers relevant sind. Wenn Sie eine Anwendung erstellen, die nur Ergebnisse eines bestimmten Typs erfordert, empfiehlt sich unter Umständen die Verwendung der [Bing-Bildersuche-API](../Bing-Image-Search/overview.md), der [Bing-Videosuche-API](../bing-video-search/overview.md) oder der [Bing News-Suche-API](../Bing-News-Search/search-the-web.md). Eine vollständige Liste der Bing-Suche-APIs finden Sie unter [Cognitive Services-APIs](../index.yml).

Neugierig geworden? Sehen Sie sich unsere [Demo der Bing-Websuche-API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) an.

## <a name="features"></a>Features  

Die Bing-Websuche bietet nicht nur Zugang zu sofortigen Antworten. Sie bietet auch Features und Funktionen, mit denen Sie Suchergebnisse für Ihre Benutzer anpassen können.

| Funktion | BESCHREIBUNG |
|---------|-------------|
| [In Echtzeit vorgeschlagene Suchbegriffe](../bing-autosuggest/get-suggested-search-terms.md) | Verwenden Sie die Bing-Vorschlagssuche-API, um während der Eingabe Suchvorschläge anzuzeigen und die Benutzerfreundlichkeit Ihrer Anwendung zu verbessern. |
| [Filtern und Einschränken von Ergebnissen nach Inhaltstyp](filter-answers.md) | Verwenden Sie Filter und Abfrageparameter für Webseiten, Bilder, Videos, sichere Suche und mehr, um Suchergebnisse anzupassen und einzugrenzen. |
| [Treffermarkierung für Unicode-Zeichen](hit-highlighting.md) | Identifizieren und entfernen Sie mithilfe der Treffermarkierung unerwünschte Unicode-Zeichen aus Suchergebnissen, bevor sie Benutzern angezeigt werden. |
| [Lokalisieren von Suchergebnissen nach Land, Region und/oder Markt](./language-support.md) | Die Bing-Websuche unterstützt mehr als drei Dutzend Länder/Regionen. Verwenden Sie dieses Feature, um die Suchergebnisse für ein bestimmtes Land/eine bestimmte Region oder für einen bestimmten Markt einzugrenzen. |
| [Analysieren von Suchmetriken mittels Bing-Statistik](bing-web-stats.md) | Die Bing-Statistik ist ein kostenpflichtiges Abonnement mit Analysen für das Aufrufvolumen, die häufigsten Abfragezeichenfolgen, die geografische Verteilung und vieles mehr. |

## <a name="workflow"></a>Workflow

Die Bing-Websuche-API kann problemlos in jeder Programmiersprache aufgerufen werden, die HTTP-Anforderungen beherrscht und JSON-Antworten analysieren kann. Auf den Dienst kann über die [REST-API](quickstarts/python.md) oder unter Verwendung der [Clientbibliotheken für die Bing-Websuche](./quickstarts/client-libraries.md) zugegriffen werden.

1. [Erstellen Sie eine Azure-Ressource](../cognitive-services-apis-create-account.md) für die Bing-Suche-APIs. Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen.  
2. Senden Sie eine [Anforderung an die Bing-Websuche-API](quickstarts/python.md).
3. Analysieren Sie die JSON-Antwort.

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie unsere [Python-Schnellstartanleitung](./quickstarts/client-libraries.md?pivots=programming-language-python), um die Bing-Websuche-API zum ersten Mal aufzurufen.  
* [Erstellen Sie eine Web-App mit einer einzelnen Seite.](tutorial-bing-web-search-single-page-app.md)
* Sehen Sie sich die [Referenzdokumentation für die Websuche-API (Version 7)](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) an.  
* Informieren Sie sich über die [Verwendungs- und Anzeigeanforderungen](./use-display-requirements.md) für die Bing-Websuche.