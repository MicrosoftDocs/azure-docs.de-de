---
title: Vorbereiten von Azure VMware Solution für die Notfallwiederherstellung zu Azure Site Recovery
description: Hier erfahren Sie, wie Sie Azure VMware Solution-Server für die Notfallwiederherstellung in Azure mithilfe des Azure Site Recovery-Diensts vorbereiten.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 8e77ede7b04c95bfd6b6b8f660c8d811e7434c0f
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395443"
---
# <a name="prepare-azure-vmware-solution-for-disaster-recovery-to-azure-site-recovery"></a>Vorbereiten von Azure VMware Solution für die Notfallwiederherstellung zu Azure Site Recovery

In diesem Artikel wird beschrieben, wie Azure VMware Solution-Server mithilfe der [Azure Site Recovery](site-recovery-overview.md)-Dienste für die Notfallwiederherstellung in Azure vorbereitet werden. 

Dies ist das zweite in einer Reihe von Tutorials zur Einrichtung der Notfallwiederherstellung in Azure für VMs von Azure VMware Solution. Im ersten Tutorial haben Sie [die Azure-Komponenten eingerichtet](avs-tutorial-prepare-azure.md), die für die Azure VMware Solution-Notfallwiederherstellung benötigt werden.


In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Vorbereiten eines Kontos auf dem vCenter-Server oder dem vSphere ESXi-Host zum Automatisieren der VM-Ermittlung
> * Vorbereiten eines Kontos für die automatische Installation von Mobility Service auf virtuellen VMware-Computern
> * Überprüfen der Anforderungen und der Unterstützung für VMware-Server und virtuelle Computer
> * Vorbereiten der Verbindungsherstellung mit virtuellen Azure-Computern nach dem Failover

> [!NOTE]
> In den Tutorials wird der einfachste Bereitstellungspfad für ein Szenario erläutert. Sie verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade. Ausführliche Anweisungen finden Sie in dem Artikel, der im Anleitungsabschnitt des Site Recovery-Inhaltsverzeichnisses angegeben ist.

## <a name="before-you-start"></a>Vorbereitung

Vergewissern Sie sich, dass Azure wie im [ersten Tutorial dieser Reihe](avs-tutorial-prepare-azure.md) beschrieben vorbereitet wurde.

## <a name="prepare-an-account-for-automatic-discovery"></a>Vorbereiten eines Kontos für die automatische Ermittlung

Site Recovery benötigt Zugriff auf Azure VMware Solution-Server, um folgende Aufgaben durchzuführen:

- Automatisches Ermitteln von VMs. Dafür ist mindestens ein Konto mit Lesezugriff erforderlich.
- Orchestrieren von Replikation, Failover und Failback. Sie benötigen ein Konto, das berechtigt ist, Vorgänge wie das Erstellen und Entfernen von Datenträgern sowie das Einschalten virtueller Computer durchzuführen.

Erstellen Sie das Konto wie folgt:

1. Erstellen Sie zum Verwenden eines dedizierten Kontos eine Rolle auf vCenter-Ebene. Geben Sie der Rolle einen Namen wie **Azure_Site_Recovery**.
2. Weisen Sie der Rolle die Berechtigungen, die in der folgenden Tabelle aufgeführt sind, zu.
3. Erstellen Sie einen Benutzer auf dem vCenter-Server oder vSphere-Host. Weisen Sie dem Benutzer die Rolle zu.

### <a name="vmware-account-permissions"></a>VMware-Kontoberechtigungen

**Aufgabe** | **Rolle/Berechtigungen** | **Details**
--- | --- | ---
**VM-Ermittlung** | Mindestens ein Benutzer mit Lesezugriff<br/><br/> Data Center object (Rechenzentrenobjekt) –> Propagate to Child Object (An untergeordnetes Objekt weitergeben), role=Read-only (Rolle=schreibgeschützt) | Der Benutzer wird auf Datencenterebene zugewiesen und hat Zugriff auf alle Objekte im Datencenter.<br/><br/> Um den Zugriff einzuschränken, weisen Sie den untergeordneten Objekten (vSphere-Hosts, Datenspeicher, VMs und Netzwerke) die Rolle **No access** (Kein Zugriff) mit **Propagate to child object** (Auf untergeordnetes Objekt übertragen) zu.
**Vollständige Replikation, Failover, Failback** |  Erstellen Sie eine Rolle („Azure_Site_Recovery“) mit den erforderlichen Berechtigungen, und weisen Sie die Rolle dann einem VMware-Benutzer oder einer VMware-Gruppe zu.<br/><br/> Rechenzentrumsobjekt -> An untergeordnetes Objekt weitergeben, Rolle=Azure_Site_Recovery<br/><br/> Datenspeicher -> Speicherplatz zuordnen, Datenspeicher durchsuchen, Low-Level-Dateivorgänge, Datei entfernen, Dateien virtueller Computer aktualisieren<br/><br/> Netzwerk -> Netzwerk zuweisen<br/><br/> Ressource -> Zuweisen der VM zu einem Ressourcenpool, ausgeschaltete VM migrieren, eingeschaltete VM migrieren<br/><br/> Tasks (Aufgaben) -> Create task (Aufgabe erstellen), update task (Aufgabe aktualisieren)<br/><br/> Virtueller Computer -> Konfiguration<br/><br/> Virtueller Computer -> Interagieren -> Frage beantworten, Geräteverbindung, CD-Medien konfigurieren, Diskettenmedien konfigurieren, Ausschalten, Einschalten, VMware-Tools installieren<br/><br/> Virtueller Computer -> Inventar -> Erstellen, Registrieren, Registrierung aufheben<br/><br/> Virtueller Computer -> Bereitstellung -> Download virtueller Computer zulassen, Upload von Dateien virtueller Computer zulassen<br/><br/> Virtual machine -> Snapshots -> Remove snapshots | Der Benutzer wird auf Datencenterebene zugewiesen und hat Zugriff auf alle Objekte im Datencenter.<br/><br/> Um den Zugriff einzuschränken, weisen Sie den untergeordneten Objekten (vSphere-Hosts, Datenspeicher, VMs und Netzwerke) die Rolle **No access** (Kein Zugriff) mit **Propagate to child object** (Auf untergeordnetes Objekt übertragen) zu.

## <a name="prepare-an-account-for-mobility-service-installation"></a>Vorbereiten eines Kontos für die Installation des Mobility Services

Der Mobilitätsdienst muss auf Computern installiert sein, die Sie replizieren möchten. Site Recovery kann eine Pushinstallation dieses Diensts ausführen, wenn Sie die Replikation für einen Computer aktivieren. Sie haben auch die Möglichkeit, den Dienst manuell oder mithilfe von Installationstools zu installieren.

- In diesem Tutorial installieren wir den Mobilitätsdienst mit der Pushinstallation.
- Für diese Pushinstallation müssen Sie ein Konto vorbereiten, das Site Recovery verwenden kann, um auf die den virtuellen Computer zuzugreifen. Sie geben dieses Konto an, wenn Sie die Notfallwiederherstellung an der Azure-Konsole einrichten.

Bereiten Sie das Konto wie folgt vor:

Bereiten Sie ein Domänen- oder lokales Konto mit den Berechtigungen zum Installieren auf dem virtuellen Computer vor.

- **Virtuelle Windows-Computer** : Wenn Sie kein Domänenkonto verwenden, deaktivieren Sie für die Installation auf Windows-VMs die Remote-Benutzerzugriffssteuerung auf dem lokalen Computer. Dazu fügen Sie in der Registrierung unter **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** den DWORD-Eintrag **LocalAccountTokenFilterPolicy** mit dem Wert 1 hinzu.
- **Virtuelle Linux-Computer** : Für die Installation auf virtuellen Linux-Computern bereiten Sie einen Superuser auf dem Linux-Quellserver vor.


## <a name="check-vmware-requirements"></a>Überprüfen der VMware-Anforderungen

Stellen Sie sicher, dass VMware-Server und virtuelle Computer die Anforderungen erfüllen.

1. Überprüfen Sie die [Softwareversionen](../azure-vmware/concepts-private-clouds-clusters.md#vmware-software-versions) von Azure VMware Solution.
2. Überprüfen Sie die [Anforderungen für VMware-Server](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
3. [Überprüfen](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) Sie für virtuelle Linux-Computer die Dateisystem- und Speicheranforderungen. 
4. Überprüfen Sie die Unterstützung für [Netzwerke](vmware-physical-azure-support-matrix.md#network) und [Speicher](vmware-physical-azure-support-matrix.md#storage). 
5. Überprüfen Sie, was nach einem Failover für [Azure-Netzwerke](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [Speicher](vmware-physical-azure-support-matrix.md#azure-storage) und [Computer](vmware-physical-azure-support-matrix.md#azure-compute) unterstützt wird.
6. Ihre Azure VMware Solution-VMs, die Sie in Azure replizieren, müssen die [Azure-VM-Anforderungen](vmware-physical-azure-support-matrix.md#azure-vm-requirements) einhalten.
7. Auf virtuellen Linux-Computern sollte der Gerätename oder Bereitstellungspunktname eindeutig sein. Stellen Sie sicher, dass keine zwei Geräte/Bereitstellungspunkte denselben Namen aufweisen. Hinweis: Bei Namen wird die Groß-/Kleinschreibung nicht beachtet. So ist es beispielsweise nicht zulässig, zwei Geräte für denselben virtuellen Computers als _device1_ und _Device1_ zu benennen.


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Vorbereiten der Verbindungsherstellung mit Azure-VMs nach dem Failover

Nach einem Failover sollten Sie eine Verbindung mit den Azure-VMs über Ihr Azure VMware Solution-Netzwerk herstellen.

Gehen Sie wie folgt vor, wenn Sie die Verbindung mit Windows-VMs nach dem Failover per RDP herstellen möchten:

- **Zugriff auf das Internet**. Aktivieren Sie RDP auf der Azure VMware Solution-VM, bevor das Failover durchgeführt wird. Stellen Sie sicher, dass TCP- und UDP-Regeln für das Profil **Öffentlich** hinzugefügt werden und dass RDP unter **Windows-Firewall** > **Zugelassene Apps** für alle Profile zugelassen ist.
- **Standort-zu-Standort-VPN-Zugriff** :
    - Aktivieren Sie RDP auf der Azure VMware Solution-VM, bevor das Failover durchgeführt wird.
    - RDP sollte unter **Windows-Firewall** -> **Zugelassene Apps und Feature** für **private und Domänennetzwerke** zugelassen werden.
    - Achten Sie darauf, dass die SAN-Richtlinie des Betriebssystems auf **OnlineAll** festgelegt ist. [Weitere Informationen](https://support.microsoft.com/kb/3031135)
- Auf dem virtuellen Computer sollten keine ausstehenden Windows-Updates vorhanden sein, wenn Sie ein Failover auslösen. Andernfalls können Sie sich nach Abschluss des Updates nicht mehr bei dem virtuellen Computer anmelden.
- Aktivieren Sie nach dem Failover auf der Windows-Azure-VM die **Startdiagnose** , um einen Screenshot des virtuellen Computers anzuzeigen. Wenn Sie keine Verbindung herstellen können, überprüfen Sie, ob der virtuelle Computer ausgeführt wird, und sehen sich dann diese [Tipps zur Problembehandlung](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) an.

Gehen Sie wie folgt vor, wenn Sie die Verbindung mit Linux-VMs nach dem Failover per SSH herstellen möchten:

- Stellen Sie vor dem Failover sicher, dass der Secure Shell-Dienst auf der Azure VMware Solution-VM so eingerichtet ist, dass er automatisch beim Systemstart gestartet wird.
- Überprüfen Sie, ob die Firewallregeln eine SSH-Verbindung zulassen.
- Erlauben Sie nach dem Failover auf der Azure-VM in den Netzwerksicherheitsgruppen-Regeln eingehende Verbindungen am SSH-Port und für das Azure-Subnetz, mit dem der virtuelle Computer verbunden ist.
- Fügen Sie der VM eine [öffentliche IP-Adresse](./site-recovery-monitor-and-troubleshoot.md) hinzu.
- Sie können die **Startdiagnose** aktivieren, um einen Screenshot des virtuellen Computers anzuzeigen.


## <a name="failback-requirements"></a>Failbackanforderungen
Wenn Sie ein Failback auf Ihre Azure VMware Solution-Cloud planen, sollten Sie sich mit [einigen Voraussetzungen für Failbacks](avs-tutorial-reprotect.md#before-you-begin) vertraut machen. Falls gewünscht, können Sie die erforderlichen Vorbereitungen jetzt durchführen. Sie können damit aber auch bis nach dem Failover auf Azure warten.




## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Einrichten der Notfallwiederherstellung](avs-tutorial-replication.md)
- [Führen Sie eine Kapazitätsplanung durch](site-recovery-deployment-planner.md), wenn Sie mehrere VMs replizieren möchten.
