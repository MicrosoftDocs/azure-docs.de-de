---
title: 'Azure PowerShell-Skriptbeispiel: Konfigurieren einer Point-to-Site-VPN-Verbindung mit nativer Azure-Zertifikatauthentifizierung | Microsoft-Dokumentation'
description: Konfigurieren Sie eine Point-to-Site-VPN-Verbindung (P2S) mit nativer Azure-Zertifikatauthentifizierung unter Verwendung selbstsignierter Zertifikate. In diesem Artikel wird PowerShell verwendet.
services: vpn-gateway
documentationcenter: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 04/17/2018
ms.author: alzam
ms.openlocfilehash: 6dae21b5f1360a2507eeb70cb64cea44720b92b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66113715"
---
# <a name="create-a-vpn-gateway-and-add-point-to-site-configuration-using-powershell"></a>Erstellen eines VPN-Gateways und Hinzufügen einer P2S-Konfiguration mithilfe von PowerShell

Mit diesem Skript wird ein routenbasiertes VPN-Gateway erstellt und eine P2S-Konfiguration mit nativer Azure-Zertifikatauthentifizierung hinzugefügt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```azurepowershell-interactive
# Declare variables
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "10.0.0.0/16"
  $FESubPrefix = "10.1.0.0/24"
  $BESubPrefix = "10.1.1.0/24"
  $GWSubPrefix = "10.1.255.0/27"
  $VPNClientAddressPool = "192.168.0.0/24"
  $RG = "TestRG1"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWIP"
  $GWIPconfName = "gwipconf"
# Create a resource group
New-AzResourceGroup -Name TestRG1 -Location EastUS
# Create a virtual network
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
# Create a subnet configuration
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
# Set the subnet configuration for the virtual network
$virtualNetwork | Set-AzVirtualNetwork
# Add a gateway subnet
$vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
# Set the subnet configuration for the virtual network
$vnet | Set-AzVirtualNetwork
# Request a public IP address
$gwpip= New-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' `
 -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
# Create the VPN gateway
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
 -Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
# Add the VPN client address pool
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
# Create a self-signed root certificate
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
 -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
 -HashAlgorithm sha256 -KeyLength 2048 `
 -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
# Export the root certificate to "C:\cert\P2SRootCert.cer"
# Upload the root certificate public key information
$P2SRootCertName = "P2SRootCert.cer"
$filePathForCert = "C:\cert\P2SRootCert.cer"
$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
$CertBase64 = [system.convert]::ToBase64String($cert.RawData)
$p2srootcert = New-AzVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName `
 -VirtualNetworkGatewayname "VNet1GW" `
 -ResourceGroupName "TestRG1" -PublicCertData $CertBase64

```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die erstellten Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Damit löschen Sie die Ressourcengruppe mit allen enthaltenen Ressourcen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet die folgenden Befehle zum Erstellen der Bereitstellung. Jedes Element in der Tabelle ist mit der befehlsspezifischen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Fügt eine Subnetzkonfiguration hinzu. Diese Konfiguration wird mit dem Prozess der Erstellung des virtuellen Netzwerks verwendet. |
| [Add-AzVpnClientRootCertificate](/powershell/module/az.network/add-azvpnclientrootcertificate) | Lädt die Informationen des öffentlichen Schlüssels des Stammzertifikats in das VPN-Gateway hoch.|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Ruft Details zu einem virtuellen Netzwerk ab. |
| [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) | Ruft Details zu einem Gateway für virtuelle Netzwerke ab. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Ruft Details zur Subnetzkonfiguration für virtuelle Netzwerke ab. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Erstellt eine Subnetzkonfiguration. Diese Konfiguration wird mit dem Prozess der Erstellung des virtuellen Netzwerks verwendet. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Erstellen Sie ein virtuelles Netzwerk. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Erstellt eine öffentliche IP-Adresse. |
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Erstellt eine neue Gateway-IP-Konfiguration. |
| [New-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgateway) | Erstellt ein VPN-Gateway. |
| [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate?view=win10-ps) | Erstellt ein neues selbstsigniertes Stammzertifikat. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Entfernt eine Ressourcengruppe und alle darin enthaltenen Ressourcen. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Legt die Subnetzkonfiguration für das virtuelle Netzwerk fest. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).
