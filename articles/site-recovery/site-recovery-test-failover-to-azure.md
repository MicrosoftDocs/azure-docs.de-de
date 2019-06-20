---
title: Ausführen einer Übung zur Notfallwiederherstellung in Azure mithilfe von Azure Site Recovery | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Azure Site Recovery-Dienst eine Übung zur Notfallwiederherstellung von der lokalen Umgebung in Azure durchführen.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 67cbd37becb1fe87a7f4f554f574b6e5219c9243
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399930"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Durchführen eines Notfallwiederherstellungsverfahrens in Azure 


Dieser Artikel beschreibt die Ausführung einer Übung zur Notfallwiederherstellung in Azure mithilfe eines Site Recovery-Testfailovers.  

Sie führen ein Testfailover aus, um Ihre Replikations- und Notfallwiederherstellungsstrategie ohne Datenverlust oder Ausfallzeiten zu überprüfen. Ein Testfailover hat keinerlei Auswirkungen auf die laufende Replikation oder Ihre Produktionsumgebung. Sie können ein Testfailover auf einem bestimmten virtuellen Computer (VM) ausführen oder nach einem [Wiederherstellungsplan](site-recovery-create-recovery-plans.md), der mehrere VMs enthält.


## <a name="run-a-test-failover"></a>Ausführen eines Testfailovers
Hier erfahren Sie, wie Sie ein Test-Failover für einen Wiederherstellungsplan durchführen. Wenn Sie ein Testfailover für einen einzelnen virtuellen Computer ausführen möchten, befolgen Sie die [hier](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm) genannten Schritte.

![Testfailover](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. Klicken Sie im Azure-Portal in der Site Recovery auf **Wiederherstellungspläne** > *wiederherstellungsplan_name* > **Testfailover**.
2. Wählen Sie einen **Wiederherstellungspunkt** für das Failover aus. Sie können eine der folgenden Optionen auswählen:
    - **Letzte Verarbeitung**: Mit dieser Option wird ein Failover aller VMs im Plan auf den letzten Wiederherstellungspunkt ausgeführt, der von Site Recovery verarbeitet wurde. Um den letzten Wiederherstellungspunkt für eine bestimmte VM zu finden, überprüfen Sie **Letzte Wiederherstellungspunkte** in den Einstellungen der VM. Diese Option bietet eine niedrige Recovery Time Objective (RTO), da keine Zeit für die Verarbeitung unverarbeiteter Daten aufgewendet wird.
    - **Letzte App-Konsistenz**: Mit dieser Option wird ein Failover aller VMs im Plan auf den letzten app-konsistenten Wiederherstellungspunkt ausgeführt, der von Site Recovery verarbeitet wurde. Um den letzten Wiederherstellungspunkt für eine bestimmte VM zu finden, überprüfen Sie **Letzte Wiederherstellungspunkte** in den Einstellungen der VM.
    - **Neueste**: Diese Option verarbeitet zuerst alle Daten, die an den Site Recovery-Dienst gesendet wurden, um vor dem Failover einen Wiederherstellungspunkt für jede VM zu erstellen. Diese Option bietet die niedrigste RPO (Recovery Point Objective), da die nach dem Failover erstellte VM über alle Daten verfügt, die bei Auslösung des Failovers zu Site Recovery repliziert wurden.
    - **Letzte Verarbeitung mit mehreren VMs**: Diese Option steht nur für Wiederherstellungspläne mit einer oder mehreren VMs zur Verfügung, bei denen Multi-VM-Konsistenz aktiviert ist. VMs, für die die Einstellung aktiviert ist, führen ein Failover auf den letzten allgemeinen Wiederherstellungspunkt mit Multi-VM-Konsistenz durch. Andere VMs führen ein Failover auf den letzten verarbeiteten Wiederherstellungspunkt durch.  
    - **Letzter anwendungskonsistenter Zeitpunkt (mehrere VMs)** : Diese Option steht nur für Wiederherstellungspläne mit einer oder mehreren VMs zur Verfügung, bei denen Multi-VM-Konsistenz aktiviert ist. VMs, die Teil einer Replikationsgruppe sind, führen ein Failover auf den letzten allgemeinen Wiederherstellungspunkt mit Multi-VM-Anwendungskonsistenz durch. Andere VMs führen ein Failover auf ihren letzten anwendungskonsistenten Wiederherstellungspunkt durch.
    - **Benutzerdefinierte**: Verwenden Sie diese Option für ein Failover einer bestimmten VM auf einen bestimmten Wiederherstellungspunkt.
3. Wählen Sie ein virtuelles Azure-Netzwerk aus, in dem Test-VMs erstellt werden.

    - Site Recovery versucht, Test-VMs in einem Subnetz mit demselben Namen und derselben IP-Adresse zu erstellen, die in den Einstellungen der VM unter **Compute und Netzwerk** angegeben sind.
    - Wenn ein Subnetz identischen Namens nicht im virtuellen Azure-Netzwerk für das Testfailover verfügbar ist, wird die Test-VM im (nach alphabetischer Reihenfolge) ersten Subnetz erstellt.
    - Wenn dieselbe IP-Adresse nicht im Subnetz verfügbar ist, erhält die VM im Subnetz eine andere verfügbare IP-Adresse. [Weitere Informationen](#create-a-network-for-test-failover)
4. Wenn Sie ein Failover auf Azure ausführen und die Datenverschlüsselung aktiviert ist, wählen Sie unter **Verschlüsselungsschlüssel** das Zertifikat aus, das beim Aktivieren der Verschlüsselung im Rahmen der Anbieterinstallation ausgestellt wurde. Sie können diesen Schritt ignorieren, wenn die Verschlüsselung nicht aktiviert ist.
5. Verfolgen Sie den Verlauf des Failovers auf der Registerkarte **Aufträge** . Der Test-Replikatcomputer wird im Azure-Portal angezeigt.
6. Zum Initiieren einer RDP-Verbindung mit der Azure-VM müssen Sie auf der Netzwerkschnittstelle der VM, für die ein Failover durchgeführt wurde, [eine öffentliche IP-Adresse hinzufügen](https://aka.ms/addpublicip).
7. Wenn alles wie erwartet funktioniert, klicken Sie auf **Testfailover bereinigen**. Dadurch werden die während des Testfailovers erstellten VMs gelöscht.
8. Erfassen und speichern Sie unter **Notizen** alle Beobachtungen im Zusammenhang mit dem Testfailover.


![Testfailover](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Wenn ein Testfailover ausgelöst wird, geschieht Folgendes:

1. **Voraussetzungen**: Eine Überprüfung der Voraussetzungen wird ausgeführt, um sicherzustellen, dass alle Bedingungen für ein Failover erfüllt sind.
2. **Failover**: Das Failover verarbeitet die Daten und bereitet sie vor, sodass auf ihrer Basis eine Azure-VM erstellt werden kann.
3. **Neueste**: Wenn Sie den neuesten Wiederherstellungspunkt gewählt haben, wird ein Wiederherstellungspunkt aus den Daten erstellt, die an den Dienst gesendet wurden.
4. **Start**: In diesem Schritt wird ein virtueller Azure-Computer aus den Daten erstellt, die im vorherigen Schritt verarbeitet wurden.

### <a name="failover-timing"></a>Failovertiming

In den folgenden Szenarien erfordert das Failover einen zusätzliche Zwischenschritt, der in der Regel ca. 8 bis 10 Minuten dauert:

* VMware-VMs, auf denen eine niedrigere Mobility Service-Version als 9.8 ausgeführt wird
* Physische Server
* VMware-Linux-VMs
* Als physische Server geschützte Hyper-V-VMs
* VMware-VM, auf der die folgenden Treiber keine Starttreiber sind:
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* VMware-VMs, bei denen DHCP nicht aktiviert ist, unabhängig davon, ob DHCP oder statische IP-Adressen verwendet werden.

In allen anderen Fällen ist dieser Zwischenschritt nicht erforderlich und der Zeitaufwand für das Failover deutlich niedriger.


## <a name="create-a-network-for-test-failover"></a>Erstellen eines Netzwerks für Testfailover

Für Testfailover sollten Sie ein Netzwerk wählen, das von dem in den Einstellungen **Compute und Netzwerk** für jede VM angegebenen Produktionswiederherstellungsstandort-Netzwerk isoliert ist. Standardmäßig ist ein virtuelles Azure-Netzwerk beim Erstellen von anderen Netzwerken isoliert. Das Testnetzwerk sollte Ihr Produktionsnetzwerk imitieren:

- Das Testnetzwerk muss dieselbe Anzahl von Subnetzen wie das Produktionsnetzwerk besitzen. Subnetze müssen die gleichen Namen aufweisen.
- Das Testnetzwerk muss den gleichen IP-Adressbereich verwenden.
- Aktualisieren Sie das DNS des Testnetzwerks mit der IP-Adresse, die in den Einstellungen **Compute und Netzwerk** für die DNS-VM angegeben ist. Unter [Überlegungen zum Test-Failover](site-recovery-active-directory.md#test-failover-considerations) finden Sie weitere Details.


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Testfailover auf ein Produktionsnetzwerk am Wiederherstellungsstandort

Sie sollten zwar ein von Ihrem Produktionsnetzwerk getrenntes Testnetzwerk verwenden, wenn Sie jedoch unbedingt eine Notfallwiederherstellung in Ihrem Produktionsnetzwerk üben möchten, beachten Sie Folgendes:

- Die primäre VM muss heruntergefahren sein, wenn Sie das Testfailover durchführen. Andernfalls werden zwei VMs mit derselben Identität gleichzeitig im selben Netzwerk ausgeführt. Dies kann unerwartete Folgen haben.
- Alle Änderungen an den für das Testfailover erstellten VMs gehen verloren, wenn Sie das Failover bereinigen. Diese Änderungen werden nicht wieder auf der primären VM repliziert.
- Das Testen in Ihrer Produktionsumgebung führt zu einem Ausfall Ihrer Produktionsanwendung. Benutzer sollten keine auf VMs ausgeführten Apps verwenden, während das Testfailover läuft.  



## <a name="prepare-active-directory-and-dns"></a>Vorbereiten von Active Directory und DNS

Wenn Sie ein Testfailover durchführen möchten, um eine Anwendung zu testen, muss in der Testumgebung eine Kopie der Active Directory-Produktionsumgebung vorhanden sein. Unter [Überlegungen zum Test-Failover](site-recovery-active-directory.md#test-failover-considerations) finden Sie weitere Informationen.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Vorbereiten der Verbindungsherstellung mit Azure-VMs nach dem Failover

Wenn Sie nach dem Failover per RDP/SSH eine Verbindung mit Azure-VMs herstellen möchten, müssen Sie die in der Tabelle zusammengefassten Anforderungen erfüllen.

**Failover** | **Location** | **Aktionen**
--- | --- | ---
**Virtueller Azure-Computer unter Windows** | Lokaler Computer vor dem Failover | Aktivieren Sie für den Zugriff auf die Azure-VM über das Internet RDP, stellen Sie sicher, dass TCP- und UDP-Regeln für **Öffentlich** hinzugefügt werden, und dass RDP unter **Windows-Firewall** > **Zugelassene Apps** für alle Profile zugelassen ist.<br/><br/> Aktivieren Sie für den Zugriff auf die Azure-VM über eine Standort-zu-Standort-Verbindung RDP auf dem Computer, und stellen Sie sicher, dass RDP unter **Windows-Firewall** -> **Zugelassene Apps und Features** für Netzwerke vom Typ **Domäne und Privat** zugelassen ist.<br/><br/>  Achten Sie darauf, dass die SAN-Richtlinie des Betriebssystems auf **OnlineAll** festgelegt ist. [Weitere Informationen](https://support.microsoft.com/kb/3031135)<br/><br/> Stellen Sie sicher, dass auf der VM keine ausstehenden Windows-Updates vorhanden sind, wenn Sie ein Failover auslösen. Ansonsten wird Windows Update möglicherweise während des Failovers gestartet, und Sie können sich nicht mehr bei der VM anmelden, bis das Update abgeschlossen ist.
**Virtueller Azure-Computer unter Windows** | Azure-VM nach einem Failover |  Fügen Sie der VM eine [öffentliche IP-Adresse](https://aka.ms/addpublicip) hinzu.<br/><br/> In den Netzwerksicherheitsgruppen-Regeln auf der VM nach dem Failover (und für das Azure-Subnetz, mit dem sie verbunden ist) müssen eingehende Verbindungen über den RDP-Port zulässig sein.<br/><br/> Aktivieren Sie die **Startdiagnose**, um einen Screenshot der VM anzuzeigen.<br/><br/> Wenn Sie keine Verbindung herstellen können, überprüfen Sie, ob die VM ausgeführt wird, und sehen sich dann diese [Tipps zur Problembehandlung](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) an.
**Virtueller Azure-Computer unter Linux** | Lokaler Computer vor dem Failover | Stellen Sie sicher, dass der Secure Shell-Dienst auf der VM so festgelegt ist, dass er beim Systemstart automatisch gestartet wird.<br/><br/> Überprüfen Sie, ob die Firewallregeln eine SSH-Verbindung damit zulassen.
**Virtueller Azure-Computer unter Linux** | Azure-VM nach einem Failover | In den Netzwerksicherheitsgruppen-Regeln auf der VM nach dem Failover (und für das Azure-Subnetz, mit dem sie verbunden ist) müssen eingehende Verbindungen über den SSH-Port zulässig sein.<br/><br/> Fügen Sie der VM eine [öffentliche IP-Adresse](https://aka.ms/addpublicip) hinzu.<br/><br/> Aktivieren Sie die **Startdiagnose**, um einen Screenshot der VM anzuzeigen.<br/><br/>

Führen Sie die [hier](site-recovery-failover-to-azure-troubleshoot.md) beschriebenen Schritte aus, um nach dem Failover ggf. Konnektivitätsprobleme zu beheben.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie eine Übung zur Notfallwiederherstellung abgeschlossen haben, informieren Sie sich über andere [Failover](site-recovery-failover.md)-Typen.
