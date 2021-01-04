---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/15/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 22d68722afe4be6113263a7e7282dde3f188b18a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026217"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress---no-gateway-connection"></a><a name="gwipnoconnection"></a> So ändern Sie die Angabe für „GatewayIpAddress“ für ein Gateway des lokalen Netzwerks: Keine Gatewayverbindung

Wenn für das VPN-Gerät, mit dem Sie eine Verbindung herstellen möchten, die öffentliche IP-Adresse geändert wurde, müssen Sie das Gateway des lokalen Netzwerks entsprechend anpassen. Ändern Sie das Gateway eines lokalen Netzwerks, für das keine Gatewayverbindung besteht, anhand des Beispiels.

Bei dieser Gelegenheit können Sie auch die Adresspräfixe ändern. Achten Sie darauf, dass Sie den Namen des Gateways des lokalen Netzwerks verwenden, um die aktuellen Einstellungen zu überschreiben. Wenn Sie einen anderen Namen verwenden, wird nicht das bereits vorhandene Gateway des lokalen Netzwerks überschrieben, sondern ein neues erstellt.

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name Site1 `
-Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName TestRG1
```

### <a name="to-modify-the-local-network-gateway-gatewayipaddress---existing-gateway-connection"></a><a name="gwipwithconnection"></a>So ändern Sie die Angabe für „GatewayIpAddress“ für ein Gateway des lokalen Netzwerks: Vorhandene Gatewayverbindung

Wenn für das VPN-Gerät, mit dem Sie eine Verbindung herstellen möchten, die öffentliche IP-Adresse geändert wurde, müssen Sie das Gateway des lokalen Netzwerks entsprechend anpassen. Ist bereits eine Gatewayverbindung vorhanden, muss sie zuerst entfernt werden. Nachdem die Verbindung entfernt wurde, können Sie die Gateway-IP-Adresse ändern und eine neue Verbindung erstellen. Bei dieser Gelegenheit können Sie auch die Adresspräfixe ändern. Dies führt zu Ausfallzeiten bei Ihrer VPN-Verbindung. Beim Ändern der Gateway-IP-Adresse müssen Sie das VPN-Gateway nicht löschen. Sie müssen nur die Verbindung entfernen.
 

1. Entfernen Sie die Verbindung. Den Namen Ihrer Verbindung können Sie mithilfe des Cmdlets „Get-AzVirtualNetworkGatewayConnection“ ermitteln.

   ```azurepowershell-interactive
   Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
   -ResourceGroupName TestRG1
   ```
2. Ändern Sie den GatewayIpAddress-Wert. Bei dieser Gelegenheit können Sie auch die Adresspräfixe ändern. Achten Sie darauf, dass Sie den Namen Ihres lokalen Netzwerkgateways verwenden, um die aktuellen Einstellungen zu überschreiben. Andernfalls wird nicht das bereits vorhandene lokale Netzwerkgateway überschrieben, sondern ein neues erstellt.

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name Site1 `
   -Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
   -GatewayIpAddress "104.40.81.124" -ResourceGroupName TestRG1
   ```
3. Erstellen Sie die Verbindung. In diesem Beispiel konfigurieren wir einen IPsec-Verbindungstyp. Verwenden Sie beim erneuten Erstellen der Verbindung den für Ihre Konfiguration angegebenen Verbindungstyp. Weitere Verbindungstypen finden Sie auf der Seite [PowerShell-Cmdlet](/powershell/module/Azurerm.Network/New-AzureRmVirtualNetworkGatewayConnection) .  Der VirtualNetworkGateway-Name kann mithilfe des Cmdlets „Get-AzVirtualNetworkGateway“ abgerufen werden.
   
    Legen Sie die Variablen fest.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1

   $vnetgw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   ```
   
    Erstellen Sie die Verbindung.

   ```azurepowershell-interactive 
   New-AzVirtualNetworkGatewayConnection -Name VNet1Site1 -ResourceGroupName TestRG1 `
   -Location "East US" `
   -VirtualNetworkGateway1 $vnetgw `
   -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```