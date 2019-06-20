---
title: Failover und Failback von physischen Servern für die Notfallwiederherstellung in Azure mit Site Recovery | Microsoft-Dokumentation
description: Informationen zum Ausführen eines Failovers physischer Server in Azure und eines Failbacks an den lokalen Standort für die Notfallwiederherstellung mit Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 14fa5822c575f2d2d60a956263cf916ee8f9bb4d
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400035"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Ausführen eines Failovers und Failbacks für physische Server, die in Azure repliziert werden

In diesem Tutorial wird das Ausführen eines Failovers eines physischen Servers in Azure beschrieben. Nach dem Failover erfolgt ein Failback des Servers zum lokalen Standort, sobald dieser verfügbar ist.

## <a name="preparing-for-failover-and-failback"></a>Vorbereiten für Failover und Failback

Für physische Server, die mithilfe von Site Recovery nach Azure repliziert wurden, kann ein Failback nur als VMware-VMs erfolgen. Sie benötigen eine VMware-Infrastruktur, damit Sie ein Failback ausführen können.

Failover und Failback weisen vier Phasen auf:

1. **Failover auf Azure**: Failover von Computern vom lokalen Standort auf Azure.
2. **Erneutes Schützen der Azure-VMs**: Schützen Sie die Azure-VMs erneut, damit sie wieder zurück in lokale VMware-VMs repliziert werden.
3. **Failover auf lokalen Standort**: Führen Sie ein Failover aus, um ein Failback aus Azure durchzuführen.
4. **Erneutes Schützen lokaler VMs**: Schützen Sie nach dem Failback der Daten erneut die lokalen VMware-VMs, auf die Sie das Failback durchgeführt haben, damit diese mit der Replikation nach Azure beginnen.

## <a name="verify-server-properties"></a>Überprüfen der Servereigenschaften

Überprüfen Sie die Servereigenschaften, und stellen Sie sicher, dass sie die [Azure-Anforderungen](vmware-physical-azure-support-matrix.md#replicated-machines) für Azure-VMs erfüllen.

1. Klicken Sie unter **Geschützte Elemente** auf **Replizierte Elemente**, und wählen Sie den Computer aus.

2. Im Bereich **Repliziertes Element** finden Sie eine Zusammenfassung der Computerinformationen, den Integritätsstatus sowie die neuesten verfügbaren Wiederherstellungspunkte. Klicken Sie auf **Eigenschaften**, um weitere Details anzuzeigen.
3. In **Compute und Netzwerk** können Sie den Azure-Namen, die Ressourcengruppe, Zielgröße, [Verfügbarkeitsgruppe](../virtual-machines/windows/tutorial-availability-sets.md) und Einstellungen verwalteter Datenträger ändern.
4. Sie können Netzwerkeinstellungen einschließlich des Netzwerks/Subnetzes, in dem der virtuelle Azure-Computer nach dem Failover platziert wird, sowie der IP-Adresse, die ihm zugewiesen wird, anzeigen und ändern.
5. Unter **Datenträger** finden Sie Informationen über das Betriebssystem und die Datenträger auf dem Computer.

## <a name="run-a-failover-to-azure"></a>Ausführen eines Failovers auf Azure

1. Klicken Sie in **Einstellungen** > **Replizierte Elemente** auf den Computer > **Failover**.
2. Wählen Sie in **Failover** einen **Wiederherstellungspunkt** für das Failover aus. Sie können eine der folgenden Optionen auswählen:
   - **Letzter Zeitpunkt**: Mit dieser Option werden zuerst alle an Site Recovery gesendeten Daten verarbeitet. Sie bietet die niedrigste RPO (Recovery Point Objective), da die nach dem Failover erstellte Azure-VM über alle Daten verfügt, die bei Auslösung des Failovers zu Site Recovery repliziert wurden.
   - **Letzte Verarbeitung**: Mit dieser Option wird ein Failover des Computers auf den letzten Wiederherstellungspunkt ausgeführt, der von Site Recovery verarbeitet wurde. Diese Option bietet eine niedrige Recovery Time Objective (RTO), da keine Zeit für die Verarbeitung unverarbeiteter Daten aufgewendet wird.
   - **Letzter anwendungskonsistenter Zeitpunkt**: Mit dieser Option wird ein Failover des Computers auf den letzten anwendungskonsistenten Wiederherstellungspunkt ausgeführt, der von Site Recovery verarbeitet wurde.
   - **Benutzerdefinierte**: Geben Sie einen Wiederherstellungspunkt an.

3. Klicken Sie auf **Der Computer wird vor Beginn des Failovers heruntergefahren**, wenn Site Recovery versuchen soll, den Quellcomputer herunterzufahren, bevor das Failover ausgelöst wird. Das Failover wird auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt.
4. Wenn Sie das Herstellen einer Verbindung mit der Azure-VM vorbereitet haben, stellen Sie eine Verbindung her, um sie nach dem Failover zu überprüfen.
5. **Committen** Sie das Failover nach der Überprüfung. Dadurch werden alle verfügbaren Wiederherstellungspunkte gelöscht.

> [!WARNING]
> Brechen Sie ein Failover in Bearbeitung nicht ab. Vor Beginn des Failovers wird die Replikation der Computer beendet. Wenn Sie ein Failover abbrechen, wird es beendet, ohne dass die Replikation des Computers erneut erfolgt.
> Bei physischen Servern kann die zusätzliche Failoververarbeitung ca. 8 bis 10 Minuten dauern.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Vorbereiten der Verbindungsherstellung mit Azure-VMs nach dem Failover

Wenn Sie nach dem Failover per RDP/SSH eine Verbindung mit Azure-VMs herstellen möchten, müssen Sie die in dieser [Tabelle](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) zusammengefassten Anforderungen erfüllen.

Führen Sie die [hier](site-recovery-failover-to-azure-troubleshoot.md) beschriebenen Schritte aus, um nach dem Failover ggf. Konnektivitätsprobleme zu beheben.

## <a name="create-a-process-server-in-azure"></a>Erstellen eines Prozessservers in Azure

Der Prozessserver empfängt Daten von der Azure-VM und sendet Daten an den lokalen Standort. Zwischen dem Prozessserver und geschützten Computer ist ein Netzwerk mit kurzer Wartezeit erforderlich.

- Wenn Sie über eine Azure ExpressRoute-Verbindung verfügen, können Sie zu Testzwecken den lokalen Prozessserver verwenden, der automatisch auf dem Konfigurationsserver installiert wird.
- Wenn Sie über eine VPN-Verbindung verfügen oder Sie das Failback in einer Produktionsumgebung ausführen, müssen Sie eine Azure-VM als Azure-basierten Prozessserver für Failback einrichten.
- Um einen Prozessserver in Azure einzurichten, befolgen Sie die Anweisungen in [diesem Artikel](vmware-azure-set-up-process-server-azure.md).

## <a name="configure-the-master-target-server"></a>Konfigurieren des Masterzielservers

Standardmäßig empfängt der Masterzielserver Failbackdaten. Er wird auf dem lokalen Konfigurationsserver ausgeführt.

- Wenn die VMware-VM, auf die das Failback erfolgt, sich auf einem ESXi-Host befindet, der von VMware vCenter Server verwaltet wird, benötigt der Masterzielserver Zugriff auf den VM-Datenspeicher (VMDK), um replizierte Daten auf die VM-Datenträger zu schreiben. Stellen Sie sicher, dass der VM-Datenspeicher auf dem Host des Masterziels mit Lese-/Schreibzugriff eingebunden wird.
- Wenn der ESXi-Host nicht von einem vCenter-Server verwaltet wird, erstellt der Site Recovery-Dienst während des erneuten Schutzes eine neue VM. Dieser VM wird auf dem ESX-Host erstellt, auf dem Sie die Masterziel-VM erstellen. Die Festplatte des virtuellen Computers muss sich in einen Datenspeicher befinden, der für den Host zugänglich ist, auf dem der Masterzielserver ausgeführt wird.
- Für physische Computer mit ausstehendem Failback sollten Sie die Ermittlung des Hosts abschließen, auf dem der Masterzielserver ausgeführt wird, bevor Sie den Computer erneut schützen können.
- Als weitere Option können Sie die bereits vorhandene lokale VM vor Ausführen des Failbacks löschen. Beim Failback wird dann ein neuer virtueller Computer auf dem gleichen Host erstellt, der als Masterziel-ESX-Host fungiert. Wenn Sie das Failback zu einem anderen Speicherort durchführen, werden die Daten in dem gleichen Datenspeicher und in dem gleichen ESX-Host wiederhergestellt, der vom lokalen Masterzielserver verwendet wird.
- Es ist nicht möglich, Storage vMotion auf dem Masterzielserver zu verwenden. Wenn Sie dies tun, ist kein Failback möglich, da die Datenträger nicht verfügbar sind. Schließen Sie die Masterzielserver aus der vMotion-Liste aus.

## <a name="reprotect-azure-vms"></a>Erneutes Schützen der virtuellen Azure-Computer

Dieses Verfahren setzt voraus, dass der lokale virtuelle Computer nicht verfügbar ist, und Sie für das erneute Schützen einen anderen Speicherort verwenden.

1. Klicken Sie in **Einstellungen** > **Replizierte Elemente** mit der rechten Maustaste auf den virtuellen Computer, für den ein Failover ausgeführt wurde > **Erneut schützen**.
2. Überprüfen Sie in **Erneut schützen**, ob **Azure auf lokal** ausgewählt ist.
3. Geben Sie den lokalen Masterzielserver und den Prozessserver an.

4. Wählen Sie in **Datenspeicher** den Masterziel-Datenspeicher aus, in dem die Datenträger lokal wiederhergestellt werden sollen. Verwenden Sie diese Option, wenn der lokale virtuelle Computer gelöscht wurde und neue Datenträger erstellt werden müssen. Diese Einstellungen werden ignoriert, wenn die Datenträger bereits vorhanden sind. Sie müssen aber trotzdem einen Wert angeben.
5. Wählen Sie das Masterziel-Aufbewahrungslaufwerk aus. Die Failbackrichtlinie wird automatisch ausgewählt.
6. Klicken Sie auf **OK**, um das erneute Schützen zu starten. Ein Auftrag beginnt mit der Replikation des virtuellen Computers von Azure am lokalen Standort. Sie können den Fortschritt auf der Registerkarte **Aufträge** nachverfolgen.

> [!NOTE]
> Wenn Sie den virtuellen Azure-Computer unter einem vorhandenen lokalen virtuellen Computer wiederherstellen möchten, binden Sie den Datenspeicher des lokalen virtuellen Computers mit Lese-/Schreibzugriff auf dem ESXi-Host des Masterzielservers ein.


## <a name="run-a-failover-from-azure-to-on-premises"></a>Ausführen eines Failovers von Azure auf den lokalen Standort

Um zurück nach dem lokalen Standort zu replizieren, wird eine Failbackrichtlinie verwendet. Diese Richtlinie wird automatisch erstellt, wenn Sie eine Replikationsrichtlinie für die Replikation zu Azure erstellen:

- Die Richtlinie wird dem Konfigurationsserver automatisch zugeordnet.
- Die Richtlinie kann nicht geändert werden.
- Die Werte der Richtlinie sind:
    - RPO-Schwellenwert = 15 Minuten
    - Aufbewahrungszeitraum des Wiederherstellungspunkts = 24 Stunden
    - Häufigkeit von Momentaufnahmen für App-Konsistenz = 60 Minuten

Führen Sie das Failover wie folgt aus:

1. Klicken Sie auf der Seite **Replizierte Elemente** mit der rechten Maustaste auf den Computer > **Ungeplantes Failover**.
2. Überprüfen Sie in **Failover bestätigen**, dass das Failover von Azure aus erfolgt.

3. Wählen Sie den Wiederherstellungspunkt aus, den Sie für das Failover verwenden möchten. Ein anwendungskonsistenter Punkt liegt vor dem aktuellsten Zeitpunkt und verursacht einigen Datenverlust. Wenn das Failover ausgeführt wird, fährt Site Recovery die Azure-VMs herunter und startet den lokalen virtuellen Computer. Da dies mit einer gewissen Ausfallzeit verbunden ist, wählen Sie einen geeigneten Zeitpunkt.
4. Klicken Sie mit der rechten Maustaste auf den Computer, und klicken Sie auf **Committen**. Dadurch wird ein Auftrag ausgelöst, der die Azure-VMs entfernt.
5. Stellen Sie sicher, dass die Azure-VMs wie erwartet heruntergefahren wurden.


## <a name="reprotect-on-premises-machines-to-azure"></a>Erneutes Schützen lokaler Computer in Azure

Die Daten sollten nun wieder auf Ihrem lokalen Standort sein, aber sie werden nicht nach Azure repliziert. Sie können die Replikation nach Azure folgendermaßen erneut starten:

1. Wählen Sie im Tresor > **Einstellungen** >**Replizierte Elemente** die virtuellen Computer aus, für die das Failback durchgeführt wurde, und klicken Sie auf **Erneut schützen**.
2. Wählen Sie den Prozessserver, mit dem die replizierten Daten nach Azure gesendet werden, und klicken Sie auf **OK**.

Nachdem das erneute Schützen abgeschlossen ist, wird der virtuelle Computer zurück nach Azure repliziert, und Sie können ggf. ein Failover ausführen.
