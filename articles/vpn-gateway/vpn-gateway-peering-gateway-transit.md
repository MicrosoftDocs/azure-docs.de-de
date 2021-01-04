---
title: Konfigurieren des VPN-Gatewaytransits für ein Peering virtueller Netzwerke
description: Konfigurieren des Gatewaytransits für ein Peering virtueller Netzwerke, um nahtlos zwei virtuelle Azure-Netzwerke zu Konnektivitätszwecken zu einem zu verbinden.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/30/2020
ms.author: cherylmc
ms.openlocfilehash: 2fc12385c78135269b6a73038fd0ad810ebaedd6
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576174"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>Konfigurieren des VPN-Gatewaytransits für ein Peering virtueller Netzwerke

In diesem Artikel erfahren Sie mehr zum Konfigurieren des VPN-Gatewaytransits für ein Peering virtueller Netzwerke. Ein [Peering virtueller Netzwerke](../virtual-network/virtual-network-peering-overview.md) verbindet nahtlos zwei virtuelle Azure-Netzwerke, indem es die beiden virtuellen Netzwerke für Konnektivitätszwecke zu einem zusammenführt. [Gatewaytransit](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) ist eine Peeringeigenschaft, die es einem virtuellen Netzwerk ermöglicht, das VPN-Gateway im Peer-VNET für standortübergreifende oder VNET-zu-VNET-Konnektivität zu nutzen. Die folgende Abbildung zeigt, wie der Gatewaytransit mit einem Peering virtueller Netzwerke funktioniert.

![Diagramm des Gatewaytransits](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

Im Diagramm ermöglicht der Gatewaytransit dem virtuellen Netzwerk mit Peeringbeziehung die Nutzung des Azure-VPN-Gateways in „Hub-RM“. Die im VPN-Gateway verfügbare Konnektivität, einschließlich S2S-, P2S- und VNET-zu-VNET-Verbindungen, gilt für alle drei virtuellen Netzwerke. Die Transitoption ist für Peering zwischen den gleichen oder verschiedenen Bereitstellungsmodellen verfügbar. Wenn Sie den Transit zwischen verschiedenen Bereitstellungsmodellen konfigurieren, müssen das Hub-VNET und das Gateway des virtuellen Netzwerks das Resource Manager-Bereitstellungsmodell und nicht das klassische Bereitstellungsmodell aufweisen.
>

In der Hub-Spoke-Netzwerkarchitektur ermöglicht der Gatewaytransit die gemeinsame Nutzung des VPN-Gateways im Hub, anstatt VPN-Gateways in jedem virtuellen Spoke-Netzwerk bereitzustellen. Routen zu den mit dem Gateway verbundenen virtuellen Netzwerken oder lokalen Netzwerken werden über den Gatewaytransit an die Routingtabellen für die virtuellen Netzwerke mit Peeringbeziehung verteilt. Sie können die automatische Routenverteilung im VPN-Gateway deaktivieren. Erstellen Sie eine Routingtabelle mit der Option **BGP-Routenverteilung deaktivieren**, und ordnen Sie die Routingtabelle den Subnetzen zu, um die Verteilung der Route an diese Subnetze zu verhindern. Weitere Informationen finden Sie unter [Erstellen, Ändern oder Löschen einer Routingtabelle](../virtual-network/manage-route-table.md).

In diesem Artikel gibt es zwei Szenarien:

* **Gleiches Bereitstellungsmodell:** Beide virtuellen Netzwerke wurden mit dem Resource Manager-Bereitstellungsmodell erstellt.
* **Unterschiedliche Bereitstellungsmodelle:** Das Spoke-VNET wurde mit dem klassischen Bereitstellungsmodell erstellt, und das Hub-VNET sowie das Gateway nutzen das Resource Manager-Bereitstellungsmodell.

>[!NOTE]
> Wenn Sie eine Änderung an der Topologie Ihres Netzwerks vornehmen und Windows-VPN-Clients verwenden, muss das VPN-Clientpaket für Windows-Clients heruntergeladen und erneut installiert werden, damit die Änderungen auf den Client angewendet werden.
>

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich zunächst, dass Sie über die folgenden virtuellen Netzwerke und Berechtigungen verfügen:

### <a name="virtual-networks"></a><a name="vnet"></a>Virtuelle Netzwerke

|VNet|Bereitstellungsmodell| Gateway des virtuellen Netzwerks|
|---|---|---|---|
| Hub-RM| [Ressourcen-Manager](vpn-gateway-howto-site-to-site-resource-manager-portal.md)| [Ja](tutorial-create-gateway-portal.md)|
| Spoke-RM | [Ressourcen-Manager](vpn-gateway-howto-site-to-site-resource-manager-portal.md)| Nein |
| Spoke-Classic | [Klassisch](vpn-gateway-howto-site-to-site-classic-portal.md#CreatVNet) | Nein |

### <a name="permissions"></a><a name="permissions"></a>Berechtigungen

Die Konten, mit denen Sie das Peering virtueller Netzwerke erstellen, müssen die erforderliche Rolle oder die erforderlichen Berechtigungen haben. Wenn Sie im folgenden Beispiel ein Peering zweier virtueller Netzwerke mit den Namen **Hub-RM** und **Spoke-Classic** einrichten, benötigt Ihr Konto für jedes virtuelle Netzwerk die folgenden Rollen oder Berechtigungen:

|VNet|Bereitstellungsmodell|Role|Berechtigungen|
|---|---|---|---|
|Hub-RM|Ressourcen-Manager|[Mitwirkender von virtuellem Netzwerk](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klassisch|[Mitwirkender von klassischem Netzwerk](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|–|
|Spoke-Classic|Ressourcen-Manager|[Mitwirkender von virtuellem Netzwerk](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klassisch|[Mitwirkender von klassischem Netzwerk](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Erfahren Sie mehr über [integrierte Rollen](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) und das Zuweisen bestimmter Berechtigungen zu [benutzerdefinierten Rollen](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (nur für Resource Manager).

## <a name="same-deployment-model"></a><a name="same"></a>Gleiches Bereitstellungsmodell

In diesem Szenario wurden die beiden virtuellen Netzwerke mit dem Resource Manager-Bereitstellungsmodell erstellt. Führen Sie die folgenden Schritte zum Erstellen oder Aktualisieren des Peerings virtueller Netzwerke aus, um den Gatewaytransit zu aktivieren.

### <a name="to-add-a-peering-and-enable-transit"></a>So fügen Sie Peering hinzu und aktivieren den Transit

1. Erstellen oder aktualisieren Sie das Peering virtueller Netzwerke von „Hub-RM“ im [Azure-Portal](https://portal.azure.com). Navigieren Sie zum virtuellen Netzwerk **Hub-RM**. Wählen Sie **Peerings** und dann **+ Hinzufügen** aus, um **Peering hinzufügen** zu öffnen.
1. Konfigurieren Sie auf der Seite **Peering hinzufügen** die Werte für **dieses virtuelle Netzwerk**.

   * Name des Peeringlinks: Benennen Sie den Link. Beispiel: **HubRMToSpokeRM**
   * Traffic to remote virtual network (Datenverkehr zum virtuellen Remotenetzwerk): **Zulassen**
   * Traffic forwarded from remote virtual network (Vom virtuellen Remotenetzwerk weitergeleiteter Datenverkehr): **Zulassen**
   * Gateway für virtuelle Netzwerke: **Gateway dieses virtuellen Netzwerks verwenden**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-vnet.png" alt-text="Screenshot der Schaltfläche „Peering hinzufügen“":::

1. Fahren Sie auf derselben Seite mit der Konfiguration der Werte für das **virtuelle Remotenetzwerk** fort.

   * Name des Peeringlinks: Benennen Sie den Link. Beispiel: **SpokeRMtoHubRM**
   * Bereitstellungsmodell: **Ressourcen-Manager**
   * Virtuelles Netzwerk: **Spoke-RM**
   * Traffic to remote virtual network (Datenverkehr zum virtuellen Remotenetzwerk): **Zulassen**
   * Traffic forwarded from remote virtual network (Vom virtuellen Remotenetzwerk weitergeleiteter Datenverkehr): **Zulassen**
   * Gateway für virtuelle Netzwerke: **Gateway des virtuellen Remotenetzwerks verwenden**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-remote.png" alt-text="Screenshot der Werte für das virtuelle Remotenetzwerk":::

1. Wählen Sie **Hinzufügen** aus, um das Peering zu erstellen.
1. Überprüfen Sie, ob der Peeringstatus für beide virtuellen Netzwerke **Verbunden** ist.

### <a name="to-modify-an-existing-peering-for-transit"></a>So ändern Sie ein vorhandenes Peering für den Transit

Wenn das Peering bereits erstellt wurde, können Sie es für den Transit bearbeiten.

1. Navigieren Sie zum virtuellen Netzwerk. Wählen Sie **Peerings** aus und dann das Peering, das Sie ändern möchten.

   :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-modify.png" alt-text="Screenshot der Auswahl des Peerings":::

1. Aktualisieren Sie das VNET-Peering.

   * Traffic to remote virtual network (Datenverkehr zum virtuellen Remotenetzwerk): **Zulassen**
   * Traffic forwarded to virtual network (Zum virtuellen Netzwerk weitergeleiteter Datenverkehr): **Zulassen**
   * Gateway für virtuelle Netzwerke: **Gateway des virtuellen Remotenetzwerks verwenden**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/modify-peering-settings.png" alt-text="Screenshot der Bearbeitung des Peeringgateways":::

1. **Speichern** Sie die Peeringeinstellungen.

### <a name="powershell-sample"></a><a name="ps-same"></a>PowerShell-Beispiel

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

## <a name="different-deployment-models"></a><a name="different"></a>Unterschiedliche Bereitstellungsmodelle

In dieser Konfiguration wird das Spoke-VNET **Spoke-Classic** im klassischen Bereitstellungsmodell und das Hub-VNET **Hub-RM** im Resource Manager-Bereitstellungsmodell bereitgestellt. Beim Konfigurieren des Transits zwischen Bereitstellungsmodellen muss das Gateway des virtuellen Netzwerks für das Resource Manager-VNET und nicht für das klassische VNET konfiguriert werden.

Sie müssen bei dieser Konfiguration also nur das virtuelle Netzwerk **Hub-RM** konfigurieren. Im VNET **Spoke-Classic** müssen Sie nichts konfigurieren.

1. Navigieren Sie im Azure-Portal zum virtuellen Netzwerk **Hub-RM**, und wählen Sie **Peerings** und dann **+ Hinzufügen** aus.
1. Konfigurieren Sie auf der Seite **Peering hinzufügen** die folgenden Werte:

   * Name des Peeringlinks: Benennen Sie den Link. Beispiel: **HubRMToClassic**
   * Traffic to remote virtual network (Datenverkehr zum virtuellen Remotenetzwerk): **Zulassen**
   * Traffic forwarded from remote virtual network (Vom virtuellen Remotenetzwerk weitergeleiteter Datenverkehr): **Zulassen**
   * Gateway für virtuelle Netzwerke: **Gateway dieses virtuellen Netzwerks verwenden**
   * Virtuelles Remotenetzwerk: **Klassisch**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-classic.png" alt-text="Seite „Peering hinzufügen“ für „Spoke-Classic“":::

1. Vergewissern Sie sich, dass das Abonnement stimmt, und wählen Sie dann das virtuelle Netzwerk in der Dropdownliste aus.
1. Wählen Sie **Hinzufügen** aus, um das Peering hinzuzufügen.
1. Überprüfen Sie, ob der Peeringstatus für das virtuelle Netzwerk „Hub-RM“ **Verbunden** ist. 

Bei dieser Konfiguration ist es nicht erforderlich, eine Konfiguration im klassischen virtuellen Netzwerk **Spoke-Classic** vorzunehmen. Sobald der Status **Verbunden** angezeigt wird, kann das virtuelle Spoke-Netzwerk die Konnektivität über das VPN-Gateway im virtuellen Hub-Netzwerk nutzen.

### <a name="powershell-sample"></a><a name="ps-different"></a>PowerShell-Beispiel

Sie können das Peering im obigen Beispiel auch mit PowerShell erstellen oder aktualisieren. Ersetzen Sie die Variablen und Abonnement-ID durch die Werte Ihres virtuellen Netzwerks, Ihrer Ressourcengruppen und Ihres Abonnements. Sie müssen lediglich ein Peering virtueller Netzwerke im virtuellen Netzwerk des Hubs erstellen.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToClassic `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Einschränkungen und Verhalten eines Peerings virtueller Netzwerke](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) und [Einstellungen für ein Peering virtueller Netzwerke](../virtual-network/virtual-network-manage-peering.md#create-a-peering), bevor Sie ein Peering virtueller Netzwerker für den Produktionseinsatz erstellen.
* Erfahren Sie, wie Sie eine [Hub-Spoke-Netzwerktopologie ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) mit einem Peering virtueller Netzwerke und Gatewaytransit erstellen.
* [Erstellen eines Peerings virtueller Netzwerke mit dem gleichen Bereitstellungsmodell](../virtual-network/tutorial-connect-virtual-networks-portal.md)
* [Erstellen eines Peerings virtueller Netzwerke mit unterschiedlichen Bereitstellungsmodellen](../virtual-network/create-peering-different-deployment-models.md)