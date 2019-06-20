---
title: Datenkonvertierung
titleSuffix: Language Understanding - Azure Cognitive Services
description: Erfahren Sie, wie Äußerungen vor der Vorhersage in Language Understanding (LUIS) geändert werden können.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: diberry
ms.openlocfilehash: a148c849d0935978f049e01dd254c4c18800ee3b
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496981"
---
# <a name="convert-data-format-of-utterances"></a>Konvertieren des Datenformats von Äußerungen
LUIS verwendet den Cognitive Services-Sprachdienst zum Konvertieren von Äußerungen aus gesprochenen Äußerungen in Textäußerungen vor der Vorhersage. 

## <a name="speech-to-intent-conversion-concepts"></a>Konzepte zur Sprache-Absichts-Umsetzung
Die Sprache-Absichts-Umsetzung in LUIS ermöglicht es Ihnen, gesprochene Äußerungen an einen Endpunkt zu senden und als Antwort eine LUIS-Vorhersage zu erhalten. Dieser Vorgang stellt eine Integration des [Sprachverständnis](https://docs.microsoft.com/azure/cognitive-services/Speech)-Diensts in LUIS dar. Erfahren Sie mehr über die Sprache-Absichts-Umsetzung in einem [Tutorial](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Schlüsselanfoderungen
Sie brauchen für diese Integration keinen Schlüssel für eine **Bing-Sprach-API** zu erstellen. Ein im Azure-Portal erstellter **Language Understanding**-Schlüssel funktioniert bei dieser Integration. Verwenden Sie nicht den LUIS-Starterschlüssel.

### <a name="pricing-tier"></a>Preisstufe
Für diese Integration gilt ein anderes [Preismodell](luis-boundaries.md#key-limits) als für die üblichen Language Understanding-Tarife. 

### <a name="quota-usage"></a>Kontingentverbrauch
Informationen finden Sie unter [Schlüsselgrenzwerte](luis-boundaries.md#key-limits). 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Extrahieren von Daten](luis-concept-data-extraction.md)

