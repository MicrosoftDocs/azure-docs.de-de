---
title: Alle vordefinierten Entitäten
titleSuffix: Azure Cognitive Services
description: Dieser Artikel enthält Listen mit vordefinierten Entitäten, die in LUIS (Language Understanding Intelligent Service) enthalten sind.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/29/2019
ms.author: diberry
ms.openlocfilehash: 729d5d1c9ad21654fbd5923b9f3ffacc3b709ef0
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388788"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entitäten nach Kultur in Ihrem LUIS-Modell

LUIS stellt vordefinierte Entitäten bereit. Wenn eine vordefinierte Entität in Ihrer Anwendung enthalten ist, schließt LUIS die entsprechende Entitätsvorhersage in die Endpunktantwort ein. Alle Beispieläußerungen sind ebenfalls mit der Entität gekennzeichnet. Das Verhalten der vordefinierten Entitäten **kann nicht** geändert werden. Sofern nicht anders vermerkt, sind vordefinierte Entitäten in allen LUIS-Anwendungsgebietsschemas (Kulturen) verfügbar. Die folgende Tabelle zeigt die vordefinierten Entitäten, die für die jeweilige Kultur unterstützt werden.

|Kultur|Subkulturen|Notizen|
|--|--|--|
|Chinesisch|[zh-CN](#chinese-entity-support)||
|Niederländisch|[nl-NL](#dutch-entity-support)||
|Englisch|[en-US (Amerika)](#english-american-entity-support)||
|Französisch|[fr-CA (Kanada)](#french-canadian-entity-support), [fr-FR (Frankreich)](#french-france-entity-support), ||
|Deutsch|[de-DE](#german-entity-support)||
|Italienisch|[it-IT](#italian-entity-support)||
|Japanisch|[ja-JP](#japanese-entity-support)||
|Koreanisch|[ko-KR](#korean-entity-support)||
|Portugiesisch|[pt-BR (Brasilien)](#portuguese-brazil-entity-support)||
|Spanisch|[es-ES (Spanien)](#spanish-spain-entity-support), [es-MX (Mexiko)](#spanish-mexico-entity-support)||
|Türkisch|[Türkisch](#turkish-entity-support)|Für Türkisch werden keine vordefinierten Entitäten unterstützt|

## <a name="chinese-entity-support"></a>Unterstützung chinesischer Entitäten

Die folgenden Entitäten werden unterstützt:

|Vordefinierte Entität|```zh-CN``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Währung (Geld)](luis-reference-prebuilt-currency.md):<br>Dollar<br>Untereinheit (z.B. „penny“)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>Volume<br>area<br>weight<br>information (Beispiel: bit/byte)<br>length (Beispiel: meter)<br>speed (Beispiel: mile per hour)  |    ✔   | 
[E-Mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="dutch-entity-support"></a>Unterstützung niederländischer Entitäten

Die folgenden Entitäten werden unterstützt:

|Vordefinierte Entität|```nl-NL``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Währung (Geld)](luis-reference-prebuilt-currency.md):<br>Dollar<br>Untereinheit (z.B. „penny“)  |    ✔   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>Volume<br>area<br>weight<br>information (Beispiel: bit/byte)<br>length (Beispiel: meter)<br>speed (Beispiel: mile per hour)  |    ✔   | 
[E-Mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="english-american-entity-support"></a>Unterstützung englischer (Amerika) Entitäten

Die folgenden Entitäten werden unterstützt:

|Vordefinierte Entität|```en-US``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Währung (Geld)](luis-reference-prebuilt-currency.md):<br>Dollar<br>Untereinheit (z.B. „penny“)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>Volume<br>area<br>weight<br>information (Beispiel: bit/byte)<br>length (Beispiel: meter)<br>speed (Beispiel: mile per hour)  |    ✔   | 
[E-Mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-france-entity-support"></a>Unterstützung französischer (Frankreich) Entitäten

Die folgenden Entitäten werden unterstützt:

|Vordefinierte Entität|```fr-FR``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Währung (Geld)](luis-reference-prebuilt-currency.md):<br>Dollar<br>Untereinheit (z.B. „penny“)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>Volume<br>area<br>weight<br>information (Beispiel: bit/byte)<br>length (Beispiel: meter)<br>speed (Beispiel: mile per hour)  |    ✔   | 
[E-Mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |   -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-canadian-entity-support"></a>Unterstützung französischer (Kanada) Entitäten

Die folgenden Entitäten werden unterstützt:

|Vordefinierte Entität|```fr-CA``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Währung (Geld)](luis-reference-prebuilt-currency.md):<br>Dollar<br>Untereinheit (z.B. „penny“)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>Volume<br>area<br>weight<br>information (Beispiel: bit/byte)<br>length (Beispiel: meter)<br>speed (Beispiel: mile per hour)  |    ✔   | 
[E-Mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="german-entity-support"></a>Unterstützung deutscher Entitäten

Die folgenden Entitäten werden unterstützt:

|Vordefinierte Entität|```de-DE``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Währung (Geld)](luis-reference-prebuilt-currency.md):<br>Dollar<br>Untereinheit (z.B. „penny“)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>Volume<br>area<br>weight<br>information (Beispiel: bit/byte)<br>length (Beispiel: meter)<br>speed (Beispiel: mile per hour)  |    ✔   | 
[E-Mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="italian-entity-support"></a>Unterstützung italienischer Entitäten

Die folgenden Entitäten werden unterstützt:

|Vordefinierte Entität|```it-IT``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Währung (Geld)](luis-reference-prebuilt-currency.md):<br>Dollar<br>Untereinheit (z.B. „penny“)  |    ✔   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>Volume<br>area<br>weight<br>information (Beispiel: bit/byte)<br>length (Beispiel: meter)<br>speed (Beispiel: mile per hour)  |    ✔   | 
[E-Mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="japanese-entity-support"></a>Unterstützung japanischer Entitäten

Die folgenden Entitäten werden unterstützt:

|Vordefinierte Entität|```ja-JP``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Währung (Geld)](luis-reference-prebuilt-currency.md):<br>Dollar<br>Untereinheit (z.B. „penny“)  |    ✔   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>Volume<br>area<br>weight<br>information (Beispiel: bit/byte)<br>length (Beispiel: meter)<br>speed (Beispiel: mile per hour)  |    ✔   | 
[E-Mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="korean-entity-support"></a>Unterstützung koreanischer Entitäten

Die folgenden Entitäten werden unterstützt:

|Vordefinierte Entität|```ko-KR``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Währung (Geld)](luis-reference-prebuilt-currency.md):<br>Dollar<br>Untereinheit (z.B. „penny“)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>Volume<br>area<br>weight<br>information (Beispiel: bit/byte)<br>length (Beispiel: meter)<br>speed (Beispiel: mile per hour)  |    -   | 
[E-Mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="portuguese-brazil-entity-support"></a>Unterstützung portugiesischer (Brasilien) Entitäten

Die folgenden Entitäten werden unterstützt:

|Vordefinierte Entität|```pt-BR``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Währung (Geld)](luis-reference-prebuilt-currency.md):<br>Dollar<br>Untereinheit (z.B. „penny“)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>Volume<br>area<br>weight<br>information (Beispiel: bit/byte)<br>length (Beispiel: meter)<br>speed (Beispiel: mile per hour)  |    ✔   | 
[E-Mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-spain-entity-support"></a>Unterstützung spanischer (Spanien) Entitäten

Die folgenden Entitäten werden unterstützt:

|Vordefinierte Entität|```es-ES``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Währung (Geld)](luis-reference-prebuilt-currency.md):<br>Dollar<br>Untereinheit (z.B. „penny“)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>Volume<br>area<br>weight<br>information (Beispiel: bit/byte)<br>length (Beispiel: meter)<br>speed (Beispiel: mile per hour)  |    ✔   | 
[E-Mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-mexico-entity-support"></a>Unterstützung spanischer (Mexiko) Entitäten

Die folgenden Entitäten werden unterstützt:

|Vordefinierte Entität|```es-MX``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Währung (Geld)](luis-reference-prebuilt-currency.md):<br>Dollar<br>Untereinheit (z.B. „penny“)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>Volume<br>area<br>weight<br>information (Beispiel: bit/byte)<br>length (Beispiel: meter)<br>speed (Beispiel: mile per hour)  |    -   | 
[E-Mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

Beachten Sie auch die Hinweise in [Veraltete Entitäten](luis-reference-prebuilt-deprecated.md).

Die keyPhrase-Entität ist nicht für alle Unterkulturen von Portugiesisch (Brasilien) verfügbar (```pt-BR```).

## <a name="turkish-entity-support"></a>Unterstützung türkischer Entitäten

**Für Türkisch werden keine vordefinierten Entitäten unterstützt.** 

<!--

|Prebuilt entity|```tr-tr``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>weight<br>information (ex: bit/byte)<br>length (ex: meter)<br>speed (ex: mile per hour)  |    -   | 
[Email](luis-reference-prebuilt-email.md)   |    -   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Number](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    -   |

See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)


KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Beitragen zu vordefinierten Entitäten in verschiedenen Kulturen
Die vordefinierten Entitäten werden im Open Source-Projekt „Recognizers-Text“ entwickelt. Zum Projekt [beitragen](https://github.com/Microsoft/Recognizers-Text). Dieses Projekt enthält Beispiele zur Entität „currency“ (Währung) in den verschiedenen Kulturen. 

„GeographyV2“ und „PersonName“ sind im Projekt „Recognizers-Text“ nicht enthalten. Bei Problemen mit diesen vordefinierten Entitäten stellen Sie eine [Supportanfrage](../../azure-supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Entitäten [number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) und [currency](luis-reference-prebuilt-currency.md). 
