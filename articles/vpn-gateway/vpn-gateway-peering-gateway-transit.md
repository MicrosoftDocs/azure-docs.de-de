---
title: 'Konfigurieren des VPN-Gatewaytransits für ein Peering virtueller Netzwerke: Azure Resource Manager | Microsoft-Dokumentation'
description: Konfigurieren Sie den VPN-Gatewaytransit für ein Peering virtueller Netzwerke.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/25/2018
ms.author: yushwang
ms.openlocfilehash: d5e62bf1838c8f07068208019d28d7273c28bd63
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60457373"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>Konfigurieren des VPN-Gatewaytransits für ein Peering virtueller Netzwerke

In diesem Artikel erfahren Sie mehr zum Konfigurieren des VPN-Gatewaytransits für ein Peering virtueller Netzwerke. Ein [Peering virtueller Netzwerke](../virtual-network/virtual-network-peering-overview.md) verbindet nahtlos zwei virtuelle Azure-Netzwerke, indem es die beiden virtuellen Netzwerke für Konnektivitätszwecke zu einem zusammenführt. [Gatewaytransit](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) ist eine Peeringeigenschaft, die es einem virtuellen Netzwerk ermöglicht, das VPN-Gateway im virtuellen Netzwerk mit Peeringbeziehung für standortübergreifende oder VNET-zu-VNET-Konnektivität zu nutzen. Die folgende Abbildung zeigt, wie der Gatewaytransit mit einem Peering virtueller Netzwerke funktioniert.

![Gatewaytransit](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

Im Diagramm ermöglicht der Gatewaytransit dem virtuellen Netzwerk mit Peeringbeziehung die Nutzung des Azure-VPN-Gateways in „Hub-RM“. Die im VPN-Gateway verfügbare Konnektivität, einschließlich S2S-, P2S- und VNET-zu-VNET-Verbindungen, gilt für alle drei virtuellen Netzwerke. Die Transitoption ist für Peering zwischen den gleichen oder verschiedenen Bereitstellungsmodellen verfügbar. Die Einschränkung ist, dass sich das VPN-Gateway nur bei Verwenden des Ressourcen-Manager-Bereitstellungsmodells im virtuellen Netzwerk befinden kann, wie in der Abbildung dargestellt.

In der Hub-Spoke-Netzwerkarchitektur ermöglicht der Gatewaytransit die gemeinsame Nutzung des VPN-Gateways im Hub, anstatt VPN-Gateways in jedem virtuellen Spoke-Netzwerk bereitzustellen. Routen zu den mit dem Gateway verbundenen virtuellen Netzwerken oder lokalen Netzwerken werden über den Gatewaytransit an die Routingtabellen für die virtuellen Netzwerke mit Peeringbeziehung verteilt. Sie können die automatische Routenverteilung im VPN-Gateway deaktivieren. Erstellen Sie eine Routingtabelle mit der Option **BGP-Routenverteilung deaktivieren**, und ordnen Sie die Routingtabelle den Subnetzen zu, um die Verteilung der Route an diese Subnetze zu verhindern. Weitere Informationen finden Sie unter [Erstellen, Ändern oder Löschen einer Routingtabelle](../virtual-network/manage-route-table.md).

In diesem Dokument werden zwei Szenarien beschrieben:

1. Beide virtuellen Netzwerke verwenden das Ressourcen-Manager-Bereitstellungsmodell
2. Das virtuelle Spoke-Netzwerk verwendet das klassische Bereitstellungsmodell, das virtuelle Hub-Netzwerk mit Gateway das Ressourcen-Manager-Bereitstellungsmodell

## <a name="requirements"></a>Requirements (Anforderungen)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Das Beispiel in diesem Dokument erfordert die Erstellung der folgenden Ressourcen:

1. Virtuelles Netzwerk „Hub-RM“ mit VPN-Gateway
2. Virtuelles Netzwerk „Spoke-RM“
3. Virtuelles Netzwerk „Spoke-Classic“ mit dem klassischen Bereitstellungsmodell
4. Das von Ihnen verwendete Konto benötigt die erforderlichen Rollen und Berechtigungen. Weitere Informationen finden Sie im Abschnitt [Berechtigungen](#permissions) dieses Artikels.

Anweisungen finden Sie in den folgenden Dokumenten:

1. [Erstellen eines VPN-Gateways in einem virtuellen Netzwerk](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
2. [Erstellen eines Peerings virtueller Netzwerke mit dem gleichen Bereitstellungsmodell](../virtual-network/tutorial-connect-virtual-networks-portal.md)
3. [Erstellen eines Peerings virtueller Netzwerke mit unterschiedlichen Bereitstellungsmodellen](../virtual-network/create-peering-different-deployment-models.md)

## <a name="permissions"></a>Berechtigungen

Die Konten, mit denen Sie das Peering virtueller Netzwerke erstellen, müssen die erforderliche Rolle oder die erforderlichen Berechtigungen haben. Wenn Sie im folgenden Beispiel ein Peering zweier virtueller Netzwerke mit den Namen „Hub-RM“ und „Spoke-Classic“ einrichten, benötigt Ihr Konto für jedes virtuelle Netzwerk die folgenden Rollen oder Berechtigungen:
    
|Virtuelles Netzwerk|Bereitstellungsmodell|Rolle|Berechtigungen|
|---|---|---|---|
|Hub-RM|Ressourcen-Manager|[Mitwirkender von virtuellem Netzwerk](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klassisch|[Mitwirkender von klassischem Netzwerk](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|–|
|Spoke-Classic|Ressourcen-Manager|[Mitwirkender von virtuellem Netzwerk](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klassisch|[Mitwirkender von klassischem Netzwerk](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Erfahren Sie mehr über [integrierte Rollen](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) und das Zuweisen bestimmter Berechtigungen zu [benutzerdefinierten Rollen](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (nur für Resource Manager).

## <a name="resource-manager-to-resource-manager-peering-with-gateway-transit"></a>Ressourcen-Manager-zu-Ressourcen-Manager-Peering mit Gatewaytransit

Befolgen Sie die Anweisungen zum Erstellen oder Aktualisieren des Peerings virtueller Netzwerke, um den Gatewaytransit zu aktivieren.

1. Erstellen oder aktualisieren Sie das Peering virtueller Netzwerke von „Spoke-RM“ zu „Hub-RM“ im Azure-Portal. Navigieren Sie zur virtuellen Netzwerkressource „Spoke-RM“, klicken Sie auf „Peerings“ und dann auf „Hinzufügen“:
    - Legen Sie die Option „Ressourcen-Manager“ fest.
    - Wählen Sie das virtuelle Netzwerk „Hub-RM“ im entsprechenden Abonnement aus.
    - Vergewissern Sie sich, dass „Zugriff auf virtuelles Netzwerk zulassen“ aktiviert ist.
    - Legen Sie die Option **Remotegateways verwenden** fest.
    - Klicken Sie auf „OK“.

      ![SpokeRM-zu-HubRM](./media/vpn-gateway-peering-gateway-transit/spokerm-hubrm-peering.png)

2. Wenn das Peering bereits erstellt wurde, navigieren Sie zur Peeringressource, und aktivieren Sie dann die Option **Remotegateways verwenden**, wie in Schritt (1) gezeigt.

3. Erstellen oder aktualisieren Sie das Peering virtueller Netzwerke von „Hub-RM“ zu „Spoke-RM“ im Azure-Portal. Navigieren Sie zur virtuellen Netzwerkressource „Hub-RM“, klicken Sie auf „Peerings“ und dann auf „Hinzufügen“:
    - Legen Sie die Option „Ressourcen-Manager“ fest.
    - Vergewissern Sie sich, dass „Zugriff auf virtuelles Netzwerk zulassen“ aktiviert ist.
    - Wählen Sie das virtuelle Netzwerk „Spoke-RM“ im entsprechenden Abonnement aus.
    - Legen Sie die Option **Gatewaytransit zulassen** fest.
    - Klicken Sie auf „OK“.

      ![HubRM-zu-SpokeRM](./media/vpn-gateway-peering-gateway-transit/hubrm-spokerm-peering.png)

4. Wenn das Peering bereits erstellt wurde, navigieren Sie zur Peeringressource, und aktivieren Sie dann die Option **Gatewaytransit zulassen**, wie in Schritt (3) gezeigt.

5. Prüfen Sie, ob der Peeringstatus für beide virtuellen Netzwerke **Verbunden** ist.

### <a name="powershell-sample"></a>PowerShell-Beispiel

Sie können das Peering im obigen Beispiel auch mit PowerShell erstellen oder aktualisieren. Ersetzen Sie die Variablen durch die Namen Ihrer virtuellen Netzwerke und Ressourcengruppen.

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="classic-to-resource-manager-peering-with-gateway-transit"></a>Klassisch-zu-Ressourcen-Manager-Peering mit Gatewaytransit

Die Schritte sind ähnlich wie beim Ressourcen-Manager-Beispiel, nur dass die Vorgänge auf das virtuelle Netzwerk „Hub-RM“ angewendet werden.

1. Erstellen oder aktualisieren Sie das Peering virtueller Netzwerke von „Hub-RM“ zu „Spoke-RM“ im Azure-Portal. Navigieren Sie zur virtuellen Netzwerkressource „Hub-RM“, klicken Sie auf „Peerings“ und dann auf „Hinzufügen“:
   - Festlegen der Option „Klassisch“ für das Bereitstellungsmodell für virtuelle Netzwerke
   - Wählen Sie das virtuelle Netzwerk „Spoke-Classic“ im entsprechenden Abonnement aus.
   - Vergewissern Sie sich, dass „Zugriff auf virtuelles Netzwerk zulassen“ aktiviert ist.
   - Legen Sie die Option **Gatewaytransit zulassen** fest.
   - Klicken Sie auf „OK“.

     ![HubRM-zu-Spoke-Classic](./media/vpn-gateway-peering-gateway-transit/hubrm-spokeclassic-peering.png)

2. Wenn das Peering bereits erstellt wurde, navigieren Sie zur Peeringressource, und aktivieren Sie dann die Option **Gatewaytransit zulassen**, wie in Schritt (1) gezeigt.

3. Im virtuellen Netzwerk „Spoke-Classic“ gibt es keinen Vorgang.

4. Prüfen Sie, ob der Peeringstatus für das virtuelle Netzwerk „HubRM“ **Verbunden** ist.

Sobald der Status "Verbunden" angezeigt wird, können die virtuellen Spoke-Netzwerke über das VPN-Gateway im virtuellen Netzwerk des Hubs mithilfe von VNET-zu-VNET- oder standortübergreifende Verbindungen verbunden werden.

### <a name="powershell-sample"></a>PowerShell-Beispiel

Sie können das Peering im obigen Beispiel auch mit PowerShell erstellen oder aktualisieren. Ersetzen Sie die Variablen und Abonnement-ID durch die Werte Ihres virtuellen Netzwerks, Ihrer Ressourcengruppen und Ihres Abonnements. Sie müssen lediglich ein Peering virtueller Netzwerke im virtuellen Netzwerk des Hubs erstellen.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Einschränkungen und Verhalten eines Peerings virtueller Netzwerke](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) und [Einstellungen für ein Peering virtueller Netzwerke](../virtual-network/virtual-network-manage-peering.md#create-a-peering), bevor Sie ein Peering virtueller Netzwerker für den Produktionseinsatz erstellen.
* Erfahren Sie, wie Sie eine [Hub-Spoke-Netzwerktopologie ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) mit einem Peering virtueller Netzwerke und Gatewaytransit erstellen.
