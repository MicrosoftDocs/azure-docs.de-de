---
title: Worum handelt es sich bei der Bing-Such-API für ortsansässige Unternehmen? | Microsoft-Dokumentation
titleSuffix: Azure Cognitive Services
description: Die Bing-Such-API für ortsansässige Unternehmen ist ein RESTful-Dienst, mit dem Ihre Anwendungen basierend auf Suchabfragen Informationen zu Orten und Unternehmen in der näheren Umgebung suchen können.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 3cc9ed4dd108e76da6430a450876b709be514356
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796767"
---
# <a name="what-is-bing-local-business-search"></a>Worum handelt es sich bei der API für die Bing-Suche nach ortsansässigen Unternehmen?
Die API für die Bing-Suche nach ortsansässigen Unternehmen ist ein RESTful-Dienst, mit dem Ihre Anwendungen basierend auf Suchabfragen Informationen zu Unternehmen in der näheren Umgebung suchen können. Beispiel: `q=<business-name> in Redmond, Washington` oder `q=Italian restaurants near me`. 

## <a name="features"></a>Features
| Feature | BESCHREIBUNG |  
| -- | -- | 
| [Unternehmen und Orte in der näheren Umgebung suchen](quickstarts/local-quickstart.md) | Die API für die Bing-Suche nach ortsansässigen Unternehmen ruft lokalisierte Ergebnisse aus einer Abfrage ab. Die Ergebnisse enthalten eine URL der Website des Unternehmens und zeigen Text, die Telefonnummer und den geografischen Standort an, einschließlich GPS-Koordinaten, Stadt, Straße und Hausnummer. |  
| [Lokale Ergebnisse mit geografischen Grenzen filtern](specify-geographic-search.md) | Fügen Sie Koordinaten als Suchparameter hinzu, um Ergebnisse auf einen bestimmten geografischen Bereich zu beschränken, der durch eine runde Fläche oder einen quadratischen Begrenzungsrahmen angegeben wird. | 
| [Ergebnisse für ortsansässige Unternehmen nach Kategorie filtern](local-categories.md) | Suchen Sie mit Kategorien nach Ergebnissen für ortsansässige Unternehmen. Diese Option verwendet den umgekehrten IP-Standort oder GPS-Koordinaten des Aufrufers, um lokalisierte Ergebnisse in verschiedenen Unternehmenskategorien zurückzugeben.|

## <a name="workflow"></a>Workflow
Rufen Sie die API für die Bing-Suche nach ortsansässigen Unternehmen in einer beliebigen Programmiersprache auf, die HTTP-Anforderungen erstellen und JSON-Antworten analysieren kann. Der Zugriff auf diesen Dienst ist über die REST-API möglich.
 
1. Erstellen Sie ein [Cognitive Services-API-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) mit Zugriff auf die Bing-Suche-APIs. Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) erstellen.   
2. Versehen Sie Ihre Suchbegriffe für den Abfrageparameter `q=""` mit URL-Codierung. Beispiel: `q=nearby+restaurant` oder `q=nearby%20restaurant`. Legen Sie bei Bedarf auch die Paginierung fest. 
3. Senden Sie eine [Anforderung an die API für die Bing-Suche nach ortsansässigen Unternehmen](quickstarts/local-quickstart.md). 
4. Analysieren der JSON-Antwort 

> [!NOTE]
> Derzeit unterstützt die Suche nach ortsansässigen Unternehmen nur den Markt `en-US`. 
> [!NOTE]
> Derzeit unterstützt die Suche nach ortsansässigen Unternehmen keine Vorschlagssuche. 

## <a name="next-steps"></a>Nächste Schritte
- [Abfrage und Antwort](local-search-query-response.md)
- [Schnellstart: Suche nach ortsansässigen Unternehmen](quickstarts/local-quickstart.md)
- [Referenz zur API für die Suche nach ortsansässigen Unternehmen](local-search-reference.md)
- [Verwenden und Anzeigen von Anforderungen](use-display-requirements.md)
