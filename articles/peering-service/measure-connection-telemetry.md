---
title: 'Azure Peering Service: Vorgehensweise zum Messen von Verbindungstelemetrie '
description: In diesem Tutorial lernen Sie, wie Sie Verbindungstelemetrie messen.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service.
ms.openlocfilehash: abbe69ebbaed56ed416f85fafa7b77a1740fabe7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022495"
---
# <a name="tutorial-measure-peering-service-connection-telemetry"></a>Tutorial: Messen von Peering Service-Verbindungstelemetrie

 In diesem Tutorial lernen Sie, wie Sie die Telemetrie für Ihre Peering Service-Verbindungen messen.
 
 Die Verbindungstelemetrie bietet Einblicke, die für die Konnektivität zwischen dem Standort des Kunden und dem Microsoft-Netzwerk erfasst werden. In diesem Artikel erfahren Sie, wie Sie den Latenzbericht für eine bestimmte Azure Peering Service-Verbindung anzeigen. 

Zum Messen von Peering Service-Verbindungstelemetrie müssen Sie eine Peering Service-Verbindung im Azure-Portal registrieren. Informationen zum Registrieren einer Verbindung finden Sie unter [Registrieren einer Peering Service-Verbindung – Azure-Portal](azure-portal.md).


## <a name="view-a-latency-report"></a>Anzeigen eines Latenzberichts

Führen Sie zum Anzeigen eines Latenzberichts für eine bestimmte Peering Service-Verbindung die folgenden Schritte aus.

1. Wählen Sie im linken Bereich **Alle Ressourcen** und dann die Peering Service-Verbindung aus. Wählen Sie dann unter **Präfixe** die Option **Öffnen** aus. 

   ![Auswählen der Peering Service-Verbindung](./media/peering-service-measure/peering-service-measure-menu.png)

2. Eine Latenzberichtseite für alle Präfixe, die dieser Peering Service-Verbindung zugeordnet sind, wird angezeigt. 

      ![Seite „Latenzbericht“](./media/peering-service-measure/peering-service-latency-report.png)

3. Der Bericht wird standardmäßig für jede Stunde aktualisiert, die auf dieser Seite angezeigt wird. Wenn Sie den Bericht für andere Zeitachsen anzeigen möchten, wählen Sie die entsprechende Option aus **Datenanzeige für die letzten:** aus. 

4. Wenn Sie die Ereignisse für ein bestimmtes Präfix anzeigen möchten, wählen Sie dessen Namen und dann im linken Bereich **Präfixereignisse** aus. Die erfassten Ereignisse werden angezeigt.


   ![Präfixereignisse](./media/peering-service-measure/peering-service-prefix-event.png)

 Einige der möglichen Ereignisse, die in der Liste **Präfixereignisse** erfasst werden, werden hier gezeigt.

| **Präfixereignisse** | **Ereignistyp**|**Erläuterung**|
|-----------|---------|---------|
| PrefixAnnouncementEvent |Information|Eine Präfixankündigung wurde empfangen.|
| PrefixWithdrawalEvent|Warnung| Eine Zurücknahme des Präfixes wurde empfangen. |
| PrefixBackupRouteAnnouncementEvent |Information|Eine Ankündigung der Präfixsicherungsroute wurde empfangen. |
| PrefixBackupRouteWithdrawalEvent|Warnung|Eine Zurücknahme der Präfixsicherungsroute wurde empfangen. |
| PrefixActivePath |Information| Aktuelle aktive Präfixroute   |
| PrefixBackupPath | Information|Aktuelle Präfixsicherungsroute   |
| PrefixOriginAsChangeEvent|Kritisch| Ein exaktes Präfix wurde mit anderer ursprünglicher autonomer Systemnummer (für aktive Route) empfangen.| 
| PrefixBackupRouteOriginAsChangeEvent  | Fehler|Ein Präfix wurde mit anderer ursprünglicher autonomer Systemnummer (für Sicherungsroute) empfangen.  |

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Peering Service-Verbindung finden Sie unter [Peering Service-Verbindung](connection.md).
- Informationen zur Peering Service-Verbindungstelemetrie finden Sie unter [Peering Service-Verbindungstelemetrie](connection-telemetry.md).