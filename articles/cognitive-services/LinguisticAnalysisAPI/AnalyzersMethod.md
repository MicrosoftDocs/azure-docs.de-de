---
title: Analyzers-Methode – API für die linguistische Analyse
titlesuffix: Azure Cognitive Services
description: Die REST-API des Analysetools enthält eine Liste von Analysemodulen, die derzeit von der API für die linguistische Analyse unterstützt werden.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 06/30/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 1b33b60f674eebb15fdc6112e1d630b93b98494b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60404716"
---
# <a name="analyzers-method"></a>analyzers-Methode

> [!IMPORTANT]
> Die Vorschauversion für die linguistische Analyse wurde am 9. August 2018 außer Betrieb genommen. Es wird empfohlen, [Azure Machine Learning-Textanalysemodule](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) für die Textverarbeitung und -analyse zu verwenden.

Die **analyzers**-REST-API enthält eine Liste der Analysetools, die derzeit vom Dienst unterstützt werden.
Die Antwort enthält ihre [Namen](Analyzer-Names.md) und die jeweils unterstützten Sprachen (z. B. „en“ für Englisch).

## <a name="request-parameters"></a>Anforderungsparameter
Keine

<br>

## <a name="response-parameters"></a>Antwortparameter

NAME | Type | BESCHREIBUNG
-----|------|--------------
languages | Liste von Zeichenfolgen | Liste von aus zwei Buchstaben bestehenden ISO-Sprachcodes, für die das Analysetool verwendet werden können
id   | Zeichenfolge | eindeutige ID für dieses Analysetool
kind | Zeichenfolge | der allgemeine Typ des verwendeten Analysetools
specification | Zeichenfolge | der Name der für dieses Analysetool verwendeten Spezifikation
implementation | Zeichenfolge | Beschreibung des Modells und/oder des Algorithmus hinter diesem Analysetool

<br>

## <a name="example"></a>Beispiel
GET /analyzers

Antwort: JSON
```json
[
    {
        "id": "22A6B758-420F-4745-8A3C-46835A67C0D2",
        "languages": ["en"],
        "kind": "Constituency_Tree",  
        "specification": "PennTreebank3",
        "implementation": "SplitMerge"
    },
    {
        "id" : "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
        "languages": ["en"],
        "kind": "POS_Tags",
        "specification": "PennTreebank3",
        "implementation": "cmm"
    },
    {
        "id" : "08EA174B-BFDB-4E64-987E-602F85DA7F72",
        "languages": ["en"],
        "kind": "Tokens",
        "specification":"PennTreebank3",
        "implementation": "regexes"
    }
]
```
