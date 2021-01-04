---
title: 'Azure Service Bus: Anhalten von Messagingentitäten'
description: In diesem Artikel wird erläutert, wie Sie Azure Service Bus-Messagingentitäten (Warteschlangen, Themen und Abonnements) vorübergehend anhalten und reaktivieren.
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: ea1acab3d0a86b0064f8b3eef7bfd1496bd17041
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543050"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Anhalten und Reaktivieren von Messagingentitäten (deaktiviert)

Warteschlangen, Themen und Abonnements können vorübergehend angehalten werden. Das Anhalten versetzt die Entität in einen deaktivierten Zustand, in dem alle Nachrichten im Speicher beibehalten werden. Es können jedoch keine Nachrichten entfernt oder hinzugefügt werden, und die entsprechenden Protokollvorgänge führen zu Fehlern.

Möglicherweise möchten Sie eine Entität aus dringenden administrativen Gründen anhalten. Ein fehlerhafter Empfänger nimmt z. B. Nachrichten aus der Warteschlange, verarbeitet diese nicht, vervollständigt die Nachrichten jedoch falsch und entfernt sie. In diesem Fall können Sie die Warteschlange für Empfangsvorgänge deaktivieren, bis Sie den Code korrigiert haben und bereitstellen. 

Das Anhalten oder Reaktivieren kann entweder vom Benutzer oder vom System durchgeführt werden. Das System hält Entitäten nur aufgrund von schwerwiegenden administrativen Gründen an, z. B. das Erreichen des Ausgabenlimits des Abonnements. Vom System deaktivierte Entitäten können nicht vom Benutzer reaktiviert werden, sondern werden wiederhergestellt, wenn die Ursache des Anhaltens behandelt wurde.

## <a name="queue-status"></a>Warteschlangenstatus 
Folgende Zustände können für eine **Warteschlange** festgelegt werden:

-   **Aktiv:** Die Warteschlange ist aktiv. Sie können Nachrichten an die Warteschlange senden und aus dieser empfangen. 
-   **Disabled**: Die Warteschlange wurde angehalten. Dies entspricht dem Festlegen von **SendDisabled** und **ReceiveDisabled**. 
-   **SendDisabled**: Sie können keine Nachrichten an die Warteschlange senden, aber Sie können Nachrichten aus der Warteschlange empfangen. Sie erhalten eine Ausnahme, wenn Sie versuchen, Nachrichten an die Warteschlange zu senden. 
-   **ReceiveDisabled**: Sie können Nachrichten an die Warteschlange senden, aber Sie können keine Nachrichten aus der Warteschlange empfangen. Sie erhalten eine Ausnahme, wenn Sie versuchen, Nachrichten aus der Warteschlange zu empfangen.


### <a name="change-the-queue-status-in-the-azure-portal"></a>Ändern des Warteschlangenstatus im Azure-Portal: 

1. Navigieren Sie im Azure-Portal zu Ihrem Service Bus-Namespace. 
1. Wählen Sie die Warteschlange aus, deren Status Sie ändern möchten. Die Warteschlangen werden im unteren Bereich in der Mitte angezeigt. 
1. Auf der Seite **Service Bus-Warteschlange** wird der aktuelle Status der Warteschlange als Hyperlink angezeigt. Wenn die **Übersicht** nicht im linken Menü ausgewählt ist, wählen Sie diese Option aus, um den Status der Warteschlange anzuzeigen. Wählen Sie den aktuellen Status der Warteschlange aus, um ihn zu ändern. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="Auswählen des Warteschlangenstatus":::
4. Wählen Sie den neuen Status für die Warteschlange und dann **OK** aus. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="Festlegen des Warteschlangenstatus":::
    
Sie können die Vorgänge „Senden“ und „Empfangen“ auch deaktivieren, indem Sie die [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager)-APIs von Service Bus im .NET SDK oder mit einer Azure Resource Manager-Vorlage über die Azure CLI oder Azure PowerShell verwenden.

### <a name="change-the-queue-status-using-azure-powershell"></a>Ändern des Warteschlangenstatus mithilfe von Azure PowerShell
Der PowerShell-Befehl zum Deaktivieren einer Warteschlange wird im folgenden Beispiel gezeigt. Der Befehl für die Reaktivierung ist identisch und legt `Status` auf **Active** fest.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>Themenstatus
Sie können den Themastatus im Azure-Portal ändern. Wählen Sie den aktuellen Status des Themas aus, um die folgende Seite anzuzeigen, auf der Sie den Status ändern können. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="Ändern des Themenstatus":::

Folgende Status können für ein **Thema** festgelegt werden:
- **Aktiv:** Das Thema ist aktiv. Sie können Nachrichten an das Thema senden. 
- **Disabled**: Das Thema wurde angehalten. Sie können keine Nachrichten an das Thema senden. 
- **SendDisabled**: Dieselbe Auswirkung wie **Deaktiviert**. Sie können keine Nachrichten an das Thema senden. Sie erhalten eine Ausnahme, wenn Sie versuchen, Nachrichten an das Thema zu senden. 

## <a name="subscription-status"></a>Abonnementstatus
Sie können den Abonnementstatus im Azure-Portal ändern. Wählen Sie den aktuellen Status des Abonnements aus, um die folgende Seite anzuzeigen, auf der Sie den Status ändern können. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="Ändern des Abonnementstatus":::

Folgende Status können für ein **Abonnement** festgelegt werden:
- **Aktiv:** Das Abonnement ist aktiv. Sie können Nachrichten aus dem Abonnement empfangen.
- **Disabled**: Das Abonnement wurde angehalten. Sie können keine Nachrichten aus dem Abonnement empfangen. 
- **ReceiveDisabled**: Dieselbe Auswirkung wie **Deaktiviert**. Sie können keine Nachrichten aus dem Abonnement empfangen. Sie erhalten eine Ausnahme, wenn Sie versuchen, Nachrichten aus dem Abonnement zu empfangen.

| Themenstatus | Abonnementstatus | Verhalten | 
| ------------ | ------------------- | -------- | 
| Aktiv | Aktiv | Sie können Nachrichten an das Thema senden und aus dem Abonnement empfangen. | 
| Aktiv | Deaktiviert oder Empfangen deaktiviert | Sie können Nachrichten an das Thema senden, aber keine Nachrichten aus dem Abonnement empfangen. | 
| Deaktiviert oder Senden deaktiviert | Aktiv | Sie können keine Nachrichten an das Thema senden, aber Nachrichten aus dem Abonnement empfangen, die sich bereits im Abonnement befinden. | 
| Deaktiviert oder Senden deaktiviert | Deaktiviert oder Empfangen deaktiviert | Sie können keine Nachrichten an das Thema senden und auch nicht aus dem Abonnement empfangen. | 

## <a name="other-statuses"></a>Andere Status
Die Enumeration [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) definiert ebenfalls eine Reihe von Übergangszuständen, die nur vom System festgelegt werden können. 


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Service Bus-Messaging finden Sie in folgenden Themen:

* [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Verwenden von Service Bus-Themen und -Abonnements](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

