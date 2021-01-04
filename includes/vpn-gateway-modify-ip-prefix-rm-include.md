---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 76a602ae722bd975e634631819ebc703e8896c98
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026653"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>So ändern Sie die IP-Adresspräfixe eines Gateways des lokalen Netzwerks: Keine Gatewayverbindung

So fügen Sie weitere Adresspräfixe hinzu:

1. Legen Sie die Variable für das lokale Netzwerkgateway (LocalNetworkGateway) fest.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
2. Ändern Sie die Präfixe.

   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24','10.101.2.0/24')
   ```

So entfernen Sie Adresspräfixe:

  Lassen Sie die Präfixe weg, die Sie nicht mehr benötigen. In diesem Beispiel wird das Präfix „10.101.2.0/24“ (aus dem vorherigen Beispiel) nicht mehr benötigt. Daher wird das Gateway für lokale Netzwerke aktualisiert und das Präfix weggelassen.

1. Legen Sie die Variable für das lokale Netzwerkgateway (LocalNetworkGateway) fest.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
2. Legen Sie das Gateway mit den aktualisierten Präfixen fest.

   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
   ```

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>So ändern Sie die IP-Adresspräfixe eines Gateways des lokalen Netzwerks: Vorhandene Gatewayverbindung

Führen Sie die folgenden Schritte in der angegebenen Reihenfolge aus, wenn eine Gatewayverbindung besteht und Sie die IP-Adresspräfixe Ihres lokalen Netzwerkgateways hinzufügen oder entfernen möchten. Dies führt zu Ausfallzeiten bei Ihrer VPN-Verbindung. Beim Ändern von IP-Adresspräfixen müssen Sie das VPN-Gateway nicht löschen. Sie müssen nur die Verbindung entfernen.

1. Entfernen Sie die Verbindung.

   ```azurepowershell-interactive
   Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. Legen Sie das Gateway für das lokale Netzwerk mit den geänderten Adresspräfixen fest.
   
   Legen Sie die Variable für das lokale Netzwerkgateway (LocalNetworkGateway) fest.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
   
   Ändern Sie die Präfixe.
   
   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
   ```
3. Erstellen Sie die Verbindung. In diesem Beispiel konfigurieren wir einen IPsec-Verbindungstyp. Verwenden Sie beim erneuten Erstellen der Verbindung den für Ihre Konfiguration angegebenen Verbindungstyp. Weitere Verbindungstypen finden Sie auf der Seite [PowerShell-Cmdlet](/powershell/module/Azurerm.Network/New-AzureRmVirtualNetworkGatewayConnection) .
   
   Legen Sie die Variable für das virtuelle Netzwerkgateway (VirtualNetworkGateway) fest.

   ```azurepowershell-interactive
   $gateway1 = Get-AzVirtualNetworkGateway -Name VNet1GW  -ResourceGroupName TestRG1
   ```
   
   Erstellen Sie die Verbindung. In diesem Beispiel wird die in Schritt 2 festgelegte Variable „$local“ verwendet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
   -ResourceGroupName TestRG1 -Location 'East US' `
   -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
   -ConnectionType IPsec `
   -RoutingWeight 10 -SharedKey 'abc123'
   ```