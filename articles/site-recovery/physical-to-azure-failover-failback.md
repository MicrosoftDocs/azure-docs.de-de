---
title: Einrichten von Failover und Failback für physische Server mit Site Recovery
description: Informationen zum Ausführen eines Failovers physischer Server in Azure und eines Failbacks an den lokalen Standort für die Notfallwiederherstellung mit Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.author: raynew
ms.openlocfilehash: 2994f68e4159c7c4aa7d82bef7a5891deb5055a0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017422"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Ausführen eines Failovers und Failbacks für physische Server, die in Azure repliziert werden

In diesem Tutorial wird beschrieben, wie Sie ein Failover für lokale physische Server durchführen, die mit [Azure Site Recovery](site-recovery-overview.md) nach Azure repliziert werden. Nach dem Failover erfolgt ein Failback von Azure zum lokalen Standort, wenn er verfügbar ist.

## <a name="before-you-start"></a>Vorbereitung

- [Informieren Sie sich](failover-failback-overview.md) über den Failoverprozess bei der Notfallwiederherstellung.
- [Informieren Sie sich](recovery-plan-overview.md), wie Sie Computer in einem Wiederherstellungsplan zusammenfassen, wenn Sie ein Failover für mehrere Computer ausführen möchten.
- Führen Sie vor einem vollständigen Failover eine [Notfallwiederherstellungsübung](site-recovery-test-failover-to-azure.md) durch, um sicherzustellen, dass alles wie erwartet funktioniert.
- Befolgen Sie [diese Anweisungen](site-recovery-failover.md#prepare-to-connect-after-failover), um die Verbindung zu virtuellen Azure-Computern nach dem Failover vorzubereiten.



## <a name="run-a-failover"></a>Ausführen eines Failovers

### <a name="verify-server-properties"></a>Überprüfen der Servereigenschaften

Überprüfen Sie die Servereigenschaften, und stellen Sie sicher, dass sie die [Azure-Anforderungen](vmware-physical-azure-support-matrix.md#replicated-machines) für Azure-VMs erfüllen.

1. Klicken Sie unter **Geschützte Elemente** auf **Replizierte Elemente**, und wählen Sie den Computer aus.
2. Im Bereich **Repliziertes Element** finden Sie eine Zusammenfassung der Computerinformationen, den Integritätsstatus sowie die neuesten verfügbaren Wiederherstellungspunkte. Klicken Sie auf **Eigenschaften**, um weitere Details anzuzeigen.
3. In **Compute und Netzwerk** können Sie den Azure-Namen, die Ressourcengruppe, Zielgröße, [Verfügbarkeitsgruppe](../virtual-machines/windows/tutorial-availability-sets.md) und Einstellungen verwalteter Datenträger ändern.
4. Sie können Netzwerkeinstellungen einschließlich des Netzwerks/Subnetzes, in dem der virtuelle Azure-Computer nach dem Failover platziert wird, sowie der IP-Adresse, die ihm zugewiesen wird, anzeigen und ändern.
5. Unter **Datenträger** finden Sie Informationen über das Betriebssystem und die Datenträger auf dem Computer.

### <a name="fail-over-to-azure"></a>Failover in Azure

1. Klicken Sie in **Einstellungen** > **Replizierte Elemente** auf den Computer > **Failover**.
2. Wählen Sie in **Failover** einen **Wiederherstellungspunkt** für das Failover aus. Sie können eine der folgenden Optionen auswählen:
   - **Neueste**: Mit dieser Option werden zuerst alle an Site Recovery gesendeten Daten verarbeitet. Sie bietet die niedrigste RPO (Recovery Point Objective), da die nach dem Failover erstellte Azure-VM über alle Daten verfügt, die bei Auslösung des Failovers zu Site Recovery repliziert wurden.
   - **Letzte Verarbeitung:** Mit dieser Option wird ein Failover des Computers auf den letzten Wiederherstellungspunkt ausgeführt, der von Site Recovery verarbeitet wurde. Diese Option bietet eine niedrige Recovery Time Objective (RTO), da keine Zeit für die Verarbeitung unverarbeiteter Daten aufgewendet wird.
   - **Letzte App-Konsistenz:** Mit dieser Option wird ein Failover des Computers mithilfe des letzten App-konsistenten Wiederherstellungspunkts ausgeführt, der von Site Recovery verarbeitet wurde.
   - **Benutzerdefiniert**: Geben Sie einen Wiederherstellungspunkt an.

3. Klicken Sie auf **Der Computer wird vor Beginn des Failovers heruntergefahren**, wenn Site Recovery versuchen soll, den Quellcomputer herunterzufahren, bevor das Failover ausgelöst wird. Das Failover wird auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt.
4. Wenn Sie das Herstellen einer Verbindung mit der Azure-VM vorbereitet haben, stellen Sie eine Verbindung her, um sie nach dem Failover zu überprüfen.
5. **Committen** Sie das Failover nach der Überprüfung. Dadurch werden alle verfügbaren Wiederherstellungspunkte gelöscht.

> [!WARNING]
> Brechen Sie ein Failover in Bearbeitung nicht ab. Vor Beginn des Failovers wird die Replikation der Computer beendet. Wenn Sie ein Failover abbrechen, wird es beendet, ohne dass die Replikation des Computers erneut erfolgt.
> Bei physischen Servern kann die zusätzliche Failoververarbeitung ca. 8 bis 10 Minuten dauern.

## <a name="automate-actions-during-failover"></a>Automatisieren von Aktionen während des Failovers

Möglicherweise möchten Sie Aktionen während des Failovers automatisieren. Hierfür können Sie Skripts oder Azure Automation-Runbooks in Wiederherstellungsplänen verwenden.

- [Informieren Sie sich](site-recovery-create-recovery-plans.md) über Erstellung und Anpassung von Wiederherstellungsplänen (z. B. Hinzufügen von Skripts).
- [Informieren Sie sich](site-recovery-runbook-automation.md) über das Hinzufügen von Azure Automation-Runbooks zu Wiederherstellungsplänen.

## <a name="configure-settings-after-failover"></a>Konfigurieren von Einstellungen nach einem Failover

Nach dem Failover müssen Sie [Azure-Einstellungen konfigurieren](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover), um eine Verbindung zu den replizierten Azure-VMs herzustellen. Darüber hinaus müssen Sie [interne und öffentliche](site-recovery-failover.md#set-up-ip-addressing) IP-Adressen einrichten.

## <a name="prepare-for-reprotection-and-failback"></a>Vorbereiten für erneuten Schutz und Failback

Nachdem Sie ein Failover zu Azure ausgeführt haben, schützen Sie Azure-VMs wieder, indem Sie sie an den lokalen Standort replizieren. Nach der Replikation können Sie ein Failback zum lokalen Standort ausführen, indem Sie ein Failover von Azure zum lokalen Standort ausführen.

1. Für physische Server, die mithilfe von Site Recovery nach Azure repliziert wurden, kann ein Failback nur als VMware-VMs erfolgen. Sie benötigen eine VMware-Infrastruktur, damit Sie ein Failback ausführen können. Führen Sie die Schritte in [diesem Artikel](vmware-azure-prepare-failback.md) aus, um den erneuten Schutz und das Failback vorzubereiten, einschließlich der Einrichtung eines Prozessservers in Azure und eines lokalen Masterzielservers sowie der Konfiguration eines Site-to-Site-VPN oder des privaten Peerings von ExpressRoute für das Failback.
2. Stellen Sie sicher, dass der lokale Konfigurationsserver ausgeführt wird und mit Azure verbunden ist. Beim Failover zu Azure ist der lokale Standort möglicherweise nicht verfügbar. Der Konfigurationsserver ist deshalb möglicherweise nicht verfügbar oder heruntergefahren. Während des Failbacks muss der virtuellen Computer in der Konfigurationsserverdatenbank vorhanden sein. Andernfalls ist das Failback nicht erfolgreich.
3. Löschen Sie alle Momentaufnahmen auf dem lokalen Masterzielserver. Der erneute Schutz funktioniert nicht, wenn Momentaufnahmen vorhanden sind.  Die Momentaufnahmen auf dem virtuellen Computer werden während des Auftrags zum erneuten Schützen automatisch zusammengeführt.
4. Wenn Sie in einer Replikationsgruppe zusammengefasste virtuelle Computer erneut schützen, um die Konsistenz mehrerer virtueller Computer zu gewährleisten, stellen Sie sicher, dass sie alle dasselbe Betriebssystem (Windows oder Linux) verwenden, und stellen Sie sicher, dass der von Ihnen bereitgestellte Masterzielserver denselben Betriebssystemtyp aufweist. Alle virtuellen Computer in einer Replikationsgruppe müssen denselben Masterzielserver verwenden.
5. Öffnen Sie [die für das Failback erforderlichen Ports](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback).
6. Stellen Sie sicher, dass vor dem Failback eine Verbindung zu vCenter Server hergestellt wurde. Andernfalls führt das Trennen und erneute Anfügen von Datenträgern an den virtuellen Computer zu einem Fehler.
7. Wenn die virtuellen Computer, für die Sie das Failback ausführen möchten, von einem vCenter Server verwaltet werden, stellen Sie sicher, dass Sie über die erforderlichen Berechtigungen verfügen. Wenn Sie die vCenter-Ermittlung schreibgeschützter Benutzer ausführen und virtuelle Computer schützen, ist das Schützen erfolgreich, und das Failover funktioniert. Während des erneuten Schützens tritt beim Failover jedoch ein Fehler auf, da die Datenspeicher nicht ermittelt werden können und beim erneuten Schützen nicht aufgeführt werden. Zum Beheben dieses Problems können Sie die vCenter-Anmeldeinformationen mit einem [entsprechenden Konto aktualisieren, das über die erforderlichen Berechtigungen verfügt](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery), und dann den Auftrag wiederholen. 
8. Wenn Sie zum Erstellen virtueller Computer eine Vorlage verwendet haben, sollten Sie sicherstellen, dass jeder virtuelle Computer über eine eigene UUID für die Datenträger verfügt. Falls ein Konflikt zwischen der UUID des virtuellen Computers und der UUID des Masterziels besteht, weil beide mit der gleichen Vorlage erstellt wurden, tritt beim erneuten Schützen ein Fehler auf. Verwenden Sie für die Bereitstellung eine andere Vorlage.
9. Wenn Sie ein Failback zu einem anderen vCenter Server durchführen, stellen Sie sicher, dass der neue vCenter Server und der Masterzielserver erkannt werden. In der Regel sind die Datenspeicher nicht zugänglich oder nicht in **Erneutes Schützen** sichtbar, wenn sie es nicht werden.
10. In den folgenden Szenarios ist kein Failback möglich:
    - Sie verwenden entweder die kostenlose Edition von ESXi 5.5 oder die kostenlose Edition von vSphere 6 Hypervisor. Führen Sie ein Upgrade auf eine andere Version durch.
    - Sie verfügen über einen physischen Windows Server 2008 R2 SP1-Server.
    - Virtuelle Computer, die migriert wurden.
    - Ein virtueller Computer wurde in eine andere Ressourcengruppe verschoben.
    - Ein Replikat des virtuellen Azure-Computers wurde gelöscht.
    - Ein Replikat des virtuellen Azure-Computers ist nicht geschützt (und wird im lokalen Standort repliziert).
10. [Überprüfen Sie die Typen der Failbacks](concepts-types-of-failback.md), die Sie verwenden können: Wiederherstellung am ursprünglichen Standort und Wiederherstellung an einem anderen Standort.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Erneutes Schützen von Azure-VMs an einem alternativen Speicherort

Bei diesem Verfahren wird davon ausgegangen, dass der lokale virtuelle Computer nicht verfügbar ist.

1. Klicken Sie im Tresor auf **Einstellungen** > **Replizierte Elemente** und dann mit der rechten Maustaste auf den Computer, für den ein Failover ausgeführt wurde, und wählen Sie **Erneut schützen** aus.
2. Überprüfen Sie in **Erneut schützen**, ob **Azure auf lokal** ausgewählt ist.
3. Geben Sie den lokalen Masterzielserver und den Prozessserver an.
4. Wählen Sie in **Datenspeicher** den Masterziel-Datenspeicher aus, in dem die Datenträger lokal wiederhergestellt werden sollen.
       - Verwenden Sie diese Option, wenn der lokale virtuelle Computer gelöscht wurde oder nicht vorhanden ist und neue Datenträger erstellt werden müssen.
       - Diese Einstellung wird ignoriert, wenn die Datenträger bereits vorhanden sind. Sie müssen aber trotzdem einen Wert angeben.
5. Wählen Sie das Masterziel-Aufbewahrungslaufwerk aus. Die Failbackrichtlinie wird automatisch ausgewählt.
6. Klicken Sie auf **OK**, um das erneute Schützen zu starten. Ein Auftrag beginnt mit der Replikation des virtuellen Azure-Computers am lokalen Standort. Sie können den Fortschritt auf der Registerkarte **Aufträge** nachverfolgen.

> [!NOTE]
> Wenn Sie den virtuellen Azure-Computer unter einem vorhandenen lokalen virtuellen Computer wiederherstellen möchten, binden Sie den Datenspeicher des lokalen virtuellen Computers mit Lese-/Schreibzugriff auf dem ESXi-Host des Masterzielservers ein.


## <a name="fail-back-from-azure"></a>Failback von Azure

Führen Sie das Failover wie folgt aus:

1. Klicken Sie auf der Seite **Replizierte Elemente** mit der rechten Maustaste auf den Computer > **Ungeplantes Failover**.
2. Überprüfen Sie in **Failover bestätigen**, dass das Failover von Azure aus erfolgt.
3. Wählen Sie den Wiederherstellungspunkt aus, den Sie für das Failover verwenden möchten.
    - Es wird empfohlen, den **neuesten** Wiederherstellungspunkts zu verwenden. Der App-konsistente Punkt liegt vor dem aktuellen Zeitpunkt und verursacht einige Datenverluste.
    - Der **neueste** Wiederherstellungspunkt ist ein absturzkonsistenter Wiederherstellungspunkt.
    - Wenn das Failover ausgeführt wird, fährt Site Recovery die Azure-VMs herunter und startet den lokalen virtuellen Computer. Da dies mit einer gewissen Ausfallzeit verbunden ist, wählen Sie einen geeigneten Zeitpunkt.
4. Klicken Sie mit der rechten Maustaste auf den Computer, und klicken Sie auf **Committen**. Dadurch wird ein Auftrag ausgelöst, der die Azure-VMs entfernt.
5. Stellen Sie sicher, dass die Azure-VMs wie erwartet heruntergefahren wurden.


## <a name="reprotect-on-premises-machines-to-azure"></a>Erneutes Schützen lokaler Computer in Azure

Die Daten sollten nun wieder auf Ihrem lokalen Standort sein, aber sie werden nicht nach Azure repliziert. Sie können die Replikation nach Azure folgendermaßen erneut starten:

1. Wählen Sie im Tresor **Einstellungen**>**Replizierte Elemente** sowie die virtuellen Computer aus, für die das Failback durchgeführt wurde, und klicken Sie auf **Erneut schützen**.
2. Wählen Sie den Prozessserver, mit dem die replizierten Daten nach Azure gesendet werden, und klicken Sie auf **OK**.


## <a name="next-steps"></a>Nächste Schritte

Nachdem der Auftrag zum erneuten Schützen abgeschlossen wurde, wird der lokale virtuelle Computer nach Azure repliziert. Bei Bedarf können Sie [ein weiteres Failover](site-recovery-failover.md) zu Azure ausführen.
