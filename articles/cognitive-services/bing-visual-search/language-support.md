---
title: Sprachunterstützung – API für die visuelle Bing-Suche
titleSuffix: Azure Cognitive Services
description: Eine Liste der von der API für die visuelle Bing-Suche unterstützten natürlichen Sprachen, Länder und Regionen. Die API für die visuelle Bing-Suche unterstützt mehr als drei Dutzend Länder/Regionen, viele davon mit mehreren Sprachen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: 1639b8066f3c9943bc42f5151fcb456585441baf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64866242"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Sprach- und Regionsunterstützung für die API für die visuelle Bing-Suche

Die API für die visuelle Bing-Suche unterstützt mehr als drei Dutzend Länder/Regionen, viele davon mit mehreren Sprachen. Jede Anforderung sollte das Land/die Region und die gewählte Sprache des Benutzers enthalten. Wenn der Markt des Benutzers bekannt ist, kann Bing entsprechende Ergebnisse zurückgeben. Wenn Sie kein Land/keine Region und keine Sprache angeben, versucht Bing, das Land/die Region und die Sprache des Benutzers zu ermitteln. Da die Ergebnisse Links zu Bing enthalten können, kann bei Kenntnis des Lands/der Region und der Sprache eine bevorzugte lokalisierte Bing-Benutzeroberfläche bereitgestellt werden, wenn der Benutzer auf die Bing-Links klickt.

Um das Land/die Region und die Sprache anzugeben, legen Sie den `mkt`-Abfrageparameter (Markt) auf einen Code aus der Tabelle **Märkte** unten fest. Der Markt gibt ein Land/eine Region und eine Sprache an. Wenn der Benutzer es bevorzugt, Text in einer anderen Sprache anzuzeigen, legen Sie den `setLang`-Abfrageparameter auf den entsprechenden Sprachcode fest.

Alternativ können Sie das Land/die Region mit dem `cc`-Abfrageparameter angeben. Wenn Sie ein Land/eine Region angeben, müssen Sie mithilfe des HTTP-Headers `Accept-Language` auch mindestens einen Sprachcode angeben. Die unterstützten Sprachen sind je nach Land/Region unterschiedlich. Sie werden für jedes Land in der Tabelle „Märkte“ angegeben.



> [!NOTE]
> Es gelten folgende Markteinschränkungen:
>
> - Anmerkungen zur Bilderkennung sind nur in Englisch verfügbar.
> - Auswertungen in Bezug auf Rezepte, Einkaufsquellen und Seiten mit gleichem Inhalt sind nur für den englischsprachigen US-Markt verfügbar.


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
