---
title: Architektur der Azure-zu-Azure-Replikation in Azure Site Recovery | Microsoft-Dokumentation
description: Dieser Artikel bietet einen Überblick über die Komponenten und die Architektur, die beim Einrichten der Notfallwiederherstellung zwischen Azure-Regionen für virtuelle Azure-Computer mit dem Azure Site Recovery-Dienst verwendet werden.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 25cf3914274e73e0789aa87e9288649d1b0cb1eb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399570"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Architektur der Notfallwiederherstellung von Azure zu Azure


Dieser Artikel beschreibt die Architektur, Komponenten und Prozesse, die bei der Bereitstellung einer Notfallwiederherstellung von virtuellen Azure-Computern (VMs) mithilfe des Diensts [Azure Site Recovery](site-recovery-overview.md) verwendet werden. Mit der Einrichtung der Notfallwiederherstellung werden Azure-VMs zwischen Regionen repliziert. Bei einem Ausfall können Sie für die VMs ein Failover zur sekundären Region ausführen und dort darauf zugreifen. Wenn alles wieder normal funktioniert, können Sie erneut ein Failback ausführen und am primären Standort weiterarbeiten.



## <a name="architectural-components"></a>Komponenten der Architektur

Die an der Notfallwiederherstellung beteiligten Komponenten für Azure-VMs werden in der folgenden Tabelle zusammengefasst.

**Komponente** | **Anforderungen**
--- | ---
**VMs in der Quellregion** | Eine von mehreren Azure-VMs in einer [unterstützten Quellregion](azure-to-azure-support-matrix.md#region-support).<br/><br/> Die VMs können unter beliebigen [unterstützten Betriebssystemen](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) ausgeführt werden.
**Speicher der Quell-VM** | Virtuelle Azure-Computer können verwaltet werden oder über nicht verwaltete Datenträger verfügen, die über mehrere Speicherkonten verteilt sind.<br/><br/>[Erfahren Sie mehr](azure-to-azure-support-matrix.md#replicated-machines---storage) über die unterstützten Azure Storage-Varianten.
**VM-Quellnetzwerke** | Die VMs können sich in einem oder mehreren Subnetzen in einem virtuellen Netzwerk (VNET) in der Quellregion befinden. [Erfahren Sie mehr](azure-to-azure-support-matrix.md#replicated-machines---networking) über die Netzwerkanforderungen.
**Cachespeicherkonto** | Sie benötigen ein Cachespeicherkonto im Quellnetzwerk. Während der Replikation werden VM-Änderungen im Cache gespeichert, bevor sie an den Zielspeicher gesendet werden.<br/><br/> Ein Cache stellt sicher, dass die Auswirkungen auf die auf dem virtuellen Computer ausgeführten Produktionsanwendungen möglichst gering sind.<br/><br/> [Erfahren Sie mehr](azure-to-azure-support-matrix.md#cache-storage) über die Anforderungen an den Cachespeicher. 
**Zielressourcen** | Zielressourcen werden während der Replikation und bei einem Failover verwendet. Site Recovery kann standardmäßig Zielressourcen einrichten, Sie können diese aber auch selbst erstellen oder bearbeiten.<br/><br/> Überprüfen Sie in der Zielregion, ob Sie virtuelle Computer erstellen können und ob Ihr Abonnement über ausreichend Ressourcen zur Unterstützung der VM-Größen verfügt, die in der Zielregion benötigt werden. 

![Replikationsquelle und -ziel](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>Zielressourcen

Wenn Sie die Replikation für einen virtuellen Computer aktivieren, bietet Site Recovery die Möglichkeit, Zielressourcen automatisch zu erstellen. 

**Zielressource** | **Standardeinstellung**
--- | ---
**Zielabonnement** | Identisch mit dem Quellabonnement.
**Zielressourcengruppe** | Die Ressourcengruppe, der VMs nach einem Failover angehören.<br/><br/> Dies kann jede Azure-Region außer der Quellregion sein.<br/><br/> Site Recovery erstellt in der Zielregion eine neue Ressourcengruppe mit dem Suffix „asr“.<br/><br/>
**Ziel-VNET** | Das virtuelle Netzwerk (VNET), in dem sich replizierte VMs nach einem Failover befinden. Eine Netzwerkzuordnung von virtuellen Quell- zu Zielnetzwerken (und umgekehrt) wird erstellt.<br/><br/> Site Recovery erstellt ein neues VNET und ein Subnetz mit dem Suffix „asr“.
**Zielspeicherkonto** |  Wenn der virtuelle Computer keine verwalteten Datenträger verwendet, ist dies das Speicherkonto, in das die Daten repliziert werden.<br/><br/> Site Recovery erstellt ein neues Speicherkonto in der Zielregion, um das Quellspeicherkonto zu spiegeln.
**Verwaltete Replikatdatenträger** | Wenn der virtuelle Computer einen verwalteten Datenträger verwendet, ist dies der verwaltete Datenträger, auf den die Daten repliziert werden.<br/><br/> Site Recovery erstellt verwaltete Replikatdatenträger in der Speicherregion, um die Quelle zu spiegeln.
**Zielverfügbarkeitsgruppen** |  Verfügbarkeitsgruppe, in der sich die replizierten VMs nach einem Failover befinden.<br/><br/> Site Recovery erstellt eine Verfügbarkeitsgruppe in der Zielregion mit dem Suffix „asr“ für die VMs, die sich am Quellstandort in einer Verfügbarkeitsgruppe befinden. Wenn eine Verfügbarkeitsgruppe vorhanden ist, wird diese verwendet und keine neue erstellt.
**Zielverfügbarkeitszonen** | Wenn die Zielregion Verfügbarkeitszonen unterstützt, weist Site Recovery die gleiche Anzahl von Zonen zu wie in der Quellregion.

### <a name="managing-target-resources"></a>Verwalten von Zielressourcen

Sie können die Zielressourcen wie folgt verwalten:

- Sie können Einstellungen für das Ziel ändern, während Sie die Replikation aktivieren.
- Sie können Einstellungen für das Ziel ändern, nachdem die Replikation bereits ausgeführt wird. Die einzige Ausnahme bildet der Verfügbarkeitstyp (Einzelinstanz, Satz oder Zone). Zum Ändern dieser Einstellung müssen Sie die Replikation deaktivieren, anschließend die Einstellung ändern und die Replikation dann wieder aktivieren.



## <a name="replication-policy"></a>Replikationsrichtlinie 

Wenn Sie die Replikation von Azure-VMs aktivieren, erstellt Site Recovery standardmäßig eine neue Replikationsrichtlinie mit den Standardeinstellungen, die in der Tabelle zusammengefasst sind.

**Richtlinieneinstellung** | **Details** | **Standard**
--- | --- | ---
**Aufbewahrungszeitraum des Wiederherstellungspunkts** | Gibt an, wie lange Site Recovery Wiederherstellungspunkte beibehält | 24 Stunden
**App-konsistente Momentaufnahmenhäufigkeit** | Gibt an, wie oft Site Recovery eine App-konsistente Momentaufnahme erstellt. | Alle 60 Minuten.

### <a name="managing-replication-policies"></a>Verwalten von Replikationsrichtlinien

Sie können die Standardrichtlinien für die Replikation wie folgt verwalten und ändern:
- Sie können die Einstellungen ändern, während Sie die Replikation aktivieren.
- Sie können jederzeit eine Replikationsrichtlinie erstellen und diese dann anwenden, wenn Sie die Replikation aktivieren.

### <a name="multi-vm-consistency"></a>Multi-VM-Konsistenz

Wenn Sie virtuelle Computer gemeinsam replizieren möchten und beim Failover über gemeinsame absturz- und App-konsistente Wiederherstellungspunkte verfügen, können Sie sie in einer Replikationsgruppe zusammenfassen. Multi-VM-Konsistenz wirkt sich auf die Leistung einer Workload aus und sollte nur für virtuelle Computer mit Workloads verwendet werden, bei denen Konsistenz auf sämtlichen Computern erforderlich ist. 



## <a name="snapshots-and-recovery-points"></a>Momentaufnahmen und Wiederherstellungspunkte

Wiederherstellungspunkte werden aus Momentaufnahmen von VM-Datenträgern zu einem bestimmten Zeitpunkt erstellt. Wenn Sie ein Failover eines virtuellen Computers ausführen, verwenden Sie einen Wiederherstellungspunkt, um die VM am Zielstandort wiederherzustellen.

Bei einem Failover soll in der Regel sichergestellt werden, dass der virtuelle Computer ohne Beschädigung oder Verlust von Daten gestartet wird und dass die VM-Daten für das Betriebssystem und die Apps, die auf dem virtuellen Computer ausgeführt werden, konsistent sind. Dies hängt vom Typ der erstellten Momentaufnahmen ab.

Site Recovery verwendet Momentaufnahmen wie folgt:

1. Site Recovery verwendet absturzkonsistente Momentaufnahmen von Daten standardmäßig und App-konsistente Momentaufnahmen, wenn Sie für diese eine Häufigkeit angeben.
2. Wiederherstellungspunkte werden aus Momentaufnahmen erstellt und gemäß den Aufbewahrungseinstellungen in der Replikationsrichtlinie gespeichert.

### <a name="consistency"></a>Konsistenz

In der folgenden Tabelle werden die verschiedenen Konsistenztypen erläutert.

### <a name="crash-consistent"></a>Absturzkonsistent

**Beschreibung** | **Details** | **Empfehlung**
--- | --- | ---
Eine absturzkonsistente Momentaufnahme erfasst Daten, die sich zum Zeitpunkt der Erstellung der Momentaufnahme auf dem Datenträger befunden haben. Sie enthält keine Daten aus dem Arbeitsspeicher.<br/><br/> Sie enthält die Entsprechung der Daten auf dem Datenträger, wenn zum Zeitpunkt der Momentaufnahme der virtuelle Computer abgestürzt oder das Verbindungskabel zum Server getrennt worden wäre.<br/><br/> Absturzkonsistenz garantiert keine Datenkonsistenz für das Betriebssystem oder die Apps auf der VM. | Site Recovery erstellt standardmäßig alle fünf Minuten einen absturzkonsistenten Wiederherstellungspunkt. Diese Einstellung kann nicht geändert werden.<br/><br/>  | Heutzutage können die meisten Apps gut aus absturzkonsistenten Wiederherstellungspunkten wiederhergestellt werden.<br/><br/> Absturzkonsistente Wiederherstellungspunkte reichen in der Regel für die Replikation von Betriebssystemen und Apps wie DHCP-Server und Druckserver aus.

### <a name="app-consistent"></a>App-konsistent

**Beschreibung** | **Details** | **Empfehlung**
--- | --- | ---
App-konsistente Wiederherstellungspunkte werden aus App-konsistenten Momentaufnahmen erstellt.<br/><br/> Eine App-konsistente Momentaufnahme enthält alle Informationen in einer absturzkonsistenten Momentaufnahme sowie darüber hinaus alle Daten im Arbeitsspeicher und alle gerade bearbeiteten Transaktionen. | App-konsistente Momentaufnahmen verwenden den Volumeschattenkopie-Dienst (Volume Shadow Copy Service, VSS):<br/><br/>   1) Wenn eine Momentaufnahme initiiert wird, führt VSS einen COW-Vorgang (Copy-On-Write) auf dem Volume aus.<br/><br/>   2) Vor der Ausführung des COW-Vorgangs informiert VSS jede App auf dem Computer darüber, dass die im Arbeitsspeicher befindlichen Daten auf den Datenträger übertragen werden müssen.<br/><br/>   3) VSS erlaubt dann der Sicherungs-/Notfallwiederherstellungs-App (in diesem Fall Site Recovery) das Lesen der Momentaufnahmedaten und das Fortsetzen des Vorgangs. | App-konsistente Momentaufnahmen werden mit der von Ihnen angegebenen Häufigkeit erstellt. Diese Häufigkeit sollte immer kleiner sein als der Wert für die Beibehaltung von Wiederherstellungspunkten. Wenn Sie beispielsweise Wiederherstellungspunkte gemäß der Standardeinstellung von 24 Stunden beibehalten, sollten Sie die Häufigkeit auf weniger als 24 Stunden festlegen.<br/><br/>Sie sind komplexer und dauern daher länger als absturzkonsistente Momentaufnahmen.<br/><br/> Sie haben auch Auswirkungen auf die Leistung von Apps, die auf einem virtuellen Computer, für den die Replikation aktiviert wurde, ausgeführt werden. 

## <a name="replication-process"></a>Replikationsprozess

Wenn Sie die Replikation für eine Azure-VM aktivieren, geschieht Folgendes:

1. Die Site Recovery Mobility Service-Erweiterung wird automatisch auf der VM installiert.
2. Die Erweiterung registriert die VM bei Site Recovery.
3. Die fortlaufende Replikation für den virtuellen Computer beginnt.  Schreibvorgänge auf den Datenträgern werden sofort auf das Cachespeicherkonto am Quellstandort übertragen.
4. Site Recovery verarbeitet die Daten im Cache und sendet sie an das Zielspeicherkonto oder an verwaltete Replikatdatenträger weiter.
5. Nach der Verarbeitung der Daten werden alle fünf Minuten absturzkonsistente Wiederherstellungspunkte generiert. App-konsistente Wiederherstellungspunkte werden gemäß der Einstellung in der Replikationsrichtlinie generiert.

![Schritt 2: Aktivieren des Replikationsprozesses](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**Replikationsprozess**

## <a name="connectivity-requirements"></a>Konnektivitätsanforderungen

 Die Azure-VMs, die Sie replizieren, benötigen ausgehende Konnektivität. Site Recovery benötigt nie eingehende Verbindungen mit der VM. 

### <a name="outbound-connectivity-urls"></a>Ausgehende Konnektivität (URLs)

Wenn der ausgehende Zugriff für virtuelle Computer über URLs gesteuert wird, erlauben Sie diese URLs.

| **URL** | **Details** |
| ------- | ----------- |
| *.blob.core.windows.net | Ermöglicht das Schreiben von Daten aus der VM in das Cachespeicherkonto in der Quellregion |
| login.microsoftonline.com | Stellt die Autorisierung und Authentifizierung für Site Recovery-Dienst-URLs bereit. |
| *.hypervrecoverymanager.windowsazure.com | Ermöglicht die Kommunikation der VM mit Site Recovery |
| *.servicebus.windows.net | Ermöglicht es der VM, die Site Recovery-Überwachung und -Diagnosedaten zu schreiben |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Ausgehende Konnektivität für IP-Adressbereiche

Zum Steuern der ausgehenden Konnektivität für virtuelle Computer über IP-Adressen erlauben Sie diese Adressen.

#### <a name="source-region-rules"></a>Regeln für die Quellregion

**Regel** |  **Details** | **Diensttag**
--- | --- | --- 
HTTPS ausgehend zulassen: Port 443 | Erlauben Sie die Adressbereiche der Speicherkonten in der Quellregion. | Speicher.\<Name-der-Region>.
HTTPS ausgehend zulassen: Port 443 | Erlauben Sie die Adressbereiche für Azure Active Directory (Azure AD).<br/><br/> Wenn zukünftig Azure AD-Adressen hinzugefügt werden, müssen Sie neue Regeln für Netzwerksicherheitsgruppen (NSG) erstellen.  | AzureActiveDirectory
HTTPS ausgehend zulassen: Port 443 | Erlauben Sie den Zugriff auf [Site Recovery-Endpunkte](https://aka.ms/site-recovery-public-ips), die denen am Zielstandort entsprechen. 

#### <a name="target-region-rules"></a>Regeln für die Zielregion

**Regel** |  **Details** | **Diensttag**
--- | --- | --- 
HTTPS ausgehend zulassen: Port 443 | Erlauben Sie die Adressbereiche der Speicherkonten in der Zielregion. | Speicher.\<Name-der-Region>.
HTTPS ausgehend zulassen: Port 443 | Erlauben Sie die Adressbereiche für Azure AD.<br/><br/> Wenn zukünftig Azure AD-Adressen hinzugefügt werden, müssen Sie neue NSG-Regeln erstellen.  | AzureActiveDirectory
HTTPS ausgehend zulassen: Port 443 | Erlauben Sie den Zugriff auf [Site Recovery-Endpunkte](https://aka.ms/site-recovery-public-ips), die denen am Quellstandort entsprechen. 


#### <a name="control-access-with-nsg-rules"></a>Steuern des Zugriffs mit NSG-Regeln

Wenn Sie die VM-Konnektivität durch Filtern des Netzwerkdatenverkehrs zu und aus Azure-Netzwerken/-Subnetzen mithilfe von [NSG-Regeln](https://docs.microsoft.com/azure/virtual-network/security-overview) steuern, beachten Sie die folgenden Voraussetzungen:

- NSG-Regeln für die Azure-Quellregion sollten ausgehenden Zugriff für den Replikationsdatenverkehr zulassen.
- Es wird empfohlen, die Regeln zunächst in einer Testumgebung zu erstellen, bevor sie in die Produktion übernommen werden.
- Verwenden Sie [Diensttags](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags), anstatt einzelne IP-Adressen zuzulassen.
    - Diensttags stellen eine Gruppe von IP-Adresspräfixen dar und vereinfachen die Erstellung von Sicherheitsregeln.
    - Microsoft aktualisiert die Diensttags im Lauf der Zeit automatisch. 
 
Erfahren Sie mehr über [ausgehende Konnektivität](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) für Site Recovery und das [Steuern der Konnektivität mit Netzwerksicherheitsgruppen](concepts-network-security-group-with-site-recovery.md).


### <a name="connectivity-for-multi-vm-consistency"></a>Konnektivität für Multi-VM-Konsistenz

Wenn Sie die Multi-VM-Konsistenz aktivieren, kommunizieren Computer in der Replikationsgruppe über den Port 20004 miteinander.
- Stellen Sie sicher, dass die interne Kommunikation zwischen den VMs über Port 20004 nicht durch eine Firewallappliance blockiert wird.
- Wenn Sie Linux-VMs in eine Replikationsgruppe einschließen möchten, stellen Sie sicher, dass der ausgehende Datenverkehr auf Port 20004 gemäß den Anweisungen für die jeweilige Linux-Version manuell geöffnet wird.




## <a name="failover-process"></a>Failoverprozess

Bei der Initiierung eines Failovers werden die VMs in der Zielressourcengruppe, im virtuellen Zielnetzwerk, im Zielsubnetz und in der Zielverfügbarkeitsgruppe erstellt. Bei einem Failover können Sie einen beliebigen Wiederherstellungspunkt verwenden.

![Failoverprozess](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Nächste Schritte

[Schnelles Replizieren](azure-to-azure-quickstart.md) einer Azure-VM in eine sekundäre Region
