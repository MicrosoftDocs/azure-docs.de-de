---
title: IP-Adresstypen in Azure
titlesuffix: Azure Virtual Network
description: Enthält Informationen zu öffentlichen und privaten IP-Adressen in Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: kumud
ms.openlocfilehash: 73b185eabc77d293328b1251a4af1aafffc5f319
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65236354"
---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>IP-Adresstypen und Zuordnungsmethoden in Azure

Sie können Azure-Ressourcen IP-Adressen zuweisen, um die Kommunikation mit anderen Azure-Ressourcen, Ihrem lokalen Netzwerk und dem Internet zu ermöglichen. In Azure können zwei Arten von IP-Adressen verwendet werden.

* **Öffentliche IP-Adressen**: Diese werden für die Kommunikation mit dem Internet verwendet. Hierzu zählen auch öffentliche Azure-Dienste.
* **Private IP-Adressen**: Sie werden für die Kommunikation innerhalb eines virtuellen Azure-Netzwerks (VNET) und innerhalb Ihres lokalen Netzwerks verwendet, wenn Sie Ihr Netzwerk mithilfe eines VPN-Gateways oder einer ExpressRoute-Verbindung auf Azure ausdehnen.

Sie können auch einen zusammenhängenden Bereich von statischen öffentlichen IP-Adressen über ein Präfix für öffentliche IP-Adressen erstellen. Informationen zu Präfixen für öffentliche IP-Adressen finden Sie [hier](public-ip-address-prefix.md).

> [!NOTE]
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen:  [das Resource Manager-Modell und das klassische Bereitstellungsmodell](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  Dieser Artikel befasst sich mit dem Resource Manager-Bereitstellungsmodell, das von Microsoft für die meisten neuen Bereitstellungen anstatt des [klassischen Bereitstellungsmodells](virtual-network-ip-addresses-overview-classic.md) empfohlen wird.
> 

Wenn Sie mit dem klassischen Bereitstellungsmodell vertraut sind, sollten Sie sich die [Unterschiede bei IP-Adressen zwischen dem klassischen Bereitstellungsmodell und dem Resource Manager-Bereitstellungsmodell](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) ansehen.

## <a name="public-ip-addresses"></a>Öffentliche IP-Adressen

Öffentliche IP-Adressen ermöglichen Internetressourcen die eingehende Kommunikation mit Azure-Ressourcen. Öffentliche IP-Adressen ermöglichen Azure-Ressourcen auch die ausgehende Kommunikation mit dem Internet und öffentlichen Azure-Diensten mit einer der Ressource zugewiesenen IP-Adresse. Die Adresse ist für die Ressource reserviert, bis Sie die Zuweisung aufheben. Wenn einer Ressource keine öffentliche IP-Adresse zugewiesen wird, kann sie trotzdem ausgehend mit dem Internet kommunizieren, Azure weist jedoch dynamisch eine verfügbare IP-Adresse zu, die nicht für die Ressource reserviert wird. Weitere Informationen zu ausgehenden Verbindungen in Azure finden Sie unter [Grundlegendes zu ausgehenden Verbindungen](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

In Azure-Ressourcen-Manager ist eine [öffentliche IP-Adresse](virtual-network-public-ip-address.md) eine Ressource, die über eigene Eigenschaften verfügt. Sie können eine öffentliche IP-Adressressource beispielsweise einer der folgenden Ressourcen zuordnen:

* Netzwerkschnittstellen für virtuelle Computer
* Load Balancer mit Internetzugriff
* VPN-Gateways
* Anwendungsgateways

### <a name="ip-address-version"></a>IP-Adressversion

Öffentliche IP-Adressen werden mit einer IPv4- oder IPv6-Adresse erstellt. Öffentliche IPv6-Adressen können nur Lastenausgleichsmodulen mit Internetzugriff zugewiesen werden.

### <a name="sku"></a>SKU

Öffentliche IP-Adressen werden mit einer der folgenden SKUs erstellt:

>[!IMPORTANT]
> Für Lastenausgleichs- und öffentliche IP-Ressourcen müssen übereinstimmende SKUs verwendet werden. Eine Kombination von Ressourcen der SKU-Typen „Basic“ und „Standard“ ist nicht möglich. Sie können eigenständige virtuelle Computer, virtuelle Computer in einer Ressource einer Verfügbarkeitsgruppe oder eine Ressource einer VM-Skalierungsgruppe an beide SKUs gleichzeitig anfügen.  Für neue Entwürfe sollte SKU-Standardressourcen verwendet werden.  Unter [Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) finden Sie ausführliche Informationen.

#### <a name="basic"></a>Basic

Alle öffentlichen IP-Adressen, die vor der Einführung von SKUs erstellt wurden, sind öffentliche IP-Adressen vom Typ „Basic-SKU“. Nach der Einführung von SKUs können Sie nun angeben, welche SKU für die öffentliche IP-Adresse verwendet werden soll. Für Basic-SKU-Adressen gilt Folgendes:

- Sie werden mit der statischen oder der dynamischen Zuordnungsmethode zugewiesen.
- Sie verfügen über ein anpassbares Leerlauftimeout für den ursprünglich eingehenden Datenfluss, das auf einen Wert zwischen vier und 30 Minuten (Standardwert: vier Minuten) festgelegt werden kann, sowie über ein vorgegebenes Leerlauftimeout für den ursprünglich ausgehenden Datenfluss von vier Minuten.
- Sind standardmäßig geöffnet.  Netzwerksicherheitsgruppen werden empfohlen, sind aber zum Einschränken des ein- oder ausgehenden Datenverkehrs optional.
- Die Zuweisung ist für jede Azure-Ressource möglich, der eine öffentliche IP-Adresse zugewiesen werden kann, z.B. Netzwerkschnittstellen, VPN Gateways, Application Gateways und Lastenausgleiche mit Internetzugriff.
- Sie unterstützen keine Szenarien mit Verfügbarkeitszone.  Für Szenarien mit Verfügbarkeitszone muss eine öffentliche IP-Adresse vom Typ „Standard-SKU“ verwendet werden. Weitere Informationen zu Verfügbarkeitszonen finden Sie unter [Übersicht über Verfügbarkeitszonen in Azure](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und [Azure Load Balancer Standard und Verfügbarkeitszonen](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="standard"></a>Standard

Für öffentliche IP-Adressen vom Typ „Standard-SKU“ gilt Folgendes:

- Sie verwenden immer eine statische Zuordnungsmethode.
- Sie verfügen über ein anpassbares Leerlauftimeout für den ursprünglich eingehenden Datenfluss, das auf einen Wert zwischen vier und 30 Minuten (Standardwert: vier Minuten) festgelegt werden kann, sowie über ein vorgegebenes Leerlauftimeout für den ursprünglich ausgehenden Datenfluss von vier Minuten.
- Sind standardmäßig sicher und für eingehenden Datenverkehr geschlossen. Sie müssen zulässigen eingehenden Datenverkehr mithilfe einer [Netzwerksicherheitsgruppe](security-overview.md#network-security-groups) explizit in einer Whitelist angeben.
- Sie werden Netzwerkschnittstellen, öffentlichen Standard Load Balancer-Instanzen, Anwendungsgateways oder VPN-Gateways zugewiesen. Weitere Informationen zu Standard Load Balancer finden Sie unter [Übersicht: Azure Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Sie sind standardmäßig zonenredundant und können optional zonengebunden sein. (Sie können also zonengebunden erstellt und in einer bestimmten Verfügbarkeitszone garantiert werden.) Weitere Informationen zu Verfügbarkeitszonen finden Sie unter [Übersicht über Verfügbarkeitszonen in Azure](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und [Azure Load Balancer Standard und Verfügbarkeitszonen](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
 
> [!NOTE]
> Die eingehende Kommunikation mit einer SKU-Standardressource ist erst erfolgreich, wenn Sie eine [Netzwerksicherheitsgruppe](security-overview.md#network-security-groups) erstellen und zuordnen und den gewünschten eingehenden Datenverkehr explizit zulassen.

> [!NOTE]
> Nur öffentliche IP-Adressen mit SKU-Typ „Basic“ stehen zur Verfügung, wenn der [Instance Metadata Service IMDS](../virtual-machines/windows/instance-metadata-service.md) verwendet wird. Die SKU vom Typ „Standard“ wird nicht unterstützt.

### <a name="allocation-method"></a>Zuordnungsmethode

Öffentliche IP-Adressen der SKU-Typen „Basic“ und „Standard“ unterstützen die *statische* Zuordnungsmethode.  Der Ressource wird beim Anlegen eine IP-Adresse zugewiesen. Beim Löschen der Ressource wird die IP-Adresse freigegeben.

Öffentliche IP-Adressen des SKU-Typs „Basic“ unterstützen auch eine *dynamische* Zuordnungsmethode, die Standard ist, wenn die Zuordnungsmethode nicht angegeben ist.  Die Wahl der *dynamischen* Zuordnungsmethode für eine öffentliche IP-Adressressource des Typs „Basic“ bedeutet, dass die IP-Adresse zum Zeitpunkt der Ressourcenerstellung **nicht** zugeordnet wird.  Die öffentliche IP-Adresse wird zugeordnet, wenn Sie die öffentliche IP-Adresse einem virtuellen Computer zuordnen oder Sie die erste Instanz des virtuellen Computers im Back-End-Pool eines Lastenausgleichs des Typs „Basic“ platzieren.   Die IP-Adresse wird freigegeben, wenn Sie die Ressource beenden oder löschen.  Nach dem Freigeben für Ressource A kann die IP-Adresse beispielsweise einer anderen Ressource zugewiesen werden. Wenn die IP-Adresse einer anderen Ressource zugewiesen wird, während Ressource A angehalten ist, erhält Ressource A eine andere IP-Adresse, wenn Sie sie neu starten. Wenn Sie die Zuordnungsmethode einer öffentlichen IP-Adressressource des Typs „Basic“ von *statisch* in *dynamisch* ändern, wird die Adresse freigegeben. Damit die IP-Adresse für die zugeordnete Ressource unverändert bleibt, können Sie die Zuordnungsmethode explizit auf *statisch*festlegen. Eine statische IP-Adresse wird sofort zugewiesen.

> [!NOTE]
> Auch wenn Sie die Zuordnungsmethode auf *statisch* festlegen, können Sie nicht die tatsächliche IP-Adresse angeben, die der öffentlichen IP-Adressressource zugewiesen ist. Azure weist die IP-Adresse aus einem Pool mit verfügbaren IP-Adressen an dem Azure-Standort zu, an dem die Ressource erstellt wird.
>

Statische öffentliche IP-Adressen werden häufig in den folgenden Szenarien verwendet:

* Sie müssen Firewallregeln für die Kommunikation mit Ihren Azure-Ressourcen aktualisieren.
* Bei der DNS-Namensauflösung erfordert eine Änderung einer IP-Adresse eine Aktualisierung von A-Datensätzen.
* Ihre Azure-Ressourcen kommunizieren mit anderen Apps oder Diensten, die ein auf IP-Adressen basierendes Sicherheitsmodell verwenden.
* Sie verwenden SSL-Zertifikate, die mit einer IP-Adresse verknüpft sind.

> [!NOTE]
> Azure ordnet öffentliche IP-Adressen aus einem Bereich zu, der für jede Region in jeder Azure-Cloud eindeutig ist. Sie können die Liste von Bereichen (Präfixen) für die [öffentliche Azure-Cloud](https://www.microsoft.com/download/details.aspx?id=56519), die [Azure US Government-Cloud](https://www.microsoft.com/download/details.aspx?id=57063) sowie für die Azure-Cloud in [China](https://www.microsoft.com/download/details.aspx?id=57062) und [Deutschland](https://www.microsoft.com/download/details.aspx?id=57064) herunterladen.
>

### <a name="dns-hostname-resolution"></a>DNS-Hostnamenauflösung
Sie können eine DNS-Domänennamensbezeichnung für eine öffentliche IP-Ressource angeben. Dadurch erstellen Sie für „*Domänennamensbezeichnung*.*Standort*.cloudapp.azure.com“ eine Zuordnung zur öffentlichen IP-Adresse auf den von Azure verwalteten DNS-Servern. Wenn Sie beispielsweise eine öffentliche IP-Ressource mit **contoso** als *Domänennamensbezeichnung* am Azure-*Standort* **USA, Westen** erstellen, wird der vollqualifizierte Domänenname (FQDN) **contoso.westus.cloudapp.azure.com** in die öffentliche IP-Adresse der Ressource aufgelöst.

> [!IMPORTANT]
> Jede erstellte Domänennamensbezeichnung muss innerhalb des Azure-Standorts eindeutig sein.  
>

### <a name="dns-best-practices"></a>DNS – bewährte Methoden
Wenn Sie zu einer anderen Region migrieren müssen, können Sie den FQDN Ihrer öffentlichen IP-Adresse nicht migrieren. Als bewährte Methode können Sie mit dem FQDN einen benutzerdefinierten CNAME-Domäneneintrag mit Verweis auf die öffentliche IP-Adresse in Azure erstellen. Wenn Sie zu einer anderen öffentlichen IP-Adresse wechseln müssen, ist eine Aktualisierung des CNAME-Eintrags statt der manuellen Aktualisierung des FQDN mit der neuen Adresse erforderlich. Sie können [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) oder einen externen DNS-Anbieter für Ihren DNS-Eintrag verwenden. 

### <a name="virtual-machines"></a>Virtuelle Computer

Sie können eine öffentliche IP-Adresse einem virtuellen [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- oder [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Computer zuordnen, indem Sie sie dessen **Netzwerkschnittstelle** zuweisen. Sie können einem virtuellen Computer eine dynamische oder eine statische öffentliche IP-Adresse zuweisen. Informieren Sie sich über das [Zuweisen von IP-Adressen zu Netzwerkschnittstellen](virtual-network-network-interface-addresses.md).

### <a name="internet-facing-load-balancers"></a>Load Balancer mit Internetzugriff

Sie können eine öffentliche IP-Adresse, die mit einer der [SKUs](#sku) erstellt wurde, einem [Azure Load Balancer](../load-balancer/load-balancer-overview.md) zuordnen, indem Sie sie der **Front-End**-Konfiguration des Load Balancers zuweisen. Die öffentliche IP-Adresse fungiert als virtuelle IP-Adresse (VIP) mit Lastenausgleich. Sie können einem Load Balancer-Front-End eine dynamische oder eine statische öffentliche IP-Adresse zuweisen. Sie können einem Load Balancer-Front-End auch mehrere öffentliche IP-Adressen zuweisen. Dadurch werden Szenarien mit [mehreren VIPs](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ermöglicht, z.B. mehrinstanzenfähige Umgebungen mit SSL-basierten Websites. Weitere Informationen zu SKUs von Azure Load Balancer finden Sie unter [Azure load balancer standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Standard-SKU von Azure Load Balancer).

### <a name="vpn-gateways"></a>VPN-Gateways

Ein [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) stellt für ein virtuelles Azure-Netzwerk eine Verbindung mit anderen virtuellen Azure-Netzwerken oder einem lokalen Netzwerk her. Eine öffentliche IP-Adresse wird dem VPN Gateway zugewiesen, um dafür die Kommunikation mit dem Remotenetzwerk zu ermöglichen. Sie können einem VPN Gateway nur eine *dynamische* öffentliche IP-Adresse des Typs „Basic“ zuweisen.

### <a name="application-gateways"></a>Anwendungsgateways

Sie können eine öffentliche IP-Adresse einem Azure [Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)zuordnen, indem Sie sie der **Front-End** -Konfiguration des Gateways zuweisen. Diese öffentliche IP-Adresse fungiert als VIP (virtuelle IP-Adresse) mit Lastenausgleich. Sie können der V1-Front-End-Konfiguration eines Anwendungsgateway nur eine *dynamische* öffentliche IP-Adresse des und einer V2-Front-End-Konfiguration nur eine *statische* SKU-Adresse zuweisen.

### <a name="at-a-glance"></a>Auf einen Blick
In der Tabelle unten sind die spezifischen Eigenschaften, über die eine öffentliche IP-Adresse einer Ressource der obersten Ebene zugeordnet sein kann, und die möglichen Zuweisungsverfahren (dynamisch oder statisch) angegeben.

| Ressource der obersten Ebene | Zuordnung der IP-Adresse | Dynamisch | statischen |
| --- | --- | --- | --- |
| Virtueller Computer |Netzwerkschnittstelle |Ja |Ja |
| Lastenausgleich mit Internetzugriff |Front-End-Konfiguration |Ja |Ja |
| VPN-Gateway |Gateway-IP-Konfiguration |Ja |Nein |
| Anwendungsgateway |Front-End-Konfiguration |Ja (nur V1) |Ja (nur V2) |

## <a name="private-ip-addresses"></a>Private IP-Adressen
Private IP-Adressen ermöglichen Azure-Ressourcen die Kommunikation mit anderen Ressourcen in einem [virtuellen Netzwerk](virtual-networks-overview.md) oder in einem lokalen Netzwerk über ein VPN-Gateway oder eine ExpressRoute-Verbindung, ohne dass dabei eine über das Internet erreichbare IP-Adresse verwendet wird.

Im Azure Resource Manager-Bereitstellungsmodell wird den folgenden Typen von Azure-Ressourcen eine private IP-Adresse zugeordnet.

* Netzwerkschnittstellen für virtuelle Computer
* Interne Lastenausgleichsmodule (ILBs) abrufen
* Anwendungsgateways

### <a name="ip-address-version"></a>IP-Adressversion

Private IP-Adressen werden mit einer IPv4- oder IPv6-Adresse erstellt. Privaten IPv6-Adressen kann nur die dynamische Zuordnungsmethode zugewiesen werden. Die Kommunikation zwischen privaten IPv6-Adressen ist in einem virtuellen Netzwerk nicht möglich. Sie können aus dem Internet in eingehender Richtung mit einer privaten IPv6-Adresse über einen Lastenausgleich mit Internetzugriff kommunizieren. Ausführliche Informationen finden Sie unter [Erstellen eines Lastenausgleichs für den Internetzugriff mit IPv6 – Azure CLI](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="allocation-method"></a>Zuordnungsmethode

Eine private IP-Adresse wird aus dem Adressbereich des VNET-Subnetzes zugeteilt, in dem eine Ressource bereitgestellt wurde. Azure reserviert die ersten vier Adressen jedes Subnetzadressbereichs, sodass die Adressen keinen Ressourcen zugewiesen werden können. Wenn der Adressbereich des Subnetzes beispielsweise 10.0.0.0/16 ist, können die Adressen 10.0.0.0 bis 10.0.0.3 keinen Ressourcen zugewiesen werden. IP-Adressen innerhalb des Adressbereichs des Subnetzes können jeweils nur einer Ressource zugewiesen werden. 

Es gibt zwei Methoden zum Zuordnen von privaten IP-Adressen:

- **Dynamisch**: Azure weist die nächste verfügbare nicht zugewiesene oder nicht reservierte IP-Adresse im Adressbereich des Subnetzes zu. Beispielsweise weist Azure 10.0.0.10 einer neuen Ressource zu, wenn die Adressen 10.0.0.4 bis 10.0.0.9 bereits anderen Ressourcen zugewiesen sind. „Dynamisch“ ist das Standardverfahren für die Zuteilung. Nach der Zuweisung werden dynamische IP-Adressen nur freigegeben, wenn eine Netzwerkschnittstelle gelöscht wird oder einem anderen Subnetz desselben virtuellen Netzwerks zugewiesen wird oder wenn die Zuordnungsmethode in „Statisch“ geändert und eine andere IP-Adresse angegeben wird. Standardmäßig wird in Azure die zuvor dynamisch zugewiesene Adresse als statische Adresse zugewiesen, wenn Sie die Zuteilungsmethode von „Dynamisch“ in „Statisch“ ändern.
- **Statisch**: Sie wählen eine beliebige nicht zugewiesene oder nicht reservierte IP-Adresse im Adressbereich des Subnetzes aus und weisen sie zu. Wenn der Adressbereich eines Subnetzes beispielsweise 10.0.0.0/16 ist und die Adressen 10.0.0.4 bis 10.0.0.9 bereits anderen Ressourcen zugewiesen sind, weisen Sie eine beliebige Adresse zwischen 10.0.0.10 und 10.0.255.254 zu. Statische Adressen werden nur freigegeben, wenn eine Netzwerkschnittstelle gelöscht wird. Wenn Sie die Zuordnungsmethode in „Dynamisch“ ändern, wird in Azure dynamisch die zuvor zugewiesene statische IP-Adresse als dynamische Adresse zugewiesen. Das gilt auch, wenn die Adresse nicht die nächste verfügbare Adresse im Adressbereich des Subnetzes ist. Die Adresse ändert sich auch, wenn die Netzwerkschnittstelle einem anderen Subnetz desselben virtuellen Netzwerks zugewiesen wird. Wenn Sie die Netzwerkschnittstelle einem anderen Subnetz zuweisen möchten, müssen Sie zuerst die Zuteilungsmethode von „Statisch“ in „Dynamisch“ ändern. Nachdem Sie die Netzwerkschnittstelle einem anderen Subnetz zugewiesen haben, können Sie die Zuteilungsmethode wieder in „Statisch“ ändern und eine IP-Adresse aus dem Adressbereich des neuen Subnetzes zuweisen.

### <a name="virtual-machines"></a>Virtuelle Computer

Eine oder mehrere private IP-Adressen werden mindestens einer **Netzwerkschnittstelle** eines virtuellen [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- oder [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Computers zugewiesen. Für jede private IP-Adresse können Sie als Zuteilungsmethode „Dynamisch“ oder „Statisch“ angeben.

#### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Interne DNS-Hostnamensauflösung (für virtuelle Computer)

Alle virtuellen Azure-Computer werden standardmäßig mit [von Azure verwalteten DNS-Servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) konfiguriert, sofern nicht explizit benutzerdefinierte DNS-Server konfiguriert werden. Diese DNS-Server stellen die interne Namensauflösung für virtuelle Computer in demselben virtuellen Netzwerk bereit.

Beim Erstellen eines virtuellen Computers wird den von Azure verwalteten DNS-Servern eine Hostnamenzuordnung für die private IP-Adresse hinzugefügt. Wenn ein virtueller Computer über mehrere Netzwerkschnittstellen oder mehrere IP-Konfigurationen für eine Netzwerkschnittstelle verfügt, wird der Hostname der privaten IP-Adresse für die primäre IP-Konfiguration der primären Netzwerkschnittstelle zugeordnet.

Virtuelle Computer, die mit von Azure verwalteten DNS-Servern konfiguriert sind, können die Hostnamen aller virtuellen Computer in demselben virtuellen Netzwerk in ihre privaten IP-Adressen auflösen. Sie müssen einen benutzerdefinierten DNS-Server verwenden, um Hostnamen von virtuellen Computern in verbundenen virtuellen Netzwerken aufzulösen.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Interner Lastenausgleich (Internal Load Balancer, ILB) und Anwendungsgateways

Sie können der **Front-End**-Konfiguration eines [internen Azure-Lastenausgleichs](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB) oder eines [Azure Application Gateways](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eine private IP-Adresse zuweisen. Diese private IP-Adresse fungiert als interner Endpunkt und steht nur den Ressourcen innerhalb des entsprechenden virtuellen Netzwerks und der damit verbundenen Remotenetzwerke zur Verfügung. Der Front-End-Konfiguration kann eine dynamische oder eine statische private IP-Adresse zugewiesen werden.

### <a name="at-a-glance"></a>Auf einen Blick
In der Tabelle unten sind die spezifischen Eigenschaften, über die eine private IP-Adresse einer Ressource der obersten Ebene zugeordnet sein kann, und die möglichen Zuweisungsverfahren (dynamisch oder statisch) angegeben.

| Ressource der obersten Ebene | Zuordnung der IP-Adresse | dynamisch | statischen |
| --- | --- | --- | --- |
| Virtueller Computer |Netzwerkschnittstelle |Ja |Ja |
| Load Balancer |Front-End-Konfiguration |Ja |Ja |
| Anwendungsgateway |Front-End-Konfiguration |Ja |Ja |

## <a name="limits"></a>Einschränkungen
Die Grenzwerte für die IP-Adressierung finden Sie in den vollständigen Informationen zu [Grenzwerten für Netzwerke](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) in Azure. Die Grenzwerte gelten pro Region und pro Abonnement. Sie können sich [an den Support wenden](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , um die Standardgrenzwerte je nach Ihren Unternehmensanforderungen bis auf die maximalen Grenzwerte zu erhöhen.

## <a name="pricing"></a>Preise
Für öffentliche IP-Adressen wird unter Umständen eine geringe Gebühr berechnet. Weitere Informationen zu den Preisen für IP-Adressen in Azure finden Sie auf der Seite [Preise für IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Nächste Schritte
* [Bereitstellen einer VM mit einer statischen öffentlichen IP-Adresse mithilfe des Azure-Portals](virtual-network-deploy-static-pip-arm-portal.md)
* [Bereitstellen eines virtuellen Computers mit einer statischen privaten IP-Adresse mithilfe des Azure-Portals](virtual-networks-static-private-ip-arm-pportal.md)
