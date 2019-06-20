---
title: Unterstützte Sprachen – Sprachübersetzungs-API
titlesuffix: Azure Cognitive Services
description: Zeigen Sie die von der Sprachübersetzungs-API unterstützten Sprachen an.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 3/5/2018
ms.author: v-jansko
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 513fd93f148a8a133901a5dbb60722fbc4a7d9e7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64866251"
---
# <a name="languages-supported-by-the-translator-speech-api"></a>Von der Sprachübersetzungs-API unterstützte Sprachen

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Die Sprachübersetzung wird für folgende Sprachen unterstützt. Wenn beide Sprachen für die Sprachübersetzung unterstützt werden, ist die Sprache-zu-Sprache- oder die Sprache-zu-Text-Übersetzung verfügbar. Wenn die Zielsprache nicht für die Sprachübersetzung unterstützt wird, ist nur die Sprache-zu-Text-Übersetzung verfügbar.

| Sprache    |
|:----------- |
| Arabisch (modernes Hocharabisch)      |
| Portugiesisch (Brasilien)     |
| Chinesisch (Mandarin)      |
| Englisch      |
| Französisch      |
| Deutsch      |
| Italienisch      |
| Japanisch      |
| Russisch      |
| Spanisch      |

Die Sprachübersetzungs-API unterstützt die folgenden Sprachen als Zielsprachen für eine Spracherkennungsübersetzungen.

| Sprache für Textausgabe    | Sprachcode |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabisch       | `ar`          |
| Bengalisch      | `bn`          |
| Bosnisch (Lateinisch)      | `bs`          |
| Bulgarisch      | `bg`          |
| Chinesisch (traditionell)      | `yue`          |
| Katalanisch      | `ca`          |
| Chinesisch (vereinfacht)      | `zh-Hans`          |
| Chinesisch (traditionell)      | `zh-Hant`          |
| Kroatisch      | `hr`          |
| Tschechisch      | `cs`          |
| Dänisch      | `da`          |
| Niederländisch      | `nl`          |
| Englisch      | `en`          |
| Estnisch      | `et`          |
| Fidschi      | `fj`          |
| Filipino      | `fil`          |
| Finnisch      | `fi`          |
| Französisch      | `fr`          |
| Deutsch      | `de`          |
| Griechisch      | `el`          |
| Haitianisches Kreolisch      | `ht`          |
| Hebräisch      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Ungarisch      | `hu`          |
|Isländisch|`is`          |
| Indonesisch      | `id`          |
| Italienisch      | `it`          |
| Japanisch      | `ja`          |
| Suaheli      | `sw`          |
| Klingonisch      | `tlh`          |
| Klingonisch (plqaD)      | `tlh-Qaak`          |
| Koreanisch      | `ko`          |
| Lettisch      | `lv`          |
| Litauisch      | `lt`          |
| Madagassisch      | `mg`          |
| Malaiisch      | `ms`          |
| Maltesisch      | `mt`          |
| Norwegisch      | `nb`          |
| Persisch      | `fa`          |
| Polnisch      | `pl`          |
| Portugiesisch      | `pt`          |
| Queretaro-Otomi      | `otq`          |
| Rumänisch      | `ro`          |
| Russisch      | `ru`          |
| Samoanisch      | `sm`          |
| Serbisch (Kyrillisch)      | `sr-Cyrl`          |
| Serbisch (Lateinisch)      | `sr-Latn`          |
| Slowakisch     | `sk`          |
| Slowenisch      | `sl`          |
| Spanisch      | `es`          |
| Schwedisch      | `sv`          |
| Tahitisch      | `ty`          |
| Tamilisch      | `ta`          |
| Thailändisch      | `th`          |
| Tongaisch      | `to`          |
| Türkisch      | `tr`          |
| Ukrainisch      | `uk`          |
| Urdu      | `ur`          |
| Vietnamesisch      | `vi`          |
| Walisisch      | `cy`          |
| Yukatekisches Maya      | `yua`          |

## <a name="access-the-list-programmatically"></a>Programmgesteuertes Zugreifen auf die Liste

Mithilfe der Ressource „languages“ können Sie programmgesteuert auf die Liste der unterstützten Sprachen zugreifen. Die Liste enthält den Sprachcode sowie den Sprachnamen in Englisch oder in jeder anderen unterstützten Sprache. Sobald neue Sprachen verfügbar sind, wird diese Liste automatisch vom Sprachübersetzungsdienst aktualisiert.

Die Ressource „languages“ gibt die Liste der unterstützten Sprachen für Sprache, Text und Sprachsynthese zurück. Die Ressource „languages“ erfordert keine Authentifizierung.

[Besuchen Sie die API-Referenz, um die languages-Methode zu testen.](languages-reference.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Zugreifen auf die Liste auf der Microsoft Translator-Website

Für einen schnellen Überblick über die Sprachen werden auf der Microsoft Translator-Website alle Sprachen angezeigt, die von der Translator-Text-API und -Sprach-API unterstützt werden. Diese Liste enthält keine entwicklerspezifischen Informationen wie z.B. Sprachcodes.

[Die Liste der Sprachen](https://www.microsoft.com/translator/languages.aspx)
