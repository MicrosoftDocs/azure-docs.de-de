---
title: Worum handelt es sich bei der Bing-Vorschlagssuche?
titlesuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie4 Sie die Bing-Vorschlagssuche-API verwenden.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 24f35d795b34e7d9c214a23c040791841b4a711b
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66382571"
---
# <a name="what-is-bing-autosuggest"></a>Worum handelt es sich bei der Bing-Vorschlagssuche?

Wenn Ihre Anwendung Abfragen an eine der Bing-Suche-APIs senden, können Sie die Bing-Vorschlagssuche-API verwenden, um die Suchumgebung des Benutzers zu verbessern. Die Bing-Vorschlagssuche-API gibt eine Liste vorgeschlagener Abfragen basierend auf der unvollständigen Abfragezeichenfolge im Suchfeld zurück. Bei der Eingabe von Zeichen in das Suchfeld können Sie Vorschläge in einer Dropdownliste anzeigen.

## <a name="bing-autosuggest-api-features"></a>Funktionen der Bing-Vorschlagssuche-API

| Feature                                                                                                                                                                                 | BESCHREIBUNG                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [In Echtzeit vorgeschlagene Suchbegriffe](concepts/get-suggestions.md) | Verwenden Sie die Vorschlagssuche-API, um während der Eingabe Suchvorschläge anzuzeigen und die Benutzerfreundlichkeit Ihrer App zu verbessern. |

## <a name="workflow"></a>Workflow

Die Bing-Vorschlagssuche-API ist ein RESTful-Webdienst und kann somit problemlos in jeder Programmiersprache aufgerufen werden, die HTTP-Anforderungen beherrscht und JSON analysieren kann. 

1. Erstellen Sie ein [Cognitive Services-API-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) mit Zugriff auf die Bing-Suche-APIs. Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses [Konto erstellen](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Senden Sie jedes Mal eine Anforderung an diese API, wenn ein Benutzer ein neues Zeichen in das Suchfeld Ihrer Anwendung eingibt.
3. Analysieren Sie die zurückgegebene JSON-Nachricht, um die API-Antwort zu verarbeiten.

In der Regel rufen Sie diese API jedes Mal auf, wenn ein Benutzer ein neues Zeichen in das Suchfeld Ihrer Anwendung eingibt. Je mehr Zeichen eingegeben werden, desto relevantere Suchvorschläge gibt die API zurück. Beispielsweise sind die Vorschläge, die die API unter Umständen für ein einfaches `s` zurückgibt, wahrscheinlich weniger relevant als die für `sail`.

Das folgende Beispiel zeigt ein Dropdownsuchfeld mit vorgeschlagenen Suchbegriffen der Bing-Vorschlagssuche-API.

![Dropdownliste des Suchfelds der Vorschlagssuche](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Wenn ein Benutzer einen Vorschlag aus der Dropdownliste auswählt, können Sie damit und mit einer der Bing-Suche-APIs eine Suche starten oder direkt zur Seite mit den Bing-Suchergebnissen gehen.

## <a name="next-steps"></a>Nächste Schritte

Der Artikel [Erstellen Ihrer ersten Anforderung](quickstarts/csharp.md) ermöglicht einen schnellen Einstieg in die Verwendung Ihrer ersten Abfrage.

Machen Sie sich mit der Referenz für die [Bing-Vorschlagssuche-API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) vertraut. Die Referenz enthält die Liste mit den Endpunkten, Headern und Abfrageparametern, die Sie zum Anfordern von vorgeschlagenen Abfragebegriffen verwenden können, sowie die Definitionen der Antwortobjekte.

Hier erfahren Sie, wie Sie das Internet unter Verwendung der [Bing-Websuche-API](../bing-web-search/search-the-web.md) durchsuchen:

Machen Sie sich vorher unbedingt mit den [Verwendungs- und Anzeigeanforderungen von Bing](./useanddisplayrequirements.md) vertraut, um nicht gegen die Regeln für die Verwendung der Suchergebnisse zu verstoßen.
