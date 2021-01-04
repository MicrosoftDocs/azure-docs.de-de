---
title: Worum handelt es sich bei der Bing-Such-API für ortsansässige Unternehmen?
titleSuffix: Azure Cognitive Services
description: Die Bing-Such-API für ortsansässige Unternehmen ist ein RESTful-Dienst, mit dem Ihre Anwendungen basierend auf Suchabfragen Informationen zu Orten und Unternehmen in der näheren Umgebung suchen können.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: overview
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 7a5829623707797b98593f837d6cadf009410f31
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487013"
---
# <a name="what-is-bing-local-business-search"></a>Worum handelt es sich bei der API für die Bing-Suche nach ortsansässigen Unternehmen?

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
Die API für die Bing-Suche nach ortsansässigen Unternehmen ist ein RESTful-Dienst, mit dem Ihre Anwendungen basierend auf Suchabfragen Informationen zu Unternehmen in der näheren Umgebung suchen können. Beispiel: `q=<business-name> in Redmond, Washington` oder `q=Italian restaurants near me`. 

## <a name="features"></a>Features
| Funktion | BESCHREIBUNG |  
| -- | -- | 
| [Unternehmen und Orte in der näheren Umgebung suchen](quickstarts/local-quickstart.md) | Die API für die Bing-Suche nach ortsansässigen Unternehmen ruft lokalisierte Ergebnisse aus einer Abfrage ab. Die Ergebnisse enthalten eine URL der Website des Unternehmens und zeigen Text, die Telefonnummer und den geografischen Standort an, einschließlich GPS-Koordinaten, Stadt, Straße und Hausnummer. |  
| [Lokale Ergebnisse mit geografischen Grenzen filtern](specify-geographic-search.md) | Fügen Sie Koordinaten als Suchparameter hinzu, um Ergebnisse auf einen bestimmten geografischen Bereich zu beschränken, der durch eine runde Fläche oder einen quadratischen Begrenzungsrahmen angegeben wird. | 
| [Ergebnisse für ortsansässige Unternehmen nach Kategorie filtern](local-categories.md) | Suchen Sie mit Kategorien nach Ergebnissen für ortsansässige Unternehmen. Diese Option verwendet den umgekehrten IP-Standort oder GPS-Koordinaten des Aufrufers, um lokalisierte Ergebnisse in verschiedenen Unternehmenskategorien zurückzugeben.|

## <a name="workflow"></a>Workflow
Rufen Sie die API für die Bing-Suche nach ortsansässigen Unternehmen in einer beliebigen Programmiersprache auf, die HTTP-Anforderungen erstellen und JSON-Antworten analysieren kann. Der Zugriff auf diesen Dienst ist über die REST-API möglich.
 
1. Erstellen Sie ein [Cognitive Services-API-Konto](../cognitive-services-apis-create-account.md) mit Zugriff auf die Bing-Suche-APIs. Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen.   
2. Versehen Sie Ihre Suchbegriffe für den Abfrageparameter `q=""` mit URL-Codierung. Zum Beispiel: `q=nearby+restaurant` oder `q=nearby%20restaurant`. Legen Sie bei Bedarf auch die Paginierung fest. 
3. Senden Sie eine [Anforderung an die API für die Bing-Suche nach ortsansässigen Unternehmen](quickstarts/local-quickstart.md). 
4. Analysieren der JSON-Antwort 

> [!NOTE]
> Für die Suche für ortsansässige Unternehmen gilt derzeit Folgendes: 
> * Sie unterstützt nur den Markt `en-US`. 
> * Sie unterstützt nicht die Bing-Vorschlagssuche. 

## <a name="next-steps"></a>Nächste Schritte
- [Abfrage und Antwort](local-search-query-response.md)
- [Schnellstart: Suche nach ortsansässigen Unternehmen](quickstarts/local-quickstart.md)
- [Referenz zur API für die Suche nach ortsansässigen Unternehmen](local-search-reference.md)
- [Verwenden und Anzeigen von Anforderungen](../bing-web-search/use-display-requirements.md)