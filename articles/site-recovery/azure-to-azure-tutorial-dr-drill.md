---
title: Durchführen eines Notfallwiederherstellungsverfahrens für virtuelle Azure-Computer in einer sekundären Azure-Region mit dem Azure Site Recovery-Dienst
description: Hier erfahren Sie, wie Sie mit dem Azure Site Recovery-Dienst ein Notfallwiederherstellungsverfahren für virtuelle Azure-Computer in einer sekundären Azure-Region durchführen.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: d058282a9e903b37505f2a762d4264efbbec3a5a
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399513"
---
# <a name="run-a-disaster-recovery-drill-for-azure-vms-to-a-secondary-azure-region"></a>Durchführen eines Notfallwiederherstellungsverfahrens für virtuelle Azure-Computer in einer sekundären Azure-Region

Der Dienst [Azure Site Recovery](site-recovery-overview.md) unterstützt Ihre Strategien für Geschäftskontinuität und Notfallwiederherstellung, indem die Verfügbarkeit Ihrer Geschäftsanwendungen bei geplanten und ungeplanten Ausfällen gewährleistet wird. Site Recovery verwaltet und koordiniert die Notfallwiederherstellung von lokalen Computern sowie virtuellen Azure-Computern (VMs), einschließlich Replikation, Failover und Wiederherstellung.

In diesem Tutorial wird erläutert, wie ein Notfallwiederherstellungsverfahren für eine Azure-VM aus einer Azure-Region in eine andere mit einem Testfailover durchgeführt wird. Bei dem Verfahren wird Ihre Replikationsstrategie ohne Datenverlust oder Ausfallzeiten überprüft. Dies hat keinen Einfluss auf Ihre Produktionsumgebung. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Überprüfen der Voraussetzungen
> * Ausführen eines Testfailovers für eine einzelne VM

> [!NOTE]
> In diesem Tutorial wird für Benutzer beschrieben, wie mit möglichst wenigen Schritten eine Übung für die Notfallwiederherstellung durchgeführt werden kann. Falls Sie mehr zu den verschiedenen Aspekten zur Durchführung einer Übung für die Notfallwiederherstellung erfahren möchten, z.B. Netzwerküberlegungen, Automatisierung oder Problembehandlung, helfen Ihnen die Gewusst wie-Dokumente für Azure-VMs weiter.

## <a name="prerequisites"></a>Voraussetzungen

- Bevor Sie ein Testfailover ausführen, empfehlen wir Ihnen, die VM-Eigenschaften zu überprüfen, um sicherzustellen, dass sie den Erwartungen entsprechen.  Die VM-Einstellungen finden Sie unter **Replizierte Elemente**. Das Blatt **Zusammenfassung** enthält Informationen zu den Einstellungen und zum Status der Computer.
- **Es wird empfohlen, ein separates Azure-VM-Netzwerk für das Testfailover zu nutzen**, und nicht das Standardnetzwerk, das bei der Aktivierung der Replikation eingerichtet wurde.


## <a name="run-a-test-failover"></a>Ausführen eines Testfailovers

1. Klicken Sie unter **Einstellungen** > **Replizierte Elemente** auf das VM-Symbol **+Testfailover**.

2. Wählen Sie unter **Testfailover** einen Wiederherstellungspunkt für das Failover aus:

   - **Letzte Verarbeitung**: Führt ein Failover des virtuellen Computers auf den letzten Wiederherstellungspunkt aus, der vom Site Recovery-Dienst verarbeitet wurde. Der Zeitstempel wird angezeigt. Mit dieser Option wird keine Zeit für die Verarbeitung von Daten verwendet und die Recovery Time Objective (RTO) niedrig gehalten.
   - **Letzter anwendungskonsistenter Zeitpunkt**: Diese Option führt ein Failover aller virtuellen Computer auf den letzten App-konsistenten Wiederherstellungspunkt aus. Der Zeitstempel wird angezeigt.
   - **Benutzerdefiniert**: Wählen Sie einen beliebigen Wiederherstellungspunkt aus.

3. Wählen Sie das virtuelle Azure-Zielnetzwerk aus, mit dem Azure-VMs in der sekundären Region nach dem Failover verbunden werden.

4. Um das Failover zu starten, klicken Sie auf **OK**. Klicken Sie zum Überwachen des Fortschritts auf die VM, um die Eigenschaften aufzurufen. Alternativ können Sie auf den Auftrag **Testfailover** unter dem Tresornamen und dann auf **Einstellungen** > **Aufträge** > **Site Recovery-Aufträge** klicken.
5. Nach Abschluss des Failovers wird der virtuelle Azure-Replikatcomputer im Azure-Portal unter **Virtuelle Computer** angezeigt. Stellen Sie sicher, dass die VM ausgeführt wird, die passende Größe hat und mit dem entsprechenden Netzwerk verbunden ist.
6. Um die VMs zu löschen, die beim Testfailover erstellt wurden, klicken Sie im replizierten Element oder im Wiederherstellungsplan auf **Testfailover bereinigen**. Erfassen und speichern Sie unter **Notizen** alle Beobachtungen im Zusammenhang mit dem Testfailover.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Ausführen eines Produktionsfailovers](azure-to-azure-tutorial-failover-failback.md)
