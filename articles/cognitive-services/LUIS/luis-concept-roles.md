---
title: Rollen für Entitäten
titleSuffix: Azure Cognitive Services
description: Rollen sind benannte, kontextabhängige Untertypen einer Entität, die nur in Mustern verwendet wird. In der Äußerung `buy a ticket from New York to London` sind beispielsweise sowohl „New York“ als auch „London“ Städte, die aber jeweils eine andere Bedeutung in dem Satz haben. „New York“ ist die Ausgangsstadt „London“ ist die Zielstadt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 318e71b68bbabeeef34c75a412f9fdd5b6db754a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073023"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Entitätsrollen für kontextabhängige Untertypen

Rollen ermöglichen Entitäten die Verwendung benannter Untertypen. Eine Rolle kann mit allen vordefinierten oder benutzerdefinierten Entitätstypen und sowohl in Beispieläußerungen als auch in Mustern verwendet werden. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Durch maschinelles Lernen erworbenes Entitätsbeispiel für Rollen

In der Äußerung „Kaufe ein Ticket von **New York** nach **London**“ sind sowohl „New York“ als auch „London“ Städte, die aber jeweils eine andere Bedeutung in dem Satz haben. „New York“ ist die Ausgangsstadt „London“ ist die Zielstadt. 

```
buy a ticket from New York to London
```

Rollen benennen diese Unterschiede:

|Entitätstyp|Name der Entität|Rolle|Zweck|
|--|--|--|--|
|Einfach|Location|origin|Start des Flugs|
|Einfach|Location|destination|Ziel des Flugs|

## <a name="non-machine-learned-entity-example-of-roles"></a>Nicht durch maschinelles Lernen erworbenes Entitätsbeispiel für Rollen

In der Äußerung „Plane das Meeting von 8 bis 9 Uhr“ geben beide Zahlen eine Zeit an, die aber jeweils eine andere Bedeutung in der Äußerung haben. Rollen geben den Namen für die Unterschiede an. 

```
Schedule the meeting from 8 to 9
```

|Entitätstyp|Rollenname|Wert|
|--|--|--|
|Vordefinierte datetimeV2|Starttime|8|
|Vordefinierte datetimeV2|Endtime|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>Sind mehrere Entitäten in einer Äußerung dasselbe wie Rollen? 

Mehrere Entitäten können in einer Äußerung existieren und können ohne Verwendung von Rollen extrahiert werden. Wenn der Kontext des Satzes angibt, welche Version der Entität einen Wert anzeigt, dann sollte eine Rolle verwendet werden. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>Verwenden Sie keine Rollen für Duplikate ohne Bedeutung.

Wenn die Äußerung eine Liste von Orten enthält, `I want to travel to Seattle, Cairo, and London.`, ist dies eine Liste, in der jedes Element keine zusätzliche Bedeutung hat. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>Verwenden Sie Rollen, wenn Duplikate die Bedeutung anzeigen.

Wenn die Äußerung eine Liste von Orten mit Bedeutung enthält, `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`, sollte diese Bedeutung von Ursprung, Zwischenstopp und Ziel mit Rollen erfasst werden.

### <a name="roles-can-indicate-order"></a>Rollen können die Reihenfolge angeben.

Wenn sich die Äußerung geändert hat, um die Reihenfolge anzugeben, die Sie extrahieren wollten, `I want to first start with Seattle, second London, then third Cairo`, können Sie auf verschiedene Weise extrahieren. Sie können die Token markieren, die die Rolle kennzeichnen, `first start with`, `second`, `third`. Sie können auch die vorgefertigte Entität **Ordinal** und die vorgefertigte Entität **GeographyV2** in einer gemischten Entität verwenden, um das Konzept von Reihenfolge und Position aufzunehmen. 

## <a name="how-are-roles-used-in-example-utterances"></a>Wie werden Rollen in Beispieläußerungen verwendet?

Wenn eine Entität eine Rolle aufweist und die Entität in einer Beispieläußerung markiert ist, haben Sie die Möglichkeit, nur die Entität oder die Entität zusammen mit der Rolle auszuwählen. 

Im folgenden Beispiel verwenden Äußerungen Entitäten und Rollen:

|Tokenansicht|Entitätsansicht|
|--|--|
|Ich möchte mehr über **Seattle** erfahren.|Ich möchte mehr über {Location} erfahren.|
|Kaufe ein Ticket von Seattle nach New York.|Kaufe ein Ticket von {Location:Origin} nach {Location:Destination}.|

## <a name="how-are-roles-used-in-patterns"></a>Wie werden Rollen in Mustern verwendet?
In der Äußerungsvorlage eines Musters werden Rollen innerhalb der Äußerung verwendet: 

|Muster mit Entitätsrollen|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Rollensyntax in Mustern
Die Entität und die Rolle werden in Klammern `{}` eingeschlossen. Die Entität und die Rolle werden durch einen Doppelpunkt voneinander getrennt. 

## <a name="entity-roles-versus-collaborator-roles"></a>Entitätsrollen im Vergleich zu Projektmitarbeiterrollen

Entitätsrollen gelten für das Datenmodell der LUIS-App. [Projektmitarbeiter](luis-concept-collaborator.md)-Rollen gelten für die Ebenen des Erstellungszugriffs. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie ein [praxisoriertiertes Tutorial](tutorial-entity-roles.md) mithilfe von Entitätsrollen mit nicht durch maschinelles Lernen erworbene Entitäten.
* Erfahren Sie, wie das Hinzufügen von [Rollen](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity) funktioniert.
