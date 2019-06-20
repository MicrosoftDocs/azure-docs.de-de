---
title: Aktivieren der Replikation von virtuellen VMware-Computern für die Notfallwiederherstellung in Azure mit Azure Site Recovery | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie mithilfe von Azure Site Recovery die Replikation von virtuellen VMware-Computern für die Notfallwiederherstellung in Azure aktivieren.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 05/10/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: add0f8252bdae6857b28deeb7de4c1d09973e452
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540753"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Aktivieren der Replikation in Azure für VMware-VMs

Dieser Artikel beschreibt die Aktivierung der Replikation von lokalen VMware-VMs in Azure.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie Folgendes abgeschlossen haben:

- [Einrichten der lokalen Quellumgebung](vmware-azure-set-up-source.md)
- [Einrichten der Zielumgebung in Azure](vmware-azure-set-up-target.md)

## <a name="before-you-start"></a>Vorbereitung
Wenn Sie virtuelle VMware-Computer replizieren, beachten Sie Folgendes:

* Ihr Azure-Benutzerkonto benötigt bestimmte [Berechtigungen](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) zum Aktivieren der Replikation eines neuen virtuellen Computers in Azure.
* VMware-VMs werden alle 15 Minuten ermittelt. Es kann 15 Minuten oder länger dauern, bis virtuelle Computer nach der Ermittlung im Azure-Portal angezeigt werden. Ebenso kann die Ermittlung 15 Minuten oder länger dauern, wenn Sie einen neuen vCenter-Server oder vSphere-Host hinzufügen.
* Es kann auch 15 Minuten oder länger dauern, bis Umgebungsänderungen auf dem virtuellen Computer (z.B. die Installation von VMware-Tools) im Portal aktualisiert werden.
* Sie können den Zeitpunkt der letzten Ermittlung von virtuellen VMware-Computern überprüfen: Auf der Seite **Konfigurationsserver** befindet sich das Feld **Letzter Kontakt um** für den vCenter-Server/vSphere-Host.
* Wenn Sie virtuelle Computer für die Replikation hinzufügen möchten, ohne auf die planmäßige Ermittlung warten zu müssen, markieren Sie den Konfigurationsserver (ohne darauf zu klicken), und wählen Sie **Aktualisieren** aus.
* Wenn Sie die Replikation aktivieren, installiert der Prozessserver den Azure Site Recovery Mobility-Dienst automatisch auf dem virtuellen Computer, falls dieser vorbereitet ist.

## <a name="enable-replication"></a>Aktivieren der Replikation

Bevor Sie die Schritte in diesem Abschnitt ausführen, beachten Sie die Folgendes:
* Azure Site Recovery führt jetzt bei allen neuen Replikationen die Replikation direkt auf verwaltete Datenträger aus. Der Prozessserver schreibt Replikationsprotokolle in ein Cachespeicherkonto in der Zielregion. Diese Protokolle werden verwendet, um Wiederherstellungspunkte in verwalteten Replikatdatenträgern zu erstellen.
* Zum Zeitpunkt des Failovers wird der von Ihnen ausgewählte Wiederherstellungspunkt verwendet, um den verwalteten Zieldatenträger zu erstellen.
* Virtuelle Computer, die zuvor für die Replikation in Zielspeicherkonten konfiguriert wurden, sind davon nicht betroffen.
* Eine Replikation in Speicherkonten für einen neuen virtuellen Computer ist nur über die REST-API (Representational State Transfer) und Powershell verfügbar. Verwenden Sie die Azure REST-API-Version 2016-08-10 oder 2018-01-10 für die Replikation in Speicherkonten.

1. Fahren Sie fort mit **Schritt 2: Replizieren Sie die Anwendung** > **Quelle**. Wählen Sie nach der erstmaligen Aktivierung der Replikation im Tresor die Option **+Replizieren** aus, um die Replikation für weitere virtuelle Computer zu aktivieren.
2. Wählen Sie auf der Seite **Quelle** unter **Quelle** den Konfigurationsserver aus.
3. Wählen Sie unter **Computertyp** die Option **Virtuelle Computer** oder **Physische Computer** aus.
4. Wählen Sie unter **vCenter-/vSphere-Hypervisor** den vCenter-Server aus, der den vSphere-Host verwaltet, oder wählen Sie den Host aus. Wenn Sie physische Computer replizieren, ist diese Einstellung nicht relevant.
5. Wählen Sie den Prozessserver aus. Wurden keine zusätzlichen Prozessserver erstellt, ist der integrierte Prozessserver des Konfigurationsservers in der Dropdownliste verfügbar. Der Integritätsstatus der einzelnen Prozessserver wird gemäß der empfohlenen Grenzwerte und anderer Parameter angezeigt. Wählen Sie einen fehlerfreien Prozessserver aus. Ein [kritischer](vmware-physical-azure-monitor-process-server.md#process-server-alerts) Prozessserver kann nicht ausgewählt werden. Sie können entweder die Fehler [behandeln und beheben](vmware-physical-azure-troubleshoot-process-server.md) **oder** einen [horizontal hochskalierten Prozessserver](vmware-azure-set-up-process-server-scale.md) einrichten.
    ![Fenster „Replikation aktivieren: Quelle“](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> Ab [Version 9.24](service-updates-how-to.md#links-to-currently-supported-update-rollups) wurden weitere Warnungen eingeführt, um die Integritätswarnungen des Prozessservers zu verbessern. Aktualisieren Sie die Site Recovery-Komponenten auf Version 9.24 oder höher, damit alle Warnungen generiert werden.

6. Wählen Sie unter **Ziel** das Abonnement und die Ressourcengruppe aus, in dem bzw. der Sie die virtuellen Computer erstellen möchten, für ein Failover erfolgt ist. Wählen Sie das Bereitstellungsmodell aus, das Sie in Azure für die virtuellen Computer verwenden möchten, für die ein Failover erfolgt ist.
2. Wählen Sie das Azure-Netzwerk und das Subnetz aus, mit dem virtuelle Azure-Computer nach dem Failover eine Verbindung herstellen. Das Netzwerk muss sich in derselben Region wie der Tresor des Site Recovery-Diensts befinden.

   Wählen Sie **Jetzt für die ausgewählten Computer konfigurieren** aus, um die Netzwerkeinstellung auf alle virtuellen Computer anzuwenden, die geschützt werden sollen. Wählen Sie **Später konfigurieren** aus, um das Azure-Netzwerk über einzelne virtuelle Computer auszuwählen. Wenn Sie über kein Netzwerk verfügen, müssen Sie ein Netzwerk erstellen. Wählen Sie zum Erstellen eines Netzwerks mit Azure Resource Manager **Neu erstellen** aus. Wählen Sie ein Subnetz aus (falls zutreffend), und wählen Sie dann **OK** aus.
   
   ![Fenster „Replikation aktivieren: Ziel“](./media/vmware-azure-enable-replication/enable-rep3.png)

1. Wählen Sie unter **Virtuelle Computer** > **Virtuelle Computer auswählen** die virtuellen Computer aus, die Sie replizieren möchten. Sie können nur virtuelle Computer auswählen, für welche die Replikation aktiviert werden kann. Wählen Sie dann **OK**aus. Wenn Sie keinen bestimmten virtuellen Computer finden bzw. auswählen können, finden Sie unter [Quellcomputer ist nicht im Azure-Portal aufgeführt](https://aka.ms/doc-plugin-VM-not-showing) eine Lösung für dieses Problem.

    ![Fenster „Replikation aktivieren: Virtuelle Computer auswählen“](./media/vmware-azure-enable-replication/enable-replication5.png)

1. Wählen Sie unter **Eigenschaften** > **Eigenschaften konfigurieren** das Konto aus, das der Prozessserver zum automatischen Installieren des Site Recovery Mobility-Diensts auf dem virtuellen Computer verwenden soll. Wählen Sie außerdem den Typ des verwalteten Zieldatenträgers aus, auf den anhand Ihrer Datenänderungsmuster die Replikation erfolgen soll.
10. Standardmäßig werden alle Datenträger eines virtuellen Quellcomputers repliziert. Wenn Sie Datenträger von der Replikation ausschließen möchten, deaktivieren Sie das Kontrollkästchen **Einschließen** für alle Datenträger, die nicht repliziert werden sollen. Wählen Sie dann **OK**aus. Sie können später weitere Eigenschaften festlegen. Weitere Informationen finden Sie unter [Ausschließen von Datenträgern](vmware-azure-exclude-disk.md).

    ![Fenster „Replikation aktivieren: Eigenschaften konfigurieren“](./media/vmware-azure-enable-replication/enable-replication6.png)

1. Überprüfen Sie unter **Replikationseinstellungen** > **Replikationseinstellungen konfigurieren**, ob die richtige Replikationsrichtlinie ausgewählt ist. Sie können die Replikationsrichtlinieneinstellungen unter **Einstellungen** > **Replikationsrichtlinien** > ***Richtlinienname*** > **Einstellungen bearbeiten** ändern. Änderungen, die Sie an einer Richtlinie vornehmen, werden auch auf die Replikation und neue virtuelle Computer angewendet.
1. Aktivieren Sie **Multi-VM-Konsistenz**, wenn Sie virtuelle Computer in einer Replikationsgruppe zusammenfassen möchten. Geben Sie einen Namen für die Gruppe ein, und wählen Sie dann **OK** aus.

    > [!NOTE]
    >    * Die virtuellen Computer in einer Replikationsgruppe werden gemeinsam repliziert und verfügen beim Failover über gemeinsame ausfallsichere und App-konsistente Wiederherstellungspunkte.
    >    * Fassen Sie VMs und physische Server zusammen, damit sie Ihre Workloads widerspiegeln. Das Aktivieren von Multi-VM-Konsistenz kann die Workloadleistung beeinträchtigen. Nutzen Sie diese Option nur, wenn mehrere Computer dieselbe Workload ausführen und Konsistenz erforderlich ist.

    ![Fenster „Replikation aktivieren“](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Klicken Sie auf **Replikation aktivieren**. Sie können den Fortschritt des Auftrags **Schutz aktivieren** unter **Einstellungen** > **Aufträge** > **Site Recovery-Aufträge** verfolgen. Nachdem der Auftrag **Schutz abschließen** ausgeführt wurde, ist der virtuelle Computer bereit zum Failover.

## <a name="view-and-manage-vm-properties"></a>Anzeigen und Verwalten von VM-Eigenschaften

Überprüfen Sie als Nächstes die Eigenschaften des virtuellen Quellcomputers. Beachten Sie, dass der Name der Azure-VM die [Anforderungen für virtuelle Azure-Computer](vmware-physical-azure-support-matrix.md#replicated-machines) erfüllen muss.

1. Klicken Sie auf **Einstellungen** > **Replizierte Elemente**, und wählen Sie dann den virtuellen Computer aus. Die Seite **Zusammenfassung** enthält Informationen zu den Einstellungen und zum Status des virtuellen Computers.
1. Unter **Eigenschaften** können Sie die Informationen zur Replikation und zum Failover für den virtuellen Computer anzeigen.
1. Unter **Compute und Netzwerk** > **Compute-Eigenschaften** können Sie mehrere Eigenschaften von virtuellen Computern ändern. 

    ![Fenster „Compute- und Netzwerkeigenschaften“](./media/vmware-azure-enable-replication/vmproperties.png)

    * Name des virtuellen Azure-Computers: Ändern Sie ggf. den Namen, damit er den Azure-Anforderungen entspricht.
    * Größe oder Typ des virtuellen Zielcomputers: Die Standard-VM-Größe wird basierend auf einigen Parametern ausgewählt, zu denen Anzahl der Datenträger, NIC-Anzahl, Anzahl der CPU-Kerne, Arbeitsspeicher und verfügbare VM-Rollengrößen in der Azure-Zielregion zählen. Azure Site Recovery wählt die erste verfügbare VM-Größe aus, die alle Kriterien erfüllt. Sie können je nach Bedarf jederzeit vor dem Failover eine andere VM-Größe auswählen. Beachten Sie, dass die VM-Datenträgergröße auch auf der Größe des Quelldatenträgers basiert, und die kann nur nach dem Failover geändert werden. Weitere Informationen zu Datenträgergrößen und IOPS-Raten finden Sie unter [Skalierbarkeits- und Leistungsziele für VM-Datenträger unter Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Ressourcengruppe: Sie können eine [Ressourcengruppe](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines) auswählen, der ein virtueller Computer nach einem Failover angehören soll. Sie können diese Einstellung jederzeit vor dem Failover ändern. Wenn Sie den virtuellen Computer nach dem Failover zu einer anderen Ressourcengruppe migrieren, funktionieren die Schutzeinstellungen des virtuellen Computers nicht mehr.
    * Verfügbarkeitsgruppe: Sie können eine [Verfügbarkeitsgruppe](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) auswählen, wenn der virtuelle Computer nach dem Failover einer Verfügbarkeitsgruppe angehören muss. Bei Auswahl einer Verfügbarkeitsgruppe sollten Sie Folgendes beachten:

        * Es werden nur Verfügbarkeitsgruppen aufgelistet, die der angegebenen Ressourcengruppe angehören.  
        * Virtuelle Computer in unterschiedlichen virtuellen Netzwerken können nicht der gleichen Verfügbarkeitsgruppe angehören.
        * Einer Verfügbarkeitsgruppe dürfen nur virtuelle Computer gleicher Größe angehören.
1. Sie können auch Informationen zum Zielnetzwerk, zum Subnetz sowie zu der IP-Adresse hinzufügen, die dem virtuellen Azure-Computer zugewiesen ist.
2. Unter **Datenträger** werden das Betriebssystem und die Datenträger auf der VM angezeigt, die repliziert werden.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurieren von Netzwerken und IP-Adressen

Sie können die Ziel-IP-Adresse festlegen. Wenn Sie keine Adresse angeben, wird für den virtuellen Computer, für den das Failover durchgeführt wurde, DHCP verwendet. Wenn Sie eine Adresse festlegen, die beim Failover nicht verfügbar ist, funktioniert das Failover nicht. Wenn die Adresse im Testfailover-Netzwerk verfügbar ist, kann dieselbe IP-Zieladresse für das Testfailover verwendet werden.

Die Anzahl der Netzwerkadapter hängt von der Größe ab, die Sie für den virtuellen Zielcomputer angeben. Hierbei gilt Folgendes:

- Wenn die Anzahl der Netzwerkadapter des virtuellen Quellcomputers der Anzahl der für die Größe des virtuellen Zielcomputers zulässigen Netzwerkadapter entspricht oder diese Anzahl unterschreitet, verfügt der Zielcomputer über dieselbe Anzahl von Netzwerkadaptern wie der Quellcomputer.
- Wenn die Anzahl der Netzwerkadapter für den virtuellen Quellcomputer die maximal zulässige Anzahl für die Größe des virtuellen Zielcomputers überschreitet, wird die Anzahl verwendet, die maximal für die Größe des virtuellen Zielcomputers zulässig ist. Beispiel: Wenn ein Quellcomputer zwei Netzwerkadapter hat und der virtuelle Zielcomputer aufgrund seiner Größe vier Netzwerkadapter unterstützt, erhält der virtuelle Zielcomputer zwei Netzwerkadapter. Wenn der virtuelle Quellcomputer zwei Netzwerkadapter hat, der virtuelle Zielcomputer aufgrund seiner Größe aber nur einen Adapter unterstützt, erhält der virtuelle Zielcomputer nur einen Adapter.
- Wenn der virtuelle Computer über mehrere Netzwerkkarten verfügt, werden alle mit dem gleichen Netzwerk verbunden. Außerdem wird der erste in der Liste angezeigte Adapter als *Standard*netzwerkadapter des virtuellen Azure-Computers festgelegt. 

### <a name="azure-hybrid-benefit"></a>Azure-Hybridvorteil

Microsoft Software Assurance-Kunden können den Azure-Hybridvorteil nutzen, um Einsparungen bei den Lizenzierungskosten für Windows Server-Computer zu erzielen, die nach Azure migriert werden. Der Vorteil gilt auch für die Azure-Notfallwiederherstellung. Wenn Sie berechtigt sind, können Sie den Vorteil dem virtuellen Computer zuweisen, den Site Recovery bei einem Failover erstellt. Gehen Sie dazu folgendermaßen vor:
1. Navigieren Sie zum Abschnitt **Computer- und Netzwerkeigenschaften** des replizierten virtuellen Computers.
2. Sie werden gefragt, ob Sie eine Windows Server-Lizenz besitzen, die Sie zur Nutzung des Azure-Hybridvorteils berechtigt.
3. Bestätigen Sie, dass Sie eine berechtigte Windows Server-Lizenz mit Software Assurance haben, die Sie für die Anwendung des Hybridnutzungsvorteils für den virtuellen Computer, der bei einem Failover erstellt wird, verwenden können.
4. Speichern Sie die Einstellungen für den replizierten virtuellen Computer.

Weitere Informationen zum Azure-Hybridvorteil finden Sie [hier](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="resolve-common-issues"></a>Beheben allgemeiner Probleme

* Jeder Datenträger sollte kleiner als 4 TB sein.
* Bei dem Betriebssystem-Datenträger sollte es sich um einen Basisdatenträger, und nicht um einen dynamischen Datenträger handeln.
* Bei virtuellen UEFI-Computern/virtuellen Computern der zweiten Generation muss es sich bei der Betriebssystemfamilie um Windows handeln, und der Startdatenträger muss kleiner als 300 GB sein.

## <a name="next-steps"></a>Nächste Schritte

Nachdem der virtuelle Computer einen geschützten Zustand erreicht hat, versuchen Sie ein [Failover](site-recovery-failover.md), um zu überprüfen, ob Ihre Anwendung in Azure angezeigt wird.

* Erfahren Sie, wie Sie [Registrierungs- und Schutzeinstellungen bereinigen](site-recovery-manage-registration-and-protection.md), um die Replikation zu deaktivieren.
* Erfahren Sie, wie Sie die [Replikation für Ihre virtuellen Computer mithilfe von Powershell automatisieren](vmware-azure-disaster-recovery-powershell.md).
