---
title: Georedundante Notfallwiederherstellung in Azure Event Grid | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie Azure Event Grid die georedundante Notfallwiederherstellung automatisch unterstützt.
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: 10beaf0ae25f3ed9b7bcda5961a89494b18b84d9
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980847"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Serverseitige georedundante Notfallwiederherstellung in Azure Event Grid
Event Grid verfügt jetzt über eine automatische georedundante Notfallwiederherstellung (GeoDR) für Metadaten – nicht nur für neue, sondern auch für alle vorhandenen Domänen, Themen und Ereignisabonnements. Wenn eine vollständige Azure-Region ausfällt, hat Event Grid bereits alle ereignisbezogenen Infrastrukturmetadaten in ein Regionspaar synchronisiert. Neue Ereignisse werden übertragen, ohne dass Sie eingreifen müssen. 

Die Notfallwiederherstellung wird anhand von zwei Metriken gemessen:

- Recovery Point Objective (RPO): Der Zeitraum in Minuten oder Stunden, innerhalb dessen Daten verloren gehen können.
- Recovery Time Objective (RTO): Der Zeitraum in Minuten oder Stunden, innerhalb dessen der Dienst ausfallen kann.

Beim automatischen Failover von Event Grid gelten andere RPOs und RTOs für Ihre Metadaten (Ereignisabonnements usw.) und für Ihre Daten (Ereignisse). Wenn Sie andere Werte als die unten angegebenen benötigen, können Sie auch weiterhin ein eigenes [clientseitiges Failover mithilfe von APIs für die Themenintegrität](custom-disaster-recovery.md) implementieren.

## <a name="recovery-point-objective-rpo"></a>Recovery Point Objective (RPO)
- **Metadaten-RPO**: Null Minuten. Wann immer eine Ressource in Event Grid erstellt wird, erfolgt eine sofortige Replikation über verschiedene Regionen hinweg. Bei einem Failover gehen keine Metadaten verloren.
- **Daten-RPO**: Wenn Ihr System fehlerfrei funktioniert und den bestehenden Datenverkehr zum Zeitpunkt eines Regionsfailover erfasst hat, beträgt das RPO für Ereignisse etwa 5 Minuten.

## <a name="recovery-time-objective-rto"></a>Recovery Time Objective (RTO)
- **Metadaten-RTO**: Event Grid beginnt innerhalb von 60 Minuten damit, Aufrufe zum Erstellen/Aktualisieren/Löschen von Themen und Abonnements zu akzeptieren. In der Regel startet dieser Vorgang deutlich schneller.
- **Daten-RTO**: Nach einem regionalen Failover beginnt Event Grid innerhalb von 60 Minuten damit, Datenverkehr zu akzeptieren. Wie bei Metadaten startet auch dieser Vorgang in der Regel deutlich schneller.

> [!NOTE]
> Für die georedundante Notfallwiederherstellung von Metadaten in Event Grid fallen keine Kosten an.


## <a name="next-steps"></a>Nächste Schritte
Wenn Sie selbst eine clientseitige Failoverlogik implementieren möchten, finden Sie Informationen dazu unter [Erstellen einer eigenen Notfallwiederherstellung für benutzerdefinierte Themen in Event Grid](custom-disaster-recovery.md).
