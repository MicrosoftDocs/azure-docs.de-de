---
title: 'Konfigurieren der Tunnelerzwingung für Azure-Site-to-Site-Verbindungen: klassisch | Microsoft-Dokumentation'
description: 'Gewusst wie: „Erzwingen“ der Umleitung des gesamten Internetdatenverkehrs an Ihren lokalen Standort.'
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 5c0177f1-540c-4474-9b80-f541fa44240b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: 0955d95ebfd9e1f72ed1da577bf3520a70b71624
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60506010"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Konfigurieren der Tunnelerzwingung mit dem klassischen Bereitstellungsmodell

Über die Tunnelerzwingung können Sie die Umleitung des gesamten Internetdatenverkehrs an Ihren lokalen Standort „erzwingen“. Sie verwenden dazu einen Standort-zu-Standort-VPN-Tunnel für die Kontrolle und Überwachung. Dies ist eine wichtige Sicherheitsvoraussetzung der IT-Richtlinien für die meisten Unternehmen. Ohne die Tunnelerzwingung wird der Internetdatenverkehr Ihrer virtuellen Computer in Azure immer direkt von der Azure-Netzwerkinfrastruktur an das Internet geleitet, ohne dass Sie die Möglichkeit haben, diesen zu überprüfen oder zu überwachen. Nicht autorisierter Zugriff auf das Internet kann potenziell zur Offenlegung von Informationen oder anderen Arten von Sicherheitsverletzungen führen.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Dieser Artikel beschreibt die Konfiguration der Tunnelerzwingung für virtuelle Netzwerke, die mit dem klassischen Bereitstellungsmodell erstellt wurden. Das erzwungene Tunneling kann mithilfe von PowerShell konfiguriert werden, nicht über das Portal. Wenn Sie das erzwungene Tunneling für das Resource Manager-Bereitstellungsmodell konfigurieren möchten, wählen Sie in der folgenden Dropdownliste den Resource Manager-Artikel aus:

> [!div class="op_single_selector"]
> * [PowerShell – klassisch](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Anforderungen und Überlegungen
Die Tunnelerzwingung in Azure wird über benutzerdefinierte Routen im virtuellen Netzwerk konfiguriert. Das Umleiten von Datenverkehr an einen lokalen Standort wird als eine Standardroute zum Azure-VPN-Gateway umgesetzt. Im folgende Abschnitt werden die aktuellen Einschränkung für die Routingtabelle und die Routen in Azure Virtual Network aufgeführt:

* Jedes Subnetz des virtuellen Netzwerks verfügt über eine integrierte Systemroutingtabelle. Die Systemroutingtabelle verfügt über die folgenden drei Gruppen von Routen:

  * **Lokale VNET-Routen:** Direkt zu den virtuellen Zielcomputern im selben virtuellen Netzwerk
  * **Lokale Routen:** Zum Azure-VPN-Gateway
  * **Standardroute:** Direkt zum Internet Pakete an private IP-Adressen, die nicht durch die vorherigen beiden Routen abgedeckt sind, werden verworfen.
* Sie können mit der Veröffentlichung von benutzerdefinierten Routen eine Routingtabelle erstellen, um eine Standardroute hinzuzufügen. Anschließend verknüpfen Sie dann die Routingtabelle mit den VNET-Subnetzen, um die Tunnelerzwingung in diesen Subnetzen zu aktivieren.
* Sie müssen einen "Standardstandort" unter den standortübergreifenden lokalen Standorten auswählen, der mit dem virtuellen Netzwerk verbunden ist.
* Die Tunnelerzwingung muss einem VNet zugeordnet werden, das über ein VPN-Gateway für dynamisches Routing (kein statisches Gateway) verfügt.
* Die ExpressRoute-Tunnelerzwingung kann über diesen Mechanismus nicht konfiguriert werden. Sie wird stattdessen durch Anfordern einer Standardroute über die ExpressRoute-BGP-Peeringsitzungen aktiviert. Weitere Informationen finden Sie in der [Dokumentation zu ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Konfigurationsübersicht
Im folgenden Beispiel wird für das Front-End-Subnetz kein Tunneln erzwungen. Die Workloads im Frontend-Subnetz können weiterhin Kundenanfragen direkt aus dem Internet akzeptieren und darauf reagieren. Für die Subnetze "Midtier" und "Backend" wird das Tunneln erzwungen. Bei allen ausgehenden Verbindungen mit dem Internet aus diesen zwei Subnetzen wird eine Umleitung an einen lokalen Standort über einen der S2S-VPN-Tunnel erzwungen.

Dadurch können Sie den Internetzugriff über die virtuellen Computer oder Clouddienste in Azure einschränken und überprüfen, während die erforderliche mehrschichtige Dienstarchitektur weiterhin in Betrieb bleibt. Sie können das erzwungene Tunneln auch auf alle virtuellen Netzwerke anwenden, wenn in diesen keine Workloads mit Internetverbindung erforderlich sind.

![Tunnelerzwingung](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Voraussetzungen
Vergewissern Sie sich vor Beginn der Konfiguration, dass Sie über Folgendes verfügen:

* Ein Azure-Abonnement. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) registrieren.
* Ein konfiguriertes virtuelles Netzwerk. 
* Die neueste Version der Azure PowerShell-Cmdlets. Weitere Informationen zum Installieren der PowerShell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview) .

## <a name="configure-forced-tunneling"></a>Konfigurieren der Tunnelerzwingung
Das folgende Verfahren hilft Ihnen bei der Angabe der Tunnelerzwingung für ein virtuelles Netzwerk. Die Konfigurationsschritte entsprechen der VNet-Netzwerkkonfigurationsdatei.

```xml
<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>
```

In diesem Beispiel weist das virtuelle Netzwerk MultiTier-VNet drei Subnetze auf: „Frontend“, „Midtier“ und „Backend“ mit vier standortübergreifenden Verbindungen: „DefaultSiteHQ“ und drei Branches. 

Mithilfe der Schritte wird festgelegt, dass „DefaultSiteHQ“ die Standard-Standortverbindung für die Tunnelerzwingung ist und die Subnetze „Midtier“ und „Backend“ die Tunnelerzwingung verwenden müssen.

1. Erstellen Sie eine Routingtabelle. Verwenden Sie das folgende Cmdlet zum Erstellen der Routingtabelle.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

2. Fügen Sie der Routingtabelle eine Standardroute hinzu. 

   Das folgende Beispiel fügt der in Schritt 1 erstellten Routingtabelle eine Standardroute hinzu. Beachten Sie, dass die einzige unterstützte Route das Zielpräfix von 0.0.0.0/0 zum nächsten Hop „VPNGateway“ ist.

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

3. Ordnen Sie die Routingtabelle den Subnetzen zu. 

   Nachdem eine Routingtabelle erstellt und eine Route hinzugefügt wurde, verwenden Sie das folgende Beispiel, um die Routingtabelle einem VNet-Subnetz hinzuzufügen oder zuzuordnen. Das Beispiel fügt die Routingtabelle „MyRouteTable“ den Subnetzen „Midtier“ und „Backend“ des virtuellen Netzwerks „MultiTier-VNet“ hinzu.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

4. Weisen Sie einen Standardstandort für die Tunnelerzwingung zu. 

   Im vorherigen Schritt wurde mit dem Cmdlet-Beispielskript die Routingtabelle erstellt und zwei VNet-Subnetzen zugeordnet. Im letzten Schritt wird ein lokaler Standort aus den Verbindungen mit mehreren Standorten des virtuellen Netzwerks als Standardstandort oder -tunnel ausgewählt.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>Weitere PowerShell-Cmdlets
### <a name="to-delete-a-route-table"></a>So löschen Sie eine Routingtabelle

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>So listen Sie eine Routingtabelle auf

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>So löschen Sie eine Route aus einer Routingtabelle

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>So entfernen Sie eine Route aus einem Subnetz

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>So listen Sie die einem Subnetz zugeordnete Routingtabelle auf

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>So entfernen Sie einen Standardstandort aus einem VNET-VPN-Gateway

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
