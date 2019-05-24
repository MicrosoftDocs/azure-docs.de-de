---
title: Einrichten der Konnektivität zwischen einem virtuellen Netzwerk und SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Einrichten der Konnektivität zwischen einem virtuellen Netzwerk und SAP HANA in Azure (große Instanzen)
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cb14d0784ecb87c85b02952880e9eb5744d205a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66153871"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Verbinden eines virtuellen Netzwerks mit großen HANA-Instanzen

Nachdem Sie ein virtuelles Azure-Netzwerk erstellt haben, können Sie dieses Netzwerk mit großen SAP HANA-Instanzen in Azure verbinden. Erstellen Sie ein Azure ExpressRoute-Gateway im virtuellen Netzwerk. Mit diesem Gateway können Sie das virtuelle Netzwerk mit der ExpressRoute-Leitung verknüpfen, die eine Verbindung mit dem Kundenmandanten auf dem großen Instanzstempel herstellt.

> [!NOTE] 
> Die vollständige Ausführung dieses Schritts kann bis zu 30 Minuten dauern. Das neue Gateway wird im vorgesehenen Azure-Abonnement erstellt und dann mit dem angegebenen virtuellen Azure-Netzwerk verbunden.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Wenn bereits ein Gateway vorhanden ist, überprüfen Sie, ob es sich dabei um ein ExpressRoute-Gateway handelt. Wenn dies nicht der Fall ist, löschen Sie das Gateway, und erstellen Sie ein ExpressRoute-Gateway neu. Wenn bereits ein ExpressRoute-Gateway eingerichtet ist, lesen Sie den Abschnitt „Verknüpfen virtueller Netzwerke“ in diesem Artikel. 

- Verwenden Sie entweder das [Azure-Portal](https://portal.azure.com/) oder PowerShell, um ein mit Ihrem virtuellen Netzwerk verbundenes ExpressRoute-VPN-Gateway zu erstellen.
  - Wenn Sie das Azure-Portal verwenden, fügen Sie ein neues **virtuelles Netzwerkgateway** hinzu, und wählen Sie dann **ExpressRoute** als Gatewaytyp aus.
  - Bei Verwendung von PowerShell laden Sie zuerst das aktuelle [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) herunter, und verwenden Sie dieses. Über die folgenden Befehle erstellen Sie ein ExpressRoute-Gateway. Die Texte, denen _$_ vorangestellt ist, sind benutzerdefinierte Variablen, die mit Ihren spezifischen Informationen aktualisiert werden müssen.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

In diesem Beispiel wurde die Gateway-SKU „HighPerformance“ verwendet. Ihre Optionen sind „HighPerformance“ oder „UltraPerformance“ als einzige Gateway-SKUs, die für SAP HANA in Azure (große Instanzen) unterstützt werden.

> [!IMPORTANT]
> Für große HANA-Instanzen von Typ-II-SKUs müssen Sie die UltraPerformance-Gateway-SKU verwenden.

## <a name="link-virtual-networks"></a>Verknüpfen virtueller Netzwerke

Das virtuelle Azure-Netzwerk verfügt jetzt über ein ExpressRoute-Gateway. Verwenden Sie die von Microsoft bereitgestellten Autorisierungsinformationen, um das ExpressRoute-Gateway mit der ExpressRoute-Leitung von SAP HANA in Azure (große Instanzen) zu verbinden. Sie können die Verbindung im Azure-Portal oder mit PowerShell herstellen. Die Verwendung des Portals wird empfohlen, aber wenn Sie PowerShell verwenden möchten, folgen Sie den nachstehenden Anweisungen. 

Führen Sie die folgenden Befehle für jedes virtuelle Netzwerkgateway aus, und verwenden Sie dabei für jede Verbindung eine andere AuthGUID. Die ersten beiden Einträge im folgenden Skript stammen aus den von Microsoft bereitgestellten Informationen. Darüber hinaus ist die AuthGUID spezifisch für jedes virtuelle Netzwerk und das zugehörige Gateway. Wenn Sie ein weiteres virtuelles Azure-Netzwerk hinzufügen möchten, müssen Sie eine weitere AuthID für Ihre ExpressRoute-Leitung abrufen, die große SAP HANA-Instanzen in Azure verbindet. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Wenn Sie das Gateway mit mehreren ExpressRoute-Leitungen verbinden möchten, die Ihrem Abonnement zugeordnet sind, müssen Sie diesen Schritt unter Umständen mehrmals ausführen. Beispielsweise ist die Wahrscheinlichkeit hoch, dass Sie das virtuelle Netzwerkgateway, das das virtuelle Netzwerk mit Ihrem lokalen Netzwerk verbindet, mit der ExpressRoute-Leitung verbinden.

## <a name="next-steps"></a>Nächste Schritte

- [Zusätzliche Netzwerkanforderungen für HLI](hana-additional-network-requirements.md)
