---
title: Informationen zu zonenredundanten Gateways für das virtuelle Netzwerk in Azure-Verfügbarkeitszonen | Microsoft-Dokumentation
description: Informationen zu VPN Gateway-Instanzen und ExpressRoute-Gateways in Verfügbarkeitszonen.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 0ba818ef3c24d0e88e662adf87b22cc938fe5fab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60391051"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Informationen zu zonenredundanten Gateways für das virtuelle Netzwerk in Azure-Verfügbarkeitszonen

Sie können VPN- und ExpressRoute-Gateways in [Azure-Verfügbarkeitszonen](../availability-zones/az-overview.md) bereitstellen. So erzielen Sie Stabilität, Skalierbarkeit und eine höhere Verfügbarkeit für die Gateways des virtuellen Netzwerks. Durch die Bereitstellung von Gateways in Azure-Verfügbarkeitszonen werden die Gateways innerhalb einer Region physisch und logisch getrennt. Gleichzeitig wird die Konnektivität Ihres lokalen Netzwerks mit Azure vor Ausfällen auf Zonenebene geschützt.

### <a name="zrgw"></a>Zonenredundante Gateways

Um Ihre Gateways für virtuelle Netzwerke automatisch über Verfügbarkeitszonen hinweg bereitzustellen, können Sie zonenredundante Gateways für virtuelle Netzwerke verwenden. Mit zonenredundanten Gateways können Sie von Zonenresilienz profitieren, um auf Ihre unternehmenskritischen, skalierbaren Dienste in Azure zuzugreifen.

<br>
<br>

![Grafik: zonenredundante Gateways](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Zonenbasierte Gateways

Um Gateways in einer bestimmten Zone bereitzustellen, verwenden Sie zonenbasierte Gateways. Wenn Sie ein zonenbasiertes Gateway bereitstellen, werden alle Instanzen des Gateways in derselben Verfügbarkeitszone bereitgestellt.

<br>
<br>

![Grafik: zonenbasierte Gateways](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>Gateway-SKUs

Zonenredundante und zonenbasierte Gateways sind als neue Gateway-SKUs verfügbar. In Regionen mit Azure-Verfügbarkeitszonen haben wir neue SKUs für virtuelle Netzwerkgateways hinzugefügt. Diese SKUs ähneln den entsprechenden SKUs für ExpressRoute und VPN Gateway, sind aber speziell auf zonenredundante und zonenbasierte Gateways ausgelegt.

Folgende neue Gateway-SKUs stehen zur Verfügung:

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>SKUs für öffentliche IP-Ressourcen

Zonenredundante und zonenbasierte Gateways verwenden die Azure-SKU für öffentliche IP-Ressourcen vom Typ *Standard*. Die Konfiguration der öffentlichen Azure-IP-Ressource bestimmt, ob Sie ein zonenredundantes oder zonenbasiertes Gateway bereitstellen. Wenn Sie eine öffentliche IP-Ressource mit einer *Basic*-SKU erstellen, verfügt das Gateway nicht über Zonenredundanz, und die Gatewayressourcen sind auf eine Region begrenzt.

### <a name="pipzrg"></a>Zonenredundante Gateways

Wenn Sie eine öffentliche IP-Adresse mit der **Standard**-SKU für öffentliche IP-Adressen erstellen, ohne eine Zone anzugeben, ändert sich das Verhalten, je nachdem, ob es sich um ein VPN-Gateway oder ein ExpressRoute-Gateway handelt. 

* Im Fall eines VPN-Gateways werden die beiden Gatewayinstanzen in zwei beliebigen der drei Zonen bereitgestellt, um für Zonenredundanz zu sorgen. 
* Da es bei einem ExpressRoute-Gateway mehr als zwei Instanzen geben kann, kann sich das Gateway über alle drei Zonen erstrecken.

### <a name="pipzg"></a>Zonenbasierte Gateways

Wenn Sie eine öffentliche IP-Adresse mit der **Standard**-SKU für öffentliche IP-Adressen erstellen und die Zone (1, 2 oder 3) angeben, werden alle Gatewayinstanzen in derselben Zone bereitgestellt.

### <a name="piprg"></a>Regionsbezogene Gateways

Wenn Sie eine öffentliche IP-Adresse mit der **Basic**-SKU für öffentliche IP-Adressen erstellen, wird das Gateway als regionsbezogenes Gateway bereitgestellt und bietet keine in das Gateway integrierte Zonenredundanz.

## <a name="faq"></a>Häufig gestellte Fragen

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Was ändert sich durch die Bereitstellung dieser neuen SKUs?

Aus Ihrer Sicht können Sie Ihre Gateways mit Zonenredundanz bereitstellen. Das bedeutet, dass alle Instanzen der Gateways in Azure-Verfügbarkeitszonen bereitgestellt werden und jede Verfügbarkeitszone eine eigene Fehler- und Updatedomäne darstellt. Dadurch werden Ihre Gateways zuverlässiger, verfügbarer und robuster bei Zonenausfällen.

### <a name="can-i-use-the-azure-portal"></a>Kann ich das Azure-Portal verwenden?

Ja, Sie können die neuen SKUs über das Azure-Portal bereitstellen. Allerdings werden diese neuen SKUs nur in den Azure-Regionen angezeigt, die Azure-Verfügbarkeitszonen haben.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>In welchen Regionen stehen die neuen SKUs zur Verfügung?

Die neuen SKUs sind in Azure-Regionen mit Azure-Verfügbarkeitszonen verfügbar. Hierzu zählen „USA, Mitte“, „Frankreich, Mitte“, „Europa, Norden“, „Europa, Westen“ und „USA, Westen 2“. In Zukunft werden zonenredundante Gateways auch in anderen öffentlichen Azure-Regionen verfügbar sein.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Kann ich meine vorhandenen Gateways für virtuelle Netzwerke in zonenredundante oder zonenbasierte Gateways ändern/migrieren/aktualisieren?

Die Migration vorhandener Gateways für virtuelle Netzwerke zu zonenredundanten oder zonenbasierten Gateways wird derzeit nicht unterstützt. Sie können jedoch Ihr vorhandenes Gateway löschen und ein neues zonenredundantes oder zonenbasiertes Gateway erstellen.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Kann ich VPN- und ExpressRoute-Gateways im gleichen virtuellen Netzwerk bereitstellen?

Die Bereitstellung von VPN- und ExpressRoute-Gateways im gleichen virtuellen Netzwerk wird unterstützt. Sie sollten jedoch den IP-Adressbereich „/27“ für das Gatewaysubnetz reservieren.

## <a name="next-steps"></a>Nächste Schritte

[Erstellen eines zonenredundanten Gateways für virtuelle Netzwerke](create-zone-redundant-vnet-gateway.md)
