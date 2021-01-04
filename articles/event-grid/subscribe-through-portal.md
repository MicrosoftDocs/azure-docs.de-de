---
title: Azure Event Grid-Abonnements über das Portal
description: In diesem Artikel wird beschrieben, wie Sie Event Grid Abonnements für die unterstützten Quellen mithilfe des Azure-Portals erstellen, z. B. für Azure Blob Storage.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e80e2243c93ab38187646256f567d6ab73c40100
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995062"
---
# <a name="subscribe-to-events-through-portal"></a>Abonnieren von Ereignissen über das Portal

In diesem Artikel wird beschrieben, wie Event Grid-Abonnements über das Portal erstellt werden.

## <a name="create-event-subscriptions"></a>Erstellen von Ereignisabonnements

Befolgen Sie die folgenden Schritte, um ein Event Grid-Abonnement für jede der unterstützten [Ereignisquellen](overview.md#event-sources) zu erstellen. Dieser Artikel zeigt, wie ein Event Grid-Abonnement für ein Azure-Abonnement erstellt wird.

1. Wählen Sie **Alle Dienste** aus.

   ![Auswählen von „Alle Dienste“](./media/subscribe-through-portal/select-all-services.png)

1. Suchen Sie nach **Event Grid-Abonnements**, und wählen Sie diese Option aus den verfügbaren Optionen aus.

   ![Screenshot der Suche im Azure-Portal, in dem „Event Grid-Abonnements“ ausgewählt ist](./media/subscribe-through-portal/search.png)

1. Klicken Sie auf **+ Ereignisabonnement**.

   ![Abonnement hinzufügen](./media/subscribe-through-portal/add-subscription.png)

1. Wählen Sie den Typ des zu erstellenden Abonnements aus. Wählen Sie **Azure-Abonnement** und das Zielabonnement aus, wenn Sie z.B. Ereignisse für Ihr Azure-Abonnement abonnieren möchten.

   ![Azure-Abonnement auswählen](./media/subscribe-through-portal/azure-subscription.png)

1. Lassen Sie die Option **Alle Ereignistypen abonnieren** aktiviert, um alle Ereignistypen für diese Ereignisquelle zu abonnieren. Wählen Sie andernfalls die Ereignistypen für dieses Abonnement aus.

   ![Ereignistypen auswählen](./media/subscribe-through-portal/select-event-types.png)

1. Stellen Sie zusätzliche Details zum Ereignisabonnement bereit, wie etwa den Endpunkt für die Behandlung von Ereignissen sowie einen Abonnementnamen.

   ![Screenshot, der die Abschnitte „Endpunktdetails“ und „Ereignisabonnementdetails“ mit einem eingegebenen Wert für den Abonnementnamen zeigt.](./media/subscribe-through-portal/provide-subscription-details.png)

1. Wählen Sie **Zusätzliche Features** aus, um die Verarbeitung unzustellbarer Nachrichten zu aktivieren und Wiederholungsrichtlinien anzupassen.

   ![Auswählen zusätzlicher Features](./media/subscribe-through-portal/select-additional-features.png)

1. Wählen Sie einen Container zum Speichern von nicht zugestellten Ereignissen aus, und legen Sie fest, wie Wiederholungen gesendet werden.

   ![Aktivieren der Verarbeitung von unzustellbaren Nachrichten und Wiederholung](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Wählen Sie abschließend die Option **Erstellen**.

## <a name="create-subscription-on-resource"></a>Erstellen eines Abonnements für die Ressource

Einige Ereignisquellen unterstützen die Erstellung eines Ereignisabonnements über die Portalschnittstelle für diese Ressource. Wählen Sie die Ereignisquelle aus, und suchen Sie im linken Bereich nach **Ereignisse**.

![Abonnementdetails bereitstellen](./media/subscribe-through-portal/resource-events.png)

Das Portal zeigt die Optionen zum Erstellen eines Ereignisabonnements an, das für diese Quelle relevant ist.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu Ereignisübermittlungen und Wiederholungen finden Sie unter [Event Grid – Übermittlung und Wiederholung von Nachrichten](delivery-and-retry.md).
* Eine Einführung in Event Grid finden Sie unter [Informationen zu Event Grid](overview.md).
* Um sich schnell mit der Verwendung von Event Grid vertraut zu machen, lesen Sie [Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit Azure Event Grid](custom-event-quickstart.md).
