---
title: Ausführen eines Failbacks von in einer sekundären Azure-Region replizierten virtuellen Azure-Computern zur Notfallwiederherstellung mit dem Azure Site Recovery-Dienst
description: Es wird beschrieben, wie Sie mit dem Azure Site Recovery-Dienst ein Failback für Azure-VMs durchführen.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: a3b67e9b0dc41eeb14000400912892fbf29acfe2
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399499"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Failback für Azure-VMs zwischen Azure-Regionen

Der Dienst [Azure Site Recovery](site-recovery-overview.md) verwaltet und orchestriert die Replikation, das Failover und das Failback von lokalen Computern sowie Azure-VMs und unterstützt dadurch Ihre Strategie zur Notfallwiederherstellung.

In diesem Tutorial wird beschrieben, wie Sie das Failback für eine einzelne Azure-VM durchführen. Nach dem Failover müssen Sie ein Failback auf die primäre Region ausführen, wenn diese verfügbar ist. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> 
> * Ausführen eines Failbacks für den virtuellen Computer in der sekundären Region
> * Erneutes Schützen der primären VM zurück zur sekundären Region
> 
> [!NOTE]
> 
> In diesem Tutorial erfahren Sie, wie Sie mit minimalem Anpassungsaufwand für einige virtuelle Computer ein Failover auf eine Zielregion sowie ein Failback auf die Quellregion ausführen. Ausführlichere Anweisungen finden Sie in den [Anleitungsartikeln für virtuelle Azure-Computer](https://docs.microsoft.com/azure/virtual-machines/windows/).

## <a name="before-you-start"></a>Vorbereitung

* Vergewissern Sie sich, dass der Status des virtuellen Computers **Commit für Failover ausgeführt** lautet.
* Vergewissern Sie sich außerdem, dass die primäre Region verfügbar ist und dass Sie darin neue Ressourcen erstellen und auf diese zugreifen können.
* Stellen Sie sicher, dass die Option für das erneute Schützen aktiviert ist.

## <a name="fail-back-to-the-primary-region"></a>Ausführen eines Failbacks auf die primäre Region

Nachdem virtuelle Computer erneut geschützt wurden, können Sie bei Bedarf ein Failback zur primären Region ausführen.

1. Wählen Sie im Tresor **Replizierte Elemente** und dann den virtuellen Computer aus, der erneut geschützt wurde.

    ![Ausführen eines Failbacks auf die primäre Region](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Wählen Sie **Testfailover** aus, um ein Testfailover zurück zur primären Region auszuführen.
4. Wählen Sie den Wiederherstellungspunkt und das virtuelle Netzwerk für das Testfailover und dann **OK** aus. Sie können den virtuellen Computer überprüfen, der zu Testzwecken in der primären Region erstellt wurde.
5. Wählen Sie nach erfolgreicher Ausführung des Testfailovers **Testfailover bereinigen** aus, um die in der Quellregion für das Testfailover erstellten Ressourcen zu bereinigen.
6. Wählen Sie unter **Replizierte Elemente** den virtuellen Computer und dann **Failover** aus.
7. Wählen Sie unter **Failover** einen Wiederherstellungspunkt für das Failover aus:
    - **Neuester (Standardeinstellung)** : Bei dieser Option werden alle Daten in Site Recovery verarbeitet. Sie bietet außerdem den niedrigsten RPO-Wert (Recovery Point Objective).
    - **Letzte Verarbeitung**: Bei dieser Option wird für den virtuellen Computer der Zustand des letzten Wiederherstellungspunkts wiederhergestellt, der von Site Recovery verarbeitet wurde.
    - **Benutzerdefiniert**: Bei dieser Option wird ein Failover auf einen bestimmten Wiederherstellungspunkt ausgeführt. Diese Option eignet sich für ein Testfailover.

8. Wählen Sie **Der Computer wird vor Beginn des Failovers heruntergefahren** aus, wenn Site Recovery vor dem Auslösen des Failovers versuchen soll, Quell-VMs herunterzufahren. Das Failover wird auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist. Hinweis: Site Recovery bereinigt die Quelle nach dem Failover nicht.
9. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt.
10. Überprüfen Sie den virtuellen Computer nach Abschluss des Failovers, indem Sie sich bei ihm anmelden. Der Wiederherstellungspunkt kann bei Bedarf geändert werden.
11. Wählen Sie nach der Überprüfung des Failovers **Commit** aus. Dadurch werden alle verfügbaren Wiederherstellungspunkte gelöscht. Die Option „Wiederherstellungspunkt ändern“ ist nicht mehr verfügbar.
12. Für den virtuellen Computer wird angezeigt, dass ein Failover und ein Failback ausgeführt wurden.

    ![VM in primärer und sekundärer Region](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Die virtuellen Computer für die Notfallwiederherstellung bleiben im heruntergefahrenen Zustand bzw. im Zustand mit aufgehobener Zuordnung. Dies ist so beabsichtigt, da Site Recovery die VM-Informationen speichert, was später ggf. bei einem Failover von der primären auf die sekundäre Region hilfreich ist. Da für die virtuellen Computer mit aufgehobener Zuordnung keine Gebühren anfallen, empfiehlt es sich, sie beizubehalten.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über den [erneuten Schutz](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection).
