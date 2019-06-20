---
title: Ausführen eines Failovers und Failbacks für virtuelle Hyper-V-Computer während der Notfallwiederherstellung in Azure mit Azure Site Recovery | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zum Ausführen eines Failovers und Failbacks für virtuelle Hyper-V-Computer während der Notfallwiederherstellung in Azure mit dem Azure Site Recovery-Dienst.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: a4889d82ac1c837581771860f2aba86faf7650ee
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399440"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Failover und Failback von Hyper-V-VMs, die in Azure repliziert werden

In diesem Tutorial wird das Ausführen eines Failovers einer Hyper-V-VM zu Azure beschrieben. Nach dem Failover erfolgt ein Failback zum lokalen Standort, wenn er verfügbar ist. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Überprüfen der Hyper-V-VM-Eigenschaften zum Feststellen der Konformität mit Azure-Anforderungen
> * Ausführen eines Failovers auf Azure
> * Failback von Azure zum lokalen Standort
> * Umgekehrtes Replizieren von lokalen VMs, um das erneute Replizieren nach Azure zu starten

Dies ist das fünfte Tutorial in einer Reihe. Es wird davon ausgegangen, dass Sie bereits die Aufgaben in den vorherigen Tutorials durchgearbeitet haben.    

1. [Vorbereiten von Azure](tutorial-prepare-azure.md)
2. [Vorbereiten lokaler Hyper-V-Instanzen](tutorial-prepare-on-premises-hyper-v.md)
3. Einrichten der Notfallwiederherstellung für [Hyper-V-VMs](tutorial-hyper-v-to-azure.md) oder [in System Center VMM-Clouds verwaltete Hyper-V-VMs](tutorial-hyper-v-vmm-to-azure.md)
4. [Durchführen eines Notfallwiederherstellungsverfahrens](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Vorbereiten für Failover und Failback

Stellen Sie sicher, dass auf dem virtuellen Computer keine Momentaufnahmen vorhanden sind und dass die lokalen virtuellen Computer während des Failbacks deaktiviert sind. Dies hilft, die Konsistenz der Daten während der Replikation sicherzustellen. Aktivieren Sie während des Failbacks keine lokalen virtuellen Computer. 

Failover und Failback weisen drei Phasen auf:

1. **Failover zu Azure**: Failover von Hyper-V-VMs vom lokalen Standort zu Azure.
2. **Lokales Failover**: Failover von Azure-VMs an Ihren lokalen Standort, wenn dieser verfügbar ist. Hierbei wird mit dem Synchronisieren der Daten aus Azure am lokalen Speicherort begonnen, und nach Abschluss des Vorgangs werden die VMs lokal gestartet.  
3. **Umgekehrtes Replizieren von lokalen VMs**: Nach dem Failback am lokalen Standort werden die lokalen virtuellen Computer umgekehrt repliziert, um mit der Replikation in Azure zu beginnen.

## <a name="verify-vm-properties"></a>Überprüfen von VM-Eigenschaften

Überprüfen Sie vor dem Ausführen des Failovers die VM-Eigenschaften, und stellen Sie sicher, dass der virtuelle Computer die [Azure-Anforderungen](hyper-v-azure-support-matrix.md#replicated-vms) erfüllt.

Klicken Sie unter **Geschützte Elemente** auf **Replizierte Elemente** > VM.

1. Im Bereich **Repliziertes Element** finden Sie eine Zusammenfassung der Informationen zu virtuellen Computern, den Integritätsstatus sowie die neuesten verfügbaren Wiederherstellungspunkte. Klicken Sie auf **Eigenschaften**, um weitere Details anzuzeigen.

1. In **Compute und Netzwerk** können Sie den Azure-Namen, die Ressourcengruppe, die Zielgröße, die [Verfügbarkeitsgruppe](../virtual-machines/windows/tutorial-availability-sets.md) und die Einstellungen verwalteter Datenträger ändern.

1. Sie können Netzwerkeinstellungen einschließlich des Netzwerks/Subnetzes, in dem der virtuelle Azure-Computer nach dem Failover platziert wird, sowie der IP-Adresse, die ihm zugewiesen wird, anzeigen und ändern.

1. Unter **Datenträger** finden Sie Informationen über das Betriebssystem und die Datenträger auf dem virtuellen Computer.

## <a name="failover-to-azure"></a>Failover zu Azure

1. Klicken Sie unter **Einstellungen** > **Replizierte Elemente** auf VM > **Failover**.
2. Wählen Sie unter **Failover** den **letzten** Wiederherstellungspunkt aus. 
3. Wählen Sie **Computer vor Beginn des Failovers herunterfahren** aus. Site Recovery versucht, die Quell-VMs herunterzufahren, bevor das Failover ausgelöst wird. Das Failover wird auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt.
4. Klicken Sie auf **Commit ausführen**, nachdem Sie das Failover überprüft haben. Daraufhin werden alle verfügbaren Wiederherstellungspunkte gelöscht.

> [!WARNING]
> **Brechen Sie ein aktuell ausgeführtes Failover nicht ab**: Wenn Sie den Fortschritt abbrechen, wird das Failover beendet, die Replikation der VM wird jedoch nicht erneut durchgeführt.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Failback von Azure-VMs auf lokale Standorte und umgekehrtes Replizieren von lokalen VMs

Der Failbackvorgang ist im Grunde genommen ein Failover von Azure auf den lokalen Standort und impliziert ein umgekehrtes Replizieren der VMs vom lokalen Standort nach Azure.

1. Klicken Sie unter **Einstellungen** > **Replizierte Elemente** auf VM > **Geplantes Failover**.
2. Überprüfen Sie in **Geplantes Failover bestätigen** die Failoverrichtung (von Azure), und wählen Sie Quell- und Zielspeicherort aus.
3. Wählen Sie **Daten vor Failover synchronisieren (nur Deltaänderungen synchronisieren)** aus. Diese Option minimiert VM-Ausfallzeiten, da hierbei eine Synchronisierung durchgeführt wird, ohne den virtuellen Computer herunterzufahren.
4. Initiieren Sie das Failover. Der Fortschritt des Failovers wird auf der Registerkarte **Aufträge** angezeigt.
5. Wenn die erste Datensynchronisierung abgeschlossen ist und Sie zum Herunterfahren der Azure-VMs bereit sind, klicken Sie auf **Aufträge** > Auftragsname des geplanten Failovers > **Failover abschließen**. Daraufhin wird die Azure-VM heruntergefahren, die neuesten Änderungen werden an den lokalen Standort übertragen, und der lokale virtuelle Computer wird gestartet.
6. Melden Sie sich auf dem lokalen virtuellen Computer an, um zu überprüfen, ob er wie erwartet verfügbar ist.
7. Der lokale virtuelle Computer befindet sich nun im Status **Commit ausstehend**. Klicken Sie auf **Commit ausführen**. Daraufhin werden die Azure-VMs und die zugehörigen Datenträger gelöscht, und der lokale virtuelle Computer wird auf die umgekehrte Replikation vorbereitet.
Aktivieren Sie zum Starten der Replikation des lokalen virtuellen Computers in Azure **Umgekehrt replizieren**. Daraufhin wird die Replikation von Deltaänderungen seit dem Ausschalten des virtuellen Azure-Computers ausgelöst.  
