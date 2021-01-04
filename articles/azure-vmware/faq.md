---
title: Häufig gestellte Fragen
description: Hier finden Sie Antworten auf einige der häufig gestellten Fragen zu Azure VMware Solution.
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: dikamath
ms.openlocfilehash: 3033df2021a8d1780caf9b0b7cd4dbe8de2a6050
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861407"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution"></a>Häufig gestellte Fragen zu Azure VMware Solution

In diesem Artikel finden Sie häufig gestellte Fragen zu Azure VMware Solution.

## <a name="general"></a>Allgemein

#### <a name="what-is-azure-vmware-solution"></a>Was ist Azure VMware Solution?

Im Zuge der IT-Modernisierungsstrategien von Unternehmen zur Verbesserung der Geschäftsflexibilität, Kostensenkung und Innovationsbeschleunigung haben sich hybride Cloudplattformen als Schlüsselfaktoren für die digitale Transformation von Kunden erwiesen. Azure VMware Solution kombiniert die SDDC-Software (Software Defined Data Center) von VMware mit dem globalen Clouddienstökosystem von Microsoft Azure. Azure VMware Solution wird verwaltet, um die Anforderungen an Leistung, Verfügbarkeit, Sicherheit und Compliance zu erfüllen.

## <a name="azure-vmware-solution-service"></a>Azure VMware Solution-Dienst

#### <a name="where-is-azure-vmware-solution-available-today"></a>Wo ist Azure VMware Solution aktuell verfügbar?

Der Dienst wird fortlaufend neuen Regionen hinzugefügt. Details finden Sie auf der Seite mit den [neuesten Informationen zur Dienstverfügbarkeit](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware). 

#### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>Können Workloads, die in einer Azure VMware Solution-Instanz ausgeführt werden, Azure-Dienste nutzen oder in diese integriert werden?

Alle Azure-Dienste stehen Azure VMware Solution-Kunden zur Verfügung. Leistungs- und Verfügbarkeitseinschränkungen für bestimmte Dienste müssen von Fall zu Fall behandelt werden.

#### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>Verwende ich die gleichen Tools, die ich jetzt zum Verwalten von privaten Cloudressourcen verwende?

Ja. Das Azure-Portal wird für die Bereitstellung und einige Verwaltungsvorgänge verwendet. vCenter und NSX Manager werden zum Verwalten von vSphere- und NSX-T-Ressourcen verwendet.

#### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>Kann ich eine private Cloud mit meiner lokalen vCenter-Instanz verwalten?

Beim Start unterstützt Azure VMware Solution keine einzelne Verwaltungsumgebung für lokale und private Cloudumgebungen. Cluster der privaten Cloud werden mit vCenter und NSX Manager lokal für eine private Cloud verwaltet.

#### <a name="can-i-use-vrealize-suite-running-on-premises"></a>Kann ich eine lokal ausgeführte vRealize-Suite verwenden? 

Bestimmte Integrationen und Anwendungsfälle können von Fall zu Fall ausgewertet werden.

#### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>Kann ich vSphere-VMs aus lokalen Umgebungen zu privaten Azure VMware Solution-Clouds migrieren?

Ja. Die VM-Migration und vMotion können verwendet werden, um VMs in eine private Cloud zu verschieben, wenn die standardmäßigen vCenter-übergreifenden [vMotion-Anforderungen](https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=2106952) erfüllt sind.

#### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>Ist eine bestimmte Version von vSphere in lokalen Umgebungen erforderlich?

Alle Cloudumgebungen stellen VMware HCX und vSphere 5.5 oder höher in lokalen Umgebungen für vMotion bereit.

#### <a name="what-does-the-change-control-process-look-like"></a>Wie sieht der Änderungssteuerungsvorgang aus?

Updates, die am Dienst selbst vorgenommen werden, folgen dem Change Management-Standardprozess von Microsoft Azure. Kunden sind für alle Workloadverwaltungsaufgaben und die zugehörigen Change Management-Prozesse verantwortlich.

#### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>Wie unterscheidet sich dies von Azure VMware Solution by CloudSimple?

Mit dem neuen Dienst Azure VMware Solution verfügen Microsoft und VMware über eine direkte Cloudanbieterpartnerschaft. Es handelt sich dabei um eine vollständig von Microsoft entworfene, erstellte und unterstützte Lösung, die von VMware unterstützt wird. Die Architektur der beiden Lösungen ist konsistent. Bei beiden wird der VMware-Technologiestapel in einer dedizierten Azure-Infrastruktur ausgeführt.



#### <a name="can-azure-vmware-solution-vms-be-managed-by-vmrc"></a>Können virtuelle Computer von Azure VMware Solution durch VMRC verwaltet werden?
Ja, sofern das System, auf dem es installiert ist, auf vCenter für die private Cloud zugreifen kann und es ein öffentliches DNS verwendet, um ESXi-Hostnamen aufzulösen.

#### <a name="are-there-special-instructions-for-installing-and-using-vmrc-with-azure-vmware-solution-vms"></a>Gibt es spezielle Anweisungen für die Installation und Verwendung von VMRC mit virtuellen Azure VMware Solution-Computern (VMs)?
Nein. Verwenden Sie die von [VMware bereitgestellten Anweisungen](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-89E7E8F0-DB2B-437F-8F70-BA34C505053F.html), um die in diesen Anweisungen angegebenen VM-Voraussetzungen zu erfüllen. 

#### <a name="is-vmware-hcx-supported-on-vpns"></a>Wird VMware HCX auf VPNs unterstützt?
Nein, aufgrund der Anforderungen an Bandbreite und Wartezeit.

#### <a name="can-azure-bastion-be-used-for-connecting-to-azure-vmware-solution-vms"></a>Kann Azure Bastion zum Herstellen einer Verbindung mit Azure VMware Solution-VMs verwendet werden?
Azure Bastion ist der Dienst, der empfohlen wird, um eine Verbindung mit der Jumpbox herzustellen, damit die Azure VMware Solution-Instanz nicht im Internet veröffentlicht wird. Sie können Azure Bastion nicht verwenden, um eine Verbindung mit Azure VMware Solution-VMs herzustellen, da es sich hierbei nicht um Azure-IaaS-Objekte handelt.

#### <a name="can-azure-load-balancer-internal-be-used-for-azure-vmware-solution-vms"></a>Kann Azure Load Balancer intern für VMs der Azure VMware Solution-VMs verwendet werden?
Nein. Azure Load Balancer unterstützt intern nur Azure-IaaS-VMs. Azure Load Balancer unterstützt keine auf der IP-Adresse basierenden Back-End-Pools, sondern nur Azure-VMs oder VM-Skalierungsgruppenobjekte, bei denen Azure VMware Solution-VMs keine Azure-Objekte sind.

#### <a name="can-an-existing-expressroute-gateway-be-used-to-connect-to-azure-vmware-solution"></a>Kann ein vorhandenes ExpressRoute Gateway verwendet werden, um eine Verbindung mit Azure VMware Solution herzustellen?
Ja, Sie können ein vorhandenes ExpressRoute-Gateway verwenden, um eine Verbindung mit Azure VMware Solution herzustellen, sofern der Grenzwert von vier ExpressRoute-Verbindungen pro virtuellem Netzwerk nicht überschritten wird.  Für den lokalen Zugriff auf Azure VMware Solution über ExpressRoute benötigen Sie ExpressRoute Global Reach, da das ExpressRoute-Gateway kein transitives Routing zwischen den Verbindungen unterstützt.

## <a name="compute-network-storage-and-backup"></a>Compute, Netzwerk, Speicher und Sicherung

#### <a name="is-there-more-than-one-type-of-host-available"></a>Sind mehrere Hosttypen verfügbar?

Es ist nur ein Hosttyp verfügbar.

#### <a name="what-are-the-cpu-specifications-in-each-type-of-host"></a>Wie lauten die CPU-Spezifikationen in den einzelnen Hosttypen?

Die Server verfügen über Intel Dual-CPUs mit 18 Kernen mit 2,3 GHz.

#### <a name="how-much-memory-is-in-each-host"></a>Wie viel Arbeitsspeicher ist auf jedem Host vorhanden?

Die Server verfügen über 576 GB RAM.

#### <a name="what-is-the-storage-capacity-of-each-host"></a>Wie groß ist die Speicherkapazität jedes Hosts?

Jeder ESXi-Host verfügt über zwei vSAN-Datenträgergruppen mit einer Kapazitätsebene von 15,2 TB und einer NVMe-Cacheebene von 3,2 TB (1,6 TB in jeder Datenträgergruppe).

#### <a name="how-much-network-bandwidth-is-available-in-each-esxi-host"></a>Wie viel Netzwerkbandbreite ist in jedem ESXi-Host verfügbar?

Jeder ESXi-Host in Azure VMware Solution ist mit vier Netzwerkadaptern mit 25 GBit/s konfiguriert. Dabei werden zwei Netzwerkadapter für ESXi-Systemdatenverkehr und zwei Netzwerkadapter für Workloaddatenverkehr bereitgestellt. 

#### <a name="is-data-stored-on-the-vsan-datastores-encrypted-at-rest"></a>Werden Daten, die in den vSAN-Datenspeichern gespeichert sind, im Ruhezustand verschlüsselt?

Ja, alle vSAN-Daten werden standardmäßig mithilfe der in Azure Key Vault gespeicherten Schlüssel verschlüsselt.

#### <a name="you-document-that-commvault-veritas-and-veeam-have-extended-their-backup-solutions-to-work-with-azure-vmware-solution-what-about-other-independent-software-vendors-isvs-backup-solutions"></a>Sie dokumentieren, dass Commvault, Veritas und Veeam ihre Sicherungslösungen erweitert haben, um mit Azure VMware Solution zu arbeiten. Was ist mit anderen Sicherungslösungen unabhängiger Softwareanbieter (ISVs)?

Soweit uns bekannt ist, sollte jede Sicherungslösung, die VMware VADP mit dem HotAdd-Transportmodus verwendet, sofort mit Azure VMware Solution funktionieren.

#### <a name="what-about-support-for-isv-backup-solutions"></a>Was ist mit der Unterstützung für ISV-Sicherungslösungen?

Da diese Sicherungslösungen von Kunden installiert und verwaltet werden, können sie sich an den jeweiligen unabhängigen Softwareanbieter wenden, um Unterstützung zu erhalten. 

#### <a name="what-is-the-correct-storage-policy-for-the-dedupe-setup"></a>Welche ist die richtige Speicherrichtlinie für die Einrichtung der Deduplizierung?

Verwenden Sie die Richtlinie *thin_provision* für die Speicherung Ihrer VM-Vorlage.  Der Standardwert ist *thick_provision*.

#### <a name="are-the-snmp-infrastructure-logs-shared"></a>Werden die Protokolle der SNMP-Infrastruktur freigegeben?

Nein.

## <a name="hosts-clusters-and-private-clouds"></a>Hosts, Cluster und private Clouds

#### <a name="is-the-underlying-infrastructure-shared"></a>Wird die zugrunde liegende Infrastruktur gemeinsam verwendet?

Nein, private Cloudhosts und Cluster sind dediziert und werden vor und nach der Verwendung sicher gelöscht.

#### <a name="what-are-the-minimum-and-the-maximum-number-of-hosts-per-cluster"></a>Wie hoch sind die minimale und die maximale Anzahl von Hosts pro Cluster?

Cluster können zwischen 3 und 16 ESXi-Hosts skaliert werden. Testcluster sind auf drei Hosts beschränkt.

#### <a name="can-i-scale-my-private-cloud-clusters"></a>Kann ich die Cluster meiner privaten Cloud skalieren?

Ja, Cluster werden zwischen der minimalen und der maximalen Anzahl von ESXi-Hosts skaliert. Testcluster sind auf drei Hosts beschränkt.

#### <a name="what-are-trial-clusters"></a>Was sind Testcluster?

Testcluster sind drei Hostcluster, die für Bewertungen von privaten Azure VMware Solution-Clouds für einen Monat verwendet werden.

#### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>Kann ich High-End-Hosts für Testcluster verwenden?

Nein. High-End-ESXi-Hosts sind für die Verwendung in Produktionsclustern reserviert.

## <a name="azure-vmware-solution-and-vmware-software"></a>Azure VMware Solution und VMware-Software

#### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>Welche Versionen von VMware-Software werden in privaten Clouds verwendet?

Private Clouds verwenden vSphere 6.7 U3, vSAN 6.7 U3, VMware HCX und NSX-T 2.5.  Weitere Informationen finden Sie in den [Anforderungen der VMware-Softwareversion](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

#### <a name="do-private-clouds-use-vmware-nsx"></a>Verwenden private Clouds VMware NSX?

Ja, NSX-T 2.5 wird für softwaredefinierte Netzwerke in privaten Azure VMware Solution-Clouds verwendet.

#### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>Kann ich VMware NSX-V in einer privaten Cloud verwenden?

Nein. NSX-T ist die einzige unterstützte Version von NSX.

#### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>Ist NSX in lokalen Umgebungen oder Netzwerken erforderlich, die eine Verbindung mit einer privaten Cloud herstellen?

Nein, Sie müssen NSX nicht lokal verwenden.

#### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>Wie sieht der Upgrade- und Updatezeitplan für VMware-Software in einer privaten Cloud aus?

Die Upgrades für die Softwarepakete der privaten Cloud behalten eine Version der Software des neuesten Release des Softwarepakets von VMware bei. Die Softwareversionen der privaten Cloud können sich von den aktuellen Versionen der einzelnen Softwarekomponenten (ESXi, NSX-T, vCenter und vSAN) unterscheiden.

#### <a name="how-often-will-the-private-cloud-software-stack-be-updated"></a>Wie häufig wird der Softwarestapel der privaten Cloud aktualisiert?

Das Upgrade der Software der privaten Cloud erfolgt nach einem Zeitplan, der sich nach der Veröffentlichung des Softwarepakets von VMware richtet. Ihre private Cloud erfordert keine Ausfallzeiten für Upgrades.

## <a name="connectivity"></a>Konnektivität

#### <a name="what-network-ip-address-planning-is-required-to-incorporate-private-clouds-with-on-premises-environments"></a>Welche Planung von Netzwerk-IP-Adressen ist erforderlich, um private Clouds in lokale Umgebungen einzubinden?

Ein /22-Adressraum eines privaten Netzwerks ist erforderlich, um eine private Azure VMware Solution-Cloud bereitzustellen. Dieser private Adressraum darf sich nicht mit anderen virtuellen Netzwerken in einem Abonnement oder mit lokalen Netzwerken überschneiden.
 
#### <a name="how-do-i-connect-from-on-premises-environments-to-an-azure-vmware-solution-private-cloud"></a>Wie kann ich eine Verbindung aus lokalen Umgebungen mit einer privaten Azure VMware Solution-Cloud herstellen?

Sie können mit einer von zwei Methoden eine Verbindung mit dem Dienst herstellen: 

- Mit einer VM oder einem Anwendungsgateway in einem virtuellen Azure-Netzwerk, die bzw. das über ExpressRoute und Peering mit der privaten Cloud verbunden ist.
- Über ExpressRoute Global Reach aus Ihrem lokalen Rechenzentrum mit einer Azure ExpressRoute-Verbindung

#### <a name="how-do-i-connect-a-workload-vm-to-the-internet-or-an-azure-service-endpoint"></a>Wie verbinde ich eine Workload-VM mit dem Internet oder einem Azure-Dienstendpunkt?

Aktivieren Sie im Azure-Portal Internetkonnektivität für eine private Cloud. Erstellen Sie mit NSX-T Manager einen NSX-T T1-Router und einen logischen Switch. Sie verwenden dann vCenter, um eine VM in dem Netzwerksegment bereitzustellen, das durch den logischen Switch definiert wird. Diese VM verfügt über Netzwerkzugriff auf das Internet und Azure-Dienste.

#### <a name="do-i-need-to-restrict-access-from-the-internet-to-vms-on-logical-networks-in-a-private-cloud"></a>Muss ich den Zugriff aus dem Internet auf VMs in logischen Netzwerken in einer privaten Cloud einschränken?

Nein. Eingehender Netzwerkdatenverkehr aus dem Internet direkt in private Clouds ist standardmäßig nicht zulässig.  Sie können Azure VMware Solution-VMs jedoch für den Empfang von Datenverkehr aus dem Internet konfigurieren, indem Sie die Option [Öffentliche IP-Adresse](public-ip-usage.md) im Azure-Portal für Ihre private Azure VMware Solution-Cloud verwenden.

#### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>Muss ich Internetzugriff von VMs in logischen Netzwerken auf das Internet in einer privaten Cloud einschränken?

Ja. Sie müssen NSX-T Manager zum Erstellen einer Firewall verwenden, die den VM-Zugriff auf das Internet einschränkt.


#### <a name="can-azure-vmware-solution-use-azure-virtual-wan-hosted-expressroute-gateways"></a>Kann Azure VMware Solution mit Azure Virtual WAN gehostete ExpressRoute-Gateways verwenden?
Ja.

#### <a name="can-transit-connectivity-be-established-between-on-premises-and-azure-vmware-solution-through-azure-virtual-wan-over-expressroute-global-reach"></a>Kann die Transitkonnektivität zwischen der lokalen Lösung und Azure VMware Solution mit Azure Virtual WAN über ExpressRoute Global Reach hergestellt werden?
Azure Virtual WAN bietet kein transitives Routing zwischen zwei ExpressRoute-Verbindungen und einem nicht virtuellen WAN-ExpressRoute-Gateway. ExpressRoute Global Reach ermöglicht Konnektivität zwischen Ihrer lokalen Infrastruktur und Azure VMware Solution, wobei die Verbindung über das globale Microsoft-Netzwerk, nicht über den Virtual WAN-Hub hergestellt wird.


## <a name="accounts-and-privileges"></a>Konten und Berechtigungen

#### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>Welche Konten und Berechtigungen erhalte ich mit meiner neuen privaten Azure VMware Solution-Cloud?

Sie erhalten Anmeldeinformationen für einen Cloudadministratorbenutzer in vCenter und Administratorzugriff auf NSX-T Manager. Es gibt auch eine Cloudadministratorgruppe, die verwendet werden kann, um Azure Active Directory zu integrieren. Weitere Informationen finden Sie unter [Zugriffs- und Identitätskonzepte](concepts-identity.md).

#### <a name="can-have-administrator-access-to-esxi-hosts"></a>Kann Administratorzugriff auf ESXi-Hosts erhalten?

Nein, Administratorzugriff auf ESXi ist darauf beschränkt, die Sicherheitsanforderungen der Lösung zu erfüllen.

#### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>Welche Zugriffsberechtigungen und Berechtigungen erhalte ich in vCenter?

Sie verfügen über CloudAdmin-Gruppenberechtigungen. Weitere Informationen finden Sie unter [Zugriffs- und Identitätskonzepte](concepts-identity.md).

#### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>Welche Zugriffsberechtigungen und Berechtigungen erhalte ich für NSX-T Manager?

Sie verfügen über vollständige Administratorrechte für NSX-T und können rollenbasierte vSphere-Zugriffssteuerung wie im lokalen NSX-T-Rechenzentrum verwalten. Weitere Informationen finden Sie unter [Zugriffs- und Identitätskonzepte](concepts-identity.md).

> [!NOTE]
> Ein T0-Router wird im Rahmen der Bereitstellung einer privaten Cloud erstellt und konfiguriert. Änderungen an diesem logischen Router oder den NSX-T-Edgeknoten-VMs können sich auf die Konnektivität mit Ihrer privaten Cloud auswirken.

## <a name="billing-and-support"></a>Abrechnung und Support

#### <a name="how-will-pricing-be-structured-for-azure-vmware-solution"></a>Wie werden die Preise für Azure VMware Solution strukturiert sein?

Allgemeine Fragen zu Preisen finden Sie auf der Seite [Preise](https://azure.microsoft.com/pricing/details/azure-vmware) für Azure VMware Solution. 

#### <a name="can-azure-vmware-solution-be-purchased-through-a-microsoft-csp"></a>Ist es möglich, Azure VMware Solution über einen Microsoft-Cloudlösungsanbieter (CSP) zu erwerben?

Ja. Kunden können Azure VMware Solution unter einem Azure-Abonnement bereitstellen, das von einem CSP verwaltet wird.

#### <a name="who-supports-azure-vmware-solution"></a>Wer unterstützt Azure VMware Solution?

Der Support für Azure VMware Solution wird von Microsoft bereitgestellt. Sie können eine [Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) übermitteln. 

Für von einem CSP verwaltete Abonnements wird die erste Supportebene vom Lösungsanbieter genauso bereitgestellt, wie es der CSP für andere Azure-Dienste tut.

#### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>Welche Konten benötige ich zum Erstellen einer privaten Azure VMware Solution-Cloud?

Sie benötigen ein Azure-Konto in einem Azure-Abonnement.

#### <a name="are-red-hat-solutions-supported-on-azure-vmware-solution"></a>Werden Red Hat-Lösungen unter Azure VMware Solution unterstützt?

Microsoft und Red Hat teilen sich ein integriertes, zusammengefasstes Supportteam, das eine einheitliche Anlaufstelle für Red Hat-Ökosysteme bietet, die auf der Azure-Plattform ausgeführt werden.  Wie andere Dienste der Azure-Plattform, die mit Red Hat Enterprise Linux zusammenarbeiten, fällt Azure VMware Solution unter den Bereich von Cloudzugriff und integriertem Support. Red Hat Enterprise Linux wird für die Ausführung auf Azure VMware Solution in Azure unterstützt.

#### <a name="is-vmware-hcx-enterprise-available-and-if-so-how-much-does-it-cost"></a>Ist VMware HCX Enterprise verfügbar, und wenn ja, wie hoch ist der Preis?

VMware HCX Enterprise ist mit Azure VMware Solution als *Vorschau*-Funktion/-Dienst verfügbar. Solange sich VMware HCX Enterprise für Azure VMware Solution in der Vorschau befindet, handelt es sich um eine kostenlose Funktion/einen kostenlosen Dienst, für die bzw. den die Nutzungsbedingungen für Dienste in der Vorschau gelten. Sobald der VMware HCX Enterprise-Dienst die Phase „Allgemeine Verfügbarkeit“ erreicht hat, erhalten Sie eine Benachrichtigung, dass in 30 Tagen die Abrechnung umgestellt wird. Sie können den Dienst ausschalten oder deaktivieren.

#### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>Wie beantrage ich eine Erhöhung des Hostkontingents für eine Azure-VMware-Lösung?

Für CSP-verwaltete Abonnements muss der Kunde die Anfrage an den Partner senden. Das Partnerteam setzt sich dann mit Microsoft in Verbindung, um das Kontingent für das Abonnement zu erhöhen. Ausführliche Informationen finden Sie unter [Aktivieren einer Azure VMware Solution-Ressource](enable-azure-vmware-solution.md). 

Verwenden Sie für EA-Abonnements das folgende Verfahren. Voraussetzungen:

* Ein [Azure Konzernvertrag (Enterprise Agreement, EA)](../cost-management-billing/manage/ea-portal-agreements.md) mit Microsoft.
* Ein Azure-Konto in einem Azure-Abonnement.

Bevor Sie Ihre Azure VMware Solution-Ressource erstellen können, müssen Sie ein Supportticket übermitteln, damit Ihre Hosts zugewiesen werden. Es kann bis zu fünf Werktage dauern, Ihre Anforderung zu bestätigen und zu erfüllen. Wenn Sie über eine vorhandene private Azure VMware Solution-Cloud verfügen und weitere Hosts zugeordnet werden sollen, müssen Sie den gleichen Prozess durchlaufen.

1. Erstellen Sie im Azure-Portal unter **Hilfe und Support** eine **[Neue Supportanfrage](https://rc.portal.azure.com/#create/Microsoft.Support)** , und geben Sie die folgenden Informationen für das Ticket an:
   - **Problemtyp:** Technisch
   - **Abonnement:** Wählen Sie Ihr Abonnement aus.
   - **Dienst:** Alle Dienste > Azure VMware Solution
   - **Ressource:** Allgemeine Frage 
   - **Zusammenfassung:** Kapazität erforderlich
   - **Problemtyp:** Capacity Management Issues (Kapazitätsverwaltungsprobleme)
   - **Problemuntertyp:** Customer Request for Additional Host Quota/Capacity (Kundenanfrage für zusätzliches Hostkontingent/zusätzliche Hostkapazität)

1. Geben Sie in der **Beschreibung** des Supporttickets auf der Registerkarte **Details** Folgendes an:

   - Proof of Concept oder Produktion 
   - Name der Region
   - Anzahl von Hosts
   - Weitere Details

   >[!NOTE]
   >Für Azure VMware Solution werden mindestens drei Hosts empfohlen, um Ihre private Cloud aufzubauen. Für Redundanzzwecke werden N+1 Hosts empfohlen. 

1. Wählen Sie **Überprüfen + erstellen** aus, um die Anforderung zu übermitteln.

   Es dauert bis zu fünf Werktage, bis ein Supportmitarbeiter Ihre Anforderung bestätigt.

   >[!IMPORTANT] 
   >Wenn Sie bereits über eine Azure VMware Solution-Instanz verfügen und zusätzliche Hosts anfordern, beachten Sie bitte, dass wir fünf Werktage für die Zuordnung der Hosts benötigen. 

1. Bevor Sie Ihre Hosts bereitstellen, vergewissern Sie sich, dass Sie den Ressourcenanbieter **Microsoft.AVS** im Azure-Portal registriert haben.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Informationen zu weiteren Möglichkeiten für die Ressourcenanbieterregistrierung finden Sie unter [Azure-Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md). 

#### <a name="are-reserved-instances-available-for-purchasing-through-the-cloud-solution-provider-csp-program"></a>Sind reservierte Instanzen für den Einkauf über das CSP-Programm (Cloud Solution Provider) verfügbar?

Ja. Der CSP kann reservierte Instanzen für seine Kunden kaufen. Weitere Informationen finden Sie im Artikel [Sparen von Kosten mit Azure VMware Solution](reserved-instance.md). 

#### <a name="does-azure-vmware-solution-offer-multi-tenancy-for-hosting-csp-partners"></a>Bietet Azure VMware Solution Mehrinstanzenfähigkeit für das Hosting von CSP-Partnern?

Nein. Derzeit bietet Azure VMware Solution keine Mehrinstanzenfähigkeit.

#### <a name="will-traffic-between-on-premises-and-azure-vmware-solution-over-expressroute-incur-any-outbound-data-transfer-charge-in-the-metered-data-plan"></a>Fallen für den Datenverkehr zwischen einem lokalen Standort und Azure VMware Solution über ExpressRoute Gebühren für ausgehende Datenübertragungen im Volumentarif an?

Der Datenverkehr zwischen ExpressRoute und Azure VMware Solution wird in keiner Weise gemessen. Der Datenverkehr auf Ihrer ExpressRoute-Verbindung zwischen Ihrem lokalen Standort und Azure wird nach ExpressRoute-Tarifen abgerechnet.


## <a name="customer-communication"></a>Kundenkommunikation

#### <a name="how-can-i-receive-an-alert-when-azure-sends-service-health-notifications-to-my-azure-subscription"></a>Wie kann ich eine Warnung erhalten, wenn Azure Benachrichtigungen zur Dienstintegrität an mein Azure-Abonnement sendet?

Benachrichtigungen zu Dienstproblemen, geplanter Wartung, Integritätsempfehlungen, Sicherheitsempfehlungen werden über **Dienstintegrität** im Azure-Portal veröffentlicht.  Um rechtzeitige Aktionen auszuführen, können Sie Aktivitätsprotokollwarnungen für diese Benachrichtigungen einrichten. Weitere Informationen finden Sie unter [Erstellen einer Service Health-Warnung über das Azure-Portal](../service-health/alerts-activity-log-service-notifications-portal.md#create-service-health-alert-using-azure-portal).

:::image type="content" source="media/service-health.png" alt-text="Screenshot von Dienstintegritätsbenachrichtigungen":::



<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=21069522

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
