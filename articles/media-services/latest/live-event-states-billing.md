---
title: Status und Abrechnung von Liveereignissen in Azure Media Services | Microsoft-Dokumentation
description: Dieses Thema bietet eine Übersicht über den Status und die entsprechende Abrechnung von Liveereignissen in Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: 2d3d3f5c56bd42aeb148c19fefebc0e7d364cd1c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782364"
---
# <a name="live-event-states-and-billing"></a>Status und Abrechnung von Liveereignissen

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In Azure Media Services fallen für ein Liveereignis Gebühren an, sobald der Zustand in **Wird ausgeführt** oder **Standby** geändert wird. Es wird Ihnen auch dann in Rechnung gestellt, wenn kein Video den Dienst durchläuft. Damit für das Liveereignis keine Gebühren mehr anfallen, müssen Sie es beenden. Die Livetranskription wird auf dieselbe Weise abgerechnet wie das Liveereignis.

Wenn **LiveEventEncodingType** für Ihr [Liveereignis](/rest/api/media/liveevents) auf „Standard“ oder „Premium1080p“ festgelegt ist, schaltet Media Services jedes Liveereignis, das sich noch im Zustand **Wird ausgeführt** befindet, 12 Stunden nach dem Verlust des Eingangsfeeds automatisch ab. Es werden keine **Liveausgaben** mehr ausgeführt. Allerdings wird Ihnen die Zeit in Rechnung gestellt, in der das Liveereignis den Zustand **Wird ausgeführt** hatte.

> [!NOTE]
> Passthrough-Liveereignisse werden nicht automatisch heruntergefahren und müssen explizit über die API gestoppt werden, um eine übermäßige Abrechnung zu vermeiden.

## <a name="states"></a>Zustände

Das Liveereignis kann folgende Zustände aufweisen:

|State|BESCHREIBUNG|
|---|---|
|**Beendet**| Dies ist der anfängliche Zustand des Liveereignisses nach der Erstellung (es sei denn, der automatische Start wurde auf „true“ festgelegt). In diesem Status werden keine Gebühren berechnet. Vom Liveereignis können keine Eingaben empfangen werden. |
|**Wird gestartet**| Das Liveereignis wird gestartet, und die Ressourcen werden zugewiesen. In diesem Status werden keine Gebühren berechnet.  Wenn ein Fehler auftritt, wird das Liveereignis in den Zustand „Angehalten“ zurückgesetzt.|
| **Allocating** (Wird zugeordnet) | Die Aktion „Zuordnen“ wurde für das Liveereignis aufgerufen, und Ressourcen werden für dieses Liveereignis bereitgestellt. Nachdem dieser Vorgang erfolgreich abgeschlossen wurde, wechselt das Liveereignis in den Status „Standby“.
|**Standby**| Liveereignisressourcen wurden bereitgestellt und können gestartet werden. In diesem Status werden Gebühren berechnet.  Die meisten Eigenschaften können weiterhin aktualisiert werden, allerdings ist die Erfassung oder das Streaming während dieses Zustands nicht zulässig.
|**Wird ausgeführt**| Die Liveereignisressourcen wurden zugewiesen, Erfassungs- und Vorschau-URLs wurden generiert, und Livestreams können empfangen werden. Zu diesem Zeitpunkt ist die Abrechnung aktiv. Sie müssen für die Liveereignisressource explizit „Beenden“ auswählen, damit keine Gebühren mehr anfallen.|
|**Wird beendet**| Das Liveereignis wird beendet und die Bereitstellung der Ressourcen aufgehoben. In diesem Übergangszustand erfolgt keine Berechnung. |
|**Wird gelöscht**.| Das Liveereignis wird gelöscht. In diesem Übergangszustand erfolgt keine Berechnung. |

Sie können die Livetranskriptionen bei der Erstellung des Liveereignisses aktivieren. In diesem Fall werden Ihnen die Livetranskriptionen in Rechnung gestellt, wenn das Liveereignis den Status **Wird ausgeführt** aufweist. Beachten Sie, dass Ihnen die Kosten auch dann in Rechnung gestellt werden, wenn kein Audio das Liveereignis durchläuft.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über das Livestreaming](live-streaming-overview.md)
- [Tutorial zu Livestreaming](stream-live-tutorial-with-api.md)
