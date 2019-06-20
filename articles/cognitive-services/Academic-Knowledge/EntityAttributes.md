---
title: Attribute der Academic Graph-Entität – Academic Knowledge-API
titlesuffix: Azure Cognitive Services
description: Erfahren Sie mehr über die Entitätsattribute, die Sie mit einer Academic Graph-Instanz in der Academic Knowledge-API verwenden können.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 485775660ecfdf2291365ab98c9188295ea2cbde
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340178"
---
# <a name="entity-attributes"></a>Entitätsattribute

Der Academic Graph ist aus sieben Entitätstypen. Alle Entitäten weisen eine Entitäts-ID und einen Entitätstyp auf.

## <a name="common-entity-attributes"></a>Allgemeine Entitätsattribute
NAME    |BESCHREIBUNG                |Type       | Vorgänge
------- | ------------------------- | --------- | ----------------------------
id      |Entitäts-ID                  |Int64      |Equals
Ty      |Entitätstyp                |enum   |Equals

## <a name="entity-type-enum"></a>Entitätstyp „enum“
NAME                                                            |value
----------------------------------------------------------------|-----
[Dokument](PaperEntityAttributes.md)                               |0
[Autor](AuthorEntityAttributes.md)                             |1
[Journal](JournalEntityAttributes.md)                           |2
[Konferenzreihe](JournalEntityAttributes.md)                 |3
[Konferenzinstanz](ConferenceInstanceEntityAttributes.md)    |4
[Zuordnung](AffiliationEntityAttributes.md)                   |5
[Forschungsbereich](FieldsOfStudyEntityAttributes.md)                      |6

