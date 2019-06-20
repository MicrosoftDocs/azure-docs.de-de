---
title: Einrichten der IP-Adressierung zum Herstellen einer Verbindung mit einem sekundären lokalen Standort nach einem Failover mit Azure Site Recovery | Microsoft Docs
description: Hier wird beschrieben, wie die IP-Adressierung für die Verbindung mit virtuellen Computern an einem sekundären lokalen Standort nach einer Notfallwiederherstellung und einem Failover mit Azure Site Recovery eingerichtet wird.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 8e4dca61016adce209bdce356ea4280fee525c05
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66397970"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Einrichten der IP-Adressierung zum Herstellen einer Verbindung mit einem sekundären lokalen Standort nach einem Failover

Nach dem Failover von Hyper-V-VMs in VMM-Clouds (System Center Virtual Machine Manager) an einen sekundären Standort müssen Sie in der Lage sein, eine Verbindung mit den virtuelle Replikatcomputern herzustellen. Dieser Artikel hilft Ihnen dabei. 

## <a name="connection-options"></a>Verbindungsoptionen

Nach einem Failover gibt es mehrere Möglichkeiten, die IP-Adressierung für virtuelle Replikatcomputer zu behandeln: 

- **Beibehalten derselben IP-Adresse nach einem Failover**: In diesem Szenario besitzt der replizierte virtuelle Computer die gleiche IP-Adresse wie der primäre virtuelle Computer. Dies vereinfacht netzwerkbezogene Probleme nach dem Failover, erfordert jedoch einige Infrastrukturarbeiten.
- **Verwenden einer anderen IP-Adresse nach einem Failover**: In diesem Szenario erhält die VM nach einem Failover eine neue IP-Adresse. 
 

## <a name="retain-the-ip-address"></a>Beibehalten der IP-Adresse

Wenn Sie die IP-Adressen vom primären Standort nach einem Failover zum sekundären Standort beibehalten möchten, können Sie folgendermaßen vorgehen:

- Bereitstellen eines Stretchingsubnetzes zwischen dem primären und dem sekundären Standort.
- Ausführen eines vollständigen Failovers vom primären Standort an den sekundären Standort. Sie müssen die Routen aktualisieren, um den neuen Standort der IP-Adressen anzugeben.


### <a name="deploy-a-stretched-subnet"></a>Bereitstellen eines Stretchingsubnetzes

In einer Stretchingkonfiguration ist das Subnetz gleichzeitig am primären und am sekundären Standort verfügbar. Wenn Sie in einem Stretchingsubnetz einen Computer und seine IP-Adresskonfiguration (Layer 3) an den sekundären Standort verschieben, leitet das Netzwerk den Datenverkehr automatisch an den neuen Standort weiter. 

- Aus Layer-2-Perspektive (Sicherungsschicht) benötigen Sie Netzwerkkomponenten, die ein Stretching-VLAN verarbeiten können.
- Durch Stretching des VLANs erstreckt sich die potenzielle Fehlerdomäne auf beide Standorte. Dies wird zu einer einzelnen Fehlerquelle. Es ist zwar unwahrscheinlich, aber in einem solchen Szenario ist es nicht möglich, einen Vorfall wie z.B. einen Broadcast-Sturm zu isolieren. 


### <a name="fail-over-a-subnet"></a>Failover eines Subnetzes

Sie können ein Failover des gesamten Subnetzes ausführen, um von den Vorteilen eines Stretchingsubnetzes zu profitieren, ohne für das Subnetz tatsächlich Stretching auszuführen. In dieser Lösung ist ein Subnetz am Quell- oder Zielstandort verfügbar, aber nicht an beiden Standorten gleichzeitig.

- Um den IP-Adressraum im Fall eines Failovers beizubehalten, können Sie programmgesteuert dafür sorgen, dass die Routerinfrastruktur Subnetze von einem Standort an einen anderen verschiebt.
- Nach einem Failover werden die Subnetze mit den zugehörigen virtuellen Computern verschoben.
- Der größte Nachteil bei dieser Vorgehensweise besteht darin, dass Sie bei einem Ausfall das gesamte Subnetz verschieben müssen.

#### <a name="example"></a>Beispiel

Hier finden Sie ein Beispiel für ein vollständiges Subnetzfailover. 

- Vor dem Failover führte der primäre Standort Anwendungen im Subnetz 192.168.1.0/24 aus.
- Während des Failovers werden alle virtuellen Computer in diesem Subnetz an den sekundären Standort verlagert und behalten Ihre IP-Adressen bei. 
- Die Routen zwischen allen Standorten müssen geändert werden, um die Tatsache widerzuspiegeln, dass alle virtuellen Computer im Subnetz 192.168.1.0/24 jetzt an den sekundären Standort verschoben wurden.

Die folgende Abbildung zeigt die Subnetze vor und nach dem Failover.


**Vor dem Failover**

![Vor dem Failover](./media/hyper-v-vmm-networking/network-design2.png)

**Nach dem Failover**

![Nach dem Failover](./media/hyper-v-vmm-networking/network-design3.png)

Nach dem Failover weist Site Recovery eine IP-Adresse für jede Netzwerkschnittstelle auf dem virtuellen Computer zu. Die Adresse wird aus dem statischen IP-Adresspool im relevanten Netzwerk für jede VM-Instanz zugewiesen.

- Wenn der IP-Adresspool am sekundären Standort der gleiche Adresspool ist wie am Quellstandort, weist Site Recovery dem virtuellen Replikatcomputer die gleiche IP-Adresse (des virtuellen Quellcomputers) zu. Die IP-Adresse ist in VMM reserviert, aber auf dem Hyper-V-Host nicht als Failover IP-Adresse festgelegt. Die Failover-IP-Adresse auf einem Hyper-V-Host wird erst unmittelbar vor dem Failover festgelegt.
- Wenn die gleiche IP-Adresse nicht verfügbar ist, ordnet Site Recovery eine andere verfügbare Adresse aus dem Pool zu.
- Wenn virtuelle Computer DHCP verwenden, verwaltet Site Recovery die IP-Adressen nicht. Sie müssen überprüfen, ob der DHCP-Server am sekundären Standort Adressen aus dem gleichen Bereich zuweisen kann wie am Quellstandort.

### <a name="validate-the-ip-address"></a>Überprüfen der IP-Adresse

Nachdem Sie den Schutz für einen virtuellen Computer aktiviert haben, können Sie das folgende Beispielskript verwenden, um die Adresse zu überprüfen, die dem virtuellen Computer zugewiesen wurde. Diese IP-Adresse wird als Failover-IP-Adresse festgelegt und dem virtuellen Computer zum Zeitpunkt des Failovers zugewiesen:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Verwenden einer anderen IP-Adresse

In diesem Szenario werden die IP-Adressen von VMs, für die ein Failover durchgeführt wird, geändert. Der Nachteil dieser Lösung liegt im erforderlichen Wartungsaufwand.  DNS- und Cacheeinträge müssen möglicherweise aktualisiert werden. Dies kann zu Ausfallzeiten führen, die wie folgt verringert werden können:

- Verwenden Sie niedrige TTL-Werte für Intranetanwendungen.
- Verwenden Sie das folgende Skript in einem Site Recovery-Wiederherstellungsplan, um den DNS-Server rechtzeitig zu aktualisieren. Wenn Sie die dynamische DNS-Registrierung verwenden, benötigen Sie das Skript nicht.

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>Beispiel 

In diesem Beispiel sind am primären und am sekundären Standort verschiedene IP-Adressen vorhanden, und es gibt einen dritten Standort, von dem aus auf Anwendungen zugegriffen werden kann, die am primären oder am Wiederherstellungsstandort gehostet werden.

- Vor dem Failover werden Apps im Subnetz 192.168.1.0/24 am primären Standort gehostet.
- Nach dem Failover werden Apps im Subnetz 172.16.1.0/24 am sekundären Standort konfiguriert.
- Alle drei Standorte können aufeinander zugreifen.
- Nach dem Failover werden Apps im Wiederherstellungssubnetz wiederhergestellt.
- In diesem Szenario ist kein Failover des gesamten Subnetzes erforderlich, und es müssen keine Änderungen vorgenommen werden, um VPN- oder Netzwerkrouten neu zu konfigurieren. Mit einem Failover und einigen DNS-Updates wird sichergestellt, dass der Zugriff auf Anwendungen nicht unterbrochen wird.
- Wenn der DNS so konfiguriert ist, dass er dynamische Updates zulässt, registrieren die virtuellen Computer sich unter Verwendung der neuen IP-Adresse selbst, sobald sie nach einem Failover gestartet werden.

**Vor dem Failover**

![Andere IP-Adresse: vor dem Failover](./media/hyper-v-vmm-networking/network-design10.png)

**Nach dem Failover**

![Andere IP-Adresse: nach dem Failover](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Nächste Schritte

[Ausführen eines Failovers](hyper-v-vmm-failover-failback.md)

