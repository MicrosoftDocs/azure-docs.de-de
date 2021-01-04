---
title: Sprachunterstützung – Textanalyse-API
titleSuffix: Azure Cognitive Services
description: Eine Liste der von der Textanalyse-API unterstützten natürlichen Sprachen. In diesem Artikel wird beschrieben, welche Sprachen für die einzelnen Vorgänge unterstützt werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: aahi
ms.openlocfilehash: b90fe6b7e0937a9ac3b300e904085122610d5645
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957077"
---
# <a name="text-analytics-api-v3-language-support"></a>Textanalyse-API v3: Sprachunterstützung 

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]


#### <a name="sentiment-analysis"></a>[Standpunktanalyse](#tab/sentiment-analysis)

| Sprache              | Sprachcode | v2-Unterstützung | v3-Unterstützung | Ab Modellversion 3: |              Notizen |
|:----------------------|:-------------:|:----------:|:----------:|:--------------------------:|-------------------:|
| Chinesisch (vereinfacht)    |   `zh-hans`   |     ✓      |     ✓      |         2019-10-01         | `zh` wird ebenfalls akzeptiert. |
| Chinesisch (traditionell)   |   `zh-hant`   |            |     ✓      |         2019-10-01         |                    |
| Dänisch               |     `da`      |     ✓      |            |                            |                    |
| Niederländisch                 |     `nl`      |     ✓      |            |                            |                    |
| Englisch               |     `en`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Finnisch               |     `fi`      |     ✓      |            |                            |                    |
| Französisch                |     `fr`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Deutsch                |     `de`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Griechisch                 |     `el`      |     ✓      |            |                            |                    |
| Hindi                 |     `hi`      |            |      ✓     |          2020-04-01        |                    |
| Italienisch               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Japanisch              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Koreanisch                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norwegisch (Bokmål)   |     `no`      |     ✓      |     ✓      |         2020-07-01         |                    |
| Polnisch                |     `pl`      |     ✓      |            |                            |                    |
| Portugiesisch (Brasilien)   |    `pt-BR`    |            |     ✓      |         2020-04-01         |                    |
| Portugiesisch (Portugal) |    `pt-PT`    |     ✓      |     ✓      |         2019-10-01         | `pt` wird ebenfalls akzeptiert. |
| Russisch               |     `ru`      |     ✓      |            |                            |                    |
| Spanisch               |     `es`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Schwedisch               |     `sv`      |     ✓      |            |                            |                    |
| Türkisch               |     `tr`      |     ✓      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Opinion Mining (nur v3.1-preview)

| Sprache              | Sprachcode | Ab Modellversion 3: |              Notizen |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Englisch               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Erkennung benannter Entitäten (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * Für NER v3 werden derzeit nur Englisch und Spanisch unterstützt. Wenn Sie NER v3 mit einer anderen Sprache aufrufen, gibt die API Ergebnisse für v2.1 zurück, sofern die Sprache in Version 2.1 unterstützt wird.
> * Bei v2.1 wird der vollständige Satz mit den verfügbaren Entitäten nur für die Sprachen Englisch, Chinesisch (Vereinfacht), Französisch, Deutsch und Spanisch zurückgegeben.  Für die anderen unterstützten Sprachen werden die Entitäten für „Person“, „Standort“ und „Organisation“ zurückgegeben.

| Sprache               | Sprachcode | v2.1-Unterstützung | v3-Unterstützung | Ab Modellversion 3: |       Notizen        |
|:-----------------------|:-------------:|:----------:|:----------:|:-------------------------------:|:------------------:|
| Arabisch                |     `ar`      |     ✓      |            |                                 |                    |
| Tschechisch                 |     `cs`      |     ✓      |            |                                 |                    |
| Chinesisch (vereinfacht)     |   `zh-hans`   |     ✓      |            |                                 | `zh` wird ebenfalls akzeptiert. |
| Chinesisch (traditionell)   |   `zh-hant`   |     ✓      |            |                                 |                    |
| Dänisch                |     `da`      |     ✓      |            |                                 |                    |
| Niederländisch                 |     `nl`      |     ✓      |            |                                 |                    |
| Englisch                |     `en`      |     ✓      |     ✓      |           2019-10-01            |                    |
| Finnisch               |     `fi`      |     ✓      |            |                                 |                    |
| Französisch                 |     `fr`      |     ✓      |            |                                 |                    |
| Deutsch                 |     `de`      |     ✓      |            |                                 |                    |
| Hebräisch                |     `he`      |     ✓      |            |                                 |                    |
| Ungarisch             |     `hu`      |     ✓      |            |                                 |                    |
| Italienisch               |     `it`      |     ✓      |            |                                 |                    |
| Japanisch              |     `ja`      |     ✓      |            |                                 |                    |
| Koreanisch                |     `ko`      |     ✓      |            |                                 |                    |
| Norwegisch (Bokmål)   |     `no`      |     ✓      |            |                                 | `nb` wird ebenfalls akzeptiert. |
| Polnisch                |     `pl`      |     ✓      |            |                                 |                    |
| Portugiesisch (Portugal) |    `pt-PT`    |     ✓      |            |                                 | `pt` wird ebenfalls akzeptiert. |
| Portugiesisch (Brasilien)   |    `pt-BR`    |     ✓      |            |                                 |                    |
| Russisch              |     `ru`      |     ✓      |            |                                 |                    |
| Spanisch               |     `es`      |     ✓      |     ✓       |              2020-04-01                   |                    |
| Schwedisch               |     `sv`      |     ✓      |            |                                 |                    |
| Türkisch               |     `tr`      |     ✓      |            |                                 |                    |

#### <a name="key-phrase-extraction"></a>[Schlüsselbegriffserkennung](#tab/key-phrase-extraction)

> [!NOTE]
> Modellversionen der Schlüsselbegriffserkennung vor Version 2020-07-01 sind auf 64 Zeichen begrenzt. Dieser Grenzwert gilt bei späteren Modellversionen nicht mehr.

| Sprache              | Sprachcode | v2-Unterstützung | v3-Unterstützung | Ab Modellversion 3 verfügbar: |       Notizen        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
| Niederländisch                 |     `nl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Englisch               |     `en`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Finnisch               |     `fi`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Französisch                |     `fr`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Deutsch                |     `de`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Italienisch               |     `it`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Japanisch              |     `ja`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Koreanisch                |     `ko`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Norwegisch (Bokmål)   |     `no`      |     ✓      |     ✓      |                2019-10-01                 | `nb` wird ebenfalls akzeptiert. |
| Polnisch                |     `pl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Portugiesisch (Portugal) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | `pt` wird ebenfalls akzeptiert. |
| Portugiesisch (Brasilien)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| Russisch               |     `ru`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Spanisch               |     `es`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Schwedisch               |     `sv`      |     ✓      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Entitätsverknüpfung](#tab/entity-linking)

| Sprache | Sprachcode | v2-Unterstützung | v3-Unterstützung | Ab Modellversion 3 verfügbar: | Notizen |
|:---------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:-----:|
| Englisch  |     `en`      |     ✓      |     ✓      |                2019-10-01                 |       |
| Spanisch  |     `es`      |     ✓      |     ✓      |                2019-10-01                 |       |

#### <a name="language-detection"></a>[Sprachenerkennung](#tab/language-detection)

Die Textanalyse-API kann eine Vielzahl von Sprachen, Varianten und Dialekten sowie einige Regional- und Kultursprachen erkennen und erkannte Sprachen mit Name und Code zurückgeben. Sprachcodeparameter für die Sprachenerkennung der Textanalyse entsprechen dem Standard [BCP-47](https://tools.ietf.org/html/bcp47), und der Großteil ist mit [ISO-639-1](https://www.iso.org/iso-639-language-codes.html)-Bezeichnern konform. 

Bei Inhalten in einer seltener verwendeten Sprache können Sie die Sprachenerkennung ausprobieren, um zu sehen, ob sie einen Code zurückgibt. Die Antwort bei Sprachen, die nicht erkannt werden können, lautet `unknown`.

| Sprache | Sprachcode |  v3-Unterstützung | Ab Modellversion 3 verfügbar: |
|:---------|:-------------:|:----------:|:-----------------------------------------:|
|Afrikaans|`af`|✓|    |
|Albanisch|`sq`|✓|    |
|Arabisch|`ar`|✓|    |
|Armenisch|`hy`|✓|    |
|Baskisch|`eu`|✓|    |
|Belarussisch|`be`|✓|    |
|Bengali|`bn`|✓|    |
|Bosnisch|`bs`|✓|2020-09-01|
|Bulgarisch|`bg`|✓|    |
|Birmanisch|`my`|✓|    |
|Katalanisch, Valencianisch|`ca`|✓|    |
|Khmer|`km`|✓|    |
|Chinesisch|`zh`|✓|    |
|Chinesisch (vereinfacht)|`zh_chs`|✓|    |
|Chinesisch (traditionell)|`zh_cht`|✓|    |
|Kroatisch|`hr`|✓|    |
|Tschechisch|`cs`|✓|    |
|Dänisch|`da`|✓|    |
|Dari|`prs`|✓|2020-09-01|
|Divehi, Dhivehi|`dv`|✓|    |
|Niederländisch, Flämisch|`nl`|✓|    |
|Englisch|`en`|✓|    |
|Esperanto|`eo`|✓|    |
|Estnisch|`et`|✓|    |
|Fidschi|`fj`|✓|2020-09-01|
|Finnisch|`fi`|✓|    |
|Französisch|`fr`|✓|    |
|Galizisch|`gl`|✓|    |
|Georgisch|`ka`|✓|    |
|Deutsch|`de`|✓|    |
|Griechisch|`el`|✓|    |
|Gujarati|`gu`|✓|    |
|Haiti-Kreolisch, Haitianisches Kreolisch|`ht`|✓|    |
|Hebräisch|`he`|✓|    |
|Hindi|`hi`|✓|    |
|Hmong Daw|`mww`|✓|2020-09-01|
|Ungarisch|`hu`|✓|    |
|Isländisch|`is`|✓|    |
|Indonesisch|`id`|✓|    |
|Inuktitut|`iu`|✓|    |
|Irisch|`ga`|✓|    |
|Italienisch|`it`|✓|    |
|Japanisch|`ja`|✓|    |
|Kannada|`kn`|✓|    |
|Kasachisch|`kk`|✓|2020-09-01|
|Koreanisch|`ko`|✓|    |
|Kurdisch|`ku`|✓|    |
|Laotisch|`lo`|✓|    |
|Lateinisch|`la`|✓|    |
|Lettisch|`lv`|✓|    |
|Litauisch|`lt`|✓|    |
|Mazedonisch|`mk`|✓|    |
|Madagassisch|`mg`|✓|2020-09-01|
|Malaiisch|`ms`|✓|    |
|Malayalam|`ml`|✓|    |
|Maltesisch|`mt`|✓|    |
|Maori|`mi`|✓|2020-09-01|
|Marathi|`mr`|✓|2020-09-01|
|Norwegisch|`no`|✓|    |
|Norwegisch Nynorsk|`nn`|✓|    |
|Oriya|`or`|✓|    |
|Paschtu, Pushto|`ps`|✓|    |
|Persisch|`fa`|✓|    |
|Polnisch|`pl`|✓|    |
|Portugiesisch|`pt`|✓|    |
|Punjabi, Panjabi|`pa`|✓|    |
|Queretaro-Otomi|`otq`|✓|2020-09-01|
|Rumänisch|`ro`|✓|    |
|Russisch|`ru`|✓|    |
|Samoanisch|`sm`|✓|2020-09-01|
|Serbisch|`sr`|✓|    |
|Singhalesisch|`si`|✓|    |
|Slowakisch|`sk`|✓|    |
|Slowenisch|`sl`|✓|    |
|Somali|`so`|✓|    |
|Spanisch, Kastilisch|`es`|✓|    |
|Suaheli|`sw`|✓|    |
|Schwedisch|`sv`|✓|    |
|Tagalog|`tl`|✓|    |
|Tahitisch|`ty`|✓|2020-09-01|
|Tamilisch|`ta`|✓|    |
|Telugu|`te`|✓|    |
|Thailändisch|`th`|✓|    |
|Tongaisch|`to`|✓|2020-09-01|
|Türkisch|`tr`|✓|    |
|Ukrainisch|`uk`|✓|    |
|Urdu|`ur`|✓|    |
|Usbekisch|`uz`|✓|    |
|Vietnamesisch|`vi`|✓|    |
|Walisisch|`cy`|✓|    |
|Jiddisch|`yi`|✓|    |
|Yukatekisches Maya|`yua`|✓|    |


---


## <a name="see-also"></a>Weitere Informationen

* [Worum handelt es sich bei der Textanalyse-API?](overview.md)   
