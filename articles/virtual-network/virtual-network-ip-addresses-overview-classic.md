---
title: IP-Adresstypen in Azure (klassisch)
titlesuffix: Azure Virtual Network
description: Erfahren Sie mehr zu öffentlichen und privaten IP-Adressen (klassisch) in Azure.
services: virtual-network
documentationcenter: na
author: genlin
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: genli
ms.openlocfilehash: 9e7a5772dd1e10abf43eddf0548833d625ecfb24
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60742119"
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>IP-Adresstypen und Zuordnungsmethoden (klassisch) in Azure
Sie können Azure-Ressourcen IP-Adressen zuweisen, um die Kommunikation mit anderen Azure-Ressourcen, Ihrem lokalen Netzwerk und dem Internet zu ermöglichen. In Azure können zwei Arten von IP-Adressen verwendet werden: öffentliche und private.

Öffentliche IP-Adressen werden für die Kommunikation mit dem Internet verwendet. Hierzu zählen auch öffentliche Azure-Dienste.

Private IP-Adressen werden für die Kommunikation innerhalb eines virtuellen Azure-Netzwerks (VNet), Clouddiensts und Ihres lokalen Netzwerks verwendet, wenn Sie Ihr Netzwerk mithilfe eines VPN-Gateways oder einer ExpressRoute-Verbindung auf Azure ausdehnen.

> [!IMPORTANT]
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen:  [das Resource Manager-Modell und das klassische Bereitstellungsmodell](../resource-manager-deployment-model.md).  Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung von Resource Manager. Informationen zu IP-Adressen in Resource Manager finden Sie im Artikel [IP-Adressen](virtual-network-ip-addresses-overview-arm.md).

## <a name="public-ip-addresses"></a>Öffentliche IP-Adressen
Öffentliche IP-Adressen ermöglichen Azure-Ressourcen die Kommunikation mit dem Internet und öffentlichen Azure-Diensten wie [Azure Cache for Redis](https://azure.microsoft.com/services/cache/), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [SQL-Datenbanken](../sql-database/sql-database-technical-overview.md) und [Azure Storage](../storage/common/storage-introduction.md).

Eine öffentliche IP-Adresse wird folgenden Ressourcentypen zugeordnet:

* Clouddienste
* Virtuelle IaaS-Maschinen (IaaS-VMs)
* PaaS-Rolleninstanzen
* VPN-Gateways
* Anwendungsgateways

### <a name="allocation-method"></a>Zuordnungsmethode
Die öffentliche IP-Adresse einer Azure-Ressource wird bei Bedarf *dynamisch* aus einem Pool mit verfügbaren öffentlichen IP-Adressen des Ressourcenerstellungsorts zugewiesen. Bei Beendigung der Ressource wird die IP-Adresse wieder freigegeben. Bei einem Clouddienst tritt dieser Fall auf, wenn alle Rolleninstanzen angehalten werden. Dies lässt sich durch Verwendung einer *statischen* (reservierten) IP-Adresse verhindern. (Weitere Informationen finden Sie unter [Clouddienste](#cloud-services).)

> [!NOTE]
> Die Liste mit den IP-Adressbereichen, aus denen Azure-Ressourcen öffentliche IP-Adressen zugewiesen werden, finden Sie unter [IP-Bereiche des Azure-Rechenzentrums](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

### <a name="dns-hostname-resolution"></a>DNS-Hostnamenauflösung
Beim Erstellen eines Clouddiensts oder einer IaaS-VM müssen Sie einen für alle Ressourcen in Azure eindeutigen DNS-Clouddienstnamen angeben. Dadurch wird „ *DNS-Name*.cloudapp.net“ in den von Azure verwalteten DNS-Servern der öffentlichen IP-Adresse der Ressource zugeordnet. Wenn Sie also beispielsweise einen Clouddienst mit dem DNS-Clouddienstnamen **contoso** erstellen, wird der vollqualifizierte Domänenname (Fully-Qualified Domain Name, FQDN) **contoso.cloudapp.net** zu einer öffentlichen IP-Adresse (VIP) des Clouddiensts aufgelöst. Mit diesem FQDN können Sie einen benutzerdefinierten CNAME-Domäneneintrag mit Verweis auf die öffentliche IP-Adresse in Azure erstellen.

### <a name="cloud-services"></a>Clouddienste
Ein Clouddienst besitzt immer eine öffentliche IP-Adresse. Diese wird als virtuelle IP-Adresse (VIP) bezeichnet. In einem Clouddienst können Sie Endpunkte erstellen, um verschiedene VIP-Ports den internen Ports von VMs und Rolleninstanzen innerhalb des Clouddiensts zuzuordnen. 

Ein Clouddienst kann mehrere IaaS-VMs oder PaaS-Rolleninstanzen enthalten, die alle über die gleiche Clouddienst-VIP verfügbar gemacht werden. Sie können [einem Clouddienst auch mehrere VIPs zuweisen](../load-balancer/load-balancer-multivip.md), um entsprechende Szenarien (etwa eine mehrinstanzenfähige Umgebung mit SSL-basierten Websites) zu realisieren.

Durch Verwendung einer *statischen* öffentlichen IP-Adresse (einer so genannten [reservierten IP-Adresse](virtual-networks-reserved-public-ip.md)) können Sie sicherstellen, dass die öffentliche IP-Adresse eines Clouddiensts auch bei Beendigung aller Rolleninstanzen unverändert bleibt. Eine statische (reservierte) IP-Ressource kann an einem bestimmten Ort erstellt und einem beliebigen Clouddienst an diesem Ort zugewiesen werden. Die reservierte IP-Adresse stammt aus einem Pool verfügbarer IP-Adressen für den Erstellungsort und kann nicht direkt angegeben werden. Diese IP-Adresse wird erst freigegeben, wenn Sie sie explizit löschen.

Statische (reservierte) öffentliche IP-Adressen werden häufig verwendet, wenn für den Clouddienst Folgendes gilt:

* Der Clouddienst erfordert die Einrichtung von Firewallregeln durch Endbenutzer.
* Der Clouddienst ist abhängig von einer externen DNS-Namensauflösung, und eine dynamische IP-Adresse würde eine Aktualisierung der A-Einträge erfordern.
* Der Clouddienst nutzt externe Webdienste mit einem IP-basierten Sicherheitsmodell.
* Der Clouddienst verwendet mit einer IP-Adresse verknüpfte SSL-Zertifikate.

> [!NOTE]
> Wenn Sie eine klassische VM erstellen, wird von Azure ein Container- *Clouddienst* erstellt, der über eine virtuelle IP-Adresse (VIP) verfügt. Wenn die Erstellung über das Portal durchgeführt wird, wird vom Portal ein standardmäßiger RDP- oder SSH- *Endpunkt* konfiguriert, sodass Sie eine Verbindung mit der VM über die Clouddienst-VIP herstellen können. Diese Clouddienst-VIP kann reserviert werden, sodass praktisch eine reservierte IP-Adresse für die Verbindungsherstellung mit der VM bereitgestellt wird. Sie können weitere Ports öffnen, indem Sie mehr Endpunkte konfigurieren.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS-VMs und PaaS-Rolleninstanzen
Sie können eine öffentliche IP-Adresse direkt einer IaaS-[VM](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oder einer PaaS-Rolleninstanz innerhalb eines Clouddiensts zuweisen. Diese wird als öffentliche IP auf Instanzebene (Instance-Level Public IP,[ILPIP](virtual-networks-instance-level-public-ip.md)) bezeichnet. Diese öffentliche IP-Adresse kann nur dynamisch sein.

> [!NOTE]
> Dies ist ein Unterschied gegenüber der VIP des Clouddiensts. Dabei handelt es sich um einen Container für IaaS-VMs oder PaaS-Rolleninstanzen, da ein Clouddienst mehrere IaaS-VMs oder PaaS-Rolleninstanzen enthalten kann, die allesamt über die gleiche Clouddienst-VIP verfügbar gemacht werden.
> 
> 

### <a name="vpn-gateways"></a>VPN-Gateways
Mit einem [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) lässt sich eine Verbindung zwischen einem Azure-VNet und anderen Azure-VNets oder lokalen Netzwerken herstellen. Einem VPN-Gateway wird *dynamisch*eine öffentliche IP-Adresse zugewiesen, was die Kommunikation mit dem Remotenetzwerk ermöglicht.

### <a name="application-gateways"></a>Anwendungsgateways
Ein Azure [Anwendungsgateway](../application-gateway/application-gateway-introduction.md) kann im Rahmen eines Layer7-Lastenausgleichs zur Weiterleitung von HTTP-basiertem Netzwerkdatenverkehr verwendet werden. Dem Anwendungsgateway wird *dynamisch*eine öffentliche IP-Adresse zugewiesen, die dann als Lastenausgleich-VIP fungiert.

### <a name="at-a-glance"></a>Auf einen Blick
Die folgende Tabelle gibt Aufschluss über die einzelnen Ressourcentypen, die möglichen Zuordnungsmethoden (dynamisch/statisch) sowie über die Möglichkeit, mehrere öffentliche IP-Adressen zuzuweisen:

| Resource | Dynamisch | statischen | Mehrere IP-Adressen |
| --- | --- | --- | --- |
| Clouddienst |Ja |Ja |Ja |
| IaaS-VM oder PaaS-Rolleninstanz |Ja |Nein |Nein |
| VPN-Gateway |Ja |Nein |Nein |
| Anwendungsgateway |Ja |Nein |Nein |

## <a name="private-ip-addresses"></a>Private IP-Adressen
Private IP-Adressen ermöglichen Azure-Ressourcen die Kommunikation mit anderen Ressourcen in einem Clouddienst oder [virtuellen Netzwerk](virtual-networks-overview.md)(VNet) oder mit einem lokalen Netzwerk (über ein VPN-Gateway oder eine ExpressRoute-Verbindung) ohne Verwendung einer über das Internet erreichbaren IP-Adresse.

Im klassischen Bereitstellungsmodell von Azure kann eine private IP-Adresse den folgenden Azure-Ressourcen zugewiesen werden:

* IaaS-VMs und PaaS-Rolleninstanzen
* Interner Lastenausgleich
* Anwendungsgateway

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS-VMs und PaaS-Rolleninstanzen
Virtuelle Maschinen (VMs), die mit dem klassischen Bereitstellungsmodell erstellt werden, werden (ähnlich wie PaaS-Rolleninstanzen) immer in einem Clouddienst platziert. Das Verhalten privater IP-Adressen ist bei diesen Ressourcen daher ähnlich.

Ein Clouddienst kann auf zwei Arten bereitgestellt werden:

* Als *eigenständiger* Clouddienst (also nicht innerhalb eines virtuellen Netzwerks).
* Als Teil eines virtuellen Netzwerks.

#### <a name="allocation-method"></a>Zuordnungsmethode
Bei einem *eigenständigen* Clouddienst wird den Ressourcen *dynamisch* eine private IP-Adresse aus dem Adressbereich privater IP-Adressen des Azure-Datencenters zugewiesen. Die IP-Adresse kann nur für die Kommunikation mit anderen VMs verwendet werden, die sich innerhalb des gleichen Clouddiensts befinden. Wenn die Ressource beendet und wieder gestartet wird, kann sich diese IP-Adresse ändern.

Bei Bereitstellung eines Clouddiensts innerhalb eines virtuellen Netzwerks erhalten die Ressourcen private IP-Adressen aus dem Adressbereich der zugeordneten Subnetze (gemäß Angabe in der Netzwerkkonfiguration). Diese privaten IP-Adressen können für die Kommunikation zwischen allen VMs des VNets verwendet werden.

Darüber hinaus wird bei Clouddiensten in einem VNet standardmäßig eine private IP-Adresse *dynamisch* (per DHCP) zugewiesen. Diese kann sich ändern, wenn die Ressource beendet und wieder gestartet wird. Wenn die IP-Adresse unverändert bleiben soll, müssen Sie die *statische*Zuordnungsmethode verwenden und eine gültige IP-Adresse innerhalb des entsprechenden Adressbereichs angeben.

Statische private IP-Adressen werden häufig für Folgendes verwendet:

* VMs, die als Domänencontroller oder DNS-Server fungieren.
* VMs, die Firewallregeln mit IP-Adressen erfordern.
* VMs, die Dienste ausführen, auf die von anderen Apps über eine IP-Adresse zugegriffen wird.

#### <a name="internal-dns-hostname-resolution"></a>Interne DNS-Hostnamenauflösung
Alle Azure-VMs und PaaS-Rolleninstanzen werden standardmäßig mit [von Azure verwalteten DNS-Servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) konfiguriert, sofern nicht explizit benutzerdefinierte DNS-Server konfiguriert werden. Diese DNS-Server stellen die interne Namensauflösung für VMs und Rolleninstanzen im gleichen VNet oder Clouddienst bereit.

Beim Erstellen einer VM wird den von Azure verwalteten DNS-Servern eine Hostnamenzuordnung für die private IP-Adresse hinzugefügt. Im Fall einer VM mit mehreren Netzwerkschnittstellenkarten wird der Hostname der privaten IP-Adresse der primären Netzwerkschnittstellenkarte zugeordnet. Diese Zuordnungsinformationen sind allerdings auf Ressourcen innerhalb des gleichen Clouddiensts oder VNets beschränkt.

Bei einem *eigenständigen* Clouddienst können nur die Hostnamen der VMs/Rolleninstanzen innerhalb des gleichen Clouddiensts aufgelöst werden. Bei einem Clouddienst innerhalb eines VNets können die Hostnamen aller VMs/Rolleninstanzen innerhalb des VNets aufgelöst werden.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Interner Lastenausgleich (Internal Load Balancer, ILB) und Anwendungsgateways
Sie können der **Front-End**-Konfiguration eines [Azure-ILBs](../load-balancer/load-balancer-internal-overview.md) oder eines [Azure Application Gateways](../application-gateway/application-gateway-introduction.md) eine private IP-Adresse zuweisen. Diese private IP-Adresse fungiert als interner Endpunkt und steht nur den Ressourcen innerhalb des entsprechenden virtuellen Netzwerks (VNet) und der damit verbundenen Remotenetzwerke zur Verfügung. Der Front-End-Konfiguration kann eine dynamische oder eine statische private IP-Adresse zugewiesen werden. Für Multi-VIP-Szenarien können auch mehrere private IP-Adressen zugewiesen werden.

### <a name="at-a-glance"></a>Auf einen Blick
Die folgende Tabelle gibt Aufschluss über die einzelnen Ressourcentypen, die möglichen Zuordnungsmethoden (dynamisch/statisch) sowie über die Möglichkeit, mehrere private IP-Adressen zuzuweisen:

| Resource | Dynamisch | statischen | Mehrere IP-Adressen |
| --- | --- | --- | --- |
| VMs (in einem *eigenständigen* Clouddienst oder VNET) |Ja |Ja |Ja |
| PaaS-Rolleninstanz (in einem *eigenständigen* Clouddienst oder VNET) |Ja |Nein |Nein |
| Front-End für internen Lastenausgleich |Ja |Ja |Ja |
| Application Gateway-Front-End |Ja |Ja |Ja |

## <a name="limits"></a>Einschränkungen
Folgende Tabelle zeigt die Einschränkungen für die IP-Adressierung in Azure gemäß Abonnement. Sie können sich [an den Support wenden](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , um die Standardgrenzwerte je nach Ihren Unternehmensanforderungen bis auf die maximalen Grenzwerte zu erhöhen.

|  | Standardlimit | Maximales Limit |
| --- | --- | --- |
| Öffentliche IP-Adressen (dynamisch) |5 |an den Support wenden |
| Reservierte öffentliche IP-Adressen |20 |an den Support wenden |
| Öffentliche VIP-Adressen pro Bereitstellung (Clouddienst) |5 |an den Support wenden |
| Private VIP-Adressen (ILB) pro Bereitstellung (Clouddienst) |1 |1 |

Lesen Sie unbedingt alle Informationen zu den [Netzwerkeinschränkungen](../azure-subscription-service-limits.md#networking-limits) in Azure.

## <a name="pricing"></a>Preise
In den meisten Fällen sind öffentliche IP-Adressen kostenlos. Es wird eine Schutzgebühr für die Verwendung zusätzlicher und/oder statischer öffentlicher IP-Adressen erhoben. Informieren Sie sich unbedingt über die [Preisstruktur für öffentliche IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Unterschiede zwischen Ressourcen-Manager-Bereitstellungen und klassischen Bereitstellungen
Im Folgenden werden IP-Adressfeatures im Ressourcen-Manager-Bereitstellungsmodell und im klassischen Bereitstellungsmodell miteinander verglichen.

|  | Resource | Klassisch | Ressourcen-Manager |
| --- | --- | --- | --- |
| **Öffentliche IP-Adresse** |***VM*** |Bezeichnet als ILPIP (nur dynamisch) |Bezeichnet als öffentliche IP-Adresse (dynamisch oder statisch) |
|  ||Einem virtuellen IaaS-Computer oder einer PaaS-Rolleninstanz zugewiesen |Der NIC des virtuellen Computers zugeordnet |
|  |***Load Balancer mit Internetzugriff*** |Bezeichnet als VIP (dynamisch) oder reservierte IP-Adresse (statisch) |Bezeichnet als öffentliche IP-Adresse (dynamisch oder statisch) |
|  ||Einem Clouddienst zugewiesen |Der Front-End-Konfiguration des Load-Balancer zugeordnet |
|  | | | |
| **Private IP-Adresse** |***VM*** |Bezeichnet als DIP |Bezeichnet als private IP-Adresse |
|  ||Einem virtuellen IaaS-Computer oder einer PaaS-Rolleninstanz zugewiesen |Der NIC des virtuellen Computers zugewiesen |
|  |***Interner Load Balancer (ILB)*** |Dem ILB (dynamisch oder statisch) zugewiesen |Der ILB-Front-End-Konfiguration (dynamisch oder statisch) zugewiesen |

## <a name="next-steps"></a>Nächste Schritte
* [Bereitstellen eines virtuellen Computers mit einer statischen privaten IP-Adresse](virtual-networks-static-private-ip-classic-pportal.md) mithilfe des Azure-Portals

