---
title: Sprachunterstützung – maschinelles Sehen
titleSuffix: Azure Cognitive Services
description: Eine Liste der von den Maschinelles Sehen-Features unterstützten natürlichen Sprachen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1a70d1b2ea504d0ccfba925810a2d19d0c7583cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60759604"
---
# <a name="language-support-for-computer-vision"></a>Sprachunterstützung für maschinelles Sehen

Einige Features des maschinellen Sehens unterstützen mehrere Sprachen; alle hier nicht erwähnten Features unterstützen nur Englisch.

## <a name="text-recognition"></a>Texterkennung

Maschinelles Sehen kann Text in zahlreichen Sprachen erkennen. Insbesondere die [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)-API unterstützt eine Vielzahl von Sprachen, während die [Lesen](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)-API und [Texterkennungs](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)-API nur Englisch unterstützen. Unter [Erkennen von gedrucktem und handschriftlichem Text](concept-recognizing-text.md) finden Sie weitere Informationen zu dieser Funktionalität und den Vorteilen der einzelnen APIs.

OCR erkennt automatisch die Sprache des eingegebenen Materials, sodass kein Sprachcode im API-Aufruf angegeben werden muss. Sprachcodes werden jedoch immer als Wert des `"language"`-Knotens in der JSON-Antwort zurückgegeben.

|Sprache| Sprachcode | OCR-API |
|:-----|:----:|:-----:|
|Arabisch | `ar`|✔ |
|Chinesisch (vereinfacht) | `zh-Hans`|✔ |
|Chinesisch (traditionell) | `zh-Hant`|✔ |
|Tschechisch | `cs` |✔ |
|Dänisch | `da` |✔ |
|Niederländisch | `nl` |✔ |
|Englisch | `en` |✔ |
|Finnisch | `fi` |✔ |
|Französisch | `fr` |✔ |
|Deutsch | `de` |✔ |
|Griechisch | `el` |✔ |
|Ungarisch | `hu` |✔ |
|Italienisch | `it` |✔ |
|Japanisch | `ja` |✔ |
|Koreanisch | `ko` |✔ |
|Norwegisch | `nb` |✔ |
|Polnisch | `pl` |✔ |
|Portugiesisch | `pt` |✔ |
|Rumänisch | `ro` |✔ |
|Russisch | `ru` |✔ |
|Serbisch (Kyrillisch) | `sr-Cyrl` |✔ |
|Serbisch (Lateinisch) | `sr-Latn` |✔ |
|Slowakisch | `sk` |✔ |
|Spanisch | `es` |✔ |
|Schwedisch | `sw` |✔ |
|Türkisch | `tr` |✔ |

## <a name="image-analysis"></a>Bildanalyse

Einige Aktionen der [Bildanalyse](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)-API können Ergebnisse in anderen Sprachen zurückgeben, angegeben mit dem `language`-Abfrageparameter. Andere Aktionen geben unabhängig davon, welche Sprache angegeben ist, Ergebnisse in englischer Sprache zurück, und andere lösen bei nicht unterstützten Sprachen eine Ausnahme aus. Aktionen werden mit dem `visualFeatures`- und `details`-Abfrageparameter angegeben; in der [Übersicht](home.md) finden Sie eine Liste mit allen Aktionen, die Sie mit der Bildanalyse durchführen können.

|Sprache | Sprachcode | Categories | `Tags` | BESCHREIBUNG | Erwachsene | Marken | Farbe | Gesichtserkennung | ImageType | Objekte | Prominente | Besondere Merkmale |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chinesisch | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Englisch | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japanisch | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugiesisch | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Spanisch | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Nächste Schritte

Mit den in diesem Handbuch erwähnten Features für maschinelles Sehen können Sie die ersten Schritte ausführen.

* [Analysieren eines lokalen Bilds (REST)](./quickstarts/csharp-analyze.md)
* [Extrahieren von gedrucktem Text (REST)](./quickstarts/csharp-print-text.md)