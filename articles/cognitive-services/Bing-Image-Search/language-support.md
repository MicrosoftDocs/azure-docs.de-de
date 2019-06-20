---
title: Sprachunterstützung – Bing-Bildersuche-API
titleSuffix: Azure Cognitive Services
description: Finden Sie heraus, welche Länder/Regionen und Sprachen durch die Bing-Bildersuche-API unterstützt werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 6f4c354c89fa00d5fc65c635f5f6315761be2f01
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384144"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Sprach- und Regionsunterstützung für die Bing-Bildersuche-API

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
|Kanada|CA|
|Chile|CL|
|Dänemark|DK|
|Finnland|FI|
|Frankreich|BV|
|Deutschland|DE|
|Hongkong (SAR)|HK|
|Indien|IN|
|Indonesien|ID|
|Italien|IT|
|Japan|JP|
|Korea|KR|
|Malaysia|MY|
|Mexiko|MX|
|Niederlande|NL|
|Neuseeland|NZ|
|Norwegen|NO|
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
|Vereinigtes Königreich|GB|
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
|Kanada|Englisch|en-CA|
|Kanada|Französisch|fr-CA|
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
|Vereinigtes Königreich|Englisch|en-GB|
|USA|Englisch|en-US|
|USA|Spanisch|es-US|

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu den Endpunkten der Bing-News-Suche finden Sie unter [Referenz zur News-Bildersuche-API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).
