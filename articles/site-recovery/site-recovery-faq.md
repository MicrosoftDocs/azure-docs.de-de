---
title: Allgemeine Fragen zum Azure Site Recovery-Dienst
description: In diesem Artikel werden häufig gestellte allgemeine Fragen zu Azure Site Recovery behandelt.
ms.topic: conceptual
ms.date: 7/14/2020
ms.author: raynew
ms.openlocfilehash: 9db91a15c0ee5c982f73f36a36f12b38b969a125
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820195"
---
# <a name="general-questions-about-azure-site-recovery"></a>Allgemeine Fragen zu Azure Site Recovery

Dieser Artikel fasst häufig gestellte Fragen zur Azure Site Recovery zusammen. In den folgenden Artikeln werden spezielle Szenarien behandelt:

- [Fragen zur Notfallwiederherstellung von virtuellen Azure-Computern in Azure](azure-to-azure-common-questions.md)
- [Fragen zur Notfallwiederherstellung von virtuellen VMware-Computern in Azure](vmware-azure-common-questions.md)
- [Fragen zur Notfallwiederherstellung von virtuellen Hyper-V-Computern in Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Allgemein

### <a name="what-does-site-recovery-do"></a>Welche Funktion hat Site Recovery?

Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem die Replikation von virtuellen Azure-Computern zwischen Regionen, von lokalen virtuellen Computern und physischen Servern in Azure und von lokalen Computer in ein sekundäres Rechenzentrum orchestriert und automatisiert werden. [Weitere Informationen](site-recovery-overview.md)

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Kann ich einen virtuellen Computer mit Docker-Datenträger schützen?

Nein, dieses Szenario wird nicht unterstützt.

### <a name="what-does-site-recovery-do-to-ensure-data-integrity"></a>Wie stellt Site Recovery die Datenintegrität sicher?

Mit verschiedenen Maßnahmen stellt Site Recovery die Datenintegrität sicher. Zwischen allen Diensten wird mithilfe des HTTPS-Protokolls eine sichere Verbindung hergestellt. Dadurch wird sichergestellt, dass Malware oder externe Entitäten die Daten nicht manipulieren können. Eine weitere Maßnahme ist die Verwendung von Prüfsummen. Bei der Datenübertragung zwischen Quelle und Ziel werden Prüfsummen zwischen den Daten berechnet. So wird eine konsistente Datenübertragung sichergestellt.

## <a name="service-providers"></a>Dienstanbieter

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Ich bin ein Dienstanbieter. Ist Site Recovery für dedizierte und gemeinsam genutzte Infrastrukturmodelle geeignet?
Ja, Site Recovery unterstützt dedizierte und gemeinsam genutzte Infrastrukturmodelle.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Für einen Dienstanbieter wird die Identität meines Mandanten an den Site Recovery-Dienst weitergegeben?
Nein. Die Mandantenidentität bleibt anonym. Ihre Mandanten benötigen keinen Zugriff auf das Site Recovery-Portal. Nur der Administrator des Dienstanbieters interagiert mit dem Portal.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Werden die Anwendungsdaten der Mandanten jemals an Azure übermittelt?
Bei der Replikation zwischen den Standorten von Dienstanbietern werden die Anwendungsdaten niemals an Azure übermittelt. Die Daten werden während der Übertragung verschlüsselt und direkt zwischen den Standorten des Dienstanbieters repliziert.

Wenn Sie in Azure replizieren, werden Anwendungsdaten an den Azure-Speicher gesendet, jedoch nicht an den Site Recovery-Dienst. Daten werden während der Übertragung verschlüsselt und bleiben in Azure verschlüsselt.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Erhalten meine Mandanten eine Rechnung über Azure-Dienste?
Nein. Azure rechnet direkt mit dem Dienstanbieter ab. Mandantenspezifische Rechnungen müssen von den Dienstanbietern selbst erstellt werden.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Wenn ich in Azure repliziere, müssen wir dann ständig virtuelle Computer in Azure ausführen?
Nein. Die Daten werden in einem Azure-Speicherkonto Ihres Abonnements repliziert. Bei einem Testfailover (Notfallwiederherstellungsübung) oder einem tatsächlichen Failover erstellt Site Recovery automatisch virtuelle Computer in Ihrem Abonnement.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Stellen Sie die Isolierung auf Mandantenebene sicher, wenn ich in Azure repliziere?
Ja.

### <a name="what-platforms-do-you-currently-support"></a>Welche Plattformen werden derzeit unterstützt?
Wir unterstützen Bereitstellungen auf der Grundlage von Azure Pack, Cloud Platform System und System Center (2012 und höher). [Erfahren Sie mehr](/previous-versions/azure/windows-server-azure-pack/dn850370(v=technet.10)) zur Integration von Azure Pack und Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Werden einzelne Bereitstellungen von Azure Pack und VMM-Servern unterstützt?
Ja, Sie können virtuelle Hyper-V-Computer in Azure oder zwischen Dienstanbieterstandorten replizieren.  Beachten Sie, dass für die Replikation zwischen Dienstanbieterstandorten die Azure-Runbookintegration nicht verfügbar ist.

## <a name="pricing"></a>Preise

### <a name="where-can-i-find-pricing-information"></a>Wo finde ich Preisinformationen?
Sehen Sie sich die Details unter [Azure Site Recovery – Preise](https://azure.microsoft.com/pricing/details/site-recovery/) an.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Wie kann ich die ungefähren Kosten für die Nutzung von Site Recovery berechnen?

Sie können den [Preisrechner](https://aka.ms/asr_pricing_calculator) verwenden, um die Kosten für die Verwendung von Site Recovery zu schätzen.

Für eine detaillierte Kostenschätzung können Sie das Bereitstellungsplanertool für [VMware](./site-recovery-deployment-planner.md) oder [Hyper-V](https://aka.ms/asr-deployment-planner) ausführen und den [Kostenschätzungsbericht](./site-recovery-vmware-deployment-planner-cost-estimation.md) verwenden.


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Zum Replizieren von virtuellen VMware-Computern und physischen Servern werden jetzt verwaltete Datenträger verwendet. Fallen zusätzliche Gebühren für das Cachespeicherkonto mit verwalteten Datenträgern an?

Nein, für den Cache fallen keine zusätzlichen Gebühren an. Wenn Sie die Replikation in einem Standardspeicherkonto durchführen, ist dieser Cache Teil desselben Zielspeicherkontos.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Ich nutze Azure Site Recovery seit mehr als einem Monat. Sind die ersten 31 Tage für jede geschützte Instanz immer noch kostenlos?

Ja. Für jede geschützte Instanz fallen in den ersten 31 Tagen keine Azure Site Recovery-Gebühren an. Wenn Sie beispielsweise in den letzten sechs Monaten zehn Instanzen geschützt haben und eine elfte Instanz mit Azure Site Recovery verbinden, fallen in den ersten 31 Tagen keine Gebühren für die elfte Instanz an. Für die ersten zehn Instanzen werden weiterhin Azure Site Recovery-Gebühren berechnet, da sie seit mehr als 31 Tage geschützt werden.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Fallen während der ersten 31 Tage irgendwelche anderen Azure-Gebühren an?

Ja. Auch wenn Site Recovery während der ersten 31 Tage einer geschützten Instanz kostenlos ist, können für Azure Storage, Speichertransaktionen und Datenübertragungen Gebühren anfallen. Auch für einen wiederhergestellten virtuellen Computer können Azure-Computegebühren anfallen.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Entstehen Kosten für die Durchführung von Übungen zur Notfallwiederherstellung oder Testfailovern?

Es gibt keine separates Kosten für DR-Drills. Nach der Erstellung des virtuellen Computers nach dem Testfailover werden Computegebühren berechnet.



## <a name="security"></a>Sicherheit

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Werden Replikationsdaten an den Site Recovery-Dienst gesendet?
Nein. Site Recovery fängt replizierte Daten nicht ab und besitzt keine Informationen dazu, was auf Ihren virtuellen Computern oder physischen Servern ausgeführt wird.
Replikationsdaten werden zwischen lokalen Hyper-V-Hosts, VMware-Hypervisoren oder physischen Servern und Azure-Speicher an Ihrem sekundären Standort ausgetauscht. Site Recovery hat keine Möglichkeit, diese Daten abzufangen. Nur die Metadaten, die zum Orchestrieren von Replikation und Failover erforderlich sind, werden an den Site Recovery-Dienst gesendet.  

Site Recovery ist nach ISO 27001:2013, 27018, HIPAA und DPA zertifiziert und durchläuft gerade die Prüfungen für SOC2 und FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Aus Compliance-Gründen müssen sogar unsere lokalen Metadaten innerhalb derselben geografischen Region verbleiben. Kann Site Recovery uns helfen?
Ja. Durch die Erstellung eines Site Recovery-Tresors in einer Region wird sichergestellt, dass alle Metadaten, die wir zum Ermöglichen und Orchestrieren von Replikation und Failover benötigen, innerhalb der geografischen Grenzen dieser Region bleiben.

### <a name="does-site-recovery-encrypt-replication"></a>Verschlüsselt Site Recovery die Replikation?
Für virtuelle Computer und physische Server wird bei der Replikation zwischen lokalen Standorten die Verschlüsselung während der Übertragung unterstützt. Für virtuelle Computer und physische Server wird bei der Replikation in Azure sowohl die Verschlüsselung während der Übertragung als auch die [Verschlüsselung ruhender Daten (in Azure)](../storage/common/storage-service-encryption.md) unterstützt.

### <a name="does-azure-to-azure-site-recovery-use-tls-12-for-all-communications-across-microservices-of-azure"></a>Verwendet Azure-zu-Azure-Site Recovery für die gesamte Kommunikation zwischen den verschiedenen Microservices von Azure TLS 1.2?
Ja, das TLS 1.2-Protokoll wird standardmäßig für das Azure-zu-Azure-Site Recovery-Szenario erzwungen. 

### <a name="how-can-i-enforce-tls-12-on-vmware-to-azure-and-physical-server-to-azure-site-recovery-scenarios"></a>Wie erzwinge ich TLS 1.2 in VMware-zu-Azure- und Physischer-Server-zu-Azure-Site Recovery-Szenarien?
Auf den replizierten Elementen installierte Mobility-Agents kommunizieren ausschließlich über TLS 1.2 mit dem Prozessserver. Die Kommunikation zwischen dem Konfigurationsserver und Azure sowie zwischen dem Prozessserver und Azure kann jedoch über TLS 1.1 oder 1.0 erfolgen. Befolgen Sie die [Anweisungen](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) zum Erzwingen von TLS 1.2 für alle von Ihnen eingerichteten Konfigurations- und Prozessserver.

### <a name="how-can-i-enforce-tls-12-on-hyperv-to-azure-site-recovery-scenarios"></a>Wie kann ich TLS 1.2 für HyperV-zu-Azure-Site Recovery-Szenarien erzwingen?
Die gesamte Kommunikation zwischen den Microservices von Azure Site Recovery erfolgt über das TLS 1.2-Protokoll. Site Recovery verwendet Sicherheitsanbieter, die im System (BS) konfiguriert sind, und verwendet das neueste verfügbare TLS-Protokoll. Sie müssen TLS 1.2 explizit in der Registrierung aktivieren, und dann wird Site Recovery mit der Verwendung von TLS 1.2 zur Kommunikation mit Diensten beginnen. 

### <a name="how-can-i-enforce-restricted-access-on-my-storage-accounts-which-are-accessed-by-site-recovery-service-for-readingwriting-replication-data"></a>Wie erzwinge ich eingeschränkten Zugriff auf meine Speicherkonten, auf die der Site Recovery-Dienst zum Lesen/Schreiben von Replikationsdaten zugreift?
Sie können die verwaltete Identität des Recovery Services-Tresors aktivieren, indem Sie zur *Identity*-Einstellung (Identität) wechseln. Nachdem der Tresor bei Azure Active Directory registriert wurde, können Sie zu Ihren Speicherkonten wechseln und den Tresor die folgenden Rollenzuweisungen zuweisen:

- Resource Manager-basierte Speicherkonten (Standard):
  - [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor)
  - [Mitwirkender an Speicherblobdaten](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager-basierte Speicherkonten (Premium):
  - [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor)
  - [Besitzer von Speicherblobdaten](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Klassische Speicherkonten:
  - [Mitwirkender von klassischem Speicherkonto](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Klassische Dienstrolle „Speicherkonto-Schlüsseloperator“](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

## <a name="disaster-recovery"></a>Notfallwiederherstellung

### <a name="what-can-site-recovery-protect"></a>Was kann mit Site Recovery geschützt werden?
* **Virtuelle Azure-Computer**: Mit Site Recovery können alle Workloads repliziert werden, die auf einem unterstützten virtuellen Azure-Computer ausgeführt werden.
* **Virtuelle Hyper-V-Computer**: Mit Site Recovery kann jede Workload, die auf einer Hyper-V-VM ausgeführt wird, geschützt werden.
* **Physische Server**: Mit Site Recovery können physische Server unter Windows oder Linux geschützt werden.
* **Virtuelle VMware-Computer**: Mit Site Recovery kann jede Workload, die auf einer VMware-VM ausgeführt wird, geschützt werden.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Welche Workloads können mit Site Recovery geschützt werden?
Mit Site Recovery können die meisten Workloads geschützt werden, die auf einer unterstützten VM oder einem physischen Server ausgeführt werden. Site Recovery bietet Unterstützung für die anwendungsorientierte Replikation, sodass für Apps ein „intelligenter Zustand“ wiederhergestellt werden kann. Eine Integration in Microsoft-Anwendungen wie SharePoint, Exchange, Dynamics, SQL Server und Active Directory ist möglich. Zudem kann Site Recovery eng in die Produkte führender Anbieter, z.B. Oracle, SAP, IBM und Red Hat, eingebunden werden. [Erfahren Sie mehr](site-recovery-workload.md) über den Schutz von Workloads.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Kann ich mit Site Recovery die Notfallwiederherstellung für meine Zweigstellen verwalten?
Ja. Wenn Sie Site Recovery zum Orchestrieren von Replikation und Failover in Zweigstellen verwenden, erhalten Sie eine einheitliche Orchestrierung und Anzeige der Workloads aller Zweigstellen an einer zentralen Stelle. Sie können problemlos über Ihren Hauptsitz für alle Zweigstellen Failover ausführen und die Notfallwiederherstellung verwalten, ohne die Zweigstellen zu besuchen.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Wird die Notfallwiederherstellung für virtuelle Azure-Computer unterstützt?

Ja, Site Recovery unterstützt die Notfallwiederherstellung für virtuelle Azure-Computer zwischen Azure-Regionen. [Lesen Sie häufig gestellte Fragen](azure-to-azure-common-questions.md) zur Notfallwiederherstellung von virtuellen Azure-Computern. Wenn Sie zwischen zwei Azure-Regionen auf demselben Kontinent replizieren möchten, verwenden Sie das Angebot für die Notfallwiederherstellung von Azure zu Azure. Sie müssen weder einen Konfigurationsserver/Prozessserver noch ExpressRoute-Verbindungen einrichten.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Wird die Notfallwiederherstellung für virtuelle VMware-Computer unterstützt?

Ja, Site Recovery unterstützt die Notfallwiederherstellung von lokalen virtuellen VMware-Computern. [Lesen Sie häufig gestellte Fragen](vmware-azure-common-questions.md) zur Notfallwiederherstellung von virtuellen VMware-Computern.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Wird die Notfallwiederherstellung für virtuelle Hyper-V-Computer unterstützt?
Ja, Site Recovery unterstützt die Notfallwiederherstellung von lokalen virtuellen Hyper-V-Computern. [Lesen Sie häufig gestellte Fragen](hyper-v-azure-common-questions.md) zur Notfallwiederherstellung von virtuellen Hyper-V-Computern.

### <a name="is-disaster-recovery-supported-for-physical-servers"></a>Wird die Notfallwiederherstellung für physische Server unterstützt?
Ja, Site Recovery unterstützt die Notfallwiederherstellung von lokalen physischen Servern unter Windows und Linux in Azure oder an einem sekundären Standort. Erfahren Sie mehr über die Anforderungen für die Notfallwiederherstellung in [Azure](vmware-physical-azure-support-matrix.md#replicated-machines) und an [einem sekundären Standort](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Beachten Sie, dass physische Server nach einem Failover als virtuelle Computer in Azure ausgeführt werden. Das Failback von Azure auf einen lokalen physischen Server wird derzeit nicht unterstützt. Ein Failback kann nur auf einen virtuellen VMware-Computer ausgeführt werden.





## <a name="replication"></a>Replikation

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Kann ich über ein Standort-zu-Standort-VPN zu Azure replizieren?
Azure Site Recovery repliziert Daten über einen öffentlichen Endpunkt in ein Azure Storage-Konto oder verwaltete Datenträger. Replikation erfolgt nicht über ein Site-to-Site-VPN. 

### <a name="why-cant-i-replicate-over-vpn"></a>Warum kann ich nicht über VPN replizieren?

Wenn Sie nach Azure replizieren, erreicht der Replikationsdatenverkehr die öffentlichen Endpunkte einer Azure Storage-Instanz. Daher kann die Replikation nur über das öffentliche Internet oder ExpressRoute (Microsoft-Peering oder bestehendes öffentliches Peering) erfolgen.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Kann ich Riverbed SteelHeads für die Replikation verwenden?

Unser Partner Riverbed bietet eine detaillierte Anleitung zum Arbeiten mit Azure Site Recovery. Lesen Sie den entsprechenden [Lösungsleitfaden](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Kann ich virtuelle Computer mithilfe von ExpressRoute zu Azure replizieren?
Ja, [ExpressRoute kann zum Replizieren virtueller Computer zu Azure verwendet werden](concepts-expressroute-with-site-recovery.md).

- Azure Site Recovery repliziert Daten über einen öffentlichen Endpunkt in ein Azure Storage-Konto. Wenn Sie ExpressRoute für die Site Recovery-Replikation verwenden möchten, müssen Sie [Microsoft-Peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) oder ein vorhandenes [öffentliches Peering](../expressroute/about-public-peering.md) (für neue Verbindungen veraltet) einrichten.
- Microsoft-Peering ist die empfohlene Routingdomäne für die Replikation.
- Replikation über privates Peering wird nicht unterstützt.
- Falls Sie VMware-Computer oder physische Computer schützen, stellen Sie sicher, dass die [Netzwerkanforderungen](vmware-azure-configuration-server-requirements.md#network-requirements) auch für den Konfigurationsserver erfüllt sind. Der Konfigurationsserver muss für die Orchestrierung der Site Recovery-Replikation eine Verbindung mit bestimmten URLs herstellen können. Für diese Verbindung kann ExpressRoute nicht verwendet werden.
- Nachdem für die virtuellen Computer ein Failover auf ein virtuelles Azure-Netzwerk ausgeführt wurde, können Sie mithilfe der Einrichtung des [privaten Peering](../expressroute/expressroute-circuit-peerings.md#privatepeering) für das virtuelle Azure-Netzwerk darauf zugreifen.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Wenn ich in Azure repliziere, welche Art von Speicherkonto oder verwaltetem Datenträger benötige ich?

Die Verwendung von Speicherkonten als Zielspeicher wird von Azure Site Recovery nicht unterstützt. Es wird empfohlen, stattdessen verwaltete Datenträger als Zielspeicher für Ihre Computer zu verwenden. Verwaltete Datenträger unterstützen nur den LRS-Typ für die Datenresilienz.

### <a name="how-often-can-i-replicate-data"></a>Wie oft kann ich Daten replizieren?
* **Hyper-V:** Hyper-V-VMs können alle 30 Sekunden (außer bei Storage Premium), alle fünf Minuten oder alle 15 Minuten repliziert werden.
* **Virtuelle Azure-Computer, virtuelle VMware-Computer und physische Server:** Hier ist eine Replikationshäufigkeit irrelevant. Die Replikation ist fortlaufend.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Kann die Replikation von vorhandenen Wiederherstellungsstandorten auf einen weiteren tertiären Standort erweitert werden?
Eine erweiterte oder verkettete Replikation wird nicht unterstützt. Fordern Sie dieses Feature im [Feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Kann ich eine Offlinereplikation durchführen, wenn ich zum ersten Mal in Azure repliziere?
Dies wird nicht unterstützt. Fordern Sie dieses Feature im [Feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Können bestimmte Datenträger von der Replikation ausgeschlossen werden?
Dies wird beim Replizieren von virtuellen VMware-Computern und virtuellen Hyper-V-Computern nach Azure über das Azure-Portal unterstützt.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Können virtuelle Computer mit dynamischen Datenträgern repliziert werden?
Dynamische Datenträger werden beim Replizieren virtueller Hyper-V-Computer und beim Replizieren virtueller VMware-Computer und physischer Computer in Azure unterstützt. Der Betriebssystem-Datenträger muss ein Basisdatenträger sein.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Kann ich die Bandbreite drosseln, die für den Replikationsdatenverkehr zugeordnet ist?
Ja. In folgenden Artikeln erfahren Sie mehr über die Drosselung der Bandbreite:

* [Kapazitätsplanung für die Replikation von VMware-VMs und physischen Servern](site-recovery-plan-capacity-vmware.md)
* [Kapazitätsplanung für die Replikation von Hyper-V-VMs in Azure](./hyper-v-deployment-planner-overview.md)

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>Kann ich die Replikation mit App-Konsistenz auf Linux-Servern aktivieren? 
Ja. Azure Site Recovery für Linux-Betriebssysteme unterstützt benutzerdefinierte Anwendungsskripts für App-Konsistenz. Das benutzerdefinierte Skript mit den „Pre“- und „Post“-Optionen wird vom Mobilitäts-Agent von Azure Site Recovery während des Vorgangs für die App-Konsistenz verwendet. Dies kann mithilfe folgender Schritte ermöglicht werden.

1. Melden Sie sich beim Computer als Root-Benutzer an.
2. Wechseln Sie in das Installationsverzeichnis des Mobilitäts-Agent von Azure Site Recovery. Der Standardwert ist „/usr/local/ASR“.<br>
    `# cd /usr/local/ASR`
3. Wechseln Sie im Installationsverzeichnis in „VX/Scripts“.<br>
    `# cd VX/scripts`
4. Erstellen Sie ein Bash-Shellskript mit dem Namen „customscript.sh“ mit Ausführungsberechtigungen für den Root-Benutzer.<br>
    a. Das Skript sollte die Befehlszeilenoptionen „--pre“ und „--post“ (beachten Sie die doppelten Bindestriche) unterstützen.<br>
    b. Wenn das Skript mit der „--pre“-Option aufgerufen wird, sollte es die Eingabe/Ausgabe der Anwendung einfrieren, und wenn es mit der „--post“-Option aufgerufen wird, sollte es die Eingabe/Ausgabe der Anwendung reaktivieren.<br>
    c. Eine Beispielvorlage –<br>

    `# cat customscript.sh`<br>

```
    #!/bin/bash

    if [ $# -ne 1 ]; then
        echo "Usage: $0 [--pre | --post]"
        exit 1
    elif [ "$1" == "--pre" ]; then
        echo "Freezing app IO"
        exit 0
    elif [ "$1" == "--post" ]; then
        echo "Thawed app IO"
        exit 0
    fi
```

5. Fügen Sie die Eingabe-/Ausgabebefehle zum Einfrieren und Reaktivieren in „--pre“- und „--post“-Schritten für Anwendungen hinzu, die App-Konsistenz erfordern. Sie können wahlweise ein weiteres Skript hinzufügen, das diese Befehle angibt, und von „customscript.sh“ aus mit „--pre“- und „--post“-Option aufrufen.

>[!Note]
>Zur Unterstützung von benutzerdefinierten Skripts sollte die Version des Site Recovery-Agents 9.24 oder höher sein.

## <a name="replication-policy"></a>Replikationsrichtlinie

### <a name="what-is-a-replication-policy"></a>Was ist eine Replikationsrichtlinie?

Eine Replikationsrichtlinie definiert die Einstellungen für den Aufbewahrungsverlauf von Wiederherstellungspunkten. Die Richtlinie definiert auch die Häufigkeit anwendungskonsistenter Momentaufnahmen. Standardmäßig erstellt Azure Site Recovery eine neue Replikationsrichtlinie mit folgenden Standardeinstellungen:

- 24 Stunden für den Aufbewahrungsverlauf von Wiederherstellungspunkten.
- 4 Stunden für die Häufigkeit anwendungskonsistenter Momentaufnahmen.

### <a name="what-is-a-crash-consistent-recovery-point"></a>Was ist ein absturzkonsistenter Wiederherstellungspunkt?

Ein absturzkonsistenter Wiederherstellungspunkt enthält die Daten auf dem Datenträger, als ob Sie das Netzkabel während der Momentaufnahme vom Server abgezogen hätten. Der absturzkonsistente Wiederherstellungspunkt enthält nichts, was sich zum Zeitpunkt der Momentaufnahme im Arbeitsspeicher befand.

Heutzutage können die meisten Anwendungen aus absturzkonsistenten Momentaufnahmen gut wiederhergestellt werden. Ein absturzkonsistenter Wiederherstellungspunkt ist normalerweise ausreichend für Betriebssysteme ohne Datenbank und Anwendungen wie Dateiserver, DHCP-Server und Druckerserver.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Mit welcher Häufigkeit wird ein absturzkonsistenter Wiederherstellungspunkt generiert?

Site Recovery erstellt alle 5 Minuten einen absturzkonsistenten Wiederherstellungspunkt.

### <a name="what-is-an-application-consistent-recovery-point"></a>Was ist ein anwendungskonsistenter Wiederherstellungspunkt?

Anwendungskonsistente Wiederherstellungspunkte werden aus anwendungskonsistenten Momentaufnahmen erstellt. Anwendungskonsistente Wiederherstellungspunkte erfassen dieselben Daten wie absturzkonsistente Momentaufnahmen. Zudem werden alle Daten im Arbeitsspeicher und alle laufenden Transaktionen erfasst.

Durch diesen zusätzlichen Inhalt sind anwendungskonsistente Momentaufnahmen am stärksten involviert, und ihre Ausführung dauert am längsten. Anwendungskonsistente Wiederherstellungspunkte werden für Betriebssysteme mit Datenbank und Anwendungen wie SQL Server empfohlen.

>[!Note]
>Das Erstellen von anwendungskonsistenten Wiederherstellungspunkten führt auf einem Windows-Computer zu einem Fehler, wenn er mehr als 64 Volumes enthält.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Welche Auswirkungen haben anwendungskonsistente Wiederherstellungspunkte auf die Anwendungsleistung?

Anwendungskonsistente Wiederherstellungspunkte erfassen alle Daten im Arbeitsspeicher und in Prozessen. Da Wiederherstellungspunkte diese Daten erfassen, benötigen Sie ein Framework wie den Volumeschattenkopie-Dienst unter Windows, um die Anwendung in einen inaktiven Status zu versetzen. Wird der Erfassungsprozess häufig ausgeführt, kann sich dies auf die Leistung auswirken, wenn die Workload bereits hoch ist. Wir raten davon ab, für anwendungskonsistente Wiederherstellungspunkte bei Nicht-Datenbankworkloads ein kleines Intervall zu verwenden. Auch für die Datenbankworkloads reicht eine Stunde aus.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Mit welcher Mindesthäufigkeit wird ein anwendungskonsistenter Wiederherstellungspunkt generiert?

Site Recovery kann einen anwendungskonsistenten Wiederherstellungspunkt mit einer Mindesthäufigkeit von einmal pro Stunde erstellen.

### <a name="how-are-recovery-points-generated-and-saved"></a>Wie werden Wiederherstellungspunkte generiert und gespeichert?

Sehen Sie sich das folgende Beispiel für eine Replikationsrichtlinie an, um zu verstehen, wie Site Recovery Wiederherstellungspunkte generiert. Diese Replikationsrichtlinie verfügt über einen Wiederherstellungspunkt mit einem Aufbewahrungszeitfenster von 24 Stunden und einem Intervall von 1 Stunde für anwendungskonsistente Momentaufnahmen.

Site Recovery erstellt alle 5 Minuten einen absturzkonsistenten Wiederherstellungspunkt. Der Benutzer kann diese Häufigkeit nicht ändern. Für die letzte Stunde können Sie zwischen 12 absturzkonsistenten und einem App-konsistenten Punkt wählen. Im Lauf der Zeit löscht Site Recovery alle Wiederherstellungspunkte, die älter als 1 Stunde sind, und speichert nur einen Wiederherstellungspunkt pro Stunde.

Der folgende Screenshot veranschaulicht dieses Beispiel. Im Screenshot:

- Innerhalb der letzten Stunde sind Wiederherstellungspunkte im Intervall von 5 Minuten vorhanden.
- Für den Zeitraum vor der letzten Stunde bewahrt Site Recovery nur einen Wiederherstellungspunkt auf.

   ![Liste der generierten Wiederherstellungspunkte](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Wie weit kann ich bei der Wiederherstellung zurückgehen?

Der älteste Wiederherstellungspunkt, den Sie verwenden können, ist 72 Stunden alt.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Ich verfüge über eine Replikationsrichtlinie von 24 Stunden. Was passiert, wenn Site Recovery aufgrund eines Problems für mehr als 24 Stunden keinen Wiederherstellungspunkt generiert hat? Gehen meine vorherigen Wiederherstellungspunkte verloren?

Nein. Site Recovery bewahrt alle Ihre vorherigen Wiederherstellungspunkte auf. Abhängig vom Aufbewahrungszeitfenster für Wiederherstellungspunkte ersetzt Site Recovery den ältesten Punkt nur, wenn er neue Punkte generiert. Aufgrund des Problems kann Site Recovery keine neuen Wiederherstellungspunkte generieren. Bis neue Wiederherstellungspunkte vorhanden sind, bleiben alle alten Punkte erhalten, auch wenn das Aufbewahrungszeitfenster erreicht ist.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Wie ändere ich die Replikationsrichtlinie, nachdem die Replikation auf einer VM aktiviert wurde?

Navigieren Sie zu **Site Recovery-Tresor** > **Site Recovery-Infrastruktur** > **Replikationsrichtlinien**. Wählen Sie die Richtlinie aus, die Sie bearbeiten möchten, und speichern Sie die Änderungen. Alle Änderungen werden auch auf alle vorhandenen Replikationen angewendet.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Sind alle Wiederherstellungspunkte vollständige Kopien des virtuellen Computers oder gibt es Unterschiede?

Der erste Wiederherstellungspunkt, der generiert wird, ist eine vollständige Kopie. Alle nachfolgenden Wiederherstellungspunkte enthalten nur die geänderten Daten (Deltaänderungen).

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Erhöhen sich durch eine längere Aufbewahrungsdauer der Wiederherstellungspunkte die Speicherkosten?

Ja, wenn Sie die Aufbewahrungsdauer von 24 auf 72 Stunden erhöhen, speichert Site Recovery die Wiederherstellungspunkte für zusätzliche 48 Stunden. Durch die zusätzliche Zeit fallen Speicherkosten an. Beispiel: Ein einzelner Wiederherstellungspunkt weist Deltaänderungen von 10 GB auf, bei pro-GB-Kosten von $0,16 pro Monat. Die zusätzlichen Kosten betragen $1,60 × 48 pro Monat.


## <a name="failover"></a>Failover
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Wie greife ich nach einem Failover in Azure auf die virtuellen Azure-Computer zu?

Sie können auf die Azure-VMs über eine sichere Internetverbindung, über eine Site-to-Site-VPN-Verbindung oder über Azure ExpressRoute zugreifen. Sie müssen eine Reihe von Vorbereitungen treffen, um eine Verbindung herzustellen. [Weitere Informationen](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Wie wird bei einem Failover auf Azure in Azure sichergestellt, dass die Daten stabil sind?
Azure ist auf Resilienz ausgelegt. Site Recovery ist bereits für ein Failover zu einem sekundären Azure-Rechenzentrum (gemäß Azure-SLA) konzipiert. Wenn dies der Fall ist, stellen wir sicher, dass Ihre Metadaten und Tresore innerhalb der gleichen geografischen Region bleiben, die Sie für Ihren Tresor ausgewählt haben.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Wenn ich eine Replikation zwischen zwei Rechenzentren ausführe, was geschieht, wenn in meinem primären Rechenzentrum ein unerwarteter Ausfall auftritt?
Sie können ein ungeplantes Failover vom sekundären Standort auslösen. Site Recovery benötigt keine Verbindung mit dem primären Standort, um das Failover auszuführen.

### <a name="is-failover-automatic"></a>Erfolgt ein Failover automatisch?
Ein Failover erfolgt nicht automatisch. Sie initiieren Failover mit einem Mausklick im Portal, oder Sie können die [Site Recovery-PowerShell](/powershell/module/az.recoveryservices) verwenden, um ein Failover auslösen. Ein Failback ist eine einfache Aktion im Site Recovery-Portal.

Zum Automatisieren können Sie den lokalen Orchestrator oder Operations Manager verwenden, um einen Fehler bei virtuellen Computern zu erkennen und dann das Failover mithilfe des SDK auszulösen.

* [Weitere Informationen](site-recovery-create-recovery-plans.md) .
* [Weitere Informationen](site-recovery-failover.md) .
* [Weitere Informationen](./vmware-azure-failback.md) .

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Angenommen, mein lokaler Host reagiert nicht mehr oder ist abgestürzt. Kann ein Failback auf einen anderen Host ausgeführt werden?
Ja, Sie können über die Wiederherstellung an einem alternativen Speicherort ein Failback auf einen anderen Host als Azure ausführen.

* [Für virtuelle VMware-Computer](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Für virtuelle Hyper-V-Computer](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

### <a name="what-is-the-difference-between-complete-migration-commit-and-disable-replication"></a>Worin besteht der Unterschied zwischen „Migration abschließen“, „Commit“ und „Replikation deaktivieren“?

Wenn ein Failover eines Computer vom Quellspeicherort zum Zielspeicherort ausgeführt wurde, stehen Ihnen drei Optionen zur Auswahl. Alle drei dienen unterschiedlichen Zwecken:

1.  **Migration abschließen** bedeutet, dass Sie nicht mehr zum Quellspeicherort zurückkehren. Sie haben zu der Zielregion migriert und sind nun fertig. Durch Klicken auf „Migration abschließen“ werden intern „Commit“ und dann „Replikation deaktivieren“ ausgelöst. 
2.  **Commit** bedeutet, dass es sich hierbei nicht um das Ende des Replikationsprozesses handelt. Das Replikationselement und die gesamte Konfiguration bleiben erhalten, und Sie können zu einem späteren Zeitpunkt **Erneut schützen** wählen, um die Replikation ihrer Computer zurück in die Quellregion zu ermöglichen. 
3.  **Replikation deaktivieren** deaktiviert die Replikation und entfernt alle zugehörigen Konfigurationen. Dies wirkt sich nicht auf den bereits vorhandenen Computer in der Zielregion aus.

## <a name="automation"></a>Automation

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Kann ich Site Recovery-Szenarien mit einem SDK automatisieren?
Ja. Sie können Site Recovery-Workflows mithilfe der REST-API, PowerShell oder Azure SDK automatisieren. Derzeit unterstützte Szenarien für die Bereitstellung von Site Recovery mit PowerShell:

* [Replizieren von Hyper-V-VMs in VMM-Clouds im Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Replizieren von Hyper-V-VMs ohne VMM in Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Replizieren von VMware in Azure mit PowerShell Resource Manager](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Upgrade von Komponenten und Anbietern

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Wo finde ich die Versionshinweise und Updaterollups von Site Recovery-Upgrades?

[Hier](site-recovery-whats-new.md) finden Sie Informationen zu neuen Updates und [hier](service-updates-how-to.md) zum Rollup.

## <a name="next-steps"></a>Nächste Schritte
* Lesen Sie die [Site Recovery-Übersicht](site-recovery-overview.md)