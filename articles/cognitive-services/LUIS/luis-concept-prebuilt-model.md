---
title: Vordefinierte Modelle – LUIS
titleSuffix: Azure Cognitive Services
description: Vordefinierte Modelle stellen Domänen, Absichten, Entitäten und Äußerungen bereit. Sie können Ihre App mit einer vordefinierten Domäne starten oder ihr später eine geeignete Domäne hinzufügen.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 6642e59c2957b298d54bc587853752b9fce74686
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95019142"
---
# <a name="prebuilt-models"></a>Vordefinierte Modelle

Vordefinierte Modelle stellen Domänen, Absichten, Entitäten und Äußerungen bereit. Sie können Ihre App mit einem vordefinierten Modell starten oder ihr später ein geeignetes Modell hinzufügen. 

## <a name="types-of-prebuilt-models"></a>Typen von vordefinierten Modellen

LUIS bietet drei Typen von vordefinierten Modellen. Jedes Modell kann Ihrer App jederzeit hinzugefügt werden. 

|Modelltyp|Includes|
|--|--|
|[Domäne](luis-reference-prebuilt-domains.md)|Absichten, Äußerungen, Entitäten|
|Absichten|Absichten, Äußerungen|
|[Entitäten](luis-reference-prebuilt-entities.md)|Nur Entitäten| 

## <a name="prebuilt-domains"></a>Vordefinierte Domänen

Language Understanding Intelligent Service (LUIS) bietet *vordefinierte Domänen*. Dabei handelt es sich um vortrainierte Modelle von [Absichten](luis-how-to-add-intents.md) und [Entitäten](luis-concept-entity-types.md), die für Domänen oder allgemeine Kategorien von Clientanwendungen zusammenarbeiten. 

Die vordefinierten Domänen wurden trainiert und können Ihrer LUIS-App direkt hinzugefügt werden. Die Absichten und Entitäten einer vordefinierten Domäne sind nach dem Hinzufügen zu einer App vollständig anpassbar. 

> [!TIP]
> Die Absichten und Entitäten in einer vordefinierten Domäne funktionieren am besten zusammen. Es ist besser, nach Möglichkeit Absichten und Entitäten aus derselben Domäne zu kombinieren.
> In der vordefinierten Domäne „Utilities“ finden Sie Absichten, die Sie für die Verwendung in beliebigen Domänen anpassen können. Beispielsweise können Sie Ihrer App `Utilities.Repeat` hinzufügen und für die Aktionen trainieren, die Benutzer in Ihrer Anwendung wiederholen möchten. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Ändern des Verhaltens der Absicht einer vordefinierten Domäne

Möglicherweise enthält eine vordefinierte Domäne eine Absicht, die einer Absicht, die Sie in Ihrer LUIS-App haben möchten, ähnelt, Sie möchten das Verhalten jedoch ändern. Beispielsweise enthält die vordefinierte Domäne **Places** die Absicht `MakeReservation` zum Durchführen einer Restaurantreservierung, aber Ihre App soll mit dieser Absicht Hotelreservierungen vornehmen. In diesem Fall können Sie das Verhalten der Absicht ändern, indem Sie Beispieläußerungen zum Tätigen von Hotelreservierungen zur Absicht hinzufügen und dann die App nochmal trainieren. 

Eine vollständige Auflistung der vordefinierten Domänen finden Sie in der [Referenz zu vordefinierten Domänen](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Vordefinierte Absichten

LUIS bietet vordefinierte Absichten mit Äußerungen für alle vordefinierten Domänen. Absichten können auch ohne die gesamte Domäne hinzugefügt werden. Beim Hinzufügen einer Absicht werden Ihrer App die Absicht selbst und ihre Äußerungen hinzugefügt. Sowohl der Name der Absicht als auch die Liste der Äußerungen können geändert werden.  

## <a name="prebuilt-entities"></a>Vordefinierte Entitäten

LUIS umfasst eine Reihe von vordefinierten Entitäten zur Erkennung allgemeiner Informationstypen, z.B. Datumsangaben, Zeitangaben, Zahlen, Maße und Währungen. Die Unterstützung von vordefinierten Entität variiert je nach Kultur Ihrer LUIS-App. Eine vollständige Liste vordefinierter Entitäten, die von LUIS unterstützt werden, einschließlich der Unterstützung durch die Kultur, finden Sie unter [Referenz zu vordefinierten Entitäten](./luis-reference-prebuilt-entities.md).

Wenn Ihre Anwendung eine vordefinierte Entität enthält, werden ihre Vorhersagen in der veröffentlichten Anwendung eingeschlossen. Das Verhalten vordefinierter Entitäten ist vortrainiert und kann **nicht** angepasst werden. 

> [!NOTE]
> Die Entität **builtin.datetime** ist veraltet. Sie wurde durch [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md) ersetzt. Diese stellt die Erkennung von Datums- und Zeitbereichen sowie verbesserte Erkennung mehrdeutiger Datums- und Zeitangaben bereit.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Ihrer App [vordefinierte Entitäten hinzufügen](./howto-add-prebuilt-models.md).