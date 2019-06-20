---
title: Ausführen einer Übung für die Notfallwiederherstellung von Hyper-V-VMs an einem sekundären Standort mit Azure Site Recovery | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Übung für die Notfallwiederherstellung von Hyper-V-VMs in VMM-Clouds in einem sekundären Rechenzentrum mit Azure Site Recovery ausführen.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: dc8deb16f7d124c5fb11568f25050eee99a245b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60865516"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Ausführen eines DR-Drills für Hyper-V-VMs an einen sekundären Standort


Dieser Artikel beschreibt, wie Sie einen DR-Drill (Disaster Recovery, Notfallwiederherstellung) für Hyper-V-VMs, die in System Center Virtual Machine Manager-Clouds (VMM-Clouds) verwaltet werden, mithilfe von [Azure Site Recovery](site-recovery-overview.md) an einen sekundären lokalen Standort ausführen.

Sie führen ein Testfailover aus, um Ihre Replikationsstrategie zu überprüfen, und führen dann einen DR-Drill ohne Datenverlust oder Ausfallzeiten aus. Ein Testfailover besitzt keinerlei Auswirkungen auf die laufende Replikation oder Ihre Produktionsumgebung. 

## <a name="how-do-test-failovers-work"></a>Wie funktionieren Testfailover?

Sie führen ein Testfailover vom primären Standort an den sekundären Standort aus. Wenn Sie einfach nur überprüfen möchten, ob eine VM ein Failover ausführt, können Sie ein Testfailover ausführen, ohne eine Umgebung am sekundären Standort einzurichten. Wenn Sie überprüfen möchten, ob das App-Failover wie erwartet funktioniert, müssen Sie ein Netzwerk und Infrastruktur am sekundären Standort einrichten.
- Sie können ein Failover für einen einzelnen virtuellen Computer oder für einen [Wiederherstellungsplan](site-recovery-create-recovery-plans.md) ausführen.
- Sie können ein Testfailover ohne Netzwerk, mit einem vorhandenen Netzwerk oder mit einem automatisch erstellten Netzwerk ausführen. Weitere Details zu diesen Optionen werden in der Tabelle unten bereitgestellt.
    - Sie können ein Testfailover ohne Netzwerk ausführen. Diese Option ist nützlich, wenn Sie einfach bestätigen möchten, dass ein virtueller Computer ein Failover ausführen kann. Es ist jedoch nicht möglich, die Netzwerkkonfiguration zu überprüfen.
    - Ausführen des Failovers mit einem vorhandenen Netzwerk. Es wird empfohlen, kein Produktionsnetzwerk zu verwenden.
    - Führen Sie das Failover aus, und lassen Sie Site Recovery automatisch ein Testnetzwerk erstellen. In diesem Fall erstellt Site Recovery das Netzwerk automatisch und bereinigt es, nachdem das Testfailover abgeschlossen wurde.
- Sie müssen einen Wiederherstellungspunkt für das Testfailover auswählen: 
    - **Letzte Verarbeitung**: Mit dieser Option wird ein Failover des virtuellen Computers auf den letzten Wiederherstellungspunkt ausgeführt, der von Site Recovery verarbeitet wurde. Diese Option bietet eine niedrige Recovery Time Objective (RTO), da keine Zeit für die Verarbeitung unverarbeiteter Daten aufgewendet wird.
    - **Letzter anwendungskonsistenter Zeitpunkt**: Mit dieser Option wird ein Failover des virtuellen Computers auf den letzten anwendungskonsistenten Wiederherstellungspunkt ausgeführt, der von Site Recovery verarbeitet wurde. 
    - **Letzter Zeitpunkt**: Diese Option verarbeitet zuerst alle Daten, die an den Site Recovery-Dienst gesendet wurden, um vor dem Failover einen Wiederherstellungspunkt für jede VM zu erstellen. Diese Option bietet die niedrigste RPO (Recovery Point Objective), da die nach dem Failover erstellte VM über alle Daten verfügt, die bei Auslösung des Failovers zu Site Recovery repliziert wurden.
    - **Letzte Verarbeitung mit mehreren VMs**: Verfügbar für Wiederherstellungspläne mit mindestens einer VM, für die Multi-VM-Konsistenz aktiviert ist. VMs, für die die Einstellung aktiviert ist, führen ein Failover auf den letzten allgemeinen Wiederherstellungspunkt mit Multi-VM-Konsistenz durch. Andere VMs führen ein Failover auf den letzten verarbeiteten Wiederherstellungspunkt durch.
    - **Letzter anwendungskonsistenter Zeitpunkt (mehrere VMs)** : Diese Option steht nur für Wiederherstellungspläne mit einer oder mehreren VMs zur Verfügung, bei denen Multi-VM-Konsistenz aktiviert ist. VMs, die Teil einer Replikationsgruppe sind, führen ein Failover auf den letzten allgemeinen Wiederherstellungspunkt mit Multi-VM-Anwendungskonsistenz durch. Andere VMs führen ein Failover auf ihren letzten anwendungskonsistenten Wiederherstellungspunkt durch.
    - **Benutzerdefinierte**: Verwenden Sie diese Option für ein Failover einer bestimmten VM auf einen bestimmten Wiederherstellungspunkt.



## <a name="prepare-networking"></a>Vorbereiten des Netzwerks

Bei einem Testfailover werden Sie zum Auswählen von Netzwerkeinstellungen für die Testreplikatcomputer aufgefordert. In der Tabelle werden die Optionen zusammengefasst.

| **Option** | **Details** | |
| --- | --- | --- |
| **Keine** | Der virtuelle Testcomputer wird auf dem Host erstellt, auf dem sich auch der virtuelle Replikatcomputer befindet. Er wird nicht der Cloud hinzugefügt und ist mit keinem Netzwerk verbunden.<br/><br/> Der Computer kann nach der Erstellung mit einem VM-Netzwerk verbunden werden.| |
| **Vorhandene verwenden** | Der virtuelle Testcomputer wird auf dem Host erstellt, auf dem sich auch der virtuelle Replikatcomputer befindet. Er wird nicht der Cloud hinzugefügt.<br/><br/>Erstellen Sie ein von Ihrem Produktionsnetzwerk isoliertes VM-Netzwerk.<br/><br/>Bei Verwendung eines VLAN-basierten Netzwerks empfiehlt es sich, hierzu in VMM ein separates logisches Netzwerk zu erstellen, das nicht in der Produktionsumgebung verwendet wird. Dieses logische Netzwerk dient zum Erstellen von VM-Netzwerken für Testfailover.<br/><br/>Das logische Netzwerk muss mindestens einem der Netzwerkadapter aller Hyper-V-Server zugeordnet werden, die virtuelle Computer hosten.<br/><br/>Bei logischen VLAN-Netzwerken müssen die Netzwerkstandorte, die Sie dem logischen Netzwerk hinzufügen, isoliert sein.<br/><br/>Bei Verwendung eines logischen Netzwerks, das auf der Windows-Netzwerkvirtualisierung basiert, erstellt Azure Site Recovery automatisch isolierte VM-Netzwerke. | |
| **Netzwerk erstellen** | Auf der Grundlage der Einstellung, die Sie unter **Logisches Netzwerk** angeben und den zugehörigen Netzwerkstandorten wird automatisch ein temporäres Testnetzwerk erstellt.<br/><br/> Beim Failover wird überprüft, ob die virtuellen Computer erstellt werden.<br/><br/> Sie sollten diese Option verwenden, wenn für den Wiederherstellungsplan mehrere VM-Netzwerke verwendet werden.<br/><br/> Im Falle von Netzwerken, die auf der Windows-Netzwerkvirtualisierung basieren, kann diese Option im Netzwerk des virtuellen Replikatcomputers automatisch VM-Netzwerke mit den gleichen Einstellungen (Subnetze und IP-Adresspools) erstellen. Diese VM-Netzwerke werden nach dem Test-Failover automatisch bereinigt.<br/><br/> Der virtuelle Testcomputer wird auf dem Host erstellt, auf dem sich auch der virtuelle Replikatcomputer befindet. Er wird nicht der Cloud hinzugefügt.|

### <a name="best-practices"></a>Bewährte Methoden

- Das Testen eines Produktionsnetzwerks führt zu einem Ausfall von Produktionsworkloads. Bitten Sie Ihre Benutzer, während der Notfallwiederherstellungsübung keine zugehörigen Apps zu verwenden.

- Das Testnetzwerk muss nicht dem für das Testfailover verwendeten Typ des logischen VMM-Netzwerks entsprechen. Einige Kombinationen funktionieren jedoch nicht:

     - Wenn das Replikat DHCP und eine VLAN-basierte Isolation verwendet, benötigt das VM-Netzwerk für das Replikat keinen statischen IP-Adresspool. In diesem Fall kann für das Testfailover also keine Windows-Netzwerkvirtualisierung verwendet werden, da keine Adresspools verfügbar sind. 
        
     - Das Testfailover funktioniert nicht, wenn das Replikatnetzwerk keine Isolation verwendet und im Testnetzwerk Windows-Netzwerkvirtualisierung verwendet wird. Das liegt daran, dass das nicht isolierte Netzwerk nicht über die Subnetze verfügt, die zum Erstellen eines Netzwerks mit Windows-Netzwerkvirtualisierung erforderlich sind.
        
- Es wird empfohlen, nicht das für die Netzwerkzuordnung ausgewählte Netzwerk für das Testfailover zu verwenden.

- Auf welche Weise nach dem Failover eine Verbindung zwischen virtuellen Replikatcomputern und zugeordneten VM-Netzwerken besteht, hängt davon ab, wie das VM-Netzwerk in der VMM-Konsole konfiguriert ist.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Ohne Isolation oder VLAN-Isolation konfiguriertes VM-Netzwerk

Wenn ein VM-Netzwerk ohne Isolation oder mit VLAN-Isolation in VMM konfiguriert ist, ist Folgendes zu beachten:

- Wenn DHCP für das VM-Netzwerk definiert ist, wird der virtuelle Replikatcomputer mit der VLAN-ID verbunden. Hierbei kommen die Einstellungen zur Anwendung, die für den Netzwerkstandort im zugeordneten logischen Netzwerk angegeben sind. Der virtuelle Computer erhält seine IP-Adresse vom verfügbaren DHCP-Server.
- Sie müssen keinen statischen IP-Adresspool für das VM-Zielnetzwerk definieren. Bei Verwendung eines statischen IP-Adresspools für das VM-Netzwerk wird der virtuelle Replikatcomputer mit der VLAN-ID verbunden. Hierbei kommen die Einstellungen zur Anwendung, die für den Netzwerkstandort im zugeordneten logischen Netzwerk angegeben sind.
- Der virtuelle Computer erhält seine IP-Adresse aus dem für das VM-Netzwerk definierten Adresspool. Ist für das VM-Zielnetzwerk kein statischer IP-Adresspool definiert, ist die IP-Adresszuweisung nicht erfolgreich. Erstellen Sie den IP-Adresspool sowohl auf dem Quell- als auch auf dem Ziel-VMM-Server, den Sie für den Schutz und die Wiederherstellung verwenden.

### <a name="vm-network-with-windows-network-virtualization"></a>VM-Netzwerk mit Windows-Netzwerkvirtualisierung

Wenn ein VM-Netzwerk mit Windows-Netzwerkvirtualisierung in VMM konfiguriert ist, ist Folgendes zu beachten:

- Sie sollten für das VM-Zielnetzwerk einen statischen Adresspool definieren. Dies ist unabhängig davon, ob für das VM-Quellnetzwerk die Verwendung von DHCP oder die Verwendung eines statischen IP-Adresspools konfiguriert ist. 
- Bei Verwendung von DHCP fungiert der VMM-Zielserver als DHCP-Server und stellt eine IP-Adresse aus dem für das VM-Zielnetzwerk definierten Pool bereit.
- Ist für den Quellserver die Verwendung eines statischen IP-Adresspools definiert, weist der VMM-Zielserver eine IP-Adresse aus dem Pool zu. In beiden Fällen ist die IP-Adresszuweisung nicht erfolgreich, wenn kein statischer IP-Adresspool definiert ist.



## <a name="prepare-the-infrastructure"></a>Vorbereiten der Infrastruktur

Wenn Sie einfach überprüfen möchten, ob ein virtueller Computer ein Failover ausführen kann, können Sie ein Testfailover ohne Infrastruktur ausführen. Wenn Sie einen vollständigen DR-Drill ausführen möchten, um das App-Failover zu testen, müssen Sie die Infrastruktur am sekundären Standort vorbereiten:

- Wenn Sie ein Testfailover mit einem vorhandenen Netzwerk ausführen, bereiten Sie Active Directory, DHCP und DNS in diesem Netzwerk vor.
- Wenn Sie ein Testfailover mit der Option zum automatischen Erstellen eines VM-Netzwerks ausführen, müssen Sie dem automatisch erstellten Netzwerk Infrastrukturressourcen hinzufügen, bevor Sie das Testfailover ausführen. In einem Wiederherstellungsplan erreichen Sie dies durch Hinzufügen eines manuellen Schritts vor Group-1 im Wiederherstellungsplan, den Sie für das Testfailover verwenden. Fügen Sie anschließend die Infrastrukturressourcen zum automatisch erstellten Netzwerk hinzu, bevor Sie das Testfailover durchführen.


### <a name="prepare-dhcp"></a>Vorbereiten von DHCP
Wenn die am Testfailover beteiligten virtuellen Computer DHCP nutzen, muss innerhalb des isolierten Netzwerks, das für das Testfailover erstellt wurde, ein DHCP-Testserver erstellt werden.


### <a name="prepare-active-directory"></a>Vorbereiten von Active Directory
Wenn Sie ein Test-Failover durchführen möchten, um eine Anwendung zu testen, muss in der Testumgebung eine Kopie der Active Directory-Produktionsumgebung vorhanden sein. Weitere Informationen finden Sie unter [Überlegungen zum Testfailover für Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Vorbereiten von DNS
Gehen Sie wie folgt vor, um einen DNS-Server für das Test-Failover vorzubereiten:

* **DHCP**: Wenn virtuelle Computer DHCP nutzen, muss die IP-Adresse des Test-DNS auf dem DHCP-Testserver aktualisiert werden. Bei Verwendung der Windows-Netzwerkvirtualisierung fungiert der VMM-Server als DHCP-Server. Daher sollte die IP-Adresse des DNS im Testfailovernetzwerk aktualisiert werden. In diesem Fall werden die virtuellen Computer automatisch beim relevanten DNS-Server registriert.
* **Statische Adresse**: Wenn virtuelle Computer eine statische IP-Adresse verwenden, muss die IP-Adresse des DNS-Testservers im Testfailovernetzwerk aktualisiert werden. Das DNS muss eventuell mit der IP-Adresse der virtuellen Testcomputer aktualisiert werden. Hierzu können Sie das folgende Beispielskript verwenden:

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>Ausführen eines Testfailovers

Hier erfahren Sie, wie Sie ein Test-Failover für einen Wiederherstellungsplan durchführen. Alternativ können Sie das Failover über die Registerkarte **Virtuelle Computer** auch für einen einzelnen virtuellen Computer durchführen.

1. Wählen Sie **Wiederherstellungspläne** > *Name des Wiederherstellungsplans* aus. Klicken Sie auf **Failover** > **Test Failover**veröffentlichen.
2. Geben Sie auf dem Blatt **Testfailover** an, wie Replikat-VMs nach dem Testfailover mit Netzwerken verbunden werden sollen.
3. Verfolgen Sie den Verlauf des Failovers auf der Registerkarte **Aufträge** .
4. Vergewissern Sie sich nach Abschluss des Failovers, dass die virtuellen Computer erfolgreich gestartet werden.
5. Klicken Sie anschließend auf dem Wiederherstellungsplan auf **Cleanup test failover** (Testfailover bereinigen). Erfassen und speichern Sie unter **Notizen** alle Beobachtungen im Zusammenhang mit dem Testfailover. Durch diesen Schritt werden die während des Testfailovers durch Azure Site Recovery erstellten virtuellen Computer und Netzwerke gelöscht. 

![Testfailover](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> Die IP-Adresse, die einem virtuellen Computer bei einem Testfailover zugewiesen wird, ist mit der IP-Adresse identisch, die dem virtuellen Computer bei einem geplanten oder nicht geplanten Failover zugewiesen wird (sofern die IP-Adresse im Testfailovernetzwerk verfügbar ist). Wenn dieselbe IP-Adresse nicht im Testfailovernetzwerk verfügbar ist, wird dem virtuellen Computer eine andere IP-Adresse zugewiesen, die im Testfailovernetzwerk verfügbar ist.



### <a name="run-a-test-failover-to-a-production-network"></a>Ausführen eines Testfailovers auf ein Produktionsnetzwerk

Es wird empfohlen, kein Testfailover auf Ihr Produktionsnetzwerk am Wiederherstellungsstandort auszuführen, das Sie bei der Netzwerkzuordnung angegeben haben. Beachten Sie folgende Punkte, wenn Sie wirklich die End-to-End-Netzwerkkonnektivität auf einem virtuellen Computer überprüfen müssen, für den ein Failover ausgeführt wurde:

* Stellen Sie sicher, dass die primäre VM heruntergefahren ist, wenn Sie das Testfailover ausführen. Wenn Sie dies nicht tun, werden zwei virtuelle Computer mit derselben Identität gleichzeitig im selben Netzwerk ausgeführt. Diese Situation kann unerwünschte Folgen haben.
* Änderungen, die Sie auf virtuellen Computern mit Testfailover vornehmen, gehen verloren, wenn Sie die virtuellen Computer mit Testfailover bereinigen. Diese Änderungen werden nicht wieder zurück auf die primäre VM repliziert.
* Diese Art von Test führt zu einem Ausfall Ihrer Produktionsanwendung. Fordern Sie Benutzer der Anwendung auf, die Anwendung während des DR-Drills nicht zu verwenden.  


## <a name="next-steps"></a>Nächste Schritte
Sobald Sie erfolgreich einen DR-Drill ausgeführt haben, können Sie ein [vollständiges Failover ausführen](site-recovery-failover.md).



