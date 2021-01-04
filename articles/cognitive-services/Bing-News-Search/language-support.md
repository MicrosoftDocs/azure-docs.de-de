---
title: Sprachunterstützung – Bing-News-Suche-API
titleSuffix: Azure Cognitive Services
description: Eine Liste der von der Bing-News-Suche-API unterstützten natürlichen Sprachen, Länder und Regionen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 9c7fd03c2239cea05dc79ad4dd1965fe253a2ce9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341596"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Sprach- und Regionsunterstützung für die Bing-News-Suche-API

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Die Bing-News-Suche-API unterstützt zahlreiche Länder/Regionen, viele davon mit mehreren Sprachen. Die Angabe eines Lands/einer Region mit einer Abfrage dient in erster Linie dazu, Suchergebnisse auf Grundlage des Interesses an diesem Land/dieser Region zu verfeinern. Außerdem können die Ergebnisse Links zu Bing enthalten, und diese Links können die Bing-Benutzeroberfläche entsprechend dem angegebenen Land/der angegebenen Region oder der angegebenen Sprache lokalisieren.

Sie können mit dem `cc`-Abfrageparameter ein Land/eine Region angeben. Wenn Sie ein Land/eine Region angeben, müssen Sie mithilfe des HTTP-Headers `Accept-Language` auch mindestens einen Sprachcode angeben. Die unterstützten Sprachen sind je nach Land/Region unterschiedlich. Sie werden für jedes Land/jede Region in der Tabelle „Märkte“ angegeben.

Alternativ können Sie den Markt mit dem Abfrageparameter `mkt` und einen Code aus der Tabelle **Märkte** angeben. Bei der Angabe eines Markts werden gleichzeitig ein Land/eine Region und die bevorzugte Sprache angegeben. Der Abfrageparameter `setLang` kann in diesem Fall auf einen Sprachcode festgelegt sein. Dies ist in der Regel dieselbe von `mkt` angegebene Sprache, es sei denn, der Benutzer möchte Bing in einer anderen Sprache anzeigen.

## <a name="supported-markets-for-news-search-endpoint"></a>Unterstützte Märkte für den Endpunkt der News-Suche

Für den `/news/search`-Endpunkt enthält die folgende Tabelle die Marktcodewerte, mit denen Sie den `mkt`-Abfrageparameter angeben können. Bing gibt nur Inhalt für diese Märkte zurück. Änderungen der Liste vorbehalten.  

Eine Liste der Länder-/Regionscodes, die Sie im `cc`-Abfrageparameter angeben können, finden Sie unter [Ländercodes](#countrycodes).  

|Land/Region|Sprache|Marktcode|  
|---------------------|--------------|-----------------|
|Dänemark|Dänisch|da-DK|
|Österreich|Deutsch|de-AT|
|Schweiz|Deutsch|de-CH|
|Deutschland|Deutsch|de-DE|
|Australien|Englisch|en-AU|
|Canada|Englisch|en-CA|
|United Kingdom|Englisch|en-GB|
|Indonesien|Englisch|en-ID|
|Irland|Englisch|en-IE|
|Indien|Englisch|en-IN|
|Malaysia|Englisch|en-MY|
|Neuseeland|Englisch|en-NZ|
|Republik Philippinen|Englisch|en-PH|
|Singapur|Englisch|en-SG|
|USA|Englisch|de-DE|
|Englisch|allgemein|en-WW|
|Englisch|allgemein|en-XA|
|Südafrika|Englisch|en-ZA|
|Argentinien|Spanisch|es-AR|
|Chile|Spanisch|es-CL|
|Spanien|Spanisch|es-ES|
|Mexiko|Spanisch|es-MX|
|USA|Spanisch|es-US|
|Spanisch|allgemein|es-XL|
|Finnland|Finnisch|fi-FI|  
|Frankreich|Französisch|fr-BE|
|Canada|Französisch|fr-CA|
|Belgien|Niederländisch|nl-BE|
|Schweiz|Französisch|fr-CH|
|Frankreich|Französisch|fr-FR|  
|Italien|Italienisch|it-IT|
|Hongkong (SAR)|Chinesisch (traditionell)|zh-HK|  
|Taiwan|Chinesisch (traditionell)|zh-TW|
|Japan|Japanisch|ja-JP|  
|Korea|Koreanisch|ko-KR|  
|Niederlande|Niederländisch|nl-NL|  
|VR China|Chinesisch|zh-CN|  
|Brasilien|Portugiesisch|pt-BR|
|Russland|Russisch|ru-RU|  
|Schweden|Schwedisch|sv-SE|  
|Türkei|Türkisch|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Unterstützte Märkte für den News-Endpunkt
Für den `/news`-Endpunkt enthält die folgende Tabelle die Marktcodewerte, mit denen Sie den `mkt`-Abfrageparameter angeben können. Bing gibt nur Inhalt für diese Märkte zurück. Änderungen der Liste vorbehalten.  

Eine Liste der Länder-/Regionscodes, die Sie im `cc`-Abfrageparameter angeben können, finden Sie unter [Ländercodes](#countrycodes).  

|Land/Region|Sprache|Marktcode|  
|---------------------|--------------|-----------------|
|Dänemark|Dänisch|da-DK|
|Deutschland|Deutsch|de-DE|
|Australien|Englisch|en-AU|
|United Kingdom|Englisch|en-GB|
|USA|Englisch|de-DE|
|Englisch|allgemein|en-WW|
|Chile|Spanisch|es-CL|
|Mexiko|Spanisch|es-MX|
|USA|Spanisch|es-US|
|Finnland|Finnisch|fi-FI|  
|Canada|Französisch|fr-CA|
|Frankreich|Französisch|fr-FR|  
|Italien|Italienisch|it-IT|
|Brasilien|Portugiesisch|pt-BR|
|VR China|Chinesisch|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Unterstützte Märkte für den News-Trends-Endpunkt
Für den `/news/trendingtopics`-Endpunkt enthält die folgende Tabelle die Marktcodewerte, mit denen Sie den `mkt`-Abfrageparameter angeben können. Bing gibt nur Inhalt für diese Märkte zurück. Änderungen der Liste vorbehalten.  

Eine Liste der Länder-/Regionscodes, die Sie im `cc`-Abfrageparameter angeben können, finden Sie unter [Ländercodes](#countrycodes).  

|Land/Region|Sprache|Marktcode|  
|---------------------|--------------|-----------------|
|Deutschland|Deutsch|de-DE|
|Australien|Englisch|en-AU|
|United Kingdom|Englisch|en-GB|
|USA|Englisch|de-DE|
|Canada|Englisch|en-CA|
|Indien|Englisch|en-IN|
|Frankreich|Französisch|fr-FR|
|Canada|Französisch|fr-CA|
|Brasilien|Portugiesisch|pt-BR|
|VR China|Chinesisch|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Landeskennzahlen  

Die folgenden Länder-/Regionscodes können Sie im `cc`-Abfrageparameter angeben. Änderungen der Liste vorbehalten.  

|Land/Region|Landesvorwahl|  
|---------------------|------------------|  
|Argentinien|AR|  
|Australien|AU|  
|Österreich|AT|  
|Belgien|BE|  
|Brasilien|BR|  
|Canada|CA|  
|Chile|CL|  
|Dänemark|DK|  
|Finnland|FI|  
|Frankreich|BV|  
|Deutschland|DE|  
|Hongkong (SAR)|HK|  
|Indien|IN|  
|Indonesien|id|  
|Italien|IT|  
|Japan|JP|  
|Korea|KR|  
|Malaysia|MY|  
|Mexiko|MX|  
|Niederlande|NL|  
|Neuseeland|NZ|  
|Norwegen|Nein|  
|VR China|CN|  
|Polen|PL|  
|Portugal|PT|  
|Republik Philippinen|PH|  
|Russland|RU|  
|Saudi-Arabien|SA|  
|Südafrika|ZA|  
|Spanien|ES|  
|Schweden|SE|  
|Schweiz|CH|  
|Taiwan|TW|  
|Türkei|TR|  
|United Kingdom|GB|  
|USA|US|

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu den Endpunkten der Bing-News-Suche finden Sie unter [API v7-Referenz zur News-Suche](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).