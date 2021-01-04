---
title: Vorbereiten von Computern für die Migration mit Azure Migrate
description: Es wird beschrieben, wie Sie lokale Computer für die Migration mit Azure Migrate vorbereiten.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 06/08/2020
ms.openlocfilehash: 979f40e13aab71f02a316e4ddf60306170166845
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753925"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Vorbereiten von lokalen Computern für die Migration zu Azure

In diesem Artikel erfahren Sie, wie Sie lokale Computer vorbereiten, bevor Sie diese mithilfe der [Azure Migrate-Servermigration](migrate-services-overview.md#azure-migrate-server-migration-tool) zu Azure migrieren.

In diesem Artikel führen Sie folgende Schritte aus:
> [!div class="checklist"]
> * Überprüfen der Einschränkungen bei der Migration
> * Auswählen einer Migrationsmethode für virtuelle VMware-Computer
> * Überprüfen der Hypervisor- und Betriebssystemanforderungen für Computer, die Sie migrieren möchten
> * Überprüfen des URL- und Portzugriffs für zu migrierende Computer
> * Überprüfen der Änderungen, die Sie vor Beginn der Migration ggf. vornehmen müssen
> * Überprüfen der Azure VM-Anforderungen für migrierte Computer
> * Vorbereiten von Computern, sodass Sie nach der Migration eine Verbindung mit den virtuellen Azure-Computern herstellen können



## <a name="verify-migration-limitations"></a>Überprüfen der Einschränkungen bei der Migration

Die Tabelle enthält eine Übersicht über die Ermittlungs-, Bewertungs- und Migrationsgrenzwerte für Azure Migrate. Es empfiehlt sich, Computer vor der Migration zu bewerten, dies ist jedoch nicht zwingend erforderlich.

**Szenario** | **Projekt** | **Ermittlung/Bewertung** | **Migration**
--- | --- | --- | ---
**Virtuelle VMware-Computer** | In einem einzelnen Azure Migrate-Projekt können bis zu 35.000 virtuelle Computer ermittelt und bewertet werden. | Mit einer einzelnen [Azure Migrate-Appliance](common-questions-appliance.md) für VMware können bis zu 10.000 virtuelle VMware-Computer ermittelt werden. | **Migration ohne Agent:** Bis zu 300 virtuelle Computer können gleichzeitig repliziert werden. Ab 50 virtuellen Computern empfiehlt es sich, mehrere Batches zu erstellen, um eine optimale Leistung zu erzielen.<br/><br/> **Agent-basierte Migration:** Die [Replikationsappliance](migrate-replication-appliance.md) kann [aufskaliert](./agent-based-migration-architecture.md#performance-and-scaling) werden, um viele virtuelle Computer zu replizieren.<br/><br/> Im Portal können für die Replikation bis zu zehn virtuelle Computer gleichzeitig ausgewählt werden. Wenn Sie mehr Computer replizieren möchten, fügen Sie jeweils Batches mit zehn Stück hinzu.
**Virtuelle Hyper-V-Computer** | In einem einzelnen Azure Migrate-Projekt können bis zu 35.000 virtuelle Computer ermittelt und bewertet werden. | Mit einer einzelnen Azure Migrate-Appliance können bis zu 5.000 virtuelle Hyper-V-Computer ermittelt werden. | Für die Hyper-V-Migration wird keine Appliance verwendet. Stattdessen wird auf jedem Hyper-V-Host der Hyper-V-Replikationsanbieter ausgeführt.<br/><br/> Die Replikationskapazität hängt von Leistungsfaktoren wie VM-Änderungsrate und Uploadbandbreite für Replikationsdaten ab.<br/><br/> Im Portal können für die Replikation bis zu zehn virtuelle Computer gleichzeitig ausgewählt werden. Wenn Sie mehr Computer replizieren möchten, fügen Sie jeweils Batches mit zehn Stück hinzu.
**Physische Computer** | In einem einzelnen Azure Migrate-Projekt können bis zu 35.000 Computer ermittelt und bewertet werden. | Mit einer einzelnen Azure Migrate-Appliance für physische Server können bis zu 250 physische Server ermittelt werden. | Die [Replikationsappliance](migrate-replication-appliance.md) kann [aufskaliert](./agent-based-migration-architecture.md#performance-and-scaling) werden, um viele Server zu replizieren.<br/><br/> Im Portal können für die Replikation bis zu zehn virtuelle Computer gleichzeitig ausgewählt werden. Wenn Sie mehr Computer replizieren möchten, fügen Sie jeweils Batches mit zehn Stück hinzu.

## <a name="select-a-vmware-migration-method"></a>Auswählen einer VMware-Migrationsmethode

Wenn Sie virtuelle VMware-Computer zu Azure migrieren, [vergleichen](server-migrate-overview.md#compare-migration-methods) Sie die Migration ohne Agent und die Agent-basierte Migration, um die für Sie geeignete Methode zu ermitteln.

## <a name="verify-hypervisor-requirements"></a>Überprüfen der Hypervisor-Anforderungen

- Überprüfen Sie die Anforderungen der [VMware-Migration ohne Agent](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) bzw. der [Agent-basierten VMware-Migration](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based).
- Überprüfen Sie die [Anforderungen für Hyper-V-Hosts](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements).


## <a name="verify-operating-system-requirements"></a>Überprüfen der Betriebssystemanforderungen

Überprüfen Sie die unterstützten Betriebssysteme für die Migration:

- Wenn Sie virtuelle VMware- oder Hyper-V-Computer migrieren, überprüfen Sie die Anforderungen im Zusammenhang mit virtuellen VMware-Computern für die [Migration ohne Agent](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless) bzw. für die [Agent-basierte Migration](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) sowie die Anforderungen für [virtuelle Hyper-V-Computer](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms).
- Vergewissern Sie sich, dass [Windows-Betriebssysteme](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) in Azure unterstützt werden.
- Vergewissern Sie sich, dass [Linux-Distributionen](../virtual-machines/linux/endorsed-distros.md) in Azure unterstützt werden.

## <a name="review-url-and-port-access"></a>Überprüfen des URL- und Portzugriffs

Überprüfen Sie, auf welche URLs und Ports bei der Migration zugegriffen wird.

**Szenario** | **Details** |  **URLs** | **Ports**
--- | --- | --- | ---
**VMware-Migration ohne Agent** | Hierbei wird die [Azure Migrate-Appliance](migrate-appliance-architecture.md) für die Migration verwendet. Auf den virtuellen VMware-Computern wird nichts installiert. | Überprüfen Sie die [URLs](migrate-appliance.md#url-access) für die öffentliche Cloud und für Azure Government-Clouds, die für die Ermittlung, Bewertung und Migration mit der Appliance erforderlich sind. | [Überprüfen](migrate-support-matrix-vmware-migration.md#port-requirements-agentless) Sie die Portanforderungen für die Migration ohne Agent.
**Agent-basierte VMware-Migration** | Hierbei wird die [Replikationsappliance](migrate-replication-appliance.md) für die Migration verwendet. Auf den virtuellen Computern wird jeweils der Mobilitätsdienst-Agent installiert. | Überprüfen Sie die URLs für die [öffentliche Cloud](migrate-replication-appliance.md#url-access) und die URLs für [Azure Government](migrate-replication-appliance.md#azure-government-url-access), auf die die Replikationsappliance Zugriff benötigt. | [Überprüfen](migrate-replication-appliance.md#port-access) Sie die Ports, die im Rahmen der Agent-basierten Migration verwendet werden.
**Hyper-V-Migration** | Hierbei wird für die Migration ein auf Hyper-V-Hosts installierter Anbieter verwendet. Auf den virtuellen Hyper-V-Computern wird nichts installiert. | Überprüfen Sie die URLs für die [öffentliche Cloud](migrate-support-matrix-hyper-v-migration.md#url-access-public-cloud) und die URLs für [Azure Government](migrate-support-matrix-hyper-v-migration.md#url-access-azure-government), auf die der auf den Hosts ausgeführte Anbieter Zugriff benötigt. | VM-Replikationsdaten werden vom Replikationsanbieter auf dem Hyper-V-Host über ausgehende Verbindungen am HTTPS-Port 443 gesendet.
**Physische Computer** | Hierbei wird die [Replikationsappliance](migrate-replication-appliance.md) für die Migration verwendet. Auf den physischen Computern wird jeweils der Mobilitätsdienst-Agent installiert. | Überprüfen Sie die URLs für die [öffentliche Cloud](migrate-replication-appliance.md#url-access) und die URLs für [Azure Government](migrate-replication-appliance.md#azure-government-url-access), auf die die Replikationsappliance Zugriff benötigt. | [Überprüfen](migrate-replication-appliance.md#port-access) Sie die Ports, die im Rahmen der physischen Migration verwendet werden.

## <a name="verify-required-changes-before-migrating"></a>Überprüfen der erforderlichen Änderungen vor der Migration

Für virtuelle Computer müssen vor der Migration zu Azure ein paar Änderungen vorgenommen werden.

- Bei einigen Betriebssystemen werden Änderungen automatisch von Azure Migrate im Rahmen des Replikations-/Migrationsprozesses vorgenommen.
- Bei anderen Betriebssystemen müssen Einstellungen manuell konfiguriert werden.
- Wichtig: Die Einstellungen müssen vor Beginn der Migration manuell konfiguriert werden. Wenn Sie den virtuellen Computer migrieren, bevor Sie die Änderung vorgenommen haben, wird der virtuelle Computer in Azure unter Umständen nicht gestartet.

Ermitteln Sie anhand der Tabellen, welche Änderungen erforderlich sind.

### <a name="windows-machines"></a>Windows-Computer

Die erforderlichen Änderungen sind in der Tabelle zusammengefasst.

**Aktion** | **VMware (Migration ohne Agent)** | **VMware (Agent-basiert)/physische Computer** | **Windows mit Hyper-V** 
--- | --- | --- | ---
**Konfigurieren der SAN-Richtlinie als „Online – Alle“**<br/><br/> Dadurch wird sichergestellt, dass für Windows-Volumes auf dem virtuellen Azure-Computer die gleichen Laufwerkbuchstaben verwendet werden wie auf dem lokalen virtuellen Computer. | Wird für Computer unter Windows Server 2008 R2 oder höher automatisch festgelegt.<br/><br/> Bei älteren Betriebssystemen muss dieser Konfigurationsschritt manuell ausgeführt werden. | Wird in den meisten Fällen automatisch festgelegt. | Muss manuell konfiguriert werden.
**Installieren der Hyper-V-Gastintegration** | Muss auf Computern unter Windows Server 2003 [manuell installiert](prepare-windows-server-2003-migration.md#install-on-vmware-vms) werden. | Muss auf Computern unter Windows Server 2003 [manuell installiert](prepare-windows-server-2003-migration.md#install-on-vmware-vms) werden. | Muss auf Computern unter Windows Server 2003 [manuell installiert](prepare-windows-server-2003-migration.md#install-on-hyper-v-vms) werden.
**Aktivieren der seriellen Azure-Konsole**<br/><br/>[Aktivieren Sie die Konsole](../virtual-machines/troubleshooting/serial-console-windows.md) auf virtuellen Azure-Computern, um die Problembehandlung zu erleichtern. Sie müssen den virtuellen Computer nicht neu starten. Der virtuelle Azure-Computer wird unter Verwendung des Datenträgerimages gestartet. Der Start mit dem Datenträgerimage entspricht einem Neustart für den neuen virtuellen Computer. | Muss manuell aktiviert werden. | Muss manuell aktiviert werden. | Muss manuell aktiviert werden.
**Herstellen der Verbindung nach der Migration**<br/><br/> Für die Verbindungsherstellung nach der Migration müssen vor der Migration mehrere Schritte ausgeführt werden. | [Manuelle Einrichtung](#prepare-to-connect-to-azure-windows-vms) erforderlich | [Manuelle Einrichtung](#prepare-to-connect-to-azure-windows-vms) erforderlich | [Manuelle Einrichtung](#prepare-to-connect-to-azure-windows-vms) erforderlich


#### <a name="configure-san-policy"></a>Konfigurieren der SAN-Richtlinie

Standardmäßig wird virtuellen Azure-Computern das Laufwerk D als temporärer Speicher zugewiesen.

- Diese Laufwerkzuweisung führt dazu, dass alle anderen angefügten Zuweisungen von Speicherlaufwerken um einen Buchstaben inkrementiert werden.
- Wenn für Ihre lokale Installation beispielsweise ein Datenträger genutzt wird, dem der Laufwerkbuchstabe D für Anwendungsinstallationen zugewiesen ist, wird die Zuweisung für dieses Laufwerk auf Laufwerk E inkrementiert, nachdem Sie die VM zu Azure migriert haben. 
- Legen Sie die SAN-Richtlinie (Storage Area Network) auf **OnlineAll** fest, um diese automatische Zuweisung zu verhindern und sicherzustellen, dass Azure dem temporären Volume den nächsten freien Laufwerkbuchstaben zuweist:

Konfigurieren Sie diese Einstellung manuell wie folgt:

1. Öffnen Sie auf dem lokalen Computer (nicht auf dem Hostserver) eine Eingabeaufforderung mit erhöhten Rechten.
2. Geben Sie **diskpart** ein.
3. Geben Sie **SAN** ein. Wenn der Laufwerkbuchstabe des Gastbetriebssystems nicht beibehalten wird, wird **Offline – Alle** oder **Offline – Freigegeben** zurückgegeben.
4. Geben Sie an der Eingabeaufforderung **DISKPART** Folgendes ein: **SAN Policy=OnlineAll**. Durch diese Einstellung wird sichergestellt, dass Datenträger online geschaltet werden und dass Sie von beiden Datenträgern lesen sowie auf beide Datenträger schreiben können.
5. Während der Testmigration können Sie überprüfen, ob die Laufwerkbuchstaben beibehalten werden.


### <a name="linux-machines"></a>Linux-Computer

Für folgende Versionen werden diese Aktionen von Azure Migrate automatisch ausgeführt:

- Red Hat Enterprise Linux 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x
- Cent OS 7.7, 7.6, 7.5, 7.4, 6.x
- SUSE Linux Enterprise Server 12 SP1+
- SUSE Linux Enterprise Server 15 SP1
- Ubuntu 19.04, 19.10, 18.04LTS, 16.04LTS, 14.04LTS
- Debian 8, 7
- Oracle Linux 7.7, 7.7-CI

Für andere Versionen müssen die Computer gemäß der Zusammenfassung in der Tabelle vorbereitet werden.  


**Aktion** | **Details** | **Linux-Version**
--- | --- | ---
**Installieren von Linux Integration Services für Hyper-V** | Erstellen Sie das Linux-Initialisierungsimage neu, damit es die erforderlichen Hyper-V-Treiber enthält. Durch die Neuerstellung des Initialisierungsimages wird sichergestellt, dass der virtuelle Computer in Azure gestartet wird. | In den meisten neuen Versionen von Linux-Distributionen ist dies standardmäßig enthalten.<br/><br/> Andernfalls muss die Installation für alle oben nicht angegebenen Versionen manuell durchgeführt werden.
**Aktivieren der Protokollierung der seriellen Azure-Konsole** | Die Aktivierung der Konsolenprotokollierung vereinfacht die Problembehandlung. Sie müssen den virtuellen Computer nicht neu starten. Der virtuelle Azure-Computer wird unter Verwendung des Datenträgerimages gestartet. Der Start mit dem Datenträgerimage entspricht einem Neustart für den neuen virtuellen Computer.<br/><br/> Befolgen Sie zur Aktivierung [diese Anleitung](../virtual-machines/troubleshooting/serial-console-linux.md).
**Aktualisieren der Gerätezuordnungsdatei** | Aktualisieren Sie die Gerätezuordnungsdatei mit den Zuordnungen von Gerätenamen und Volumes so, dass persistente Gerätebezeichner verwendet werden. | Führen Sie die Installation für alle oben nicht angegebenen Versionen manuell durch. (Gilt nur für ein agent-basiertes VMware-Szenario)
**Aktualisieren von fstab-Einträgen** |  Aktualisieren Sie die Einträge so, dass persistente Volumebezeichner verwendet werden.    | Führen Sie die Aktualisierung für alle oben nicht angegebenen Versionen manuell durch.
**Entfernen der udev-Regel** | Entfernen Sie alle udev-Regeln, mit denen Schnittstellennamen basierend auf der MAC-Adresse usw. reserviert werden. | Führen Sie die Entfernung für alle oben nicht angegebenen Versionen manuell durch.
**Aktualisieren von Netzwerkschnittstellen** | Aktualisieren Sie die Netzwerkschnittstellen so, dass IP-Adressen basierend auf DHCP.nst empfangen werden. | Führen Sie die Aktualisierung für alle oben nicht angegebenen Versionen manuell durch.
**Aktivieren von SSH** | Stellen Sie sicher, dass SSH aktiviert und für den SSHD-Dienst das Starten während des Neustartvorgangs festgelegt ist.<br/><br/> Stellen Sie sicher, dass eingehende SSH-Verbindungsanforderungen nicht durch die Firewall des Betriebssystems oder skriptfähige Regeln blockiert werden.| Führen Sie die Aktivierung für alle oben nicht angegebenen Versionen manuell durch.

In der folgenden Tabelle sind die Schritte zusammengefasst, die automatisch für die oben aufgeführten Betriebssysteme ausgeführt werden:


| Aktion                                      | Agent\-basierte VMware-Migration | VMware-Migration ohne Agent | Hyper\-V   |
|---------------------------------------------|-------------------------------|----------------------------|------------|
| Installieren von Linux Integration Services für Hyper\-V | Ja                           | Ja                        | Nicht erforderlich |
| Aktivieren der Protokollierung der seriellen Azure-Konsole         | Ja                           | Ja                        | Nein         |
| Aktualisieren der Gerätezuordnungsdatei                      | Ja                           | Nein                         | Nein         |
| Aktualisieren von fstab-Einträgen                        | Ja                           | Ja                        | Nein         |
| Entfernen der udev-Regel                            | Ja                           | Ja                        | Nein         |
| Aktualisieren von Netzwerkschnittstellen                   | Ja                           | Ja                        | Nein         |
| Aktivieren von SSH                                  | Nein                            | Nein                         | Nein         |

Machen Sie sich ausführlicher mit [Schritten zum Ausführen eines virtuellen Linux-Computers in Azure](../virtual-machines/linux/create-upload-generic.md) vertraut, und sehen Sie sich die Anleitungen für einige gängige Linux-Distributionen an.


## <a name="check-azure-vm-requirements"></a>Überprüfen der Anforderungen von Azure-VMs

Für lokale Computer, die Sie in Azure replizieren, müssen die Azure-VM-Anforderungen für Betriebssystem und Architektur, Datenträger, Netzwerkeinstellungen und die VM-Benennung erfüllt sein.

Überprüfen Sie vor der Migration die Azure-VM-Anforderungen für die Migration von [VMware](migrate-support-matrix-vmware-migration.md#azure-vm-requirements), [Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) und [physischen Servern](migrate-support-matrix-physical-migration.md#azure-vm-requirements).



## <a name="prepare-to-connect-after-migration"></a>Vorbereiten der Verbindung nach der Migration

Azure-VMs werden während der Migration zu Azure erstellt. Nach der Migration müssen Sie eine Verbindung mit den neuen virtuellen Azure-Computern herstellen können. Für eine erfolgreiche Verbindungsherstellung sind mehrere Schritte erforderlich.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Vorbereiten der Verbindungsherstellung mit virtuellen Windows-Computern in Azure

Vorgehensweise für lokale Windows-Computer:

1. Konfigurieren Sie die Windows-Einstellungen. Hierzu gehört auch das Entfernen aller statischen beständigen Routen oder des WinHTTP-Proxys.
2. Vergewissern Sie sich, dass die [erforderlichen Dienste](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) ausgeführt werden.
3. Aktivieren Sie den Remotedesktop (RDP), um Remoteverbindungen mit dem lokalen Computer zuzulassen. Informationen zum Aktivieren von RDP mithilfe von PowerShell finden Sie [hier](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
4. Wenn Sie nach der Migration über das Internet auf eine Azure-VM zugreifen möchten, lassen Sie in der Windows-Firewall auf dem lokalen Computer TCP und UDP im öffentlichen Profil zu, und legen Sie RDP als zulässige App für alle Profile fest.
5. Wenn Sie nach der Migration auf eine Azure-VM über ein Site-to-Site-VPN zugreifen möchten, lassen Sie in der Windows-Firewall auf dem lokalen Computer RDP für das Domänenprofil und private Profile zu. Informationen zum Zulassen von RDP-Datenverkehr finden Sie [hier](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. Vergewissern Sie sich, dass auf dem lokalen virtuellen Computer keine ausstehenden Windows-Updates vorhanden sind, wenn Sie die Migration durchführen. Sollte dies nämlich der Fall sein, werden nach der Migration auf dem virtuellen Azure-Computer ggf. Updates installiert, und Sie können sich dann erst bei der VM anmelden, nachdem die Updates abgeschlossen sind.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Vorbereiten der Verbindung mit Linux-Azure-VMs

Vorgehensweise für lokale Linux-Computer:

1. Stellen Sie sicher, dass der Secure Shell-Dienst so festgelegt ist, dass er beim Systemstart automatisch gestartet wird.
2. Überprüfen Sie, ob die Firewallregeln eine SSH-Verbindung zulassen.

### <a name="configure-azure-vms-after-migration"></a>Vorbereiten virtueller Azure-Computer nach der Migration

Führen Sie nach der Migration auf den erstellten virtuellen Azure-Computern die folgenden Schritte aus:

1. Um aus dem Internet auf die VM zugreifen zu können, müssen Sie ihr eine öffentliche IP-Adresse zuweisen. Die öffentliche IP-Adresse für den virtuellen Azure-Computer muss sich von der öffentlichen IP-Adresse Ihres lokalen Computers unterscheiden. [Weitere Informationen](../virtual-network/virtual-network-public-ip-address.md)
2. Überprüfen Sie, ob die Regeln der Netzwerksicherheitsgruppen (NSG) auf dem virtuellen Computer eingehende Verbindungen am RDP- oder SSH-Port zulassen.
3. Überprüfen Sie die [Startdiagnose](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine), um den virtuellen Computer anzuzeigen.


## <a name="next-steps"></a>Nächste Schritte

Entscheiden Sie, welche Methode Sie für die [Migration virtueller VMware-Computer](server-migrate-overview.md) zu Azure verwenden möchten, oder beginnen Sie mit der Migration von [virtuellen Hyper-V-Computern](tutorial-migrate-hyper-v.md) oder [physischen Servern oder virtualisierten oder cloudbasierten virtuellen Computern](tutorial-migrate-physical-virtual-machines.md).

## <a name="see-whats-supported"></a>Überprüfen der Unterstützung

Für VMware-VMs wird bei der Servermigration die [Migration mit oder ohne Agent](server-migrate-overview.md) unterstützt.

- **VMware-VMs:** Überprüfen Sie die [Migrationsanforderungen und -unterstützung](migrate-support-matrix-vmware-migration.md) für virtuelle VMware-Computer.
- **Virtuelle Hyper-V-Computer:** Überprüfen Sie die [Migrationsanforderungen und -unterstützung](migrate-support-matrix-hyper-v-migration.md) in Bezug auf Hyper-V-VMs.
- **Physische Computer:** Überprüfen Sie die [Migrationsanforderungen und -unterstützung](migrate-support-matrix-physical-migration.md) für lokale physische Computer und andere virtualisierte Server. 
