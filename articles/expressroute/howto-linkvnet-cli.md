---
title: 'Verknüpfen eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung: CLI: Azure | Microsoft-Dokumentation'
description: Dieser Artikel zeigt Ihnen, wie Sie virtuelle Netzwerke (VNETs) über das Resource Manager-Bereitstellungsmodell und die CLI mit Azure ExpressRoute-Verbindungen verknüpfen.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: anzaman,cherylmc
ms.custom: seodec18
ms.openlocfilehash: 9a6f16df4b827538c1f8bdb9b7382ed06d543b62
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991520"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Verbinden eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung mithilfe der CLI

In diesem Artikel können Sie virtuelle Netzwerke (VNETs) mithilfe der CLI mit Azure-ExpressRoute-Verbindungen verknüpfen. Um mithilfe der Azure CLI eine Verknüpfung durchzuführen, müssen die virtuellen Netzwerke mit dem Resource Manager-Bereitstellungsmodell erstellt werden. Sie können Teil desselben Abonnements sein oder zu einem anderen Abonnement gehören. Wenn Sie eine andere Methode für die Verbindung Ihres VNET mit einer ExpressRoute-Verbindung verwenden möchten, können Sie einen Artikel aus der folgenden Liste auswählen:

> [!div class="op_single_selector"]
> * [Azure-Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure-Befehlszeilenschnittstelle](howto-linkvnet-cli.md)
> * [Video – Azure-Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisch)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Konfigurationsvoraussetzungen

* Sie benötigen die neueste Version der Befehlszeilenschnittstelle (CLI). Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Stellen Sie sicher, dass Sie vor Beginn der Konfiguration die Seiten [Voraussetzungen](expressroute-prerequisites.md), [Routinganforderungen](expressroute-routing.md) und [Workflows](expressroute-workflows.md) gelesen haben.

* Sie benötigen eine aktive ExpressRoute-Verbindung. 
  * Führen Sie die Schritte zum [Erstellen einer ExpressRoute-Verbindung](howto-circuit-cli.md) aus, und lassen Sie sie vom Konnektivitätsanbieter aktivieren. 
  * Stellen Sie sicher, dass privates Azure-Peering für die Verbindung konfiguriert ist. Informationen zum Routing finden Sie unter [Konfigurieren des Routings](howto-routing-cli.md) . 
  * Stellen Sie sicher, dass das private Azure-Peering konfiguriert ist. Das BGP-Peering zwischen Ihrem Netzwerk und Microsoft muss aktiv sein, damit End-to-End-Konnektivität bereitgestellt werden kann.
  * Vergewissern Sie sich, dass ein virtuelles Netzwerk und ein virtuelles Netzwerkgateway erstellt und vollständig bereitgestellt wurden. Befolgen Sie die Anweisungen zum [Konfigurieren eines virtuellen Netzwerkgateways für ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Achten Sie darauf, `--gateway-type ExpressRoute` zu verwenden.

* Sie können bis zu zehn virtuelle Netzwerke mit einer ExpressRoute-Standardverbindung verknüpfen. Alle virtuellen Netzwerke müssen sich in der gleichen geopolitischen Region befinden, wenn Sie eine ExpressRoute-Standardverbindung verwenden. 

* Ein einzelnes VNET kann mit bis zu vier ExpressRoute-Leitungen verknüpft werden. Erstellen Sie anhand der unten erläuterten Vorgehensweise ein neues Verbindungsobjekt für jede ExpressRoute-Leitung, mit der Sie eine Verbindung herstellen. Die ExpressRoute-Leitungen können sich im gleichen Abonnement, in verschiedenen Abonnements oder in einer Kombination aus beidem befinden.

* Wenn Sie das ExpressRoute-Premium-Add-On aktivieren, können Sie ein virtuelles Netzwerk außerhalb der geopolitischen Region der ExpressRoute-Verbindung oder eine größere Anzahl von virtuellen Netzwerken mit der ExpressRoute-Verbindung verknüpfen. Weitere Informationen zum Premium-Add-On finden Sie in den [FAQs](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Herstellen einer Verbindung zwischen einem virtuellen Netzwerk in demselben Abonnement und einer Verbindung

Sie können anhand des folgenden Beispiels ein virtuelles Netzwerkgateway mit einer ExpressRoute-Verbindung verbinden. Stellen Sie sicher, dass das Gateway für das virtuelle Netzwerk erstellt wurde und für das Erstellen von Verknüpfungen bereit ist, bevor Sie den Befehl ausführen.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Herstellen einer Verbindung zwischen einem virtuellen Netzwerk in einem anderen Abonnement und einer Verbindung

Sie können eine ExpressRoute-Verbindung für mehrere Abonnements freigeben. Die Abbildung unten zeigt eine einfache schematische Darstellung der Freigabe von Lasten für ExpressRoute-Verbindungen für mehrere Abonnements.

Jede der kleineren Clouds innerhalb der großen Cloud stellt Abonnements dar, die zu verschiedenen Abteilungen innerhalb einer Organisation gehören. Jede der Abteilungen innerhalb der Organisation kann ihr eigenes Abonnement zum Bereitstellen von Diensten verwenden, für die Verbindung mit dem lokalen Netzwerk kann jedoch eine einzelne gemeinsam genutzte ExpressRoute-Verbindung verwendet werden. Eine einzelne Abteilung (in diesem Beispiel: IT) kann die ExpressRoute-Verbindung besitzen. Andere Abonnements innerhalb der Organisation können die ExpressRoute-Verbindung nutzen.

> [!NOTE]
> Konnektivitäts- und Bandbreitengebühren für die dedizierte Verbindung werden dem Besitzer der ExpressRoute-Verbindung in Rechnung gestellt. Alle virtuellen Netzwerke verwenden gemeinsam dieselbe Bandbreite.
> 
> 

![Abonnementübergreifende Konnektivität](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Verwaltung – Verbindungsbesitzer und Verbindungsbenutzer

Der „Verbindungsbesitzer“ ist ein autorisierter Poweruser der ExpressRoute-Verbindungsressource. Der Verbindungsbesitzer kann Autorisierungen erstellen, die durch „Verbindungsbenutzer“ eingelöst werden können. Verbindungsbenutzer sind Besitzer virtueller Netzwerkgateways, die sich nicht im selben Abonnement wie die ExpressRoute-Verbindung befinden. Verbindungsbenutzer können Autorisierungen einlösen (eine Autorisierung für jedes virtuelle Netzwerk).

Der Besitzer der Verbindung hat die Möglichkeit, Autorisierungen jederzeit zu ändern und zu widerrufen. Wenn eine Autorisierung widerrufen wird, führt dies dazu, dass alle Verbindungen aus dem Abonnement gelöscht werden, dessen Zugriff aufgehoben wurde.

### <a name="circuit-owner-operations"></a>Aktionen als Verbindungsbesitzer

**So erstellen Sie eine Autorisierung**

Der Verbindungsbesitzer erstellt eine Autorisierung, wodurch ein Autorisierungsschlüssel erstellt wird. Dieser kann von einem Verbindungsbenutzer verwendet werden, um dessen virtuelle Netzwerkgateways mit der ExpressRoute-Verbindung zu verbinden. Eine Autorisierung gilt nur für jeweils eine Verbindung.

Im folgenden Beispiel wird veranschaulicht, wie Sie eine Autorisierung erstellen:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

Die Antwort enthält Schlüssel und Status für die Autorisierung:

```azurecli
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**So überprüfen Sie Autorisierungen**

Mit dem folgenden Beispiel kann der Besitzer einer Verbindung alle für eine bestimmte Verbindung ausgestellten Autorisierungen überprüfen:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**So fügen Sie Autorisierung hinzu**

Mit dem folgenden Beispiel kann der Verbindungsbesitzer Autorisierungen hinzufügen:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**So löschen Sie Autorisierungen**

Mit dem folgenden Beispiel kann der Besitzer einer Verbindung Autorisierungen für einen Benutzer widerrufen oder löschen:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Aktionen als Verbindungsbenutzer

Der Verbindungsbenutzer benötigt die Peer-ID und einen Autorisierungsschlüssel vom Verbindungsbesitzer. Der Autorisierungsschlüssel ist eine GUID.

```azurecli
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**So lösen Sie eine Verbindungsautorisierung ein**

Mit dem folgenden Beispiel können Benutzer einer Verbindung eine Verknüpfungsautorisierung abrufen:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**So geben Sie eine Verbindungsautorisierung frei**

Sie können eine Autorisierung durch das Löschen der Verbindung freigeben, die die ExpressRoute-Verbindung mit dem virtuellen Netzwerk verknüpft.

## <a name="modify-a-virtual-network-connection"></a>Ändern einer Verbindung mit einem virtuellen Netzwerk
Sie können bestimmte Eigenschaften einer Verbindung mit einem virtuellen Netzwerk aktualisieren. 

**So aktualisieren Sie die Verbindungsgewichtung**

Das virtuelle Netzwerk kann mit mehreren ExpressRoute-Verbindungen verbunden werden. Möglicherweise erhalten Sie von mehreren ExpressRoute-Verbindungen dasselbe Präfix. Um auszuwählen, an welche Verbindung Datenverkehr für dieses Präfix gesendet werden soll, ändern Sie die Eigenschaft *RoutingWeight* einer Verbindung. Datenverkehr wird an die Verbindung mit dem höchsten *RoutingWeight* gesendet.

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

Der Bereich von *RoutingWeight* liegt zwischen 0 und 32.000. Der Standardwert ist 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurieren von ExpressRoute FastPath 
Sie können [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) aktivieren, wenn Ihre ExpressRoute-Leitung auf [ExpressRoute Direct](expressroute-erdirect-about.md) festgelegt ist und Ihr virtuelles Netzwerkgateway den Typ „Höchstleistung“ oder „ErGw3AZ“ hat. FastPath verbessert die Leistung von Datenpfaden (Pakete pro Sekunde und Verbindungen pro Sekunde) zwischen Ihrem lokalen und Ihrem virtuellen Netzwerk. 

> [!NOTE] 
> Wenn Sie bereits eine virtuelle Netzwerkverbindung haben, aber FastPath noch nicht aktiviert ist, müssen Sie die virtuelle Netzwerkverbindung löschen und eine neue erstellen. 
> 
>  

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).
