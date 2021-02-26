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
ms.date: 12/17/2020
ms.author: aahi
ms.openlocfilehash: af7c3f85a75cfb425003b0da2af268fbba1d1d10
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092583"
---
# <a name="text-analytics-api-v3-language-support"></a>Textanalyse-API v3: Sprachunterstützung 

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
> * Nur die Entitäten „Person“, „Standort“ und „Organisation“ werden für mit einem * gekennzeichnete Sprachen zurückgegeben.

| Sprache               | Sprachcode | v3-Unterstützung | Ab Modellversion 3: |       Notizen        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Arabisch                 |     `ar`      |      ✓*    |               2019-10-01        |                    |
| Chinesisch (vereinfacht)     |   `zh-hans`   |     ✓      |               15.1.2021        | `zh` wird ebenfalls akzeptiert. |
| Chinesisch (traditionell)   |   `zh-hant`   |     ✓*      |               2019-10-01        |                    |
| Tschechisch                 |     `cs`      |     ✓*      |               2019-10-01        |                    |
| Dänisch                |     `da`      |     ✓*      |               2019-10-01        |                    |
| Niederländisch                 |     `nl`      |     ✓*      |               2019-10-01        |                    |
| Englisch                |     `en`      |     ✓      |               2019-10-01        |                    |
| Finnisch               |     `fi`      |     ✓*      |               2019-10-01        |                    |
| Französisch                 |     `fr`      |     ✓      |               15.1.2021        |                    |
| Deutsch                 |     `de`      |     ✓      |               15.1.2021        |                    |
| Hebräisch                |     `he`      |     ✓*      |               2019-10-01        |                    |
| Ungarisch             |     `hu`      |     ✓*      |               2019-10-01        |                    |
| Italienisch               |     `it`      |     ✓       |               15.1.2021        |                    |
| Japanisch              |     `ja`      |     ✓       |               15.1.2021        |                    |
| Koreanisch                |     `ko`      |     ✓       |               15.1.2021        |                    |
| Norwegisch (Bokmål)   |     `no`      |     ✓*      |               2019-10-01        | `nb` wird ebenfalls akzeptiert. |
| Polnisch                |     `pl`      |     ✓*      |               2019-10-01        |                    |
| Portugiesisch (Brasilien)   |    `pt-BR`    |     ✓       |               15.1.2021        |                    |
| Portugiesisch (Portugal) |    `pt-PT`    |     ✓       |               15.1.2021        | `pt` wird ebenfalls akzeptiert. |
| Russisch              |     `ru`      |     ✓*       |               2019-10-01        |                    |
| Spanisch               |     `es`      |     ✓       |               2020-04-01        |                    |
| Schwedisch               |     `sv`      |     ✓*      |               2019-10-01        |                    |
| Türkisch               |     `tr`      |     ✓*      |               2019-10-01        |                    |

#### <a name="key-phrase-extraction"></a>[Schlüsselbegriffserkennung](#tab/key-phrase-extraction)

| Sprache              | Sprachcode | v2-Unterstützung | v3-Unterstützung | Ab Modellversion 3 verfügbar: |       Notizen        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
| Dänisch                |     `da`      |     ✓      |      ✓     |                2019-10-01                 |                    |
| Niederländisch                 |     `nl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Englisch               |     `en`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Finnisch               |     `fi`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Französisch                |     `fr`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Deutsch                |     `de`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Italienisch               |     `it`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Japanisch              |     `ja`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Koreanisch                |     `ko`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Norwegisch (Bokmål)   |     `no`      |     ✓      |     ✓      |                2020-07-01                 | `nb` wird ebenfalls akzeptiert. |
| Polnisch                |     `pl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Portugiesisch (Brasilien)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| Portugiesisch (Portugal) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | `pt` wird ebenfalls akzeptiert. |
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

| Sprache | Sprachcode | v3-Unterstützung | Ab Modellversion 3 verfügbar: |
|:-|:-:|:-:|:-:|
|Afrikaans|`af`|✓|    |
|Albanisch|`sq`|✓|    |
|Amharisch|`am`|✓|5\.1.2021|
|Arabisch|`ar`|✓|    |
|Armenisch|`hy`|✓|    |
|Assamesisch|`as`|✓|5\.1.2021|
|Aserbaidschanisch|`az`|✓|5\.1.2021|
|Baskisch|`eu`|✓|    |
|Belarussisch|`be`|✓|    |
|Bengali|`bn`|✓|    |
|Bosnisch|`bs`|✓|2020-09-01|
|Bulgarisch|`bg`|✓|    |
|Birmanisch|`my`|✓|    |
|Katalanisch|`ca`|✓|    |
|Khmer|`km`|✓|    |
|Chinesisch|`zh`|✓|    |
|Chinesisch (vereinfacht)|`zh_chs`|✓|    |
|Chinesisch (traditionell)|`zh_cht`|✓|    |
|Korsisch|`co`|✓|5\.1.2021|
|Kroatisch|`hr`|✓|    |
|Tschechisch|`cs`|✓|    |
|Dänisch|`da`|✓|    |
|Dari|`prs`|✓|2020-09-01|
|Divehi|`dv`|✓|    |
|Niederländisch|`nl`|✓|    |
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
|Haiti-Kreolisch|`ht`|✓|    |
|Haussa|`ha`|✓|5\.1.2021|
|Hebräisch|`he`|✓|    |
|Hindi|`hi`|✓|    |
|Hmong Daw|`mww`|✓|2020-09-01|
|Ungarisch|`hu`|✓|    |
|Isländisch|`is`|✓|    |
|Igbo|`ig`|✓|5\.1.2021|
|Indonesisch|`id`|✓|    |
|Inuktitut|`iu`|✓|    |
|Irisch|`ga`|✓|    |
|Italienisch|`it`|✓|    |
|Japanisch|`ja`|✓|    |
|Javanisch|`jv`|✓|5\.1.2021|
|Kannada|`kn`|✓|    |
|Kasachisch|`kk`|✓|2020-09-01|
|Kinyarwanda|`rw`|✓|5\.1.2021|
|Kirgisisch|`ky`|✓|5\.1.2021|
|Koreanisch|`ko`|✓|    |
|Kurdisch|`ku`|✓|    |
|Laotisch|`lo`|✓|    |
|Lateinisch|`la`|✓|    |
|Lettisch|`lv`|✓|    |
|Litauisch|`lt`|✓|    |
|Luxemburgisch|`lb`|✓|5\.1.2021|
|Mazedonisch|`mk`|✓|    |
|Madagassisch|`mg`|✓|2020-09-01|
|Malaiisch|`ms`|✓|    |
|Malayalam|`ml`|✓|    |
|Maltesisch|`mt`|✓|    |
|Maori|`mi`|✓|2020-09-01|
|Marathi|`mr`|✓|2020-09-01|
|Mongolisch|`mn`|✓|5\.1.2021|
|Nepalesisch|`ne`|✓|5\.1.2021|
|Norwegisch|`no`|✓|    |
|Norwegisch Nynorsk|`nn`|✓|    |
|Oriya|`or`|✓|    |
|Paschto|`ps`|✓|    |
|Persisch|`fa`|✓|    |
|Polnisch|`pl`|✓|    |
|Portugiesisch|`pt`|✓|    |
|Pandschabi|`pa`|✓|    |
|Queretaro-Otomi|`otq`|✓|2020-09-01|
|Rumänisch|`ro`|✓|    |
|Russisch|`ru`|✓|    |
|Samoanisch|`sm`|✓|2020-09-01|
|Serbisch|`sr`|✓|    |
|Shona|`sn`|✓|5\.1.2021|
|Sindhi|`sd`|✓|5\.1.2021|
|Singhalesisch|`si`|✓|    |
|Slowakisch|`sk`|✓|    |
|Slowenisch|`sl`|✓|    |
|Somali|`so`|✓|    |
|Spanisch|`es`|✓|    |
|Sundanesisch|`su`|✓|5\.1.2021|
|Suaheli|`sw`|✓|    |
|Schwedisch|`sv`|✓|    |
|Tagalog|`tl`|✓|    |
|Tahitisch|`ty`|✓|2020-09-01|
|Tadschikisch|`tg`|✓|5\.1.2021|
|Tamilisch|`ta`|✓|    |
|Tatarisch|`tt`|✓|5\.1.2021|
|Telugu|`te`|✓|    |
|Thailändisch|`th`|✓|    |
|Tibetisch|`bo`|✓|5\.1.2021|
|Tigrinya|`ti`|✓|5\.1.2021|
|Tongaisch|`to`|✓|2020-09-01|
|Turkmenisch|`tk`|✓|5\.1.2021|
|Xhosa|`xh`|✓|5\.1.2021|
|Yoruba|`yo`|✓|5\.1.2021|
|Zulu|`zu`|✓|5\.1.2021|

---

## <a name="see-also"></a>Weitere Informationen

* [Worum handelt es sich bei der Textanalyse-API?](overview.md)   
