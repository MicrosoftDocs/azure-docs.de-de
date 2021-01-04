---
title: Azure Virtual Network – häufig gestellte Fragen
titlesuffix: Azure Virtual Network
description: Enthält Antworten auf häufig gestellte Fragen zu virtuellen Microsoft Azure-Netzwerken.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/26/2020
ms.author: kumud
ms.openlocfilehash: 3ee9e165ce9c24968b072d19367e0285f5438259
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938799"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Azure Virtual Network – häufig gestellte Fragen

## <a name="virtual-network-basics"></a>Grundlagen zu Virtual Networks

### <a name="what-is-an-azure-virtual-network-vnet"></a>Was ist ein Azure Virtual Network (VNet)?
Ein Azure Virtual Network (VNet) ist eine Darstellung Ihres eigenen Netzwerks in der Cloud. Es ist eine logische Isolierung von der Azure-Cloud für Ihr Abonnement. Mit VNets können Sie virtuelle private Netzwerke (VPNs) in Azure bereitstellen und verwalten und die VNets optional mit anderen VNets in Azure oder mit Ihrer lokalen IT-Infrastruktur verbinden, um hybride oder standortübergreifende Lösungen zu erstellen. Jedes erstellte VNet verfügt über einen eigenen CIDR-Block und kann mit anderen VNets und lokalen Netzwerken verbunden werden, solange sich die CIDR-Blöcke nicht überlappen. Sie können zudem die DNS-Servereinstellungen für VNets steuern und das VNet in Subnetze segmentieren.

Verwenden Sie VNets für Folgendes:

* Erstellen eines dedizierten privaten VNET, das auf die Cloud beschränkt ist. Manchmal benötigen Sie keine standortübergreifende Konfiguration für Ihre Lösung. Wenn Sie ein VNet erstellen, können die Dienste und virtuellen Computer des VNet direkt und sicher in der Cloud miteinander kommunizieren. Sie können trotzdem in Ihrer Lösung Endpunktverbindungen für die virtuellen Computer und Dienste konfigurieren, die eine Internetkommunikation erfordern.

* Sicheres Erweitern des Rechenzentrums. Mit VNets können Sie herkömmliche Standort-zu-Standort (S2S)-VPNs erstellen, um die Kapazität des Rechenzentrums sicher zu skalieren. S2S-VPNs verwenden IPsec, um eine sichere Verbindung zwischen dem unternehmenseigenen VPN-Gateway und Azure bereitzustellen.

* Unterstützung von Hybrid Cloud-Szenarien. VNets bieten flexible Möglichkeiten, verschiedene Hybrid Cloud-Szenarios zu unterstützen. Sie können cloudbasierte Anwendungen auf sichere Weise mit beliebigen lokalen Systemen wie z. B. Mainframes oder Unix-Systemen verbinden.

### <a name="how-do-i-get-started"></a>Wie fange ich an?
Auf der Webseite [Dokumentation zu virtuellen Netzwerken](https://docs.microsoft.com/azure/virtual-network/) finden Sie Informationen zu den ersten Schritten. Hierbei handelt es sich um Übersichts- und Bereitstellungsinformationen für alle VNET-Features.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Können VNets ohne standortübergreifende Konnektivität verwendet werden?
Ja. Sie können ein VNET verwenden, ohne es mit Ihrer lokalen Umgebung verbinden zu müssen. Sie können beispielsweise Microsoft Windows Server-Active Directory-Domänencontroller und SharePoint-Farmen nur in einem Azure-VNET ausführen.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Kann ich eine WAN-Optimierung zwischen VNets oder einem VNet und meinem lokalen Rechenzentrum durchführen?
Ja. Sie können ein [virtuelles Netzwerkgerät für die WAN-Optimierung](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization) von mehreren Anbietern über den Azure Marketplace bereitstellen.

## <a name="configuration"></a>Konfiguration

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Welche Tools werden zum Erstellen eines VNet verwendet?
Sie können die folgenden Tools zum Erstellen oder Konfigurieren eines VNet verwenden:

* Azure-Portal
* PowerShell
* Azure CLI
* Eine Netzwerkkonfigurationsdatei (NETCFG-Datei, nur für klassische VNets). Weitere Informationen finden Sie im Artikel [Konfigurieren eines Virtual Network mit einer Netzwerkkonfigurationsdatei](virtual-networks-using-network-configuration-file.md).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Welche Adressbereiche kann ich in meinen VNets verwenden?
Es wird empfohlen, die in [RFC 1918](https://tools.ietf.org/html/rfc1918) aufgelisteten Adressbereiche zu verwenden, die von der IETF für private, nicht routingfähige Adressräume reserviert wurden:
* 10.0.0.0 – 10.255.255.255 (Präfix: 10/8)
* 172.16.0.0 – 172.31.255.255 (Präfix: 172.16/12)
* 192.168.0.0 – 192.168.255.255 (Präfix: 192.168/16)

Andere Adressräume funktionieren möglicherweise, können aber unerwünschte Nebeneffekte haben.

Die folgenden Adressbereiche können nicht hinzugefügt werden:
* 224.0.0.0/4 (Multicast)
* 255.255.255.255/32 (Übertragung)
* 127.0.0.0/8 (Loopback)
* 169.254.0.0/16 (verbindungslokal)
* 168.63.129.16/32 (Internes DNS)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Können öffentliche IP-Adressen in VNets verwendet werden?
Ja. Weitere Informationen zu öffentlichen IP-Adressbereichen finden Sie unter [Create, change, or delete a virtual network](manage-virtual-network.md#create-a-virtual-network) (Erstellen, Ändern oder Löschen eines virtuellen Netzwerks). Auf öffentliche IP-Adressen kann nicht direkt über das Internet zugegriffen werden.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Ist die Anzahl von Subnetzen im VNet begrenzt?
Ja. Ausführliche Informationen finden Sie im Artikel zu den [Einschränkungen für Azure-Abonnements](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Die Adressräume von Subnetzen können sich nicht überlappen.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Unterliegen die in den Subnetzen verwendeten IP-Adressen bestimmten Beschränkungen?
Ja. Azure reserviert fünf IP-Adressen in jedem Subnetz. Dies sind x.x.x.0-x.x.x.3 und die letzte Adresse des Subnetzes. x.x.x.1-x.x.x.3 ist in jedem Subnetz für Azure-Dienste reserviert.   
- x.x.x.0: Netzwerkadresse
- x.x.x.1: Von Azure für das Standardgateway reserviert.
- x.x.x.2, x.x.x.3: Von Azure zum Zuordnen der Azure DNS-IPs zum VNet-Adressraum reserviert.
- x.x.x.255: Netzwerkadresse für Broadcasts

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Wie ist die minimale und maximale Größe von VNets und Subnetzen?
Das kleinste unterstützte IPv4-Subnetz weist die Netzmaske /29 und das größte die Netzmaske /8 (gemäß CIDR-Subnetzdefinitionen) auf.  IPv6-Subnetze müssen exakt /64 groß sein.  

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Können VLANs mithilfe von VNets in Azure integriert werden?
Nein. VNets sind Layer-3-Overlays. Layer-2-Semantik wird in Azure nicht unterstützt.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Können benutzerdefinierte Routingrichtlinien für VNets und Subnetze angegeben werden?
Ja. Sie können eine Routingtabelle erstellen und einem Subnetz zuordnen. Weitere Informationen zum Routing in Azure finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Unterstützen VNets Multicasting oder Broadcasting?
Nein. Multicast und Broadcast werden nicht unterstützt.

### <a name="what-protocols-can-i-use-within-vnets"></a>Welche Protokolle werden innerhalb von VNets unterstützt?
Sie können die Protokolle TCP, UDP und ICMP TCP/IP in VNets verwenden. Unicast wird innerhalb von VNETs unterstützt, mit Ausnahme von DHCP (Dynamic Host Configuration Protocol) per Unicast (Quellport UDP/68, Zielport UDP/67) und UDP-Quellport 65330, der für den Host reserviert ist. Verkapselte Multicast-, Broadcast- und IP-in-IP-Pakete sowie GRE-Pakete (Generic Routing Encapsulation) werden blockiert. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Kann ich meine Standardrouter innerhalb eines VNet mit "ping" abfragen?
Nein.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Können Verbindungsdiagnosen mit "tracert" durchgeführt werden?
Nein.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Können Subnetze hinzugefügt werden, nachdem das VNet erstellt wurde?
Ja. Subnetze können jederzeit zu VNETs hinzugefügt werden, sofern der Subnetzadressbereich nicht Teil eines anderen Subnetzes und ausreichend Platz im Adressbereich des virtuellen Netzwerks verfügbar ist.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Kann die Größe des Subnetzes nach dessen Erstellung geändert werden?
Ja. Sie können ein Subnetz hinzufügen, entfernen, erweitern oder verkleinern, wenn darin keine VMs oder Dienste bereitgestellt werden.

### <a name="can-i-modify-vnet-after-i-created-them"></a>Kann ein VNET nach dem Erstellen geändert werden?
Ja. Sie können die in einem VNet verwendeten CIDR-Blöcke hinzufügen, entfernen und ändern.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Kann eine Verbindung mit dem Internet hergestellt werden, wenn Dienste in einem VNET ausgeführt werden?
Ja. Alle Dienste, die in einem VNET bereitgestellt werden, können eine ausgehende Verbindung mit dem Internet herstellen. Weitere Informationen zu ausgehenden Internetverbindungen in Azure finden Sie unter [Ausgehende Verbindungen in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Wenn Sie mit einer Ressource eine Verbindung in eingehender Richtung herstellen möchten, die über Resource Manager bereitgestellt wurde, muss der Ressource eine öffentliche IP-Adresse zugewiesen sein. Weitere Informationen zu öffentlichen IP-Adressen finden Sie unter [Erstellen, Ändern oder Löschen einer öffentlichen IP-Adresse](virtual-network-public-ip-address.md). Jeder in Azure bereitgestellte Azure-Clouddienst verfügt über eine öffentlich adressierbare, zugewiesene VIP-Adresse. Sie müssen Eingabeendpunkte für PaaS-Rollen und Endpunkte für virtuelle Computer definieren, damit diese Dienste Verbindungen über das Internet annehmen können.

### <a name="do-vnets-support-ipv6"></a>Unterstützen VNets IPv6?
Ja, VNets können „Nur-IPv4“ oder „Dual-Stack“ sein (IPv4 und IPv6).  Ausführliche Informationen finden Sie unter [Übersicht über IPv6 für Azure Virtual Networks](./ipv6-overview.md).

### <a name="can-a-vnet-span-regions"></a>Kann sich ein VNet über mehrere Regionen erstrecken?
Nein. Ein VNet ist auf eine Region beschränkt. Ein virtuelles Netzwerk erstreckt sich jedoch über Verfügbarkeitszonen. Weitere Informationen zu Verfügbarkeitszonen finden Sie unter [Overview of Availability Zones in Azure (Preview) (Übersicht über Verfügbarkeitszonen in Azure (Vorschauversion))](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Sie können mithilfe von Peering in virtuellen Netzwerken Verbindungen zwischen virtuellen Netzwerken in verschiedenen Regionen herstellen. Weitere Informationen finden Sie unter [Peering in virtuellen Netzwerken](virtual-network-peering-overview.md).

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Kann ein VNet mit einem anderen VNet in Azure verbunden werden?
Ja. Sie können zwei VNETs miteinander verbinden, indem Sie Folgendes verwenden:
- **Peering in virtuellen Netzwerken:** Ausführliche Informationen finden Sie in der [Übersicht über VNET-Peering](virtual-network-peering-overview.md).
- **Ein Azure VPN Gateway:** Ausführliche Informationen finden Sie unter [Konfigurieren von VNET-zu-VNET-Verbindungen](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Namensauflösung (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Welche DNS-Optionen sind für VNets verfügbar?
Eine Übersicht über die verfügbaren DNS-Optionen finden Sie in der Entscheidungstabelle auf der Seite [Namensauflösung für virtuelle Computer und Rolleninstanzen](virtual-networks-name-resolution-for-vms-and-role-instances.md) .

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Können DNS-Server für ein VNet angegeben werden?
Ja. Sie haben die Möglichkeit, IP-Adressen von DNS-Servern in den Einstellungen des VNet anzugeben. Die Einstellung gilt als DNS-Standardserver für alle virtuellen Computer im VNET.

### <a name="how-many-dns-servers-can-i-specify"></a>Wie viele DNS-Server können angegeben werden?
Siehe [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Können DNS-Server geändert werden, nachdem das Netzwerk erstellt wurde?
Ja. Sie können die Liste der DNS-Server für das VNet jederzeit ändern. Wenn Sie Ihre DNS-Serverliste ändern, müssen Sie für alle betroffenen VMs im VNET eine Verlängerung der DHCP-Lease durchführen, damit die neuen DNS-Einstellungen wirksam werden. Für VMs, auf denen das Windows-Betriebssystem ausgeführt wird, können Sie hierzu `ipconfig /renew` direkt auf der VM eingeben. Informationen zu anderen Betriebssystemtypen finden Sie in der Dokumentation zur Verlängerung der DHCP-Lease für den jeweiligen Betriebssystemtyp. 

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Was ist der von Azure bereitgestellte DNS-Dienst, und wie wird er bei VNets verwendet?
Der von Azure bereitgestellte DNS-Dienst ist ein von Microsoft angebotener mehrinstanzenfähiger DNS-Dienst. In Azure werden Ihre gesamten VMs und Clouddienst-Rolleninstanzen dieses Diensts registriert. Dieser Dienst stellt die Namensauflösung nach dem Hostnamen für virtuelle Computer und Rolleninstanzen, die im gleichen Clouddienst enthalten sind, und nach dem FQDN für virtuelle Computer und Rolleninstanzen im gleichen VNet zur Verfügung. Weitere Informationen zum DNS finden Sie unter [Namensauflösung für virtuelle Computer und Rolleninstanzen](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Die mandantenübergreifende Namensauflösung mithilfe des von Azure bereitgestellten DNS-Diensts ist auf die ersten 100 Clouddienste in einem VNET beschränkt. Diese Einschränkung gilt nicht, wenn Sie einen eigenen DNS-Server verwenden.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Können DNS-Einstellungen für einzelne virtuelle Computer oder Clouddienste überschrieben werden?
Ja. Sie können DNS-Server pro VM oder Clouddienst festlegen, um die standardmäßigen Netzwerkeinstellungen zu überschreiben. Es wird jedoch empfohlen, nach Möglichkeit den netzwerkweiten DNS-Server zu verwenden.

### <a name="can-i-bring-my-own-dns-suffix"></a>Können benutzerdefinierte DNS-Suffixe angegeben werden?
Nein. Sie können kein benutzerdefiniertes DNS-Suffix für die VNets angeben.

## <a name="connecting-virtual-machines"></a>Verbinden von virtuellen Computern

### <a name="can-i-deploy-vms-to-a-vnet"></a>Können virtuelle Computer in einem VNet bereitgestellt werden?
Ja. Alle Netzwerkschnittstellen (NICs) einer VM, die mit dem Resource Manager-Bereitstellungsmodell bereitgestellt wurden, müssen mit einem VNet verbunden sein. VMs, die mit dem klassischen Bereitstellungsmodell bereitgestellt wurden, können optional mit einem VNet verbunden werden.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Wie lauten die unterschiedlichen Typen von IP-Adressen, die ich VMs zuweisen kann?
* **Privat:** Wird allen NICs jeder einzelnen VM zugewiesen. Die Adresse wird entweder mit der statischen oder dynamischen Methode zugewiesen. Private IP-Adressen werden aus dem Bereich zugewiesen, den Sie in den Subnetzeinstellungen Ihres VNet angegeben haben. Mit dem klassischen Bereitstellungsmodell bereitgestellten Ressourcen werden private IP-Adressen zugewiesen, selbst wenn keine Verbindung mit einem VNet besteht. Das Verhalten der Zuordnungsmethode unterscheidet sich abhängig davon, ob eine Ressource mit dem Resource Manager-Bereitstellungsmodell oder mit dem klassischen Bereitstellungsmodell bereitgestellt wurde: 

  - **Resource Manager:** Eine mit der dynamischen oder statischen Methode zugewiesene private IP-Adresse bleibt einem virtuellen Computer (Resource Manager) zugewiesen, bis die Ressource gelöscht wird. Der Unterschied besteht darin, dass bei Verwendung der statischen Methode Sie die zuzuweisende Adresse auswählen und bei Verwendung der dynamischen Methode Azure die Adresse auswählt. 
  - **Klassisch:** Eine mit der dynamischen Methode zugewiesene private IP-Adresse ändert sich möglicherweise, wenn ein virtueller Computer (klassisch) neu gestartet wird, nachdem er sich im Zustand „Beendet“ (Zuordnung aufgehoben) befand. Wenn Sie sicherstellen müssen, dass sich die private IP-Adresse einer Ressource, die über das klassische Bereitstellungsmodell bereitgestellt wurde, nie ändert, weisen Sie eine private IP-Adresse mit der statischen Methode zu.

* **Öffentlich:** Kann optional NICs von VMs zugewiesen werden, die über das Azure Resource Manager-Bereitstellungsmodell bereitgestellt wurden. Die Adresse kann mit der statischen oder der dynamischen Zuteilungsmethode zugewiesen werden. Alle VMs und Cloud Services-Rolleninstanzen, die über das klassische Bereitstellungsmodell bereitgestellt werden, sind in einem Clouddienst vorhanden, dem eine *dynamische* öffentliche virtuelle IP-Adresse (VIP) zugewiesen ist. Eine öffentliche *statische* IP-Adresse, die als [reservierte IP-Adresse](virtual-networks-reserved-public-ip.md) bezeichnet wird, kann optional als VIP zugewiesen werden. Sie können öffentliche IP-Adressen einzelnen VMs oder Cloud Services-Rolleninstanzen zuweisen, die mit dem klassischen Bereitstellungsmodell bereitgestellt wurden. Diese Adressen werden als [ILPIP-Adressen](virtual-networks-instance-level-public-ip.md) (Instance Level Public IP, öffentliche IP auf Instanzebene) bezeichnet und können dynamisch zugewiesen werden.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Kann ich eine private IP-Adresse für einen virtuellen Computer reservieren, den ich zu einem späteren Zeitpunkt erstelle?
Nein. Eine private IP-Adresse kann nicht reserviert werden. Wenn eine private IP-Adresse verfügbar ist, wird sie einem virtuellen Computer oder einer Rolleninstanz durch den DHCP-Server zugewiesen. Der virtuelle Computer kann der Computer sein, dem Sie die interne IP-Adresse zuweisen möchten. Es kann aber auch ein anderer Computer sein. Sie können aber die private IP-Adresse eines bereits erstellten virtuellen Computers in eine verfügbare private IP-Adresse ändern.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Ändern sich private IP-Adressen für virtuelle Computer in einem VNet?
Das ist unterschiedlich. Wenn der virtuelle Computer über Resource Manager bereitgestellt wurde, ändern sie sich nicht, unabhängig davon, ob die IP-Adresse mit der statischen oder der dynamischen Zuordnungsmethode zugewiesen wurde. Wenn der virtuelle Computer über das klassische Bereitstellungsmodell bereitgestellt wurde, können sich dynamische IP-Adressen ändern, wenn ein virtueller Computer gestartet wird, nachdem er im beendeten Zustand (Zuordnung aufgehoben) war. Die Adresse eines virtuellen Computers, der über eins der beiden Bereitstellungsmodelle bereitgestellt wurde, wird freigegeben, wenn der virtuelle Computer gelöscht wird.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Kann ich IP-Adressen den NICs im VM-Betriebssystem manuell zuordnen?
Ja, aber dies wird nur empfohlen, wenn es unbedingt notwendig ist, etwa beim Zuweisen mehrerer IP-Adressen zu einem virtuellen Computer. Ausführliche Informationen finden Sie unter [Zuweisen von mehreren IP-Adressen zu virtuellen Computern mithilfe des Azure-Portals](virtual-network-multiple-ip-addresses-portal.md#os-config). Falls sich die IP-Adresse ändert, die einer an einen virtuellen Computer angefügten Azure-NIC zugewiesen ist, und die IP-Adresse im VM-Betriebssystem sich davon unterscheidet, geht die Konnektivität zum virtuellen Computer verloren.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Was passiert mit meinen IP-Adressen, wenn ich einen Clouddienst-Bereitstellungsslot beende oder einen virtuellen Computer über das Betriebssystem herunterfahre?
Nichts. Die IP-Adressen (öffentliche VIP, öffentlich und privat) bleiben dem Clouddienst-Bereitstellungsslot bzw. der VM zugewiesen.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Können virtuelle Computer ohne erneute Bereitstellung zwischen Subnetzen in einem VNET verschoben werden?
Ja. Weitere Informationen finden Sie im Artikel [Verschieben eines virtuellen Computers oder einer Rolleninstanz in ein anderes Subnetz](virtual-networks-move-vm-role-to-subnet.md).

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Kann eine statische MAC-Adresse für einen virtuellen Computer konfiguriert werden?
Nein. Eine MAC-Adresse kann nicht statisch konfiguriert werden.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>Bleibt die MAC-Adresse eines virtuellen Computers nach ihrer Erstellung unverändert?
Ja. Die MAC-Adresse bleibt für eine VM so lange erhalten, bis sie gelöscht wird. Dabei spielt es keine Rolle, ob die VM über das Resource Manager- oder das klassische Bereitstellungsmodell bereitgestellt wurde. Bisher wurde die MAC-Adresse freigegeben, wenn die VM beendet (Aufhebung der Zuordnung) wurde, aber jetzt wird die MAC-Adresse auch dann beibehalten, wenn sich die VM im nicht zugeordneten Zustand befindet. Die MAC-Adresse bleibt der Netzwerkschnittstelle zugewiesen, bis die Netzwerkschnittstelle gelöscht oder die private IP-Adresse, die der primären IP-Konfiguration der primären Netzwerkschnittstelle zugewiesen ist, geändert wird. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Kann ich von einem virtuellen Computer in einem VNet eine Verbindung mit dem Internet herstellen?
Ja. Für alle in einem VNet bereitgestellten VMs und Cloud Services-Rolleninstanzen kann eine Verbindung mit dem Internet hergestellt werden.

## <a name="azure-services-that-connect-to-vnets"></a>Azure-Dienste mit Verbindung mit VNets

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Kann ich Azure App Service-Web-Apps mit einem VNet verwenden?
Ja. Sie können Web-Apps in einem VNet bereitstellen. Dazu verwenden Sie eine ASE (App Service-Umgebung), verbinden das Back-End Ihrer Apps mit Ihren VNets mit VNet-Integration und sperren den eingehenden Datenverkehr zu Ihrer App mit Dienstendpunkten. Weitere Informationen finden Sie in den folgenden Artikeln:

* [App Service-Netzwerkfunktionen](../app-service/networking-features.md)
* [Erstellen von Web-Apps in einer App Service-Umgebung](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrieren Ihrer App in ein Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Azure App Service – Zugriffseinschränkungen](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Können Cloud Services mit Web- und Workerrollen (PaaS) in einem VNet bereitgestellt werden?
Ja. Sie können Cloud Services-Rolleninstanzen (optional) in VNets bereitstellen. Hierfür geben Sie den Namen des VNet und die Rollen-/Subnetzzuordnungen im Netzwerkkonfigurationsabschnitt der Dienstkonfiguration an. Sie müssen keine Binärdateien aktualisieren.

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>Kann ich für eine VM-Skalierungsgruppe eine Verbindung mit einem VNet herstellen?
Ja. Sie müssen für eine VM-Skalierungsgruppe eine Verbindung mit einem VNet herstellen.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Gibt es eine vollständige Liste der Azure-Dienste, über die ich Ressourcen in einem VNET bereitstellen kann?
Ja. Ausführliche Informationen finden Sie unter [Integration virtueller Netzwerke für Azure-Dienste](virtual-network-for-azure-services.md).

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>Wie kann ich den Zugriff auf Azure PaaS-Ressourcen über ein VNET einschränken?

Ressourcen, die über ausgewählte Azure PaaS-Dienste (wie Azure Storage und Azure SQL-Datenbank) bereitgestellt werden, können den Netzwerkzugriff auf das VNET durch die Verwendung von virtuellen Netzwerkdienstendpunkten oder Azure Private Link einschränken. Weitere Informationen finden Sie in der [Übersicht über VNET-Dienstendpunkte](virtual-network-service-endpoints-overview.md) und der [Übersicht über Azure Private Link](../private-link/private-link-overview.md).

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Können Dienste in und aus VNets verschoben werden?
Nein. Sie können Dienste nicht in oder aus VNets verschieben. Wenn Sie eine Ressource in ein anderes VNET verschieben möchten, müssen Sie die Ressource löschen und neu bereitstellen.

## <a name="security"></a>Sicherheit

### <a name="what-is-the-security-model-for-vnets"></a>Welches Sicherheitsmodell wird für VNets verwendet?
VNETs werden von anderen VNETs und von anderen in der Azure-Infrastruktur gehosteten Diensten isoliert ausgeführt. Die Vertrauensstellungsgrenze entspricht der Begrenzung des VNet.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Kann ich den Flow von eingehendem oder ausgehendem Datenverkehr auf Ressourcen mit VNet-Verbindung beschränken?
Ja. Sie können [Netzwerksicherheitsgruppen](security-overview.md) auf einzelne Subnetze in einem VNet, an ein VNet angefügte NICs oder beides anwenden.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Kann ich zwischen Ressourcen mit VNet-Verbindung eine Firewall einrichten?
Ja. Sie können ein [virtuelles Netzwerkgerät für eine Firewall](https://azure.microsoft.com/marketplace/?term=firewall) von mehreren Anbietern über den Azure Marketplace bereitstellen.

### <a name="is-there-information-available-about-securing-vnets"></a>Sind Informationen zum Schützen von VNets verfügbar?
Ja. Ausführliche Informationen finden Sie im Artikel [Die Netzwerksicherheit in Azure im Überblick](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="do-virtual-networks-store-customer-data"></a>Werden in virtuellen Netzwerken Kundendaten gespeichert?
Nein. In virtuellen Netzwerken werden keine Kundendaten gespeichert. 

## <a name="apis-schemas-and-tools"></a>APIs, Schemas und Tools

### <a name="can-i-manage-vnets-from-code"></a>Können VNets programmgesteuert verwaltet werden?
Ja. Sie können REST-APIs für VNETs im Rahmen des [Azure Resource Manager-Bereitstellungsmodells](/rest/api/virtual-network) und des [klassischen Bereitstellungsmodells](https://go.microsoft.com/fwlink/?LinkId=296833) verwenden.

### <a name="is-there-tooling-support-for-vnets"></a>Sind Tools zur Unterstützung von VNets verfügbar?
Ja. Weitere Informationen zur Verwendung von folgenden Tools:
- Azure-Portal: Bereitstellen von VNets über das [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network)- und [klassische](virtual-networks-create-vnet-classic-pportal.md) Bereitstellungsmodell.
- PowerShell: Verwalten von VNets, die über das [Resource Manager](/powershell/module/az.network)- und [klassische](/powershell/module/servicemanagement/azure.service/?view=azuresmps-3.7.0) Bereitstellungsmodell bereitgestellt werden.
- Azure-Befehlszeilenschnittstelle (Command-Line Interface, CLI) zum Bereitstellen und Verwalten von VNETs, die über das [Resource Manager-Modell](/cli/azure/network/vnet) und das [klassische](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-resources) Bereitstellungsmodell bereitgestellt werden  

## <a name="vnet-peering"></a>VNet-Peering

### <a name="what-is-vnet-peering"></a>Was ist VNET-Peering?
VNET-Peering (das Peering virtueller Netzwerke) ermöglicht Ihnen das Verbinden von virtuellen Netzwerken. Über eine VNET-Peeringverbindung zwischen virtuellen Netzwerken können Sie Datenverkehr zwischen diesen privat über IPv4-Adressen weiterleiten. Virtuelle Computer in den mittels Peering verknüpften VNETs können miteinander kommunizieren, als ob sie sich im gleichen Netzwerks befinden. Diese virtuellen Netzwerke können sich in derselben oder in unterschiedlichen Regionen (dann auch als globales VNET-Peering bezeichnet) befinden. VNET-Peeringverbindungen können auch über mehrere Azure-Abonnements hinweg erstellt werden.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Kann ich eine Peeringverbindung mit einem VNET in einer anderen Region herstellen?
Ja. Globales VNET-Peering ermöglicht Ihnen das Peering mit VNETs in unterschiedlichen Regionen. Globales VNet-Peering ist in allen öffentlichen Azure-Regionen, China-Cloudregionen und Government-Cloudregionen verfügbar. Das globale Peering von öffentlichen Azure-Regionen in nationale Cloudregionen ist nicht möglich.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>Welche Einschränkungen gibt es im Zusammenhang mit globalem VNET-Peering und Load Balancern?
Wenn die beiden virtuellen Netzwerke in zwei verschiedenen Regionen mittels Peering über das globale VNET-Peering verbunden sind, können Sie über die Front-End-IP des Load Balancer keine Verbindung mit Ressourcen herstellen, die sich hinter einem Load Balancer im Tarif „Basic“ befinden. Diese Einschränkung gilt nicht für einen Load Balancer Standard.
Die folgenden Ressourcen können Load Balancer im Tarif „Basic“ verwenden, d. h. Sie können sie nicht über die Front-End-IP-Adresse des Load Balancer über globales VNET-Peering erreichen. Sie können jedoch das globale VNET-Peering verwenden, um die Ressourcen direkt über ihre privaten VNET-IP-Adressen zu erreichen, falls zulässig. 
- VMs hinter Basic-Load Balancern
- VM-Skalierungsgruppen mit Basic-Load Balancern 
- Redis Cache 
- Application Gateway (v1) SKU
- Service Fabric
- API Management
- Active Directory Domain Service (ADDS)
- Logic Apps
- HDInsight
-   Azure Batch
- App Service-Umgebung

Sie können sich mit diesen Ressourcen über ExpressRoute oder VNET-zu-VNET über VNet-Gateways verbinden.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Kann ich VNET-Peering aktivieren, wenn meine virtuellen Netzwerke zu Abonnements in verschiedenen Azure Active Directory-Mandanten gehören?
Ja. Es ist möglich, VNET-Peering (lokal oder global) einzurichten, wenn Ihre Abonnements zu verschiedenen Azure Active Directory-Mandanten gehören. Dies kann über das Portal, mit PowerShell oder mit der CLI erfolgen.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>Meine VNET-Peeringverbindung befindet sich im Status *Initiiert* – warum kann ich keine Verbindung herstellen?
Wenn Ihre Peeringverbindung sich im Status *Initiiert* befindet, bedeutet dies, dass Sie nur einen Link erstellt haben. Damit eine Verbindung erfolgreich hergestellt werden kann, muss ein bidirektionaler Link erstellt werden. Um beispielsweise eine Peeringverbindung zwischen VNET A und VNET B herzustellen, muss ein Link von VNetA zu VNetB und von VNetB zu VNetA erstellt werden. Nach dem Erstellen beider Links ändert sich der Status in *Verbunden*.

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>Meine VNET-Peeringverbindung befindet sich im Zustand *Getrennt*. Warum kann ich keine Peeringverbindung herstellen?
Wenn Ihre VNET-Peeringverbindung den Zustand *Getrennt* aufweist, bedeutet dies, dass eine der erstellen Verknüpfungen gelöscht wurde. Um die Peeringverbindung erneut herzustellen, müssen Sie die Verknüpfung löschen und neu erstellen.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Kann ich eine Peeringverbindung meines VNETs mit einem VNET in einem anderen Abonnement herstellen?
Ja. Sie können Peeringverbindungen zwischen VNETs Abonnements und Regionen übergreifend herstellen.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Kann ich eine Peeringverbindung zwischen zwei VNETs mit übereinstimmenden oder sich überlappenden Adressbereichen herstellen?
Nein. Bei sich überlappenden Adressbereichen ist kein VNET-Peering möglich.

### <a name="can-i-peer-a-vnet-to-two-different-vnets-with-the-the-use-remote-gateway-option-enabled-on-both-the-peerings"></a>Kann ich ein VNET per Peering mit zwei verschiedenen VNETs koppeln, wenn die Option „Remotegateway verwenden“ für beide Peerings aktiviert ist?
Nein. Sie können die Option „Remotegateway verwenden“ nur bei einem Peering in einem der VNETs aktivieren.

### <a name="how-much-do-vnet-peering-links-cost"></a>Was kosten Links für das VNET-Peering?
Für das Erstellen einer VNET-Peeringverbindung fallen keine Gebühren an. Die Datenübertragung über Peeringverbindungen wird in Rechnung gestellt. [Siehe hier](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Wird VNET-Peeringdatenverkehr verschlüsselt?
Wenn Azure-Datenverkehr zwischen Rechenzentren (außerhalb physischer Grenzen, die nicht von Microsoft oder im Auftrag von Microsoft kontrolliert werden) bewegt wird, wird auf der zugrunde liegenden Netzwerkhardware eine [MACsec-Verschlüsselung der Sicherungsschicht](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit) verwendet.  Dies gilt für VNET-Peeringdatenverkehr.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Warum hat meine Peeringverbindung den Status *Getrennt*?
VNET-Peeringverbindungen wechseln in den Status *Getrennt*, wenn ein VNET-Peeringlink gelöscht wird. Sie müssen beide Links löschen, um erfolgreich eine Peeringverbindung wiederherzustellen.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Wenn ich eine Peeringverbindung zwischen VNetA und VNetB sowie VNetB und VNetC herstelle, bedeutet dies, dass eine Peeringverbindung zwischen VNetA und VNetC besteht?
Nein. Transitives Peering wird nicht unterstützt. Sie müssen hierzu eine Peeringverbindung zwischen VNetA und VNetC herstellen.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Gibt es Bandbreiteneinschränkungen für Peeringverbindungen?
Nein. Für VNET-Peering, ob lokal oder global, bestehen keine Bandbreiteneinschränkungen. Die Bandbreite wird nur durch die VM oder die Computeressource beschränkt.

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>Wie kann ich Probleme beim VNet-Peering beheben?
Hier ist eine [Anleitung zur Problembehandlung](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues), die Sie ausprobieren können.

## <a name="virtual-network-tap"></a>TAP eines virtuellen Netzwerks

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Welche Azure-Regionen sind für den TAP eines virtuellen Netzwerks verfügbar?
Die Vorschau des TAPs für virtuelle Netzwerke ist in allen Azure-Regionen verfügbar. Die überwachten Netzwerkschnittstellen, die TAP-Ressource des virtuellen Netzwerks und die Collector- oder Analyselösung müssen in der gleichen Region bereitgestellt werden.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>Unterstützt der TAP eines virtuellen Netzwerks Filterfunktionen für die gespiegelten Pakete?
Filterfunktionen werden in der Vorschauversion des TAP für ein virtuelles Netzwerk nicht unterstützt. Beim Hinzufügen einer TAP-Konfiguration zu einer Netzwerkschnittstelle wird eine Tiefenkopie des gesamten eingehenden und ausgehenden Datenverkehrs an das TAP-Ziel gestreamt.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Können einer überwachten Netzwerkschnittstelle mehrere TAP-Konfigurationen hinzugefügt werden?
Eine überwachte Netzwerkschnittstelle kann nur über eine TAP-Konfiguration verfügen. Überprüfen Sie die einzelne [Partnerlösung](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) auf die Möglichkeit zum Streamen mehrerer Kopien des TAP-Datenverkehrs an die Analysetools Ihrer Wahl.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>Kann dieselbe TAP-Ressource eines virtuellen Netzwerks Datenverkehr von überwachten Netzwerkschnittstellen in mehr als einem virtuellen Netzwerk aggregieren?
Ja. Dieselbe TAP-Ressource eines virtuellen Netzwerks kann zum Aggregieren von gespiegeltem Datenverkehr von überwachten Netzwerkschnittstellen in virtuellen Netzwerken mit Peering im gleichen Abonnement oder in einem anderen Abonnement verwendet werden. Die TAP-Ressource des virtuellen Netzwerks und der Lastenausgleich oder die Zielnetzwerk-Schnittstelle müssen sich im selben Abonnement befinden. Alle Abonnements müssen demselben Azure Active Directory-Mandanten zugeordnet sein.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Gibt es Überlegungen zur Leistung für Produktionsdatenverkehr, wenn ich eine TAP-Konfiguration für ein virtuelles Netzwerk an einer Netzwerkschnittstelle aktiviere?

Der TAP für virtuelle Netzwerke befindet sich in der Vorschau. Während der Vorschau gibt es keine Vereinbarung zum Servicelevel. Die Funktion darf nicht für Produktionsworkloads verwendet werden. Wenn die Netzwerkschnittstelle eines virtuellen Computers mit einer TAP-Konfiguration aktiviert ist, werden mit den Ressourcen auf dem Azure-Host, der dem virtuellen Computer für das Senden des Produktionsdatenverkehrs zugeordnet ist, auch die Spiegelungsfunktion ausgeführt und die gespiegelten Pakete versendet. Wählen Sie die richtige Größe für einen virtuellen [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- oder [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Computer aus, um sicherzustellen, dass für den virtuellen Computer genügend Ressourcen zum Senden des Produktionsdatenverkehrs und des gespiegelten Datenverkehrs verfügbar sind.

### <a name="is-accelerated-networking-for-linux-or-windows-supported-with-virtual-network-tap"></a>Wird beschleunigter Netzwerkbetrieb für [Linux](create-vm-accelerated-networking-cli.md) oder [Windows](create-vm-accelerated-networking-powershell.md) mit TAP eines virtuellen Netzwerks unterstützt?

Sie haben die Möglichkeit, eine TAP-Konfiguration an einer Netzwerkschnittstelle hinzuzufügen, die einem virtuellen Computer angefügt ist, für den beschleunigter Netzwerkbetrieb aktiviert ist. Die Leistung und Latenz auf dem virtuellen Computer werden durch das Hinzufügen einer TAP-Konfiguration jedoch beeinträchtigt, da die Auslagerung für Spiegelungsdatenverkehr vom beschleunigten Netzwerkbetrieb in Azure derzeit nicht unterstützt wird.

## <a name="virtual-network-service-endpoints"></a>VNET-Dienstendpunkte

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Was ist die richtige Reihenfolge der Vorgänge zum Einrichten von Endpunkten für einen Azure-Dienst?
Das Schützen einer Azure-Dienstressource über Dienstendpunkte erfolgt in zwei Schritten:
1. Aktivieren von Dienstendpunkten für den Azure-Dienst
2. Einrichten von VNET-ACLs für den Azure-Dienst

Der erste Schritt ist ein netzwerkseitiger Vorgang, und der zweite Schritt ist ein Vorgang auf der Seite der Dienstressource. Beide Schritte können durch denselben oder verschiedene Administratoren erfolgen, basierend auf den Azure RBAC-Berechtigungen der Administratorrolle. Es wird empfohlen, zunächst Dienstendpunkte für Ihr virtuelles Netzwerk zu aktivieren, bevor Sie VNET-ACLs auf der Seite des Azure-Diensts einrichten. Die Schritte müssen daher in der oben angegebenen Reihenfolge ausgeführt werden, um VNET-Dienstendpunkte einzurichten.

>[!NOTE]
> Beide der oben beschriebenen Verfahren müssen ausgeführt werden, bevor Sie den Zugriff auf den Azure-Dienst auf das zulässige VNET und Subnetz einschränken können. Wenn Sie nur die Dienstendpunkte für den Azure-Dienst auf der Netzwerkseite aktivieren, erhalten Sie noch keinen eingeschränkten Zugriff. Sie müssen zusätzlich auch VNET-ACLs auf der Seite des Azure-Diensts festlegen.

Bestimmte Dienste (z.B. SQL und Cosmos DB) lassen Ausnahmen für die oben angegebene Reihenfolge über das Flag **IgnoreMissingVnetServiceEndpoint** zu. Sobald das Flag auf **True** festgelegt wurde, können vor dem Einrichten der Dienstendpunkte auf der Netzwerkseite VNET-ACLs auf der Seite des Azure-Diensts festgelegt werden. Azure-Dienste stellen dieses Flag bereit, um Kunden in Fällen zu unterstützen, bei denen Firewalls mit spezifischen IP-Adressen für Azure-Dienste konfiguriert wurden und das Aktivieren der Dienstendpunkte auf der Netzwerkseite zu Verbindungsausfällen führen kann, da die Quell-IP-Adresse von einer öffentlichen IPv4-Adresse in eine private Adresse geändert wird. Durch Einrichten der VNET-ACLs auf der Azure-Dienstseite vor dem Festlegen von Dienstendpunkten auf der Netzwerkseite können Sie Verbindungsausfälle vermeiden.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Befinden sich alle Azure-Dienste in dem vom Kunden bereitgestellten virtuellen Azure-Netzwerk? Wie funktioniert der VNET-Dienstendpunkt mit Azure-Diensten?

Nein, es befinden sich nicht alle Azure-Dienste im virtuellen Netzwerk des Kunden. Bei einem Großteil der Azure-Datendienste wie Azure Storage, Azure SQL und Azure Cosmos DB handelt es sich um mehrmandantenfähige Dienste, auf die über öffentliche IP-Adressen zugegriffen werden kann. [Hier](virtual-network-for-azure-services.md) erfahren Sie mehr über die Integration virtueller Netzwerke für Azure-Dienste. 

Wenn Sie die Funktion für VNET-Dienstendpunkte (Aktivieren eines VNET-Dienstendpunkts auf der Netzwerkseite und Einrichten der entsprechenden VNET-ACLs auf der Azure-Dienstseite) verwenden, wird der Zugriff auf Azure-Dienste auf zulässige VNETs und Subnetze beschränkt.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>Wie stellt der VNET-Dienstendpunkt Sicherheit bereit?

Die Funktion für VNET-Dienstendpunkte (Aktivieren eines VNET-Dienstendpunkts auf der Netzwerkseite und Einrichten der entsprechenden VNET-ACLs auf der Azure-Dienstseite) beschränkt den Zugriff für Azure-Dienste auf das zulässige VNET und Subnetz und stellt somit Sicherheit auf Netzwerkebene und Isolierung des Azure-spezifischen Datenverkehrs bereit. Sämtlicher über VNET-Dienstendpunkte geleiteter Datenverkehr durchläuft den Microsoft-Backbone, dadurch wird eine andere Ebene der Isolation über das öffentliche Internet bereitgestellt. Darüber hinaus können Kunden den Zugriff vom öffentlichen Internet auf die Azure-Dienstressourcen auch vollständig entfernen und nur Datenverkehr vom eigenen virtuellen Netzwerk über eine Kombination aus IP-Firewall und VNET-ACLs zulassen, sodass die Azure-Dienstressourcen vor nicht autorisierten Zugriffen geschützt sind.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>Was schützt der VNET-Dienstendpunkt – VNET-Ressourcen oder Azure-Dienste?
VNET-Dienstendpunkte helfen dabei, Azure-Dienstressourcen zu schützen. VNET-Ressourcen werden über Netzwerksicherheitsgruppen (NSGs) geschützt.

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Fallen Kosten für die Verwendung von VNET-Dienstendpunkten an?

Nein, es fallen keinerlei zusätzliche Kosten für die Verwendung von VNET-Dienstendpunkten an.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>Kann ich VNET-Dienstendpunkte aktivieren und VNET-ACLs einrichten, wenn das virtuelle Netzwerk und die Azure-Dienstressourcen zu unterschiedlichen Abonnements gehören?

Ja, das ist möglich. Virtuelle Netzwerke und Ressourcen von Azure-Diensten können sich in demselben oder in unterschiedlichen Abonnements befinden. Es gilt lediglich die Voraussetzung, dass sich das virtuelle Netzwerk und die Azure-Dienstressourcen zu demselben Active Directory-Mandanten gehören.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>Kann ich VNET-Dienstendpunkte aktivieren und VNET-ACLs einrichten, wenn das virtuelle Netzwerk und die Azure-Dienstressourcen zu unterschiedlichen AD-Mandanten gehören?
Ja. Dies ist möglich, wenn Sie Dienstendpunkte für Azure Storage und Azure Key Vault verwenden. Für die restlichen Dienste werden VNET-Dienstendpunkte und VNET-ACLs für AD-Mandanten nicht übergreifend unterstützt.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>Kann die IP-Adresse eines lokalen Geräts, das über ein Azure Virtual Network-Gateway (VPN) oder ein ExpressRoute-Gateway verbunden ist, über VNET-Dienstendpunkte auf einen Azure-PaaS-Dienst zugreifen?
Standardmäßig sind Azure-Dienstressourcen, die auf virtuelle Netzwerke beschränkt und so geschützt sind, über lokale Netzwerke nicht erreichbar. Wenn Sie Datenverkehr aus der lokalen Umgebung zulassen möchten, müssen Sie auch öffentliche IP-Adressen (meist NAT) aus der lokalen Umgebung bzw. per ExpressRoute zulassen. Diese IP-Adressen können über die Konfiguration der IP-Firewall für die Azure-Dienstressourcen hinzugefügt werden.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>Kann ich mit dem VNET-Dienstendpunktfeature Azure-Dienste in mehreren Subnetzen innerhalb eines virtuellen Netzwerks oder mehrerer virtueller Netzwerke verwenden?
Aktivieren Sie zum Schützen von Azure-Diensten in mehreren Subnetzen innerhalb eines virtuellen Netzwerks oder mehrerer virtueller Netzwerke netzwerkseitige Dienstendpunkte in den einzelnen Subnetzen unabhängig voneinander, und schützen Sie Azure-Dienstressourcen dann in allen diesen Subnetzen, indem Sie geeignete VNET-ACLs auf der Azure-Dienstseite einrichten.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>Wie kann ich ausgehenden Datenverkehr aus einem virtuellen Netzwerk an Azure-Dienste filtern und weiterhin Dienstendpunkte verwenden?
Wenn Sie den Datenverkehr, der aus dem virtuellen Netzwerk an einen Azure-Dienst fließen soll, untersuchen und filtern möchten, können Sie in diesem virtuellen Netzwerk ein virtuelles Netzwerkgerät bereitstellen. Anschließend können Sie Dienstendpunkte auf das Subnetz anwenden, in dem das virtuelle Netzwerkgerät bereitgestellt wurde, und Ressourcen des Azure-Diensts mithilfe von VNET-ACLs auf dieses Subnetz beschränken. Dieses Szenario kann auch hilfreich sein, wenn Sie den Zugriff auf den Azure-Dienst aus Ihrem virtuellen Netzwerk per Filterung durch ein virtuelles Netzwerkgerät nur auf bestimmte Azure-Ressourcen beschränken möchten. Weitere Informationen finden Sie unter [egress with network virtual appliances](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha) (Ausgehender Datenverkehr mit virtuellen Netzwerkgeräten).

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>Was passiert, wenn von außerhalb des VNET auf ein Azure-Dienstkonto zugegriffen wird, für das eine VNET-Zugriffssteuerungsliste (ACL) aktiviert ist?
Es wird einer der HTTP-Fehler 403 oder 404 zurückgegeben.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>Können Subnetze eines virtuellen Netzwerks, die in verschiedenen Regionen erstellt wurden, auf ein Azure-Dienstkonto in einer anderen Region zugreifen? 
Ja, für die meisten Azure-Dienste können in unterschiedlichen Regionen erstellte virtuelle Netzwerke über die VNET-Dienstendpunkte auf Azure-Dienste in einer anderen Region zugreifen. Wenn sich ein Azure Cosmos DB-Konto z.B. in einer der Regionen „USA, Westen“ oder „USA, Osten“ befindet und sich virtuelle Netzwerke in mehreren Regionen befinden, kann das virtuelle Netzwerk auf Azure Cosmos DB zugreifen. Storage und SQL sind Ausnahmen, die ihrer Natur nach regional sind. Sowohl das virtuelle Netzwerk als auch der Azure-Dienst müssen sich hierbei in derselben Region befinden.
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Kann ein Azure-Dienst sowohl eine VNET-ACL als auch eine IP-Firewall aufweisen?
Ja, eine VNET-ACL und eine IP-Firewall können gleichzeitig vorhanden sein. Beide Features ergänzen einander, um Isolation und Sicherheit zu gewährleisten.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>Was passiert beim Löschen eines virtuellen Netzwerks oder Subnetzes, das als Dienstendpunkt für Azure-Dienste festgelegt ist?
Das Löschen von VNETs und von Subnetzen sind unabhängige Vorgänge, die auch dann unterstützt werden, wenn Dienstendpunkte für Azure-Dienste aktiviert sind. Wenn für Azure-Dienste VNETs eingerichtet sind, werden beim Löschen eines VNET oder Subnetzes, das als VNET-Dienstendpunkt aktiviert wurde, die mit diesem Azure-Dienst verknüpften VNET-ACL-Informationen für diese VNETs und Subnetze deaktiviert.
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>Was passiert, wenn ein Azure-Dienstkonto, für das ein VNET-Dienstendpunkt aktiviert ist, gelöscht wird?
Das Löschen eines Azure-Dienstkontos ist ein unabhängiger Vorgang, der auch dann unterstützt wird, wenn der Dienstendpunkt auf der Netzwerkseite aktiviert und VNET-ACLs auf der Azure-Dienstseite eingerichtet sind. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>Was passiert mit der IP-Quelladresse einer Ressource (z.B. eines virtuellen Computers in einem Subnetz), für die ein VNET-Dienstendpunkt aktiviert ist?
Wenn VNET-Dienstendpunkte aktiviert sind, verwenden die IP-Quelladressen der Ressourcen im Subnetz des virtuellen Netzwerks keine öffentlichen IPv4-Adressen mehr, sondern private IP-Adressen des virtuellen Azure-Netzwerks, um Datenverkehr an den Azure-Dienst zu leiten. Beachten Sie, dass dies zu Fehlern bei bestimmten IP-Firewalls, die zuvor in den Azure-Diensten für öffentliche IPv4-Adressen festgelegt wurden, führen kann. 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>Hat die Dienstendpunktroute immer Vorrang?
Dienstendpunkte fügen eine Systemroute hinzu, die Vorrang vor BGP-Routen hat und eine optimale Weiterleitung für den Dienstendpunkt-Datenverkehr ermöglicht. Dienstendpunkte leiten den Datenverkehr der Dienste immer direkt aus Ihrem virtuellen Netzwerk an den Dienst im Microsoft Azure-Backbonenetzwerk weiter. Weitere Informationen zum Auswählen einer Route durch Azure finden Sie unter [Datenverkehrsrouting für virtuelle Azure-Netzwerke](virtual-networks-udr-overview.md).
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Wie arbeiten NSGs in einem Subnetz mit Dienstendpunkten zusammen?
Um den Azure-Dienst zu erreichen, müssen NSGs ausgehende Verbindungen zulassen. Wenn Ihre NSGs für sämtlichen ausgehenden Internet-Datenverkehr geöffnet sind, sollte der Datenverkehr für den Dienstendpunkt funktionieren. Sie können den ausgehenden Datenverkehr über Diensttags auch auf Dienst-IP-Adressen beschränken.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Welche Berechtigungen benötige ich, um Dienstendpunkte einzurichten?
Dienstendpunkte können in einem virtuellen Netzwerken unabhängig durch einen Benutzer konfiguriert werden, der Schreibzugriff auf das virtuelle Netzwerk hat. Um Azure-Dienstressourcen in einem VNET zu sichern, muss der Benutzer für die hinzuzufügenden Subnetze über die Berechtigung für **„Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action“** verfügen. Diese Berechtigung ist standardmäßig in den integrierten Dienstadministratorrollen enthalten und kann durch Erstellen von benutzerdefinierten Rollen geändert werden. Erfahren Sie mehr über integrierte Rollen und das Zuweisen bestimmter Berechtigungen zu [benutzerdefinierten Rollen](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json).
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Kann ich über VNET-Dienstendpunkte Datenverkehr aus virtuellen Netzwerken an Azure-Dienste filtern, sodass nur bestimmte Azure-Dienstressourcen zugelassen werden? 

Richtlinien für Dienstendpunkte in virtuellen Azure-Netzwerken (VNETs) ermöglichen es Ihnen, virtuellen Netzwerkdatenverkehr an Azure-Dienste zu filtern, sodass nur bestimmte Azure-Dienstressourcen über Dienstendpunkte zugelassen werden. Endpunktrichtlinien bieten eine differenzierte Zugriffssteuerung für virtuellen Netzwerkdatenverkehr an Azure-Dienste. Weitere Informationen zu den Richtlinien für Dienstendpunkte finden Sie [hier](virtual-network-service-endpoint-policies-overview.md).

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>Unterstützt Azure Active Directory (Azure AD) VNet-Dienstendpunkte?

Azure Active Directory (Azure AD) unterstützt nativ keine Dienstendpunkte. Eine vollständige Liste der Azure-Dienste, die VNet-Dienstendpunkte unterstützen, finden Sie [hier](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Beachten Sie, dass das „Microsoft.AzureActiveDirectory“-Tag, das in den dienstunterstützenden Dienstendpunkten aufgeführt ist, nur für die Unterstützung von Dienstendpunkten für ADLS Gen 1 verwendet wird. Bitte beachten Sie für ADLS Gen1, dass die Integration virtueller Netzwerke für Azure Data Lake Storage Gen1 die Sicherheit von VNET-Dienstendpunkten zwischen Ihrem virtuellen Netzwerk und Azure Active Directory (Azure AD) nutzt, um zusätzliche Sicherheitsansprüche im Zugriffstoken zu generieren. Diese Ansprüche werden dann genutzt, um Ihr virtuelles Netzwerk mit Ihrem Data Lake Storage Gen1-Konto zu authentifizieren und den Zugriff zu ermöglichen. Erfahren Sie mehr über die [VNET-Integration für Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Gibt es eine Grenze für die Anzahl der VNET-Dienstendpunkte, die ich in meinem VNET einrichten kann?
Für die Gesamtzahl von VNET-Dienstendpunkten in einem virtuellen Netzwerk gilt keine Beschränkung. Für die Ressource eines Azure-Diensts (z.B. ein Azure Storage-Konto) können Dienste Beschränkungen in Bezug auf die Anzahl von Subnetzen erzwingen, die zum Schützen der Ressource verwendet werden. Die folgende Tabelle zeigt einige Beispielgrenzwerte: 

|Azure-Dienst| Grenzwerte für VNET-Regeln|
|---|---|
|Azure Storage| 100|
|Azure SQL| 128|
|Azure Synapse Analytics|   128|
|Azure Key Vault|    127|
|Azure Cosmos DB|   64|
|Azure Event Hub|   128|
|Azure-Servicebus| 128|
|Azure Data Lake Store V1|  100|
 
>[!NOTE]
> Die Grenzwerte unterlegen Änderungen im alleinigen Ermessen des Azure-Diensts. Details zu den einzelnen Diensten finden Sie in der jeweiligen Dokumentation. 




  



