---
title: Anzeigen von Azure Event Grid-Metriken und Festlegen von Warnungen
description: In diesem Artikel wird beschrieben, wie Sie im Azure-Portal Metriken für Azure Event Grid-Themen und Abonnements anzeigen und Warnungen hierfür erstellen.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 518d34d39e6fbecc408fe9a44d899fe4745d60d0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008955"
---
# <a name="monitor-event-grid-message-delivery"></a>Überwachen der Event Grid-Nachrichtenübermittlung 
In diesem Artikel wird beschrieben, wie Sie über das Portal Metriken für Event Grid-Themen und Abonnements anzeigen und Warnungen hierfür erstellen. 

## <a name="metrics"></a>Metriken

Das Portal zeigt Metriken für den Status der Übermittlung von Ereignisnachrichten an.

In den folgenden Themen finden Sie einige der Metriken:

* **Veröffentlichung erfolgreich**: Das Ereignis wurde erfolgreich an das Thema gesendet und mit einer Antwort des Typs 2xx verarbeitet.
* **Fehler beim Veröffentlichen**: Das Ereignis wurde an das Thema gesendet, aber mit einem Fehlercode abgelehnt.
* **Ohne Übereinstimmung**: Das Ereignis wurde erfolgreich im Thema veröffentlicht, stimmt aber mit keinem Ereignisabonnement überein. Das Ereignis wurde gelöscht.

Für Abonnements sind hier einige Metriken aufgeführt:

* **Übermittlung erfolgreich**: Das Ereignis wurde erfolgreich an den Endpunkt des Abonnements übermittelt und hat eine Antwort des Typs 2xx erhalten.
* **Übermittlungsfehler**: Jedes Mal, wenn der Dienst eine Übermittlung versucht und der Ereignishandler keinen 2xx-Erfolgscode zurückgibt, wird der Zähler **Übermittlungsfehler** erhöht. Wenn Sie versuchen, dasselbe Ereignis mehrmals zu übermitteln, und dies fehlschlägt, wird der Zähler **Übermittlungsfehler** für jeden Fehler erhöht.
* **Abgelaufene Ereignisse**: Das Ereignis wurde nicht übermittelt, und alle Wiederholungsversuche wurden gesendet. Das Ereignis wurde gelöscht.
* **Übereinstimmende Ereignisse**: Das Ereignis im Thema stimmt mit dem Ereignisabonnement überein.

    > [!NOTE]
    > Die vollständige Liste der Metriken finden Sie unter [Von Azure Event Grid unterstützte Metriken](metrics.md).

## <a name="view-custom-topic-metrics"></a>Anzeigen von Metriken für benutzerdefinierte Themen

Wenn Sie ein benutzerdefiniertes Thema veröffentlicht haben, können Sie die Metriken dafür anzeigen. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Geben Sie in der Suchleiste des Themas **Event Grid-Themen** ein, und wählen Sie dann **Event Grid-Themen** aus der Dropdownliste aus. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Event Grid-Themen suchen und auswählen":::
3. Wählen Sie in der Themenliste das benutzerdefinierte Thema aus. 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="Benutzerdefiniertes Thema auswählen":::
4. Zeigen Sie die Metriken für das benutzerdefinierte Ereignisthema auf der Seite **Event Grid-Thema** an. In der folgenden Abbildung ist der Abschnitt **Grundlagen**, in dem die Ressourcengruppe, das Abonnement usw. angezeigt werden, minimiert. 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="Ereignismetriken anzeigen":::

Auf der Registerkarte **Metriken** auf der Seite **Event Grid-Thema** können Sie Diagramme mit unterstützten Metriken erstellen.

:::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="Seite „Themen“ – „Metriken“":::

Weitere Informationen zu Metriken finden Sie unter [Metriken in Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).

Beispiel: Metrikendiagramm für die Metrik **Veröffentlichte Ereignisse**.

:::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="Metrik „Veröffentlichte Ereignisse“":::


## <a name="view-subscription-metrics"></a>Anzeigen von Metriken für Abonnements
1. Navigieren Sie zur Seite **Event Grid-Thema**, indem Sie die Schritte aus dem vorherigen Abschnitt ausführen. 
2. Wählen Sie das Abonnement im unteren Bereich aus, wie im folgenden Beispiel gezeigt. 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="Ereignisabonnement auswählen":::    

    Sie können auch in der Suchleiste des Azure-Portals nach **Event Grid-Abonnements** suchen und **Thementyp**, **Abonnement** und **Speicherort** auswählen, um ein Ereignisabonnement anzuzeigen. 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="Ereignisabonnement auf der Seite „Event Grid-Abonnements“ auswählen":::        

    Wählen Sie für benutzerdefinierte Themen **Event Grid-Themen** unter **Thementyp** aus. Wählen Sie für Systemthemen den Typ der Azure-Ressource aus, z. B. **Speicherkonten (BLOB, GPv2)** . 
3. Die Metriken für das Abonnement werden auf der Homepage für das Abonnement in einem Diagramm angezeigt. Sie können die Metriken **Allgemein**, **Fehler**, **Latenz** und **Unzustellbare Nachrichten** für die letzte Stunde, 6 Stunden, 12 Stunden, 1 Tag, 7 Tage und 30 Tage anzeigen. 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="Metriken auf der Homepage des Abonnements":::    

## <a name="view-system-topic-metrics"></a>Anzeigen von Systemthemenmetriken

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Geben Sie in der Suchleiste des Themas **Event Grid-Systemthemen** ein, und wählen Sie dann **Event Grid-Systemthemen** aus der Dropdownliste aus. 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Event Grid-Systemthemen suchen und auswählen":::
3. Wählen Sie in der Themenliste das Systemthema aus. 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="Systemthema auswählen":::
4. Zeigen Sie die Metriken für das Systemthema auf der Seite **Event Grid-Thema** an. In der folgenden Abbildung ist der Abschnitt **Grundlagen**, in dem die Ressourcengruppe, das Abonnement usw. angezeigt werden, minimiert. 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="Metriken für Systemthemen auf der Übersichtsseite anzeigen":::

Auf der Registerkarte **Metriken** auf der Seite **Event Grid-Thema** können Sie Diagramme mit unterstützten Metriken erstellen.

:::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="Seite „Systemthema“ – „Metriken“":::

Weitere Informationen zu Metriken finden Sie unter [Metriken in Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

- Informationen zum Erstellen von Warnungen zu Metriken und Aktivitätsprotokollvorgängen finden Sie im [Artikel zum Festlegen von Warnungen](set-alerts.md).
- Informationen zu Ereignisübermittlungen und Wiederholungen finden Sie unter [Event Grid – Übermittlung und Wiederholung von Nachrichten](delivery-and-retry.md).
