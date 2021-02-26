---
title: Unterstützungsmatrix für die Sicherung virtueller Azure-Computer
description: Enthält eine Zusammenfassung der Unterstützungseinstellungen und Einschränkungen bei der Sicherung virtueller Azure-Computer mit dem Azure Backup-Dienst.
ms.topic: conceptual
ms.date: 09/13/2019
ms.custom: references_regions
ms.openlocfilehash: 1752e6687cd4dda72ee330c489a4642ae32f8219
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820771"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Unterstützungsmatrix für die Sicherung virtueller Azure-Computer

Mit dem [Azure Backup-Dienst](backup-overview.md) können Sie lokale Computer und Workloads sowie virtuelle Azure-Computer (VMs) sichern. Dieser Artikel enthält eine Zusammenfassung der Unterstützungseinstellungen und Einschränkungen bei der Sicherung von Azure-VMs mit Azure Backup.

Andere Unterstützungsmatrizen:

- [Allgemeine Unterstützungsmatrix](backup-support-matrix.md) für Azure Backup
- [Unterstützungsmatrix](backup-support-matrix-mabs-dpm.md) für Azure Backup-Server/System Center Data Protection Manager (DPM)-Sicherungen
- [Unterstützungsmatrix](backup-support-matrix-mars-agent.md) für die Sicherung mit dem Microsoft Azure Recovery Services-Agent (MARS)

## <a name="supported-scenarios"></a>Unterstützte Szenarios

Virtuelle Azure-Computer können wie folgt mit dem Azure Backup-Dienst gesichert und wiederhergestellt werden.

**Szenario** | **Backup** | **Agent** |**Wiederherstellen**
--- | --- | --- | ---
Direkte Sicherung virtueller Azure-Computer  | Sicherung der gesamten VM  | Auf dem virtuellen Azure-Computer ist kein zusätzlicher Agent erforderlich. Azure Backup installiert und verwendet eine Erweiterung für den [Azure-VM-Agent](../virtual-machines/extensions/agent-windows.md), der auf dem virtuellen Computer ausgeführt wird. | Die Wiederherstellung wird wie folgt durchgeführt:<br/><br/> - **Erstellen eines einfachen virtuellen Computers:** Dies ist nützlich, wenn der virtuelle Computer keine besondere Konfiguration (z.B. mehrere IP-Adressen) aufweist.<br/><br/> - **Wiederherstellen des VM-Datenträgers:** Stellen Sie den Datenträger wieder her. Fügen Sie ihn dann an einen vorhandenen virtuellen Computer an, oder erstellen Sie mit PowerShell über den Datenträger einen neuen virtuellen Computer.<br/><br/> - **Ersetzen des VM-Datenträgers:** Wenn ein virtueller Computer vorhanden ist, der verwaltete Datenträger (unverschlüsselt) verwendet, können Sie einen Datenträger wiederherstellen und zum Ersetzen eines vorhandenen Datenträgers auf dem virtuellen Computer verwenden.<br/><br/> - **Wiederherstellen bestimmter Dateien oder Ordner:** Sie können Dateien oder Ordner einer VM statt der gesamten VM wiederherstellen.
Direkte Sicherung von Azure-VMs (nur Windows)  | Sichern bestimmter Dateien, Ordner oder Volumes | Installieren Sie den [Azure Recovery Services-Agent](backup-azure-file-folder-backup-faq.md).<br/><br/> Sie können den MARS-Agent zusammen mit der Sicherungserweiterung für den Azure-VM-Agent ausführen, um den virtuellen Computer auf Datei- oder Ordnerebene zu sichern. | Wiederherstellen bestimmter Ordner oder Dateien
Sichern von Azure-VMs auf Sicherungsservern  | Sichern von Dateien, Ordnern oder Volumes, von Systemstatusdateien oder Bare-Metal-Dateien und von Anwendungsdaten in System Center DPM oder Microsoft Azure Backup Server (MABS)<br/><br/> DPM/MABS erstellt die Sicherung dann im Sicherungstresor. | Installieren Sie den MABS- oder DPM-Schutz-Agent auf der VM. Der MARS-Agent wird in DPM oder MABS installiert.| Wiederherstellen von Dateien, Ordnern oder Volumes, von Systemstatus- oder Bare-Metal-Dateien und von Anwendungsdaten

Weitere Informationen zum Sichern finden Sie in den Artikeln zu [Sicherungsservern](backup-architecture.md#architecture-back-up-to-dpmmabs) und den [Anforderungen für die Unterstützung](backup-support-matrix-mabs-dpm.md).

## <a name="supported-backup-actions"></a>Unterstützte Sicherungsaktionen

**Aktion** | **Unterstützung**
--- | ---
Sichern eines heruntergefahrenen virtuellen Computers / einer Offline-VM | Unterstützt.<br/><br/> Die Momentaufnahme ist nur ausfallkonsistent, jedoch nicht anwendungskonsistent.
Sicherung von Datenträgern nach Migration zu verwalteten Datenträgern | Unterstützt.<br/><br/> Sicherung wird weiterhin durchgeführt. Keine Aktion erforderlich.
Sicherung von verwalteten Datenträgern nach Aktivierung einer Ressourcengruppensperre | Wird nicht unterstützt.<br/><br/> Die älteren Wiederherstellungspunkte können von Azure Backup nicht gelöscht werden, und bei Sicherungen treten Fehler auf, wenn die maximale Anzahl von Wiederherstellungspunkten erreicht ist.
Ändern der Sicherungsrichtlinie für einen virtuellen Computer | Unterstützt.<br/><br/> Der virtuelle Computer wird unter Verwendung der Zeitplan- und Aufbewahrungseinstellungen der neuen Richtlinie gesichert. Wenn die Aufbewahrungseinstellungen erweitert werden, werden vorhandene Wiederherstellungspunkte markiert und beibehalten. Wenn die Einstellungen reduziert werden, werden vorhandene Wiederherstellungspunkte im nächsten Bereinigungsauftrag gelöscht und schließlich endgültig entfernt.
Abbrechen eines Sicherungsauftrags| Wird während des Prozesses der Momentaufnahme unterstützt.<br/><br/> Wird nicht unterstützt, wenn die Momentaufnahme in den Tresor übertragen wird.
Sicherung des virtuellen Computers in einer anderen Region oder einem anderen Abonnement |Wird nicht unterstützt.<br><br>Damit die Sicherung erfolgreich ausgeführt werden kann, müssen sich die virtuellen Computer im gleichen Abonnement wie der Tresor für die Sicherung befinden.
Sicherungen pro Tag (über die Azure-VM-Erweiterung) | Eine geplante Sicherung pro Tag<br/><br/>Der Azure Backup-Dienst unterstützt bis zu neun bedarfsgesteuerte Sicherungen pro Tag. Microsoft empfiehlt jedoch nicht mehr als vier bedarfsgesteuerte Sicherungen pro Tag, um eine optimale Leistung zu gewährleisten.
Sicherungen pro Tag (über den MARS-Agent) | Drei geplante Sicherungen pro Tag
Sicherungen pro Tag (über DPM/MABS) | Zwei geplante Sicherungen pro Tag
Monatliche oder jährliche Sicherung| Wird bei der Sicherung mit der Azure-VM-Erweiterung nicht unterstützt. Lediglich tägliche und wöchentliche Sicherungen werden unterstützt.<br/><br/> Sie können die Richtlinie so einrichten, dass tägliche oder wöchentliche Sicherungen in einem monatlichen oder jährlichen Aufbewahrungszeitraum beibehalten werden.
Automatische Uhrzeitanpassung | Wird nicht unterstützt.<br/><br/> Azure Backup passt Änderungen an der Sommer- oder Winterzeit bei der Sicherung einer VM nicht automatisch an.<br/><br/>  Ändern Sie die Richtlinie nach Bedarf manuell.
[Sicherheitsfunktionen für Hybridsicherungen](./backup-azure-security-feature.md) |Die Sicherheitsfunktionen können nicht deaktiviert werden.
Sichern von virtuellen Computern mit geänderter Computerzeit | Wird nicht unterstützt.<br/><br/> Eine erfolgreiche Sicherung nicht garantiert werden, wenn die Computerzeit nach dem Aktivieren der Sicherung für diesen virtuellen Computer in ein zukünftiges Datum bzw. eine zukünftige Uhrzeit geändert wird. Dies gilt auch, wenn diese Änderung wieder rückgängig gemacht wird.

## <a name="operating-system-support-windows"></a>Unterstützte Betriebssysteme (Windows)

In der folgenden Tabelle sind die unterstützten Betriebssysteme für die Sicherung von Azure-VMs aufgeführt.

**Szenario** | **Betriebssystemunterstützung**
--- | ---
Sicherung mit der Azure-VM-Agent-Erweiterung | – Windows 10 Client (nur 64 Bit) <br/><br/>- Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> - Windows Server 2012 (Datacenter/Standard) <br/><br/> - Windows Server 2008 R2 (RTM und SP1 Standard)  <br/><br/> – Windows Server 2008 (nur 64 Bit)
Sicherung mit dem MARS-Agent | [Unterstützte](backup-support-matrix-mars-agent.md#supported-operating-systems) Betriebssysteme
Sicherung mit DPM/MABS | Unterstützte Betriebssysteme für die Sicherung mit [MABS](backup-mabs-protection-matrix.md) und [DPM](/system-center/dpm/dpm-protection-matrix)

Azure Backup unterstützt keine 32-Bit-Betriebssysteme.

## <a name="support-for-linux-backup"></a>Unterstützung für die Linux-Sicherung

Hier ist die Unterstützung für den Fall angegeben, dass Sie Linux-Computer sichern möchten.

**Aktion** | **Unterstützung**
--- | ---
Sicherung virtueller Linux-Azure-Computer mit dem Linux-Azure-VM-Agent | Dateikonsistente Sicherung<br/><br/> App-konsistente Sicherung mit [benutzerdefinierten Skripts](backup-azure-linux-app-consistent.md).<br/><br/> Während der Wiederherstellung können Sie eine neue VM erstellen oder einen Datenträger wiederherstellen und damit eine VM erstellen oder einen Datenträger auf einer vorhandenen VM ersetzen. Zudem können Sie einzelne Dateien und Ordner wiederherstellen.
Sicherung virtueller Linux-Azure-Computer mit dem MARS-Agent | Wird nicht unterstützt.<br/><br/> Der MARS-Agent kann nur auf Windows-Computern installiert werden.
Sicherung virtueller Linux-Azure-Computer mit DPM/MABS | Wird nicht unterstützt.

## <a name="operating-system-support-linux"></a>Unterstützte Betriebssysteme (Linux)

Für Sicherungen von virtuellen Linux-Azure-Computern unterstützt Azure Backup [die Liste der von Azure unterstützten Linux-Distributionen](../virtual-machines/linux/endorsed-distros.md). Beachten Sie Folgendes:

- Azure Backup unterstützt CoreOS Linux nicht.
- Azure Backup unterstützt keine 32-Bit-Betriebssysteme.
- Andere Bring-Your-Own-Linux-Distributionen sollten funktionieren, sofern der [Azure-VM-Agent für Linux](../virtual-machines/extensions/agent-linux.md) auf der VM verfügbar ist und Python unterstützt wird.
- Azure Backup unterstützt keine über Proxy konfigurierte Linux-VM, wenn darauf nicht die Python-Version 2.7 installiert ist.
- Azure Backup unterstützt keine Sicherung von NFS-Dateien, die aus dem Speicher oder von einem anderen NFS-Server in Linux- oder Windows-Computer eingebunden werden. Es werden nur Datenträger gesichert, die lokal an die VM angefügt sind.

## <a name="backup-frequency-and-retention"></a>Sicherungshäufigkeit und -aufbewahrung

**Einstellung** | **Einschränkungen**
--- | ---
Maximale Wiederherstellungspunkte pro geschützter Instanz (Computer/Workload) | 9999
Maximale Ablaufzeit für einen Wiederherstellungspunkt | Keine Beschränkung (99 Jahre)
Maximale Sicherungshäufigkeit für Tresor (Azure-VM-Erweiterung) | Einmal täglich
Maximale Sicherungshäufigkeit für Tresor (MARS-Agent) | Drei Sicherungen pro Tag
Maximale Sicherungshäufigkeit für DPM/MABS | Alle 15 Minuten für SQL Server<br/><br/> Stündlich für andere Workloads
Aufbewahrung des Wiederherstellungspunkts | Täglich, wöchentlich, monatlich und jährlich
Maximale Aufbewahrungsdauer | Abhängig von der Sicherungshäufigkeit.
Wiederherstellungspunkte auf DPM-/MABS-Datenträger | 64 für Dateiserver und 448 für App-Server<br/><br/> Für Bandwiederherstellungspunkte für lokalen DPM gelten keine Grenzwerte.

## <a name="supported-restore-methods"></a>Unterstützte Wiederherstellungsmethoden

**Wiederherstellungsoptionen** | **Details**
--- | ---
**Erstellen eines neuen virtuellen Computers** | Sie können schnell eine einfache VM erstellen und können Sie über einen Wiederherstellungspunkt betriebsbereit machen.<br/><br/> Sie können einen Namen für die VM angeben, die Ressourcengruppe und das virtuelle Netzwerk (VNet) auswählen, in dem sie platziert werden soll, und ein Speicherkonto für die wiederhergestellte VM angeben. Die neue VM muss in derselben Region wie die Quell-VM erstellt werden.
**Datenträger wiederherstellen** | Stellt einen VM-Datenträger wieder her, der dann zum Erstellen einer neuen VM verwendet werden kann.<br/><br/> Azure Backup bietet eine Vorlage, mit der Sie eine VM anpassen und erstellen können. <br/><br> Der Wiederherstellungsauftrag generiert eine Vorlage, die Sie herunterladen und verwenden können, um benutzerdefinierte VM-Einstellungen festzulegen und eine VM zu erstellen.<br/><br/> Die Datenträger werden in die von Ihnen angegebene Ressourcengruppe kopiert.<br/><br/> Alternativ können Sie den Datenträger an eine vorhandene VM anhängen oder mit PowerShell eine neue VM erstellen.<br/><br/> VM anpassen, Konfigurationseinstellungen hinzufügen möchten, die zum Zeitpunkt der Sicherung nicht vorhanden waren, oder Einstellungen hinzufügen möchten, die mithilfe der Vorlage oder von PowerShell konfiguriert werden müssen.
**Vorhandene ersetzen** | Sie können einen Datenträger wiederherstellen und damit einen Datenträger auf der vorhandenen VM ersetzen.<br/><br/> Der aktuelle virtuelle Computer muss jedoch vorhanden sein. Wenn dieser gelöscht wurde, kann diese Option nicht verwendet werden.<br/><br/> Vor dem Ersetzen des Datenträgers erstellt Azure Backup eine Momentaufnahme der vorhandenen VM und speichert sie im von Ihnen Stagingspeicherort. Vorhandene Datenträger, die mit der VM verbunden sind, werden durch den ausgewählten Wiederherstellungspunkt ersetzt.<br/><br/> Die Momentaufnahme wird in den Tresor kopiert und entsprechend der Aufbewahrungsrichtlinie beibehalten. <br/><br/> Nach dem Ersetzungsvorgang für den Datenträger wird der ursprüngliche Datenträger in der Ressourcengruppe beibehalten. Sie können die ursprünglichen Datenträger manuell löschen, wenn sie nicht mehr benötigt werden. <br/><br/>„Vorhandene ersetzen“ wird für nicht verschlüsselte verwaltete VMs sowie für VMs unterstützt, [die mithilfe benutzerdefinierter Images erstellt wurden](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/). Die Option wird nicht für nicht verwaltete Datenträger und VMs, klassische VMs und [generalisierte VMs](../virtual-machines/windows/capture-image-resource.md) nicht unterstützt.<br/><br/> Wenn der Wiederherstellungspunkt mehr oder weniger Datenträger als die aktuelle VM aufweist, dann spiegelt die Anzahl der Datenträger im Wiederherstellungspunkt nur die VM-Konfiguration wieder.<br><br> „Vorhandene ersetzen“ wird auch für VMs mit verknüpften Ressourcen unterstützt, z. B. [benutzerseitig zugewiesene verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md) und [Key Vault](../key-vault/general/overview.md).
**Regionsübergreifend (sekundäre Region)** | Die regionsübergreifende Wiederherstellung kann verwendet werden, um virtuelle Azure-Computer in der sekundären Region wiederherzustellen, bei der es sich um eine [gepaarte Azure-Region](../best-practices-availability-paired-regions.md#what-are-paired-regions) handelt.<br><br> Wenn die Sicherung in der sekundären Region durchgeführt wird, können Sie alle Azure-VMs für den ausgewählten Wiederherstellungspunkt wiederherstellen.<br><br> Dieses Feature ist für die folgenden Optionen verfügbar:<br> <li> [Erstellen eines virtuellen Computers](./backup-azure-arm-restore-vms.md#create-a-vm) <br> <li> [Wiederherstellen von Datenträgern](./backup-azure-arm-restore-vms.md#restore-disks) <br><br> Die Option [Vorhandene Datenträger ersetzen](./backup-azure-arm-restore-vms.md#replace-existing-disks) wird derzeit nicht unterstützt.<br><br> Berechtigungen<br> Der Wiederherstellungsvorgang in der sekundären Region kann von Sicherungsadministratoren und App-Administratoren durchgeführt werden.

## <a name="support-for-file-level-restore"></a>Unterstützung für die Wiederherstellung auf Dateiebene

**Wiederherstellen** | **Unterstützt**
--- | ---
Wiederherstellen von Dateien unter verschiedenen Betriebssystemen | Sie können Dateien auf allen Computern wiederherstellen, die dasselbe (oder ein kompatibles) Betriebssystem wie der gesicherte virtuelle Computer haben. Weitere Informationen finden Sie in der Tabelle mit [kompatiblen Betriebssystemen](backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script).
Wiederherstellen von Dateien von verschlüsselten virtuellen Computern | Wird nicht unterstützt.
Wiederherstellen von Dateien von Speicherkonten mit Netzwerkbeschränkung | Wird nicht unterstützt.
Wiederherstellen von Dateien auf virtuellen Computern mit Windows-Speicherplätzen | Die Wiederherstellung auf demselben Computer wird nicht unterstützt.<br/><br/> Führen Sie stattdessen die Wiederherstellung der Dateien auf einem kompatiblen virtuellen Computer durch.
Wiederherstellen von Dateien auf einem virtuellen Linux-Computer mit LVM/RAID-Arrays | Die Wiederherstellung auf demselben Computer wird nicht unterstützt.<br/><br/> Führen Sie die Wiederherstellung auf einem kompatiblen virtuellen Computer durch.
Wiederherstellen von Dateien mit spezifischen Netzwerkeinstellungen | Die Wiederherstellung auf demselben Computer wird nicht unterstützt. <br/><br/> Führen Sie die Wiederherstellung auf einem kompatiblen virtuellen Computer durch.

## <a name="support-for-vm-management"></a>Unterstützung für die Datenträgerverwaltung

In der folgenden Tabelle ist die Unterstützung für die Sicherung bei Verwaltungsaufgaben für VMs aufgeführt, z.B. das Hinzufügen oder Ersetzen von VM-Datenträgern.

**Wiederherstellen** | **Unterstützt**
--- | ---
Wiederherstellung in verschiedenen Abonnements, Regionen oder Zonen | Wird nicht unterstützt.
Wiederherstellung auf einem vorhandenen virtuellen Computer | Verwenden Sie die Option zum Ersetzen eines Datenträgers.
Wiederherstellen eines Datenträgers mit für die Azure-Speicherdienstverschlüsselung (SSE) aktiviertem Speicherkonto | Wird nicht unterstützt.<br/><br/> Führen Sie die Wiederherstellung in einem Konto aus, für das SSE nicht aktiviert ist.
Wiederherstellung in gemischten Speicherkonten |Wird nicht unterstützt.<br/><br/> Basierend auf dem Speicherkontotyp sind alle wiederhergestellten Datenträger entweder jeweils Premium- oder Standard-Datenträger und können nicht gemischt sein.
Wiederherstellen eines virtuellen Computers direkt in einer Verfügbarkeitsgruppe | Bei verwalteten Datenträgern können Sie den Datenträger wiederherstellen und die Option für die Verfügbarkeitsgruppe in der Vorlage verwenden.<br/><br/> Dies wird für nicht verwaltete Datenträger nicht unterstützt. Bei nicht verwalteten Datenträgern stellen Sie den Datenträger wieder her und erstellen dann einen virtuellen Computer in der Verfügbarkeitsgruppe.
Wiederherstellen einer Sicherung von nicht verwalteten VMs nach dem Upgrade auf verwaltete VMs| Unterstützt.<br/><br/> Sie können Datenträger wiederherstellen und dann einen verwalteten virtuellen Computer erstellen.
Wiederherstellen eines virtuellen Computers an einem Wiederherstellungspunkt vor der Migration des virtuellen Computers zu verwalteten Datenträgern | Unterstützt.<br/><br/> Sie führen die Wiederherstellung auf nicht verwalteten Datenträgern durch (Standardeinstellung), konvertieren die wiederhergestellten Datenträger in verwaltete Datenträger und erstellen einen virtuellen Computer mit den verwalteten Datenträgern.
Wiederherstellen eines gelöschten virtuellen Computers | Unterstützt.<br/><br/> Sie können den virtuellen Computer aus einem Wiederherstellungspunkt wiederherstellen.
Wiederherstellen einer Domänencontroller-VM  | Unterstützt. Einzelheiten finden Sie unter [Wiederherstellen von Domänencontroller-VMs](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
Wiederherstellen einer VM in einem anderen virtuellen Netzwerk |Unterstützt.<br/><br/> Das virtuelle Netzwerk muss sich im selben Abonnement und derselben Region befinden.

## <a name="vm-compute-support"></a>Unterstützung für VM-Compute

**Compute** | **Unterstützung**
--- | ---
Größe des virtuellen Computers |Jede Größe von virtuellen Azure-Computern mit mindestens 2 CPU-Kernen und 1 GB RAM<br/><br/> [Weitere Informationen.](../virtual-machines/sizes.md)
Sicherung virtueller Computer in [Verfügbarkeitsgruppen](../virtual-machines/availability.md#availability-sets) | Unterstützt.<br/><br/> Sie können eine VM in einer Verfügbarkeitsgruppe nicht mit der Option zum schnellen Erstellen einer VM wiederherstellen. Stattdessen müssen Sie bei der Wiederherstellung der VM den Datenträger wiederherstellen und damit entweder eine VM bereitstellen oder einen vorhandenen Datenträger ersetzen.
Sicherung von mit [Hybridvorteil (HUB)](../virtual-machines/windows/hybrid-use-benefit-licensing.md) bereitgestellten VMs | Unterstützt.
Sicherung von auf dem [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images) bereitgestellten VMs<br/><br/> (Veröffentlicht von Microsoft oder Drittanbietern) |Unterstützt.<br/><br/> Auf dem virtuellen Computer muss ein unterstütztes Betriebssystem ausgeführt werden.<br/><br/> Die Wiederherstellung von Dateien auf dem virtuellen Computer kann nur unter einem kompatiblen Betriebssystem (kein früheres oder späteres Betriebssystem) durchgeführt werden. Als VMs gesicherte Azure Marketplace-VMs werden nicht wiederhergestellt, da dafür Kaufinformationen erforderlich sind. Sie werden nur als Datenträger wiederhergestellt.
Sicherung von über ein benutzerdefiniertes Image bereitgestellten VMs (Drittanbieter) |Unterstützt.<br/><br/> Auf dem virtuellen Computer muss ein unterstütztes Betriebssystem ausgeführt werden.<br/><br/> Die Wiederherstellung von Dateien auf dem virtuellen Computer kann nur unter einem kompatiblen Betriebssystem (kein früheres oder späteres Betriebssystem) durchgeführt werden.
Sicherung von zu Azure migrierten VMs| Unterstützt.<br/><br/> Für die Sicherung des virtuellen Computers muss der VM-Agent auf dem migrierten Computer installiert werden.
Sichern der Konsistenz mehrerer virtueller Computer | Azure Backup bietet keine mehrere VMs übergreifende Konsistenz von Daten und Anwendungen.
Sichern mit [Diagnoseeinstellungen](../azure-monitor/platform/platform-logs-overview.md)  | Nicht unterstützt. <br/><br/> Wenn die Wiederherstellung der Azure-VM mit Diagnoseeinstellungen mithilfe der Option [Neu erstellen](backup-azure-arm-restore-vms.md#create-a-vm) ausgelöst wird, tritt bei der Wiederherstellung ein Fehler auf.
Wiederherstellen von an Zonen angehefteten virtuellen Computern | Unterstützt (für VMs, die nach Januar 2019 gesichert wurden und bei denen es [Verfügbarkeitszonen](https://azure.microsoft.com/global-infrastructure/availability-zones/) gibt).<br/><br/>Wir unterstützen derzeit die Wiederherstellung in derselben Zone, die in virtuellen Computern angeheftet ist. Wenn die Zone jedoch aufgrund eines Ausfalls nicht verfügbar ist, tritt bei der Wiederherstellung ein Fehler auf.
Gen2-VMS | Unterstützt <br> Azure Backup unterstützt die Sicherung und Wiederherstellung von [Gen2-VMS](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). Bei Wiederherstellung über einen Wiederherstellungspunkt werden diese VMs als [Gen2-VMs](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/) wiederhergestellt.
Sicherung virtueller Azure-Computer mit Sperren | Wird für nicht verwaltete virtuelle Computer nicht unterstützt. <br><br> Wird für verwaltete virtuelle Computer unterstützt.
[Spot-VMs](../virtual-machines/spot-vms.md) | Nicht unterstützt. Azure Backup stellt Spot-VMs als reguläre Azure-VMs wieder her.
[Azure Dedicated Host](https://docs.microsoft.com/azure/virtual-machines/dedicated-hosts) | Unterstützt
Konfiguration von „Windows-Speicherplätze“ für eigenständige virtuelle Azure-Computer | Unterstützt

## <a name="vm-storage-support"></a>Unterstützung für VM-Speicher

**Komponente** | **Unterstützung**
--- | ---
Azure-VM-Datenträger (für Daten) | Unterstützung für die Sicherung virtueller Azure-Computer mit bis zu 32 Datenträgern.<br><br> Die Unterstützung für die Sicherung virtueller Azure-Computer mit nicht verwalteten Datenträgern oder klassischer VMs umfasst nur bis zu 16 Datenträger.
Datenträgergröße | Die Größe einzelner Datenträger kann bis zu 32 TB und maximal 256 TB für alle Datenträger in einer VM betragen.
Speichertyp | HDD Standard, SSD Standard, SSD Premium.
Verwaltete Datenträger | Unterstützt.
Verschlüsselte Datenträger | Unterstützt.<br/><br/> Virtuelle Azure-Computer mit aktiviertem Azure Disk Encryption können (mit oder ohne Azure AD-App) gesichert werden.<br/><br/> Verschlüsselte VMs können nicht auf Datei- oder Ordnerebene wiederhergestellt werden. Stattdessen muss die gesamte VM wiederhergestellt werden.<br/><br/> Sie können die Verschlüsselung auf virtuellen Computern aktivieren, die bereits durch Azure Backup geschützt werden.
Datenträger mit aktivierter Schreibbeschleunigung | Ab dem 23. November 2020 wird dies nur in den Regionen „Südkorea, Mitte“ (KRC) und „Südafrika, Norden“ (SAN) und für eine begrenzte Anzahl von Abonnements unterstützt. Für diese unterstützten Abonnements sichert Azure Backup die VMs mit Datenträgern mit aktivierter Schreibbeschleunigung während der Sicherung.<br><br>Für die nicht unterstützten Regionen ist eine Internetverbindung auf der VM erforderlich, um Momentaufnahmen von virtuellen Computern mit aktivierter Schreibbeschleunigung zu erstellen.<br><br> **Wichtiger Hinweis**: In diesen nicht unterstützten Regionen benötigen VMs mit WA-Datenträgern eine Internetverbindung für eine erfolgreiche Sicherung (auch wenn diese Datenträger von der Sicherung ausgeschlossen sind).
Sichern und Wiederherstellen von deduplizierten VMs/Datenträgern | Azure Backup unterstützt die Deduplizierung nicht. Weitere Informationen finden Sie in [diesem Artikel](./backup-support-matrix.md#disk-deduplication-support). <br/> <br/>  – Azure Backup dedupliziert nicht VM-übergreifend im Recovery Services-Tresor. <br/> <br/>  – Wenn es während der Wiederherstellung VMs im Deduplizierungsstatus gibt, können die Dateien nicht wiederhergestellt werden, da der Tresor das Format nicht verarbeiten kann. Allerdings können Sie die vollständige VM-Wiederherstellung erfolgreich ausführen.
Hinzufügen eines Datenträgers zu geschütztem virtuellen Computer | Unterstützt.
Ändern der Datenträgergröße auf geschütztem virtuellen Computer | Unterstützt.
Freigegebener Speicher| Das Sichern von VMs mit freigegebenem Clustervolume (Cluster Shared Volume, CSV) oder Dateiservern mit horizontaler Skalierung wird nicht unterstützt. Bei CSV-Schreibern treten während der Sicherung voraussichtlich Fehler auf. Bei der Wiederherstellung werden Datenträger, die CSV-Volumes enthalten, möglicherweise nicht hochgefahren.
[Freigegebene Datenträger](../virtual-machines/disks-shared-enable.md) | Wird nicht unterstützt.
SSD Ultra-Datenträger | Wird nicht unterstützt. Weitere Informationen finden Sie unter [Einschränkungen](selective-disk-backup-restore.md#limitations).
[Temporäre Datenträger](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview#temporary-disk) | Temporäre Datenträger werden nicht von Azure Backup gesichert.

## <a name="vm-network-support"></a>Netzwerkunterstützung bei virtuellen Computern

**Komponente** | **Unterstützung**
--- | ---
Anzahl der Netzwerkschnittstellen | Bis zur maximalen Anzahl von Netzwerkadaptern, die für eine bestimmte Größe von virtuellen Azure-Computern unterstützt werden<br/><br/> Netzwerkadapter werden erstellt, wenn der virtuelle Computer während des Wiederherstellungsvorgangs erstellt wird.<br/><br/> Die Anzahl von Netzwerkadaptern auf dem wiederhergestellten virtuellen Computer entspricht der Anzahl von Netzwerkadaptern auf dem virtuellen Computer bei Aktivierung des Schutzes. Wenn nach der Aktivierung des Schutzes Netzwerkschnittstellen entfernt werden, wirkt sich das nicht auf die Anzahl aus.
Externer und interner Lastenausgleich |Unterstützt. <br/><br/> Weitere Informationen zum Wiederherstellen von virtuellen Computern mit spezifischen Netzwerkeinstellungen finden Sie [hier](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations).
Mehrere reservierte IP-Adressen |Unterstützt. <br/><br/> Weitere Informationen zum Wiederherstellen von virtuellen Computern mit spezifischen Netzwerkeinstellungen finden Sie [hier](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations).
Virtuelle Computer mit mehreren Netzwerkadaptern| Unterstützt. <br/><br/> Weitere Informationen zum Wiederherstellen von virtuellen Computern mit spezifischen Netzwerkeinstellungen finden Sie [hier](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations).
Virtuelle Computer mit öffentlichen IP-Adressen| Unterstützt.<br/><br/> Ordnen Sie dem Netzwerkadapter eine vorhandene öffentliche IP-Adresse zu, oder erstellen Sie eine Adresse, und ordnen Sie diese nach der Wiederherstellung dem Netzwerkadapter zu.
Netzwerksicherheitsgruppe (NSG) für Netzwerkadapter oder Subnetz |Unterstützt.
Statische IP-Adresse | Wird nicht unterstützt.<br/><br/> Einer neuen VM, die aus einem Wiederherstellungspunkt erstellt wird, wird eine dynamische IP-Adresse zugewiesen.<br/><br/> Bei klassischen VMs kann eine VM mit einer reservierten IP-Adresse und ohne definierten Endpunkt nicht gesichert werden.
Dynamische IP-Adresse |Unterstützt.<br/><br/> Wenn der Netzwerkadapter auf der Quell-VM dynamische IP-Adressen verwendet, werden diese standardmäßig auch für den Netzwerkadapter auf der wiederhergestellten VM verwendet.
Azure Traffic Manager| Unterstützt.<br/><br/>Wenn sich die gesicherte VM in Traffic Manager befindet, müssen Sie die wiederhergestellte VM manuell in derselben Traffic Manager-Instanz hinzufügen.
Azure DNS |Unterstützt.
Benutzerdefinierter DNS |Unterstützt.
Ausgehende Verbindungen über HTTP-Proxy | Unterstützt.<br/><br/> Ein authentifizierter Proxy wird nicht unterstützt.
VNET-Dienstendpunkte| Unterstützt.<br/><br/> In den Speicherkontoeinstellungen für die Firewall und das virtuelle Netzwerk muss der Zugriff über alle Netzwerke zugelassen werden.

## <a name="vm-security-and-encryption-support"></a>Unterstützung für VM-Sicherheit und -Verschlüsselung

Azure Backup unterstützt die Verschlüsselung für Daten während der Übertragung und im Ruhezustand:

Netzwerkdatenverkehr für Azure:

- Sicherungsdatenverkehr von den Servern zum Recovery Services-Tresor wird per AES 256 (Advanced Encryption Standard) verschlüsselt.
- Sicherungsdaten werden über eine sichere HTTPS-Verbindung übertragen.
- Die Sicherungsdaten werden im Recovery Services-Tresor in verschlüsselter Form gespeichert.
- Nur Sie verfügen über den Verschlüsselungsschlüssel zum Entsperren dieser Daten. Die Sicherungsdaten können zu keinem Zeitpunkt von Microsoft entschlüsselt werden.

  > [!WARNING]
  > Nachdem der Tresor eingerichtet wurde, haben nur Sie Zugriff auf den Verschlüsselungsschlüssel. Microsoft bewahrt keine Kopie auf und hat keinen Zugriff auf den Schlüssel. Wenn der Schlüssel verlegt wird, kann Microsoft die gesicherten Daten nicht wiederherstellen.

Datensicherheit:

- Beim Sichern von Azure-VMs müssen Sie die Verschlüsselung *auf der* VM einrichten.
- Azure Backup unterstützt den Azure Disk Encryption-Dienst, der auf Windows-VMs BitLocker und auf Linux-VMs **dm-crypt** verwendet.
- Auf dem Back-End nutzt Azure Backup die [Speicherdienstverschlüsselung von Azure](../storage/common/storage-service-encryption.md), mit der ruhende Daten geschützt werden.

**Computer** | **Während der Übertragung** | **Ruhende Daten**
--- | --- | ---
Lokale Windows-Computer ohne DPM/MABS | ![Ja][green] | ![Ja][green]
Virtuelle Azure-Computer | ![Ja][green] | ![Ja][green]
Lokal/Azure-VMs mit DPM | ![Ja][green] | ![Ja][green]
Lokal/Azure-VMs mit MABS | ![Ja][green] | ![Ja][green]

## <a name="vm-compression-support"></a>Unterstützung für die Komprimierung bei virtuellen Computern

Azure Backup unterstützt die Komprimierung des Sicherungsdatenverkehrs. In der folgenden Tabelle finden Sie eine Zusammenfassung. Beachten Sie Folgendes:

- Für Azure-VMs liest die VM-Erweiterung die Daten über das Speichernetzwerk direkt aus dem Azure-Speicherkonto. Es ist also nicht erforderlich, diesen Datenverkehr zu komprimieren.
- Wenn Sie DPM oder MABS verwenden, können Sie Bandbreite sparen, indem Sie die Daten vor der Sicherung in DPM/MABS komprimieren.

**Computer** | **Komprimierung für MABS/DPM (TCP)** | **Komprimierung für Tresor (HTTPS)**
--- | --- | ---
Lokale Windows-Computer ohne DPM/MABS | Nicht verfügbar | ![Ja][green]
Virtuelle Azure-Computer | Nicht verfügbar | Nicht verfügbar
Lokal/Azure-VMs mit DPM | ![Ja][green] | ![Ja][green]
Lokal/Azure-VMs mit MABS | ![Ja][green] | ![Ja][green]

## <a name="next-steps"></a>Nächste Schritte

- [Sichern virtueller Azure-Computer](backup-azure-arm-vms-prepare.md)
- [Direktes Sichern von Windows-Computern](tutorial-backup-windows-server-to-azure.md) (ohne Sicherungsserver)
- [Einrichten von MABS](backup-azure-microsoft-azure-backup.md) für die Sicherung in Azure und anschließendes Sichern von Workloads per MABS
- [Einrichten von DPM](backup-azure-dpm-introduction.md) für die Sicherung in Azure und anschließendes Sichern von Workloads per DPM

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
