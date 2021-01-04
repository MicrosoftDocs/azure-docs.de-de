---
title: Allgemeine Fragen zur Hyper-V-Notfallwiederherstellung mit Azure Site Recovery
description: In diesem Artikel werden häufig gestellte Fragen zum Einrichten der Notfallwiederherstellung für lokale virtuelle Hyper-V-Computer in Azure mithilfe des Azure Site Recovery-Diensts zusammengefasst.
ms.date: 11/12/2019
ms.topic: conceptual
ms.openlocfilehash: e4f5ee71fc56cad247b57f94bf08a68bed8775f9
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754350"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Allgemeine Fragen: Hyper-V-Notfallwiederherstellung in Azure

Dieser Artikel enthält Antworten auf häufig gestellte Fragen zum Replizieren lokaler Hyper-V-VMs zu Azure. 

## <a name="general"></a>Allgemein

### <a name="how-is-site-recovery-priced"></a>Wie werden Site Recovery-Preise kalkuliert?
Nähere Informationen finden Sie unter [Site Recovery – Preise](https://azure.microsoft.com/pricing/details/site-recovery/).

### <a name="how-do-i-pay-for-azure-vms"></a>Wie zahle ich für Azure-VMs?
Während der Replikation werden Daten zu Azure-Speicher repliziert, und Sie bezahlen keine VM-Änderungen. Wenn Sie einen Failover zu Azure ausführen, erstellt Site Recovery automatisch Azure-IaaS-VMs. Danach werden Ihnen die Computeressourcen in Rechnung gestellt, die Sie in Azure nutzen.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Gibt es einen Kostenunterschied bei der Replikation zu einem Speicherkonto vom Typ „Allgemein v2“?

Normalerweise treten erhöhte Transaktionskosten bei GPv2-Speicherkonten auf, da viele Transaktionen über Azure Site Recovery durchgeführt werden. [Erfahren Sie mehr](../storage/common/storage-account-upgrade.md#pricing-and-billing), um die Änderung einschätzen zu können.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Was benötige ich in Hyper-V, um die Replikation mit Site Recovery zu orchestrieren?

Was Sie für Hyper-V-Hostserver benötigen, richtet sich nach dem Bereitstellungsszenario. Sehen Sie sich die Voraussetzungen für Hyper-V an:

* [Replizieren von Hyper-V-VMs (ohne VMM) in Azure](./hyper-v-azure-tutorial.md)
* [Replizieren von Hyper-V-VMs (mit VMM) in Azure](./hyper-v-vmm-disaster-recovery.md)
* [Replizieren von Hyper-V-VMs in ein sekundäres Rechenzentrum](./hyper-v-vmm-disaster-recovery.md)
* Wenn Sie die Replikation an einem sekundären Rechenzentrum durchführen, helfen Ihnen die Informationen unter [Unterstützte Gastbetriebssysteme für Hyper-V-VMs](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/mt126277(v=ws.11))weiter.
* Wenn Sie in Azure replizieren, unterstützt Site Recovery alle Gastbetriebssysteme, die [von Azure unterstützt werden](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794868(v=ws.10)).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Kann ich virtuelle Computer schützen, wenn Hyper-V auf einem Clientbetriebssystem ausgeführt wird?
Nein. VMs müssen sich auf einem Hyper-V-Hostserver befinden, der auf einem unterstützten Windows-Servercomputer ausgeführt wird. Wenn Sie einen Clientcomputer schützen müssen, können Sie diesen als physischen Computer in [Azure](./vmware-azure-tutorial.md) oder in einem [sekundären Rechenzentrum](./vmware-physical-secondary-disaster-recovery.md) replizieren.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Müssen Hyper-V-Hosts sich in VMM-Clouds befinden?
Wenn Sie die Replikation in einem sekundären Rechenzentrum durchführen möchten, müssen Hyper-V-VMs auf Hyper-V-Hostservern in einer VMM-Cloud angeordnet sein. Falls Sie in Azure replizieren möchten, können Sie virtuelle Computer mit oder ohne VMM-Clouds replizieren. [Weitere Informationen](./hyper-v-azure-tutorial.md) zur Hyper-V-Replikation in Azure.


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Kann ich virtuelle Hyper-V-Computer der 2. Generation in Azure replizieren?
Ja. Während des Failovers konvertiert Site Recovery Computer der 2. Generation in die 1. Generation. Beim Failback werden die Computer wieder in die 2. Generation zurückkonvertiert. [Weitere Informationen](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Kann ich Site Recovery mit VMM bereitstellen, wenn ich nur über einen VMM-Server verfüge?

Ja. Sie können VMs auf Hyper-V-Servern in der VMM-Cloud in Azure replizieren, oder Sie können zwischen VMM-Clouds auf demselben Server replizieren. Für die Replikation „lokal zu lokal“ empfehlen wir Ihnen, sowohl am primären Standort als auch am sekundären Standort jeweils einen VMM-Server zu verwenden. 

### <a name="what-do-i-need-in-azure"></a>Was muss ich in Azure tun?
Sie benötigen ein Azure-Abonnement, einen Recovery Services-Tresor, ein Speicherkonto und ein virtuelles Netzwerk. Tresor, Speicherkonto und Netzwerk müssen sich in derselben Region befinden.

### <a name="what-azure-storage-account-do-i-need"></a>Welches Azure-Speicherkonto benötige ich?
Sie benötigen ein LRS- oder GRS-Speicherkonto. Wir empfehlen Ihnen die Verwendung von GRS, damit Resilienz für die Daten besteht, wenn es zu einem regionalen Ausfall kommt oder wenn die primäre Region nicht wiederhergestellt werden kann. Storage Premium wird unterstützt.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Benötigt mein Azure-Konto Berechtigungen zum Erstellen von virtuellen Computern?
Wenn Sie ein Abonnementadminstrator sind, besitzen Sie die erforderlichen Replikationsberechtigungen. Wenn nicht, benötigen Sie Berechtigungen zum Erstellen einer Azure-VM in der Ressourcengruppe und dem virtuellen Netzwerk, die Sie beim Konfigurieren von Site Reocvery angeben, und Schreibberechtigungen für das ausgewählte Speicherkonto. [Weitere Informationen](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)

### <a name="is-replication-data-sent-to-site-recovery"></a>Werden Replikationsdaten an Site Recovery gesendet?
Nein. Site Recovery fängt replizierte Daten nicht ab und besitzt keine Informationen dazu, was auf Ihren virtuellen Computern ausgeführt wird. Replikationsdaten werden zwischen Hyper-V-Hosts und Azure-Speicher ausgetauscht. Site Recovery hat keine Möglichkeit, diese Daten abzufangen. Nur die Metadaten, die zum Orchestrieren von Replikation und Failover erforderlich sind, werden an den Site Recovery-Dienst gesendet.  

Site Recovery ist nach ISO 27001:2013, 27018, HIPAA und DPA zertifiziert und durchläuft gerade die Prüfungen für SOC2 und FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>Können wir lokale Metadaten innerhalb einer geografischer Region halten?
Ja. Wenn Sie einen Tresor in einer Region erstellen, stellen wir sicher, dass alle von Site Recovery verwendeten Metadaten innerhalb der geografischen Begrenzung der Region bleiben.

### <a name="does-site-recovery-encrypt-replication"></a>Verschlüsselt Site Recovery die Replikation?
Ja, sowohl Verschlüsselung bei der Übertragung als auch [Verschlüsselung in Azure](../storage/common/storage-service-encryption.md) wird unterstützt.


## <a name="deployment"></a>Bereitstellung

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Was kann ich mit der Hyper-V-zu-Azure-Replikation tun?

- **Notfallwiederherstellung**: Sie können die vollständige Notfallwiederherstellung einrichten. In diesem Szenario replizieren Sie lokale Hyper-V-VMs zu Azure-Speicher:
    - Sie können VMs in Azure replizieren. Wenn Ihre lokale Infrastruktur nicht verfügbar ist, führen Sie ein Failover zu Azure aus.
    - Wenn Sie ein Failover ausführen, werden Azure-VMs aus replizierten Daten erstellt. Sie können auf Apps und Workloads auf den Azure-VMs zugreifen.
    - Wenn Ihr lokales Datencenter wieder verfügbar ist, können Sie ein Failback von Azure zu Ihrem lokalen Standort ausführen.
- **Migration**: Sie können mit Site Recovery lokale Hyper-V-VMs zum Azure-Speicher migrieren. Dann führen Sie ein Failover vom lokalen Standort zu Azure aus. Nach dem Failover sind Ihre Apps und Workloads verfügbar und werden auf virtuellen Azure-Computern ausgeführt.


### <a name="what-do-i-need-on-premises"></a>Was benötige ich lokal?

Sie benötigen eine oder mehrere VMs, die auf einem oder mehreren eigenständigen oder gruppierten Hyper-V-Hosts ausgeführt werden. Sie können auch VMs replizieren, die auf von System Center Virtual Machine Manager (VMM) verwalteten Hosts ausgeführt werden.
- Wenn VMM nicht ausgeführt wird, sammeln Sie während der Bereitstellung von Site Recovery Hyper-V-Hosts und -Cluster in Hyper-V-Standorten. Installieren Sie die Site Recovery-Agents (den Azure Site Recovery-Anbieter und den Recovery Services-Agent) auf allen Hyper-V-Computern.
- Wenn sich die Hyper-V-Hosts in einer VMM-Cloud befinden, orchestrieren Sie die Replikation in VMM. Installieren Sie den Site Recovery-Anbieter auf dem VMM-Server und den Recovery Services-Agent auf allen Hyper-V-Computern. Stellen Sie eine Zuordnung zwischen logischen Netzwerken für VMM/VM-Netzwerken und Azure-VNets her.
- [Erfahren Sie mehr](hyper-v-azure-architecture.md) über die Hyper-V-zu-Azure-Architektur.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Kann ich VMs, die sich in einem Hyper-V-Cluster befinden, replizieren?

Ja, Site Recovery unterstützt gruppierte Hyper-V-Hosts. Beachten Sie dabei Folgendes:

- Alle Knoten des Clusters müssen beim selben Tresor registriert werden.
- Wenn Sie VMM nicht verwenden, müssen alle Hyper-V-Hosts im Cluster derselben Hyper-V-Site hinzugefügt werden.
- Installieren Sie den Azure Site Recovery-Anbieter und den Recovery Services-Agent auf allen Hyper-V-Hosts im Cluster, und fügen Sie alle Hosts einer Hyper-V-Site hinzu.
- Im Cluster müssen keine bestimmten Schritte ausgeführt werden.
- Wenn Sie das Bereitstellungsplanertool für Hyper-V ausführen, werden vom Tool die Profildaten dazu gesammelt, welcher Knoten ausgeführt wird, sowie dazu, wo die VM ausgeführt wird. Vom Tool können keine Daten zu einem deaktivierten Knoten gesammelt werden. Dieser wird jedoch überwacht. Sobald der Knoten betriebsbereit ist, beginnt das Tool mit dem Sammeln der VM-Profildaten (sofern die VM Teil der VM-Profilliste ist und auf dem Knoten ausgeführt wird).
- Wenn eine VM auf einem Hyper-V-Host in einem Site Recovery-Tresor zu einem anderen Hyper-V-Host im selben Cluster oder zu einem eigenständigen Host migriert wird, hat dies keine Auswirkung auf die Replikation für die VM. Der Hyper-V-Host muss bestimmte [Voraussetzungen](hyper-v-azure-support-matrix.md#on-premises-servers) erfüllen und in einem Site Recovery-Tresor konfiguriert werden. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Kann ich virtuelle Computer schützen, wenn Hyper-V auf einem Clientbetriebssystem ausgeführt wird?
Nein. VMs müssen sich auf einem Hyper-V-Hostserver befinden, der auf einem unterstützten Windows-Servercomputer ausgeführt wird. Wenn Sie einen Clientcomputer schützen müssen, können Sie [diesen als physischen Computer in Azure replizieren](physical-azure-disaster-recovery.md).

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Kann ich virtuelle Hyper-V-Computer der 2. Generation in Azure replizieren?
Ja. Während des Failovers konvertiert Site Recovery Computer der 2. Generation in die 1. Generation. Beim Failback werden die Computer wieder in die 2. Generation zurückkonvertiert.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Kann ich Site Recovery-Szenarien mit einem SDK automatisieren?
Ja. Sie können Site Recovery-Workflows mithilfe der REST-API, PowerShell oder Azure SDK automatisieren. Derzeit unterstützte Szenarios für die Replikation von Hyper-V zu Azure mithilfe von PowerShell:

- [Replizieren von Hyper-V ohne VMM mithilfe von PowerShell](hyper-v-azure-powershell-resource-manager.md)
- [Replizieren von Hyper-V mit VMM mithilfe von PowerShell](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replikation

### <a name="where-do-on-premises-vms-replicate-to"></a>Zu welchem Speicher werden lokale virtuelle Computer repliziert?
Daten werden zu Azure-Speicher repliziert. Wenn Sie ein Failover zu Azure ausführen, erstellt Site Recovery automatisch Azure-VMs aus dem Speicherkonto.

### <a name="what-apps-can-i-replicate"></a>Welche Apps kann ich replizieren?
Sie können jede App oder Arbeitsauslastung, auf einer Hyper-V-VM ausführen, die mit den [Replikationsanforderungen](hyper-v-azure-support-matrix.md#replicated-vms) kompatibel ist. Site Recovery bietet Unterstützung für die anwendungsorientierte Replikation, sodass für Apps ein Failover und ein Failback zum „intelligenten Zustand“ ausgeführt werden kann. Site Recovery kann in Microsoft-Anwendungen wie SharePoint, Exchange, Dynamics, SQL Server und Active Directory integriert werden. Zudem kann Site Recovery eng in die Produkte führender Anbieter wie Oracle, SAP, IBM und Red Hat eingebunden werden. [Erfahren Sie mehr](site-recovery-workload.md) über den Schutz von Workloads.

### <a name="whats-the-replication-process"></a>Was ist ein Replikationsprozess?

1. Wenn die erste Replikation ausgelöst wird, wird eine Hyper-V-Momentaufnahme für den virtuellen Computer erstellt.
2. Virtuelle Festplatten auf der VM werden nacheinander repliziert, bis alle in Azure kopiert wurden. Die Dauer ist abhängig von der Größe der VM und von der Netzwerkbandbreite. Erfahren Sie, wie die Netzwerkbandbreite vergrößert werden kann.
3. Falls während der ersten Replikation Datenträgeränderungen auftreten, werden die Änderungen mit dem Replication Tracker für Hyper-V-Replikate in Form von Hyper-V-Replikationsprotokollen (.hrl) nachverfolgt. Diese Protokolldateien befinden sich im gleichen Ordner wie die Datenträger. Jeder Datenträger verfügt über eine zugeordnete HRL-Datei, die an den sekundären Speicher gesendet wird. Beachten Sie, dass die Momentaufnahme- und Protokolldateien Festplattenressourcen belegen, während die anfängliche Replikation durchgeführt wird.
4. Nach Abschluss der ersten Replikation wird die Momentaufnahme des virtuellen Computers gelöscht.
5. Alle Datenträgeränderungen im Protokoll werden synchronisiert und mit dem übergeordneten Datenträger zusammengeführt.
6. Nach Abschluss der ersten Replikation wird der Auftrag „Schutz auf dem virtuellen Computer abschließen“ ausgeführt. Er konfiguriert Einstellungen für das Netzwerk und andere Einstellungen für die Zeit nach der Replikation, sodass die VM geschützt ist.
7. In dieser Phase können Sie die Einstellungen der VM überprüfen, um sicherzustellen, dass sie bereit für das Failover ist. Sie können einen Notfallwiederherstellungsdrill (Testfailover) für die VM ausführen, um zu überprüfen, ob das Failover wie erwartet ausgeführt wird.
8. Nach der ersten Replikation beginnt die Deltareplikation gemäß der Replikationsrichtlinie.
9. Änderungen werden in HRL-Dateien protokolliert. Jedem für die Replikation konfigurierten Datenträger ist eine HRL-Datei zugeordnet.
10. Das Protokoll wird an das Speicherkonto des Kunden gesendet. Beim Übermitteln eines Protokolls an Azure werden Änderungen am primären Datenträger in einer anderen Protokolldatei im gleichen Ordner nachverfolgt.
11. Während der Erst- und der Deltareplikation können Sie die VM im Azure-Portal überwachen.

[Erfahren Sie mehr](hyper-v-azure-architecture.md#replication-process) zum Replikationsprozess.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Kann ich über ein Site-to-Site-VPN zu Azure replizieren?

Site Recovery repliziert Daten über einen öffentlichen Endpunkt oder mittels Microsoft ExpressRoute-Peering aus einem lokalen Speicher zu Azure-Speicher. Replikation über ein Site-to-Site-VPN-Netzwerk wird nicht unterstützt.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Kann ich mit ExpressRoute zu Azure replizieren?

Ja, mit ExpressRoute können VMs zu Azure repliziert werden. Site Recovery repliziert die Daten über einen öffentlichen Endpunkt zu einem Azure Storage-Konto, und Sie müssen [Microsoft-Peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) für die Site Recovery-Replikation einrichten. Nach dem Failover virtueller Computer zu einem virtuellen Azure-Netzwerk können Sie mit [privatem Peering](../expressroute/expressroute-circuit-peerings.md#privatepeering) auf sie zugreifen.


### <a name="why-cant-i-replicate-over-vpn"></a>Warum kann ich nicht über VPN replizieren?

Wenn Sie nach Azure replizieren, erreicht der Replikationsdatenverkehr die öffentlichen Endpunkte eines Azure Storage-Kontos. Daher kann die Replikation über das öffentliche Internet nur mit ExpressRoute (Microsoft-Peering) erfolgen und VPN funktioniert nicht. 

### <a name="what-are-the-replicated-vm-requirements"></a>Welche Anforderungen stellt die Replikation an virtuelle Computer?

Für die Replikation muss auf einer Hyper-V-VM ein unterstütztes Betriebssystem ausgeführt werden. Darüber hinaus muss der virtuelle Computer die Anforderungen für Azure-VMs erfüllen. [Erfahren Sie mehr](hyper-v-azure-support-matrix.md#replicated-vms) in der Unterstützungsmatrix.

### <a name="why-is-an-additional-standard-storage-account-required-if-i-replicate-my-virtual-machine-disks-to-premium-storage"></a>Warum ist ein zusätzliches Storage Standard-Konto erforderlich, wenn ich meine VM-Datenträger in Storage Premium repliziere?

Wenn Sie Ihre lokalen virtuellen Computer/physischen Server in Storage Premium replizieren, werden alle Daten auf den Datenträgern des geschützten Computers im Storage Premium-Konto repliziert. Zum Speichern von Replikationsprotokollen ist ein zusätzliches Storage Standard-Konto erforderlich. Nachdem die Anfangsphase der Replikation von Datenträgerdaten abgeschlossen ist, werden alle Änderungen an den Daten der lokalen Datenträger kontinuierlich nachverfolgt und als Replikationsprotokolle in diesem zusätzlichen Standard Storage-Konto gespeichert.

### <a name="how-often-can-i-replicate-to-azure"></a>Wie oft kann ich zu Azure replizieren?

Hyper-V-VMs können alle 30 Sekunden (außer bei Storage Premium) oder alle 5 Minuten repliziert werden.

### <a name="can-azure-site-recovery-and-hyper-v-replica-be-configured-together-on-a-hyper-v-machine"></a>Können Azure Site Recovery und das Hyper-V-Replikat auf einem Hyper-V-Computer gleichzeitig konfiguriert werden?

Ja, Azure Site Recovery und das Hyper-V-Replikat können zusammen für einen Computer konfiguriert werden. Der Computer muss jedoch als physischer Computer geschützt werden und wird mithilfe eines Konfigurations-/Prozessservers in Azure repliziert. Weitere Informationen zum Schützen physischer Computer finden Sie [hier](https://docs.microsoft.com/azure/site-recovery/physical-azure-architecture).

### <a name="can-i-extend-replication"></a>Kann ich die Replikation erweitern?
Eine erweiterte oder verkettete Replikation wird nicht unterstützt. Fordern Sie dieses Feature im [Feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Kann ich eine erste Offlinereplikation durchführen?
Dies wird nicht unterstützt. Fordern Sie dieses Feature im [Feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Kann ich Datenträger ausschließen?
Ja, sie können Datenträger von der Replikation ausschließen. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Kann ich virtuelle Computer mit dynamischen Datenträgern replizieren?
Dynamische Datenträger können repliziert werden. Der Betriebssystem-Datenträger muss ein Basisdatenträger sein.



## <a name="security"></a>Sicherheit

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Welchen Zugriff auf Hyper-V-Hosts benötigt Site Recovery?

Site Recovery benötigt Zugriff auf Hyper-V-Hosts, um die VMs zu replizieren, die Sie auswählen. Folgendes wird von Site Recovery auf Hyper-V-Hosts installiert:

- Wenn Sie VMM nicht ausführen, werden der Site Recovery-Anbieter und der Recovery Services-Agent auf allen Hosts installiert.
- Wenn Sie VMM ausführen, wird der Recovery Services-Agent auf allen Hosts installiert. Der Anbieter wird auf dem VMM-Server ausgeführt.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Was wird von Site Recovery auf Hyper-V-VMs installiert?

Von Site Recovery wird nichts explizit auf Hyper-V-VMs installiert, die für die Replikation aktiviert sind.




## <a name="failover-and-failback"></a>Failover und Failback


### <a name="how-do-i-fail-over-to-azure"></a>Wie führe ich ein Failover zu Azure aus?

Sie können ein geplantes oder ungeplantes Failover von lokalen Hyper-V-VMs zu Azure ausführen.

- Wenn Sie ein geplantes Failover durchführen, werden die Quell-VMs heruntergefahren, um sicherzustellen, dass kein Datenverlust auftritt.
- Sie können ein ungeplantes Failover ausführen, wenn kein Zugriff auf Ihren primären Standort möglich ist.
- Sie können ein Failover für einen einzelnen Computer ausführen oder Wiederherstellungspläne erstellen, um das Failover von mehreren Computern zu orchestrieren.
- Das Failover besteht aus zwei Teilen:
    - Nachdem die erste Phase des Failovers abgeschlossen ist, sollten Sie die erstellten Replikat-VMs in Azure sehen können. Sie können der VM bei Bedarf eine öffentliche IP-Adresse zuweisen.
    - Anschließend führen Sie ein Commit für das Failover durch, um von der Replikat-VM in Azure auf die Workload zuzugreifen.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Wie greife ich nach einem Failover auf virtuelle Azure-Computer zu?
Nach einem Failover können Sie über eine sichere Internetverbindung, eine Site-to-Site-VPN-Verbindung oder über Azure ExpressRoute auf die Azure-VMs zugreifen. Sie müssen eine Reihe von Vorbereitungen treffen, um eine Verbindung herzustellen. [Weitere Informationen](failover-failback-overview.md#connect-to-azure-after-failover)

### <a name="is-failed-over-data-resilient"></a>Sind Daten, mit denen ein Failover ausgeführt wurde, stabil?
Azure ist auf Resilienz ausgelegt. Site Recovery ist für ein Failover zu einem sekundären Azure-Rechenzentrum (gemäß Azure-SLA) konzipiert. Wenn ein Failover auftritt, stellen wir sicher, dass Ihre Metadaten und Tresore innerhalb der gleichen geografischen Region bleiben, die Sie für Ihren Tresor ausgewählt haben.

### <a name="is-failover-automatic"></a>Erfolgt ein Failover automatisch?
Ein [Failover](site-recovery-failover.md) erfolgt nicht automatisch. Sie initiieren Failover mit einem Mausklick im Portal, oder Sie können [PowerShell](/powershell/module/az.recoveryservices) verwenden, um ein Failover auszulösen.

### <a name="how-do-i-fail-back"></a>Wie führe ich ein Failback aus?

Sobald Ihre lokale Infrastruktur wieder funktioniert und ausgeführt werden kann, können Sie ein Failback ausführen. Das Failback erfolgt in drei Phasen:

1. Starten Sie ein geplantes Failover von Azure zum lokalen Standort. Hierzu haben Sie verschiedene Möglichkeiten:

    - Weniger Ausfallzeiten: Wenn Sie diese Option verwenden, synchronisiert Site Recovery Daten vor dem Failover. und überprüft auf geänderte Datenblocks. Diese werden dann an den lokalen Standort heruntergeladen, während die Azure-VM weiterhin ausgeführt wird und die Downtime minimiert. Wenn Sie manuell angeben, dass das Failover abgeschlossen werden soll, wird die Azure-VM heruntergefahren, endgültige Deltaänderungen werden kopiert und das Failover startet.
    - Vollständiger Download: Bei dieser Option werden Daten während des Failovers synchronisiert. Mit dieser Option wird der gesamte Datenträger heruntergeladen. Dies erfolgt schneller, da keine Prüfsummen berechnet werden. Jedoch kommt es zu mehr Downtime. Verwenden Sie diese Option, wenn Sie die Azure-Replikat-VMs für einige Zeit ausgeführt haben oder die lokale VM gelöscht wurde.

2. Sie können wählen, ob Sie das Failback zur gleichen VM oder zu einer alternativen VM ausführen. Sie können angeben, dass Site Recovery die VM erstellen sollte, wenn sie nicht bereits vorhanden ist.
3. Nach Beendigung der Erstsynchronisierung wählen Sie aus, dass das Failover abgeschlossen werden soll. Sobald es abgeschlossen ist, können Sie sich bei der lokalen VM anmelden, um zu überprüfen, ob alles wie erwartet funktioniert. Im Azure-Portal können Sie sehen, dass die Azure-VMs angehalten wurden.
4. Committen Sie das Failover, um es abzuschließen, und greifen wieder von der lokalen VM auf die Workload zu.
5. Nachdem Failbacks für die Workloads ausgeführt wurden, aktivieren Sie die umgekehrte Replikation, sodass die lokalen VMs erneut in Azure replizieren.

### <a name="can-i-fail-back-to-a-different-location"></a>Kann ich ein Failback zu einem anderen Speicherort ausführen?
Ja, wenn Sie ein Failover zu Azure ausgeführt haben, können Sie ein Failback zu einem anderen Speicherort ausführen, wenn der ursprüngliche nicht verfügbar ist. [Weitere Informationen](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)
