---
title: Überwachen des Azure Site Recovery-Prozessservers
description: In diesem Artikel wird beschrieben, wie Sie den Azure Site Recovery-Prozessserver überwachen.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 4ff52e737438210296b8f2201d5e66e1d38b7bc9
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418285"
---
# <a name="monitor-the-process-server"></a>Überwachen des Prozessservers

In diesem Artikel wird beschrieben, wie Sie den [Site Recovery](site-recovery-overview.md)-Prozessserver überwachen.

- Der Prozessserver wird verwendet, wenn Sie die Notfallwiederherstellung für lokale virtuelle VMware-Computer und physische Server für Azure einrichten.
- Der Prozessserver wird standardmäßig auf dem Konfigurationsserver ausgeführt. Er wird standardmäßig beim Bereitstellen des Konfigurationsservers installiert.
- Zur Skalierung und Verarbeitung einer größeren Anzahl replizierter Computer und eines höheren Volumens an Replikationsdatenverkehr können Sie optional zusätzliche horizontal skalierte Prozessserver bereitstellen.

[Erfahren Sie mehr](vmware-physical-azure-config-process-server-overview.md) über die Rolle und die Bereitstellung von Prozessservern.

## <a name="monitoring-overview"></a>Übersicht zur Überwachung

Da der Prozessserver so viele Rollen hat, insbesondere bei der Zwischenspeicherung, Komprimierung und Übertragung von replizierten Daten in Azure, ist es wichtig, die Integrität des Prozessservers kontinuierlich zu überwachen.

Es gibt eine Reihe von Fällen, die sich häufig auf die Leistung des Prozessservers auswirken. Probleme, die die Leistung beeinträchtigen, haben einen Kaskadeneffekt auf die Integrität der virtuellen Computer und führen schließlich dazu, dass sowohl der Prozessserver als auch die replizierten Computer sich in einem kritischen Zustand befinden. Dies betrifft u. a. folgende Fälle:

- Eine große Anzahl virtueller Computer verwendet einen Prozessserver, sodass die empfohlenen Einschränkungen erreicht oder überschritten werden.
- Die virtuellen Computer, die den Prozessserver verwenden, weisen eine hohe Änderungsrate auf.
- Der Netzwerkdurchsatz zwischen den virtuellen Computern und dem Prozessserver reicht nicht aus, um Replikationsdaten auf den Prozessserver hochzuladen.
- Der Netzwerkdurchsatz zwischen dem Prozessserver und Azure reicht nicht aus, um Replikationsdaten vom Prozessserver in Azure hochzuladen.

Alle diese Probleme können sich auf das Recovery Point Objective (RPO) von virtuellen Computern auswirken. 

**Warum?** Die Generierung eines Wiederherstellungspunkts für einen virtuellen Computer erfordert, dass alle Datenträger auf dem virtuellen Computer einen gemeinsamen Punkt haben. Wenn ein Datenträger eine hohe Änderungsrate aufweist, die Replikation langsam erfolgt oder der Prozessserver nicht optimal ist, hat dies Auswirkungen darauf, wie effizient Wiederherstellungspunkte erstellt werden.

## <a name="monitor-proactively"></a>Proaktive Überwachung

Um Probleme mit dem Prozessserver zu vermeiden, ist Folgendes wichtig:

- Kenntnis der spezifischen Anforderungen für Prozessserver unter Verwendung der [Leitlinien für Kapazität und Größenanpassung](site-recovery-plan-capacity-vmware.md#capacity-considerations) und Sicherstellung, dass Prozessserver entsprechend den Empfehlungen bereitgestellt und ausgeführt werden.
- Überwachung von Warnungen und Behebung von Problemen, sobald diese auftreten, damit Prozessserver kontinuierlich effizient ausgeführt werden.


## <a name="process-server-alerts"></a>Warnungen zum Prozessserver

Der Prozessserver generiert eine Reihe von Integritätswarnungen, die in der folgenden Tabelle zusammengefasst sind.

**Warnungstyp** | **Details**
--- | ---
![Healthy][green] | Der Prozessserver ist verbunden und fehlerfrei.
![Warnung][yellow] | CPU-Auslastung > 80 % während der letzten 15 Minuten
![Warnung][yellow] | Speicherauslastung > 80 % während der letzten 15 Minuten
![Warnung][yellow] | Freier Speicherplatz des Cacheordners < 30 % während der letzten 15 Minuten
![Warnung][yellow] | Prozessserverdienste wurden während der letzten 15 Minuten nicht ausgeführt
![Kritisch][red] | CPU-Auslastung > 95 % während der letzten 15 Minuten
![Kritisch][red] | Speicherauslastung > 95 % während der letzten 15 Minuten
![Kritisch][red] | Freier Speicherplatz des Cacheordners < 25 % während der letzten 15 Minuten
![Kritisch][red] | 15 Minuten kein Heartbeat vom Prozessserver

![Tabellenschlüssel](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> Der allgemeine Integritätsstatus des Prozessservers basiert auf der kritischsten generierten Warnung.



## <a name="monitor-process-server-health"></a>Überwachen der Integrität des Prozessservers

Sie können den Integritätszustand des Prozessservers wie folgt überwachen: 

1. Zum Überwachen der Replikationsintegrität und des Status eines replizierten Computers und des zugehörigen Prozessservers klicken Sie im Tresor unter **Replizierte Elemente** auf den Computer, der überwacht werden soll.
2. Unter **Replikationsintegrität** können Sie den Integritätsstatus des virtuellen Computers überwachen. Klicken Sie auf den Status, um einen Drilldown zu den Fehlerdetails auszuführen.

    ![Integrität des Prozessservers im Dashboard des virtuellen Computers](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. Unter **Integrität des Prozessservers** können Sie den Status des Prozessservers überwachen. Führen Sie einen Drilldown zu den Details aus.

    ![Details zum Prozessserver im Dashboard des virtuellen Computers](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. Die Integrität kann auch über die grafische Darstellung auf der Seite des virtuellen Computers überwacht werden.
    - Ein horizontal skalierter Prozessserver wird orangefarben hervorgehoben, wenn Warnungen vorhanden sind, und rot, wenn kritische Probleme aufgetreten sind. 
    - Wenn der Prozessserver in der Standardbereitstellung auf dem Konfigurationsserver ausgeführt wird, wird der Konfigurationsserver entsprechend hervorgehoben.
    - Um einen Drilldown auszuführen, klicken Sie auf den Konfigurationsserver oder den Prozessserver. Überprüfen Sie alle Probleme und Empfehlungen zur Problembehebung.

Außerdem können Sie Prozessserver im Tresor unter **Site Recovery-Infrastruktur** überwachen. Klicken Sie in **Verwalten Sie Ihre Site Recovery-Infrastruktur** auf **Konfigurationsserver**. Wählen Sie den Konfigurationsserver aus, der dem Prozessserver zugeordnet ist, und führen Sie einen Drilldown zu den Details des Prozessservers aus.


## <a name="next-steps"></a>Nächste Schritte

- Wenn Probleme mit dem Prozessserver auftreten, finden Sie Informationen unter [Problembehandlung des Prozessservers](vmware-physical-azure-troubleshoot-process-server.md).
- Wenn Sie weitere Hilfe benötigen, können Sie Ihre Frage im [Azure Site Recovery-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) veröffentlichen. 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
