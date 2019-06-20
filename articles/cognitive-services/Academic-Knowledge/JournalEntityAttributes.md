---
title: Attribute der Journalentität – Academic Knowledge-API
titlesuffix: Azure Cognitive Services
description: Erfahren Sie mehr über die Attribute, die Sie für die Journalentität in der Academic Knowledge-API in Cognitive Services verwenden können.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ffb159dc684b4b6663dcb966706d4745ab88a403
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61337804"
---
# <a name="journal-entity"></a>Journalentität

<sub> * Die folgenden Attribute sind spezifisch für die Journalentität. (Ty = '2') </sub>

NAME    |BESCHREIBUNG                            |Type       | Vorgänge
------- | ------------------------------------- | --------- | ----------------------------
id      |Entitäts-ID                              |Int64      |Equals
DJN     |Normalisierter Journalname                |string     |none
JN      |Journalanzeigename                   |string     |Equals
CC      |Gesamtzitatanzahl des Journals           |Int32      |none  
ECC     |Geschätzte Gesamtzitatanzahl des Journals |Int32      |none
