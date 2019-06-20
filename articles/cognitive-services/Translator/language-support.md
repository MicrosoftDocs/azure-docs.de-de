---
title: Sprachunterstützung – Textübersetzungs-API
titleSuffix: Azure Cognitive Services
description: Eine Liste der von der Textübersetzungs-API unterstützten natürlichen Sprachen.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/04/2019
ms.author: v-pawal
ms.openlocfilehash: 924324b11f49a50bfb5f00e117b33c0cc572e3bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514979"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Sprach- und Regionsunterstützung für die Textübersetzungs-API

Die Textübersetzungs-API unterstützt die folgenden Sprachen für die Übersetzung von Texten. Die neuronale maschinelle Übersetzung (NMT) ist der neue Standard für qualitativ hochwertige, auf künstlicher Intelligenz basierende, maschinelle Übersetzungen und steht standardmäßig in V3 der Translator-Text-API bereit, wenn ein neuronales System verfügbar ist.

[Weitere Informationen zur Funktionsweise von maschineller Übersetzung](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Sprachübersetzung

**V2 der Textübersetzungs-API**

> [!NOTE]
> V2 wurde am 30. April 2018 eingestellt. Migrieren Sie Ihre Anwendungen zu V3, um die Vorteile der neuen Funktionen zu nutzen, die ausschließlich in V3 verfügbar sind.

* Nur statistisches System: Für diese Sprache ist kein neuronales System verfügbar.
* Neuronales System verfügbar: Es ist ein neuronales System verfügbar. Verwenden Sie den Parameter `category=generalnn`, um auf das neuronale System zuzugreifen.
* Neuronales System als Standard: Das neuronale System ist als standardmäßiges Übersetzungssystem festgelegt. Verwenden Sie den Parameter `category=smt`, um auf das statistische System zur Verwendung mit Microsoft Translator Hub zuzugreifen.
* Nur neuronales System: Es ist nur die neuronale Übersetzung verfügbar.

**V3 der Textübersetzungs-API**: Version 3 der Textübersetzungs-API verwendet standardmäßig das neuronale System. Die statistischen Systeme sind nur verfügbar, wenn keine neuronalen Systeme vorhanden sind.

> [!NOTE]
> Derzeit ist eine Teilmenge der neuronalen Sprachen in Custom Translator verfügbar und es werden nach und nach zusätzliche Sprachen hinzugefügt. [Zeigen Sie die zurzeit verfügbaren Sprachen im benutzerdefinierten Translator an](#customization).

|Sprache|  Sprachcode|  V2-API| V3-API|
|:-----|:-----:|:-----|:-----|
|Afrikaans| `af`    |Nur statistisches System|  Neuronal|
|Arabisch|    `ar`    |Neuronales System verfügbar|  Neuronal|
|Bengalisch|    `bn`    |Neuronales System verfügbar|  Neuronal|
|Bosnisch (Lateinisch)|   `bs`    |Neuronales System verfügbar|  Neuronal|
|Bulgarisch| `bg`    |Neuronales System verfügbar|  Neuronal|
|Chinesisch (traditionell)|   `yue`   |Nur statistisches System|  Statistisch|
|Katalanisch|   `ca`    |Nur statistisches System|  Statistisch|
|Chinesisch (vereinfacht)|    `zh-Hans`   |Neuronales System als Standard |Neuronal|
|Chinesisch (traditionell)|   `zh-Hant`   |Neuronales System als Standard |Neuronal|
|Kroatisch|  `hr`    |Neuronales System verfügbar|  Neuronal|
|Tschechisch| `cs`    |Neuronales System verfügbar|  Neuronal|
|Dänisch|    `da`    |Neuronales System verfügbar   |Neuronal|
|Niederländisch| `nl`    |Neuronales System verfügbar|  Neuronal|
|Englisch|   `en`    |Neuronales System verfügbar|  Neuronal|
|Estnisch|  `et`    |Neuronales System verfügbar|  Neuronal|
|Fidschi|    `fj`    |Nur statistisches System|  Statistisch|
|Filipino|  `fil`   |Nur statistisches System|  Statistisch|
|Finnisch|   `fi`    |Neuronales System verfügbar|  Neuronal|
|Französisch|    `fr`    |Neuronales System verfügbar|  Neuronal|
|Deutsch|    `de`    |Neuronales System verfügbar|  Neuronal|
|Griechisch| `el`    |Neuronales System verfügbar|  Neuronal|
|Haitianisches Kreolisch|    `ht`    |Nur statistisches System   |Statistisch|
|Hebräisch |`he`   |Neuronales System verfügbar   |Neuronal|
|Hindi| `hi`    |Neuronales System als Standard|    Neuronal|
|Hmong Daw| `mww`   |Nur statistisches System|  Statistisch|
|Ungarisch| `hu`    |Neuronales System verfügbar|  Neuronal|
|Isländisch| `is`    |Nur neuronales System|   Neuronal|
|Indonesisch|    `id`    |Nur statistisches System|  Statistisch|
|Italienisch|   `it`    |Neuronales System verfügbar|  Neuronal|
|Japanisch|  `ja`    |Neuronales System verfügbar|  Neuronal|
|Suaheli| `sw`    |Nur statistisches System|  Statistisch|
|Klingonisch|   `tlh`   |Nur statistisches System|  Statistisch|
|Klingonisch (plqaD)|   `tlh-Qaak`  |Nur statistisches System|  Statistisch|
|Koreanisch |`ko`   |Neuronales System verfügbar|  Neuronal|
|Lettisch|   `lv`    |Neuronales System verfügbar|  Neuronal|
|Litauisch|    `lt`    |Neuronales System verfügbar|  Neuronal|
|Madagassisch|  `mg`    |Nur statistisches System|  Statistisch|
|Malaiisch| `ms`    |Nur statistisches System   |Statistisch|
|Maltesisch|   `mt`    |Nur statistisches System|  Statistisch|
|Norwegisch| `nb`    |Neuronales System verfügbar|  Neuronal|
|Persisch|   `fa`    |Nur statistisches System|  Statistisch|
|Polnisch|    `pl`    |Neuronales System verfügbar|  Neuronal|
|Portugiesisch|    `pt`    |Neuronales System verfügbar|  Neuronal|
|Queretaro-Otomi|   `otq`   |Nur statistisches System|  Statistisch|
|Rumänisch|  `ro`    |Neuronales System verfügbar|  Neuronal|
|Russisch|   `ru`    |Neuronales System verfügbar|  Neuronal|
|Samoanisch|    `sm`    |Nur statistisches System|  Statistisch|
|Serbisch (Kyrillisch)|    `sr-Cyrl`   |Nur statistisches System|  Statistisch|
|Serbisch (Lateinisch)|   `sr-Latn`   |Nur statistisches System   |Statistisch|
|Slowakisch|    `sk`    |Neuronales System verfügbar|  Neuronal|
|Slowenisch| `sl`    |Neuronales System verfügbar|  Neuronal|
|Spanisch|   `es`    |Neuronales System verfügbar|  Neuronal|
|Schwedisch|   `sv`    |Neuronales System verfügbar   |Neuronal|
|Tahitisch|  `ty`    |Nur statistisches System|  Statistisch|
|Tamilisch| `ta`    |Nur statistisches System|  Statistisch|
|Telugu|    `te`    |Nur neuronales System|   Neuronal|
|Thailändisch|  `th`    |Neuronales System verfügbar|  Neuronal|
|Tongaisch|    `to`    |Nur statistisches System|  Statistisch|
|Türkisch|   `tr`    |Neuronales System verfügbar   |Neuronal|
|Ukrainisch| `uk`    |Neuronales System verfügbar|  Neuronal|
|Urdu|  `ur`    |Nur statistisches System|  Statistisch|
|Vietnamesisch|    `vi`    |Neuronales System verfügbar|  Neuronal|
|Walisisch| `cy`    |Neuronales System verfügbar|  Neuronal|
|Yukatekisches Maya|  `yua`   |Nur statistisches System|  Statistisch|

## <a name="transliteration"></a>Transliteration

Die „Transliterate“-Methode unterstützt die folgenden Sprachen. In der Spalte „Von/In“ wird durch „<-->“ angezeigt, dass für die Sprache eine Transliteration beider Skripts in das jeweils andere erfolgen kann. Mit „-->“ wird angezeigt, dass für die Sprache nur eine Transliteration von einem Skript in das andere möglich ist.

| Sprache    | Sprachcode | Skript | Von/In | Skript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabisch | `ar` | Arabisch `Arab` | <--> | Latein `Latn` |
|Bengalisch  | `bn` | Bangla `Beng` | <--> | Latein `Latn` |
| Chinesisch (vereinfacht) | `zh-Hans` | Chinesisch (vereinfacht) `Hans`| <--> | Latein `Latn` |
| Chinesisch (vereinfacht) | `zh-Hans` | Chinesisch (vereinfacht) `Hans`| <--> | Chinesisch (traditionell) `Hant`|
| Chinesisch (traditionell) | `zh-Hant` | Chinesisch (traditionell) `Hant`| <--> | Latein `Latn` |
| Chinesisch (traditionell) | `zh-Hant` | Chinesisch (traditionell) `Hant`| <--> | Chinesisch (vereinfacht) `Hans` |
| Gujarati | `gu`  | Gujarati `Gujr` | --> | Latein `Latn` |
| Hebräisch | `he` | Hebräisch `Hebr` | <--> | Latein `Latn` |
| Hindi | `hi` | Devanagari `Deva` | <--> | Latein `Latn` |
| Japanisch | `ja` | Japanisch `Jpan` | <--> | Latein `Latn` |
| Kannada | `kn` | Kannada `Knda` | --> | Latein `Latn` |
| Malayalam | `ml` | Malayalam `Mlym` | --> | Latein `Latn` |
| Marathi | `mr` | Devanagari `Deva` | --> | Latein `Latn` |
| Oriya | `or` | Oriya `Orya` | <--> | Latein `Latn` |
| Pandschabi | `pa` | Gurmukhi `Guru`  | <--> | Latein `Latn`  |
| Serbisch (Kyrillisch) | `sr-Cyrl` | Kyrillisch `Cyrl`  | --> | Latein `Latn` |
| Serbisch (Lateinisch) | `sr-Latn` | Latein `Latn` | --> | Kyrillisch `Cyrl`|
| Tamilisch | `ta` | Tamilisch `Taml` | --> | Latein `Latn` |
| Telugu | `te` | Telugu `Telu` | --> | Latein `Latn` |
| Thailändisch | `th` | Thailändisch `Thai` | <--> | Latein `Latn` |

## <a name="dictionary"></a>Wörterbuch

Das Wörterbuch unterstützt die Übertragung der folgenden Sprachen in das oder aus dem Englischen anhand der „Lookup“- und „Examples“-Methode.

| Sprache    | Sprachcode |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabisch       | `ar`          |
| Bengalisch      | `bn`          |
| Bosnisch (Lateinisch)      | `bs`          |
| Bulgarisch      | `bg`          |
| Katalanisch      | `ca`          |
| Chinesisch (vereinfacht)      | `zh-Hans`          |
| Kroatisch      | `hr`          |
| Tschechisch      | `cs`          |
| Dänisch      | `da`          |
| Niederländisch      | `nl`          |
| Estnisch      | `et`          |
| Finnisch      | `fi`          |
| Französisch      | `fr`          |
| Deutsch      | `de`          |
| Griechisch      | `el`          |
| Haitianisches Kreolisch      | `ht`          |
| Hebräisch      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Ungarisch      | `hu`          |
| Isländisch    | `is`  |
| Indonesisch      | `id`          |
| Italienisch      | `it`          |
| Japanisch      | `ja`          |
| Suaheli      | `sw`          |
| Klingonisch      | `tlh`          |
| Koreanisch      | `ko`          |
| Lettisch      | `lv`          |
| Litauisch      | `lt`          |
| Malaiisch      | `ms`          |
| Maltesisch      | `mt`          |
| Norwegisch      | `nb`          |
| Persisch      | `fa`          |
| Polnisch      | `pl`          |
| Portugiesisch      | `pt`          |
| Rumänisch      | `ro`          |
| Russisch      | `ru`          |
| Serbisch (Lateinisch)      | `sr-Latn`          |
| Slowakisch     | `sk`          |
| Slowenisch      | `sl`          |
| Spanisch      | `es`          |
| Schwedisch      | `sv`          |
| Tamilisch      | `ta`          |
| Thailändisch      | `th`          |
| Türkisch      | `tr`          |
| Ukrainisch      | `uk`          |
| Urdu      | `ur`          |
| Vietnamesisch      | `vi`          |
| Walisisch      | `cy`          |

## <a name="detect"></a>Detect

Die Textübersetzungs-API erkennt alle verfügbaren Sprachen für die Übersetzung und Transliteration.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Programmgesteuerter Zugriff auf die Textübersetzungs-API-Sprachliste

Sie können eine Liste der unterstützten Sprachen für die Textübersetzungs-API v3. 0 mit der Sprachen-Methode abrufen. Sie können die Liste nach Funktion, Sprachcode sowie Sprachname in Englisch oder jeder anderen unterstützten Sprache anzeigen. Sobald neue Sprachen verfügbar gemacht werden, wird diese Liste automatisch vom Microsoft Translator-Dienst aktualisiert.

[Anzeigen der Referenzdokumentation zum Vorgang „Languages“](reference/v3-0-languages.md)

## <a name="customization"></a>Anpassung

Die folgenden Sprachen sind für die Anpassung an das Englische oder aus dem Englischen mit dem [benutzerdefinierten Translator](https://aka.ms/CustomTranslator) verfügbar.

| Sprache    | Sprachcode |
|:----------- |:-------------:|
| Arabisch       | `ar`          |
| Bengalisch      | `bn`          |
| Bosnisch (Lateinisch)      | `bs`          |
| Bulgarisch      | `bg`          |
| Chinesisch (vereinfacht)      | `zh-Hans`          |
|Chinesisch (traditionell)|   `zh-Hant`   |
| Kroatisch      | `hr`          |
| Tschechisch      | `cs`          |
| Dänisch      | `da`          |
| Niederländisch      | `nl`          |
| Englisch    | `en`     |
| Estnisch      | `et`          |
| Finnisch      | `fi`          |
| Französisch      | `fr`          |
| Deutsch      | `de`          |
| Griechisch      | `el`          |
| Hebräisch      | `he`          |
| Hindi      | `hi`          |
| Ungarisch      | `hu`          |
| Isländisch | `is` |
| Indonesisch|   `id`    |
| Italienisch      | `it`          |
| Japanisch      | `ja`          |
|Suaheli| `sw`    |
| Koreanisch      | `ko`          |
| Lettisch      | `lv`          |
| Litauisch      | `lt`          |
|Madagassisch|  `mg`    |
| Norwegisch      | `nb`          |
| Polnisch      | `pl`          |
| Portugiesisch      | `pt`          |
| Rumänisch      | `ro`          |
| Russisch      | `ru`          |
|Samoanisch|    `sm`    |
| Serbisch (Lateinisch)      | `sr-Latn`          |
| Slowakisch     | `sk`          |
| Slowenisch      | `sl`          |
| Spanisch      | `es`          |
| Schwedisch      | `sv`          |
| Thailändisch      | `th`          |
| Türkisch      | `tr`          |
| Ukrainisch      | `uk`          |
| Vietnamesisch      | `vi`          |
| Walisisch | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Zugreifen auf die Liste auf der Microsoft Translator-Website

Für einen schnellen Überblick über die Sprachen werden auf der Microsoft Translator-Website alle Sprachen angezeigt, die von der Translator-Text-API und -Sprach-API unterstützt werden. Diese Liste enthält keine entwicklerspezifischen Informationen wie z.B. Sprachcodes.

[Anzeige der Sprachenliste](https://www.microsoft.com/translator/languages.aspx)
