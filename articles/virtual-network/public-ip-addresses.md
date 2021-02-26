---
title: Öffentliche IP-Adressen in Azure
titlesuffix: Azure Virtual Network
description: Erfahren Sie etwas über öffentliche IP-Adressen in Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: f2818965013e44cbbe3202887bf79a737dbbbb58
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806958"
---
# <a name="public-ip-addresses"></a>Öffentliche IP-Adressen

Öffentliche IP-Adressen ermöglichen Internetressourcen die eingehende Kommunikation mit Azure-Ressourcen. Öffentliche IP-Adressen ermöglichen Azure-Ressourcen, mit dem Internet und öffentlichen Azure-Diensten zu kommunizieren. Die Adresse ist für die Ressource reserviert, bis Sie die Zuweisung aufheben. Eine Ressource ohne zugewiesene öffentliche IP-Adresse kann ausgehend kommunizieren. Azure weist dynamisch eine verfügbare IP-Adresse zu, die nicht für die Ressource dediziert ist. Weitere Informationen zu ausgehenden Verbindungen in Azure finden Sie unter [Grundlegendes zu ausgehenden Verbindungen](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

In Azure-Ressourcen-Manager ist eine [öffentliche IP-Adresse](virtual-network-public-ip-address.md) eine Ressource, die über eigene Eigenschaften verfügt. Sie können eine öffentliche IP-Adressressource beispielsweise den folgenden Ressourcen zuordnen:

* Netzwerkschnittstellen für virtuelle Computer
* Load Balancer mit Internetzugriff
* VPN-Gateways
* Anwendungsgateways
* Azure Firewall

## <a name="ip-address-version"></a>IP-Adressversion

Öffentliche IP-Adressen werden mit einer IPv4- oder IPv6-Adresse erstellt. 

## <a name="sku"></a>SKU

Weitere Informationen zum SKU-Upgrade finden Sie unter [Ausführen eines Upgrades für öffentliche IP-Adressen](../virtual-network/virtual-network-public-ip-address-upgrade.md).

Öffentliche IP-Adressen werden mit einer der folgenden SKUs erstellt:

>[!IMPORTANT]
> Für Lastenausgleichs- und öffentliche IP-Adressressourcen müssen übereinstimmende SKUs verwendet werden. Eine Kombination von Ressourcen der SKU-Typen „Basic“ und „Standard“ ist nicht möglich. Sie können eigenständige virtuelle Computer, virtuelle Computer in einer Ressource einer Verfügbarkeitsgruppe oder eine Ressource einer VM-Skalierungsgruppe an beide SKUs gleichzeitig anfügen.  Für neue Entwürfe sollte SKU-Standardressourcen verwendet werden.  Unter [Load Balancer Standard](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) finden Sie ausführliche Informationen.

### <a name="standard"></a>Standard

Für öffentliche IP-Adressen mit Standard-SKU gilt Folgendes:

- Sie verwenden immer eine statische Zuordnungsmethode.
- Sie verfügen über ein anpassbares Leerlauftimeout für den ursprünglich eingehenden Datenfluss, das auf einen Wert zwischen vier und 30 Minuten (Standardwert: vier Minuten) festgelegt werden kann, sowie über ein vorgegebenes Leerlauftimeout für den ursprünglich ausgehenden Datenfluss von vier Minuten.
- Sie sind standardmäßig sicher und für eingehenden Datenverkehr geschlossen. Sie ermöglichen das Auflisten von eingehendem Datenverkehr mit einer [Netzwerksicherheitsgruppe](./network-security-groups-overview.md#network-security-groups).
- Sie werden Netzwerkschnittstellen, öffentlichen Load Balancer Standard-Instanzen oder Application Gateway-Instanzen zugewiesen. Weitere Informationen zu Load Balancer Standard finden Sie unter [Azure Load Balancer Standard](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Sie können zonenredundant (aus allen 3 Zonen angekündigt), zonenbezogen ( in einer bestimmten vorausgewählten Verfügbarkeitszone garantiert) oder nicht zonenbezogen (keiner bestimmten vorausgewählten Verfügbarkeitszone zugeordnet) sein. Weitere Informationen zu Verfügbarkeitszonen finden Sie unter [Übersicht über Verfügbarkeitszonen in Azure](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und [Azure Load Balancer Standard und Verfügbarkeitszonen](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json). **Zonenredundante IP-Adressen können nur in [Regionen mit drei aktiven Verfügbarkeitszonen](../availability-zones/az-region.md) erstellt werden.** IP-Adressen, die vor der Aktivierung der Zonen erstellt wurden, sind nicht zonenredundant.
- Können als Anycast-Front-End-IP-Adressen für [regionsübergreifende Load Balancer](../load-balancer/cross-region-overview.md) verwendet werden (Vorschaufunktionalität).
 
> [!NOTE]
> Die eingehende Kommunikation mit einer SKU-Standardressource ist erst erfolgreich, wenn Sie eine [Netzwerksicherheitsgruppe](./network-security-groups-overview.md#network-security-groups) erstellen und zuordnen und den gewünschten eingehenden Datenverkehr explizit zulassen.

> [!NOTE]
> Nur öffentliche IP-Adressen mit SKU-Typ „Basic“ stehen zur Verfügung, wenn der [Instance Metadata Service IMDS](../virtual-machines/windows/instance-metadata-service.md) verwendet wird. Die SKU vom Typ „Standard“ wird nicht unterstützt.

> [!NOTE]
> Diagnoseeinstellungen werden nicht unter dem Blatt „Ressource“ angezeigt, wenn eine öffentliche IP-Adresse der Standard-SKU verwendet wird. Um die Protokollierung Ihrer öffentlichen Standard-IP-Adressenressource zu aktivieren, navigieren Sie unter dem Blatt „Azure Monitor“ zu „Diagnoseeinstellungen“, und wählen Sie Ihre IP-Adressenressource aus.

### <a name="basic"></a>Basic

Alle öffentlichen IP-Adressen, die vor der Einführung von SKUs erstellt wurden, sind öffentliche IP-Adressen vom Typ „Basic-SKU“. 

Nach der Einführung von SKUs können Sie angeben, welche SKU für die öffentliche IP-Adresse verwendet werden soll. 

Für Basic-SKU-Adressen gilt Folgendes:

- Sie werden mit der statischen oder der dynamischen Zuordnungsmethode zugewiesen.
- Sie verfügen über ein anpassbares Leerlauftimeout für den ursprünglich eingehenden Datenfluss, das auf einen Wert zwischen vier und 30 Minuten (Standardwert: vier Minuten) festgelegt werden kann, sowie über ein vorgegebenes Leerlauftimeout für den ursprünglich ausgehenden Datenfluss von vier Minuten.
- Sind standardmäßig geöffnet.  Netzwerksicherheitsgruppen werden empfohlen, sind aber zum Einschränken des ein- oder ausgehenden Datenverkehrs optional.
- Sie können jeder Azure-Ressource zugewiesen werden, der eine öffentliche IP-Adresse zugewiesen werden kann, z. B.:
    * Netzwerkschnittstellen
    * VPN-Gateways
    * Anwendungsgateways
    * Öffentliche Lastenausgleichsmodule
- Sie unterstützen keine Szenarien mit Verfügbarkeitszone. Verwenden Sie für Szenarien mit Verfügbarkeitszone eine öffentliche IP-Adresse mit Standard-SKU. Weitere Informationen zu Verfügbarkeitszonen finden Sie unter [Übersicht über Verfügbarkeitszonen in Azure](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und [Azure Load Balancer Standard und Verfügbarkeitszonen](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="allocation-method"></a>Zuordnungsmethode

Öffentliche IP-Adressen unterstützen sowohl als Basic als auch als Standard die **statische** Zuweisung.  Der Ressource wird bei der Erstellung eine IP-Adresse zugewiesen. Beim Löschen der Ressource wird die IP-Adresse wieder freigegeben.

Öffentliche IP-Adressen mit Basic-SKU unterstützen eine **dynamische** Zuweisung. „Dynamisch“ ist das Standardverfahren für die Zuweisung. Bei Auswahl von „Dynamisch“ wird die IP-Adresse **nicht** bei der Erstellung der Ressource zugewiesen.

Die IP-Adresse wird zugewiesen, wenn Sie die öffentliche IP-Adressressource zuordnen:

* Virtueller Computer 
* Der erste virtuelle Computer wird dem Back-End-Pool eines Lastenausgleichs zugeordnet.

Die IP-Adresse wird freigegeben, wenn Sie die Ressource beenden oder löschen.  

Beispielsweise wird eine öffentliche IP-Adressressource von einer Ressource mit dem Namen **Ressource A** freigegeben. **Ressource A** erhält beim Starten eine andere IP-Adresse, wenn die öffentliche IP-Ressource neu zugewiesen wird.

Die IP-Adresse wird freigegeben, wenn die Zuordnungsmethode von **Statisch** in **Dynamisch** geändert wird. Damit die IP-Adresse für die zugeordnete Ressource unverändert bleibt, können Sie die Zuordnungsmethode explizit auf **Statisch** festlegen. Eine statische IP-Adresse wird sofort zugewiesen.

> [!NOTE]
> Auch wenn Sie die Zuordnungsmethode auf **statisch** festlegen, können Sie nicht die tatsächliche IP-Adresse angeben, die der öffentlichen IP-Adressressource zugewiesen ist. Azure weist die IP-Adresse aus einem Pool mit verfügbaren IP-Adressen an dem Azure-Standort zu, an dem die Ressource erstellt wird.
>

Statische öffentliche IP-Adressen werden häufig in den folgenden Szenarien verwendet:

* Sie müssen Firewallregeln für die Kommunikation mit Ihren Azure-Ressourcen aktualisieren.
* Bei der DNS-Namensauflösung erfordert eine Änderung einer IP-Adresse eine Aktualisierung von A-Datensätzen.
* Ihre Azure-Ressourcen kommunizieren mit anderen Apps oder Diensten, die ein auf IP-Adressen basierendes Sicherheitsmodell verwenden.
* Sie verwenden TLS/SSL-Zertifikate, die mit einer IP-Adresse verknüpft sind.

> [!NOTE]
> Azure ordnet öffentliche IP-Adressen aus einem Bereich zu, der für jede Region in jeder Azure-Cloud eindeutig ist. Sie können die Liste von Bereichen (Präfixen) für die [öffentliche Azure-Cloud](https://www.microsoft.com/download/details.aspx?id=56519), die [Azure US Government-Cloud](https://www.microsoft.com/download/details.aspx?id=57063) sowie für die Azure-Cloud in [China](https://www.microsoft.com/download/details.aspx?id=57062) und [Deutschland](https://www.microsoft.com/download/details.aspx?id=57064) herunterladen.
>

## <a name="dns-hostname-resolution"></a>DNS-Hostnamenauflösung

Wählen Sie die Option aus, eine DNS-Domänennamenbezeichnung für eine öffentliche IP-Ressource anzugeben. 

Mit dieser Auswahl wird eine Zuordnung zwischen **domainnamelabel**.**location**.cloudapp.azure.com und der öffentlichen IP-Adresse im von Azure verwalteten DNS erstellt. 

Ein Beispiel ist eine öffentliche IP-Adresse mit:

* **contoso** als **domainnamelabel**
* **USA, Westen** als Azure-**Standort**

Der vollqualifizierte Domänenname (Fully Qualified Domain Name, FQDN) **contoso.westus.cloudapp.azure.com** wird in die öffentliche IP-Adresse der Ressource aufgelöst.

> [!IMPORTANT]
> Jede erstellte Domänennamensbezeichnung muss innerhalb des Azure-Standorts eindeutig sein.  
>

## <a name="dns-recommendations"></a>DNS-Empfehlungen

Wenn ein Regionswechsel erforderlich ist, können Sie nicht den FQDN der öffentlichen IP-Adresse migrieren. Verwenden Sie den FQDN, um einen benutzerdefinierten CNAME-Eintrag mit Verweis auf die öffentliche IP-Adresse zu erstellen. 

Wenn ein Wechsel zu einer anderen öffentlichen IP-Adresse erforderlich ist, aktualisieren Sie den CNAME-Eintrag anstelle des FQDN.

Sie können [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) oder einen externen DNS-Anbieter für Ihren DNS-Eintrag verwenden. 

## <a name="virtual-machines"></a>Virtuelle Computer

Sie können eine öffentliche IP-Adresse einem virtuellen [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- oder [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Computer zuordnen, indem Sie sie dessen **Netzwerkschnittstelle** zuweisen. 

Wählen Sie für die öffentliche IP-Adresse **Dynamisch** oder **Statisch** aus. Informieren Sie sich über das [Zuweisen von IP-Adressen zu Netzwerkschnittstellen](virtual-network-network-interface-addresses.md).

## <a name="internet-facing-load-balancers"></a>Load Balancer mit Internetzugriff

Sie können eine öffentliche IP-Adresse mit einer der beiden [SKUs](#sku) einer [Azure Load Balancer](../load-balancer/load-balancer-overview.md)-Instanz zuordnen, indem Sie sie der **Front-End**-Konfiguration für den Lastenausgleich zuweisen. Diese öffentliche IP-Adresse dient als IP-Adresse mit Lastenausgleich. 

Sie können einem Load Balancer-Front-End eine dynamische oder eine statische öffentliche IP-Adresse zuweisen. Sie können einem Load Balancer-Front-End mehrere öffentliche IP-Adressen zuweisen. Diese Konfiguration ermöglicht Szenarien mit [mehreren VIPs](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), z. B. eine mehrinstanzenfähige Umgebung mit TLS-basierten Websites. 

Weitere Informationen zu SKUs von Azure Load Balancer finden Sie unter [Azure load balancer standard SKU](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Standard-SKU von Azure Load Balancer).

## <a name="vpn-gateways"></a>VPN-Gateways

[Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) verbindet ein virtuelles Azure-Netzwerk mit den folgenden Elementen:

* Virtuelle Azure-Netzwerke
* Lokale Netzwerke. 

Eine öffentliche IP-Adresse wird dem VPN Gateway zugewiesen, um die Kommunikation mit dem Remotenetzwerk zuzulassen. Sie können einem VPN Gateway nur eine *dynamische* öffentliche IP-Adresse des Typs „Basic“ zuweisen.

## <a name="application-gateways"></a>Anwendungsgateways

Sie können eine öffentliche IP-Adresse einem Azure [Application Gateway](../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)zuordnen, indem Sie sie der **Front-End** -Konfiguration des Gateways zuweisen. 

* Weisen Sie einer Application Gateway-Front-End-Konfiguration der Version 1 eine **dynamische** öffentliche IP-Adresse mit Basic-SKU zu. 
* Weisen Sie einer Front-End-Konfiguration der Version 2 eine **statische** Adresse mit Standard-SKU zu.

## <a name="azure-firewall"></a>Azure Firewall

Von [Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) können Richtlinien zur Anwendungs- und Netzwerkkonnektivität für Abonnements und virtuelle Netzwerke erstellt, erzwungen und protokolliert werden.

Sie können einer Firewall nur **statische** öffentliche Standard-IP-Adressen zuordnen. Dadurch können externe Firewalls Datenverkehr aus Ihrem virtuellen Netzwerk identifizieren. 


## <a name="at-a-glance"></a>Auf einen Blick

In der folgenden Tabelle sind die Eigenschaften, über die eine öffentliche IP-Adresse einer Ressource der obersten Ebene zugeordnet werden kann, und die möglichen Zuweisungsverfahren angegeben.

| Ressource der obersten Ebene | Zuordnung der IP-Adresse | Dynamisch | statischen |
| --- | --- | --- | --- |
| Virtueller Computer |Netzwerkschnittstelle |Ja |Ja |
| Lastenausgleich mit Internetzugriff |Front-End-Konfiguration |Ja |Ja |
| VPN-Gateway |Gateway-IP-Konfiguration |Ja |Nein |
| Anwendungsgateway |Front-End-Konfiguration |Ja (nur V1) |Ja (nur V2) |
| Azure Firewall | Front-End-Konfiguration | Nein | Ja|

## <a name="limits"></a>Einschränkungen

Die Grenzwerte für die IP-Adressierung finden Sie in den vollständigen Informationen zu [Grenzwerten für Netzwerke](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) in Azure. 

Die Grenzwerte gelten pro Region und pro Abonnement. [Wenden Sie sich an den Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um die Standardgrenzwerte je nach Ihren Unternehmensanforderungen bis auf die maximalen Grenzwerte zu erhöhen.

## <a name="pricing"></a>Preise

Für öffentliche IP-Adressen wird unter Umständen eine geringe Gebühr berechnet. Weitere Informationen zu den Preisen für IP-Adressen in Azure finden Sie auf der Seite [Preise für IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie etwas über [private IP-Adressen in Azure](private-ip-addresses.md).
* [Bereitstellen einer VM mit einer statischen öffentlichen IP-Adresse mithilfe des Azure-Portals](virtual-network-deploy-static-pip-arm-portal.md)
