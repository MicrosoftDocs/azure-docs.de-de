---
title: Automatische Weiterleitung von Azure Service Bus-Messagingentitäten
description: In diesem Artikel wird beschrieben, wie Sie eine Azure Service Bus-Warteschlange oder ein Abonnement mit einer anderen Warteschlange oder einem anderen Thema verketten.
ms.topic: article
ms.date: 04/23/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 78fb7478e0584d7c6dc79829d4bb242a448d43bd
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/25/2021
ms.locfileid: "107988692"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Verketten von Service Bus-Entitäten mit automatischer Weiterleitung

Mit dem Service Bus-Feature *Automatische Weiterleitung* können Sie eine Warteschlange oder ein Abonnement mit einer weiteren Warteschlange oder einem Thema aus dem selben Namespace verketten. Wenn die automatische Weiterleitung aktiviert ist, entfernt Service Bus die Nachrichten automatisch, die in der ersten Warteschlange oder dem Abonnement (Quelle) platziert wurden, und fügt sie in die zweite Warteschlange oder das Thema (Ziel) ein. Es ist weiterhin möglich, eine Nachricht direkt an die Zielentität zu senden.

> [!NOTE]
> Der Basic-Tarif von Service Bus unterstützt die automatische Weiterleitung nicht. Die Standard- und Premium-Tarife unterstützen das Feature. Informationen zu den Unterschieden zwischen diesen Tarifen finden Sie unter [Service Bus-Preise](https://azure.microsoft.com/pricing/details/service-bus/).

Die Zielentität muss vorhanden sein, wenn die Quellentität erstellt wird. Wenn die Zielentität nicht vorhanden ist, gibt Service Bus eine Ausnahme zurück, wenn die Quellentität erstellt werden soll.

## <a name="scenarios"></a>Szenarien

### <a name="scale-out-an-individual-topic"></a>Individuelle Themen skalieren
Sie können die automatische Weiterleitung zum Aufskalieren eines einzelnen Themas verwenden. Service Bus beschränkt die [Anzahl von Abonnements für ein bestimmtes Thema](service-bus-quotas.md) auf 2.000. Sie können weitere Abonnements durch Erstellen von Themen der zweiten Ebene aufnehmen. Selbst wenn Sie nicht durch die Service Bus-Beschränkung für die Anzahl der Abonnements gebunden sind, kann das Hinzufügen einer zweiten Ebene von Themen den Gesamtdurchsatz Ihres Themas verbessern.

![Abbildung eines Szenarios mit automatischer Weiterleitung, die eine Nachricht zeigt, die durch ein Auftragsthema verarbeitet wird, das zu einem von drei Auftragsthemen der zweiten Ebene verzweigt werden kann.][0]

### <a name="decouple-message-senders-from-receivers"></a>Entkoppeln Sie Nachrichtenabsender und -empfänger
Sie können die automatische Weiterleitung auch verwenden, um Nachrichtenabsender von den Empfängern zu entkoppeln. Betrachten Sie z. B. ein ERP-System, das aus drei Modulen besteht: Auftragsverarbeitung, Bestandsverwaltung und Kundenbeziehungsmanagement. Jedes dieser Module generiert Nachrichten, die in ein entsprechendes Thema aufgenommen werden. Alice und Bob sind Vertriebsmitarbeiter, die an allen Nachrichten interessiert sind, die sich auf ihre Kunden beziehen. Zum Empfangen dieser Nachrichten erstellen Alice und Bob eine persönliche Warteschlange und ein Abonnement für jedes der ERP-Themen, das automatisch alle Nachrichten an ihre Warteschlange weiterleitet.

![Abbildung eines Szenarios mit automatischer Weiterleitung, in dem drei Verarbeitungsmodule Nachrichten über drei entsprechende Themen an zwei separate Warteschlangen senden.][1]

Wenn Alice im Urlaub ist, wird ihre persönliche Warteschlange gefüllt, nicht das ERP-Thema. Da in diesem Szenario kein Vertriebsmitarbeiter Nachrichten empfangen hat, erreicht keines der ERP-Themen je das Kontingent.

> [!NOTE]
> Wenn automatische Weiterleitung eingerichtet ist, wird der Wert für AutoDeleteOnIdle für **die Quelle und das Ziel** automatisch auf den Maximalwert des Datentyps festgelegt.
> 
>   - Auf der Quellseite fungiert die automatische Weiterleitung als Empfangsvorgang. Die Quelle, die für automatische Weiterleitung eingerichtet ist, befindet sich also niemals wirklich „im Leerlauf“.
>   - Auf der Zielseite erfolgt dies, um sicherzustellen, dass immer ein Ziel vorhanden ist, an das die Nachricht weitergeleitet werden kann.

## <a name="autoforwarding-considerations"></a>Überlegungen bei der automatischen Weiterleitung

Wenn die Zielentität zu viele Nachrichten gesammelt hat und das Kontingent überschreitet oder wenn die Zielentität deaktiviert ist, fügt die Quellentität die Nachrichten zur eigenen [Warteschlange für unzustellbare Nachrichten](service-bus-dead-letter-queues.md) hinzu, bis Speicherplatz im Ziel vorhanden ist (oder die Entität wieder aktiviert wird). Diese Nachrichten sind weiterhin in der Warteschlange für unzustellbare Nachrichten aktiv, daher müssen Sie sie explizit aus der Warteschlange für unzustellbare Nachrichten empfangen und verarbeiten.

Beim Verketteten einzelner Themen, um ein kombiniertes Thema mit vielen Abonnements zu erhalten, wird empfohlen, dass Sie eine moderate Anzahl von Abonnements für das Thema der ersten Ebene und viele Abonnements für Themen der zweiten Ebene nutzen. Beispiel: Ein Thema der ersten Ebene mit 20 Abonnements, von denen jedes mit einem Thema der zweiten Ebene mit 200 Abonnements verkettet ist, ermöglicht einen höheren Durchsatz als ein Thema der ersten Ebene mit 200 Abonnements, von denen jedes mit einem Thema der zweiten Ebene mit 20 Abonnements verkettet ist.

Service Bus rechnet einen Vorgang für jede weitergeleitete Nachricht ab. Beispiel: Das Senden einer Nachricht an ein Thema mit 20 Abonnements, von denen jedes so konfiguriert ist, dass Nachrichten automatisch an eine andere Warteschlange oder ein Thema weitergeleitet werden, wird als 21 Vorgänge abgerechnet, wenn alle Abonnements der ersten Ebene eine Kopie der Nachricht erhalten.

Um ein Abonnement zu erstellen, das mit einer anderen Warteschlange oder einem Thema verkettet ist, muss der Ersteller des Abonnements über die Berechtigung **Verwalten** für die Quell- und die Zielentität verfügen. Das Senden von Nachrichten an das Quellthema erfordert nur die Berechtigung **Senden** für das Quellthema.

Erstellen Sie keine Kette, die 4 Hops überschreitet. Nachrichten, die 4 Hops überschreiten, sind unzustellbar.

## <a name="next-steps"></a>Nächste Schritte
Um zu erfahren, wie Sie die automatische Weiterleitung auf unterschiedliche Weise aktivieren oder deaktivieren können (Azure-Portal, PowerShell, CLI, Azure Resource Management-Vorlage usw.), siehe [Auto-Weiterleitung für Warteschlangen und Abonnements aktivieren](enable-auto-forward.md).


[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
