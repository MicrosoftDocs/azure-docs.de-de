---
title: Worum handelt es sich bei der API für die benutzerdefinierte Bing-Suche?
titleSuffix: Azure Cognitive Services
description: Die API für die benutzerdefinierte Bing-Suche ermöglicht das Erstellen einer maßgeschneiderten Suchbenutzeroberfläche für Themen, die Sie interessieren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: e70d4d83fcd9641fa86f013688b5a3a6e3652919
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338400"
---
# <a name="what-is-the-bing-custom-search-api"></a>Worum handelt es sich bei der API für die benutzerdefinierte Bing-Suche?

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Die API für die benutzerdefinierte Bing-Suche ermöglicht das Erstellen einer maßgeschneiderten werbefreien Suchbenutzeroberfläche für Themen, die Sie interessieren. Sie können angeben, welche Domänen und Webseiten Bing durchsuchen soll, sowie bestimmte Inhalte anheften, verstärken oder tiefer stufen, um eine benutzerdefinierte Ansicht des Webs zu erhalten und Ihren Benutzern schnell zu relevanten Suchergebnissen zu verhelfen. 

## <a name="features"></a>Features

|Funktion  |BESCHREIBUNG  |
|---------|---------|
|[Benutzerdefinierte Suchvorschläge in Echtzeit](define-custom-suggestions.md)     | Stellen Sie Suchvorschläge bereit, die wie eine Dropdownliste angezeigt werden können, während der Benutzer etwas eingibt.       | 
|[Benutzerdefinierte Bildersuche](get-images-from-instance.md)     | Ermöglichen Sie Benutzern die Suche nach Bildern aus den Domänen und Websites, die in Ihrer Instanz für die benutzerdefinierte Suche angegeben sind.        |        
|[Benutzerdefinierte Videosuche](get-videos-from-instance.md)     | Ermöglichen Sie Benutzern die Suche nach Videos aus den Domänen und Websites, die in Ihrer Instanz für die benutzerdefinierte Suche angegeben sind.        |    
|[Freigeben der Instanz für die benutzerdefinierte Suche](share-your-custom-search.md)     | Geben Sie Ihre Suchinstanz für Mitglieder Ihres Teams frei, um sie gemeinsam bearbeiten und testen zu können.        | 
|[Konfigurieren einer Benutzeroberfläche für Ihre Anwendungen und Websites](hosted-ui.md)     | Bietet eine gehostete Benutzeroberfläche, die Sie problemlos als JavaScript-Codeausschnitt in Ihre Webseiten und -anwendungen integrieren können.        | 
## <a name="workflow"></a>Workflow

Über das [Portal für die benutzerdefinierte Bing-Suche](https://customsearch.ai) können Sie eine angepasste Suchinstanz erstellen. Mithilfe des Portals können Sie eine Instanz für die benutzerdefinierte Suche erstellen, die angibt, welche Domänen, Websites und Webseiten von Bing durchsucht werden sollen (und welche nicht). Darüber hinaus können Sie im Portal eine Vorschau der Suchumgebung anzeigen, die von der API bereitgestellten Suchrangfolgen anpassen und optional eine durchsuchbare Benutzeroberfläche konfigurieren, die auf Ihren Websites und in Ihren Anwendungen gerendert wird.

Die erstellte Suchinstanz (und optional eine Benutzeroberfläche) können Sie dann in Ihre Website oder Anwendung integrieren, indem Sie die API für die benutzerdefinierte Bing-Suche aufrufen:

![Abbildung: Herstellen einer Verbindung mit einer benutzerdefinierten Bing-Suche über die API](media/BCS-Overview.png "Funktionsweise der benutzerdefinierten Bing-Suche.")


## <a name="next-steps"></a>Nächste Schritte

Informationen zum schnellen Einstieg finden Sie unter [Erstellen Ihrer ersten Instanz der benutzerdefinierten Bing-Suche](quick-start.md).

Weitere Informationen zum Anpassen Ihrer Suchinstanz finden Sie unter [Konfigurieren der Benutzeroberfläche für die benutzerdefinierte Suche](define-your-custom-view.md).

Informieren Sie sich unter [Anforderungen für die Verwendung und Anzeige der Bing-Suche-API](../bing-web-search/use-display-requirements.md) über die Verwendung von Suchergebnissen in Ihren Diensten und Anwendungen.

Besuchen Sie die Seite [Was ist die Bing-Websuche-API?](../bing-web-search/overview.md), um die anderen verfügbaren APIs zu untersuchen.

Machen Sie sich mit dem Referenzinhalt für die einzelnen Endpunkte für die benutzerdefinierte Suche vertraut. Die Referenz enthält die Endpunkte, Header und Abfrageparameter, die Sie zum Anfordern von Suchergebnissen verwenden können. Darüber hinaus finden Sie dort Definitionen der Antwortobjekte.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

- [API für die benutzerdefinierte Suche](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [API für die benutzerdefinierte Bildersuche](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [API für die benutzerdefinierte Videosuche](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [API für die benutzerdefinierte Vorschlagssuche](/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)