---
title: 'Bing-Websuche-SDK: Beispiele'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie das SDK der Bing-Websuche, um Ihrer Python-, Node.js-, C#- oder Java-Anwendung Suchfunktionen hinzuzufügen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: sample
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: f13250bb2d6829cf7f1bf3dd0417974b810a36fe
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350431"
---
# <a name="bing-web-search-sdk-samples"></a>Bing-Websuche-SDK: Beispiele

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Das Bing-Websuche-SDK ist in Python, Node.js, C# und Java verfügbar. Codebeispiele, Voraussetzungen und Buildanweisungen werden in GitHub bereitgestellt. Folgende Szenarios werden abgedeckt:

* Abfragen eines einzelnen Worts und Ausgeben des Namens und der URL des ersten Ergebnisses für Webseiten, Bilder, Nachrichtenartikel und Videos.
* Abfragen eines Begriffs, Überprüfen der Anzahl von Ergebnissen und Ausgeben des Namens und der URL des ersten Ergebnisses.
* Abfragen eines Suchbegriffs mit festgelegtem Antwortfilter `news` und Ausgeben von Detailinformationen zu den Nachrichtenergebnissen.
* Abfragen eines Suchbegriffs mit den Parametern `answerCount` und `promote` und anschließendes Ausgeben von Detailinformationen zu den Ergebnissen.

## <a name="sdks-and-libraries"></a>SDKs und Bibliotheken

Verwenden Sie die folgenden Links, um auf das SDK für Ihre bevorzugte Sprache zuzugreifen.

* Erste Schritte mit [Python-Beispielen](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)
  * In den [Python-Bibliotheken](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-websearch) finden Sie Definitionen und Informationen zu Abhängigkeiten.
* Erste Schritte mit [Node.js-Beispielen](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
  * Siehe auch den Artikel zur [Node.js-Websuche](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesWebSearch).
* Erste Schritte mit [.NET-Beispielen](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
  * [NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
  * In den [.NET-Bibliotheken](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingWebSearch) finden Sie Definitionen und Informationen zu Abhängigkeiten.
* Erste Schritte mit [Java-Beispielen](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
  * In den [Java-Bibliotheken](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch) finden Sie Definitionen und Informationen zu Abhängigkeiten.