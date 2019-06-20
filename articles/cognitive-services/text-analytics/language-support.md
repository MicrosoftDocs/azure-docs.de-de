---
title: Sprachunterstützung – Textanalyse-API
titleSuffix: Azure Cognitive Services
description: 'Eine Liste der von der Textanalyse-API unterstützten natürlichen Sprachen. In diesem Artikel wird erläutert, welche Sprachen für die einzelnen Vorgänge unterstützt werden: Standpunktanalyse, Schlüsselbegriffserkennung, Spracherkennung und Entitätserkennung.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 69c14c6b98b572bc413f5a35696269e13344387e
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417310"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Sprach- und Regionsunterstützung für die Textanalyse-API

In diesem Artikel wird erläutert, welche Sprachen für die einzelnen Vorgänge unterstützt werden: Standpunktanalyse, Schlüsselbegriffserkennung, Spracherkennung und Erkennung der benannten Entität.

## <a name="language-detection"></a>Spracherkennung

Die Textanalyse-API kann eine Vielzahl von Sprachen, Varianten und Dialekten sowie einige Regional- und Kultursprachen erkennen.  Die Spracherkennung gibt das „Skript“ einer Sprache zurück. Für den englischen Satz „I have a dog“ wird beispielsweise `en` anstelle von `en-US` zurückgegeben. Der einzige Sonderfall tritt im Chinesischen auf. Für diese Sprache gibt die Spracherkennungsfunktion `zh_CHS` oder `zh_CHT` zurück, wenn sie das Skript anhand des verfügbaren Texts ermitteln kann. In Situationen, in denen kein bestimmtes Skript für ein Dokument auf Chinesisch ermittelt werden kann, wird nur `zh` zurückgegeben.

Für dieses Feature wird keine genaue Liste mit Sprachen veröffentlicht. Es kann jedoch eine Vielzahl von Sprachen, Varianten und Dialekten sowie einige Regional- und Kultursprachen erkennen. 

Bei Inhalten in einer seltener verwendeten Sprache können Sie die Sprachenerkennung ausprobieren, um zu sehen, ob sie einen Code zurückgibt. Die Antwort bei Sprachen, die nicht erkannt werden können, lautet `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Standpunktanalyse, Schlüsselbegriffserkennung und Erkennung der benannten Entität.

Für die Standpunktanalyse, Schlüsselbegriffserkennung und Entitätserkennung ist die Liste der unterstützten Sprachen selektiver, da die Analysetools auf die sprachlichen Regeln zusätzlicher Sprachen ausgelegt sind.

## <a name="language-list-and-status"></a>Liste und Status der Sprachen

Die Sprachunterstützung wird in der Vorschauversion eingeführt und soll allmählich in die allgemein verfügbare Version integriert werden. Die beiden Versionen sind unabhängig voneinander und vom Textanalysedienst im Allgemeinen. Sprachen können selbst dann noch ausschließlich für die Vorschauversion verfügbar sein, wenn eine Version der Textanalyse-API allgemein verfügbar ist.

| Sprache    | Sprachcode | Stimmung | Schlüsselwörter | Erkennung benannter Entitäten |   Notizen  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| Arabisch      | `ar`          |           |             | ✔ \*                     | |
| Tschechisch       | `cs`          |           |             | ✔ \*                     | |
| Chinesisch (vereinfacht) | `zh-CN`|           |             | ✔ \*        |    |
| Dänisch      | `da`          | ✔ \*     | ✔           | ✔ \*            |     |
| Niederländisch       | `nl`          | ✔ \*     | ✔          |  ✔ \*           |     |
| Englisch     | `en`          | ✔        | ✔           |  ✔ \*\*     |      |
| Finnisch     | `fi`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Französisch      | `fr`          | ✔        | ✔           |  ✔ \*           |     |
| Deutsch      | `de`          | ✔ \*     | ✔           |  ✔ \*          |     |
| Griechisch       | `el`          | ✔ \*     |             |            |     |
| Ungarisch   | `hu`          |           |             |  ✔ \*          |     | 
| Italienisch     | `it`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Japanisch    | `ja`          |          | ✔           |  ✔ \*          |     |
| Koreanisch      | `ko`          |          | ✔           |  ✔ \*          |     |
| Norwegisch (Bokmål) | `no`  | ✔ \*     |  ✔          | ✔ \*            |     |
| Polnisch      | `pl`          | ✔ \*     |  ✔          |  ✔ \*           |     |
| Portugiesisch (Portugal) | `pt-PT`| ✔        |  ✔          | ✔ \*      |`pt` wird ebenfalls akzeptiert.|
| Portugiesisch (Brasilien)   | `pt-BR`|          |  ✔   |  ✔ \*       |     |
| Russisch     | `ru`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Spanisch     | `es`          | ✔        |            |   ✔ \*\*      |     | 
| Schwedisch     | `sv`          | ✔ \*     | ✔           |   ✔ \*          |     |
| Türkisch     | `tr`          | ✔ \*     |             |   ✔ \*          |  |

\* Die Sprachunterstützung befindet sich in der Vorschauversion.

\*\* Die Erkennung der bekannten Entität und die [Entitätsverknüpfung](how-tos/text-analytics-how-to-entity-linking.md) sind jeweils für diese Sprache verfügbar.    

## <a name="see-also"></a>Weitere Informationen

[Dokumentationsseite zu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)   
[Produktseite zu Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
