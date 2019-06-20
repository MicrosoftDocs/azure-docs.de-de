---
title: Überwachen der Azure Event Grid-Nachrichtenübermittlung
description: Beschreibt, wie die Übermittlung von Azure Event Grid-Nachrichten überwacht wird.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: spelluru
ms.openlocfilehash: fdd18b833794c25cb90188ba8bc418d4785492ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60824158"
---
# <a name="monitor-event-grid-message-delivery"></a>Überwachen der Event Grid-Nachrichtenübermittlung 

Dieser Artikel beschreibt, wie Sie im Portal den Status von Ereignisübermittlungen einsehen können.

Event Grid bietet permanente Übermittlung. Jede Nachricht wird für jedes Abonnement mindestens einmal übermittelt. Ereignisse werden sofort an den registrierten Webhook des jeweiligen Abonnements gesendet. Wenn ein Webhook den Eingang eines Ereignisses nicht innerhalb von 60 Sekunden nach dem ersten Übermittlungsversuch bestätigt, wiederholt Event Grid die Übermittlung des Ereignisses.

Informationen zu Ereignisübermittlungen und Wiederholungen finden Sie unter [Event Grid – Übermittlung und Wiederholung von Nachrichten](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Übermittlungsmetriken

Das Portal zeigt Metriken für den Status der Übermittlung von Ereignisnachrichten an.

Für Themen gibt es diese Metriken:

* **Veröffentlichung erfolgreich**: Das Ereignis wurde erfolgreich an das Thema gesendet und mit einer Antwort des Typs 2xx verarbeitet.
* **Fehler beim Veröffentlichen**: Das Ereignis wurde an das Thema gesendet, aber mit einem Fehlercode abgelehnt.
* **Ohne Übereinstimmung**: Das Ereignis wurde erfolgreich im Thema veröffentlicht, stimmt aber mit keinem Ereignisabonnement überein. Das Ereignis wurde gelöscht.

Für Abonnements gibt es diese Metriken:

* **Übermittlung erfolgreich**: Das Ereignis wurde erfolgreich an den Endpunkt des Abonnements übermittelt und hat eine Antwort des Typs 2xx erhalten.
* **Übermittlungsfehler**: Das Ereignis wurde erfolgreich an den Endpunkt des Abonnements übermittelt, hat aber eine Antwort des Typs 4xx oder 5xx erhalten.
* **Abgelaufene Ereignisse**: Das Ereignis wurde nicht übermittelt, und alle Wiederholungsversuche wurden gesendet. Das Ereignis wurde gelöscht.
* **Übereinstimmende Ereignisse**: Das Ereignis im Thema stimmt mit dem Ereignisabonnement überein.

## <a name="event-subscription-status"></a>Status des Ereignisabonnements

Wenn Sie Metriken für ein Ereignisabonnement anzeigen möchten, können Sie entweder nach Abonnementtyp oder nach Abonnements für eine bestimmte Ressource suchen.

Um nach Ereignisabonnementtyp zu suchen, wählen Sie **Alle Dienste** aus.

![Auswählen von „Alle Dienste“](./media/monitor-event-delivery/all-services.png)

Suchen Sie nach **Event Grid**, und wählen Sie die Option **Event Grid-Abonnements** aus.

![Suchen nach Ereignisabonnements](./media/monitor-event-delivery/search-and-select.png)

Filtern Sie nach dem Typ des Ereignisses, des Abonnements und des Standorts. Wählen Sie **Metriken** für das Abonnement aus, um diese anzuzeigen.

![Filtern von Ereignisabonnements](./media/monitor-event-delivery/filter-events.png)

Zeigen Sie die Metriken für das Ereignisthema und Abonnement an.

![Anzeigen von Ereignismetriken](./media/monitor-event-delivery/subscription-metrics.png)

Um nach den Metriken für eine bestimmte Ressource zu suchen, wählen Sie die entsprechende Ressource aus. Wählen Sie dann **Ereignisse** aus.

![Auswählen von Ereignissen für eine Ressource](./media/monitor-event-delivery/select-events.png)

Daraufhin werden die Metriken für die Abonnements für diese Ressource angezeigt.

## <a name="custom-event-status"></a>Benutzerdefinierter Ereignisstatus

Wenn Sie ein benutzerdefiniertes Thema veröffentlicht haben, können Sie die Metriken dafür anzeigen. Wählen Sie die Ressourcengruppe für das Thema aus, und wählen Sie dann das Thema aus.

![Auswählen eines benutzerdefinierten Themas](./media/monitor-event-delivery/select-custom-topic.png)

Zeigen Sie die Metriken für das benutzerdefinierte Ereignisthema an.

![Anzeigen von Ereignismetriken](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu Ereignisübermittlungen und Wiederholungen finden Sie unter [Event Grid – Übermittlung und Wiederholung von Nachrichten](delivery-and-retry.md).
* Eine Einführung in Event Grid finden Sie unter [Informationen zu Event Grid](overview.md).
* Um sich schnell mit der Verwendung von Event Grid vertraut zu machen, lesen Sie [Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit Azure Event Grid](custom-event-quickstart.md).
