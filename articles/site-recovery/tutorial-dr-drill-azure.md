---
title: Durchführen einer Notfallwiederherstellungsübung für lokale Computer in Azure mit Azure Site Recovery | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit Azure Site Recovery eine Notfallwiederherstellungsübung von lokalen Computern nach Azure durchführen.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4ed66e3a0237eced852c806e78a8af6bdf8d8579
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417829"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Durchführen eines Notfallwiederherstellungsverfahrens in Azure

In diesem Artikel erfahren Sie, wie Sie für einen lokalen Computer unter Verwendung des [Azure Site Recovery](site-recovery-overview.md)-Diensts ein Notfallwiederherstellungsverfahren in Azure durchführen. Eine Übung dient der Überprüfung Ihrer Replikationsstrategie ohne Datenverlust.


Dies ist das vierte Tutorial einer Reihe zur Einrichtung der Notfallwiederherstellung in Azure für lokale Computer.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten eines isolierten Netzwerks für das Testfailover
> * Vorbereiten der Verbindungsherstellung mit Azure-VMs nach dem Failover
> * Ausführen eines Testfailovers für einen einzelnen Computer

> [!NOTE]
> In den Tutorials wird der einfachste Bereitstellungspfad für ein Szenario erläutert. Sie verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade. Ausführlichere Informationen zu den Schritten des Notfallwiederherstellungsverfahrens finden Sie in [diesem Artikel](site-recovery-test-failover-to-azure.md).

## <a name="before-you-start"></a>Vorbereitung

Absolvieren Sie die vorherigen Tutorials:

1. Vergewissern Sie sich, dass Sie [Azure ordnungsgemäß eingerichtet haben](tutorial-prepare-azure.md) (für die lokale Notfallwiederherstellung virtueller VMware-Computer, virtueller Hyper-V-Computer und physischer Computer in Azure).
2. Bereiten Sie Ihre lokale [VMware](vmware-azure-tutorial-prepare-on-premises.md)- oder [Hyper-V](hyper-v-prepare-on-premises-tutorial.md)-Umgebung für die Notfallwiederherstellung vor. Falls Sie die Notfallwiederherstellung für physische Server einrichten möchten, sehen Sie sich die [Unterstützungsmatrix](vmware-physical-secondary-support-matrix.md) an.
3. Richten Sie die Notfallwiederherstellung für [virtuelle VMware-Computer](vmware-azure-tutorial.md), für [virtuelle Hyper-V-Computer](hyper-v-azure-tutorial.md) oder für [physische Computer](physical-azure-disaster-recovery.md) ein.
 

## <a name="verify-vm-properties"></a>Überprüfen von VM-Eigenschaften

Bevor Sie ein Testfailover ausführen, überprüfen Sie die VM-Eigenschaften, und stellen Sie sicher, dass der virtuelle [Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms)- oder [VMware](vmware-physical-azure-support-matrix.md#replicated-machines)-Computer die Azure-Anforderungen erfüllt.

1. Klicken Sie unter **Geschützte Elemente** auf **Replizierte Elemente** und dann auf den virtuellen Computer.
2. Im Bereich **Repliziertes Element** finden Sie eine Zusammenfassung der Informationen zu virtuellen Computern, den Integritätsstatus sowie die neuesten verfügbaren Wiederherstellungspunkte. Klicken Sie auf **Eigenschaften**, um weitere Details anzuzeigen.
3. In **Compute und Netzwerk** können Sie den Azure-Namen, die Ressourcengruppe, die Zielgröße, die Verfügbarkeitsgruppe und die Einstellungen verwalteter Datenträger ändern.
4. Sie können Netzwerkeinstellungen einschließlich des Netzwerks/Subnetzes, in dem der virtuelle Azure-Computer nach dem Failover platziert wird, sowie der IP-Adresse, die ihm zugewiesen wird, anzeigen und ändern.
5. Unter **Datenträger** finden Sie Informationen über das Betriebssystem und die Datenträger auf dem virtuellen Computer.

## <a name="create-a-network-for-test-failover"></a>Erstellen eines Netzwerks für Testfailover

Für Testfailover sollten Sie ein Netzwerk wählen, das von dem in den Einstellungen **Compute und Netzwerk** für jede VM angegebenen Produktionswiederherstellungsstandort-Netzwerk isoliert ist. Standardmäßig ist ein virtuelles Azure-Netzwerk beim Erstellen von anderen Netzwerken isoliert. Das Testnetzwerk sollte Ihr Produktionsnetzwerk imitieren:

- Das Testnetzwerk muss dieselbe Anzahl von Subnetzen wie das Produktionsnetzwerk besitzen. Subnetze müssen die gleichen Namen aufweisen.
- Das Testnetzwerk muss den gleichen IP-Adressbereich verwenden.
- Aktualisieren Sie das DNS des Testnetzwerks mit der IP-Adresse, die in den Einstellungen **Compute und Netzwerk** für die DNS-VM angegeben ist. Unter [Überlegungen zum Test-Failover](site-recovery-active-directory.md#test-failover-considerations) finden Sie weitere Details.

## <a name="run-a-test-failover-for-a-single-vm"></a>Ausführen eines Testfailovers für eine einzelne VM

Beim Durchführen eines Testfailovers geschieht Folgendes:

1. Eine Überprüfung der erforderlichen Komponenten wird ausgeführt, um sicherzustellen, dass alle Bedingungen für ein Failover erfüllt sind.
2. Durch das Failover werden die Daten verarbeitet, sodass eine Azure-VM erstellt werden kann. Wenn Sie den letzten Wiederherstellungspunkt auswählen, wird ein Wiederherstellungspunkt auf der Grundlage der Daten erstellt.
3. Eine Azure-VM wird anhand der im vorherigen Schritt verarbeiteten Daten erstellt.

Führen Sie das Testfailover wie folgt aus:

1. Klicken Sie unter **Einstellungen** > **Replizierte Elemente** auf die VM > **+Testfailover**.
2. Wählen Sie für dieses Tutorial **Letzte Verarbeitung** für den Wiederherstellungspunkt aus. Bei Auswahl dieser Option wird für die VM ein Failover auf den letzten verfügbaren Zeitpunkt durchgeführt. Der Zeitstempel wird angezeigt. Mit dieser Option wird keine Zeit für die Verarbeitung von Daten aufgewendet, und der RTO-Wert (Recovery Time Objective) wird niedrig gehalten.
3. Wählen Sie unter **Testfailover** das Azure-Zielnetzwerk aus, mit dem Azure-VMs nach dem Failover verbunden werden.
4. Klicken Sie auf **OK**, um den Failovervorgang zu starten. Sie können den Fortschritt des Vorgangs verfolgen, indem Sie auf die VM klicken, um ihre Eigenschaften zu öffnen. Alternativ können Sie auf den Auftrag **Testfailover** unter dem Tresornamen und dann auf **Einstellungen** > **Aufträge** >
   **Site Recovery-Aufträge** klicken.
5. Nach Abschluss des Failovers wird der virtuelle Azure-Replikatcomputer im Azure-Portal unter **Virtuelle Computer** angezeigt. Vergewissern Sie sich, dass die VM die richtige Größe hat, mit dem richtigen Netzwerk verbunden ist und ausgeführt wird.
6. Sie sollten nun eine Verbindung mit der replizierten VM in Azure herstellen können.
7. Klicken Sie zum Löschen der während des Testfailovers erstellten Azure-VMs auf **Testfailover bereinigen** für die VM. Erfassen und speichern Sie unter **Notizen** alle Beobachtungen im Zusammenhang mit dem Testfailover.

In einigen Szenarien erfordert ein Failover zusätzliche Verarbeitungsschritte, die etwa 8 bis 10 Minuten dauern können. Bei VMware-Linux-Computern, VMware-VMs ohne aktivierten DHCP-Dienst und VMware-VMs ohne die Starttreiber storvsc, vmbus, storflt, intelide und atapi kann das Testfailover länger dauern.

## <a name="connect-after-failover"></a>Herstellen einer Verbindung nach einem Failover

Wenn Sie nach einem Failover eine RDP-/SSH-Verbindung mit virtuellen Azure-Computern herstellen möchten, [bereiten Sie die Verbindungsherstellung vor](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover). Sollten nach dem Failover Verbindungsprobleme auftreten, lesen Sie das [Handbuch zur Problembehandlung](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Durchführen von Failover und Failback für VMware-VMs](vmware-azure-tutorial-failover-failback.md)
> [Durchführen von Failover und Failback für Hyper-V-VMs](hyper-v-azure-failover-failback-tutorial.md)
> [Durchführen von Failover und Failback für physische Computer](physical-to-azure-failover-failback.md)
