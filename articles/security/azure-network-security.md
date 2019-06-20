---
title: Azure-Netzwerksicherheit | Microsoft-Dokumentation
description: Enthält Informationen zu cloudbasierten Computingdiensten mit einer großen Auswahl an Computeinstanzen und -diensten, die automatisch zentral hoch- und herunterskaliert werden können, um die Anforderungen Ihrer Anwendung bzw. Ihres Unternehmens zu erfüllen.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: f684a9d7bca77a8aa3aa60f5079dda0ce3b58a1c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60587332"
---
# <a name="azure-network-security"></a>Azure-Netzwerksicherheit

Wir wissen, dass Sicherheit in der Cloud an erster Stelle steht und wie wichtig es ist, dass Sie präzise und zeitnahe Informationen zur Azure-Sicherheit finden. Eines der schlagkräftigsten Argumente für die Verwendung von Azure für Anwendungen und Dienste ist die Vielzahl an Sicherheitstools und -funktionen von Azure. Diese Tools und Funktionen ermöglichen die Erstellung sicherer Lösungen auf Grundlage der Azure-Plattform.

Microsoft Azure bietet sowohl Vertraulichkeit, Integrität und Verfügbarkeit von Kundendaten als auch eine transparente Verantwortlichkeit. Damit Sie die gesammelten Sicherheitskontrollen für Netzwerke, die aus Sicht des Kunden in Microsoft Azure enthalten sind, besser verstehen, haben wir diesen Artikel zur „Azure-Netzwerksicherheit“ verfasst. Darin werden die Sicherheitskontrollen für Netzwerke, die für Microsoft Azure zur Verfügung stehen, umfassend beschrieben.

In diesem Dokument erhalten Sie Informationen zu den vielen Netzwerkkontrollen, die Sie konfigurieren können, um die Sicherheit der von Ihnen in Azure bereitgestellten Lösungen zu erhöhen. Wenn Sie daran interessiert sind, was Microsoft in Bezug auf den Schutz des Netzwerkfabric der eigentlichen Azure Platform unternimmt, helfen Ihnen die Informationen im Abschnitt zur „Azure-Sicherheit“ im [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/cloudservices/azure) weiter.

## <a name="azure-platform"></a>Azure Platform

Azure ist eine öffentliche Clouddienstplattform, die eine breite Palette an Betriebssystemen, Programmiersprachen, Frameworks, Tools, Datenbanken und Geräten unterstützt.  Es kann Linux-Container mit Docker-Integration ausführen, Apps mit JavaScript, Python, .NET, PHP, Java und Node.js sowie Back-Ends für iOS-, Android- und Windows-Geräte erstellen. Die Azure-Clouddienste unterstützen dieselben Technologien, die bereits von Millionen von Entwicklern und IT-Profis zuverlässig eingesetzt werden.

Wenn Sie IT-Ressourcen eines öffentlichen Clouddienstanbieters nutzen oder Daten in die Cloud eines Anbieters migrieren, verlassen Sie sich darauf, dass die jeweilige Organisation Ihre Anwendungen und Daten mit den Diensten und Steuerungsmöglichkeiten schützen kann, die sie zum Verwalten der Sicherheit Ihrer cloudbasierten Ressourcen bereitstellt.

Die Infrastruktur von Azure ist von den Hardwareressourcen bis zu den Anwendungen vollständig auf das gleichzeitige Hosten von Millionen von Kunden ausgelegt und stellt für Unternehmen eine vertrauenswürdige Grundlage zur Erfüllung ihrer Sicherheitsanforderungen dar. Darüber hinaus bietet Azure Ihnen eine umfassende Sammlung an konfigurierbaren Sicherheitsoptionen, die Sie selbst steuern können, um die Sicherheit an die individuellen Anforderungen der Bereitstellungen Ihrer Organisation anzupassen.

## <a name="abstract"></a>Zusammenfassung

Öffentliche Microsoft-Clouddienste bieten hyperskalierbare Dienste und Infrastrukturen, Features auf Unternehmensniveau und vielfältige Auswahlmöglichkeiten für Hybridkonnektivität. Sie können entweder über das Internet oder mit Azure ExpressRoute auf diese Dienste zugreifen. Bei ExpressRoute wird private Netzwerkkonnektivität bereitgestellt. Die Microsoft Azure Platform ermöglicht Ihnen eine nahtlose Erweiterung Ihrer Infrastruktur auf die Cloud und den Aufbau von Architekturen mit mehreren Ebenen. Zudem können Drittanbieter über Sicherheitsdienste und virtuelle Geräte erweiterte Funktionen anbieten.

Die Netzwerkdienste von Azure sind so konzipiert, dass Flexibilität, Verfügbarkeit, Resilienz, Sicherheit und Integrität maximiert werden. Dieses Whitepaper enthält Details zu den Netzwerkfunktionen von Azure und Informationen dazu, wie Kunden die nativen Sicherheitsfeatures von Azure verwenden können, um ihre Datenassets zu schützen.

Zielgruppen dieses Whitepapers:

- Technische Manager, Netzwerkadministratoren und Entwickler, die nach in Azure verfügbaren und unterstützten Sicherheitslösungen suchen.

-   KMUs oder Führungskräfte, die sich einen allgemeinen Überblick über die Azure-Netzwerktechnologien und -dienste verschaffen möchten, die für Diskussionen der Netzwerksicherheit in der öffentlichen Azure-Cloud relevant sind.

## <a name="azure-networking-big-picture"></a>Azure-Netzwerke – Gesamtüberblick
Microsoft Azure verfügt über eine robuste Netzwerkinfrastruktur zur Unterstützung der Konnektivitätsanforderungen Ihrer Anwendungen und Dienste. Netzwerkkonnektivität ist zwischen Ressourcen in Azure, zwischen lokalen und in Azure gehosteten Ressourcen und zu und aus dem Internet und Azure möglich.

![Azure-Netzwerke – Gesamtüberblick](media/azure-network-security/azure-network-security-fig-1.png)

Mit der [Azure-Netzwerkinfrastruktur](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) können Sie Azure-Ressourcen über virtuelle Netzwerke (VNets) sicher miteinander verbinden. Ein VNet ist eine Darstellung Ihres eigenen Netzwerks in der Cloud. Bei einem VNet handelt es sich um eine logische Isolation vom dedizierten Azure-Cloudnetzwerk für Ihr Abonnement. Sie können VNets mit Ihren lokalen Netzwerken verbinden.

Azure unterstützt eine dedizierte WAN-Linkkonnektivität mit Ihrem lokalen Netzwerk und ein Azure Virtual Network mit [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). Für den Link zwischen Azure und Ihrem Standort wird eine dedizierte Verbindung verwendet, die nicht über das öffentliche Internet verläuft. Wenn Ihre Azure-Anwendung in mehreren Datencentern ausgeführt wird, können Sie den [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) verwenden, um Anfragen von Benutzern auf intelligente Weise zwischen Instanzen der Anwendung weiterzuleiten. Sie können Datenverkehr auch an Dienste leiten, die nicht in Azure ausgeführt werden, sofern sie über das Internet zugänglich sind.

## <a name="enterprise-view-of-azure-networking-components"></a>Unternehmensansicht der Azure-Netzwerkkomponenten
Azure umfasst viele Netzwerkkomponenten, die für die Beschreibung der Netzwerksicherheit relevant sind. Wir beschreiben diese Netzwerkkomponenten und konzentrieren uns auf die damit verbundenen Sicherheitsfragen.

> [!Note]
> Es werden nicht alle Aspekte von Azure-Netzwerken beschrieben, sondern nur diejenigen, die für die Planung und den Entwurf einer sicheren Netzwerkinfrastruktur im Rahmen Ihrer in Azure bereitgestellten Dienste und Anwendungen wichtig sind.

In diesem Dokument werden die folgenden Unternehmensfunktionen für Azure-Netzwerke behandelt:

-   Grundlegende Netzwerkkonnektivität

-   Hybridkonnektivität

-   Einstellungen für einmaliges Anmelden

-   Netzwerküberprüfung

### <a name="basic-network-connectivity"></a>Grundlegende Netzwerkkonnektivität

Mit dem Dienst [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) können Sie Azure-Ressourcen über virtuelle Netzwerke (VNets) sicher miteinander verbinden. Ein VNet ist eine Darstellung Ihres eigenen Netzwerks in der Cloud. Es handelt sich hierbei um eine logische Isolation der Azure-Netzwerkinfrastruktur für Ihr Abonnement. Sie können VNETs auch miteinander und mit Ihren lokalen Netzwerken verbinden, indem Sie Site-to-Site-VPN-Verbindungen und dedizierte [WAN-Verbindungen](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) verwenden.

![Grundlegende Netzwerkkonnektivität](media/azure-network-security/azure-network-security-fig-2.png)

Wenn VMs zum Hosten von Servern in Azure eingesetzt werden, stellt sich die Frage, wie diese VMs mit einem Netzwerk verbunden werden. Die Antwort ist, dass VMs über ein [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview), also ein virtuelles Azure-Netzwerk, verbunden werden.

„Azure Virtual Networks“ ähneln den virtuellen Netzwerken, die Sie lokal für Ihre eigenen Virtualisierungsplattformlösungen wie Microsoft Hyper-V oder VMware verwenden.

#### <a name="intra-vnet-connectivity"></a>Konnektivität zwischen VNets

Sie können VNets miteinander verbinden, sodass Ressourcen, für die eine Verbindung mit einem der VNets besteht, darüber miteinander kommunizieren können. Nutzen Sie eine der folgenden Optionen (oder beide), um VNets miteinander zu verbinden:

- **Peering**: Hierdurch wird ermöglicht, dass Ressourcen, die mit unterschiedlichen Azure-VNets desselben Azure-Standorts verbunden sind, miteinander kommunizieren können. Die Bandbreite und Wartezeit für das VNet entspricht den Werten, die gelten, wenn die Ressourcen mit demselben VNet verbunden sind. Weitere Informationen zu Peerings finden Sie unter [Peering virtueller Netzwerke](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

  ![Peering](media/azure-network-security/azure-network-security-fig-3.png)

- **VNet-zu-VNet-Verbindung**: Hierbei können Ressourcen, für die eine Verbindung mit unterschiedlichen Azure-VNets besteht, an demselben oder verschiedenen Azure-Standorten verbunden werden. Im Gegensatz zum Peering ist die Bandbreite zwischen VNets beschränkt, da der Datenverkehr über ein Azure VPN Gateway fließen muss.

![VNet-zu-VNet-Verbindung](media/azure-network-security/azure-network-security-fig-4.png)


Weitere Informationen zum Verbinden von VNets mit einer VNet-zu-VNet-Verbindung finden Sie im Artikel [Konfigurieren von VNet-zu-VNet-Verbindungen](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Azure Virtual Network-Funktionen:

Wie Sie sehen, werden in einem Azure Virtual Network virtuelle Computer bereitgestellt, um eine Verbindung mit dem Netzwerk herzustellen, damit auf sichere Weise Verbindungen mit anderen Netzwerkressourcen hergestellt werden können. Die grundlegende Konnektivität ist hierbei aber nur der Anfang. Die folgenden Funktionen des Azure Virtual Network-Diensts verdeutlichen die Sicherheitsmerkmale des Azure Virtual Network:

-   Isolation

-   Internetkonnektivität

-   Konnektivität von Azure-Ressourcen

-   VNet-Konnektivität

-   Lokale Konnektivität

-   Filtern von Datenverkehr

-   Routing

**Isolation**

VNets sind voneinander [isoliert](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Sie können separate VNets für Entwicklung, Tests und Produktion erstellen, die die gleichen [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)-Adressblöcke verwenden. Im Gegensatz dazu können Sie mehrere VNets erstellen, für die unterschiedliche CIDR-Adressblöcke verwendet werden, und die Netzwerke verbinden. Sie können ein VNet in mehrere Subnetze segmentieren.

Azure ermöglicht die interne Namensauflösung für VMs und [Cloud Services](https://azure.microsoft.com/services/cloud-services/)-Rolleninstanzen, die mit einem VNet verbunden sind. Sie können ein VNet optional so konfigurieren, dass Ihre eigenen DNS-Server verwendet werden, anstatt die interne Namensauflösung von Azure zu nutzen.

Sie können in jedem Azure-[Abonnement](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) und in jeder Azure-[Region](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) mehrere VNets implementieren. Jedes VNet ist von anderen VNets isoliert. Für jedes VNet ist Folgendes möglich:

-   Geben Sie einen benutzerdefinierten privaten IP-Adressraum ein, indem Sie öffentliche und private Adressen (RFC 1918) verwenden. Azure weist Ressourcen, die mit dem VNet verbunden sind, eine private IP-Adresse aus dem von Ihnen zugewiesenen Adressraum zu.

-   Segmentieren Sie das VNet in mindestens ein Subnetz, und ordnen Sie jedem Subnetz einen Teil des VNet-Adressraums zu.

-   Verwenden Sie die in Azure enthaltene Namensauflösung, oder geben Sie Ihren eigenen DNS-Server an, der für die mit einem VNet verbundenen Ressourcen genutzt werden kann. Weitere Informationen zur Namensauflösung in VNets finden Sie unter [Namensauflösung für virtuelle Computer und Clouddienste](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

**Internetkonnektivität**

Für alle [Azure Virtual Machines (VMs)](https://docs.microsoft.com/azure/virtual-machines/windows/) und Cloud Services-Rolleninstanzen, die mit einem VNet verbunden sind, besteht standardmäßig Zugriff auf das Internet. Sie können auch je nach Bedarf den Zugriff in eingehender Richtung auf bestimmte Ressourcen ermöglichen. Für VM- und Cloud Services-Rolleninstanzen, die mit einem VNet verbunden sind, besteht standardmäßig Zugriff auf das Internet. Bei Bedarf können Sie auch den Zugriff in eingehender Richtung auf bestimmte Ressourcen ermöglichen.

Alle Ressourcen, die mit einem VNet verbunden sind, verfügen standardmäßig über Internetkonnektivität in ausgehender Richtung. Die private IP-Adresse der Ressource wird von der Azure-Infrastruktur per SNAT-Vorgang (Source Network Address Translated) in eine öffentliche IP-Adresse übersetzt. Sie können die Standardkonnektivität ändern, indem Sie das benutzerdefinierte Routing und die Filterung von Datenverkehr implementieren. Weitere Informationen zur Internetkonnektivität in ausgehender Richtung finden Sie unter [Grundlegendes zu ausgehenden Verbindungen in Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

Um über das Internet in eingehender Richtung mit Azure-Ressourcen bzw. in ausgehender Richtung ohne SNAT über das Internet kommunizieren zu können, muss einer Ressource eine öffentliche IP-Adresse zugewiesen sein. Weitere Informationen zu öffentlichen IP-Adressen finden Sie unter [Öffentliche IP-Adressen](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

**Konnektivität von Azure-Ressourcen**

[Azure-Ressourcen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview), z.B. Cloud Services und VMs, können mit demselben VNet verbunden werden. Für die Verbindung miteinander können für die Ressourcen auch dann private IP-Adressen verwendet werden, wenn sie sich in unterschiedlichen Subnetzen befinden. Azure verfügt über Standardrouting zwischen Subnetzen, VNets und lokalen Netzwerken, sodass Sie keine Routen konfigurieren und verwalten müssen.

Sie können mehrere Azure-Ressourcen mit einem VNet verbinden, z.B. virtuelle Computer (VMs), Cloud Services, App Service-Umgebungen und VM-Skalierungsgruppen. VMs stellen Verbindungen mit einem Subnetz in einem VNet über eine Netzwerkschnittstelle (NIC) her. Weitere Informationen zu NICs finden Sie unter [Netzwerkschnittstellen](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface).

**VNet-Konnektivität**

[VNets](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) können miteinander verbunden werden, sodass Ressourcen, für die eine Verbindung mit einem VNet besteht, mit Ressourcen in einem anderen VNet kommunizieren können.

Sie können VNets miteinander verbinden, sodass Ressourcen, für die eine Verbindung mit einem der VNets besteht, darüber miteinander kommunizieren können. Nutzen Sie eine der folgenden Optionen (oder beide), um VNets miteinander zu verbinden:

- **Peering**: Hierdurch wird ermöglicht, dass Ressourcen, die mit unterschiedlichen Azure-VNets desselben Azure-Standorts verbunden sind, miteinander kommunizieren können. Die Bandbreite und Wartezeit für die VNets entspricht den Werten, die gelten, wenn die Ressourcen mit demselben VNet verbunden sind. Weitere Informationen zum Peering finden Sie unter [Peering in virtuellen Netzwerken](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

- **VNet-zu-VNet-Verbindung**: Hierbei können Ressourcen, für die eine Verbindung mit unterschiedlichen Azure-VNets besteht, an demselben oder verschiedenen Azure-Standorten verbunden werden. Im Gegensatz zum Peering ist die Bandbreite zwischen VNets beschränkt, da der Datenverkehr über ein Azure VPN Gateway fließen muss. Weitere Informationen zum Verbinden von VNets per VNet-zu-VNet-Verbindung: Lesen Sie den Artikel [Konfigurieren einer VNET-zu-VNET-VPN-Gatewayverbindung über das Azure-Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Lokale Konnektivität**

VNets können mit [lokalen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) Netzwerken verbunden werden, indem private Netzwerkverbindungen zwischen Ihrem Netzwerk und Azure oder Site-to-Site-VPN-Verbindungen über das Internet verwendet werden.

Sie können Ihr lokales Netzwerk mit einem VNet verbinden, indem Sie eine Kombination der folgenden Optionen verwenden:

- **Point-to-Site-VPN (virtuelles privates Netzwerk)** : Wird zwischen einem einzelnen PC Ihres Netzwerks und dem VNet eingerichtet. Dieser Verbindungstyp ist gut geeignet, wenn Azure noch neu für Sie ist, oder wenn Sie ein Entwickler sind, da keine oder nur sehr geringe Änderungen Ihres vorhandenen Netzwerks erforderlich sind. Für die Verbindung wird das SSTP-Protokoll verwendet, um zwischen dem PC und dem VNet eine verschlüsselte Kommunikation über das Internet zu ermöglichen. Die Dauer der Wartezeit für ein Point-to-Site-VPN ist unvorhersehbar, weil der Datenverkehr über das Internet übertragen wird.

- **Site-to-Site-VPN**: Wird zwischen Ihrem VPN-Gerät und einem Azure VPN Gateway eingerichtet. Bei diesem Verbindungstyp können alle lokalen Ressourcen, die von Ihnen autorisiert werden, auf ein VNet zugreifen. Die Verbindung ist ein IPsec/IKE-VPN, mit dem die verschlüsselte Kommunikation über das Internet zwischen Ihrem lokalen Gerät und dem Azure VPN Gateway ermöglicht wird. Die Dauer der Wartezeit für eine Site-to-Site-Verbindung ist unvorhersehbar, da der Datenverkehr über das Internet übertragen wird.

- **Azure ExpressRoute:** Wird zwischen Ihrem Netzwerk und Azure über einen ExpressRoute-Partner eingerichtet. Diese Verbindung ist privat. Der Datenverkehr wird nicht über das Internet übertragen. Die Dauer der Wartezeit für eine ExpressRoute-Verbindung ist vorhersehbar, da der Datenverkehr nicht über das Internet verläuft. Weitere Informationen zu allen vorherigen Verbindungsoptionen finden Sie unter [Diagramme zur Verbindungstopologie](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Filtern von Datenverkehr**

[Netzwerkdatenverkehr](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) von VM- und Cloud Services-Rolleninstanzen kann in eingehender und ausgehender Richtung nach IP-Quelladresse und -port, IP-Zieladresse und -port und Protokoll gefiltert werden.

Sie können den Netzwerkdatenverkehr zwischen Subnetzen filtern, indem Sie eine oder beide folgenden Optionen verwenden:

- **Netzwerksicherheitsgruppen (NSGs)** : Jede Netzwerksicherheitsgruppe kann mehrere Sicherheitsregeln für die eingehende und ausgehende Richtung enthalten, mit denen Sie Datenverkehr nach IP-Adresse, Port und Protokoll für die Quelle und das Ziel filtern können. Sie können eine NSG auf jede Netzwerkschnittstelle einer VM anwenden. Außerdem können Sie eine NSG auf das Subnetz anwenden, mit dem eine Netzwerkschnittstelle oder eine andere Azure-Ressource verbunden ist. Weitere Informationen zu NSGs finden Sie unter [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

- **Virtuelle Network Appliances**: Eine virtuelle Network Appliance ist eine VM, auf der Software zum Ausführen einer Netzwerkfunktion, z.B. einer Firewall, betrieben wird. In Azure Marketplace können Sie eine Liste mit verfügbaren NVAs anzeigen. Es sind auch NVAs verfügbar, die eine WAN-Optimierung und andere Funktionen für den Netzwerkdatenverkehr ermöglichen. NVAs werden normalerweise mit benutzerdefinierten oder BGP-Routen verwendet. Darüber hinaus können Sie eine NVA zum Filtern von Datenverkehr zwischen VNets verwenden.

**Routing**

Sie können das Standardrouting von Azure optional außer Kraft setzen, indem Sie Ihre eigenen Routen konfigurieren oder BGP-Routen über ein Netzwerkgateway verwenden.

Azure erstellt Routentabellen, über die Ressourcen, die mit einem beliebigen Subnetz in einem beliebigen VNet verbunden sind, standardmäßig kommunizieren können. Sie können eine oder beide der folgenden Optionen implementieren, um die von Azure erstellten Standardrouten außer Kraft zu setzen:

- **Benutzerdefinierte Routen:** Sie können benutzerdefinierte Routingtabellen mit Routen erstellen, über die gesteuert wird, wohin der Datenverkehr für die einzelnen Subnetze geleitet wird. Weitere Informationen zu benutzerdefinierten Routen finden Sie unter [Benutzerdefinierte Routen und IP-Weiterleitung](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

- **BGP-Routen**: Wenn Sie Ihr VNET über ein Azure VPN Gateway oder eine ExpressRoute-Verbindung mit dem lokalen Netzwerk verbinden, können Sie die BGP-Routen für Ihre VNets übernehmen.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Hybrid-Internetkonnektivität: Herstellen einer Verbindung mit einem lokalen Netzwerk
Sie können Ihr lokales Netzwerk mit einem VNet verbinden, indem Sie eine Kombination der folgenden Optionen verwenden:

-   Internetkonnektivität

-   Point-to-Site-VPN (P2S-VPN)

-   Site-to-Site-VPN (S2S-VPN)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Internetverbindung

Wie der Name vermuten lässt, sind Ihre Workloads bei der Internetverbindung über das Internet zugänglich. Sie machen verschiedene öffentliche Endpunkte für Workloads verfügbar, die im virtuellen Netzwerk angeordnet sind. Diese Workloads können mit einem [Lastenausgleich für Internetzugriff ](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview) verfügbar gemacht werden, oder der VM kann einfach eine öffentliche IP-Adresse zugewiesen werden. So ist es möglich, dass dieser virtuelle Computer für alle Geräte im Internet erreichbar ist, sofern der Vorgang von einer Hostfirewall, [Netzwerksicherheitsgruppen (NSGs)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) und [benutzerdefinierten Routen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) zugelassen wird.

In diesem Szenario können Sie eine Anwendung verfügbar machen, die im Internet öffentlich zugänglich sein muss, und können dann von beliebigen Orten bzw. – je nach Konfiguration der Workloads – bestimmten Orten aus eine Verbindung damit herstellen.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>Point-to-Site-VPN oder Site-to-Site-VPN
Diese beiden Verbindungen fallen in dieselbe Kategorie. Für beide Verbindungen muss Ihr VNet über ein VPN Gateway verfügen. Sie können eine Verbindung damit herstellen, indem Sie entweder einen VPN-Client für Ihre Arbeitsstation im Rahmen der [Point-to-Site-Konfiguration](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) verwenden, oder indem Sie Ihr lokales [VPN-Gerät](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) so konfigurieren, dass eine Site-to-Site-VPN-Verbindung beendet werden kann. Auf diese Weise können lokale Geräte eine Verbindung mit Ressourcen im VNet herstellen.

Mit einer P2S-Konfiguration (Point-to-Site) können Sie von einem einzelnen Clientcomputer eine sichere Verbindung mit einem virtuellen Netzwerk herstellen. P2S ist eine VPN-Verbindung über SSTP (Secure Socket Tunneling-Protokoll).

![Punkt-zu-Standort-VPN](media/azure-network-security/azure-network-security-fig-5.png)

Point-to-Site-Verbindungen sind nützlich, wenn Sie von einem Remotestandort, z.B. von zu Hause oder aus einem Konferenzzentrum, eine Verbindung mit Ihrem VNet herstellen möchten. Diese Methode eignet sich auch, wenn Sie nur wenige Clients besitzen, die mit einem virtuellen Netzwerk verbunden werden müssen.

Für P2S-Verbindungen ist kein VPN-Gerät und auch keine öffentliche IP-Adresse erforderlich. Sie stellen die VPN-Verbindung über den Clientcomputer her. P2S ist für die Verbindungsherstellung mit Azure also nicht zu empfehlen, wenn Sie eine dauerhafte Verbindung von vielen lokalen Geräten und Computern mit Ihrem Azure-Netzwerk benötigen.

![Site-to-Site-VPN-Verbindung](media/azure-network-security/azure-network-security-fig-6.png)

> [!Note]
> Weitere Informationen zu Point-to-Site-Verbindungen finden Sie unter [Point-to-Site – Häufig gestellte Fragen](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal).

Eine Site-to-Site-VPN-Gateway-Verbindung wird verwendet, um Ihr lokales Netzwerk über einen IPsec/IKE-VPN-Tunnel (IKEv1 oder IKEv2) mit einem virtuellen Azure-Netzwerk zu verbinden.

Für diese Art von Verbindung wird ein lokales VPN-Gerät benötigt, dem eine extern zugängliche, öffentliche IP-Adresse zugewiesen ist. Diese Verbindung wird über das Internet hergestellt und ermöglicht Ihnen das Übertragen von Informationen in einem „Tunnel“ innerhalb eines verschlüsselten Links zwischen Ihrem Netzwerk und Azure. Eine Site-to-Site-VPN-Verbindung ist eine sichere, ausgereifte Technologie, die seit Jahrzehnten von Unternehmen aller Größen eingesetzt wird. Die Tunnelverschlüsselung wird mit dem [IPSec-Tunnelmodus](https://technet.microsoft.com/library/cc786385.aspx) durchgeführt.

Bei einer Site-to-Site-VPN-Verbindung handelt es sich zwar um eine vertrauenswürdige, zuverlässige und etablierte Technologie, aber der Datenverkehr im Tunnel durchläuft das Internet. Außerdem ist die Bandbreite mit einem Maximum von ca. 200 MBit/s relativ begrenzt.

Falls bei Ihnen eine außergewöhnlich hohe Sicherheitsstufe oder Leistung für standortübergreifende Verbindungen erforderlich ist, empfehlen wir Ihnen, Azure ExpressRoute für Ihre standortübergreifende Konnektivität zu verwenden. ExpressRoute ist ein dedizierter WAN-Link zwischen Ihrem lokalen Standort und einem Exchange-Hostinganbieter. Da dies eine Telekommunikationsverbindung ist, werden Ihre Daten nicht über das Internet übertragen und unterliegen daher auch nicht den potenziellen Risiken der Internetkommunikation.

> [!Note]
> Weitere Informationen zu VPN-Gateways finden Sie unter [Informationen zu VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

#### <a name="dedicated-wan-link"></a>Dedizierte WAN-Verbindung
Mit Microsoft Azure ExpressRoute können Sie Ihre lokalen Netzwerke über eine dedizierte private Verbindung, die von einem Konnektivitätsanbieter bereitgestellt wird, auf Azure erweitern.

ExpressRoute-Verbindungen verlaufen nicht über das öffentliche Internet. Auf diese Weise können ExpressRoute-Verbindungen eine höhere Sicherheit, größere Zuverlässigkeit und schnellere Geschwindigkeit bei geringerer Latenz als herkömmliche Verbindungen über das Internet bieten.

![ Dedizierte WAN-Verbindung](media/azure-network-security/azure-network-security-fig-7.png)

> [!Note]
> Informationen zum Verbinden Ihres Netzwerks mit Microsoft mithilfe von ExpressRoute finden Sie unter [ExpressRoute-Konnektivitätsmodelle](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) und [ExpressRoute-Übersicht](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

Wie bei den Site-to-Site-VPN-Optionen auch, können Sie bei ExpressRoute eine Verbindung mit Ressourcen herstellen, die sich nicht zwingend in nur einem VNet befinden müssen. Je nach SKU können Sie eine Verbindung mit bis zu zehn VNets herstellen. Wenn Sie über das [Premium-Add-On](https://docs.microsoft.com/azure/expressroute/expressroute-faqs) verfügen, sind je nach Bandbreite Verbindungen mit maximal 100 VNets möglich. Weitere Informationen zu diesen Arten von Verbindungen finden Sie unter [Diagramme zur Verbindungstopologie](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="security-controls"></a>Sicherheitskontrollen
Bei Azure Virtual Network wird ein geschütztes logisches Netzwerk bereitgestellt, das von anderen virtuellen Netzwerken isoliert ist und viele Sicherheitskontrollen unterstützt, die Sie in Ihren lokalen Netzwerken nutzen können. Kunden erstellen ihre eigene Struktur mithilfe von Subnetzen. Sie verwenden einen eigenen privaten IP-Adressbereich und konfigurieren Routentabellen, Netzwerksicherheitsgruppen, Zugriffssteuerungslisten (Access Control Lists, ACLs), Gateways und virtuelle Geräte, um ihre Workloads in der Cloud auszuführen.

Hier sind die Sicherheitskontrollen angegeben, die Sie in Ihren Azure Virtual Networks verwenden können:

-   Kontrolle des Netzwerkzugriffs

-   Benutzerdefinierte Routen

-   Appliance für Netzwerksicherheit

-   Application Gateway

-   Azure Web Application Firewall

-   Kontrolle der Netzwerkverfügbarkeit

#### <a name="network-access-controls"></a>Kontrolle des Netzwerkzugriffs
Das Azure Virtual Network (VNet) stellt die Grundlage des Azure-Netzwerkmodells dar und ermöglicht die Isolation und den Schutz. [Netzwerksicherheitsgruppen (NSGs)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) sind aber das wichtigste Werkzeug, das Sie einsetzen, um die Netzwerkdatenverkehr-Regeln auf Netzwerkebene durchzusetzen und zu kontrollieren.

![ Kontrolle des Netzwerkzugriffs](media/azure-network-security/azure-network-security-fig-8.png)


Sie können den Zugriff steuern, indem Sie die Kommunikation zwischen den Workloads innerhalb eines virtuellen Netzwerks, zwischen Systemen im Kundennetzwerk über standortübergreifende Verbindungen oder die direkte Internetkommunikation zulassen oder ablehnen.

Im Diagramm befinden sich sowohl VNets als auch NSGs auf einer bestimmten Ebene im allgemeinen Azure-Sicherheitsstapel, in dem NSGs, benutzerdefinierte Routen und virtuelle Netzwerkgeräte verwendet werden können, um Sicherheitsgrenzen zum Schützen von Anwendungsbereitstellungen im geschützten Netzwerk zu erstellen.

Für NSGs wird zum Auswerten von Datenverkehr ein 5-Tupel genutzt (ebenso in den Regeln, die Sie für die NSG-Konfiguration verwenden):

-   [IP-Quell- und -Zieladresse](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Quell- und Zielport](https://technet.microsoft.com/library/dd197515)

-   Protokoll: [Transmission Control-Protokoll (TCP)](https://technet.microsoft.com/library/cc940037.aspx) oder [User Datagram-Protokoll (UDP)](https://technet.microsoft.com/library/cc940034.aspx)

Dies bedeutet, dass Sie den Zugriff zwischen einer einzelnen VM und einer Gruppe von VMs, einer einzelnen VM und einer anderen einzelnen VM oder zwischen gesamten Subnetzen kontrollieren können. Beachten Sie auch hierbei wieder, dass es sich um eine einfache zustandsbehaftete Paketfilterung handelt, und nicht um eine vollständige Paketuntersuchung. Für eine Netzwerksicherheitsgruppe ist keine Protokollüberprüfung oder IDS- oder IPS-Funktion auf Netzwerkebene vorhanden.

In eine NSG sind einige Regeln integriert, die Sie kennen sollten. Dies sind:

-   **Zulassen des gesamten Datenverkehrs innerhalb eines bestimmten virtuellen Netzwerks:** Alle VMs, die mit demselben virtuellen Azure-Netzwerk verbunden sind, können miteinander kommunizieren.

-   **Zulassen des Azure-Lastenausgleichs in eingehender Richtung:**   Mit dieser Regel wird Datenverkehr von jeder Quelladresse zu jeder Zieladresse für den Azure-Lastenausgleich zugelassen.

-   **Eingehenden Datenverkehr verweigern:**   Mit dieser Regel wird der gesamte Datenverkehr aus dem Internet blockiert, den Sie explizit zugelassen haben.

-   **Zulassen des ausgehenden Datenverkehrs in das Internet:** Mit dieser Regel wird zugelassen, dass VMs Verbindungen mit dem Internet einleiten. Wenn Sie nicht möchten, dass diese Verbindungen initiiert werden, müssen Sie eine Regel erstellen, um diese Verbindungen zu blockieren oder die Tunnelerzwingung durchzusetzen.

#### <a name="system-routes-and-user-defined-routes"></a>Systemrouten und benutzerdefinierte Routen

Wenn Sie virtuelle Computer in einem virtuellen Netzwerk (VNet) in Azure hinzufügen, werden Sie feststellen, dass die virtuellen Computer automatisch über das Netzwerk miteinander kommunizieren können. Sie müssen kein Gateway angeben, auch wenn die virtuellen Computer sich in unterschiedlichen Subnetzen befinden.

Das gleiche gilt für die Kommunikation zwischen den virtuellen Computern und dem öffentlichen Internet und sogar für das lokale Netzwerk, wenn eine Hybridverbindung zwischen Azure und Ihrem eigenen Rechenzentrum vorhanden ist.

![Systemrouten](media/azure-network-security/azure-network-security-fig-9.png)

Dieser Kommunikationsfluss ist möglich, weil Azure eine Reihe von Systemrouten verwendet, um den IP-Datenverkehr zu definieren. Mit Systemrouten kann der Kommunikationsfluss in den folgenden Szenarien gesteuert werden:

-   Aus dem gleichen Subnetz.

-   Aus einem Subnetz in ein anderes Subnetz innerhalb eines VNet.

-   Von virtuellen Computern zum Internet.

-   Aus einem VNet in ein anderes VNet über ein VPN-Gateway.

-   Aus einem VNet in ein anderes VNet über VNet-Peering ([Dienstverkettung](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)).

-   Aus einem VNet in ein lokales Netzwerk über ein VPN-Gateway.

In vielen Unternehmen gelten strenge Sicherheits- und Konformitätsanforderungen, für die eine lokale Untersuchung aller Netzwerkpakete zur Durchsetzung bestimmter Richtlinien erforderlich ist. Azure verfügt über den Mechanismus der [Tunnelerzwingung](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling). Hierbei wird Datenverkehr von den VMs zum lokalen Standort geleitet, indem eine benutzerdefinierte Route erstellt wird oder [BGP](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp)-Ankündigungen (Border Gateway Protocol) per ExpressRoute oder VPN genutzt werden.

Die Tunnelerzwingung in Azure wird über benutzerdefinierte Routen im virtuellen Netzwerk konfiguriert. Das Umleiten von Datenverkehr an einen lokalen Standort wird als eine Standardroute zum Azure-VPN-Gateway umgesetzt.

Im folgende Abschnitt werden die aktuellen Einschränkung für die Routingtabelle und die Routen in Azure Virtual Network aufgeführt:

- Jedes Subnetz des virtuellen Netzwerks verfügt über eine integrierte Systemroutingtabelle. Die Systemroutingtabelle verfügt über die folgenden drei Gruppen von Routen:

  -  **Lokale VNET-Routen:** Direkt zu den Ziel-VMs im selben virtuellen Netzwerk

  - **Lokale Routen:** Zum Azure-VPN-Gateway

  -  **Standardroute:** Direkt zum Internet Pakete an private IP-Adressen, die nicht durch die vorherigen beiden Routen abgedeckt sind, werden verworfen.

- Sie können mit der Veröffentlichung von benutzerdefinierten Routen eine Routingtabelle erstellen, um eine Standardroute hinzuzufügen. Anschließend verknüpfen Sie dann die Routingtabelle mit Ihrem VNET-Subnetz, um dafür die Tunnelerzwingung zu aktivieren.

- Sie müssen einen "Standardstandort" unter den standortübergreifenden lokalen Standorten auswählen, der mit dem virtuellen Netzwerk verbunden ist.

- Die Tunnelerzwingung muss einem VNet zugeordnet werden, das über ein VPN-Gateway für dynamisches Routing (kein statisches Gateway) verfügt.

- Die ExpressRoute-Tunnelerzwingung kann über diesen Mechanismus nicht konfiguriert werden. Sie wird stattdessen durch Anfordern einer Standardroute über die ExpressRoute-BGP-Peeringsitzungen aktiviert.

> [!Note]
> Weitere Informationen finden Sie in der [Dokumentation zu ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

#### <a name="network-security-appliances"></a>Appliances für die Netzwerksicherheit
Mit Netzwerksicherheitsgruppen und benutzerdefinierten Routen kann zwar ein gewisses Maß an Netzwerksicherheit auf der Vermittlungs- und Transportebene des [OSI-Modells](https://en.wikipedia.org/wiki/OSI_model) erzielt werden, aber es kann auch zu Situationen kommen, in denen Sie die Sicherheit für die höheren Ebenen des Netzwerkstapels aktivieren möchten bzw. müssen. In diesen Situationen ist es ratsam, von Azure-Partnern angebotene Appliances für die Sicherheit virtueller Netzwerke bereitzustellen.

![Appliances für die Netzwerksicherheit](./media/azure-network-security/azure-network-security-fig-10.png)

Mit Azure-Appliances für die Netzwerksicherheit werden die VNet-Sicherheit und die Netzwerkfunktionen verbessert. Sie sind von vielen Anbietern über den [Azure Marketplace](https://azuremarketplace.microsoft.com) erhältlich. Diese virtuellen Appliances für die Sicherheit können für folgende Zwecke bereitgestellt werden:

-   Hoch verfügbare Firewalls

-   Eindringschutz

-   Angriffserkennung

-   Web Application Firewalls (WAFs)

-   WAN-Optimierung

-   Routing

-   Lastenausgleich

-   VPN

-   Zertifikatverwaltung

-   Active Directory

-   Multi-Factor Authentication

#### <a name="application-gateway"></a>Anwendungsgateway

[Microsoft Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) ist eine dedizierte virtuelle Appliance, mit der ein Application Delivery Controller (ADC) als Dienst bereitgestellt wird.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-11.png)

Mithilfe von Application Gateway können Sie die Leistung und Verfügbarkeit von Webfarmen optimieren, indem Sie die CPU-intensive SSL-Beendigung auf das Anwendungsgateway verlagern (SSL-Abladung). Außerdem werden beispielsweise die folgenden Routingfunktionen für Schicht 7 bereitgestellt:

-   Roundrobin-Verteilung des eingehenden Datenverkehrs

-   Cookiebasierte Sitzungsaffinität

-   Routing auf URL-Pfadbasis

-   Möglichkeit zum Hosten von mehreren Websites hinter einer Application Gateway-Einzelinstanz


Eine [Web Application Firewall (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) wird als Teil des Anwendungsgateways ebenfalls bereitgestellt. Sie bietet Schutz für Webanwendungen vor allgemeinen Onlinesicherheitsrisiken und Exploits. Application Gateway kann als

Die Application Gateway-WAF kann im Erkennungs- oder Schutzmodus ausgeführt werden. Ein häufiger Anwendungsfall für Administratoren ist die Ausführung im Erkennungsmodus, um Datenverkehr zu beobachten und auf schädliche Muster zu prüfen. Nachdem potenzielle Exploits erkannt wurden, kann verdächtiger eingehender Datenverkehr blockiert werden, indem der Schutzmodus aktiviert wird.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-12.png)

Außerdem dient die Application Gateway-WAF als Unterstützung beim Überwachen von Webanwendungen auf Angriffe. Hierfür wird ein WAF-Echtzeitprotokoll verwendet, das in [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) und [Azure Security Center](https://azure.microsoft.com/services/security-center/) integriert ist, um WAF-Warnungen nachzuverfolgen und Trends auf einfache Weise zu überwachen.

Das Protokoll im JSON-Format wird direkt im Speicherkonto des Kunden gespeichert. Sie verfügen über die vollständige Kontrolle über diese Protokolle und können Ihre eigenen Richtlinien für die Aufbewahrung anwenden.

Darüber hinaus können Sie diese Protokolle per [Azure-Protokollintegration](https://aka.ms/AzLog) in Ihrem eigenen Analysesystem erfassen. WAF-Protokolle sind auch in [Azure Monitor-Protokolle](../log-analytics/log-analytics-overview.md) integriert, sodass Sie Azure Monitor-Protokolle nutzen können, um anspruchsvolle, differenzierte Abfragen auszuführen.

#### <a name="azure-web-application-firewall-waf"></a>Azure Web Application Firewall (WAF)

Webanwendungen sind immer häufiger das Ziel böswilliger Angriffe, bei denen bekannte Sicherheitslücken ausgenutzt werden, z.B. Einschleusung von SQL-Befehlen, Cross-Site Scripting-Angriffe und andere Angriffe der [OWASP Top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)-Liste. Zum Verhindern dieser Exploits in der Anwendung sind auf mehreren Ebenen der Anwendungstopologie rigorose Wartungs-, Patch- und Überwachungsmaßnahmen erforderlich.

 ![Azure Web Application Firewall (WAF)](./media/azure-network-security/azure-network-security-fig-13.png)

Eine zentralisierte [Web Application Firewall (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) kann Schutz vor Webangriffen bieten und vereinfacht die Sicherheitsverwaltung, ohne dass Änderungen an Anwendungen erforderlich sind.

Mit einer WAF-Lösung können Sie ebenfalls schneller auf ein Sicherheitsrisiko reagieren, da eine bekannte Schwachstelle an einem zentralen Ort gepatcht wird, statt jede einzelne Webanwendung separat zu sichern. Vorhandene Anwendungsgateways lassen sich problemlos in ein Anwendungsgateway mit Web Application Firewall konvertieren.

#### <a name="network-availability-controls"></a>Kontrollelemente für die Netzwerkverfügbarkeit

Es gibt verschiedene Optionen zur Verteilung des Netzwerkdatenverkehrs mit Microsoft Azure. Diese Optionen funktionieren unterschiedlich, bieten jeweils verschiedene Featuregruppen und unterstützen verschiedene Szenarien. Sie können unabhängig voneinander oder kombiniert verwendet werden.

Hier sind die Kontrollelemente für die Netzwerkverfügbarkeit aufgeführt:

-   Azure Load Balancer

-   Application Gateway

-   Traffic Manager

**Azure Load balancer**

Dient zum Sicherstellen der Hochverfügbarkeit und Netzwerkleistung für Ihre Anwendungen. Es ist ein Layer-4-Lastenausgleichsmodul (TCP, UDP), das eingehenden Datenverkehr auf funktionierende Dienstinstanzen verteilt, die in einer Lastenausgleichsgruppe definiert sind.

 ![Azure Load Balancer](media/azure-network-security/azure-network-security-fig-14.png)


Azure Load Balancer kann für Folgendes konfiguriert werden:

-   Lastenausgleich des eingehenden Internetdatenverkehrs für virtuelle Computer. Diese Konfiguration wird als [Lastenausgleich für Internetzugriff](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview)bezeichnet.

-   Lastenausgleich für Datenverkehr zwischen virtuellen Computern in einem virtuellen Netzwerk, zwischen virtuellen Computern in Clouddiensten oder zwischen lokalen und virtuellen Computern in einem standortübergreifenden virtuellen Netzwerk. Diese Konfiguration wird als [interner Lastenausgleich](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview)bezeichnet.

-   Weiterleiten von externem Datenverkehr an eine bestimmte Instanz eines virtuellen Computers.

Alle Ressourcen in der Cloud benötigen eine öffentliche IP-Adresse, damit sie im Internet erreichbar sind. Die Cloudinfrastruktur in Azure verwendet für ihre Ressourcen nicht routingfähige IP-Adressen. Für die Kommunikation mit dem Internet verwendet Azure die Netzwerkadressübersetzung (Network Address Translation, NAT) mit öffentlichen IP-Adressen.

 **Anwendungsgateway**

 Application Gateway wird auf der Anwendungsschicht (Schicht 7 des OSI-Netzwerkreferenzstapels) ausgeführt. Er fungiert als Reverseproxydienst, beendet die Clientverbindung und leitet Anforderungen an Back-End-Endpunkte weiter.

 **Traffic Manager**

Microsoft Azure Traffic Manager ermöglicht die Verteilung von Benutzerdatenverkehr für Dienstendpunkte in unterschiedlichen Rechenzentren. Zu den von Traffic Manager unterstützten Dienstendpunkten zählen virtuelle Azure-Computer, Web-Apps und Clouddienste. Darüber hinaus kann Traffic Manager auch mit externen, Azure-fremden Endpunkten verwendet werden.

Traffic Manager verwendet das Domain Name System (DNS), um Clientanforderungen auf der Grundlage einer [Datenverkehrsrouting-Methode](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) und der Integrität der Endpunkte an den optimalen Endpunkt weiterzuleiten. Traffic Manager bietet eine Reihe von Datenverkehrsrouting-Methoden, die verschiedene Anwendungsanforderungen erfüllen und die [Überwachung](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) der Integrität von Endpunkten sowie automatisches Failover ermöglichen. Traffic Manager zeichnet sich durch eine geringe Fehleranfälligkeit aus, selbst wenn es zu einem Ausfall einer ganzen Azure-Region kommt.

Mit Azure Traffic Manager können Sie die Verteilung von Datenverkehr auf Anwendungsendpunkte steuern. Ein Endpunkt ist ein Dienst mit Internetzugriff, der innerhalb oder außerhalb von Azure gehostet wird.

Traffic Manager bietet zwei wesentliche Vorteile:

-   Verteilung von Datenverkehr gemäß einer von mehreren [Datenverkehrsrouting-Methoden](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods)

-   [Kontinuierliche Überwachung der Endpunktintegrität](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) und automatisches Failover bei einem Ausfall von Endpunkten

Wenn ein Client eine Verbindung mit einem Dienst herstellen möchte, muss zunächst der DNS-Name des Diensts in eine IP-Adresse aufgelöst werden. Anschließend stellt der Client eine Verbindung mit dieser IP-Adresse her, um auf den Dienst zuzugreifen. Traffic Manager verwendet DNS, um Clients anhand der Regeln der Routingmethode für den Datenverkehr an bestimmte Dienstendpunkte weiterzuleiten. Clients stellen mit dem ausgewählten Endpunkt eine direkte Verbindung her. Traffic Manager ist weder ein Proxy noch ein Gateway. Traffic Manager hat keinen Zugriff auf den Datenverkehr zwischen Client und Dienst.

### <a name="azure-network-validation"></a>Azure-Netzwerküberprüfung

Mit der Azure-Netzwerküberprüfung wird sichergestellt, dass das Azure-Netzwerk gemäß seiner Konfiguration funktioniert. Die Überprüfung kann durchgeführt werden, indem die verfügbaren Dienste und Features zum Überwachen des Netzwerks genutzt werden. Mit Azure Network Watcher können Sie auf eine Vielzahl von Protokollierungs- und Diagnosefunktionen zugreifen, die Ihnen die Erkenntnisse liefern, mit denen Sie den Leistungs- und Integritätszustand Ihres Netzwerks verstehen. Auf diese Funktionen kann per Portal, PowerShell, CLI, REST-API und SDK zugegriffen werden.

Azure Operational Security bezieht sich auf die Dienste, Steuerelemente und Features, die für Benutzer zum Schützen ihrer Daten, Anwendungen und anderen Ressourcen in Microsoft Azure zur Verfügung stehen. Azure Operational Security basiert auf einem Framework, mit dem die über verschiedene einzigartige Microsoft-Funktionen erworbenen Kenntnisse einbezogen werden, z.B. Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Center-Programm und umfassende Informationen zur Bedrohungslage hinsichtlich der Sicherheit im Internet.

-   [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Azure-Speicheranalyse](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Das vielleicht wichtigste Sicherheitsfeature der Plattform sind die Personen und Prozesse, von denen Microsoft Azure bedient bzw. betrieben wird. In diesem Abschnitt werden die Features der globalen Datencenterinfrastruktur von Microsoft beschrieben, die zum Verwalten der Sicherheit, der Kontinuität und des Datenschutzes dienen.

Die Infrastruktur für Ihre Anwendung besteht normalerweise aus vielen Komponenten. Dies können beispielsweise ein virtueller Computer, ein Speicherkonto und ein virtuelles Netzwerk oder eine Web-App, eine Datenbank, ein Datenbankserver und Drittanbieterdienste sein. Sie sehen diese Komponenten nicht als separate Entitäten, sondern als verwandte und voneinander abhängige Teile einer einzelnen Entität. Diese möchten Sie als Gruppe bereitstellen, verwalten und überwachen. Mit dem Azure-Ressourcen-Manager können Sie als Gruppe mit den Ressourcen in Ihrer Lösung arbeiten.

Sie können alle Ressourcen für Ihre Lösung in einem einzigen koordinierten Vorgang bereitstellen, aktualisieren oder löschen. Sie verwenden eine Vorlage für die Bereitstellung, die für unterschiedliche Umgebungen geeignet sein kann, z.B. Testing, Staging und Produktion. Der Ressourcen-Manager bietet Sicherheits-, Überwachungs- und Kennzeichnungsfunktionen, mit denen Sie Ihre Ressourcen nach der Bereitstellung verwalten können.

**Vorteile der Verwendung des Resource Managers**

Der Ressourcen-Manager bietet mehrere Vorteile:

-   Sie können alle Ressourcen für Ihre Lösung als Gruppe bereitstellen, verwalten und überwachen, anstatt diese Ressourcen einzeln zu verarbeiten.

-   Sie können die Lösung während des gesamten Entwicklungslebenszyklus wiederholt bereitstellen und sicher sein, dass Ihre Ressourcen einheitlich bereitgestellt werden.

-   Sie können zum Verwalten Ihrer Infrastruktur anstelle von Skripts auch deklarative Vorlagen verwenden.

-   Sie können die Abhängigkeiten zwischen Ressourcen definieren, sodass diese in der richtigen Reihenfolge bereitgestellt werden.

-   Sie können die Zugriffssteuerung auf alle Dienste in der Ressourcengruppe anwenden, da die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) standardmäßig in die Verwaltungsplattform integriert ist.

-   Sie können Tags auf Ressourcen anwenden, um alle Ressourcen in Ihrem Abonnement logisch zu organisieren.

-   Indem Sie die Kosten für eine Gruppe mit Ressourcen anzeigen, für die das gleiche Tag verwendet wird, erhalten Sie die Abrechnungsinformationen für Ihre Organisation.

> [!Note]
> Der Ressourcen-Manager stellt eine neue Möglichkeit zur Bereitstellung und Verwaltung Ihrer Lösungen bereit. Weitere Informationen zu den Änderungen an dem von Ihnen verwendeten früheren Bereitstellungsmodell finden Sie unter [Grundlegendes zur Bereitstellung über den Ressourcen-Manager im Vergleich zur klassischen Bereitstellung](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="azure-network-logging-and-monitoring"></a>Protokollierung und Überwachung von Azure-Netzwerken

Azure umfasst viele Tools zum Überwachen, Verhindern, Erkennen von und Reagieren auf Ereignisse zur Netzwerksicherheit. Dies sind einige der leistungsstärksten Tools, die Ihnen hierfür zur Verfügung stehen:

-   Network Watcher

-   Netzwerküberwachung auf Ressourcenebene

-   Azure Monitor-Protokolle

### <a name="network-watcher"></a>Network Watcher

[Network Watcher:](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) Die szenariobasierte Überwachung wird mit den Features in Network Watcher bereitgestellt. Dieser Dienst umfasst die Paketerfassung, „Nächster Hop“, die IP-Datenflussüberprüfung, die Sicherheitsgruppenansicht und NSG-Datenflussprotokolle. Die Überwachung auf Szenarioebene bietet im Gegensatz zur Überwachung einzelner Netzwerkressourcen eine End-to-End-Ansicht der Netzwerkressourcen.

 ![Network Watcher](./media/azure-network-security/azure-network-security-fig-15.png)

Network Watcher ist ein regionaler Dienst, mit dem Sie Bedingungen auf der Ebene von Netzwerkszenarien in Azure überwachen und diagnostizieren können. Die Tools zur Netzwerkdiagnose und -visualisierung von Network Watcher helfen Ihnen dabei, Ihr Netzwerk in Azure zu verstehen, Diagnosen durchzuführen und Einblicke zu gewinnen.

Network Watcher verfügt derzeit über die folgenden Funktionen:

#### <a name="topology"></a>Topologie

Die [Topologie](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) gibt ein Diagramm mit den Netzwerkressourcen in einem virtuellen Netzwerk zurück. Das Diagramm zeigt die Verbindung zwischen den Ressourcen, um die End-to-End-Netzwerkkonnektivität darzustellen. Im Portal gibt die Topologie die Ressourcenobjekte jeweils basierend auf dem virtuellen Netzwerk zurück. Die Beziehungen werden durch Linien zwischen den Ressourcen dargestellt. Außerhalb der Network Watcher-Region liegende Ressourcen werden auch dann nicht dargestellt, wenn sie zur Ressourcengruppe gehören. Die in der Portalansicht zurückgegebenen Ressourcen sind eine Teilmenge der Netzwerkkomponenten, die grafisch dargestellt werden. Um die vollständige Liste der Netzwerkressourcen anzuzeigen, können Sie [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) oder [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest) verwenden.

Wenn die Ressourcen zurückgegeben werden, werden für Verbindungen dazwischen zwei Beziehungen modelliert.

- **Einschlussbeziehung**: Das virtuelle Netzwerk enthält ein Subnetz mit einer NIC.

- **Zuordnungsbeziehung**: Eine NIC ist einem virtuellen Computer zugeordnet.

#### <a name="variable-packet-capture"></a>Variable Paketerfassung

Mithilfe der [variablen Paketerfassung](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) in Network Watcher können Sie Paketerfassungssitzungen erstellen, um den eingehenden und ausgehenden Datenverkehr eines virtuellen Computers nachzuverfolgen. Mithilfe der Paketerfassung können Sie Netzwerkanomalien sowohl reaktiv als auch proaktiv diagnostizieren. Weitere Verwendungszwecke sind das Erfassen von Netzwerkstatistiken, das Gewinnen von Informationen zu Netzwerkangriffen, das Debuggen der Kommunikation zwischen Client und Server und vieles mehr.

Paketerfassung ist eine Erweiterung des virtuellen Computers, die remote über Network Watcher gestartet wird. Diese Funktion erleichtert die manuelle Ausführung einer Paketerfassung auf dem gewünschten virtuellen Computer. So sparen Sie wertvolle Zeit. Paketerfassung kann über Portal, PowerShell, CLI oder REST-API ausgelöst werden. Ein Beispiel dafür, wie die Paketerfassung ausgelöst werden kann, ist die Auslösung mit VM-Warnungen.

#### <a name="ip-flow-verify"></a>IP-Datenflussüberprüfung

Bei der [IP-Datenflussüberprüfung](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) wird basierend auf 5-Tupel-Informationen geprüft, ob ein Paket an eine VM bzw. von einer VM zugelassen oder verweigert wird. Diese Informationen enthalten die Richtung, das Protokoll, die lokale IP-Adresse, die Remote-IP-Adresse, den lokalen Port und den Remoteport. Wenn das Paket von einer Sicherheitsgruppe abgelehnt wird, wird der Name der Regel, die das Paket verweigert hat, zurückgegeben. Es können beliebige Quell- oder Ziel-IP-Adressen ausgewählt werden. Damit hilft dieses Feature Administratoren bei der schnellen Diagnose von Verbindungsproblemen mit dem Internet und in der lokalen Umgebung.

Bei IP-Datenflüssen wird eine Netzwerkschnittstelle eines virtuellen Computers überprüft. Der Datenfluss zu oder von dieser Netzwerkschnittstelle wird dann basierend auf den konfigurierten Einstellungen überprüft. Diese Funktion ist nützlich, um zu bestätigen, ob eine Regel in einer Netzwerksicherheitsgruppe ein- oder ausgehenden Datenverkehr eines virtuellen Computers blockiert.

#### <a name="next-hop"></a>Nächster Hop

Ermittelt den [nächsten Hop](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) für Pakete, die im Azure-Netzwerkfabric weitergeleitet werden, sodass Sie eine Diagnose zur Ermittlung von falsch konfigurierten benutzerdefinierten Routen durchführen können. Der Datenverkehr von einem virtuellen Computer an ein Ziel wird basierend auf den gültigen Routen, die einer Netzwerkkarte zugeordnet sind, gesendet. Nächster Hop ruft den Typ und die IP-Adresse des nächsten Hops eines Pakets von einem angegebenen virtuellen Computer und der Netzwerkkarte ab. Dadurch wird ermittelt, ob das Paket an das Ziel gesendet oder ob der Datenverkehr blockiert wird.

Nächster Hop gibt auch die Routentabelle, die dem nächsten Hop zugeordnet ist, zurück. Wenn vom nächsten Hop abgefragt wird, ob die Route als eine benutzerdefinierte Route definiert ist, wird diese Route zurückgegeben. Andernfalls gibt Nächster Hop die „Systemroute“ zurück.

#### <a name="security-group-view"></a>Sicherheitsgruppenansicht

Ruft die geltenden und angewendeten Sicherheitsregeln ab, die auf einen virtuellen Computer angewendet werden. Netzwerksicherheitsgruppen werden auf Subnetzebene oder auf NIC-Ebene zugeordnet. Bei einer Zuordnung auf Subnetzebene gilt sie für alle VM-Instanzen in dem Subnetz. Die [Netzwerksicherheitsgruppen-Ansicht](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) gibt alle konfigurierten NSGs und Regeln zurück, die auf NIC- und Subnetzebene für einen virtuellen Computer zugeordnet wurden, und bietet dabei Einblicke in die Konfiguration. Darüber hinaus werden die geltenden Sicherheitsregeln für jede Netzwerkkarte auf einem virtuellen Computer zurückgegeben. Mithilfe der Netzwerksicherheitsgruppen-Ansicht können Sie Netzwerksicherheitslücken auf einem virtuellen Computer, z.B. geöffnete Ports, beurteilen. Sie können auch basierend auf einem [Vergleich zwischen den konfigurierten und angewendeten Sicherheitsregeln](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell) überprüfen, ob die Netzwerksicherheitsgruppe wie erwartet funktioniert.

#### <a name="nsg-flow-logging"></a>NSG-Datenflussprotokollierung

 Mithilfe von Datenflussprotokollen für Netzwerksicherheitsgruppen können Sie Protokolle zum Datenverkehr erfassen, der von den Sicherheitsregeln in der Gruppe zugelassen oder verweigert wird. Der Datenfluss wird durch 5-Tupel-Informationen definiert: Quell-IP-Adresse, Ziel-IP-Adresse, Quellport, Zielport und Protokoll.

[Datenflussprotokolle für Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) sind ein Network Watcher-Feature, mit dem Sie Informationen zu ein- und ausgehendem IP-Datenverkehr über eine Netzwerksicherheitsgruppe anzeigen können.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Gateway des virtuellen Netzwerks und Problembehandlung für Verbindungen

Network Watcher bietet viele Funktionen zum Erfassen von Informationen über Ihre Netzwerkressourcen in Azure. Zu diesen Funktionen zählt die Ressourcenproblembehandlung. Die [Ressourcenproblembehandlung](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) kann über PowerShell, die CLI oder die REST-API aufgerufen werden. Nach dem Aufrufen untersucht Network Watcher die Integrität von Virtual Network-Gateways oder -Verbindungen und gibt entsprechende Ergebnisse zurück.

In diesem Abschnitt werden die verschiedenen Verwaltungsaufgaben beschrieben, die derzeit für die Problembehandlung für Ressourcen verfügbar sind.

-   [Problembehandlung für ein Virtual Network-Gateway](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [Problembehandlung für Verbindungen](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>Grenzwerte für Netzwerkabonnements

Unter [Grenzwerte für Netzwerkabonnements](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) werden Details zur Verwendung der einzelnen Netzwerkressourcen in einem Abonnement und einer Region im Vergleich zur Anzahl von maximal verfügbaren Ressourcen angezeigt.

#### <a name="configuring-diagnostics-log"></a>Konfigurieren des Diagnoseprotokolls

Network Watcher enthält eine Ansicht für die [Diagnoseprotokolle](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). Diese Ansicht enthält alle Netzwerkressourcen, die die Diagnoseprotokollierung unterstützen. In dieser Ansicht können Sie Netzwerkressourcen einfach und schnell aktivieren und deaktivieren.

### <a name="network-resource-level-monitoring"></a>Netzwerküberwachung auf Ressourcenebene

Die folgenden Features stehen für die Überwachung auf Ressourcenebene zur Verfügung:

#### <a name="audit-log"></a>Überwachungsprotokoll

Vorgänge, die im Rahmen der Konfiguration von Netzwerken durchgeführt werden, werden protokolliert. Diese Überwachungsprotokolle sind für die Einrichtung von verschiedenen Konformitäten von entscheidender Bedeutung. Diese Protokolle können im Azure-Portal angezeigt oder mithilfe von Microsoft-Tools wie Power BI oder Drittanbietertools abgerufen werden. Die Überwachungsprotokolle sind über das Verwaltungsportal, PowerShell, die Befehlszeilenschnittstelle und die Rest-API verfügbar.

> [!Note]
> Weitere Informationen zu Überwachungsprotokollen finden Sie unter [Überwachen von Vorgängen mit Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
Überwachungsprotokolle stehen für Vorgänge zur Verfügung, die für alle Netzwerkressourcen durchgeführt werden.


#### <a name="metrics"></a>metrics

Metriken sind Leistungsmessungen und -indikatoren, die über einen bestimmten Zeitraum gesammelt werden. Zurzeit sind Metriken für Application Gateway verfügbar. Metriken können verwendet werden, um basierend auf Schwellenwerten Warnungen auszulösen. Azure Application Gateway überwacht standardmäßig die Integrität aller Ressourcen in seinem Back-End-Pool und entfernt automatisch alle als fehlerhaft geltenden Ressourcen aus dem Pool. Application Gateway überwacht die fehlerhaften Instanzen weiterhin und fügt sie dem fehlerfreien Back-End-Pool hinzu, sobald sie verfügbar sind und auf Zustandsüberprüfungen reagieren. Application Gateway sendet die Integritätstests über denselben Port, der in den HTTP-Einstellungen des Back-Ends festgelegt wurde. Durch diese Konfiguration wird sichergestellt, dass derselbe Port getestet wird, den der Kunde für die Verbindung mit dem Back-End verwenden würde.

> [!Note]
> Unter [Application Gateway-Diagnose](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview) erfahren Sie, wie Metriken verwendet werden können, um Warnungen zu erstellen.

#### <a name="diagnostic-logs"></a>Diagnoseprotokolle

Regelmäßige und spontane Ereignisse werden von Netzwerkressourcen erstellt, in Speicherkonten protokolliert und dann an Event Hub oder Azure Monitor-Protokolle gesendet. Diese Protokolle geben Einblick in die Integrität einer Ressource. Diese Protokolle können in Tools wie Power BI und Azure Monitor-Protokolle angezeigt werden. Informationen zum Anzeigen von Diagnoseprotokollen finden Sie unter [Azure Monitor-Protokolle](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

Diagnoseprotokolle sind für [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), Routen und [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics) verfügbar.

Network Watcher bietet eine Ansicht der Diagnoseprotokolle. Diese Ansicht enthält alle Netzwerkressourcen, die die Diagnoseprotokollierung unterstützen. In dieser Ansicht können Sie Netzwerkressourcen einfach und schnell aktivieren und deaktivieren.

### <a name="azure-monitor-logs"></a>Azure Monitor-Protokolle

[Azure Monitor-Protokolle](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) ist ein Dienst in Azure, der Ihre cloudbasierten und lokalen Umgebungen überwacht, um die Verfügbarkeit und Leistung sicherzustellen. Er sammelt Daten, die von Ressourcen in Ihren cloudbasierten und lokalen Umgebungen sowie von anderen Überwachungstools generiert werden, um Analysen für mehrere Quellen zu ermöglichen.

Azure Monitor-Protokolle bietet die folgenden Lösungen zur Überwachung Ihrer Netzwerke:

-   Netzwerkleistungsmonitor (NPM)

-   Azure Application Gateway-Analyse

-   Azure-Netzwerksicherheitsgruppen-Analyse

#### <a name="network-performance-monitor-npm"></a>Netzwerkleistungsmonitor (NPM)
Die [Netzwerkleistungsmonitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor)-Verwaltungslösung ist eine Lösung zur Netzwerküberwachung, mit der die Integrität, Verfügbarkeit und Erreichbarkeit von Netzwerken überwacht wird.

Sie dient zur Überwachung der Konnektivität zwischen:

-   Public Cloud und lokaler Umgebung

-   Rechenzentren und Benutzerstandorten (Zweigstellen)

-   Subnetzen, die verschiedene Ebenen einer Multi-Tier-Anwendung hosten.


#### <a name="azure-application-gateway-analytics-in-azure-monitor-logs"></a>Azure Application Gateway-Analyse in Azure Monitor-Protokolle

Die folgenden Protokolle werden für Anwendungsgateways unterstützt:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

Für Anwendungsgateways werden folgende Metriken unterstützt:

-   5-Minuten-Durchsatz

#### <a name="azure-network-security-group-analytics-in-azure-monitor-logs"></a>Azure-Netzwerksicherheitsgruppen-Analyse in Azure Monitor-Protokolle

Die folgenden Protokolle werden für [Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) unterstützt:

- **NetworkSecurityGroupEvent:** Enthält Einträge, für die anhand der MAC-Adresse NSG-Regeln auf virtuelle Computer und Instanzrollen angewendet werden. Der Status für diese Regeln wird alle 60 Sekunden erfasst.

- **NetworkSecurityGroupRuleCounter:** Enthält Einträge darüber, wie oft jede NSG-Regel angewendet wurde, um Datenverkehr zuzulassen oder zu verweigern.

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich weiter über die Sicherheit, indem Sie einige unserer weiter gehenden Sicherheitsthemen lesen:

-   [Azure Monitor-Protokolle für Netzwerksicherheitsgruppen (NSGs)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [Networking innovations that drive the cloud disruption](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/) (Netzwerkinnovationen, die die Cloudrevolution fördern)

-   [SONiC: The networking switch software that powers the Microsoft Global Cloud](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/) (SONiC: Netzwerkswitch-Software als Grundlage der Microsoft Global Cloud)

-   [How Microsoft builds its fast and reliable global network](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/) (Wie Microsoft sein schnelles und zuverlässiges globales Netzwerk aufbaut)

-   [Lighting up network innovation](https://azure.microsoft.com/blog/lighting-up-network-innovation/) (Förderung von Netzwerkinnovationen)
