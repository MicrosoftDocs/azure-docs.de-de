---
title: Sprachunterstützung – Bing-Bildersuche-API
titleSuffix: Azure Cognitive Services
description: Finden Sie heraus, welche Länder/Regionen und Sprachen durch die Bing-Bildersuche-API unterstützt werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 14dacc4351af444c75517df4d4e02ecca3bbf40a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350074"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Sprach- und Regionsunterstützung für die Bing-Bildersuche-API

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Die Bing-Bildersuche-API unterstützt mehr als drei Dutzend Länder/Regionen, viele davon mit mehreren Sprachen. Die Angabe eines Lands/einer Region mit einer Abfrage dient in erster Linie dazu, Suchergebnisse auf Grundlage des Interesses an diesem Land/dieser Region zu verfeinern. Außerdem können die Ergebnisse Links zu Bing enthalten, und diese Links können die Bing-Benutzeroberfläche entsprechend dem angegebenen Land/der angegebenen Region oder der angegebenen Sprache lokalisieren.

Um das Land/die Region und die Sprache anzugeben, legen Sie den `mkt`-Abfrageparameter (Markt) auf einen Code aus der Tabelle **Märkte** unten fest. Der Markt gibt ein Land/eine Region und eine Sprache an. Wenn der Benutzer es bevorzugt, Text in einer anderen Sprache anzuzeigen, legen Sie den `setLang`-Abfrageparameter auf den entsprechenden Sprachcode fest.

Alternativ können Sie das Land/die Region mit dem `cc`-Abfrageparameter angeben. Wenn Sie ein Land/eine Region angeben, müssen Sie mithilfe des HTTP-Headers `Accept-Language` auch mindestens einen Sprachcode angeben. Die unterstützten Sprachen sind je nach Land/Region unterschiedlich. Sie werden für jedes Land/jede Region in der Tabelle „Märkte“ angegeben.

> [!NOTE]
> Die API für beliebte Bilder unterstützt zurzeit nur die folgenden Märkte:
> - en-US (Englisch, USA)
> - en-CA (Englisch, Kanada)
> - en-AU (Englisch, Australien)
> - zh-CN (Chinesisch, China)

## <a name="countriesregions"></a>Länder/Regionen

|Land/Region|Code|
|-------|----|
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
|China|CN|
|Polen|PL|
|Portugal|PT|
|Philippinen|PH|
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


## <a name="markets"></a>Märkte

|Land/Region|Sprache|Marktcode|
|-------|--------|-----------|
|Argentinien|Spanisch|es-AR|
|Australien|Englisch|en-AU|
|Österreich|Deutsch|de-AT|
|Belgien|Niederländisch|nl-BE|
|Belgien|Französisch|fr-BE|
|Brasilien|Portugiesisch|pt-BR|
|Canada|Englisch|en-CA|
|Canada|Französisch|fr-CA|
|Chile|Spanisch|es-CL|
|Dänemark|Dänisch|da-DK|
|Finnland|Finnisch|fi-FI|
|Frankreich|Französisch|fr-FR|
|Deutschland|Deutsch|de-DE|
|Hongkong (SAR)|Chinesisch (traditionell)|zh-HK|
|Indien|Englisch|en-IN|
|Indonesien|Englisch|en-ID|
|Italien|Italienisch|it-IT|
|Japan|Japanisch|ja-JP|
|Korea|Koreanisch|ko-KR|
|Malaysia|Englisch|en-MY|
|Mexiko|Spanisch|es-MX|
|Niederlande|Niederländisch|nl-NL|
|Neuseeland|Englisch|en-NZ|
|China|Chinesisch|zh-CN|
|Polen|Polnisch|pl-PL|
|Portugal|Portugiesisch|pt-PT|
|Philippinen|Englisch|en-PH|
|Russland|Russisch|ru-RU|
|Saudi-Arabien|Arabisch|ar-SA|
|Südafrika|Englisch|en-ZA|
|Spanien|Spanisch|es-ES|
|Schweden|Schwedisch|sv-SE|
|Schweiz|Französisch|fr-CH|
|Schweiz|Deutsch|de-CH|
|Taiwan|Chinesisch (traditionell)|zh-TW|
|Türkei|Türkisch|tr-TR|
|United Kingdom|Englisch|en-GB|
|USA|Englisch|de-DE|
|USA|Spanisch|es-US|

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu den Endpunkten der Bing-News-Suche finden Sie unter [Referenz zur News-Bildersuche-API v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).