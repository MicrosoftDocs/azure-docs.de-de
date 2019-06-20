---
title: Attribute der Forschungsbereichsentität – Academic Knowledge-API
titlesuffix: Azure Cognitive Services
description: Erfahren Sie mehr über die Attribute, die Sie für die Forschungsbereichsentität in der Academic Knowledge-API verwenden können.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: e9d6badf76efd03c0520a728af7b3e47b25f200a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61339584"
---
# <a name="field-of-study-entity"></a>Forschungsbereichsentität

<sub> * Die folgenden Attribute sind spezifisch für die Forschungsbereichsentität. (Ty = '6') </sub>

NAME    |BESCHREIBUNG                            |Type       | Vorgänge
------- | ------------------------------------- | --------- | ----------------------------
id      |Entitäts-ID                              |Int64      |Equals
FN      |Normalisierter Name des Forschungsbereichs         |string     |Equals
DFN     |Anzeigename des Forschungsbereichs            |string     |none
CC      |Gesamtzitatanzahl des Forschungsbereichs    |Int32      |none  
ECC     |Geschätzte Gesamtzitatanzahl des Forschungsbereichs|Int32      |none
FL      |Ebene in der Hierarchie des Forschungsbereichs     |Int32      |Equals, <br/>IsBetween
FP.FN   |Name für übergeordneten Forschungsbereich             |string     |Equals
FP.FId  |ID für übergeordneten Forschungsbereich               |Int64      |Equals
FC.FN   |Name für untergeordneten Forschungsbereich              |string     |Equals
FC.FId  |ID für untergeordneten Forschungsbereich                |Int64      |Equals
