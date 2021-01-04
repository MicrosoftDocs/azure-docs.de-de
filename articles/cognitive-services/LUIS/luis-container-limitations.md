---
title: 'Einschränkungen für Container: LUIS'
titleSuffix: Azure Cognitive Services
description: Die unterstützten LUIS-Container Sprachen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c76e45f09a0f432d2775eb19d3dcaa668294e6f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002336"
---
# <a name="language-understanding-luis-container-limitations"></a>Einschränkungen für Language Understanding-Container (LUIS)

Die LUIS-Container weisen eine Reihe von beachtenswerten Einschränkungen auf. Von nicht unterstützten Abhängigkeiten bis zu einer Teilmenge der unterstützten Sprachen befasst sich dieser Artikel im Detail mit diesen Einschränkungen.

## <a name="supported-dependencies-for-latest-container"></a>Unterstützte Abhängigkeiten für `latest`-Container

Vom aktuellen LUIS-Container wird Folgendes unterstützt:

* [Neue vordefinierte Bereiche](luis-reference-prebuilt-domains.md): Zu diesen auf Unternehmen fokussierten Domänen gehören Entitäten, Beispieläußerungen und Muster. Erweitern Sie diese Domänen für Ihre eigenen Zwecke.

## <a name="unsupported-dependencies-for-latest-container"></a>Nicht unterstützte Abhängigkeiten für `latest`-Container

Für das [Exportieren von Containern](luis-container-howto.md#export-packaged-app-from-luis) müssen Sie nicht unterstützte Abhängigkeiten aus Ihrer LUIS-App entfernen. Wenn Sie den Export für Container versuchen, meldet das LUIS-Portal, dass Sie diese nicht unterstützten Features entfernen müssen.

Sie können eine LUIS-Anwendung verwenden, sofern diese **keine** der folgenden Abhängigkeiten enthält:

Nicht unterstützte App-Konfigurationen|Details|
|--|--|
|Nicht unterstützte Containerkulturen| Die Sprachen Niederländisch (`nl-NL`), Japanisch (`ja-JP`) und Deutsch (`de-DE`) werden nur mit dem [Tokenizer 1.0.2](luis-language-support.md#custom-tokenizer-versions) unterstützt.|
|Nicht unterstützte Entitäten für alle Kulturen|Vordefinierte [KeyPhrase](luis-reference-prebuilt-keyphrase.md)-Entität für alle Kulturen|
|Nicht unterstützte Entitäten für die Kultur Englisch (`en-US`)|Vordefinierte [GeographyV2](luis-reference-prebuilt-geographyV2.md)-Entitäten|
|Sprachvorbereitung|Externe Abhängigkeiten werden im Container nicht unterstützt.|
|Stimmungsanalyse|Externe Abhängigkeiten werden im Container nicht unterstützt.|
|Bing-Rechtschreibprüfung|Externe Abhängigkeiten werden im Container nicht unterstützt.|

## <a name="languages-supported"></a>Unterstützte Sprachen

LUIS-Container unterstützen eine Teilmenge der von LUIS insgesamt [unterstützten Sprachen](luis-language-support.md#languages-supported). Die LUIS-Container sind imstande, Äußerungen in den folgenden Sprachen zu verstehen:

| Sprache | Gebietsschema | Vordefinierte Domäne | Vordefinierte Entität | Ausdrucklistenempfehlungen | \**[Textanalyse](../text-analytics/language-support.md)<br>(Stimmung und<br>Schlüsselwörter)|
|--|--|:--:|:--:|:--:|:--:|
| Englisch (USA) | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| Arabisch (Vorschau, modernes Hocharabisch) |`ar-AR`|❌|❌|❌|❌|
| *[Chinesisch](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Französisch (Frankreich) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Französisch (Kanada) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Deutsch |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Italienisch |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Koreanisch |`ko-KR` | ✔️ | ❌ | ❌ | Nur *Schlüsselausdruck* |
| Marathi | `mr-IN`|❌|❌|❌|❌|
| Portugiesisch (Brasilien) |`pt-BR` | ✔️ | ✔️ | ✔️ | Nicht alle Unterkulturen |
| Spanisch (Spanien) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Spanisch (Mexiko)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Tamilisch | `ta-IN`|❌|❌|❌|❌|
| Telugu | `te-IN`|❌|❌|❌|❌|
| Türkisch | `tr-TR` |✔️| ❌ | ❌ | Nur *Stimmung* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]
