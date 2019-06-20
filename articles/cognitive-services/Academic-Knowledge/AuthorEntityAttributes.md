---
title: Attribute der Autorentität – Academic Knowledge-API
titlesuffix: Azure Cognitive Services
description: Erfahren Sie mehr über die Attribute, die Sie für die Autorentität in der Academic Knowledge-API verwenden können.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: d4b33c06ab023023aadf403cf0ef0b08c2bafc5f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60609757"
---
# <a name="author-entity"></a>Autorentität
<sub> * Die folgenden Attribute sind spezifisch für die Autorentität. (Ty = '1') </sub>

NAME    |BESCHREIBUNG                            |Type       | Vorgänge
------- | ------------------------------------- | --------- | ----------------------------
id      |Entitäts-ID                              |Int64      |Equals
AuN     |Normalisierter Autorname                 |string     |Equals
DAuN    |Autoranzeigename                    |string     |none
CC      |Gesamtzitatanzahl des Autors            |Int32      |none  
ECC     |Geschätzte Gesamtzitatanzahl des Autors  |Int32      |none
E       |Erweiterte Metadaten (siehe Tabelle „Erweiterte Metadatenattribute“)  |string     |none  


## <a name="extended-metadata-attributes"></a>Erweiterte Metadatenattribute ##

NAME    | BESCHREIBUNG               
--------|---------------------------    
LKA.Afn     | Der mit dem Autoren verknüpfte Anzeigename der Zuordnung  
LKA.AfId        | Die mit dem Autoren verknüpfte Entitäts-ID der Zuordnung
