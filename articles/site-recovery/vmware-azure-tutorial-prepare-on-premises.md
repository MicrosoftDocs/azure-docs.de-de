---
title: Vorbereiten lokaler VMware-Server für die Notfallwiederherstellung von VMware-VMs in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie lokale VMware-Server mit dem Azure Site Recovery-Dienst für die Notfallwiederherstellung in Azure einrichten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 7a61edc01a87be8996b4d7dd5093f9d3554e6585
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417753"
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Vorbereiten lokaler VMware-Server für die Notfallwiederherstellung in Azure

In diesem Artikel erfahren Sie, wie Sie lokale VMware-Server mit den [Azure Site Recovery](site-recovery-overview.md)-Diensten für die Notfallwiederherstellung in Azure einrichten. 

Dies ist das zweite Tutorial in einer Reihe, welche die Einrichtung der Notfallwiederherstellung in Azure für lokale virtuelle VMware-Computer veranschaulicht. Im ersten Tutorial [richten wir die erforderlichen Azure-Komponenten](tutorial-prepare-azure.md) für die VMware-Notfallwiederherstellung ein.


In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Vorbereiten eines Kontos auf dem vCenter-Server oder dem vSphere ESXi-Host zum Automatisieren der VM-Ermittlung
> * Vorbereiten eines Kontos für die automatische Installation von Mobility Service auf virtuellen VMware-Computern
> * Überprüfen der Anforderungen und der Unterstützung für VMware-Server und virtuelle Computer
> * Vorbereiten der Verbindungsherstellung mit virtuellen Azure-Computern nach dem Failover

> [!NOTE]
> In den Tutorials wird der einfachste Bereitstellungspfad für ein Szenario erläutert. Sie verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade. Ausführliche Anweisungen finden Sie in dem Artikel, der im Anleitungsabschnitt des Site Recovery-Inhaltsverzeichnisses angegeben ist.

## <a name="before-you-start"></a>Vorbereitung

Vergewissern Sie sich, dass Azure wie im [ersten Tutorial dieser Reihe](tutorial-prepare-azure.md) beschrieben vorbereitet wurde.

## <a name="prepare-an-account-for-automatic-discovery"></a>Vorbereiten eines Kontos für die automatische Ermittlung

Site Recovery benötigt Zugriff auf VMware-Server, um folgende Aufgaben durchzuführen:

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

- **Virtuelle Windows-Computer:** Wenn Sie kein Domänenkonto verwenden, deaktivieren Sie für die Installation auf Windows-VMs die Remote-Benutzerzugriffssteuerung auf dem lokalen Computer. Dazu fügen Sie in der Registrierung unter **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** den DWORD-Eintrag **LocalAccountTokenFilterPolicy** mit dem Wert 1 hinzu.
- **Virtuelle Linux-Computer:** Für die Installation auf virtuellen Linux-Computern bereiten Sie ein root-Konto auf dem Linux-Quellserver vor.


## <a name="check-vmware-requirements"></a>Überprüfen der VMware-Anforderungen

Stellen Sie sicher, dass VMware-Server und virtuelle Computer die Anforderungen erfüllen.

1. [Überprüfen](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers) Sie die Anforderungen für VMware-Server.
2. [Überprüfen](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) Sie für virtuelle Linux-Computer die Dateisystem- und Speicheranforderungen. 
3. Überprüfen Sie die Unterstützung für das lokale [Netzwerk](vmware-physical-azure-support-matrix.md#network) und den lokalen [Speicher](vmware-physical-azure-support-matrix.md#storage). 
4. Überprüfen Sie, was nach einem Failover für [Azure-Netzwerke](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [Speicher](vmware-physical-azure-support-matrix.md#azure-storage) und [Computer](vmware-physical-azure-support-matrix.md#azure-compute) unterstützt wird.
5. Ihre lokalen virtuellen Computer, die Sie in Azure replizieren, müssen die [Azure-VM-Anforderungen](vmware-physical-azure-support-matrix.md#azure-vm-requirements) einhalten.
6. Auf virtuellen Linux-Computern sollte der Gerätename oder Bereitstellungspunktname eindeutig sein. Stellen Sie sicher, dass keine zwei Geräte/Bereitstellungspunkte denselben Namen aufweisen. Hinweis: Bei Namen wird die Groß-/Kleinschreibung nicht beachtet. So ist es beispielsweise nicht zulässig, zwei Geräte für denselben virtuellen Computers als _device1_ und _Device1_ zu benennen.


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Vorbereiten der Verbindungsherstellung mit Azure-VMs nach dem Failover

Nach einem Failover sollten Sie von Ihrem lokalen Netzwerk aus eine Verbindung mit den virtuellen Azure-Computern herstellen.

Gehen Sie wie folgt vor, wenn Sie die Verbindung mit Windows-VMs nach dem Failover per RDP herstellen möchten:

- **Zugriff auf das Internet**. Aktivieren Sie vor dem Failover RDP auf dem lokalen virtuellen Computer. Stellen Sie sicher, dass TCP- und UDP-Regeln für das Profil **Öffentlich** hinzugefügt werden und dass RDP unter **Windows-Firewall** > **Zugelassene Apps** für alle Profile zugelassen ist.
- **Standort-zu-Standort-VPN-Zugriff**:
    - Aktivieren Sie vor dem Failover RDP auf dem lokalen Computer.
    - RDP sollte unter **Windows-Firewall** -> **Zugelassene Apps und Feature** für **private und Domänennetzwerke** zugelassen werden.
    - Achten Sie darauf, dass die SAN-Richtlinie des Betriebssystems auf **OnlineAll** festgelegt ist. [Weitere Informationen](https://support.microsoft.com/kb/3031135)
- Auf dem virtuellen Computer sollten keine ausstehenden Windows-Updates vorhanden sein, wenn Sie ein Failover auslösen. Andernfalls können Sie sich nach Abschluss des Updates nicht mehr bei dem virtuellen Computer anmelden.
- Aktivieren Sie nach dem Failover auf der Windows-Azure-VM die **Startdiagnose**, um einen Screenshot des virtuellen Computers anzuzeigen. Wenn Sie keine Verbindung herstellen können, überprüfen Sie, ob der virtuelle Computer ausgeführt wird, und sehen sich dann diese [Tipps zur Problembehandlung](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) an.

Gehen Sie wie folgt vor, wenn Sie die Verbindung mit Linux-VMs nach dem Failover per SSH herstellen möchten:

- Überprüfen Sie vor dem Failover auf dem lokalen Computer, ob der Secure Shell-Dienst für den automatischen Start beim Systemstart aktiviert ist.
- Überprüfen Sie, ob die Firewallregeln eine SSH-Verbindung zulassen.
- Erlauben Sie nach dem Failover auf der Azure-VM in den Netzwerksicherheitsgruppen-Regeln eingehende Verbindungen am SSH-Port und für das Azure-Subnetz, mit dem der virtuelle Computer verbunden ist.
- Fügen Sie der VM eine [öffentliche IP-Adresse](site-recovery-monitoring-and-troubleshooting.md) hinzu.
- Sie können die **Startdiagnose** aktivieren, um einen Screenshot des virtuellen Computers anzuzeigen.


## <a name="failback-requirements"></a>Failbackanforderungen
Wenn Sie ein Failback auf Ihren lokalen Standort ausführen möchten, machen Sie sich mit den [Voraussetzungen für das Failback](vmware-azure-reprotect.md##before-you-begin) vertraut. Falls gewünscht, können Sie die erforderlichen Vorbereitungen jetzt durchführen. Sie können damit aber auch bis nach dem Failover auf Azure warten.



## <a name="next-steps"></a>Nächste Schritte

Richten Sie die Notfallwiederherstellung ein. Planen Sie die Kapazität, falls Sie mehrere virtuelle Computer replizieren.
> [!div class="nextstepaction"]
> [Einrichten der Notfallwiederherstellung in Azure für lokale VMware-VMs](vmware-azure-tutorial.md)
> [Informationen zum Azure Site Recovery-Bereitstellungsplaner für VMware in Azure](site-recovery-deployment-planner.md)
